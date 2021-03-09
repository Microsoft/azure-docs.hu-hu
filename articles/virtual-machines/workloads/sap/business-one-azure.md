---
title: SAP Business One az Azure Virtual Machines-on | Microsoft Docs
description: SAP Business One az Azure-ban.
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.openlocfilehash: e17739c65c0b80beb1f6fdd09f31897b317d7858
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506888"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One az Azure Virtual Machinesban
Ez a dokumentum útmutatást nyújt az SAP Business One Azure Virtual Machines üzembe helyezéséhez. A dokumentáció nem helyettesíti a Business One for SAP szolgáltatás telepítési dokumentációját. A dokumentációnak az Azure-infrastruktúrára vonatkozó alapszintű tervezési és üzembe helyezési útmutatókat kell kiterjednie, amelyekkel az üzleti alkalmazások futtatása elérhető

A Business One két különböző adatbázist támogat:
- SQL Server – lásd: [SAP-megjegyzés #928839 – kiadás tervezése Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA – a SAP Business One támogatási mátrixának kifizetése SAP HANA esetében, az [SAP-termék rendelkezésre állási mátrixának](https://support.sap.com/pam) kifizetése

SQL Server kapcsolatban az [Azure Virtual Machines adatbázis-kezelő rendszerbe állítás az SAP NetWeaver](./dbms_guide_general.md) -ben című dokumentumban ismertetett alapszintű telepítési szempontok érvényesek. SAP HANA esetében a jelen dokumentum a következő szempontokat említi.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató használatához a következő Azure-összetevők alapszintű ismerete szükséges:

- [Azure-beli virtuális gépek Windows rendszeren](../../windows/tutorial-manage-vm.md)
- [Azure-beli virtuális gépek Linuxon](../../linux/tutorial-manage-vm.md)
- [Azure Hálózatkezelés és virtuális hálózatok kezelése a PowerShell-lel](../../windows/tutorial-virtual-network.md)
- [Azure-Hálózatkezelés és virtuális hálózatok parancssori felülettel](../../linux/tutorial-virtual-network.md)
- [Azure-lemezek kezelése az Azure CLI használatával](../../linux/tutorial-manage-disks.md)

Még ha csak a vállalat érdekli, az [Azure Virtual Machines az SAP NetWeaver megtervezése és megvalósítása](./planning-guide.md) hasznos információforrás lehet.

Feltételezi, hogy az SAP Business egyik példánya az alábbiakat telepíti:

- A SAP HANA telepítésének megismerése egy adott infrastruktúrán, például egy virtuális gépen
- Az SAP Business One-alkalmazás telepítése az Azure-beli virtuális gépekhez hasonló infrastruktúra esetén
- Ismerje meg az SAP Business One és a kiválasztott adatbázis-kezelő rendszer működését
- Ismerje meg az infrastruktúra üzembe helyezését az Azure-ban

A jelen dokumentum nem fedi le ezeket a területeket.

Az Azure-dokumentáción kívül érdemes figyelembe vennie a legfontosabb SAP-megjegyzéseket, amelyek a Business One-ra vonatkoznak, vagy amelyek az SAP for Business egy központi megjegyzései:

- [528296 – általános áttekintés Megjegyzés az SAP Business One kiadásokhoz és a kapcsolódó termékekhez](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 – kiadási frissítések megjegyzése az SAP Business One 9,2, verzió: SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 – központi Megjegyzés az SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 – kiadási frissítések megjegyzése az SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 – kollektív Megjegyzés az SAP Business One 9,3 általános problémáira](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 – csoportos tanácsadási Megjegyzés SAP-HANA-Related SAP Business One-ban, verzió: SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Üzleti egy architektúra
Az üzleti egy olyan alkalmazás, amely két rétegből áll:

- "Fat" ügyféllel rendelkező ügyféloldali rétegek
- A bérlőhöz tartozó adatbázis-sémát tartalmazó adatbázis-csomag

Az [SAP Business One rendszergazdai útmutatója](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) részletes áttekintést nyújt arról, hogy mely összetevők futnak az ügyfél részén, és mely részek futnak a kiszolgálói részben 

Mivel az ügyfél és az adatbázis-kezelői rétegek között jelentős késések vannak, az Azure-ban való üzembe helyezéskor mindkét szintet az Azure-ban kell elhelyezni. a felhasználók ezt követően az RDS szolgáltatást futtató egy vagy több virtuális gépre egy ügyfél-összetevőhöz tartozó ügyfelet futtatnak.

### <a name="sizing-vms-for-sap-business-one"></a>Virtuális gépek méretezése az SAP Business One-ban

Az ügyfél-virtuális gép (ek) méretezésével kapcsolatban az erőforrás-követelmények az SAP-ben vannak dokumentálva az [SAP Business One Hardware követelmények útmutatójában](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Az Azure-ban a dokumentum 2,4-es fejezetében ismertetett követelményekkel kell összpontosítania és kiszámítani.

Az olyan Azure-beli virtuális gépek, amelyek a vállalat egy ügyfél-összetevőjét és az adatbázis-kezelői gazdagépet üzemeltetik, csak az SAP NetWeaver által támogatott virtuális gépek engedélyezettek. Az SAP NetWeaver által támogatott Azure-beli virtuális gépek listájának megkereséséhez olvassa el az [SAP-megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533).

SAP HANA futtatása a vállalati adatbázis-kezelői háttérként, csak a Hana [Certified IaaS platform listájában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) a Hana-ban található, a Hana-beli vállalatoknak listán szereplő virtuális gépeket a Hana támogatja. A vállalat egy ügyfél-összetevőjét nem érinti ez a SAP HANA adatbázis-kezelő rendszerként való szigorúbb korlátozás.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Az SAP Business One-hoz használt operációs rendszerek kiadásai

Elvileg mindig a legjobb, ha a legújabb operációsrendszer-kiadásokat használja. Különösen a Linux-térben az új Azure-funkciók a SUSE és a Red Hat újabb kisebb kiadásaival lettek bevezetve. A Windows-oldalon a Windows Server 2016 használata kifejezetten ajánlott.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Infrastruktúra üzembe helyezése az Azure-ban az SAP Business One-ban
A következő néhány fejezetben az SAP üzembe helyezéséhez fontos infrastruktúra-darabok.

### <a name="azure-network-infrastructure"></a>Azure hálózati infrastruktúra
Az Azure-ban üzembe helyezni kívánt hálózati infrastruktúra attól függ, hogy egyetlen üzleti rendszerbe helyezi a telepítést. Vagy akár egy olyan szolgáltató, aki több tucat üzleti tevékenységet üzemeltet az ügyfeleknek. Előfordulhat, hogy az Azure-hoz való kapcsolódás módjában is kisebb változások vannak a tervben. Az Azure-ba való VPN-kapcsolattal és a Active Directory a [VPN-en](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy a [ExpressRoute](../../../expressroute/expressroute-introduction.md) keresztül az Azure-ba való kiterjesztésével különböző lehetőségekkel haladhat át.

![Egyszerű hálózati konfiguráció a Business One-ban](./media/business-one-azure/simple-network-with-VPN.PNG)

A bemutatott egyszerűsített konfiguráció számos olyan biztonsági példányt vezet be, amelyek lehetővé teszik az Útválasztás szabályozását és korlátozását. A 

- Az útválasztó/tűzfal a helyszíni ügyfélen.
- A következő példány az Azure-beli [hálózati biztonsági csoport](../../../virtual-network/network-security-groups-overview.md) , amelynek segítségével bevezetheti az SAP Business One konfigurációját futtató Azure-VNet útválasztási és biztonsági szabályait.
- Annak elkerülése érdekében, hogy a felhasználók az egyik ügyfelet is lássák, az adatbázist futtató kiszolgálót is láthatják, külön kell választania az üzleti kiszolgálót üzemeltető virtuális gépet és az üzleti kiszolgálót, amely a VNet belül két különböző alhálózaton található.
- Az Azure NSG-t a két különböző alhálózathoz rendeli hozzá, hogy korlátozza a hozzáférést az üzleti egy kiszolgálóhoz.

Az Azure hálózati konfiguráció kifinomultabb verziója az Azure [-beli központi és küllős architektúrával kapcsolatos ajánlott eljárások](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)alapján érhető el. A hub és a küllő architektúra mintázata a következőhöz hasonló módon változtatja meg az első egyszerűsített konfigurációt:


![Hub és küllős konfiguráció a Business One-vel](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Azokban az esetekben, amikor a felhasználók az Azure-ba való magánhálózati kapcsolat nélkül csatlakoznak az interneten keresztül, az Azure-beli hálózat kialakításának összhangban kell lennie az Azure [és az internet között a DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)-hez készült Azure-hivatkozási architektúrában dokumentált elvekkel.

### <a name="business-one-database-server"></a>Üzleti egy adatbázis-kiszolgáló
Az adatbázis típusához SQL Server és SAP HANA érhető el. Az adatbázis-kezelőtől függetlenül olvassa el az [azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP-munkaterheléshez](./dbms_guide_general.md) című dokumentumot, amely általános ismereteket nyújt az adatbázis-kezelők üzembe helyezéséről az Azure-beli virtuális gépeken és a kapcsolódó hálózati és tárolási témakörökben

Bár az adott és általános adatbázis-dokumentumokban már szerepelnek, a következőket kell megismernie:

- [Kezelheti a Windows rendszerű virtuális gépek rendelkezésre állását az Azure-ban](../../availability.md) , és [kezelheti az Azure-beli linuxos virtuális gépek rendelkezésre állását](../../availability.md)
- [A virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Ezek a dokumentumok segítenek dönteni a tárolási típusok és a magas rendelkezésre állási konfiguráció kiválasztásáról.

Elvileg a következőket kell tennie:

- Prémium SSD-k használata a standard HDD-k használatával. Ha többet szeretne megtudni a rendelkezésre álló lemezek típusairól, tekintse meg a [lemez típusának kiválasztása](../../disks-types.md) című cikket.
- Az Azure Managed Disks használata nem felügyelt lemezeken
- Győződjön meg arról, hogy elegendő IOPS és I/O-átviteli sebesség van konfigurálva a lemez konfigurációjával
- /Hana/Data-és/Hana/log-kötet egyesítése a költséghatékony tárolási konfiguráció biztosítása érdekében


#### <a name="sql-server-as-dbms"></a>SQL Server adatbázis-kezelő rendszerként
SQL Server a vállalati adatbázis-kezelő rendszerként való üzembe helyezéséhez látogasson el a dokumentumra [SQL Server Azure Virtual Machines adatbázis-kezelő rendszerbe állítása az SAP NetWeaver szolgáltatáshoz](./dbms_guide_sqlserver.md). 

A SQL Server adatbázis-kezelői oldalának durva méretezési becslései a következők:

| Felhasználók száma | vCPU-k | Memória | Példa a virtuális gépek típusaira |
| --- | --- | --- | --- |
| legfeljebb 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| akár 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| akár 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| akár 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

A fent felsorolt méretezésnek egy ötletet kell megadnia, hogy hol kezdjen hozzá. Előfordulhat, hogy kevesebb vagy több erőforrásra van szüksége, ebben az esetben az Azure-ra való alkalmazkodás egyszerű. A virtuálisgép-típusok változása csak a virtuális gép újraindításával lehetséges.

#### <a name="sap-hana-as-dbms"></a>SAP HANA adatbázis-kezelő rendszerként
A SAP HANA használata a következő szakaszokban a dokumentum [SAP HANA az Azure üzemeltetési útmutatójának](./hana-vm-operations.md)szempontjait követve.

A magas rendelkezésre állást és a vész-helyreállítási konfigurációk SAP HANA az Azure-beli üzleti adatbázisként való használata esetén olvassa el a dokumentációt, SAP HANA az Azure-beli [virtuális gépek magas rendelkezésre állását](./sap-hana-availability-overview.md) , valamint a dokumentumból kimutatott dokumentációt.

A biztonsági mentési és visszaállítási stratégiák SAP HANA olvassa el az [Virtual Machines Azure-beli SAP HANA dokumentum biztonsági mentési útmutatóját](./sap-hana-backup-guide.md) , valamint a dokumentumból kimutatott dokumentációt.

 
### <a name="business-one-client-server"></a>Üzleti egy ügyfél-kiszolgáló
Ezeknek az összetevőknek a tárolási szempontjai nem az elsődleges szempont. azonban megbízható platformot szeretne használni. Ezért az Azure Premium Storaget kell használnia ehhez a virtuális géphez, még az alap VHD-hez is. A virtuális gép méretezése az [SAP Business One Hardware követelmények útmutatójában](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)megadott adattal. Az Azure-ban a dokumentum 2,4-es fejezetében ismertetett követelményekkel kell összpontosítania és kiszámítani. A követelmények kiszámítása során össze kell hasonlítani azokat a következő dokumentumokkal, hogy megtalálják az ideális virtuális gépet:

- [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md)
- [SAP-Megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)

A Microsoft által dokumentált processzorok és memória számának összevetése. A virtuális gépek kiválasztásakor tartsa szem előtt a hálózat átviteli sebességét is.
