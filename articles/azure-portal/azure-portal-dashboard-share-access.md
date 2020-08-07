---
title: Azure Portal irányítópultok megosztása szerepköralapú Access Control használatával
description: Ez a cikk azt ismerteti, hogyan oszthat meg egy irányítópultot a Azure Portal szerepköralapú Access Control használatával.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: b478272ff790121d914a51f8ee4c5cf250134f4d
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923883"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával

Az irányítópult konfigurálását követően közzéteheti és megoszthatja azt a szervezet más felhasználóival. Lehetővé teszi mások számára az irányítópult megtekintését az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)használatával. Rendeljen hozzá egy felhasználót vagy egy felhasználói csoportot egy szerepkörhöz. Ez a szerepkör határozza meg, hogy a felhasználók megtekinthetik vagy módosíthatják a közzétett irányítópultot.

Minden közzétett irányítópult Azure-erőforrásként van megvalósítva. Az előfizetéshez tartozó felügyelhető elemek, és egy erőforráscsoport tartalmazza őket. Hozzáférés-vezérlési perspektívából az irányítópultok nem különböznek más erőforrásokkal, például egy virtuális géppel vagy egy Storage-fiókkal.

> [!TIP]
> Az irányítópulton lévő csempék a saját hozzáférés-vezérlési követelményeiket a megjelenített erőforrások alapján kényszerítik ki. Az irányítópultok széles körben is megoszthatók az egyes csempék adatvédelme során.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Az irányítópultok hozzáférés-vezérlésének ismertetése

Szerepköralapú Access Control (RBAC) esetén a felhasználók a hatókör három különböző szintjén oszthatók ki szerepkörökhöz:

* előfizetést
* erőforráscsoport
* erőforrás

A hozzárendelt engedélyek öröklik az előfizetést az erőforrásnak. A közzétett irányítópult egy erőforrás. Lehetséges, hogy már rendelkezik a közzétett irányítópultra vonatkozó előfizetéshez tartozó szerepkörökhöz hozzárendelt felhasználókkal.

Tegyük fel, hogy rendelkezik Azure-előfizetéssel, és a csapat különböző tagjai az előfizetéshez tartozó *tulajdonos*, *közreműködő*vagy *olvasó* szerepkört kaptak. A tulajdonosok vagy közreműködők az előfizetésben lévő irányítópultokat listázhatja, megtekinthetik, létrehozhatják, módosíthatják vagy törölhetik. Azok a felhasználók, akik az olvasók listázzák és megtekinthetik az irányítópultokat, de nem módosíthatják és nem törölhetik azokat. Az olvasói hozzáféréssel rendelkező felhasználók helyi módosításokat végezhetnek egy közzétett irányítópulton, például egy probléma hibaelhárításakor, de a módosításokat nem tehetik vissza a kiszolgálóra. Saját maguk készíthetik el az irányítópult saját példányát.

Emellett engedélyeket is hozzárendelhet az erőforráscsoporthoz, amely több irányítópultot vagy egy egyéni irányítópultot tartalmaz. Dönthet például úgy, hogy egy adott felhasználói csoportnak korlátozott engedélyekkel kell rendelkeznie az előfizetésben, de nagyobb hozzáférésre van szüksége egy adott irányítópulthoz. Rendelje hozzá ezeket a felhasználókat az irányítópult egyik szerepköréhez.

## <a name="publish-dashboard"></a>Irányítópult közzététele

Tegyük fel, hogy olyan irányítópultot konfigurál, amelyet szeretne megosztani az előfizetésében lévő felhasználói csoporttal. A következő lépések bemutatják, hogyan oszthat meg egy irányítópultot a Storage Managers nevű csoportba. Tetszés szerint megadhatja a csoport nevét. További információ: [csoportok kezelése a Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

A hozzáférés kiosztása előtt közzé kell tennie az irányítópultot.

1. Az irányítópulton válassza a **megosztás**lehetőséget.

    ![Az irányítópult megosztásának kiválasztása](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. A **megosztás + hozzáférés-vezérlés**területen válassza a **Közzététel**lehetőséget.

    ![Az irányítópult közzététele](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Alapértelmezés szerint a megosztás közzéteszi az irányítópultot az **irányítópultok**nevű erőforráscsoporthoz. Másik erőforráscsoport kiválasztásához törölje a jelet a jelölőnégyzetből.

Az irányítópult már közzé van téve. Ha az előfizetésből örökölt engedélyek megfelelőek, semmit nem kell tennie. A szervezet többi felhasználója elérheti és módosíthatja az irányítópultot az előfizetési szint szerepkörük alapján.

## <a name="assign-access-to-a-dashboard"></a>Irányítópulthoz való hozzáférés kiosztása

Az irányítópult egyik szerepköréhez hozzárendelhet egy felhasználói csoportot.

1. Az irányítópult közzététele után válassza a megosztás **vagy** a **megosztás megszüntetése** lehetőséget a **megosztás + hozzáférés-vezérlés**eléréséhez.

1. A **megosztás + hozzáférés-vezérlés**területen válassza a **felhasználók kezelése**lehetőséget.

    ![irányítópultok felhasználóinak kezelése](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Válassza a **szerepkör-hozzárendelések** lehetőséget, hogy megtekintse azokat a meglévő felhasználókat, akik már hozzá vannak rendelve ehhez az irányítópulthoz.

1. Új felhasználó vagy csoport hozzáadásához válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**.

    ![felhasználó hozzáadása az irányítópulthoz való hozzáféréshez](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Válassza ki azt a szerepkört, amely a megadható engedélyeket jelöli. Ebben a példában válassza a **közreműködő**lehetőséget.

1. Válassza ki a szerepkörhöz hozzárendelni kívánt felhasználót vagy csoportot. Ha nem látja a keresett felhasználót vagy csoportot a listában, használja a keresőmezőt. Az elérhető csoportok listája a Active Directoryban létrehozott csoportoktól függ.

1. Ha befejezte a felhasználók vagy csoportok hozzáadását, válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>További lépések

* A szerepkörök listáját az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md)című részben tekintheti meg.
* Az erőforrások kezelésével kapcsolatos további információkért lásd: [Az Azure-erőforrások kezelése a Azure Portal használatával](resource-group-portal.md).
