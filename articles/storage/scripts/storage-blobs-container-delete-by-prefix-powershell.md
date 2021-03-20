---
title: Azure PowerShell szkript minta – tárolók törlése előtag alapján | Microsoft Docs
description: Itt olvashat egy példát, amely bemutatja, hogyan törölheti az Azure Blob Storage-tárolót a tároló nevében lévő előtag alapján Azure PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b95c5ab243fbd938e8a7eb1d3b9619b0d46fb046
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89072950"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Tárolók törlése a nevük előtagja alapján

Ez a szkript törli a tárolókat az Azure Blob Storage-tárolóban a tároló neve előtag alapján.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a maradék tárolók és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja a tárolók a nevük előtagja alapján történő törléséhez. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | A megadott Storage-fiók vagy az erőforráscsoport vagy az előfizetés összes tárolási fiókjának beolvasása. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Felsorolja a Storage-fiókhoz társított tároló tárolókat. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Eltávolítja a megadott Storage-tárolót. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További PowerShell-példaszkripteket az [Azure Blob Storage Azure PowerShell-példái](../blobs/storage-samples-blobs-powershell.md) között találhat.
