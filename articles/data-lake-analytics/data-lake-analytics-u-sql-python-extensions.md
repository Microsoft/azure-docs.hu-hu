---
title: U-SQL-parancsfájlok kiterjesztése a Pythonsal Azure Data Lake Analytics
description: Megtudhatja, hogyan futtathat Python-kódokat a U-SQL-szkriptekben Azure Data Lake Analytics használatával
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.custom: devx-track-python
ms.openlocfilehash: d81e529d7073e8200c5a1d1fa8f51504b1dc9259
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218442"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>U-SQL-parancsfájlok kiterjesztése Python-kóddal Azure Data Lake Analytics

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a Python-bővítmények telepítve vannak a Azure Data Lake Analytics-fiókjában.

* Navigáljon Data Lake Analytics fiókhoz a Azure Portal
* A bal oldali menüben az **első lépések** területen kattintson a **minta parancsfájlok** elemre.
* Kattintson az **U-SQL-bővítmények telepítése** , majd **az OK gombra** .

## <a name="overview"></a>Áttekintés

A U-SQL Python-bővítményei lehetővé teszik a fejlesztők számára a Python-kód nagymértékben párhuzamos végrehajtását. Az alábbi példa az alapszintű lépéseket szemlélteti:

* A `REFERENCE ASSEMBLY` következő utasítás használatával engedélyezheti a Python-bővítményeket a U-SQL-parancsfájlhoz
* A `REDUCE` bemeneti adatok particionálásának használata a kulcsban
* A U-SQL Python-bővítményei közé tartozik egy beépített szűkítő ( `Extension.Python.Reducer` ), amely Python-kódot futtat a szűkítőhöz rendelt minden egyes csúcsponton.
* A U-SQL-szkript tartalmazza a beágyazott Python-kódot, amely egy nevű függvényt fogad el, `usqlml_main` amely a Panda DataFrame bemenetként fogadja el, és a Panda DataFrame adja vissza kimenetként.

```usql
REFERENCE ASSEMBLY [ExtPython];
DECLARE @myScript = @"
def get_mentions(tweet):
    return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )
def usqlml_main(df):
    del df['time']
    del df['author']
    df['mentions'] = df.tweet.apply(get_mentions)
    del df['tweet']
    return df
";
@t  =
    SELECT * FROM
       (VALUES
           ("D1","T1","A1","@foo Hello World @bar"),
           ("D2","T2","A2","@baz Hello World @beer")
       ) AS date, time, author, tweet );
@m  =
    REDUCE @t ON date
    PRODUCE date string, mentions string
    USING new Extension.Python.Reducer(pyScript:@myScript);
OUTPUT @m
    TO "/tweetmentions.csv"
    USING Outputters.Csv();
```

## <a name="how-python-integrates-with-u-sql"></a>Hogyan integrálódik a Python a U-SQL-vel

### <a name="datatypes"></a>Adattípusok

* A U-SQL által konvertált karakterlánc-és numerikus oszlopok a Panda és az U-SQL között lesznek átalakítva.
* A U-SQL nullák a pandák `NA` értékeire konvertálva

### <a name="schemas"></a>Sémák

* A pandák-vektorok indexelése nem támogatott az U-SQL-ben. A Python függvényben lévő összes bemeneti adatkeretnek a 0 értékről a 0 és 1 közötti számú numerikus indexszel kell 64 rendelkeznie.
* Az U-SQL-adatkészletek nem rendelkezhetnek duplikált oszlopnevek
* A U-SQL-adatkészletek oszlopai nem sztringek.

### <a name="python-versions"></a>Python-verziók

Csak a Windows rendszerhez készült Python 3.5.1 (fordítás) támogatott.

### <a name="standard-python-modules"></a>Standard Python-modulok

A standard Python-modulok is elérhetők.

### <a name="additional-python-modules"></a>További Python-modulok

A standard Python-kódtárak mellett számos gyakran használt Python-kódtárat is tartalmaz:

* pandas
* numpy
* numexpr

### <a name="exception-messages"></a>Kivételek üzenetei

Jelenleg a Python-kódban szereplő kivétel általános csúcspont-hibaként jelenik meg. A jövőben a U-SQL-feladatok hibaüzenetei megjelennek a Python-kivétel üzenetében.

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretre vonatkozó korlátozások

Minden csúcspont korlátozott mennyiségű memóriát rendel hozzá. Jelenleg ez a korlát 6 GB az AU-hoz. Mivel a bemeneti és a kimeneti DataFrames léteznie kell a Python-kódban lévő memóriában, a bemenet és a kimenet teljes mérete nem haladhatja meg a 6 GB-ot.

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [A U-SQL Window functions használata Azure Data Lake Analytics feladatokhoz](./data-lake-analytics-u-sql-get-started.md)
* [A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata](data-lake-analytics-data-lake-tools-for-vscode.md)