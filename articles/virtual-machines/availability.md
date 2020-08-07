---
title: Rendelkezésre állási beállítások
description: További információ a virtuális gépek Azure-beli futtatásának rendelkezésre állási funkcióiról
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 18d7755bf6bf9d09a8da30cb5c2892af6ed90c7d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830648"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek rendelkezésreállási beállításai

Ez a cikk áttekintést nyújt az Azure Virtual Machines (VM) rendelkezésre állási funkcióiról.

## <a name="high-availability"></a>Magas rendelkezésre állás

A számítási feladatok általában különböző virtuális gépeken vannak elosztva, hogy magas átviteli sebességet, teljesítményt és redundanciát hozzanak létre abban az esetben, ha egy virtuális gépet egy frissítés vagy más esemény miatt érintenek. 

Az Azure számos lehetőséget biztosít a magas rendelkezésre állás eléréséhez. Először beszéljünk az alapszintű szerkezetekről. 

### <a name="availability-zones"></a>Rendelkezésre állási zónák

A [rendelkezésre állási zónák](../availability-zones/az-overview.md) kibővítik a vezérlés szintjét, hogy a virtuális gépeken elérhető alkalmazások és adatmennyiségek rendelkezésre álljanak. A rendelkezésre állási zónák egy Azure-régión belül fizikailag különálló zónák. Egy támogatott Azure-régióban három Availability Zones érhető el. 

Mindegyik rendelkezésre állási zóna különálló áramforrással, hálózattal és hűtéssel rendelkezik. Ha a megoldásait a zónákban lévő replikált virtuális gépek használatára kívánja használni, az alkalmazások és az adatok az adatközpont elvesztése miatt is védhetők. Ha egy zóna biztonsága sérül, a replikált alkalmazások és az adatszolgáltatások azonnal elérhetők lesznek egy másik zónában. 

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a Windows vagy [Linux](./linux/create-cli-availability-zone.md) [rendszerű](./windows/create-powershell-availability-zone.md) virtuális gépek rendelkezésre állási zónában való üzembe helyezéséről.


### <a name="fault-domains"></a>Tartalék tartományok

A tartalék tartomány a mögöttes hardverelemek logikus csoportja, ahol az áramforrás és a hálózati kapcsoló közös, a helyszíni adatközpontok állványaihoz hasonlóan. 

### <a name="update-domains"></a>Frissítési tartományok

A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. 

Ez lehetővé teszi, hogy az alkalmazás legalább egy példánya mindig fusson akkor is, ha az Azure platformon épp rendszeres karbantartás folyik. Előfordulhat, hogy az újrainduló frissítési tartományok sorrendje nem folytatható egymás után a karbantartás során, de egyszerre csak egy frissítési tartományt indít el a rendszer.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines méretezési csoportok 

Az Azure virtuálisgép-méretezési csoportok lehetővé teszik elosztott terhelésű virtuális gépek csoportjának létrehozását és kezelését. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A méretezési csoportok magas rendelkezésre állást biztosítanak alkalmazásai számára, és lehetővé teszik számos virtuális gép központi felügyeletét, konfigurálását és frissítését. Azt javasoljuk, hogy legalább két virtuális gépet hozzon létre egy méretezési csoporton belül, hogy egy magasan elérhető alkalmazást biztosítson, és kielégítse a [99,95%-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-t. Magának a méretezési csoportnak nincs díja, csak az Ön által létrehozott virtuálisgép-példányért kell fizetnie. Ha egyetlen virtuális gép használja az [Azure Premium SSD](./windows/disks-types.md#premium-ssd)-ket, az Azure SLA a nem tervezett karbantartási eseményekre vonatkozik. A méretezési csoportokban lévő virtuális gépek több frissítési tartományon és tartalék tartományon is üzembe helyezhetők, hogy maximalizálja a rendelkezésre állást és a rugalmasságot az adatközpont-kimaradások, valamint a tervezett vagy nem tervezett karbantartási események miatt. A méretezési csoportokban lévő virtuális gépeket egyetlen rendelkezésre állási zónába vagy regionálisan is üzembe lehet helyezni. A rendelkezésre állási zónák központi telepítési beállításai eltérhetnek az előkészítési mód alapján.

**Tartalék tartományok és frissítési tartományok**

A virtuálisgép-méretezési csoportok a tartalék tartományok és a frissítési tartományok igazításával egyszerűbbé teszik a magas rendelkezésre állás kialakítását. A méretezési csoporthoz csak a tartalék tartományok darabszámát kell megadnia. A méretezési csoportok számára elérhető tartalék tartományok száma régiónként eltérő lehet. Lásd: [virtuális gépek rendelkezésre állásának kezelése az Azure-ban](./windows/manage-availability.md).


## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A rendelkezésre állási csoport a virtuális gépek logikai csoportosítása az adatközponton belül, amely lehetővé teszi az Azure számára, hogy megtudja, hogyan épülnek fel az alkalmazás a redundancia és a rendelkezésre állás érdekében. Javasoljuk, hogy legalább két virtuális gépet hozzon létre egy rendelkezésre állási csoporton belül, hogy egy magasan elérhető alkalmazást biztosítson, és kielégítse a [99,95%-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-t. Magának a rendelkezésre állási csoportnak nincs díja, csak a létrehozott virtuálisgép-példányért kell fizetnie. Ha egyetlen virtuális gép használja az [Azure Premium SSD](./windows/disks-types.md#premium-ssd)-ket, az Azure SLA a nem tervezett karbantartási eseményekre vonatkozik.

A rendelkezésre állási csoportokban a virtuális gépek automatikusan el lesznek osztva ezen tartalék tartományok között. Ez a módszer korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy a tápellátás megszakadásának hatását.

Az [Azure Managed Disks](./windows/faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. 

Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet. Ezekről a felügyelt lemezes tartalék tartományokról a [Linux rendszerű virtuális gépek](./linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) vagy a [Windows rendszerű virtuális gépek](./windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)esetében olvashat bővebben.

![Felügyelt rendelkezésre állási csoport](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


A rendelkezésre állási csoporton belüli virtuális gépeket is automatikusan elosztja a frissítési tartományok között. 

![Rendelkezésre állási csoportok](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Következő lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](/azure/architecture/checklist/resiliency-per-service).