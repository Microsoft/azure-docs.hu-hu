---
title: SQL JOIN-lekérdezések Azure Cosmos DB
description: Ismerje meg, hogyan CSATLAKOZHAT több táblázathoz a Azure Cosmos DBban az adatlekérdezéshez
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: tisande
ms.openlocfilehash: cb7b2e62a9fabeeca675edb8e6aa356213e0999e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011391"
---
# <a name="joins-in-azure-cosmos-db"></a>Illesztések Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Egy rokon adatbázisban a táblázatok közötti illesztések a normalizált sémák megtervezéséhez használható logikaik. Ezzel szemben az SQL API a séma nélküli elemek denormalizált adatmodelljét használja, amely egy *Önillesztés* logikai megfelelője.

Az illesztések az illesztésben részt vevő készletek teljes termékét eredményezik. Az N-Way illesztés eredménye egy N-Element rekordok, ahol a rekordban lévő minden érték társítva van az összekapcsolásban részt vevő alias-készlettel, és a többi záradékban található aliasra hivatkozva elérhető.

## <a name="syntax"></a>Syntax

A nyelv támogatja a szintaxist `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` . Ez a lekérdezés egy rekordok-készletet ad vissza `N` értékekkel. Az egyes rekordhalmazok értékeit az összes tároló aliasnevének megismétlésével állítjuk be a megfelelő készleteken. 

Nézzük meg a következő FROM záradékot: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Minden forrás definiálásának engedélyezése `input_alias1, input_alias2, …, input_aliasN` . Ez a FROM záradék N-rekordok-készletet ad vissza (N értékkel rendelkező rekord). Az egyes rekordhalmazok értékeit az összes tároló aliasnevének megismétlésével állítjuk be a megfelelő készleteken.  
  
**1. példa** – 2 forrás  
  
- Legyen `<from_source1>` a tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- `<from_source2>`A dokumentumokra szűkített viszonyítási input_alias1 és a készleteket jelölő csoportok:  
  
    {1, 2} a következőhöz: `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} a következőhöz: `input_alias1 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2>` a következő rekordok eredményezi:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**2. példa** – 3 forrás  
  
- Legyen `<from_source1>` a tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- `<from_source2>`A dokumentumokra szűkített referenciák és az azokra vonatkozó `input_alias1` készletek:  
  
    {1, 2} a következőhöz: `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} a következőhöz: `input_alias1 = C,`  
  
- `<from_source3>`A dokumentumokra szűkített referenciák és az azokra vonatkozó `input_alias2` készletek:  
  
    {100, 200} a következőhöz: `input_alias2 = 1,`  
  
    {300} a `input_alias2 = 3,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok eredményezi:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > A () egyéb értékeinek rekordok hiánya, `input_alias1` `input_alias2` amelyekhez a nem adott `<from_source3>` vissza értéket.  
  
**3. példa** – 3 forrás  
  
- <from_source1> tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- Legyen `<from_source1>` a tároló hatókörű, és az {A, B, C} készletet jelöli.  
  
- <from_source2> dokumentumokra szűkíthető viszonyítási input_alias1, és beállítja a készleteket:  
  
    {1, 2} a következőhöz: `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} a következőhöz: `input_alias1 = C,`  
  
- Legyen `<from_source3>` a hatóköre `input_alias1` és a halmazok jelölése:  
  
    {100, 200} a következőhöz: `input_alias2 = A,`  
  
    {300} a `input_alias2 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok eredményezi:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Ez a és a közti termékek közti különbségeket eredményezett `<from_source2>` , `<from_source3>` a kettő pedig ugyanarra a hatókörre vonatkozik `<from_source1>` .  Ez 4 (2x2) rekordok eredményezett, amelynek értéke A 0 rekordok, amelynek értéke B (1x0) és 2 (2x1) rekordok, amelynek értéke C.  
  
## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan működik a JOIN záradék. A példák futtatása előtt töltse fel a minta [családi adatok](sql-query-getting-started.md#upload-sample-data)feltöltését. A következő példában az eredmény üres, mivel az egyes elemek forrásból és üres készletből származó szorzata üres:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Az eredmény a következőket eredményezi:

```json
    [{
    }]
```

A következő példában az illesztés egy két JSON-objektum, az elem gyökerének `id` és az algyökerének egy közti terméke `children` . Az a tény, hogy `children` egy tömb nem hatékony a csatlakozásban, mert egyetlen, a tömbhöz tartozó gyökérrel foglalkozik `children` . Az eredmény csak két eredményt tartalmaz, mivel a tömbhöz tartozó egyes elemek több mint egyetlen elemét eredményezi.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Az eredmény a következő:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Az alábbi példa egy hagyományosabb illesztést mutat be:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Az eredmény a következő:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Az JOIN záradék FROM forrása egy iteráció. Így az előző példában szereplő folyamat a következőket eredményezi:  

1. Bontsa ki a tömb minden gyermek elemét `c` .
2. Alkalmazzon egy több terméket az elem gyökerével `f` minden olyan alárendelt elemmel, `c` amelyet az első lépés összelapított.
3. Végül pedig a root Object `f` tulajdonságot csak a projektben kell megtervezni `id` .

Az első elem `AndersenFamily` csak egy `children` elemet tartalmaz, így az eredményhalmaz csak egyetlen objektumot tartalmaz. A második elem, amely `WakefieldFamily` két `children` elemet tartalmaz, így a termék két objektumot hoz létre, amelyek mindegyike egy `children` elem. Mindkét elem legfelső szintű mezői azonosak, ugyanúgy, mint a több termék esetében.

Az JOIN záradék valódi segédprogramja egy olyan alakzat rekordok, amely egyébként nehéz a projektben. Az alábbi példa egy olyan rekord kombinációján szűri a szűrőket, amely lehetővé teszi a felhasználó számára, hogy a rekordok teljes körűen kiválassza a feltételt.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Az eredmény a következő:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Az előző példa alábbi bővítménye dupla illesztést hajt végre. A terméket a következő pszeudo-kóddal tekintheti meg:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` van egy gyermeke, aki rendelkezik egy kisállattal, így a Cross termék egy sort (1 1 1 \* \* ) eredményez ebből a családból. `WakefieldFamily` két gyermeke van, akik közül csak az egyik rendelkezik háziállattal, de a gyermeknek két háziállata van. A családhoz tartozó több termék 1 – \* \* 2 = 2 sort eredményez.

A következő példában egy további szűrő van `pet` , amely kizárja az összes olyan rekordok, ahol a PET neve nem `Shadow` . A tömbökből létrehozhat rekordok, szűrheti a rekord bármelyik elemét, és az elemek bármely kombinációját kialakíthatja.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Az eredmény a következő:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Ha a lekérdezés ILLESZTÉSt és szűrőket tartalmaz, a teljesítmény javítása érdekében a lekérdezés egy részét újraírhatja [allekérdezésként](sql-query-subquery.md#optimize-join-expressions) .

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Segédlekérdezések](sql-query-subquery.md)