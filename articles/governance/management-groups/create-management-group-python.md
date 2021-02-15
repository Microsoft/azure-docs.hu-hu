---
title: 'Gyors útmutató: felügyeleti csoport létrehozása a Pythonban'
description: Ebben a rövid útmutatóban a Python használatával hozzon létre egy felügyeleti csoportot az erőforrások erőforrás-hierarchiába rendezéséhez.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101758"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Gyors útmutató: felügyeleti csoport létrehozása a Pythonban

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Az erőforrás-gráf könyvtárának hozzáadása

Ahhoz, hogy a Python felügyelje a felügyeleti csoportokat, hozzá kell adni a könyvtárat. Ez a könyvtár működik, ahol a Python használható, beleértve [a Windows 10 vagy a](/windows/wsl/install-win10) helyileg telepített bash-t is.

1. Győződjön meg arról, hogy a legújabb Python telepítve van (legalább **3,8**). Ha még nincs telepítve, töltse le a következő címen: [Python.org](https://www.python.org/downloads/).

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.5.1**). Ha még nincs telepítve, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

   > [!NOTE]
   > Az Azure CLI-vel engedélyezni kell a Python használatát a **CLI-alapú hitelesítés** használatához az alábbi példákban. További információ az egyéb lehetőségekről: [hitelesítés a Pythonhoz készült Azure Management librarys használatával](/azure/developer/python/azure-sdk-authenticate).

1. Hitelesítés az Azure CLI-n keresztül.

   ```azurecli
   az login
   ```

1. A kiválasztott Python-környezetben telepítse a szükséges kódtárakat a felügyeleti csoportokhoz:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Ha a Python telepítve van az összes felhasználó számára, akkor ezeket a parancsokat emelt szintű konzolról kell futtatni.

1. Ellenőrizze, hogy telepítve vannak-e a kódtárak. `azure-mgmt-managementgroups`**0.2.0** vagy magasabbnak kell lennie, vagy 9.0.0 vagy magasabbnak kell lennie `azure-mgmt-resource` , és  `azure-cli-core` legalább **2.5.0** -nek kell lennie.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>A felügyeleti csoport létrehozása

1. Hozza létre a Python-parancsfájlt, és mentse a következő forrást `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Hitelesítés az Azure CLI-vel a használatával `az login` .

1. Adja meg a következő parancsot a terminálon:

   ```bash
   py mgCreate.py
   ```

A felügyeleti csoport létrehozásának eredménye objektumként kimenet a konzolon `LROPoller` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat a Python-környezetből, ezt a következő paranccsal teheti meg:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)