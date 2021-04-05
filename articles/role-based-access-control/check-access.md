---
title: Gyors útmutató – felhasználók hozzáférésének megkeresése az Azure-erőforrásokhoz – Azure RBAC
description: Ebből a rövid útmutatóból megtudhatja, hogyan ellenőrizheti az Azure-erőforrásokhoz való hozzáférést saját maga vagy egy másik felhasználó számára a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5e4f3314ba580dddbd995855bc0f0512b7597107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98115732"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Gyors útmutató: felhasználók hozzáférésének megkeresése az Azure-erőforrásokhoz

Időnként ellenőriznie kell, hogy a felhasználók milyen hozzáféréssel rendelkeznek az Azure-erőforrásokhoz. A hozzáférését a hozzárendelések listázásával tekintheti meg. A hozzáférés- **vezérlés (iam)** lapon **a hozzáférés ellenőrzése funkció használatával** gyorsan ellenőrizheti egy adott felhasználó hozzáférését.

## <a name="step-1-open-the-azure-resources"></a>1. lépés: az Azure-erőforrások megnyitása

Egy felhasználó hozzáférésének ellenõrzéséhez először meg kell nyitnia azokat az Azure-erőforrásokat, amelyeknek a hozzáférését ellenőriznie szeretné. Az Azure-erőforrások olyan szintekre vannak rendezve, amelyeket általában *hatókörnek* neveznek. Az Azure-ban négy szintű hatókört adhat meg a szélestől a keskenyig: felügyeleti csoport, előfizetés, erőforráscsoport és erőforrás.

![Az Azure RBAC hatóköri szintjei](../../includes/role-based-access-control/media/scope-levels.png)

Az alábbi lépéseket követve megnyithatja azon Azure-erőforrások készletét, amelyeknek a hozzáférését ellenőriznie szeretné.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az Azure-erőforrások, például a **felügyeleti csoportok**, **előfizetések**, **erőforráscsoportok** vagy egy adott erőforrás csoportját.

1. Kattintson az adott erőforrásra a hatókörben.

    A következő példa egy erőforráscsoportot mutat be.

    ![Erőforráscsoport – áttekintés](./media/shared/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>2. lépés: a felhasználó hozzáférésének engedélyezése

Az alábbi lépésekkel ellenőrizheti, hogy egy adott felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás hozzáférése van-e a korábban kiválasztott Azure-erőforrásokhoz.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

    Az alábbi példa az erőforráscsoport hozzáférés-vezérlés (IAM) lapját mutatja be.

    ![Erőforráscsoport hozzáférés-vezérlése – hozzáférés ellenőrzése lap](./media/shared/rg-access-control.png)

1. A **hozzáférés engedélyezése** lapon a **Keresés** listában válassza ki azt a felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást, amelynek a hozzáférését ellenőriznie szeretné.

1. A keresőmezőbe írja be a karakterláncot a megjelenítendő nevek, e-mail-címek vagy objektumazonosítók kereséséhez.

    ![Hozzáférés-kiválasztási lista kijelölése](./media/shared/rg-check-access-select.png)

1. Kattintson a rendszerbiztonsági tag elemre a **hozzárendelések** panel megnyitásához.

    Ezen a panelen megtekintheti a kijelölt rendszerbiztonsági tag hozzáférését ezen a hatókörön, és örökölheti ezt a hatókört. A gyermek hatókörökön lévő hozzárendelések nem szerepelnek a felsorolásban. A következő hozzárendelések jelennek meg:

    - Szerepkör-hozzárendelések hozzáadva az Azure RBAC-hoz.
    - Az Azure-tervezetekkel vagy az Azure által felügyelt alkalmazásokkal hozzáadott hozzárendelések megtagadása.
    - Klasszikus szolgáltatás-rendszergazdai vagy Co-Administrator-hozzárendelések klasszikus központi telepítésekhez. 

    ![Felhasználó szerepkör-és megtagadó hozzárendelései panelje](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>3. lépés: a hozzáférésének megkeresése

Kövesse az alábbi lépéseket a korábban kiválasztott Azure-erőforrásokhoz való hozzáférésének vizsgálatához.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **hozzáférés-ellenőrzési** lapon kattintson a **saját hozzáférés megtekintése** gombra.

    Megjelenik egy hozzárendelések ablaktábla, amely felsorolja az ezen a hatókörön belüli hozzáférését, és örökli a hatókört. A gyermek hatókörökön lévő hozzárendelések nem szerepelnek a felsorolásban.

    ![Szerepkörök és megtagadási Hozzárendelések panel](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával](role-assignments-list-portal.md)
