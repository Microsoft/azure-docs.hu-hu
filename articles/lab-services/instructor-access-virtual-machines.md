---
title: A Azure Lab Services virtuális gépekhez való hozzáférés oktatója
description: Ez a cikk bemutatja, hogyan férhetnek hozzá az oktatók a tanulói virtuális gépekhez a pedagógus nézetből. Egy tanítási asszisztens például lehet egy oktató az egyik osztályhoz, de egy tanuló más osztályokhoz.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad4f9cfd11b372e5c6da5c17eaeba82b0cd8a91f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445100"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Virtuális gépek elérése tanulóként a pedagógus nézetből
Ez a cikk bemutatja, hogyan érhetik el a pedagógusok a virtuális gépeket tanulóként részt vevő osztályokhoz. 

Itt talál egy olyan forgatókönyvet, amelyben ez a funkció segít. A tanítási asszisztens egy oktató, de más osztályok tanulói. Az oktatási asszisztens pedig a tanuló virtuális gépeket a pedagógus nézetből szeretné megtekinteni és elérni, amely a saját laborokat mutatja. 

## <a name="access-vms-from-educator-view"></a>Virtuális gépek elérése a pedagógus nézetből

1. Jelentkezzen be a [Azure Lab Services webhelyére](https://labs.azure.com). Láthatja a saját laboratóriumait. Ezek a laborok olyan laborok, amelyeket Ön hozott létre saját maga vagy a rendszergazda által a tulajdonosként hozzárendelt laborokban. További információ: [további tulajdonosok hozzáadása meglévő laborhoz](how-to-add-user-lab-owner.md)
2. Ha tanulóként részt vevő osztályokba kíván hozzáférni a virtuális gépekhez, válassza a számítógép ikont a jobb felső sarokban. Győződjön meg arról, hogy az Ön által tanulóként elérhető virtuális gépek jelennek meg. A következő példában a felhasználó a Python Lab tanítási asszisztense, de a Java Lab tanulója. Így a felhasználó a Java laborból látja a virtuális gépet a legördülő listából. A felhasználó elindíthatja a virtuális gépet, és csatlakozhat hozzá. 
    
    ![Tanuló virtuális gépek elérése](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Csatlakozás virtuális géphez](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Kapcsolódás virtuális géphez RDP használatával Mac gépen](connect-virtual-machine-mac-remote-desktop.md)
- [Kapcsolódás virtuális géphez RDP használatával egy Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)
- [A távoli asztal használata Linux rendszerű virtuális gépekhez](how-to-use-remote-desktop-linux-student.md)
