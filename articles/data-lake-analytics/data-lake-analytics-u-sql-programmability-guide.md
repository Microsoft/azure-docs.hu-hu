---
title: A U-SQL programozható útmutatója Azure Data Lake
description: Ismerkedjen meg a U-SQL áttekintésével és az UDF-programozással a Azure Data Lake Analyticsban, hogy lehetővé váljon a jó USQL-szkriptek létrehozása.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96510862"
---
# <a name="u-sql-programmability-guide-overview"></a>A U-SQL programozható útmutatójának áttekintése

Az U-SQL egy olyan lekérdezési nyelv, amelyet a számítási feladatok big dataához terveztek. Az U-SQL egyik egyedi funkciója az SQL-szerű deklaratív nyelv kombinációja a C# által biztosított bővíthetőséggel és programozhatóság használatával. Ebben az útmutatóban a C# által engedélyezett U-SQL nyelv bővíthetőségére és programozhatóra koncentrálunk.

## <a name="requirements"></a>Követelmények

Töltse le és telepítse [a Visual studióhoz készült Azure Data Lake-eszközöket](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>U-SQL – első lépések  

Tekintse meg a következő U-SQL-parancsfájlt:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Ez a parancsfájl két sorhalmazt definiál: `@a` és `@results` . A sorhalmaz a `@results` következőből van definiálva: `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-típusok és-kifejezések az U-SQL-parancsfájlban

A U-SQL kifejezés egy C# kifejezés, amely a U-SQL logikai műveletekkel, például a és a szolgáltatással együtt használható `AND` `OR` `NOT` . A U-SQL kifejezések a SELECT, a EXTRACT, a WHERE, a GROUP BY és a DECLARE kifejezéssel használhatók. Például a következő parancsfájl egy sztringet DateTime értékként elemez.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

A következő kódrészlet egy karakterláncot DateTime értékként elemez a DECLARE utasításban.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C#-kifejezések használata adattípus-konverzióhoz

Az alábbi példa azt mutatja be, hogyan végezheti el a DateTime típusú adatkonverziót C#-kifejezések használatával. Ebben az adott esetben a string datetime-adatok konvertálása standard datetime értékre történik, éjfél 00:00:00 idő jelöléssel.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>A mai dátumhoz C#-kifejezések használata

A mai dátum lekéréséhez a következő C# kifejezést használhatja: `DateTime.Now.ToString("M/d/yyyy")`

Íme egy példa arra, hogyan használhatja ezt a kifejezést egy parancsfájlban:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET-szerelvények használata

Az U-SQL bővíthetőségi modellje nagy mértékben támaszkodik arra, hogy egyéni kódot adjon hozzá a .NET-szerelvényekhez. 

### <a name="register-a-net-assembly"></a>.NET-szerelvény regisztrálása

A `CREATE ASSEMBLY` .net-szerelvényt a következő utasítással helyezheti el egy U-SQL Databaseba. Ezt követően az U-SQL-parancsfájlok ezeket a szerelvényeket használhatják az `REFERENCE ASSEMBLY` utasítás használatával. 

A következő kód bemutatja, hogyan regisztrálhat egy szerelvényt:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

A következő kód bemutatja, hogyan hivatkozhat egy szerelvényre:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

További részletekért tekintse meg a [szerelvény regisztrációs utasításait](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) , amelyek részletesebben ismertetik ezt a témakört.


### <a name="use-assembly-versioning"></a>Szerelvény verziószámozásának használata
Az U-SQL jelenleg a .NET-keretrendszer 4.7.2 verzióját használja. Ügyeljen arra, hogy a saját szerelvényei kompatibilisek legyenek a futtatókörnyezet adott verziójával.

Ahogy korábban említettük, az U-SQL 64 bites (x64) formátumban futtatja a kódot. Ügyeljen arra, hogy a kód fordítása x64-re fusson. Ellenkező esetben helytelen formátumú hibaüzenetet kap.

Minden feltöltött összeállítási DLL-fájl és erőforrásfájl, például egy másik futtatókörnyezet, egy natív szerelvény vagy egy konfigurációs fájl legfeljebb 400 MB lehet. A központilag telepített erőforrások teljes mérete (akár üzembe helyezési erőforrás, akár szerelvények és a további fájlok esetében) nem haladhatja meg a 3 GB-ot.

Végezetül vegye figyelembe, hogy minden U-SQL-adatbázis csak egy adott szerelvény egyetlen verzióját tudja tartalmazni. Ha például a NewtonSoft Json.NET Library 7-es verziójára és 8-as verziójára van szüksége, akkor két különböző adatbázisban kell regisztrálnia. Emellett az egyes parancsfájlok csak egy adott szerelvény-DLL egy verziójára vonatkozhatnak. Ebben a tekintetben az U-SQL a C# szerelvények felügyeletét és verziószámozását követi.

## <a name="use-user-defined-functions-udf"></a>Felhasználó által definiált függvények használata: UDF
A felhasználó által definiált U-SQL-függvények vagy UDF-EK olyan programozási rutinok, amelyek elfogadják a paramétereket, végrehajtanak egy műveletet (például egy összetett számítást), és értékként adják vissza a művelet eredményét. Az UDF visszatérési értéke csak egyetlen skaláris lehet. A u-SQL UDF a U-SQL alapparancsfájlban hívható meg, mint bármely más C# skaláris függvény.

Javasoljuk, hogy a felhasználó által definiált U-SQL-függvényeket **nyilvánosként** és **statikusan** inicializálja.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Először tekintsük át az UDF létrehozásának egyszerű példáját.

Ebben a használati helyzetben meg kell határozni a pénzügyi időszakot, beleértve az adott felhasználóhoz tartozó első bejelentkezés pénzügyi negyedévét és pénzügyi hónapját. Az év első pénzügyi hónapja a forgatókönyvben június.

A pénzügyi időszak kiszámításához a következő C#-függvényt mutatjuk be:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
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

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Egyszerűen kiszámítja a pénzügyi hónapot és a negyedévet, és egy sztring értéket ad vissza. Júniusban az első pénzügyi negyedév első hónapjára a "Q1: P1" kifejezést használjuk. Júliusban a "Q1: P2" kifejezést használjuk, és így tovább.

Ez egy normál C#-függvény, amelyet a U-SQL-projektben fogunk használni.

Itt látható, hogyan néz ki a kód mögötti szakasz ebben a forgatókönyvben:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
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

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Most hívjuk ezt a függvényt az alap U-SQL-szkriptből. Ehhez meg kell adnia a függvény teljes nevét, beleértve a névteret is, amely ebben az esetben a NameSpace. class. Function (paraméter).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

A következő a tényleges U-SQL-alapú parancsfájl:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

A szkript végrehajtásának kimeneti fájlja a következő:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Ez a példa a beágyazott UDF egyszerű használatát mutatja be U-SQL-ben.

### <a name="keep-state-between-udf-invocations"></a>Állapot megőrzése az UDF-hívások között
Az U-SQL C# programozható objektumok kifinomultabbak, és a globális változók mögötti interaktivitást is igénybe vehetik. Nézzük meg a következő üzleti használati esetet.

A nagyméretű szervezeteknél a felhasználók válthatnak a belső alkalmazások különböző fajtái között. Ilyenek lehetnek például a Microsoft Dynamics CRM, a Power BI stb. Előfordulhat, hogy az ügyfelek telemetria-elemzést kívánnak alkalmazni arról, hogy a felhasználók hogyan válthatnak a különböző alkalmazások között, milyen trendek vannak, és így tovább. Az üzleti cél az alkalmazások használatának optimalizálása. Emellett érdemes lehet különböző alkalmazásokat vagy konkrét bejelentkezési rutinokat kombinálni.

Ennek a célnak a megvalósításához meg kell határozni a munkamenet-azonosítókat és a késési időt az utolsó munkamenet során.

Meg kell találni egy korábbi bejelentkezést, majd hozzá kell rendelnie ezt a bejelentkezést az ugyanahhoz az alkalmazáshoz létrehozott összes munkamenethez. Az első kihívás az, hogy az U-SQL alap parancsfájl nem teszi lehetővé a számítások alkalmazását a már számított oszlopokon a LAG függvénnyel. A második kihívás az, hogy meg kell őrizni az adott munkamenetet az adott időszakon belüli összes munkamenethez.

A probléma megoldásához egy globális változót használunk a kód mögötti szakaszban: `static public string globalSession;` .

Ezt a globális változót a szkript végrehajtása során a teljes sorhalmazra alkalmazza a rendszer.

Itt látható a U-SQL program kódjának mögötti szakasza:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Ez a példa a függvényen belül használt globális változót mutatja be `static public string globalSession;` `getStampUserSession` , és minden alkalommal újrainicializálja a munkamenet-paramétert.

A U-SQL alap parancsfájl a következő:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

A függvényt `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` a rendszer a második memória sorhalmazának kiszámítása során hívja meg. Átadja az `UserSessionTimestamp` oszlopot, és a változásig visszaadja az értéket `UserSessionTimestamp` .

A kimeneti fájl a következő:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Ez a példa egy bonyolultabb használati esetet mutat be, amelyben egy globális változót használunk egy kód mögötti szakaszban, amely a teljes memória sorhalmazra van alkalmazva.

## <a name="next-steps"></a>Következő lépések
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL programozható útmutató – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)