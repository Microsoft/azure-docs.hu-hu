---
title: Ismerje meg az Azure BareMetal-infrastruktúra feltételeit
description: Ismerje meg az Azure BareMetal-infrastruktúra feltételeit.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: b22a6cecb2647df3878cb8fd4ade93d9a7d963fd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770886"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>A BareMetal-infrastruktúra feltételeinek megismerése

Ebben a cikkben néhány fontos BareMetal-kifejezést ismertetünk.

- **Változat**: van egy eredeti Stamp változat, amelyet 3. változat (3. Rev.), a BareMetal-példányok pedig két különböző Stamp változatot. Az egyes bélyegzők az Azure-beli virtuális gépek gazdagépének architektúrájában és közelségében különböznek:
    - **4. változat** (Rev 4): egy újabb kialakítás, amely az Azure-beli virtuális gép (VM) gazdagépek közelségét teszi lehetővé, és csökkenti az Azure-beli és a BareMetal-példányok közötti késleltetést. 
    - **4,2** -es változat (Rev 4,2): a legújabb BareMetal-infrastruktúra a meglévő Rev 4 architektúra használatával. A Rev 4 Az Azure-beli virtuális gépek (VM) gazdagépei számára biztosít szorosabb közelséget. Az Azure-beli virtuális gépek és a BareMetal-példányok közötti hálózati késés jelentős javulása a Rev 4-bélyegekben vagy-sorokban van üzembe helyezve. A BareMetal-példányok a Azure Portalon keresztül érhetők el és kezelhetők.    

- **Stamp**: a Microsoft belső telepítési méretének meghatározása a BareMetal-példányok számára. A példányok üzembe helyezése előtt a számítási, hálózati és tárolási állványokból álló BareMetal-példányokat egy adatközpont-helyen kell telepíteni. Ilyen üzembe helyezés neve BareMetal-példány bélyegzője vagy 4,2-es verzió.

- **Bérlő**: a BareMetal-példányban üzembe helyezett ügyfelek elkülönítettek egy *bérlőhöz.* A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a BareMetal-példány Stamp szintjén. Egy ügyfél dönthet úgy is, hogy a központi telepítések különböző bérlők között vannak. Még akkor sem, ha a bérlők között nincs kommunikáció a BareMetal-példány Stamp szintjén.

## <a name="next-steps"></a>Következő lépések
További információ a [BareMetal-infrastruktúráról](concepts-baremetal-infrastructure-overview.md) , illetve a [BareMetal-példányok egységének azonosításáról és](connect-baremetal-infrastructure.md)használatáról. 