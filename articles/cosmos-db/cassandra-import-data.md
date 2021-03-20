---
title: Migrálja adatait egy Cassandra API-fiókba Azure Cosmos DB-oktatóanyag
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a & Spark CQL másolási parancsát az adatok Apache Cassandra-ből egy Cassandra API-fiókba való másolásához Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: bd2d27addb6860e49ac12eb36d8b625b8bf92001
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100405"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Oktatóanyag: az adatáttelepítés Cassandra API fiókba Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Fejlesztőként lehetnek olyan meglévő Cassandra-munkaterhelések, amelyek a helyszínen vagy a felhőben futnak, és előfordulhat, hogy át kívánják telepíteni őket az Azure-ba. Az ilyen számítási feladatok áttelepíthetők a Azure Cosmos DB Cassandra API-fiókjába. Ez az oktatóanyag Cassandra API az Apache Cassandra-ba való áttelepítéshez használható különböző lehetőségekről nyújt útmutatást Azure Cosmos DB.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Migrálási terv
> * A migrálás előfeltételei
> * Adatok migrálása a cqlsh COPY paranccsal
> * Adatok migrálása a Spark használatával

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites-for-migration"></a>A migrálás előfeltételei

* **A teljesítményre vonatkozó igények becslése:** Mielőtt áttelepíti az adatait a Azure Cosmos DB Cassandra API-fiókjába, érdemes megbecsülni a munkaterhelés átviteli sebességét. Általában ajánlott a CRUD-műveletekhez szükséges átlagos átviteli sebességgel kezdeni, majd ezt kiegészíteni a kinyerési, átalakítási és betöltési (ETL-) vagy a csúcsidejű műveletekhez szükséges további sebességgel. A migrálás megtervezéséhez a következő adatokra lesz szükség: 

  * **Meglévő vagy becsült adatmennyiség:** A minimálisan szükséges adatbázisméretet és átviteli sebességet határozza meg. Ha egy új alkalmazás adatmennyiségét becsüli fel, feltételezheti, hogy az adatok egyenletesen oszlanak majd el a sorok között, és az értéket az adatok méretét felszorozva kaphatja meg. 

  * **Szükséges átviteli sebesség:** Hozzávetőleges olvasás (lekérdezés/beolvasás) és írás (frissítés/törlés/Beszúrás) átviteli sebesség. Erre az értékre a szükséges kérelemegység-mennyiség állandó adatmennyiség melletti kiszámításához van szükség.  

  * **A séma:** Kapcsolódjon a meglévő Cassandra-fürthöz a cqlsh-n keresztül, és exportálja a sémát a Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Miután azonosította a meglévő számítási feladatok követelményeit, létre kell hoznia egy Azure Cosmos-fiókot,-adatbázist és-tárolót az összegyűjtött átviteli követelmények szerint.  

  * **Egy művelethez tartozó ru-díj meghatározása:** Az RUs a Cassandra API által támogatott SDK-k használatával határozható meg. Ez a példa a kérelemegység-díjak .NET-tel való beszerzését mutatja be.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **A szükséges átviteli sebesség lefoglalása:** Az Azure Cosmos DB-vel automatikusan, a növekvő követelményeknek megfelelően skálázható a tárolás és az átviteli sebesség. A szükséges átviteli sebességet az [Azure Cosmos DB kérelemegység-kalkulátorával](https://www.documentdb.com/capacityplanner) számíthatja ki. 

* **Táblák létrehozása a Cassandra API fiókban:** Mielőtt megkezdené az adatok áttelepítését, előzetesen hozza létre az összes táblát a Azure Portalból vagy a cqlsh. Ha olyan Azure Cosmos-fiókba végez áttelepítést, amely adatbázis-szintű átviteli sebességgel rendelkezik, ügyeljen arra, hogy az Azure Cosmos-tárolók létrehozásakor adjon meg egy partíciós kulcsot.

* **Átviteli sebesség növelése:** Az adatok migrálásának időtartama az Azure Cosmos DB-táblákhoz lefoglalt átviteli sebességtől függ. A migrálás idejére növelje meg az átviteli sebességet. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Azt is javasoljuk, hogy az Azure Cosmos-fiók ugyanabban a régióban legyen, mint a forrás-adatbázis. 

* **TLS engedélyezése:** Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Ügyeljen arra, hogy engedélyezze a TLS-t a fiókjával való interakció során. Ha SSH-val használja az CQL-t, lehetősége van a TLS-információk megadására.

## <a name="options-to-migrate-data"></a>Adatmigrálási lehetőségek

A meglévő Cassandra-számításifeladatokból a következő lehetőségekkel migrálhatja az adatokat az Azure Cosmos DB-be:

* [A cqlsh COPY parancsával](#migrate-data-using-cqlsh-copy-command)  
* [A Spark használatával](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Adatok migrálása a cqlsh COPY paranccsal

A [CQL másolási parancs](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) a helyi és a Azure Cosmos db Cassandra API fiókba való másolására szolgál. Az adatok másolásához hajtsa végre a következő lépéseket:

1. A Cassandra API-fiók kapcsolatisztring-adatainak lekérése:

   * Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és navigáljon az Azure Cosmos-fiókjához.

   * Nyissa meg a **Kapcsolati sztring** panelt, amely a Cassandra API-fiókhoz a cqlsh-ból való kapcsolódáshoz szükséges összes információt tartalmazza.

2. A portál kapcsolati adatait használva jelentkezzen be a cqlsh-ba.

3. Használja a CQL COPY parancsot a helyi adatok a Cassandra API-fiókba való másolásához.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Adatok migrálása a Spark használatával 

Az alábbi lépések végrehajtásával áttelepítheti az Cassandra API-fiókba a Spark használatával:

- Azure Databricks- [fürt](cassandra-spark-databricks.md) vagy HDInsight- [fürt](cassandra-spark-hdinsight.md) kiépítése 

- Az adatáthelyezés a cél Cassandra API végpontba a [tábla másolási műveletének](cassandra-spark-table-copy-ops.md) használatával 

Az adatok Spark-feladatok használatával történő áttelepítése ajánlott lehetőség, ha az Azure Virtual Machines szolgáltatásban vagy bármely más felhőben meglévő fürtben található adatokkal rendelkezik. Ez a beállítás megköveteli, hogy a Spark egy egyszeri vagy normál betöltéshez legyen beállítva. Ezt az áttelepítést felgyorsíthatja az Azure ExpressRoute-kapcsolat használatával a helyszíni és az Azure között. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, az Azure Cosmos-fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a **Törlés** lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan migrálhatja adatait Azure Cosmos DB Cassandra API fiókba. A következő cikkből megismerheti a további Azure Cosmos DB fogalmakat is:

> [!div class="nextstepaction"]
> [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)


