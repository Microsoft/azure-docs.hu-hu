---
title: PowerShell szkript – Azure-SSIS integrációs modul üzembe helyezése |} A Microsoft Docs
description: A PowerShell-példaszkript létrehoz egy Azure-SSIS integrációs modult, amely SSIS-csomagokat is futtathatók a felhőben.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a2d53ddb9f55942cd6ad396d42cc926f20c2d396
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66166357"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell szkript – Azure-SSIS integrációs modul üzembe helyezése

A PowerShell-példaszkript létrehoz egy Azure-SSIS integrációs modult az SSIS-csomagok az Azure-ban futtatható.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő parancsot használhatja, ha az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás eltávolítása:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Az erőforráscsoport az adat-előállító eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Létrehoz egy Azure-SSIS integrációs modult, amely SSIS-csomagokat is futtathatók a felhőben |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Az Azure-SSIS integrációs modul elindul. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Az Azure-SSIS integrációs modul adatainak beolvasása. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-minták](../samples-powershell.md).
