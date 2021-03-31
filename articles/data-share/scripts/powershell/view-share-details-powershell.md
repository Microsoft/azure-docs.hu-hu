---
title: 'PowerShell-parancsfájl: meglévő megosztások listázása az Azure-beli adatmegosztásban'
description: Ez a PowerShell-parancsfájl felsorolja és megjeleníti a megosztások részleteit.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 54b4b6cedc2dc09e4a9d5c4b7b937e80750e17c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220822"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Az elküldett adatmegosztás részleteinek megtekintése a PowerShell használatával

Ez a PowerShell-parancsfájl felsorolja egy meglévő fiókból származó adatmegosztásokat, és lekéri egy adott megosztás részleteit.


## <a name="sample-script"></a>Példaszkript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | Lekérdezi és felsorolja a fiókban található megosztásokat. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
