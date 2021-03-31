---
title: BLOB-tároló méretének kiszámítása a PowerShell-lel
titleSuffix: Azure Storage
description: Egy tároló méretének kiszámítása az Azure Blob Storageban az egyes Blobok méretének összesítésével.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87ef18530c549396b7d8fe1ec4ff0e08cb8535e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784275"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>BLOB-tároló méretének kiszámítása a PowerShell-lel

Ez a szkript kiszámítja egy tároló méretének kiszámítását az Azure Blob Storage a tárolóban lévő Blobok méretének összesítésével.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ez a PowerShell-parancsfájl a tároló becsült méretét biztosítja, és nem használható a számlázási számításokhoz. Egy olyan parancsfájl esetében, amely kiszámítja a tároló méretét a számlázási célokra, tekintse meg [a blob Storage-tároló méretének kiszámítása számlázási célokra](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)című témakört.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a tároló és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a Blob Storage-tároló méretének kiszámításához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | A megadott Storage-fiók vagy az erőforráscsoport vagy az előfizetés összes tárolási fiókjának beolvasása. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Egy tárolóban lévő Blobok felsorolása. |

## <a name="next-steps"></a>Következő lépések

Egy olyan parancsfájl esetében, amely kiszámítja a tároló méretét a számlázási célokra, tekintse meg [a blob Storage-tároló méretének kiszámítása számlázási célokra](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)című témakört.

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Storage PowerShell-szkriptek az Azure Storage-hoz készült [PowerShell-mintákban](../blobs/storage-samples-blobs-powershell.md)találhatók.
