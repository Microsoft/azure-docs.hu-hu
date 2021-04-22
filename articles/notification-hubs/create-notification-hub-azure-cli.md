---
title: Rövid útmutató – Azure értesítési központ létrehozása az Azure CLI | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure értesítési központot az Azure CLI használatával.
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
ms.openlocfilehash: d9754bb1390e242b12944b0b59595d4a4d46af33
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873576"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rövid útmutató: Azure értesítési központ létrehozása az Azure CLI használatával

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

Ebben a rövid útmutatóban egy értesítési központot hoz létre az Azure CLI használatával. Az első szakasz egy új névtér Notification Hubs be. A második szakasz egy értesítési központ meglévő névtérben való létrehozásához nyújt lépéseket. Azt is megtudhatja, hogyan hozhat létre egyéni hozzáférési szabályzatot.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs Azure CLI 2.0.67-es vagy újabb verziójára van szükség. Futtassa az [az version](/cli/azure/reference-index#az_version) parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az [az upgrade](/cli/azure/reference-index#az_upgrade) paranccsal frissíthet.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Notification Hubs, mint minden Azure-erőforrást, egy erőforráscsoportban kell üzembe helyezni.  Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.  Az [erőforráscsoportokkal Azure Resource Manager](../azure-resource-manager/management/overview.md) információkért lásd: What is Azure Resource Manager is Azure Resource Manager.)

Ebben a rövid útmutatóban hozzon létre egy **spnhubrg** nevű erőforráscsoportot az **eastus** helyen a következő [az group create paranccsal.](/cli/azure/group#az_group_create)

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Új Notification Hubs létrehozása

1. Hozzon létre egy névteret az értesítési központokhoz.

   A névtér egy vagy több központot tartalmaz, és a névnek egyedinek kell lennie az összes Azure-előfizetésben, és legalább hat karakter hosszúságúnak kell lennie. Egy név rendelkezésre állásának ellenőrzéshez használja az [az notification-hub namespace check-availability](/cli/azure/notification-hub/namespace#az_notification_hub_namespace_check-availability) parancsot.

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Az Azure CLI a következő konzolkimenet megjelenítésével válaszol a rendelkezésre állási kérésre:

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

   Figyelje meg az Azure CLI-válasz második sorát: `"isAvailable": true` . Ez a sor beolvassa, ha a névtérhez megadott kívánt név `false` nem érhető el. Miután megerősítette a név rendelkezésre állását, futtassa [az az notification-hub namespace create](/cli/azure/notification-hub/namespace#az_notification_hub_namespace_create) parancsot a névtér létrehozásához.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Ha a parancshoz megadott parancs nem érhető el, vagy nem felel meg az Azure-erőforrások elnevezési szabályainak és korlátozásának, az Azure CLI a következő `--name` `az notification-hub namespace create` konzolkimenettel válaszol: [](../azure-resource-manager/management/resource-name-rules.md)

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Ha az első kipróbált név nem sikerült, válasszon egy másik nevet az új névtérnek, és futtassa újra a `az notification-hub namespace create` parancsot.

   > [!NOTE]
   > Ebben a lépésben le kell cserélnie a paraméter értékét az ebben a rövid útmutatóban másolt `--namespace` Azure CLI-parancsokban.

2. Lekért névterek listája.

   Az új névtér részleteinek megtekintése az [az notification-hub namespace list paranccsal.](/cli/azure/notification-hub/namespace#az_notification_hub_namespace_list) A paraméter megadása nem kötelező, ha egy előfizetés összes `--resource-group` névterét meg szeretné látni.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Értesítési központok létrehozása

1. Hozza létre az első értesítési központot.

   Mostantól egy vagy több értesítési központ is létre lehet hozva az új névtérben. Az [értesítési központ létrehozásához futtassa az az notification-hub create](/cli/azure/notification-hub#az_notification_hub_create) parancsot.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Hozzon létre egy második értesítési központot.

   Egyetlen névtérben több értesítési központ is létre lehet hozva. Egy második értesítési központ ugyanabban a névtérben való létrehozásához futtassa újra a parancsot `az notification-hub create` egy másik központnévvel.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Az értesítési központok listájának leküldése.

   Az Azure CLI minden végrehajtott paranccsal egy sikert vagy hibaüzenetet ad vissza; Az értesítési központok listájának lekérdezése azonban nem kérdés. Az [az notification-hub list](/cli/azure/notification-hub#az_notification_hub_list) parancs erre a célra lett tervezve.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Hozzáférési szabályzatok elérése

1. Az Azure Notification Hubs közös [hozzáférésű jogosultságaláírás-biztonságot használ](./notification-hubs-push-notification-security.md) hozzáférési szabályzatok használatával. Az értesítési központ létrehozásakor automatikusan két szabályzat jön létre. A szabályzatok kapcsolati sztringjére a leküldéses értesítések konfigurálása érdekében van szükség. Az [az notification-hub authorization-rule list parancs](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization-rule-list) felsorolja a szabályzatok nevét és azok erőforráscsoportját.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Ne használja a _DefaultFullSharedAccessSignature szabályzatot_ az alkalmazásban. Ez a szabályzat csak a háttérben használható. Csak hozzáférési `Listen` házirendeket használjon az ügyfélalkalmazásban.

2. Ha további engedélyezési szabályokat szeretne létrehozni jelentéssel bíró névvel, létrehozhatja és testre szabhatja saját hozzáférési szabályzatát [az az notification-hub authorization-rule create paranccsal.](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_create) A paraméter a hozzárendelni kívánt engedélyek szóközrel tagolt `--rights` listája.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Minden hozzáférési szabályzathoz két kulcskészlet és kapcsolati sztring van. Később szüksége lesz rájuk az [értesítési központ konfigurálásához.](./configure-notification-hub-portal-pns-settings.md) Egy új hozzáférési szabályzat kulcsának és kapcsolati sztringének Notification Hubs [az az notification-hub authorization-rule list-keys paranccsal.](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys)

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > A [Notification Hubs névtér és](/cli/azure/notification-hub/namespace/authorization-rule#az_notification_hub_namespace_authorization_rule_list_keys) az [értesítési központ](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) külön hozzáférési szabályzatokkal rendelkezik. Győződjön meg arról, hogy a megfelelő Azure CLI-hivatkozást használja a kulcsok és kapcsolati sztringek lekérdezéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Következő lépések

* Ebben a rövid útmutatóban létrehozott egy értesítési központot. A központ platformértesítési rendszer (PNS) beállításokkal való konfigurálásával kapcsolatos további információkért lásd: Leküldéses értesítések beállítása [értesítési központban](configure-notification-hub-portal-pns-settings.md)

* Ismerje meg az értesítési központok Azure CLI-val való kezelésének kiterjedt képességeit:

  [Notification Hubs teljes referencialista](/cli/azure/notification-hub)

  [Notification Hubs-hivatkozáslista](/cli/azure/notification-hub/namespace)

  [Notification Hubs engedélyezési szabályok hivatkozási listája](/cli/azure/notification-hub/authorization-rule)

  [Notification Hubs hitelesítő adatok hivatkozási listája](/cli/azure/notification-hub/credential)