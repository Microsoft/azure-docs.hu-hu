---
title: Az Azure SSIS Integration Runtime üzembe helyezése a PowerShell használatával
description: Ez a PowerShell-szkript létrehoz egy Azure-SSIS integrációs modult, amely képes SSIS-csomagokat futtatni a felhőben.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74929820"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell-parancsfájl – Azure-SSIS integrációs modul üzembe helyezése

Ez a PowerShell-szkript létrehoz egy Azure-SSIS integrációs modult, amely képes futtatni a SSIS-csomagokat az Azure-ban.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A minta parancsfájl futtatása után a következő paranccsal távolíthatja el az erőforráscsoportot és az ahhoz társított összes erőforrást:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
A következő parancs futtatásával távolíthatja el az adatgyárat az erőforráscsoporthoz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Létrehoz egy Azure-SSIS integrációs modult, amely képes SSIS-csomagokat futtatni a felhőben |
| [Start – AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Elindítja az Azure-SSIS integrációs modult. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Információk beolvasása az Azure-SSIS Integration Runtime szolgáltatással kapcsolatban. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell-szkriptek is találhatók a [Azure Data Factory PowerShell-mintákban](../samples-powershell.md).
