---
title: 'Gyors útmutató: felügyeleti csoport létrehozása Azure PowerShell'
description: Ebben a rövid útmutatóban a Azure PowerShell használatával hozzon létre egy felügyeleti csoportot, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216cfeb6e1389793afcfd27d8785a5f912db2c97
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592534"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Gyors útmutató: felügyeleti csoport létrehozása Azure PowerShell

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Mielőtt elkezdené, győződjön meg arról, hogy a Azure PowerShell legújabb verziója van telepítve. Részletes információkért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps) .

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Létrehozás Azure PowerShell

A PowerShell esetében a [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) parancsmag használatával hozzon létre egy új felügyeleti csoportot. Ebben a példában a következő felügyeleti csoport: **Csoportnév** a _contoso_.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

A **Csoportnév** egy egyedi azonosítót hoz létre. Ezt az azonosítót más parancsok használják a csoportra való hivatkozáshoz, és később nem módosíthatók.

Ha azt szeretné, hogy a felügyeleti csoport más nevet jelenítsen meg a Azure Portalon belül, adja hozzá a **DisplayName** paramétert. Ha például egy olyan felügyeleti csoportot szeretne létrehozni a contoso csoportnév-vel és a "contoso-csoport" megjelenített nevével, amely a következő parancsmagot használja:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport a gyökérszintű felügyeleti csoport alatt jön létre. Másik felügyeleti csoport szülőként való megadásához használja a **ParentID** paramétert.

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

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)