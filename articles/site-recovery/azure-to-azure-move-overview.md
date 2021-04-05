---
title: Azure-beli virtuális gépek áthelyezése másik régióba Azure Site Recovery
description: A Azure Site Recovery használatával Azure-beli virtuális gépeket helyezhet át egyik Azure-régióból a másikba.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90007366"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Azure-beli virtuális gépek áthelyezése másik Azure-régióba

Ez a cikk áttekintést nyújt az Azure-beli virtuális gépek egy másik Azure-régióba való áthelyezésének okairól és lépéseiről [Azure site Recovery](site-recovery-overview.md)használatával. 


## <a name="reasons-to-move-azure-vms"></a>Az Azure-beli virtuális gépek áthelyezésének okai

A virtuális gépeket a következő okokból lehet áthelyezni:

- Egy régióban már üzembe helyezte a szolgáltatást, és egy új régió támogatása lett hozzáadva, amely közelebb áll az alkalmazás vagy szolgáltatás végfelhasználói számára. Ebben az esetben érdemes áthelyeznie a virtuális gépeket az új régióba, hogy csökkentse a késést. Használja ugyanazt a megközelítést, ha szeretné összevonni az előfizetéseket, vagy ha vannak olyan irányítási vagy szervezeti szabályok, amelyek szükségesek az áthelyezéshez.
- A virtuális gép egy egypéldányos virtuális gép vagy egy rendelkezésre állási csoport részeként lett üzembe helyezve. Ha szeretné bővíteni a rendelkezésre állási SLA-kat, áthelyezheti a virtuális gépeket egy rendelkezésre állási zónába.

## <a name="move-vms-with-resource-mover"></a>Virtuális gépek áthelyezése az erőforrás-mozgatóval

Mostantól áthelyezheti a virtuális gépeket egy másik régióba az [Azure-erőforrás-mozgató](../resource-mover/tutorial-move-region-virtual-machines.md)használatával. Az erőforrás-mozgató nyilvános előzetes verzióban érhető el, és a következőket biztosítja:
- Egyetlen központ az erőforrások régiók közötti áthelyezéséhez.
- Csökkentheti a mozgatási időt és a bonyolultságot. Minden, amire szüksége van, egyetlen helyen kell lennie.
- Egyszerű és egységes felület a különböző típusú Azure-erőforrások áthelyezéséhez.
- Egyszerű módszer az áthelyezni kívánt erőforrások függőségeinek azonosítására. Ezzel a művelettel a kapcsolódó erőforrásokat együtt helyezheti át, így minden a várt módon működik a megcélzott régióban, az áthelyezést követően.
- A forrás régió erőforrásainak automatikus törlése, ha az áthelyezés után törölni szeretné őket.
- Tesztelés: Kipróbálhatja a mozgatást, majd elvetheti, ha nem szeretne teljes áthelyezést végezni.



## <a name="move-vms-with-site-recovery"></a>Virtuális gépek áthelyezése Site Recovery

Site Recovery a virtuális gépek áthelyezése a következő lépésekkel történhet:

1. Ellenőrizze az előfeltételeket.
2. Készítse elő a forrás virtuális gépeket.
3. Készítse elő a célként megadott régiót.
4. Adatmásolás a célként megadott régióba. Azure Site Recovery replikációs technológiával másolhatja át az adatait a forrás virtuális gépről a célként megadott régióba.
5. Tesztelje a konfigurációt. A replikáció befejezését követően tesztelje a konfigurációt egy nem éles hálózatra irányuló feladatátvételi teszt végrehajtásával.
6. Hajtsa végre az áthelyezést.
7. A forrás régió erőforrásainak elvetése.

> [!NOTE]
> Ezekről a lépésekről a következő részben olvashat bővebben.
> [!IMPORTANT]
> Jelenleg Azure Site Recovery támogatja a virtuális gépek áthelyezését az egyik régióból a másikba, de nem támogatja az áthelyezést a régión belül.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>A többrétegű üzembe helyezések jellemző architektúrái

Ez a szakasz az Azure-beli többrétegű alkalmazások leggyakoribb telepítési architektúráit ismerteti. A példa egy háromrétegű, nyilvános IP-címmel rendelkező alkalmazás. A rétegek (web, alkalmazás és adatbázis) mindegyike két virtuális géppel rendelkezik, és egy Azure Load Balancer csatlakozik a többi réteghez. Az adatbázis szintje SQL Server mindig a virtuális gépek közötti replikációt a magas rendelkezésre állás érdekében.

* **Különböző rétegekben üzembe helyezett egypéldányos virtuális gépek**: a rétegben lévő minden virtuális gép egypéldányos virtuális gépként van konfigurálva, és a terheléselosztó a többi réteghez csatlakozik. Ez a konfiguráció a legegyszerűbben alkalmazható.

     ![Az Egypéldányos virtuálisgép-telepítés rétegek közötti áthelyezésének kiválasztása](media/move-vm-overview/regular-deployment.png)

* **Virtuális gépek a rendelkezésre állási csoportokban üzembe helyezett egyes szinteken**: egy adott szinten lévő minden virtuális gép egy rendelkezésre állási csoportban van konfigurálva. A [rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md) csoportok biztosítják, hogy az Azure-ban üzembe helyezett virtuális gépek a fürt több elkülönített hardveres csomópontján keresztül legyenek elosztva. Ez biztosítja, hogy ha az Azure-ban hardveres vagy szoftveres hiba történik, a rendszer csak a virtuális gépek egy részhalmazát érinti, és a teljes megoldás továbbra is elérhető és működőképes marad.

     ![VIRTUÁLIS gépek üzembe helyezése rendelkezésre állási csoportokon keresztül](media/move-vm-overview/avset.png)

* **Az egyes szinteken üzembe helyezett virtuális gépek Availability Zones**: az egyes szinteken lévő összes virtuális gép [Availability Zones](../availability-zones/az-overview.md)között van konfigurálva. Az Azure-régiók rendelkezésre állási zónái egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha például három vagy több virtuális gépet hoz létre három zónában egy Azure-régióban, a virtuális gépeket a rendszer gyakorlatilag három tartalék tartományba és három frissítési tartományba terjeszti. Az Azure platform felismeri ezt az eloszlást a frissítési tartományok között, hogy megbizonyosodjon róla, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyidőben.

     ![Rendelkezésre állási zónák üzembe helyezése](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Virtuális gépek áthelyezése célként megadott régióba

A korábban említett [architektúrák](#typical-architectures-for-a-multi-tier-deployment) alapján az üzemelő példányok a cél régióba való áthelyezést követően jelennek meg.

* **Különböző szinteken üzembe helyezett egypéldányos virtuális gépek**
* **A rendelkezésre állási csoportokon belül üzembe helyezett virtuális gépek**
* **Az egyes szinteken üzembe helyezett virtuális gépek Availability Zones**

## <a name="move-vms-to-increase-availability"></a>Virtuális gépek áthelyezése a rendelkezésre állás növeléséhez

* **Különböző szinteken üzembe helyezett egypéldányos virtuális gépek**

     ![Egypéldányos virtuális gép üzembe helyezése rétegek között](media/move-vm-overview/single-zone.png)

* **Virtuális gépek a rendelkezésre állási csoportokban üzembe helyezett egyes szinteken**: a virtuális gépeket egy rendelkezésre állási csoportban állíthatja be külön Availability Zones, ha Azure site Recovery használatával engedélyezi a virtuális gép replikálását. A rendelkezésre állásra vonatkozó SLA az áthelyezési művelet elvégzése után 99,99% lesz.

     ![VIRTUÁLIS gépek üzembe helyezése rendelkezésre állási csoportokban és Availability Zones](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> 
> * [Azure-beli virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)
> 
> * [Azure-beli virtuális gépek áthelyezése Availability Zonesba](move-azure-vms-avset-azone.md)

