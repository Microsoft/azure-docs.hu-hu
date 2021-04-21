---
title: Azure CLI-példaszkript – Virtuális gép elindítása egy tesztkörnyezetben | Microsoft Docs
description: Ez az Azure CLI-példaszkript elindít egy virtuális gépet az Azure DevTest Labs tesztkörnyezetben.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f82d43fc212da034d978ee3d65236d806daeb008
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764980"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép indítása Azure DevTest Labs tesztkörnyezetben az Azure CLI használatával

Ez az Azure CLI-példaszkript elindít egy virtuális gépet (VM) a tesztkörnyezetben. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az lab vm start](/cli/azure/lab/vm#az_lab_vm_start) | Elindít egy virtuális gépet (VM) egy tesztkörnyezetben. A művelet elvégzése eltarthat egy ideig. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Lab Services CLI-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.
