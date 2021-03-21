---
title: Felügyelt identitások konfigurálása a virtuálisgép-méretezési csoporton – Azure AD
description: Részletes útmutató az Azure-erőforrások felügyelt identitásának konfigurálásához egy virtuálisgép-méretezési csoporton a Azure Portal használatával.
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
ms.openlocfilehash: 73c2f4167d6ce5465a879f6b2ef7e96d91065c4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590919"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-vmss-using-the-azure-portal"></a>Felügyelt identitások konfigurálása egy virtuálisgép-méretezési csoport (VMSS) Azure-erőforrásaihoz a Azure Portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy virtuálisgép-méretezési csoporton:

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-szerepkör-hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

    - A virtuálisgép-méretezési csoportokból a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához a [virtuális gép közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást a Azure Portal használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport létrehozása során

A Azure Portal jelenleg nem támogatja a rendszerhez rendelt felügyelt identitás engedélyezését egy virtuálisgép-méretezési csoport létrehozása során. Ehelyett a következő virtuálisgép-méretezési csoport létrehozási rövid útmutatója című cikkből megtudhatja, hogyan hozhat létre először virtuálisgép-méretezési készletet, majd folytassa a következő szakasszal, amely részletesen ismerteti a rendszerhez rendelt felügyelt identitást a virtuálisgép-méretezési csoportokban:

- [Virtuálisgép-méretezési csoport létrehozása a Azure Portalban](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>A rendszerhez rendelt felügyelt identitás engedélyezése meglévő virtuálisgép-méretezési csoportokon

A rendszer által hozzárendelt felügyelt identitás engedélyezése egy olyan virtuálisgép-méretezési csoporton, amely eredetileg nélkül lett kiépítve:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez társított fiók használatával.

2. Navigáljon a kívánt virtuálisgép-méretezési csoporthoz.

3. A **rendszer által hozzárendelt**, **állapot** területen válassza a be lehetőséget, majd kattintson **a** **Mentés** gombra:

   ![Képernyőfelvétel: az "identitás (előzetes verzió)" oldal a "rendszer kiosztva" beállítással, a "be" állapottal és a "Mentés" gomb kiemelésével jelenik meg.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Rendszerhez rendelt felügyelt identitás eltávolítása virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amelynek már nincs szüksége a rendszerhez rendelt felügyelt identitásra:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez társított fiók használatával. Győződjön meg arról is, hogy a fiókja olyan szerepkörhöz tartozik, amely írási jogosultságot biztosít a virtuálisgép-méretezési csoport számára.

2. Navigáljon a kívánt virtuálisgép-méretezési csoporthoz.

3. A **rendszer által hozzárendelt**, állapot **területen válassza ki** , majd kattintson a **Mentés** gombra: 

   ![Konfiguráció lap képernyőképe](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználóhoz rendelt felügyelt identitást egy virtuálisgép-méretezési csoportból a Azure Portal használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

A Azure Portal jelenleg nem támogatja a felhasználó által hozzárendelt felügyelt identitások hozzárendelését egy virtuálisgép-méretezési csoport létrehozása során. Ehelyett a következő virtuálisgép-méretezési csoport létrehozási rövid útmutatója című cikkből megtudhatja, hogyan hozhat létre először virtuálisgép-méretezési készletet, majd folytassa a következő szakasszal, amely részletesen ismerteti a felhasználóhoz rendelt felügyelt identitás hozzárendelésének részleteit:

- [Virtuálisgép-méretezési csoport létrehozása a Azure Portalban](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő virtuálisgép-méretezési csoportokhoz

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez társított fiók használatával.
2. Navigáljon a kívánt virtuálisgép-méretezési csoporthoz, és kattintson az **identitás**, a **felhasználó hozzárendelve** , majd a **\+ Hozzáadás** elemre.

   ![Képernyőkép, amely a "felhasználó által hozzárendelt" nevű "Identity" oldalt jeleníti meg, és a "Hozzáadás" gomb ki van emelve.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kattintson arra a felhasználó által hozzárendelt identitásra, amelyet hozzá szeretne adni a virtuálisgép-méretezési csoporthoz, majd kattintson a **Hozzáadás** gombra.
   
   ![Felhasználó által hozzárendelt identitás hozzáadása a VMSS-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása virtuálisgép-méretezési csoportból

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy olyan fiókkal, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.
2. Navigáljon a kívánt virtuálisgép-méretezési csoporthoz, és kattintson az **identitás**, a **felhasználó hozzárendelése** elemre, a törölni kívánt, felhasználóhoz rendelt felügyelt identitás nevére, majd kattintson az **Eltávolítás** gombra (a megerősítő ablaktáblán kattintson az **Igen** gombra).

   ![Felhasználó által hozzárendelt identitás eltávolítása egy VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Következő lépések

- A Azure Portal használatával adjon meg egy Azure-beli virtuálisgép-méretezési csoport felügyelt identitás [-hozzáférését egy másik Azure-erőforráshoz](howto-assign-access-portal.md).
