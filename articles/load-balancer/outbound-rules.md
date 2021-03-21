---
title: Kimenő szabályok Azure Load Balancer
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a kimenő szabályokat az internetes forgalom Azure Load Balancer-vel való megadásának szabályozására.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 2fc703e0532c86bfc0874c8dccbb17c6142aeed0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590211"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Kimenő szabályok Azure Load Balancer

A kimenő szabályok lehetővé teszik, hogy explicit módon meghatározza a nyilvános standard Load Balancer SNAT (forrásoldali hálózati címfordítás). Ez a konfiguráció lehetővé teszi, hogy a terheléselosztó nyilvános IP-címe (i) használatával kimenő internetkapcsolatot biztosítson a háttérbeli példányok számára.

Ez a konfiguráció a következőket teszi lehetővé:

* IP-cím maszkolása
* Egyszerűsítse az engedélyezési listát.
* Csökkenti a nyilvános IP-erőforrások számát az üzembe helyezéshez.

A kimenő szabályok teljes mértékben deklaratív vezérléssel rendelkeznek a kimenő internetkapcsolattal szemben. A kimenő szabályok lehetővé teszik az adott igényeknek megfelelő méretezést és finomhangolást. 

A kimenő szabályok csak akkor lesznek követve, ha a háttérbeli virtuális gép nem rendelkezik példány szintű nyilvános IP-címmel (ILPIP).

![Load Balancer kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok használatával explicit módon definiálhatja a kimenő **SNAT** viselkedését.

A kimenő szabályok segítségével a következőket vezérelheti:

* **Mely virtuális gépek fordíthatók le a nyilvános IP-címekre.**
     * Két szabály volt a háttér-készlet 1. a kék IP-címet 1. és 2. a háttér-készlet 2 a sárga IP-előtagot használja.
* **A kimenő SNAT portok kiosztása.**
     * Ha a 2. háttér-készlet az egyetlen, kimenő kapcsolatokat eredményező készlet, az összes SNAT-port a 2. háttér-készletbe, a háttér-készletbe pedig az 1.
* **A kimenő fordítását biztosító protokollok.**
     * Ha a 2. háttérrendszer-készletnek UDP-portokra van szüksége a kimenő és a háttér-készlet 1 számára, a TCP-portoknak 1 és UDP portoknak kell lenniük 2-ra.
* **A kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.**
     * Ha hosszú ideig futó kapcsolatok vannak a Keepalives-mel, a hosszú ideig futó kapcsolatok esetében foglaljon le üresjárati portokat akár 120 percre. Az elavult kapcsolatok elhagyása és a portok 4 percen belüli felszabadítása friss kapcsolatok esetén 
* **Azt jelzi, hogy a TCP alaphelyzetbe állítása üresjárati időkorláton történjen.**
     * Az üresjárati kapcsolatok időtúllépése esetén a rendszer az ügyfél és a kiszolgáló számára is elküld egy TCP-t, hogy tudja, hogy a folyamat el lett hagyva?

## <a name="outbound-rule-definition"></a>Kimenő szabály definíciója

A kimenő szabályok ugyanazt a megszokott szintaxist követik, mint a terheléselosztás és a bejövő NAT- **szabályok: előtér**-  +  **Paraméterek**  +  **háttér-készlete**. 

Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  

A _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Kimenő NAT méretezése több IP-címmel

A frontend által biztosított további IP-címek további 64 000 ideiglenes portokat biztosítanak a terheléselosztó számára SNAT-portokként való használathoz. 

Több IP-cím használata a nagy léptékű forgatókönyvek megtervezéséhez. A kimenő szabályok használatával csökkentheti a [SNAT-kimerültséget](troubleshoot-outbound-connection.md#snatexhaust). 

A [nyilvános IP-előtagot](./load-balancer-outbound-connections.md#outboundrules) közvetlenül is használhatja egy kimenő szabállyal. 

A nyilvános IP-előtag növeli az üzemelő példány skálázását. Az előtag hozzáadható az Azure-erőforrásokból származó folyamatok engedélyezési listájához. A Load balancerben konfigurálhatja a előtérbeli IP-konfigurációt a nyilvános IP-cím előtagjaként való hivatkozáshoz.  

A terheléselosztó felügyeli a nyilvános IP-előtagot. A kimenő kapcsolatok esetében a kimeneti szabály automatikusan a nyilvános IP-előtagban található összes nyilvános IP-címet fogja használni. 

A nyilvános IP-előtagon belüli összes IP-cím egy további 64 000 ideiglenes portot biztosít IP-címként a terheléselosztó számára SNAT-portokként való használathoz.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Kimenő forgalom üresjárati időkorlátja és a TCP-visszaállítás

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időkorlátjának szabályozására, és az alkalmazás igényeinek megfelelően illeszkednek. A kimenő üresjárati időtúllépések alapértelmezett értéke 4 perc. További információ: az [üresjárati időtúllépések konfigurálása](load-balancer-tcp-idle-timeout.md). 

A terheléselosztó alapértelmezett viselkedése a folyamat csendes eldobása, ha elérte a kimenő üresjárati időkorlátot. A `enableTCPReset` paraméter lehetővé teszi a kiszámítható alkalmazások viselkedését és vezérlését. A paraméter azt határozza meg, hogy a kimenő Üresjárati időkorlát időtúllépése esetén a kétirányú TCP alaphelyzetbe állítás (TCP első) legyen-e. 

Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](./load-balancer-tcp-reset.md) , beleértve a régió rendelkezésre állását.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>A kimenő kapcsolatok biztonságossá tétele és szabályozása explicit módon

Terheléselosztási szabályok biztosítják a kimenő NAT automatikus programozását. Bizonyos forgatókönyvek esetében a kimenő NAT automatikus programozásának letiltására van szükség a terheléselosztási szabály alapján. A szabály használatával történő letiltás lehetővé teszi a viselkedés szabályozását vagy pontosítását.  

Ezt a paramétert kétféleképpen is használhatja:

1. A kimenő SNAT bejövő IP-címének megakadályozása. Tiltsa le a kimenő SNAT a terheléselosztási szabályban.
  
2. A bejövő és kimenő IP-címek kimenő **SNAT** paramétereinek hangolása egyidejűleg. Az automatikus kimenő NAT-t le kell tiltani ahhoz, hogy a kimenő szabályok szabályozni tudják a vezérlést. Ha módosítani szeretné egy SNAT-port lefoglalását is, akkor a `disableOutboundSnat` paramétert igaz értékre kell állítani. 

A kimenő szabályok konfigurálásának művelete sikertelen lesz, ha megpróbál újradefiniálni egy bejövő IP-címet.  Először tiltsa le a terheléselosztási szabály kimenő NAT-át.

>[!IMPORTANT]
> Ha a paraméter értéke TRUE (igaz), a virtuális gép nem rendelkezik kimenő kapcsolattal, és nem rendelkezik kimenő kapcsolattal.  A virtuális gép vagy az alkalmazás néhány művelete attól függ, hogy a kimenő kapcsolat elérhető-e. Győződjön meg arról, hogy tisztában van a forgatókönyv függőségeivel, és befolyásolta ennek a változásnak a hatását.

Esetenként nem kívánatos, hogy egy virtuális gép kimenő folyamatot hozzon létre. Előfordulhat, hogy egy olyan követelményt kell kezelnie, hogy mely célhelyek kapják meg a kimenő folyamatokat, vagy hogy mely célhelyek kezdenek bejövő forgalmat. [Hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával kezelheti a virtuális gép által elnyúló célhelyeket. A NSG használatával felügyelheti, hogy mely nyilvános célhelyek indítják el a bejövő folyamatokat.

Ha NSG alkalmaz egy elosztott terhelésű virtuális gépre, ügyeljen a [szolgáltatás-címkékre](../virtual-network/network-security-groups-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra](../virtual-network/network-security-groups-overview.md#default-security-rules). 

Győződjön meg arról, hogy a virtuális gép Azure Load Balancertól származó állapot-mintavételi kérelmeket tud fogadni.

Ha egy NSG letiltja az állapot-mintavételi kérelmeket a AZURE_LOADBALANCER alapértelmezett címkétől, a virtuális gép állapotának mintavétele meghiúsul, és a virtuális gép nem érhető el. A terheléselosztó leállítja az új folyamatok küldését az adott virtuális gépre.

## <a name="scenarios-with-outbound-rules"></a>Forgatókönyvek kimenő szabályokkal
        

### <a name="outbound-rules-scenarios"></a>Kimenő szabályok forgatókönyvei


* Konfigurálja a kimenő kapcsolatokat egy adott nyilvános IP-cím vagy előtag-készletbe.
* Módosítsa a [SNAT](load-balancer-outbound-connections.md) -portok foglalását.
* Csak a kimenő engedélyezése.
* Kimenő NAT csak virtuális gépekhez (nincs bejövő).
* Kimenő NAT a belső standard Load Balancerhez.
* Engedélyezze mindkét TCP-& UDP protokollt a kimenő NAT-hoz egy nyilvános standard Load balancerrel.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>1. forgatókönyv: kimenő kapcsolatok konfigurálása egy adott nyilvános IP-cím vagy előtag-készlethez


#### <a name="details"></a>Részletek


Ezzel a módszerrel a kimenő kapcsolatok a nyilvános IP-címek készletéről származnak. Nyilvános IP-címeket vagy előtagokat adhat hozzá egy engedélyezési vagy megtagadási listához a létrehozásuk alapján.


Ez a nyilvános IP-cím vagy előtag ugyanaz lehet, mint a terheléselosztási szabályok. 


Eltérő nyilvános IP-cím vagy előtag használata egy terheléselosztási szabály által használtnál: 


1. Hozzon létre nyilvános IP-előtagot vagy nyilvános IP-címet.
2. Nyilvános standard Load Balancer létrehozása 
3. Hozzon létre egy, a használni kívánt nyilvános IP-előtagra vagy nyilvános IP-címére hivatkozó előtérbeli felületet. 
4. Háttérbeli készlet újrafelhasználása vagy háttérbeli készlet létrehozása és a virtuális gépek elhelyezése a nyilvános Load Balancer háttér-készletében
5. Konfiguráljon egy kimenő szabályt a nyilvános terheléselosztó számára, hogy engedélyezze a kimenő NAT-t a virtuális gépek számára a frontend használatával. Nem ajánlott terheléselosztási szabályt használni a kimenő forgalomhoz, letiltani a kimenő SNAT a terheléselosztási szabályban.


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>2. forgatókönyv: a [SNAT](load-balancer-outbound-connections.md)-portok foglalásának módosítása


#### <a name="details"></a>Részletek


A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet méretétől függően](load-balancer-outbound-connections.md#preallocatedports). 


Ha SNAT kimerültséget tapasztal, növelje az alapértelmezett 1024-as számú [SNAT](load-balancer-outbound-connections.md)-portot. 


A nyilvános IP-címek legfeljebb 64 000 ideiglenes portot vesznek részt. A háttérrendszer-készletben lévő virtuális gépek száma határozza meg az egyes virtuális gépekre terjesztett portok számát. A háttér-készlet egyik virtuális gépe fér hozzá a maximális 64 000-as porthoz. Két virtuális gép esetében legfeljebb 32 000 [SNAT](load-balancer-outbound-connections.md)-portot lehet megadni kimenő szabállyal (2x 32 000 = 64 000). 


A kimenő szabályok használatával beállíthatja az alapértelmezés szerint megadott SNAT-portokat. Az alapértelmezett [SNAT](load-balancer-outbound-connections.md)-portok közül több vagy kevesebb is adható meg. A kimenő szabályok egy felületéről érkező összes nyilvános IP-cím akár 64 000 ideiglenes portot is felhasználhat [SNAT](load-balancer-outbound-connections.md)-portokként való használatra. 


A Load Balancer 8-nál több [SNAT](load-balancer-outbound-connections.md)-portot ad meg. Ha a 8 értékkel nem osztható értéket ad meg, a rendszer elutasítja a konfigurációs műveletet. Az egyes terheléselosztási szabályok és a bejövő NAT-szabályok 8 portot használnak. Ha a terheléselosztás vagy a bejövő NAT-szabály ugyanazon a tartományon osztozik, mint a 8, akkor további portok nem lesznek felhasználva.


Ha a nyilvános IP-címek száma alapján több [SNAT](load-balancer-outbound-connections.md)-portot próbál meg megadni, a rendszer elutasítja a konfigurációs műveletet. Ha például egy virtuális gépen 10 000 portot ad meg, és a háttérrendszer hét virtuális gépe egyetlen nyilvános IP-címmel rendelkezik, akkor a rendszer elutasítja a konfigurációt. A hét megszorozva a 10 000-as mérettel, amely meghaladja a 64 000-es portot. A forgatókönyv engedélyezéséhez adjon hozzá több nyilvános IP-címet a Kimenő szabály előtérbeli felületéhez. 


A portok számának megadásával visszaállíthatja az [alapértelmezett portok kiosztását](load-balancer-outbound-connections.md#preallocatedports) . Az első 50-es virtuálisgép-példány 1024 portot kap, a 51-100-as virtuálisgép-példányok pedig a maximális példányszámig 512-t kapnak. További információ a SNAT alapértelmezett kiosztásáról: [SNAT-portok foglalási táblázata](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>3. forgatókönyv: csak kimenő engedélyezése


#### <a name="details"></a>Részletek


Egy nyilvános standard Load Balancer használatával biztosíthatja a kimenő NAT-t a virtuális gépek csoportjai számára. Ebben a forgatókönyvben saját maga is használhat kimenő szabályt a további szabályok konfigurálása nélkül.


> [!NOTE]
> Az **Azure Virtual Network NAT** a terheléselosztó szükségessége nélkül biztosíthatja a kimenő kapcsolatot a virtuális gépek számára. További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>4. forgatókönyv: kimenő NAT csak virtuális gépek számára (nincs bejövő)


> [!NOTE]
> Az **Azure Virtual Network NAT** a terheléselosztó szükségessége nélkül biztosíthatja a kimenő kapcsolatot a virtuális gépek számára. További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Részletek


Ebben a forgatókönyvben: Azure Load Balancer a kimenő szabályok és a Virtual Network NAT a virtuális hálózatból való kilépéshez elérhető lehetőségek.


1. Hozzon létre egy nyilvános IP-címet vagy előtagot.
2. Hozzon létre egy nyilvános standard Load balancert. 
3. Hozzon létre egy, a nyilvános IP-címhez vagy a kimenő számára dedikált előtaghoz társított előtérbeli felületet.
4. Hozzon létre egy háttér-készletet a virtuális gépek számára.
5. Helyezze a virtuális gépeket a háttér-készletbe.
6. Konfiguráljon egy kimenő szabályt a kimenő NAT engedélyezéséhez.



A [SNAT](load-balancer-outbound-connections.md)-portok méretezéséhez használjon előtagot vagy nyilvános IP-címet. Adja hozzá a kimenő kapcsolatok forrását egy engedélyezési vagy megtagadási listához.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>5. forgatókönyv: kimenő NAT belső standard Load Balancerhez


> [!NOTE]
> Az **Azure Virtual Network NAT** a belső standard Load balancert használó virtuális gépek kimenő kapcsolatait is biztosíthatja. További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Részletek


A kimenő kapcsolat nem érhető el a belső standard Load Balancer számára, amíg explicit módon be nem nyilvánították a példányok nyilvános IP-címein vagy Virtual Network NAT-on keresztül, vagy ha a háttér-készlet tagjait csak kimenő terheléselosztó-konfigurációval társítja. 


További információ: [csak kimenő terheléselosztó konfigurálása](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>6. forgatókönyv: mindkét TCP-& UDP protokoll engedélyezése a kimenő NAT-hoz egy nyilvános standard Load balancerrel


#### <a name="details"></a>Részletek


Nyilvános standard Load Balancer használata esetén a megadott automatikus kimenő NAT megfelel a terheléselosztási szabály átviteli protokolljának. 


1. Tiltsa le a kimenő [SNAT](load-balancer-outbound-connections.md)a terheléselosztási szabályban. 
2. Konfiguráljon egy kimenő szabályt ugyanazon a terheléselosztóon.
3. Használja újra a virtuális gépek által már használt háttér-készletet. 
4. A Kimenő szabály részeként a "protokoll": "all" érték adható meg. 


Ha csak a bejövő NAT-szabályok vannak használatban, a rendszer nem biztosít kimenő NAT-szabályokat. 


1. Helyezze a virtuális gépeket egy háttérbeli készletbe.
2. Egy vagy több előtér-IP-konfiguráció definiálása nyilvános IP-cím (ek) vagy nyilvános IP-előtag esetén 
3. Konfiguráljon egy kimenő szabályt ugyanazon a terheléselosztóon. 
4. A "protokoll": "all" érték meghatározása a Kimenő szabály részeként


## <a name="limitations"></a>Korlátozások

- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- A Load Balancer nem támogatja az ICMP protokollt a kimenő NAT-hoz.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Nem hozható létre Kimenő szabály a virtuális gép vagy NVA másodlagos IP-címéhez. Több hálózati adapter is támogatott.
- A kimenő kapcsolatok esetében a **rendelkezésre állási csoporton** belüli összes virtuális gépet hozzá kell adni a háttérbeli készlethez. 
- A **virtuálisgép-méretezési csoporton** belüli összes virtuális gépet a kimenő kapcsolathoz hozzá kell adni a háttérbeli készlethez.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure standard Load Balancer](load-balancer-overview.md)
- Tekintse meg a [Azure Load Balancerekkel kapcsolatos gyakori kérdéseket](load-balancer-faqs.md)
