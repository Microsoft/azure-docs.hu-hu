---
title: Leküldéses értesítések beállítása az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az Azure Notification Hubst a Azure Portal a platform Notification System (PNS) beállításainak használatával.
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
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94887263"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Gyors útmutató: leküldéses értesítések beállítása egy értesítési központban

Az Azure Notification Hubs egy könnyen használható leküldéses motort biztosít, amely kibővíthető. A Notification Hubs használatával küldhet értesítéseket bármilyen platformra (iOS, Android, Windows, Baidu) és bármilyen háttérből (Felhőbeli vagy helyszíni). További információ: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban a Notification Hubs platform Notification System (PNS) beállításait fogja használni a leküldéses értesítések több platformon történő beállításához. A rövid útmutatóban a Azure Portal elvégzendő lépések láthatók. A [Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) az Azure CLI használatára vonatkozó utasításokat tartalmaz.

Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portal](create-notification-hub-portal.md) vagy Azure [Notification hub létrehozása az Azure CLI használatával](create-notification-hub-azure-cli.md).

## <a name="apple-push-notification-service"></a>Apple Push Notification szolgáltatás

Apple Push Notification Service (APNS) beállítása:

1. A Azure Portal az **értesítési központ** lapon válassza az **Apple (APNS)** lehetőséget a bal oldali menüből.

1. A **hitelesítési mód** beállításnál válassza a **tanúsítvány** vagy a **token** lehetőséget.

   a. Ha a **tanúsítvány** lehetőséget választja:
   * Válassza ki a fájl ikont, majd válassza ki a feltölteni kívánt *. P12* -fájlt.
   * Adjon meg egy jelszót.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-tanúsítvány konfigurációjának Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Ha a **jogkivonat** lehetőséget választja:

   * Adja meg a **kulcs azonosítójának**, **a köteg azonosítójának**, a **csoport azonosítójának** és a **tokennek** az értékeit.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-jogkivonat konfigurációjának Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

További információ: [leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Leküldéses értesítések beállítása a Google FCM-hez:

1. A Azure Portal az **értesítési központ** lapon válassza a **Google (GCM/FCM)** elemet a bal oldali menüből.
2. Illessze be a korábban mentett Google FCM-projekthez tartozó **API-kulcsot** .
3. Kattintson a **Mentés** gombra.

   ![Képernyőkép, amely bemutatja, hogyan konfigurálható Notification Hubs a Google FCM-hez](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Ha elvégezte ezeket a lépéseket, a riasztás azt jelzi, hogy az értesítési központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Szüksége lesz a Google Firebase Cloud Messaging (FCM) projekt **API-kulcsára** .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Ehhez a cikkhez az Azure CLI 2.0.67 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

### <a name="set-up-push-notifications-for-google-fcm"></a>Leküldéses értesítések beállítása a Google FCM-hez

1. Az az [Notification-hub hitelesítőadat GCM Update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) paranccsal adja hozzá Google API-kulcsát az értesítési központhoz.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Az Android-alkalmazásnak kapcsolati sztringre van szüksége az értesítési központhoz való kapcsolódáshoz.  Az elérhető hozzáférési szabályzatok listázásához használja az az [Notification-hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) parancsot.  Használja az az [Notification-hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsot a hozzáférési szabályzat kapcsolati karakterláncának beszerzéséhez.  A (z) paraméterben a **primaryConnectionString** vagy a **secondaryConnectionString** megadásával `--query` kérje le az elsődleges kapcsolódási karakterláncot közvetlenül.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Az az [Notification-hub test-Send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) paranccsal tesztelheti az üzeneteket az Android-alkalmazásba.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Azure CLI-referenciák beszerzése más platformokra az az [Notification-hub hitelesítőadat](/cli/azure/ext/notification-hub/notification-hub/credential) paranccsal.

Az értesítések Android-alkalmazásokba való küldésével kapcsolatos további információkért lásd: [leküldéses értesítések küldése Android-eszközökre a Firebase használatával](notification-hubs-android-push-notification-google-fcm-get-started.md).

---

## <a name="windows-push-notification-service"></a>Windows leküldéses értesítési szolgáltatás

A Windows leküldéses értesítési szolgáltatás (WNS) beállítása:

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows (WNS)** lehetőséget a bal oldali menüből.
2. Adja meg a **csomag biztonsági azonosítójának** és **biztonsági kulcsának** értékeit.
3. Kattintson a **Mentés** gombra.

   ![Képernyőfelvétel a csomag biztonsági AZONOSÍTÓjának és biztonsági kulcsának mezőiről](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

További információ: [értesítések küldése UWP-alkalmazásoknak az Azure Notification Hubs használatával](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>A Microsoft leküldéses értesítési szolgáltatása a Windows Phone-telefon

A Microsoft leküldéses értesítési szolgáltatás (MPNS) beállítása a Windows Phone-telefonhoz:

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows Phone-telefon (MPNS)** elemet a bal oldali menüből.
1. Nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez válassza a nem **hitelesített leküldéses**  >  **Mentés** engedélyezése lehetőséget.

      ![A nem hitelesített leküldéses értesítések engedélyezését bemutató képernyőkép](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a **tanúsítvány feltöltése** lehetőséget.
      * Válassza ki a fájl ikont, majd válassza ki a tanúsítványfájl.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Válassza az **OK** lehetőséget.
      * A **Windows Phone-telefon (MPNS)** lapon válassza a **Mentés** lehetőséget.

További információ: [leküldéses értesítések Windows Phone-telefon alkalmazások számára Notification Hubs használatával](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Leküldéses értesítések beállítása a Baidu számára:

1. A Azure Portal az **értesítési központ** lapon válassza a **Baidu (Android China)** lehetőséget a bal oldali menüből.
2. Adja meg a felhőalapú Baidu-értesítés projekt Baidu-konzolján beszerzett **API-kulcsot** .
3. Adja meg a felhőalapú Baidu-értesítés projekt Baidu-konzolján beszerzett **titkos kulcsot** .
4. Kattintson a **Mentés** gombra.

    ![A leküldéses értesítések Baidu (Android China) konfigurációját bemutató Notification Hubs képernyőképe](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Ha elvégezte ezeket a lépéseket, a riasztás azt jelzi, hogy az értesítési központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva.

További információ: a [Notification Hubs használatának első lépései a Baidu használatával](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan konfigurálhatja az értesítési központ platform-értesítési rendszerbeállításait a Azure Portal.

Az értesítések különböző platformokra való leküldésével kapcsolatos további tudnivalókért tekintse meg a következő oktatóanyagokat:

* [Leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával](ios-sdk-get-started.md)
* [Értesítések küldése Android-eszközökre a Notification Hubs és a Google FCM használatával](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Értesítések küldése Windows-eszközön futó UWP-alkalmazásnak](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Értesítések küldése Windows Phone-telefon 8 alkalmazásnak a MPNS használatával](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Értesítések küldése Notification Hubs és felhőalapú Baidu-értesítés használatával](notification-hubs-baidu-china-android-notifications-get-started.md)
