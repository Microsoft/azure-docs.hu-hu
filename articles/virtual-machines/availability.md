---
title: Az Azure Virtual Machines rendelkezésre állási lehetőségei
description: További információ a virtuális gépek Azure-beli futtatásának rendelkezésre állási lehetőségeiről
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 1ea87d40430dbf3edabd557b80ab1456b49f4605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507874"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Az Azure Virtual Machines rendelkezésre állási lehetőségei
Ez a cikk áttekintést nyújt az Azure Virtual Machines (VM) rendelkezésre állási lehetőségeiről.

## <a name="availability-zones"></a>Rendelkezésreállási zónák

A [rendelkezésre állási zónák](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) kibővítik a vezérlési szintet, hogy a virtuális gépeken elérhető alkalmazások és adatmennyiségek rendelkezésre álljanak. A rendelkezésre állási zónák egy Azure-régión belül fizikailag különálló zónák. Egy támogatott Azure-régióban három Availability Zones érhető el. 

Mindegyik rendelkezésre állási zóna különálló áramforrással, hálózattal és hűtéssel rendelkezik. Ha a megoldásokat úgy tervezik, hogy a zónákban replikált virtuális gépeket használjanak, az alkalmazások és az adatok védelme az adatközpont elvesztése miatt biztosítható. Ha egy zóna biztonsága sérül, a replikált alkalmazások és az adatszolgáltatások azonnal elérhetők lesznek egy másik zónában. 

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A [rendelkezésre állási csoport](availability-set-overview.md) a virtuális gépek logikai csoportosítása, amely lehetővé teszi az Azure számára, hogy megtudja, hogyan épülnek fel az alkalmazás a redundancia és a rendelkezésre állás biztosítása érdekében. Javasoljuk, hogy legalább két virtuális gépet hozzon létre egy rendelkezésre állási csoporton belül, hogy egy magasan elérhető alkalmazást biztosítson, és kielégítse a [99,95%-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-t. Magának a rendelkezésre állási csoportnak nincs díja, csak a létrehozott virtuálisgép-példányért kell fizetnie.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines méretezési csoportok 

Az [Azure virtuálisgép-méretezési](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) csoportok lehetővé teszik elosztott terhelésű virtuális gépek csoportjának létrehozását és kezelését. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A méretezési csoportok magas rendelkezésre állást biztosítanak alkalmazásai számára, és lehetővé teszik számos virtuális gép központi felügyeletét, konfigurálását és frissítését. Azt javasoljuk, hogy legalább két virtuális gépet hozzon létre egy méretezési csoporton belül, hogy egy magasan elérhető alkalmazást biztosítson, és kielégítse a [99,95%-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-t. Magának a méretezési csoportnak nincs díja, csak az Ön által létrehozott virtuálisgép-példányért kell fizetnie.

A méretezési csoportokban lévő virtuális gépeket egyetlen rendelkezésre állási zónába vagy regionálisan is üzembe lehet helyezni. A rendelkezésre állási zónák központi telepítési beállításai eltérhetnek az előkészítési [mód](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context)alapján.

## <a name="load-balancer"></a>Terheléselosztóval
Egyesítse a [Azure Load Balancer](../load-balancer/load-balancer-overview.md) egy rendelkezésre állási zónával vagy rendelkezésre állási csoporttal a legtöbb alkalmazás rugalmasságának beszerzéséhez. Az Azure Load Balancer több virtuális gép között osztja el a forgalmat. A Standard csomagban elérhető virtuális gépek esetében az Azure Load Balancer a csomag részét képezi. Nem mindegyik virtuális gép csomagja tartalmazza az Azure Load Balancert. További információ a virtuális gépek terheléselosztásáról: **virtuális gépek** terheléselosztása [Linux](linux/tutorial-load-balancer.md) vagy [Windows rendszerre](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Redundancia az Azure Storage szolgáltatásban
Az Azure Storage mindig több példányban tárolja az adatait, így védve van a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardver meghibásodását, a hálózati vagy áramkimaradást, valamint a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a Storage-fiók a meghibásodások előtt is kielégítse a rendelkezésre állási és tartóssági célokat.

Annak eldöntéséhez, hogy melyik redundancia-lehetőség a legmegfelelőbb a forgatókönyvhöz, vegye figyelembe az alacsonyabb költségek és a magasabb rendelkezésre állás közötti kompromisszumokat. Az alábbi tényezők segítenek meghatározni, hogy melyik redundancia-beállítást kell választania:
- Hogyan replikálódnak az adatai az elsődleges régióban
- Azt jelzi, hogy a rendszer replikálja-e az adatait egy második régióba, amely földrajzilag távol van az elsődleges régiótól, hogy védelmet nyújtson a regionális katasztrófák ellen
- Azt jelzi, hogy az alkalmazásnak van-e olvasási hozzáférése a másodlagos régióban lévő replikált értékekhez, ha az elsődleges régió bármilyen okból elérhetetlenné válik

További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Szervezetként olyan üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiát kell bevezetni, amely a tervezett és nem tervezett leállások esetén gondoskodik az adatok biztonságáról, valamint az alkalmazások és a munkaterhelések online állapotáról.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) segít az üzletmenet folytonosságának biztosításában azáltal, hogy az üzleti alkalmazásokat és a munkaterheléseket a leállás során futtatja. A Site Recovery képes a fizikai és virtuális gépeken futó számítási feladatok replikálására egy elsődleges helyről egy másodlagos helyre. Ha az elsődleges helyen kimaradás történik, a rendszer átadja a feladatokat a másodlagos helynek, és az alkalmazások onnan lesznek elérhetők. Amint az elsődleges hely megint elérhetővé válik, visszatérhet hozzá.

A Site Recovery a következők replikációját képes kezelni:
- Azure virtuális gépek replikációja Azure-régiók között.
- Helyszíni virtuális gépek, Azure Stack virtuális gépek és fizikai kiszolgálók.

## <a name="next-steps"></a>Következő lépések
- [Virtuális gép létrehozása rendelkezésre állási zónában](/linux/create-cli-availability-zone.md)
- [Virtuális gép létrehozása rendelkezésre állási készletben](/linux/tutorial-availability.md)
- [Virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/quick-create-portal.md)