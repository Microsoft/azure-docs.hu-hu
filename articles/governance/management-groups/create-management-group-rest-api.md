---
title: 'Gyors útmutató: felügyeleti csoport létrehozása REST API'
description: Ebben a rövid útmutatóban a REST API használatával hozzon létre egy felügyeleti csoportot, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ff1487bf25945c733402ddb74d1e102bea80b4b1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592500"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Gyors útmutató: felügyeleti csoport létrehozása REST API

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Ha még nem tette meg, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient). Ez egy olyan eszköz, amely HTTP-kéréseket küld Azure Resource Manager-alapú REST API-khoz. Ehelyett használhatja a REST dokumentációban található "kipróbálás" funkciót, vagy olyan eszközt, mint például a PowerShell [meghívása – RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) vagy [Poster](https://www.postman.com).

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Létrehozás REST API

REST API esetén a [Management groups-Create vagy a Update](/rest/api/resources/managementgroups/createorupdate) Endpoint paranccsal hozzon létre egy új felügyeleti csoportot. Ebben a példában a **GroupID** felügyeleti csoport a _contoso_.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Nincs kérelem törzse

A **GroupID** egy egyedi azonosítót hoz létre. Ezt az azonosítót más parancsok használják a csoportra való hivatkozáshoz, és később nem módosíthatók.

Ha azt szeretné, hogy a felügyeleti csoport más nevet jelenítsen meg a Azure Portalon belül, adja hozzá a **Properties. DisplayName** tulajdonságot a kérelem törzséhez. Ha például a _contoso_ **GroupID** rendelkező felügyeleti csoportot és a _contoso-csoport_ megjelenítendő nevét szeretné létrehozni, használja a következő végpontot és a kérelem törzsét:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Kérelem törzse

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

Az előző példákban az új felügyeleti csoport a gyökérszintű felügyeleti csoport alatt jön létre. Másik felügyeleti csoport szülőként való megadásához használja a **Properties.Parent.id** tulajdonságot.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Kérelem törzse

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A fent létrehozott felügyeleti csoport eltávolításához használja a [Management groups-delete](/rest/api/resources/managementgroups/delete) végpontot:

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Nincs kérelem törzse

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)