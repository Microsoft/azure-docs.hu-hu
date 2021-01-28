---
title: WASB hibakeresése az Azure HDInsight
description: Leírja az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938736"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>WASB hibakeresése az Azure HDInsight

Előfordulhat, hogy meg szeretné ismerni, hogy milyen műveleteket végez a WASB-illesztőprogram az Azure Storage-ban. Az ügyféloldali WASB-illesztőprogram **hibakeresési** szinten létrehozza a naplófájlokat az egyes fájlrendszer-műveletekhez. A WASB-illesztőprogram a log4j használatával vezérli a naplózási szintet, és az alapértelmezett érték az **információs** szint. Az Azure Storage kiszolgálóoldali elemzési naplóival kapcsolatban lásd: az [Azure Storage Analytics naplózása](../../storage/common/storage-analytics-logging.md).

A létrehozott napló a következőhöz hasonlóan fog kinézni:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>A WASB hibakeresési naplójának bekapcsolása

1. Egy böngészőben nyissa meg a (z `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` ) elemet, ahol a a Spark- `CLUSTERNAME` fürt neve.

1. Navigáljon a **speciális spark2-log4j-Properties** elemre.

    1. Módosítás a következőre: `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG` .

    1. Hozzáadás `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Navigáljon a **speciális livy2-log4j-Properties** elemre.

    Hozzáadás `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Mentse a módosításokat.

## <a name="additional-logging"></a>További naplózási lehetőségek

A fenti naplóknak magas szintű ismeretekkel kell rendelkezniük a fájlrendszer műveleteiről. Ha a fenti naplók még nem biztosítanak hasznos információkat, vagy ha a blob Storage API-hívásokat szeretné kivizsgálni, adja hozzá a következőt: `fs.azure.storage.client.logging=true` `core-site` . Ezzel a beállítással engedélyezheti a wasb Java SDK-naplóit, és minden hívást a blob Storage-kiszolgálóra kell nyomtatnia. Távolítsa el a beállítást a vizsgálatok után, mert a lemez gyorsan kitölthető, és lelassíthatja a folyamatot.

Ha a háttér Azure Data Lake alapul, használja a következő log4j-beállítást az összetevőhöz (például Spark/TEZ/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Keresse meg a naplókhoz `/var/log/adl/adl.log` tartozó naplókat.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.