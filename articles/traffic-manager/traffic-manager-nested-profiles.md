---
title: Beágyazott Traffic Manager profilok az Azure-ban
titleSuffix: Azure Traffic Manager
description: Ez a cikk az Azure "beágyazott profilok" funkcióját ismerteti Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185580"
---
# <a name="nested-traffic-manager-profiles"></a>Beágyazott Traffic Manager-profilok

Traffic Manager számos forgalom-útválasztási módszert tartalmaz, amelyek segítségével szabályozhatja, hogy a Traffic Manager hogyan válassza ki, hogy melyik végpont kapjon forgalmat az egyes végfelhasználók számára. További információ: [Traffic Manager Traffic-Routing metódusok](traffic-manager-routing-methods.md).

Mindegyik Traffic Manager profil egyetlen forgalom-útválasztási módszert határoz meg. Vannak azonban olyan forgatókönyvek, amelyekkel összetettebb forgalmi útválasztás szükséges, mint az egyetlen Traffic Manager profil által biztosított útválasztás. Traffic Manager-profilok beágyazásával több forgalom-útválasztási módszer előnyeit is összekapcsolhatja. A beágyazott profilok lehetővé teszik az alapértelmezett Traffic Manager viselkedés felülbírálását a nagyobb és összetettebb alkalmazások központi telepítésének támogatásához.

Az alábbi példák bemutatják, hogyan használhatók a beágyazott Traffic Manager profilok különböző forgatókönyvekben.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>1. példa: a "teljesítmény" és a "súlyozott" forgalom-útválasztás összekapcsolása

Tegyük fel, hogy üzembe helyezett egy alkalmazást a következő Azure-régiókban: USA nyugati régiója, Nyugat-Európa és Kelet-Ázsia. Traffic Manager "Performance" Traffic – útválasztási módszert használ a forgalomnak a felhasználóhoz legközelebb eső régióba való terjesztéséhez.

![Egyetlen Traffic Manager profil][4]

Tegyük fel, hogy tesztelni szeretné a szolgáltatás frissítését, mielőtt szélesebb körben kellene elvégeznie. Azt szeretné, hogy a "súlyozott" forgalom-útválasztási módszert használja a tesztelési célú központi forgalom kis hányadának irányítására. A tesztelési üzembe helyezést a Nyugat-Európában meglévő éles környezettel együtt kell beállítani.

Egyetlen profilban nem kombinálható mind a "súlyozott", mind a "Performance-Routing. Ennek a forgatókönyvnek a támogatásához létre kell hoznia egy Traffic Manager profilt a két Nyugat-európai végpont és a "súlyozott" forgalom-útválasztási módszer használatával. Ezután adja hozzá ezt a "gyermek" profilt végpontként a "Parent" profilhoz. A szülő profil továbbra is a teljesítmény forgalom-útválasztási módszert használja, és a többi globális telepítést végpontként tartalmazza.

A következő ábra a példát szemlélteti:

![Beágyazott Traffic Manager-profilok][2]

Ebben a konfigurációban a szülő profilon keresztül irányított forgalom általában a régiók közötti forgalmat osztja el. Nyugat-Európában a beágyazott profil a hozzárendelt súlyok alapján osztja el a forgalmat az éles és a tesztelési végpontokra.

Ha a szülő profil a "Performance" Traffic-Routing metódust használja, minden végpontnak helyet kell rendelnie. A hely a végpont konfigurálásakor lesz hozzárendelve. Válassza ki az üzemelő példányhoz legközelebb eső Azure-régiót. Az Azure-régiók az Internet késési táblázat által támogatott hely értékei. További információ: [Traffic Manager "Performance" Traffic – útválasztási módszer](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>2. példa: végpontok figyelése beágyazott profilokban

Traffic Manager aktívan figyeli az egyes szolgáltatási végpontok állapotát. Ha egy végpont állapota nem megfelelő, a Traffic Manager a szolgáltatás rendelkezésre állásának megőrzése érdekében a felhasználókat más végpontokra irányítja. Ez a végpont-figyelési és feladatátvételi viselkedés az összes forgalom-útválasztási módszerre vonatkozik. További információ: [Traffic Manager Endpoint monitoring](traffic-manager-monitoring.md). A végpontok figyelése a beágyazott profilok esetében eltérően működik. A beágyazott profilok esetében a szülő profil közvetlenül nem hajtja végre az állapot-ellenőrzéseket a gyermeken. Ehelyett a gyermek profil végpontjának állapotát használjuk a gyermek profil általános állapotának kiszámításához. Ezek az állapotadatok a beágyazott profilok hierarchiájában vannak propagálva. A szülő profil ezt az összesített állapotot használja annak megállapítására, hogy a rendszer irányítsa-e a forgalmat az alárendelt profilba. A beágyazott profilok állapotának figyelésével kapcsolatos részletes információkért tekintse meg a [gyakori kérdéseket](traffic-manager-FAQs.md#traffic-manager-nested-profiles) .

Ha visszatér az előző példához, tegyük fel, hogy az éles üzembe helyezés nem sikerül a Nyugat-Európában. Alapértelmezés szerint a "gyermek" profil az összes forgalmat a tesztelési üzembe helyezésre irányítja. Ha a teszt üzembe helyezése is meghiúsul, a szülő profil megállapítja, hogy a gyermek profil nem kap forgalmat, mert az összes alárendelt végpont állapota nem kifogástalan. Ezután a szülő profil elosztja a forgalmat a többi régióba.

![Beágyazott profil feladatátvétele (alapértelmezett viselkedés)][3]

Lehet, hogy elégedett a megegyezéssel. Vagy előfordulhat, hogy a Nyugat-európai forgalom jelenleg csak korlátozott számú adatforgalom helyett a tesztelési üzembe helyezésre kerül. Függetlenül attól, hogy milyen állapotban van a teszt üzembe helyezése, a feladatátvételt a többi régióba kívánja végrehajtani, amikor a Nyugat-európai üzemi környezet nem sikerül. A feladatátvétel engedélyezéséhez megadhatja a "MinChildEndpoints" paramétert, ha az alárendelt profilt végpontként konfigurálja a szülő profilban. A paraméter határozza meg az elérhető végpontok minimális számát a gyermek profilban. Az alapértelmezett érték: "1". Ebben a forgatókönyvben a MinChildEndpoints értékét állítsa 2 értékre. A küszöbérték alatt a szülő profil úgy véli, hogy a teljes gyermek profil nem érhető el, és a többi végpontra irányítja a forgalmat.

A következő ábra ezt a konfigurációt szemlélteti:

![Beágyazott profil feladatátvétele a következővel: "MinChildEndpoints" = 2][4]

> [!NOTE]
> A "priority" forgalom – az útválasztási módszer egyetlen végpontra osztja el az összes forgalmat. Így a gyermek profilhoz tartozó MinChildEndpoints nem lehet kisebb, mint "1".

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>3. példa: rangsorolt feladatátvételi régiók a "Performance" forgalom-útválasztásban

A "Performance" forgalom alapértelmezett viselkedése – az útválasztási módszer akkor fordul elő, ha különböző földrajzi helyekhez tartozó végpontokkal rendelkezik, a végfelhasználók a legalacsonyabb hálózati késés szempontjából a "legközelebbi" végponthoz vannak irányítva.

Tegyük fel azonban, hogy a Nyugat-európai forgalom feladatátvételt végez az USA nyugati régiójában, és csak akkor irányítja át a forgalmat más régiókba, ha mindkét végpont nem érhető el. Ezt a megoldást létrehozhatja egy alárendelt profillal a "priority" forgalom – útválasztási módszer használatával.

!["Performance" forgalom-útválasztás preferenciális feladatátvételsel][6]

Mivel a Nyugat-európai végpont magasabb prioritású, mint az USA nyugati végpontja, a rendszer minden forgalmat a Nyugat-európai végpontnak küld, ha mindkét végpont online állapotú. Ha Nyugat-Európa meghibásodik, a rendszer a forgalmat az USA nyugati régiójában irányítja. A beágyazott profillal a forgalom a Kelet-Ázsiare lesz irányítva, ha a Nyugat-Európa és az USA nyugati régiója is sikertelen.

Ezt a mintát minden régiónál megismételheti. Cserélje le mindhárom végpontot a szülő profilba három alárendelt profillal, amelyek mindegyike rangsorolt feladatátvételi sorozatot biztosít.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>4. példa: a "teljesítmény" forgalom továbbításának szabályozása több végpont között ugyanabban a régióban

Tegyük fel, hogy a "Performance" forgalom-útválasztási módszer egy adott régióban egynél több végponttal rendelkező profilban van használatban. Alapértelmezés szerint az adott régióhoz irányított forgalom egyenletesen oszlik el az adott régióban elérhető összes végpont között.

![A "teljesítmény" forgalom-útválasztás a régión belüli forgalom eloszlása (alapértelmezett viselkedés)][7]

A Nyugat-Európában több végpont hozzáadása helyett ezek a végpontok külön gyermek profilba vannak csatolva. A gyermek profilt a rendszer az egyetlen, Nyugat-európai végpontként adja hozzá a szülőhöz. A gyermek profil beállításai vezérelhetik a Nyugat-európai forgalom elosztását, ha az adott régión belül prioritási szintű vagy súlyozott forgalom-útválasztást engedélyeznek.

!["Performance" adatforgalom-útválasztás az egyéni régión belüli forgalom eloszlásával][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>5. példa: végpont figyelési beállításai

Tegyük fel, hogy Traffic Managert használ a régi helyszíni webhelyről érkező forgalom zökkenőmentes átköltöztetésére az Azure-ban üzemeltetett új felhőalapú verzióra. A régi hely esetében a Kezdőlap URI-JÁT a hely állapotának figyelésére szeretné használni. Az új felhőalapú verzió esetében azonban egyéni figyelési oldalt ("/monitor.aspx") kell megvalósítani, amely további ellenőrzéseket is tartalmaz.

![Traffic Manager végpontok figyelése (alapértelmezett viselkedés)][9]

Egy Traffic Manager-profil figyelési beállításai az összes végpontra vonatkoznak egyetlen profilon belül. A beágyazott profilok használatakor a rendszer egy másik alárendelt profilt használ a különböző figyelési beállítások megadásához.

![Végpontok figyelésének Traffic Manager végponti beállításokkal][10]

## <a name="faqs"></a>Gyakori kérdések

* [Hogyan konfigurálja a beágyazott profilokat?](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Hány réteg beágyazását támogatja a Traffic Manger?](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [Összekeverhető más típusú végpontok beágyazott alárendelt profilokkal is, ugyanabban a Traffic Manager-profilban?](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Hogyan vonatkozik a számlázási modell a beágyazott profilokra?](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [Hatással van a beágyazott profilok teljesítményére?](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Hogyan számítja Traffic Manager egy beágyazott végpont állapotát a fölérendelt profilban?](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Következő lépések

További információ a [Traffic Manager profilokról](traffic-manager-overview.md)

Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png