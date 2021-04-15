---
title: Forráshálózati címfordítás (SNAT) kimenő kapcsolatokhoz
titleSuffix: Azure Load Balancer
description: Megtudhatja, Azure Load Balancer hogyan használható a kimenő internetkapcsolat (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3b92ef3ce195a2eee9bce53e08d977593a9f1fc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477706"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Forráshálózati címfordítás (SNAT) használata kimenő kapcsolatokhoz

Az Azure nyilvános terheléselosztási kiszolgáló előtér-IP-je segítségével kimenő internetkapcsolatot lehet biztosítani a háttérpéldányok számára. Ez a konfiguráció **forráshálózati címfordítást (SNAT) használ.** Az SNAT átírja a háttérhálózat IP-címét a terheléselosztás nyilvános IP-címére. 

Az SNAT lehetővé **teszi** a háttérpéldány IP-címlekérdezését. Ez az álcázás megakadályozza, hogy a külső források közvetlenül a háttérpéldányokhoz címeket adnak. A háttérpéldányok között megosztott IP-címek csökkentik a statikus nyilvános IP-címek költségeit. Egy ismert IP-cím olyan forgatókönyveket támogat, mint például az IP-engedélyezési lista egyszerűsítése az ismert nyilvános IP-címekről származó forgalommal. 

>[!Note]
> A nagy számú kimenő kapcsolatot igénylő alkalmazások vagy olyan vállalati ügyfelek esetén, akiknek egyetlen IP-készletet kell használniuk egy adott [virtuális](../virtual-network/nat-overview.md) hálózatról, a Virtual Network NAT az ajánlott megoldás. A dinamikus kiosztás egyszerű konfigurációt tesz lehetővé, és az egyes IP-címekről származó SNAT-portok leghatékonyabb használatát teszi lehetővé. Lehetővé teszi, hogy a virtuális hálózatban lévő összes erőforrás ip-címeket osson meg anélkül, hogy egy terheléselosztást kellene megosztaniuk.

>[!Important]
> Még ha nincs is konfigurálva kimenő SNAT, az azonos régióban található Azure Storage-fiókok továbbra is elérhetők lesznek, és a háttérerőforrások továbbra is hozzáférhetnek Microsoft-szolgáltatások Windows-frissítésekhez.

>[!NOTE] 
>Ez a cikk csak Azure Resource Manager telepítéseket fedi le. Tekintse [át a kimenő kapcsolatok (klasszikus)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) áttekintését az Azure összes klasszikus üzembe helyezési forgatókönyve esetében.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Előtere IP-címének megosztása háttérerőforrások között

Ha egy terheléseltöltő háttérerőforrása nem rendelkezik példányszintű nyilvános IP- (ILPIP-) címekkel, akkor a nyilvános load balancer előtere IP-címén keresztül létesítik a kimenő kapcsolatot. A portokkal egyedi azonosítókat hozhat létre, amelyek különböző folyamatok fenntartására használhatók. Az internet öt rekordot használ ennek a különbségnek a megkülönböztetéshez.

Az öt rekord a következőkből áll:

* Cél IP-címe
* Célport
* Forrás IP-címe
* A megkülönböztetés forrásportja és protokollja.

Ha a bejövő kapcsolatokhoz portot használ, a porton található egy **listener** a bejövő kapcsolati kérelmekhez. Ez a port nem használható kimenő kapcsolatokhoz. Kimenő kapcsolat létesítenie **egyphemerális portot** használ, amely biztosítja a célnak azt a portot, amelyen keresztül kommunikálhat és fenntarthat egy különálló adatforgalmat. Ha ezeket aphemerális portokat használja az SNAT-hez, **SNAT-portnak nevezzük őket** 

Definíció szerint minden IP-cím 65 535 porttal rendelkezik. Minden port használható a TCP(Transmission Control Protocol) és az UDP(User Datagram Protocol) bejövő vagy kimenő kapcsolataihoz. 

Amikor egy nyilvános IP-címet előtéri IP-címként ad hozzá egy terheléselosztáshoz, az Azure 64 000 SNAT-portot biztosít.

>[!NOTE]
> A terheléselosztáshoz vagy a bejövő NAT-szabályhoz használt portok egy nyolc portos tartományt használnak a 64 000 portból, ezzel csökkentve az SNAT-ra jogosult portok számát. Ha egy terheléselosztási vagy nat-szabály nyolc tartományban van, mint egy másik, akkor nem használ további portokat. 

A [kimenő szabályok](./outbound-rules.md) és a terheléselosztási szabályok révén ezek az SNAT-portok eloszthatóak a háttérpéldányok között, így lehetővé teheti számukra a terheléselosztás nyilvános IP-jainak megosztását a kimenő kapcsolatokhoz.

Ha [az alábbi 2.](#scenario2) forgatókönyv be van állítva, az egyes háttérpéldányok gazdagépe SNAT-csomagokat fog kapni, amelyek egy kimenő kapcsolat részei. 

Amikor SNAT-t hoz létre egy háttérpéldányból kimenő kapcsolaton, a gazdagép átírja a forrás IP-címet az egyik előtere IP-címre. 

Az egyedi folyamatok fenntartása érdekében a gazdagép újraírja az egyes kimenő csomagok forrásportját egy SNAT-portra a háttérpéldányon.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Kimenő kapcsolatok viselkedése különböző forgatókönyvek esetén
  * Virtuális gép nyilvános IP-címmel.
  * Nyilvános IP-cím nélküli virtuális gép.
  * Virtuális gép nyilvános IP-cím és standard terheléselosztás nélkül.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip-either-with-or-without-a-load-balancer"></a><a name="scenario1"></a> 1. forgatókönyv: Virtuális gép nyilvános IP-címmel vagy terheléselosztással vagy anélkül.

 | Egyesületek | Metódus | IP-protokollok |
 | ---------- | ------ | ------------ |
 | Nyilvános terheléselosztás vagy önálló | [SNAT (forráshálózati címfordítás)](#snat) </br> nincs használva. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Biztonsági hasznos adatok beágyazása) |

 #### <a name="description"></a>Leírás

 Minden forgalom a virtuális gép nyilvános IP-címéből (példányszintű IP-cím) fog visszatérni a kérelmező ügyfélhez.
 
 Az Azure a példány hálózati adapterének IP-konfigurációjában hozzárendelt nyilvános IP-címet használja minden kimenő folyamathoz. A példány az összes elérhető, a rendszer által használt, a rendszer által használt porttal rendelkezik. Nem számít, hogy a virtuális gép terhelése elosztott-e vagy sem. Ez a forgatókönyv elsőbbséget élvez a többivel szemben. 

 A virtuális géphez rendelt nyilvános IP-cím egy 1:1-es kapcsolat (nem 1: több), és állapot nélküli 1:1 NAT-ként van megvalósítva.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>2. forgatókönyv: Nyilvános IP-cím nélküli virtuális gép, standard nyilvános Load Balancer

 | Egyesületek | Metódus | IP-protokollok |
 | ------------ | ------ | ------------ |
 | Standard nyilvános terheléselosztás | Terheléselosztási előtere IP-k használata az [SNAT-hez.](#snat)| TCP </br> UDP |

 #### <a name="description"></a>Leírás

 A terheléselosztási erőforrás kimenő vagy az SNAT-t lehetővé tő terheléselosztási s szabálysal van konfigurálva. Ez a szabály a háttérkészlet nyilvános IP-előtere közötti kapcsolat létrehozására használható. 

 Ha nem fejeződik be ez a szabálykonfiguráció, a viselkedés a 3. forgatókönyvben leírtak szerint történik. 

 Ahhoz, hogy az állapot-mintavétel sikeres legyen, nincs szükség listenerrel kapcsolatos szabályra.

 Amikor egy virtuális gép kimenő folyamatot hoz létre, az Azure lefordítja a forrás IP-címet a nyilvános terheléselosztási előtér nyilvános IP-címére. Ez a fordítás az [SNAT-n keresztül történik.](#snat) 

 A terheléselosztási előtere nyilvános IP-címénekphemeral portjai a virtuális gép által származó egyes folyamatok megkülönböztetésére használhatók. Kimenő folyamatok létrehozásakor az SNAT dinamikusan használ előre [lefoglalt,](#preallocatedports) kisajátító portokat. 

 Ebben a környezetben az SNAT-hez használtphemerális portokat SNAT-portnak nevezzük. Erősen ajánlott explicit módon konfigurálni [egy](./outbound-rules.md) kimenő szabályt. Ha alapértelmezett SNAT-t használ egy terheléselosztási szabályon keresztül, a rendszer előre lefoglalja az SNAT-portokat az alapértelmezett SNAT portfoglalási [táblázatban leírtak szerint.](#snatporttable)

> [!NOTE]
> **Azure Virtual Network NAT** a virtuális gépek kimenő kapcsolatát anélkül, hogy terheléselosztásra lenne szükség. További [információ: Mi Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>3. forgatókönyv: Nyilvános IP-címmel nem és Standard belső ip-cím mögötti virtuális Load Balancer

 | Egyesületek | Metódus | IP-protokollok |
 | ------------ | ------ | ------------ |
 | Standard belső terheléselosztás | Nincs internetkapcsolat.| Nincsenek |

 #### <a name="description"></a>Leírás
 
Standard belső terheléselosztás használata esetén az SNAT-hez nem használhatók időfokos IP-címek. Ez a funkció alapértelmezés szerint támogatja a biztonságot. Ez a funkció biztosítja, hogy az erőforrások által használt összes IP-cím konfigurálható és lefoglalható legyen. 

Ha standard belső terheléselosztást használ, kimenő internetkapcsolatot kell elérnie, konfigurálnia kell egy példányszintű nyilvános IP-címet, hogy az kövesse az [1. forgatókönyvben használt viselkedést.](#scenario1) 

Egy másik lehetőség, hogy hozzáadja a háttérpéldányokat egy standard nyilvános terheléselosztáshoz egy konfigurált kimenő s szabályt konfigurálva. A háttérpéldányok a belső terheléselosztáshoz hozzáadják a belső terheléselosztáshoz. Ez az üzembe helyezés a [2. forgatókönyvben is ezt a viselkedést követi.](#scenario2) 

> [!NOTE]
> **Azure Virtual Network NAT** a virtuális gépek kimenő kapcsolatát anélkül, hogy terheléselosztásra lenne szükség. További [információ: Mi Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>4. forgatókönyv: Nyilvános IP-cím nélküli és alapszintű ip-cím mögötti virtuális Load Balancer

 | Egyesületek | Metódus | IP-protokollok |
 | ------------ | ------ | ------------ |
 |Nincsenek </br> Alapszintű terheléselosztás | [SNAT](#snat) példányszintű dinamikus IP-címmel| TCP </br> UDP | 

 #### <a name="description"></a>Leírás

 Amikor a virtuális gép kimenő folyamatot hoz létre, az Azure egy dinamikusan megadott nyilvános IP-címre fordítja le a forrás IP-címét. Ez a nyilvános **IP-cím nem konfigurálható,** és nem foglalható le. Ez a cím nem számít bele az előfizetés nyilvános IP-cím erőforráskorlátba. 

A rendszer felszabadítja a nyilvános IP-címet, és új nyilvános IP-címet kér, ha újra üzembe fogja hozni a következőt: 

 * Virtuális gép
 * Rendelkezésre állási csoport
 * Virtuálisgép-méretezési csoport 

 Ne használja ezt a forgatókönyvet IP-k engedélyezési listához való hozzáadásához. Az 1. vagy 2. forgatókönyvben explicit módon deklarálhatja a kimenő viselkedést. [Az SNAT-portok](#snat) előre le vannak állítva az alapértelmezett SNAT portfoglalási [táblázatban leírtak szerint.](#snatporttable)

## <a name="exhausting-ports"></a><a name="scenarios"></a> Kimerítő portok

Az azonos cél IP-címmel és célporttal létesített minden kapcsolat SNAT-portot használ. Ez a kapcsolat  eltérő forgalmat tart fenn  a háttérpéldánytól vagy -ügyféltől a **kiszolgálóhoz.** Ez a folyamat egy különálló portot biztosít a kiszolgálónak a forgalom címzéséhez. E nélkül a folyamat nélkül az ügyfélszámítógép nem tudja, hogy melyik folyamat része egy csomagnak.

Tegyük fel, hogy több böngészőt is használ a https://www.microsoft.com következőre:

* Cél IP-címe = 23.53.254.142
* Célport = 443
* Protokoll = TCP

A visszatérő forgalom különböző célportjai (a kapcsolat létesítéséhez használt SNAT-port) nélkül az ügyfél nem fogja tudni elkülöníteni az egyik lekérdezési eredményt a másiktól.

A kimenő kapcsolatok felpördhet. A háttérpéldány elegendő portot is lefoglalhat. A **kapcsolat újbóli használatának** engedélyezése nélkül nő az SNAT-portok **kimerülésének** kockázata.

A cél IP-cím felé irányuló új kimenő kapcsolatok sikertelenek lesznek a portok kimerítése esetén. A kapcsolatok akkor lesznek sikeresek, ha egy port elérhetővé válik. Ez a kimerülés akkor fordul elő, ha az IP-címről származó 64 000 port sok háttérpéldány között dinamikusan el van vava. Az SNAT-portok kimerítésének elhárításával kapcsolatos útmutatásért tekintse meg a [hibaelhárítási útmutatót.](./troubleshoot-outbound-connection.md)  

TCP-kapcsolatok esetén a terheléselosztás egyetlen SNAT-portot használ minden cél IP-címhez és porthoz. Ez a többszöri mód több kapcsolatot tesz lehetővé ugyanabba a cél IP-címbe ugyanannak az SNAT-portnak a használatával. Ez a többszörös hozzáférés korlátozott, ha a kapcsolat nem a különböző célportokhoz van korlátozva.

UDP-kapcsolatok esetén a terheléselosztás port által korlátozott **cone NAT-algoritmust** használ, amely célportonként egy SNAT-portot használ cél-IP-címenként. 

A portok korlátlan számú kapcsolathoz használhatók újra. A portot csak akkor használja újra a rendszer, ha a cél IP-címe vagy portja eltér.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Alapértelmezett portfoglalás

A terheléselosztás előtereként hozzárendelt minden nyilvános IP-cím 64 000 SNAT-portot kap a háttérkészlet tagjai számára. A portok nem megoszthatóak a háttérkészlet tagjaival. Az SNAT-portok tartományát csak egyetlen háttérpéldány használhatja annak érdekében, hogy a visszaadott csomagok útválasztása megfelelő legyen. 

Javasoljuk, hogy explicit kimenő szabályt használjon az SNAT-portfoglalás konfigurálása érdekében. Ez a szabály maximalizálja az egyes háttérpéldányok SNAT-portjainak számát a kimenő kapcsolatokhoz. 

Ha terheléselosztási szabályon keresztül használja a kimenő SNAT automatikus lefoglalását, a kiosztási tábla határozza meg a portfoglalást.

Az alábbi táblázatban a háttérkészletméretek rétegei esetében az <a name="snatporttable"></a> SNAT-portok előhelyezései láthatóak:

| Készlet mérete (virtuálisgép-példányok) | Előre lefoglalt SNAT-portok IP-konfigurációnként |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Ha legfeljebb 10-es méretű háttérkészlettel rendelkezik, minden példány 64 000/10 = 6400 porttal is rendelkezik, ha explicit kimenő szabályt határoz meg. A fenti táblázat szerint mindegyik csak 1024-es lesz, ha automatikus foglalást választ.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Kimenő szabályok és Virtual Network NAT

Azure Load Balancer kimenő szabályok és Virtual Network NAT virtuális hálózatból kimenő forgalomhoz is elérhetők lehetőségek.

További információ a kimenő szabályokról: [Kimenő szabályok.](outbound-rules.md)

További információ a [Azure Virtual Network NAT: Mi a Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

## <a name="constraints"></a>Korlátozások

*   Ha egy kapcsolat tétlen, és nem küld új csomagokat, a portok 4–120 perc múlva felszabadulnak.
  * Ez a küszöbérték kimenő szabályokkal konfigurálható.
*   Minden IP-cím 64 000 portot biztosít, amelyek használhatók az SNAT-hez.
*   Az egyes portok TCP- és UDP-kapcsolatokhoz is használhatók egy cél IP-címhez
  * Szükség van egy UDP SNAT-portra, függetlenül attól, hogy a célport egyedi-e. A cél IP-címhez való minden UDP-kapcsolathoz egy UDP SNAT-portot használ a rendszer.
  * Egy TCP SNAT-port több kapcsolathoz is használható ugyanabba a cél IP-címbe, feltéve, hogy a célportok eltérnek.
*   SNAT-elfogyás akkor fordul elő, ha egy háttérpéldány adott SNAT-portból elfogy. A terheléselosztáshoz továbbra is lehet használaton kívüli SNAT-port. Ha egy háttérpéldány használt SNAT-portjai meghaladják a megadott SNAT-portokat, nem fog tudni új kimenő kapcsolatokat létesíteni.

## <a name="next-steps"></a>Következő lépések

*   [Kimenő kapcsolatok SNAT-kimerítés miatti hibáinak elhárítása](./troubleshoot-outbound-connection.md)
*   [Tekintse át a SNAT-metrikákat,](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) és ismerkedjen meg a szűrésük, felosztásuk és megtekintésük helyes módjaival.
