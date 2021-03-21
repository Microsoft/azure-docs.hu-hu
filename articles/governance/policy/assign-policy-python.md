---
title: 'Rövid útmutató: új szabályzat-hozzárendelés a Pythonban'
description: Ebben a rövid útmutatóban a Python használatával hozzon létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: e600f97dafdd1040c22b6e4d9e333f638334b663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742330"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a Python használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre a felügyelt lemezeket nem használó virtuális gépek azonosításához. Ha elkészült, azonosíthatja azokat a virtuális gépeket, amelyek _nem megfelelőek_.

A Python-függvénytár az Azure-erőforrások parancssorból vagy parancsfájlokból való kezelésére szolgál. Ez az útmutató bemutatja, hogyan hozhat létre szabályzat-hozzárendelést a Python Library használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>A házirend-könyvtár hozzáadása

Ahhoz, hogy a Python működjön a Azure Policyval, a könyvtárat hozzá kell adni. Ez a könyvtár működik, ahol a Python használható, beleértve [a Windows 10 vagy a](/windows/wsl/install-win10) helyileg telepített bash-t is.

1. Győződjön meg arról, hogy a legújabb Python telepítve van (legalább **3,8**). Ha még nincs telepítve, töltse le a következő címen: [Python.org](https://www.python.org/downloads/).

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.5.1**). Ha még nincs telepítve, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

   > [!NOTE]
   > Az Azure CLI-vel engedélyezni kell a Python használatát a **CLI-alapú hitelesítés** használatához az alábbi példákban. További információ az egyéb lehetőségekről: [hitelesítés a Pythonhoz készült Azure Management librarys használatával](/azure/developer/python/azure-sdk-authenticate).

1. Hitelesítés az Azure CLI-n keresztül.

   ```azurecli
   az login
   ```

1. A kiválasztható Python-környezetben telepítse a szükséges kódtárakat a Azure Policyhoz:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Ha a Python telepítve van az összes felhasználó számára, akkor ezeket a parancsokat emelt szintű konzolról kell futtatni.

1. Ellenőrizze, hogy telepítve vannak-e a kódtárak. `azure-mgmt-policyinsights`**0.5.0** vagy magasabbnak kell lennie, vagy 9.0.0 vagy magasabbnak kell lennie `azure-mgmt-resource` , és  `azure-cli-core` legalább **2.5.0** -nek kell lennie.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli **azokat a naplózási virtuális gépeket, amelyek nem használnak Managed Disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Új szabályzat-hozzárendelés létrehozásához futtassa a következő kódot:

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
- **DISPLAY_NAME** – a házirend-hozzárendelés megjelenítendő neve. Ebben az esetben a _virtuális gépek naplózása felügyelt lemezek hozzárendelése nélkül_ történik.
- **policy_definition_id** – a házirend-definíció elérési útja, amely alapján létrehozza a hozzárendelést. Ebben az esetben ez a házirend-definíciós _virtuális gépek azonosítója, amelyek nem használnak felügyelt lemezeket_. Ebben a példában a házirend-definíció beépített, és az elérési út nem tartalmaz felügyeleti csoportot vagy előfizetési adatokat.
- **hatókör** – a hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrásokat vagy erőforrásokat kíván érvényesíteni. Egy felügyeleti csoportból egy adott erőforrásra terjedhet. Ügyeljen arra, hogy a `{scope}` következő minták egyikét cserélje le:
  - Felügyeleti csoport: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Előfizetés: `/subscriptions/{subscriptionId}`
  - Erőforráscsoport: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Erőforrás `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description (Leírás** ) – mélyebb magyarázat arról, hogy mit tesz a szabályzat, vagy miért van hozzárendelve ehhez a hatókörhöz.

Hozzárendelés létrehozása:

- Hatókör – ez a hatókör határozza meg, hogy a szabályzat-hozzárendelés hol lesz mentve. A hozzárendelés részleteiben szereplő hatókörnek léteznie kell ezen a hatókörön belül.
- Name – A hozzárendelés tényleges neve. A fenti példában az _audit-vm-manageddisks_ nevet használtuk.
- Szabályzat-hozzárendelés – az előző lépésben létrehozott Python **PolicyAssignment** objektum.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megismerése érdekében.

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

A helyére írja be azt `{subscriptionId}` az előfizetést, amelyre vonatkozóan meg szeretné jeleníteni a szabályzat-hozzárendelés megfelelőségi eredményeit. Az egyéb hatókörök és az adatösszesítési módok listáját lásd: [házirend állapotának módszerei](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

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

Az eredmények megegyeznek a szabályzat-hozzárendelés **erőforrás-megfelelőség** lapján látható Azure Portal nézetben.

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

Cserélje le `{scope}` ugyanazt a hatókört, amelyet a szabályzat-hozzárendelés létrehozásához használt.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni a szabályzat-definíciók hozzárendeléséről, hogy ellenőrizze, hogy az új erőforrások megfelelőek-e, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
