---
title: Leküldéses értesítések beállítása az Azure Notification Hubs | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be az Azure Notification Hubs a Azure Portal platformértesítési rendszer (PNS) beállításaival.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 31d915cd44bcf60f3515eb1a84309980f45d40b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868284"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Rövid útmutató: Leküldéses értesítések beállítása értesítési központban

Az Azure Notification Hubs egyszerűen használható, horizontálisan felskálásos leküldéses motort biztosít. A Notification Hubs használatával értesítéseket küldhet bármely platformra (iOS, Android, Windows, Baidu) és bármely háttérből (felhőbeli vagy helyszíni). További információ: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

Ebben a rövid útmutatóban a platformértesítési rendszer (PNS) beállításaival fog leküldéses értesítéseket Notification Hubs több platformon beállítani. A rövid útmutató bemutatja a lépések lépéseit a Azure Portal. [A Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) az Azure CLI használatának utasításait tartalmazza.

Ha még nem hozott létre értesítési központot, hozzon létre egyet. További információ: [Azure](create-notification-hub-portal.md) értesítési központ létrehozása a Azure Portal Azure értesítési központ létrehozása az [Azure CLI használatával.](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification szolgáltatás

A (APNS Apple Push Notification Service beállítása:

1. A Azure Portal notification **hub (Értesítési központ)** oldalon válassza a bal oldali menüben az **Apple (APNS)** elemet.

1. Hitelesítési **módként válassza** a Tanúsítvány **vagy** a **Jogkivonat lehetőséget.**

   a. Ha a Tanúsítvány lehetőséget **választja:**
   * Válassza a fájl ikont, majd válassza ki a feltölteni kívánt *.p12* fájlt.
   * Adjon meg egy jelszót.
   * Válassza a **Védőfal** módot. Vagy ha leküldéses értesítéseket kell küldenie az áruházból az alkalmazást megvásárló felhasználóknak, válassza az Éles **mód** lehetőséget.

     ![Képernyőkép egy APNS-tanúsítvány konfigurációról a Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Ha a Token **(Jogkivonat) lehetőséget választja:**

   * Adja meg a **kulcsazonosító,** a **csomagazonosító,** a **csapatazonosító** és a **jogkivonat értékeit.**
   * Válassza a **Védőfal** módot. Vagy ha leküldéses értesítéseket kell küldenie az áruházból az alkalmazást megvásárló felhasználóknak, válassza az Éles **mód** lehetőséget.

     ![Az APNS-jogkivonat konfigurációjának képernyőképe a Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

További információ: Leküldéses értesítések [küldése iOS-alkalmazásokba az Azure Notification Hubs.](ios-sdk-get-started.md)

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Leküldéses értesítések beállítása a Google FCM-hez:

1. A Azure Portal az **Értesítési központ oldalon** válassza a Google **(GCM/FCM)** elemet a bal oldali menüben.
2. Illessze **be a** korábban mentett Google FCM-projekt API-kulcsát.
3. Kattintson a **Mentés** gombra.

   ![Képernyőkép a Google FCM Notification Hubs konfigurálásról](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

A lépések befejezésekor egy riasztás jelzi, hogy az értesítési központ frissítése sikeres volt. A **Mentés** gomb le van tiltva.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Szüksége lesz az **API-kulcsra** a Google Firebase Cloud Messaging - FCM-projekthez.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Ehhez a cikkhez az Azure CLI 2.0.67-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

### <a name="set-up-push-notifications-for-google-fcm"></a>Leküldéses értesítések beállítása a Google FCM-hez

1. Az [az notification-hub credential gcm update](/cli/azure/notification-hub/credential/gcm#az_notification_hub_credential_gcm_update) paranccsal adja hozzá a Google API-kulcsot az értesítési központhoz.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Az Android-alkalmazásnak szüksége van egy kapcsolati sztringre az értesítési központhoz való csatlakozáshoz.  Az [az notification-hub authorization-rule list paranccsal](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list) listálhatja az elérhető hozzáférési szabályzatokat.  A hozzáférési [szabályzat kapcsolati sztringjeit az az notification-hub authorization-rule list-keys](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) paranccsal kaphatja meg.  Adja meg **a primaryConnectionString vagy** **secondaryConnectionString** sztringet a paraméterben, hogy közvetlenül lekérte az `--query` elsődleges kapcsolati sztringet.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Az [az notification-hub test-send paranccsal](/cli/azure/notification-hub#az_notification_hub_test_send) tesztelje az üzenetek küldését az Android-alkalmazásba.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Az az [notification-hub credential](/cli/azure/notification-hub/credential) paranccsal azure CLI-referenciákat kaphat más platformokhoz.

További információ az értesítések Android-alkalmazásokba való küldéséről: Leküldéses értesítések küldése Android-eszközökre [a Firebase használatával.](notification-hubs-android-push-notification-google-fcm-get-started.md)

---

## <a name="windows-push-notification-service"></a>a Windows leküldéses értesítéseket kezelő szolgáltatása

A WNS (WNS a Windows leküldéses értesítéseket kezelő szolgáltatása beállítása:

1. A Azure Portal notification hub **(Értesítési központ)** lapon válassza a Bal oldali menüben a **Windows (WNS)** elemet.
2. Adja meg a Csomag biztonsági **azonosítója és** **a Biztonsági kulcs értékeit.**
3. Kattintson a **Mentés** gombra.

   ![A Csomag biztonsági azonosító és a Biztonsági kulcs mezőket bemutató képernyőkép](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

További információ: [Értesítések küldése UWP-alkalmazásokba az Azure Notification Hubs.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)

## <a name="microsoft-push-notification-service-for-windows-phone"></a>a Microsoft leküldéses értesítéseket kezelő szolgáltatása a Windows Phone-telefon

A következő a Microsoft leküldéses értesítéseket kezelő szolgáltatása (MPNS) Windows Phone-telefon:

1. A Azure Portal az **Értesítési központ lapon** válassza a bal Windows Phone-telefon **(MPNS)** lehetőséget.
1. Engedélyezze a nem hitelesített vagy hitelesített leküldéses értesítéseket:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez válassza a **Nem** hitelesített leküldéses értesítések engedélyezése Mentés  >  **lehetőséget.**

      ![A nem hitelesített leküldéses értesítések engedélyezését bemutató képernyőkép](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a Tanúsítvány **feltöltése lehetőséget.**
      * Válassza a fájl ikont, majd válassza ki a tanúsítványfájlt.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Válassza az **OK** lehetőséget.
      * A Windows Phone-telefon **(MPNS)** lapon válassza a **Mentés lehetőséget.**

További információ: [Leküldéses értesítések Windows Phone-telefon alkalmazásokba a Notification Hubs.](notification-hubs-windows-mobile-push-notifications-mpns.md)

## <a name="baidu-android-china"></a>Baidu (Android China)

Leküldéses értesítések beállítása a Baiduhoz:

1. A Azure Portal az Értesítési **központ oldalon** válassza a bal oldali menüben a **Baidu (Android China)** lehetőséget.
2. A **projektben adja** meg a Baidu-konzolról felhőalapú Baidu-értesítés API-kulcsot.
3. A **projektben adja** meg a Baidu-konzolról felhőalapú Baidu-értesítés kulcsot.
4. Kattintson a **Mentés** gombra.

    ![A leküldéses Notification Hubs Baidu (Android China) konfigurációját bemutató képernyőkép](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

A lépések befejezésekor egy riasztás jelzi, hogy az értesítési központ frissítése sikeres volt. A **Mentés** gomb le van tiltva.

További információ: [Get started with Notification Hubs by using Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)(Első lépések a Notification Hubs Baidu használatával).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan konfigurálhatja a platformértesítési rendszer beállításait egy értesítési központhoz a Azure Portal.

A leküldéses értesítések különböző platformokra való leküldéses értesítésekkel kapcsolatos további információkért tekintse meg az alábbi oktatóanyagokat:

* [Leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs](ios-sdk-get-started.md)
* [Értesítések küldése Android-eszközökre a Notification Hubs Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Értesítések küldése Windows-eszközön futó UWP-alkalmazásba](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Értesítések küldése Windows Phone-telefon 8-as alkalmazásba az MPNS használatával](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Értesítések küldése a Notification Hubs és felhőalapú Baidu-értesítés](notification-hubs-baidu-china-android-notifications-get-started.md)
