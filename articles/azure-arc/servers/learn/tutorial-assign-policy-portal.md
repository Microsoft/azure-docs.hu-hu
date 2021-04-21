---
title: Oktatóanyag – Új szabályzat-hozzárendelés Azure Portal
description: Ebben az oktatóanyagban a Azure Portal fog létrehozni egy Azure Policy a nem megfelelő erőforrások azonosításához.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 20cb8125a157a3ce97c88278c99f90936b449b65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831653"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Oktatóanyag: Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Azure Policy támogatja az Arc-kompatibilis kiszolgáló állapotának vendégkonfigurációs szabályzatokkal való naplózását. A vendégkonfigurációs szabályzatok nem alkalmaznak konfigurációkat, csak a gépen belüli beállításokat naplóznak. Ez az oktatóanyag végigveszi egy szabályzat létrehozásának és hozzárendelésének folyamatán, és meghatározza, hogy mely Arc-kompatibilis kiszolgálókon nincs telepítve a Log Analytics-ügynök.

A folyamat végén sikeresen azonosíthatja azokat a gépeket, amelyeken nincs telepítve a Windowshoz vagy Linuxhoz használható Log Analytics-ügynök. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben az oktatóanyagban egy szabályzat-hozzárendelést hoz létre és hozzárendeli az előzetes _\[ verzióhoz]: A Log Analytics-ügynököt_ telepíteni kell a Linux rendszerű Azure Arc gépek szabályzatdefinícióra.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Szabályzat keresése a Minden szolgáltatásban" border="false":::

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="A Hozzárendelések oldal kiválasztása a Szabályzat áttekintése lapon" border="false":::

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Szabályzatdefiníció hozzárendelése a Hozzárendelések oldalról" border="false":::

1. A **Szabályzat hozzárendelése** oldalon a **Hatókör** kiválasztásához kattintson a három pontra, majd válasszon felügyeleti csoportot vagy előfizetést. Ha szeretne, válasszon erőforráscsoportot. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ezután kattintson a **Kiválasztás** gombra a **Hatókör** oldal alján.

   Ez a példa a **Parnell Repülőgépspace előfizetést** használja. Saját előfizetése ettől eltérhet.

1. Az erőforrások kizárhatóak a **Hatókör** alapján. A **Kizárások** alacsonyabb szinten kezdődnek, mint a **Hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Számos szabályzatdefiníció elérhető, többek között az alábbiak:

   - Címke és a hozzá tartozó érték kényszerítése
   - Címke és a hozzá tartozó érték alkalmazása
   - Címke öröklése az erőforráscsoportból, ha hiányzik

   Az elérhető beépített szabályzatok részleges listájáért tekintse meg a Azure Policy [példákat.](../../../governance/policy/samples/index.md)

1. Keresse meg az előzetes verzió listáját a szabályzatdefiníciók listájában]: A Log Analytics-ügynököt telepíteni kell _\[ a Windows Azure Arc-gépek_ definíciójában, ha engedélyezte az Arc-kompatibilis kiszolgálóügynöket egy Windows-alapú gépen. Linux-alapú gépnél keresse meg a megfelelő előzetes _\[ verziójút]: A Log Analytics-ügynököt_ telepíteni kell a Linux rendszerű Azure Arc gépek szabályzatdefinícióra. Kattintson a szabályzatra, majd kattintson a **Kiválasztás** elemre.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="A megfelelő szabályzatdefiníció megtalálása" border="false":::

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. Ebben a példában hagyja meg az Előzetes _\[ verzió]: A Log Analytics-ügynököt windowsos Azure Arc_ vagy előzetes verziójú gépekre kell _\[ telepíteni]:_ A Log Analytics-ügynököt a kiválasztott linuxos Azure Arc gépeken kell telepíteni. Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.
   A **Hozzárendelte** mező automatikusan ki lesz töltve az alapján, hogy ki van bejelentkezve. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. A **Felügyelt identitás létrehozása** jelölőnégyzetet hagyja üresen. Ezt a _négyzetet akkor_ kell bejelölve lennie, ha a szabályzat vagy kezdeményezés egy [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) effektusú szabályzatot tartalmaz. Mivel a rövid útmutatóhoz használt szabályzat nem, hagyja üresen. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Kattintson a **Hozzárendelés** gombra.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának meghatározásához.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

A **lap** bal oldalán válassza a Megfelelőség lehetőséget. Ezután keresse meg az Előzetes **\[ verzió]: A Log Analytics-ügynököt** telepíteni kell a Windows Azure Arc-gépekre vagy az előzetes **\[ verzióra]: A Log Analytics-ügynököt** telepíteni kell a Létrehozott Linux Azure Arc-gépek szabályzat-hozzárendelésre.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Megfelelőségi részletek a Szabályzatok megfelelősége lapon" border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem kompatibilisek ezzel az új hozzárendeléssel, azok a Nem megfelelő **erőforrások alatt jelennek meg.**

Ha a meglévő erőforrások kiértékelésekor egy feltétel igaznak bizonyul, ezek az erőforrások a szabályzatnak nem megfelelőként lesznek megjelölve. A következő táblázat azt mutatja be, hogyan működnek együtt a szabályzatok különböző hatásai a feltételek kiértékelésével a megfelelőségi állapot eléréséhez. Bár a kiértékelési logika nem látható a Azure Portal, a megfelelőségi állapot eredményei is megjelennek. A megfelelőségi állapotok eredménye lehet megfelelő vagy nem megfelelő.

| **Erőforrás-állapot** | **Hatás** | **Szabályzat-kiértékelés** | **Megfelelőségi állapot** |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Igaz | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Hamis | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | Igaz | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | Hamis | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a Megfelelőség **(vagy** Hozzárendelések) lehetőséget az Azure Policy oldal bal oldalán, és keresse meg az Előzetes **\[ verzió]: A Log Analytics-ügynököt** telepíteni kell a Windows Azure Arc-gépekre vagy az előzetes verzióra]: A Log Analytics-ügynököt telepíteni kell a létrehozott Linux **\[ Azure Arc-gépek** szabályzat-hozzárendelésre.

1. Kattintson a jobb gombbal a szabályzat-hozzárendelésre, és válassza a **Hozzárendelés törlése lehetőséget.**

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Hozzárendelés törlése a Megfelelőség lapról" border="false":::

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban hozzárendelt egy szabályzatdefiníciót egy hatókörhöz, és kiértékelte a megfelelőségi jelentését. A szabályzatdefiníció ellenőrzi, hogy a hatókörben minden erőforrás megfelelő-e, és azonosítja azokat, amelyek nem. Most már készen áll a virtuálisgép-elemzések engedélyezésével Azure Arc a virtuálisgép-kompatibilis kiszolgálók [gépének figyeléséhez.](../../../azure-monitor/vm/vminsights-overview.md)

Ha meg szeretne ismerkedni a teljesítmény figyelésével és megtekintésével, a folyamat futtatásával és a géptől való függőségeik megtekintésével, folytassa az oktatóanyagtal:

> [!div class="nextstepaction"]
> [Virtuálisgép-elemzések engedélyezése](tutorial-enable-vm-insights.md)