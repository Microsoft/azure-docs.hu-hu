---
title: Felhasználó által definiált függvények (UDF) Azure Cosmos DB
description: A Azure Cosmos DB felhasználó által definiált függvények megismerése.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011123"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Felhasználó által definiált függvények (UDF) Azure Cosmos DB

Az SQL API támogatást nyújt a felhasználó által definiált függvények (UDF) számára. A skaláris UDF használatával nulla vagy több argumentumot adhat meg, és egyetlen argumentumot ad vissza eredményként. Az API ellenőrzi, hogy az egyes argumentumok jogi JSON-értékekként vannak-e megadva.  

## <a name="udf-use-cases"></a>UDF használati esetek

Az API kiterjeszti az SQL szintaxisát, hogy támogassa az egyéni alkalmazás-logikát a UDF használatával. A UDF az SQL API-val regisztrálhatja, és SQL-lekérdezésekben hivatkozhat rájuk. A tárolt eljárásokkal és eseményindítókkal ellentétben a UDF csak olvashatók.

A UDF használatával kiterjesztheti Azure Cosmos DB lekérdezési nyelvét. A UDF nagyszerű lehetőséget biztosít összetett üzleti logikák kiértékelésére egy lekérdezés kivetítésében.

Javasoljuk azonban a UDF elkerülését, ha:

- Egy egyenértékű [rendszerfunkció](sql-query-system-functions.md) már létezik a Azure Cosmos DBban. A System functions mindig kevesebb RU-t használ, mint az egyenértékű UDF.
- Az UDF az egyetlen szűrő a lekérdezés `WHERE` záradékában. Az UDF nem használja az indexet, ezért az UDF kiértékeléséhez dokumentumokat kell betölteni. Az indexet használó további szűrési predikátumok és az UDF együttes használata esetén a `WHERE` záradékban az UDF által feldolgozott dokumentumok száma is csökken.

Ha ugyanazokat az UDF-ket kell használnia egy lekérdezésben, hivatkoznia kell az UDF-re egy [allekérdezésben](sql-query-subquery.md#evaluate-once-and-reference-many-times), így egy JOIN kifejezéssel kiértékelheti az UDF-t egyszer, de többször is hivatkozhat rájuk.

## <a name="examples"></a>Példák

Az alábbi példa egy UDF-tárolót regisztrál a Cosmos adatbázisban. A példa létrehoz egy UDF-t, `REGEX_MATCH`amelynek a neve:. Két JSON-karakterlánc-értéket `input` fogad el `pattern`, és ellenőrzi, hogy az első a JavaScript `string.match()` függvény használatával a másodikban megadott mintázattal egyezik-e.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Most használja ezt az UDF-t egy lekérdezési leképezésben. A UDF a kis-és nagybetűket megkülönböztető `udf.` előtaggal kell rendelkeznie, ha lekérdezéseken belülről hívja őket.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Az eredmény a következő:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

A szűrőn belüli `udf.` előtaggal minősített UDF-t használhatja az alábbi példában látható módon:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Az eredmény a következő:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Lényegében a UDF érvényes skaláris kifejezések, amelyeket kivetítések és szűrők is használhatnak.

Ha ki szeretné bővíteni a UDF erejét, tekintse meg a feltételes logikát bemutató példát:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Az alábbi példa az UDF-t alkalmazza:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Az eredmény a következő:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Ha az UDF-paraméterek által hivatkozott tulajdonságok nem érhetők el a JSON-értékben, a paraméter nem definiált minősül, és az UDF-hívás kimarad. Hasonlóképpen, ha az UDF eredménye nincs meghatározva, az eredmény nem tartalmazza azt.

Ahogy az előző példákban is látható, a UDF integrálja a JavaScript nyelv erejét az SQL API-val. A UDF a beépített JavaScript-futtatókörnyezeti funkciókkal összetett eljárási, feltételes logikát biztosító, sokoldalú, programozható felületet biztosít. Az SQL API megadja a UDF argumentumait az egyes forrásoldali elemeknél az aktuális WHERE vagy SELECT záradékban a feldolgozáshoz. Az eredmény zökkenőmentesen be van építve a teljes végrehajtási folyamatba. Összefoglalva, a UDF nagyszerű eszközei a lekérdezések részeként összetett üzleti logikának.

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Rendszerfüggvények](sql-query-system-functions.md)
- [Összesítések](sql-query-aggregates.md)