---
title: Lemez mérőszámai
description: Példák a lemezes feltört mérőszámokra
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8f5b7f8b5cd563f8b265d7051a57b0ac22598d1c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617378"
---
# <a name="disk-performance-metrics"></a>Lemez teljesítményének mérőszámai
Az Azure mérőszámokat biztosít a Azure Portalban, amelyek betekintést nyújtanak a virtuális gépek és a lemezek teljesítményének módjára. A metrikák egy API-hívás segítségével is beolvashatók. Ez a cikk 3 alszakaszra van bontva:

- **Lemez i/o-, átviteli és üzenetsor-mérési mérőszámai** – ezek a metrikák lehetővé teszik, hogy megtekintse a tárolási teljesítményt egy lemez és egy virtuális gép szempontjából.
- **Lemezes feltört mérőszámok** – ezek a mérőszámok a prémium szintű lemezeken megfigyelhető [kitörési](disk-bursting.md) funkciójuk.
- **Storage IO-kihasználtság metrikái** – ezek a metrikák segítenek diagnosztizálni a tárolási teljesítmény szűk keresztmetszetét a lemezekkel. 

Minden metrikát percenként bocsátunk ki, kivéve a kitört kreditek százalékos mérőszámát, amelyet 5 percenként bocsátunk ki.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Lemez i/o, átviteli sebesség és várólista mélységi mérőszámai
A következő mérőszámok érhetők el a virtuális gép és a lemez i/o-, átviteli sebesség-és üzenetsor-mélységi teljesítményének megismeréséhez:

- **Operációsrendszer-lemez várólistájának mélysége**: az operációsrendszer-lemezre való beolvasásra vagy írásra váró aktuális kimaradó i/o-kérelmek száma.
- **Operációsrendszer-lemez olvasási sebessége (bájt/mp**): az operációsrendszer-lemezről másodpercenként olvasott bájtok száma.
- **Operációsrendszer-lemez olvasási műveletei/mp**: az operációsrendszer-lemezről egy másodpercben beolvasott bemeneti műveletek száma.
- **Operációsrendszer-lemez írási sebessége (bájt/mp**): az operációs rendszer lemezének másodpercében írt bájtok száma.
- **Operációsrendszer-lemez írási műveletei/mp**: az operációsrendszer-lemez másodpercében írt kimeneti műveletek száma.
- **Adatlemez-várólista mélysége**: az adatlemez (ek) re való beolvasásra vagy az azokba való írásra váró aktuális, függőben lévő i/o-kérelmek száma.
- **Adatlemez-olvasási sebesség (bájt/mp**): az adatlemez (ek) egy másodpercében olvasott bájtok száma.
- **Adatlemez olvasási műveletei/mp**: a másodpercenként beolvasott bemeneti műveletek száma az adatlemez (ek) ről.
- **Adatlemez-írási sebesség (bájt/mp**): az adatlemez (ek) másodpercében írt bájtok száma.
- **Adatlemez írási műveletei/mp**: az adatlemez (ek) másodpercében írt kimeneti műveletek száma.
- **Lemez olvasási sebessége (bájt/mp**): a virtuális géphez csatolt összes lemezről beolvasott bájtok teljes száma.
- **Lemez olvasási műveletei/mp**: a virtuális géphez csatolt összes lemezről beolvasott bemeneti műveletek másodpercenkénti száma.
- **Lemez írási sebessége (bájt/mp**): a virtuális géphez csatolt összes lemezről a másodpercben írt bájtok száma.
- **Lemez írási műveletei/mp**: a virtuális géphez csatolt összes lemezről másodpercek alatt írt kimeneti műveletek száma.

## <a name="bursting-metrics"></a>Feltört mérőszámok
A következő mérőszámok a prémium szintű lemezeken való betekintést segítik a bevezetési [funkcióban](disk-bursting.md) :

- **Adatlemez maximális burst sávszélessége**: az adatlemez (ek) adatforgalmának korlátja.
- **Operációsrendszer-lemez maximális burst sávszélessége**: az operációsrendszer-lemez által feltört átviteli sebesség korlátja.
- **Adatlemez maximális burst IOPS**: a IOPS korlátja, amelyet az adatlemez (ek) fel tud hatolni.
- **Operációsrendszer-lemez maximális burst IOPS**: a IOPS korlátja, amelyet az operációsrendszer-lemez képes feltörten.
- **Adatlemez céljának sávszélessége**: az adatlemez (ek) lemezének adatvesztés nélküli elérési korlátja.
- **Operációsrendszer-lemez célként megadott sávszélessége**: az operációsrendszer-lemez terhelés nélküli elérési sebessége.
- **Adatlemez céljának IOPS**: a IOPS korlátja, hogy az adatlemez (ek) a kitörés nélkül is elérhetők.
- **Operációsrendszer-lemez céljának IOPS**: a IOPS korlátja, hogy az adatlemez (ek) el tudnak érni a burst nélkül.
- Az **adatlemez feltörte a bps-kreditek százalékos arányát**: az adatlemez (ek) adatforgalmának feltört hányada. 5 perces időtartamon belül kibocsátva.
- Az **operációsrendszer-lemez burst bps-kreditek százalékos arányát használta**: az operációsrendszer-lemezhez felhasznált átviteli sebesség összesített százalékát. 5 perces időtartamon belül kibocsátva.
- A **felhasznált adatlemez burst IO-kreditek százalékos aránya**: az adatlemez (ek) esetében felhasznált IOPS burst százalékos aránya. 5 perces időtartamon belül kibocsátva.
- Az **operációsrendszer-lemez burst i/o-kreditek százalékos aránya**: az operációsrendszer-lemezhez felhasznált IOPS burst arányának összesített aránya. 5 perces időtartamon belül kibocsátva.

## <a name="storage-io-utilization-metrics"></a>Storage IO kihasználtsági metrikái
A következő metrikák segítenek diagnosztizálni a szűk keresztmetszetet a virtuális gépen és a lemez kombinációjában. Ezek a metrikák csak Premium-kompatibilis virtuális gépek használata esetén érhetők el. Ezek a metrikák az ultra kivételével minden lemez típushoz elérhetők. 

A lemez i/o-korlátjának diagnosztizálását segítő mérőszámok:

- **Adatlemez IOPS** kihasználtsága (%): az ADATlemez IOPS kiszámított százalékos arány a kiépített adatlemez IOPS. Ha ez az érték 100%-os, az alkalmazás futása az adatlemez IOPS-korlátjának i/o-értéke.
- **Adatlemez sávszélességének** kihasználtsága (%): az adatlemez átviteli sebessége által kiszámított százalékos arány a kiépített adatlemez átviteli sebessége alapján. Ha ez az érték 100%-os, az alkalmazás futása az adatlemez sávszélesség-korlátján belül az i/o-érték.
- **Operációsrendszer-lemez IOPS felhasznált százaléka**: az operációsrendszer-lemez IOPS kiszámított százalék a kiépített operációsrendszer-lemez IOPS befejeződött. Ha ez az érték 100%-os, az alkalmazás futása az operációsrendszer-lemez IOPS korlátján belül az IO-ra van korlátozva.
- **Operációsrendszer-lemez sávszélességének** kihasználtsága (%): az operációsrendszer-lemez átviteli sebessége által kiszámított százalékos érték a kiépített operációsrendszer-lemez átviteli sebességén. Ha ez az érték 100%-os, az alkalmazás futása az operációsrendszer-lemez sávszélesség-korlátján kívül esik.

A virtuális gépek IO-korlátjának diagnosztizálását segítő mérőszámok:

- A virtuális **gép gyorsítótárazott IOPS** kihasználtsága (%): az összes IOPS által kiszámított százalék, amely a maximálisan gyorsítótárazott virtuális gép IOPS korlátján fejeződött be. Ha ez az érték 100%-os, az alkalmazás futása a virtuális gép gyorsítótárazott IOPS-korlátjának i/o-értéke.
- A virtuális **gép gyorsítótárazott sávszélességének** kihasználtsága (%): a teljes lemez átviteli sebessége alapján kiszámított százalékos érték a maximálisan gyorsítótárazott virtuálisgép-átviteli sebességnél. Ha ez az érték 100%-os, az alkalmazás futása a virtuális gép gyorsítótárazott sávszélesség-korlátjának i/o-határértéke.
- A virtuális gép nem **gyorsítótárazott IOPS** kihasználtsága (%): a virtuális gépen lévő összes IOPS által kiszámított százalék a nem gyorsítótárazott virtuális gép maximális IOPS-korlátja alapján lett végrehajtva. Ha ez az érték 100%-os, az alkalmazás futása a virtuális gép nem gyorsítótárazott IOPS korlátjának i/o-értéke.
- A virtuális gép nem **gyorsítótárazott sávszélességének** kihasználtsága (%): a virtuális gépen lévő teljes lemez átviteli sebessége által kiszámított százalék a virtuális gép maximálisan kiépített átviteli sebességével fejeződött be. Ha ez az érték 100%-os, az alkalmazás futása a virtuális gép nem gyorsítótárazott sávszélesség-korlátjának i/o-korlátja.

## <a name="storage-io-metrics-example"></a>Storage IO-mérőszámok – példa

Futtassunk egy példát arra, hogyan használhatja ezeket az új Storage i/o-kihasználtsági mérőszámokat, hogy segítsen a rendszerünk szűk keresztmetszetének hibakeresésében. A rendszer telepítése megegyezik az előző példával, kivéve, ha a csatlakoztatott operációsrendszer-lemez *nincs gyorsítótárazva* .

**Telepítő**

- Standard_D8s_v3
  - Gyorsítótárazott IOPS: 16 000
  - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **Letiltva**
- Két P30 adatlemez × 2
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **olvasás/írás**
- Két P30 adatlemez × 2
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **Letiltva**

Futtasson egy teljesítményteszt-tesztet ezen a virtuális gépen és az i/o-tevékenységet létrehozó lemez-kombináción. Ha szeretné megtudni, hogyan lehet az Azure-on alapuló adatforgalomra vonatkozó teljesítménytesztet megismerni, tekintse [meg az alkalmazás az Azure Disk Storage](disks-benchmarks.md) A benchmarking eszközből láthatja, hogy a virtuális gép és a lemez kombinációja elérheti a 22 800 IOPS:

![Képernyőkép az f i o kimenetről, amely az r = 22.8 k Kiemelt értékre mutat.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



A Standard_D8s_v3 összesen 28 600 IOPS érhető el. A metrikák használatával vizsgáljuk meg, mi történik, és azonosítjuk a Storage IO szűk keresztmetszetét. A bal oldali panelen válassza a **metrikák** elemet:

![Képernyőfelvétel a bal oldali ablaktáblán Kiemelt metrikák megjelenítéséhez.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Először vessünk egy pillantást a **virtuális gép gyorsítótárazott IOPS felhasznált százalékos** metrikára:

![Képernyőfelvétel: V M gyorsítótárazott I O P S felhasznált százalék.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Ez a mérőszám azt jelzi, hogy a virtuális gép gyorsítótárazott IOPS kiosztott 16 000 IOPS 61%-a használatban van. Ez a százalék azt jelenti, hogy a tároló i/o-szűk keresztmetszete nem a gyorsítótárazott lemezekkel van ellátva, mert nem 100%-on. Most nézzük meg, mi a **virtuális gép nem gyorsítótárazott IOPS felhasznált százalékos** mérőszáma:

![Képernyőfelvétel: a V M gyorsítótárba helyezett I O P-k százalékos aránya.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

A metrika értéke 100%. Azt mondja nekünk, hogy a virtuális gép nem gyorsítótárazott IOPS kiosztott összes 12 800-IOPS használatban van. A probléma megoldásának egyik módja, ha megváltoztatjuk a virtuális gép méretét egy nagyobb méretűre, amely képes kezelni a további IO-t. Mielőtt azonban megtesszük ezt, nézzük meg a csatlakoztatott lemezt, hogy megtudja, hány IOPS látnak. Az operációsrendszer-lemez ellenőrzéséhez tekintse meg az **operációsrendszer-lemez IOPS felhasznált százalékát**:

![Képernyőfelvétel: O S lemez – o P S felhasznált százalék.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Ez a metrika azt jelzi, hogy a P30 operációsrendszer-lemezre kiépített 5 000 IOPS körülbelül 90%-a használatban van. Ez a százalék azt jelenti, hogy az operációsrendszer-lemezen nincs szűk keresztmetszet. Most nézzük meg a virtuális géphez csatolt adatlemezeket, ha megtekinti az **adatlemez IOPS felhasznált százalékát**:

![Az adatlemez I O P S felhasznált százalékát ábrázoló képernyőkép.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Ez a metrika azt jelzi, hogy az összes csatlakoztatott lemez átlagos IOPS-értéke 42% körül van. A rendszer ezt a százalékarányt a lemezek által használt IOPS alapján számítja ki, és nem szolgálja ki a gazdagép-gyorsítótárból. Részletesebben is megvizsgáljuk ezt a metrikát a metrikák *felosztásának* alkalmazásával és a LUN értékének felosztásával:

![Képernyőfelvétel: az adatlemez I O P-S kihasználtságának százalékos aránya a felosztással.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Ez a metrika azt jelzi, hogy a 3. és 2. logikai egységben csatolt adatlemezek a kiépített IOPS körülbelül 85%-át használják. Az i/o-ábra a virtuális gép és a lemezek architektúrájának megjelenését mutatja be:

![Példa a Storage I O mérőszámokra.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Következő lépések

- [Azure Monitor mérőszámok áttekintése](../azure-monitor/platform/data-platform-metrics.md)
- [Metrikák összesítésének magyarázata](../azure-monitor/platform/metrics-aggregation-explained.md)
- [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral](../azure-monitor/platform/alerts-metric.md)
