---
title: Azure-szerepkörök kiosztása felügyelt identitáshoz (előzetes verzió) – Azure RBAC
description: Ismerje meg, hogyan rendelhet Azure-szerepköröket a felügyelt identitással, majd kiválaszthatja a hatókört és a szerepkört a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 57c8c00a64996bc6223fbe7e514db9db38ccdcc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556847"
---
# <a name="assign-azure-roles-to-a-managed-identity-preview"></a>Azure-szerepkörök kiosztása felügyelt identitáshoz (előzetes verzió)

A felügyelt identitáshoz hozzárendelheti a szerepkört a **hozzáférés-vezérlés (iam)** lapon leírt módon, az [Azure-szerepkörök hozzárendelésének Azure Portal használatával](role-assignments-portal.md)című témakörben leírtak szerint. Ha a hozzáférés-vezérlés (IAM) lapot használja, először a hatókörrel kell kezdődnie, majd ki kell választania a felügyelt identitást és szerepkört. Ez a cikk a szerepkörök felügyelt identitáshoz való hozzárendelésének másik módját ismerteti. Ezekkel a lépésekkel először a felügyelt identitást kell használni, majd ki kell választania a hatókört és a szerepkört.

> [!IMPORTANT]
> Szerepkör társítása felügyelt identitáshoz ezen alternatív lépések jelenleg előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Az alábbi lépéseket követve rendeljen hozzá egy szerepkört egy rendszerhez rendelt felügyelt identitáshoz a felügyelt identitással.

1. A Azure Portal nyissa meg a rendszer által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Identity (identitás**) elemre.

    ![Rendszer által hozzárendelt felügyelt identitás](./media/shared/identity-system-assigned.png)

1. Az **engedélyek** területen kattintson az **Azure szerepkör-hozzárendelések** elemre.

    Ha a szerepkörök már hozzá vannak rendelve a kiválasztott rendszerhez rendelt felügyelt identitáshoz, megjelenik a szerepkör-hozzárendelések listája. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Szerepkör-hozzárendelések egy rendszerhez rendelt felügyelt identitáshoz](./media/shared/role-assignments-system-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetés** listára.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása (előzetes verzió)** elemre.

1. A legördülő lista segítségével kiválaszthatja, hogy a szerepkör-hozzárendelés mely erőforrásokra vonatkozzon, például az **előfizetés**, az **erőforráscsoport** vagy az erőforrás.

    Ha nem rendelkezik a szerepkör-hozzárendelés írási engedélyeivel a kiválasztott hatókörhöz, a rendszer egy beágyazott üzenetet jelenít meg. 

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása a rendszerhez rendelt felügyelt identitáshoz](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a felügyelt identitás hozzárendeli a szerepkört a kiválasztott hatókörhöz.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Az alábbi lépéseket követve rendeljen hozzá egy szerepkört a felhasználóhoz rendelt felügyelt identitáshoz a felügyelt identitással.

1. A Azure Portal nyissa meg a felhasználó által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Azure szerepkör-hozzárendelések** elemre.

    Ha a szerepkörök már hozzá vannak rendelve a kiválasztott felhasználóhoz rendelt felügyelt identitáshoz, megjelenik a szerepkör-hozzárendelések listája. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Felhasználó által hozzárendelt felügyelt identitás szerepkör-hozzárendelései](./media/shared/role-assignments-user-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetés** listára.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása (előzetes verzió)** elemre.

1. A legördülő lista segítségével kiválaszthatja, hogy a szerepkör-hozzárendelés mely erőforrásokra vonatkozzon, például az **előfizetés**, az **erőforráscsoport** vagy az erőforrás.

    Ha nem rendelkezik a szerepkör-hozzárendelés írási engedélyeivel a kiválasztott hatókörhöz, a rendszer egy beágyazott üzenetet jelenít meg. 

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása felhasználó által hozzárendelt felügyelt identitáshoz](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a felügyelt identitás hozzárendeli a szerepkört a kiválasztott hatókörhöz.

## <a name="next-steps"></a>Következő lépések

- [Mik azok az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)
- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
- [Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával](role-assignments-list-portal.md)
