---
title: Windows rendszerű számítógép használata a Hadoop a HDInsight-ben – Azure
description: Munka Windows rendszerű SZÁMÍTÓGÉPRŐL a Hadoop a HDInsight-ben. Fürtök kezelése és lekérdezése a PowerShell-lel, a Visual Studióval és a Linux-eszközökkel. Big data-megoldások fejlesztése a .NET-tel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933930"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Munka Apache Hadoop ökoszisztémán a HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL

Ismerkedjen meg a Windows rendszerű számítógép fejlesztési és felügyeleti lehetőségeivel a HDInsight-beli Apache Hadoop-ökoszisztémán való munkához.

A HDInsight Apache Hadoop-és Hadoop-összetevőkön alapul, a Linuxon kifejlesztett nyílt forráskódú technológiákkal. A HDInsight 3,4-es és újabb verziója az Ubuntu Linux eloszlást használja a fürt alapjául szolgáló operációs rendszerként. A HDInsight-t azonban Windows-ügyfélről vagy Windows-alapú fejlesztési környezetből is használhatja.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>A PowerShell használata üzembe helyezési és felügyeleti feladatokhoz

Azure PowerShell egy parancsfájlkezelési környezet, amellyel a Windows rendszerből a HDInsight vezérelheti és automatizálhatja a központi telepítési és felügyeleti feladatokat.

Példák a PowerShell-lel elvégezhető feladatokra:

* [Fürtök létrehozása a PowerShell használatával](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Apache Hive lekérdezések futtatása a PowerShell használatával](hadoop/apache-hadoop-use-hive-powershell.md).
* [Fürtök kezelése a PowerShell](hdinsight-administer-use-powershell.md)-lel.

Kövesse az [Azure PowerShell telepítésének és konfigurálásának](https://docs.microsoft.com/powershell/azure/install-az-ps) lépéseit a legújabb verzió beszerzéséhez.

## <a name="utilities-you-can-run-in-a-browser"></a>Böngészőben futtatható segédprogramok

A következő segédprogramok egy böngészőben futtatott webes KEZELŐFELÜLETtel rendelkeznek:
* A **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** egy interaktív, parancssori rendszerhéj, amely a böngészőben és a Azure Portal belül fut.

* Az **[Apache Ambari webes felhasználói felülete](hdinsight-hadoop-manage-ambari.md)** a Azure Portalben elérhető felügyeleti és figyelési segédprogram, amely különböző típusú feladatok felügyeletére használható, például:
    * [Az Apache Ambari használata a REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive nézet az Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache TEZ-nézet az Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>A Visual studióhoz készült Data Lake-(Hadoop-) eszközök

A Storm-topológiák üzembe helyezéséhez és kezeléséhez használja a Data Lake Tools for Visual Studio eszközt. A Data Lake Tools is telepíti a SCP.NET SDK-t, amely lehetővé C# teszi a Storm-topológiák fejlesztését a Visual Studióval.

Mielőtt elkezdené az alábbi példákat, [telepítse és próbálja meg Data Lake Tools for Visual Studio alkalmazást](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Példák a Visual Studióval és a Visual Studióval Data Lake eszközökkel elvégezhető feladatokra:
* [Storm-topológiák üzembe helyezése és kezelése a Visual studióból](storm/apache-storm-deploy-monitor-topology-linux.md)
* [A C# Visual Studióval a Storm-topológiákat fejlesztheti](storm/apache-storm-develop-csharp-visual-studio-topology.md). A BITS példákat tartalmaz a Storm-topológiák számára, amelyek adatbázisokhoz, például Azure Cosmos DBhoz és SQL Databasehoz csatlakozhatnak.

## <a name="visual-studio-and-the-net-sdk"></a>A Visual Studio és a .NET SDK

A Visual Studio és a .NET SDK segítségével kezelheti a fürtöket, és big data alkalmazásokat fejleszthet. A következő feladatokhoz más ide-ket is használhat, de a Visual Studióban példák láthatók.

Példák a Visual Studióban a .NET SDK-val elvégezhető feladatokra:
* [Azure HDINSIGHT SDK a .net-hez](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Apache Hive lekérdezések futtatása a .net SDK használatával](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [A C# felhasználó által definiált függvények használata a Apache Hive és az Apache Pig streaming használatával Apache Hadoopon](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IntelliJ IDEA és Eclipse IDE a Spark-fürtökhöz

A [INTELLIJ Idea](https://www.jetbrains.com/idea/download) és az [Eclipse ide](https://www.eclipse.org/downloads/) is használható:
* Scala Spark-alkalmazás fejlesztése és beküldése egy HDInsight Spark-fürtön.
* A Spark-fürt erőforrásainak elérése.
* A Scala Spark alkalmazást helyileg fejlesztheti és futtathatja.

Ezek a cikkek a következőket mutatják be:
* IntelliJ IDEA: [Apache Spark-alkalmazások létrehozása az Azure Toolkit for IntelliJ beépülő modullal és a Scala SDK-val.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE vagy Scala IDE az Eclipse-hez: [hozzon létre Apache Spark alkalmazásokat és a Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notebookok a Sparkban adatszakértők számára

A HDInsight Apache Spark-fürtök közé tartoznak az Apache Zeppelin jegyzetfüzetek és a Jupyter-jegyzetfüzetekkel használható kernelek is.

* [Ismerje meg, hogyan használhat kerneleket Apache Spark-fürtökön a Jupyter notebookokkal a Spark-alkalmazások teszteléséhez](spark/apache-spark-zeppelin-notebook.md)
* [Ismerje meg, hogyan használhatók a Spark-feladatok az Apache Zeppelin-jegyzetfüzetekkel Apache Spark-fürtökön](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Linux-alapú eszközök és technológiák futtatása Windows rendszeren

Ha olyan helyzetbe kerül, ahol olyan eszközt vagy technológiát kell használnia, amely csak Linux rendszeren érhető el, vegye figyelembe a következő lehetőségeket:

* A **bash on Ubuntu on Windows 10** Linux alrendszert biztosít a Windows rendszeren. A bash lehetővé teszi a Linux-segédprogramok közvetlen futtatását anélkül, hogy egy dedikált Linux-telepítést kellene fenntartania. A telepítési lépésekért lásd: [Windows-alrendszer Linux-telepítési útmutató Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) rendszerhez.  Más [UNIX-rendszerhéj](https://www.gnu.org/software/bash/) is működik.
* A **Windows Docker** számos Linux-alapú eszközhöz biztosít hozzáférést, és közvetlenül a Windows rendszerből is futtatható. A Docker használatával például közvetlenül a Windowsból futtathatja a Beeline-ügyfelet a Kaptárhoz. Azt is megteheti, hogy a Docker használatával futtat egy helyi Jupyter notebookot, és távolról csatlakozik a Spark on HDInsight. [Ismerkedés a Windowshoz készült Docker szolgáltatással](https://docs.docker.com/docker-for-windows/)
* A **[MobaXTerm](https://mobaxterm.mobatek.net/)** lehetővé teszi, hogy grafikusan tallózással keresse meg a fürt FÁJLRENDSZERét SSH-kapcsolaton keresztül.

## <a name="cross-platform-tools"></a>Platformfüggetlen eszközök

Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.  További információ: [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Következő lépések

Ha most ismerkedik a Linux-alapú fürtökkel, tekintse meg a következő cikkeket:
* [Apache Hadoop, Apache Kafka, Apache Spark vagy más fürtök beállítása](hdinsight-hadoop-provision-linux-clusters.md)
* [Tippek a Linux rendszeren futó HDInsight-fürtökhöz](hdinsight-hadoop-linux-information.md)
