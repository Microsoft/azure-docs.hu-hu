---
title: Azure CLI – virtuális gép leállítása és törlése tesztkörnyezetben
description: Ez a cikk egy olyan Azure CLI-parancsfájlt tartalmaz, amely leállítja és törli a virtuális gépet egy Azure DevTest Labs található laborban.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198200"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép leállítása és törlése Azure DevTest Labs tesztkörnyezetben az Azure CLI használatával

Ez az Azure CLI-példaszkript leállít és töröl egy tesztkörnyezetben lévő virtuális gépet (VM). 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Leállít egy virtuális gépet (VM) egy tesztkörnyezetben. A művelet elvégzése eltarthat egy ideig. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Töröl egy virtuális gépet (VM) egy tesztkörnyezetben. A művelet elvégzése eltarthat egy ideig. |


## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Lab Services CLI-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.
