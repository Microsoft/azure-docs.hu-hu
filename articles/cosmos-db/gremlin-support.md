---
title: Azure Cosmos DB Gremlin-támogatás és kompatibilitás az TinkerPop-funkciókkal
description: Tudnivalók az Apache TinkerPop Gremlin nyelvéről. Megtudhatja, hogy mely funkciók és lépések érhetők el Azure Cosmos DB és a TinkerPop Graph motor kompatibilitási különbségeit.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: 036338e90a3e7b466924d419400c0dcc692dec5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630751"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Azure Cosmos DB Gremlin Graph támogatás és kompatibilitás az TinkerPop-funkciókkal
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB támogatja az [Apache Tinkerpop](https://tinkerpop.apache.org) Graph bejárási nyelvét, amely [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)néven ismert. A Gremlin nyelv segítségével létrehozhat gráfentitásokat (csúcspontokat és éleket), módosíthatja ezen entitások tulajdonságait, végrehajthat lekérdezéseket és bejárásokat, és törölhet entitásokat.

Azure Cosmos DB Graph Engine szorosan követi az [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) bejárási lépések specifikációját, de eltérések vannak a Azure Cosmos DBra vonatkozó implementációban. Ebben a cikkben egy rövid útmutatót biztosítunk a Gremlin, és enumeráljuk a Gremlin API által támogatott Gremlin-szolgáltatásokat.

## <a name="compatible-client-libraries"></a>Kompatibilis ügyféloldali kódtárak

Az alábbi táblázat az Azure Cosmos DB-n használható népszerű Gremlin-illesztőprogramokat foglalja össze:

| Letöltés | Forrás | Első lépések | Támogatott összekötő-verzió |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET on GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Gráf létrehozása a .NET használatával](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Gráf létrehozása a Java használatával](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript a GitHubon](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Gráf létrehozása a Node.js használatával](create-graph-nodejs.md) | 3.3.4 + |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python a GitHubon](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Gráf létrehozása a Python használatával](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP a GitHubon](https://github.com/PommeVerte/gremlin-php) | [Gráf létrehozása a PHP használatával](create-graph-php.md) | 3.1.0 |
| [Go lang](https://github.com/supplyon/gremcos/) | [Go lang](https://github.com/supplyon/gremcos/) | | Ezt a kódtárat külső közreműködők építették. A Azure Cosmos DB csapat semmilyen támogatást nem nyújt, vagy nem tartja karban a könyvtárat. |
| [Gremlin-konzol](https://tinkerpop.apache.org/downloads.html) | [TinkerPop dokumentumok](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gráf létrehozása a Gremlin-konzol használatával](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Támogatott Graph-objektumok

A TinkerPop egy olyan szabvány, amely számos különböző gráftechnológiára kiterjed. Ebből adódóan szabványos kifejezésekkel írja le, hogy az egyes gráfszolgáltatók milyen funkciókat nyújtanak. Az Azure Cosmos DB egy állandó, magas egyidejűségű, írható gráfadatbázis, amely egyszerre több kiszolgálóra vagy fürtre is particionálható. 

Az alábbi táblázat a TinkerPop azon funkcióit sorolja fel, amelyeket az Azure Cosmos DB megvalósít: 

| Kategória | Azure Cosmos DB-megvalósítás |  Jegyzetek | 
| --- | --- | --- |
| Gráffunkciók | Állandóságot és egyidejű hozzáférést biztosít. A tranzakciók támogatására lett tervezve. | A számítógépes módszerek a Spark-összekötőn keresztül valósíthatók meg. |
| Változófunkciók | A logikai, egész számú, bájt-, dupla, lebegőpontos, hosszú és sztringértékeket támogatja. | Támogatja az egyszerű típusokat, és kompatibilis az összetett típusokkal adatmodellek révén. |
| Csúcspontfunkciók | A következőket támogatja: RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Lehetővé teszi csúcspontok létrehozását, módosítását és törlését. |
| Csúcsponttulajdonság-funkciók | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Lehetővé teszi csúcsponttulajdonságok létrehozását, módosítását és törlését. |
| Élfunkciók | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Lehetővé teszi élek létrehozását, módosítását és törlését. |
| Éltulajdonság-funkciók | Tulajdonságok, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Lehetővé teszi éltulajdonságok létrehozását, módosítását és törlését. |

## <a name="gremlin-wire-format"></a>Gremlin huzal formátuma

A Azure Cosmos DB JSON-formátumot használ a Gremlin-műveletek eredményeinek visszaadása során. A Azure Cosmos DB jelenleg a JSON formátumot támogatja. Az alábbi kódrészlet például az *ügyfélnek a Azure Cosmos DBból visszaadott* csúcspont JSON-ábrázolását jeleníti meg:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

A csúcsok JSON-formátuma által használt tulajdonságok ismertetését alább találja:

| Tulajdonság | Leírás | 
| --- | --- | --- |
| `id` | A csúcspont azonosítója. Egyedinek kell lennie (a megfelelő értékkel együtt `_partition` ). Ha nincs megadva érték, a rendszer automatikusan GUID azonosítóval fogja ellátni | 
| `label` | A csúcspont címkéje. Ez a tulajdonság az entitás típusának leírására szolgál. |
| `type` | A használatával megkülönböztethetők a csúcspontok a nem gráfdokumentumoktól. |
| `properties` | A csúcsponthoz tartozó, felhasználó által megadott tulajdonságok összessége. Minden tulajdonságnak több értéke is lehet. |
| `_partition` | A csúcspont partíciókulcsa. [Gráf particionálásához](graph-partitioning.md)használatos. |
| `outE` | Ez a tulajdonság a csúcspontokból kimenő élek listáját tartalmazza. A csúcspontok szomszédsági adatainak tárolása lehetővé teszi a bejárások gyors végrehajtását. Az élek a címkéik alapján vannak csoportosítva. |

Az él pedig a következő információkat tartalmazza, ezzel segítve a gráf többi részéhez való navigációt.

| Tulajdonság | Leírás |
| --- | --- |
| `id` | Az él azonosítója. Egyedinek kell lennie (a megfelelő értékkel együtt `_partition` ) |
| `label` | Az él címkéje. Ezt a tulajdonságot nem kötelező megadni, és a kapcsolat típusának leírására használható. |
| `inV` | Ez a tulajdonság egy Edge-csúcsok listáját tartalmazza. Az élek szomszédsági adatainak tárolása lehetővé teszi a bejárások gyors végrehajtását. A csúcspontok a címkéik alapján vannak csoportosítva. |
| `properties` | Az élhez tartozó, felhasználó által megadott tulajdonságok összessége. Minden tulajdonságnak több értéke is lehet. |

Az egyes tulajdonságok több értéket is tárolhatnak egy tömbben. 

| Tulajdonság | Leírás |
| --- | --- |
| `value` | A tulajdonság értéke.

## <a name="gremlin-steps"></a>Gremlin-lépések

Most pedig tekintsük át az Azure Cosmos DB által támogatott Gremlin-lépéseket. A Gremlin teljes körű ismertetését a [TinkerPop referenciaanyaga](https://tinkerpop.apache.org/docs/3.3.2/reference) tartalmazza.

| lépés | Description | TinkerPop 3.2-dokumentáció |
| --- | --- | --- |
| `addE` | Hozzáad egy élt két csúcspont között. | [addE lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Hozzáad egy csúcspontot a gráfhoz. | [addV lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Biztosítja, hogy minden bejárás visszaadjon egy értéket. | [and lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Egy lépésmodulátor, amely egy változót rendel a lépés kimenetéhez. | [as lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | A `group` és az `order` lépéssel használt lépésmodulátor. | [by lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Visszaadja az első olyan bejárást, amely értéket ad vissza. | [coalesce lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Egy állandó értéket ad vissza. A `coalesce` lépéssel használható.| [constant lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Visszaadja a darabszámot a bejárásból. | [count lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Visszaadja az értékeket, eltávolítva az ismétlődéseket. | [dedup lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Elveti az értékeket (csúcspont/él). | [drop lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | A végrehajtott Gremlin lépés által létrehozott összes művelet leírásának létrehozása | [executionProfile lépés](graph-execution-profile.md) |
| `fold` | Korlátként funkcionál, amely kiszámítja az eredmények összesítését.| [fold lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Csoportosítja az értékeket a megadott címkék alapján.| [group lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Tulajdonságok, csúcspontok és élek szűrésére szolgál. A következő változatokat támogatja: `hasLabel`, `hasId`, `hasNot` és `has`. | [has lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Értékeket szúr be egy streambe.| [inject lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Szűrés végrehajtására használható egy logikai kifejezés használatával. | [is lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | A bejárásban található elemek számának korlátozására szolgál.| [limit lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Helyileg becsomagolja egy bejárás egy szakaszát, egy segédlekérdezéshez hasonlóan. | [local lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Egy szűrő eltávolítására szolgál. | [not lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | A megadott bejárás eredményét adja vissza, ha az ad eredményt, ha nem, akkor a hívó elemet adja vissza. | [választható lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Biztosítja, hogy legalább az egyik bejárás visszaadjon egy értéket. | [or lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | A megadott rendezési sorrendben adja vissza az eredményeket. | [order lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Visszaadja a bejárás teljes útvonalát. | [path lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Leképezésként jeleníti meg a tulajdonságokat. | [project lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Visszaadja a megadott címkék tulajdonságait. | [properties lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | A megadott értéktartományra szűr.| [range lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Megismétli a lépést a megadott számú alkalommal. Ismétlődések beállítására szolgál. | [repeat lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Mintát vesz a bejárás eredményeiből. | [sample lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Megjeleníti a bejárás eredményeit. |  [select lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Nem blokkoló összesítéseket hajt végre a bejárásból. | [store lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Karakterlánc-szűrési függvény Ez a függvény predikátumként szolgál ahhoz a lépéshez, amely megfelel `has()` egy adott sztring kezdetét megadó tulajdonságnak. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Karakterlánc-szűrési függvény Ez a függvény predikátumként szolgál ahhoz a lépéshez, amely egy `has()` adott sztring végén található tulajdonságnak felel meg. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Karakterlánc-szűrési függvény Ez a függvény predikátumként szolgál ahhoz a lépéshez, amely egy `has()` adott sztring tartalmával egyező tulajdonságot tartalmaz. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Karakterlánc-szűrési függvény Ez a függvény predikátumként szolgál a `has()` lépéshez, hogy megfeleljen egy olyan tulajdonságnak, amely nem egy adott karakterlánccal kezdődik. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Karakterlánc-szűrési függvény Ez a függvény predikátumként szolgál ahhoz a `has()` lépéshez, amely nem egy adott karakterlánccal végződik. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Karakterlánc-szűrési függvény Ez a függvény predikátumként szolgál a lépéshez, `has()` amely nem tartalmaz megadott karakterláncot. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Egy fában összesíti a csúcspontból induló útvonalakat. | [tree lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Visszaalakít egy iterátort egy lépésként.| [unfold lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Egyesíti több bejárás eredményeit.| [union lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | A csúcspontok és élek közötti bejárásokhoz szükséges lépéseket foglalja magában: `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` és `otherV`. | [vertex lépések](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | A bejárás eredményeinek szűrésére szolgál. A következő operátorokat támogatja: `eq`, `neq`, `lt`, `lte`, `gt`, `gte`, `between`.  | [where lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Az Azure Cosmos DB által biztosított, írásra optimalizált motor alapértelmezés szerint támogatja a csúcspontokon és éleken belüli összes tulajdonság automatikus indexelését. Ezért a szűrővel rendelkező lekérdezéseket, a tartománylekérdezéseket, a rendezéseket és a tulajdonságösszesítések mindegyikét a rendszer közvetlenül az indexből dolgozza fel a hatékony kiszolgálás érdekében. Az indexelésnek az Azure Cosmos DB-ben való működésével kapcsolatban a [sémafüggetlen indexelésről](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) szóló tanulmányunkban tekinthet meg további információt.

## <a name="behavior-differences"></a>Viselkedési különbségek

* Azure Cosmos DB gráf motorja a ***szélesség – első*** bejárást futtatja, miközben a TinkerPop-Gremlin a mélysége. Ez a viselkedés jobb teljesítményt érhet el vízszintesen méretezhető, például Cosmos DB.

## <a name="unsupported-features"></a>Nem támogatott funkciók

* A ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** egy programnyelvfüggetlen specifikáció gráfbejárásokhoz. Cosmos DB gráf még nem támogatja. `GremlinClient.SubmitAsync()`A bejárást szöveges karakterláncként használja és adja át.

* ***`property(set, 'xyz', 1)`*** a kardinális beállítása jelenleg nem támogatott. A `property(list, 'xyz', 1)` használható helyette. További információ: [Vertex Properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* A ***`match()` lépés*** jelenleg nem érhető el. Ez a lépés deklaratív lekérdezési képességeket biztosít.

* A csúcspontokon vagy éleken lévő ***Tulajdonságok*** nem támogatottak. A tulajdonságok csak egyszerű típusok vagy tömbök lehetnek.

* ***Rendezés tömb tulajdonságai szerint*** `order().by(<array property>)` nem támogatott. A rendezést csak az egyszerű típusok támogatják.

* A ***nem PRIMITÍV JSON-típusok*** nem támogatottak. Használjon `string` , `number` vagy `true` / `false` típusokat. `null` az értékek nem támogatottak. 

* A ***GraphSONv3*** szerializáló jelenleg nem támogatott. `GraphSONv2`Szerializáló, olvasó és író osztályok használata a kapcsolatok konfigurációjában. A Azure Cosmos DB Gremlin API által visszaadott eredmények formátuma nem egyezik meg a GraphSON formátumával. 

* **A lambda kifejezések és függvények** jelenleg nem támogatottak. Ez magában foglalja a `.map{<expression>}` , a `.by{<expression>}` , és a `.filter{<expression>}` függvényeket. Ha többet szeretne megtudni, és meg szeretné tudni, hogyan írhatók újra a Gremlin lépések használatával, tekintse [meg a lambdas-ról szóló megjegyzést](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* A rendszer elosztott jellege miatt a ***tranzakciók*** nem támogatottak.  Konfigurálja a megfelelő konzisztencia-modellt a Gremlin-fiókban a "saját írások olvasása" elemre, és használja az optimista párhuzamosságot az ütköző írások feloldásához.

## <a name="known-limitations"></a>Ismert korlátozások

* A **Gremlin lekérdezések használatának indexelése a bejárási `.V()` lépésekkel**: jelenleg csak a `.V()` bejárások első hívása fogja használni az indexet az ahhoz csatolt szűrők vagy predikátumok feloldásához. A következő hívások nem fogják megkeresni az indexet, ami növelheti a lekérdezés késését és költségeit.
    
Az alapértelmezett indexelés feltételezi, hogy a lépéssel kezdődő tipikus olvasási Gremlin-lekérdezés `.V()` paramétereket használ a csatolt szűrési lépéseiben, például a `.has()` vagy a `.where()` segítségével optimalizálja a lekérdezés költségeit és teljesítményét. Például:

```java
g.V().has('category', 'A')
```

Ha azonban `.V()` a Gremlin-lekérdezés több lépést is tartalmaz, előfordulhat, hogy a lekérdezéshez tartozó adatfelbontás nem optimális. Példaként végezze el a következő lekérdezést:

```java
g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
```

Ez a lekérdezés a csúcspontok két csoportját fogja visszaadni a megnevezett tulajdonság alapján `category` . Ebben az esetben csak az első hívás `g.V().has('category', 'A')` fogja használni az indexet, hogy feloldja a csúcspontokat a tulajdonságaik értékei alapján.

A lekérdezésre vonatkozó Áthidaló megoldás az olyan bejárási lépések használata, mint a `.map()` és a `union()` . Ez az alábbi példán alapul:

```java
// Query workaround using .map()
g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

// Query workaround using .union()
g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
```

A lekérdezések teljesítményét a [Gremlin `executionProfile()` lépés](graph-execution-profile.md)használatával tekintheti át.

## <a name="next-steps"></a>Következő lépések

* Bevezetés egy gráfalkalmazás létrehozásába [az SDK-k használatával](create-graph-dotnet.md) 
* További információk a [gráfok támogatásáról](graph-introduction.md) az Azure Cosmos DB-ben
