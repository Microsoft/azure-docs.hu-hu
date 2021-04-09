---
title: Felhasználó kiosztása Azure-előfizetés rendszergazdájaként – Azure RBAC
description: Megtudhatja, hogyan teheti a felhasználók számára egy Azure-előfizetés rendszergazdáját a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556840"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Azure-előfizetés rendszergazdai szerepkörének felhasználóhoz rendelése

Ahhoz, hogy egy felhasználó egy Azure-előfizetés rendszergazdája legyen, rendelje hozzá őket a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. A tulajdonosi szerepkör teljes hozzáférést biztosít a felhasználó számára az előfizetés összes erőforrásához, beleértve a mások számára való hozzáférés biztosításának engedélyét is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>1. lépés: az előfizetés megnyitása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A felső keresőmezőbe keressen előfizetéseket.

    ![Erőforráscsoport keresése Azure Portal](./media/shared/sub-portal-search.png)

1. Kattintson a használni kívánt előfizetésre.

    A következő példa egy előfizetést mutat be.

    ![Erőforráscsoport – áttekintés](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>2. lépés: a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitása

A **hozzáférés-vezérlés (iam)** az a lap, amelyet általában a szerepkörök hozzárendelésére használ az Azure-erőforrásokhoz való hozzáférés biztosításához. Ez az identitás-és hozzáférés-kezelés (IAM) néven is ismert, és a Azure Portal több helyén is megjelenik.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

    Az alábbi példa az előfizetés hozzáférés-vezérlés (IAM) oldalát mutatja be.

    ![Egy erőforráscsoport hozzáférés-vezérlés (IAM) lapja](./media/shared/sub-access-control.png)

1. Kattintson a **szerepkör-hozzárendelések** lapra a szerepkör-hozzárendelések ezen a hatókörön való megtekintéséhez.

1. Kattintson a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása** lehetőségre.
   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Szerepkör-hozzárendelési menü hozzáadása](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>3. lépés: a tulajdonosi szerepkör kiválasztása

A [tulajdonosi](built-in-roles.md#owner) szerepkör teljes hozzáférést biztosít az összes erőforrás kezeléséhez, beleértve a szerepkörök hozzárendelésének lehetőségét az Azure RBAC. Legfeljebb 3 előfizetés-tulajdonossal rendelkezhet, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét.

- A **szerepkör** listából válassza ki a **tulajdonosi** szerepkört.

   ![Tulajdonosi szerepkör kiválasztása a szerepkör-hozzárendelés hozzáadása panelen](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>4. lépés: válassza ki, hogy kinek van szüksége hozzáférésre

1. A **hozzáférés kiosztása** listában válassza a **felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget.

1. A **Select (kiválasztás** ) szakaszban keresse meg a felhasználót egy karakterlánc beírásával vagy a lista görgetésével.

   ![Válasszon felhasználót a szerepkör-hozzárendelés hozzáadása területen](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Ha megtalálta a felhasználót, kattintson rá a kijelöléshez.

## <a name="step-5-assign-role"></a>5. lépés: szerepkör kiosztása

1. A szerepkör hozzárendeléséhez kattintson a **Mentés** gombra.

   Néhány pillanat elteltével a felhasználónak hozzá kell rendelnie a szerepkört a kiválasztott hatókörhöz.

1. A **szerepkör-hozzárendelések** lapon ellenőrizze, hogy megjelenik-e a szerepkör-hozzárendelés a listában.

    ![Szerepkör-hozzárendelés mentett hozzáadása](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
- [Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával](role-assignments-list-portal.md)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
