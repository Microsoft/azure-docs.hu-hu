---
title: 'Rövid útmutató: új szabályzat-hozzárendelés a portálon'
description: Ebben a rövid útmutatóban a Azure Portal használatával hozhat létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220887"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli a felügyelt lemezek házirend-definícióját _nem használó naplózási virtuális gépeket_ .

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **szabályzat** keresésével és kiválasztásával.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Képernyőkép a szabályzatok kereséséről az összes szolgáltatásban." border="false":::

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Képernyőkép a hozzárendelések lap kiválasztásáról a házirend áttekintése lapon." border="false":::

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Képernyőkép a &quot;szabályzat hozzárendelése&quot; lehetőség kiválasztásáról a hozzárendelések lapon." border="false":::

1. A **házirend kiosztása** lapon állítsa be a **hatókört** úgy, hogy kiválasztja a három pontot, majd kiválasztja a felügyeleti csoportot vagy az előfizetést. Ha szeretne, válasszon erőforráscsoportot. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ezután használja a **Select (kiválasztás** ) gombot a **hatókör** lap alján.

   Ebben a példában a **Contoso** előfizetést használjuk. Saját előfizetése ettől eltérhet.

1. Az erőforrások kizárhatóak a **Hatókör** alapján. A **Kizárások** alacsonyabb szinten kezdődnek, mint a **Hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Számos szabályzatdefiníció elérhető, többek között az alábbiak:

   - Címke és a hozzá tartozó érték kényszerítése
   - Címke és a hozzá tartozó érték alkalmazása
   - Címke öröklése az erőforráscsoporthoz, ha hiányzik

   Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

1. Keresse meg a _Felügyelt lemezeket nem használó virtuális gépek naplózása_ definíciót a szabályzatdefiníciók listájában. Válassza ki ezt a házirendet, majd használja a **kiválasztás** gombot.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Képernyőkép az elérhető definíciók szűréséről." border="false":::

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. A példánkban meghagyjuk a _Felügyelt lemezeket nem használó virtuális gépek naplózása_ értéket. Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.
   A **Hozzárendelte** mező automatikusan ki lesz töltve az alapján, hogy ki van bejelentkezve. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Hagyja _engedélyezve_ a szabályzat kényszerítését. További információ: [szabályzat-hozzárendelés – kényszerítési mód](./concepts/assignment-structure.md#enforcement-mode).

1. Kattintson a **tovább** gombra a lap alján, vagy a lap tetején található **Parameters (paraméterek** ) lapon a hozzárendelés varázsló következő szegmensére való áttéréshez.

1. Ha a házirend-definíció az **alapok** lapon a paraméterek között van kiválasztva, akkor ezen a lapon konfigurálhatók. Mivel a _felügyelt lemezeket nem használó naplózási virtuális gépek nem rendelkeznek_ paraméterrel, a lap alján található **tovább** gombra kattintva vagy az oldal tetején lévő **szervizelés** lapon lépjen a hozzárendelés varázsló következő szegmensére.

1. A **Felügyelt identitás létrehozása** jelölőnégyzetet hagyja üresen. Ezt a _jelölőnégyzetet be kell jelölni_ , ha a házirend vagy kezdeményezés olyan házirendet tartalmaz, amely a [deployIfNotExists](./concepts/effects.md#deployifnotexists) vagy a [módosítás](./concepts/effects.md#modify) hatásával rendelkezik. Mivel a rövid útmutatóhoz használt szabályzat nem, hagyja üresen. További információkért lásd a [felügyelt identitásokat](../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](./how-to/remediate-resources.md#how-remediation-security-works).

1. Kattintson a lap alján található **tovább** gombra, vagy a lap tetején található **nem megfelelőségi üzenetek** lapfülre, hogy a hozzárendelés varázsló következő szegmensére váltson.

1. A **nem megfelelőségi üzenetnek** a _virtuális gépekre történő beállításához felügyelt lemezt kell használnia_. Ez az egyéni üzenet akkor jelenik meg, ha egy erőforrást megtagadnak vagy a nem megfelelő erőforrásokat a normál kiértékelés során.

1. Kattintson a **tovább** gombra az oldal alján vagy a lap tetején található **felülvizsgálat + létrehozás** lapon a hozzárendelés varázsló következő szegmensére való áttéréshez.

1. Tekintse át a kiválasztott beállításokat, majd kattintson a **Létrehozás** gombra a lap alján.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megismerése érdekében.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg _azokat a naplózási virtuális gépeket, amelyek nem használják a felügyelt lemezeken_ létrehozott házirend-hozzárendelést.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Képernyőkép a megfelelőségi részletekről a szabályzat megfelelősége lapon." border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások** alatt jelennek meg.

Ha a meglévő erőforrások kiértékelésekor egy feltétel igaznak bizonyul, ezek az erőforrások a szabályzatnak nem megfelelőként lesznek megjelölve. A következő táblázat azt mutatja be, hogyan működnek együtt a szabályzatok különböző hatásai a feltételek kiértékelésével a megfelelőségi állapot eléréséhez. Bár a Azure Portalban nem jelenik meg a kiértékelési logika, a megfelelőségi állapot eredményei jelennek meg. A megfelelőségi állapotok eredménye lehet megfelelő vagy nem megfelelő.

| Erőforrás-állapot | Hatás | Szabályzat-kiértékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Új vagy frissített | Naplózás, módosítás, AuditIfNotExist | Igaz | Nem megfelelő |
| Új vagy frissített | Naplózás, módosítás, AuditIfNotExist | Hamis | Megfelelő |
| Létezik | Megtagadás, naplózás, Hozzáfűzés, módosítás, DeployIfNotExist, AuditIfNotExist | Igaz | Nem megfelelő |
| Létezik | Megtagadás, naplózás, Hozzáfűzés, módosítás, DeployIfNotExist, AuditIfNotExist | Hamis | Megfelelő |

> [!NOTE]
> A DeployIfNotExist-és AuditIfNotExist-effektusok esetében az IF utasításnak IGAZnak kell lennie, és a létezési feltétel nem megfelelőnek kell lennie. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott _Felügyelt lemezeket nem használó virtuális gépek naplózása_ szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését _nem használó naplózási virtuális gépekre_ , és válassza a **hozzárendelés törlése** lehetőséget.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Képernyőfelvétel: a helyi menü használatával törölhet egy hozzárendelést a megfelelőség lapról." border="false":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy hatókörhöz rendelt hozzá egy szabályzatdefiníciót, és kiértékelte annak megfelelőségi jelentését.
A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)