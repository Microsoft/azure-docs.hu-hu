---
title: Azure hálózati architektúra
description: Ez a cikk a Microsoft Azure infrastruktúra hálózatának általános leírását tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89567867"
---
# <a name="azure-network-architecture"></a>Azure hálózati architektúra
Az Azure hálózati architektúrája az internetről az Azure-adatközpontokhoz biztosít kapcsolatot. Az Azure-ban üzembe helyezett összes számítási feladat (IaaS, Pásti és SaaS) az Azure-adatközpont-hálózatot hasznosítja.

## <a name="network-topology"></a>Hálózati topológia
Az Azure-adatközpontok hálózati architektúrája a következő összetevőkből áll:

- Peremhálózati hálózat
- Nagy kiterjedésű hálózat
- Regionális átjárók hálózata
- Adatközpont hálózata

![Az Azure Network diagramja](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Hálózati összetevők
A hálózati összetevők rövid leírása.

- Peremhálózati hálózat

   - A Microsoft hálózata és más hálózatok (például az Internet, a vállalati hálózat) közötti kijelölési pont
   - Internet-és [ExpressRoute](../../expressroute/expressroute-introduction.md) -társítást biztosít az Azure-ban

- Nagy kiterjedésű hálózat

   - Microsoft intelligens gerinces hálózat, amely a világra terjed ki
   - Kapcsolatot biztosít az [Azure-régiók](https://azure.microsoft.com/global-infrastructure/geographies/) között

- Regionális átjáró

   - Az Azure-régió összes adatközpontjának összesítési pontja
   - Nagyszámú kapcsolatot biztosít az adatközpontok között egy Azure-régióban (például többszáz terabits/Datacenter)

- Adatközpont hálózata

   - Kapcsolat létesítése az adatközpontban található kiszolgálók között alacsony túlfizetett sávszélességgel

A fenti hálózati összetevők úgy vannak kialakítva, hogy maximális rendelkezésre állást biztosítsanak a mindig elérhető, mindig elérhető Felhőbeli vállalkozásoknak. A redundancia kialakítása és a hálózatba való beépítése a fizikai aspektusból egészen egészen a protokoll vezérlése érdekében történik.

## <a name="datacenter-network-resiliency"></a>Adatközpont hálózati rugalmassága
Nézzük meg a rugalmasság kialakításának elvét az Datacenter Network használatával.

Az adatközponti hálózat egy [Clos-hálózat](https://en.wikipedia.org/wiki/Clos_network)módosított verziója, amely magas szintű, kétszakaszos sávszélességet biztosít a Felhőbeli skálázási forgalom számára. A hálózat nagy számú, a hardver meghibásodása által okozott hatás csökkentése érdekében számos típusú eszközt használ. Ezek az eszközök stratégiai helyen találhatók a különböző fizikai helyszíneken, külön energiaellátási és hűtési tartománnyal a környezeti események hatásának csökkentése érdekében.  A vezérlési síkon az összes hálózati eszköz az OSI-modell 3. rétegének útválasztási módjaként fut, ami kiküszöböli a forgalmi hurok korábbi hibáját. A különböző rétegek közötti összes elérési út aktív, hogy magas redundanciát és sávszélességet biztosítson Equal-Cost többutas (ECMP) útválasztás használatával.

A következő ábra azt mutatja be, hogy az adatközpont-hálózatot a különböző hálózati eszközök is kiépítik. A diagramon a sávok olyan hálózati eszközök csoportjait képviselik, amelyek redundanciát és nagy sávszélességű kapcsolatot biztosítanak.

![Adatközpont hálózata](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez, tekintse meg a következőt:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
