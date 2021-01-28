---
title: Lemezterület kezelése az Azure HDInsight
description: Hibaelhárítási lépések és lehetséges megoldások a lemezterülettel kapcsolatos problémák kezeléséhez az Azure HDInsight-fürtökkel való interakció során.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944827"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Lemezterület kezelése az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="hive-log-configurations"></a>Struktúra-naplózási konfigurációk

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net` , ahol a a `CLUSTERNAME` fürt neve.

1. Navigáljon a **kaptár**  >  **konfigurációk**  >  **speciális**  >  **speciális kaptár-log4j**. Tekintse át a következő beállításokat:

    * `hive.root.logger=DEBUG,RFA`. Ez az alapértelmezett érték, és a [naplózási szintet](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) módosítsa a `INFO` kevesebb napló bejegyzéseinek kinyomtatásához.

    * `log4jhive.log.maxfilesize=1024MB`. Ez az alapértelmezett érték, amelyet igény szerint kell módosítani.

    * `log4jhive.log.maxbackupindex=10`. Ez az alapértelmezett érték, amelyet igény szerint kell módosítani. Ha a paraméter ki lett hagyva, a létrehozott naplófájlok végtelenek lesznek.

## <a name="yarn-log-configurations"></a>A fonalak naplózási beállításai

Tekintse át a következő konfigurációkat:

* Apache Ambari

    1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net` , ahol a a `CLUSTERNAME` fürt neve.

    1. Navigáljon a **struktúra**-  >  **konfigurációk**  >  **speciális**  >  **erőforrás-kezelőjéhez**. Ellenőrizze, hogy be van-e jelölve a **naplózási összesítés engedélyezése** jelölőnégyzet. Ha le van tiltva, a name csomópontok helyileg megőrzik a naplókat, és nem összesítik azokat a távoli tárolóban az alkalmazás befejezésekor vagy megszakításakor.

* Győződjön meg arról, hogy a fürt mérete megfelelő a számítási feladathoz. Lehetséges, hogy a számítási feladat nemrég módosult, vagy előfordulhat, hogy a fürt átméretezése megtörtént. A fürt vertikális [Felskálázása nagyobb számítási](../hdinsight-scaling-best-practices.md) feladatokhoz.

* `/mnt/resource` Előfordulhat, hogy az árva fájlokkal van feltöltve (a Resource Manager újraindítása esetén). Ha szükséges, manuálisan törölje `/mnt/resource/hadoop/yarn/log` és `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.