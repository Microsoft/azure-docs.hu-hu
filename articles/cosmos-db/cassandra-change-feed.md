---
title: A Azure Cosmos DB API-hoz tartozó hírcsatorna módosítása a Cassandra-hoz
description: Megtudhatja, hogyan használhatja az adatváltozásokat a Azure Cosmos DB API-ban a Cassandra paranccsal az adatváltozások beszerzéséhez.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 10f037dddcce43a1e023982af816660bd325d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605093"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>A Azure Cosmos DB API-hoz tartozó hírcsatorna módosítása a Cassandra-hoz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A "Cassandra" Azure Cosmos DB API-n keresztüli [adatcsatorna](change-feed.md) -támogatás módosítása a Cassandra Query Language (CQL) lekérdezési predikátumán keresztül érhető el. Ezen predikátum-feltételek használatával lekérdezheti a Change feed API-t. Az alkalmazások az elsődleges kulcs (más néven partíciós kulcs) használatával is beszerezhetik a tábla módosításait, ahogy az a CQL-ben szükséges. Ezután az eredmények alapján további műveleteket hajthat végre. A tábla sorainak módosításait a rendszer a módosítási idő sorrendjében rögzíti, a rendezési sorrendet pedig partíciós kulcsként.

Az alábbi példa azt mutatja be, hogyan kérhető le egy módosítási hírcsatorna a Cassandra APIi tábla összes sorában a .NET használatával. Az predikátum COSMOS_CHANGEFEED_START_TIME () közvetlenül a CQL-n belül használható a változási csatornán lévő elemek lekérdezésére a megadott kezdési időpontból (ebben az esetben az aktuális datetime értéknél). A teljes minta letölthető [a C# nyelvhez itt és](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) a Javához [itt](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java).

Az egyes iterációkban a lekérdezés az utolsó pont módosításainál folytatódik, a lapozási állapot használatával. A tábla új változásainak folyamatos streamjét láthatjuk. Ekkor megjelenik a beszúrt vagy frissített sorok változásai. A Cassandra API változási csatornán keresztüli törlési műveletek figyelése jelenleg nem támogatott.

# <a name="java"></a>[Java](#tab/java)

```java
    Session cassandraSession = utils.getSession();

    try {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
        LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
        String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
            + dtf.format(now)+ "'";
        
        byte[] token=null; 
        System.out.println(query); 
        while(true)
        {
            SimpleStatement st=new  SimpleStatement(query);
            st.setFetchSize(100);
            if(token!=null)
                st.setPagingStateUnsafe(token);
            
            ResultSet result=cassandraSession.execute(st) ;
            token=result.getExecutionInfo().getPagingState().toBytes();
            
            for(Row row:result)
            {
                System.out.println(row.getString("user_name"));
            }
        }
    } finally {
        utils.close();
        LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
    }
```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Ahhoz, hogy az elsődleges kulcs egyetlen sorba módosítsa a módosításokat, felveheti az elsődleges kulcsot a lekérdezésbe. Az alábbi példa bemutatja, hogyan követheti nyomon a sor változásait, ahol "user_id = 1"

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                       + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások érvényesek a Change feed Cassandra API használatával történő használatakor:

* A lapkák és a frissítések jelenleg támogatottak. A törlési művelet még nem támogatott. Megkerülő megoldásként hozzáadhat egy lágy jelölőt a törölt sorokhoz. Például adjon hozzá egy mezőt a "törölt" nevű sorban, és állítsa "true" (igaz) értékre.
* A legutóbbi frissítés az alapszintű SQL API-ban és az entitás közbenső frissítései nem érhetők el.


## <a name="error-handling"></a>Hibakezelés

A következő hibakódok és üzenetek támogatottak a Cassandra API változási csatornájának használatakor:

* **Http-hibakód 429** – ha a módosítási csatornára korlátozott a ráta, üres lapot ad vissza.

## <a name="next-steps"></a>Következő lépések

* [Azure Cosmos DB Cassandra API erőforrások kezelése Azure Resource Manager sablonok használatával](./templates-samples-cassandra.md)