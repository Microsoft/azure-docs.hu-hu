---
title: Tervezési szempontok az Azure Virtual Machine Scale Sets
description: Ismerje meg az Azure-Virtual Machine Scale Sets kialakításával kapcsolatos szempontokat. A méretezési készletek funkcióinak összehasonlítása virtuálisgép-funkciókkal.
keywords: linuxos virtuális gép, virtuálisgép-méretezési csoportok
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 99dc7a2350631f662e1c993908f7ef56e4f9a194
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88648564"
---
# <a name="design-considerations-for-scale-sets"></a>Tervezési szempontok a méretezési csoportokhoz
Ez a cikk a Virtual Machine Scale Sets kialakításával kapcsolatos szempontokat ismerteti. A Virtual Machine Scale Setsával kapcsolatos információkért tekintse meg a [Virtual Machine Scale sets áttekintését](./overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Mikor kell használni a méretezési csoportokat a virtuális gépek helyett?
A méretezési csoportok általában hasznosak olyan, magasan elérhető infrastruktúra üzembe helyezéséhez, ahol a különböző gépekhez hasonló konfiguráció tartozik. Bizonyos funkciók azonban csak a méretezési csoportokban érhetők el, míg más szolgáltatások csak a virtuális gépekben érhetők el. Ha tájékozott döntést szeretne hozni arról, hogy mikor érdemes használni az egyes technológiákat, tekintse át a méretezési csoportokban, de nem virtuális gépeken elérhető általánosan használt funkciókat.

### <a name="scale-set-specific-features"></a>Méretezési csoportra jellemző funkciók

- Miután megadta a méretezési csoport konfigurációját, frissítheti a *Capacity* tulajdonságot, hogy párhuzamosan helyezzen üzembe több virtuális gépet. Ez a folyamat jobb, mint egy parancsfájl írása, amely összehangolja az egyes virtuális gépek párhuzamos üzembe helyezését.
- Az [Azure automatikus méretezés használatával automatikusan méretezheti a méretezési csoportokat,](./virtual-machine-scale-sets-autoscale-overview.md) de nem lehet egyéni virtuális gépeket használni.
- Alaphelyzetbe állíthatja a [méretezési csoport virtuális gépeket](/rest/api/compute/virtualmachinescalesets/reimage) , de [nem az egyes virtuális gépeket](/rest/api/compute/virtualmachines).
- A [méretezési](#overprovisioning) csoport virtuális gépei a nagyobb megbízhatóság és a gyorsabb üzembe helyezési idő növelésére is felhasználhatók. Nem lehet túlépíteni az egyes virtuális gépeket, hacsak nem ír egyéni kódot a művelet végrehajtásához.
- Megadhat egy [frissítési szabályzatot](./virtual-machine-scale-sets-upgrade-scale-set.md) , amely megkönnyíti a méretezési csoporton belüli virtuális gépek frissítéseinek kiváltását. Az egyes virtuális gépek esetében saját maga is el kell dolgoznia a frissítéseket.

### <a name="vm-specific-features"></a>VM-specifikus funkciók

Néhány funkció jelenleg csak virtuális gépeken érhető el:

- Rögzíthet egy képet egy különálló virtuális gépről, de nem a méretezési csoportba tartozó virtuális gépekről.
- Áttelepítheti az egyes virtuális gépeket a natív lemezekről a felügyelt lemezekre, de a méretezési csoportokban nem telepíthet át virtuálisgép-példányokat.
- IPv6 nyilvános IP-címeket rendelhet az egyes VM virtuális hálózati adapterekhez (NIC), de a méretezési csoportokban lévő virtuálisgép-példányokhoz nem. Az IPv6-alapú nyilvános IP-címeket az egyes virtuális gépek vagy a méretezési csoport virtuális gépei előtt is hozzárendelheti.

## <a name="storage"></a>Tárolás

### <a name="scale-sets-with-azure-managed-disks"></a>Méretezési készletek az Azure Managed Disks
A méretezési csoportokat a hagyományos Azure Storage-fiókok helyett [Azure-Managed Disks](../virtual-machines/managed-disks-overview.md) lehet létrehozni. Managed Disks a következő előnyöket biztosítja:
- Nem kell előzetesen létrehoznia Azure Storage-fiókok készletét a méretezési csoport virtuális gépei számára.
- A méretezési csoportba tartozó virtuális gépekhez [csatlakoztatott adatlemezeket](virtual-machine-scale-sets-attached-disks.md) is meghatározhat.
- A méretezési csoportok beállítható úgy, hogy [legfeljebb 1 000 virtuális gépet támogasson egy készletben](virtual-machine-scale-sets-placement-groups.md). 

Ha rendelkezik meglévő sablonnal, [a sablont Managed Disks használatára](virtual-machine-scale-sets-convert-template-to-md.md)is módosíthatja.

### <a name="user-managed-storage"></a>Felhasználó által felügyelt tároló
Az Azure Managed Disks nem definiált méretezési csoport a felhasználó által létrehozott Storage-fiókokra támaszkodik a készletben lévő virtuális gépek operációsrendszer-lemezének tárolására. A maximális i/o-értékhez legfeljebb 20 virtuális gép arányát ajánlott használni, és a túlzott _kiépítés_ előnyeit is kihasználhatja (lásd alább). Azt is javasoljuk, hogy a Storage-fiókok nevének kezdő karaktereit az ábécében keresztül terjessze. Ez segít a terhelés elosztásában a különböző belső rendszereken. 


## <a name="overprovisioning"></a>Csúcsigények túlméretezéssel
A méretezési csoportok jelenleg alapértelmezés szerint "túlzottan kiépíthető" virtuális gépek. Ha a túlépítés be van kapcsolva, a méretezési csoport ténylegesen több virtuális gépet indít el, mint amennyit kért, majd törli a további virtuális gépeket, ha a kért számú virtuális gép sikeresen kiépítve. A túlzott kiépítés javítja a sikeres üzembe helyezési arányt, és csökkenti a telepítési időt. A további virtuális gépekért nem számolunk fel díjat, és nem számítanak bele a kvóta korlátaiba.

Míg a túlzott kiépítés javítja a sikeres kiépítést, az olyan alkalmazások zavaros viselkedését okozhatja, amelyek nem a további virtuális gépek kezelésére lettek kialakítva, és a rendszer eltűnnek. A túlterhelés kikapcsolásához ellenőrizze, hogy rendelkezik-e a következő karakterlánccal a sablonban: `"overprovision": "false"` . További részleteket a [méretezési csoport REST API dokumentációjában](/rest/api/virtualmachinescalesets/create-or-update-a-set)talál.

Ha a méretezési csoport felhasználó által felügyelt tárolót használ, és kikapcsolja a túlzott kiépítést, a Storage-fiókban több mint 20 virtuális gépet is használhat, de az IO-teljesítmény miatt nem ajánlott a 40-nál magasabban haladni. 

## <a name="limits"></a>Korlátok
A Piactéri rendszerképekre (más néven platform-rendszerképekre) épülő méretezési csoport, amely az Azure Managed Disks használatára van konfigurálva, legfeljebb 1 000 virtuális gép kapacitását támogatja. Ha úgy konfigurálja a méretezési csoportját, hogy több mint 100 virtuális gépet támogasson, nem minden forgatókönyv működik ugyanaz (például terheléselosztás). További információ: [a nagyméretű virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). 

A felhasználó által felügyelt Storage-fiókokkal konfigurált méretezési csoport jelenleg 100 virtuális gépre korlátozódik (és 5 Storage-fiók ajánlott ehhez a skálához).

Az egyéni rendszerképekre épülő méretezési csoport (amelyet Ön hozott létre) az Azure Managed Disks szolgáltatással akár 600 virtuális gép kapacitása is felhasználható. Ha a méretezési csoport felhasználó által felügyelt Storage-fiókokkal van konfigurálva, akkor az összes operációsrendszer-lemez virtuális merevlemezét egy Storage-fiókon belül kell létrehoznia. Ennek eredményeképpen a méretezési csoportokban az egyéni rendszerképekre és a felhasználó által felügyelt tárterületre épülő virtuális gépek maximálisan ajánlott száma 20. Ha kikapcsolja a túlzott kiépítést, akár 40-ig is megtekintheti.

Ennél a korlátnál több virtuális gép esetén több méretezési csoportot kell telepíteni a [sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)látható módon.
