---
title: A .NET használata Apache Spark
description: Ismerje meg a .NET és Apache Spark a kötegelt feldolgozást, a valós idejű streamelést, a gépi tanulást és az alkalmi lekérdezések írását Azure Synapse Analytics jegyzetfüzetekben.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 8d045c1ec96bb7b31a710a28e30e3d428922b65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378550"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Az Apache Sparkhoz készült .NET használata az Azure Stream Analyticsszel

[A .NET for Apache Spark](https://dot.net/spark) ingyenes, [nyílt](https://github.com/dotnet/spark)forráskódú és platformfüggetlen .NET-támogatást nyújt a Sparkhoz. 

.NET-kötéseket biztosít a Sparkhoz, amely lehetővé teszi a Spark API-k C# és F# használatával való hozzáférését. A .NET for Apache Spark használatával .NET-en írt, felhasználó által definiált Spark-függvényeket is írhat és futtathat. A .NET API-k a Sparkhoz lehetővé teszik az adatok elemzésében segítő Spark DataFrame-ek összes aspektusát, beleértve a Spark SQL-t, a Delta Lake-et és a strukturált streamelést.

A .NET segítségével elemezheti az adatokat, Apache Spark Spark batch-feladatdefiníciók vagy interaktív Azure Synapse Analytics jegyzetfüzetek segítségével. Ebből a cikkből megtudhatja, hogyan használhatja a .NET-et Apache Spark és Azure Synapse mindkét módszerrel.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Kötegelt feladatok elküldése a Spark-feladat definíciójának használatával

Az oktatóanyagból megtudhatja, hogyan használhatja a Azure Synapse Analytics synapse Spark Apache Spark készletek feladatdefinícióinak [létrehozásához.](apache-spark-job-definitions.md) Ha még nem csomagolta be az alkalmazást a Azure Synapse, kövesse az alábbi lépéseket.

1. Futtassa az alábbi parancsokat az alkalmazás közzétételéhez. Ne feledjük a *mySparkApp helyére* az alkalmazás elérési útját.
   
   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.18.04-x64
   ```

2. Tömörítse a közzétételi mappa tartalmát, amely például az 1. lépés eredményeként `publish.zip` jött létre. Minden szerelvénynek a ZIP-fájl első rétegében kell lennie, és nem kell köztes mapparétegnek lennie. Ez azt jelenti, hogy a kicsomagolásakor a rendszer minden szerelvényt kinyer `publish.zip` az aktuális munkakönyvtárba.

    **Windows rendszeren:**

    Egy kinyerési program, például [a 7-Zip](https://www.7-zip.org/) vagy a [WinZip](https://www.winzip.com/)használatával bontsa ki a fájlt a bin könyvtárba az összes közzétett bináris fájllal együtt.

    **Linux rendszeren:**

    Nyisson meg egy Bash-rendszerhéjat, és a cd-t a bin könyvtárba az összes közzétett bináris fájl használatával, és futtassa a következő parancsot.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET for Apache Spark a Azure Synapse Analytics notebookban 

A jegyzetfüzetek remekül használhatók a .NET prototípusának Apache Spark folyamatokhoz és forgatókönyvekhez. Gyorsan és hatékonyan dolgozhat az adatokkal, valamint megértheti, szűrheti, megjelenítheti és vizualizálhatja őket. 

Az adatmérnökök, adattudósok, üzleti elemzők és gépi tanulási mérnökök mind képesek együttműködni egy megosztott, interaktív dokumentumon. Az adatfeltárás azonnali eredményeit láthatja, és ugyanabban a jegyzetfüzetben vizualizálhatja az adatokat.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>A .NET használata Apache Spark notebookok számára

Új jegyzetfüzet létrehozásakor ki kell választania egy olyan nyelvi kernelt, amely ki szeretné fejezni az üzleti logikát. A kerneltámogatás több nyelvhez is elérhető, beleértve a C#-t is.

Ha a .NET-et Apache Spark a Azure Synapse Analytics notebookban, válassza a **.NET Spark (C#)** lehetőséget kernelként, és csatolja a notebookot egy meglévő, kiszolgáló nélküli Apache Spark készlethez.

A .NET Spark-jegyzetfüzet a [.NET](https://github.com/dotnet/interactive) interaktív felületére épül, és interaktív C#-élményt biztosít a .NET használatának lehetőségével a Sparkhoz előre meghatározott Spark-munkamenetváltozóval. `spark`

### <a name="install-nuget-packages-in-notebooks"></a>NuGet-csomagok telepítése jegyzetfüzetekbe

A kiválasztott NuGet-csomagokat a magic paranccsal telepítheti a notebookba a `#r nuget` NuGet-csomag neve előtt. Az alábbi ábrán egy példa látható:

![Képernyőkép a Spark .NET#r notebook NuGet-csomag telepítéséhez való használatával](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

A NuGet-csomagok jegyzetfüzetben való alkalmazásával kapcsolatos további információkért tekintse meg a [.NET interaktív dokumentációját.](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md)

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET a C#Apache Spark kernel funkcióihoz

A következő szolgáltatások érhetők el, ha a .NET-et használja Apache Spark a Azure Synapse Analytics jegyzetfüzetben:

* Deklaratív HTML: Kimenetet hozhat létre a cellákból HTML-szintaxissal, például fejlécekkel, listajeles listákkal vagy akár képek megjelenítésével.
* Egyszerű C#-utasítások (például hozzárendelések, konzolon való nyomtatás, kivételeket dobó stb.).
* Többsoros C#-kódblokkok (például if utasítások, foreach hurkok, osztálydefiníciók stb.).
* Hozzáférés a szabványos C#-kódtárhoz (például System, LINQ, Enumerables stb.).
* A C# 8.0 nyelvi funkcióinak támogatása.
* `spark` előre definiált változóként, amely hozzáférést biztosít a Apache Spark munkamenethez.
* A .NET-en belül futtatható, felhasználó által definiált [.NET-függvények Apache Spark.](/dotnet/spark/how-to-guides/udf-guide) Interaktív [környezetekhez](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) ajánlott az írási és hívási felhasználói Apache Spark .NET-ben, hogy megtanulja, hogyan használhatók a .NET felhasználói Apache Spark az interaktív élményekhez.
* A Spark-feladatok kimenetének különböző diagramok (például vonal, sáv vagy hisztogram) és elrendezések (például egy- vagy átfedő adatok stb.) használatával való megjelenítésének támogatása a kódtár `XPlot.Plotly` használatával.
* Lehetőség NuGet-csomagoknak a C#-jegyzetfüzetbe való bevezetésére.

## <a name="next-steps"></a>Következő lépések

* [A .NET for Apache Spark dokumentációja](/dotnet/spark/)
* [.NET a Apache Spark interaktív útmutatókhoz](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET Interaktív](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
