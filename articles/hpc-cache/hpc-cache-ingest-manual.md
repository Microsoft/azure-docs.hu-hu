---
title: Azure HPC cache-adatfeldolgozás – manuális másolás
description: A CP-parancsok használata az Azure HPC cache-ben lévő blob Storage-tárolóba való áthelyezéshez
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092337"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC cache-adatfeldolgozás – manuális másolási módszer

Ez a cikk részletes útmutatást nyújt az adatok blob Storage-tárolóba történő manuális másolásához az Azure HPC cache használatával. Több szálon futó párhuzamos műveleteket használ a másolás sebességének optimalizálása érdekében.

Ha többet szeretne megtudni arról, hogy az Azure HPC gyorsítótára hogyan helyezi át az adatátvitelt a blob Storage-ba, olvassa el az [Azure Blob Storage](hpc-cache-ingest.md)-ba

## <a name="simple-copy-example"></a>Egyszerű másolási példa

Manuálisan is létrehozhat többszálas másolatot egy ügyfélen, ha több másolási parancsot futtat egyszerre a háttérben a fájlok vagy elérési utak előre definiált készletei között.

A Linux/UNIX ``cp`` parancs tartalmazza a ``-p`` tulajdonosi és a mtime metaadatok megőrzésére szolgáló argumentumot. Az argumentum hozzáadása az alábbi parancsokhoz nem kötelező. (Az argumentum hozzáadása növeli az ügyféltől a célhely fájlrendszerig a metaadatok módosítására irányuló fájlrendszer-hívások számát.)

Ez az egyszerű példa két fájlt másol át párhuzamosan:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

A parancs kiadása után a `jobs` parancs azt jeleníti meg, hogy két szál fut.

## <a name="copy-data-with-predictable-file-names"></a>Az Adatmásolás Kiszámítható fájlnevekkel

Ha a fájlnevek előre jelezhető, használhat kifejezéseket párhuzamos másolási szálak létrehozására.

Ha például a könyvtár 1000-es fájlokat tartalmaz, amelyek egymás után vannak számozva a rendszerből `0001` `1000` , az alábbi kifejezésekkel hozhat létre 10 párhuzamos szálat, amelyet az egyes 100-fájlok másolnak:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Adatmásolás strukturálatlan fájlnevekkel

Ha a fájl elnevezési szerkezete nem kiszámítható, a fájlokat a címtár neve alapján csoportosíthatja.

Ez a példa a parancsok futtatásához használható teljes címtárakat gyűjti a ``cp`` háttérben:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

A fájlok gyűjtése után párhuzamos másolási parancsok futtatásával rekurzív módon másolhatja az alkönyvtárakat és azok tartalmát:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Csatlakoztatási pontok hozzáadása

Ha már van elég párhuzamos szála egy adott célfájl-rendszer csatlakoztatási pontján, akkor a több szál hozzáadására szolgáló pont nem ad nagyobb átviteli sebességet. (Az adatátviteli sebességet a rendszer a megadott adattípustól függően a fájl/másodperc vagy a bájt/másodperc értékben méri.) Vagy ami még rosszabb, az átviteli sebesség romlása időnként okozhatja a teljesítményt.

Ha ez történik, az ügyféloldali csatlakoztatási pontokat hozzáadhatja más Azure HPC gyorsítótár-csatlakoztatási címekhez ugyanazzal a távoli fájlrendszer-csatlakozási útvonallal:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Az ügyféloldali csatlakoztatási pontok hozzáadásával kikapcsolhatja a további másolási parancsokat a további `/mnt/destination[1-3]` csatlakoztatási pontokra a további párhuzamosságok elérése érdekében.

Ha például a fájlok nagyon nagy méretűek, a másolási parancsokat definiálhatja a különböző elérési utak használatára, és a másolást végző ügyféltől párhuzamosan több parancsot is küldhet.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

A fenti példában mindhárom cél csatlakoztatási pontra az ügyfél fájlmásolási folyamata irányul.

## <a name="when-to-add-clients"></a>Mikor kell ügyfeleket felvenni

Végül, ha elérte az ügyfél képességeit, további másolási szálak vagy további csatlakoztatási pontok hozzáadása nem eredményez további fájlokat/mp-t vagy bájt/mp-t. Ebben az esetben egy másik ügyfelet is telepíthet ugyanazon a csatlakoztatási ponttal, amely a fájlmásolás folyamatainak saját készletét fogja futtatni.

Példa:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Fájl-jegyzékfájlok létrehozása

Miután megértette a fenti megközelítéseket (több másolási szál a célhelyen, több célhely/ügyfél, a hálózaton keresztül elérhető forrásfájl több ügyfele), vegye figyelembe ezt a javaslatot: fájl-jegyzékfájlok létrehozása, majd azok használata több ügyfél másolási parancsaival.

Ez a forgatókönyv a UNIX ``find`` parancs használatával hozza létre a fájlok vagy könyvtárak jegyzékeit:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Az eredmény átirányítása fájlba: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Ezután megismételheti a jegyzékfájlt, ha BASH-parancsokat használ a fájlok számlálásához és az alkönyvtárak méretének meghatározásához:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Végül meg kell adnia a tényleges fájlmásolás-parancsokat az ügyfeleknek.

Ha négy ügyféllel rendelkezik, használja a következő parancsot:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Ha öt ügyfele van, használja az alábbihoz hasonlót:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

És hat.... Kikövetkeztetés igény szerint.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

A rendszer *n* eredményül kapott fájlokat fog kapni, amelyek mindegyike *n* -ügyfélhez tartozik, és az elérési út neve megegyezik a parancs kimenetének részeként megadott négy szintű könyvtárral `find` .

Az egyes fájlok használatával hozza létre a másolási parancsot:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

A fentiekben *N* fájl jelenik meg, amelyek mindegyike soronként egy másolási paranccsal fut, amely bash-parancsfájlként is futtatható az ügyfélen.

A cél a parancsfájlok több szálának párhuzamos futtatása egyszerre több ügyfélen egyidejűleg.
