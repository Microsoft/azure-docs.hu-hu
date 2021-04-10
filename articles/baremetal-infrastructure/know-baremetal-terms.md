---
title: Ismerje meg az Azure BareMetal-infrastruktúra feltételeit
description: Ismerje meg az Azure BareMetal-infrastruktúra feltételeit.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 53a601cc4556198479d8ca5d7495942d4dc2762c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580137"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>A BareMetal-infrastruktúra feltételeinek megismerése

Ebben a cikkben a BareMetal-infrastruktúrával kapcsolatos fontos fogalmakat mutatjuk be.

- **Változat**: van egy eredeti Stamp változat, amely a 3. változat (3. Rev.) néven ismert, és két további Stamp változatot is a BareMetal-példányok esetében. Az egyes bélyegzők az Azure-beli virtuális gépek gazdagépének architektúrájában és közelségében különböznek:
    - **4. változat** (Rev 4): egy újabb kialakítás, amely az Azure-beli virtuális gép (VM) gazdagépekhez való közelségét teszi lehetővé, és csökkenti az Azure-beli virtuális gépek és a SAP HANA példányok közötti késést. 
    - **4,2** -es változat (Rev 4,2): a legújabb BareMetal-infrastruktúra a meglévő Rev 4 architektúra használatával. A Rev 4 Az Azure-beli virtuális gépek (VM) gazdagépei számára biztosít szorosabb közelséget. Az Azure-beli virtuális gépek és a BareMetal-példányok közötti hálózati késés jelentős javulása a Rev 4 Stamps vagy sorokban. A BareMetal-példányok a Azure Portalon keresztül érhetők el és kezelhetők.    

- **Stamp**: a Microsoft belső telepítési méretének meghatározása a BareMetal-példányok számára. A példányok üzembe helyezése előtt a számítási, hálózati és tárolási állványokból álló BareMetal-példányt egy adatközpont-helyen kell telepíteni. Egy ilyen üzembe helyezés neve BareMetal-példány bélyegzője.

- **Bérlő**: a BareMetal-példányokat telepítő ügyfél *bérlőként* lesz elkülönítve. A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a BareMetal-példány Stamp szintjén. Egy ügyfél dönthet úgy is, hogy a központi telepítések különböző bérlők között vannak. Még akkor sem, ha a bérlők között nincs kommunikáció a BareMetal-példány Stamp szintjén.

## <a name="next-steps"></a>Következő lépések
Most, hogy bevezette a BareMetal-infrastruktúra fontos terminológiáját, érdemes megismernie az alábbiakat:
- További információ a [BareMetal-infrastruktúráról](concepts-baremetal-infrastructure-overview.md).
- [BareMetal infrastruktúra-példányok összekapcsolása az Azure-ban](connect-baremetal-infrastructure.md).

