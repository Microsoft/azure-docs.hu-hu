---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028197"
---
Ebben a szakaszban a legfrissebb híreket címkézett sablonértesítésekként fogja elküldeni egy .NET-konzolalkalmazásból.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást:
    1. A menüben válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.
    1. Az **új projekt létrehozása** területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget a C# elemnél a sablonok listájában, majd kattintson a **Tovább gombra**.
    1. Adja meg az alkalmazás nevét.
    1. A **megoldáshoz** válassza a **Hozzáadás a megoldáshoz** lehetőséget, majd válassza a **Létrehozás** lehetőséget a projekt létrehozásához.

1. Válassza az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt** , majd a konzol ablakban futtassa a következő parancsot:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Ez a művelet hozzáadja az Azure Notification Hubs SDK-ra mutató hivatkozást a [Microsoft. Azure. NotificationHubs] csomag használatával.

1. Nyissa meg a *program. cs* fájlt, és adja hozzá a következő `using` utasítást:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. A `Program` osztályban adja hozzá a következő metódust, vagy ha már létezik, cserélje le azt a következőre:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Ez a kód sablonértesítéseket küld a sztringtömb mind a hat címkéjének. A címkék használatával biztosítható, hogy az eszközök csak a regisztrált kategóriákhoz tartozó értesítéseket fogadják.

1. A `<hub name>` és a `<connection string with full access>` helyőrzőket cserélje le a fenti kódban az értesítési központ nevére és a *DefaultFullSharedAccessSignature* az értesítési központ irányítópultjáról származó kapcsolati sztringjére.

1. A `Main()` metódusban adja hozzá a következő sorokat:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Hozza létre a konzolalkalmazást.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft. Azure. NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/