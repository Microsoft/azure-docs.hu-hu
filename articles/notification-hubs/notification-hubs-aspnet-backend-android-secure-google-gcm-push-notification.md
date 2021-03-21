---
title: Biztonságos leküldéses értesítések küldése az Azure Notification Hubs
description: Megtudhatja, hogyan küldhet biztonságos leküldéses értesítéseket egy Android-alkalmazásba az Azure-ból. Java-és C#-kódban írt minták.
documentationcenter: android
keywords: leküldéses értesítés, leküldéses értesítések, leküldéses üzenetek, Android leküldéses értesítések
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88004017"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Biztonságos leküldéses értesítések küldése az Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

A Microsoft Azure leküldéses értesítések támogatása lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses üzenet-infrastruktúra elérését, ami nagy mértékben leegyszerűsíti a leküldéses értesítések megvalósítását mind a fogyasztói, mind a vállalati alkalmazások számára a mobil platformokhoz.

A szabályozási vagy biztonsági korlátozások miatt előfordulhat, hogy egy alkalmazás az értesítésben olyan dolgot is tartalmaz, amely nem továbbítható a szabványos leküldéses értesítési infrastruktúrán keresztül. Ez az oktatóanyag leírja, hogyan érheti el ugyanezt a élményt úgy, hogy bizalmas adatokat küld biztonságos, hitelesített kapcsolaton keresztül az ügyfél Android-eszköze és az alkalmazás háttere között.

Magas szinten a folyamat a következő:

- Az alkalmazás háttere:
  * Biztonságos adattartalmat tárol a háttér-adatbázisban.
  * Elküldi az értesítés AZONOSÍTÓját az Android-eszköznek (a rendszer nem küld biztonságos információt).
- Az alkalmazás az eszközön az értesítés fogadásakor:
  * Az Android-eszköz kapcsolatba lép a biztonságos adattartalmat kérő háttérrel.
  * Az alkalmazás értesítésként jeleníti meg a hasznos adatokat az eszközön.

Fontos megjegyezni, hogy az előző folyamat során (és ebben az oktatóanyagban) feltételezzük, hogy az eszköz a helyi tárolóban tárolja a hitelesítési jogkivonatot, miután a felhasználó bejelentkezik. Ez a megközelítés zökkenőmentes működést garantál, mivel az eszköz a token használatával lekérheti az értesítés biztonságos hasznos adatait. Ha az alkalmazás nem tárolja a hitelesítési jogkivonatokat az eszközön, vagy ha ezek a jogkivonatok elévülnek, az eszköz alkalmazás a leküldéses értesítés fogadásakor egy általános értesítést jelenít meg, amely arra kéri a felhasználót, hogy indítsa el az alkalmazást. Az alkalmazás ezután hitelesíti a felhasználót, és megjeleníti az értesítési adattartalmat.

Ez az oktatóanyag bemutatja, hogyan küldhet biztonságos leküldéses értesítéseket. A [felhasználók értesítése](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) oktatóanyagra épül, ezért először végre kell hajtania az oktatóanyag lépéseit.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és konfigurálta az értesítési központot az első [lépések a Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)című cikkben leírtak szerint.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Az Android-projekt módosítása

Most, hogy módosította az alkalmazást, hogy csak a leküldéses értesítések AZONOSÍTÓját küldje el, módosítania kell az Android-alkalmazást, hogy kezelni tudja ezt az értesítést, és vissza kell hívnia a háttérben a megjelenítendő biztonságos üzenet lekéréséhez.
Ennek a célnak a megvalósításához gondoskodnia kell arról, hogy az Android-alkalmazás tudja, hogyan hitelesítheti magát a háttérben, amikor megkapja a leküldéses értesítéseket.

Most módosítsa a bejelentkezési folyamatot, hogy mentse a hitelesítési fejléc értékét az alkalmazás megosztott beállításaiban. Az analóg mechanizmusok bármilyen hitelesítési jogkivonat (például OAuth tokenek) tárolására használhatók, amelyeket az alkalmazásnak felhasználói hitelesítő adatok nélkül kell használnia.

1. Az Android-alkalmazás projektben adja hozzá a következő állandókat a `MainActivity` osztály tetején:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. Még mindig az `MainActivity` osztályban, frissítse a `getAuthorizationHeader()` metódust, hogy tartalmazza a következő kódot:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```

3. Adja hozzá a következő `import` utasításokat a fájl elejéhez `MainActivity` :

    ```java
    import android.content.SharedPreferences;
    ```

Most módosítsa az értesítés fogadásakor meghívott kezelőt.

1. Az `MyHandler` osztályban módosítsa a `OnReceive()` következőket:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Ezután adja hozzá a `retrieveNotification()` metódust, és cserélje le a helyőrzőt a háttérrendszer `{back-end endpoint}` üzembe helyezése során kapott háttér-végpontra:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Ez a módszer a megosztott beállításokban tárolt hitelesítő adatokkal kéri le az alkalmazás háttér-visszaállítását, és normál értesítésként jeleníti meg az értesítési tartalmat. Az értesítés ugyanúgy néz ki az alkalmazás felhasználójának, mint bármely más leküldéses értesítéshez.

Célszerű kezelni a hiányzó hitelesítési fejléc-tulajdonság vagy a háttér általi elutasítás eseteit. Ezeknek az eseteknek a konkrét kezelését többnyire a cél felhasználói élménytől függ. Az egyik lehetőség egy általános rákérdező értesítés megjelenítése a felhasználó hitelesítéséhez a tényleges értesítés lekéréséhez.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához hajtsa végre a következő műveleteket:

1. Győződjön meg arról, hogy az **AppBackend** telepítve van az Azure-ban. Ha a Visual studiót használja, futtassa a **AppBackend** web API alkalmazást. Megjelenik egy ASP.NET weblap.
2. Az Eclipse-ben futtassa az alkalmazást egy fizikai Android-eszközön vagy az emulátoron.
3. Az Android-alkalmazás felhasználói felületén adja meg a felhasználónevet és a jelszót. Ezek bármilyen sztringek lehetnek, de az értékeknek is szerepelniük kell.
4. Az Android-alkalmazás felhasználói felületén kattintson a **Bejelentkezés** elemre. Ezután kattintson a **leküldés küldése** gombra.
