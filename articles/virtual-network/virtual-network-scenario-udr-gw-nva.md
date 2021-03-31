---
title: Hibrid kapcsolatok 2 rétegű alkalmazással | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe virtuális készülékeket és UDR többrétegű alkalmazási környezet létrehozásához az Azure-ban
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1d2dde4e77a39b114f721cd6d2be250141984e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231709"
---
# <a name="virtual-appliance-scenario"></a>Virtuális berendezés forgatókönyve
A nagyobb Azure-ügyfelek körében gyakran előfordul, hogy egy kétrétegű alkalmazást kell megadnia az internethez, miközben a helyszíni adatközpontból elérhetővé válik a háttérrendszer-hozzáférés. Ez a dokumentum végigvezeti a felhasználói útvonalakat (UDR), a VPN Gatewayt és a hálózati virtuális berendezéseket használó forgatókönyveken egy kétrétegű környezet üzembe helyezéséhez, amely megfelel az alábbi követelményeknek:

* A webalkalmazásnak csak a nyilvános internetről kell elérhetőnek lennie.
* Az alkalmazást futtató webkiszolgálónak képesnek kell lennie a háttérbeli alkalmazáskiszolgáló elérésére.
* Az internetről a webalkalmazásba irányuló összes forgalomnak a tűzfal virtuális készülékén kell haladnia. Ezt a virtuális készüléket csak internetes forgalomhoz fogja használni a rendszer.
* Az alkalmazáskiszolgáló felé irányuló összes forgalomnak egy tűzfalon áthaladó virtuális berendezésen kell átesnie. Ezt a virtuális készüléket a rendszer a háttérrendszer-kiszolgálóhoz való hozzáférésre fogja használni, és egy VPN Gateway keresztül éri el a helyszíni hálózatról érkező hozzáférést.
* A rendszergazdáknak képesnek kell lenniük a tűzfal virtuális berendezéseknek a helyszíni számítógépekről való felügyeletére egy, a kizárólag felügyeleti célokra használt, harmadik tűzfallal rendelkező virtuális berendezés használatával.

Ez egy szabványos peremhálózati hálózat (más néven DMZ) forgatókönyv egy DMZ-sel és egy védett hálózattal. Az ilyen forgatókönyvek az Azure-ban az NSG, a tűzfal virtuális berendezésekkel vagy a kettő kombinációjával hozhatók létre. Az alábbi táblázat a NSG és a tűzfal virtuális készülékek közötti előnyeit és hátrányait mutatja be.

|  | Előnyök | Hátrányok |
| --- | --- | --- |
| **NSG** |Díjmentes. <br/>Integrálva van az Azure-RBAC. <br/>Azure Resource Manager-sablonokban létrehozhatók szabályok. |Az összetettség nagyobb környezetekben változhat. |
| **Tűzfal** |Az adatsík teljes körű vezérlése. <br/>Központi felügyelet a tűzfal konzolján keresztül. |A tűzfal berendezésének díja. <br/>Nincs integrálva az Azure RBAC. |

Az alábbi megoldás a tűzfal virtuális készülékeit használja a peremhálózat (DMZ)/Protected hálózati forgatókönyv megvalósításához.

## <a name="considerations"></a>Megfontolandó szempontok
A fentiekben ismertetett környezet a jelenleg elérhető különböző funkciókkal is telepíthető az Azure-ban, az alábbiak szerint.

* **Virtuális hálózat (VNet)** . Az Azure-VNet hasonló módon működnek egy helyszíni hálózatban, és egy vagy több alhálózatba sorolhatók, így biztosítva a forgalom elkülönítését, és elkülönítik a problémákat.
* **Virtuális készülék**. Számos partner biztosít olyan virtuális berendezéseket az Azure piactéren, amelyek a fent ismertetett három tűzfalhoz is használhatók. 
* **Felhasználó által megadott útvonalak (UDR)**. Az útválasztási táblázatok tartalmazhatják az Azure Networking által a VNet belüli csomagok áramlását vezérlő UDR. Ezek az útválasztási táblák az alhálózatokra is alkalmazhatók. Az Azure egyik legújabb funkciója az útválasztási táblázat GatewaySubnet való alkalmazása, amely lehetővé teszi az Azure VNet érkező összes forgalom továbbítását a virtuális készülékre irányuló hibrid kapcsolatok révén.
* **IP-továbbítás**. Alapértelmezés szerint az Azure hálózatkezelési motorja csak akkor továbbítja a csomagokat a virtuális hálózati adapterekhez (NIC), ha a csomag cél IP-címe megegyezik a hálózati adapter IP-címével. Ezért ha egy UDR azt határozza meg, hogy egy csomagot el kell küldeni egy adott virtuális készüléknek, akkor az Azure hálózati motor el fogja dobni a csomagot. Annak biztosítása érdekében, hogy a csomag olyan virtuális GÉPHEZ legyen továbbítva (ebben az esetben egy virtuális berendezés), amely nem a csomag tényleges célja, engedélyeznie kell az IP-továbbítást a virtuális berendezés számára.
* **Hálózati biztonsági csoportok (NSG)**. Az alábbi példa nem használja az NSG-t, de a megoldás alhálózatokon és/vagy hálózati adaptereken alkalmazott NSG használatával tovább szűrheti az alhálózatok és hálózati adapterek forgalmát.

![IPv6-kapcsolat](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Ebben a példában van egy előfizetés, amely a következőket tartalmazza:

* 2 az ábrán nem látható erőforráscsoportok. 
  * **ONPREMRG**. A helyszíni hálózat szimulálásához szükséges összes erőforrást tartalmazza.
  * **AZURERG**. Az Azure-beli virtuális hálózati környezethez szükséges összes erőforrást tartalmazza. 
* Egy **onpremvnet** nevű VNet, amely egy, az alább felsorolt módon szegmentált helyszíni adatközpont utánzására szolgál.
  * **onpremsn1**. Egy, az Ubuntut futtató virtuális gépet (VM) tartalmazó alhálózat, amely egy helyszíni kiszolgálót utánoz.
  * **onpremsn2**. Egy Ubuntut futtató virtuális gépet tartalmazó alhálózat, amely egy rendszergazda által használt helyszíni számítógépet utánoz.
* Létezik egy **OPFW** nevű tűzfal virtuális készülék a **onpremvnet** , amely a **azurevnet**-alagút fenntartására szolgál.
* A **azurevnet** nevű VNet az alább látható módon szegmentált.
  * **azsn1**. Külső tűzfal alhálózata, amely kizárólag a külső tűzfalhoz használatos. Az összes internetes forgalom ezen az alhálózaton keresztül fog érkezni. Ez az alhálózat csak a külső tűzfalhoz csatolt hálózati adaptert tartalmaz.
  * **azsn2**. Előtér-alhálózat, amely az internetről elérhető webkiszolgálóként futó virtuális gépet üzemeltet.
  * **azsn3**. Háttérbeli alhálózat, amely az előtér-webkiszolgáló által elérhető háttér-alkalmazáskiszolgáló futtatására szolgáló virtuális gépet üzemeltet.
  * **azsn4**. Felügyeleti alhálózat, amely kizárólag az összes tűzfal virtuális készülékhez való felügyeleti hozzáférés biztosítására szolgál. Ez az alhálózat csak a megoldásban használt egyes tűzfal virtuális készülékek hálózati adapterét tartalmazza.
  * **Átjáróalhálózat**. Az Azure virtuális hálózatok és más hálózatok közötti kapcsolat biztosításához a ExpressRoute és a VPN Gateway Azure Hybrid kapcsolati alhálózata szükséges. 
* A **azurevnet** -hálózatban 3 tűzfal virtuális készülék található. 
  * **AZF1**. Nyilvános IP-cím-erőforrást használó külső tűzfal az Azure-ban. Győződjön meg arról, hogy rendelkezik egy, a piactéren vagy közvetlenül a készülék gyártójától származó sablonnal, amely egy 3 hálózati adapterből álló virtuális készüléket foglal magában.
  * **AZF2**. A **azsn2** és a **azsn3** közötti forgalom szabályozására szolgáló belső tűzfal. Ez egy 3 hálózati adapterből álló virtuális készülék is.
  * **AZF3**. A felügyeleti tűzfal elérhető a rendszergazdák számára a helyszíni adatközpontból, és csatlakoztatva van egy olyan felügyeleti alhálózathoz, amely az összes tűzfalat kezeli. A piactéren 2 virtuális hálózati adaptert is megtalálhat, vagy közvetlenül a készülék gyártójától kérhet le egyet.

## <a name="user-defined-routing-udr"></a>Felhasználó által megadott útválasztás (UDR)
Az Azure minden alhálózata csatolható egy UDR-táblához, amely az adott alhálózaton kezdeményezett forgalom irányításának meghatározására szolgál. Ha nincs definiálva UDR, az Azure az alapértelmezett útvonalakat használja, hogy engedélyezi a forgalmat az egyik alhálózatról a másikra. A UDR jobb megismeréséhez látogasson el [a felhasználók által megadott útvonalakra és az IP-továbbításra](virtual-networks-udr-overview.md).

Annak biztosítása érdekében, hogy a kommunikáció a megfelelő tűzfal-berendezésen keresztül történjen, a fenti legutolsó követelmény alapján létre kell hoznia a következő útválasztási táblázatot, amely tartalmazza a UDR a **azurevnet**-ben.

### <a name="azgwudr"></a>azgwudr
Ebben az esetben az egyetlen, a helyszínről az Azure-ba irányuló forgalom lesz használva a tűzfalak kezeléséhez a **AZF3** való csatlakozással, és a forgalomnak a belső tűzfalon ( **AZF2**) keresztül kell haladnia. Ezért csak egy útvonal szükséges a **GatewaySubnet** az alább látható módon.

| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Lehetővé teszi a helyszíni forgalom számára a felügyeleti tűzfal **AZF3** elérését |

### <a name="azsn2udr"></a>azsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Engedélyezi a forgalmat az alkalmazáskiszolgáló futtatására szolgáló háttér-alhálózaton a **AZF2** használatával. |
| 0.0.0.0/0 |10.0.2.10 |Lehetővé teszi az összes többi forgalom átirányítását a **AZF1** -on keresztül |

### <a name="azsn3udr"></a>azsn3udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Lehetővé teszi, hogy a **azsn2** az App Serverről a webkiszolgálóról a **AZF2** keresztül áramlson át |

A helyszíni adatközpontot a **onpremvnet** alhálózatai számára is létre kell hoznia.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Engedélyezi a **onpremsn2** a **OPFW** keresztül |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Engedélyezi a forgalmat az Azure-beli **OPFW** keresztül |
| 192.168.1.0/24 |192.168.2.4 |Engedélyezi a **onpremsn1** a **OPFW** keresztül |

## <a name="ip-forwarding"></a>IP-továbbítás
A UDR és az IP-továbbítás olyan funkciók, amelyeket kombinálva lehetővé teheti a virtuális készülékek számára a forgalom vezérlését egy Azure-VNet.  A virtuális készülék nem más, mint egy virtuális gép, amelyen egy olyan alkalmazás fut, ami a hálózati forgalmat kezeli valamilyen módon, például tűzfallal vagy NAT-eszközzel.

A virtuális készüléknek képesnek kell lennie fogadni a nem neki címzett bejövő forgalmat. Ahhoz, hogy egy virtuális gép számára engedélyezze a más célhelyre irányított forgalom fogadását, először engedélyeznie kell a virtuális gép számára az IP-továbbítást. Ez egy Azure beállítás, nem a vendég operációs rendszer beállítása. A virtuális berendezésnek továbbra is futtatnia kell valamilyen típusú alkalmazást a bejövő forgalom kezeléséhez, és megfelelően kell átirányítani.

Ha többet szeretne megtudni az IP-továbbításról, tekintse meg a [Mi a felhasználó által megadott útvonalakat és az IP-továbbítást](virtual-networks-udr-overview.md).

Tegyük fel például, hogy a következő beállítással rendelkezik egy Azure-vnet:

* Az alhálózat **onpremsn1** tartalmaz egy **onpremvm1** nevű virtuális gépet.
* Az alhálózat **onpremsn2** tartalmaz egy **onpremvm2** nevű virtuális gépet.
* Egy **OPFW** nevű virtuális készülék csatlakozik a **onpremsn1** és a **onpremsn2**.
* A **onpremsn1** kapcsolt felhasználó által megadott útvonal azt adja meg, hogy a **onpremsn2** -ra irányuló összes forgalmat el kell-e juttatni a **OPFW**.

Ezen a ponton, ha a **onpremvm1** megpróbál kapcsolatot létesíteni a **onpremvm2**-mel, a rendszer a UDR fogja használni, és a rendszer a forgalmat a következő ugrásként fogja elküldeni a **OPFW** . Ne feledje, hogy a tényleges csomag célhelye nem módosul, ezért a **onpremvm2** is a cél. 

Ha az IP-továbbítás nincs engedélyezve a **OPFW**, az Azure-beli virtuális hálózati logika elveszi a csomagokat, mivel ez csak akkor engedélyezi a csomagok küldését egy virtuális gépre, ha a virtuális gép IP-címe a csomag célhelye.

Az IP-továbbítással az Azure virtuális hálózati logikája továbbítja a csomagokat a OPFW, az eredeti célcím módosítása nélkül. A **OPFW** kezelnie kell a csomagokat, és meg kell határoznia, hogy mi történjen velük.

Ahhoz, hogy a fenti forgatókönyv működjön, engedélyeznie kell az IP-továbbítást a hálózati adaptereken az útválasztáshoz használt **OPFW**, **AZF1**, **AZF2** és **AZF3** számára (az összes hálózati adaptert, kivéve a felügyeleti alhálózathoz kapcsolódókat). 

## <a name="firewall-rules"></a>Tűzfalszabályok
A fentiekben leírtak szerint az IP-továbbítás csak a virtuális készülékeknek küldött csomagokat biztosítja. A készüléknek még el kell döntenie, hogy mi történjen a csomagokkal. A fenti forgatókönyvben a következő szabályokat kell létrehoznia a készülékeken:

### <a name="opfw"></a>OPFW
A OPFW a következő szabályokat tartalmazó helyszíni eszközt jelöli:

* **Route**: az 10.0.0.0/16 (**azurevnet**) összes forgalmát továbbítani kell az alagút **ONPREMAZURE** keresztül.
* **Házirend**: az összes kétirányú forgalom engedélyezése a **Port2** és a **ONPREMAZURE** között.

### <a name="azf1"></a>AZF1
A AZF1 egy Azure-beli virtuális készüléket jelöl, amely a következő szabályokat tartalmazza:

* **Házirend**: az összes kétirányú forgalom engedélyezése a **port1** és a **port2** között.

### <a name="azf2"></a>AZF2
A AZF2 egy Azure-beli virtuális készüléket jelöl, amely a következő szabályokat tartalmazza:

* **Route**: minden 10.0.0.0/16 (**onpremvnet**) adatforgalmat el kell juttatni az Azure Gateway IP-címére (azaz 10.0.0.1) a **port1**-on keresztül.
* **Házirend**: az összes kétirányú forgalom engedélyezése a **port1** és a **port2** között.

## <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG)
Ebben a forgatókönyvben a NSG nincsenek használatban. A bejövő és a kimenő forgalom korlátozása érdekében azonban NSG is alkalmazhat az egyes alhálózatokra. Például a következő NSG szabályokat alkalmazhatja a külső FW-alhálózatra.

**Bejövő**

* Az internetről a 80-es portra irányuló összes TCP-forgalom engedélyezése az alhálózat bármely virtuális gépén.
* Az internetről érkező összes egyéb forgalom letiltása.

**Kimenő**

* Az internetre irányuló összes forgalom megtagadása.

## <a name="high-level-steps"></a>Magas szintű lépések
A forgatókönyv üzembe helyezéséhez kövesse az alábbi magas szintű lépéseket.

1. Jelentkezzen be az Azure-előfizetésbe.
2. Ha a helyszíni hálózatot VNet szeretné telepíteni, a **ONPREMRG** részét képező erőforrásokat kell kiépíteni.
3. A **AZURERG** részét képező erőforrások kiépítése.
4. Az alagút kiépítése a **onpremvnet** -ből a **azurevnet**-be.
5. Az összes erőforrás kiépítés után jelentkezzen be a **onpremvm2** -be, és Pingelje a 10.0.3.101 a **onpremsn2** és a **azsn3** közötti kapcsolat teszteléséhez.

