---
title: Azure-beli virtuális gépek áthelyezése egy másik régióban lévő rendelkezésre állási zónákba az Azure-beli erőforrás-mozgató használatával
description: Ismerje meg, hogyan helyezheti át az Azure-beli virtuális gépeket a rendelkezésre állási zónákba az Azure-erőforrások
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95543000"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Azure-beli virtuális gépek áthelyezése egy rendelkezésre állási zónába egy másik régióban

Ebből a cikkből megtudhatja, hogyan helyezheti át az Azure-beli virtuális gépeket (és a kapcsolódó hálózati/tárolási erőforrásokat) egy másik Azure-régióban lévő rendelkezésre állási zónába az [Azure erőforrás-mozgató](overview.md)használatával.

Az Azure-beli [rendelkezésre állási zónák](../availability-zones/az-overview.md#availability-zones) segítenek az Azure-környezet védelmében az adatközpont hibáiból. Az egyes rendelkezésre állási zónák egy vagy több, önálló áramellátással, hűtéssel, és hálózattal rendelkező adatközpontból állnak. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes [engedélyezett régióban](../availability-zones/az-region.md). Az erőforrás-mozgató használatával a következőket helyezheti el:

- Egypéldányos virtuális gép egy rendelkezésre állási zónába vagy rendelkezésre állási csoportba a célként megadott régióban.
- Egy rendelkezésre állási virtuális gép egy rendelkezésre állási zónába vagy rendelkezésre állási csoportba a célként megadott régióban.
- A forrásoldali régió rendelkezésre állási zónájában lévő virtuális gép a célként megadott régióban található rendelkezésre állási zónába.


> [!IMPORTANT]
> Az Azure-beli erőforrás-mozgató szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

Ha a virtuális gépeket egy másik rendelkezésre állási zónába szeretné áthelyezni ugyanabban a régióban, [tekintse át ezt a cikket](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Előfeltételek

- Az előfizetéshez tartozó *tulajdonosi* hozzáférés, amelyben az áthelyezni kívánt erőforrások találhatók.
    - Amikor először ad hozzá egy erőforrást egy adott forrás-és cél-hozzárendeléshez egy Azure-előfizetésben, az erőforrás-mozgató létrehoz egy [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint.
    - Az identitás létrehozásához, valamint a szükséges szerepkör (közreműködő vagy felhasználói hozzáférés rendszergazdája a forrás-előfizetésben) való hozzárendeléséhez az erőforrások hozzáadásához használt fióknak *tulajdonosi* engedélyekkel kell rendelkeznie az előfizetésben. [További](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) információ az Azure-szerepkörökről.
- Az előfizetéshez elegendő kvóta szükséges ahhoz, hogy a forrás erőforrásokat létrehozza a célként megadott régióban. Ha nem, igényeljen további korlátozásokat. [További információ](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Ellenőrizze, hogy a virtuális gépeket áthelyező cél régióhoz tartozó díjszabást és díjakat kell-e használni. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával segítséget nyújthat.
    


## <a name="check-vm-requirements"></a>VIRTUÁLIS gépekre vonatkozó követelmények keresése

1. Győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek támogatottak.

    - [Ellenőrizze](support-matrix-move-region-azure-vm.md#windows-vm-support) a támogatott Windows-alapú virtuális gépeket.
    - [Ellenőrizze](support-matrix-move-region-azure-vm.md#linux-vm-support) a támogatott Linux-alapú virtuális gépek és kernel-verziók ellenőrzését.
    - Keresse meg a támogatott [számítási](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [tárolási](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)és [hálózati](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) beállításokat.
2. Győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek be vannak kapcsolva.
3. Győződjön meg arról, hogy a virtuális gépek rendelkeznek a legújabb megbízható főtanúsítványokkal és a visszavont tanúsítványok listájának frissített listájával (CRL). 
    - Windows rendszerű Azure-beli virtuális gépeken telepítse a legújabb Windows-frissítéseket.
    - A Linux rendszerű virtuális gépeken kövesse a Linux-terjesztői útmutatót annak biztosításához, hogy a gép a legújabb tanúsítványokkal és CRL-vel rendelkezik. 
4. Kimenő kapcsolatok engedélyezése a virtuális gépekről:
    - Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi [URL-címek](support-matrix-move-region-azure-vm.md#url-access) elérését
    - Ha hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok vezérléséhez, hozza létre ezeket a [szolgáltatási kódelemek szabályait](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Válassza ki az áthelyezni kívánt erőforrásokat

Válassza ki az áthelyezni kívánt erőforrásokat.

- Bármelyik támogatott erőforrástípust kiválaszthatja a kiválasztott forrás régiójában található erőforráscsoportok között.
- Erőforrásokat helyez át egy adott régióba a forrás régió előfizetésében. Ha módosítani szeretné az előfizetést, ezt az erőforrások áthelyezése után teheti meg.

1. A Azure Portal keresse meg az erőforrás-mozgató kifejezést. Ezután a **szolgáltatások** területen válassza az **Azure-erőforrás mozgató** lehetőséget.

    ![Erőforrás-mozgató keresése](./media/move-region-availability-zone/search.png)

2. Az **Áttekintés** területen válassza az első **lépések** lehetőséget.

    ![Gomb – első lépések](./media/move-region-availability-zone/get-started.png)

3. Az **erőforrások áthelyezése**  >  **forrás + cél** területen válassza ki a forrás-előfizetést és a régiót.
4. A **cél** mezőben válassza ki azt a régiót, amelyre át szeretné helyezni a virtuális gépeket. Ezután kattintson a **Tovább** gombra.

     ![A forrás és a cél előfizetése/régiója kitöltésére szolgáló oldal](./media/move-region-availability-zone/source-target.png)

6. Az **áthelyezni kívánt erőforrások** területen kattintson az **erőforrások kiválasztása** elemre.
7. Az **erőforrások kiválasztása** területen válassza ki a virtuális gépet. Csak az áthelyezéshez támogatott erőforrásokat adhat hozzá. Ezután kattintson a **kész** gombra. Az **áthelyezni kívánt erőforrásokhoz** kattintson a **tovább** gombra.

    ![Az áthelyezni kívánt virtuális gépek kiválasztására szolgáló lap](./media/move-region-availability-zone/select-vm.png)
8. A **felülvizsgálat + Hozzáadás** elemnél ellenőrizze a forrás-és a célhely beállításait.

    ![Lap a beállítások áttekintéséhez és az áthelyezés folytatásához](./media/move-region-availability-zone/review.png)

9. Az erőforrások hozzáadásának megkezdéséhez kattintson a **Folytatás** gombra.
10. A hozzáadási folyamat sikeres befejeződése után kattintson az **erőforrások hozzáadása az áthelyezéshez** az értesítési ikonra.

    ![Üzenet az értesítésekben](./media/move-region-availability-zone/notification.png)

Az értesítésre való kattintás után az erőforrások megjelennek a **régiók közötti** oldalon

> [!NOTE]
> Az értesítésre való kattintás után az erőforrások megjelennek a **régiók közötti** oldalon a *felkészülés függő* állapotában.
> - Ha el kívánja távolítani egy erőforrást egy áthelyezési gyűjteményből, akkor a művelet metódusa attól függ, hogy hol található az áthelyezési folyamat. [További információ](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Függőségek feloldása

1. Ha az erőforrások egy *érvényesítési függőségek* üzenetet jelenítenek meg a **problémák** oszlopban, kattintson a **függőségek ellenőrzése** gombra. Az érvényesítési folyamat lények.
2. Ha függőségek találhatók, kattintson a **függőségek hozzáadása** lehetőségre. 
3. A **függőségek hozzáadása** területen válassza ki a függő erőforrásokat > **függőségek hozzáadása** elemet. Figyelje az értesítések állapotát.

    ![Függőség hozzáadására szolgáló gomb](./media/move-region-availability-zone/add-dependencies.png)

3. Szükség esetén további függőségeket vehet fel, és újból érvényesítheti a függőségeket. 

    ![További függőségek hozzáadására szolgáló oldal](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Az **egyes régiók** oldalon ellenőrizze, hogy az erőforrások mostantól függőben vannak-e *előkészítésre váró* állapotban, problémák nélkül.

    ![A függőben lévő előkészítési állapotú erőforrásokat megjelenítő oldal](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése 

A virtuális gépek előkészítése és áthelyezése előtt a forrás-erőforráscsoportnek jelen kell lennie a célcsoportban. 

### <a name="prepare-to-move-the-source-resource-group"></a>Felkészülés a forrásként szolgáló erőforráscsoport áthelyezésére

Előkészítés a következőképpen történik:

1. Az **egyes régiókban** válassza ki a forrásként szolgáló erőforráscsoportot > **előkészítése**.
2. Az **erőforrások előkészítése** területen kattintson az **előkészítés** gombra.

    ![A forrás erőforráscsoport előkészítésének gombja](./media/move-region-availability-zone/prepare-resource-group.png)

    Az előkészítési folyamat során az erőforrás-mozgató Azure Resource Manager (ARM) sablonokat hoz létre az erőforráscsoport-beállítások használatával. Az erőforráscsoport erőforrásai nem érintettek.

> [!NOTE]
>  Az erőforráscsoport előkészítése után a folyamatban van a *mozgatás függőben* állapotba állítása. 

![A függő állapot kezdeményezését bemutató állapot](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése

Az áthelyezést a következőképpen indíthatja el:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **Áthelyezés kezdeményezése**
2. ln **erőforrások áthelyezése**, kattintson az **Áthelyezés kezdeményezése** lehetőségre. Az erőforráscsoport *folyamatban* állapotba lép.
3. Az áthelyezés kezdeményezése után létrejön a célként megadott erőforráscsoport, amely a generált ARM-sablon alapján történik. A forrás erőforráscsoport egy véglegesített *Áthelyezés függő* állapotba kerül.

![A véglegesítés áthelyezését jelző állapot](./media/move-region-availability-zone/commit-move-pending.png)

Az áthelyezési folyamat véglegesítése és befejezése:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **commit Move**
2. ln **erőforrások áthelyezése**, kattintson a **véglegesítés** elemre.

> [!NOTE]
> Miután véglegesíti az áthelyezést, a forrás erőforráscsoport *függő* állapotban van.


## <a name="add-a-target-availability-zone"></a>Cél rendelkezésre állási zóna hozzáadása

Mielőtt áthelyezzük a többi erőforrást, beállítjuk a virtuális gép cél rendelkezésre állási zónáját.

1. Az **egyes régiók** lapon kattintson az áthelyezni kívánt virtuális gép **cél konfigurációja** oszlopában található hivatkozásra.

    ![Virtuális gép tulajdonságai](./media/move-region-availability-zone/select-vm-settings.png)

3. A **konfigurációs beállítások** területen határozza meg a cél virtuális gép beállítását. A virtuális gépet a következőképpen konfigurálhatja a célként megadott régióban:
    -  Hozzon létre egy új egyenértékű erőforrást a célként megadott régióban. A megadott beállítások kivételével a célként megadott erőforrás a forrással megegyező beállításokkal jön létre.
    - Válasszon ki egy meglévő virtuális gépet a célként megadott régióban. 

4. A **zónák** területen válassza ki azt a zónát, amelyben a virtuális gépet át kívánja helyezni.

    Csak a szerkesztett erőforrás módosításait kell elvégezni. Az összes függő erőforrást külön kell frissítenie.

5. Az **SKU** mezőben adja meg a cél virtuális géphez hozzárendelni kívánt [Azure-szintet](..//virtual-machines/sizes.md) .
6. A **rendelkezésre állási csoport** területen válassza ki a rendelkezésre állási készletet, ha azt szeretné, hogy a célként megadott virtuális gép egy rendelkezésre állási csoporton belül fusson.
7. Válassza a **Módosítások mentése** lehetőséget.

    ![Virtuális gép beállításai](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Az áthelyezni kívánt erőforrások előkészítése

Most, hogy áthelyezte a forrás erőforráscsoportot, előkészítheti a többi erőforrás áthelyezését.

1. Az **egyes régiókban** válassza ki az előkészíteni kívánt erőforrásokat. 

    ![Az egyéb erőforrások előkészítésének kiválasztására szolgáló lap](./media/move-region-availability-zone/prepare-other.png)

2. Válassza az **előkészítés** lehetőséget. 

> [!NOTE]
> - Az előkészítési folyamat során a rendszer a Azure Site Recovery mobilitási ügynököt telepíti a virtuális gépekre a replikáláshoz.
> - A virtuálisgép-adatreplikációt a rendszer rendszeres időközönként replikálja a célhelyre. Ez nem befolyásolja a forrás virtuális gépet.
> - Az erőforrás áthelyezése ARM-sablonokat hoz létre a többi forrás erőforráshoz.
> - Az erőforrások előkészítése után folyamatban van egy *függőben lévő áthelyezési* állapot.

![A függőben lévő áthelyezés folyamatban állapotának megkezdéséhez tartozó erőforrásokat megjelenítő oldal](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

Az előkészített erőforrások segítségével most már elindíthatja az áthelyezést. 

1. Az **egyes régiókban** válassza az állapot *elindítása függőben* állapotú erőforrások lehetőséget. Ezután kattintson az **Áthelyezés kezdeményezése** gombra.
2. Az **erőforrások áthelyezése** területen kattintson az **Áthelyezés kezdeményezése** lehetőségre.

    ![Az erőforrások áthelyezését kezdeményező oldal](./media/move-region-availability-zone/initiate-move.png)

3. Nyomon követheti a folyamat lépéseit az értesítések sávján.


> [!NOTE]
> - Virtuális gépek esetén a replika virtuális gépek a célként megadott régióban jönnek létre. A forrás virtuális gép le van állítva, és néhány állásidő (általában perc).
> - Az erőforrás-mozgató újra létrehozza a többi erőforrást az előkészített ARM-sablonok használatával. Általában nincs leállás.
> - Az erőforrások előkészítése után a rendszer elvégzi a végrehajtás *függőben állapotba helyezését* .


![Az a lap, amely egy véglegesített áthelyezés függőben állapotú erőforrásait jeleníti meg](./media/move-region-availability-zone/resources-commit-move-pending.png)

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
> Virtuális gépek esetében az erőforrások elvetése után a rendszer *kezdeményezi a folyamatban lévő áthelyezést* .

## <a name="commit-the-move"></a>Az áthelyezés elkövetése

Ha szeretné befejezni az áthelyezési folyamatot, véglegesítse az áthelyezést. 

1. Az **egyes régiókban** válassza az állapot- *végrehajtási áthelyezés függőben* lévő erőforrások lehetőséget, majd kattintson az **Áthelyezés elkövetése** elemre.
2. Az **erőforrások elkövetése** területen kattintson a **véglegesítés** elemre.

    ![Az áthelyezni kívánt erőforrások véglegesítésére szolgáló oldal](./media/move-region-availability-zone/commit-resources.png)

3. Nyomon követheti a végrehajtás előrehaladását az értesítések sávon.

> [!NOTE]
> - Az áthelyezés véglegesítése után a virtuális gépek replikációja leáll. A forrás virtuális gépet nem érinti a véglegesítés.
> - A commit művelet nem befolyásolja a forrás hálózatkezelési erőforrásokat.
> - Az áthelyezés véglegesítése után az erőforrások a *forrás törlés függőben* állapotban vannak.

![Az a lap, amely a * forrás törlése függőben * állapotot jeleníti meg](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Beállítások konfigurálása az áthelyezés után

A mobilitási szolgáltatás nem lesz automatikusan eltávolítva a virtuális gépekről. Távolítsa el manuálisan, vagy hagyja meg, ha azt tervezi, hogy újra áthelyezi a kiszolgálót.


## <a name="delete-source-resources-after-commit"></a>Forrás erőforrásainak törlése a véglegesítés után

Az áthelyezést követően lehetőség van a forrás régió erőforrásainak törlésére is.

1. A **régiók között** kattintson a törölni kívánt forrás-erőforrások nevére.
2. Az egyes erőforrások Tulajdonságok lapján válassza a **Törlés** lehetőséget.

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

## <a name="next-steps"></a>Következő lépések

[További](about-move-process.md) információ az áthelyezési folyamatról.