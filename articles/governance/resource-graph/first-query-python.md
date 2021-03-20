---
title: 'Rövid útmutató: az első Python-lekérdezés'
description: Ebben a rövid útmutatóban a következő lépésekkel engedélyezheti a Pythonhoz készült Resource Graph-függvénytárat, és futtathatja az első lekérdezést.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 6fc7b8f6a6fa7cde8d10dec6a4b2b834d41325e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920122"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Python használatával

Az Azure Resource Graph használatának első lépéseként ellenőriznie kell, hogy telepítve vannak-e a Pythonhoz szükséges kódtárak. Ez a rövid útmutató végigvezeti a kódtárak Python-telepítéshez való hozzáadásának folyamatán.

A folyamat végén hozzáadta a kódtárakat a Python-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Az erőforrás-gráf könyvtárának hozzáadása

Ha engedélyezni szeretné a Python számára az Azure Resource Graph lekérdezését, hozzá kell adnia a könyvtárat. Ez a könyvtár működik, ahol a Python használható, beleértve [a Windows 10 vagy a](/windows/wsl/install-win10) helyileg telepített bash-t is.

1. Győződjön meg arról, hogy a legújabb Python telepítve van (legalább **3,8**). Ha még nincs telepítve, töltse le a következő címen: [Python.org](https://www.python.org/downloads/).

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.5.1**). Ha még nincs telepítve, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

   > [!NOTE]
   > Az Azure CLI-vel engedélyezni kell a Python használatát a **CLI-alapú hitelesítés** használatához az alábbi példákban. További információ az egyéb lehetőségekről: [hitelesítés a Pythonhoz készült Azure Management librarys használatával](/azure/developer/python/azure-sdk-authenticate).

1. Hitelesítés az Azure CLI-n keresztül.

   ```azurecli
   az login
   ```

1. A kívánt Python-környezetben telepítse a szükséges kódtárakat az Azure Resource Graph számára:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Ha a Python telepítve van az összes felhasználó számára, akkor ezeket a parancsokat emelt szintű konzolról kell futtatni.

1. Ellenőrizze, hogy telepítve vannak-e a kódtárak. `azure-mgmt-resourcegraph`**2.0.0** vagy magasabbnak kell lennie, vagy 9.0.0 vagy magasabbnak kell lennie `azure-mgmt-resource` , és  `azure-cli-core` legalább **2.5.0** -nek kell lennie.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Ha a Python-függvénytárak hozzá lettek adva a környezethez, itt az ideje, hogy kipróbáljon egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

1. Futtassa az első Azure Resource Graph-lekérdezést a telepített tárak és a `resources` metódus használatával:

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

1. Frissítse a hívást, `getresources` és módosítsa a lekérdezést a `order by` **Name (név** ) tulajdonságra:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Frissítse a hívást, `getresources` és módosítsa a lekérdezést a `order by` Name ( **név** ) tulajdonságra, majd `limit` az első öt találatra:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat a Python-környezetből, ezt a következő paranccsal teheti meg:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta az erőforrás-gráf kódtárait a Python-környezethez, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
