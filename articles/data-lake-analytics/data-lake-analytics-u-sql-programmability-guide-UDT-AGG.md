---
title: A U-SQL UDT és a UDAGG programozható útmutatója Azure Data Lake
description: Ismerkedjen meg a U-SQL-UDT és a UDAGG programozható Azure Data Lake Analytics a jó USQL-parancsfájlok létrehozásához.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606657"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL programozható útmutató – UDT és UDAGG



## <a name="use-user-defined-types-udt"></a>Felhasználó által definiált típusok használata: UDT
A felhasználó által definiált típusok vagy UDT a U-SQL egy másik programozható szolgáltatása. Az U-SQL-UDT egy normál C# felhasználó által definiált típushoz hasonlóan működik. A C# egy határozottan gépelt nyelv, amely lehetővé teszi a beépített és egyéni felhasználók által definiált típusok használatát.

Az U-SQL nem tud implicit módon szerializálni vagy deszerializálni tetszőleges UDT, amikor a UDT a csúcspontok között átadja a sorhalmazban. Ez azt jelenti, hogy a felhasználónak explicit formázó eszközt kell megadnia a IFormatter felület használatával. Ez biztosítja a U-SQL-t a UDT szerializálási és deszerializáló módszereivel.

> [!NOTE]
> A U-SQL beépített kivonói és-előállítók jelenleg nem tudják szerializálni vagy deszerializálni a UDT-adatait, még a IFormatter is. Tehát amikor UDT ír egy fájlba a kimeneti utasítással, vagy beolvassa azt egy kivonóval, akkor karakterlánc-vagy byte-tömbként kell átadnia. Ezután meghívja a szerializálási és a deszerializálási kódot (azaz a UDT ToString () metódusát) explicit módon. A felhasználó által definiált kiállítók és a lekérések a UDT olvashatók és írhatók.

Ha a UDT-t a kivonón vagy a kimeneten (az előző SELECT-ből) próbáljuk használni, az itt látható módon:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

A következő hibaüzenetet kapja:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

A UDT-ben való együttműködéshez a ToString () metódussal kell szerializálni a karakterláncot, vagy létre kell hoznia egy egyéni leválasztó eszközt.

A UDT jelenleg nem használható a GROUP BY utasításban. Ha a UDT a GROUP BY utasításban használja, a következő hiba történik:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

A UDT definiálásához a következőket kell tennie:

1. Adja hozzá a következő névtereket:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Adja hozzá `Microsoft.Analytics.Interfaces` , amely a UDT-interfészekhez szükséges. Emellett szükség lehet `System.IO` az IFormatter felület definiálására is.

3. Definiáljon egy használt típust a SqlUserDefinedType attribútummal.

A **SqlUserDefinedType** az U-SQL-ben felhasználó által definiált típusként (UDT) jelöli meg a szerelvény típus definícióját. Az attribútum tulajdonságai a UDT fizikai jellemzőit tükrözik. Ez az osztály nem örökölhető.

A SqlUserDefinedType a UDT-definíció kötelező attribútuma.

Az osztály konstruktora:  

* SqlUserDefinedTypeAttribute (típus formázó)

* Type formázó: kötelező paraméter egy UDT formázó definiálásához – konkrétan a csatoló típusát `IFormatter` itt kell átadni.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* A tipikus UDT az IFormatter felület definícióját is igényli, ahogy az az alábbi példában is látható:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Az `IFormatter` illesztőfelület szerializálja és deszerializálja az Object gráfot a gyökérszintű típussal \<typeparamref name="T"> .

\<typeparam name="T">Az objektum gráfjának legfelső szintű típusa szerializáláshoz és deszerializáláshoz.

* **Deszerializálás**: megszünteti a megadott adatfolyamban lévő adatmennyiséget, és visszaképezi az objektumok gráfját.

* **Szerializálás**: egy objektum vagy objektum gráfjának szerializálására szolgál a megadott adatfolyammal.

`MyType` példány: a típus példánya.  
`IColumnWriter` író/ `IColumnReader` olvasó: az alapul szolgáló oszlop adatfolyama.  
`ISerializationContext` Context: Enum, amely a szerializálás során megadja a forrás vagy a cél környezetét az adatfolyamhoz.

* **Köztes**: azt határozza meg, hogy a forrás vagy a cél környezet nem egy megőrzött tároló.

* **Megőrzés**: Megadja, hogy a forrás vagy a cél környezet megőrzött tároló-e.

Normál C#-típusként a U-SQL-UDT definíciója olyan operátorok felülbírálását is magában foglalhatja, mint a +/= =/! =. Statikus metódusokat is tartalmazhat. Ha például ezt a UDT paraméterként egy U-SQL MIN összesítő függvényhez fogjuk használni, meg kell határozni < operátor felülbírálását.

Az útmutató korábbi részében egy példát mutatunk be a pénzügyi időszak azonosítására a formátumban megadott dátum alapján `Qn:Pn (Q1:P10)` . Az alábbi példa bemutatja, hogyan határozhat meg egyéni típust a pénzügyi időszakok értékeihez.

Az alábbi példa egy, az egyéni UDT és a IFormatter felülettel ellátott kódrészletet tartalmaz:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

A megadott típus két számot tartalmaz: a negyedévet és a hónapot. Az operátorok `==/!=/>/<` és a statikus metódusok `ToString()` itt vannak meghatározva.

Ahogy azt korábban említettük, a UDT használhatók a SELECT kifejezésekben, de nem használhatók a kiállítók/kivonók egyéni szerializálás nélkül. Ezt a karakterláncot `ToString()` vagy egy egyéni, illetve KIvonót használó sztringként kell szerializálni.

Most beszéljünk a UDT használatáról. A kód mögötti szakaszban a GetFiscalPeriod függvényt a következőre módosították:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Amint láthatja, a FiscalPeriod típusának értékét adja vissza.

Íme egy példa arra, hogyan használhatja tovább a U-SQL alap parancsfájlban. Ez a példa az U-SQL-szkriptből származó UDT hívás különböző formáit mutatja be.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
        start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        fiscalquarter,
        fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Íme egy példa a teljes kód mögötti szakaszra:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Felhasználó által definiált összesítések használata: UDAGG
A felhasználó által definiált összesítések bármely olyan összesítéssel kapcsolatos függvény, amely nem a U-SQL-mel van kiszállítva. A példa az egyéni matematikai számítások, a karakterlánc-összefűzések, a karakterláncokkal végzett manipulációk és így tovább.

A felhasználó által definiált aggregált alaposztály definíciója a következő:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

A **SqlUserDefinedAggregate** azt jelzi, hogy a típust felhasználó által definiált összesítésként kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedType attribútum nem **kötelező** a UDAGG-definícióhoz.


Az alaposztály lehetővé teszi három absztrakt paraméter átadását: két bemeneti paraméterként, az egyiket pedig eredményként. Az adattípusok változó, és az osztályok öröklése során kell meghatározni.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* Az **init** a számítás során egyszer meghívja az egyes csoportokat. Inicializálási rutint biztosít az egyes összesítési csoportokhoz.  
* A **felhalmozódás** minden értéknél egyszer lesz végrehajtva. Ez biztosítja az összesítési algoritmus fő funkcióit. A segítségével az osztályok öröklése során definiált különböző adattípusokkal összesítheti az értékeket. Elfogadhatja a változó adattípusok két paraméterét is.
* A **megszakítás** a feldolgozás végén egy összesítési csoporton keresztül történik, hogy kiadja az eredményeket az egyes csoportokhoz.

A helyes bemeneti és kimeneti adattípusok bejelentéséhez használja az osztály definícióját az alábbiak szerint:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: az első felhalmozó paraméter
* T2: a halmozódás második paramétere
* TResult: a megszakítás visszatérési típusa

Például:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

vagy

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>A UDAGG használata a U-SQL-ben
A UDAGG használatához először kódot kell megadnia, vagy hivatkoznia kell rá a létező programozható DLL-ből a korábban tárgyalt módon.

Ezután használja a következő szintaxist:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Íme egy példa a UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

És alap U-SQL-parancsfájl:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Ebben a használati helyzetben az adott felhasználók osztály GUID azonosítóit fűzi össze.

## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
