---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094628"
---
## <a name="warm-up-the-cache"></a>A gyorsítótár bemelegítése

A írásvédett gazdagép gyorsítótárazásával rendelkező lemez magasabb IOPS tud adni, mint a lemez korlátja. A maximális olvasási teljesítmény eléréséhez a gazdagép-gyorsítótárból először be kell melegíteni a lemez gyorsítótárát. Ezzel biztosíthatja, hogy a teljesítményértékelési eszköz által a CacheReads-köteten lévő olvasási IOs legyen, valójában a gyorsítótárba kerül, nem pedig közvetlenül a lemezre. A gyorsítótárbeli találatok több IOPS eredményeznek az egyetlen gyorsítótár-kompatibilis lemezről.

> [!IMPORTANT]
> Minden alkalommal, amikor a virtuális gép újraindul, a teljesítménytesztek futtatása előtt be kell melegíteni a gyorsítótárat.

## <a name="diskspd"></a>DISKSPD

[Töltse le a DISKSP eszközt](https://github.com/Microsoft/diskspd) a virtuális gépen. A DISKSPD egy olyan eszköz, amelyet testreszabhat saját szintetikus számítási feladatainak létrehozásához. A fentiekben ismertetett telepítést a teljesítményteszt-tesztek futtatására fogjuk használni. A különböző számítási feladatok teszteléséhez módosíthatja a specifikációkat.

Ebben a példában a következő alapkonfiguráció-paramétereket használjuk:

- -c200G: létrehozza (vagy újból létrehozza) a tesztben használt mintát. A készlet mérete bájt, KiB, MiB, GiB vagy blokkok lehet. Ebben az esetben a memória gyorsítótárazásának minimalizálásához a 200 – GiB célfájl nagy fájlját használja a rendszer.
- -W100: az írási kérelmekben szereplő műveletek százalékos arányát határozza meg (a-W0 értéke 100% Read).
- -b4K: a blokk méretét adja meg bájtban, KiB-ban, MiB-ben vagy GiB-ban. Ebben az esetben a 4K-blokk mérete egy véletlenszerű I/O-teszt szimulálására szolgál.
- -F4: összesen négy szálat állít be.
- -r: a véletlenszerű I/O-tesztet jelzi (felülbírálja a-s paramétert).
- -o128: a függőben lévő I/O-kérések számát adja meg egy szál alapján. Ezt a várólista mélységét is nevezzük. Ebben az esetben a rendszer az 128-et használja a processzor kiterhelésére.
- -W7200: megadja a bemelegítő idő időtartamát a mérések kezdete előtt.
- -D30: a teszt időtartamát határozza meg, amely nem tartalmazza a bemelegítőt.
- -Sh: letilthatja a szoftver-és hardveres írási gyorsítótárazást (a-SUW értékkel egyenértékű).

A paraméterek teljes listájáért tekintse meg a [GitHub-tárházat](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Maximális írási IOPS
A 128-es magas várólista-mélységet, a 8 KB-os kis blokkot, valamint négy munkaszálat használ az írási műveletek végrehajtásához. Az írási feldolgozók a "NoCacheWrites" kötetre irányítják a forgalmat, amelyhez három lemez van beállítva a "None" értékre.

Futtassa a következő parancsot 30 másodpercig a bemelegítő és a 30 másodperces mérés után:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Az eredmények azt mutatják, hogy a Standard_D8ds_v4 virtuális gép a maximális írási IOPS korlátja 12 800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Összesen 3208642560 bájt, a 391680-es teljes I/O-k maximális száma összesen 101,97 MiB/s értékkel, valamint összesen 13052,65 I/O-t.":::

### <a name="maximum-read-iops"></a>Olvasási IOPS maximális száma

A magas üzenetsor-mélység 128, a kis blokk mérete négy KB, valamint négy munkavégző szál az olvasási műveletekhez. Az olvasási feldolgozók a "CacheReads" köteten vezetik a forgalmat, amely egy olyan lemezzel rendelkezik, amelynek a gyorsítótára "ReadOnly" értékre van állítva.

Futtassa a következő parancsot két órányi bemelegítéssel és 30 másodperces méréssel:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Az eredmények azt mutatják, hogy a Standard_D8ds_v4 virtuális gép elérte a maximális olvasási IOPS-korlátot (77 000).

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="9652785152 2356637 bájtos teljes I/o-érték, 306,72-as teljes MiB/mp, valamint összesen 78521,23 I/o-érték.":::

### <a name="maximum-throughput"></a>Maximális átviteli sebesség

A maximális olvasási és írási sebesség eléréséhez a 64 KB-os nagyobb blokk méretre válthat.
## <a name="fio"></a>FIO

A FIO egy népszerű eszköz, amely a Linux rendszerű virtuális gépeken használható. Rugalmasan választhatja ki a különböző IO-méreteket, a szekvenciális vagy véletlenszerű olvasási és írási műveleteket. Feldolgozói szálakat vagy folyamatokat indít el a megadott I/O-műveletek végrehajtásához. Megadhatja az egyes munkaszálakhoz tartozó I/O-műveletek típusát. Egy, az alábbi példákban bemutatott feladattípust hoztunk létre. A feladathoz tartozó fájlok specifikációi a Premium Storageon futó különböző számítási feladatok teljesítménytesztéhez módosíthatók. A példákban egy **Ubuntut** futtató Standard_D8ds_v4 használunk. Használja ugyanazt a telepítőt, amely a teljesítményteszt szakasz elején szerepel, és a teljesítményteszt-tesztek futtatása előtt a gyorsítótárat is bemelegíti.

Mielőtt elkezdené, [töltse le a Fio](https://github.com/axboe/fio) , és telepítse a virtuális gépre.

Futtassa az alábbi parancsot az Ubuntuhoz,

```
apt-get install fio
```

Négy munkaszálat használunk az írási műveletekhez és négy munkaszálhoz a lemezek olvasási műveleteinek elvégzéséhez. Az írási feldolgozók a "nincs" értékre beállított "nincs" gyorsítótár-köteten lévő, "nincs" állapotú lemezeket vezetnek. Az olvasási feldolgozók a "readcache" köteten vezetik a forgalmat, amely egy olyan lemezzel rendelkezik, amelynek a gyorsítótára "ReadOnly" értékre van állítva.

### <a name="maximum-write-iops"></a>Maximális írási IOPS

Hozza létre a fájlt a következő specifikációkkal a maximális írási IOPS lekéréséhez. Nevezze el "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Vegye figyelembe, hogy kövesse az előző szakaszokban tárgyalt tervezési irányelvekkel összhangban lévő legfontosabb dolgokat. Ezek a specifikációk elengedhetetlenek a maximális IOPS meghajtásához,  

* 256 magas üzenetsor-mélysége.  
* A kis blokk mérete 4 KB.  
* Több szál véletlenszerű írásokat végez.

Futtassa a következő parancsot a FIO-teszt 30 másodpercig való kikapcsolásához.  

```
sudo fio --runtime 30 fiowrite.ini
```

A teszt futtatása közben megtekintheti a virtuális gép és a prémium szintű lemezek írási IOPS számát. Ahogy az alábbi példában is látható, a Standard_D8ds_v4 virtuális gép a maximális írási IOPS korlátját (12 800 IOPS) kézbesíti.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Az írási IOPS virtuális gép és a prémium SSD-k száma":::

### <a name="maximum-read-iops"></a>Olvasási IOPS maximális száma

Hozza létre a fájlt a következő specifikációkkal a maximális olvasási IOPS lekéréséhez. Nevezze el "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Vegye figyelembe, hogy kövesse az előző szakaszokban tárgyalt tervezési irányelvekkel összhangban lévő legfontosabb dolgokat. Ezek a specifikációk elengedhetetlenek a maximális IOPS meghajtásához,

* 256 magas üzenetsor-mélysége.  
* A kis blokk mérete 4 KB.  
* Több szál véletlenszerű írásokat végez.

Futtassa a következő parancsot a FIO-teszt 30 másodpercig való kikapcsolásához.

```
sudo fio --runtime 30 fioread.ini
```

A teszt futtatása közben megtekintheti a virtuális gép és a prémium szintű lemezek olvasási IOPS számát. Ahogy az alábbi példában is látható, a Standard_D8ds_v4 virtuális gép több mint 77 000 olvasási IOPS. Ez a lemez és a gyorsítótár teljesítményének kombinációja.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Képernyőfelvétel: az írási IOPS virtuális gép és a prémium SSD-k száma, ami azt mutatja, hogy az olvasások 78.6.":::

### <a name="maximum-read-and-write-iops"></a>Olvasási és írási IOPS maximális száma

Hozza létre a fájlt a következő specifikációkkal a kombinált olvasási és írási IOPS maximális számának lekéréséhez. Nevezze el "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Vegye figyelembe, hogy kövesse az előző szakaszokban tárgyalt tervezési irányelvekkel összhangban lévő legfontosabb dolgokat. Ezek a specifikációk elengedhetetlenek a maximális IOPS meghajtásához,

* 128 magas üzenetsor-mélysége.  
* A kis blokk mérete 4 KB.  
* Több szál véletlenszerű olvasási és írási műveleteket végez.

Futtassa a következő parancsot a FIO-teszt 30 másodpercig való kikapcsolásához.

```
sudo fio --runtime 30 fioreadwrite.ini
```

A teszt futtatása közben megtekintheti a virtuális gép és a prémium szintű lemezek együttes olvasási és írási IOPS számát. Ahogy az alábbi példában is látható, a Standard_D8ds_v4 virtuális gép több mint 90 000 kombinált olvasási és írási IOPS-t biztosít. Ez a lemez és a gyorsítótár teljesítményének kombinációja.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="A kombinált olvasási és írási IOPS azt mutatja, hogy az olvasások 78.3 k, és az írások: 12.6 k IOPS.":::

### <a name="maximum-combined-throughput"></a>Kombinált átviteli sebesség maximális száma

A kombinált olvasási és írási sebesség maximális mennyiségének lekéréséhez használjon nagyobb blokk-méretet és nagy várólista-mélységet több, olvasást és írást végző szál használatával. A blokk mérete 64 KB, a várólista mélysége pedig 128.