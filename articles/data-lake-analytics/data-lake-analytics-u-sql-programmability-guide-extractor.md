---
title: U-SQL-felhasználó által definiált kivonó programozható útmutató a Azure Data Lake
description: Ismerje meg a U-SQL UDO-alapú programozhatóság útmutatóját – a felhasználó által definiált kivonót.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608017"
---
# <a name="use-user-defined-extractor"></a>Felhasználó által definiált kinyerő használata

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: felhasználó által definiált kivonó
A U-SQL lehetővé teszi külső adatok importálását egy EXTRACT utasítás használatával. A kinyerési utasítások beépített UDO-kiállítók használatát használhatják:  

* *Extrahálók. Text ()*: a különböző kódolások tagolt szövegfájlokból való kivonást biztosít.

* *Extractors.Csv ()*: a különböző kódolású, vesszővel tagolt (CSV) fájlok kinyerését teszi lehetővé.

* *Extrahálók. TSV ()*: a különböző kódolású, tabulátorokkal tagolt (TSV) fájlokból kinyert kibontást biztosít.

Hasznos lehet egyéni kivonót létrehozni. Ez az adatok importálása során hasznos lehet, ha a következő feladatokat szeretné elvégezni:

* A bemeneti adatok módosítása oszlopok felosztásával és az egyes értékek módosításával. A processzor funkciója jobb az oszlopok kombinálásával.
* Strukturálatlan adatelemzések, például weblapok és e-mailek, vagy félig strukturálatlan, például XML/JSON típusú adat elemzése.
* Az adatelemzés nem támogatott kódolású.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Felhasználó által definiált kivonók definiálása és használata
Felhasználó által definiált Kivonó vagy UDE definiálásához létre kell hozni egy `IExtractor` felületet. A kivonó összes bemeneti paraméterét, például az oszlop/sorok elhatárolóit és a kódolást az osztály konstruktorában kell meghatározni. Az `IExtractor`  illesztőfelületnek tartalmaznia kell a felülbírálás definícióját is a `IEnumerable<IRow>` következőképpen:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

A **SqlUserDefinedExtractor** attribútum azt jelzi, hogy a típust felhasználó által definiált kivonóként kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedExtractor a UDE-definíció opcionális attribútuma. A UDE objektum AtomicFileProcessing tulajdonságának definiálására szolgál.

* bool AtomicFileProcessing   

* **true** = azt jelzi, hogy a kivonóhoz atomi bemeneti fájlok szükségesek (JSON, XML,...)
* **false** = azt jelzi, hogy ez a kivonó képes kezelni a felosztott/elosztott fájlokat (CSV, SEQ,...)

A fő UDE programozható objektumok **bemenetek** és **kimenetek**. A bemeneti objektum a bemeneti adatok enumerálására szolgál a következőként: `IUnstructuredReader` . A kimeneti objektum a kivonó tevékenység eredményeként a kimeneti adatokat adja meg.

A bemeneti adatok a és a használatával érhetők el `System.IO.Stream` `System.IO.StreamReader` .

A bemeneti oszlopok enumerálásakor a bemeneti adatfolyamot először egy sor elválasztójának használatával daraboljuk.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Ezután bontsa ki a bemeneti sort oszlop részeibe.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

A kimeneti adatokat a metódus használatával állíthatja be `output.Set` .

Fontos tisztában lenni azzal, hogy az egyéni kivonó csak a kimenetben definiált oszlopokat és értékeket jeleníti meg. Állítsa be a metódus hívását.

```csharp
output.Set<string>(count, part);
```

A tényleges kivonó kimenet a meghívásával aktiválódik `yield return output.AsReadOnly();` .

A következő a kivonó példa:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Ebben a használati helyzetben a Extractor újragenerálta a GUID azonosítóját a "GUID" oszlophoz, és a "felhasználó" oszlop értékeit nagybetűvé alakítja. Az egyéni kivonók bonyolultabb eredményeket hozhatnak létre a bemeneti adatok elemzésével és a kezelésével.

A következő olyan alap U-SQL-szkript, amely egyéni kivonót használ:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)