---
title: A fürt konfigurálásának ajánlott eljárásai
description: Ismerje meg a támogatott fürtcsomópontokon, ha magas rendelkezésre állást és vész-helyreállítást (HADR) konfigurál az Azure Virtual Machines SQL Server, például a támogatott kvórumokat vagy a kapcsolatok útválasztási beállításait.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 4ab4e40e1dd4bbaf9ae73ab545285f5ae6261e27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201770"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>A fürtkonfiguráció ajánlott eljárásai (SQL Server Azure-beli virtuális gépeken)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A magas rendelkezésre állást és a vész-helyreállítást (HADR) használó fürt a SQL Server Azure Virtual Machines (VM) használatával. 

Ez a cikk a [Feladatátvevőfürt-példányok (FCIs-EK)](failover-cluster-instance-overview.md) és a [rendelkezésre állási csoportok](availability-group-overview.md) számára ajánlott, az Azure-beli virtuális gépeken való használattal SQL Server. 


## <a name="networking"></a>Hálózatkezelés

Egyetlen NIC-kiszolgálót (fürtcsomópont) és egyetlen alhálózatot használjon. Az Azure hálózatkezelésének fizikai redundancia van, így a további hálózati adapterek és alhálózatok szükségtelenek az Azure-beli virtuális gépek vendég fürtjében. A fürt ellenőrzési jelentése figyelmezteti, hogy a csomópontok csak egyetlen hálózaton érhetők el. Ezt a figyelmeztetést figyelmen kívül hagyhatja az Azure-beli virtuális gépek vendég feladatátvevő fürtökön.

### <a name="tuning-failover-cluster-network-thresholds"></a>Feladatátvevő fürt hálózati küszöbértékének finomhangolása

Ha SQL Server AlwaysOn rendelkező Azure-beli virtuális gépeken futtatja a Windows feladatátvevő fürt csomópontjait, ajánlott a fürt beállításának módosítása egy nyugodtabb figyelési állapotra.  Ez sokkal stabilabb és megbízhatóbb lesz a fürt számára.  További részletekért lásd: [IaaS és SQL AlwaysOn – a feladatátvevő fürt hálózati küszöbértékének finomhangolása](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Kvórumerőforrás

Bár a két csomópontos fürt [kvórum-erőforrás](/windows-server/storage/storage-spaces/understand-quorum)nélkül működik, az ügyfeleknek feltétlenül szükségük van egy kvórum erőforrás használatára a termelési támogatáshoz. A fürt érvényesítése nem felel meg a fürtnek kvórum-erőforrás nélkül. 

A három csomópontos fürt gyakorlatilag egyetlen csomópontból álló veszteséget (legfeljebb két csomópontot) tud túlélni kvórum-erőforrás nélkül. Ha azonban a fürt két csomópontra van lebontva, fennáll a kockázata annak, hogy a fürtözött erőforrások a csomópont elvesztése vagy kommunikációs meghibásodása esetén offline állapotba lépnek.

A kvórum erőforrásának konfigurálása lehetővé teszi a fürt számára, hogy online állapotba lépjen, csak egy csomóponttal.

A következő táblázat felsorolja az Azure-beli virtuális géppel való használatra ajánlott sorrendben elérhető kvórum beállításokat, és a tanúsító lemez előnyben részesített választása: 


||[Tanúsító lemez](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness)  |[Tanúsító fájlmegosztás](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Támogatott operációs rendszer**| Mind |Windows Server 2016 +| Mind|


### <a name="disk-witness"></a>Tanúsító lemez

A tanúsító lemez egy kisméretű fürtözött lemez a fürt rendelkezésre álló tárolási csoportjában. Ez a lemez nagyon elérhető, és feladatátvételt hajt végre a csomópontok között. A fürt adatbázisának egy példányát tartalmazza, amelynek alapértelmezett mérete általában 1 GB-nál kisebb. A tanúsító lemez minden olyan fürt előnyben részesített kvóruma, amely az Azure megosztott lemezeit (vagy bármilyen megosztott lemezes megoldást, például a megosztott SCSI-t, az iSCSI-t vagy a Fibre Channel SAN-t) használja.  Fürtözött megosztott kötet nem használható tanúsító lemezként.

Konfiguráljon egy Azure-beli megosztott lemezt a tanúsító lemezként. 

Első lépésként tekintse meg [a tanúsító lemez konfigurálása](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)című témakört.


**Támogatott operációs rendszer**: mind   


### <a name="cloud-witness"></a>Felhőbeli tanúsító

A Felhőbeli tanúsító a feladatátvevő fürt Kvórumának olyan típusa, amely Microsoft Azure használatával szavaz a fürt kvórumáról. Az alapértelmezett méret körülbelül 1 MB, és csak az időbélyegző szerepel. A Felhőbeli tanúsító a több helyen, több zónában és több régióban történő üzembe helyezéshez ideális.

Első lépésként tekintse meg [a Felhőbeli tanúsító konfigurálása](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)című témakört.


**Támogatott operációs rendszer**: Windows Server 2016 és újabb verziók   


### <a name="file-share-witness"></a>Tanúsító fájlmegosztás

A tanúsító fájlmegosztás egy SMB-fájlmegosztás, amely általában Windows Server rendszert futtató fájlkiszolgálón van konfigurálva. Egy tanúsító. log fájlban tárolja a fürtszolgáltatási információkat, de nem tárolja a fürt adatbázisának másolatát. Az Azure-ban a fájlmegosztás külön virtuális gépen is konfigurálható.

Első lépésként tekintse [meg a tanúsító fájlmegosztás konfigurálása](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)című témakört.


**Támogatott operációs rendszer**: Windows Server 2012 és újabb verziók   

## <a name="connectivity"></a>Kapcsolat

A hagyományos helyszíni hálózati környezetekben a SQL Server feladatátvevő fürt példánya egyetlen számítógépen futó SQL Server egyetlen példánya lehet. Mivel a feladatátvevő fürt példánya feladatátvételt hajt végre a csomópontról a csomópontra, a példány virtuális hálózatának neve (VNN) egységes csatlakozási pontot biztosít, és lehetővé teszi, hogy az alkalmazások a SQL Server-példányhoz kapcsolódjanak anélkül, hogy a csomópont éppen aktív. Feladatátvétel esetén a virtuális hálózat neve az új aktív csomópontra van regisztrálva az indítás után. Ez a folyamat átlátható a SQL Serverhoz csatlakozó ügyfél vagy alkalmazás számára, és ez lekicsinyíti az állásidőt, amely miatt az ügyfél vagy az alkalmazás hibát tapasztal. Hasonlóképpen, a rendelkezésre állási csoport figyelője egy VNN használatával irányítja át a forgalmat a megfelelő replikára. 

Használjon Azure Load Balancer vagy elosztott hálózati névvel (DNN) rendelkező VNN, hogy átirányítsa a forgalmat az Azure-beli virtuális gépeken futó SQL Server, vagy egy rendelkezésre állási csoportban lévő meglévő VNN-figyelőt. 


A következő táblázat a HADR-kapcsolatok támogatását hasonlítja össze: 

| |**Virtual Network neve (VNN)**  |**Elosztott hálózat neve (DNN)**  |
|---------|---------|---------|
|**Operációs rendszer minimális verziója**| Mind | Windows Server 2016 |
|**Minimális SQL Server-verzió** |Mind |SQL Server 2019 CU2 (-es)<br/> SQL Server 2019 CU8 (AG)|
|**Támogatott HADR-megoldás** | Feladatátvevőfürt-példány <br/> Rendelkezésre állási csoport | Feladatátvevőfürt-példány <br/> Rendelkezésre állási csoport|


### <a name="virtual-network-name-vnn"></a>Virtuális hálózat neve (VNN)

Mivel a virtuális IP-hozzáférési pont az Azure-ban eltérően működik, konfigurálnia kell a [Azure Load Balancert](../../../load-balancer/index.yml) , hogy a forgalmat a (vagy a rendelkezésre állási csoport figyelője) IP-címére irányítsa. Az Azure Virtual Machines szolgáltatásban a terheléselosztó tárolja azon VNN IP-címét, amelyeknek a fürtözött SQL Server erőforrásai támaszkodnak. A terheléselosztó elosztja az előtéri bejövő folyamatokat, majd átirányítja a forgalmat a háttér-készlet által definiált példányokra. A forgalom áramlását a terheléselosztási szabályok és az állapot-mintavételek használatával konfigurálhatja. A SQL Server-alapú, a háttérbeli készlet példányai a SQL Server rendszert futtató Azure-beli virtuális gépek. 

A terheléselosztó használata során enyhe feladatátvételi késleltetés történik, mivel az állapotfigyelő szolgáltatás alapértelmezés szerint 10 másodpercenként ellenőrzi az élő állapotot. 

Első lépésként ismerje meg, hogyan konfigurálhatja Azure Load Balancer a [feladatátvevő fürt példányára](failover-cluster-instance-vnn-azure-load-balancer-configure.md) vagy egy [rendelkezésre állási csoportra](availability-group-vnn-azure-load-balancer-configure.md)

**Támogatott operációs rendszer**: mind   
**Támogatott SQL-verzió**: ALL   
**Támogatott HADR-megoldás**: feladatátvevő fürt példánya és rendelkezésre állási csoport   


### <a name="distributed-network-name-dnn"></a>Elosztott hálózat neve (DNN)

Az elosztott hálózat neve a SQL Server 2019 új Azure-szolgáltatása. A DNN lehetővé teszi, hogy SQL Server ügyfelek a terheléselosztó használata nélkül kapcsolódjanak a SQL Server feladatátvevő fürt példányához vagy a rendelkezésre állási csoporthoz. 

DNN-erőforrás létrehozásakor a fürt a fürt összes csomópontjának IP-címeivel köti össze a DNS-nevet. Az SQL-ügyfél megpróbál csatlakozni a lista minden IP-címéhez, hogy megkeresse, melyik erőforráshoz kell csatlakoznia.  Ezt `MultiSubnetFailover=True` a folyamatot a kapcsolatok karakterláncának megadásával gyorsíthatja fel. Ez a beállítás azt jelzi, hogy a szolgáltató az összes IP-címet párhuzamosan próbálja kipróbálni, így az ügyfél azonnal tud csatlakozni a (z) vagy a figyelőhöz. 

Ha lehetséges, az elosztott hálózatnév használata a terheléselosztó során ajánlott: 
- A végpontok közötti megoldás sokkal megbízhatóbb, mivel már nem kell fenntartania a terheléselosztó erőforrását. 
- A terheléselosztó-mintavételek kiküszöbölése a feladatátvétel időtartamát csökkentheti. 
- A DNN leegyszerűsíti a feladatátvevő fürt példánya vagy a rendelkezésre állási csoport figyelő üzembe helyezését és felügyeletét SQL Server Azure-beli virtuális gépeken. 

A legtöbb SQL Server a funkciók a DNN használatakor transzparens módon működnek együtt a rendelkezésre állási csoportokkal, de vannak olyan szolgáltatások, amelyek különleges megfontolást igényelhetnek. További információért lásd: a [DNN együttműködési](failover-cluster-instance-dnn-interoperability.md) és az [AG és a DNN együttműködési képesség](availability-group-dnn-interoperability.md) . 

Első lépésként ismerje meg, hogyan konfigurálhatja az elosztott hálózati név erőforrást [egy feladatátvevő fürt](failover-cluster-instance-distributed-network-name-dnn-configure.md) vagy egy [rendelkezésre állási csoport](availability-group-distributed-network-name-dnn-listener-configure.md) számára.

**Támogatott operációs rendszer**: Windows Server 2016 és újabb verziók   
**Támogatott SQL-verzió**: SQL Server 2019 CU2 (Version) és SQL Server 2019 CU8 (AG)   
**Támogatott HADR-megoldás**: feladatátvevő fürt példánya és rendelkezésre állási csoport   


## <a name="limitations"></a>Korlátozások

A következő korlátozásokat érdemes figyelembe venni, ha az Azure-ban vagy rendelkezésre állási csoportokkal dolgozik, és SQL Server az Azure Virtual Machines. 

### <a name="msdtc"></a>MSDTC 

Az Azure Virtual Machines támogatja a Microsoft Distributed Transaction Coordinator (MSDTC) szolgáltatást a Windows Server 2019 rendszeren a fürtözött megosztott kötetek (CSV) és az [azure standard Load Balancer](../../../load-balancer/load-balancer-overview.md) , illetve az Azure-beli megosztott lemezeket használó SQL Server virtuális gépeken. 

Az Azure Virtual Machines az MSDTC nem támogatott a Windows Server 2016-es vagy korábbi verzióiban fürtözött megosztott kötetekkel, mert:

- A fürtözött MSDTC-erőforrás nem konfigurálható megosztott tároló használatára. Windows Server 2016 rendszeren, ha MSDTC-erőforrást hoz létre, az nem fog tudni használni megosztott tárterületet, még akkor sem, ha rendelkezésre áll tárterület. Ezt a problémát a Windows Server 2019-es verzióban javítottuk.
- Az alapszintű Load Balancer nem kezeli az RPC-portokat.


## <a name="next-steps"></a>Következő lépések

Miután meghatározta a megoldáshoz szükséges ajánlott eljárásokat, első lépésként [készítse elő a SQL Server VMt a (](failover-cluster-instance-prepare-vm.md) [Azure Portal](availability-group-azure-portal-configure.md)), az [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)vagy az [Azure gyorsindítási sablonok](availability-group-quickstart-template-configure.md)használatával, vagy hozza létre a rendelkezésre állási csoportot.
