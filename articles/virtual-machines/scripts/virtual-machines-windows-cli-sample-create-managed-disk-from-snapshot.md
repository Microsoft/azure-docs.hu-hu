---
title: Felügyelt lemez létrehozása pillanatképből – CLI-minta
description: Azure CLI-példaszkript – Felügyelt lemez létrehozása pillanatképből
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 2d415a12ceaf2cda0172d806d5a621b1297a74be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75375850"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Felügyelt lemez létrehozása pillanatképből a CLI-vel

Ez a szkript egy felügyelt lemezt hoz létre egy pillanatképből. A használatával virtuális gépeket állíthat vissza operációsrendszer- és adatlemezek pillanatképeiből. A megfelelő pillanatképekből létrehozhatja operációs rendszerek és adatok felügyelt lemezeit, majd a felügyelt lemezek csatolásával létrehozhat egy új virtuális gépet. Meglévő virtuális gépek adatlemezeit is visszaállíthatja pillanatképekből létrehozott adatlemezek csatolásával.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja arra, hogy létrehozzon egy felügyelt lemezt egy pillanatképből. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Lekérdezi egy pillanatkép összes tulajdonságát a pillanatkép neve és erőforráscsoport-tulajdonságai alapján. A felügyelt lemezek létrehozásához a rendszer az „Id” tulajdonságot használja.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Létrehoz egy felügyelt lemezt egy felügyelt pillanatkép pillanatkép-azonosítójának használatával. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális gép és felügyelt lemezek CLI parancsfájl minták találhatók az [Azure Windows VM dokumentációban.](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
