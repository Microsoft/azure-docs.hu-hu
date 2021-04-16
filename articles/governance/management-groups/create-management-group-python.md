---
title: 'Rövid útmutató: Felügyeleti csoport létrehozása a Python használatával'
description: Ebben a rövid útmutatóban a Python használatával hoz létre egy felügyeleti csoportot, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9aec47e067ca62f4902df2dafb6a5d6d50a26d0e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533176"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Rövid útmutató: Felügyeleti csoport létrehozása a Python használatával

A felügyeleti csoportok olyan tárolók, amelyek segítségével több előfizetés hozzáférését, szabályzatát és megfelelőségét kezelheti. Hozza létre ezeket a tárolókat egy hatékony és [](../policy/overview.md) hatékony hierarchia létrehozásához, amely a Azure Policy azure-beli szerepköralapú [hozzáférés-vezérléssel együtt használható.](../../role-based-access-control/overview.md) További információ a felügyeleti csoportokról: [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal.](overview.md)

A címtárban létrehozott első felügyeleti csoport létrehozása akár 15 percet is igénybe vehet. Vannak olyan folyamatok, amelyek az első alkalommal futnak, amikor beállítják a felügyeleticsoport-szolgáltatást az Azure-ban a címtárhoz. A folyamat befejezésekor értesítést kap. További információkért lásd a [felügyeleti csoportok kezdeti beállítását.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoporthoz írási engedély lenne hozzárendelve, ha a [hierarchiavédelem](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nincs engedélyezve. Ez az új felügyeleti csoport a gyökér [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) felügyeleti csoport vagy az alapértelmezett felügyeleti csoport gyermeke lesz, és a létrehozó "Tulajdonos" szerepkör-hozzárendelést kap. A felügyeleticsoport-szolgáltatás lehetővé teszi ezt a képességet, így a gyökérszinten nincs szükség szerepkör-hozzárendelésre. Létrehozáskor egyetlen felhasználó sem fér hozzá a gyökér felügyeleti csoporthoz. Annak érdekében, hogy ne kelljen megtalálni az Azure AD globális rendszergazdáit a felügyeleti csoportok használatának elkezdésében, engedélyezhetjük a kezdeti felügyeleti csoportok gyökérszinten való létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>A Resource Graph hozzáadása

Ahhoz, hogy a Python kezelni tudja a felügyeleti csoportokat, a kódtárat hozzá kell adni. Ez a kódtár mindenhol működik, ahol a Python használható, beleértve a [Bash on Windows 10](/windows/wsl/install-win10) helyileg telepítettet.

1. Ellenőrizze, hogy telepítve van-e a legújabb Python **(legalább 3.8).** Ha még nincs telepítve, töltse le a [Python.org.](https://www.python.org/downloads/)

1. Ellenőrizze, hogy telepítve van-e a legújabb Azure CLI **(legalább 2.5.1).** Ha még nincs telepítve, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Az alábbi példákban az Azure CLI-nek kell engedélyeznie, hogy a Python **a CLI-alapú** hitelesítést használja. További információ az egyéb lehetőségekről: [Hitelesítés a Pythonhoz készült Azure felügyeleti kódtárakkal.](/azure/developer/python/azure-sdk-authenticate)

1. Hitelesítés az Azure CLI használatával.

   ```azurecli
   az login
   ```

1. A választott Python-környezetben telepítse a felügyeleti csoportokhoz szükséges kódtárakat:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Ha a Python minden felhasználó számára telepítve van, ezeket a parancsokat emelt szintű konzolról kell futtatni.

1. Ellenőrizze, hogy a kódtárak telepítve vannak-e. `azure-mgmt-managementgroups`**0.2.0-s** vagy újabb, `azure-mgmt-resource` **9.0.0-s** vagy újabb, `azure-cli-core` és **2.5.0-s** vagy újabb verziónak kell lennie.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>A felügyeleti csoport létrehozása

1. Hozza létre a Python-szkriptet, és mentse a következő forrást a `mgCreate.py` következőként:

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

1. Hitelesítés az Azure CLI-val `az login` a használatával.

1. Írja be a következő parancsot a terminálba:

   ```bash
   py mgCreate.py
   ```

A felügyeleti csoport létrehozásának eredménye objektumként lesz a `LROPoller` konzolon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat a Python-környezetből, ezt a következő paranccsal használhatja:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport előfizetéseket vagy más felügyeleti csoportokat is képes tartani.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezelésével kapcsolatban, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)
