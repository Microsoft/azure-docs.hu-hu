---
title: Azure CLI – Virtuális gép leállítása és törlése tesztkörnyezetben
description: Ez a cikk egy Azure CLI-szkriptet tartalmaz, amely leállít és töröl egy virtuális gépet egy tesztkörnyezetben a Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: e30ee47c4f34fe6a71e8c934a4f36cb7edbbe20e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777314"
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
| [az lab vm stop](/cli/azure/lab/vm#az_lab_vm_stop) | Leállít egy virtuális gépet (VM) egy tesztkörnyezetben. A művelet elvégzése eltarthat egy ideig. |
| [az lab vm delete](/cli/azure/lab/vm#az_lab_vm_delete) | Töröl egy virtuális gépet (VM) egy tesztkörnyezetben. A művelet elvégzése eltarthat egy ideig. |


## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Lab Services CLI-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.
