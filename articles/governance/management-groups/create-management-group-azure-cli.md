---
title: 'Gyors útmutató: felügyeleti csoport létrehozása az Azure CLI-vel'
description: Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy felügyeleti csoportot az erőforrások erőforrás-hierarchiába rendezéséhez.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592449"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Gyors útmutató: felügyeleti csoport létrehozása az Azure CLI-vel

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Ehhez a rövid útmutatóhoz a parancssori felület helyi telepítéséhez és használatához az Azure CLI 2.0.76 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Létrehozás az Azure CLI-ben

Az Azure CLI esetében használja az az [Account Management-Group Create](/cli/azure/account/management-group#az_account_management_group_create) parancsot egy új felügyeleti csoport létrehozásához. Ebben a példában a felügyeleti csoport **neve** _contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

A **név** a létrehozandó egyedi azonosító. Ezt az azonosítót más parancsok használják a csoportra való hivatkozáshoz, és később nem módosíthatók.

Ha azt szeretné, hogy a felügyeleti csoport más nevet jelenítsen meg a Azure Portalon belül, adja hozzá a **Display-Name** paramétert. Ha például egy olyan felügyeleti csoportot szeretne létrehozni a contoso csoportnév-vel és a "contoso Group" névvel, amely a következő parancsot használja:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport a gyökérszintű felügyeleti csoport alatt jön létre. Másik felügyeleti csoport szülőként való megadásához használja a **szülő** paramétert, és adja meg a szülő csoport nevét.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A fent létrehozott felügyeleti csoport eltávolításához használja az az [Account Management-Group delete](/cli/azure/account/management-group#az_account_management_group_delete) parancsot:

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)