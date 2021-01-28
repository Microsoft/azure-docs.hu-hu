---
title: A .NET és a Hadoop MapReduce használata Linux-alapú HDInsight – Azure
description: Ismerje meg, hogyan használhatók a .NET-alkalmazások a streaming MapReduce Linux-alapú HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 02/27/2018
ms.openlocfilehash: 1ceb5d323ecfed54b4e01d13d66c640769a224ee
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945592"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>.NET-megoldások migrálása Windows-alapú HDInsight Linux-alapú HDInsight

A Linux-alapú HDInsight-fürtök a MONO-t használják [( https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. A Mono lehetővé teszi a .NET-összetevők, például a MapReduce-alkalmazások Linux-alapú HDInsight való használatát. Ebből a dokumentumból megtudhatja, hogyan telepíthet át Windows-alapú HDInsight-fürtökhöz létrehozott .NET-megoldásokat a Mono használatával a Linux-alapú HDInsight.

## <a name="mono-compatibility-with-net"></a>Mono-kompatibilitás a .NET-tel

A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját. További információ a HDInsight által tartalmazott mono-verzióról: HDInsight- [összetevő verziója](hdinsight-component-versioning.md).

A mono és a .NET közötti kompatibilitásról további információt a [mono kompatibilitási ( https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentum) című témakörben talál.

> [!IMPORTANT]  
> A SCP.NET keretrendszer kompatibilis a Mono-val. A SCP.NET és a Mono használatával kapcsolatos további információkért lásd: [a Visual Studio használata C#-topológiák fejlesztéséhez a HDInsight Apache Storm](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatizált hordozhatóság elemzése

A [.net-hordozhatósági elemző](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) segítségével az alkalmazás és a Mono közötti inkompatibilitási jelentések készíthetők. A következő lépésekkel konfigurálhatja az elemzőt az alkalmazás monó-hordozhatóságra való vizsgálatához:

1. Telepítse a [.net-hordozhatósági elemzőt](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). A telepítés során válassza ki a használni kívánt Visual Studio-verziót.

2. A Visual Studio 2015-ból válassza  >  a __hordozhatósági elemző beállításainak__ elemzése lehetőséget, és győződjön meg arról, hogy a __4,5__ be van jelölve a __mono__ szakaszban.

    ![4,5 az analizátor beállításainál a Mono szakaszban be van jelölve](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    A konfiguráció mentéséhez kattintson __az OK gombra__ .

3. Válassza az elemzés a  >  __szerelvények hordozhatóságának__ elemzése lehetőséget. Válassza ki a megoldást tartalmazó szerelvényt, majd válassza a __Megnyitás__ lehetőséget az elemzés megkezdéséhez.

4. Az elemzések befejezése után válassza az  >  __elemzés megtekintése jelentések__ elemzése lehetőséget. A __hordozhatósági elemzés eredményei__ között válassza a __jelentés megnyitása__ lehetőséget a jelentés megnyitásához.

    ![A hordozhatósági elemző eredményei párbeszédpanel](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Az analizátor nem tud minden problémát megfogni a megoldásával. A fájl elérési útja például az `c:\temp\file.txt` OK, ha a Mono a Windows rendszeren fut. Ugyanez az elérési út nem érvényes Linux platformon.

## <a name="manual-portability-analysis"></a>Manuális hordozhatóság elemzése

Végezze el a kód manuális vizsgálatát az [alkalmazás-hordozhatóságban https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) található információk használatával (dokumentum.

## <a name="modify-and-build"></a>Módosítás és létrehozás

Továbbra is használhatja a Visual studiót a .NET-megoldások HDInsight való létrehozásához. Gondoskodnia kell azonban arról, hogy a projekt a .NET-keretrendszer 4,5-es használatára legyen konfigurálva.

## <a name="deploy-and-test"></a>Üzembe helyezés és tesztelés

Miután módosította a megoldást a .NET-hordozhatósági elemző ajánlásaival vagy manuális elemzéssel, tesztelni kell a HDInsight használatával. A megoldás Linux-alapú HDInsight-fürtön való tesztelése a kijavítani kívánt finom problémákat jelezheti. Javasoljuk, hogy a tesztelés során engedélyezze a további naplózást az alkalmazásban.

A naplók elérésével kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz a Linux-alapú HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Következő lépések

* [A C# és a MapReduce használata a HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Felhasználó által definiált C#-függvények használata Apache Hive és Apache Pig használatával](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [C#-topológiák fejlesztése a HDInsight Apache Storméhez](storm/apache-storm-develop-csharp-visual-studio-topology.md)
