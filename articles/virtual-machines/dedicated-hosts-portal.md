---
title: Az Azure dedikált gazdagépek üzembe helyezése a Azure Portal használatával
description: A virtuális gépeket és a méretezési csoportokat a Azure Portal használatával helyezheti üzembe a dedikált gazdagépeken.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 3bc7909f38e63256d7d708ec189c628662cf8837
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667336"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Virtuális gépek és méretezési csoportok üzembe helyezése dedikált gazdagépeken a portál használatával 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy dedikált Azure- [gazdagépet](dedicated-hosts.md) a virtuális gépek (VM-EK) üzemeltetéséhez. 


## <a name="limitations"></a>Korlátozások

- A dedikált gazdagépek számára elérhető méretek és hardver típusok régiónként eltérőek. További információért tekintse meg a gazdagép [díjszabását ismertető oldalt](https://aka.ms/ADHPricing) .

## <a name="create-a-host-group"></a>Gazda csoport létrehozása

A **gazda-csoport** egy olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket. A magas rendelkezésre állás tervezése során további lehetőségek is rendelkezésre állnak. A dedikált gazdagépekhez a következő lehetőségek közül választhat: 
- Több rendelkezésre állási zónára kiterjedő span. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagép-csoporttal.
- Több tartalék tartomány között, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagép-csoport tartalék tartományának darabszámát. Ha nem szeretné a csoportban lévő tartalék tartományokat kivonni, használja az 1. számú tartalék tartományt. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. 

Ebben a példában egy 1 rendelkezésre állási zónát és 2 tartalék tartományt használó gazdagépet hozunk létre. 


1. Nyissa meg az Azure [Portalt](https://portal.azure.com). 
1. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Keresse meg a **gazdagépet** , majd válassza az eredmények közül a **gazdagép csoportok** lehetőséget.
1. A **gazdagép-csoportok** lapon válassza a **Létrehozás** lehetőséget.
1. Válassza ki a használni kívánt előfizetést, majd válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.
1. A név mezőbe írja be a *MyDedicatedHostsRG* **nevet** , majd kattintson az **OK gombra**.
1. A **gazdagép-csoport neve** mezőbe írja be a következőt: *myHostGroup*.
1. A hely mezőben válassza az **USA keleti** **régiója** lehetőséget.
1. A **rendelkezésre állási zóna** beállításnál válassza az **1** lehetőséget.
1. A tartalék **tartományok száma** beállításnál válassza a **2** lehetőséget.
1. Válassza az **automatikus elhelyezés** lehetőséget, hogy a rendszer automatikusan rendeljen virtuális gépeket és méretezési csoport példányokat egy elérhető gazdagéphez ebben a csoportban.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd várja meg az érvényesítést.
1. Ha megjelenik az **átadott érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget a gazda csoport létrehozásához.

Csak néhány percet vehet igénybe a gazda csoport létrehozásakor.


## <a name="create-a-dedicated-host"></a>Dedikált gazdagép létrehozása

Most hozzon létre egy dedikált gazdagépet a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a gazdagéphez tartozó SKU-t is. A gazdagép SKU rögzíti a támogatott virtuálisgép-sorozatot, valamint a dedikált gazdagép hardveres generációját.

A gazdagép SKU-ról és a díjszabásról további információt az [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing)című témakörben talál.

Ha a gazdagéphez a tartalék tartományokat állítja be, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát.  

1. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Keressen rá a **dedikált gazdagépre** , majd válassza ki a **dedikált gazdagépeket** az eredmények közül.
1. A **dedikált gazdagépek** lapon válassza a **Létrehozás** lehetőséget.
1. Válassza ki a használni kívánt előfizetést.
1. Válassza az *myDedicatedHostsRG* elemet az **erőforráscsoport** elemnél.
1. A **példány részletei** mezőbe írja  be a myHost **nevet** , és válassza az *USA keleti* régiója lehetőséget a helyhez.
1. A **hardverprofil** területen válassza a *standard Es3 Family-Type 1* elemet a **család méretéhez**, válassza a *MyHostGroup* lehetőséget **, majd válassza** az *1* értéket a tartalék **tartomány** számára. Hagyja meg az alapértelmezett értékeket a többi mezőnél.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** lehetőséget, és várja meg az érvényesítést.
1. Ha megjelenik az **átadott érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget a gazdagép létrehozásához.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
1. Az Azure Marketplace-erőforrások listája felett található keresőmezőbe keresse meg és válassza ki a használni kívánt rendszerképet, majd válassza a **Létrehozás** lehetőséget.
1. Az **alapvető beállítások** lap **projekt részletei** területén ellenőrizze, **hogy a megfelelő** előfizetés van-e kiválasztva, majd válassza a *myDedicatedHostsRG* lehetőséget. 
1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a **Régió** menüjéből válassza ki az *USA keleti régiója* lehetőséget.
1. A **rendelkezésre állási beállítások** területen válassza a **rendelkezésre állási zóna** elemet, majd a legördülő listából válassza az *1* lehetőséget.
1. A méret beállításnál válassza a **méret módosítása** lehetőséget. Az elérhető méretek listájában válasszon egyet a Esv3 sorozatból, például a **standard E2s v3**-as verzióban. Előfordulhat, hogy törölnie kell a szűrőt, hogy az összes rendelkezésre álló méretet meg lehessen tekinteni.
1. Szükség szerint hajtsa végre a többi mezőt az **alapvető beállítások** lapon.
1. Ha meg szeretné határozni, hogy melyik gazdagépet szeretné használni a virtuális géphez, akkor a lap tetején válassza a **speciális** fület, és a **gazdagép** szakaszban válassza a *myHostGroup* lehetőséget **a gazdagéphez és a** myHost **.**  Ellenkező esetben a virtuális gép automatikusan egy kapacitású gazdagépre lesz helyezve.
    ![A gazda csoport és a gazdagép kiválasztása](./media/dedicated-hosts-portal/advanced.png)
1. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.
1. Amikor megjelenik az érvényesítés által átadott üzenet, válassza a **Létrehozás** lehetőséget.

A virtuális gép üzembe helyezése eltarthat néhány percig.

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása 

Méretezési csoport telepítésekor meg kell adnia a gazdagépet.

1. Keresse meg a *méretezési* csoportot, és válassza a listából a **virtuálisgép-méretezési** csoportok lehetőséget.
1. Új méretezési csoport létrehozásához válassza a **Hozzáadás** lehetőséget.
1. A szokásos módon végezze el a mezőket az **alapvető beállítások** lapon, de győződjön meg arról, hogy olyan virtuálisgép-méretet választ ki, amely a dedikált gazdagéphez (például a **standard E2s v3**) kiválasztott sorozatból származik.
1. A **speciális** lapon a **terjesztési algoritmus** kiválasztásával válassza a **maximális terjesztés** lehetőséget.
1. A **gazdagép csoportban** válassza ki a gazdagép csoportot a legördülő listából. Ha nemrég hozta létre a csoportot, egy percet is igénybe vehet, hogy bekerüljön a listára.

## <a name="add-an-existing-vm"></a>Meglévő virtuális gép hozzáadása 

A kilépő virtuális gépet egy dedikált gazdagéphez is hozzáadhatja, de a virtuális gépnek először Stop\Deallocated. kell lennie. Mielőtt áthelyezi a virtuális gépet egy dedikált gazdagépre, győződjön meg arról, hogy a virtuális gép konfigurációja támogatott:

- A virtuális gép méretének azonos méretűnek kell lennie, mint a dedikált gazdagépnek. Ha például a dedikált gazdagép DSv3, akkor a virtuális gép mérete Standard_D4s_v3, de nem lehet Standard_A4_v2. 
- A virtuális gépnek ugyanabban a régióban kell lennie, ahol a dedikált gazdagép található.
- A virtuális gép nem lehet a közelségi elhelyezési csoport része. A dedikált gazdagépre való áthelyezés előtt távolítsa el a virtuális gépet a közelségi csoportból. További információ: [virtuális gép áthelyezése a közelségi csoportból](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- A virtuális gép nem lehet rendelkezésre állási készletben.
- Ha a virtuális gép egy rendelkezésre állási zónában van, akkor a gazdagép-csoporttal megegyező rendelkezésre állási zónának kell lennie. A virtuális gép rendelkezésre állási zónájának beállításai és a gazdagép csoportjának egyeznie kell.

Helyezze át a virtuális gépet egy dedikált gazdagépre a [portál](https://portal.azure.com)használatával.

1. Nyissa meg a virtuális gép lapját.
1. A virtuális gép stop\deallocate válassza a **Leállítás** lehetőséget.
1. A bal oldali menüben válassza a **konfiguráció** lehetőséget.
1. Válasszon ki egy gazdagépet és egy gazdagépet a legördülő menükből.
1. Ha elkészült, válassza a **Mentés** lehetőséget az oldal tetején.
1. Miután hozzáadta a virtuális gépet a gazdagéphez, válassza az **Áttekintés** lehetőséget a bal oldali menüben.
1. A lap tetején kattintson a **Start** gombra a virtuális gép újraindításához.

## <a name="next-steps"></a>Következő lépések

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A dedikált gazdagépeket az [Azure CLI](./linux/dedicated-hosts-cli.md) vagy a [PowerShell](./windows/dedicated-hosts-powershell.md)használatával is üzembe helyezheti.
