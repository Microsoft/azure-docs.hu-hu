---
title: Azure-beli virtuális gépek áthelyezése a régiók között az Azure-erőforrás-mozgatóval
description: Ismerje meg, hogyan helyezhet át Azure-beli virtuális gépeket egy másik régióba az Azure-erőforrás-mozgató használatával
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5712448c8c5248d3c84ce43f8a41c669355f1d43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565733"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Oktatóanyag: Azure-beli virtuális gépek áthelyezése régiók között

Ebből a cikkből megtudhatja, hogyan helyezheti át az Azure-beli virtuális gépeket és a kapcsolódó hálózati/tárolási erőforrásokat egy másik Azure-régióba az [Azure-erőforrás-mozgató](overview.md)használatával.
.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek és a követelmények ellenőrzése.
> * Válassza ki az áthelyezni kívánt erőforrásokat.
> * Erőforrás-függőségek feloldása.
> * A forrás erőforráscsoport előkészítése és áthelyezése. 
> * A többi erőforrás előkészítése és áthelyezése.
> * Döntse el, hogy el kívánja-e dobni vagy véglegesíteni az áthelyezést. 
> * Az áthelyezést követően szükség esetén eltávolíthatja az erőforrásokat a forrás régióban.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb elérési útját mutatják be, és az alapértelmezett beállításokat használják. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/). Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek
**Követelmény** | **Leírás**
--- | ---
**Erőforrás-mozgató támogatás** | [Tekintse át](common-questions.md) a támogatott régiókat és az egyéb gyakori kérdéseket.
**Előfizetés engedélyei** | Győződjön meg arról, hogy *tulajdonosi* hozzáféréssel rendelkezik az áthelyezni kívánt erőforrásokat tartalmazó előfizetéshez<br/><br/> **Miért van szükség tulajdonosi hozzáférésre?** Amikor először ad hozzá egy erőforrást egy adott forráshoz és célhoz egy Azure-előfizetésben, az erőforrás-mozgató létrehoz egy [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint. Az identitás létrehozásához, valamint a szükséges szerepkör (közreműködő vagy felhasználói hozzáférés rendszergazdája a forrás-előfizetésben) való hozzárendeléséhez az erőforrások hozzáadásához használt fióknak *tulajdonosi* engedélyekkel kell rendelkeznie az előfizetésben. [További](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) információ az Azure-szerepkörökről.
**VM-támogatás** |  Győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek támogatottak.<br/><br/> - [Ellenőrizze](support-matrix-move-region-azure-vm.md#windows-vm-support) a támogatott Windows-alapú virtuális gépeket.<br/><br/> - [Ellenőrizze](support-matrix-move-region-azure-vm.md#linux-vm-support) a támogatott Linux-alapú virtuális gépek és kernel-verziók ellenőrzését.<br/><br/> – A támogatott [számítási](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [tárolási](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)és [hálózati](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) beállítások keresése.
**Cél-előfizetés** | A célhelyen lévő előfizetésnek elegendő kvótára van szüksége ahhoz, hogy létrehozza a célhelyen áthelyezett erőforrásokat. Ha nem rendelkezik kvótával, [kérjen további korlátozásokat](../azure-resource-manager/management/azure-subscription-service-limits.md).
**A célként megadott régió díjai** | Ellenőrizze, hogy a virtuális gépeket áthelyező cél régióhoz tartozó díjszabást és díjakat kell-e használni. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával segítséget nyújthat.
    

## <a name="prepare-vms"></a>Virtuális gépek előkészítése

1. Miután ellenőrizte, hogy a virtuális gépek megfelelnek-e a követelményeknek, győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek be vannak kapcsolva. A célként megadott régióban elérhetővé kívánt összes virtuális gép lemezét csatolni és inicializálni kell a virtuális gépen.
1. Győződjön meg arról, hogy a virtuális gépek rendelkeznek a legújabb megbízható főtanúsítványokkal, valamint a visszavont tanúsítványok listájának (CRL) frissítése. Ehhez tegye a következőket:
    - Windows rendszerű virtuális gépeken telepítse a legújabb Windows-frissítéseket.
    - Linux rendszerű virtuális gépeken kövesse a terjesztői útmutatót, hogy a számítógépek rendelkezzenek a legújabb tanúsítványokkal és CRL-vel. 
1. Kimenő kapcsolatok engedélyezése a virtuális gépekről:
    - Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi [URL-címek](support-matrix-move-region-azure-vm.md#url-access) elérését
    - Ha hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok vezérléséhez, hozza létre ezeket a [szolgáltatási kódelemek szabályait](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Erőforrások kiválasztása 

Válassza ki az áthelyezni kívánt erőforrásokat.

- A kiválasztott forrástartományban található erőforráscsoportok összes támogatott erőforrástípus megjelenik.
- A régiók közötti áthelyezéshez már hozzáadott erőforrások nem jelennek meg.
- Az erőforrásokat egy olyan célcsoportba helyezi át, amely ugyanabban az előfizetésben található, mint a forrásoldali régió. Ha módosítani szeretné az előfizetést, ezt az erőforrások áthelyezése után teheti meg.

1. A Azure Portal keresse meg az *erőforrás-mozgató* kifejezést. Ezután a **szolgáltatások** területen válassza az **Azure-erőforrás mozgató** lehetőséget.

    ![Az erőforrás-mozgató keresési eredményei a Azure Portal](./media/tutorial-move-region-virtual-machines/search.png)

2. Az **Áttekintés** területen kattintson az első **lépések** elemre.

    ![Gomb egy másik régióba való áthelyezéshez szükséges erőforrások hozzáadásához](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Az **erőforrások áthelyezése**  >  **forrás + cél** területen válassza ki a forrás-előfizetést és a régiót.
4. A **cél** mezőben válassza ki azt a régiót, amelyre át szeretné helyezni a virtuális gépeket. Ezután kattintson a **Tovább** gombra.

    ![A forrás és a cél régió kiválasztására szolgáló lap](./media/tutorial-move-region-virtual-machines/source-target.png)

6. Az **áthelyezni kívánt erőforrások** területen kattintson az **erőforrások kiválasztása** elemre.
7. Az **erőforrások kiválasztása** területen válassza ki a virtuális gépet. Csak [az áthelyezéshez támogatott erőforrásokat](#prepare-vms)adhat hozzá. Ezután kattintson a **kész** gombra.

    ![Az áthelyezni kívánt virtuális gépek kiválasztására szolgáló lap](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  Az **áthelyezni kívánt erőforrásokhoz** kattintson a **tovább** gombra.
9. A **felülvizsgálat** területen ellenőrizze a forrás-és a célhely beállításait. 

    ![Lap a beállítások áttekintéséhez és az áthelyezés folytatásához](./media/tutorial-move-region-virtual-machines/review.png)
10. Az erőforrások hozzáadásának megkezdéséhez kattintson a **Folytatás** gombra.
11. A hozzáadási folyamat sikeres befejeződése után kattintson az **erőforrások hozzáadása az áthelyezéshez** az értesítési ikonra.
12. Az értesítésre való kattintás után tekintse át az erőforrásokat az **egyes régiók között** oldalon.

> [!NOTE]
> - A hozzáadott erőforrások *előkészítésre váró* állapotban vannak.
> - A virtuális gépek erőforráscsoport hozzáadása automatikusan megtörténik.
> - Ha el kívánja távolítani egy erőforrást egy áthelyezési gyűjteményből, akkor a művelet metódusa attól függ, hogy hol található az áthelyezési folyamat. [További információ](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Függőségek feloldása

1. Ha az erőforrások egy *érvényesítési függőségek* üzenetet jelenítenek meg a **problémák** oszlopban, kattintson a **függőségek ellenőrzése** gombra. Az érvényesítési folyamat megkezdődik.
2. Ha függőségek találhatók, kattintson a **függőségek hozzáadása** lehetőségre. 
3. A **függőségek hozzáadása** területen hagyja meg az alapértelmezett **összes függőség megjelenítése** beállítást.

    - Az összes függőség megjelenítése az erőforrás közvetlen és közvetett függőségein keresztül. Egy virtuális gép esetében például a NIC, a Virtual Network, a hálózati biztonsági csoportok (NSG) stb.
    - Az első szint függőségeinek megjelenítése csak a közvetlen függőségeket jeleníti meg. Egy virtuális gép esetében például a hálózati ADAPTERt jeleníti meg, a virtuális hálózatot azonban nem.


4. Válassza ki azokat a függő erőforrásokat, amelyeket hozzá szeretne adni > **függőségek hozzáadása** elemet. Figyelje az értesítések állapotát.

    ![Függőségek hozzáadása](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Újból érvényesítse a függőségeket. 
    ![További függőségek hozzáadására szolgáló oldal](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése 

A virtuális gépek előkészítése és áthelyezése előtt a virtuálisgép-erőforráscsoporthoz a célként megadott régióban kell lennie. 

### <a name="prepare-to-move-the-source-resource-group"></a>Felkészülés a forrásként szolgáló erőforráscsoport áthelyezésére

Az előkészítési folyamat során az erőforrás-mozgató Azure Resource Manager (ARM) sablonokat hoz létre az erőforráscsoport-beállítások használatával. Az erőforráscsoport erőforrásai nem érintettek.

Előkészítés a következőképpen történik:

1. Az **egyes régiókban** válassza ki a forrásként szolgáló erőforráscsoportot > **előkészítése**.
2. Az **erőforrások előkészítése** területen kattintson az **előkészítés** gombra.

    ![Erőforráscsoport előkészítése](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Az erőforráscsoport előkészítése után a folyamatban van a *mozgatás függőben* állapotba állítása. 

 
### <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése

Az áthelyezést a következőképpen indíthatja el:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **Áthelyezés kezdeményezése**
2. ln **erőforrások áthelyezése**, kattintson az **Áthelyezés kezdeményezése** lehetőségre. Az erőforráscsoport *folyamatban* állapotba lép.
3. Az áthelyezés kezdeményezése után létrejön a célként megadott erőforráscsoport, amely a generált ARM-sablon alapján történik. A forrás erőforráscsoport egy véglegesített *Áthelyezés függő* állapotba kerül.

    ![Kattintson az áthelyezés kezdeményezése gombra](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Az áthelyezési folyamat véglegesítése és befejezése:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **véglegesíti az áthelyezést**.
2. ln **erőforrások áthelyezése**, kattintson a **véglegesítés** elemre.

> [!NOTE]
> Miután véglegesíti az áthelyezést, a forrás erőforráscsoport *függő* állapotban van.

## <a name="prepare-resources-to-move"></a>Az áthelyezni kívánt erőforrások előkészítése

Most, hogy áthelyezte a forrás-erőforráscsoport áthelyezését, előkészítheti az *előkészítés függő* állapotában lévő egyéb erőforrások áthelyezését.

1. Az **egyes régiókban** ellenőrizze, hogy az erőforrások mostantól *függőben* lévő állapotban vannak-e, és nincs probléma. Ha nem, ellenőrizze újra, és hárítsa el a függőben lévő problémákat.

    ![A függőben lévő előkészítési állapotú erőforrásokat megjelenítő oldal](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Ha a cél beállításait a mozgatás megkezdése előtt szeretné szerkeszteni, válassza ki a hivatkozást az erőforráshoz tartozó **cél konfiguráció** oszlopban, és szerkessze a beállításokat. Ha szerkeszti a cél virtuális gép beállításait, a cél virtuális gép mérete nem lehet kisebb, mint a forrás virtuális gép mérete.  

Most, hogy áthelyezte a forrás erőforráscsoportot, előkészítheti a többi erőforrás áthelyezését.

3. Válassza ki az előkészíteni kívánt erőforrásokat. 

    ![Az egyéb erőforrások előkészítésének kiválasztására szolgáló lap](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Válassza az **előkészítés** lehetőséget. 

> [!NOTE]
> - Az előkészítési folyamat során a rendszer a Azure Site Recovery mobilitási ügynököt telepíti a virtuális gépekre a replikáláshoz.
> - A virtuálisgép-adatreplikációt a rendszer rendszeres időközönként replikálja a célhelyre. Ez nem befolyásolja a forrás virtuális gépet.
> - Az erőforrás áthelyezése ARM-sablonokat hoz létre a többi forrás erőforráshoz.
> - Az erőforrások előkészítése után folyamatban van egy *függőben lévő áthelyezési* állapot.

![A függőben lévő áthelyezés folyamatban állapotának megkezdéséhez tartozó erőforrásokat megjelenítő oldal](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

Az előkészített erőforrások segítségével most már elindíthatja az áthelyezést. 

1. Az **egyes régiókban** válassza az állapot *elindítása függőben* állapotú erőforrások lehetőséget. Ezután kattintson az **Áthelyezés kezdeményezése** lehetőségre.
2. Az **erőforrások áthelyezése** területen kattintson az **Áthelyezés kezdeményezése** lehetőségre.

    ![Kattintson az áthelyezés kezdeményezése gombra](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Nyomon követheti a folyamat lépéseit az értesítések sávján.

> [!NOTE]
> - Virtuális gépek esetén a replika virtuális gépek a célként megadott régióban jönnek létre. A forrás virtuális gép le van állítva, és néhány állásidő (általában perc).
> - Az erőforrás-mozgató újra létrehozza a többi erőforrást az előkészített ARM-sablonok használatával. Általában nincs leállás.
> - Az erőforrások áthelyezése után a rendszer végrehajtja a *függőben lévő* véglegesített állapotot.

![Az a lap, amely a * forrás törlése függőben * állapotot jeleníti meg](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="discard-or-commit"></a>Elveti vagy véglegesíti?

A kezdeti áthelyezés után eldöntheti, hogy véglegesíteni kívánja-e az áthelyezést, vagy elveti azt. 

- **Elvetés**: Ha teszteli a tesztet, elkerülheti az áthelyezést, és nem szeretné ténylegesen áthelyezni a forrás erőforrást. Az áthelyezés elvetése visszaadja az erőforrást a *függőben lévő áthelyezés indításának* állapotára.
- **Véglegesítés**: a véglegesítés befejezi az áthelyezést a célként megadott régióba. A véglegesítést követően a forrás erőforrás a *delete Source függőben* állapotba kerül, és eldöntheti, hogy szeretné-e törölni.


## <a name="discard-the-move"></a>Áthelyezés elvetése 

Az áthelyezés a következőképpen törölhető:

1. Az **egyes régiókban** válassza az állapot- *végrehajtási áthelyezés függőben* lévő erőforrások lehetőséget, majd kattintson az **Áthelyezés elvetése** lehetőségre.
2. Az **Áthelyezés elvetése** területen kattintson az **Elvetés** gombra.
3. Nyomon követheti a folyamat lépéseit az értesítések sávján.


> [!NOTE]
> Az erőforrások elvetése után a virtuális gépek egy *függőben lévő áthelyezési* állapotba kerülnek.

## <a name="commit-the-move"></a>Az áthelyezés elkövetése

Ha szeretné befejezni az áthelyezési folyamatot, véglegesítse az áthelyezést. 

1. Az **egyes régiókban** válassza az állapot- *végrehajtási áthelyezés függőben* lévő erőforrások lehetőséget, majd kattintson az **Áthelyezés elkövetése** elemre.
2. Az **erőforrások elkövetése** területen kattintson a **véglegesítés** elemre.

    ![Az áthelyezni kívánt erőforrások véglegesítésére szolgáló oldal](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Nyomon követheti a végrehajtás előrehaladását az értesítések sávon.

> [!NOTE]
> - Az áthelyezés véglegesítése után a virtuális gépek replikációja leáll. A forrás virtuális gépet nem érinti a véglegesítés.
> - A commit művelet nem befolyásolja a forrás hálózatkezelési erőforrásokat.
> - Az áthelyezés véglegesítése után az erőforrások a *forrás törlés függőben* állapotban vannak.

![Az a lap, amely a * forrás törlése függőben * állapotot jeleníti meg](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Beállítások konfigurálása az áthelyezés után

- A mobilitási szolgáltatás nem lesz automatikusan eltávolítva a virtuális gépekről. Távolítsa el manuálisan, vagy hagyja meg, ha azt tervezi, hogy újra áthelyezi a kiszolgálót.
- Módosítsa az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szabályait az áthelyezés után.


## <a name="delete-source-resources-after-commit"></a>Forrás erőforrásainak törlése a véglegesítés után

Az áthelyezést követően lehetőség van a forrás régió erőforrásainak törlésére is. 

> [!NOTE]
> Néhány erőforrás, például a kulcstartók és a SQL Server-kiszolgálók nem törölhetők a portálról, és az erőforrás-tulajdonságok lapról kell törölni.

1. A **különböző régiókban** kattintson a törölni kívánt forrás-erőforrás nevére.
2. Válassza a **forrás törlése** lehetőséget.

## <a name="delete-additional-resources-created-for-move"></a>Az áthelyezéshez létrehozott további erőforrások törlése

Az áthelyezést követően manuálisan törölheti az áthelyezési gyűjteményt, és Site Recovery a létrehozott erőforrásokat.

- Az áthelyezési gyűjtemény alapértelmezés szerint el van rejtve. Ha szeretné látni, be kell kapcsolnia a rejtett erőforrásokat.
- A gyorsítótár-tárolónak olyan zárolása van, amelyet törölni kell, mielőtt törölni lehet.

A következőképpen törölheti: 
1. Keresse meg az erőforrásokat az erőforráscsoporthoz ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Győződjön meg arról, hogy a forrás régióban lévő összes virtuális gép és más forrás erőforrás át lett helyezve vagy törölve lett. Ezzel biztosíthatja, hogy ne legyenek használatban függő erőforrások.
2. Erőforrások törlése:

    - Az áthelyezési gyűjtemény neve: ```movecollection-<sourceregion>-<target-region>``` .
    - A cache Storage-fiók neve ```resmovecache<guid>```
    - A tár neve: ```ResourceMove-<sourceregion>-<target-region>-GUID``` .
## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-beli virtuális gépek áthelyezve egy másik Azure-régióba.
> * Virtuális gépekhez társított erőforrások áthelyezése egy másik régióba.

Az Azure SQL Database-adatbázisok és a rugalmas készletek másik régióba való áthelyezésének kipróbálása folyamatban van.

> [!div class="nextstepaction"]
> [Azure SQL-erőforrások áthelyezése](./tutorial-move-region-sql.md)
