---
title: 'Rövid útmutató: Új szabályzat-hozzárendelés a Python használatával'
description: Ebben a rövid útmutatóban a Python használatával fog létrehozni egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c9a0d6bb00d82748505304264aeaefa409c4b06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379400"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Rövid útmutató: Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a Python használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban létrehoz egy szabályzat-hozzárendelést a felügyelt lemezeket nem használó virtuális gépek azonosításához. Ha elkészült, azonosíthatja a nem megfelelő _virtuális gépeket._

A Python-kódtár az Azure-erőforrások parancssorból vagy szkriptekkel való kezelésére szolgál. Ez az útmutató azt ismerteti, hogyan használható a Python-kódtár szabályzat-hozzárendelések létrehozására.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>A Szabályzattár hozzáadása

Ahhoz, hogy a Python Azure Policy, a kódtárat hozzá kell adni. Ez a kódtár mindenhol működik, ahol a Python használható, beleértve a [Bash on Windows 10](/windows/wsl/install-win10) helyileg telepítettet.

1. Ellenőrizze, hogy telepítve van-e a legújabb Python **(legalább 3.8-as verzió).** Ha még nincs telepítve, töltse le a [Python.org.](https://www.python.org/downloads/)

1. Ellenőrizze, hogy telepítve van-e a legújabb Azure CLI **(legalább 2.5.1).** Ha még nincs telepítve, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Az alábbi példákban az Azure CLI-nek kell engedélyeznie, hogy a Python a **CLI-alapú** hitelesítést használja. További információ az egyéb lehetőségekről: [Hitelesítés a Pythonhoz készült Azure felügyeleti kódtárakkal.](/azure/developer/python/azure-sdk-authenticate)

1. Hitelesítés az Azure CLI használatával.

   ```azurecli
   az login
   ```

1. A választott Python-környezetben telepítse a szükséges kódtárakat a Azure Policy:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Ha a Python minden felhasználó számára telepítve van, ezeket a parancsokat emelt szintű konzolról kell futtatni.

1. Ellenőrizze, hogy a kódtárak telepítve vannak-e. `azure-mgmt-policyinsights`**0.5.0-s** vagy újabb, `azure-mgmt-resource` **9.0.0-s** vagy újabb, `azure-cli-core` és **2.5.0-s** vagy újabb verziónak kell lennie.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban létrehoz egy szabályzat-hozzárendelést, és hozzárendeli a Felügyelt lemezeket **nem** használó virtuális gépek naplózása ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) definíciót. Ez a szabályzatdefiníció azonosítja azokat az erőforrásokat, amelyek nem kompatibilisek a szabályzatdefinícióban meghatározott feltételekkel.

Futtassa a következő kódot egy új szabályzat-hozzárendelés létrehozásához:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Az előző parancsok a következő információkat használják:

Hozzárendelés részletei:
- **display_name** – A szabályzat-hozzárendelés megjelenítendő neve. Ebben az esetben felügyelt lemezeket nem használó virtuális gépek naplózása _hozzárendelést használ._
- **policy_definition_id** – A szabályzatdefiníció elérési útja, amely alapján létrehozza a hozzárendelést. Ebben az esetben ez a felügyelt lemezeket nem használó virtuális gépek naplózása szabályzatdefiníció _azonosítója._ Ebben a példában a szabályzatdefiníció egy beépített, és az elérési út nem tartalmazza a felügyeleti csoport vagy az előfizetés adatait.
- **scope** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrásokon vagy erőforráscsoporton lesz kényszerítve. Ez a felügyeleti csoporttól az egyes erőforrásokon át is lehet. Ne `{scope}` feledjük a helyére az alábbi minták egyikét:
  - Felügyeleti csoport: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Előfizetés: `/subscriptions/{subscriptionId}`
  - Erőforráscsoport: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Erőforrás: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description** – A szabályzat tevékenységének vagy a hatókörhöz való hozzárendelésének mélyebb magyarázata.

Hozzárendelés létrehozása:

- Hatókör – Ez a hatókör határozza meg, hogy a rendszer hová menti a szabályzat-hozzárendelést. A hozzárendelés részleteiben beállított hatókörnek ebben a hatókörben kell lennie.
- Name – A hozzárendelés tényleges neve. A fenti példában az _audit-vm-manageddisks_ nevet használtuk.
- Szabályzat-hozzárendelés – Az előző lépésben létrehozott Python **PolicyAssignment** objektum.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának meghatározásához.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

A következő információkkal azonosíthatja a létrehozott szabályzat-hozzárendelés szempontjából nem megfelelő erőforrásokat. Futtassa az alábbi kódot:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Cserélje `{subscriptionId}` le a helyére azt az előfizetést, amely a szabályzat-hozzárendelés megfelelőségi eredményeit szeretné látni. Az egyéb hatókörök listájáért és az adatok összegzésének módjaiért lásd: [Szabályzatállapot-metódusok.](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods)

Az eredmények a következő példához hasonlók:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Az eredmények megegyeznek a  szabályzat-hozzárendelés Erőforrás-megfelelőség lapján látható Azure Portal nézetben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához használja a következő parancsot:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Cserélje `{scope}` le a helyére ugyanazt a hatókört, mint a szabályzat-hozzárendelés létrehozásához.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni az új erőforrások megfelelőnek minősülő szabályzatdefiníciók hozzárendelésről, folytassa a következő oktatóanyagokkal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
