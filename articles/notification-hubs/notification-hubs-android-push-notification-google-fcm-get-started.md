---
title: Leküldéses értesítések küldése Androidra az Azure Notification Hubs és a Firebase SDK 0,6-es verziójának használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Notification Hubs és a Google Firebase Cloud Messaging szolgáltatást leküldéses értesítések küldésére Android-eszközökre (0,6-es verzió).
services: notification-hubs
documentationcenter: android
keywords: leküldéses értesítések,leküldéses értesítés,android leküldéses értesítés,fcm,firebase cloud messaging
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c5485dacc4d9e3210ad69819caf4e36f96c626da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92428379"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-06"></a>Oktatóanyag: leküldéses értesítések küldése Android-eszközökre a Firebase SDK 0,6-es verziójának használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ez az oktatóanyag bemutatja, hogyan használható az Azure Notification Hubs és a Firebase Cloud Messaging (FCM) SDK 0,6-es verziója, hogy leküldéses értesítéseket küldjön egy Android-alkalmazásnak. Ebben az oktatóanyagban létrehoz egy üres Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával.

Az oktatóanyaghoz tartozó befejezett kód letölthető a [githubról](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Android Studio-projekt létrehozása.
> * A Firebase Cloud Messaginget támogató Firebase-projekt létrehozása.
> * Hozzon létre egy hubot.
> * Csatlakoztathatja az alkalmazást az hubhoz.
> * Az alkalmazás tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/). 

A következő elemek is szükségesek: 

* A [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) legújabb verziója
* Android 2,3 vagy újabb a Firebase Cloud Messaging szolgáltatáshoz
* A Google adattár 27-ös vagy újabb verziója a Firebase Cloud Messaging szolgáltatáshoz
* A Google Play Services 9.0.2 vagy újabb verziója a Firebase Cloud Messaging szolgáltatáshoz

Az oktatóanyag elvégzése előfeltétele az Android-alkalmazásokhoz tartozó összes más Notification Hubs-oktatóanyag végrehajtásához.

## <a name="create-an-android-studio-project"></a>Android Studio-projekt létrehozása

1. Android Studio elindítása.
2. Válassza a **fájl** lehetőséget, mutasson az **új** elemre, majd válassza az **új projekt** lehetőséget. 
2. A **projekt kiválasztása** lapon válassza az **üres tevékenység** elemet, majd kattintson a **tovább** gombra. 
3. A **projekt konfigurálása** oldalon hajtsa végre a következő lépéseket: 
    1. Adjon nevet az alkalmazásnak.
    2. Itt adhatja meg a projektfájlok mentési helyét. 
    3. Válassza a **Befejezés** gombot. 

        ![A projekt konfigurálása)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Az FCM-et támogató Firebase-projekt létrehozása

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Központ konfigurálása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>A központ Firebase Cloud Messaging-beállításainak konfigurálása

1. A bal oldali ablaktábla **Beállítások** területén válassza a **Google (GCM/FCM)** lehetőséget. 
2. Adja meg a korábban mentett FCM-projekthez tartozó **kiszolgálói kulcsot** . 
3. Az eszköztáron válassza a **Mentés** lehetőséget. 

    ![Azure Notification hub – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. A Azure Portal üzenet jelenik meg a riasztásokban, hogy a központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva. 

A központ most már úgy van konfigurálva, hogy működjön a Firebase Cloud Messaging szolgáltatással. Emellett a kapcsolódási sztringek is megtalálhatók, amelyek szükségesek az értesítések egy eszközre való küldéséhez és egy alkalmazás regisztrálásához az értesítések fogadásához.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe

1. A Android Studio területen válassza a menü **eszközök** elemét, majd válassza az **SDK-kezelő** elemet. 
2. Válassza ki a projektben használt Android SDK cél verzióját. Ezután válassza a **csomag adatainak megjelenítése** lehetőséget. 

    ![Android SDK-kezelő – cél verziójának kiválasztása](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Válassza a **Google API**-k lehetőséget, ha még nincs telepítve.

    ![Android SDK-kezelő – kiválasztott Google API-k](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Váltson az **SDK-eszközök** lapra. Ha még nem telepítette a Google Play-szolgáltatásokat, válassza a **Google Play-szolgáltatások** lehetőséget az alábbi ábrán látható módon. Ezután válassza az **alkalmaz** elemet a telepítéshez. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

    ![Android SDK-kezelő – kiválasztott Google Play-szolgáltatások](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Ha megjelenik a **módosítás megerősítése** párbeszédpanel, kattintson az **OK gombra**. Az összetevő-telepítő telepíti a kért összetevőket. Az összetevők telepítése után válassza a **Befejezés** lehetőséget.
4. Az **új projektek beállításainak** bezárásához kattintson **az OK gombra** .  
1. Nyissa meg a AndroidManifest.xml fájlt, majd adja hozzá a következő címkét az *alkalmazás* címkéjéhez.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs-kódtárak hozzáadása

1. Az alkalmazás Build. Gradle fájljában adja hozzá a következő sorokat a függőségek szakaszhoz.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. A dependencies (függőségek) szakasz után vegye fel az alábbi tárhelyet.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Google Firebase-támogatás hozzáadása

1. Az alkalmazás Build. Gradle fájljában adja hozzá a következő sorokat a **függőségek** szakaszban, ha még nem léteznek. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Ha még nem létezik, adja hozzá a következő beépülő modult a fájl végéhez. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Kattintson a **szinkronizálás most** elemre az eszköztáron.

### <a name="update-the-androidmanifestxml-file"></a>A AndroidManifest.xml fájl frissítése

1. Az FCM regisztrációs jogkivonat kézhezvétele után az Azure Notification Hubs-ban való [regisztrálásra](notification-hubs-push-notification-registration-management.md)használható. Ezt a regisztrációt a háttérben egy elnevezett paranccsal támogathatja `IntentService` `RegistrationIntentService` . Ez a szolgáltatás az FCM regisztrációs tokent is frissíti. Létrehoz egy nevű osztályt is a `FirebaseService` alosztályként, `FirebaseMessagingService` és felülbírálja az `onMessageReceived` értesítések fogadásához és kezeléséhez szükséges metódust. 

    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Adja hozzá a következő szükséges FCM-hez kapcsolódó engedélyeket a `</application>` címke alá.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Kód hozzáadása

1. A Project nézetben bontsa ki az **app**  >  **src**  >  **Main**  >  **Java** elemet. Kattintson a jobb gombbal a csomag mappájára a **Java** területen, válassza az **új**, majd a **Java-osztály** elemet. Adja meg a **NotificationSettings** nevet, majd kattintson **az OK gombra**.

    Győződjön meg arról, hogy frissítette ezt a három helyőrzőt a `NotificationSettings` osztály alábbi kódjában:

   * **HubListenConnectionString**: A központ **DefaultListenAccessSignature** kapcsolati sztringje. A kapcsolati karakterlánc másolásához kattintson a [Azure Portal]a központ **hozzáférési szabályzatok** elemére.
   * **HubName**: használja az [Azure Portal]hub lapján megjelenő hub nevét.

     `NotificationSettings` kód:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > A folytatás előtt adja meg a hub **nevét** és **DefaultListenSharedAccessSignature** . 

2. Adjon hozzá a projekthez egy másik új, `RegistrationIntentService` nevű osztályt. Ez az osztály valósítja meg a `IntentService` felületet. Emellett kezeli [az FCM-token frissítését](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) és [az értesítési központba való regisztrációt](notification-hubs-push-notification-registration-management.md)is.

    Ehhez az osztályhoz az alábbi kódokat használja.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
               else if (!(storedToken = sharedPreferences.getString("FCMtoken", "")).equals(FCM_token)) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. A `MainActivity` osztályban adja hozzá a következő `import` utasításokat az osztály deklarációja felett.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Adja hozzá a következő tagokat az osztály tetején. Ezeket a mezőket a [Google Play-szolgáltatások rendelkezésre állásának ellenőrzésére használja a Google által javasolt módon](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. A `MainActivity` osztályban adja hozzá a következő metódust a Google Play-szolgáltatások rendelkezésre állásának vizsgálatához.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

6. A `MainActivity` osztályban adja hozzá a következő kódot, amely a Google Play-szolgáltatásokat ellenőrzi, mielőtt meghívja a `IntentService` -t az FCM regisztrációs jogkivonat beszerzéséhez, és regisztrálja a hubot a központban:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. A `OnCreate` osztály metódusában `MainActivity` adja hozzá a következő kódot a regisztrációs folyamat elindításához a tevékenység létrehozásakor:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Az alkalmazás állapotának és a jelentés állapotának az alkalmazásban való ellenőrzéséhez adja hozzá ezeket a további metódusokat a következőhöz `MainActivity` :

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

9. A `ToastNotify` metódus a *„Hello World”* `TextView` vezérlőt használja az állapot és az értesítések folyamatos jelentéséhez az alkalmazásban. A **res**  >  **elrendezés**  >  **activity_main.xml** elrendezésében adja hozzá a következő azonosítót a vezérlőhöz.

    ```java
    android:id="@+id/text_hello"
    ```

    ![A TextView vezérlőelemre alkalmazott Android: id = "@ + id/text_hello" azonosítót megjelenítő képernyőkép.](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Ezután felvesz egy alosztályt a AndroidManifest.xmlban definiált fogadóhoz. Adjon hozzá a projekthez egy másik új, `FirebaseService` nevű osztályt.

11. Vegye fel a következő importálási utasításokat a `FirebaseService.java` felső részén:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Adja hozzá a következő kódot az `FirebaseService` osztályhoz, ami egy alosztálya `FirebaseMessagingService` .

    Ez a kód felülbírálja a `onMessageReceived` kapott metódust és a jelentések értesítéseit. a leküldéses értesítést is elküldi az Android Notification Managernek az `sendNotification()` metódus használatával. Hívja meg a `sendNotification()` metódust, ha az alkalmazás nem fut, és értesítést kap.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. Android Studio a menüsávon válassza a **Létrehozás**  >  **Újraépítés projekt** lehetőséget, hogy a kódban ne legyen hiba. Ha hibaüzenet jelenik meg az `ic_launcher` ikonról, távolítsa el a következő utasítást a AndroidManifest.xml fájlból: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Győződjön meg arról, hogy rendelkezik virtuális eszközzel az alkalmazás futtatásához. Ha még nem rendelkezik ilyennel, vegyen fel egyet a következő módon:
    1. ![Eszközkezelő megnyitása](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Virtuális eszköz létrehozása](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Futtassa az alkalmazást a kiválasztott eszközön, és ellenőrizze, hogy sikeresen regisztrálja-e az eszközt a hubhoz.

    > [!NOTE]
    > A regisztráció sikertelen lehet a kezdeti indítás során, amíg meg nem `onTokenRefresh()` történik a példány-azonosító szolgáltatás metódusának meghívása. Az értesítési központban történő regisztráció a frissítés után sikeresen megtörténik.

    ![Az eszköz regisztrálása sikerült](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Értesítések küldésének tesztelése az értesítési központból

Leküldéses értesítéseket a [Azure Portal] küldhet a következő lépések végrehajtásával:

1. A Azure Portal a központ értesítési központ lapján válassza a **küldési teszt** lehetőséget a **Hibaelhárítás** szakaszban.
3. A **Platformok** beállításnál válassza az **Android** lehetőséget.
4. Kattintson a **Küldés** gombra.  Az Android-eszközön még nem jelenik meg értesítés, mert nem futtatta rajta a Mobile alkalmazást. Miután futtatta a Mobile alkalmazást, kattintson ismét a **Küldés** gombra az értesítési üzenet megtekintéséhez.
5. A művelet eredményeit a lap alján található listában láthatja.

    ![Azure Notification Hubs – küldés tesztelése](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Megjelenik az értesítési üzenet az eszközön. 

    ![Értesítési üzenet az eszközön](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>A Mobile App futtatása az emulátoron

Mielőtt a leküldéses értesítéseket egy emulátoron belül teszteli, győződjön meg arról, hogy az Emulator-rendszerkép támogatja az alkalmazáshoz választott Google API-szintet. Ha a rendszerkép nem támogatja a natív Google API-kat, akkor előfordulhat, hogy a **szolgáltatás \_ nem \_ érhető el** kivételt.

Győződjön meg arról is, hogy hozzáadta Google-fiókját a futó emulátorhoz a **Beállítások**  >  **fiókok** területen. Ellenkező esetben az FCM-sel való regisztrációra tett kísérletek a **\_ sikertelen hitelesítésre** vonatkozó kivételt okozhatnak.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Firebase Cloud Messaging szolgáltatást használta az értesítések küldéséhez a szolgáltatásban regisztrált összes Android-eszközre. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Oktatóanyag: leküldéses értesítések adott Android-eszközökhöz](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
