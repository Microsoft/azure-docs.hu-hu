---
title: Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB Python-jegyzetfüzetekben (előzetes verzió)
description: Ismerje meg, hogyan használhatók a beépített parancsok és szolgáltatások a Azure Cosmos DB beépített Python-jegyzetfüzeteit használó gyakori műveletek végrehajtásához.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: b89fcf32ed033f359b4db601e36cc69bb899944d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165824"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB Python-jegyzetfüzetekben (előzetes verzió)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB beépített Jupyter notebookok lehetővé teszik az adatok elemzését és megjelenítését a Azure Portal. Ez a cikk azt ismerteti, hogyan használhatók a beépített notebook-parancsok és-funkciók a gyakori műveletek végrehajtásához a Python-jegyzetfüzetekben.

## <a name="install-a-new-package"></a>Új csomag telepítése
Miután engedélyezte az Azure Cosmos-fiókok notebook-támogatását, megnyithat egy új jegyzetfüzetet, és telepítheti a csomagot.

Az új kód cellában szúrja be és futtassa a következő kódot, és cserélje le a ``PackageToBeInstalled`` kívánt Python-csomagra.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Ez a csomag minden jegyzetfüzetből elérhető lesz az Azure Cosmos-fiók munkaterületen. 

> [!TIP]
> Ha a notebookjának egyéni csomagra van szüksége, javasoljuk, hogy adjon hozzá egy cellát a jegyzetfüzetben a csomag telepítéséhez, mivel a rendszer eltávolítja a csomagokat, ha [alaphelyzetbe állítja a munkaterületet](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>SQL-lekérdezés futtatása

A ``%%sql`` Magic paranccsal [SQL-lekérdezést](sql-query-getting-started.md) futtathat a fiókjában lévő bármelyik tárolón. Használja a szintaxist:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Cserélje le a ``{database_id}`` és a ``{container_id}`` nevet az adatbázis és a tároló nevére a Cosmos-fiókban. Ha a ``--database`` és az ``--container`` argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- A Azure Cosmos DBban érvényes SQL-lekérdezéseket futtathat. A lekérdezés szövegének új sorban kell lennie.

Például: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Futtassa a ```%%sql?``` parancsot egy cellában, és tekintse meg a jegyzetfüzetben található SQL Magic parancs súgóját.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL-lekérdezés és kimenet futtatása pandák DataFrame

A ``%%sql`` lekérdezés eredményeit egy [Panda DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)is kiválaszthatja. Használja a szintaxist: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Cserélje le a ``{database_id}`` és a ``{container_id}`` nevet az adatbázis és a tároló nevére a Cosmos-fiókban. Ha a ``--database`` és az ``--container`` argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- A helyére írja be annak a ``{outputDataFrameVar}`` DataFrame-változónak a nevét, amely az eredményeket fogja tartalmazni.
- A Azure Cosmos DBban érvényes SQL-lekérdezéseket futtathat. A lekérdezés szövegének új sorban kell lennie. 

Például:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Alapértelmezett adatbázis beállítása lekérdezésekhez
Beállíthatja, hogy a rendszer az alapértelmezett adatbázis- ```%%sql``` parancsokat használja a jegyzetfüzethez. Cserélje le az ```{database_id}``` nevet az adatbázis nevére.

```python
%database {database_id}
```
Futtasson ```%database?``` egy cellában, és tekintse meg a dokumentációt a jegyzetfüzetben.

## <a name="set-default-container-for-queries"></a>Alapértelmezett tároló beállítása lekérdezésekhez
Beállíthatja, hogy a rendszer az alapértelmezett tároló- ```%%sql``` parancsokat használja a jegyzetfüzethez. Cserélje le a ```{container_id}``` nevet a tároló nevére.

```python
%container {container_id}
```
Futtasson ```%container?``` egy cellában, és tekintse meg a dokumentációt a jegyzetfüzetben.

## <a name="upload-json-items-to-a-container"></a>JSON-elemek feltöltése tárolóba
A ``%%upload`` Magic paranccsal adatok tölthetők fel egy JSON-fájlból egy megadott Azure Cosmos-tárolóba. Az elemek feltöltéséhez használja az alábbi parancsot:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Cserélje le az ``{database_id}`` és az ``{container_id}`` nevet az Azure Cosmos-fiók adatbázisának és tárolójának nevére. Ha a ``--database`` és az ``--container`` argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- Cserélje le a helyére a ``{url_location_of_file}`` JSON-fájl helyét. A fájlnak érvényes JSON-objektumokból álló tömbnek kell lennie, és a nyilvános interneten keresztül elérhetőnek kell lennie.

Például:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
A kimeneti statisztikával kiszámíthatja az elemek feltöltéséhez használt hatályos RU/s értékeit. Ha például az 25 000 RUs-t 38 másodpercen keresztül használták, a hatályos RU/s értéke 25 000 RUs/38 másodperc = 658 RU/s.

A fájlokat (például CSV-vagy JSON-fájlokat) mentheti a helyi jegyzetfüzet munkaterületre. Azt javasoljuk, hogy a fájlok mentéséhez vegyen fel egy cellát a jegyzetfüzetbe. Ezeket a fájlokat a beépített terminálról is megtekintheti a notebook-környezetben. Az "ls" parancs használatával megtekintheti a mentett fájlokat. Ezek a fájlok azonban törlődnek, ha alaphelyzetbe állítja a munkaterületet. Ezért érdemes állandó tárterületet használni, például a GitHubot vagy a Storage-fiókot a helyi munkaterület helyett.

## <a name="run-another-notebook-in-current-notebook"></a>Másik jegyzetfüzet futtatása az aktuális jegyzetfüzetben 
A ``%%run`` Magic paranccsal egy másik jegyzetfüzetet futtathat a munkaterületen az aktuális jegyzetfüzetből. Használja a szintaxist:

```python
%%run ./path/to/{notebookName}.ipynb
```
Cserélje le a helyére a ``{notebookName}`` futtatni kívánt jegyzetfüzet nevét. A notebooknak a jelenlegi "saját jegyzetfüzetek" munkaterületen kell lennie. 

## <a name="use-built-in-nteract-data-explorer"></a>Beépített nteract adatkezelő használata
A beépített [nteract adatkezelő](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) használatával szűrheti és megjelenítheti a DataFrame. A funkció engedélyezéséhez állítsa a ``pd.options.display.html.table_schema`` ``True`` ``pd.options.display.max_rows`` kívánt értékre a és a értéket (az ``pd.options.display.max_rows`` ``None`` összes eredmény megjelenítéséhez).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="nteract adatkezelő":::

## <a name="use-the-built-in-python-sdk"></a>A beépített Python SDK használata
A [Azure Cosmos db PYTHON SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 4-es verziója telepítve van, és tartalmazza az Azure Cosmos-fiók notebook-környezetében.

A beépített ``cosmos_client`` példány használatával futtasson bármely SDK-műveletet. 

Például:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Lásd: [PYTHON SDK-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> A beépített Python SDK csak az SQL (Core) API-fiókok esetében támogatott. Más API-k esetén [telepítenie kell az](#install-a-new-package) API-nak megfelelő Python-illesztőprogramot. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Egyéni példány létrehozása ``cosmos_client``
A rugalmasság érdekében létrehozhat egy egyéni példányt a ``cosmos_client`` következőhöz:

- A [kapcsolatok házirendjének](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview) testreszabása
- Futtasson műveleteket egy másik Azure Cosmos-fiókon, mint a

Az aktuális fiók kapcsolati sztringjét és elsődleges kulcsát a [környezeti változók](#access-the-account-endpoint-and-primary-key-env-variables)segítségével érheti el. 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Hozzáférés a fiók végpontja és az elsődleges kulcs env változói
Elérheti annak a fióknak a beépített végpontját és kulcsát, amelyen a jegyzetfüzet található.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> A ``COSMOS.ENDPOINT`` és a ``COSMOS.KEY`` változók csak az SQL API-ra alkalmazhatók. Más API-k esetén keresse meg a végpontot és a kulcsot az Azure Cosmos-fiókjának **kapcsolatok karakterláncok** vagy **kulcsok** paneljén.  

## <a name="reset-notebooks-workspace"></a>Jegyzetfüzetek alaphelyzetbe állítása munkaterület
Ha a jegyzetfüzetek munkaterületet az alapértelmezett beállításokra szeretné visszaállítani, válassza a parancssáv **munkaterület alaphelyzetbe** állítása lehetőséget. Ezzel eltávolítja az összes egyéni telepített csomagot, majd újraindítja a Jupyter-kiszolgálót. A jegyzetfüzeteket, a fájlokat és az Azure Cosmos-erőforrásokat nem érinti a rendszer.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Jegyzetfüzetek alaphelyzetbe állítása munkaterület":::

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
- Tudnivalók a [Azure Cosmos db PYTHON SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) -ról