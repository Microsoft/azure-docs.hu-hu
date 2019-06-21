---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179617"
---
1. A Mac számítógépén indítsa el a **Keychain Access**. A bal oldali navigációs sávon a **kategória**, nyissa meg **saját tanúsítványok**. Keresse meg az SSL-tanúsítványt, amelyet az előző szakaszban töltött le, és majd kiadni a tartalmát. Válassza ki a tanúsítványt csak (ne válassza a titkos kulcsot). Ezután [exportálja](https://support.apple.com/kb/PH20122?locale=en_US).
2. Az a [az Azure portal](https://portal.azure.com/)válassza **összes tallózása** > **App Services**. Ezután válassza ki a Mobile Apps háttérrendszerre. 
3. A **beállítások**válassza **App Service Push**. Ezután válassza ki az értesítési központ nevére. 
4. Lépjen a **Apple leküldéses értesítési szolgáltatások** >  **-tanúsítvány feltöltése**. Töltse fel a .p12 fájlt, és kiválasztja a megfelelő **mód** (attól függően, hogy az SSL-ügyféltanúsítvány a korábban az éles, akár a védőfal). Mentse a módosításokat.

A szolgáltatás most már konfigurálva van leküldéses értesítések IOS-eszközökön.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
