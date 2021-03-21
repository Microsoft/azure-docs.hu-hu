---
title: 'Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a Azure Portal-Azure RBAC használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan biztosíthat felhasználói hozzáférést az Azure-erőforrásokhoz a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: fcba9cad208c2ac170f91cc06a6db22e271f2a70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559318"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a Azure Portal használatával

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ebben az oktatóanyagban felhasználói hozzáférést biztosít a virtuális gépek egy erőforráscsoporthoz való létrehozásához és kezeléséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés biztosítása egy erőforráscsoport-hatókörben lévő felhasználó számára
> * Hozzáférés eltávolítása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. A navigációs listában kattintson az **Erőforráscsoportok** elemre.

1. Kattintson a **Hozzáadás** elemre az **erőforráscsoport** panel megnyitásához.

   ![Új erőforráscsoport hozzáadása](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Az **erőforráscsoport neve** mezőbe írja be a következőt: **RBAC-Resource-Group**.

1. Válasszon egy előfizetést és a egy helyet.

1. Az erőforráscsoport létrehozásához kattintson a **Létrehozás** lehetőségre.

1. Az erőforráscsoportok listájának frissítéséhez kattintson a **frissítés** gombra.

   Az új erőforráscsoport az erőforráscsoportok listájában jelenik meg.

   ![Erőforráscsoport-lista](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

Az Azure RBAC a hozzáférés biztosításához rendeljen hozzá egy Azure-szerepkört.

1. Az **erőforráscsoportok** listájában kattintson az új **RBAC-Resource-Group** erőforráscsoport elemre.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A szerepkör-hozzárendelések aktuális listájának megtekintéséhez kattintson a **Szerepkör-hozzárendelés** fülre.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/quickstart-assign-role-user-portal/access-control.png)

1. Kattintson a **Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőségre a Szerepkör-hozzárendelés hozzáadása szakasz megnyitásához.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Szerepkör-hozzárendelési menü hozzáadása](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válassza ki saját magát vagy egy másik felhasználót.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a felhasználónak hozzá kell rendelnie a virtuálisgép-közreműködő szerepkört a RBAC-Resource-Group erőforráscsoport-hatókörhöz.

   ![Virtuális gépek közreműködője szerepkör hozzárendelése](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az Azure RBAC-ben a hozzáférés eltávolításához el kell eltávolítania egy szerepkör-hozzárendelést.

1. A szerepkör-hozzárendelések listájában vegyen fel egy pipát a felhasználó mellett a virtuális gép közreműködői szerepkörével.

1. Kattintson az **Eltávolítás** elemre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen** gombra.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

1. A navigációs listában kattintson az **Erőforráscsoportok** elemre.

1. Kattintson a **RBAC-Resource-Group (csoport** ) elemre az erőforráscsoport megnyitásához.

1. Az erőforráscsoport törléséhez kattintson az **erőforráscsoport törlése** elemre.

   ![Erőforráscsoport törlése](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. A biztosan **törölni** kívánja a panelt, írja be az erőforráscsoport nevét: **RBAC-Resource-Group**.

1. Az erőforráscsoport törléséhez kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
