---
title: 'Rövid útmutató: Az első Python-lekérdezés'
description: Ebben a rövid útmutatóban a lépéseket követve engedélyezheti Resource Graph Pythonhoz készült kódtárat, és futtatja az első lekérdezését.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 0bae0639e3f9913bc47b18fbc0b1d3d9ef1ad324
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533021"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Rövid útmutató: Az első lekérdezés Resource Graph Python használatával

Az első lépés a Azure Resource Graph, hogy ellenőrizze, telepítve vannak-e a Pythonhoz szükséges kódtárak. Ez a rövid útmutató végigvezeti a kódtárak Python-telepítéshez való hozzáadásának folyamatán.

A folyamat végén hozzáadja a kódtárakat a Python-telepítéshez, és futtatja az első Resource Graph lekérdezését.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>A Resource Graph hozzáadása

Ahhoz, hogy a Python Azure Resource Graph lekérdezni, a kódtárat hozzá kell adni. Ez a kódtár mindenhol működik, ahol a Python használható, beleértve a [Bash on Windows 10](/windows/wsl/install-win10) helyileg telepítettet.

1. Ellenőrizze, hogy telepítve van-e a legújabb Python **(legalább 3.8-as verzió).** Ha még nincs telepítve, töltse le a [Python.org.](https://www.python.org/downloads/)

1. Ellenőrizze, hogy telepítve van-e a legújabb Azure CLI **(legalább 2.5.1).** Ha még nincs telepítve, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Az alábbi példákban az Azure CLI-nek kell engedélyeznie, hogy a Python a **CLI-alapú** hitelesítést használja. További információ az egyéb lehetőségekről: [Hitelesítés a Pythonhoz készült Azure felügyeleti kódtárakkal.](/azure/developer/python/azure-sdk-authenticate)

1. Hitelesítés az Azure CLI használatával.

   ```azurecli
   az login
   ```

1. A választott Python-környezetben telepítse a szükséges kódtárakat a Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Ha a Python minden felhasználó számára telepítve van, ezeket a parancsokat emelt szintű konzolról kell futtatni.

1. Ellenőrizze, hogy a kódtárak telepítve vannak-e. `azure-mgmt-resourcegraph`**2.0.0-s** vagy újabb, `azure-mgmt-resource` **9.0.0-s** vagy újabb, `azure-cli-core` illetve **2.5.0-s** vagy újabb verziónak kell lennie.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Most, hogy a választott környezethez hozzáadta a Python-kódtárakat, ideje kipróbálni egy egyszerű Resource Graph lekérdezést. A lekérdezés visszaadja az első  öt Azure-erőforrást az egyes erőforrások nevével **és** erőforrástípusával.

1. Futtassa az első Azure Resource Graph a telepített kódtárak és a metódus `resources` használatával:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a hívását, `getresources` és módosítsa a lekérdezést a Name `order by` **(Név) tulajdonságra:**

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancsrend először korlátozza a lekérdezési eredményeket, majd megrendeli azokat.

1. Frissítse a hívását, és módosítsa a lekérdezést a Name (Név) tulajdonságra, majd az `getresources` `order by` első öt  `limit` találatra:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Ha az utolsó lekérdezés többször is lefut, feltéve, hogy a környezetben semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** (Név) tulajdonság szerint vannak megrendelve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat a Python-környezetből, ezt a következő paranccsal használhatja:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta a Resource Graph kódtárakat a Python-környezethez, és futtatta az első lekérdezését. Ha többet szeretne megtudni a Resource Graph nyelvről, folytassa a lekérdezési nyelv részleteit tartalmazó oldalon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
