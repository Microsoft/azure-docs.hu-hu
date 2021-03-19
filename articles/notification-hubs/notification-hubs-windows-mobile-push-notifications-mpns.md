---
title: Leküldéses értesítések küldése Windows Phone-telefon alkalmazásoknak az Azure Notification Hubs használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Windows Phone 8 és Windows Phone 8.1 rendszeren futó Silverlight-alkalmazásokba.
services: notification-hubs
documentationcenter: windows
keywords: leküldéses értesítés,leküldéses értesítés,windows phone leküldéses értesítés
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998339"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>Oktatóanyag: leküldéses értesítések küldése Windows Phone-telefon alkalmazásoknak Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Windows Phone 8 és Windows Phone 8.1 rendszeren futó Silverlight-alkalmazásokba. A Windows Phone 8.1 (nem Silverlight) rendszerre vonatkozó útmutatásért tekintse meg az oktatóanyag [Univerzális Windows-platformmal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kapcsolatos verzióját.

Az oktatóanyagban létrehoz egy üres Windows Phone 8-alkalmazást, amely leküldéses értesítéseket fogad a Microsoft leküldéses értesítéseket kezelő szolgáltatásának (MPNS) a használatával. Az alkalmazást létrehozását követően az értesítési központ használatával fog leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre.

> [!NOTE]
> A Notification Hubs Windows Phone SDK nem támogatja a Windows leküldéses értesítéseket kezelő szolgáltatásának (WNS) használatát Windows Phone 8.1 Silverlight-alkalmazások esetén. Ha a WNS-t szeretné használni (az MPNS helyett) Windows Phone 8.1 Silverlight-alkalmazásokkal kövesse a [Notification Hubs – Windows Phone Silverlight-oktatóanyag], amely REST API-kat használ.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Értesítési központ létrehozása
> * Windows Phone-alkalmazás létrehozása
> * Tesztértesítés küldése

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/) Kezdés előtt.
* [Visual Studio 2015 Express mobilfejlesztési összetevőkkel](https://www.visualstudio.com/vs/older-downloads/)

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Windows Phone 8-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="create-your-notification-hub"></a>Az értesítési központ létrehozása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Windows Phone (MPNS) beállításainak konfigurálása

1. Válassza a **Windows Phone (MPNS)** lehetőséget a **NOTIFICATION SETTINGS** (Értesítési beállítások) kategóriában.
2. Válassza az **Enable authentication push** (Leküldéses értesítések hitelesítésének engedélyezése) elemet.
3. Válassza az eszköztár **Save** (Mentés) elemét.

    ![Azure Portal – Nem hitelesített leküldéses értesítések engedélyezése](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    A szolgáltatás létrehozza a központot, és nem hitelesített értesítések Windows Phone rendszerű eszközökre való küldésére konfigurálja.

    > [!NOTE]
    > Ez az oktatóanyag nem hitelesített módban használja az MPNS-t. Az MPNS nem hitelesített módja korlátozásokat tartalmaz az egyes csatornákra küldhető értesítések tekintetében. A Notification Hubs tanúsítvány feltöltésének lehetővé tételével támogatja az [MPNS hitelesített módját](/previous-versions/windows/apps/ff941099(v=vs.105)).

## <a name="create-a-windows-phone-application"></a>Windows Phone-alkalmazás létrehozása

Ebben a szakaszban egy új Windows Phone-alkalmazást hoz létre, amely regisztrálja magát az értesítési központban.

1. Hozzon létre egy új Windows Phone 8-alkalmazást a Visual Studióban.

    ![Visual Studio – Új projekt – Windows Phone-alkalmazás][13]

    A Visual Studio 2013 Update 2 vagy újabb verzióban Windows Phone Silverlight-alkalmazást hozzon létre.

    ![Visual Studio – Új projekt – Üres alkalmazás – Windows Phone Silverlight][11]
2. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
3. Keresse meg a `WindowsAzure.Messaging.Managed` csomagot, kattintson a **Telepítés** gombra, majd fogadja el a használati feltételeket.

    ![Visual Studio – NuGet Package Manager (NuGet-csomagkezelő)][20]
4. Nyissa meg az App.xaml.cs fájlt, majd adja hozzá a következő `using` utasításokat:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. Adja hozzá a következő kódot a metódus tetején `Application_Launching` `App.xaml.cs` :

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > Az érték `MyPushChannel` egy olyan index, amely a [HttpNotificationChannel](/previous-versions/ff402781(v=vs.110)) -gyűjtemény meglévő csatornájának keresésére szolgál. Amennyiben nem létezik ott ilyen, hozzon létre egy bejegyzést ezen a néven.

    Szúrja be a központ nevét és az `DefaultListenSharedAccessSignature` előző szakaszban feljegyzett, a kapcsolatok karakterláncát.
    Ez a kód lekéri a csatorna URI azonosítóját az alkalmazás számára az MPNS-ből, majd regisztrálja a csatorna URI azonosítóját az értesítési központban. Emellett biztosítja azt, hogy a csatorna URI azonosítója legyen regisztrálva az értesítési központban az alkalmazás minden indításakor.

   > [!NOTE]
   > Ez az oktatóanyag egy bejelentési értesítést küld az eszközre. Csempe-értesítés küldésekor Ehelyett a metódust kell meghívnia a `BindToShellTile` csatornán. A Toast és a csempe értesítések támogatásához hívja a `BindToShellTile` és a-t is  `BindToShellToast` .

6. A Megoldáskezelőben bontsa ki a **Tulajdonságok** csomópontot, nyissa meg a `WMAppManifest.xml` fájlt, kattintson a **Képességek** fülre, és jelölje be az **ID_CAP_PUSH_NOTIFICATION** képességet. Az alkalmazás mostantól képes leküldéses értesítések fogadására.

    ![Visual Studio – Windows Phone-alkalmazás képességei][14]
7. Nyomja le az `F5` billentyűt az alkalmazás futtatásához. Megjelenik egy regisztrációs üzenet az alkalmazásban.
8. Zárja be az alkalmazást, vagy váltson a kezdőlapra.

   > [!NOTE]
   > A bejelentési leküldéses értesítések fogadásához az alkalmazás nem futhat az előtérben.

## <a name="test-send-a-notification"></a>Tesztértesítés küldése

1. Az Azure Portalon váltson az Áttekintés lapra.
2. Kattintson a **Tesztküldés** gombra.

    ![Tesztküldés gomb](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. A **Tesztküldés** ablakban hajtsa végre az alábbi műveleteket:

    1. A **Platformok** beállításnál válassza a **Windows Phone** lehetőséget.
    2. Az **Értesítés típusa** beállításnál válassza a **Bejelentés** lehetőséget.
    3. **Küldés** kiválasztása
    4. A művelet **eredményét** az ablak alján található listában láthatja.

        ![Tesztküldés ablak](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. A Windows Phone-emulátorban vagy a Windows Phone-eszközön ellenőrizze, hogy látható-e a leküldéses üzenet.

    ![Értesítés a Windows Phone-eszközön](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Következő lépések

Ebben az egyszerű példában leküldéses értesítéseket küldött az összes Windows Phone 8 rendszerű eszközre. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[Notification Hubs – Windows Phone Silverlight-oktatóanyagot]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
