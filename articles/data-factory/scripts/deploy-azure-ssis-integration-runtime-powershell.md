---
title: PowerShell szkript – Azure-SSIS integrációs modul üzembe helyezése |} A Microsoft Docs
description: A PowerShell-példaszkript létrehoz egy Azure-SSIS integrációs modult, amely SSIS-csomagokat is futtathatók a felhőben.
services: data-factory
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: douglasl
ms.openlocfilehash: 4551d0cebf51fd2c028b6b83a75863cedb32fdc3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014029"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell szkript – Azure-SSIS integrációs modul üzembe helyezése

A PowerShell-példaszkript létrehoz egy Azure-SSIS integrációs modult az SSIS-csomagok az Azure-ban futtatható.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő parancsot használhatja, ha az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás eltávolítása:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Az erőforráscsoport az adat-előállító eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | Létrehoz egy Azure-SSIS integrációs modult, amely SSIS-csomagokat is futtathatók a felhőben |
| [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | Az Azure-SSIS integrációs modul elindul. |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | Az Azure-SSIS integrációs modul adatainak beolvasása. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-minták](../samples-powershell.md).
