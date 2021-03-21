---
title: Erőforrások áthelyezése másik régióba az Azure-erőforrás-mozgató használatával
description: Megtudhatja, hogyan helyezhet át erőforrásokat egy erőforráscsoporthoz egy másik régióban az Azure-erőforrás-mozgató használatával.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797275"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Erőforrások áthelyezése régiók között (erőforráscsoport)

Ebből a cikkből megtudhatja, hogyan helyezhet át egy adott erőforráscsoporthoz tartozó erőforrásokat egy másik Azure-régióba. Az erőforráscsoport területen válassza ki az áthelyezni kívánt erőforrásokat. Ezután áthelyezheti őket az [Azure](overview.md)-beli erőforrás-mozgató használatával.

> [!IMPORTANT]
> Az Azure-beli erőforrás-mozgató szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.


## <a name="prerequisites"></a>Előfeltételek

- *Tulajdonosi* hozzáférésre van szüksége azon az előfizetésen, amelyben az áthelyezni kívánt erőforrások találhatók.
    - Amikor először ad hozzá egy erőforrást egy adott forrás-és cél-hozzárendeléshez egy Azure-előfizetésben, az erőforrás-mozgató létrehoz egy [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint.
    - Az identitás létrehozásához, valamint a szükséges szerepkör (közreműködő vagy felhasználói hozzáférés rendszergazdája a forrás-előfizetésben) való hozzárendeléséhez az erőforrások hozzáadásához használt fióknak *tulajdonosi* engedélyekkel kell rendelkeznie az előfizetésben. [További](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) információ az Azure-szerepkörökről.
- Az előfizetéshez elegendő kvóta szükséges ahhoz, hogy a forrás erőforrásokat létrehozza a célként megadott régióban. Ha nem, igényeljen további korlátozásokat. [További információ](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Ellenőrizze, hogy a virtuális gépeket áthelyező cél régióhoz tartozó díjszabást és díjakat kell-e használni. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával segítséget nyújthat.
- Győződjön meg arról, hogy az áthelyezni kívánt erőforrások támogatottak az erőforrás-mozgató:
    - Azure-beli virtuális gépek és kapcsolódó lemezek
    - Hálózati adapterek (NIC-k)
    - Rendelkezésre állási csoportok
    - Azure-beli virtuális hálózatok
    - Nyilvános IP-címek
    - Hálózati biztonsági csoportok (NSG-k)
    - Belső és nyilvános terheléselosztó
    - Azure SQL Database-adatbázisok és rugalmas készletek


## <a name="check-vm-requirements"></a>VIRTUÁLIS gépekre vonatkozó követelmények keresése

1. Győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek támogatottak.

    - [Ellenőrizze](support-matrix-move-region-azure-vm.md#windows-vm-support) a támogatott Windows-alapú virtuális gépeket.
    - [Ellenőrizze](support-matrix-move-region-azure-vm.md#linux-vm-support) a támogatott Linux-alapú virtuális gépek és kernel-verziók ellenőrzését.
    - Keresse meg a támogatott [számítási](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [tárolási](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)és [hálózati](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) beállításokat.
2. Győződjön meg arról, hogy a virtuális gépek rendelkeznek a legújabb megbízható főtanúsítványokkal és a visszavont tanúsítványok listájának frissített listájával (CRL). 
    - Windows rendszerű Azure-beli virtuális gépeken telepítse a legújabb Windows-frissítéseket.
    - A Linux rendszerű virtuális gépeken kövesse a Linux-terjesztői útmutatót annak biztosításához, hogy a gép a legújabb tanúsítványokkal és CRL-vel rendelkezik. 
3. Kimenő kapcsolatok engedélyezése a virtuális gépekről:
    - Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi [URL-címek](support-matrix-move-region-azure-vm.md#url-access) elérését
    - Ha hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok vezérléséhez, hozza létre ezeket a [szolgáltatási kódelemek szabályait](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Válassza ki az áthelyezni kívánt erőforrásokat

Válassza ki az áthelyezni kívánt erőforrásokat. Erőforrásokat helyez át egy adott régióba a forrás régió előfizetésében. Ha módosítani szeretné az előfizetést, ezt az erőforrások áthelyezése után teheti meg.

> [!NOTE]
>  Ne válassza a társított lemezek lehetőséget, vagy a művelet sikertelen lesz. A társított lemezek automatikusan bekerülnek a virtuális gépekbe.

1. A Azure Portal nyissa meg a megfelelő erőforráscsoportot.
2. Az erőforráscsoport lapon válassza ki az áthelyezni kívánt erőforrásokat.
3. Válassza   >  **az áthelyezés másik régióba** lehetőséget.

    ![Az erőforrások másik régióba való áthelyezésének kiválasztása](./media/move-region-within-resource-group/select-move-region.png)
    
4. A **forrás + cél** területen válassza ki azt a célhelyet, amelyre át szeretné helyezni az erőforrásokat. Ezután kattintson a **Tovább** gombra.


    ![A forrás és a cél lap a célként kijelölt régió kiválasztásához](./media/move-region-within-resource-group/source-target.png)


7. Az **áthelyezni kívánt erőforrások** területen válassza a **tovább** lehetőséget.  
8. Az **erőforrások kiválasztása** területen válassza ki az áthelyezni kívánt erőforrást. Csak az áthelyezéshez támogatott erőforrásokat adhat hozzá. Ezután válassza a **Done** (Kész) elemet.
9. Az **erőforrások áthelyezése** területen válassza a **tovább** lehetőséget. 
10. A **felülvizsgálat + Hozzáadás** területen ellenőrizze a forrás és a cél részleteit.
11. Győződjön meg róla, hogy tisztában van azzal, hogy az áthelyezett erőforrásokra vonatkozó metaadatok az erre a célra létrehozott erőforráscsoporthoz lesznek tárolva, és lehetővé teszi, hogy az erőforrás-mozgató rendszer által felügyelt identitást hozzon létre az előfizetés erőforrásainak eléréséhez.
1. Válassza a **Folytatás** lehetőséget az erőforrások hozzáadásának megkezdéséhez.

    ![Összefoglalás oldal a részletek megtekintéséhez és az áthelyezés folytatásához](./media/move-region-within-resource-group/summary.png)    

11. Elindul az erőforrás hozzáadása művelet. Ha a művelet befejeződik, az értesítések azt mutatják, hogy az erőforrások hozzá lettek adva, és a telepítés sikerült.
13. Az értesítések területen válassza az **erőforrások hozzáadása az áthelyezéshez** lehetőséget.

    ![Az értesítések között megjelenő üzenet](./media/move-region-within-resource-group/notification.png)    


14. Az értesítés kiválasztása után a kiválasztott erőforrások egy áthelyezési gyűjteményhez lesznek hozzáadva az Azure-beli erőforrás-mozgató központban.  Az erőforrás-mozgató segítségével megtekintheti a függőségeket, majd megkezdheti az erőforrások áthelyezését a célként megadott régióba.

## <a name="resolve-dependencies"></a>Függőségek feloldása

Az áthelyezett erőforrások megjelennek a **régiók közötti** oldalon, *előkészítés függőben* állapotban. Az érvényesítés megkezdése az alábbiak szerint történik:

1. Ha az erőforrások egy *érvényesítési függőségek* üzenetet jelenítenek meg a **problémák** oszlopban, válassza a **függőségek ellenőrzése** gombot. Az érvényesítési folyamat megkezdődik.

    ![A függőségek érvényesítésére szolgáló gomb](./media/move-region-within-resource-group/validate-dependencies.png)

2. Ha függőségek találhatók, válassza a **függőségek hozzáadása** lehetőséget. 
3. A **függőségek hozzáadása** területen válassza ki a függő erőforrásokat > **függőségek hozzáadása** elemet. Figyelje az értesítések állapotát.

    ![Függőség hozzáadására szolgáló gomb](./media/move-region-within-resource-group/add-dependencies.png)

3. Szükség esetén további függőségeket is hozzáadhat, és szükség szerint ellenőrizheti a függőségeket. Válassza a **frissítés** lehetőséget, és győződjön meg arról, hogy az erőforrások naprakész állapotot mutatnak.

4. Az **egyes régiók** oldalon ellenőrizze, hogy az erőforrások mostantól függőben vannak-e *előkészítésre váró* állapotban, problémák nélkül.

    ![Az összes erőforrás előkészítése függőben állapotának megjelenítésére szolgáló lap](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése 

Az erőforrások előkészítése és áthelyezése előtt a forrás erőforráscsoport szerepelnie kell a célként megadott régióban. 

### <a name="prepare-to-move-the-source-resource-group"></a>Felkészülés a forrásként szolgáló erőforráscsoport áthelyezésére

Előkészítés a következőképpen történik:

1. Az **egyes régiókban** válassza ki a forrásként szolgáló erőforráscsoportot > **előkészítése**.
2. Az **erőforrások előkészítése** területen válassza az **előkészítés** lehetőséget.
1. 
    ![A forrás erőforráscsoport előkészítésének gombja](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Az előkészítési folyamat során az erőforrás-mozgató Azure Resource Manager (ARM) sablonokat hoz létre az erőforráscsoport-beállítások használatával. Az erőforráscsoport erőforrásai nem érintettek.
    
> [!NOTE]
>  Az erőforráscsoport előkészítése után a folyamatban van a *mozgatás függőben* állapotba állítása. A legutóbbi állapot megjelenítéséhez frissítse a frissítést.

![A függő állapot kezdeményezését bemutató állapot](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése

Az áthelyezést a következőképpen indíthatja el:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **Áthelyezés kezdeményezése**
2. ln **erőforrások áthelyezése**, válassza az **Áthelyezés kezdeményezése** lehetőséget. Az erőforráscsoport *folyamatban* állapotba lép.
3. Az áthelyezés kezdeményezése után létrejön a célként megadott erőforráscsoport, amely a generált ARM-sablon alapján történik. A forrás erőforráscsoport egy véglegesített *Áthelyezés függő* állapotba kerül.

![A véglegesítés áthelyezését jelző állapot](./media/move-region-availability-zone/commit-move-pending.png)

Az áthelyezési folyamat véglegesítése és befejezése:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **commit Move**
2. ln **erőforrások áthelyezése**, válassza a **véglegesítés** lehetőséget.

> [!NOTE]
> Miután véglegesíti az áthelyezést, a forrás erőforráscsoport *függő* állapotban van.

## <a name="modify-target-settings"></a>Célbeállítások módosítása

Ha nem szeretné áthelyezni a forrás erőforrást, a következők valamelyikét végezheti el:

- Hozzon létre egy erőforrást a céltartományban a forrás régióban lévő erőforrással megegyező névvel és beállításokkal.
- Hozzon létre egy új egyenértékű erőforrást a célként megadott régióban. A megadott beállítások kivételével a célként megadott erőforrás a forrással megegyező beállításokkal jön létre.
- Meglévő erőforrás használata a célként megadott régióban.

Módosítsa a beállításokat a következőképpen:

1. Egy beállítás módosításához válassza ki a bejegyzést az erőforráshoz tartozó **cél konfiguráció** oszlopban.
2. A **cél konfigurációja** lapon válassza ki a használni kívánt beállításokat.
    Csak a szerkesztett erőforrás módosításait kell elvégezni. Az összes függő erőforrást külön kell frissítenie.   
    
A módosítható beállítások az erőforrás típusától függenek. [További](modify-target-settings.md) információ a cél beállításainak szerkesztéséről.

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

1. Az **egyes régiókban** válassza az állapot *elindítása függőben* állapotú erőforrások lehetőséget. Ezután válassza az **Áthelyezés kezdeményezése** lehetőséget.
2. Az **erőforrások áthelyezése** területen válassza az **Áthelyezés kezdeményezése** lehetőséget.

    ![Az áthelyezés kezdeményezése gomb kiválasztása](./media/move-region-within-resource-group/initiate-move.png)

3. Nyomon követheti a folyamat lépéseit az értesítések sávján.


> [!NOTE]
> - Virtuális gépek esetén a replika virtuális gépek a célként megadott régióban jönnek létre. A forrás virtuális gép le van állítva, és néhány állásidő (általában perc).<br/>
> - Az erőforrás-mozgató újra létrehozza a többi erőforrást az előkészített ARM-sablonok használatával. Általában nincs leállás.<br/> 
> - A terheléselosztó esetében a NAT-szabályok nem másolódnak át. Az áthelyezés véglegesíte után hozza létre őket a célként megadott régióban.
> - Nyilvános IP-címek esetén a DNS-név címkéje nincs átmásolva. Az áthelyezés véglegesíte után hozza létre újra a címkét.
> - Az erőforrások előkészítése után a rendszer elvégzi a végrehajtás *függőben állapotba helyezését* .


## <a name="discard-or-commit"></a>Elveti vagy véglegesíti?

A kezdeti áthelyezés után eldöntheti, hogy véglegesíteni kívánja-e az áthelyezést, vagy elveti azt. 

- **Elvetés**: Ha teszteli a tesztet, elkerülheti az áthelyezést, és nem szeretné ténylegesen áthelyezni a forrás erőforrást. Az áthelyezés elvetése visszaadja az erőforrást a *függőben lévő áthelyezés indításának* állapotára.
- **Véglegesítés**: a véglegesítés befejezi az áthelyezést a célként megadott régióba. A véglegesítést követően a forrás erőforrás a *delete Source függőben* állapotba kerül, és eldöntheti, hogy szeretné-e törölni.


## <a name="discard-the-move"></a>Áthelyezés elvetése 

Az áthelyezés a következőképpen törölhető:

1. Az **egyes régiókban** válassza ki a *függőben lévő* állapottal rendelkező erőforrások lehetőséget, majd kattintson az **Áthelyezés elvetése** lehetőségre.
2. Az **Áthelyezés elvetése** lapon válassza az **Elvetés** lehetőséget.
3. Nyomon követheti a folyamat lépéseit az értesítések sávján.
4. Ha az értesítések azt mutatják, hogy az áthelyezés sikeres volt, válassza a **frissítés** lehetőséget. 

> [!NOTE]
> Virtuális gépek esetében az erőforrások elvetése után a rendszer *kezdeményezi a folyamatban lévő áthelyezést* .

## <a name="commit-the-move"></a>Az áthelyezés elkövetése

Ha szeretné befejezni az áthelyezési folyamatot, véglegesítse az áthelyezést. 


1. Az **egyes régiókban** válassza az állapot- *visszalépési áthelyezés függőben* lévő erőforrások lehetőséget, majd válassza a végrehajtás **áthelyezése** lehetőséget.
2. Az **erőforrások véglegesítve** területen **válassza a** végrehajtás lehetőséget.

    ![Az áthelyezni kívánt erőforrások véglegesítésére szolgáló oldal](./media/move-region-within-resource-group/commit-resources.png)

3. Nyomon követheti a végrehajtás előrehaladását az értesítések sávon.

> [!NOTE]
> - Az áthelyezés véglegesítése után a virtuális gépek replikációja leáll. A forrás virtuális gépet nem érinti a véglegesítés.
> - A commit művelet nem befolyásolja a forrás hálózatkezelési erőforrásokat.
> - Az áthelyezés véglegesítése után az erőforrások a *forrás törlés függőben* állapotban vannak.

## <a name="configure-settings-after-the-move"></a>Beállítások konfigurálása az áthelyezés után

1. Mivel a DNS-címkék nem lettek átmásolva a nyilvános IP-címekre, a mozgatás után navigáljon a cél erőforrásaihoz, és frissítse a címkét. 
2. Belső terheléselosztó esetén, mivel a NAT-szabályok nem másolódnak át, navigáljon a célként megadott régióban létrehozott erőforrásokhoz, és frissítse a NAT-szabályokat.
3. A mobilitási szolgáltatás nem lesz automatikusan eltávolítva a virtuális gépekről. Távolítsa el manuálisan, vagy hagyja meg, ha azt tervezi, hogy újra áthelyezi a kiszolgálót.
## <a name="delete-source-resources-after-commit"></a>Forrás erőforrásainak törlése a véglegesítés után

Az áthelyezést követően lehetőség van a forrás régió erőforrásainak törlésére is. 

1. A **régiók között** válassza ki a törölni kívánt forrás-erőforrások nevét.
2. Az egyes erőforrások Tulajdonságok lapján válassza a **Törlés** lehetőséget.

## <a name="delete-additional-resources-created-for-move"></a>Az áthelyezéshez létrehozott további erőforrások törlése

Az áthelyezést követően manuálisan törölheti az áthelyezési gyűjteményt, és Site Recovery a létrehozott erőforrásokat.

- Az áthelyezési gyűjtemény alapértelmezés szerint el van rejtve. Ha szeretné látni, be kell kapcsolnia a rejtett erőforrásokat.
- A gyorsítótár-tárolónak olyan zárolása van, amelyet törölni kell, mielőtt törölni lehet.

A következőképpen törölheti: 

1. Keresse meg az erőforrásokat az erőforráscsoport ```RegionMoveRG-<sourceregion>-<target-region>``` területen a forrás régióban.
2. Győződjön meg arról, hogy a mozgatási gyűjteményben lévő összes virtuális gép és más forrás erőforrás át lett helyezve/törölve lett. Ezzel biztosíthatja, hogy ne legyenek használatban függő erőforrások.
2. Erőforrások törlése:

    - Az áthelyezési gyűjtemény neve: ```movecollection-<sourceregion>-<target-region>``` .
    - A cache Storage-fiók neve ```resmovecache<guid>```
    - A tár neve: ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Következő lépések


[További](about-move-process.md) információ az áthelyezési folyamatról.