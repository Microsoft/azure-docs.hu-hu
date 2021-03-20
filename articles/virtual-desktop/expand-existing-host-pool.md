---
title: Meglévő címkészlet kibontása új munkamenet-gazdagépekkel – Azure
description: Meglévő címkészlet kibontása új munkamenet-gazdagépekkel a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929591"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Meglévő gazdagép kibontása új munkamenet-gazdagépekkel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

A gazdagépen belüli használat során előfordulhat, hogy a meglévő gazdagépet új munkamenet-gazdagépekkel kell bővíteni az új terhelés kezeléséhez.

Ebből a cikkből megtudhatja, hogyan bővítheti a meglévő gazdagépeket új munkamenet-gazdagépekkel.

## <a name="what-you-need-to-expand-the-host-pool"></a>Mire van szükség a gazdagép kibontásához

Mielőtt elkezdené, győződjön meg arról, hogy létrehozta a gazdagép-készletet és a munkamenet-gazda virtuális gépeket (VM) az alábbi módszerek egyikének használatával:

- [Azure Portal](./create-host-pools-azure-marketplace.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](./create-host-pools-powershell.md)

A következő információkra is szüksége lesz a gazdagép és a munkamenet-gazda virtuális gépek első létrehozásakor:

- VM-méret,-rendszerkép és-név előtagja
- A tartományhoz való csatlakozás rendszergazdai hitelesítő adatai
- Virtuális hálózat neve és alhálózatának neve

## <a name="add-virtual-machines-with-the-azure-portal"></a>Virtuális gépek hozzáadása a Azure Portal

A gazdagép-készlet bővítése virtuális gépek hozzáadásával:

1. Jelentkezzen be az Azure Portalra.

2. Keresse meg és válassza ki a **Windows virtuális asztal** elemet.

3. A képernyő bal oldali menüjében válassza a **gazdagép készletek** lehetőséget, majd válassza ki annak a gazdagépnek a nevét, amelyhez virtuális gépeket szeretne hozzáadni.

4. Válassza a **munkamenet-gazdagépek** lehetőséget a képernyő bal oldalán található menüből.

5. Válassza a **+ Hozzáadás** lehetőséget a gazdagép-készlet létrehozásának megkezdéséhez.

6. Hagyja figyelmen kívül az alapvető beállítások lapot, és válassza a **virtuális gép részletei** lapot. Itt megtekintheti és szerkesztheti a gazdagép-készlethez hozzáadni kívánt virtuális gép (VM) adatait.

7. Válassza ki azt az erőforráscsoportot, amelyben létre kívánja hozni a virtuális gépeket, majd válassza ki a régiót. Kiválaszthatja az Ön által használt aktuális régiót vagy egy új régiót.

8. Adja meg a gazdagép-készletbe felvenni kívánt munkamenet-gazdagépek számát a **virtuális gépek számával**. Ha például öt gazdagépen bővíti a gazdagép-készletet, írja be az **5** értéket.

    >[!NOTE]
    >Bár lehetséges a virtuális gépek lemezképének és előtagja szerkesztésének szerkesztése, nem javasoljuk a szerkesztést, ha olyan virtuális gépekkel rendelkezik, amelyeken különböző lemezképek vannak ugyanazon a gazdagépen. Csak akkor szerkessze a lemezképet és az előtagot, ha azt tervezi, hogy eltávolítja a régebbi lemezképekkel rendelkező virtuális gépeket az érintett gazdagép-készletből.

9. A **virtuális hálózat adatainál** válassza ki azt a virtuális hálózatot és alhálózatot, amelyhez csatlakoztatni kívánja a virtuális gépeket. Kiválaszthatja ugyanazt a virtuális hálózatot, amelyet jelenleg használ meglévő gépe, vagy válasszon egy másikat, amely jobban megfelel a 7. lépésben kiválasztott régiónak.

10. A **rendszergazdai fióknál** adja meg a kiválasztott virtuális hálózathoz társított Active Directory tartományi felhasználónevet és jelszót. A rendszer ezeket a hitelesítő adatokat használja a virtuális gépek virtuális hálózathoz való csatlakoztatásához.

      >[!NOTE]
      >Győződjön meg arról, hogy a rendszergazda neve megfelel az itt megadott információknak. Továbbá, hogy a fiókban nincs engedélyezve az MFA.

11. Válassza a **címke** fület, ha olyan címkékkel rendelkezik, amelyekhez a virtuális gépeket szeretné csoportosítani. Ellenkező esetben ugorja át ezt a lapot.

12. Válassza a **felülvizsgálat + létrehozás** lapot. Tekintse át a beállításokat, és ha mindent jól néz ki, válassza a **Létrehozás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy kibontotta a meglévő gazdagép-készletet, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, és tesztelheti őket egy felhasználói munkamenet részeként. Csatlakozhat egy munkamenethez a következő ügyfelek bármelyikével:

- [Kapcsolódás a Windows asztali ügyféllel](./connect-windows-7-10.md)
- [Kapcsolódás a webügyféllel](./connect-web.md)
- [Kapcsolódás az Android-ügyféllel](./connect-android.md)
- [Kapcsolódás a macOS-ügyfélhez](./connect-macos.md)
- [Kapcsolódás az iOS-ügyfélhez](./connect-ios.md)
