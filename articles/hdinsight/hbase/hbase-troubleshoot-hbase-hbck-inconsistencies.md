---
title: az hbase hbck inkonzisztencia-értéket ad vissza az Azure HDInsight
description: az hbase hbck inkonzisztencia-értéket ad vissza az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: cbe4231bbecdf279c637cd334336437a020188d4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936989"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Forgatókönyv: `hbase hbck` a parancs inkonzisztencia-értéket ad vissza az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue-region-is-not-in-hbasemeta"></a>Probléma: a régió nincs a (z) `hbase:meta`

Az XXX régiója a HDFS-on, de nem szerepel a `hbase:meta` régióban, vagy nincs telepítve egyetlen régió-kiszolgálón sem.

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Feloldás

1. Javítsa ki a meta-táblázatot a futtatásával:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Régiók RegionServers való hozzárendeléséhez futtassa a következőt:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Probléma: a régió offline állapotban van

A (z) xxx régió nincs telepítve egyetlen RegionServer sem. Ez azt jelenti, hogy a régió `hbase:meta` , de offline állapotban van.

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Feloldás

Régiók online állapotba helyezése a futtatásával:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Probléma: a régiók azonos kezdő/záró kulccsal rendelkeznek

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Feloldás

Egyesítse az átfedésben lévő régiókat manuálisan. Nyissa meg a HBase HMaster webes felület tábla szakaszát, és válassza ki a tábla hivatkozást, amely a probléma. Ekkor megjelenik a táblázathoz tartozó egyes régiók indítási kulcs/vége kulcsa. Ezután egyesítse az átfedésben lévő régiókat. A HBase-rendszerhéjban tegye a t `merge_region 'xxxxxxxx','yyyyyyy', true` . Például:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Ebben az esetben egyesíteni kell a régiót és a RegionC, és a régiót ugyanazzal a RegionB, majd egyesítse a RegionB és a régiót. a xxxxxxx és a YYYYYY az egyes régiók nevének végén található kivonatoló karakterlánc. Ügyeljen arra, hogy ne egyesítse két nem folytonos régiót. Az egyes egyesítések, például az A és A C egyesítése után a HBase megkezdi a tömörítést a régión belül. Várjon, amíg a tömörítés befejeződik, mielőtt újabb egyesítést végez a régión belül. A HBase HMaster felhasználói felületén megtalálhatja a tömörítési állapotot a régió-kiszolgáló lapon.

---

## <a name="issue-cant-load-regioninfo"></a>Probléma: nem tölthető be `.regioninfo`

Nem tölthető be `.regioninfo` a régió `/hbase/data/default/tablex/regiony` .

### <a name="cause"></a>Ok

Ez valószínűleg a régió részleges törlése miatt RegionServer összeomlik vagy a virtuális gép újraindításakor. Az Azure Storage jelenleg egy egyszerű blob-fájlrendszer, és néhány fájl művelete nem atomi.

### <a name="resolution"></a>Feloldás

A fennmaradó fájlok és mappák manuális törlése:

1. Végrehajtásával `hdfs dfs -ls /hbase/data/default/tablex/regiony` Győződjön meg arról, hogy a mappák/fájlok még mindig alá vannak tartva.

1. Végrehajtás `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` az összes gyermekobjektum és mappa törléséhez

1. Végrehajtás `hdfs dfs -rmr /hbase/data/default/tablex/regiony` a területi mappa törléséhez.

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.