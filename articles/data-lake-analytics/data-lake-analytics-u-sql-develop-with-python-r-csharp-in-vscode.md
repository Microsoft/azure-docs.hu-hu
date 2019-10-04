---
title: U-SQL-feladatok futtatása a Pythonban, az C# R-ben és a-Azure Data Lake Analytics
description: Megtudhatja, hogyan használhatja a Python, az R C# és a feladatainak elküldéséhez szükséges kódot a Azure Data Lakeban.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309707"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>A U-SQL fejlesztése a Python, az R C# és a Azure Data Lake Analytics a Visual Studio Code-ban
Ismerje meg, hogy a Visual Studio Code (VSCode) használatával hogyan írhat Pythont C# , R-t és kódot a U-SQL segítségével, és hogyan küldhet feladatokat Azure Data Lake szolgáltatásnak. A VSCode Azure Data Lake eszközeivel kapcsolatos további információkért lásd: [a Azure Data Lake Tools for Visual Studio Code használata](data-lake-analytics-data-lake-tools-for-vscode.md).

A kód az egyéni kód mögé írása előtt meg kell nyitnia egy mappát vagy egy munkaterületet a VSCode.


## <a name="prerequisites-for-python-and-r"></a>A Python és az R előfeltételei
Regisztráljon a Python és az R Extensions szerelvényeket az ADL-fiókjához. 
1. Nyissa meg a fiókját a portálon.
   - Válassza az **Áttekintés** lehetőséget. 
   - Kattintson a **minta parancsfájl**elemre.
2. Kattintsona továbbiak gombra.
3. Válassza az **U-SQL-bővítmények telepítése**lehetőséget. 
4. Az U-SQL-bővítmények telepítése után megerősítő üzenet jelenik meg. 

   ![A környezet beállítása a Pythonhoz és az R-hez](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > A Python és az R Language Service szolgáltatással kapcsolatos legjobb tapasztalatokért telepítse a VSCode Python és az R bővítményt. 

## <a name="develop-python-file"></a>Python-fájl fejlesztése
1. Kattintson az **új fájlra** a munkaterületen.
2. Írja be a kódot a U-SQL-ben. A következő egy mintakód.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Kattintson a jobb gombbal egy parancsfájlra, majd válassza **az ADL: Python-kód készítése fájl**mögött. 
4. A rendszer létrehozza a **xxx.usql.py** fájlt a munkamappában. Írja be a kódot a Python-fájlba. A következő egy mintakód.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Kattintson a jobb gombbal a **USQL** fájlra, kattintson a **parancsfájl fordítása** vagy a **feladatok** futtatása a feladatokhoz lehetőségre.

## <a name="develop-r-file"></a>R-fájl fejlesztése
1. Kattintson az **új fájlra** a munkaterületen.
2. Írja be a kódot az U-SQL-fájlba. A következő egy mintakód.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Kattintson a jobb gombbal a **USQL** fájl elemre, **majd válassza az ADL elemet: Fájl**mögötti R-kód előállítása. 
4. A rendszer a munkahelyi mappában hozza létre a **xxx. usql. r** fájlt. Írja be a kódot az R-fájlba. A következő egy mintakód.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kattintson a jobb gombbal a **USQL** fájlra, kattintson a **parancsfájl fordítása** vagy a **feladatok** futtatása a feladatokhoz lehetőségre.

## <a name="develop-c-file"></a>Fájl C# fejlesztése
A kód mögötti fájl egy egyetlen C# U-SQL-parancsfájllal társított fájl. A kód mögött található UDO, UDA, UDT és UDF dedikált parancsfájlt is meghatározhat. A UDO, a UDA, a UDT és az UDF közvetlenül a szkriptben használható, anélkül, hogy először regisztrálja a szerelvényt. A kód mögötti fájl ugyanabba a mappába kerül, mint a társ U-SQL-parancsfájl. Ha a parancsfájl neve xxx. usql, a kód mögött a xxx.usql.cs lesz elnevezve. Ha manuálisan törli a kód mögötti fájlt, a kód mögötti funkció le van tiltva a társított U-SQL-parancsfájlhoz. További információ az u-SQL-szkriptek [vásárlói kódjának írásáról: egyéni kód írása és használata az u-SQL-ben: Felhasználó által definiált]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)függvények.

1. Kattintson az **új fájlra** a munkaterületen.
2. Írja be a kódot az U-SQL-fájlba. A következő egy mintakód.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Kattintson a jobb gombbal a **USQL** fájl elemre, **majd válassza az ADL elemet: Fájl**mögötti CS-kód előállítása. 
4. A rendszer létrehozza a **xxx.usql.cs** fájlt a munkamappában. Írja be a kódot a CS-fájlba. A következő egy mintakód.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Kattintson a jobb gombbal a **USQL** fájlra, kattintson a **parancsfájl fordítása** vagy a **feladatok** futtatása a feladatokhoz lehetőségre.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL helyi Futtatás és helyi hibakeresés a Visual Studio Code-ban](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Ismerkedés a Data Lake Analytics a PowerShell használatával](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a Azure Portal](data-lake-analytics-get-started-portal.md)
* [A Visual studióhoz készült Data Lake Tools használata az U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics (U-SQL) katalógus használata](data-lake-analytics-use-u-sql-catalog.md)
