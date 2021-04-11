---
title: Migrálja adatait egy Cassandra API-fiókba Azure Cosmos DB-oktatóanyag
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat adatokat az Apache Cassandra-ből egy Azure Cosmos DB Cassandra API-fiókjába.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449290"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Oktatóanyag: az adatai áttelepíthetők egy Cassandra API-fiókba
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Fejlesztőként lehetnek olyan meglévő Cassandra-munkaterhelések, amelyek a helyszínen vagy a felhőben futnak, és előfordulhat, hogy át kívánják telepíteni őket az Azure-ba. Az ilyen számítási feladatok áttelepíthetők a Azure Cosmos DB Cassandra API-fiókjába. Ez az oktatóanyag Cassandra API az Apache Cassandra-ba való áttelepítéshez használható különböző lehetőségekről nyújt útmutatást Azure Cosmos DB.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Migrálási terv
> * A migrálás előfeltételei
> * Az adatáttelepítés parancs használatával `cqlsh` `COPY`
> * Adatok migrálása a Spark használatával

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites-for-migration"></a>A migrálás előfeltételei

* **A teljesítményre vonatkozó igények becslése:** Mielőtt áttelepíti az adatait a Azure Cosmos DB Cassandra API-fiókjába, érdemes megbecsülni a munkaterhelés átviteli sebességét. Általánosságban elmondható, hogy a szifilisz-műveletek átlagos átviteli sebességét kell megadnia, majd az átalakítási és a tüskés műveletekhez szükséges további átviteli sebességet is be kell írnia. A migrálás megtervezéséhez a következő adatokra lesz szükség: 

  * **Meglévő vagy becsült adatmennyiség:** A minimálisan szükséges adatbázisméretet és átviteli sebességet határozza meg. Ha egy új alkalmazás adatméretét becsüli fel, feltételezheti, hogy az adat egyenletesen oszlik el a sorok között, és az adatméret szorzatával megbecsüli az értéket. 

  * **Szükséges átviteli sebesség:** Az olvasási (lekérdezési/lekérési) és az írási (frissítési/törlési/beszúrási) műveletek hozzávetőleges átviteli sebessége. Ez az érték szükséges a szükséges kérési egységek kiszámításához, valamint az állandó állapotú adatok méretéhez.  

  * **A séma:** Kapcsolódjon a meglévő Cassandra-fürthöz a használatával `cqlsh` , és exportálja a sémát a Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Miután azonosította a meglévő számítási feladatok követelményeit, hozzon létre egy Azure Cosmos DB fiókot, adatbázist és tárolót az összegyűjtött átviteli követelmények szerint.  

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

* **Táblák létrehozása a Cassandra API fiókban:** Mielőtt megkezdené az adatok áttelepítését, előzetesen hozza létre az összes táblát a Azure Portalból vagy a következőből: `cqlsh` . Ha olyan Azure Cosmos DB-fiókra végez áttelepítést, amely adatbázis-szintű átviteli sebességgel rendelkezik, ügyeljen arra, hogy a tárolók létrehozásakor adjon meg egy partíciós kulcsot.

* **Átviteli sebesség növelése:** Az adatok migrálásának időtartama az Azure Cosmos DB-táblákhoz lefoglalt átviteli sebességtől függ. A migrálás idejére növelje meg az átviteli sebességet. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Azt is javasoljuk, hogy a Azure Cosmos DB fiókja ugyanabban a régióban legyen, mint a forrás-adatbázis. 

* **TLS engedélyezése:** Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Ügyeljen arra, hogy engedélyezze a TLS-t a fiókjával való interakció során. Ha SSH-val használja az CQL-t, lehetősége van a TLS-információk megadására.

## <a name="options-to-migrate-data"></a>Adatmigrálási lehetőségek

A meglévő Cassandra számítási feladatokból áthelyezheti az adatait, hogy Azure Cosmos DB a `cqlsh` `COPY` paranccsal, vagy a Spark használatával. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Az adatáttelepítés az cqlsh MÁSOLÁSi parancsának használatával

A [CQL Másolás parancs](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) használatával másolja a helyi fájlokat a Azure Cosmos db Cassandra API-fiókjába.

1. A Cassandra API-fiók kapcsolatisztring-adatainak lekérése:

   * Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és lépjen a Azure Cosmos db-fiókjához.

   * Nyissa meg a **kapcsolatok karakterlánca** panelt. Itt láthatja az összes olyan információt, amelyre szüksége lesz a Cassandra API fiókjához való kapcsolódáshoz `cqlsh` .

1. Jelentkezzen be a `cqlsh` szolgáltatásba a portálon található kapcsolódási adatok használatával.

1. A `CQL` `COPY` paranccsal másolja a helyi fájlokat a Cassandra API-fiókba.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Adatok migrálása a Spark használatával 

Az alábbi lépések végrehajtásával áttelepítheti az Cassandra API-fiókba a Spark használatával:

1. [Azure Databricks fürt](cassandra-spark-databricks.md) vagy [Azure HDInsight-fürt](cassandra-spark-hdinsight.md)kiépítése. 

1. Helyezze át az adatátvitelt a cél Cassandra API végpontba a [tábla másolási műveletének](cassandra-spark-table-copy-ops.md)használatával. 

Az adatok Spark-feladatok használatával történő áttelepítése ajánlott lehetőség, ha az Azure Virtual Machines szolgáltatásban vagy bármely más felhőben meglévő fürtben található adatokkal rendelkezik. Ehhez be kell állítania a Sparkot közvetítőként egyszeri vagy normál betöltéshez. Ezt az áttelepítést a helyszíni környezet és az Azure közötti Azure ExpressRoute-kapcsolat használatával gyorsíthatja fel. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a Azure Cosmos DB fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a **Törlés** lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan migrálhatja adatait egy Cassandra API-fiókba Azure Cosmos DBban. Mostantól megismerheti a Azure Cosmos DB egyéb fogalmait is:

> [!div class="nextstepaction"]
> [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)




