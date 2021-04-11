---
title: Azure Portal irányítópultok megosztása az Azure szerepköralapú hozzáférés-vezérlés használatával
description: Ez a cikk azt ismerteti, hogyan oszthat meg irányítópultokat a Azure Portal az Azure szerepköralapú hozzáférés-vezérlés használatával.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 336bfe9792c5ba4246458368e008a8c50833ed03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771540"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Azure-irányítópultok megosztása az Azure szerepköralapú hozzáférés-vezérlés használatával

Az irányítópult konfigurálását követően közzéteheti és megoszthatja azt a szervezet más felhasználóival. Lehetővé teszi mások számára az irányítópult megtekintését az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)használatával. Rendeljen egyetlen felhasználót vagy felhasználói csoportot egy szerepkörhöz. Ez a szerepkör határozza meg, hogy a felhasználók megtekinthetik vagy módosíthatják a közzétett irányítópultot.

Minden közzétett irányítópult Azure-erőforrásként van megvalósítva. Az előfizetéshez tartozó felügyelhető elemek, és egy erőforráscsoport tartalmazza őket. Hozzáférés-vezérlési perspektívából az irányítópultok nem különböznek más erőforrásokkal, például egy virtuális géppel vagy egy Storage-fiókkal. Az irányítópulton lévő csempék a saját hozzáférés-vezérlési követelményeiket a megjelenített erőforrások alapján kényszerítik ki. Az irányítópultok széles körben is megoszthatók az egyes csempék adatvédelme során.

## <a name="understanding-access-control-for-dashboards"></a>Az irányítópultok hozzáférés-vezérlésének ismertetése

Az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) a hatókör három különböző szintjén rendelhet hozzá felhasználókat a szerepkörökhöz:

* előfizetést
* erőforráscsoport
* erőforrás

A hozzárendelt engedélyek öröklik az előfizetést az erőforrásnak. A közzétett irányítópult egy erőforrás. Lehetséges, hogy már rendelkezik a közzétett irányítópultra vonatkozó előfizetéshez tartozó szerepkörökhöz hozzárendelt felhasználókkal.

Tegyük fel, hogy rendelkezik Azure-előfizetéssel, és a csapat különböző tagjai az előfizetéshez tartozó *tulajdonos*, *közreműködő* vagy *olvasó* szerepkört kaptak. A tulajdonosok vagy közreműködők az előfizetésben lévő irányítópultokat listázhatja, megtekinthetik, létrehozhatják, módosíthatják vagy törölhetik. Azok a felhasználók, akik az olvasók listázzák és megtekinthetik az irányítópultokat, de nem módosíthatják és nem törölhetik azokat. Az olvasói hozzáféréssel rendelkező felhasználók helyi módosításokat végezhetnek egy közzétett irányítópulton, például egy probléma hibaelhárításakor, de a módosításokat nem tehetik vissza a kiszolgálóra. Saját maguk készíthetik el az irányítópult saját példányát.

Engedélyeket rendelhet hozzá az erőforráscsoporthoz, amely több irányítópultot vagy egy egyéni irányítópultot tartalmaz. Dönthet például úgy, hogy egy adott felhasználói csoportnak korlátozott engedélyekkel kell rendelkeznie az előfizetésben, de nagyobb hozzáférésre van szüksége egy adott irányítópulthoz. Rendelje hozzá ezeket a felhasználókat az irányítópult egyik szerepköréhez.

## <a name="publish-a-dashboard"></a>Irányítópult közzététele

Tegyük fel, hogy olyan irányítópultot konfigurál, amelyet szeretne megosztani az előfizetésében lévő felhasználói csoporttal. A következő lépések bemutatják, hogyan oszthat meg egy irányítópultot a Storage Managers nevű csoportba. Tetszés szerint megadhatja a csoport nevét. További információ: [csoportok kezelése a Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

A hozzáférés kiosztása előtt közzé kell tennie az irányítópultot.

1. Az irányítópulton válassza a **megosztás** lehetőséget.

    ![Az irányítópult megosztásának kiválasztása](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. A **megosztás + hozzáférés-vezérlés** területen válassza a **Közzététel** lehetőséget.

    ![Az irányítópult közzététele](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Alapértelmezés szerint a megosztás közzéteszi az irányítópultot az **irányítópultok** nevű erőforráscsoporthoz. Másik erőforráscsoport kiválasztásához törölje a jelet a jelölőnégyzetből.

Az irányítópult már közzé van téve. Ha az előfizetésből örökölt engedélyek megfelelőek, semmit nem kell tennie. A szervezet többi felhasználója elérheti és módosíthatja az irányítópultot az előfizetési szint szerepkörük alapján.

## <a name="assign-access-to-a-dashboard"></a>Irányítópulthoz való hozzáférés kiosztása

Az irányítópult egyik szerepköréhez hozzárendelhet egy felhasználói csoportot.

1. Az irányítópult közzététele után válassza a **megosztás kezelése** lehetőséget.

1. A **Access Control** válassza a **szerepkör-hozzárendelések** lehetőséget, hogy megtekintse azokat a meglévő felhasználókat, akik már hozzá lettek rendelve az irányítópulthoz.

1. Új felhasználó vagy csoport hozzáadásához válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**.

    ![felhasználó hozzáadása az irányítópulthoz való hozzáféréshez](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Válassza ki azt a szerepkört, amely a megadható engedélyeket jelöli, például **közreműködő**.

1. Válassza ki a szerepkörhöz hozzárendelni kívánt felhasználót vagy csoportot. Ha nem látja a keresett felhasználót vagy csoportot a listában, használja a keresőmezőt. Az elérhető csoportok listája a Active Directoryban létrehozott csoportoktól függ.

1. Ha befejezte a felhasználók vagy csoportok hozzáadását, válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* A szerepkörök listáját az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md)című részben tekintheti meg.
* Az erőforrások kezelésével kapcsolatos további információkért lásd: [Az Azure-erőforrások kezelése a Azure Portal használatával](../azure-resource-manager/management/manage-resources-portal.md).