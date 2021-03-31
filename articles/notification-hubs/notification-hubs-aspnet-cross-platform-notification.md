---
title: Platformfüggetlen értesítések küldése a felhasználóknak az Azure Notification Hubs (ASP.NET) használatával
description: Ismerje meg, hogyan küldhet Notification Hubs sablonokat egyetlen kérelemben, amely az összes platformra kiterjedő, a platformtól független értesítést küld.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92313891"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Platformfüggetlen értesítések küldése az Azure Notification Hubs

Ez az oktatóanyag az előző oktatóanyagra épül, az [Azure Notification Hubs használatával küldhet értesítéseket adott felhasználóknak]. Ez az oktatóanyag azt ismerteti, hogyan lehet leküldéses értesítéseket küldeni minden olyan eszközre, amely regisztrálva van egy adott hitelesített felhasználó számára. Ennek a megközelítésnek több kérelemre van szüksége ahhoz, hogy értesítést küldjön az egyes támogatott ügyféloldali platformokra. Az Azure Notification Hubs támogatja a sablonokat, amellyel meghatározhatja, hogy egy adott eszköz hogyan kapjon értesítéseket. Ez a módszer leegyszerűsíti a platformok közötti értesítések küldését.

Ez a cikk bemutatja, hogyan használhatja ki a sablonok előnyeit, hogy az összes platformot tároló értesítést küldjön. Ez a cikk egyetlen kérelmet használ a platform semleges értesítéseinek küldéséhez. További információ a sablonokról: [Notification Hubs Overview (áttekintés][Templates]).

> [!IMPORTANT]
> Windows Phone-telefon a 8,1-es és korábbi projektek nem támogatottak a Visual Studio 2019-ben. További információ: [Visual Studio 2019 platform Targeting and Compatibility](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> A Notification Hubs segítségével egy eszköz több sablont is regisztrálhat ugyanazzal a címkével. Ebben az esetben a címkére irányuló bejövő üzenet több értesítést küld az eszköznek, egy pedig minden sablonhoz. Ez a folyamat lehetővé teszi, hogy ugyanazt az üzenetet több vizualizációs értesítésben jelenítse meg, például jelvényként, valamint egy Windows áruházbeli alkalmazásban bejelentési értesítésként.

## <a name="send-cross-platform-notifications-using-templates"></a>Platformfüggetlen értesítések küldése sablonok használatával

Ez a szakasz a [küldési értesítések küldése adott felhasználóknak az Azure Notification Hubs oktatóanyag segítségével] létrehozott mintakód használatával készült. [A teljes mintát letöltheti a githubról](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Ha platformfüggetlen értesítéseket szeretne küldeni a sablonok használatával, tegye a következőket:

1. A Visual Studióban **megoldáskezelő** bontsa ki a **vezérlők** mappát, majd nyissa meg a *RegisterController. cs* fájlt.

1. Keresse meg a kód blokkját a `Put` metódusban, amely új regisztrációt hoz létre, majd cserélje le a `switch` tartalmat a következő kódra:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Ez a kód meghívja a platform-specifikus metódust, hogy natív regisztráció helyett sablon-regisztrációt hozzon létre. Mivel a sablon regisztrációja natív regisztrációból származik, nem kell módosítania a meglévő regisztrációkat.

1. A **megoldáskezelő** a **vezérlők** mappában Nyissa meg a **NotificationsController. cs** fájlt. Cserélje le az `Post` metódust az alábbi kódra:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Ez a kód egyszerre küld értesítést az összes platformra. Nem ad meg natív adattartalmat. A Notification Hubs a megadott címke értékkel rendelkező összes eszközre kiépíti és biztosítja a megfelelő adattartalmat a regisztrált sablonokban meghatározottak szerint.

1. Tegye közzé újra a webes API-projektet.

1. Futtassa újra az ügyfélalkalmazás alkalmazást annak ellenőrzéséhez, hogy a regisztráció sikeres volt-e.

1. Szükség esetén telepítse az ügyfélalkalmazás egy második eszközre, majd futtassa az alkalmazást. Egy értesítés jelenik meg minden eszközön.

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte az oktatóanyagot, ismerkedjen meg a következő cikkekben található Notification Hubs és sablonokkal:

* A sablonok használatára vonatkozó más forgatókönyv esetén tekintse [meg a leküldéses értesítések adott Windows-eszközökön univerzális Windows-platform alkalmazások][Use Notification Hubs to send breaking news] oktatóanyagát.
* További információ a sablonokról: [Notification Hubs Overview (áttekintés][Templates]).

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Értesítések küldése adott felhasználóknak az Azure Notification Hubs használatával]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)