---
layout: post
title: Retrieve secrets from Azure Keyvault into your GitHub Codespaces
permalink: /codespaces-keyvault-secrets
---
During development at some point you might need to use a secret, e.g. a password or connection string either within your development environment or while debugging. For GitHub Codespaces a solution exists with [Codespaces Secrets in GitHub ](https://docs.github.com/en/codespaces/managing-codespaces-for-your-organization/managing-encrypted-secrets-for-your-repository-and-organization-for-codespaces) and this actually works pretty good. 

I was looking for a way how to controll my  secrets within an Azure KeyVault, e.g.  to be able to share  secrets with peers outside of my organisation and because I like the handling and API and GUI better in Azure KeyVault. So my idea was to integrate GitHub codespaces with Azure Keyvault.

I found the way described below and can now access Keyvault secrets via environment variables from both my commandline in Codespaces and from the app.

## Here's what I did:

1. Create an Azure KeyVault with some secrets.
1. Create a Service Principal on Azure Commandline.
```
az ad spn create-for-rbac -n GHCodespaces
```
1. Create a GitHub Codespaces secret and paste the output from the previous command into it. Make sure you name the secret AZSPN.
1. Create another GitHub Codespaces secret and provide the name of your keyvault. Make sure you name this secret VAULTNAME.
1. Create an [Azure Keyvault](https://docs.microsoft.com/en-us/azure/key-vault/general/quick-create-portal) and an an access policy in Azure Keyvault to give your Service Principal secret list and secret get permissions.
1. In your Codespace modify the .devcontainer.json file to call the script I'm providing [here](https://github.com/DanielMeixner/demo-AzureSecrets/blob/main/.devcontainer/readSecretsFromKeyVaultToEnv.sh) as a poststartcommand.
1. Additionally set remoteuser and containeruser in .devcontainer.json to "root".
1. You can find a full sample implementation in the repo provided [https://github.com/DanielMeixner/demo-AzureSecrets/](https://github.com/DanielMeixner/demo-AzureSecrets/)

## This is how it works
- Under the hood the script simply uses the Azure Service Principal to reach out to the Azure Keyvault. Therefore some string operations are required to get username, password, tenant from the GitHub secret in the first place. (You can see them in the script file)
- Then for every secret found a new environment variable will be exported to the .bashrc file using the export command and bashrc will be reloaded.
- The environment variable will be all upper case and - will be replaced with _

## Try yourself
- If you want to give it a try, just fork the [demo repo](https://github.com/DanielMeixner/demo-AzureSecrets/).
- Then follow the steps above. Make sure you provide access to your GitHub Codespaces repo to your repo.
- For demo purposes add a secret in your keyvault instance. Name the secret pw.
- Open the demo in a Codespace
- To check if everything worked, just run this on the commandline to start the demo app:
```
node index.js
```
If everything worked, you should see that you get the secret value returned.

> **Hint**
>
>  Never store secrets in files which are in danger to be committed to source control. In our case the secrets are stored in .bashrc which is not in the working tree per default. If you have an idea how to get it done without even writing to a file let me know. 





