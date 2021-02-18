---
title: Azure Monitor for VMs engedélyezése Azure Policy használatával
description: Ismerteti, hogyan engedélyezhető Azure Monitor for VMs több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Policy használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 4da0610de1f71cd422ec684ea633a4474c078862
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619828"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Azure Monitor for VMs engedélyezése Azure Policy használatával
Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Monitor for VMs az Azure-beli virtuális gépekhez vagy az Azure arc (előzetes verzió) szolgáltatással összekapcsolt hibrid virtuális géphez Azure Policy használatával. Azure Policy lehetővé teszi olyan szabályzat-definíciók hozzárendelését, amelyek telepítik a szükséges ügynököket az Azure-környezet Azure Monitor for VMséhez, és automatikusan engedélyezik a virtuális gépek figyelését minden egyes virtuális gép létrehozásakor. Azure Monitor for VMs egy olyan szolgáltatást biztosít, amely lehetővé teszi a nem megfelelő virtuális gépek felderítését és szervizelését a környezetben. Használja ezt a funkciót ahelyett, hogy közvetlenül a Azure Policy használatával kellene dolgoznia.

Ha még nem ismeri a Azure Policyt, rövid bevezetést kaphat a [Azure monitor üzembe helyezéséről a Azure Policy használatával](../deploy-scale.md).

> [!NOTE]
> Ha Azure Policyt szeretne használni az Azure-beli virtuálisgép-méretezési csoportokkal, vagy közvetlenül az Azure-beli virtuális gépeket engedélyező Azure Policy szeretne dolgozni, tekintse meg a [Azure monitor üzembe helyezése méretezéssel Azure Policy használatával](../deploy-scale.md#azure-monitor-for-vms)

## <a name="prerequisites"></a>Előfeltételek
- [Hozzon létre és konfiguráljon egy log Analytics munkaterületet](../insights/vminsights-configure-workspace.md).
- A [támogatott operációs rendszerekkel](../insights/vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport operációs rendszere támogatott legyen. 


## <a name="azure-monitor-for-vms-initiative"></a>Azure Monitor for VMs kezdeményezés
A Azure Monitor for VMs beépített szabályzat-definíciókat biztosít a Log Analytics-ügynök és a függőségi ügynök telepítéséhez az Azure Virtual Machines szolgáltatásban. A kezdeményezés **engedélyezése Azure monitor for VMS** tartalmazza ezeket a szabályzat-definíciókat. Ezt a kezdeményezést hozzárendelheti egy felügyeleti csoporthoz, előfizetéshez vagy erőforráscsoporthoz, hogy automatikusan telepítse az ügynököket az adott hatókörben lévő Windows vagy Linux rendszerű Azure-beli virtuális gépekre.

## <a name="open-policy-coverage-feature"></a>Házirend-lefedettségi funkció megnyitása
**Azure monitor for VMS szabályzatok lefedettségének** eléréséhez nyissa meg a **virtuális gépeket** a Azure Portal **Azure monitor** menüjében. Válassza a további bevezetési **Beállítások lehetőséget** , majd **engedélyezze** az Engedélyezés lehetőséget a **házirend használata** beállításnál.

[![Azure Monitor virtuális gépekről – első lépések lap](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Új hozzárendelés létrehozása
Ha még nem rendelkezik hozzárendeléssel, akkor a **házirend hozzárendelése** elemre kattintva hozzon létre egy újat.

[![Hozzárendelés létrehozása](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Ez ugyanaz az oldal, amellyel a Azure Policy egy kezdeményezést rendelhet hozzá, azzal a különbséggel, hogy a kiválasztott hatókörrel hardcoded, és az **Azure monitor for VMS kezdeményezés engedélyezése** definíciója. Igény szerint módosíthatja a **hozzárendelés nevét** , és hozzáadhat egy **leírást**. Válassza a **kizárások** lehetőséget, ha kizárást szeretne biztosítani a hatókörhöz. A hatókör lehet például egy felügyeleti csoport, és megadhat egy előfizetést az adott felügyeleti csoportban a hozzárendelésből való kizáráshoz.

[![Kezdeményezés kiosztása](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

A **Parameters (paraméterek** ) lapon válasszon ki egy **log Analytics munkaterületet** , amelyet a hozzárendelésben szereplő összes virtuális gép használni fog. Ha különböző munkaterületeket szeretne megadni a különböző virtuális gépekhez, több hozzárendelést kell létrehoznia, amelyek mindegyike saját hatókörrel rendelkezik. 

   > [!NOTE]
   > Ha a munkaterület a hozzárendelés hatókörén kívül esik, adja meg *log Analytics közreműködői* engedélyeit a szabályzat-HOZZÁRENDELÉS elsődleges azonosítójához. Ha ezt nem teszi meg, előfordulhat, hogy az üzembe helyezési hiba például a következő: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Munkaterület](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

A létrehozás **gombra kattintva** tekintse át a hozzárendelés részleteit, és kattintson a Create **(létrehozás)** gombra. Ne hozzon létre szervizelési feladatot ezen a ponton, mivel valószínűleg több szervizelési feladatra lesz szüksége a meglévő virtuális gépek engedélyezéséhez. Lásd a [megfelelőségi eredmények szervizelését](#remediate-compliance-results) alább.

## <a name="review-compliance"></a>Megfelelőség áttekintése
A hozzárendelések létrehozása után áttekintheti és kezelheti a lefedettséget a felügyeleti csoportokban és előfizetésekben a **Azure monitor for VMS kezdeményezés engedélyezése** lehetőségnél. Ez azt mutatja, hogy hány virtuális gép létezik az egyes felügyeleti csoportokban vagy előfizetésekben, valamint a megfelelőségi állapotukban.

[![Azure Monitor for VMs házirend kezelése lap](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


A következő táblázat az ebben a nézetben található információk leírását tartalmazza.

| Függvény | Leírás | 
|----------|-------------| 
| **Hatókör** | Felügyeleti csoport és előfizetések, amelyekkel a felügyeleti csoport hierarchiáján keresztül lehatolhat, vagy örökölt a hozzáférés.|
| **Szerepkör** | A hatókörben lévő szerepkör, amely lehet olvasó, tulajdonos vagy közreműködő. Ez üresen marad, ha rendelkezik hozzáféréssel az előfizetéshez, de nem ahhoz a felügyeleti csoporthoz, amelyhez tartozik. Ez a szerepkör határozza meg, hogy milyen adatok láthatók és milyen műveleteket végezhet el a szabályzatok vagy kezdeményezések (tulajdonos), a Szerkesztés vagy a megfelelőség megtekintése szempontjából. |
| **Összes virtuális gép** | A hatókörben lévő virtuális gépek teljes száma, függetlenül azok állapotától. Felügyeleti csoport esetén ez az előfizetések vagy a alárendelt felügyeleti csoportok alá beágyazott virtuális gépek teljes összege. |
| **Hozzárendelések lefedettsége** | A kezdeményezés által érintett virtuális gépek százalékos aránya. |
| **Hozzárendelés állapota** | **Sikeres** – a hatókörben lévő összes virtuális gép rendelkezik a rájuk telepített log Analytics és függőségi ügynökökkel.<br>**Figyelmeztetés** – az előfizetés nem felügyeleti csoportban van.<br>**Nincs elindítva** – új hozzárendelés lett hozzáadva.<br>**Zárolás** – nem rendelkezik megfelelő jogosultsággal a felügyeleti csoport számára.<br>**Üres** – nem léteznek virtuális gépek, vagy nincs hozzárendelve szabályzat. |
| **Megfelelő virtuális gépek** | A megfelelő virtuális gépek száma, amely a Log Analytics ügynökkel és függőségi ügynökkel rendelkező virtuális gépek száma. Ez üresen marad, ha nincsenek hozzárendelések, sem a hatókörben lévő virtuális gépek, sem a megfelelő engedélyek. |
| **Megfelelőség** | A teljes megfelelőségi szám a különböző erőforrások összegével megosztható különálló erőforrások összege. |
| **Megfelelőségi állapot** | **Megfelelő** – a hatókörben lévő virtuális gépeken lévő összes virtuális gép rendelkezik a rájuk telepített log Analytics és függőségi ügynökökkel, illetve a hozzárendelés hatálya alá tartozó hatókörben lévő összes új virtuális gépre még nincs kiértékelve.<br>**Nem megfelelő** – vannak olyan virtuális gépek, amelyek ki lettek értékelve, de nem engedélyezettek, és szervizelést igényelhetnek.<br>**Nincs elindítva** – új hozzárendelés lett hozzáadva.<br>**Zárolás** – nem rendelkezik megfelelő jogosultsággal a felügyeleti csoport számára.<br>**Üres** – nincs hozzárendelve szabályzat.  |


A kezdeményezés hozzárendelésekor a hozzárendelésben kiválasztott hatókör lehet a felsorolt hatókör vagy annak egy részhalmaza. Előfordulhat például, hogy létrehozott egy előfizetéshez tartozó hozzárendelést (házirend hatóköre), és nem felügyeleti csoportot (lefedettségi hatókör). Ebben az esetben a **hozzárendelési lefedettség** értéke azt jelzi, hogy a kezdeményezési hatókörben lévő virtuális gépek a hatókörben lévő virtuális gépekkel vannak osztva. Egy másik esetben előfordulhat, hogy kizárta néhány virtuális gépet, erőforráscsoportot vagy előfizetést a házirend hatókörből. Ha az érték üres, akkor azt jelzi, hogy a házirend vagy a kezdeményezés nem létezik, vagy Önnek nincs megfelelő engedélye. Az információk a **hozzárendelés állapota** alatt vannak megadva.


## <a name="remediate-compliance-results"></a>A megfelelőségi eredmények szervizelése
A kezdeményezés a létrehozott vagy módosított virtuális gépekre lesz alkalmazva, de a rendszer nem alkalmazza a meglévő virtuális gépekre. Ha a hozzárendelés nem jeleníti meg a 100%-os megfelelőséget, hozzon létre szervizelési feladatokat a meglévő virtuális gépek kiértékeléséhez és engedélyezéséhez, válassza a **megfelelőség megtekintése** elemet a három pont (...) gombra kattintva

[![Megfelelőség megtekintése](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

A **megfelelőség** lap felsorolja a megadott szűrőnek megfelelő hozzárendeléseket, és hogy azok megfelelőek-e. Kattintson egy hozzárendelésre a részleteinek megtekintéséhez.

[![Azure-beli virtuális gépek szabályzatának megfelelősége](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

A **kezdeményezés megfelelősége** lap felsorolja a kezdeményezésben szereplő szabályzat-definíciókat, valamint azt, hogy az egyes megfelelőségi állapotú-e.

[![Megfelelőség részletei](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Kattintson a házirend-definícióra a részleteinek megtekintéséhez. Azok a forgatókönyvek, amelyeknek a szabályzat-definíciók a megfelelőséget mutatják, a következők:

* Log Analytics ügynök vagy függőségi ügynök nincs telepítve. Hozzon létre egy szervizelési feladatot a mérsékléshez.
* A virtuálisgép-rendszerkép (operációs rendszer) nincs azonosítva a házirend-definícióban. A központi telepítési házirend feltételei csak a jól ismert Azure VM-rendszerképekből üzembe helyezett virtuális gépekre vonatkoznak. A dokumentációban megtekintheti, hogy támogatott-e a virtuális gép operációs rendszere.
* A virtuális gépek nem jelentkeznek be a megadott Log Analytics munkaterületre. A kezdeményezési hatókör egyes virtuális gépei a szabályzat-hozzárendelésben megadotttől eltérő Log Analytics-munkaterülethez csatlakoznak.

[![Szabályzat-megfelelőség részletei](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

A megfelelőségi problémák enyhítésére szolgáló szervizelési feladat létrehozásához kattintson a **szervizelési feladat létrehozása** elemre. 

[![Új szervizelési feladat](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Kattintson a **szervizelés** elemre a Szervizelési feladat létrehozásához **, majd az** elindításához. Valószínűleg több szervizelési feladatot is létre kell hoznia, egyet az egyes szabályzat-definíciók esetében. Egy kezdeményezéshez nem hozható létre szervizelési feladat.

[![Képernyőfelvétel: a szabályzat szervizelési panelje a figyeléshez | Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


A Szervizelési feladatok befejezését követően a virtuális gépeknek meg kell felelniük a Azure Monitor for VMs telepített és engedélyezett ügynököknek. 

## <a name="next-steps"></a>Következő lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el. 

- A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md). 
- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:. 
