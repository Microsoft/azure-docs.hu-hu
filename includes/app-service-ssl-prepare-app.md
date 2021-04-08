---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8b3c1992a1cff18390f9d1332103e0650af418e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347724"
---
## <a name="prepare-your-web-app"></a>A webalkalmazás előkészítése

Egyéni TLS/SSL-kötések létrehozásához vagy az App Service alkalmazáshoz tartozó Ügyféltanúsítványok engedélyezéséhez a [app Service tervének](https://azure.microsoft.com/pricing/details/app-service/) **alapszintű**, **standard**, **prémium** vagy **elszigetelt** szinten kell lennie. Ebben a lépésben ellenőrzi, hogy a webalkalmazás a támogatott tarifacsomagban van-e.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az [Azure Portalt](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Keresse meg a webalkalmazást.

Keresse meg és válassza ki a **app Services**.

![App Services kiválasztása](./media/app-service-ssl-prepare-app/app-services.png)

A **app Services** lapon válassza ki a webalkalmazás nevét.

![Képernyőfelvétel a App Services oldaláról Azure Portal az összes futó Web Apps listáját jeleníti meg, a lista első alkalmazásával.](./media/app-service-ssl-prepare-app/select-app.png)

A webalkalmazás felügyeleti lapján található.  

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

A webalkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszhoz, és válassza a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Ellenőrizze, hogy a webalkalmazás nem az **F1** vagy a **D1** szinten van-e. A webalkalmazás aktuális tarifacsomagja sötétkék kerettel van kiemelve.

![A tarifacsomag ellenőrzése](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Az egyéni SSL nem támogatott az **F1** és a **D1** szinten. Ha vertikális felskálázásra van szüksége, kövesse az alábbi szakaszban található lépéseket. Ellenkező esetben zárjuk be a vertikális **felskálázás** lapot, és hagyja [ki a app Service-csomag vertikális felskálázása](#scale-up-your-app-service-plan) szakaszt.

### <a name="scale-up-your-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![Tarifacsomag kiválasztása](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Vertikális felskálázási értesítés](./media/app-service-ssl-prepare-app/scale-notification.png)

