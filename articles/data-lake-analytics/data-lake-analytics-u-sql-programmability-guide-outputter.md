---
title: U-SQL-felhasználó által definiált Azure Data Lake
description: Ismerje meg a U-SQL UDO-alapú programozhatóság útmutatóját, amelyet a felhasználó definiált.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512568"
---
# <a name="use-user-defined-outputter"></a>Felhasználó által definiált kiíró használata

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: felhasználó által definiált leszerkesztő
A felhasználó által definiált egy másik U-SQL UDO, amely lehetővé teszi a beépített U-SQL-funkciók kiterjesztését. A kivonóhoz hasonlóan több beépített kimenet is található.

* Kimenetek *. Text ()*: a különböző kódolású szövegfájlba írja az adatot.
* *Outputters.Csv ()*: a különböző kódolású, vesszővel tagolt (CSV) fájlokba írja az adatok értékét.
* Kimenetek *. TSV ()*: a különböző kódolású, tabulátorokkal tagolt (TSV) fájlokba írja az adatok.

Az egyéni leválasztó lehetővé teszi, hogy egyéni formátumban írja az adatbevitelt. Ez a következő feladatokhoz lehet hasznos:

* Adat írása részben strukturált vagy strukturálatlan fájlokra.
* Az adatírás nem támogatott kódolást eredményez.
* Kimeneti adatokat módosíthat vagy egyéni attribútumokat adhat hozzá.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Felhasználó által definiált leszerkesztő megadása és használata
A felhasználó által definiált megadáshoz létre kell hozni a `IOutputter` felületet.

Az `IOutputter` alaposztály implementációja a következő:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

A kijelölő összes bemeneti paraméterét, például az oszlop/sorok határolóit, a kódolást és így tovább, az osztály konstruktorában kell meghatározni. Az `IOutputter` illesztőfelületnek tartalmaznia kell a felülbírálás definícióját is `void Output` . Az attribútum `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` beállítható a atomi fájlok feldolgozásához. További információ: az alábbi részletek.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` minden bemeneti sorhoz meghívva. Visszaadja a `IUnstructuredWriter output` sorhalmazt.
* A konstruktor osztály paraméterek átadására szolgál a felhasználó által definiált leszerkesztőhöz.
* `Close` a nem kötelezően felülbírálja a költséges állapotot, vagy meghatározhatja az utolsó sor megírását.

A **SqlUserDefinedOutputter** attribútum azt jelzi, hogy a típust felhasználó által definiált előtagjaként kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedOutputter egy nem kötelező attribútum a felhasználó által definiált leválasztási definícióhoz. A AtomicFileProcessing tulajdonság definiálására szolgál.

* bool AtomicFileProcessing   

* **igaz** = azt jelzi, hogy ez a kijelölő atomi kimeneti fájlokat igényel (JSON, XML,...)
* **false** = azt jelzi, hogy ez a leválasztó képes kezelni a felosztott/elosztott fájlokat (CSV, SEQ,...)

A fő programozható objektumok a **sor** és a **kimenet**. A **sor** objektum a kimeneti adatokat illesztőfelületként való enumerálásra használja `IRow` . A **kimenet** a célfájl kimeneti értékének megadására szolgál.

A kimeneti adatokat a felületén keresztül érheti el a rendszer `IRow` . A kimeneti adatokat egyszerre egy sor adja át.

Az egyes értékeket a rendszer a IRow felület Get metódusának meghívásával sorolja fel:

```csharp
row.Get<string>("column_name")
```

Az egyes oszlopnevek az alábbiak meghívásával határozhatók meg `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ez a megközelítés lehetővé teszi, hogy rugalmasan hozzon létre egy metaadat-sémát.

A kimeneti adatokat fájlba kell írni a használatával `System.IO.StreamWriter` . A stream paraméter a (z `output.BaseStream` ) részeként van beállítva `IUnstructuredWriter output` .

Fontos megjegyezni, hogy az adatpuffert az egyes sorok iterációja után ki kell üríteni a fájlba. Emellett az `StreamWriter` objektumot az engedélyezett rendelkezésre álló attribútummal (alapértelmezett) és a **using** kulcsszóval kell használni:

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Ellenkező esetben a flush () metódus explicit módon meghívja az egyes iterációkat. Ezt a következő példában mutatjuk be.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Fejlécek és láblécek beállítása a felhasználó által definiált kiállítók számára
A fejlécek beállításához használjon egy iterációs végrehajtási folyamatot.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Az első `if (isHeaderRow)` blokkban lévő kódot csak egyszer hajtja végre a rendszer.

A lábléchez használja az objektum () példányának hivatkozását `System.IO.Stream` `output.BaseStream` . Írja be a láblécet az interfész Bezárás () metódusában `IOutputter` .  (További információ a következő példában található.)

A következő példa egy felhasználó által definiált leszerkesztőt mutat be:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

És U-SQL alap parancsfájl:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Ez egy HTML-fájl, amely egy táblázatos adattal rendelkező HTML-fájlt hoz létre.

### <a name="call-outputter-from-u-sql-base-script"></a>A U-SQL alap parancsfájlból származó
Ha az alapszintű U-SQL-parancsfájlból szeretne egy egyéni leírókat meghívni, létre kell hozni a kibővített objektum új példányát.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Ha el szeretné kerülni az objektum példányának létrehozását az alapparancsfájlban, létrehozhat egy függvény burkolót, ahogy az a korábbi példában is látható:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Ebben az esetben az eredeti hívás a következőhöz hasonlóan néz ki:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)