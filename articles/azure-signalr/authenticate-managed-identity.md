---
title: Felügyelt identitás hitelesítése Azure Active Directory
description: Ez a cikk a felügyelt identitásnak a Azure Active Directory segítségével történő hitelesítésével kapcsolatos információkat tartalmaz az Azure Signaler szolgáltatás eléréséhez
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092563"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Felügyelt identitás hitelesítése Azure Active Directory az Azure-beli szignáló erőforrásainak eléréséhez
Az Azure Signaler szolgáltatás támogatja a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik az Azure-beli szignáló szolgáltatás erőforrásaihoz való hozzáférést az Azure-Virtual Machines (VM), a Function apps, a Virtual Machine Scale Sets és más szolgáltatások által futtatott alkalmazások Azure AD-beli hitelesítő adataival. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure Signaler szolgáltatásokhoz való hozzáférést egy Azure-beli virtuális gép felügyelt identitásának használatával.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen
Mielőtt felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure SIgnaler szolgáltatás erőforrásainak a virtuális gépről való engedélyezéséhez, először engedélyeznie kell a felügyelt identitásokat az Azure-erőforrásokhoz a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitásnak az Azure AD-ben
Ahhoz, hogy az alkalmazásban egy felügyelt identitástól kérjen engedélyt az Azure Signaler szolgáltatásnak, először konfigurálja a felügyelt identitáshoz tartozó szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Az Azure Signaler szolgáltatás definiálja azokat a RBAC-szerepköröket, amelyek magukban foglalják a vagy a beszerzésére `AccessKey` `ClientToken` Ha a RBAC szerepkör felügyelt identitáshoz van rendelve, a felügyelt identitás a megfelelő hatókörben hozzáférést kap az Azure Signaler szolgáltatáshoz.

A RBAC szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hitelesítés a Azure Active Directory az Azure signaler szolgáltatás erőforrásainak eléréséhez](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Kapcsolódás az Azure Signaler szolgáltatáshoz felügyelt identitásokkal
Ahhoz, hogy a felügyelt identitásokkal csatlakozhasson az Azure Signaler szolgáltatáshoz, hozzá kell rendelnie a szerepkört és a megfelelő hatókört. Az ebben a szakaszban szereplő eljárás egy olyan egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és hozzáfér az Azure Signaler szolgáltatás erőforrásaihoz.

Itt egy minta Azure-beli virtuálisgép-erőforrást használunk.

1. Lépjen a **Beállítások** menüpontra, és válassza az **identitás** elemet. 
1. Válassza ki a **bekapcsolni** kívánt **állapotot** . 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Virtuális gép felügyelt identitása](./media/authenticate/identity-virtual-machine.png)

Ha engedélyezte ezt a beállítást, a rendszer létrehoz egy új szolgáltatás-identitást a Azure Active Directory (Azure AD), és az App Service gazdagépre van konfigurálva.

Most rendelje hozzá a szolgáltatás identitását egy szerepkörhöz a szükséges hatókörben az Azure Signaler szolgáltatás erőforrásaiban.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal használatával  
Ha többet szeretne megtudni az Azure-erőforrásokhoz való hozzáférés kezeléséről az Azure RBAC és a Azure Portal használatával, tekintse meg [ezt a cikket](..//role-based-access-control/role-assignments-portal.md). 

Miután meghatározta a szerepkör-hozzárendelés megfelelő hatókörét, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás hozzáférés-vezérlési (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

1. A [Azure Portal](https://portal.azure.com/)navigáljon a signaler-erőforráshoz.
1. Válassza a **Access Control (iam)** lehetőséget az Azure-jelző hozzáférés-vezérlési beállításainak megjelenítéséhez. 
1. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez. Kattintson a **Hozzáadás** gombra az eszköztáron, majd válassza a **szerepkör-hozzárendelés hozzáadása** elemet. 

    ![Hozzáadás gomb az eszköztáron](./media/authenticate/role-assignments-add-button.png)

1. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a hozzárendelni kívánt **Azure-jelző szerepkört** . 
    1. Keresse meg a **rendszerbiztonsági tag** (felhasználó, csoport, egyszerű szolgáltatásnév) megkeresését, amelyhez hozzá szeretné rendelni a szerepkört.
    1. A szerepkör-hozzárendelés mentéséhez válassza a **Mentés** lehetőséget. 

        ![Szerepkör társítása egy alkalmazáshoz](./media/authenticate/assign-role-to-application.png)

    1. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy az alkalmazás `signalr-dev` `signalr-service` a Signaler app Server szerepkörben található. 
        
        ![Szerepkör-hozzárendelési lista](./media/authenticate/role-assignment-list.png)

A hasonló lépések végrehajtásával hozzárendelhet egy szerepkört az erőforráscsoport vagy az előfizetés hatóköréhez. Miután definiálta a szerepkört és a hatókörét, tesztelheti ezt a [GitHub-helyen található](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)mintákkal.

## <a name="samples-code-while-configuring-your-app-server"></a>Minta kód az App Server konfigurálásakor

Adja hozzá a következő beállításokat, ha `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Következő lépések
- További információ a RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)?
- Az Azure-beli szerepkör-hozzárendelések Azure PowerShell, az Azure CLI vagy a REST API használatával történő hozzárendeléséről és kezeléséről a következő cikkekben talál további információt:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API](../role-based-access-control/role-assignments-rest.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure Resource Manager-sablonokkal](../role-based-access-control/role-assignments-template.md)

Tekintse meg a következő kapcsolódó cikkeket:
- [Alkalmazás hitelesítése Azure Active Directory az Azure Signaler szolgáltatás eléréséhez](authenticate-application.md)
- [Az Azure Signaler szolgáltatáshoz való hozzáférés engedélyezése Azure Active Directory használatával](authorize-access-azure-active-directory.md)