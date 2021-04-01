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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678818"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján adja meg az itt látható mezőket.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú lap konfigurációjának létrehozása](../media/setup-direct-conf-tab.png)

    * A **társ**-kezelés típusa beállításnál válassza a **közvetlen** lehetőséget.
    * A **Microsoft Network** esetében válassza a **AS8075** lehetőséget. Ne válassza az ASN 8069 lehetőséget. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft-partnerek](mailto:peering@microsoft.com)használják.
    * Válassza  az SKU **alapszintű ingyenes** lehetőséget. Ne válassza a prémium szintű ingyenes lehetőséget, mert speciális alkalmazások számára van fenntartva.
    * Válassza ki azt a **Metro** -helyet, ahová át szeretné alakítani az Azure-erőforrást. Ha a kiválasztott **metróban** nem Azure-erőforrásra konvertált kapcsolattal rendelkezik a Microsofttal, akkor ezek a kapcsolatok a megjelenő **kapcsolatok** szakaszban jelennek meg. Most már konvertálhatja ezeket a társ-kapcsolatokat egy Azure-erőforráshoz.

        > [!div class="mx-imgBorder"]
        > ![Egyenrangú kapcsolatok listája](../media/setup-directlegacy-conf-tab.png)

1. Ha frissítenie kell a sávszélességet, a kapcsolat beállításainak módosításához kattintson a Szerkesztés gombra.

    > [!div class="mx-imgBorder"]
    > ![Szerkesztés gomb](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Ha további összekapcsolási kapcsolatokat szeretne hozzáadni a Microsofthoz a kiválasztott **metró** helyén, válassza az **új létrehozása** lehetőséget. További információ: közvetlen társítás [létrehozása vagy módosítása a portál használatával](../howto-direct-portal.md).
    >

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Figyelje meg, hogy a portál a beírt adatok alapszintű érvényesítését futtatja. A felső menüszalagon megjelenik a *végső érvényesítést futtató* üzenet...

    > [!div class="mx-imgBorder"]
    > ![Társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Az üzenet *sikeres érvényesítése* után ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás** gombra kattintva. A kérelem módosításához válassza az **előző** lehetőséget, majd ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú küldés](../media/setup-direct-review-tab-submit.png)

1. A kérelem elküldése után várjon, amíg a telepítés befejeződik. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés megjelenik az itt látható módon.

    > [!div class="mx-imgBorder"]
    > ![Sikeres társítás](../media/setup-direct-success.png)
