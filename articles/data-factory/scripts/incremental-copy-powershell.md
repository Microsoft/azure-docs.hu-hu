---
title: Az adatterhelés növekményes betöltése a PowerShell használatával
description: Ez a PowerShell-szkript azt mutatja be, hogyan használhatók a Azure Data Factory az adatok növekményes másolásához egy Azure SQL Databaseból egy Azure-Blob Storageba.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 736403696ec340e547547458cb62e243e6e660b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389841"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell-parancsfájl – az adatgyűjtés növekményes betöltése a Azure Data Factory használatával

Ez a PowerShell-parancsfájl csak az új vagy frissített rekordokat tölti be a forrás adattárolóból a fogadó adattárba, miután a forrásról a fogadóba másolta az adatok kezdeti teljes másolatát.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Tekintse meg a minta futtatásához szükséges előfeltételeket [ismertető oktatóanyagot: növekményes másolás](../tutorial-incremental-copy-powershell.md#prerequisites) . 

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript olyan JSON-fájlokat hoz létre, amelyek meghatározzák Data Factory entitásokat (társított szolgáltatás, adatkészlet és folyamat) a merevlemezen a c:\ mappa.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Létrehoz egy társított szolgáltatást az adatelőállítóban. A társított szolgáltatások adattárakhoz vagy számítási feladatokhoz kapcsolódnak egy adatgyárban. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Létrehoz egy adatkészletet az adat-előállítóban. Az adatkészlet egy folyamat egy tevékenységének bemenetét/kimenetét jelöli. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Folyamat létrehozása az adatelőállítóban. Egy folyamat egy vagy több olyan tevékenységet tartalmaz, amely egy bizonyos műveletet hajt végre. Ebben a folyamatban a másolási tevékenységek az egyik helyről egy másik helyre másolják az adatok egy Azure-Blob Storage. |
| [Meghívás – AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Létrehoz egy futtatást a folyamathoz. Más szóval a folyamat futtatása folyamatban van. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A folyamaton belüli tevékenység futtatásának (tevékenység futtatása) részleteinek beolvasása. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Data Factory PowerShell-szkriptek is találhatók a [Azure Data Factory PowerShell-parancsfájlokban](../samples-powershell.md).