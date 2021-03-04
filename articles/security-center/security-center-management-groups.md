---
title: Előfizetések rendszerezése felügyeleti csoportokra és szerepkörök társítása a felhasználókhoz Azure Security Center
description: Ismerje meg, hogyan rendezheti az Azure-előfizetéseit a Azure Security Center felügyeleti csoportjaiba, és hogyan rendelhet hozzá szerepköröket a szervezet felhasználóihoz
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099386"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Előfizetések rendszerezése felügyeleti csoportokra és szerepkörök társítása a felhasználókhoz

Ez a cikk bemutatja, hogyan kezelheti a szervezete biztonsági állapotát a Azure Active Directory bérlőhöz kapcsolódó összes Azure-előfizetésre vonatkozó biztonsági szabályzatok alkalmazásával.

Ha szeretné megtekinteni az Azure AD-bérlőben regisztrált összes előfizetés biztonsági állapotát, szükséges, hogy a legfelső szintű felügyeleti csoportban hozzá legyen rendelve egy megfelelő olvasási engedéllyel rendelkező Azure-szerepkör.


## <a name="organize-your-subscriptions-into-management-groups"></a>Előfizetések rendszerezése felügyeleti csoportokba

### <a name="introduction-to-management-groups"></a>A felügyeleti csoportok bemutatása

Az Azure felügyeleti csoportjai hatékonyan kezelhetik a hozzáférést, a szabályzatokat és a jelentéskészítést az előfizetések csoportjaira vonatkozóan, valamint hatékonyan kezelhetik a teljes Azure-birtokot a gyökérszintű felügyeleti csoport műveleteinek végrehajtásával. Az előfizetéseket felügyeleti csoportokba rendezheti, és az irányítási szabályzatokat alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. 

Minden egyes Azure AD-bérlő egyetlen legfelső szintű felügyeleti csoportot kap, amelynek neve a **gyökérszintű felügyeleti csoport**. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a csoport lehetővé teszi, hogy a globális házirendek és az Azure-beli szerepkör-hozzárendelések a címtár szintjén legyenek alkalmazva. 

A felügyeleti csoport automatikusan létrejön, ha az alábbi műveletek bármelyikét végrehajtja: 
- Nyissa meg **Management groups** a [Azure Portalban](https://portal.azure.com).
- Hozzon létre egy felügyeleti csoportot egy API-hívással.
- Hozzon létre egy felügyeleti csoportot a PowerShell használatával. A PowerShell-utasításokért lásd: [felügyeleti csoportok létrehozása erőforrás-és szervezeti felügyelethez](../governance/management-groups/create-management-group-portal.md).

A felügyeleti csoportoknak nincs szükségük Security Center bevezetésére, de javasoljuk, hogy hozzon létre legalább egyet, hogy a gyökérszintű felügyeleti csoport létre legyen hozva. A csoport létrehozása után az Azure AD-bérlő alá tartozó összes előfizetés hozzá lesz kapcsolva. 


A felügyeleti csoportok részletes áttekintését az [erőforrások rendszerezése az Azure felügyeleti csoportokkal](../governance/management-groups/overview.md) című cikkben találja.

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Felügyeleti csoportok megtekintése és létrehozása a Azure Portal

1. A [Azure Portal](https://portal.azure.com)a felső sávban található keresőmező használatával keresse meg és nyissa meg **Management groups**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="A felügyeleti csoportok elérése":::

    Megjelenik a felügyeleti csoportok listája.

1. Felügyeleti csoport létrehozásához válassza a **felügyeleti csoport hozzáadása** elemet, adja meg a megfelelő adatokat, majd kattintson a **Mentés** gombra.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Felügyeleti csoport hozzáadása az Azure-hoz":::

    - A **felügyeleti csoport azonosítója** a címtár egyedi azonosítója, amely a parancsok ezen a felügyeleti csoporton való elküldésére szolgál. Ez az azonosító nem szerkeszthető a létrehozás után, mivel az egész Azure-rendszeren a csoport azonosítására szolgál. 
    - A megjelenítendő név mező a Azure Portal belül megjelenő név. A felügyeleti csoport létrehozásakor egy külön megjelenítendő név nem választható mező, és bármikor módosítható.  


### <a name="add-subscriptions-to-a-management-group"></a>Előfizetés hozzáadása egy felügyeleti csoporthoz
Előfizetéseket adhat hozzá a létrehozott felügyeleti csoporthoz.

1. A **Management groups** alatt válassza ki az előfizetéséhez tartozó felügyeleti csoportot.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Válasszon ki egy felügyeleti csoportot az előfizetéséhez":::

1. A csoport oldalának megnyitásakor válassza a **részletek** lehetőséget.

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Felügyeleti csoport részleteket tartalmazó oldalának megnyitása":::

1. A csoport részletei lapon válassza az **előfizetés hozzáadása** lehetőséget, majd válassza ki az előfizetéseket, és válassza a **Mentés** lehetőséget. Ismételje meg a műveletet, amíg hozzá nem adta az összes előfizetést a hatókörben.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Előfizetés hozzáadása egy felügyeleti csoporthoz":::
   > [!IMPORTANT]
   > A felügyeleti csoportok előfizetéseket és alárendelt felügyeleti csoportokat is tartalmazhatnak. Amikor egy felhasználóhoz egy Azure-szerepkört rendel a fölérendelt felügyeleti csoporthoz, a gyermek-felügyeleti csoport előfizetései öröklik a hozzáférést. A szülő felügyeleti csoportban beállított házirendeket a gyermekek is öröklik. 



## <a name="assign-azure-roles-to-other-users"></a>Azure-szerepkörök kiosztása más felhasználóknak

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Azure-szerepköröket rendelhet a felhasználókhoz a Azure Portalon keresztül: 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. A felügyeleti csoportok megtekintéséhez válassza a **minden szolgáltatás** lehetőséget az Azure főmenüjében, majd válassza a **Management groups** lehetőséget.
1.  Válasszon ki egy felügyeleti csoportot, és válassza a **részletek** lehetőséget.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Management Groups részletek képernyőképe":::

1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd a **szerepkör-hozzárendelések** elemet.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. Válassza ki a hozzárendelni kívánt szerepkört és a felhasználót, majd kattintson a **Mentés** gombra.  
   
   ![Biztonsági olvasó szerepkör hozzáadása képernyőkép](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Azure-szerepkörök társítása a felhasználókhoz a PowerShell használatával: 
1. Telepítse a [Azure PowerShell](/powershell/azure/install-az-ps).
2. Futtassa az alábbi parancsot: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Ha a rendszer kéri, jelentkezzen be a globális rendszergazdai hitelesítő adatokkal. 

    ![Bejelentkezés a képernyőn](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. A következő parancs futtatásával adja meg az olvasói szerepkör engedélyeit:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. A szerepkör eltávolításához használja a következő parancsot: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása 

Miután hozzárendelte az Azure-szerepköröket a felhasználókhoz, a bérlői rendszergazdának el kell távolítania magát a felhasználói hozzáférés rendszergazdai szerepkörből.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com).

2. A navigációs listában válassza a **Azure Active Directory** lehetőséget, majd válassza a **Tulajdonságok** lehetőséget.

3. Az **Azure-erőforrások hozzáférés-kezelés** területén állítsa a **nem** értékre a kapcsolót.

4. A beállítás mentéséhez válassza a **Mentés** lehetőséget.



## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan rendezheti az előfizetéseket felügyeleti csoportokba, és hogyan rendelhet hozzá szerepköröket a felhasználókhoz. A kapcsolódó információk a következő témakörben találhatók:

- [Engedélyek az Azure Security Centerben](security-center-permissions.md)