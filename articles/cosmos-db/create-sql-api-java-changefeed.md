---
title: Végpontok közötti Azure Cosmos DB Java SDK v4-alkalmazás mintájának létrehozása a hírcsatorna módosítása szolgáltatás használatával
description: Ez az útmutató végigvezeti egy olyan egyszerű Java SQL API-alkalmazáson, amely a dokumentumokat egy Azure Cosmos DB tárolóba szúrja be, miközben a változási hírcsatorna használatával megtartja a tároló anyagilag látható nézetét.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096852"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Azure Cosmos DB SQL API-t használó Java-alkalmazás létrehozása és a hírcsatorna-feldolgozó módosítása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az útmutató végigvezeti egy olyan egyszerű Java-alkalmazáson, amely a Azure Cosmos DB SQL API-t használja a dokumentumok Azure Cosmos DB tárolóba való beszúrásához, miközben a tárolóban lévő, a változási csatorna és a változási adatcsatorna használatával megtartja a tárolóra vonatkozó anyagilag látható nézetet. A Java-alkalmazás a Azure Cosmos DB SQL API-val kommunikál Azure Cosmos DB Java SDK v4 használatával.

> [!IMPORTANT]  
> Ez az oktatóanyag csak Azure Cosmos DB Java SDK v4 esetében érhető el. További információkért tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos), az Azure Cosmos db Java SDK v4 [teljesítménnyel kapcsolatos TIPPEKET](performance-tips-java-sdk-v4-sql.md), valamint Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatóját](troubleshoot-java-sdk-v4-sql.md) . Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>

## <a name="prerequisites"></a>Előfeltételek

* A Azure Cosmos DB fiókjának URI-ja és kulcsa

* Maven

* Java 8

## <a name="background"></a>Háttér

A Azure Cosmos DB változási hírcsatorna eseményvezérelt felületet biztosít a dokumentumok beszúrására reagáló műveletek elindításához. Ennek számos felhasználási felhasználása van. Az olvasási és írási nehéz alkalmazások esetében például a változási hírcsatorna fő felhasználási területe a tároló valós idejű, a dokumentumok betöltését eredményező **nézetének** létrehozása. Az anyagot tartalmazó nézet tárolója ugyanazokat az adatokat fogja tárolni, de a hatékony olvasásokra particionálja, így az alkalmazás olvasási és írási hatékonyságot is biztosít.

A Change feed-események kezelésének munkája nagy mértékben gondoskodik az SDK-ba épített Change feed Processor Library-ről. Ez a kódtár elég erős ahhoz, hogy a változási hírcsatornák eseményeit több feldolgozón is el lehessen osztani, ha erre szükség van. Mindössze annyit kell tennie, hogy megadja a Change feed Library a visszahívást.

Ez az egyszerű példa azt mutatja be, hogy a hírcsatorna-feldolgozó függvénytár egyetlen dolgozóval hozza létre és törli a dokumentumokat egy anyagbeli nézetből.

## <a name="setup"></a>Beállítás

Ha még nem tette meg, az alkalmazás klónozása:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Nyisson meg egy terminált a tárház címtárában. Az alkalmazás összeállítása a futtatásával

```bash
mvn clean package
```

## <a name="walkthrough"></a>Útmutatás

1. Első lépésként Azure Cosmos DB fiókkal kell rendelkeznie. Nyissa meg a **Azure Portal** a böngészőben, lépjen a Azure Cosmos db-fiókra, és a bal oldali ablaktáblán navigáljon **adatkezelő**.

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Azure Cosmos DB-fiók":::

1. Futtassa az alkalmazást a terminálon a következő parancs használatával:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Nyomja le az ENTER billentyűt, amikor megjelenik

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Ezután térjen vissza a böngészőben a Azure Portal Adatkezelő. Látni fogja, hogy egy adatbázis- **GroceryStoreDatabase** három üres tárolóval lett hozzáadva: 

    * **InventoryContainer** – a példaként szolgáló áruház leltározási rekordja, ```id``` amely egy UUID-t tartalmazó elemre van particionálva.
    * **InventoryContainer-pktype** – az elemre irányuló lekérdezésekre optimalizált leltári rekord anyagilag látható nézete ```type```
    * **InventoryContainer – bérletek** – a változási hírcsatornára mindig a bérletek tárolója szükséges. a bérletek nyomon követik az alkalmazás előrehaladását a változási hírcsatorna olvasásakor.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Üres tárolók":::

1. A terminálban ekkor megjelenik egy üzenet

    ```bash
    Press enter to start creating the materialized view...
    ```

    Nyomja le az ENTER billentyűt. Most a következő kódrészlet végrehajtja és inicializálja a változási hírcsatorna processzorát egy másik szálon: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` a a változási csatorna feldolgozó feldolgozójának neve. ```changeFeedProcessorInstance.start()``` valójában elindítja a Change feed processzort.

    Térjen vissza a böngészőben a Azure Portal Adatkezelő. A **InventoryContainer-bérletek** tárolóban kattintson az **elemek elemre** a tartalmának megtekintéséhez. Látni fogja, hogy a változási hírcsatorna processzora feltöltte a címbérleti tárolót, azaz a processzor a ```SampleHost_1``` **InventoryContainer** egyes partícióinak bérletét rendelte hozzá a feldolgozóhoz.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Bérletek":::

1. Nyomja le ismét az ENTER billentyűt a terminálon. Ekkor a rendszer 10 dokumentumot szúr be a **InventoryContainerba**. Minden dokumentum beszúrása JSON-ként jelenik meg a változási hírcsatornában. a következő visszahívási kód kezeli ezeket az eseményeket úgy, hogy a JSON-dokumentumokat egy anyagbeli nézetbe tükrözi:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. 5 10sec futtatásának engedélyezése a kód számára. Ezután térjen vissza a Azure Portal Adatkezelő, és navigáljon a **InventoryContainer > elemekhez**. Látnia kell, hogy a rendszer beszúrja az elemeket a leltári tárolóba; Jegyezze fel a partíciós kulcsot ( ```id``` ).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Hírcsatorna-tároló":::

1. Most Adatkezelő navigáljon a **InventoryContainer-pktype > elemekhez**. Ez az anyagbeli nézet – az ebben a tárolóban lévő elemek tükrözött **InventoryContainer** , mert programozott módon szúrták be a hírcsatorna módosításával. Jegyezze fel a partíciós kulcsot ( ```type``` ). Így ez az anyagbeli nézet a lekérdezési szűrésre van optimalizálva ```type``` , ami nem hatékony a **InventoryContainer** , mert particionálva van ```id``` .

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Képernyőfelvétel: az Azure Cosmos D B-fiók Adatkezelő lapja, amelyen elemek vannak kiválasztva.":::

1. A **InventoryContainer** és a **InventoryContainer-pktype** dokumentumból csak egyetlen hívást fogunk törölni ```upsertItem()``` . Először tekintse meg Azure Portal Adatkezelő. Töröljük azt a dokumentumot, amelynek ```/type == "plums"``` ; a következő piros színnel van Bekerítve:

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Képernyőfelvétel: az Azure Cosmos D B-fiók Adatkezelő lapja, amely egy adott elemet kiválasztott. D.":::

    Nyomja meg ismét az ENTER billentyűt a függvény meghívásához ```deleteDocument()``` a példában szereplő kódban. Ez a függvény az alább látható módon upsert a dokumentum egy új verzióját ```/ttl == 5``` , amely a dokumentumot élettartam (TTL) értékre állítja a 5Sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    A változási csatorna ```feedPollDelay``` 100ms értékre van állítva, ezért a változási csatorna szinte azonnal reagál erre a frissítésre, és a ```updateInventoryTypeMaterializedView()``` fent látható hívásokat kéri. Az utolsó függvény hívása az új dokumentumot a 5Sec TTL-upsert fogja a **InventoryContainer-pktype**.

    A hatás az, hogy körülbelül 5 másodperc elteltével a dokumentum lejár, és mindkét tárolóból törölve lesz.

    Ez az eljárás azért szükséges, mert a csak az elemek beszúrásával vagy frissítésével kapcsolatos eseményeket, nem pedig az elemek törlését kell kiállítani.

1. Nyomja le még egyszer az ENTER billentyűt a program bezárásához és az erőforrások törléséhez.
