---
title: Régiók közötti Load Balancer (előzetes verzió)
titleSuffix: Azure Load Balancer
description: A Azure Load Balancer-hez készült Cross region Load Balancer-rétegek áttekintése.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 898b0d00fcad99a1059e01ad54ce13d192f1227f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398011"
---
# <a name="cross-region-load-balancer-preview"></a>Régiók közötti Load Balancer (előzetes verzió)

A Azure Load Balancer elosztja a terheléselosztó előtérben megjelenő bejövő forgalmat a háttérbeli készlet példányaira.

Az Azure standard Load Balancer támogatja a régiók közötti terheléselosztást, ami lehetővé teszi a Geo-redundáns HA-forgatókönyveket, például a következőket:

* Több régióból származó bejövő forgalom.
* [Azonnali globális feladatátvétel](#regional-redundancy) a következő optimális regionális üzembe helyezéshez.
* A régiókban lévő eloszlás elosztása a legközelebbi Azure-régióba [rendkívül késleltetéssel](#ultra-low-latency).
* Egyetlen végpont mögötti vertikális [Felskálázási](#ability-to-scale-updown-behind-a-single-endpoint) képesség.
* [Statikus IP-cím](#static-ip)
* [Ügyfél IP-címének megőrzése](#client-ip-preservation)
* [Meglévő terheléselosztó megoldás létrehozása](#build-cross-region-solution-on-existing-azure-load-balancer) tanulási görbe nélkül

> [!IMPORTANT]
> A régiók közötti terheléselosztó jelenleg előzetes verzióban érhető el, és nem általánosan elérhető.  A régiók közötti terheléselosztó előzetes verziójához való hozzáféréshez vegye fel a kapcsolatot a következővel: [crossregionlb@microsoft.com](mailto:crossregionlb@microsoft.com) . </br> </br>
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A régiók közötti terheléselosztás a regionális standard Load Balancer esetében is ugyanazokat az előnyöket kínálja, mint a nagy teljesítmény és a kis késleltetés. 

A régiók közötti terheléselosztó előtérbeli IP-konfigurációja statikus, és a [legtöbb Azure-régióban](#participating-regions)meghirdetve.

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Régiók közötti Load Balancer diagramja" border="true":::

> [!NOTE]
> A régiók közötti terheléselosztó terheléselosztási szabályának háttér-portjának meg kell egyeznie a regionális standard Load Balancer terheléselosztási szabályának/bejövő NAT-szabályának előtérbeli portjával. 

### <a name="regional-redundancy"></a>Regionális redundancia

Konfigurálja a regionális redundanciát úgy, hogy egy globális előtér-nyilvános IP-címet ad hozzá a meglévő terheléselosztó számára. 

Ha az egyik régió meghibásodik, a rendszer átirányítja a forgalmat a legközelebbi, legközelebb egészséges regionális Load balancerbe.  

A régiók közötti terheléselosztó állapotának vizsgálata 20 másodpercenként adatokat gyűjt a rendelkezésre állásról. Ha egy regionális terheléselosztó a rendelkezésre állását 0-ra, a régiók közötti terheléselosztó a hibát fogja felderíteni. A regionális terheléselosztó ezt követően kikerül a rotációból. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="A globális régió forgalmi nézetének ábrája." border="true":::

### <a name="ultra-low-latency"></a>Rendkívül alacsony késés

A földrajzi közelségi terheléselosztási algoritmus a felhasználók földrajzi elhelyezkedésén és a regionális környezeteken alapul. 

Az ügyféltől megkezdett forgalom a legközelebb eső régióba kerül, és a Microsoft globális hálózati gerincén keresztül utazik, hogy a legközelebbi regionális üzembe helyezéshez jusson. 

Tegyük fel például, hogy egy régiók közötti terheléselosztó standard Load balancerrel rendelkezik az Azure-régiókban:

* USA nyugati régiója
* Észak-Európa

Ha a folyamat elindult Seattle-ből, a forgalom az USA nyugati régiójában lép be. Ez a régió a legszorosabb résztvevő régió a Seattle-ből. A forgalmat a legközelebbi, az USA nyugati régiójában található Load Balancer irányítja.

Az Azure régiók közötti terheléselosztó a földrajzi közelségi terheléselosztási algoritmust használja az útválasztási döntéshez. 

A regionális terheléselosztó konfigurált terheléselosztási módja arra szolgál, hogy a végső útválasztási döntést hozza, ha több regionális terheléselosztó van használatban a földrajzi közelséghez.

További információ: [Azure Load Balancer elosztási módjának konfigurálása](https://docs.microsoft.com/azure/load-balancer/load-balancer-distribution-mode).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Egyetlen végpont mögötti vertikális Felskálázási képesség

Ha a régiók közötti terheléselosztó globális végpontját elérhetővé teszi az ügyfelek számára, a globális végpont mögötti regionális telepítéseket adhat hozzá vagy távolíthat el az ügyfelek hatása nélkül. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statikus IP-cím
A régiók közötti Load Balancer statikus nyilvános IP-címmel rendelkezik, amely gondoskodik arról, hogy az IP-cím változatlan maradjon. Ha [többet szeretne](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#allocation-method) megtudni a statikus IP-címekről, tekintse meg a következőt

### <a name="client-ip-preservation"></a>Ügyfél IP-címének megőrzése
A régiók közötti Load Balancer egy 4. rétegbeli csatlakoztatott hálózati terheléselosztó. Ez a továbbítás megőrzi a csomag eredeti IP-címét.  Az eredeti IP-cím a virtuális gépen futó kód számára érhető el. Ez a megőrzés lehetővé teszi, hogy az IP-címekre vonatkozó logikát alkalmazzon.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Régiók közötti megoldás létrehozása meglévő Azure Load Balancer
A régiók közötti terheléselosztó háttér-készlete egy vagy több regionális terheléselosztót tartalmaz. 

A meglévő terheléselosztó-környezetek hozzáadása egy régiós terheléselosztó számára egy magasan elérhető, régiók közötti környezetben történő üzembe helyezéshez.

A **hazai régió** , ahol a régiók közötti terheléselosztó üzembe van helyezve. Ez a régió nem befolyásolja a forgalom irányításának módját. Ha egy otthoni régió leáll, nem befolyásolja a forgalom áramlását.

### <a name="home-regions"></a>Otthoni régiók
* USA 2. keleti régiója
* USA nyugati régiója
* Nyugat-Európa
* Délkelet-Ázsia
* USA középső régiója
* Észak-Európa
* Kelet-Ázsia

> [!NOTE]
> A régiók közötti terheléselosztó csak a fenti 8 régió egyikében helyezhető üzembe.

A **bevett régió** az a hely, ahol a terheléselosztó globális nyilvános IP-címe elérhető. 

A felhasználó által megkezdett forgalom a legközelebbi részt vevő régióba fog utazni a Microsoft Core hálózaton keresztül. 

A régiók közötti terheléselosztó a megfelelő regionális terheléselosztó felé irányítja át a forgalmat.

### <a name="participating-regions"></a>Részt vevő régiók
* USA keleti régiója 
* Nyugat-Európa 
* Central US 
* USA 2. keleti régiója 
* USA nyugati régiója 
* Észak-Európa 
* USA déli középső régiója 
* USA 2. nyugati régiója 
* Az Egyesült Királyság déli régiója 
* Délkelet-Ázsia 
* USA északi középső régiója 
* Kelet-Japán 
* Kelet-Ázsia 
* USA nyugati középső régiója 
* Délkelet-Ausztrália 
* Kelet-Ausztrália 
* Közép-India 

## <a name="limitations"></a>Korlátozások

* A régiók közötti előtér-IP-konfigurációk csak nyilvánosak. A belső frontend jelenleg nem támogatott.

* Magán-vagy belső terheléselosztó nem adható hozzá a régiók közötti terheléselosztó háttér-készletéhez 

* A régiók közötti IPv6-előtérbeli IP-konfigurációk nem támogatottak. 

* Az állapot-mintavétel jelenleg nem konfigurálható. Az alapértelmezett állapot-mintavétel 20 másodpercenként automatikusan gyűjti a regionális terheléselosztó rendelkezésre állási adatait. 

## <a name="pricing-and-sla"></a>Díjszabás és SLA
A régión kívüli terheléselosztó a standard Load Balancer [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) -át osztja meg.

 
## <a name="next-steps"></a>További lépések

- A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) című témakört.
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Load Balancer – [Gyakori kérdések](load-balancer-faqs.md)
