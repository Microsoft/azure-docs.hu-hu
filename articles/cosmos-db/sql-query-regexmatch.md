---
title: RegexMatch Azure Cosmos DB lekérdezési nyelven
description: Tudnivalók a RegexMatch SQL System függvényéről Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341637"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Reguláris kifejezési képességeket biztosít. A reguláris kifejezések tömör és rugalmas jelölést biztosítanak a szöveg mintázatának megállapításához. A Azure Cosmos DB [Perl-kompatibilis reguláris kifejezéseket (PCRE)](http://www.pcre.org/)használ. 

## <a name="syntax"></a>Szintaxis
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A keresendő karakterlánc-kifejezés.  
  
*str_expr2*  
   A reguláris kifejezés.

*str_expr3*  
   A reguláris kifejezéssel használandó kiválasztott módosítók karakterlánca. Ez a karakterlánc-érték nem kötelező. Ha a RegexMatch-t nem módosítók nélkül szeretné futtatni, hozzáadhat egy üres karakterláncot, vagy kihagyhat teljes egészében. 

Megtudhatja, hogyan [hozhat létre reguláris kifejezéseket a perlban a szintaxis](https://perldoc.perl.org/perlre)használatával. 

A Azure Cosmos DB a következő négy módosítót támogatja:

| Módosító | Leírás |
| ------ | ----------- |
| `m` | A karakterlánc-kifejezést több sorba való kereséshez kell kezelnie. A beállítás nélkül a "^" és a "$" egyeztetése a karakterlánc elején vagy végén, és nem minden egyes sorban történik. |
| `s` | A "." engedélyezése bármely karakternek, beleértve a sortörési karaktert is. | 
| `i` | Eset figyelmen kívül hagyása, ha a mintázat megfeleltetése. |
| `x` | Az összes szóköz figyelmen kívül hagyása. |

## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza. A nem definiált értéket adja vissza, ha a keresendő karakterlánc-kifejezés, a reguláris kifejezés vagy a kiválasztott módosító érvénytelen.
  
## <a name="examples"></a>Példák
  
A következő egyszerű RegexMatch példa az "ABCD" karakterláncot ellenőrzi a reguláris kifejezések egyezéséhez néhány különböző módosító használatával.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

A RegexMatch használatával a metakarakterek összetettebb karakterlánc-kereséseket végezhet, amelyek egyébként nem lennének lehetségesek a StartsWith, a EndsWith, a tartalmaz vagy a StringEquals rendszerfunkciókkal. Íme néhány további példa, amely a [Azure Cosmos DB lekérdezési demókörnyezet](https://www.documentdb.com/sql/demo)által elérhető táplálkozási adathalmaz használatával futtatható. 

> [!NOTE] 
> Ha egy metakarakter reguláris kifejezésben kell használnia, és nem szeretné, hogy a speciális jelentéssel rendelkezzen, akkor el kell menekülnie a metakarakter a használatával `\` .

**A "Salt" szót tartalmazó leírással rendelkező elemeket pontosan egyszer kell megnéznie:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**A 0 és 99 közötti számot tartalmazó leírással rendelkező elemek keresése:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Az "S" vagy "s" karaktertől kezdődő négy betűből álló leírást tartalmazó elemek keresése:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználhatja a [tartomány indexét](index-policy.md#includeexclude-strategy) , ha a reguláris kifejezés a StartsWith, a EndsWith, a tartalmaz vagy a StringEquals rendszerfunkciókkal is bontható.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
