---
title: Az Azure BareMetal-infrastruktúra használati szerződésének
description: Az Azure BareMetal-infrastruktúra használatának fogalma.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725457"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Az BareMetal-infrastruktúra használati szerződésének

Ebben a cikkben az BareMetal-infrastruktúrával kapcsolatos néhány fontos kifejezéssel fogunk beszélni.

- **Változat:** Az BareMetal Infrastructure- (HANA nagyméretű példányok) bélyegek két különböző változatban vannak meg. Ezek az architektúra és az Azure-beli virtuálisgép-gazdagépek közelében különböznek:
    - "Revision 3" (Rev 3): The original design deployed mid-2016.
    - "4.2-es változat" (Rev 4.2): Új kialakítás, amely közelebb nyújt az Azure-beli virtuális gépek gazdagépeihez, rendkívül alacsony hálózati késéssel az Azure-beli virtuális gépek és a HANA nagyméretű példányai között. A Azure Portal az "BareMetal Infrastructure" (Operációs rendszer nélküli infrastruktúra) néven hivatkozunk az erőforrásokra, és az ügyfelek bareMetal-példányként férhetnek hozzá az Azure Portal.

- **Bélyeg:** Az BareMetal-példányok Microsoft belső telepítési méretét határozza meg. A példányok üzembe helyezése előtt üzembe kell helyezni egy számítási, hálózati és tárolóállványokból álló BareMetal-példánybélyeget egy adatközpont-helyen. Az ilyen üzembe helyezést BareMetal-példánybélyegnek nevezzük.

- **Bérlő:** Az BareMetal-példánybélyeget üzembe helyező ügyfél bérlőként el lesz *különítve.* A bérlők a hálózati, tárolási és számítási rétegben elkülönülnek a többi bérlőtől. A különböző bérlőkhöz rendelt tárolási és számítási egységek nem láthatják egymást, és nem kommunikálhatnak egymással az BareMetal-példány bélyegszinten. Az ügyfelek dönthetnek úgy, hogy különböző bérlőkben üzemelő példányokat hoznak üzembe. A bérlők között ekkor sincs kommunikáció az BareMetal-példánybélyeg szintjén.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az BareMetal-infrastruktúra fontos terminológiáját, a következővel ismerkedhet meg:
- További részletek az [BareMetal-infrastruktúráról.](concepts-baremetal-infrastructure-overview.md)
- [BareMetal Infrastructure-példányok csatlakoztatása az Azure-ban.](connect-baremetal-infrastructure.md)

