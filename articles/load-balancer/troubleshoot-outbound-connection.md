---
title: A Azure Load Balancer kimenő kapcsolatainak hibáinak megoldása
description: A Azure Load Balanceron keresztüli kimenő kapcsolattal kapcsolatos gyakori problémák megoldása.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696353"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> A kimenő kapcsolatok hibáinak elhárítása

Ez a cikk olyan gyakori problémák megoldását mutatja be, amelyekkel a Azure Load Balancer kimenő kapcsolatai is előfordulhatnak. Az ügyfelek által tapasztalt kimenő kapcsolattal kapcsolatos legtöbb probléma a SNAT-portok kimerülése és a megszakadt csomagok miatti kapcsolati időtúllépések miatt fordul elő. Ez a cikk az egyes problémák enyhítésének lépéseit ismerteti.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> A SNAT (PAT) portjának kimerülésének kezelése
A [pathoz](load-balancer-outbound-connections.md) használt [ideiglenes portok](load-balancer-outbound-connections.md) kimeríthető erőforrások, amelyeket az [önálló virtuális gép nyilvános IP-cím nélküli](load-balancer-outbound-connections.md) és [elosztott terhelésű virtuális gép nyilvános IP-cím](load-balancer-outbound-connections.md)nélkül című része ismertet. Az időszakos portok használatát figyelemmel kísérheti, és összehasonlíthatja az aktuális kiosztásával, hogy meghatározza a kockázatát vagy a SNAT kimerülésének megerősítését [az útmutató segítségével](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) .

Ha tudja, hogy több kimenő TCP-vagy UDP-kapcsolatra van szüksége ugyanahhoz a cél IP-címhez és porthoz, és megfigyelheti a sikertelen kimenő kapcsolatokat, vagy ha támogatja a SNAT-portok kimerítését (a [Pat](load-balancer-outbound-connections.md)által használt időszakosan lefoglalt időszakos [portok](load-balancer-outbound-connections.md#preallocatedports) ), számos általános kockázatcsökkentő lehetőség közül választhat. Tekintse át ezeket a beállításokat, és döntse el, hogy mi az elérhető és melyik a legmegfelelőbb a forgatókönyvhöz. Lehetséges, hogy egy vagy több segíthet a forgatókönyv kezelésében.

Ha nem sikerül megismerni a kimenő kapcsolatok viselkedését, használhatja az IP-verem statisztikáit (netstat). Vagy hasznos lehet a kapcsolatok viselkedésének figyelésére a csomagok rögzítései használatával. Ezeket a csomagokat a példány vendég operációs rendszerében hajthatja végre, vagy Network Watcher is használhatja a [csomagok rögzítéséhez](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>SNAT-portok manuális lefoglalása a SNAT-portok maximalizálása érdekében virtuális gépenként
Az előre [lefoglalt portokban](load-balancer-outbound-connections.md#preallocatedports)meghatározottak szerint a terheléselosztó automatikusan kiosztja a portokat a háttérbeli virtuális gépek száma alapján. Alapértelmezés szerint ez konzervatívan történik a méretezhetőség biztosításához. Ha ismeri a háttérbeli virtuális gépek maximális számát, akkor az egyes kimenő szabályokban manuálisan is lefoglalhatja a SNAT-portokat. Ha például tudja, hogy legfeljebb 10 virtuális gép van, akkor az alapértelmezett 1 024 helyett virtuális gépenként 6 400 SNAT-portot foglalhat le. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Az alkalmazás módosítása a kapcsolatok újrafelhasználásához 
A SNAT használt ideiglenes portok igényét csökkentheti az alkalmazásban található kapcsolatok újrafelhasználásával. A kapcsolatok újrafelhasználása különösen a HTTP/1.1-hez hasonló protokollok esetén érvényes, ahol a kapcsolatok újrafelhasználása az alapértelmezett. Továbbá a HTTP protokollt használó más protokollok (például a REST) is hasznosak lehetnek. 

Az újrafelhasználás mindig jobb, mint az egyes kérések egyéni, atomi TCP-kapcsolatai. A több teljesítményű, nagyon hatékony TCP-tranzakció eredményeit is újra felhasználhatja.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Az alkalmazás módosítása a kapcsolatok készletezésének használatára
Az alkalmazásban létrehozhat egy kapcsolati készletezési sémát, ahol a kérések belsőleg oszlanak meg egy rögzített kapcsolaton belül (az összes újrafelhasználás, ahol lehetséges). Ez a séma korlátozza a használatban lévő ideiglenes portok számát, és egy előre jelezhető környezetet hoz létre. Ez a séma növelheti a kérések átviteli sebességét azáltal, hogy lehetővé teszi több egyidejű művelet engedélyezését, ha egyetlen kapcsolódás blokkolja a művelet válaszát.  

Előfordulhat, hogy a kapcsolatok készletezése már létezik azon a keretrendszeren belül, amelyet az alkalmazás fejlesztéséhez vagy az alkalmazás konfigurációs beállításaihoz használ. Összekapcsolhatja a kapcsolatok készletezését a kapcsolatok újbóli használatával. A többszörös kérelmek ezután egy rögzített, kiszámítható számú portot használnak ugyanahhoz a cél IP-címhez és porthoz. A kérések kihasználják a TCP-tranzakciók hatékony használatát a késés és az erőforrások kihasználtságának csökkentése érdekében. Az UDP-tranzakciók is hasznosak lehetnek, mivel az UDP-folyamatok számának kezelése a kimerülési feltételek elkerülésével és a SNAT-portok kihasználtságának kezelésével is jár.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára
Ha a [pathoz](load-balancer-outbound-connections.md) használt, [előlefoglalt](load-balancer-outbound-connections.md#preallocatedports) időszakos portok kimerülése vagy az alkalmazások meghibásodása esetén az agresszív vagy találgatásos kényszerített újrapróbálkozások a romlás és a leállítási logikája miatt kimerítik vagy megtartják a fáradtságot. Az ideiglenes portok igényét kevésbé agresszív újrapróbálkozási logika használatával csökkentheti. 

Az ideiglenes portok 4 perces üresjárati időkorláttal rendelkeznek (nem állítható be). Ha az újrapróbálkozások túl agresszívek, a kimerültségnek nincs lehetősége a saját törlésére. Ezért figyelembe véve, hogy a--és milyen gyakran – az alkalmazás újrapróbálkozási tranzakciói a terv kritikus részét képezik.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Nyilvános IP-cím kiosztása minden virtuális géphez
Egy nyilvános IP-cím hozzárendelésével a forgatókönyv a [nyilvános IP-címekre változik egy virtuális gépen](load-balancer-outbound-connections.md). Az egyes virtuális gépekhez használt nyilvános IP-címek minden ideiglenes portja elérhető a virtuális gép számára. (Azon forgatókönyvek esetében, amelyekben a nyilvános IP-címek ideiglenes portjai meg vannak osztva a megfelelő háttér-készlethez társított összes virtuális géppel.) Kompromisszumok merülnek fel, mint például a nyilvános IP-címek további díja, valamint a nagy számú egyedi IP-cím szűrésének lehetséges következményei.

>[!NOTE] 
>Ez a beállítás webes feldolgozói szerepkörök esetén nem érhető el.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Több előtér-felület használata
Nyilvános standard Load Balancer használata esetén [több előtér-IP-címet kell hozzárendelni a kimenő kapcsolatokhoz](load-balancer-outbound-connections.md) , és meg kell [szorozni a rendelkezésre álló SNAT-portok számát](load-balancer-outbound-connections.md#preallocatedports).  Hozzon létre egy előtérbeli IP-konfigurációt, szabályt és háttér-készletet a SNAT programozásának elindításához a frontend nyilvános IP-címére.  A szabálynak nem kell működnie, és az állapot-mintavételnek nem kell sikeresnek lennie.  Ha több előtérbeli felületet is használ a bejövő (nem csak a kimenő) előtérben, akkor a megbízhatóság biztosításához használja az egyéni állapotú mintavételt is.

>[!NOTE]
>A legtöbb esetben a SNAT-portok kimerítése hibás kialakítás jele.  Győződjön meg arról, hogy a portok kimerítésének okát érdemes megismerni, mielőtt SNAT-portok hozzáadására több felületet használ.  Előfordulhat, hogy olyan problémát takar, amely később hibát okozhat.

## <a name="scale-out"></a><a name="scaleout"></a>Vertikális felskálázás
Az [előlefoglalt portok](load-balancer-outbound-connections.md#preallocatedports) a háttérrendszer-készlet mérete alapján vannak hozzárendelve, és rétegekbe vannak csoportosítva, hogy kis mennyiségű portot lehessen megszakítani, ha a portok némelyikét újra kell osztani a háttérrendszer következő méretének növeléséhez.  Előfordulhat, hogy egy adott előtérben a SNAT-portok kihasználtságának növelésére van lehetősége, ha a háttér-készletet egy adott réteg maximális méretére szeretné méretezni.  Vegye figyelembe, hogy az alkalmazás számára az alapértelmezett portok kiosztása szükséges ahhoz, hogy a kockázat SNAT kimerültség nélkül hatékonyan felskálázásra kerüljön.

A háttér-készletben lévő két virtuális gép esetében például IP-konfiguráció esetén 1024 SNAT-port érhető el, ami lehetővé teszi, hogy összesen 2048 SNAT portot engedélyezzen a telepítéshez.  Ha az üzembe helyezést 50 virtuális gépre szeretné növelni, bár az előre lefoglalt portok száma virtuális gépenként állandó marad, akkor az üzemelő példány összesen 51 200 (50 x 1024) SNAT-portot használhat.  Ha ki szeretné bővíteni az üzemelő példányt, ellenőrizze, hogy az előre [lefoglalt portok](load-balancer-outbound-connections.md#preallocatedports) száma szintenként van-e kibővítve, hogy a maximális méretet a megfelelő rétegre alakítsa ki.  Ha az előző példában úgy döntött, hogy 50-példány helyett 51-ra szeretné felskálázást végezni, akkor a következő szintet kell végrehajtania, és a végén a virtuális gép kevesebb SNAT-portja, valamint összesen szerepel.

Ha felskálázást végez a következő nagyobb háttérrendszer-készlet méretével, előfordulhat, hogy a kimenő kapcsolatok némelyike időtúllépést okoz, ha a lefoglalt portok újra le lesznek foglalva.  Ha csak néhány SNAT-portot használ, a következő nagyobb méretű háttér-készlet méretének horizontális felskálázása lényegtelen.  A meglévő portok felét a rendszer minden alkalommal újra lefoglalja, amikor a következő háttérbeli készletre lép.  Ha nem szeretné, hogy ez megtörténjen, az üzembe helyezést a rétegek méretére kell alakítania.  Vagy ellenőrizze, hogy az alkalmazás képes-e az észlelésre, és szükség esetén próbálkozzon újra.  A TCP-Keepalives segítséget nyújthat az észlelésben, ha a SNAT-portok már nem működnek az újrafoglalás miatt.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>A kimenő Üresjárati időkorlát alaphelyzetbe állítása a Keepalives használatával
A kimenő kapcsolatok 4 perces üresjárati időkorláttal rendelkeznek. Ez az időkorlát a [kimenő szabályokon](outbound-rules.md)keresztül állítható be. A Transport (például TCP Keepalives) vagy az alkalmazás-réteg Keepalives használatával is frissítheti az üresjárati folyamatokat, és szükség esetén visszaállíthatja ezt az üresjárati időkorlátot.  

A TCP-Keepalives használata esetén elegendő a csatlakozás egyik oldalán való engedélyezése. Például elegendő, ha csak a kiszolgáló oldalon engedélyezi őket, hogy alaphelyzetbe állítsa a folyamat üresjárati időzítőjét, és nem szükséges mindkét fél számára a TCP-Keepalives kezdeményezni.  Hasonló fogalmak léteznek az alkalmazási réteghez, beleértve az adatbázis-ügyfél-kiszolgáló konfigurációkat is.  Tekintse meg a kiszolgáló oldalát, hogy milyen lehetőségek léteznek az alkalmazásspecifikus Keepalives.

## <a name="next-steps"></a>Következő lépések
Folyamatosan fejlesztjük ügyfeleink tapasztalatait. Ha a jelen cikkben nem felsorolt vagy feloldható kimenő kapcsolattal kapcsolatos problémákat tapasztal, küldje el visszajelzését a GitHubon a lap alján keresztül, és a lehető leghamarabb válaszolunk a visszajelzésre.