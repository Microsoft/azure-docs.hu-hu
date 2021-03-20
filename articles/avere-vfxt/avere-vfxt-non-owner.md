---
title: Avere vFXT nem tulajdonosi megkerülő megoldás – Azure
description: Megkerülő megoldás, amely lehetővé teszi, hogy a felhasználók előfizetés-tulajdonos engedélye nélkül avere vFXT az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505375"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Az Avere vFXT nem tulajdonosi üzembe helyezésének engedélyezése

Ezek az utasítások egy megkerülő megoldás, amely lehetővé teszi, hogy a felhasználó előfizetés-tulajdonosi jogosultság nélkül hozzon létre egy avere-vFXT az Azure System számára

(A avere vFXT rendszer központi telepítésének ajánlott módja, ha egy tulajdonosi jogosultsággal rendelkező felhasználó rendelkezik a létrehozás lépéseivel, ahogy azt a [felkészülés a avere vFXT létrehozására](avere-vfxt-prereqs.md)című cikk ismerteti.)  

A megkerülő megoldás olyan további hozzáférési szerepkör létrehozásával jár, amely megfelelő jogosultságot biztosít a felhasználóknak a fürt telepítéséhez. A szerepkört egy előfizetés tulajdonosának kell létrehoznia, és a tulajdonosnak hozzá kell rendelnie a megfelelő felhasználókhoz.

Az előfizetéshez tartozó tulajdonosnak el kell fogadnia a avere vFXT Marketplace-rendszerkép [használati feltételeit](avere-vfxt-prereqs.md) is.

> [!IMPORTANT]
> Ezeket a lépéseket a fürthöz használni kívánt előfizetéshez tartozó tulajdonosi jogosultságokkal rendelkező felhasználónak kell elvégeznie.

1. Másolja ezeket a sorokat, és mentse őket fájlba (például `averecreatecluster.json` ). Használja az előfizetés-AZONOSÍTÓját az `AssignableScopes` utasításban.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Futtassa ezt a parancsot a szerepkör létrehozásához:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Példa:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Rendelje hozzá ezt a szerepkört a fürtöt létrehozó felhasználóhoz:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

A folyamat befejezése után a szerepkör minden felhasználóhoz hozzárendelte a következő engedélyeket az előfizetéshez:

* A hálózati infrastruktúra létrehozása és konfigurálása
* A fürt létrehozása
* Fürt létrehozási parancsfájljainak futtatása a fürtből a fürt létrehozásához
