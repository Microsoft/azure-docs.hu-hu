---
title: Azure CLI – Virtuális gép létrehozása és ellenőrzése tesztkörnyezetben
description: Ez az Azure CLI-példaszkript létrehoz egy virtuális gépet egy tesztkörnyezetben, majd ellenőrzi, hogy elérhető-e.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 779ddee7da4248ce11b906c2a4736fe7851603cf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786746"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép létrehozása és elérhetőségének ellenőrzése Azure DevTest Labs tesztkörnyezetben az Azure CLI használatával

Ez az Azure CLI-példaszkript létrehoz egy tesztkörnyezetben lévő virtuális gépet (VM). A virtuális gép egy SSH-hitelesítéssel rendelkező, Marketplace-ről származó rendszerkép alapján jön létre. Ezután a szkript ellenőrzi, hogy használható-e a virtuális gép. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create) | Létrehoz egy virtuális gépet (VM) egy tesztkörnyezetben. |
| [az lab vm show](/cli/azure/lab/vm#az_lab_vm_show) | Megjeleníti a tesztkörnyezetben lévő virtuális gép állapotát. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Lab Services CLI-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.
