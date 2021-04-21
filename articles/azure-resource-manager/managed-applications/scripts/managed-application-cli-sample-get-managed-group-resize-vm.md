---
title: Felügyelt erőforráscsoport le & virtuális gépek átméretezése – Azure CLI
description: Azure CLI-példaszk szkriptet biztosít, amely lekért egy felügyelt erőforráscsoportot egy Azure-beli felügyelt alkalmazásban. A szkript átméretezi a virtuális gépeket.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 179b1b64656d3f97778e183d57797e4b3660fece
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775438"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Felügyelt erőforráscsoport erőforrásainak lekérése és virtuális gépek átméretezése az Azure CLI használatával

Ez a parancsfájl erőforrásokat kér le egy felügyelt erőforráscsoportból, és átméretezi az erőforráscsoportban található virtuális gépeket.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az_managedapp_list) | Listázza a felügyelt alkalmazásokat. Lekérdezési értékeket ad meg az eredmények szűréséhez. |
| [az resource list](/cli/azure/resource#az_resource_list) | Listázza az erőforrásokat. Erőforráscsoportot és lekérdezési értékeket ad meg az eredmények szűréséhez. |
| [az vm resize](/cli/azure/vm#az_vm_resize) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
