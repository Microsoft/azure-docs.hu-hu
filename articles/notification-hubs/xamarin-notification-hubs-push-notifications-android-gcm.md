---
title: Leküldéses értesítések küldése Xamarin. Android-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin Android-alkalmazásokba.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221034"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Oktatóanyag: leküldéses értesítések küldése a Xamarin. Android-alkalmazásoknak Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin.Android-alkalmazásokba. Létre fog hozni egy üres Xamarin.Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával. Az értesítési központ használatával fog leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs alkalmazásban](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) érhető el.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Egy Firebase-projekt létrehozása és a Firebase Cloud Messaging engedélyezése
> * Értesítési központ létrehozása
> * Egy Xamarin.Android-alkalmazás létrehozása és csatlakoztatása az értesítési központhoz
> * Tesztértesítések küldése az Azure Portalról

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
* Windows rendszeren [Visual Studio és Xamarin], vagy OS X rendszeren [Visual Studio for Mac].
* Aktív Google-fiók

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Egy Firebase-projekt létrehozása és a Firebase Cloud Messaging engedélyezése

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Az értesítési központ GCM/FCM beállításainak konfigurálása

1. Válassza a **Google (GCM/FCM)/** lehetőséget a bal oldali menüben a **Beállítások** szakaszban.
2. Adja meg a Google Firebase-konzolon megjegyzett **kiszolgáló kulcsát** .
3. Válassza az eszköztár **Save** (Mentés) elemét.

    ![Képernyőkép az értesítési központról az Azure Portalon, a Google G C M F C M-vel kiemelve, és piros színnel jelenik meg.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Az értesítési központ konfigurálva van az FCM-mel való együttműködésre, és rendelkezik a kapcsolati sztringekkel az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Xamarin. Android-alkalmazás létrehozása és az értesítési központhoz való kapcsolódása

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Egy Visual Studio-projekt létrehozása és NuGet-csomagok hozzáadása

> [!NOTE]
> Az ebben az oktatóanyagban leírt lépések a Visual Studio 2017-es verziójában találhatók. 

1. A Visual Studióban nyissa meg a **fájl** menüt, válassza az **új**, majd a **projekt** elemet. Az **új projekt** ablakban végezze el a következő lépéseket:
    1. Bontsa ki a **telepített**, **Visual C#**, majd az **Android** elemet.
    2. Válassza az **Android-alkalmazás (Xamarin)** elemet a listából.
    3. Adjon meg a projekt **nevét**.
    4. Válassza ki a projekt **helyét** .
    5. Válassza az **OK** gombot.

        ![New Project (Új projekt) párbeszédpanel](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Az **új Android-alkalmazás** párbeszédpanelen válassza az **üres alkalmazás** lehetőséget, majd kattintson **az OK gombra**.

    ![Képernyőkép, amely kiemeli az üres alkalmazás sablonját.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. A **Solution Explorer** (Megoldáskezelő) ablakában bontsa ki a **Properties** (Tulajdonságok) elemet, majd kattintson az **AndroidManifest.xml** fájlra. Frissítse a csomag nevét arra a csomagnévre, amelyet akkor adott meg, amikor hozzáadta a Firebase Cloud Messaginget a projekthez a Google Firebase konzolján.

    ![Csomag neve a GCM-ben](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. A következő lépésekkel állítsa be a Project Android-verzióját a projekthez az **android 10,0** -re: 
    1. Kattintson a jobb gombbal a projektre, és válassza a **Tulajdonságok** lehetőséget. 
    1. Az **Android-verzióval való fordításhoz: (cél-keretrendszer)** mezőben válassza az **Android 10,0** lehetőséget. 
    1. Válassza az **Igen** lehetőséget az üzenetablakban a cél keretrendszer módosításának folytatásához.
1. Adja hozzá a szükséges NuGet-csomagokat a projekthez a következő lépések végrehajtásával:
    1. Kattintson a jobb gombbal a projektre, és válassza a **Manage Nuget Packages…** (NuGet-csomagok kezelése) lehetőséget.
    1. Váltson a **telepített** lapra, válassza a **Xamarin.Android.support.design** lehetőséget, majd a jobb oldali ablaktáblán kattintson a **frissítés** elemre a csomag legújabb verzióra való frissítéséhez.
    1. Váltson a **Tallózás** lapra. Keresse meg a **Xamarin. GooglePlayServices. Base** kifejezést. A találatok listájában válassza a **Xamarin.GooglePlayServices.Base** elemet. Ezt követően válassza az **Install** (Telepítés) parancsot.

        ![Google Play-szolgáltatások NuGet-csomagjai](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban keressen rá a **Xamarin.Firebase.Messaging** kifejezésre. A találatok listájában válassza a **Xamarin.Firebase.Messaging** elemet. Ezt követően válassza az **Install** (Telepítés) parancsot.
    7. Ezután keressen rá a **Xamarin.Azure.NotificationHubs.Android** kifejezésre. A találatok listájában válassza a **Xamarin.Azure.NotificationHubs.Android** elemet. Ezt követően válassza az **Install** (Telepítés) parancsot.

### <a name="add-the-google-services-json-file"></a>A Google-szolgáltatás JSON-fájljának hozzáadása

1. Másolja a `google-services.json` letöltött fájlt a Google Firebase-konzolról a projekt mappájába.
2. Hozzáadás `google-services.json` a projekthez.
3. Válassza `google-services.json` a **megoldáskezelő** ablakot.
4. A **Properties** (Tulajdonságok) panelen a Build Action (Felépítési művelet) számára állítsa be a **GoogleServicesJson** értéket. Ha nem látja a **GoogleServicesJson** műveletet, zárja be a Visual Studiót, indítsa el újra, nyissa meg újra a projektet, és próbálkozzon újból.

    ![GoogleServicesJson felépítési művelet](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Értesítési központok beállítása a projektben

#### <a name="registering-with-firebase-cloud-messaging"></a>Regisztráció a Firebase Cloud Messagingben

1. Ha Google Cloud Messagingról Firebase-re végez áttelepítést, a projekt `AndroidManifest.xml` fájlja elavult GCM-konfigurációt tartalmazhat, ami értesítési ismétlődéseket eredményezhet. Szerkessze a fájlt, és távolítsa el a következő sorokat a `<application>` szakaszon belül, ha van:

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Adja hozzá a következő utasításokat **az alkalmazás elem előtt** .

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Gyűjtse össze az alábbi információikat az Android-alkalmazásra és az értesítési központra vonatkozóan:

   * **Figyelési kapcsolati sztring**: Az [Azure Portal] irányítópultján válassza a **Kapcsolati sztringek megtekintése** elemet. Másolja `DefaultListenSharedAccessSignature` ki az értékhez tartozó kapcsolatok karakterláncát.
   * **Központ neve**: A központ neve az [Azure Portalon]. Például: *mynotificationhub2*.
4. A **megoldáskezelő** ablakban kattintson a jobb gombbal a **projektre**, válassza a **Hozzáadás**, majd a **Class (osztály**) lehetőséget.
5. Hozzon létre egy `Constants.cs` osztályt a Xamarin projekt számára, és adja meg a következő állandó értékeket a osztályban. A helyőrzőket cserélje le az értékekkel.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Adja hozzá a következő using utasításokat a következőkhöz `MainActivity.cs` :

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. Adja hozzá a következő tulajdonságokat a MainActivity osztályhoz:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Adjon hozzá egy nevű osztályt `AzureListener` a projekthez.
10. Adja hozzá az alábbi using utasításokat a következőhöz: `AzureListener.cs` .

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Adja hozzá a következőt a Class deklarációhoz, és az osztálya örökölje `Java.Lang.Object` és implementálja a következőket `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Adja hozzá a következő kódot az `MyFirebaseMessagingService` osztályban a fogadott üzenetek feldolgozásához.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Hozza létre** a projektet.
1. Az alkalmazás **futtatása** az eszközön vagy a betöltött emulátoron

## <a name="send-test-notification-from-the-azure-portal"></a>Tesztértesítés küldése az Azure Portalról

Az [Azure Portal]**Tesztküldés** lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. A parancs egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Ha egy könyvtár nem érhető el a háttérhez, a REST API közvetlenül is használhatja az értesítési üzenetek küldéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban szórásos értesítéseket küldött a háttérrendszerben regisztrált Android-eszközök mindegyikének. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott Android-eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio és Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure Portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android