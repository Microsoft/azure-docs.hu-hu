---
title: Az Azure-ba irányuló avere-vFXT áthelyezése
description: Az Azure-hoz készült avere-vFXT való használatra szolgáló új tárolási kötethez való adatfelvétel módja
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 76bbe60397ebb01aed5694d933b3067f778a4c21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505596"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Adatáthelyezés a vFXT-fürtbe – párhuzamos adatfeldolgozás

Miután létrehozott egy új vFXT-fürtöt, az első feladat lehet az adatok áthelyezése egy új tárolási kötetre az Azure-ban. Ha azonban az adatok áthelyezésének szokásos módja egy egyszerű másolási parancs kiadása az egyik ügyfélről, valószínűleg lassú másolási teljesítményt fog látni. Az egyszálas másolás nem jó választás az adatoknak a avere vFXT-fürt háttér-tárolójába való másolásához.

Mivel az Azure-fürt avere-vFXT egy skálázható, több ügyfélre kiterjedő gyorsítótár, amely az Adatmásolás leggyorsabb és leghatékonyabb módja több ügyfél esetén. Ezzel a technikával parallelizes a fájlok és objektumok betöltését.

![Több ügyfélből álló, többszálas adatáthelyezést ábrázoló diagram: a bal felső sarokban a helyszíni hardveres tárterület ikonja több nyílból származik. A nyilak négy ügyfélszámítógépre mutatnak. Az egyes ügyfélgépekről három nyíl mutat a avere vFXT felé. A avere vFXT több nyíl mutat a blob Storage-ra.](media/avere-vfxt-parallel-ingest.png)

Az ``cp`` ``copy`` adatok az egyik tárolási rendszerről a másikra történő átviteléhez leggyakrabban használt vagy parancsok olyan egyszálas folyamatok, amelyek egyszerre csak egy fájlt másolnak. Ez azt jelenti, hogy a fájlkiszolgáló egyszerre csak egy fájlt tölt be – ez a fürt erőforrásainak hulladéka.

Ez a cikk a több ügyfelet tartalmazó, többszálas fájlmásolási rendszer létrehozására szolgáló stratégiákat ismerteti az adatoknak a avere vFXT-fürtbe való áthelyezéséhez. Ismerteti a fájlátviteli fogalmakat és a döntési pontokat, amelyek segítségével több ügyfél és egyszerű másolási parancs használatával hatékony Adatmásolást lehet használni.

Emellett ismerteti azokat a segédprogramokat is, amelyek segíthetnek. A ``msrsync`` segédprogram használatával részben automatizálható az adatkészlet gyűjtővé való osztása és ``rsync`` parancsok használata. A ``parallelcp`` parancsfájl egy másik segédprogram, amely beolvassa a forrás könyvtárat, és automatikusan kiadja a másolási parancsokat. Emellett az ``rsync`` eszköz két fázisban is használható, így gyorsabb másolást biztosít, amely továbbra is biztosítja az adatkonzisztenciát.

Kattintson a hivatkozásra a szakaszra való ugráshoz:

* [Manuális másolási példa](#manual-copy-example) – alapos magyarázat a másolási parancsok használatával
* [Kétfázisú rsync-példa](#use-a-two-phase-rsync-process)
* [Részlegesen automatizált (msrsync) példa](#use-the-msrsync-utility)
* [Példa párhuzamos másolásra](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Adatfeldolgozó virtuálisgép-sablon

A GitHubon egy Resource Manager-sablon áll rendelkezésre, amely automatikusan létrehoz egy virtuális gépet a jelen cikkben említett párhuzamos adatfeldolgozási eszközökkel.

![a blob Storage, a hardver Storage és az Azure file sources használatával egyszerre több nyilat ábrázoló diagram. A nyilak egy "betöltési virtuális gépre" és onnan több nyílra mutatnak a avere vFXT](media/avere-vfxt-ingestor-vm.png)

Az adatfeldolgozó virtuális gép egy olyan oktatóanyag része, ahol az újonnan létrehozott virtuális gép csatlakoztatja a avere vFXT-fürtöt, és letölti a rendszerindítási parancsfájlt a fürtből. A részletekért olvassa el a rendszerindító [adatfeldolgozó virtuális gép](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) című cikkét.

## <a name="strategic-planning"></a>Stratégiai tervezés

Amikor párhuzamosan másolja az adatmásolási stratégiát, ismernie kell a fájlok mérete, a fájlok száma és a könyvtár mélysége közötti kompromisszumokat.

* Ha a fájlok kicsik, a kamat a fájlok másodpercenkénti száma.
* Ha a fájlok nagy méretűek (10MiBi vagy nagyobbak), a kamat mérőszáma másodpercenként bájt.

Minden másolási folyamathoz tartozik egy átviteli sebesség és egy fájl – átvitt sebesség, amely a másolási parancs hosszának időzítésével és a fájlméret és a fájlok számának megadásával mérhető. A díjszabás mértékének megállapítása kívül esik a jelen dokumentum hatókörén, de fontos tisztában lennie azzal, hogy a kis-és nagyméretű fájlokat kell-e kezelni.

## <a name="manual-copy-example"></a>Példa manuális másolásra

Manuálisan is létrehozhat többszálas másolatot egy ügyfélen, ha több másolási parancsot futtat egyszerre a háttérben a fájlok vagy elérési utak előre definiált készletei között.

A Linux/UNIX ``cp`` parancs tartalmazza a ``-p`` tulajdonosi és a mtime metaadatok megőrzésére szolgáló argumentumot. Az argumentum hozzáadása az alábbi parancsokhoz nem kötelező. (Az argumentum hozzáadása növeli az ügyfél és a cél fájlrendszer közötti, a metaadatok módosítására irányuló hívások számát.)

Ez az egyszerű példa két fájlt másol át párhuzamosan:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

A parancs kiadása után a `jobs` parancs azt jeleníti meg, hogy két szál fut.

### <a name="predictable-filename-structure"></a>Kiszámítható fájlnév struktúra

Ha a fájlnevek kiszámíthatók, a kifejezések használatával párhuzamos másolási szálakat hozhat létre.

Ha például a könyvtár 1000-es fájlokat tartalmaz, amelyek egymás után vannak számozva `0001` `1000` , a következő kifejezésekkel hozhat létre tíz párhuzamos szálat, amelyeket az egyes 100-fájlok másolnak:

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

### <a name="unknown-filename-structure"></a>Ismeretlen fájlnév-struktúra

Ha a fájl-elnevezési struktúra nem kiszámítható, a fájlokat a címtár neve alapján csoportosíthatja.

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

### <a name="when-to-add-mount-points"></a>Csatlakoztatási pontok hozzáadása

Ha elegendő párhuzamos szálat használ egy adott cél fájlrendszer csatlakoztatási pontján, akkor a több szál hozzáadására szolgáló pont nem ad nagyobb átviteli sebességet. (Az adatátviteli sebességet a rendszer a megadott adattípustól függően a fájl/másodperc vagy a bájt/másodperc értékben méri.) Vagy ami még rosszabb, az átviteli sebesség romlása időnként okozhatja a teljesítményt.

Ebben az esetben az ügyféloldali csatlakoztatási pontokat más vFXT-fürt IP-címeihez is hozzáadhatja ugyanazzal a távoli fájlrendszer-csatlakozási útvonallal:

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

### <a name="when-to-add-clients"></a>Mikor kell ügyfeleket felvenni

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

### <a name="create-file-manifests"></a>Fájl-jegyzékfájlok létrehozása

Miután megértette a fenti megközelítéseket (több másolási szál/cél, több ügyfél, hálózaton keresztül elérhető forrásoldali fájlrendszer), vegye figyelembe ezt a javaslatot: fájl-jegyzékfájlok létrehozása, majd azok használata több ügyfél másolási parancsaival.

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

## <a name="use-a-two-phase-rsync-process"></a>Kétfázisú rsync-folyamat használata

A standard ``rsync`` segédprogram nem jól működik a felhőalapú tárolásnak az Azure-rendszerhez készült avere-vFXT keresztüli feltöltéséhez, mert az adatintegritás biztosításához nagy számú fájl létrehozására és átnevezésére szolgáló műveletet hoz létre. Ha azonban azt követi, hogy ``--inplace`` ``rsync`` egy második futtatással a fájl integritását ellenőrzi, nyugodtan használhatja a kapcsolót.

A normál ``rsync`` másolási művelet létrehoz egy ideiglenes fájlt, és betölti az adatmennyiséget. Ha az adatátvitel sikeresen befejeződött, az ideiglenes fájl átnevezve lesz az eredeti fájlnévre. Ez a módszer akkor is garantálja a konzisztenciát, ha a fájlok a másolás során hozzáférnek. Ez a módszer azonban több írási műveletet generál, ami lelassítja a fájlok áthelyezését a gyorsítótárban.

A beállítás ``--inplace`` közvetlenül a végső helyén írja az új fájlt. A fájlok nem garantáltan konzisztensek az átvitel során, de ez nem fontos, ha később szeretné használni a tárolási rendszereket.

A második ``rsync`` művelet konzisztencia-ellenőrzésként szolgál az első művelethez. Mivel a fájlok már át lettek másolva, a második fázis egy gyors vizsgálat annak biztosítására, hogy a célhelyen lévő fájlok megegyezzenek a forrás fájljaival. Ha bármelyik fájl nem egyezik, a rendszer újramásolja őket.

Mindkét fázist egy paranccsal együtt is kiállíthatja:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Ez a módszer egyszerű és időigényes módszer az adatkészletek számára a belső címtár-kezelő által kezelhető fájlok számára. (Ez általában 200 000 000 fájl egy 3 csomópontos fürthöz, 500 000 000 fájl egy hat csomópontos fürthöz, és így tovább.)

## <a name="use-the-msrsync-utility"></a>A msrsync segédprogram használata

Az ``msrsync`` eszköz segítségével áthelyezheti az adatátvitelt a avere-fürtön lévő háttérbeli alapszintű filerbe. Ez az eszköz úgy lett kialakítva, hogy több párhuzamos folyamat futtatásával optimalizálja a sávszélesség-használatot ``rsync`` . A GitHubon érhető el <https://github.com/jbd/msrsync> .

``msrsync`` elkülöníti a forrás könyvtárat külön "gyűjtő" értékre, majd minden gyűjtőn futtatja az egyes ``rsync`` folyamatokat.

A négy Magos virtuális géppel végzett előzetes tesztelés az 64-es folyamatok használatakor a legjobb hatékonyságot mutatja. A következő ``msrsync`` beállítással ``-p`` állíthatja be a folyamatok számát 64-re.

Használhatja az ``--inplace`` argumentumot is a ``msrsync`` parancsokkal. Ha ezt a beállítást használja, érdemes lehet egy második parancsot futtatni (mint a fent ismertetett [rsync](#use-a-two-phase-rsync-process)esetében) az adatok integritásának biztosításához.

``msrsync`` csak a helyi kötetek és a között lehet írni. A forrásnak és a célhelynek elérhetőnek kell lennie helyi csatlakoztatásként a fürt virtuális hálózatában.

Az Azure-beli ``msrsync`` Felhőbeli kötetek avere-fürttel való feltöltéséhez kövesse az alábbi utasításokat:

1. Telepítés ``msrsync`` és előfeltételei (rsync és Python 2,6 vagy újabb)
1. A másolandó fájlok és könyvtárak teljes számának meghatározása.

   Használja például a avere segédprogramot ``prime.py`` argumentumokkal ```prime.py --directory /path/to/some/directory``` (az URL-cím letöltésével érhető el <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Ha nem használja ``prime.py`` , a következő módon számíthatja ki az elemek számát a GNU ``find`` eszközzel:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Az elemek számának felosztása a 64 alapján az elemek számának megállapítása folyamatban. Ezt a számot használhatja a ``-f`` gyűjtők méretének beállításához a parancs futtatásakor.

1. Adja ki a ``msrsync`` parancsot a fájlok másolásához:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ha használja ``--inplace`` , adjon hozzá egy második végrehajtást anélkül, hogy ellenőriznie kellene, hogy az Adatmásolás helyesen történt-e:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ez a parancs például úgy lett kialakítva, hogy 11 000-es fájlokat helyezzen át a 64 folyamatokban a/test/Source-repository-ről a/mnt/vfxt/repository-re:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>A párhuzamos másolási parancsfájl használata

A ``parallelcp`` szkript is hasznos lehet az vFXT-fürt háttér-tárolóba való áthelyezéséhez.

Az alábbi szkript hozzáadja a végrehajtható fájlt `parallelcp` . (Ezt a szkriptet Ubuntu-re tervezték, ha más disztribúciót használ, külön kell telepítenie ``parallel`` .)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Példa párhuzamos másolásra

Ez a példa a párhuzamos másolási parancsfájl használatával fordítja ``glibc`` le a forrásfájlokat a avere-fürtből.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

A forrásfájlok a avere-fürt csatlakoztatási pontján tárolódnak, és az objektum fájljai a helyi merevlemezen vannak tárolva.

Ez a szkript a fenti párhuzamos másolási parancsfájlt használja. A kapcsolót a ``-j`` ``parallelcp`` és a ``make`` párhuzamos megszerzéséhez használja a rendszer.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
