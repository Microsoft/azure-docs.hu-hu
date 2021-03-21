---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 89549ac3233557d1991e7f728e5051432c2c29fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026758"
---
A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis könyvtár használatával, mint a Mobile Apps vagy az ASP.NET. Ha egy könyvtár nem érhető el a háttérrendszer számára, akkor a REST API közvetlenül is használhatja az értesítési üzenetek küldéséhez.

Az alábbiakban felsorolunk néhány olyan oktatóanyagot, amelyet érdemes lehet áttekinteni az értesítések küldéséhez:

* Azure Mobile Apps: a [leküldéses értesítések hozzáadása iOS-alkalmazáshoz](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)című témakörből megtudhatja, hogyan küldhet értesítéseket az Notification Hubs-vel integrált Mobile apps háttérből.  
* ASP.NET: [A Notification Hubs használata a felhasználóknak szánt leküldéses értesítések küldéséhez](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
* Azure Notification Hub Java SDK: A [How to use Notification Hubs from Java](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md) (A Notification Hubs használata Javával) ismerteti, hogyan küldhetők értesítések a Javával. Ez az Eclipse-ben lett tesztelve Android-fejlesztéshez.
* PHP: [How to use Notification Hubs from PHP](../articles/notification-hubs/notification-hubs-php-push-notification-tutorial.md) (A Notification Hubs használata PHP-val).