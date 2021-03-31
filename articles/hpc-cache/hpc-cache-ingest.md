---
title: Az Azure HPC cache Cloud Container-tárolóba való áthelyezése
description: Az Azure Blob Storage feltöltése az Azure HPC cache használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87072771"
---
# <a name="move-data-to-azure-blob-storage"></a>Az Azure Blob Storage-ba irányuló adatáthelyezés

Ha a munkafolyamat magában foglalja az Azure Blob Storage-ba való áthelyezést, győződjön meg arról, hogy hatékony stratégiát használ. Egy új blob-tárolóban megadhatja az adatait, mielőtt azt tárolási célként definiálja, vagy hozzáadja a tárolót, majd az Azure HPC cache használatával másolja az adatait.

Ez a cikk azt ismerteti, hogyan helyezhetők át a blob Storage-ba az Azure HPC cache szolgáltatással.

Tartsa szem előtt ezeket a tényeket:

* Az Azure HPC cache egy speciális tárolási formátumot használ a blob Storage-ban tárolt adatrendszerezéshez. Ezért a blob Storage-tárolónak egy új, üres tárolónak vagy egy korábban az Azure HPC gyorsítótár-adateléréséhez használt BLOB-tárolónak kell lennie.

* Ha több ügyfelet és párhuzamos műveletet használ, az adatok másolása az Azure HPC-gyorsítótárból a háttérbeli tárolási célra hatékonyabb. Az egyik ügyfél egyszerű másolási parancsa lassan áthelyezi az adatátvitelt.

Egy Python-alapú segédprogram használható a tartalom blob Storage-tárolóba való betöltésére. További információért olvassa el [a blob Storage-beli előzetes betöltését](#pre-load-data-in-blob-storage-with-clfsload) ismertető témakört.

Ha nem kívánja használni a betöltési segédprogramot, vagy ha tartalmat szeretne hozzáadni egy meglévő tárolási célhoz, kövesse a párhuzamos adatokat az [adatokat az Azure HPC-gyorsítótárból történő másolásával](#copy-data-through-the-azure-hpc-cache)kapcsolatos Tippekben.

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>A blob Storage-ban tárolt előzetes betöltés a CLFSLoad-mel

A avere CLFSLoad segédprogrammal az új blob Storage-tárolóba másolhatja az Adatmásolást, mielőtt hozzáadja azt a tároló céljához. Ez a segédprogram egyetlen Linux rendszeren fut, és az Azure HPC cache számára szükséges saját formátumban írja az adatot. A CLFSLoad a leghatékonyabb módszer a blob Storage-tárolók feltöltésére a gyorsítótárral való használatra.

Az avere CLFSLoad segédprogram az Azure HPC cache csapatának kérésére érhető el. Kérje meg a csapat kapcsolattartóját, vagy nyisson meg egy [támogatási jegyet](hpc-cache-support-ticket.md) a segítség kéréséhez.

Ez a beállítás csak az új, üres tárolókkal működik. Hozza létre a tárolót a avere CLFSLoad használata előtt.

A részletes információkat a avere CLFSLoad-eloszlás tartalmazza, amely az Azure HPC cache csapatának kérésére érhető el.

A folyamat általános áttekintése:

1. Készítse elő a linuxos rendszert (VM vagy fizikai) a Python 3,6-es vagy újabb verziójával. A Python 3,7 ajánlott a jobb teljesítmény érdekében.
1. Telepítse a Avere-CLFSLoad szoftvert a Linux rendszerre.
1. Hajtsa végre az átvitelt a Linux parancssorból.

A avere CLFSLoad segédprogramnak a következő információkra van szüksége:

* A blob Storage-tárolót tartalmazó Storage-fiók azonosítója
* Az üres blob Storage-tároló neve
* Közös hozzáférésű aláírási (SAS) token, amely lehetővé teszi a segédprogram számára a tárolóba való írást
* Az adatforrás helyi elérési útja – vagy egy helyi könyvtár, amely a másolandó adatmásolási vagy helyi elérési utat tartalmazza egy csatlakoztatott távoli rendszer számára az adattal

## <a name="copy-data-through-the-azure-hpc-cache"></a>Adatmásolás az Azure HPC cache használatával

Ha nem szeretné a avere CLFSLoad segédprogramot használni, vagy ha nagy mennyiségű adatmennyiséget szeretne hozzáadni egy meglévő blob Storage-tárolóhoz, akkor a gyorsítótárba másolhatja. Az Azure HPC cache több ügyfél egyidejű kiszolgálására lett kialakítva, így az adatok gyorsítótáron keresztül történő másolásához több ügyfélről is párhuzamos írásokat kell használnia.

![Több ügyfélből álló, többszálas adatáthelyezést ábrázoló diagram: a bal felső sarokban a helyszíni hardveres tárterület ikonja több nyílból származik. A nyilak négy ügyfélszámítógépre mutatnak. Az egyes ügyfélgépekről három nyíl mutat az Azure HPC cache felé. Az Azure HPC-gyorsítótárból több nyíl mutat a blob Storage-hoz.](media/hpc-cache-parallel-ingest.png)

Azok a ``cp`` vagy ``copy`` parancsok, amelyek használatával az adatok átvitele az egyik tárolási rendszerről a másikra történik, egyetlen szálon futó folyamatok, amelyek egyszerre csak egy fájlt másolnak. Ez azt jelenti, hogy a fájlkiszolgáló egyszerre csak egy fájlt tölt be – ez a gyorsítótár erőforrásainak hulladéka.

Ez a szakasz a több ügyfelet tartalmazó, többszálas fájlmásolási rendszer létrehozására szolgáló stratégiákat ismerteti az adatok blob Storage-ba való áthelyezéséhez az Azure HPC cache használatával. Ismerteti a fájlátviteli fogalmakat és a döntési pontokat, amelyek segítségével több ügyfél és egyszerű másolási parancs használatával hatékony Adatmásolást lehet használni.

Emellett ismerteti azokat a segédprogramokat is, amelyek segíthetnek. A ``msrsync`` segédprogrammal részben automatizálható az adatkészlet gyűjtővé való osztása és az rsync-parancsok használata. A ``parallelcp`` parancsfájl egy másik segédprogram, amely beolvassa a forrás könyvtárat, és automatikusan kiadja a másolási parancsokat.

### <a name="strategic-planning"></a>Stratégiai tervezés

Amikor párhuzamosan másolja az adatmásolási stratégiát, ismernie kell a fájlok mérete, a fájlok száma és a könyvtár mélysége közötti kompromisszumokat.

* Ha a fájlok kicsik, a kamat a fájlok másodpercenkénti száma.
* Ha a fájlok nagy méretűek (10MiBi vagy nagyobbak), a kamat mérőszáma másodpercenként bájt.

Minden másolási folyamathoz tartozik egy átviteli sebesség és egy fájl – átvitt sebesség, amely a másolási parancs hosszának időzítésével és a fájlméret és a fájlok számának megadásával mérhető. A díjszabás mértékének megállapítása kívül esik a jelen dokumentum hatókörén, de fontos tisztában lennie azzal, hogy a kis-és nagyméretű fájlokat is érdemes-e kezelni.

Az Azure HPC cache-vel való párhuzamos adatfeldolgozási stratégiák a következők:

* Manuális másolás – manuálisan is létrehozhat többszálas másolatot egy ügyfélen, ha több másolási parancsot futtat egyszerre a háttérben a fájlok vagy elérési utak előre definiált készletei között. A részletekért olvassa el az [Azure HPC cache-adatok betöltése – manuális másolási módszert](hpc-cache-ingest-manual.md) .

* A részlegesen automatizált másolás ``msrsync``  -  ``msrsync`` egy olyan burkoló segédprogram, amely több párhuzamos ``rsync`` folyamatot futtat. Részletekért olvassa el az [Azure HPC cache-adatok betöltése – msrsync metódust](hpc-cache-ingest-msrsync.md).

* Parancsfájlba foglalt másolás ``parallelcp`` – megtudhatja, hogyan hozhat létre és futtathat párhuzamos másolási parancsfájlokat az [Azure HPC cache-adatokban – párhuzamos másolási parancsfájl-metódussal](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Következő lépések

A tárterület beállítása után megismerheti, hogyan csatlakoztathatók az ügyfelek a gyorsítótárhoz.

* [Hozzáférés az Azure HPC cache Systemhez](hpc-cache-mount.md)
