---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3c4ab8362b2a717a348a59c0baf829b61e1a8006
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808482"
---
![A D s v 3 specifikációit bemutató diagram.](media/vm-disk-performance/dsv3-documentation.jpg)

- A nem *gyorsítótárazott* lemez maximális átviteli sebessége a virtuális gép által kezelhető alapértelmezett tárolási korlát.
- A *gyorsítótárazott* tárterület maximális átviteli sebessége a gazdagép gyorsítótárazásának engedélyezésekor külön korlát.

A gazdagép gyorsítótárazása úgy működik, hogy a tárolót közelebb hozza a virtuális géphez, amely írható vagy olvasható. A virtuális gép számára a gazdagép gyorsítótárazásához elérhető tárterület a dokumentációban található. Láthatja például, hogy a Standard_D8s_v3 a gyorsítótár tárterületének 200 GiB.

A gazdagépek gyorsítótárazását engedélyezheti a virtuális gép létrehozásakor és a lemezek csatlakoztatásakor. Az állomás-gyorsítótárazást egy meglévő virtuális gépen is be-és kikapcsolhatja a lemezeken.

![A gazdagép gyorsítótárazását bemutató képernyőkép.](media/vm-disk-performance/host-caching.jpg)

A gazdagép gyorsítótárazását beállíthatja úgy, hogy az megfeleljen az egyes lemezek munkaterhelési követelményeinek. Beállíthatja, hogy a gazdagép gyorsítótárazása a következő legyen:

- **Írásvédett**: csak olvasási műveleteket végző munkaterhelések esetén
- **Olvasás/írás**: az olvasási és írási műveletek egyenlegét elvégző munkaterhelések esetén

Ha a munkaterhelés nem követi ezeket a mintákat, nem javasoljuk, hogy az állomás-gyorsítótárazást használja.

Futtassunk néhány példát a gazdagép-gyorsítótár különböző beállításaira, hogy meglássuk, hogyan befolyásolja az adatfolyamot és a teljesítményt. Ebben az első példában megvizsgáljuk, hogy mi történik az IO-kérelmekkel, ha a gazdagép gyorsítótárazási beállítása **csak olvasható** értékre van beállítva.

**Telepítő**

- Standard_D8s_v3
  - Gyorsítótárazott IOPS: 16 000
  - Nem gyorsítótárazott IOPS: 12 800
- P30 adatlemez
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **csak olvasható**

Ha egy olvasás történik, és a kívánt adatok elérhetők a gyorsítótárban, a gyorsítótár visszaadja a kért adattípust. Nem kell beolvasni a lemezről. Ez az olvasási érték a virtuális gép gyorsítótárazott korlátainak számít.

![Az olvasási gazdagép gyorsítótárazásának olvasási találatát bemutató ábra.](media/vm-disk-performance/host-caching-read-hit.jpg)

Ha egy olvasás történik, és a kívánt információk *nem* érhetők el a gyorsítótárban, a rendszer továbbítja az olvasási kérelmet a lemezre. Ezután a lemez felfedi a gyorsítótárat és a virtuális gépet is. Ez az olvasás a virtuális gép nem gyorsítótárazott korlátjának és a virtuális gép gyorsítótárazott korlátjának a része.

![Az olvasási gazdagép gyorsítótárazásának olvasási lemaradó ábráját bemutató ábra.](media/vm-disk-performance/host-caching-read-miss.jpg)

Írás elvégzése esetén az írást a gyorsítótárba és a lemezre is meg kell írni, mielőtt a rendszer befejeződik. Ez az írás a virtuális gép gyorsítótár nélküli korlátjának és a virtuális gép gyorsítótárazott korlátjának a része.

![Az olvasási gazdagép gyorsítótárazását ábrázoló diagram.](media/vm-disk-performance/host-caching-write.jpg)

Ezután nézzük meg, mi történik az IO-kérelmekkel, ha a gazdagép-gyorsítótár beállítása **írási/olvasási** értékre van állítva.

**Telepítő**

- Standard_D8s_v3
  - Gyorsítótárazott IOPS: 16 000
  - Nem gyorsítótárazott IOPS: 12 800
- P30 adatlemez
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **olvasás/írás**

Az olvasás ugyanúgy történik, mint a csak olvasható. Az írás az egyetlen dolog, ami eltér az olvasási/írási gyorsítótárazástól. Ha a gazdagép-gyorsítótárazással való írást írási **/olvasási** értékre állítja, akkor a rendszer csak az írást írja be a gazdagép-gyorsítótárba, hogy az megfelelően legyen végrehajtva. Az írás ezután a háttérben a lemezre kerül. Ez azt jelenti, hogy a gyorsítótárba való íráskor a rendszer az írást a gyorsítótárazott IO irányába veszi számításba. Amikor a rendszer lustul írt a lemezre, a nem gyorsítótárazott IO irányába számít.

![Az írási/olvasási gazdagépek gyorsítótárazását ábrázoló diagram.](media/vm-disk-performance/host-caching-read-write.jpg)

Folytassa a Standard_D8s_v3 virtuális géppel. Ebben az időszakban a gazdagépek gyorsítótárazását engedélyezzük a lemezeken. Emellett a virtuális gép IOPS korlátja 16 000 IOPS. A virtuális géphez csatolva három mögöttes P30-lemez van, amelyek mindegyike képes a 5 000 IOPS kezelésére.

**Telepítő**

- Standard_D8s_v3
  - Gyorsítótárazott IOPS: 16 000
  - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **olvasás/írás**
- Két P30 adatlemez × 2
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **olvasás/írás**

![A gazdagép gyorsítótárazási példáját bemutató ábra.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Az alkalmazás egy Standard_D8s_v3 virtuális gépet használ, amelyen engedélyezve van a gyorsítótárazás. 15 000 IOPS kérelmet tesz elérhetővé. A kérések 5 000 IOPS vannak lebontva minden mögöttes lemezhez csatlakoztatva. Nem történik teljesítmény-korlátozó művelet.

## <a name="combined-uncached-and-cached-limits"></a>Kombinált gyorsítótár nélküli és gyorsítótárazott korlátok

A virtuális gép gyorsítótárazott korlátai eltérnek a nem gyorsítótárazott korlátaitól. Ez azt jelenti, hogy engedélyezheti a gazdagépek gyorsítótárazását a virtuális géphez csatlakoztatott lemezeken, ha nem engedélyezi az állomások gyorsítótárazását más lemezeken. Ez a konfiguráció lehetővé teszi, hogy a virtuális gépek teljes tárterületet kapjanak a gyorsítótárazott korláttal és a nem gyorsítótárazott korláttal együtt.

Lássunk egy példát, amely segít megérteni, hogy ezek a korlátok hogyan működnek együtt. Folytatjuk a Standard_D8s_v3 virtuális gép és a prémium szintű lemezek csatolt konfigurációját.

**Telepítő**

- Standard_D8s_v3
  - Gyorsítótárazott IOPS: 16 000
  - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **olvasás/írás**
- Két P30 adatlemez × 2
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **olvasás/írás**
- Két P30 adatlemez × 2
  - IOPS: 5 000
  - Gazdagép gyorsítótárazása: **Letiltva**

![Egy gazdagép-gyorsítótárazási példát mutató diagram, amely távoli tárterülettel rendelkezik.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Ebben az esetben a Standard_D8s_v3 virtuális gépen futó alkalmazás 25 000 IOPS kérelmet tesz elérhetővé. A kérés az egyes csatlakoztatott lemezek 5 000-IOPS van lebontva. Három lemez használ gazdagép-gyorsítótárazást, és két lemez nem használ gazdagép-gyorsítótárazást.

- Mivel a gazdagép-gyorsítótárazást használó három lemez a gyorsítótárazott 16 000-es korláton belül van, a kérelmek sikeresen befejeződtek. Nem történik meg a tárolási teljesítményre vonatkozó korlát.
- Mivel az állomás-gyorsítótárazást nem használó két lemez a 12 800-as gyorsítótáras korláton belül van, ezek a kérések is sikeresen befejeződtek. Nincs korlát.

## <a name="disk-performance-metrics"></a>Lemez teljesítményének mérőszámai

Az Azure-ban olyan mérőszámok találhatók, amelyek betekintést nyújtanak a virtuális gépek és lemezek teljesítményére. Ezek a metrikák a Azure Portalon keresztül tekinthetők meg. Egy API-hívással is lekérhető. A metrikák kiszámítása egyperces időközökkel történik. A következő mérőszámok érhetők el a virtuális gép és a lemez i/o-forgalmának beolvasásához, valamint az átviteli teljesítményről is:

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

## <a name="storage-io-utilization-metrics-example"></a>Storage IO kihasználtsági mérőszámok – példa

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
