---
title: 'PowerShell-parancsfájl: új Azure-adatmegosztás létrehozása'
description: Ez a PowerShell-parancsfájl egy új adatmegosztást hoz létre egy meglévő adatmegosztási fiókon belül.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221349"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Adatmegosztás létrehozása az Azure-ban a PowerShell használatával

Ez a PowerShell-parancsfájl egy új adatmegosztást hoz létre egy meglévő adatmegosztási fiókon belül.

## <a name="sample-script"></a>Példaszkript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Új – AzDataShare](/powershell/module/az.datashare/new-azdatashare) | Létrehoz egy adatmegosztást. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
