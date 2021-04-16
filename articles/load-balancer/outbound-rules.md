---
title: Kimenő szabályok Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a kimenő szabályokat az internetes forgalom kimenő forgalmának szabályozása Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 339bbd7edf48737113de360812165dc8148c5b93
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375865"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Kimenő szabályok Azure Load Balancer

A kimenő szabályok lehetővé teszik az SNAT (forráshálózati címfordítás) explicit definiálását egy nyilvános standard terheléselosztáshoz. Ez a konfiguráció lehetővé teszi, hogy a terheléselosztás nyilvános IP-címeinek használatával kimenő internetkapcsolatot biztosítson a háttérpéldányok számára.

Ez a konfiguráció a következőt teszi lehetővé:

* IP-címlekérdezés
* Az engedélyezési listák egyszerűsítése.
* Csökkenti az üzembe helyezéshez szükséges nyilvános IP-erőforrások számát.

A kimenő szabályokkal teljes deklaratív vezérléssel rendelkezik a kimenő internetkapcsolat felett. A kimenő szabályok lehetővé teszik, hogy ezt a képességet az igényeinek megfelelően skálázhatja és finomhangolhatja. 

A kimenő szabályokat csak akkor követi a rendszer, ha a háttér virtuális gépnek nincs példányszintű nyilvános IP-címe (ILPIP).

![Load Balancer szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Kimenő szabályokkal explicit módon definiálhatja a kimenő **SNAT viselkedését.**

A kimenő szabályok lehetővé teszik a következő szabályozásokat:

* **Mely virtuális gépek vannak lefordítva arra, hogy mely nyilvános IP-címekre vannak lefordítva.**
     * Az 1. háttérkészlet két szabálya az 1. és a 2. kék IP-címet, a 2. háttérkészlet a sárga IP-előtagot használja.
* **A kimenő SNAT-portok lefoglalása.**
     * Ha a 2. háttérkészlet az egyetlen készlet, amely kimenő kapcsolatokat létesít, minden SNAT-portot a 2. háttérkészletnek adjon, az 1. háttérkészletnek pedig egyet se.
* **Mely protokollok biztosítják a kimenő fordítást?**
     * Ha a 2. háttérkészletnek UDP-portokra van szüksége a kimenő forgalomhoz, és az 1. háttérkészletnek TCP-re van szüksége, a TCP-portokat adja 1-nek, az UDP-portokat pedig a 2-nek.
* **A kimenő kapcsolat üresjárati időtúllépésének időtartama (4–120 perc).**
     * Ha hosszú ideig futó kapcsolatok vannak a keepalives segítségével, foglalja le a tétlen portokat a hosszú ideig futó kapcsolatokhoz akár 120 percig. Tegyük fel, hogy az elavult kapcsolatok fel vannak hagyva, és a friss kapcsolatokhoz 4 percen belül felszabadítja a portokat 
* **Azt határozza meg, hogy küldjön-e TCP-visszaállítást üresjárati időtúllépés esetén.**
     * Amikor időzítjük a tétlen kapcsolatokat, küldünk egy TCP RST-t az ügyfélnek és a kiszolgálónak, hogy tudják, hogy a folyamat fel van hagyva?

## <a name="outbound-rule-definition"></a>Kimenő szabály definíciója

A kimenő szabályok ugyanazt az ismerős szintaxist követik, mint a terheléselosztás és a bejövő NAT-szabályok: előtereparaméterek  +    +  **háttérkészlete.** 

A kimenő szabály a háttérkészlet  által azonosított összes virtuális gép kimenő NAT-ját konfigurálja az _előtere fordítására._  

A _paraméterek_ további részletes vezérlést biztosítanak a kimenő NAT-algoritmus felett.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Kimenő NAT horizontális felskálálása több IP-címmel

Az előtere által biztosított további IP-címek további 64 000,000, SNAT-portként használható, a terheléselosztáshoz használt portot biztosítanak. 

Használjon több IP-címet a nagy léptékű forgatókönyvek tervezéséhez. Kimenő szabályokkal mérsékelheti az [SNAT-kimerítést.](troubleshoot-outbound-connection.md#snatexhaust) 

Nyilvános [IP-előtagot](./load-balancer-outbound-connections.md#outboundrules) közvetlenül is használhat kimenő s szabályokkal. 

A nyilvános IP-előtag növeli az üzemelő példány skálázását. Az előtag hozzáadható az Azure-erőforrásokból származó folyamatok engedélyező listájához. A terheléselosztáson belül konfigurálhat egy előtere IP-konfigurációt, hogy egy nyilvános IP-címelőtagra hivatkozzon.  

A terheléselosztás a nyilvános IP-előtag felett rendelkezik vezérléssel. A kimenő szabály automatikusan a nyilvános IP-előtagban található összes nyilvános IP-címet használja a kimenő kapcsolatokhoz. 

A nyilvános IP-előtagon belüli IP-címek ip-címenként további 64 000 rövidítóportot kínálnak, hogy a terheléselosztás SNAT-portként használható.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Kimenő forgalom üresjárati időkorlátja és a TCP alaphelyzetbe állítása

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő forgalom üresjárati időkorlátjának szabályozásához és az alkalmazás igényeihez való egyeztetéshez. A kimenő üresjárati időkorlátok alapértelmezés szerint 4 perc. További információ: [Üresjárati időtúllépések konfigurálása.](load-balancer-tcp-idle-timeout.md) 

A terheléselosztás alapértelmezett viselkedése az, hogy a kimenő üresjárati időkorlát elérésekor a folyamat csendesen el lesz dobva. A `enableTCPReset` paraméter kiszámítható alkalmazásviselkedést és -vezérlést tesz lehetővé. A paraméter határozza meg, hogy a rendszer kétirányú TCP-visszaállítást (TCP RST) küldjön-e a kimenő üresjárati időtúllépés időtúllépésekor. 

A régió rendelkezésre állását is beleértve tekintse át a [TCP-visszaállítás üresjárati](./load-balancer-tcp-reset.md) időtúllépés esetén való áttekintését.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>A kimenő kapcsolatok explicit biztonságossá tétele és vezérlése

A terheléselosztási szabályok a kimenő NAT automatikus programozását biztosítják. Egyes forgatókönyvek esetében előnyös vagy kötelező letiltani a kimenő NAT automatikus programozását a terheléselosztási szabály alapján. A szabályon keresztüli letiltás lehetővé teszi a viselkedés szabályozását vagy finomítását.  

Ezt a paramétert kétféleképpen használhatja:

1. A kimenő SNAT bejövő IP-címének megelőzése. Tiltsa le a kimenő SNAT-t a terheléselosztási szabályban.
  
2. Hangolja egy egyidejűleg bejövő és kimenő IP-cím kimenő **SNAT-paramétereit.** Az automatikus kimenő NAT-t le kell tiltani, hogy egy kimenő szabály átveszi az irányítást. Egy bejövő cím SNAT-portfoglalásának módosítása érdekében a paramétert true (igaz) `disableOutboundSnat` értékre kell állítani. 

A kimenő szabály konfiguráló művelete sikertelen lesz, ha megpróbálja újradefiniálni a bejövő forgalomhoz használt IP-címet.  Először tiltsa le a terheléselosztási szabály kimenő NAT-ját.

>[!IMPORTANT]
> A virtuális gép nem fog kimenő kapcsolattal rendelkezik, ha ezt a paramétert true (igaz) értékre adja meg, és nincs kimenő szabálya a kimenő kapcsolat meghatározásához.  A virtuális gép vagy az alkalmazás egyes műveletei attól függhetnek, hogy elérhetők-e kimenő kapcsolatok. Győződjön meg arról, hogy megértette a forgatókönyv függőségeit, és mérlegeli ennek a módosításnak a hatását.

Néha nem kívánatos, hogy egy virtuális gép kimenő folyamatot hozzon létre. Követelmény lehet annak kezelése, hogy mely célhely fogadja a kimenő folyamatokat, vagy mely célhely kezdődik a bejövő forgalomból. Hálózati [biztonsági csoportokkal kezelheti](../virtual-network/network-security-groups-overview.md) a célhelyeket, amelyekhez a virtuális gép csatlakozik. Az NSG-k segítségével kezelheti, hogy mely nyilvános célhely indítsa el a bejövő folyamatokat.

Amikor NSG-t alkalmaz egy elosztott terhelésű virtuális [](../virtual-network/network-security-groups-overview.md#service-tags) gépre, ügyeljen a szolgáltatáscímkékre és az alapértelmezett [biztonsági szabályokra.](../virtual-network/network-security-groups-overview.md#default-security-rules) 

Győződjön meg arról, hogy a virtuális gép képes állapot-mintavételi kéréseket fogadni a Azure Load Balancer.

Ha egy NSG blokkolja az állapot-mintavételi kéréseket AZURE_LOADBALANCER alapértelmezett címkéről, a virtuális gép állapotmintája sikertelen lesz, és a virtuális gép elérhetetlenként lesz megjelölve. A terheléselosztás leállítja az új folyamatok küldését a virtuális gépre.

## <a name="scenarios-with-outbound-rules"></a>Kimenő szabályokkal való forgatókönyvek
        

### <a name="outbound-rules-scenarios"></a>Kimenő szabályok forgatókönyvei


* Konfigurálja a kimenő kapcsolatokat egy adott nyilvános IP-hez vagy előtaghoz.
* Módosítsa [az SNAT-portfoglalást.](load-balancer-outbound-connections.md)
* Csak kimenő forgalom engedélyezése.
* Csak virtuális gépek kimenő NAT-ja (nincs bejövő).
* Kimenő NAT a belső standard terheléselosztáshoz.
* Engedélyezze mindkét TCP& UDP-protokollt a kimenő NAT-hez egy nyilvános standard terheléselosztással.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>1. forgatókönyv: Nyilvános IP-k vagy előtagok adott készletéhez irányuló kimenő kapcsolatok konfigurálása


#### <a name="details"></a>Részletek


Ezzel a forgatókönyvvel úgy szabja testre a kimenő kapcsolatokat, hogy nyilvános IP-címek egy készletéből származnak. Nyilvános IP-k vagy előtagok hozzáadása egy engedélyező vagy megtagadási listához az eredet alapján.


Ez a nyilvános IP-cím vagy előtag megegyezik egy terheléselosztási szabály által használt előtaggal. 


Ha a terheléselosztási szabály által használttól eltérő nyilvános IP-címet vagy előtagot használ: 


1. Hozzon létre nyilvános IP-előtagot vagy nyilvános IP-címet.
2. Nyilvános standard terheléselosztás létrehozása 
3. Hozzon létre egy előtagot, amely a használni kívánt nyilvános IP-előtagra vagy nyilvános IP-címre hivatkozik. 
4. Használja újra a háttérkészletet, vagy hozzon létre egy háttérkészletet, és helyezze a virtuális gépeket a nyilvános terheléselosztási készlet háttérkészletében
5. Konfigurálnia kell egy kimenő szabályt a nyilvános terheléselosztáson a virtuális gépek kimenő NAT-ának engedélyezéséhez az előtere használatával. Kimenő forgalomhoz nem ajánlott terheléselosztási szabályt használni, tiltsa le a kimenő SNAT-t a terheléselosztási szabályon.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>2. forgatókönyv: [SNAT-portfoglalás](load-balancer-outbound-connections.md) módosítása


#### <a name="details"></a>Részletek


Kimenő szabályokkal finomhangolhatja az [automatikus SNAT-portfoglalást a háttérkészlet mérete alapján.](load-balancer-outbound-connections.md#preallocatedports) 


Ha SNAT-kimerítést tapasztal, növelje a [megadott SNAT-portok](load-balancer-outbound-connections.md) számát az alapértelmezett 1024-es értékről. 


Minden nyilvános IP-cím legfeljebb 64 000 elameredő porthoz járul hozzá. A háttérkészletben található virtuális gépek száma határozza meg az egyes virtuális gépekre elosztott portok számát. A háttérkészlet egyik virtuális gépe legfeljebb 64 000 porthoz fér hozzá. Két virtuális gép esetén legfeljebb 32 000 [SNAT-port](load-balancer-outbound-connections.md) adható meg kimenő s szabálysal (2x 32 000 = 64 000). 


Kimenő szabályok használatával hangolhatja az alapértelmezés szerint megadott SNAT-portokat. Az alapértelmezett [SNAT-portfoglalásnál](load-balancer-outbound-connections.md) többet vagy kevesebbet ad meg. Egy kimenő szabály előtere minden nyilvános IP-címe legfeljebb 64 000, [SNAT-portként](load-balancer-outbound-connections.md) használható, nem nyilvános portot használ. 


A Load Balancer [a 8](load-balancer-outbound-connections.md) többszörösében biztosít SNAT-portokat. Ha olyan értéket ad meg, amely nem lesz 8-ban kivetve, a rendszer elutasítja a konfigurációs műveletet. Minden terheléselosztási szabály és bejövő NAT-szabály 8 portból áll. Ha egy terheléselosztási vagy bejövő NAT-szabály a 8-as tartományon osztozik, mint a másik, a rendszer nem használ fel további portokat.


Ha a nyilvános IP-címek száma alapján több [SNAT-portot](load-balancer-outbound-connections.md) próbál meg adni, mint amennyit elérhetők, a rendszer elutasítja a konfigurációs műveletet. Ha például virtuális gépenként 10 000 portot ad meg, és a háttérkészlet hét virtuális gépe egyetlen nyilvános IP-címmel rendelkezik, a rendszer elutasítja a konfigurációt. Hét szorozva 10 000-ekkel meghaladja a 64 000 portkorlátot. A forgatókönyv engedélyezéséhez adjon hozzá további nyilvános IP-címeket a kimenő szabály előtere számára. 


A portok számának [0 megadásával](load-balancer-outbound-connections.md#preallocatedports) áll vissza az alapértelmezett portfoglaláshoz. Az első 50 virtuálisgép-példány 1024 portot kap, 51–100 virtuálisgép-példány pedig legfeljebb 512 példányt kap. További információ az alapértelmezett SNAT-portfoglalásról: [SNAT portfoglalási táblázat.](./load-balancer-outbound-connections.md#preallocatedports)


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>3. forgatókönyv: Csak kimenő forgalom engedélyezése


#### <a name="details"></a>Részletek


Nyilvános standard terheléselosztás használatával biztosítson kimenő NAT-t a virtuális gépek egy csoportjának. Ebben a forgatókönyvben önmagában, további szabályok konfigurálása nélkül használjon kimenő szabályt.


> [!NOTE]
> **Azure Virtual Network NAT** a virtuális gépek kimenő kapcsolatát anélkül, hogy terheléselosztásra lenne szükség. További [információ: Mi Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>4. forgatókönyv: Csak virtuális gépek kimenő NAT-ja (nincs bejövő)


> [!NOTE]
> **Azure Virtual Network NAT** a virtuális gépek kimenő kapcsolatát anélkül, hogy terheléselosztásra lenne szükség. További [információ: Mi Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Részletek


Ebben a forgatókönyvben: Azure Load Balancer kimenő szabályok és Virtual Network NAT virtuális hálózatból kimenő forgalomra vonatkozó lehetőségek érhetők el.


1. Hozzon létre egy nyilvános IP-címet vagy előtagot.
2. Hozzon létre egy nyilvános standard terheléselosztást. 
3. Hozzon létre egy, a kimenő forgalom számára kijelölt nyilvános IP-címhez vagy előtaghoz társított előtagot.
4. Hozzon létre egy háttérkészletet a virtuális gépekhez.
5. Helyezze a virtuális gépeket a háttérkészletbe.
6. Konfigurálnia kell egy kimenő szabályt a kimenő NAT engedélyezéséhez.



SNAT-portok méretezéséhez használjon előtagot vagy nyilvános [IP-címet.](load-balancer-outbound-connections.md) Adja hozzá a kimenő kapcsolatok forrását egy engedélyező vagy megtagadási listához.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>5. forgatókönyv: Kimenő NAT belső standard terheléselosztáshoz


> [!NOTE]
> **Azure Virtual Network NAT** belső standard terheléselosztást használó virtuális gépek kimenő kapcsolatát is biztosíthatja. További [információ: Mi Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Részletek


A kimenő kapcsolat csak akkor érhető el a belső standard terheléselosztáshoz, ha példányszintű nyilvános IP-ekkel vagy Virtual Network NAT-vel kifejezetten deklarálták, vagy ha a háttérkészlet tagjait egy csak kimenő terheléselosztási konfigurációhoz társítja. 


További információ: Csak kimenő terheléselosztás [konfigurálása.](./egress-only.md)




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>6. forgatókönyv: Mindkét TCP& UDP-protokoll engedélyezése nyilvános standard terheléselosztással használt kimenő NAT-hez


#### <a name="details"></a>Részletek


Nyilvános standard terheléselosztás használata esetén a megadott automatikus kimenő NAT megegyezik a terheléselosztási szabály átviteli protokollal. 


1. Tiltsa le a [kimenő SNAT-t](load-balancer-outbound-connections.md) a terheléselosztási szabályon. 
2. Konfigurálnia kell egy kimenő szabályt ugyanazon a terheléselosztáson.
3. Használja újra a virtuális gépek által már használt háttérkészletet. 
4. Adja meg a "protocol" értéket: "Mind" a kimenő szabály részeként. 


Ha csak bejövő NAT-szabályokat használ, nem biztosít kimenő NAT-t. 


1. Helyezze a virtuális gépeket egy háttérkészletbe.
2. Egy vagy több előtere IP-konfiguráció megadása nyilvános IP-címmel vagy nyilvános IP-előtaggal 
3. Konfigurálnia kell egy kimenő szabályt ugyanazon a terheléselosztáson. 
4. Adja meg a "protocol" (protokoll) értéket: "All" (Mind) a kimenő szabály részeként


## <a name="limitations"></a>Korlátozások

- Az előtere IP-címenként legfeljebb 64 000 használható időzítő port lehet.
- A konfigurálható kimenő üresjárati időkorlát tartománya 4–120 perc (240–7200 másodperc).
- A load balancer nem támogatja az ICMP-t a kimenő NAT-hez.
- Kimenő szabályok csak a hálózati adapterek elsődleges IP-konfigurációira alkalmazhatók.  Virtuális gép vagy NVA másodlagos IP-címének kimenő szabálya nem hozható létre. Több NIC is támogatott.
- A rendelkezésre állási készleten belüli összes virtuális gépet hozzá kell adni a háttérkészlethez a kimenő kapcsolatokhoz.  
- A virtuálisgép-méretezési készletben lévő összes virtuális gépet hozzá kell adni a háttérkészlethez a kimenő kapcsolatokhoz. 

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure standard Load Balancer](load-balancer-overview.md)
- Tekintse meg [a gyakori kérdéseket a Azure Load Balancer](load-balancer-faqs.md)
