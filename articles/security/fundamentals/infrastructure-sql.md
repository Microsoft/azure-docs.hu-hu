---
title: Azure SQL Database biztonsági funkciók
description: Ez a cikk általános leírást tartalmaz arról, hogyan védi a Azure SQL Database az Azure-beli ügyféladatokat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84021857"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database biztonsági funkciók    
A Azure SQL Database az Azure-ban egy kapcsolódó adatbázis-szolgáltatást biztosít. Az ügyféladatok védelme és az ügyfelek által a kapcsolati adatbázis-szolgáltatástól várt erős biztonsági funkciók biztosítása érdekében SQL Database saját biztonsági képességekkel rendelkezik. Ezek a képességek az Azure-ból örökölt vezérlőkre épülnek.

## <a name="security-capabilities"></a>Biztonsági képességek

### <a name="usage-of-the-tds-protocol"></a>A TDS protokoll használata
A Azure SQL Database csak a táblázatos adatfolyam (TDS) protokollt támogatja, amely megköveteli, hogy az adatbázis csak a TCP/1433 alapértelmezett portján legyen elérhető.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database tűzfal
Az ügyféladatok védelme érdekében Azure SQL Database tartalmaz egy tűzfal-funkciót, amely alapértelmezés szerint megakadályozza az összes SQL Database elérését az alább látható módon.

![Azure SQL Database tűzfal](./media/infrastructure-sql/sql-database-firewall.png)

Az átjáró tűzfala korlátozhatja a címeket, ami lehetővé teszi, hogy az ügyfelek részletesen szabályozzák az elfogadható IP-címek tartományait. A tűzfal az egyes kérések származó IP-címe alapján biztosítja a hozzáférést.

Az ügyfelek felügyeleti portál használatával vagy programozott módon érhetik el a tűzfal konfigurációját a Azure SQL Database felügyeleti REST API használatával. A Azure SQL Database-átjáró tűzfal alapértelmezés szerint megakadályozza az összes ügyfél TDS-hozzáférését a Azure SQL Databasehoz. Az ügyfeleknek hozzáférés-vezérlési listákkal (ACL-ekkel) kell konfigurálniuk a hozzáférést, hogy a forrás-és cél-internetes címek, protokollok és portszámok alapján Azure SQL Database kapcsolatokat.

### <a name="dosguard"></a>DoSGuard
A szolgáltatásmegtagadási (DoS-) támadások mennyiségét a DoSGuard nevű SQL Database-átjáró csökkenti. A DoSGuard aktívan nyomon követi a sikertelen bejelentkezések IP-címeit. Ha egy adott IP-címről több sikertelen bejelentkezés történt egy adott időtartamon belül, az IP-cím blokkolva lesz a szolgáltatásban lévő összes erőforráshoz egy előre meghatározott időszakra vonatkozóan.

Továbbá a Azure SQL Database átjáró a következőket hajtja végre:

- Biztonságos csatorna-képességgel kapcsolatos tárgyalások a TDS FIPS 140-2 ellenőrzött titkosított kapcsolatok megvalósításához, amikor az adatbázis-kiszolgálókhoz csatlakozik.
- Állapot-nyilvántartó TDS-csomagok ellenőrzése, miközben az ügyfelektől érkező kapcsolatokat fogad. Az átjáró ellenőrzi a kapcsolódási adatokat, és továbbítja a TDS-csomagokat a megfelelő fizikai kiszolgálóra a kapcsolódási karakterláncban megadott adatbázis neve alapján.

Az Azure SQL Database elsődleges hálózati biztonsági alapelve, hogy kizárólag azokat a kapcsolatokat és kommunikációt engedélyezi, amelyek a szolgáltatás működéséhez szükségesek. Minden más port, protokoll és kapcsolat alapértelmezés szerint le van tiltva. A virtuális helyi hálózatok (VLAN-ok) és a hozzáférés-vezérlési listák használatával a hálózati kommunikációt a forrás-és a célkiszolgáló, a protokollok és a portszámok alapján korlátozhatja.

A hálózat alapú ACL-ek megvalósítására jóváhagyott mechanizmusok közé tartoznak az útválasztók és a terheléselosztó ACL-jei. Ezeket a mechanizmusokat az Azure Networking, a vendég virtuálisgép-tűzfal és a Azure SQL Database az ügyfél által konfigurált tűzfalszabályok szabályozzák.

## <a name="data-segregation-and-customer-isolation"></a>Az adatelkülönítés és az ügyfelek elkülönítése
Az Azure éles hálózata úgy van strukturálva, hogy a nyilvánosan elérhető rendszerösszetevők el legyenek különítve a belső erőforrásokból. Fizikai és logikai határok vannak olyan webkiszolgálók között, amelyek hozzáférést biztosítanak a nyilvános Azure Portalhoz és az alapul szolgáló Azure virtuális infrastruktúrához, ahol az ügyfél-alkalmazás példányai és az ügyféladatok találhatók.

Minden nyilvánosan elérhető információ az Azure üzemi hálózaton belül kezelhető. Az éles hálózat a kétfaktoros hitelesítés és a határ védelmi mechanizmusok hatálya alá tartozik, és az előző szakaszban ismertetett tűzfal-és biztonsági szolgáltatáskészletot használja, és az adatelkülönítési függvényeket használja a következő szakaszokban leírtak szerint.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Nem engedélyezett rendszerek és az FC elkülönítése
Mivel a Fabric Controller (FC) az Azure-háló központi Orchestrator, jelentős szabályozások vannak érvényben a fenyegetések enyhítése érdekében, különösen a potenciálisan sérült FAs-n belül az ügyfelek alkalmazásaiban. Az FC nem ismeri fel azokat a hardvereket, amelyek eszközére vonatkozó információk (például a MAC-címek) nem lettek előre betöltve az FC-en belül. Az FC-ben lévő DHCP-kiszolgálók konfigurálták azon csomópontok MAC-címeinek listáját, amelyeket el kívánnak indítani. Még ha a jogosulatlan rendszerek is csatlakoztatva vannak, a rendszer nem épít bele a háló leltárba, ezért nincs csatlakoztatva vagy nem jogosult a háló leltárában lévő bármely rendszerrel való kommunikációra. Ez csökkenti annak kockázatát, hogy a jogosulatlan rendszerek kommunikálnak az FC-szel, és hozzáférést szerezzenek a VLAN-hoz és az Azure-hoz.

### <a name="vlan-isolation"></a>VLAN-elkülönítés
Az Azure-beli üzemi hálózatot logikailag elkülönítettük három elsődleges VLAN-ra:

- A fő VLAN: kapcsolat nélküli ügyfél-csomópontok közötti kapcsolat.
- Az FC VLAN: megbízható FCs-és támogató rendszereket tartalmaz.
- Az eszköz VLAN: megbízható hálózati és egyéb infrastruktúra-eszközöket tartalmaz.

### <a name="packet-filtering"></a>Csomagszűrés
Az IPFilter és a csomópontok operációs rendszerén megvalósított szoftveres tűzfalak kényszerítik a kapcsolati korlátozásokat, és megakadályozzák a virtuális gépek közötti jogosulatlan adatforgalmat.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, gyökér operációs rendszer és vendég virtuális gépek
A gyökérszintű operációs rendszer és a vendég virtuális gépek egymástól való elkülönítését a hypervisor és a root operációs rendszer felügyeli.

### <a name="types-of-rules-on-firewalls"></a>A tűzfalakra vonatkozó szabályok típusai
A szabály a következőképpen van definiálva:

{Src IP, src port, cél IP-cím, célport, cél protokoll, be/ki, állapot-nyilvántartó/állapot nélküli, állapot-nyilvántartó időtúllépés}.

A szinkron inaktív karakter (SYN) csomagok csak abban az esetben engedélyezettek, ha az egyik szabály engedélyezi azt. A TCP esetében az Azure állapot nélküli szabályokat használ, ahol az elv az, hogy csak az összes nem SYN-csomagot engedélyezi a virtuális gép számára. A biztonsági előfeltétel, hogy minden gazdagép-verem rugalmasan figyelmen kívül hagyja a nem SYN-csomagokat, ha korábban még nem látott SYN-csomagot. Maga a TCP protokoll állapota, és az állapot nélküli SYN-alapú szabállyal együtt az állapot-nyilvántartó megvalósítás általános viselkedését éri el.

Az UDP protokoll esetében az Azure állapot-nyilvántartó szabályt használ. Minden alkalommal, amikor egy UDP-csomag megfelel egy szabálynak, egy fordított folyamat jön létre a másik irányban. A folyamat beépített időtúllépéssel rendelkezik.

Az Azure által biztosított saját tűzfalak létrehozására az ügyfelek felelősek. Itt az ügyfelek meghatározhatják a bejövő és a kimenő forgalomra vonatkozó szabályokat.

### <a name="production-configuration-management"></a>Üzemi konfiguráció kezelése
A standard szintű biztonságos konfigurációkat az Azure-ban és Azure SQL Databaseban található megfelelő műveleti csapatok tartják karban. Az éles rendszerekre vonatkozó összes konfigurációs módosítás dokumentálva van, és nyomon követhető egy központi nyomkövető rendszeren keresztül. A szoftveres és a hardveres változásokat a központi nyomkövető rendszeren követheti nyomon. Az ACL-hez kapcsolódó hálózati változásokat az ACL Management szolgáltatással követheti nyomon.

Az Azure-ban az összes konfigurációs módosítást kifejlesztjük és teszteljük az átmeneti környezetben, ezeket azután éles környezetben helyezik üzembe. A szoftveres buildek felülvizsgálata a tesztelés részeként történik. A biztonsági és adatvédelmi ellenőrzéseket a belépési ellenőrzőlista feltételeinek részeként tekintjük át. A módosításokat a megfelelő üzembe helyezési csapat ütemezett időközönként telepíti. A kiadásokat a megfelelő üzembe helyezési csapat munkatársai tekintik át és írták alá, mielőtt üzembe helyezné őket az éles környezetben.

A változások figyelése sikeres. Meghibásodási forgatókönyv esetén a rendszer visszaállítja a változást a korábbi állapotába, vagy egy gyorsjavítást helyez üzembe a kijelölt személyzet jóváhagyásával kapcsolatos hibák elhárítása érdekében. A Source Depot, a git, a TFS, a Master Data Services (MDS), a Runners, az Azure Security monitoring, az FC és a WinFabric platform segítségével központilag felügyelheti, alkalmazhatja és ellenőrizheti az Azure-beli virtuális környezet konfigurációs beállításait.

Hasonlóképpen, a hardver-és hálózati változások ellenőrzési lépéseket tettek a létrehozási követelmények betartásának kiértékeléséhez. A kiadásokat a rendszer áttekinti és engedélyezi a megfelelő csoportok összevetését a veremben.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát, olvassa el a következő témakört:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
