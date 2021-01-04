---
layout: post
title: Azure Resource Manager
---
PowerShell has been crucial to my career, I've posted a lot about it in the past on other blogs that are long since gone. However I started a project in January that was looking to deploy full environments rapidly, and deploy VMs at scale. The Azure PowerShell tools are more than capable of doing this, but what I found was that I had to write more error handling and validation for values.
Enter Azure Resource Manager (ARM) templates.
I have noticed while learning how to write, debug, and modify the templates how there seems to be a lot of people taking sides about which method to use; it feels a lot like the arguments between using straight PowerShell verses Desired State Configuration (DSC). I see both tools used differently, for me I am writing ARM templates for deployments and PowerShell for updates and modifications to deployed resources. The main reason for this is because Azure handles does all of the error handling. For instance if I am deploying a Virtual Machine that needs a Storage Account and the storage account fails it will still try to deploy the VM which will also fail.
I know most people say that you can just:
```powershell
$ErrorPreference = 'Stop'
```
