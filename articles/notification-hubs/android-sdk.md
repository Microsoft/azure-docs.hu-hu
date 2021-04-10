---
title: Leküldéses értesítések küldése Androidra az Azure Notification Hubs és a Firebase SDK-verzió 1.0.0-Preview1
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Notification Hubs és a Google Firebase Cloud Messaging szolgáltatást leküldéses értesítések küldésére Android-eszközökre (a 1.0.0-Preview1 verzióra).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 091ada7f099aca17152fc806e61dce6ab979852e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055161"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Oktatóanyag: leküldéses értesítések küldése Android-eszközökre a Firebase SDK-Preview1 használatával

Ez az oktatóanyag bemutatja, hogyan használható az Azure Notification Hubs és a Firebase Cloud Messaging (FCM) SDK frissített verziója, amely leküldéses értesítéseket küld egy Android-alkalmazásnak. Ebben az oktatóanyagban egy üres Android-alkalmazást hoz létre, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával.

Az oktatóanyaghoz tartozó befejezett kódot letöltheti a [githubról](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Ez az oktatóanyag a következő lépéseken vezet végig:

- Android Studio-projekt létrehozása.
- A Firebase Cloud Messaginget támogató Firebase-projekt létrehozása.
- Értesítési központ létrehozása.
- Csatlakoztathatja az alkalmazást az hubhoz.
- Az alkalmazás tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

A következő elemek is szükségesek:

- A [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) legújabb verziója ajánlott.
- A minimális támogatás a 19-ös API-szint.

## <a name="create-an-android-studio-project"></a>Android Studio projekt létrehozása

Első lépésként hozzon létre egy projektet a Android Studioban:

1. Android Studio elindítása.

2. Válassza a **fájl**, majd az **új**, majd az **új projekt** lehetőséget.

3. A **projekt kiválasztása** lapon válassza az **üres tevékenység** elemet, majd kattintson a **tovább** gombra.

4. A **projekt konfigurálása** lapon tegye a következőket:
   1. Adjon nevet az alkalmazásnak.
   2. Itt adhatja meg a projektfájlok mentési helyét.
   3. Válassza a **Befejezés** gombot.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Projekt konfigurálása":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Az FCM-et támogató Firebase-projekt létrehozása

1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.

2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Firebase hozzáadása":::

3. A **Firebase hozzáadása az Android-alkalmazáshoz** lapon tegye a következőket:

   1. Az **android-csomag neve mezőben** másolja a **applicationId** értékét az alkalmazás **Build. gradle** fájljában. Ebben a példában ez a következő: `com.fabrikam.fcmtutorial1app` .

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Adja meg a csomag nevét":::

   2. Válassza az **alkalmazás regisztrálása** lehetőséget.

4. Válassza a **letöltés google-services.jsbe** lehetőséget, mentse a fájlt a projekt **alkalmazás** mappájába, majd válassza a **tovább** lehetőséget.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Google-szolgáltatás letöltése":::

5. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="A projekt beállításai":::

6. Ha nem töltötte le a **google-services.js** fájlt a Android Studio projekt **alkalmazás** mappájába, ezt megteheti ezen a lapon.

7. Váltson a **Cloud Messaging (Felhőbeli üzenetkezelés** ) lapra.

8. Másolja és mentse a **kiszolgálói kulcsot** későbbi használatra. Ezt az értéket használja a hub konfigurálásához.

## <a name="configure-a-notification-hub"></a>Értesítési központ konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd a **mobil** szakaszban válassza a **Notification Hubs** lehetőséget. A szolgáltatás neve melletti csillag ikonra kattintva adja hozzá a szolgáltatást a **Kedvencek** szakaszhoz a bal oldali menüben. Miután hozzáadta **Notification Hubs** a **kedvencekhez**, válassza ki a bal oldali menüben.

3. A **Notification Hubs** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Hub hozzáadása":::

4. A **Notification Hubs** lapon tegye a következőket:

   1. Adjon meg egy nevet az **értesítési központban**.

   2. Adjon meg egy nevet az **új névtér létrehozása** mezőben. Egy névtér legalább egy hubokat tartalmaz.

   3. Válasszon egy értéket a **hely** legördülő listából. Ez az érték határozza meg azt a helyet, amelyben létre kívánja hozni a hubot.

   4. Válasszon ki egy meglévő erőforráscsoportot az **erőforráscsoporthoz**, vagy hozzon létre egy újat.

   5. Válassza a **Létrehozás** lehetőséget.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Hub létrehozása":::

5. Válassza az **értesítések** (harang ikon) lehetőséget, majd válassza **az Ugrás az erőforráshoz** lehetőséget. A listát a **Notification Hubs** oldalon is frissítheti, és kiválaszthatja a hubot.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Hub kiválasztása":::

6. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Vegye figyelembe, hogy két csatlakozási karakterlánc érhető el. A leküldéses értesítések kezeléséhez később szüksége lesz rájuk.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Hozzáférési szabályzatok":::

   > [!IMPORTANT]
   > Ne használja az **DefaultFullSharedAccessSignature** szabályzatot az alkalmazásban. Ezt a házirendet csak háttérbeli alkalmazásban lehet használni.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>A központ Firebase Cloud Messaging-beállításainak konfigurálása

1. A bal oldali ablaktábla **Beállítások** területén válassza a **Google (GCM/FCM)** lehetőséget.

2. Adja meg a korábban mentett FCM-projekthez tartozó **kiszolgálói kulcsot** .

3. Az eszköztáron válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Kiszolgáló kulcsa":::

4. A Azure Portal üzenet jelenik meg, amely szerint a központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva.

Az értesítési központ már konfigurálva van a Firebase Cloud Messaging szolgáltatással való együttműködésre. Emellett a kapcsolódási sztringek is megtalálhatók, amelyek szükségesek az értesítések egy eszközre való küldéséhez és egy alkalmazás regisztrálásához az értesítések fogadásához.

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe

1. A Android Studio területen válassza a menü **eszközök** elemét, majd válassza az **SDK-kezelő** elemet.

2. Válassza ki a projektben használt Android SDK cél verzióját. Ezután válassza a **csomag adatainak megjelenítése** lehetőséget.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK-kezelő":::

3. Válassza a **Google API**-k lehetőséget, ha még nincs telepítve.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API-k":::

4. Váltson az **SDK-eszközök** lapra. Ha még nem telepítette a Google Play-szolgáltatásokat, válassza a **Google Play-szolgáltatások** lehetőséget az alábbi ábrán látható módon. Ezután válassza az **alkalmaz** elemet a telepítéshez. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play-szolgáltatások":::

5. Ha megjelenik a **módosítás megerősítése** párbeszédpanel, kattintson az **OK gombra**. Az összetevő-telepítő telepíti a kért összetevőket. Az összetevők telepítése után válassza a **Befejezés** lehetőséget.

6. Az **új projektek beállításainak** bezárásához kattintson **az OK gombra** .

### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs-kódtárak hozzáadása

1. Az alkalmazás **Build. gradle** fájljában adja hozzá a következő sorokat a függőségek szakaszhoz:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Adja hozzá a következő tárházat a függőségek szakasz után:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Google Firebase-támogatás hozzáadása

1. Ha még nem létezik, adja hozzá a következő beépülő modult a fájl végéhez.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Kattintson a **szinkronizálás most** elemre az eszköztáron.

### <a name="add-code"></a>Kód hozzáadása

1. Hozzon létre egy **NotificationHubListener** objektumot, amely kezeli az üzenetek Azure Notification Hubsból való elfogását.

   ```java
   public class CustomNotificationListener implements NotificationListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. Az `OnCreate` osztály metódusában `MainActivity` adja hozzá a következő kódot a Notification Hubs inicializálási folyamat elindításához a tevékenység létrehozásakor:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.start(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. Android Studio a menüsávon válassza a **Létrehozás**, majd a **projekt újraépítése** lehetőséget, hogy ellenőrizze, nincsenek-e hibák a kódban. Ha hibaüzenetet kap a **ic_launcher** ikonról, távolítsa el a következő utasítást a AndroidManifest.xml fájlból:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Győződjön meg arról, hogy rendelkezik virtuális eszközzel az alkalmazás futtatásához. Ha még nem rendelkezik ilyennel, vegyen fel egyet a következő módon:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Eszközkezelő":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Virtuális eszközök":::
   3. Futtassa az alkalmazást a kiválasztott eszközön, és ellenőrizze, hogy sikeresen regisztrálja-e az eszközt a hubhoz.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Eszközregisztráció":::

      > [!NOTE]
      > Előfordulhat, hogy a regisztráció a kezdeti indítás során meghiúsul, amíg meg nem `onTokenRefresh()` történik a példány-azonosító szolgáltatás metódusának meghívása. A frissítés sikeres regisztrációt kezdeményez az értesítési központban.

## <a name="send-a-test-notification"></a>Tesztértesítés küldése

Leküldéses értesítéseket küldhet az értesítési központnak a [Azure Portalból](https://portal.azure.com/)a következőképpen:

1. A Azure Portal a központ értesítési központ lapján válassza a **küldési teszt** lehetőséget a **Hibaelhárítás** szakaszban.

2. A **platformok** területen válassza az **Android** lehetőséget.

3. Kattintson a **Küldés** gombra. Az Android-eszközön még nem jelenik meg értesítés, mert nem futtatta rajta a Mobile alkalmazást. Miután futtatta a Mobile alkalmazást, kattintson ismét a **Küldés** gombra az értesítési üzenet megtekintéséhez.

4. A művelet eredményét a portál oldalának alján található listában tekintheti meg.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Teszt értesítés küldése":::

5. Megjelenik az értesítési üzenet az eszközön.

A leküldéses értesítések általában egy olyan háttér-szolgáltatásban érkeznek, mint például a Mobile Apps vagy a ASP.NET egy kompatibilis kódtár használatával. Ha egy könyvtár nem érhető el a háttérrendszer számára, akkor a REST API közvetlenül is használhatja az értesítési üzenetek küldéséhez.

## <a name="run-the-mobile-app-on-emulator"></a>A Mobile App futtatása az emulátoron

Mielőtt a leküldéses értesítéseket egy emulátoron belül teszteli, győződjön meg arról, hogy az Emulator-rendszerkép támogatja az alkalmazáshoz választott Google API-szintet. Ha a rendszerkép nem támogatja a natív Google API-kat, előfordulhat, hogy **SERVICE_NOT_AVAILABLE** kivételt kap.

Győződjön meg arról is, hogy hozzáadta Google-fiókját a futó emulátorhoz a **Beállítások**  >  **fiókok** területen. Ellenkező esetben az FCM-sel való regisztrációra tett kísérletek **AUTHENTICATION_FAILED** kivételt okozhatnak.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Firebase Cloud Messaging szolgáltatást használta az értesítések küldéséhez a szolgáltatásban regisztrált összes Android-eszközre. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Oktatóanyag: értesítések küldése adott felhasználóknak](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Az alábbi lista felsorolja az értesítések küldésére vonatkozó egyéb oktatóanyagokat:

- Azure Mobile Apps: a [leküldéses értesítések hozzáadása iOS-alkalmazáshoz](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)című témakörből megtudhatja, hogyan küldhet értesítéseket az Notification Hubs-vel integrált Mobile apps háttérből.

- ASP.NET: [a Notification Hubs használatával leküldéses értesítéseket küldhet a felhasználóknak](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Azure Notification Hubs Java SDK: [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (A Notification Hubs használata Javából) oktatóprogram bemutatja, hogyan küldhetők értesítések a Javával. Ez az Eclipse-ben lett tesztelve Android-fejlesztéshez.

- PHP: [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (A Notification Hubs használata PHP-val).
