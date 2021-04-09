---
title: Leküldéses értesítések küldése a Xamarin az Azure Notification Hubs használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Xamarin iOS-alkalmazásokba.
services: notification-hubs
keywords: ios leküldéses értesítések,leküldéses üzenetek,leküldéses értesítések,leküldéses üzenet
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221136"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Oktatóanyag: leküldéses értesítések küldése Xamarin. iOS-alkalmazásokba az Azure Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére iOS-alkalmazásokba. Létrehoz egy üres Xamarin. iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple push Notification szolgáltatás (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)használatával.

Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre. A befejezett kód a minta [NotificationHubs alkalmazásban][GitHub] érhető el.

Ebben az oktatóanyagban kódot fog létrehozni/frissíteni az alábbi feladatok végrehajtásához:

> [!div class="checklist"]
> * A tanúsítvány-aláírási kérelem fájljának létrehozása
> * Alkalmazás regisztrálása leküldéses értesítésekhez
> * Üzembe helyezési profil létrehozása az alkalmazáshoz
> * Az értesítési központ konfigurálása iOS leküldéses értesítésekhez
> * Teszt leküldéses értesítések küldése

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
* Az [Xcode][Install Xcode] legújabb verziója
* Az iOS 10-es (vagy újabb) verziójával kompatibilis eszköz
* Tagság az [Apple fejlesztői programjában](https://developer.apple.com/programs/).
* [Visual Studio for Mac]
  
  > [!NOTE]
  > Az iOS leküldéses értesítések konfigurációs követelményei miatt a mintaalkalmazást a szimulátor helyett egy fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelnie.

Ennek az oktatóanyagnak az elvégzése a Xamarin.iOS-alkalmazásokkal kapcsolatos összes további Notification Hubs-oktatóanyag elvégzésének előfeltétele.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

### <a name="create-a-new-project"></a>Új projekt létrehozása

1. A Visual Studióban hozzon létre egy új iOS-projektet, és válassza az **Egynézetes alkalmazás** sablont, majd kattintson a **Tovább** elemre.

     ![Visual Studio – Alkalmazástípus kiválasztása][31]

2. Adja meg az alkalmazás nevét és a szervezet azonosítóját, majd kattintson a **tovább**, majd a **Létrehozás** gombra.

3. A Megoldás nézetben kattintson duplán az *Info.plist* elemre, és az **Identitás** területen győződjön meg arról, hogy a csomagazonosító megegyezik a kiépítési profil létrehozásakor használt értékkel. Az **Aláírás** alatt győződjön meg arról, hogy a Developer-fiók ki van választva a **Csoport** területen, hogy az „Aláírás automatikus kezelése” be van jelölve, és hogy az Aláíró tanúsítvány és létesítési profil automatikusan ki van választva.

    ![Visual Studio – iOS-alkalmazás konfigurációja][32]

4. A megoldás nézetben kattintson duplán a elemre, `Entitlements.plist` és győződjön meg arról, hogy a **leküldéses értesítések engedélyezése** jelölőnégyzet be van jelölve.

    ![Visual Studio – iOS-jogosultságok konfigurációja][33]

5. Adja hozzá az Azure-üzenetkezelési csomagot. A megoldás nézetben kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás**  >  **NuGet-csomagok** hozzáadása lehetőséget. Keresse meg a **Xamarin.Azure.NotificationHubs.iOS** elemet, és adja hozzá a csomagot a projektjéhez.

6. Vegyen fel egy új fájlt az osztályba, nevezze el, `Constants.cs` és adja hozzá a következő változókat, és cserélje le a karakterlánc-literál helyőrzőket a `hubname` és a `DefaultListenSharedAccessSignature` korábban feljegyzett értékre.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. A alkalmazásban `AppDelegate.cs` adja hozzá a következő using utasítást:

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. Hozza létre a megvalósítását a alkalmazásban `MSNotificationHubDelegate` `AppDelegate.cs` :

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. A (z) verzióban `AppDelegate.cs` frissítse a `FinishedLaunching()` következő kódot:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. A alkalmazásban `AppDelegate.cs` implementálja a `DidReceivePushNotification` (z) osztály metódusát `AzureNotificationHubListener` :

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. Futtassa az alkalmazást az eszközön.

## <a name="send-test-push-notifications"></a>Teszt leküldéses értesítések küldése

Az [Azure Portal]*Tesztküldés* lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. A parancs egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése][30]

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis könyvtár használatával, mint a Mobile Apps vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el kódtár, az értesítések küldéséhez használhatja közvetlenül a REST API-t is.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban szórásos értesítéseket küldött a háttérrendszerben regisztrált iOS-eszközök mindegyikének. Ha szeretné megtudni, hogy hogyan küldhet értesítéseket adott iOS-eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
