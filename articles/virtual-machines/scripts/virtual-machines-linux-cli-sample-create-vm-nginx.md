---
title: Azure CLI-példaszkript – Linux rendszerű virtuális gép létrehozása NGINX-szel
description: Azure CLI-példaszkript – Linux rendszerű virtuális gép létrehozása NGINX-szel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 00bbc74a78be93dd7167625fa98aa7f9500c1be2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502069"
---
# <a name="create-a-vm-with-nginx"></a>Virtuális gép létrehozása NGINX-szel

Ez a szkript egy Azure-beli virtuális gépet hoz létre, és az Azure Virtual Machine egyéni szkriptbővítménnyel telepíti az NGINX-et. A szkript futtatása után Ön hozzáférhet egy bemutató webhelyhez a virtuális gép nyilvános IP-címén.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az egyéni szkriptbővítmény átmásolja ezt a szkriptet a virtuális gépre. Ezután a szkript futtatásával telepíthető és konfigurálható egy NGINX-webiszolgáló.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vm create](/cli/azure/vm) | Létrehozza a virtuális gépet. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az vm open-port](/cli/azure/network/nsg/rule) | Létrehoz egy hálózati biztonsági csoportra vonatkozó szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 80-as portot nyitjuk meg a HTTP-forgalom számára. |
| [azure vm extension set](/cli/azure/vm/extension) | Hozzáad és futtat egy virtuálisgép-bővítményt egy virtuális gépen. Ebben a példában az NGINX telepítésére az egyéni szkriptbővítmény használatával kerül sor.|
| [az group delete](/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
