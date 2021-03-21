---
title: Nagy mennyiségű Adatmásolás a PowerShell használatával
description: Ez a PowerShell-parancsfájl azt mutatja be, hogyan lehet a Azure Data Factory használatával adatok másolását egy forrás adattárolóból a cél adattárolóba tömegesen.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 16cb08d49efcd46e2746c5031793af8b747a2b31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373572"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell-parancsfájl – több tábla másolása a Azure Data Factory használatával

Ez a PowerShell-parancsfájl az Azure SQL Database több táblájából másolja az Azure szinapszis Analyticsbe.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Lásd az [oktatóanyagot: tömeges másolás](../tutorial-bulk-copy.md#prerequisites) a minta futtatásának előfeltételeihez.

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript olyan JSON-fájlokat hoz létre, amelyek meghatározzák Data Factory entitásokat (társított szolgáltatás, adatkészlet és folyamat) a merevlemezen a c:\ mappa.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure Synapse Analytics")]

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

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Létrehoz egy társított szolgáltatást az adatelőállítóban. A társított szolgáltatások adattárakhoz vagy számítási feladatokhoz kapcsolódnak egy adatgyárban. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Létrehoz egy adatkészletet az adat-előállítóban. Az adatkészlet egy folyamat egy tevékenységének bemenetét/kimenetét jelöli. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Folyamat létrehozása az adatelőállítóban. Egy folyamat egy vagy több olyan tevékenységet tartalmaz, amely egy bizonyos műveletet hajt végre. Ebben a folyamatban a másolási tevékenységek az egyik helyről egy másik helyre másolják az adatok egy Azure-Blob Storage. |
| [Meghívás – AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Létrehoz egy futtatást a folyamathoz. Más szóval a folyamat futtatása folyamatban van. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | A folyamaton belüli tevékenység futtatásának (tevékenység futtatása) részleteinek beolvasása. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Data Factory PowerShell-szkriptek is találhatók a [Azure Data Factory PowerShell-parancsfájlokban](../samples-powershell.md).