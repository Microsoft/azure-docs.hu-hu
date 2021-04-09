---
title: A .NET for Apache Spark frissítése a v 1.0-s verzióra a HDI-ben
description: Ismerje meg, hogyan frissíthet a Apache Spark .NET-et a 1,0-es verzióra a HDI-ben, és hogy ez milyen hatással van a meglévő kódra és fürtökre.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788129"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>A .NET Apache Spark frissítése a 1.0-s verzióra a HDInsight-ben

Ez a dokumentum a .net első főverzióját mutatja be [Apache Spark](https://github.com/dotnet/spark), valamint azt, hogy ez milyen hatással lehet az aktuális üzemi folyamatokra a HDInsight-fürtökben.

## <a name="about-net-for-apache-spark-version-100"></a>Tudnivalók a .NET-hez Apache Spark a 1.0.0 verzióhoz

Ez a .NET első [jelentős kiadása](https://github.com/dotnet/spark/releases/tag/v1.0.0) a Apache Spark számára, és a Spark 2.4. x és a Spark 3.0. x DataFrame API-teljességét biztosítja más funkciókkal együtt. A funkciók, a javítások és a hibajavítások teljes listáját a hivatalos [v 1.0.0 kibocsátási megjegyzései](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)részben tekintheti meg.
Egy másik fontos tudnivaló, hogy ez a verzió **nem** kompatibilis a és a korábbi verzióival `Microsoft.Spark` `Microsoft.Spark.Worker` . Tekintse át az [áttelepítési útmutatót](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , ha azt tervezi, hogy frissítenie kell a .NET-et Apache Spark alkalmazásra, hogy kompatibilis legyen a v 1.0.0-val.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>.NET használata Apache Spark 1.0-s verzióhoz a HDInsight-ben

Habár az aktuális HDI-fürtöket nem érinti a rendszer (azaz még a korábbinál is ugyanaz a verzió), az újonnan létrehozott HDI-fürtök a .NET-hez készült legújabb, a Apache Spark-hoz készült verzióját fogják látni. Ez azt jelenti, hogy ha:

- **Régebbi HDI-fürttel rendelkezik**: Ha a Spark .NET-alkalmazást a v 1.0.0-ra szeretné frissíteni (ajánlott), akkor frissítenie kell a `Microsoft.Spark.Worker` verziót a HDI-fürtön. További információ: a .NET- [verziók módosítása a HDI-fürtön a Apache Sparkhoz szakasz](#changing-net-for-apache-spark-version-on-hdinsight).
Ha nem szeretné frissíteni a .NET aktuális verzióját az alkalmazásban Apache Spark, nincs szükség további lépésekre.  

- **Új HDI-fürttel rendelkezik**: Ha a Spark .NET-alkalmazást a v 1.0.0-ra szeretné frissíteni (ajánlott), akkor a feldolgozót nem kell módosítania a HDI-n, azonban az [áttelepítési útmutatóban](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) meg kell ismernie a kód és a folyamatok frissítéséhez szükséges lépéseket.
Ha nem szeretné módosítani a .NET aktuális verzióját az alkalmazásban Apache Sparkhoz, akkor a HDI-fürtön lévő verziót a v 1.0-s verziójáról kell módosítania (az alapértelmezett érték az új fürtökön), amely a használt verziót használja. További információ: a .NET- [verziók módosítása a HDI-fürtön a Apache Sparkhoz szakasz](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>A .NET Apache Spark verzióra való módosítása a HDInsight-on

### <a name="deploy-microsoftsparkworker"></a>A Microsoft. Spark. Worker üzembe helyezése

`Microsoft.Spark.Worker` a egy háttér-összetevő, amely a Spark-fürt egyes munkavégző csomópontjain él. Ha C# UDF-t (felhasználó által definiált függvényt) szeretne végrehajtani, a Sparknak ismernie kell, hogyan kell elindítani a .NET CLR-t az UDF végrehajtásához. `Microsoft.Spark.Worker` a Spark-osztályokból álló gyűjteményt biztosít, amely engedélyezi ezt a funkciót. Válassza ki a feldolgozó verzióját attól függően, hogy a .NET melyik verzióját szeretné telepíteni a HDI-fürtön Apache Spark.

1. Töltse le az adott verzió Microsoft. Spark. Worker Linux kiadását. Ha például szeretné `.NET for Apache Spark v1.0.0` , töltse le a [Microsoft. Spark. Worker. netcoreapp 3.1. Linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)fájlját.  

2. Töltse le a [install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) parancsfájlt az 1. lépésben letöltött feldolgozó bináris fájlok telepítéséhez a HDI-fürt összes munkavégző csomópontjára.  

3. Töltse fel a fent említett fájlokat azon az Azure Storage-fiókba, amelyhez a fürt hozzáfér. További részletekért tekintse meg [a .net for Apache Spark HDI üzembe helyezési cikkét](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) .

4. Futtassa a `install-worker.sh` parancsfájlt a fürt összes munkavégző csomópontján parancsfájl-műveletek használatával. További információért tekintse meg [a .net for Apache Spark HDI üzembe helyezési cikkét](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) .

### <a name="update-your-application-to-use-specific-version"></a>Az alkalmazás frissítése adott verzió használatára

A .NET for Apache Spark alkalmazást egy adott verzió használatára frissítheti a [Microsoft. Spark NuGet-csomag](https://www.nuget.org/packages/Microsoft.Spark/) szükséges verziójának a projektben való kiválasztásával. Ügyeljen arra, hogy az adott verzió és az [áttelepítési útmutató](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) kibocsátási megjegyzéseit a fentiekben leírtak szerint tekintse át, ha az alkalmazást a v 1.0.0-ra szeretné frissíteni.

## <a name="faqs"></a>Gyakori kérdések

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>A meglévő HDI-fürt verziója < 1.0.0-val nem sikerül az új kiadásban?

A meglévő HDI-fürtök továbbra is ugyanazzal a korábbi verzióval rendelkeznek a .NET-hez Apache Spark és a meglévő alkalmazás (a Spark .NET korábbi verziója) nem lesz hatással.

## <a name="next-steps"></a>Következő lépések

[A .NET telepítése Apache Spark alkalmazáshoz a HDInsight-on](/dotnet/spark/tutorials/hdinsight-deployment)