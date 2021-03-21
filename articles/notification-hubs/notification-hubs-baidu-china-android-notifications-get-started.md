---
title: Ismerkedés az Azure Notification Hubs Baiduval való használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések Android-eszközökre történő küldéséhez a Baidu segítségével.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 06/19/2019
ms.custom: devx-track-java, devx-track-csharp
ms.openlocfilehash: 098fb0ed967dcacac24ce3abfd4843f9fe14ff49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727175"
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Ismerkedés a Notification Hubs Baiduval való használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

A felhőalapú Baidu-értesítés egy kínai felhőszolgáltatás, amellyel leküldéses értesítések küldhetők mobileszközökre.

Mivel a Google Play és az FCM (Firebase Cloud Messaging) nem érhető el Kínában, más alkalmazás-áruházakat és leküldési szolgáltatásokat kell használni. Ilyen például az értesítési központ által jelenleg használt Baidu is.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyaghoz a következőkre lesz szükség:

* Android SDK (feltételezzük, hogy Android Studiót használ), amelyet az [Android webhelyéről](https://go.microsoft.com/fwlink/?LinkId=389797) tölthet le
* [Baidu Push Android SDK]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).

Első lépésként tegye a következőket:

1. Hozzon létre egy Baidu-fiókot.
2. Hozzon létre egy felhőalapú Baidu-értesítés projektet, és jegyezze fel az API-kulcsot és a titkos kulcsot.

## <a name="configure-a-new-notification-hub"></a>Új értesítési központ konfigurálása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Az értesítési központban válassza az **Értesítési szolgáltatások**, majd a **Baidu (Android China)** lehetőséget.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs – Baidu](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

&emsp;&emsp;7. Görgessen lefelé a Baidu-értesítések beállítási szakaszáig. Adja meg a felhőalapú Baidu-értesítési projekt Baidu-konzoljában korábban beszerzett API-kulcsot és titkos kulcsot. Ezután kattintson a Save (Mentés) gombra.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs – Baidu, titkos kulcsok](./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png)

Ezzel konfigurálta az értesítési központot a Baiduval folytatott együttműködésre, és rendelkezésére állnak a **kapcsolati sztringek** is, amelyekkel regisztrálhatja az alkalmazást a leküldéses értesítések küldéséhez és fogadásához.

Jegyezze fel a Kapcsolati adatok elérése ablakban látható `DefaultListenSharedAccessSignature` és `DefaultFullSharedAccessSignature` kapcsolati karakterláncokat.

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

1. Az Android Studióban hozzon létre egy új Android-projektet (File (Fájl) > New (Új) > New Project (Új projekt)).

    ![Azure Notification Hubs – Baidu, új projekt](./media/notification-hubs-baidu-get-started/AndroidNewProject.png)

2. Adjon meg egy alkalmazásnevet, és győződjön meg arról, hogy a Minimum Required SDK (minimális SDK-követelmény) verziószáma API 16: Android 4.1. **Győződjön meg arról is, hogy a csomagnév (应用包名) megegyezik a felhőalapú Baidu-értesítési portálon szereplővel**

    ![Azure Notification Hubs – Baidu min SDK1 ](./media/notification-hubs-baidu-get-started/AndroidMinSDK.png) ![ Azure Notification Hubs – BAIDU min SDK2](./media/notification-hubs-baidu-get-started/AndroidMinSDK2.png)

3. Kattintson a Next (Tovább) gombra, majd folytassa a varázsló használatát, amíg a Create Activity (Tevékenység létrehozása) ablak meg nem jelenik. Győződjön meg arról, hogy az Empty Activity (Üres tevékenység) lehetőség van kiválasztva, majd válassza a Finish (Befejezés) lehetőséget egy új Android-alkalmazás létrehozásához.

    ![Azure Notification Hubs – Baidu, tevékenység hozzáadása](./media/notification-hubs-baidu-get-started/AndroidAddActivity.png)

4. Győződjön meg arról, hogy a Project Build Target (Projekt célbuildje) beállítás értéke megfelelő.

5. Ezután vegyen fel Azure Notification Hubs-kódtárakat. Az alkalmazás `Build.Gradle` fájljában adja hozzá az alábbi sorokat a dependencies (függőségek) szakaszhoz.

    ```javascript
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

    A dependencies (függőségek) szakasz után vegye fel az alábbi tárhelyet.

    ```javascript
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

    A lista ütközésének elkerülése érdekében adja hozzá a következő kódot a projekt `Manifest.xml` fájljában:

    ```xml
    <manifest package="YOUR.PACKAGE.NAME"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android">
    ```

    és az `<application/>` címkében:

    ```xml
    <application
        tools:replace="android:allowBackup,icon,theme,label">
    ```

6. Töltse le és csomagolja ki a [Baidu Push Android SDK-t](https://push.baidu.com/doc/android/api). Másolja a `pushservice-x.y.z jar` fájlt a libs mappába. Ezután másolja a `.so` fájlokat az Android alkalmazás `src/main/jniLibs` (hozzon létre egy új mappát) mappáiba.

    ![Azure Notification Hubs – Baidu SDK kódtárak](./media/notification-hubs-baidu-get-started/BaiduSDKLib.png)

7. A projekt `libs` mappájában kattintson a jobb gombbal a `pushervice-x.y.z.jar` fájlra, és válassza a **Hozzáadás könyvtárként** lehetőséget, hogy az a projekthez tartozó könyvtárat is tartalmazza.

    ![Azure Notification Hubs – Baidu, hozzáadás kódtárként](./media/notification-hubs-baidu-get-started/BaiduAddAsALib.jpg)

8. Nyissa meg az Android `AndroidManifest.xml` -projekt fájlját, és adja hozzá a BAIDU SDK számára szükséges engedélyeket. **Cserélje le a `YOURPACKAGENAME` karakterláncot saját csomagjának nevére**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR" />
    !! <uses-permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" />
    !!<permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" android:protectionLevel="normal" />

    ```

9. Cserélje le a *yourprojectname* (például `com.example.BaiduTest`) részt a következő konfigurációra az alkalmazáselemben a `.MainActivity` tevékenységelem után:

    ```xml
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaViewActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaViewActivity" />
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaListActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaListActivity"
        android:launchMode="singleTask" />

    <!-- Push application definition message -->
    <receiver android:name=".MyPushMessageReceiver">
        <intent-filter>

            <!-- receive push message-->
            <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
            <!-- receive bind,unbind,fetch,delete.. message-->
            <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
            <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.PushServiceReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            <action android:name="android.intent.action.MEDIA_MOUNTED" />
            <action android:name="android.intent.action.USER_PRESENT" />
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.RegistrationReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.METHOD" />
            <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REMOVED" />

            <data android:scheme="package" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.baidu.android.pushservice.PushService"
        android:exported="true"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
        </intent-filter>
    </service>

    <service
        android:name="com.baidu.android.pushservice.CommandService"
        android:exported="true" />

    <!-- Adapt the ContentProvider declaration required for the Android N system, and the write permissions include the application package name-->
    <provider
        android:name="com.baidu.android.pushservice.PushInfoProvider"
        android:authorities="com.baidu.push.example.bdpush"
        android:exported="true"
        android:protectionLevel="signature"
        android:writePermission="baidu.push.permission.WRITE_PUSHINFOPROVIDER. yourprojectname  " />

    <!-- API Key of the Baidu application -->
    <meta-data
        android:name="api_key"
        !!   android:value="api_key" />
    </application>
    ```

10. Adjon hozzá egy új, `ConfigurationSettings.java` nevű osztályt a projekthez.

    ```java
    public class ConfigurationSettings {
        public static String API_KEY = "...";
        public static String NotificationHubName = "...";
        public static String NotificationHubConnectionString = "...";
    }
    ```

    Állítsa az `API_KEY` sztring értékét a felhőalapú Baidu-projektből lekért API_KEY értékre.

    Állítsa a `NotificationHubName` sztring értékét az értesítési központ [Azure Portalon] található nevére, majd a `NotificationHubConnectionString` sztringnek adja meg az [Azure Portalról] származó `DefaultListenSharedAccessSignature` értéket.

11. Nyissa meg a MainActivity.java fájlt, majd adja hozzá a következőket az onCreate metódushoz:

    ```java
    PushManager.startWork(this, PushConstants.LOGIN_TYPE_API_KEY,  API_KEY );
    ```

12. Adjon hozzá egy új, `MyPushMessageReceiver.java` nevű osztályt, majd adja hozzá a következő kódot. Ez az osztály kezeli a Baidu leküldési kiszolgálóról kapott leküldéses értesítéseket.

    ```java
    package your.package.name;

    import android.content.Context;
    import android.content.Intent;
    import android.os.AsyncTask;
    import android.text.TextUtils;
    import android.util.Log;

    import com.baidu.android.pushservice.PushMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import org.json.JSONException;
    import org.json.JSONObject;

    import java.util.List;

    public class MyPushMessageReceiver extends PushMessageReceiver {

        public static final String TAG = MyPushMessageReceiver.class
                .getSimpleName();
        public static NotificationHub hub = null;
        public static String mChannelId, mUserId;

        @Override
        public void onBind(Context context, int errorCode, String appid,
                        String userId, String channelId, String requestId) {
            String responseString = "onBind errorCode=" + errorCode + " appid="
                    + appid + " userId=" + userId + " channelId=" + channelId
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Binding successful
                Log.d(TAG, " Binding successful");
            }
            try {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName,
                            ConfigurationSettings.NotificationHubConnectionString,
                            context);
                    Log.i(TAG, "Notification hub initialized");
                }
            } catch (Exception e) {
                Log.e(TAG, e.getMessage());
            }
            mChannelId = channelId;
            mUserId = userId;

            registerWithNotificationHubs();
        }
        private void registerWithNotificationHubs() {

            new AsyncTask<Void, Void, Void>() {
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        hub.registerBaidu(mUserId, mChannelId);
                        Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        @Override
        public void onMessage(Context context, String message,
                            String customContentString) {
            String messageString = " onMessage=\"" + message
                    + "\" customContentString=" + customContentString;
            Log.d(TAG, messageString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

        }

        @Override
        public void onNotificationArrived(Context context, String title, String description, String customContentString) {
            String notifyString = " Notice Arrives onNotificationArrived  title=\"" + title
                    + "\" description=\"" + description + "\" customContent="
                    + customContentString;
            Log.d(TAG, notifyString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onNotificationClicked(Context context, String title, String description, String customContentString) {
            String notifyString = " onNotificationClicked title=\"" + title + "\" description=\""
                    + description + "\" customContent=" + customContentString;
            Log.d(TAG, notifyString);
            Intent intent = new Intent(context.getApplicationContext(),MainActivity.class);
            intent.putExtra("title",title);
            intent.putExtra("description",description);
            intent.putExtra("isFromNotify",true);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.getApplicationContext().startActivity(intent);

        }

        @Override
        public void onSetTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onSetTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onDelTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onDelTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onListTags(Context context, int errorCode, List<String> tags,
                            String requestId) {
            String responseString = "onListTags errorCode=" + errorCode + " tags="
                    + tags;
            Log.d(TAG, responseString);

        }

        @Override
        public void onUnbind(Context context, int errorCode, String requestId) {
            String responseString = "onUnbind errorCode=" + errorCode
                    + " requestId = " + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Unbinding is successful
                Log.d(TAG, " Unbinding is successful ");
            }
        }
    }
    ```

## <a name="send-notifications-to-your-app"></a>Értesítések küldése az alkalmazásnak

Az [Azure Portalról] érkező értesítések fogadásának gyors teszteléséhez használja az értesítési központ konfigurációs képernyőjén található **Küldés** gombot, ahogy az az alábbi képernyőn is látható:

![Képernyőkép az Azure Portalról, amelyen a test Send (küldés) lehetőség piros színnel van kiemelve, és egy piros nyíl mutat rá. ](./media/notification-hubs-baidu-get-started/BaiduTestSendButton.png)
 ![ Képernyőkép a Baidu-teszt küldési lapjáról a Azure Portal.](./media/notification-hubs-baidu-get-started/BaiduTestSend.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el könyvtár, az értesítési üzenetek küldéséhez használhatja közvetlenül a REST API-t.

Az egyszerűség kedvéért ez az oktatóanyag konzolalkalmazással mutatja be, hogyan küldhet értesítéseket a .NET SDK-val. Az értesítéseknek ASP.NET-háttérrendszerből történő küldéséhez azonban következő lépésként [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) oktatóanyagot ajánljuk. 

Íme néhány megközelítés az értesítések küldéséhez:

* **REST-felület**: A [REST-felület](/previous-versions/azure/reference/dn223264(v=azure.100)) használatával bármilyen háttérplatformon támogathatja az értesítéseket.
* **Microsoft Azure Notification Hubs .net SDK**: a Visual studióhoz készült Nuget csomagkezelő segédprogramban futtassa a [Microsoft. Azure. NotificationHubs Install-Package csomagot](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [A Notification Hubs használata a Node.js-ből](notification-hubs-nodejs-push-notification-tutorial.md).
* **Mobile Apps**: A [Leküldéses értesítések hozzáadása Mobile Apps-alkalmazáshoz](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) témakörben találhat példát arra, hogy hogyan küldhetők értesítések a Notification Hubs szolgáltatással integrált Azure App Service Mobile Apps háttéralkalmazásból.
* **Java/PHP**: „A Notification Hubs használata Javából/PHP-ből” ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)) témakörben találhat példát arra, hogyan küldhetők értesítések a REST API-k használatával.

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Nem kötelező) Értesítések küldése .NET-konzolalkalmazásból.

Ebben a szakaszban az értesítések .NET-konzolalkalmazásból történő küldését mutatjuk be.

1. Hozzon létre egy új Visual C#-konzolalkalmazást:

    ![Képernyőkép az új projekt párbeszédpanelről, a Console Application Visual C# lehetőség kiemelve.](./media/notification-hubs-baidu-get-started/ConsoleProject.png)

2. A Package Manager Console (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban hajtsa végre az alábbi parancsot:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

    Ez az utasítás hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a [Microsoft.Azure.Notification Hubs NuGet-csomaggal](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

    ![Képernyőkép a Package Manager konzol párbeszédpanelről, amelyen a Küldés az értesítési központba lehetőség piros színnel van kiválasztva.](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Nyissa meg a `Program.cs` fájlt, és adja hozzá a következő using utasítást:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

4. A `Program` osztályban adja hozzá a következő metódust, és cserélje le a `DefaultFullSharedAccessSignatureSASConnectionString` és a `NotificationHubName` elemeket a saját értékeire.

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
        string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
        var result = await hub.SendBaiduNativeNotificationAsync(message);
    }
    ```

5. Adja hozzá a következő sorokat a `Main` metódushoz:

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Ha egy valódi telefonon kívánja tesztelni az alkalmazást, csak csatlakoztassa a telefont a számítógéphez egy USB-kábellel. Ez a művelet feltölti az alkalmazást a csatlakoztatott telefonra.

Az alkalmazásnak az emulátorral való teszteléséhez kattintson a **Run** (Futtatás) elemre az Android Studio felső eszköztárán, majd válassza ki az alkalmazást: ez elindítja az emulátort, majd betölti és futtatja az alkalmazást.

Az alkalmazás lekéri a `userId` és a `channelId` azonosítókat a Baidu leküldéses értesítési szolgáltatásból, és regisztrál az értesítési központban.

Tesztértesítést az [Azure Portal] hibakeresési lapjáról küldhet. Ha a .NET konzolalkalmazást a Visual Studióhoz készítette, az alkalmazás futtatásához csak nyomja le az F5 billentyűt a Visual Studióban. Az alkalmazás elküld egy értesítést, amely az eszköz vagy az emulátor felső értesítési területén jelenik meg.

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: https://push.baidu.com/sdk/push_client_sdk_for_android
[Azure Portal]: https://portal.azure.com/
[Baidu portal]: https://www.baidu.com/
