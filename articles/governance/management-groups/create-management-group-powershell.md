---
title: 'Rövid útmutató: Felügyeleti csoport létrehozása Azure PowerShell'
description: Ebben a rövid útmutatóban a Azure PowerShell egy felügyeleti csoportot hoz létre az erőforrások erőforrás-hierarchiába való rendszerezéséhez.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0291bb2bfb439ad09531066f6bad4e20a3f4c6bd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535969"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Rövid útmutató: Felügyeleti csoport létrehozása Azure PowerShell

A felügyeleti csoportok olyan tárolók, amelyek segítségével több előfizetés hozzáférését, szabályzatát és megfelelőségét kezelheti. Hozza létre ezeket a tárolókat egy hatékony és [](../policy/overview.md) hatékony hierarchia létrehozásához, amely a Azure Policy [azure szerepköralapú hozzáférés-vezérléssel együtt használható.](../../role-based-access-control/overview.md) További információ a felügyeleti csoportokról: [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal.](overview.md)

A címtárban létrehozott első felügyeleti csoport létrehozása akár 15 percet is igénybe vehet. Vannak olyan folyamatok, amelyek az első alkalommal futnak, amikor beállítják a felügyeleticsoport-szolgáltatást az Azure-ban a címtárhoz. A folyamat befejezésekor értesítést kap. További információkért lásd a felügyeleti [csoportok kezdeti beállítását.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Mielőtt elkezdené, győződjön meg arról, hogy a Azure PowerShell legújabb verziója van telepítve. Részletes [információkért Azure PowerShell Modul](/powershell/azure/install-az-ps) telepítése.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoporthoz írási engedély lenne hozzárendelve a felhasználóhoz, ha a [hierarchiavédelem](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nincs engedélyezve. Ez az új felügyeleti csoport a gyökér [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) felügyeleti csoport vagy az alapértelmezett felügyeleti csoport gyermeke lesz, és a létrehozó "Tulajdonos" szerepkör-hozzárendelést kap. A felügyeleticsoport-szolgáltatás lehetővé teszi ezt a képességet, így a szerepkör-hozzárendelésre nincs szükség a gyökérszinten. Létrehozáskor egyetlen felhasználó sem fér hozzá a gyökér felügyeleti csoporthoz. Annak érdekében, hogy ne kelljen megtalálni az Azure AD globális rendszergazdáit a felügyeleti csoportok használatának elkezdésénél, engedélyezhetjük a kezdeti felügyeleti csoportok létrehozását a gyökérszinten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Létrehozás a Azure PowerShell

A PowerShellhez használja a [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) parancsmagot egy új felügyeleti csoport létrehozásához. Ebben a példában a **GroupName felügyeleti csoport** _Contoso._

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

A **GroupName** egy egyedi azonosító létrehozása. Ezt az azonosítót más parancsok is használják a csoportra való hivatkozáshoz, és később nem módosítható.

Ha azt szeretné, hogy a felügyeleti csoport más nevet jelenítsen meg a Azure Portal, adja hozzá a **DisplayName paramétert.** Ha például a Contoso GroupName (Contoso) csoportnévvel és a "Contoso Group" megjelenített névvel (GroupName) hoz létre felügyeleti csoportot, használja a következő parancsmagot:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport a gyökér szintű felügyeleti csoport alatt jön létre. Ha másik felügyeleti csoportot ad meg szülőként, használja a **ParentId paramétert.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A fent létrehozott felügyeleti csoport eltávolításához használja a [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup) parancsmagot:

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport előfizetéseket vagy más felügyeleti csoportokat is képes tartani.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezelésével kapcsolatban, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)
