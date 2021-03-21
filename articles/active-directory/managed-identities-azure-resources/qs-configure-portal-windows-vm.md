---
title: Felügyelt identitások konfigurálása a Azure Portal-Azure AD használatával
description: Részletes útmutató az Azure-beli virtuális gépeken az Azure-erőforrások felügyelt identitásának konfigurálásához a Azure Portal használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590987"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Felügyelt identitások konfigurálása egy virtuális gépen lévő Azure-erőforrásokhoz a Azure Portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti és tilthatja le egy Azure-beli virtuális gép (VM) rendszer-és felhasználó által hozzárendelt felügyelt identitásait a Azure Portal használatával. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a virtuális gép rendszerhez rendelt felügyelt identitását a Azure Portal használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése virtuális gép létrehozása során

Ha a rendszer által hozzárendelt felügyelt identitást szeretné engedélyezni egy virtuális gépen a létrehozása során, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

- Az **identitás** szakasz **felügyelet** lapján váltson a **felügyelt szolgáltatás identitása** elemre **a** következőre:.  

![A rendszer által hozzárendelt identitás engedélyezése a virtuális gépek létrehozása során](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

A virtuális gép létrehozásához tekintse meg az alábbi rövid útmutatókat: 

- [Windows virtuális gép létrehozása az Azure Portallal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Linux virtuális gép létrehozása az Azure Portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő virtuális gépen

Ha olyan virtuális gépen szeretné engedélyezni a rendszer által hozzárendelt felügyelt identitást, amelyet eredetileg anélkül osztott ki, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkörének hozzárendelésére.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy olyan fiókkal, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

2. Navigáljon a kívánt virtuális géphez, és válassza az **identitás** elemet.

3. A **rendszer által hozzárendelt**, **állapot** területen válassza a be lehetőséget, majd kattintson **a** **Mentés** gombra:

   ![Képernyőfelvétel: az "identitás (előzetes verzió)" oldal, amelynek "a rendszerhez rendelt" állapota "on" értékre van állítva.](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Rendszer által hozzárendelt felügyelt identitás eltávolítása egy virtuális gépről

Ha a rendszer által hozzárendelt felügyelt identitást el szeretné távolítani egy virtuális gépről, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkörének hozzárendelésére.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

Ha olyan virtuális géppel rendelkezik, amelyhez már nincs szükség a rendszerhez rendelt felügyelt identitásra:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy olyan fiókkal, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva. 

2. Navigáljon a kívánt virtuális géphez, és válassza az **identitás** elemet.

3. A **rendszer által hozzárendelt**, állapot **területen válassza ki** , majd kattintson a **Mentés** gombra: 

   ![Konfiguráció lap képernyőképe](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

 Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználóhoz rendelt felügyelt identitást egy virtuális gépről a Azure Portal használatával.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Felhasználó által hozzárendelt identitás hozzárendelése egy virtuális gép létrehozása során

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

A Azure Portal jelenleg nem támogatja a felhasználó által hozzárendelt felügyelt identitások hozzárendelését a virtuális gépek létrehozása során. Ehelyett tekintse meg az alábbi virtuálisgép-létrehozási rövid útmutatók egyikét a virtuális gép első létrehozásához, majd folytassa a következő szakasszal a felhasználó által hozzárendelt felügyelt identitásnak a virtuális géphez való hozzárendelésével kapcsolatos részletekért:

- [Windows virtuális gép létrehozása az Azure Portallal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux virtuális gép létrehozása az Azure Portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy olyan fiókkal, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.
2. Navigáljon a kívánt virtuális géphez, és kattintson az **identitás**, a **felhasználó hozzárendelve** , majd a **\+ Hozzáadás** elemre.

   ![Képernyőkép, amely a "felhasználó által hozzárendelt" nevű "Identity" oldalt jeleníti meg, és a "Hozzáadás" gomb ki van emelve.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kattintson arra a felhasználó által hozzárendelt identitásra, amelyet hozzá szeretne adni a virtuális géphez, majd kattintson a **Hozzáadás** gombra.

    ![Felhasználóhoz rendelt felügyelt identitás hozzáadása a virtuális géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuális gépről

A felhasználó által hozzárendelt identitás egy [virtuális gépről](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) való eltávolításához a fióknak szüksége van a virtuálisgép-közreműködő szerepkör-hozzárendelésre. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy olyan fiókkal, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.
2. Navigáljon a kívánt virtuális géphez, és kattintson az **identitás**, **felhasználó kiosztva**, a törölni kívánt felhasználó által hozzárendelt felügyelt identitás nevére, majd az **Eltávolítás** elemre (a megerősítő ablaktáblán kattintson az **Igen** gombra).

   ![Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuális gépről](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Következő lépések

- Az Azure Portal használatával egy Azure-beli virtuális gép felügyelt identitás [-hozzáférését egy másik Azure-erőforráshoz](howto-assign-access-portal.md).