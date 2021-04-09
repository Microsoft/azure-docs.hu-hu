---
title: Rövid útmutató – Azure Notification hub létrehozása az Azure CLI használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Notification hub-t az Azure CLI használatával.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c565be3aea43fc4dc86fd793e0dc2dea0b08d4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563749"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rövid útmutató: Azure Notification hub létrehozása az Azure CLI használatával

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban egy értesítési központot hoz létre az Azure CLI használatával. Az első szakasz egy Notification Hubs névtér létrehozásának lépéseit ismerteti. A második szakasz egy értesítési központ meglévő névtérben való létrehozásának lépéseit ismerteti. Azt is megtudhatja, hogyan hozhat létre egyéni hozzáférési szabályzatot.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs az Azure CLI 2.0.67 vagy újabb verzióját igényli. Futtassa az [az version](/cli/azure/reference-index#az_version) parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az [az upgrade](/cli/azure/reference-index#az_upgrade) paranccsal frissíthet.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Notification Hubsokat, például az összes Azure-erőforrást, egy erőforráscsoporthoz kell telepíteni.  Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.  Tekintse meg, [Mi az Azure Resource Manager](../azure-resource-manager/management/overview.md) az erőforráscsoportok megismeréséhez.

Ebben a rövid útmutatóban hozzon létre egy **spnhubrg** nevű erőforráscsoportot a **eastus** helyen a következő az [Group Create](/cli/azure/group#az-group-create) paranccsal.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Notification Hubs névtér létrehozása

1. Hozzon létre egy névteret az értesítési hubok számára.

   A névtér egy vagy több hubokat tartalmaz, és a névnek egyedinek kell lennie az összes Azure-előfizetésben, és legalább hat karakter hosszúnak kell lennie. A név rendelkezésre állásának ellenőrzését az az [Notification-hub névtér-ellenőrzési](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) szolgáltatás elérhetősége paranccsal tekintheti meg.

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Az Azure CLI a következő konzol kimenetének megjelenítésével válaszol a rendelkezésre állásra vonatkozó kérelmére:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Figyelje meg az Azure CLI válaszának második sorát `"isAvailable": true` . Ebben a sorban beolvasható, hogy `false` a névtérhez megadott kívánt név nem érhető-e el. Miután megerősítette a név rendelkezésre állását, futtassa az az [Notification-hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) parancsot a névtér létrehozásához.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Ha az `--name` Ön által megadott `az notification-hub namespace create` parancs nem érhető el, vagy nem felel meg az [Azure-erőforrások elnevezési szabályainak és korlátozásának](../azure-resource-manager/management/resource-name-rules.md), az Azure CLI a következő kimenettel válaszol:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Ha az utónév nem sikerült, válasszon másik nevet az új névtérnek, és futtassa `az notification-hub namespace create` újra a parancsot.

   > [!NOTE]
   > Ettől a lépéstől kezdve le kell cserélnie a `--namespace` paraméter értékét minden olyan Azure CLI-parancsban, amelyet ebből a rövid útmutatóból másol.

2. A névterek listájának beolvasása.

   Az új névtér részleteinek megtekintéséhez használja az az [Notification-hub Namespace List](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) parancsot. A `--resource-group` paraméter megadása nem kötelező, ha az előfizetéshez tartozó összes névteret meg szeretné jeleníteni.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Értesítési központok létrehozása

1. Hozza létre első értesítési központját.

   Mostantól egy vagy több értesítési központ is létrehozható az új névtérben. Futtassa az az [Notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) parancsot egy értesítési központ létrehozásához.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Hozzon létre egy második értesítési központot.

   Egyetlen névtérben több értesítési központ is létrehozható. Ha egy második értesítési központot szeretne létrehozni ugyanabban a névtérben, futtassa `az notification-hub create` újra a parancsot egy másik hub-névvel.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Az értesítési központok listájának beolvasása.

   Az Azure CLI az egyes végrehajtott parancsokkal kapcsolatos sikeres vagy hibaüzenetet ad vissza. az értesítési központok listájának lekérdezése azonban megnyugtató lehet. Az az [Notification-hub List](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) parancs erre a célra lett tervezve.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Hozzáférési szabályzatok használata

1. Az Azure Notification Hubs a hozzáférési házirendek használatával [közös hozzáférés-aláírási biztonságot](./notification-hubs-push-notification-security.md) használ. Értesítési központ létrehozásakor a rendszer automatikusan létrehoz két házirendet. A leküldéses értesítések konfigurálásához a szabályzatok kapcsolódási karakterláncai szükségesek. Az az [Notification-hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) parancs a szabályzatok és a hozzájuk tartozó erőforráscsoportok listáját tartalmazza.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Ne használja az _DefaultFullSharedAccessSignature_ szabályzatot az alkalmazásban. Ezt a házirendet csak háttérbeli használatra szántuk. Csak `Listen` hozzáférési házirendeket használjon az ügyfélalkalmazás számára.

2. Ha további engedélyezési szabályokat szeretne létrehozni értelmes nevekkel, a saját hozzáférési házirendjét az az [Notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) paranccsal hozhatja létre és szabhatja testre. A `--rights` paraméter a hozzárendelni kívánt engedélyek szóközzel tagolt listája.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Az egyes hozzáférési házirendekhez két kulcs és kapcsolati karakterlánc van. Az [értesítési központ konfigurálásához](./configure-notification-hub-portal-pns-settings.md)később szüksége lesz rájuk. Egy Notification Hubs hozzáférési szabályzat kulcsainak és kapcsolati karakterláncának listázásához használja az az [Notification-hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsot.

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > A [Notification Hubs névtér](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) és az [értesítési központ](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) külön hozzáférési házirendekkel rendelkezik. Győződjön meg arról, hogy a megfelelő Azure CLI-hivatkozást használja a kulcsok és a kapcsolódási karakterláncok lekérdezéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Következő lépések

* Ebben a rövid útmutatóban létrehozott egy értesítési központot. Ha szeretné megtudni, hogyan konfigurálhatja az elosztót a platform Notification System (PNS) beállításokkal, olvassa el a [leküldéses értesítések beállítása értesítési központban](configure-notification-hub-portal-pns-settings.md) című témakört.

* Ismerje meg az értesítési központok Azure CLI-vel való kezelésének széles körű képességeit:

  [Notification Hubs teljes referenciák listája](/cli/azure/ext/notification-hub/notification-hub)

  [Notification Hubs névtér-hivatkozási lista](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Notification Hubs engedélyezési szabály hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs hitelesítő adatok hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/credential)