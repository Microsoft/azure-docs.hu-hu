---
title: A U-SQL felhasználó által definiált processzor-programozási útmutatója Azure Data Lake
description: Ismerje meg az U-SQL UDO-alapú programozhatóság útmutatóját – a felhasználó által definiált processzort.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512597"
---
# <a name="use-user-defined-processor"></a>Felhasználó által definiált feldolgozó használata

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: felhasználó által definiált processzor
A felhasználó által definiált processzor vagy UDP a U-SQL UDO egyik típusa, amely lehetővé teszi a bejövő sorok feldolgozását a programozható funkciók alkalmazásával. Az UDP lehetővé teszi az oszlopok egyesítését, az értékek módosítását, és szükség esetén új oszlopok hozzáadását. Alapvetően segít a sorhalmaz feldolgozása a szükséges adatelemek létrehozásához.

## <a name="how-to-define-and-use-user-defined-processor"></a>Felhasználó által definiált processzor definiálása és használata
Az UDP definiálásához létre kell hozni egy `IProcessor` illesztőfelületet az `SqlUserDefinedProcessor` attribútummal, amely nem kötelező az UDP számára.

Az illesztőfelületnek tartalmaznia kell a `IRow` kapcsolati sorhalmaz felülbírálásának definícióját, ahogy az az alábbi példában is látható:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

A **SqlUserDefinedProcessor** azt jelzi, hogy a típust felhasználó által definiált processzorként kell regisztrálni. Ez az osztály nem örökölhető.

Az SqlUserDefinedProcessor attribútum nem **kötelező** az UDP-definícióhoz.

A fő programozható objektumok **bemenetek** és **kimenetek**. A bemeneti objektum a bemeneti oszlopok és kimenetek enumerálására és a kimeneti adatok beállítására szolgál a processzor tevékenységének eredményeképpen.

A bemeneti oszlopok enumerálásához a `input.Get` metódust használjuk.

```csharp
string column_name = input.Get<string>("column_name");
```

A metódus paramétere a `input.Get` `PRODUCE` `PROCESS` U-SQL alapparancsfájl utasításának részeként átadott oszlop. Itt a megfelelő adattípust kell használnia.

A kimenethez használja a `output.Set` metódust.

Fontos megjegyezni, hogy az egyéni gyártó csak a metódus hívásával definiált oszlopokat és értékeket jeleníti meg `output.Set` .

```csharp
output.Set<string>("mycolumn", mycolumn);
```

A processzor tényleges kimenete a hívásával aktiválódik `return output.AsReadOnly();` .

A következő példa egy processzort mutat be:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Ebben a használati helyzetben a processzor egy "full_description" nevű új oszlopot hoz létre a meglévő oszlopok kombinálásával – ebben az esetben a "felhasználó" a nagybetűs és a "des". Emellett újra létrehozza a GUID azonosítót, és visszaadja az eredeti és az új GUID-értéket.

Ahogy az előző példában is látható, meghívhatja a C# metódusokat a `output.Set` metódus hívása során.

Az alábbi példa olyan alap U-SQL-parancsfájlt mutat be, amely egyéni processzort használ:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)