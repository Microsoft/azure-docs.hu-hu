---
title: Particionálás Azure Cosmos DB Cassandra API
description: Tudnivalók a particionálásról Azure Cosmos DB Cassandra API
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087638"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Particionálás Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk azt ismerteti, hogyan működik a particionálás a Azure Cosmos DB Cassandra APIban. 

A Cassandra API particionálás használatával méretezi az egyes táblákat az alkalmazás teljesítményének kielégítéséhez. A partíciók egy tábla egyes rekordjaihoz társított partíciós kulcs értéke alapján jönnek létre. Egy partíció összes rekordja ugyanazzal a partíciós kulcs értékkel rendelkezik. Azure Cosmos DB átlátható módon, és automatikusan kezeli a partíciók elhelyezését a fizikai erőforrások között, hogy hatékonyan kielégítse a tábla méretezhetőségét és teljesítményét. Az alkalmazások adatátviteli és tárolási követelményeinek növekedésével a Azure Cosmos DB nagyobb számú fizikai gépen helyezi át és osztja el az adatok mennyiségét.

A fejlesztői szempontból a particionálás ugyanúgy viselkedik, mint a Azure Cosmos DB Cassandra API, ahogyan az a natív [Apache Cassandra](https://cassandra.apache.org/)-ban is működik. Vannak azonban különbségek a színfalak mögött. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Az Apache Cassandra és a Azure Cosmos DB közötti különbségek

Azure Cosmos DB minden olyan gépet, amelyen a partíciók tárolódnak, [fizikai partíciónak](partitioning-overview.md#physical-partitions)nevezzük. A fizikai partíció hasonlít a virtuális géphez. dedikált számítási egység vagy fizikai erőforrások halmaza. Az ezen számítási egységen tárolt összes partíciót a Azure Cosmos DB [logikai partíciójának](partitioning-overview.md#logical-partitions) nevezzük. Ha már ismeri az Apache Cassandra-t, úgy gondolhatja, hogy a logikai partíciók ugyanúgy jelennek meg, ahogy a Cassandra-ben lévő normál partíciókat gondolja. 

Az Apache Cassandra 100 MB-os korlátot javasol a partíciókban tárolható adatmennyiségek méretére. A Azure Cosmos DB Cassandra API akár 20 GB-nyi logikai partíción, illetve fizikai partíción akár 30 GB-onként is használható. Azure Cosmos DB az Apache Cassandratől eltérően a fizikai partícióban rendelkezésre álló számítási kapacitás a kérelmek [egységei](request-units.md)nevű egyetlen metrika használatával van kifejezve, amely lehetővé teszi, hogy a terhelést a másodpercenkénti kérések (olvasási vagy írási), illetve a magok, a memória vagy a IOPS alapján is meggondolja. Így a kapacitás megtervezése még több egyenesen előre elvégezhető, miután megértette az egyes kérések költségeit. Az egyes fizikai partíciók legfeljebb 10000 RUs mennyiségű számítási lehetőséggel rendelkezhetnek. A méretezhetőségi lehetőségekkel kapcsolatos további információkért tekintse meg a cikket a [rugalmas méretezés](manage-scale-cassandra.md) Cassandra APIban című cikkből. 

Azure Cosmos DB minden fizikai partíció replikák, más néven replika-készletből áll, és partíciók legalább 4 replikával rendelkeznek. Ez ellentétben áll az Apache Cassandra-vel, ahol az 1 replikációs tényező beállítása lehetséges. Ez azonban alacsony rendelkezésre állást eredményez, ha az egyetlen csomópont, amelyen az adat leáll. Cassandra API mindig a 4-es replikációs tényező (3 kvórum). A Azure Cosmos DB automatikusan kezeli a replikákat, és ezeket az Apache Cassandra különböző eszközeivel kell karbantartani. 

Az Apache Cassandra a tokenek fogalmát képezi, amely a partíciós kulcsok kivonata. A tokenek egy murmur3 64 bájtos kivonaton alapulnak, és a-2 ^ 63 – 2 ^ 63-1 értéktől kezdve. Ezt a tartományt általában "Token Ring"-ként nevezik az Apache Cassandra-ban. A jogkivonat-gyűrű a jogkivonat-tartományokban van elosztva, és ezek a tartományok a natív Apache Cassandra-fürtben lévő csomópontok között oszlanak meg. A Azure Cosmos DB particionálása hasonló módon valósul meg, kivéve, ha más kivonatoló algoritmust használ, és nagyobb belső jogkivonat-gyűrűvel rendelkezik. Külsőleg azonban ugyanaz a jogkivonat-tartomány lesz elérhető, mint az Apache Cassandra, azaz – 2 ^ 63 – 2 ^ 63 – 1.


## <a name="primary-key"></a>Elsődleges kulcs

Cassandra API összes táblájának definiálva kell lennie `primary key` . Az elsődleges kulcs szintaxisa alább látható:

```shell
column_name cql_type_definition PRIMARY KEY
```

Tegyük fel, hogy létre szeretne hozni egy felhasználói táblát, amely különböző felhasználók üzeneteinek tárolására használható:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

Ebben a kialakításban a `id` mezőt elsődleges kulcsként definiáljuk. Az elsődleges kulcs a tábla rekordjának azonosítóját használja, és a Azure Cosmos DBban található partíciós kulcsként is használják. Ha az elsődleges kulcs a korábban leírt módon van definiálva, akkor csak egyetlen rekord lesz az egyes partíciókban. Ez tökéletesen horizontális és skálázható eloszlást eredményez az adatadatbázisba való íráskor, és ideális a kulcs-érték keresési használati esetekben. Az olvasási teljesítmény maximalizálása érdekében az alkalmazásnak meg kell adnia az elsődleges kulcsot, amikor az adatok beolvasása a táblából. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="partíciók" border="false":::


## <a name="compound-primary-key"></a>Összetett elsődleges kulcs

Az Apache Cassandra is tartalmaz egy koncepciót  `compound keys` . Egy összetett `primary key` több oszlopból áll; az első oszlop a `partition key` , és a további oszlopok `clustering keys` . Az a szintaxisa `compound primary key` alább látható:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Tegyük fel, hogy módosítani szeretné a fenti tervet, és lehetővé teszi az üzenetek hatékony lekérését egy adott felhasználó számára:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

Ebben a kialakításban most már definiáljuk `user` a partíciós kulcsot és `id` a fürtözési kulcsot. Tetszőleges számú fürtszolgáltatási kulcsot határozhat meg, de a fürtözési kulcs minden értékének (vagy értékének kombinációjának) egyedinek kell lennie ahhoz, hogy több rekordot is hozzá lehessen adni ugyanahhoz a partícióhoz, például:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Az adatvisszaadás során a rendszer a fürtszolgáltatási kulcs szerint rendezi az Apache Cassandra-ban elvárt módon:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Képernyőkép, amely a fürtözési kulcs alapján rendezett visszaadott értékeket jeleníti meg.":::

Az ily módon létrehozott adatokkal több rekord is hozzárendelhető minden partícióhoz, felhasználó szerint csoportosítva. Így egy `partition key` `user` adott felhasználó összes üzenetének lekéréséhez egy olyan lekérdezést adhatunk ki, amely hatékonyan irányítja a (jelen esetben). 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Diagram, amely azt mutatja, hogy a rendszer hogyan rendelhető hozzá több rekord az egyes partíciókhoz, felhasználónként csoportosítva." border="false":::


## <a name="composite-partition-key"></a>Összetett partíciós kulcs

Az összetett partíciós kulcsok lényegében ugyanúgy működnek, mint az összetett kulcsok, kivéve, ha több oszlopot is megadhat összetett partíciós kulcsként. Az összetett partíciós kulcsok szintaxisa alább látható:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Például a következőkkel rendelkezhet, ahol a és az egyedi kombinációja a `firstname` `lastname` partíciós kulcsot fogja képezni, és `id` a fürtözési kulcs:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [particionálásról és a horizontális skálázásról Azure Cosmos db](partitioning-overview.md).
* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
