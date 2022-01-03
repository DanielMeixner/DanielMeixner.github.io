---
layout: post
title: az ad sp create-for-rbac -n - Values of identifierUris ...
permalink: /az-ad-sp-create-for-rbac-identifierUris-error
---

If you are running into the message 
```
Values of identifierUris property must use a verified domain of the organization or its subdomain
```
after executing "az ad sp create-for-rbac" in Azure CLI, try updating your Azure CLI version. 
Versions starting with 2.25 should fix the issue. More on this [here](https://github.com/Azure/azure-cli/issues/19892)


To check your version run

```
az version
```

To upgrade your CLI just run
```
az upgrade
```

In case az upgrade doesn't work for you, you are on a version too old, so you have to [install the upgrade manually](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli).

If you're on an new version there's also a way to automatically stay up to date:
```
az config set auto-upgrade.enable=yes
```


