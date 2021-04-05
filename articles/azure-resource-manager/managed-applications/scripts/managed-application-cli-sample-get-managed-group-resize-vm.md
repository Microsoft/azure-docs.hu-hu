---
title: Felügyelt erőforráscsoport beolvasása & virtuális gépek átméretezése – Azure CLI
description: Olyan Azure CLI-parancsfájlt biztosít, amely felügyelt erőforráscsoportot kap egy Azure által felügyelt alkalmazásban. A szkript átméretezi a virtuális gépeket.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497817"
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
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Listázza a felügyelt alkalmazásokat. Lekérdezési értékeket ad meg az eredmények szűréséhez. |
| [az resource list](/cli/azure/resource#az-resource-list) | Listázza az erőforrásokat. Erőforráscsoportot és lekérdezési értékeket ad meg az eredmények szűréséhez. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
