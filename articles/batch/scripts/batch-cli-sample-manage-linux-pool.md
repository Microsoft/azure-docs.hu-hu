---
title: Azure CLI-példaszkript – Linux-készletek a Batch szolgáltatásban | Microsoft Docs
description: Azure CLI-példaszkript – Linux-készletek létrehozása és kezelése a Batch szolgáltatásban
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 0ebda53db852a613c2ad6f0e223c25b3946b7116
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474275"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-példa: Az Azure Batch egy Linux-készlet létrehozása és kezelése

A szkript bemutat az Azure CLI elérhető parancsai közül néhányat, amelyekkel létrehozható és kezelhető egy Linux-készlet az Azure Batch szolgáltatásban.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az-batch-pool-node-agent-skus-list) | Felsorolja a csomóponti ügynök elérhető SKU-inak és rendszerképeinek adatait.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Létrehozza számítási csomópontok egy készletét.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | Átméretezi a futó virtuális gépek számát a megadott készletben.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Megjeleníti egy készlet tulajdonságait.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | Felsorolja a megadott készlet összes számítási csomópontját.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | Újraindítja a megadott számítási csomópontot.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | Törli a felsorolt csomópontokat a megadott készletből.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
