---
title: SQL-állandók a Azure Cosmos DBban
description: Ismerje meg, hogyan használhatók az SQL-lekérdezési állandók a Azure Cosmos DBban egy adott adatérték ábrázolásához
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339202"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL-lekérdezés állandói  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy konstans, más néven literál vagy skaláris érték, egy adott adatértéket jelölő szimbólum. A konstans formátuma az általa reprezentált érték adattípusától függ.  
  
 **Támogatott skaláris adattípusok:**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Nem definiált**|Egyetlen érték: nem **definiált**|  
|**Null**|Egyetlen érték: **Null**|  
|**Logikai**|Értékek: **false**, **true**.|  
|**Szám**|Egy kétszeres pontosságú lebegőpontos szám, IEEE 754 standard.|  
|**Sztring**|Nulla vagy több Unicode-karakterből álló sorozatot. A karakterláncokat szimpla vagy idézőjelek közé kell foglalni.|  
|**Tömb**|Nulla vagy több elemből álló sorozatot. Minden elem bármely skaláris adattípus értéke lehet, kivéve a nem **definiált** értéket.|  
|**Objektum**|Nulla vagy több név/érték párok rendezetlen készlete. A név egy Unicode karakterlánc, az érték bármely skaláris adattípus lehet, kivéve a nem **definiált** értéket.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Argumentumok
  
* `<undefined_constant>; Undefined`  
  
  Nem definiált típusú, nem definiált értéket képvisel.  
  
* `<null_constant>; null`  
  
  NULL típusú **Null** értéket jelöl **.**  
  
* `<boolean_constant>`  
  
  Boolean típusú konstanst jelöl.  
  
* `false`  
  
  A logikai típusú **hamis** értéket jelöli.  
  
* `true`  
  
  Boolean típusú **igaz** értéket jelöl.  
  
* `<number_constant>`  
  
  Állandót jelöl.  
  
* `decimal_literal`  
  
  A decimális literál számok decimális jelöléssel vagy tudományos jelöléssel jelennek meg.  
  
* `hexadecimal_literal`  
  
  A hexadecimális literálok a "0x" előtaggal jelölt számok, amelyeket egy vagy több hexadecimális számjegy követ.  
  
* `<string_constant>`  
  
  Karakterlánc típusú konstanst jelöl.  
  
* `string _literal`  
  
  A karakterlánc-literálok olyan Unicode-karakterláncok, amelyek nulla vagy több Unicode-karakter vagy Escape-kódrészletek sorozatából állnak. A karakterlánc-literálok aposztrófok (aposztróf: ") vagy idézőjelek (idézőjel:") közé vannak bejelölve.  
  
  A következő Escape-kódrészletek engedélyezettek:  
  
|**Escape-sorozatot**|**Leírás**|**Unicode-karakter**|  
|-|-|-|  
|\\'|aposztróf (')|U + 0027|  
|\\"|idézőjel (")|U + 0022|  
|\\\ |fordított solidus ( \\ )|U + 005C|  
|\\/|solidus (/)|U + 002F|  
|\b|Backspace|U + 0008|  
|\f|űrlap-hírcsatorna|U + 000C|  
|\n|soremelés|U + 000A|  
|\r| kocsivissza|U + 000D|  
|\t|lapon|U + 0009|  
|\uXXXX|4 hexadecimális számjegy által definiált Unicode-karakter.|U + XXXX|  

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)
