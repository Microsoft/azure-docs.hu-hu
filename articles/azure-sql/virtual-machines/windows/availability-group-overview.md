---
title: A SQL Server always on rendelkezésre állási csoportok áttekintése
description: Ez a cikk SQL Server always on rendelkezésre állási csoportokat mutatja be az Azure Virtual Machines-on.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504167"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Always On rendelkezésre állási csoport SQL Server Azure-beli virtuális gépeken
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk a SQL Server Azure Virtual Machines (VM) szolgáltatásban való rendelkezésre állási csoportjait mutatja be. 

## <a name="overview"></a>Áttekintés

Az Azure Virtual Machines always on rendelkezésre állási csoportok hasonlók a [helyszíni always on rendelkezésre állási csoportokhoz](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Mivel azonban a virtuális gépek az Azure-ban futnak, néhány további szempontot is figyelembe kell vennie, mint például a VM-redundancia és az útválasztási forgalom az Azure-hálózaton. 

Az alábbi ábra az Azure-beli virtuális gépeken SQL Server rendelkezésre állási csoportot ábrázolja:

![Rendelkezésreállási csoport](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM-redundancia 

A redundancia és a magas rendelkezésre állás növeléséhez SQL Server virtuális gépeknek ugyanahhoz a [rendelkezésre állási csoporthoz](../../../virtual-machines/availability-set-overview.md)vagy különböző [rendelkezésre állási zónákhoz](../../../availability-zones/az-overview.md)kell tartoznia.

Ha a virtuális gépek egy készletét ugyanabban a rendelkezésre állási csoportba helyezi, a berendezés meghibásodása által okozott kimaradás (a rendelkezésre állási csoportba tartozó virtuális gépek nem osztják meg az erőforrásokat) vagy a frissítések (a rendelkezésre állási csoportba tartozó virtuális gépek nem frissülnek egyszerre). Availability Zones a teljes adatközpont meghibásodása elleni védelmet, és minden olyan zónát, amely egy adott régióban található adatközpontok készletét jelképezi.  Azáltal, hogy az erőforrások különböző Availability Zonesba vannak helyezve, az adatközpont-szintű leállás nélkül az összes virtuális gép offline állapotba kerülhet.

Az Azure-beli virtuális gépek létrehozásakor választania kell a rendelkezésre állási készletek és a Availability Zones konfigurálása között.  Az Azure-beli virtuális gépek nem vehetnek részt mindkettőben.


## <a name="connectivity"></a>Kapcsolat 

A hagyományos helyszíni telepítés során az ügyfelek a virtuális hálózat neve (VNN) használatával csatlakoznak a rendelkezésre állási csoport figyelője számára, és a figyelő a rendelkezésre állási csoportban lévő megfelelő SQL Server replikához irányítja a forgalmat. Azonban további követelmény, hogy az Azure-hálózaton keresztül irányítsa a forgalmat. 

Ha SQL Server Azure-beli virtuális gépeken, állítson be egy [Load balancert](availability-group-vnn-azure-load-balancer-configure.md) a rendelkezésre állási csoport figyelője felé irányuló forgalom átirányításához, vagy ha SQL Server 2019 CU8 vagy újabb verzióval rendelkezik, konfigurálhat egy [Distributed Network name (DNN) figyelőt](availability-group-distributed-network-name-dnn-listener-configure.md) , hogy lecserélje a hagyományos VNN rendelkezésre állási csoport figyelőjét. 

A fürt csatlakozási lehetőségeivel kapcsolatos további információkért lásd: [HADR-kapcsolatok továbbítása SQL Server Azure-beli virtuális gépeken](hadr-cluster-best-practices.md#connectivity). 

### <a name="vnn-listener"></a>VNN-figyelő 

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) használatával irányíthatja át a forgalmat az ügyfélről a hagyományos rendelkezésre állási csoport virtuális hálózati neve (VNN) figyelőre az Azure-hálózaton. 

A terheléselosztó tárolja a VNN-figyelő IP-címeit. Ha egynél több rendelkezésre állási csoporttal rendelkezik, a csoportoknak VNN-figyelőre van szükségük. Egy terheléselosztó több figyelőt is támogat.

Első lépésként tekintse meg [a Load Balancer konfigurálása](availability-group-vnn-azure-load-balancer-configure.md)című témakört. 

### <a name="dnn-listener"></a>DNN-figyelő

SQL Server 2019 CU8 az elosztott hálózati név (DNN) figyelő támogatását mutatja be. A DNN-figyelő felváltja a hagyományos rendelkezésre állási csoport figyelőjét, amely nem igényel Azure Load Balancert az Azure-hálózaton lévő forgalom irányításához. 

A DNN-figyelő a javasolt HADR-kapcsolati megoldás az Azure-ban, mivel leegyszerűsíti az üzembe helyezést, csökkenti a karbantartást és a költségeket, és meghibásodás esetén csökkenti a feladatátvételi időt. 

A DNN figyelő használatával cserélje le a meglévő VNN-figyelőt, vagy használja azt egy meglévő VNN-figyelővel együtt, hogy a rendelkezésre állási csoport két különböző csatlakozási ponttal rendelkezik – az egyik a VNN-figyelő neve (és a port, ha nem alapértelmezett), és egy az DNN-figyelő neve és portja. Ez olyan ügyfelek számára hasznos lehet, akik el szeretnék kerülni a terheléselosztó feladatátvételi késését, de továbbra is kihasználják a VNN-figyelőtől függő SQL Server szolgáltatások előnyeit, például az elosztott rendelkezésre állási csoportok, a Service Broker vagy a FileStream. További információ: [DNN-figyelő és SQL Server funkció együttműködési képesség](availability-group-dnn-interoperability.md)

Első lépésként tekintse meg [a DNN-figyelő konfigurálása](availability-group-distributed-network-name-dnn-listener-configure.md)című témakört.


## <a name="deployment"></a>Üzembe helyezés 

A rendelkezésre állási csoportok több lehetőség közül választhatnak az Azure-beli virtuális gépeken való SQL Server, amelyek némelyike több automatizálással rendelkezik, mint a többi. 

Az alábbi táblázat az elérhető lehetőségek összehasonlítását tartalmazza:

| | Azure Portal | Azure CLI/PowerShell | Gyorsindítás sablonok | Kézi |
|---------|---------|---------|---------|---------|
|**SQL Server-verzió** |2016 + |2016 +|2016 +|2012 +|
|**SQL Server-kiadás** |Enterprise |Enterprise |Enterprise |Enterprise, standard|
|**Windows Server-verzió**| 2016 + | 2016 + | 2016 + | Mind|
|**Létrehozza a fürtöt**|Igen|Igen | Igen |Nem|
|**Létrehozza a rendelkezésre állási csoportot** |Igen |Nem|Nem|Nem|
|**A figyelő és a Load Balancer egymástól függetlenül jön létre** |Nem|Nem|Nem|Igen|
|**Lehetséges DNN-figyelőt létrehozni ezzel a módszerrel?**|Nem|Nem|Nem|Igen|
|**WSFC kvórum konfigurálása**|Felhőbeli tanúsító|Felhőbeli tanúsító|Felhőbeli tanúsító|Mind|
|**DR több régióban** |Nem|Nem|Nem|Igen|
|**Többalhálózat támogatása** |Igen|Igen|Igen|Yes|
|**Meglévő AD támogatása**|Igen|Igen|Igen|Yes|
|**DR többzónás, ugyanabban a régióban**|Igen|Igen|Igen|Yes|
|**AD-t nem tartalmazó elosztott AG**|Nem|Nem|Nem|Igen|
|**Fürt nélküli elosztott AG** |Nem|Nem|Nem|Igen|

További információ: [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [Gyorsindítás sablonok](availability-group-quickstart-template-configure.md)és [manuális](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Megfontolandó szempontok 

Azure IaaS virtuális gépek vendég feladatátvevő fürtjein kiszolgálónként (fürtcsomópontonként) egyetlen hálózati adapter és egyetlen alhálózat használatát javasoljuk. Az Azure-hálózatkezelés fizikai redundanciával rendelkezik, ami felesleges hálózati adaptereket és alhálózatokat tesz lehetővé az Azure IaaS VM-vendég fürtön. Bár a fürtellenőrzési jelentés figyelmeztetést küld, amely szerint a csomópontok csak egyetlen hálózaton érhetők el, ez a figyelmeztetés nyugodtan figyelmen kívül hagyható az Azure IaaS virtuális gépek vendég feladatátvevő fürtjein. 

## <a name="next-steps"></a>Következő lépések

Tekintse át a [HADR ajánlott eljárásait](hadr-cluster-best-practices.md) , és ismerkedjen meg a rendelkezésre állási csoport üzembe helyezésével a [Azure Portal](availability-group-azure-portal-configure.md), az [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), a [Gyorsindítás sablonok](availability-group-quickstart-template-configure.md) vagy a [manuális](availability-group-manually-configure-prerequisites-tutorial.md)használatával.

Azt is megteheti, hogy egy [fürtözött rendelkezésre állási csoportot](availability-group-clusterless-workgroup-configure.md) vagy rendelkezésre állási csoportot telepít [több régióban](availability-group-manually-configure-multiple-regions.md).
