---
title: Azure Cosmos DB SQL-kulcsszavai
description: A Azure Cosmos DB SQL-kulcsszavainak megismerése.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 1f3c4ef56feb77e9b01375b8b5dbdb567f5bfadb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179969"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB kulcsszavai
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk a Azure Cosmos DB SQL-lekérdezésekben felhasználható kulcsszavakat részletezi.

## <a name="between"></a>BETWEEN

A kulcsszó használatával a `BETWEEN` lekérdezéseket karakterláncok vagy numerikus értékek tartományán keresztül fejezheti ki. Például a következő lekérdezés az összes olyan elemet adja vissza, amelyben az első gyermek 1-5-as osztálya.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

A `BETWEEN` kulcsszót a záradékban is használhatja `SELECT` , ahogy az alábbi példában is látható.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Az SQL API-ban – az ANSI SQL-től eltérően – különböző típusú lekérdezéseket adhat meg a vegyes típusok tulajdonságainál. Előfordulhat például, hogy az `grade` `5` egyes elemekhez és a másokhoz hasonló sztringek száma `grade4` . Ezekben az esetekben, ahogy a JavaScriptben, a két különböző típus összehasonlítása eredményezi `Undefined` , így az elem kimarad.

## <a name="distinct"></a>DISTINCT

A `DISTINCT` kulcsszó kiküszöböli az ismétlődéseket a lekérdezés leképezésében.

Ebben a példában a lekérdezési projektek értékei az egyes vezetéknevek esetében:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Az eredmény a következő:

```json
[
    "Andersen"
]
```

Emellett egyedi objektumokat is létrehozhat. Ebben az esetben a lastName mező nem létezik a két dokumentum egyikében sem, így a lekérdezés üres objektumot ad vissza.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Az eredmény a következő:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` egy allekérdezésen belüli kivetítésben is használható:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Ez a lekérdezés egy tömböt hoz létre, amely tartalmazza az egyes gyermekekhez tartozó givenName, amelyekben duplikált elemek törlődnek. Ez a tömb ChildNames, és a külső lekérdezésben is szerepel.

Az eredmény a következő:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Az összesített rendszerfunkcióval és allekérdezéssel rendelkező lekérdezések `DISTINCT` nem támogatottak. Például a következő lekérdezés nem támogatott:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>PÉLDÁUL

Egy logikai értéket ad vissza attól függően, hogy egy adott karakterlánc megfelel-e egy megadott mintának. A minta tartalmazhat normál karaktereket és helyettesítő karaktereket. Logikusan egyenértékű lekérdezéseket írhat a `LIKE` kulcsszó vagy a [RegexMatch](sql-query-regexmatch.md) System függvény használatával. Ugyanazt az indexelési kihasználtságot kell figyelembe vennie, függetlenül attól, hogy melyiket választja. Ezért akkor érdemes használni, `LIKE` Ha a szintaxisa nagyobb, mint a reguláris kifejezés.

> [!NOTE]
> Mivel `LIKE` a képes indexet használni, [létre kell hoznia egy tartomány-indexet](./index-policy.md) a használatával összehasonlítható tulajdonságokhoz `LIKE` .

A következő helyettesítő karaktereket használhatja hasonló módon:

| Helyettesítő karakter | Leírás                                                  | Példa                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Bármely nulla vagy több karakterből álló sztring                      | WHERE c. Description LIKE "% SO% PS%"      |
| _ (aláhúzás)     | Egyetlen karakter                                       | WHERE c. Description LIKE "% SO_PS%"      |
| [ ]                  | A megadott tartományon belüli egyetlen karakter ([a-f]) vagy a set ([ABCDEF]). | AHOL c. Description LIKE "% SO [t-z] PS%"  |
| [^]                  | A megadott tartományba ([^ a-f]) vagy a set ([^ ABCDEF]) nem tartozó egyetlen karakter. | AHOL c. Description LIKE "% SO [^ ABC] PS%" |


### <a name="using-like-with-the--wildcard-character"></a>Használat a (z)% helyettesítő karakterrel

A `%` karakter bármely nulla vagy több karakterből álló sztringnek felel meg. Például a `%` minta elején és végén a következő lekérdezés visszaadja az összes olyan elemet, amely tartalmaz egy leírást, amely tartalmazza a következőket `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Ha csak a `%` minta végén használt karaktert használta, csak olyan elemeket ad vissza, amelyek a következővel kezdődnek `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>NEM hasonló használata

Az alábbi példa az összes olyan elemet visszaadja, amely tartalmaz egy leírást, amely nem tartalmazza a következőt `fruit` :

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>A Escape záradék használata

A ESCAPE záradék használatával olyan mintákat kereshet, amelyek egy vagy több helyettesítő karaktert tartalmaznak. Ha például a sztringet tartalmazó leírások keresését szeretné megkeresni `20-30%` , azt nem érdemes `%` helyettesítő karakterként értelmezni.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Helyettesítő karakterek használata literálként

Zárójelben lévő helyettesítő karakterekkel is rendelkezhet, hogy literál karakterként kezeljék őket. Ha egy helyettesítő karaktert szögletes zárójelben, akkor minden speciális attribútumot el kell távolítania. Íme néhány példa:

| Mintázat           | Értelmezés |
| ----------------- | ------- |
| PÉLDÁUL: "20-30 [%]" | 20-30%  |
| PÉLDÁUL "[_] n"     | _n      |
| PÉLDÁUL: "[[]"    | [       |
| PÉLDÁUL "]"        | ]       |

## <a name="in"></a>IN

A IN kulcsszó használatával megvizsgálhatja, hogy egy adott érték egyezik-e egy lista bármely értékével. Például a következő lekérdezés az összes olyan családi elemet adja vissza, ahol a `id` `WakefieldFamily` vagy a `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

A következő példa azokat az elemeket adja vissza, amelyekben az állapot a megadott értékek bármelyike:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Az SQL API támogatást nyújt a [JSON-tömbök megismétléséhez](sql-query-object-array.md#Iteration), és egy új, a from forrásban található kulcsszóval hozzáadott összeállítást biztosít.

Ha a szűrőben tartalmazza a partíciós kulcsot `IN` , a lekérdezés automatikusan csak a megfelelő partíciókat fogja szűrni.

## <a name="top"></a>TOP

A felső kulcsszó a `N` lekérdezés eredményeinek első számát adja vissza nem definiált sorrendben. Az ajánlott eljárás a TOP és a záradék használata az `ORDER BY` eredményeknek az első `N` számú rendezett értékre való korlátozásához. A két záradék összevonása az egyetlen módszer arra, hogy előre jelezze, hogy mely sorok érintik a LEGFONTOSABBat.

A TOP értéket állandó értékkel használhatja, ahogy az alábbi példában, vagy egy változó értékkel, amely paraméteres lekérdezéseket használ.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Az eredmény a következő:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [Illesztések](sql-query-join.md)
- [Segédlekérdezések](sql-query-subquery.md)
