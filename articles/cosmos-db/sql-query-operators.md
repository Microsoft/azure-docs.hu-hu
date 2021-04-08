---
title: A Azure Cosmos DB SQL-lekérdezési operátorai
description: Ismerje meg az olyan SQL-operátorokat, mint az egyenlőség, az összehasonlítás és a Azure Cosmos DB által támogatott logikai operátorok.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335439"
---
# <a name="operators-in-azure-cosmos-db"></a>A Azure Cosmos DB operátorai
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk a Azure Cosmos DB által támogatott különböző operátorokat részletezi.

## <a name="equality-and-comparison-operators"></a>Egyenlőségi és összehasonlító operátorok

Az alábbi táblázat a két JSON-típus közötti egyenlőség összehasonlításának eredményét mutatja az SQL API-ban.

| **Op** | **Nem definiált** | **Null** | **Logikai** | **Szám** | **Sztring** | **Objektum** | **Tömb** |
|---|---|---|---|---|---|---|---|
| **Nem definiált** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Null** | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Logikai** | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Szám** | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált |
| **Sztring** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált |
| **Objektum** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált |
| **Tömb** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** |

Az összehasonlító operátorok, például a,,, `>` `>=` `!=` `<` , és `<=` , összehasonlítás különböző típusok vagy két objektum vagy tömb között `Undefined` .  

Ha a skaláris kifejezés eredménye `Undefined` , az elem nem szerepel az eredményben, mert `Undefined` nem egyenlő `true` .

Például a következő lekérdezés összehasonlítása egy szám és egy karakterlánc értékkel `Undefined` . Ezért a szűrő nem tartalmaz eredményt.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>Logikai (és, vagy nem) operátorok

Logikai operátorok logikai értékeken működnek. Az alábbi táblázatok a logikai igazság táblázatait mutatják be a következő operátorok esetében:

**VAGY operátor**

`true`A feltételek bármelyikének beolvasása `true` .

|  | **True** | **False** | **Nem definiált** |
| --- | --- | --- | --- |
| **True** |Igaz |Igaz |Igaz |
| **False** |Igaz |Hamis |Nem definiált |
| **Nem definiált** |Igaz |Nem definiált |Nem definiált |

**ÉS operátor**

Azt adja vissza `true` , hogy mindkét kifejezés hol van `true` .

|  | **True** | **False** | **Nem definiált** |
| --- | --- | --- | --- |
| **True** |Igaz |Hamis |Nem definiált |
| **False** |Hamis |Hamis |Hamis |
| **Nem definiált** |Nem definiált |Hamis |Nem definiált |

**NEM operátor**

Bármely logikai kifejezés értékének megfordítása.

|  | **NOT** |
| --- | --- |
| **True** |Hamis |
| **False** |Igaz |
| **Nem definiált** |Nem definiált |

**Operátori prioritás**

A logikai operátorok, `OR` `AND` és `NOT` az alább látható elsőbbségi szinttel rendelkeznek:

| **Operátor** | **Prioritás** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>* operátor

A speciális operátor * a teljes tételt a következőképpen tervezi. Használata esetén az csak az egyetlen tervezett mező lehet. A hasonló lekérdezés `SELECT * FROM Families f` érvényes, de `SELECT VALUE * FROM Families f`  `SELECT *, f.id FROM Families f` nem érvényes.

## <a name="-and--operators"></a>? és? operátorok

A Ternáris (?) és az egyesítő (??) operátorok feltételes kifejezéseket hozhatnak létre, például a C# és a JavaScript programozási nyelveken.

Használhatja a? operátor az új JSON-tulajdonságok menet közbeni létrehozásához. Például a következő lekérdezés osztályozza a minősítési szinteket a vagy a értékre `elementary` `other` :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

A hívások beágyazására is lehetőség van? operátor, a következő lekérdezéshez hasonlóan: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Más lekérdezési operátorokhoz hasonlóan a? a kezelő kizárja az elemeket, ha a hivatkozott tulajdonságok hiányoznak, vagy az összehasonlítható típusok eltérnek.

Használja a?? az operátor a részben strukturált vagy kevert típusú adattípusok elleni lekérdezés során hatékonyan keres egy adott elemmel. Például a következő lekérdezés visszatér `lastName` , ha van, vagy `surname` Ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kulcsszavak](sql-query-keywords.md)
- [SELECT záradék](sql-query-select.md)
