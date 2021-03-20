---
title: Felhasználó által definiált U-SQL-operátorok fejlesztése – Azure Data Lake Analytics
description: Megtudhatja, hogyan fejleszthet felhasználó által definiált operátorokat Azure Data Lake Analytics feladatokban felhasználható és újra felhasználni.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219989"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Felhasználó által definiált U-SQL-operátorok fejlesztése (Udo)
Ez a cikk azt ismerteti, hogyan lehet felhasználó által definiált operátorokat feldolgozni egy U-SQL-feladatokban.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Felhasználó által definiált operátor definiálása és használata az U-SQL-ben

### <a name="to-create-and-submit-a-u-sql-job"></a>U-SQL-feladatok létrehozása és elküldése

1. A Visual Studióban válassza a **fájl > új > project > U-SQL-projekt** elemet.
2. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy script. usql fájllal.
3. A **megoldáskezelő** bontsa ki a script. usql csomópontot, majd kattintson duplán a **script. Usql. cs parancsfájlra**.
4. A fájlba illessze be az alábbi kódot:

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. Nyissa meg a **script. usql**, és illessze be a következő U-SQL-parancsfájlt:

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. Adja meg a Data Lake Analytics-fiókot, -adatbázist és -sémát.
7. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre.
8. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre.
9. Ha még nem csatlakozott az Azure-előfizetéséhez, a rendszer kérni fogja az Azure-fiók hitelesítő adatainak megadását.
10. Kattintson a **Submit (Küldés**) gombra. A beküldési eredmények és a feladatok hivatkozása az eredmények ablakban érhető el, amikor a Küldés befejeződött.
11. Kattintson a **frissítés** gombra a feladatok legutóbbi állapotának megtekintéséhez és a képernyő frissítéséhez.

### <a name="to-see-the-output"></a>A kimenet megtekintéséhez

1. A **Server Explorerben** bontsa ki az **Azure** elemet, bontsa ki **Data Lake Analytics**, bontsa ki a Data Lake Analytics fiókot, bontsa ki a **Storage-fiókok** csomópontot, kattintson a jobb gombbal az alapértelmezett tárolóra, **majd kattintson a**

2. Bontsa ki a minták, majd a kimenetek csomópontot, majd kattintson duplán a **Drivers.csv** elemre.

## <a name="next-steps"></a>Következő lépések

* [U-SQL-kifejezések kiterjesztése felhasználói kóddal](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [A Visual studióhoz készült Data Lake Tools használata az U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
