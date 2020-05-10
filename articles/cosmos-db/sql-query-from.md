---
title: FROM záradék Azure Cosmos DB
description: Ismerje meg az SQL-szintaxist, és példát az Azure Cosmos DB FROM záradékra. Ez a cikk példákat is mutat a hatókör eredményeire, és a FROM záradék használatával beolvassa az alárendelt elemeket.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: e4bbb27a2f49027ed5a456ad824f54b9c92a899c
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005857"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM záradék Azure Cosmos DB

A FROM (`FROM <from_specification>`) záradék nem kötelező, kivéve, ha a forrást a lekérdezésben később szűrik vagy tervezték. Egy lekérdezés, `SELECT * FROM Families` például enumerálások a teljes `Families` tárolón. A tároló neve helyett a speciális azonosító GYÖKERÉt is használhatja a tárolóhoz.

A `FROM` záradék a következő szabályokat érvényesíti a lekérdezéseken:

* A tároló lehet alias, például `SELECT f.id FROM Families AS f` vagy egyszerűen. `SELECT f.id FROM Families f` Itt `f` látható az alias a `Families`következőhöz:. MINT egy opcionális kulcsszó az azonosító [aliasához](sql-query-working-with-json.md#aliasing) .  

* Az alias megadása után az eredeti forrás neve nem köthető. Például a szintaktikai `SELECT Families.id FROM Families f` érvénytelen, mert az azonosító `Families` aliast kapott, és többé nem oldható fel.  

* Minden hivatkozott tulajdonságnak teljes mértékben minősítettnek kell lennie, hogy elkerülje a nem egyértelmű kötéseket a szigorú sémák betartásának hiányában. Például a szintaktikai `SELECT id FROM Families f` érvénytelen, mert a tulajdonság `id` nincs kötve.

## <a name="syntax"></a>Szintaxis
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=
        ROOT
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumentumok
  
- `<from_source>`  
  
  Egy adatforrás megadása aliassal vagy anélkül. Ha nincs megadva alias, a rendszer a `<container_expression>` következő szabályok használatával következtet:  
  
-  Ha a kifejezés container_name, akkor container_name aliasként lesz használva.  
  
-  Ha a kifejezés `<container_expression>`, akkor property_name, akkor property_name aliasként lesz használva. Ha a kifejezés container_name, akkor container_name aliasként lesz használva.  
  
- MINT`input_alias`  
  
  Megadja, hogy `input_alias` a a mögöttes tároló kifejezés által visszaadott értékek halmaza.  
 
- `input_alias`A  
  
  Azt adja meg `input_alias` , hogy a megfelelő értéknek kell lennie az alapul szolgáló tároló kifejezés által visszaadott tömb összes tömb elemének megismétlésével. A mögöttes tároló kifejezés által visszaadott összes érték figyelmen kívül lesz hagyva.  
  
- `<container_expression>`  
  
  Meghatározza a dokumentumok beolvasásához használandó tároló kifejezését.  
  
- `ROOT`  
  
  Megadja, hogy a rendszer beolvassa a dokumentumot az alapértelmezett, jelenleg csatlakoztatott tárolóból.  
  
- `container_name`  
  
  Megadja, hogy a dokumentumot a megadott tárolóból kell beolvasni. A tároló nevének meg kell egyeznie a jelenleg csatlakoztatott tároló nevével.  
  
- `input_alias`  
  
  Megadja, hogy a dokumentumot a megadott alias által definiált másik forrásból kell beolvasni.  
  
- `<container_expression> '.' property_name`  
  
  Megadja, hogy a dokumentumot a `property_name` tulajdonság elérésével kell beolvasni.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Azt adja meg, hogy a `property_name` dokumentumot a megadott tároló kifejezés által beolvasott összes dokumentum tulajdonságához vagy array_index Array eleméhez való hozzáféréssel kell lekérni.  
  
## <a name="remarks"></a>Megjegyzések
  
Az `<from_source>(`összes megadott vagy arra hivatkozó aliasnak egyedinek kell lennie. A szintaxis `<container_expression>.`property_name ugyanaz, mint `<container_expression>' ['"property_name"']'`. Az utóbbi szintaxis azonban akkor használható, ha egy tulajdonság neve nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzó tömb elemek és nem definiált értékek kezelésére
  
Ha egy tároló kifejezése hozzáfér a Tulajdonságok vagy a tömb elemeihez, és ez az érték nem létezik, a rendszer figyelmen kívül hagyja ezt az értéket, és nem dolgozza fel őket.  
  
### <a name="container-expression-context-scoping"></a>Container kifejezés kontextusának hatóköre  
  
A Container kifejezés lehet tároló-hatókörű vagy dokumentum – hatókör:  
  
- A kifejezés tároló hatókörű, ha a tároló kifejezésének alapjául szolgáló forrása a ROOT vagy `container_name`a. Ez a kifejezés a tárolóból közvetlenül beolvasott dokumentumok készletét jelöli, és nem függ más tároló-kifejezések feldolgozásából.  
  
- A kifejezés dokumentum-hatókörű, ha a lekérdezésben korábban a tároló kifejezés alapjául szolgáló `input_alias` forrás van bevezetve. Ez a kifejezés a tároló kifejezésnek az alias-tárolóhoz társított készlethez tartozó hatókörében való kiértékelésével beszerzett dokumentumok készletét jelöli. Az eredményül kapott készlet az alapul szolgáló készletben lévő összes dokumentum tároló kifejezésének kiértékelésével létrejött készletek Uniója lesz.

## <a name="examples"></a>Példák

### <a name="get-subitems-by-using-the-from-clause"></a>Alelemek lekérése a FROM záradék használatával

A FROM záradék kisebb részhalmazra csökkentheti a forrást. Ahhoz, hogy az egyes elemekben csak egy részfa legyen felsorolva, az algyökér forrása lehet, ahogy az az alábbi példában látható:

```sql
    SELECT *
    FROM Families.children
```

Az eredmény a következő:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Az előző lekérdezés a tömböt használta forrásként, de egy objektumot is használhat forrásként. A lekérdezés az eredménybe való felvételhez a forrásban érvényes, megadott JSON-értéket veszi figyelembe. Az alábbi példa kizárja `Families` , hogy ne legyen `address.state` érték.

```sql
    SELECT *
    FROM Families.address.state
```

Az eredmény a következő:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>További lépések

- [Első lépések](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)
