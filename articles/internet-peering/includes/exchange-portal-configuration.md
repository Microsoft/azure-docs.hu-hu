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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680959"
---
1. A társítás **létrehozása** oldalon a **konfiguráció** lapon töltse ki a mezőket az ábrán látható módon.

    > [!div class="mx-imgBorder"]
    > ![Peering Page Exchange-társi típus létrehozása](../media/setup-exchange-conf-tab.png)

    * A **társ**-kezelés típusa beállításnál válassza az **Exchange** lehetőséget.
    * Válassza  az SKU **alapszintű ingyenes** lehetőséget.
    * Válassza ki azt a **Metro** -helyet, ahol a társítást be szeretné állítani.

        > [!NOTE]
        > Ha a kiválasztott **Metro** -helyen már rendelkezik a Microsofttal való kapcsolattal, és a portálon először használja a társítást ezen a helyen, a meglévő társ-összekapcsolási kapcsolatok a megjelenő **kapcsolatok** szakaszban lesznek felsorolva. A Microsoft automatikusan átalakítja ezeket az összekapcsolási kapcsolatokat egy Azure-erőforrásra, így egyetlen helyen kezelheti őket az új kapcsolatokkal együtt. További információ: [örökölt Exchange-társítás átalakítása Azure-erőforrásra a portál használatával](../howto-legacy-exchange-portal.md).
        >

1. A társítási **kapcsolatok** területen válassza az **új létrehozása** lehetőséget, és adjon hozzá egy sort minden új kapcsolathoz, amelyet be szeretne állítani.

    * A kapcsolódási beállítások konfigurálásához vagy módosításához kattintson a sor Szerkesztés gombjára.

        > [!div class="mx-imgBorder"]
        > ![Szerkesztés gomb](../media/setup-exchange-conf-tab-edit.png)

    * Egy sor törléséhez válassza a **...**  >  lehetőséget. **Törlés**.

        > [!div class="mx-imgBorder"]
        > ![Törlés gomb](../media/setup-exchange-conf-tab-delete.png)

    * A kapcsolatok összes beállítását meg kell adnia az itt látható módon.

         > [!div class="mx-imgBorder"]
         > ![Exchange-partneri kapcsolatok lapja](../media/setup-exchange-conf-tab-connection.png)

        1. Válassza ki azt a társítási **létesítményt** , ahol a kapcsolatot be kell állítani.
        1. Az **IPv4-cím** és az **IPv6-cím** mezőbe írja be azokat az IPv4-és IPv6-címeket, amelyek a Microsoft-útválasztókon a szomszéd parancs használatával konfigurálhatók.
        1. Adja meg, hogy hány IPv4-és IPv6-előtagokat Hirdessen a meghirdetett **IPv4-címek** és a **meghirdetett IPv6-címek maximális** száma mezőben.
        1. A kapcsolódási beállítások mentéséhez kattintson **az OK gombra** .

1. Ismételje meg a lépést további kapcsolatok hozzáadásához minden olyan létesítményben, ahol a Microsoft a hálózattal együtt található, a korábban kiválasztott **metrón** belül.

1. Az összes szükséges kapcsolat hozzáadása után válassza a **felülvizsgálat + létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú konfiguráció lap](../media/setup-exchange-conf-tab-final.png)

1. Figyelje meg, hogy a portál a beírt adatok alapszintű érvényesítését futtatja. A felső menüszalagon megjelenik a *végső érvényesítést futtató* üzenet...

    > [!div class="mx-imgBorder"]
    > ![Társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Az üzenet *sikeres érvényesítése* után ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás** gombra kattintva. A kérelem módosításához válassza az **előző** lehetőséget, majd ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú küldés](../media/setup-exchange-review-tab-submit.png)

1. A kérelem elküldése után várjon, amíg a telepítés befejeződik. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés megjelenik az itt látható módon.

    > [!div class="mx-imgBorder"]
    > ![Sikeres társítás](../media/setup-direct-success.png)
