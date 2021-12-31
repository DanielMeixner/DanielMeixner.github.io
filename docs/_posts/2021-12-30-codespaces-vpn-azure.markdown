---
layout: post
title: Connect to a virtual network with GitHub Codespaces
permalink: /Codespaces-VPN-Azure/
---

If you are interested in working with Codespaces and want to access resources in a private network here’s a guide how you can achive this. 

Actually there’s already [some documentation](https://docs.github.com/en/codespaces/developing-in-codespaces/connecting-to-a-private-network) on this topic which refers to a GitHub repository with [even more docs](https://github.com/codespaces-contrib/codespaces-openvpn) here which then again links to other docs on how to achieve this. My post contains the essence of all of that boiled down to what I found important for myself. I hope it is useful for you as well. For more information, please check the original posts. I also stripped down the code to be as compact as possible. The original solution mentioned above is a little more sophisticated.

I’ll start with the interesting part and for completeness will then also quickly show the boring stuff that has to be done, to figure out if it really works.

# Goal
The goal is to connect to a virtual network in Azure from a GitHub Codespace. The VNET in Azure shall not be publicly accessible. GitHub Codespaces run on shared infrastructure in a datacenter out of my control. I want to be able to communicate with resources inside my VNET in Azure from my Codespace.

# Solution:
I create a VPN connection to my VNET using OpenSSL. The connection will be established whenever my CodeSpace starts.

# Requirements
I don't want to stored secrets in my repo. 

# Step by step:
Here’s what I do step by step.

## Setup your codespace
1. **Create a CodeSpace for your repo on GitHub and add a .devcontainer file**. You can do this manually but I recommend you use the [wizard](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/setting-up-your-project-for-codespaces?langId=java#step-2:-add-a-dev-container-to-your-codespace-from-a-template). Depending on the language of your project you can pick a container that fits. In my case it was a node.js container.

1.	**Modify the DOCKERFILE to install OpenVPN tools** With the previous command you got a DOCKERFILE. This will be used to build your container. You can edit it as you like. We need to install the OpenVPN client software so we add these lines:
    ```
    RUN export DEBIAN_FRONTEND=noninteractive && apt-get update \
    && apt-get -y install --no-install-recommends openvpn \    
    ```

1.	**Add a poststartcommand to connect** To connect we need to call a few commands. 
    We put those commands in a separate file. I put it into a subfolder in the ./devcontainer folder. This folder has been created automatically when you followed the wizard in step 1. Let's name the file "install-dev-tools". I put it into a subfolder ".devcontainer/postcreate/install-dev-tools.sh".
    
    This file will be called whenever your container starts up. Add the following line to the file.

    *Content of ./.devcontainer/postcreate/install-dev-tools.sh*
    ```
        #!/usr/bin/env bash        
        mkdir -p ./.ignore 
        echo ".ignore/" >> .gitignore 
        echo "${OPENVPNCONFIG}" > ./.ignore/openvpn.config 
        nohup sudo /bin/sh -c " openvpn --log ./.ignore/openvpn.log --config ./.ignore/openvpn.config &" 
        
    ```     

    To actually call the file we add this section to the devcontainer.json file:
    ```
    "postStartCommand": "bash ./.devcontainer/postcreate/install-dev-tools.sh",	
    ```
    
    **What does it?** 
    - First it creates a .ignore directory. (mkdir ...) 
    - It adds the .ignore folder to the .gitignore  file to make sure it will never be part of your repository and therefore not end up in the version history. (echo ".ignore/" >> .gitignore ) **This is important!** Otherwise everybody could connect to your VPN, if you accidentally push this to a public repo. 
    - It creates a file which contain the configuration for your vpn connection. This information is read from an environment variable. (echo "${OPENVPNCONFIG}" ...)
    - Then it opens a VPN connection. We use nohup here to avoid hangups. We also run with sudo - this might or might not be neccessary in your case. And of course we  refer to the newly created file for config. We log to the same folder. (nohup sudo openvpn...)

1. **Create a GitHub Codespaces secret** which contains the required information to create a VPN connection. We create a new secret as described [here](https://docs.github.com/en/codespaces/managing-your-codespaces/managing-encrypted-secrets-for-your-codespaces#adding-a-secret). Call this secret OPENVPNCONFIG. You could call it whatever you like but for this tutorial stick with this name.
You might wonder how you get the "required information". Yes, that's the tricky part. We are looking for an OpenSSL configuration here. We come to this in a second. For now you can create the secret. If you already have the info available, perfect. If not, revisit this step later and update your secret.

1. **Make the secret available to your repo**. You have to explicitly allow CodeSpaces to read this secret. This is also described [here](https://docs.github.com/en/codespaces/managing-your-codespaces/managing-encrypted-secrets-for-your-codespaces#adding-a-secret). Make sure the Codespace you want to connect can read the secret. You have to restart the Codespace to be able to actually read it.

This is it, basically. The tricky part might be how to get to the content of the secret. If you don't know how to get this openvpn config, read the next chapter.

## Create the OpenSSL Config
1. First set up a Virtual network Gateway in Azure. 
1. Set up a Point-to-Site connection for OpenSSL. This is described [here](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). It's all straight forward, the tricky part is the creation of certs. You will end up with a Virtual Network on Azure and a VPN gateway if you follwo this tutorial. When creating the Point-to-Site connection choose tunnel tpye OpenVPN.
1. Create the certificates. This is described [here](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-certificates-point-to-site) as well. It all boils down to a few PowerShell commands.
1. Export the certificates (both root and client cert) as described [here](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#cer) You will end up with a .cer file and a .pfx file.
1. Hint: You will need the server cert to be able to set up the point to site connection. For this you can simply open your .cer file in a texteditor and copy the content into the "Public certifacte data" field in Azure Portal. Do not copy the "----BEGIN CERTIFICATE ..." and "-----END ..." lines, just the content.
1. If you are finished hit the "Download VPN client" button in Azure Portal on your Point-to-Site configuration. 
![Azure Portal](images/20211230vpn.jpg)

You will get a bunch of stuff but will also find a OpenVPN folder with an vpnconfig.ovpn file. Open the file in a text editor. You will see, there are two sections missing. You have to replace $CLIENTCERTIFICATE and $PRIVATEKEY. Run this command to get the values.
    ```
    openssl pkcs12 -in clientcert.pfx  -out clientcertvalues.pem -nodes
    ```
    You will get a clientcertvalues.pem file as a result. You can open this in a text editor and will find the client certificate and the private key in there. **Do NOT put this file into source control!** A private key is called private key because it is meant to stay private! Don't share it!
1. Replace the values in vpnconfig.ovpn as described. No copy the content of this file to the clipboard and paste it as a value into the GitHub Codespaces secret you already created. You might realize that you can not access the content of your existing secret. That's on purpose, but you can [edit](https://docs.github.com/en/codespaces/managing-your-codespaces/managing-encrypted-secrets-for-your-codespaces#editing-a-secret) the value of the secret. Just replace it with the content of your clipboard. Didn't I just say you should not share this secret? Yes! You can still share it as a secret in GitHub because you will be the only one allowed to access this value. It will not be visible in your repository, even if you make the repo public.


That's it. Now let's test if it really works.



## Test it

1.	Add a virtual machine to the Virtual Network you just created. I chose a ubuntu server.
1.  Open port 8080 in the network configuration.
1.  Log into this VM via ssh.
1.  Install ruby into the VM. 
```
apt-get install ruby
```
1. Create a simple HTML page in a www folder.
```
mkdir -p ./www
echo "<HTML><BODY>Hello Codespaces </HTML></BODY>" > ./www/index.html
cd ./www
```
1. Run a webserver to deliver this website.
```
ruby -run -e httpd . -p 8080
```
1. Now you have a webserver running. Keep it running.
1. In the Azure portal check the private IP your machine got. (Not the public one!)

1. Now restart your Codespace. On the commandline run 
```
curl YOUR-PRIVATE-IP:8080
```
You should see a nice greeting from your ruby webserver.
Don't forget to delete the server afterwards.

Please make sure you read the following docs for the full picture:
- [Official docs on VPN connectivity from Codespaces ](https://docs.github.com/en/codespaces/developing-in-codespaces/connecting-to-a-private-network)
- [GitHub repo on VPN connectivity from Codespaces](https://github.com/codespaces-contrib/codespaces-openvpn#:~:text=Using%20the%20OpenVPN%20client%20from%20GitHub%20Codespaces%20GitHub,machine%20or%20the%20network%20it%20is%20sitting%20in.)
- [Info on DevContainer](https://code.visualstudio.com/docs/remote/create-dev-container)

