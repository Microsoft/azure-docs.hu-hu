---
title: Kimenő kapcsolatok hibaelhárítása a Azure Load Balancer
description: Megoldás a kimenő kapcsolattal kapcsolatos gyakori problémákra a Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 1f52900086afef09d69b80bf44474d5514e25235
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748879"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Kimenő kapcsolatok hibáinak elhárítása

Ez a cikk megoldásokat biztosít az olyan gyakori problémákra, amelyek a kimenő kapcsolatoknál léphetnek fel a Azure Load Balancer. Az ügyfelek által tapasztalt kimenő kapcsolatokkal kapcsolatos legtöbb probléma az SNAT-portok kimerítése és a csomagok eldobott csomagokhoz vezető kapcsolati időtúllépések miatt merül fel. Ez a cikk az egyes problémák megoldásának lépéseit biztosítja.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> SNAT- (PAT-) portkimerülés kezelése
A PAT-hez [](load-balancer-outbound-connections.md) [használtphemerális](load-balancer-outbound-connections.md) portok kimeríthető erőforrások, amelyek leírása [nyilvános IP-cím](load-balancer-outbound-connections.md) nélküli önálló virtuális gépek, nyilvános IP-cím nélkül, elosztott terhelésű virtuális gépek pedig nyilvános [IP-cím nélkül.](load-balancer-outbound-connections.md) Az útmutató segítségével monitorozhatja aphemerális portok használatát, és összehasonlíthatja az aktuális foglalással az SNAT-kimerítés kockázatának meghatározásához vagy [megerősítéséhez.](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation)

Ha tudja, hogy számos kimenő TCP- vagy UDP-kapcsolatot kezdeményez ugyanerre a cél IP-címre és portra, és azt figyeli, hogy a [](load-balancer-outbound-connections.md#preallocatedports) kimenő kapcsolatok sikertelenek, vagy támogatási szolgálata arra figyelmezteti, hogy kimeríti az SNAT-portokat (a [PAT](load-balancer-outbound-connections.md)által használt, előre lefoglalt, idő előtti portokat), számos általános kockázatcsökkentési lehetőség áll rendelkezésre. Tekintse át ezeket a lehetőségeket, és döntse el, mi érhető el és melyik a legmegfelelőbb az Ön forgatókönyvéhez. Lehetséges, hogy egy vagy több is segíthet ennek a forgatókönyvnek a kezelésében.

Ha nem tudja megérteni a kimenő kapcsolat viselkedését, használhatja az IP-verem statisztikáit (netstat). Vagy hasznos lehet a kapcsolat viselkedésének megfigyelése csomagrögzítések használatával. Ezeket a csomagrögzítéseket elvégezheti a példány vendég operációs rendszerén, vagy használhatja a Network Watcher [a csomagrögzítéshez.](../network-watcher/network-watcher-packet-capture-manage-portal.md) 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>SNAT-portok manuális lefoglalása az SNAT-portok virtuális gépenkénti maximalizálása érdekében
Az előre [lefoglalt portok](load-balancer-outbound-connections.md#preallocatedports)által meghatározottak szerint a terheléselosztás automatikusan lefoglalja a portokat a háttér virtuális gépek száma alapján. Alapértelmezés szerint ez a méretezhetőség biztosítása érdekében, óvatosan történik. Ha ismeri a virtuális gépek maximális számát a háttérben, manuálisan foglalhat le SNAT-portokat minden kimenő szabályban. Ha például tudja, hogy legfeljebb 10 virtuális gépe lesz, az alapértelmezett 1024 helyett virtuális gépenként 6400 SNAT-portot foglalhat le. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Az alkalmazás módosítása a kapcsolatok újbóli felhasználásához 
Az SNAT-hez használt, a kapcsolatok alkalmazáson keresztüli újrahasználása által csökkentheti az SNAT-hez használtphemerális portok iránti igényeket. A kapcsolat újrafelhasználása különösen fontos az olyan protokollok esetén, mint a HTTP/1.1, ahol a kapcsolat újrafelhasználása az alapértelmezett beállítás. Az egyéb protokollok, amelyek a HTTP-t használják átviteli módként (például REST), előnyösek. 

Az újbóli felhasználás mindig jobb, mint az egyes, atomi TCP-kapcsolatok az egyes kérések esetében. Az újbóli felhasználás nagyobb teljesítményre és rendkívül hatékony TCP-tranzakciókra vezethető vissza.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Az alkalmazás módosítása a kapcsolatkészletezés használatára
Az alkalmazásban kapcsolatkészletezési sémát is alkalmazhat, amelyben a kérések belsőleg vannak elosztva egy rögzített kapcsolatkészlet között (ahol lehetséges, minden egyes újrahasználás). Ez a séma korlátozza a használtphemerális portok számát, és kiszámíthatóbb környezetet hoz létre. Ez a séma a kérések átviteli sebességét is növelheti, ha lehetővé teszi több egyidejű művelet engedélyezését, ha egyetlen kapcsolat blokkol egy művelet válaszát.  

Előfordulhat, hogy már létezik kapcsolatkészletezés az alkalmazás fejlesztéséhez használt keretrendszerben vagy az alkalmazás konfigurációs beállításaiban. A kapcsolatkészletezést kombinálhatja a kapcsolat újbóli egyesítésével. A több kérelem ezután rögzített, kiszámítható számú portot használ fel ugyanannak a cél IP-címnek és portnak a eléréséhez. A kérések esetében szintén előnyös a TCP-tranzakciók hatékony használata, ami csökkenti a késést és az erőforrás-használatot. Az UDP-tranzakciók szintén előnyösek, mivel az UDP-folyamatok számának kezelése elkerülheti a kimerítő körülményeket, és kezelheti az SNAT-portok kihasználtságát.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára
Ha a [PAT-hez](load-balancer-outbound-connections.md#preallocatedports) használt előre [](load-balancer-outbound-connections.md) lefoglalt, elhasznált, elhasznált, illetve alkalmazáshibák lépnek fel, az agresszív vagy találgatásos újra- és visszalépési logika a kimerítést vagy a kimerülést okozhatja. Egy kevésbé agresszív újrapróbálkozási logika használatával csökkentheti a rövidebb időigényű portok iránti igényt. 

A hosszú távú portok üresjárati időkorlátja 4 perc (nem módosítható). Ha az újratörlemények túl agresszívek, a kimerítésnek nincs lehetősége saját maga is törlődni. Ezért figyelembe véve, hogy az alkalmazás milyen gyakran és milyen gyakran újraküldi a tranzakciókat, a tervezés kritikus részét képezi.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Nyilvános IP-cím hozzárendelése mindegyik virtuális géphez
Egy nyilvános IP-cím hozzárendelése nyilvános IP-címre módosítja a forgatókönyvet egy [virtuális gép nyilvános IP-címére.](load-balancer-outbound-connections.md) Az egyes virtuális gépekhez használt nyilvános IP-címek összes nyilvános portja elérhető a virtuális gép számára. (Ellentétben az olyan forgatókönyvekkel, amelyekben a nyilvános IP-címekhez tartozó nyilvános portok meg vannak osztva a megfelelő háttérkészlethez társított összes virtuális gép között.) Érdemes megfontolni néhány lehetőséget, például a nyilvános IP-címek további költségeit és a nagy számú IP-cím szűrésének lehetséges hatását.

>[!NOTE] 
>Ez a beállítás webes feldolgozói szerepkörök esetén nem érhető el.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Több előtere használata
Nyilvános hálózati standard Load Balancer több előteres [IP-címet](load-balancer-outbound-connections.md) rendel a kimenő kapcsolatokhoz, és megszorozza a rendelkezésre álló [SNAT-portok számát.](load-balancer-outbound-connections.md#preallocatedports)  Hozzon létre egy előtere IP-konfigurációt, szabályt és háttérkészletet, amely aktiválja az SNAT programozását az előtere nyilvános IP-címén.  A szabálynak nem kell működnie, és az állapot-mintavételnek nem kell sikeresnek lenni.  Ha több előtere is használható a bejövő forgalomhoz (nem csak a kimenő forgalomhoz), akkor a megbízhatóság érdekében érdemes egyéni állapot-mintavételeket használni.

>[!NOTE]
>A legtöbb esetben az SNAT-portok kimerítése a rossz kialakítás jele.  Az SNAT-portok hozzáadása előtt győződjön meg arról, hogy megértette, miért kimeríti a portokat.  Előfordulhat, hogy egy problémát maszkolt, ami később meghibásodáshoz vezethet.

## <a name="scale-out"></a><a name="scaleout"></a>Horizontális felskála
[Az előre lefoglalt](load-balancer-outbound-connections.md#preallocatedports) portok a háttérkészlet mérete alapján vannak hozzárendelve, és rétegekbe vannak csoportosítva, hogy minimalizálják a fennakadásokat, amikor egyes portokat újra kell felszabadítanunk a következő nagyobb méretű háttérkészlet számára.  Lehetősége van arra, hogy növelje egy adott előtér SNAT-portjának kihasználtságát úgy, hogy a háttérkészletet a maximális méretre skáláz egy adott szinten.  Tartsa szem előtt, hogy az alapértelmezett portfoglalásra azért van szükség, hogy az alkalmazás hatékonyan skálázható legyen az SNAT kimerülése nélkül.

Például a háttérkészlet két virtuális gépe IP-konfigurációnként 1024 SNAT-porttal rendelkezik, ami összesen 2048 SNAT-portot engedélyez a telepítéshez.  Ha az üzembe helyezést 50 virtuális gépre kellene növelni annak ellenére, hogy az előre lefoglalt portok száma virtuális gépenként állandó marad, a telepítés összesen 51 200 (50 x 1024) SNAT-portot képes használni.  Ha horizontálisan fel szeretné skálázni az üzemelő példányát, ellenőrizze az előre lefoglalt [portok](load-balancer-outbound-connections.md#preallocatedports) rétegenkénti számát, és győződjön meg arról, hogy a megfelelő szinten a maximálisra formázta a horizontális felskálát.  Ha az előző példában 50 példány helyett 51-re skálázná fel a méretet, a következő szintre lépne, és így virtuális gépenként és összesen kevesebb SNAT-portot adna meg.

Ha horizontálisan felskálol a háttérkészlet következő nagyobb méretszintjére, előfordulhat, hogy egyes kimenő kapcsolatok időkorrekulálnak, ha a lefoglalt portokat újra kell lefoglalni.  Ha csak néhány SNAT-portot használ, a következő nagyobb méretű háttérkészletre való felméretezés nem szekvenciális.  A rendszer a meglévő portok felét újra kiosztja minden alkalommal, amikor a következő háttérkészletszintre lép.  Ha ezt nem szeretné, akkor az üzemelő példányát a szint méretére kell alakítania.  Vagy győződjön meg arról, hogy az alkalmazás képes észlelni és újrapróbálkozásokat, ha szükséges.  A TCP-keepalives segít észlelni, ha az SNAT-portok már nem működnek az újra lefoglalt miatt.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>A keepalives használatával állítsa alaphelyzetbe a kimenő üresjárati időkorlátot
A kimenő kapcsolatok 4 perces üresjárati időkorláttal rendelkezik. Ez az időtúllépés a kimenő [szabályokon keresztül módosítható.](outbound-rules.md) Az átviteli folyamok (például TCP-tartások) vagy alkalmazásréteg-tartások használatával frissítheti az üresjárati folyamokat, és szükség esetén alaphelyzetbe állíthatja ezt az üresjárati időkorlátot.  

TCP keepalives használata esetén elegendő engedélyezni őket a kapcsolat egyik oldalán. Elegendő például, ha a kiszolgálóoldalon csak a folyamat tétlen időzítőit állítja alaphelyzetbe, és nem szükséges, hogy mindkét oldal kezdeményezi a TCP-megtartásokat.  Hasonló fogalmak léteznek az alkalmazásrétegre, beleértve az adatbázis ügyfél-kiszolgáló konfigurációit is.  Ellenőrizze a kiszolgálóoldalon, hogy milyen lehetőségek állnak rendelkezésre az alkalmazásspecifikus keepalive-hez.

## <a name="next-steps"></a>Következő lépések
Mindig az ügyfeleink élményét szeretnénk javítani. Ha a jelen cikk által fel nem sorolt vagy megoldott kimenő kapcsolattal kapcsolatos problémákat tapasztal, küldjön visszajelzést a GitHubon a lap alján, és a lehető leghamarabb foglalkozunk a visszajelzésével.
