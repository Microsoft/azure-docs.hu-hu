---
title: Felhasználó által hozzárendelt felügyelt identitás kezelése a Azure Portal-Azure AD-ben
description: Részletes útmutató egy szerepkör felhasználó által hozzárendelt felügyelt identitáshoz való létrehozásához, listázásához, törléséhez és hozzárendeléséhez.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4cccdfc3a2cf8dd3827776028a7738ae769673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184830"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Szerepkörök létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával

Az Azure-erőforrások felügyelt identitásai biztosítják az Azure-szolgáltatásokat Azure Active Directory felügyelt identitással. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat, törölhet vagy rendelhet hozzá egy szerepkört egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

1. A felhasználó által hozzárendelt felügyelt identitás létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával.
2. A keresőmezőbe írja be a *felügyelt identitások* kifejezést, majd a **szolgáltatások** területen kattintson a **felügyelt identitások** elemre.
3. Kattintson a **Hozzáadás** gombra, és adja meg az értékeket az alábbi mezőkben a **felhasználóhoz rendelt felügyelt identitás létrehozása** panelen:
    - **Előfizetés**: válassza ki azt az előfizetést, amely a felhasználó által hozzárendelt felügyelt identitást hozza létre.
    - **Erőforráscsoport**: válasszon ki egy erőforráscsoportot a felhasználó által hozzárendelt felügyelt identitás létrehozásához, vagy kattintson az **új létrehozása** lehetőségre egy új erőforráscsoport létrehozásához.
    - **Régió**: válasszon egy régiót a felhasználó által hozzárendelt felügyelt identitás telepítéséhez, például az **USA nyugati** régiójában.
    - **Név**: Ez a felhasználó által hozzárendelt felügyelt identitás neve, például UAI1.
    ![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. A módosítások áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.
5. Kattintson a **Létrehozás** lehetőségre.

## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) vagy a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure-előfizetéshez társított fiókkal a felhasználó által hozzárendelt felügyelt identitások listázásához.
2. A keresőmezőbe írja be a *felügyelt identitások* kifejezést, majd a szolgáltatások területen kattintson a **felügyelt identitások** elemre.
3. Az előfizetéshez tartozó felhasználó által hozzárendelt felügyelt identitások listáját adja vissza.  A felhasználó által hozzárendelt felügyelt identitás részleteinek megtekintéséhez kattintson a nevére.

![Felhasználó által hozzárendelt felügyelt identitások listázása](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

A felhasználó által hozzárendelt identitás törlése nem távolítja el azt a virtuális gépről vagy erőforrásból, amelyhez hozzá lett rendelve.  Ha el szeretné távolítani a felhasználó által hozzárendelt identitást egy virtuális gépről, [távolítsa el a felhasználó által hozzárendelt felügyelt identitást egy virtuális](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)gépről.

1. A felhasználó által hozzárendelt felügyelt identitás törléséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával.
2. Válassza ki a felhasználó által hozzárendelt felügyelt identitást, és kattintson a **Törlés** gombra.
3. A megerősítés mezőben válassza az **Igen** lehetőséget.

![Felhasználó által hozzárendelt felügyelt identitás törlése](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Szerepkör hozzárendelése felhasználóhoz rendelt felügyelt identitáshoz 

Ha szerepkört szeretne hozzárendelni egy felhasználóhoz rendelt felügyelt identitáshoz, a fióknak szüksége van a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör-hozzárendelésére.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure-előfizetéshez társított fiókkal a felhasználó által hozzárendelt felügyelt identitások listázásához.
2. A keresőmezőbe írja be a *felügyelt identitások* kifejezést, majd a szolgáltatások területen kattintson a **felügyelt identitások** elemre.
3. Az előfizetéshez tartozó felhasználó által hozzárendelt felügyelt identitások listáját adja vissza.  Válassza ki azt a felhasználó által hozzárendelt felügyelt identitást, amelyhez szerepkört szeretne hozzárendelni.
4. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása** elemet.

   ![Felhasználó által hozzárendelt felügyelt identitás kezdete](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. A szerepkör-hozzárendelés hozzáadása panelen konfigurálja a következő értékeket, majd kattintson a **Mentés** gombra:
   - **Szerepkör** – a hozzárendelni kívánt szerepkör
   - **Hozzáférés hozzárendelése**  a felhasználóhoz rendelt felügyelt identitást hozzárendelő erőforráshoz
   - **Select** – a hozzáféréshez hozzárendelni kívánt tag
   
   ![Felhasználó által hozzárendelt felügyelt identitás IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)
