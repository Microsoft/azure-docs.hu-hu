---
title: Azure CLI-példaszkript – Virtuális gép létrehozása felügyelt lemez csatlakoztatásával operációsrendszer-lemezként | Microsoft Docs
description: Azure CLI-példaszkript – Virtuális gép létrehozása felügyelt lemez csatlakoztatásával operációsrendszer-lemezként
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 3ec3d152864ab8caf9f0c68966b6d4f8fd9e64dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301919"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Virtuális gép létrehozása meglévő felügyelt operációsrendszer-lemezből a CLI használatával

Ez a szkript úgy hoz létre egy virtuális gépet, hogy operációsrendszer-lemezként csatlakoztat egy már létező felügyelt lemezt. Ezt a szkriptet az alábbi műveletekhez lehet alkalmazni:
* Virtuális gép létrehozása egy olyan meglévő felügyelt operációsrendszer-lemezről, amely egy másik előfizetésben található felügyelt lemezről lett átmásolva
* Virtuális gép létrehozása egy olyan meglévő felügyelt lemezről, amely egy speciális VHD-fájlból lett létrehozva 
* Virtuális gép létrehozása egy olyan meglévő felügyelt operációsrendszer-lemezről, amely egy pillanatképből lett létrehozva 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja a felügyelt lemez tulajdonságainak lekéréséhez, egy felügyelt lemez új virtuális géphez csatlakoztatásához, illetve egy virtuális gép létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Lekérdezi a felügyelt lemez tulajdonságait a lemeznév és az erőforráscsoport neve alapján. A felügyelt lemezt az „Id” tulajdonság használatával csatlakoztatja egy új virtuális géphez. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Létrehoz egy virtuális gépet egy felügyelt operációsrendszer-lemezből. |
## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
