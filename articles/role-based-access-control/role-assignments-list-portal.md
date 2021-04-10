---
title: Azure-beli szerepkör-hozzárendelések listázása az Azure Portal-Azure RBAC használatával
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.openlocfilehash: 7d53df4159c071c17657e8a8367da605b6a5dc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556167"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Ez a cikk a szerepkör-hozzárendelések Azure Portal használatával történő listázását ismerteti.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="list-role-assignments-for-a-user-or-group"></a>Felhasználó vagy csoport szerepkör-hozzárendeléseinek listázása

Az előfizetésben egy felhasználóhoz vagy csoporthoz rendelt szerepköröket az **Azure szerepkör-hozzárendelések** ablaktábla használatával lehet megtekinteni.

1. A Azure Portal a Azure Portal menüben válassza a **minden szolgáltatás** lehetőséget.

1. Válassza a **Azure Active Directory** , majd a **felhasználók** vagy **csoportok** lehetőséget.

1. Kattintson arra a felhasználóra vagy csoportra, amely számára a szerepkör-hozzárendeléseket ki szeretné listázni.

1. Kattintson az **Azure-szerepkör-hozzárendelések** elemre.

    A kiválasztott felhasználóhoz vagy csoporthoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás részben tekintheti meg. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Felhasználó szerepkör-hozzárendelései](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Az előfizetés módosításához kattintson az **előfizetések** listára.

## <a name="list-owners-of-a-subscription"></a>Előfizetés tulajdonosainak listázása

Az előfizetéshez tartozó [tulajdonosi](built-in-roles.md#owner) szerepkörrel rendelkező felhasználók az előfizetés összes adatát kezelhetik. Az alábbi lépéseket követve listázhatja az előfizetések tulajdonosait.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések** elemre.

1. Kattintson arra az előfizetésre, amelynek a tulajdonosait szeretné listázni.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. Görgessen a **tulajdonosok** szakaszhoz, és tekintse meg az előfizetéshez tartozó tulajdonosi szerepkörrel rendelkező összes felhasználót.

   ![Előfizetés hozzáférés-vezérlése – szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/sub-access-control-role-assignments-owners.png)

## <a name="list-role-assignments-at-a-scope"></a>Hatókörhöz tartozó szerepkör-hozzárendelések listázása

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

   ![Hozzáférés-vezérlés – szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/rg-access-control-role-assignments.png)

   A szerepkör-hozzárendelések lapon megtekintheti, hogy ki fér hozzá a hatókörhöz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. A hozzáférés vagy kifejezetten ehhez az erőforráshoz van hozzárendelve, vagy egy hozzárendelésből a szülő hatókörbe van származtatva.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Hatókörhöz tartozó felhasználó szerepkör-hozzárendeléseinek listázása

Egy felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás hozzáférésének listázásához listázhatja a szerepkör-hozzárendeléseit. Kövesse az alábbi lépéseket egy adott hatókörben egy felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás szerepkör-hozzárendeléseinek listázásához.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **hozzáférés-ellenőrzési** lapra.

    ![Erőforráscsoport hozzáférés-vezérlése – hozzáférés ellenőrzése lap](./media/role-assignments-list-portal/rg-access-control-check-access.png)

1. A **Keresés** listában válassza ki azt a felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást, amelynek a hozzáférését ellenőriznie szeretné.

1. A keresőmezőbe írja be a karakterláncot a megjelenítendő nevek, e-mail-címek vagy objektumazonosítók kereséséhez.

    ![Hozzáférés-kiválasztási lista kijelölése](./media/shared/rg-check-access-select.png)

1. Kattintson a rendszerbiztonsági tag elemre a **hozzárendelések** panel megnyitásához.

    Ezen a panelen megtekintheti a kijelölt rendszerbiztonsági tag hozzáférését ezen a hatókörön, és örökölheti ezt a hatókört. A gyermek hatókörökön lévő hozzárendelések nem szerepelnek a felsorolásban. A következő hozzárendelések jelennek meg:

    - Szerepkör-hozzárendelések hozzáadva az Azure RBAC-hoz.
    - Az Azure-tervezetekkel vagy az Azure által felügyelt alkalmazásokkal hozzáadott hozzárendelések megtagadása.
    - Klasszikus szolgáltatás-rendszergazdai vagy Co-Administrator-hozzárendelések klasszikus központi telepítésekhez. 

    ![hozzárendelések ablaktábla](./media/shared/rg-check-access-assignments-user.png)

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendeléseinek listázása

Egy adott hatókörben a rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitások szerepkör-hozzárendeléseit listázhatja, ha a korábban leírtak szerint a **hozzáférés-vezérlés (iam) panelt** használja. Ez a szakasz azt ismerteti, hogyan lehet csak a felügyelt identitáshoz tartozó szerepkör-hozzárendeléseket listázni.

### <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

1. A Azure Portal nyissa meg a rendszer által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Identity (identitás**) elemre.

    ![Rendszer által hozzárendelt felügyelt identitás](./media/shared/identity-system-assigned.png)

1. Az **engedélyek** területen kattintson az **Azure szerepkör-hozzárendelések** elemre.

    A kiválasztott rendszerhez rendelt felügyelt identitáshoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás tartalmazza. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Szerepkör-hozzárendelések egy rendszerhez rendelt felügyelt identitáshoz](./media/shared/role-assignments-system-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetés** listára.

### <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

1. A Azure Portal nyissa meg a felhasználó által hozzárendelt felügyelt identitást.

1. Kattintson az **Azure-szerepkör-hozzárendelések** elemre.

    A kiválasztott, felhasználó által hozzárendelt felügyelt identitáshoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás részben tekintheti meg. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![A felhasználó által hozzárendelt felügyelt identitáshoz tartozó szerepkör-hozzárendeléseket megjelenítő képernyőkép.](./media/shared/role-assignments-user-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetés** listára.

## <a name="list-number-of-role-assignments"></a>Szerepkör-hozzárendelések számának listázása

Az egyes előfizetésekben akár **2000** szerepkör-hozzárendelést is megadhat. Ez a korlát tartalmazza az előfizetés, az erőforráscsoport és az erőforrás-hatókörök szerepkör-hozzárendeléseit. A korlát nyomon követéséhez a **szerepkör-hozzárendelések** lapon szerepel egy diagram, amely felsorolja az aktuális előfizetéshez tartozó szerepkör-hozzárendelések számát.

![Hozzáférés-vezérlés – szerepkör-hozzárendelési diagram száma](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Ha a maximális számhoz közeledik, és további szerepkör-hozzárendeléseket próbál hozzáadni, a **szerepkör-hozzárendelés hozzáadása** panelen figyelmeztetés jelenik meg. A szerepkör-hozzárendelések számának csökkentése érdekében lásd: az [Azure RBAC hibáinak megoldása](troubleshooting.md#azure-role-assignments-limit).

![Hozzáférés-vezérlés – szerepkör-hozzárendelési figyelmeztetés hozzáadása](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Szerepkör-hozzárendelések letöltése

A szerepkör-hozzárendeléseket CSV-vagy JSON-formátumú hatókörben töltheti le. Ez akkor lehet hasznos, ha meg kell vizsgálnia a listát egy számolótáblában, vagy leltározást kell végeznie egy előfizetés áttelepítésekor.

A szerepkör-hozzárendelések letöltésekor vegye figyelembe a következő feltételeket:

- Ha nem rendelkezik a címtár olvasásához szükséges engedélyekkel, például a címtár-olvasók szerepkörrel, a DisplayName, a SignInName és a objektumtípus oszlop üres lesz.
- A rendszer nem tartalmazza azokat a szerepkör-hozzárendeléseket, amelyek rendszerbiztonsági tag törölve lett.
- A klasszikus rendszergazdák számára biztosított hozzáférés nem szerepel a rendszerben.

A szerepkör-hozzárendelések hatókörön való letöltéséhez kövesse az alábbi lépéseket.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki azt a hatókört, ahol le szeretné tölteni a szerepkör-hozzárendeléseket. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **szerepkör-hozzárendelések letöltése** elemre a szerepkör-hozzárendelések letöltése panel megnyitásához.

    ![Hozzáférés-vezérlés – szerepkör-hozzárendelések letöltése](./media/role-assignments-list-portal/download-role-assignments.png)

1. A jelölőnégyzetek segítségével kiválaszthatja a letöltött fájlba felvenni kívánt szerepkör-hozzárendeléseket.

    - **Örökölt** – az örökölt szerepkör-hozzárendelések belefoglalása az aktuális hatókörbe.
    - **Aktuális hatókör** – a szerepkör-hozzárendelések belefoglalása az aktuális hatókörbe.
    - **Gyermekek** – szerepkör-hozzárendelések belefoglalása az aktuális hatókör alá eső szinteken. Ez a jelölőnégyzet le van tiltva a felügyeleti csoport hatókörében.

1. Válassza ki a fájlformátumot, amely vesszővel tagolt értékek (CSV) vagy JavaScript Object Notation (JSON) lehet.

1. Adja meg a fájl nevét.

1. A letöltés elindításához kattintson a **Start** gombra.

    Az alábbi példák az egyes fájlformátumok kimenetét mutatják be.

    ![Szerepkör-hozzárendelések letöltése CSV-fájlként](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Képernyőkép a letöltött szerepkör-hozzárendelésekről JSON formátumban.](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)
