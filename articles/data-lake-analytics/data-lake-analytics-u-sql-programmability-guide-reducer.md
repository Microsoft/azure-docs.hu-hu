---
title: A U-SQL felhasználó által definiált redukáló programozható útmutatója Azure Data Lake
description: További információ az U-SQL UDO programozható útmutatóról – a felhasználó által definiált szűkítő.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512565"
---
# <a name="use-user-defined-reducer"></a>Felhasználó által definiált csökkentő használata

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: felhasználó által definiált szűkítő

Az U-SQL lehetővé teszi egyéni sorhalmaz-szűkítők írását a C# nyelvben a felhasználó által definiált kezelői keretrendszer használatával és egy IReducer felület megvalósításával.

A felhasználó által definiált szűkítő vagy UDR felhasználható a szükségtelen sorok eltávolítására az kinyeréskor (importáláskor). Emellett a sorok és oszlopok kezelésére és értékelésére is használható. A programozható logika alapján azt is meghatározhatja, hogy mely sorokat kell kinyerni.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Felhasználó által definiált szűkítő definiálása és használata
UDR osztály definiálásához egy `IReducer` opcionális attribútummal rendelkező felületet kell létrehozni `SqlUserDefinedReducer` .

Ez az osztály-illesztőfelületnek tartalmaznia kell egy definíciót a `IEnumerable` kapcsolati sorhalmaz felülbírálásához.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

A **SqlUserDefinedReducer** attribútum azt jelzi, hogy a típust felhasználó által definiált szűkítőként kell regisztrálni. Ez az osztály nem örökölhető.
A **SqlUserDefinedReducer** egy felhasználó által definiált csökkentő definíció opcionális attribútuma. A IsRecursive tulajdonság definiálására szolgál.

* bool IsRecursive    
* **true**  = azt jelzi, hogy ez a szűkítő asszociatív és kommutatív

A fő programozható objektumok **bemenetek** és **kimenetek**. A bemeneti objektum a bemeneti sorok enumerálására szolgál. A kimenet a tevékenységek csökkentésének eredményeképpen kimeneti sorok beállítására szolgál.

A bemeneti sorok enumerálásakor a `Row.Get` metódust használjuk.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

A metódus paramétere a `Row.Get` `PRODUCE` `REDUCE` U-SQL alap parancsfájl utasításának részeként átadott oszlop. Itt is a megfelelő adattípust kell használnia.

A kimenethez használja a `output.Set` metódust.

Fontos megérteni, hogy az egyéni csökkentő csak a metódus hívásával megadott értékeket adja meg `output.Set` .

```csharp
output.Set<string>("mycolumn", guid);
```

A tényleges csökkentő kimenet a hívásával aktiválódik `yield return output.AsReadOnly();` .

A következő egy rövidített példa:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Ebben a használati helyzetben a csökkentő kihagyja a sorokat üres felhasználónévvel. A sorhalmaz minden egyes sorában beolvassa az egyes szükséges oszlopokat, majd kiértékeli a Felhasználónév hosszát. A tényleges sort csak akkor adja eredményül, ha a Felhasználónév értéke 0-nál nagyobb.

A következő olyan alap U-SQL-szkript, amely egyéni szűkítőt használ:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)