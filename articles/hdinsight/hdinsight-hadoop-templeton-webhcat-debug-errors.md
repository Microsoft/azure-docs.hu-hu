---
title: Webhcaten-hibák megértése és megoldása a HDInsight-ben – Azure
description: Ismerkedjen meg a Webhcaten által a HDInsight-on visszaadott gyakori hibákkal és azok megoldásával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 40d49d156b76db5e02ec48defbb82ed60819c478
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651109"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>A WebHCattől kapott hibák megértése és megoldása HDInsightban

További információ a Webhcaten és a HDInsight használatával fogadott hibákról, valamint azok megoldásáról. A Webhcaten az ügyféloldali eszközök, például a Azure PowerShell és a Visual Studio Data Lake eszközei belsőleg használják.

## <a name="what-is-webhcat"></a>Mi az a Webhcaten?

A [webhcaten](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a Apache Hadoop [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), táblázat-és tárolási felügyeleti rétegének REST API. A Webhcaten alapértelmezés szerint engedélyezve van a HDInsight-fürtökön, és számos eszköz használja a feladatok elküldésére, a feladat állapotának beolvasására és így tovább, a fürtbe való bejelentkezés nélkül.

## <a name="modifying-configuration"></a>Konfiguráció módosítása

A dokumentumban felsorolt hibák némelyike miatt a rendszer túllépte a beállított maximumot. Ha a megoldási lépés megemlíti, hogy módosítható egy érték, az Apache Ambari (web vagy REST API) használatával módosíthatja az értéket. További információ: [HDInsight kezelése Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Ha túllépi a következő alapértelmezett értékeket, akkor csökkenhet a Webhcaten teljesítménye, vagy hibákat okozhat:

| Beállítás | Művelet | Alapértelmezett érték |
| --- | --- | --- |
| [fonal. Scheduler. Capacity. max-Applications][maximum-applications] |A egyszerre aktív feladatok maximális száma (függőben vagy fut) |10,000 |
| [Templeton. exec. max-procs][max-procs] |Az egyszerre kiszolgálható kérelmek maximális száma |20 |
| [MapReduce. jobhistory. max-Age-MS][max-age-ms] |Azon napok száma, ameddig a feladatok előzményei megmaradnak |7 nap |

## <a name="too-many-requests"></a>Túl sok kérelem

**Http-állapotkód**: 429

| Ok | Megoldás: |
| --- | --- |
| Túllépte a percenkénti Webhcaten által kiszolgált maximális egyidejű kéréseket (alapértelmezett érték: 20). |Csökkentse a munkaterhelést, és győződjön meg arról, hogy nem küldi el az egyidejű kérések maximális számát, vagy növelje az egyidejű kérések korlátját a módosítással `templeton.exec.max-procs` . További információ: [konfiguráció módosítása](#modifying-configuration) |

## <a name="server-unavailable"></a>A kiszolgáló nem érhető el

**Http-állapotkód**: 503

| Ok | Megoldás: |
| --- | --- |
| Ez az állapotkód általában a fürt elsődleges és másodlagos Átjárócsomóponthoz közötti feladatátvétel során fordul elő |Várjon két percet, majd próbálja megismételni a műveletet. |

## <a name="bad-request-content-could-not-find-job"></a>Helytelen kérelem tartalma: nem található a feladattípus.

**Http-állapotkód**: 400

| Ok | Megoldás: |
| --- | --- |
| A feladatok részleteit tisztábban megtisztították |A feladatok előzményeinek alapértelmezett megőrzési ideje 7 nap. Az alapértelmezett megőrzési időtartam módosítható `mapreduce.jobhistory.max-age-ms` . További információ: [konfiguráció módosítása](#modifying-configuration) |
| A feladatot egy feladatátvétel miatt meggyilkolták |Feladat beküldésének újrapróbálása legfeljebb két percig |
| A rendszer érvénytelen AZONOSÍTÓJÚ feladatot használt |Ellenőrizze, hogy helyes-e a feladatokhoz tartozó azonosító |

## <a name="bad-gateway"></a>Hibás átjáró

**Http-állapotkód**: 502

| Ok | Megoldás: |
| --- | --- |
| A belső adatgyűjtési folyamat a Webhcaten folyamaton belül következik be |Várjon, amíg befejeződik a Webhcaten-szolgáltatás begyűjtése vagy újraindítása |
| Időtúllépés a erőforráskezelő szolgáltatás válaszára való várakozáskor. Ez a hiba akkor fordulhat elő, ha az aktív alkalmazások száma a beállított maximális értékre kerül (alapértelmezett 10 000). |Várjon, amíg a jelenleg futó feladatok befejeződik, vagy növelje az egyidejű feladat korlátját módosítással `yarn.scheduler.capacity.maximum-applications` . További információ: a [konfiguráció módosítása](#modifying-configuration) szakasz. |
| Az összes feladat beolvasására tett kísérlet a [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) hívással `Fields` , míg a értéke`*` |Ne kérje le *az összes* feladatot. Ehelyett `jobid` a feladatokhoz csak bizonyos feladat-azonosítónál nagyobb részleteket kér le. Vagy ne használja`Fields` |
| A Webhcaten szolgáltatás nem érhető el a Átjárócsomóponthoz feladatátvétel során |Várjon két percet, és ismételje meg a műveletet. |
| Több mint 500 függőben lévő feladat van elküldve a Webhcaten-on keresztül |Várjon, amíg a jelenleg függőben lévő feladatok befejeződtek a további feladatok elküldése előtt |

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml