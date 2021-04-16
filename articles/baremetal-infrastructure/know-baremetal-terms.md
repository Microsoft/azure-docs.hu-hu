---
title: Az Azure BareMetal-infrastruktúra használatának fogalma
description: Az Azure BareMetal-infrastruktúra használatának fogalma.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536768"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Az BareMetal-infrastruktúra használati szerződésének

Ebben a cikkben az BareMetal-infrastruktúrával kapcsolatos néhány fontos kifejezéssel fogunk beszélni.

- **Változat:** 3. változat (Rev 3) néven ismert eredeti bélyeg-változat és két további bélyeg-változat az BareMetal-példánybélyegek számára. Az architektúrában és az Azure-beli virtuális gépek gazdagépeihez való közelségben minden bélyeg eltérő:
    - **4.** változat (4. változat): Egy újabb kialakítás, amely közelebb nyújt az Azure-beli virtuális gépek gazdagépeihez, és csökkenti az Azure-beli virtuális gépek és a virtuálisgép-példányok közötti SAP HANA késést. 
    - **4.2-es** változat (4.2-es változat): A legújabb, a meglévő Rev 4 architektúrát használó BareMetal-infrastruktúra. A 4. rev. közelebbi közelséget biztosít az Azure-beli virtuálisgép-gazdagépek számára. Jelentős mértékben javult a hálózati késés az Azure-beli virtuális gépek és a 4. javítású bélyegek vagy sorokban üzembe helyezett BareMetal-példányok között. Az BareMetal-példányokat a következő Azure Portal.    

- **Bélyeg:** Az BareMetal-példányok Microsoft belső telepítési méretét határozza meg. A példányok üzembe helyezése előtt üzembe kell helyezni egy számítási, hálózati és tárolóállványokból álló BareMetal-példánybélyeget egy adatközpont-helyen. Az ilyen üzembe helyezést BareMetal-példánybélyegnek nevezzük.

- **Bérlő:** Az BareMetal-példánybélyeget üzembe helyező ügyfél bérlőként el lesz *különítve.* A bérlők a hálózati, tárolási és számítási rétegben elkülönülnek a többi bérlőtől. A különböző bérlőkhöz rendelt tárolási és számítási egységek nem láthatják egymást, és nem kommunikálhatnak egymással az BareMetal-példány bélyegszinten. Az ügyfelek dönthetnek úgy, hogy különböző bérlőkben üzemelő példányokat hoznak be. A bérlők között ekkor sincs kommunikáció az BareMetal-példány bélyegszintén.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az BareMetal-infrastruktúra fontos terminológiáját, a következővel ismerkedhet meg:
- További részletek az [BareMetal infrastruktúráról.](concepts-baremetal-infrastructure-overview.md)
- [BareMetal Infrastructure-példányok csatlakoztatása az Azure-ban.](connect-baremetal-infrastructure.md)

