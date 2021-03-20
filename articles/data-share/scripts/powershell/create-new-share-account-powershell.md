---
title: 'PowerShell-parancsfájl: új Azure-beli adatmegosztási fiók létrehozása'
description: Ez a PowerShell-szkript egy új adatmegosztási fiókot hoz létre.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4608afc16398a4cd1fa47ee1ae2cd4a4489a3f96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221366"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Adatmegosztási fiók létrehozása az Azure-ban a PowerShell használatával

Ez a PowerShell-szkript egy új adatmegosztási fiókot hoz létre. 

## <a name="sample-script"></a>Példaszkript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Új – AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount) | Létrehoz egy adatmegosztási fiókot. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
