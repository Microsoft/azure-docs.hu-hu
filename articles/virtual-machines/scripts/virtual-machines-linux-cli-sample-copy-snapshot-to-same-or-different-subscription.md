---
title: Azure CLI-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között a CLI használatával | Microsoft Docs
description: Azure CLI-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között a CLI használatával
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 2ff32bf5a8e3c5c31b13e2e8a1594f94647ed689
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695389"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Felügyelt lemez pillanatképének másolása előfizetésen belül vagy előfizetések között a CLI használatával

Ez a szkript átmásolja egy felügyelt lemez pillanatképét az előfizetésen belül vagy előfizetések között. A szkript használatával a pillanatképet a szülőpillanatképpel egy régióban lévő előfizetésekbe helyezheti át.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy, a forráspillanatkép azonosítóját használó pillanatkép a célelőfizetésben történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Lekérdezi egy pillanatkép összes tulajdonságát a pillanatkép neve és erőforráscsoport-tulajdonságai alapján. Az „Id” tulajdonság a pillanatkép másik előfizetésbe való másolásakor használatos.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Lemásol egy pillanatképet úgy, hogy a szülőpillanatkép azonosítójának és nevének használatával egy másik előfizetésben hoz létre egy pillanatképet.  |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása pillanatképből](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez és felügyelt lemezekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
