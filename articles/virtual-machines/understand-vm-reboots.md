---
title: A virtuális gépek újraindításainak ismertetése
description: A virtuális gépek újraindításainak ismertetése – karbantartás és leállás
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: af371a8f7da5ef32e95d4096b69c5d52ce3e3700
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510531"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>A virtuális gépek újraindításainak ismertetése – karbantartás és állásidő
Az Azure-beli virtuális gépeket három forgatókönyv befolyásolja: nem tervezett hardveres karbantartás, váratlan leállás és tervezett karbantartás.

## <a name="unplanned-hardware-maintenance-event"></a>Nem tervezett hardver-karbantartási esemény
A nem tervezett hardveres karbantartás akkor következik be, amikor az Azure platform azt jelzi, hogy a hardver vagy bármely, fizikai géphez társított platform-összetevő hamarosan sikertelen lesz. Amikor a platform előre jelez egy hibát, kiad egy nem tervezett hardverkarbantartási eseményt az adott hardveren futtatott virtuális gépekre gyakorolt hatás csökkentése érdekében. Az Azure [élő áttelepítés](./maintenance-and-updates.md) technológiával telepíti át a Virtual Machinest a hibás hardverről egy kifogástalan fizikai gépre. Az Élő áttelepítés a virtuális gépet megőrző művelet, amely csak minimális időre állítja le a virtuális gépet. A memória tartalmát, a megnyitott fájlokat és a hálózati kapcsolatokat mind megőrzi, azonban a teljesítmény az esemény előtt és/vagy után csökkenhet. Ha az Élő áttelepítés nem használható, a virtuális gépen váratlan állásidő következik be az alábbiak szerint.


## <a name="unexpected-downtime"></a>Váratlan leállás
Váratlan leállás esetén a virtuális gép hardvere vagy fizikai infrastruktúrája váratlanul leáll. Ebbe beletartozhatnak a helyi hálózati hibák, a helyi lemezek meghibásodása vagy más rack-szintű hibák. Ha a rendszer észleli, az Azure platform automatikusan áttelepíti (meggyógyítja) a virtuális gépet egy kifogástalan fizikai gépre ugyanabban az adatközpontban. A javítási folyamat során a virtuális gép állásideje következik be (újraindítás), valamint bizonyos esetekben elveszhet az ideiglenes meghajtó. A csatlakoztatott rendszer- és adatmeghajtók minden esetben megmaradnak.

A virtuális gépek a teljes adatközpontot vagy akár egy teljes régiót érintő meghibásodások vagy vészhelyzetek esetén nem valószínű esetben is előfordulhatnak leállás esetén. Ezekben az esetekben az Azure olyan védelmi lehetőségeket biztosít, mint a  [rendelkezésre állási zónák](../availability-zones/az-overview.md) és a [párosított régiók](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Tervezett karbantartási események
A tervezett karbantartási események a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések a virtuális gépeket futtató platforminfrastruktúra általános megbízhatóságának, teljesítményének és biztonságának növelése érdekében. A frissítések többsége a Virtual Machinesra vagy Cloud Services gyakorolt hatás nélkül történik (lásd: [karbantartás, amely nem igényel újraindítást](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Bár az Azure platform minden esetben VM-megőrző karbantartást igyekszik végrehajtani, egyes ritka esetekben a frissítések megkövetelik a virtuális gép újraindítását, hogy a szükséges frissítéseket telepíteni lehessen a mögöttes infrastruktúrára. Ilyen esetekben karbantartással és ismételt üzembe helyezéssel kiegészített Azure tervezett karbantartási eljárást hajthat végre, ha a megfelelő időtartományban kezdeményezi a virtuális gépek karbantartását. További információkért lásd: [Virtuális gépek tervezett karbantartása](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Állásidő csökkentése
Az ilyen események okozta állásidő hatásainak csökkentése érdekében javasoljuk, hogy kövesse az alábbi bevált gyakorlatokat a virtuális gépek magas rendelkezésre állásának biztosításához:

* Az adatközpont-hibák elleni védelem [Availability Zones](../availability-zones/az-overview.md) használata
* Több virtuális gép konfigurálása [rendelkezésre állási csoportba](availability-set-overview.md) a redundancia érdekében
* A Linux vagy ütemezett események [ütemezett eseményeinek](/linux/scheduled-events.md) használata a [Windows](/windows/scheduled-events.md) számára, hogy proaktív módon válaszoljanak a virtuális gépeket érintő eseményekre
* Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása
* [Terheléselosztó](../load-balancer/load-balancer-overview.md) kombinálása rendelkezésre állási zónákkal vagy készletekkel

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az Azure-beli rendelkezésre állási lehetőségekről, tekintse meg a [rendelkezésre állás áttekintését](availability.md).
