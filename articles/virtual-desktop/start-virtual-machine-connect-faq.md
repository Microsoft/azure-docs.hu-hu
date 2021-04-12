---
title: Windows rendszerű virtuális asztali Start VM-kapcsolat – GYIK – Azure
description: Gyakori kérdések és ajánlott eljárások a virtuális gép indítása a csatlakozási szolgáltatásban való használatához.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a35d0541358af2f5fac5e4c7486a1be93797922
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445584"
---
# <a name="start-vm-on-connect-faq-preview"></a>Virtuális gép elindítása a csatlakozási GYIK-ben (előzetes verzió)

> [!IMPORTANT]
> A virtuális gép elindítása a kapcsolaton szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a Windows rendszerű virtuális asztali készletekhez készült csatlakozási (előzetes) szolgáltatással kapcsolatos gyakran feltett kérdéseket ismerteti.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>A virtuális gépek automatikusan le lesznek-e foglalva, amikor egy felhasználó leállítja a használatot?

Nem. További házirendeket kell konfigurálnia a felhasználók munkamenetből való aláírásához és az Azure Automation-parancsfájlok futtatásához a virtuális gépek felszabadításához.

A felszabadítási házirend konfigurálása:

1. Kapcsolódjon távolról arra a virtuális gépre, amelyhez be szeretné állítani a szabályzatot.

2. Nyissa meg a **Csoportházirend-szerkesztőt**, majd lépjen a **helyi számítógép-házirend**  >  **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztal munkamenet-kiszolgáló**  >  **munkamenet-korlátja** elemre.

3. Keresse meg azt a szabályzatot, amely a **Leválasztott munkamenetek időkorlátját állítja be**, majd módosítsa az értékét **engedélyezve** értékre.

4. Miután engedélyezte a szabályzatot, válassza **a leválasztott munkamenet befejezése** lehetőséget.

>[!NOTE]
>Ügyeljen arra, hogy a "leválasztott munkamenet vége" házirend időkorlátját öt percnél nagyobb értékre állítsa be. Az alacsony időkorlát azt eredményezheti, hogy a felhasználói munkamenetek megmaradnak, ha a hálózat túl sokáig elveszti a kapcsolódást, ami elveszett munkát eredményezhet.

A felhasználók aláírása nem fogja felszabadítani a virtuális gépeket. A virtuális gépek felszabadításával kapcsolatos további információkért lásd: [virtuális gépek elindítása és leállítása a munkaidőn kívül](../automation/automation-solution-vm-management.md).

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>A felhasználók kikapcsolhatják a virtuális gépet az ügyfeleikkel?

Igen. A felhasználók a munkameneten belül a Start menü segítségével állíthatják le a virtuális gépet, ugyanúgy, mint a fizikai géppel. A virtuális gép leállítása azonban nem szabadítja fel a virtuális gépet. A virtuális gépek felszabadításával kapcsolatos további információkért lásd: [virtuális gépek elindítása és leállítása a munkaidőn kívül](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan konfigurálhatja a Start VM-et a kapcsolódásra, tekintse meg [a virtuális gép indítása a kapcsolódáskor (előzetes verzió)](start-virtual-machine-connect.md)

Ha a Windows rendszerű virtuális asztallal kapcsolatos általánosabb kérdésekre van szüksége, tekintse meg az általános [GYIK](faq.md)-t.
