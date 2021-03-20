---
title: Oktatóanyag – új szabályzat-hozzárendelés Azure Portal
description: Ebben az oktatóanyagban a Azure Portal használatával hoz létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91826601"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Oktatóanyag: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Azure Policy támogatja az ív-kompatibilis kiszolgáló állapotának naplózását a vendég-konfigurációs házirendekkel. A vendég-konfigurációs házirendek nem alkalmaznak konfigurációkat, csak a számítógépen lévő beállításokat naplózzák. Ez az oktatóanyag végigvezeti a szabályzatok létrehozásának és hozzárendelésének folyamatán, és azonosítja, hogy az ív használatára képes kiszolgálók közül melyeken nincs telepítve a Log Analytics ügynök.

A folyamat végén sikeresen azonosíthatja azokat a gépeket, amelyeken nincs telepítve a Log Analytics ügynök a Windows vagy a Linux rendszerhez. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben az oktatóanyagban létrehoz egy szabályzat-hozzárendelést, és hozzárendeli az _\[ előzetes verziót]: log Analytics ügynöknek telepítve kell lennie a Linux Azure_ -beli arc-számítógépek házirend-definíciójában.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Szabályzat keresése az összes szolgáltatásban" border="false":::

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Hozzárendelések lap kiválasztása a házirend áttekintő oldaláról" border="false":::

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Házirend-definíció hozzárendelése a hozzárendelések lapról" border="false":::

1. A **Szabályzat hozzárendelése** oldalon a **Hatókör** kiválasztásához kattintson a három pontra, majd válasszon felügyeleti csoportot vagy előfizetést. Ha szeretne, válasszon erőforráscsoportot. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ezután kattintson a **Kiválasztás** gombra a **Hatókör** oldal alján.

   Ez a példa a **Parnell Aerospace** -előfizetést használja. Saját előfizetése ettől eltérhet.

1. Az erőforrások kizárhatóak a **Hatókör** alapján. A **Kizárások** alacsonyabb szinten kezdődnek, mint a **Hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Számos szabályzatdefiníció elérhető, többek között az alábbiak:

   - Címke és a hozzá tartozó érték kényszerítése
   - Címke és a hozzá tartozó érték alkalmazása
   - Címke öröklése az erőforráscsoporthoz, ha hiányzik

   Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](../../../governance/policy/samples/index.md).

1. Tekintse meg az előzetes verziót a szabályzat-definíciók listájában _\[ : log Analytics ügynöknek telepítve kell lennie a Windows Azure arc-gépek_ definíciójában, ha engedélyezte az arc-kompatibilis kiszolgálók ügynököt egy Windows-alapú gépen. A Linux-alapú gépek esetében keresse meg a megfelelő _\[ előzetes verziót]: log Analytics ügynöknek telepítve kell lennie a Linux Azure_ -beli ív-számítógép házirendjének definíciójában. Kattintson a szabályzatra, majd kattintson a **Kiválasztás** elemre.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="A megfelelő szabályzatdefiníció megtalálása" border="false":::

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. Ebben a példában az _\[ előnézet]: log Analytics ügynököt telepíteni kell a Windows Azure arc-gépekre vagy előzetes verzióra_ _\[ ]: log Analytics ügynöknek telepítve kell lennie a Linux Azure_ -beli arc-számítógépeken attól függően, hogy melyiket választotta. Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.
   A **Hozzárendelte** mező automatikusan ki lesz töltve az alapján, hogy ki van bejelentkezve. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. A **Felügyelt identitás létrehozása** jelölőnégyzetet hagyja üresen. Ezt a _jelölőnégyzetet be kell jelölni_ , ha a házirend vagy kezdeményezés olyan házirendet tartalmaz, amelynek a [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) hatása van. Mivel a rövid útmutatóhoz használt szabályzat nem, hagyja üresen. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Kattintson a **Hozzárendelés** gombra.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megismerése érdekében.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg az **\[ előzetes verziót]: log Analytics ügynököt telepíteni kell a Windows Azure arc-gépekre vagy az előzetes verzióra** **\[ ]: log Analytics ügynöknek telepítve kell lennie a létrehozott Linux Azure arc Machines** -szabályzat-hozzárendelésen.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Megfelelőségi részletek a szabályzat megfelelőségi lapján" border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások** alatt jelennek meg.

Ha a meglévő erőforrások kiértékelésekor egy feltétel igaznak bizonyul, ezek az erőforrások a szabályzatnak nem megfelelőként lesznek megjelölve. A következő táblázat azt mutatja be, hogyan működnek együtt a szabályzatok különböző hatásai a feltételek kiértékelésével a megfelelőségi állapot eléréséhez. Bár a Azure Portalban nem jelenik meg a kiértékelési logika, a megfelelőségi állapot eredményei jelennek meg. A megfelelőségi állapotok eredménye lehet megfelelő vagy nem megfelelő.

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

1. Válassza a **megfelelőség** (vagy **hozzárendelések**) lehetőséget a Azure Policy lap bal oldalán, és keresse meg az **\[ előzetes verziót]: log Analytics ügynöknek telepítve kell lennie a Windows Azure arc-gépeken** vagy az előzetes verzióban **\[ ]: log Analytics ügynöknek telepítve kell lennie a Linux Azure arc Machines** házirend-hozzárendelésében.

1. Kattintson a jobb gombbal a szabályzat-hozzárendelésre, és válassza a **hozzárendelés törlése** lehetőséget.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Hozzárendelés törlése a megfelelőség lapról" border="false":::

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem. Most már készen áll az Azure arc-kompatibilis kiszolgálók számítógépének figyelésére Azure Monitor for VMs használatával.

Ha meg szeretné tudni, hogyan figyelheti és tekintheti meg a teljesítményt, a folyamat és a függőségeik működését a gépről, folytassa az Oktatóanyaggal:

> [!div class="nextstepaction"]
> [Azure Monitor for VMs engedélyezése](tutorial-enable-vm-insights.md)