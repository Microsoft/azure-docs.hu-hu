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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81681063"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján adja meg az itt látható mezőket.

    * A **társ**-kezelés típusa beállításnál válassza a **közvetlen** lehetőséget.
    * A **Microsoft Network** esetében válassza a **AS8075** lehetőséget. Ne hozzon létre társítást az ASN 8069-mel. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft-partnerek](mailto:peering@microsoft.com)használják.
    * Válassza  az SKU **alapszintű ingyenes** lehetőséget. Ne válassza a prémium szintű ingyenes lehetőséget, mert speciális alkalmazások számára van fenntartva.
    * Válassza ki azt a **Metro** -helyet, ahol a társítást be szeretné állítani.

        > [!NOTE]
        > Ha a kiválasztott **Metro** helyen már rendelkezik a Microsofttal való kapcsolattal, és az első alkalommal használja a Azure Portal az adott helyen való társítás beállításához, a meglévő társ-összekapcsolási kapcsolatok a megjelenő **kapcsolatok** szakaszban jelennek meg. A Microsoft automatikusan átalakítja ezeket az összekapcsolási kapcsolatokat egy Azure-erőforrásra, hogy az összeset, valamint az új kapcsolatokat egy helyen kezelje. További információ: [örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](../howto-legacy-direct-portal.md).
        >

1. A társítási **kapcsolatok** területen válassza az **új létrehozása** lehetőséget, és adjon hozzá egy sort minden új kapcsolathoz, amelyet be szeretne állítani.

    * A kapcsolódási beállítások konfigurálásához vagy módosításához kattintson a sor Szerkesztés gombjára.

        > [!div class="mx-imgBorder"]
        > ![Szerkesztés gomb](../media/setup-direct-conf-tab-edit.png)
    
    * Egy sor törléséhez válassza a **...**  >  lehetőséget. **Törlés**.

        > [!div class="mx-imgBorder"]
        > ![Törlés gomb](../media/setup-direct-conf-tab-delete.png)

    * A kapcsolatok összes beállítását meg kell adnia az itt látható módon.

         > [!div class="mx-imgBorder"]
         > ![Közvetlen társas kapcsolatok lapja](../media/setup-direct-conf-tab-connection.png)

        1. Válassza ki azt a társítási **létesítményt** , ahol a kapcsolatot be kell állítani.
        1. A **munkamenet-címtartomány** segítségével megállapítható, hogy ki biztosítja a hálózat és a Microsoft közötti BGP-munkamenet beállításához szükséges alhálózatot. Ha meg tudja adni az alhálózatot, válassza a **társ** lehetőséget. Ellenkező esetben válassza a **Microsoft** és a [Microsoft partneri](mailto:peering@microsoft.com) kapcsolat lehetőséget. Ha ezt a lehetőséget választja, a Microsoft feldolgozza a partneri kérelmet. Bizonyos esetekben előfordulhat, hogy a Microsoft nem tud alhálózatokat biztosítani, ami a kérelmek megtagadását eredményezi.
        1. Ha a munkamenet- **cím szolgáltatói** lehetőséget választotta **társként**, adja meg az IPv4-és IPv6-címeket, valamint az előtag-maszkokat a **munkamenet IPv4-előtag** és a **munkamenet IPv6-előtag** mezőiben.
        1. Adja meg, hogy hány IPv4-és IPv6-előtagokat Hirdessen a meghirdetett **IPv4-címek** és a **meghirdetett IPv6-címek maximális** száma mezőben.
        1. Módosítsa a **teljes sávszélesség** csúszkát, hogy tükrözze a kapcsolat sávszélességét.
        1. A kapcsolódási beállítások mentéséhez kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépést további kapcsolatok hozzáadásához bármely olyan létesítményben, ahol a Microsoft a hálózattal együtt található a korábban kiválasztott **metrón** belül.

1. Az összes szükséges kapcsolat hozzáadása után válassza a **felülvizsgálat + létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Társítási konfiguráció lap végleges](../media/setup-direct-conf-tab-final.png)

1. Figyelje meg, hogy a portál a beírt adatok alapszintű érvényesítését futtatja. A felső menüszalagon megjelenik a *végső érvényesítést futtató* üzenet...

    > [!div class="mx-imgBorder"]
    > ![Társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Az üzenet *sikeres érvényesítése* után ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás** gombra kattintva. A kérelem módosításához válassza az **előző** lehetőséget, majd ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú küldés](../media/setup-direct-review-tab-submit.png)

1. A kérelem elküldése után várjon, amíg a telepítés befejeződik. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés megjelenik az itt látható módon.

    > [!div class="mx-imgBorder"]
    > ![Sikeres társítás](../media/setup-direct-success.png)
