Comparing Bicep against ARM and Azure CLI/PowerShell/TerraForm/Pulumi
=====================================================================

TL;DR - Go with Bicep. Because it has the upsides of a declarative language, without the downsides, and is the first language to provide support for the latest changes in Azure ARM API because it _is_ ARM ultimately.
Discuss :)

# 1. Introduction

If you want to provision resources in Azure and don't want to do that in the Azure portal (because you're doing [infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_code) - thumbs up!), you've probably come across these typical techniques to do that:

- [Azure Resource Manager (ARM) templates](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview)
- [Azure PowerShell commandlets](https://docs.microsoft.com/en-us/powershell/azure/?view=azps-5.9.0)
- [Azure Command Line Interface (CLI)](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
- [Terraform](https://www.terraform.io/)
- [Pulumi](https://www.pulumi.com/)

And now the latest addition to this collection:

- [Project Bicep](https://github.com/Azure/bicep)

This document aims to help you guide in case you you want to decide which interface to use going forward. And whereas all options will get the job done and there can be very good arguments to go with either of them, I am a proponent of Project Bicep.

# 2. Characteristics to compare

The following characteristics of the options will be compared:

1. Declarative vs Imperative nature of the language
2. Modularity
3. Familiar programming constructs
4. Support for latest APIs of the Azure Services
5. Tools

## 2.1 Declarative vs Imperative nature of the language

The big differientiatior between ARM templates, Terraform [@CHECK] and Bicep versus PowerShell, CLI and Pulumi is that the former are [declarative langues](https://en.wikipedia.org/wiki/Declarative_programming), whereas the latter are [imperative languages](https://en.wikipedia.org/wiki/Imperative_programming). Whereas this may perhaps seem like a small difference, the impact of this is profound.

First, let's look at provisioning a virtual machine in both paradigms.

ARM templates - which are in fact JSON templates:
```JSON
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    "name": "myVM",
    "location": "[parameters('location')]",
    "properties": {
        ...
    }
}
```

Azure CLI:
```bash
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser
```

And PowerShell
```PowerShell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

The first example, the ARM template, is an explicit statement where one declares "This is what my infrastructure needs to look like. A VM will be present with these properties, now go fix". This JSON document is sent to the ARM API in Azure and Azure will 'make it so'.
The CLI example shows that you're sending commands "Create VM for me with the following specifications".

As mentioned, these different paradigms have huge implications on the following:

- Parallelization of deployment
- Template validation against policies
- Idempotency
- Preventing configuration drift

Let's look at these in a bit more detail:

### 2.1.1Parallelization of deployment
Because of nature if imperative, where it's instruction after instruction - cannot parallelize.
[TODO]

### 2.1.2 Template validation against policies
Deployment is a series of instructions and so ARM cannot reason about the endstate of the system. Therefore you cannot do pre-flight checks like ARM template validation against policies.
In fact - it can be very hard if not impossible to deploy a complicated infrastructure adhering to policies with CLI, because in order to make something compliant, you may need more than 1 command.
[TODO]

### 2.1.3 Idempotency
[Idempotency](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview#why-choose-arm-templates) is important for infrastructure as code deployments because you want to do them regularly and be assured that the outcome is always the same. Whereas a lot of Azure CLI commands are idempotent, it is not guaranteed and not always the case. 

[EXAMPLE NEEDED - VNETS PERHAPS?]
[TODO]

### 2.1.4 Preventing configuration drift
Many benefits: environment configuration guaranteed - and so environment parity, security.
[EXAMPLE NEEDED - RBAC RULES]
[TODO]


## 2.2 Modularity
Composing your infrastructure as code documents from multiple files or modules is a very elegant way to reduce complexity, re-usability (and promote sharing), maintainablility etc. So modularity is very important especially for larger deployments but also in organizations where you want to share leading practices and save teams for inventing wheels over-again.
[Inner sourcing]

By far the worst in this regard are the ARM templates. ARM does have the notion of a [linked template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/linked-templates?tabs=azure-powershell), which looks like this:

```JSON
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

As you can see, the 'linked template' resides in a a storage account (public one in this case), which you can lock down with SAS keys but it's going to be cumbersome to deploy these, manage security etc. etc. The underlying reason for this is that the 'main' ARM template is sent to the public Azure API which handles the deployment. And this public service needs to pull this linked template from a public location for the deployment.

All other languages do a much better job:
- [Bicep has Bicep Modules](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/bicep-modules)
- [Terraform has Modules](https://www.terraform.io/docs/language/modules/develop/index.html)
- Azure CLI and Azure PowerShell you can make into multiple files and call the files from a 'main' script. Or wrap them in bash or PowerShell module constructs of course.

Consuming Bicep modules looks like this:

```Bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

Where the definition of the storageaccount is in a separate `.bicep` file.

## 2.3 Familiar programming constructs
Programming constructs used for control flow in Azure CLI, PowerShell, Terraform and Bicep, are likely to be more intuitive for developers that are already used to working in imperative languages like JAVA, C#, JavaScript, etc.

[Actually seem pretty similair - not really USP]

## 2.4 Support for latest APIs of the Azure Services
ARM and Bicep talk directly to the ARM API. Other CLIs need to be updated first. Sure, there are workarounds (direct HTTP calls from CLI) and the update of the CLIs typically does not take that long, but there is a delay especially for preview features.


## 2.5 Tools
All excellent IMHO

[LIST OF LINKS]



# Conclusion
[TODO]