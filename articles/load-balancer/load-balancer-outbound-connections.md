---
title: SNAT a kimenő kapcsolatokhoz
description: Azt ismerteti, hogyan használhatók a Azure Load Balancer a kimenő internetkapcsolat SNAT végrehajtásához
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: d1632c66791dd5e697b95a2c5aaaddea81629abf
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052822"
---
# <a name="using-snat-for-outbound-connections"></a>SNAT használata a kimenő kapcsolatokhoz

Az Azure nyilvános terheléselosztó előtérbeli IP-címei használhatók a háttérbeli példányok számára az internet felé irányuló kimenő kapcsolat biztosításához. Ez a konfiguráció a **forrás hálózati címfordítást (SNAT)** használja. A SNAT újraírja a háttér IP-címét a terheléselosztó nyilvános IP-címére. 

A SNAT lehetővé teszi a háttér **-példány IP-címének maszkolását** . Ez a maszkolás megakadályozza, hogy a külső források közvetlenül a háttérbeli példányokhoz legyenek letiltva. A háttérbeli példányok közötti IP-cím megosztása csökkenti a statikus nyilvános IP-címek költségeit, és olyan forgatókönyveket támogat, mint például az IP-címek engedélyezése az ismert nyilvános IP-címekről érkező forgalomhoz. 

>[!Note]
> Azon alkalmazások esetében, amelyekben nagy számú kimenő kapcsolat vagy nagyvállalati ügyfél használatára van szükség egy adott virtuális hálózatról, [Virtual Network NAT](../virtual-network/nat-overview.md) a javasolt megoldás. A dinamikus foglalás lehetővé teszi az egyszerű konfigurálást, és > a SNAT-portok leghatékonyabb használatát az egyes IP-címekről. Azt is lehetővé teszi, hogy a virtuális hálózat összes erőforrása megossza az IP-címek egy halmazát anélkül, hogy meg kellene osztania őket a terheléselosztó >.

>[!Important]
> Még a kimenő SNAT konfigurálása nélkül is, az azonos régióban található Azure Storage-fiókok továbbra is elérhetők lesznek, és a háttérbeli erőforrások továbbra is hozzáférhetnek a Microsoft-szolgáltatásokhoz, például Windows-frissítésekhez.

>[!NOTE] 
>Ez a cikk csak Azure Resource Manager központi telepítéseket tárgyalja. Tekintse át az Azure-beli klasszikus üzembe helyezési forgatókönyvek [kimenő kapcsolatait (klasszikus)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) .

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Előtéri IP-cím megosztása a háttérbeli erőforrások között

Ha egy terheléselosztó háttér-erőforrásai nem rendelkeznek ILPIP-címmel, a nyilvános terheléselosztó előtérbeli IP-címén keresztül hozhatnak létre kimenő kapcsolatokat. A portok a különböző folyamatok fenntartásához használt egyedi azonosítók előállítására szolgálnak. Az Internet egy öt rekord használatával biztosítja ezt a különbséget.

Az öt rekord a következőkből áll:

* Cél IP-címe
* Célport
* Forrás IP-címe
* A forrás portszáma és a protokoll a különbségtétel biztosításához.

Ha a bejövő kapcsolatokhoz portot használ, akkor a bejövő kapcsolatokra vonatkozó kérelmeket **figyelő** a porton, és nem használható kimenő kapcsolatokhoz. A kimenő kapcsolatok létrehozásához egy **ideiglenes portot** kell használni, amely lehetővé teszi, hogy a cél egy olyan porton keresztül legyen elérhető, amelyen keresztül kommunikálni és karbantartani kell a különböző forgalmi folyamatokat. Ha ezek az ideiglenes portok a SNAT elvégzéséhez használatosak, **SNAT-portok** 

Definíció szerint minden IP-címnek 65 535-as portja van. Minden port használható a TCP (Transmission Control Protocol) és az UDP (User Datagram Protocol) bejövő vagy kimenő kapcsolataihoz. Ha nyilvános IP-címet adnak hozzá a betöltési IP-címhez egy terheléselosztó számára, az Azure 64 000-as SNAT-portként való használatát teszi lehetővé. 

>[!NOTE]
> Az egyes terheléselosztási vagy bejövő NAT-szabályokhoz használt portok a 64 000-es portokból származó nyolc portból állnak, így csökkentve a SNAT jogosult portok számát. Ha egy terhelési > terheléselosztási vagy NAT-szabály ugyanabban a nyolc tartományban van, mint egy másik, akkor nem fog további portokat használni. 

A [kimenő](./outbound-rules.md) és a terheléselosztási szabályok révén ezek a SNAT-portok a háttérbeli példányok számára terjeszthetők, így a terheléselosztó nyilvános IP-címei megoszthatók a kimenő kapcsolatok számára.

Ha a lenti [2. forgatókönyv](#scenario2) be van állítva, az egyes backend-példányok gazdagépe a kimenő kapcsolat részét képező csomagok SNAT fogja végrehajtani. Ha a SNAT a háttérbeli példány kimenő kapcsolatain futtatja, a gazdagép átírja a forrás IP-címet az egyik előtérbeli IP-címhez. Az egyedi folyamatok fenntartása érdekében a gazdagép átírja az egyes kimenő csomagok forrás portját a háttér-példányhoz lefoglalt SNAT-portok egyikére.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>A kimenő kapcsolatok viselkedése különböző forgatókönyvek esetén
  * A virtuális gép nyilvános IP-címmel rendelkezik.
  * Virtuális gép nyilvános IP-cím nélkül.
  * Virtuális gép nyilvános IP-cím nélkül, standard Load Balancer nélkül.
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> 1. forgatókönyv: virtuális gép nyilvános IP-címmel


 | Szövetségek | Metódus | IP-protokollok |
 | ---------- | ------ | ------------ |
 | Nyilvános Load Balancer vagy önálló | [SNAT (forrás hálózati címfordítás)](#snat) </br> nincs használatban. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (biztonsági tartalom beágyazása) |


 #### <a name="description"></a>Description


 Az Azure a példány hálózati adapterének IP-konfigurációjához hozzárendelt nyilvános IP-címet használja az összes kimenő folyamathoz. A példányhoz minden elérhető ideiglenes port tartozik. Nem számít, hogy a virtuális gép terheléselosztás alatt áll-e. Ez a forgatókönyv elsőbbséget élvez a többiekkel szemben. 


 Egy virtuális géphez hozzárendelt nyilvános IP-cím 1:1-kapcsolat (nem 1: sok), és állapot nélküli 1:1 NAT-ként lett megvalósítva.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>2. forgatókönyv: virtuális gép nyilvános IP-cím nélkül és standard nyilvános Load Balancer mögött


 | Szövetségek | Metódus | IP-protokollok |
 | ------------ | ------ | ------------ |
 | Standard nyilvános Load Balancer | A terheléselosztó felületi IP-címeinek használata a [SNAT](#snat).| TCP </br> UDP |


 #### <a name="description"></a>Description


 A terheléselosztó erőforrás egy kimenő szabállyal vagy egy terheléselosztási szabállyal van konfigurálva, amely engedélyezi az alapértelmezett SNAT. Ez a szabály a nyilvános IP-frontend és a háttér-készlet közötti kapcsolat létrehozására szolgál. 


 Ha nem hajtja végre ezt a szabályt, a viselkedés a 3. forgatókönyvben leírtak szerint történik. 


 Az állapot-mintavétel sikerességéhez nincs szükség figyelőhöz tartozó szabályra.


 Amikor egy virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a forrás IP-címet a nyilvános terheléselosztó előtérbeli felületének nyilvános IP-címére. Ezt a fordítást a [SNAT](#snat)-on keresztül teheti meg. 


 A terheléselosztó nyilvános IP-címének ideiglenes portjai a virtuális gép által kezdeményezett egyes folyamatok megkülönböztetésére szolgálnak. A SNAT dinamikusan használja az [előlefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. 


 Ebben a kontextusban a SNAT használt ideiglenes portok neve SNAT-portok. Erősen ajánlott, hogy a [Kimenő szabályok](./outbound-rules.md) explicit módon legyenek konfigurálva. Ha az alapértelmezett SNAT egy terheléselosztási szabályon keresztül használja, az SNAT-portok előre le vannak foglalva az [alapértelmezett SNAT-portok kiosztási táblájában](#snatporttable)leírtak szerint.

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>3. forgatókönyv: a virtuális gép nyilvános IP-cím nélküli és a szabványos belső Load Balancer mögött


 | Szövetségek | Metódus | IP-protokollok |
 | ------------ | ------ | ------------ |
 | Standard belső terheléselosztó | Nincs internetkapcsolat.| Nincs |

 #### <a name="description"></a>Description
 
Standard belső terheléselosztó használata esetén a SNAT nem használ ideiglenes IP-címeket. Ez alapértelmezés szerint támogatja a biztonságot, és gondoskodik arról, hogy az erőforrás által használt összes IP-cím konfigurálható legyen, és le legyen foglalva. Ha standard belső terheléselosztó használatakor szeretne kimenő kapcsolatot létesíteni az internettel, állítson be egy példány szintű nyilvános IP-címet, hogy kövesse a viselkedését (1. forgatókönyv) [#scenario1], vagy vegye fel a háttérbeli példányokat egy standard nyilvános Load balancerbe egy olyan kimenő szabállyal, amely a belső terheléselosztó additon van konfigurálva a (2. forgatókönyv) [#scenario2] működésének követéséhez. 

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>4. forgatókönyv: nyilvános IP nélküli virtuális gép és az alapszintű Load Balancer mögött


 | Szövetségek | Metódus | IP-protokollok |
 | ------------ | ------ | ------------ |
 |Nincs </br> Alapszintű Load Balancer | [SNAT](#snat) a példány-szintű dinamikus IP-címmel| TCP </br> UDP | 

 #### <a name="description"></a>Description


 Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a forrás IP-címet egy dinamikusan lefoglalt nyilvános forrás IP-címére. Ez a nyilvános IP-cím **nem konfigurálható** , és nem foglalható le. Ez a cím nem számít az előfizetés nyilvános IP-erőforrásának korlátja alapján. 


 A nyilvános IP-cím fel lesz szabadítva, és új nyilvános IP-címet igényel, ha újra telepíti a következőket: 


 * Virtuális gép
 * Rendelkezésre állási csoport
 * Virtuálisgép-méretezési csoport 


 Ne használja ezt a forgatókönyvet az IP-címek engedélyezési listához való hozzáadásához. Használja az 1. vagy a 2. forgatókönyvet, ahol explicit módon deklarálja a kimenő viselkedést. Az [SNAT](#snat) -portok az [alapértelmezett SNAT-portok kiosztási táblájában](#snatporttable)leírt módon vannak lefoglalva.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Kimerített portok

A célként megadott IP-címhez és célporthoz való minden kapcsolódás SNAT-portot fog használni. Ez a kapcsolódás külön **forgalmat** tart fenn a háttér-példány vagy az **ügyfél** között egy **kiszolgálóra**. Ez a folyamat egy különálló portot ad a kiszolgálónak, amely a forgalom kezelésére szolgál. A folyamat nélkül az ügyfélszámítógép nem ismeri a csomag részét képező adatfolyamot.

Képzelje el, hogy több böngésző https://www.microsoft.com is van, ami a következő:

* Cél IP-címe = 23.53.254.142
* Célport = 443
* Protokoll = TCP

Különböző célport nélkül a visszaadott forgalomhoz (a kapcsolat létrehozásához használt SNAT-port) az ügyfélnek nincs lehetősége arra, hogy egy lekérdezési eredményt válasszon egy másikból.

A kimenő kapcsolatok feltörtek. A háttér-példányok nem rendelkeznek elegendő porttal. Ha nincs engedélyezve a **kapcsolatok újrafelhasználása** , a SNAT- **portok kimerülésének** kockázata megnő.

A célként megadott IP-címhez tartozó új kimenő kapcsolatok sikertelenek lesznek, ha a portok kimerülése történik. Ha egy port elérhetővé válik, a kapcsolatok sikeresek lesznek. Ez a kimerültség akkor fordul elő, ha az IP-címről érkező 64 000-portok a különböző háttérbeli példányok között vékonyra vannak osztva. A SNAT-portok kimerülésének mérséklésével kapcsolatos útmutatásért lásd a [hibaelhárítási útmutatót](./troubleshoot-outbound-connection.md).  

A TCP-kapcsolatok esetében a terheléselosztó egyetlen SNAT-portot fog használni minden cél IP-címhez és porthoz. Ez a multiuse több kapcsolatot engedélyez ugyanahhoz a cél IP-címhez ugyanahhoz a SNAT-porthoz. Ez a multiuse korlátozott, ha a kapcsolódás nem különböző célport.

Az UDP-kapcsolatok esetében a terheléselosztó egy **portra korlátozott kúp NAT** -algoritmust használ, amely a célként megadott IP-címenként egy SNAT portot használ. 

A portok korlátlan számú kapcsolatra vannak újra felhasználva. A portot csak akkor használja a rendszer, ha a célként megadott IP-cím vagy port eltér.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Alapértelmezett portok kiosztása

A terheléselosztó előtér-IP-címéhez hozzárendelt nyilvános IP-címek 64 000 SNAT-portot kapnak a háttérbeli készlet tagjai számára. A portok nem oszthatók meg a háttérbeli készlet tagjaival. Egy SNAT-portot csak egyetlen backend-példány használhat, így biztosítva, hogy a visszatérési csomagok megfelelően legyenek irányítva. 

Ajánlott explicit kimenő szabályt használni a SNAT-portok kiosztásának konfigurálásához. Ez a szabály maximalizálja azon SNAT-portok számát, amelyek esetében az egyes backend-példányok elérhetők a kimenő kapcsolatok számára. 

Ha a kimenő SNAT automatikus kiosztását egy terheléselosztási szabályon keresztül használja, a foglalási tábla meghatározza a portok kiosztását.

A következő <a name="snatporttable"></a> táblázat a SNAT portok előfoglalásait mutatja be a háttérbeli készlet méreteihez:

| Készlet mérete (VM-példányok) | Az előlefoglalt SNAT-portok száma IP-konfiguráció alapján |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> Ha a háttér-készlet legfeljebb 10 mérettel rendelkezik, az egyes példányok esetében a 64000/10 = 6 400 portok is rendelkezhetnek, ha explicit kimenő szabályt ad meg. A fenti táblázatnak megfelelően a rendszer csak akkor 1 024, ha az automatikus kiosztást választja.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Kimenő szabályok és Virtual Network NAT

Azure Load Balancer a kimenő szabályok és a Virtual Network NAT a virtuális hálózatból való kilépéshez elérhető lehetőségek.

További információ a kimenő szabályokról: [Kimenő szabályok](outbound-rules.md).

További információ az Azure Virtual Network NAT-ról: [Mi az az azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Korlátozások

*   Ha a kapcsolatok nem kapnak új csomagokat, a portok 4 – 120 percet követően lesznek felszabadítva.
  * Ez a küszöbérték a kimenő szabályok használatával konfigurálható.
*   Minden IP-cím 64 000 portot biztosít, amely a SNAT használható.
*   Minden port használható TCP-és UDP-kapcsolatokhoz egy cél IP-címhez is
  * Egy UDP SNAT portra van szükség, hogy a célport egyedi-e, vagy sem. A cél IP-címhez minden UDP-kapcsolatban egy UDP-SNAT portot használ a rendszer.
  * A TCP SNAT-portok több kapcsolathoz is használhatók ugyanahhoz a cél IP-címhez, ha a célként megadott portok eltérőek.
*   A SNAT kimerültség akkor következik be, amikor egy háttérbeli példány kifogyott a megadott SNAT-portok közül. A terheléselosztó továbbra is használhat fel nem használt SNAT-portokat. Ha a háttérbeli példány SNAT-portjai meghaladják a megadott SNAT-portokat, nem fog tudni új kimenő kapcsolatokat létesíteni.

## <a name="next-steps"></a>Következő lépések

*   [A kimenő kapcsolatok hibáinak elhárítása a SNAT kimerülése miatt](./troubleshoot-outbound-connection.md)
*   [Tekintse át az SNAT mérőszámait](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) , és ismerkedjen meg a megfelelő szűrési, felosztási és megtekintési módszerekkel.
