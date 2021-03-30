---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678550"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján adja meg az itt látható mezőket.

    > [!div class="mx-imgBorder"]
    > ![Peering Page Exchange-társi típus létrehozása](../media/setup-exchange-conf-tab.png)

    * A **társ**-kezelés típusa beállításnál válassza az **Exchange** lehetőséget.
    * Válassza  az SKU **alapszintű ingyenes** lehetőséget.
    * Válassza ki azt a **Metro** -helyet, ahová át szeretné alakítani az Azure-erőforrást. Ha a kiválasztott **metróban** nem Azure-erőforrásra konvertált kapcsolattal rendelkezik a Microsofttal, akkor ezek a kapcsolatok a megjelenő **kapcsolatok** szakaszban jelennek meg. Most már konvertálhatja ezeket a társ-kapcsolatokat egy Azure-erőforráshoz.

        > [!div class="mx-imgBorder"]
        > ![Egyenrangú kapcsolatok listája](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Az örökölt kapcsolati kapcsolatok beállításai nem módosíthatók. Ha további összekapcsolási kapcsolatokat szeretne hozzáadni a Microsofthoz a kiválasztott **metró** helyén, válassza az **új létrehozása** lehetőséget. További információ: [Exchange-társ létrehozása vagy módosítása a portál használatával](../howto-exchange-portal.md).
        >

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Figyelje meg, hogy a portál a beírt adatok alapszintű érvényesítését futtatja. A felső menüszalagon megjelenik a *végső érvényesítést futtató* üzenet...

    > [!div class="mx-imgBorder"]
    > ![Társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Az üzenet *sikeres érvényesítése* után ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás** gombra kattintva. Ha módosítania kell a kérést, válassza az **előző** lehetőséget, és ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú küldés](../media/setup-exchange-review-tab-submit.png)

1. A kérelem elküldése után várjon, amíg a telepítés befejeződik. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés megjelenik az ábrán látható módon.

    > [!div class="mx-imgBorder"]
    > ![Sikeres társítás](../media/setup-direct-success.png)
