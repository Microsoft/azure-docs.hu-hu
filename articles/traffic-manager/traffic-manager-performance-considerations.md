---
title: Az Azure Traffic Manager teljesítményével kapcsolatos megfontolások | Microsoft Docs
description: A Traffic Manager teljesítményének megismerése és a webhely teljesítményének tesztelése a Traffic Manager használatakor
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186175"
---
# <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

Ez az oldal a Traffic Manager használatával kapcsolatos teljesítmény-megfontolásokat ismerteti. Vegyük példaként a következő esetet:

A webhely példányai a WestUS és a EastAsia régióban vannak. A példányok egyike nem ellenőrzi a Traffic Manager-mintavétel állapotát. Az alkalmazások forgalmát az egészséges régióra irányítja a rendszer. Ez a feladatátvétel várható, de a teljesítmény a távoli régióba érkező forgalom késése alapján lehet probléma.

## <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

Az egyetlen teljesítményre gyakorolt hatás, amelyet a Traffic Manager használhat a webhelyén, a kezdeti DNS-keresés. A Traffic Manager profiljának nevére vonatkozó DNS-kérelmet a trafficmanager.net zónát futtató Microsoft DNS-gyökérkiszolgáló kezeli. Traffic Manager feltölti és rendszeresen frissíti a Microsoft DNS-legfelső szintű kiszolgálóit a Traffic Manager házirend és a mintavételi eredmények alapján. Tehát még a kezdeti DNS-keresés során sem küldi el a rendszer a DNS-lekérdezéseket Traffic Manager.

Traffic Manager több összetevőből áll: a DNS-névkiszolgálók, az API-szolgáltatás, a tárolási réteg és a végpont-figyelési szolgáltatás. Ha egy Traffic Manager szolgáltatás-összetevő meghibásodik, akkor nincs hatással a Traffic Manager profiljához társított DNS-névre. A Microsoft DNS-kiszolgálók rekordjai változatlanok maradnak. A végpontok figyelése és a DNS frissítése azonban nem történik meg. Ezért a Traffic Manager nem tudja frissíteni a DNS-t, hogy a feladatátvételi helyre mutasson, ha az elsődleges hely leáll.

A DNS-névfeloldás gyors, és a rendszer gyorsítótárazza az eredményeket. A kezdeti DNS-keresés sebessége az ügyfél által a névfeloldáshoz használt DNS-kiszolgálóktól függ. Az ügyfél általában a ~ 50 MS-ban belül elvégezheti a DNS-keresést. A keresés eredményei gyorsítótárazva lesznek a DNS élettartama (TTL) időtartamára. Traffic Manager alapértelmezett ÉLETTARTAMa 300 másodperc.

A forgalom nem Traffic Manageron keresztül áramlik. Miután a DNS-címkeresés befejeződött, az ügyfél IP-címmel rendelkezik a webhely egy példányához. Az ügyfél közvetlenül csatlakozik ehhez a címnek, és nem halad át Traffic Manageron. A kiválasztott Traffic Manager szabályzatnak nincs befolyása a DNS-teljesítményre. A teljesítmény-útválasztási módszer azonban negatív hatással lehet az alkalmazás felhasználói élményére. Ha például a házirend átirányítja a forgalmat a Észak-Amerikaról egy Ázsiában üzemeltetett példányra, az adott munkamenetek hálózati késése lehet teljesítményproblémák.

## <a name="measuring-traffic-manager-performance"></a>Traffic Manager teljesítmény mérése

Több webhely is használható a Traffic Manager-profilok teljesítményének és viselkedésének megismerésére. Ezen helyek közül sok ingyenes, de lehetnek korlátai. Egyes helyek magasabb szintű monitorozást és jelentéskészítést biztosítanak díj ellenében.

Az ezeken a helyeken lévő eszközök a DNS-késéseket mérik, és megjelenítik az ügyfelek helyeinek megoldott IP-címeit a világon. Ezeknek az eszközöknek a többsége nem gyorsítótárazza a DNS-eredményeket. Az eszközök ezért a teljes DNS-lekérdezést mutatják a tesztek futtatásakor. A saját ügyfélről történő teszteléskor csak egyszer kell megkeresnie a teljes DNS-keresési teljesítményt az élettartam időtartama alatt.

## <a name="sample-tools-to-measure-dns-performance"></a>Mintaeszközök a DNS-teljesítmény mérésére

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    A SolveDNS számos teljesítménynövelő eszközt kínál. A DNS-összehasonlító eszköz képes megmutatni, hogy mennyi ideig tart a DNS-név feloldása és a más DNS-szolgáltatókkal való összehasonlítás.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Az egyik legegyszerűbb eszköz a WebSitePulse. Adja meg az URL-címet a DNS-feloldási idő, az első bájt, az utolsó bájt és az egyéb teljesítményadatok megtekintéséhez. Három különböző tesztelési hely közül választhat. Ebben a példában láthatja, hogy az első végrehajtás azt mutatja, hogy a DNS-címkeresés 0,204 másodpercet vesz igénybe.

    ![Képernyőfelvétel: a "DNS" keresési eredmény kiemelése a "WebSitePulse" eszközre mutat.](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Mivel az eredmények gyorsítótárazva vannak, a második teszt ugyanarra a Traffic Manager végpontra vonatkozóan a DNS-címkeresés 0,002 másodpercet vesz igénybe.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA-alkalmazás szintetikus figyelője](https://asm.ca.com/en/checkit.php)

    A korábbi nevén a Watch-Mouse pipa weboldala, a webhely a DNS-feloldási időt jeleníti meg egyszerre több földrajzi régióból. Adja meg az URL-címet a DNS-feloldási idő, a kapcsolódási idő és a sebesség megjelenítéséhez több földrajzi helyről. Ezzel a teszttel megtekintheti, hogy melyik üzemeltetett szolgáltatást adja vissza a rendszer a világ különböző helyein.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Fütyülés](https://tools.pingdom.com/)

    Ez az eszköz teljesítmény-statisztikát biztosít a weblapok egyes elemeihez. Az oldal elemzése lapon a DNS-címkeresés során eltelt idő százalékos arányát jeleníti meg.

* [Mi a DNS?](https://www.whatsmydns.net/)

    Ez a hely 20 különböző helyről végez DNS-keresést, és megjeleníti az eredményeket egy térképen.

* [Webes felület kiásása](https://www.digwebinterface.com)

    Ez a hely részletesebb DNS-információkat jelenít meg, többek között A CNAME adatokat és A rekordokat. Győződjön meg arról, hogy a beállítások területen a "kimenet színezése" és a "statisztika" lehetőséget választja, majd az "összes" lehetőséget a névszerverek területen.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a Traffic Manager forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)

[Traffic Manager-beállítások tesztelése](traffic-manager-testing-settings.md)

[Operations on Traffic Manager (A Traffic Managerrel végezhető műveletek) (REST API-referencia)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager-parancsmagok](/powershell/module/az.trafficmanager)