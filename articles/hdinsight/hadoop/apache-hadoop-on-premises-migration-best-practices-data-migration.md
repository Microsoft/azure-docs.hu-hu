---
title: 'Adatáttelepítés: helyszíni Apache Hadoop az Azure HDInsight'
description: A helyszíni Hadoop-fürtök Azure HDInsight való áttelepítésére vonatkozó ajánlott eljárások ismertetése.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cdb028bcd608aa7a19a7c83e5e2c1129386928a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939795"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsight – az adatáttelepítés ajánlott eljárásai

Ez a cikk ajánlásokat nyújt az Azure HDInsight való adatáttelepítés során. Egy sorozat része, amely ajánlott eljárásokat biztosít a helyszíni Apache Hadoop rendszerek Azure HDInsight való áttelepítésének segítésére.

## <a name="migrate-on-premises-data-to-azure"></a>Helyszíni adatbázis migrálása az Azure-ba

Két fő lehetőség áll rendelkezésre a helyszíni adatok Azure-környezetbe történő átköltöztetésére:

* Adatok átvitele hálózaton keresztül TLS használatával
    * Interneten keresztül – az adatok az Azure Storage-ba normál internetkapcsolaton keresztül, többek között a következők egyikével vihetők át: Azure Storage Explorer, AzCopy, Azure PowerShell és Azure CLI. További információkért lásd: [adatok áthelyezése az Azure Storage szolgáltatásba és onnan](../../storage/common/storage-choose-data-transfer-solution.md).

    * Az Express Route-ExpressRoute egy Azure-szolgáltatás, amellyel privát kapcsolatokat hozhat létre a Microsoft-adatközpontok és a helyszíni vagy egy közös elhelyezési létesítményben lévő infrastruktúra között. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, és nagyobb biztonságot, megbízhatóságot és sebességet biztosítanak, mint a szokásos kapcsolatok az interneten. További információ: [ExpressRoute-kör létrehozása és módosítása](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Data Box online adatátvitel – a Data Box Edge és a Data Box Gateway olyan online adatátviteli termékek, amelyek hálózati tároló átjáróként működnek a hely és az Azure közötti adatkezelés érdekében. Data Box Edge, egy helyszíni hálózati eszköz, amely az Azure-ba irányuló és onnan érkező adatok átvitelét végzi, és mesterséges intelligenciát (AI) használó Edge-számítást alkalmaz az adatok feldolgozásához. Data Box Gateway a Storage Gateway-képességekkel rendelkező virtuális készülék. További információ: [Azure Data Box dokumentáció – online átvitel](../../databox-online/index.yml).

* Adatszállítás kapcsolat nélküli üzemmódban

    Data Box offline adatátvitel – a Data Box, a Data Box Disk és az Data Box Heavy eszközök segítségével nagy mennyiségű adatok vihetők át az Azure-ba, ha a hálózat nem választható. Ezeket az offline adatátviteli eszközöket a szervezet és az Azure-adatközpont között szállítjuk. AES-titkosítást használnak az adatok átvitel közbeni védelmére, és egy alapos feltöltés utáni tisztítási folyamatba esnek, amely az adatok törlését az eszközről. További információ a Data Box offline átvitelű eszközökről: [Azure Data Box dokumentáció – offline átvitel](../../databox/index.yml). A Hadoop-fürtök áttelepítésével kapcsolatos további információkért tekintse [meg a helyszíni HDFS-tárolóból az Azure Storage-ba való migrálás Azure Data Box használatát](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)ismertető témakört.

Az alábbi táblázat az adatmennyiség és a hálózati sávszélesség alapján közelíti meg az adatátviteli időtartamot. Adatmező használata, ha az adatáttelepítés várhatóan három hétig is eltarthat.

|Adatmennyiség | Hálózati sávszélesség<br>/<br>**45 Mbps (T3)**|Hálózati sávszélesség<br>/<br>**100 Mbps**|Hálózati sávszélesség<br>/<br>**1 Gbps**|Hálózati sávszélesség<br>/<br>**10 Gbps**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 nap|1 nap| 2 óra|14 perc|
|10 TB|22 nap|10 nap|1 nap|2 óra|
|35 TB|76 nap|34 nap|3 nap|8 óra|
|80 TB|173 nap|78 nap|8 nap|19 óra|
|100 TB|216 nap|97 nap|10 nap|1 nap|
|200 TB|1 év|194 nap|19 nap|2 nap|
|500 TB|3 év|1 év|49 nap|5 nap|
|1 PB|6 év|3 év|97 nap|10 nap|
|2 PB|12 év|5 év|194 nap|19 nap|

Az Azure-ba natív eszközök, például Apache Hadoop DistCp, Azure Data Factory és AzureCp, az adatok hálózaton keresztüli átvitelére használhatók. A harmadik féltől származó eszköz WANDisco is használható ugyanarra a célra. A helyszíni és az Azure Storage rendszerbe való folyamatos adatátvitelhez Apache Kafka MirrorMaker és Apache Sqoop használható.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Teljesítménnyel kapcsolatos megfontolások Apache Hadoop DistCp használatakor

A DistCp egy Apache-projekt, amely egy MapReduce térképi feladatot használ az adatok átviteléhez, a hibák kezeléséhez és a hibák helyreállításához. Hozzárendeli a forrásfájlok listáját az egyes térképi feladatokhoz. A Térkép feladat ezután az összes hozzárendelt fájlt átmásolja a célhelyre. Több módszer is javíthatja a DistCp teljesítményét.

### <a name="increase-the-number-of-mappers"></a>A leképezések számának növelésével

A DistCp megkísérli létrehozni a leképezési feladatokat, hogy mindegyik példány nagyjából azonos számú bájtot hozzon létre. Alapértelmezés szerint a DistCp-feladatok 20 leképezést használnak. Ha több leképezést használ a Distcp (a "m" paraméterrel a parancssorban), az adatátviteli folyamat során növeli a párhuzamosságot, és csökkenti az adatátvitel hosszát. Azonban két megfontolandó szempontot kell figyelembe vennie a leképezések számának növelésével kapcsolatban:

* A DistCp legalacsonyabb részletessége egyetlen fájl. Több, a forrásfájlok számánál nagyobb számú Mapper meghatározása nem segít, és a rendelkezésre álló fürterőforrás-erőforrások pazarlását fogja megadni.

* Vegye figyelembe a rendelkezésre álló szál memóriáját a fürtön a leképezések számának megállapításához. Minden leképezési feladat egy szál-tárolóként indul el. Feltételezve, hogy a fürtön nem futnak más nagy terhelésű feladatok, a leképezések számát a következő képlet határozza meg: m = (a feldolgozó csomópontok száma az \* egyes munkavégző csomópontok számára)/a fonalak tárolójának mérete. Ha azonban más alkalmazások is használják a memóriát, a DistCp feladatokhoz csak a FONALak memóriájának egy részét használják.

### <a name="use-more-than-one-distcp-job"></a>Egynél több DistCp-feladatot használjon

Ha az áthelyezni kívánt adatkészlet mérete nagyobb, mint 1 TB, használjon egynél több DistCp feladatot. Ha egynél több feladatot használ, korlátozza a hibák hatását. Ha bármelyik feladat meghiúsul, csak az összes feladat helyett újra kell indítania az adott feladatot.

### <a name="consider-splitting-files"></a>A fájlok felosztásának megfontolása

Ha kis számú nagyméretű fájl van, akkor érdemes megfontolnia, hogy 256 MB méretű fájlokba darabolja őket, hogy nagyobb potenciális párhuzamosságot kapjon a további leképezésekkel.

### <a name="use-the-strategy-command-line-parameter"></a>A "stratégia" parancssori paraméter használata

Érdemes lehet `strategy = dynamic` paramétert használni a parancssorban. A paraméter alapértelmezett értéke `strategy` `uniform size` :, ebben az esetben az egyes leképezések nagyjából azonos számú bájtot másolnak. Ha ez a paraméter a értékre módosul `dynamic` , a listaelem több "darab-fájlra" oszlik. Az adathalmazok száma – a fájlok száma a térképek számának többszöröse. Minden Térkép feladathoz hozzá van rendelve egy adathalmaz-fájl. Az adathalmaz összes elérési útjának feldolgozását követően a rendszer törli az aktuális adatrészletet, és új adatrészletet szerez be. A folyamat addig folytatódik, amíg nem állnak rendelkezésre több adathalmaz. Ez a "dinamikus" megközelítés lehetővé teszi a gyorsabb Térkép-feladatok használatát, hogy több útvonalat használjanak, mint a lassabbak, így a DistCp-feladat teljes felgyorsítása.

### <a name="increase-the-number-of-threads"></a>A szálak számának növelésével

Ellenőrizze, hogy a paraméter növelése növeli-e a `-numListstatusThreads` teljesítményt. Ezzel a paraméterrel állítható be, hogy hány szálat kell használni a fájlok listázásához, a 40 pedig a maximális értéket.

### <a name="use-the-output-committer-algorithm"></a>A kimeneti committer algoritmus használata

Ellenőrizze, hogy a paraméter átadása növeli-e a `-Dmapreduce.fileoutputcommitter.algorithm.version=2` DistCp teljesítményét. Ez a kimeneti committer algoritmus optimalizációt tartalmaz a kimeneti fájlok célhelyre írásához. A következő parancs egy példát mutat be a különböző paraméterek használatára:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Metaadatok áttelepítése

### <a name="apache-hive"></a>Apache Hive

A struktúra metaadattár áttelepíthetők a parancsfájlok használatával vagy az adatbázis-replikáció használatával.

#### <a name="hive-metastore-migration-using-scripts"></a>Áttelepítés Hive-metaadattár parancsfájlok használatával

1. Létrehozza a kaptár DDLs a helyszíni Hive-metaadattárból. Ezt a lépést [burkoló bash-parancsfájl](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)használatával teheti meg.
1. Szerkessze a generált DDL-t a HDFS URL-cím WASB/ADLS/ABFS URL-címekre való lecseréléséhez.
1. Futtassa a frissített DDL-t a metaadattár a HDInsight-fürtből.
1. Győződjön meg arról, hogy a Hive-metaadattár verziója kompatibilis a helyszíni és a felhő között.

#### <a name="hive-metastore-migration-using-db-replication"></a>Áttelepítés Hive-metaadattár adatbázis-replikáció használatával

- Adatbázis-replikáció beállítása a helyszíni Hive-metaadattár DB és a HDInsight metaadattár DB között.
- Használja a "kaptár MetaTool" a HDFS URL-cím lecserélése a WASB/ADLS/ABFS URL-címekkel, például:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Helyszíni Ranger-házirendek exportálása XML-fájlokba.
- Alakítsa át a helyszíni HDFS-alapú útvonalakat a WASB/ADLS-ra egy olyan eszköz használatával, mint az XSLT.
- Importálja a szabályzatokat a HDInsight-on futó Rangerre.

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikket a sorozatban:

- [Biztonsági és DevOps ajánlott eljárások helyszíni Azure HDInsight Hadoop áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-security-devops.md)