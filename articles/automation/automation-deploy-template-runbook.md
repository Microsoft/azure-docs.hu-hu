---
title: Azure Resource Manager-sablon üzembe helyezése Azure Automation PowerShell-runbookban
description: Ez a cikk azt ismerteti, hogyan helyezhet üzembe Azure Resource Manager Azure Storage-ban tárolt sablont egy PowerShell-runbookból.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 20dbf9f9bbf97ed0c24ea3a525c56c7cde2db428
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834839"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Alkalmazássablon Azure Resource Manager PowerShell-runbookban

Olyan [PowerShell-runbookot Azure Automation,](./learn/automation-tutorial-runbook-textual-powershell.md) amely egy Azure-erőforrást helyez üzembe egy [Azure Resource Management-sablon használatával.](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) A sablonokkal automatizálhatja az Azure Azure Automation üzembe helyezését. A sablonokat Resource Manager, biztonságos helyen, például az Azure Storage-ban tarthatja karban.

Ebben a cikkben létrehozunk egy PowerShell-runbookot, amely egy Azure Storage Resource Manager ban tárolt sablont használ egy új Azure Storage-fiók üzembe helyezéséhez. [](../storage/common/storage-introduction.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, aktiválhatja [MSDN-előfizetői](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) előnyeit, vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/)
* [Automation-fiók](./automation-security-overview.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* [Az Azure Storage-fiók,](../storage/common/storage-account-create.md) amelyben a sablont Resource Manager tárolni.
* Azure PowerShell helyi gépen van telepítve. A [modul telepítésének Azure PowerShell lásd:](/powershell/azure/install-az-ps) Install the Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a példában egy új azure Resource Manager fiókot üzembe helyező sablont használunk.

Egy szövegszerkesztőben másolja ki a következő szöveget:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Mentse helyileg a fájltTemplateTest.js **fájlba.**

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Az Resource Manager mentése az Azure Storage-ban

Most a PowerShell használatával létrehozunk egy Azure Storage-fájlmegosztást, és feltöltjük **TemplateTest.jsfájlt.** A fájlmegosztások létrehozására és fájlok feltöltésre vonatkozó utasításokért lásd a Azure Portal Azure File Storage Windows rendszeren való használatának első [útmutatóját.](../storage/files/storage-dotnet-how-to-use-files.md)

Indítsa el a PowerShellt a helyi gépen, és futtassa a következő parancsokat egy fájlmegosztás létrehozásához, és töltse fel a Resource Manager sablont erre a fájlmegosztásra.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>A PowerShell-runbook parancsfájljának létrehozása

Most létrehozunk egy PowerShell-szkriptet, amely lekérte aTemplateTest.jsaz Azure Storage-ból, és üzembe helyezheti a sablont egy új Azure Storage-fiók létrehozásához. 

Egy szövegszerkesztőbe illessze be a következő szöveget:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

Mentse helyileg a fájlt **DeployTemplate.ps1.**

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>A runbook importálása és közzététele a Azure Automation fiókjában

Most a PowerShell használatával importáljuk a runbookot a Azure Automation-fiókjába, majd közzétejük a runbookot. További információ a runbookok importálásról és közzétételéről a [Azure Portal: Runbookok](manage-runbooks.md)kezelése a Azure Automation.

Az **automationDeployTemplate.ps1fiókba** PowerShell-runbookként való importálásához futtassa a következő PowerShell-parancsokat:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>A runbook indítása

Most elindítjuk a runbookot a [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmag hívásával. További információ a runbookok futtatásáról a Azure Portal: [Starting a runbook in Azure Automation.](./start-runbooks.md)

Futtassa a következő parancsokat a PowerShell-konzolon:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

A runbook futtatása után a feladatobjektum tulajdonságértékének leolvasásával ellenőrizheti annak `$job.Status` állapotát.

A runbook le Resource Manager sablont, és egy új Azure Storage-fiók üzembe helyezéséhez használja. A következő parancs futtatásával láthatja, hogy az új tárfiók létrejött:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Következő lépések

* A sablonokkal kapcsolatos további Resource Manager lásd a Azure Resource Manager [áttekintését.](../azure-resource-manager/management/overview.md)
* Az Azure Storage használatának első lépésekhez lásd: [Az Azure Storage bemutatása.](../storage/common/storage-introduction.md)
* További hasznos forgatókönyveket Azure Automation a [Runbookok](automation-runbook-gallery.md)és modulok használata a Azure Automation.
* A PowerShell-parancsmagok referenciáiért lásd: [Az.Automation.](/powershell/module/az.automation#automation)
