---
title: Leküldéses értesítések küldése az Azure Notification Hubs és Node.js
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket egy Node.js alkalmazásból a Notification Hubs használatával.
keywords: leküldéses értesítés, leküldéses értesítések, node.js leküldés, iOS leküldés
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: eb41593938c670199be38140118f276142ceed43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453223"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Leküldéses értesítések küldése az Azure Notification Hubs és Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nem rendelkezik fiókkal, hozzon létre egy ingyenes próbaverziós fiókot néhány percen belül az [ingyenes Azure-próbaidőszakon](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)keresztül.

Ez az útmutató bemutatja, hogyan küldhet leküldéses értesítéseket az Azure Notification Hubs segítségével közvetlenül egy [Node.js](https://nodejs.org) alkalmazásból.

A tárgyalt forgatókönyvek között leküldéses értesítések küldése az alkalmazásoknak a következő platformokon:

- Android
- iOS
- Univerzális Windows-platform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Az Azure Notification Hubs egy könnyen használható, többplatformos, méretezhető infrastruktúrát biztosít a leküldéses értesítések mobileszközök számára történő küldéséhez. A szolgáltatási infrastruktúrával kapcsolatos részletekért tekintse meg az [Azure Notification Hubs](/previous-versions/azure/azure-services/jj927170(v=azure.100)) oldalát.

## <a name="create-a-nodejs-application"></a>Node.js-alkalmazás létrehozása

Az oktatóanyag első lépése egy új, üres Node.js alkalmazás létrehozása. Node.js alkalmazás létrehozásával kapcsolatos utasításokért lásd: [Node.js alkalmazás létrehozása és telepítése az Azure-webhelyre][nodejswebsite], [Node.js Cloud Service][Node.js Cloud Service] a Windows PowerShell használatával vagy [webhelyről a WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Az alkalmazás konfigurálása Notification Hubs használatára

Az Azure Notification Hubs használatához le kell töltenie és használnia kell a Node.js [Azure-csomagot](https://www.npmjs.com/package/azure), amely a leküldéses értesítési Rest-szolgáltatásokkal kommunikáló beépített segítő könyvtárakat tartalmaz.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Node Package Managert (NPM)

1. Használjon parancssori felületet (például **PowerShell** (Windows), **Terminal** (Mac) vagy **bash** (Linux)), és navigáljon ahhoz a mappához, ahová az üres alkalmazást létrehozta.
2. Végrehajtás `npm install azure-sb` a parancsablakban.
3. A vagy a parancs manuális futtatásával `ls` `dir` ellenőrizheti, hogy létrejött-e a `node_modules` mappa.
4. A mappában keresse meg azt az **Azure** -csomagot, amely az értesítési központ eléréséhez szükséges kódtárakat tartalmazza.

> [!NOTE]
> A NPM telepítésével kapcsolatos további információkért olvassa el a hivatalos [NPM blogját](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>A modul importálása
Szövegszerkesztő használatával adja hozzá a következőt az `server.js` alkalmazás fájljának elejéhez:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Azure Notification hub-kapcsolatok beállítása

Az `NotificationHubService` objektum lehetővé teszi az értesítési központok működését. A következő kód létrehoz egy `NotificationHubService` objektumot a nevű értesítési hubhoz `hubname` . Adja hozzá a fájlt a fájl elejéhez `server.js` , majd az Azure-modul importálására szolgáló utasítás után:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Szerezze be a `connectionstring` [Azure Portal] a kapcsolatok értékét a következő lépések végrehajtásával:

1. A bal oldali navigációs panelen kattintson a **Tallózás** gombra.
2. Válassza a **Notification Hubs** lehetőséget, majd keresse meg azt a hubot, amelyet a mintához használni kíván. Ha segítségre van szüksége egy új értesítési központ létrehozásához, tekintse meg a [Windows áruház első lépések oktatóanyagát](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
3. Válassza a **Beállítások** lehetőséget.
4. Kattintson a **hozzáférési házirendek** elemre. Megtekintheti a megosztott és a teljes hozzáférésű kapcsolati karakterláncokat is.

![Azure Portal – Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> A kapcsolati karakterláncot a [Azure PowerShell](/powershell/azure/) által biztosított **Get-AzureSbNamespace** parancsmaggal vagy az Azure [Command-Line Interface (Azure CLI)](/cli/azure/install-classic-cli) **parancs használatával** is lekérheti.

## <a name="general-architecture"></a>Általános architektúra

Az `NotificationHubService` objektum a következő objektum-példányokat teszi elérhetővé a leküldéses értesítések adott eszközökre és alkalmazásokba történő küldéséhez:

- **Android** – a `GcmService` következő helyen található objektum használata: `notificationHubService.gcm`
- **iOS** – használja az `ApnsService` objektumot, amely a következő címen érhető el: `notificationHubService.apns`
- **Windows Phone-telefon** – használja a `MpnsService` következő helyen elérhető objektumot: `notificationHubService.mpns`
- **Univerzális Windows-platform** – használja a `WnsService` következő helyen elérhető objektumot: `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Útmutató: leküldéses értesítések küldése Android-alkalmazásokba

Az `GcmService` objektum egy `send` metódust biztosít, amellyel leküldéses értesítések küldhetők az Android-alkalmazásokba. A `send` metódus a következő paramétereket fogadja el:

- **Címkék** – a címke azonosítója. Ha nincs megadva címke, a rendszer az értesítést az összes ügyfélnek küldi el.
- **Hasznos** adat – az üzenet JSON-vagy nyers karakterlánc-adattartalma.
- **Visszahívás** – a visszahívási függvény.

A hasznos adatok formátumával kapcsolatos további információkért tekintse meg a hasznos adatokat ismertető [dokumentációt](https://payload.readthedocs.io/en/latest/).

A következő kód a `GcmService` által közzétett példányt használja a `NotificationHubService` leküldéses értesítés küldésére az összes regisztrált ügyfélnek.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Útmutató: leküldéses értesítések küldése iOS-alkalmazásokba

Ugyanaz, mint a fent ismertetett Android-alkalmazásokban, az `ApnsService` objektum egy metódust biztosít, amellyel `send` leküldéses értesítéseket küldhet iOS-alkalmazásokba. A `send` metódus a következő paramétereket fogadja el:

- **Címkék** – a címke azonosítója. Ha nincs megadva címke, a rendszer az értesítést az összes ügyfélnek küldi el.
- **Hasznos** adatok – az üzenet JSON-vagy karakterlánc-adattartalma.
- **Visszahívás** – a visszahívási függvény.

További információ a hasznos adatok formátumáról: a [UserNotifications útmutatójának](https://developer.apple.com/documentation/usernotifications) **értesítési tartalma** szakasza.

A következő kód a `ApnsService` által elérhetővé tett példányt használja a által `NotificationHubService` küldött riasztási üzenet küldéséhez az összes ügyfélnek:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Útmutató: leküldéses értesítések küldése Windows Phone-telefon alkalmazásoknak

Az `MpnsService` objektum olyan `send` metódust biztosít, amellyel leküldéses értesítések küldhetők Windows Phone-telefon alkalmazásokba. A `send` metódus a következő paramétereket fogadja el:

- **Címkék** – a címke azonosítója. Ha nincs megadva címke, a rendszer az értesítést az összes ügyfélnek küldi el.
- **Hasznos** adatok – az üzenet XML-adattartalma.
-   -  TargetName `toast` bejelentési értesítésekhez. `token` csempe értesítéseihez.
- **NotificationClass** – az értesítés prioritása. Az érvényes értékekhez tekintse meg a [kiszolgálói dokumentum leküldéses értesítéseinek http-](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) **fejléc elemei** című szakaszát.
- **Beállítások** – nem kötelező kérelmek fejléce.
- **Visszahívás** – a visszahívási függvény.

Az érvényes `TargetName` `NotificationClass` és a fejléc beállítások listájáért tekintse meg a [kiszolgálói lapról leküldéses értesítéseket](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) .

A következő mintakód a `MpnsService` által a által elérhetővé tett példányt használja a (z) által a `NotificationHubService` Toast leküldéses értesítés küldéséhez:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Útmutató: leküldéses értesítések küldése Univerzális Windows-platform (UWP) alkalmazásoknak

Az `WnsService` objektum olyan `send` metódust biztosít, amellyel leküldéses értesítések küldhetők univerzális Windows-platform alkalmazásokba.  A `send` metódus a következő paramétereket fogadja el:

- **Címkék** – a címke azonosítója. Ha nincs megadva címke, a rendszer az értesítést az összes regisztrált ügyfélnek küldi el.
- **Hasznos** adat – az XML-üzenet tartalma.
- **Típus** – az értesítés típusa.
- **Beállítások** – nem kötelező kérelmek fejléce.
- **Visszahívás** – a visszahívási függvény.

Az érvényes típusok és a kérelmek fejlécek listáját a [leküldéses értesítési szolgáltatás kérése és a válasz fejlécei](/previous-versions/windows/apps/hh465435(v=win.10))című témakörben tekintheti meg.

A következő kód a `WnsService` által elérhetővé tett példányt használja a `NotificationHubService` UWP-alkalmazáshoz való leküldéses értesítés küldéséhez:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Következő lépések

A fenti kódrészletek lehetővé teszik a szolgáltatás-infrastruktúra egyszerű kiépítését, hogy leküldéses értesítéseket nyújtson a legkülönbözőbb eszközökhöz. Most, hogy megismerte a Notification Hubs és a node.js használatának alapjait, kövesse az alábbi hivatkozásokat, ahol további információt talál a képességek további kibővítéséről.

- Tekintse meg az [Azure Notification HUBS](/previous-versions/azure/azure-services/jj927170(v=azure.100))MSDN-referenciáját.
- További minták és megvalósítási részletekért látogasson el az [Azure SDK for Node] adattárba a githubon.

[Node-hoz készült Azure SDK]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
