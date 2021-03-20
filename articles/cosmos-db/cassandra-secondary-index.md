---
title: Indexelés Azure Cosmos DB Cassandra API fiókban
description: Ismerje meg, hogyan működik a másodlagos indexelés az Azure Azure Cosmos DB Cassandra API-fiókban.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339890"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Másodlagos indexelés Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra APIja az alapul szolgáló indexelési infrastruktúrát használja a platformon rejlő indexelési erősség kihasználása érdekében. Az alapszintű SQL API-val ellentétben azonban a Azure Cosmos DB Cassandra API nem indexeli az összes attribútumot alapértelmezés szerint. Ehelyett támogatja a másodlagos indexelést bizonyos attribútumok indexének létrehozásához, ami ugyanúgy viselkedik, mint az Apache Cassandra.  

Általánosságban elmondható, hogy a nem particionált oszlopokra vonatkozó szűrési lekérdezéseket nem tanácsos végrehajtani. Explicit módon a szűrés engedélyezése szintaxist kell használnia, ami egy olyan műveletet eredményez, amely esetleg nem jól teljesít. Azure Cosmos DB futtathatja az ilyen lekérdezéseket a kis-és nagymértékű attribútumokon, mert a partíciók között az eredmények lekéréséhez az összes partíciót felhasználják.

Nem tanácsos egy indexet létrehozni egy gyakran frissített oszlopon. Célszerű létrehozni egy indexet a tábla meghatározásakor. Ez biztosítja, hogy az adategységek és az indexek konzisztens állapotban legyenek. Abban az esetben, ha új indexet hoz létre a meglévő adatsorokban, jelenleg nem követheti a tábla előrehaladásának változását. Ha nyomon szeretné követni a művelet előrehaladását, egy [támogatási jegyen]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)keresztül kell kérnie a folyamat változását.


> [!NOTE]
> A másodlagos index nem támogatott a következő objektumokon:
> - adattípusok, például fagyasztott gyűjtemények típusai, decimális és Variant típusú típusok.
> - Statikus oszlopok
> - Fürtözési kulcsok

## <a name="indexing-example"></a>Példa indexelésre

Először hozzon létre egy minta-térközt és egy táblázatot a következő parancsok futtatásával a CQL rendszerhéj parancssorába:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Ezután szúrja be a minta felhasználói adatbevitelt a következő parancsokkal:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Ha a következő utasítás végrehajtásával próbálkozik, a rendszer hibát jelez, amely arra kéri, hogy használja a parancsot `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Bár a Cassandra API támogatja az engedélyezési SZŰRÉSt, ahogy azt az előző szakaszban is említettük, nem ajánlott. Ehelyett hozzon létre egy indexet a következő példában látható módon:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Miután létrehozta az indexet a "LastName" (vezetéknév) mezőben, most már sikeresen futtathatja az előző lekérdezést. Azure Cosmos DB Cassandra API esetén nem kell megadnia az index nevét. A rendszer egy alapértelmezett indexet használ, amely formátummal rendelkezik `tablename_columnname_idx` . Például az ` t1_lastname_idx` előző táblázat indexének neve.

## <a name="dropping-the-index"></a>Az index eldobása 
Tudnia kell, hogy az index neve hogyan dobja el az indexet. Futtassa a `desc schema` parancsot a tábla leírásának lekéréséhez. A parancs kimenete tartalmazza az index nevét a következő formátumban: `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . Ezután az index neve segítségével elhúzhatja az indexet az alábbi példában látható módon:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan működik az [Automatikus indexelés](index-overview.md) az Azure Cosmos db
* [Az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciók](cassandra-support.md)
