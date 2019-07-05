---
title: Azure Automation-runbook egy Azure Resource Manager-sablon üzembe helyezése
description: Az Azure Storage tárolja egy runbookból egy Azure Resource Manager-sablon üzembe helyezése
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, a runbook, json, az azure automation
ms.openlocfilehash: d9fab97c8c42c7ff7993c3d7203deb8a58a10bc9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476712"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Azure Resource Manager-sablon üzembe helyezése Azure Automation PowerShell-runbookban

Írhat egy [Azure Automation PowerShell-runbookban](automation-first-runbook-textual-powershell.md) használatával telepít, amely egy Azure-erőforrás- [Azure Resource Management-sablonnal](../azure-resource-manager/resource-manager-create-first-template.md).

Ezáltal a segítségével automatizálhatja az üzembe helyezés Azure-erőforrások. Akkor is fenntartható egy központi, biztonságos helyen – például az Azure Storage Resource Manager-sablonokkal.

Ebben a cikkben létrehozunk egy PowerShell-forgatókönyvet, amely tárolja a Resource Manager-sablonnal használ [Azure Storage](../storage/common/storage-introduction.md) egy új Azure Storage-fiók üzembe helyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége van a következő elemek:

* Egy Azure-előfizetés. Ha Ön még nem rendelkezik, akkor [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* [Az Azure Storage-fiók](../storage/common/storage-create-storage-account.md) , amely tárolja a Resource Manager-sablon
* Az Azure Powershell telepítve a helyi gépen. Lásd: [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) beszerzése az Azure PowerShell-lel kapcsolatos információkat.

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a példában egy Resource Manager-sablon, amely üzembe helyezi egy új Azure Storage-fiókot használ.

Egy szövegszerkesztőben másolja a következő szöveget:

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

Mentse a fájlt helyileg `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>A Resource Manager-sablon mentése az Azure Storage-ban

Most használjuk a PowerShell Azure Storage-fájlmegosztás létrehozása, és töltse fel a `TemplateTest.json` fájlt.
Hozzon létre egy fájlt való megosztása, és töltsön fel egy fájlt az Azure Portalon, lásd: [a Windows Azure File storage használatának első lépései](../storage/files/storage-dotnet-how-to-use-files.md).

Indítsa el a Powershellt a helyi gépen, és futtassa a következő parancsokat, hozzon létre egy fájlmegosztást, és töltse fel a Resource Manager-sablon fájlmegosztásra.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>A forgatókönyv PowerShell-parancsprogram létrehozása

Most létrehozunk egy PowerShell-parancsprogram, amely lekérdezi a `TemplateTest.json` fájlt az Azure Storage-ból, és üzembe helyezi a sablont egy új Azure Storage-fiók létrehozásához.

Egy szövegszerkesztőben másolja a következő szöveget:

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
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Mentse a fájlt helyileg `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importálása és a runbook közzététele az Azure Automation-fiókba

Most már tudjuk a runbook importálása az Azure Automation-fiókját a PowerShell használatával, és tegye közzé a runbookot.
Importálhatja és teheti közzé egy runbookot az Azure Portalon kapcsolatos információkért lásd: [kezelése az Azure Automation runbookjai](manage-runbooks.md).

Az importálandó `DeployTemplate.ps1` az Automation-fiók, egy PowerShell-forgatókönyvet, futtassa a következő PowerShell-parancsokat:

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
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>A runbook indítása

Most kezdődik meg a runbook meghívása a [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmagot.

Runbook indítása az Azure Portalon kapcsolatos információkért lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md).

Futtassa az alábbi parancsokat a PowerShell-konzolon:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

A runbook fut, és futtatásával ellenőrizheti annak állapotát `$job.Status`.

A runbook lekéri a Resource Manager-sablon és a egy új Azure Storage-fiók üzembe helyezése segítségével.
Láthatja, hogy az új storage-fiók létrejött-e a következő parancs futtatásával:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Összefoglalás

Ennyi az egész! Most már az Azure Automation és az Azure Storage és a Resource Manager-sablonok segítségével az Azure-erőforrások üzembe helyezése.

## <a name="next-steps"></a>További lépések

* Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md)
* Az Azure Storage használatának első lépései, lásd: [Azure Storage bemutatása](../storage/common/storage-introduction.md).
* Egyéb hasznos az Azure Automation-runbookok talál [Azure Automation forgatókönyv- és galériák](automation-runbook-gallery.md).
* Egyéb hasznos Resource Manager-sablonokkal kapcsolatban [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/)


