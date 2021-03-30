---
title: Microsoft Azure FXT Edge Filer – áttekintés
description: Leírja az Azure FXT Edge Filer Hybrid Storage cache-t, amely egy aktív archiválási és fájl-hozzáférési gyorsító megoldás a nagy teljesítményű számítástechnika számára
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218782"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Mi az Azure FXT Edge Filer Hybrid Storage cache?

Az Azure FXT Edge Filer egy hibrid tárolási gyorsítótárazási berendezés, amely gyors fájl-és aktív archívumot biztosít a nagy teljesítményű számítástechnikai (HPC) feladatokhoz.

Több adatforrással is működik, függetlenül attól, hogy a helyi adatközpontban, távolról vagy a felhőben tárolva van-e. Az Azure FXT Edge Filer egy egységes névteret biztosít a különböző tárolási rendszerekben tárolt adattároláshoz.

Három vagy több FXT Edge Filer-hardvereszköz együtt fürtözött fájlrendszerként működik a gyorsítótár biztosításához. A szükséges hardver megvásárlásával kapcsolatos részletekért forduljon a Microsoft képviselőjéhez.

További információért olvassa el az [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/)termékkel kapcsolatos információkat és adatlapot.

## <a name="use-cases"></a>Használati esetek

Az Azure FXT Edge Filer a következőhöz hasonló munkafolyamatok esetében növeli a termelékenységet:

* Olvasási – nagy mennyiségű fájl-hozzáférési munkafolyamat
* NFSv3 vagy SMB2 protokollok
* 1000 – 100 000 CPU-mag számítási farmok

### <a name="nas-optimization-and-scaling"></a>A NAS optimalizálása és skálázása

Az Azure FXT Edge Filer cache-gyorsítótárral zökkenőmentes hozzáférést biztosíthat a meglévő NetApp-és Dell EMC Isilon NAS-rendszerekhez. Az Azure blobot vagy más felhőalapú tárhelyet is hozzáadhat a méretezhetőség biztosításához anélkül, hogy újra kellene dolgoznia az adatelérési folyamatokat az ügyfél oldalán.

### <a name="wan-caching"></a>WAN-gyorsítótárazás

Az Azure FXT Edge Filer lehetővé teheti a gyors fájlokhoz való hozzáférést a Kiemelt felhasználóktól, ha a szükséges adatok máshol vannak tárolva. Hozzáférés biztosítása a biztonsági mentés és az egyéb adatkezelési rendszerek központi adatközpontban való megőrzése mellett.

### <a name="active-archive-in-azure-blob"></a>Aktív Archívum az Azure-blobban

A hozzáférési pontként kiterjesztheti az adatközpontot felhőalapú tárhelyre az Azure FXT Edge Filer-sel.

## <a name="features"></a>Funkciók

Két hardveres modell érhető el.

| Modellezés | DRAM | NVMe SSD | Hálózati portok |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 GB/10 GB + 2 x 1 GB |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 GB/10 GB + 2 x 1 GB |

## <a name="next-steps"></a>Következő lépések

* Folytassa az Azure FXT Edge Filer megismerésével kapcsolatos tudnivalókat a [specifikációk](fxt-specs.md) és a [telepítési oktatóanyag](fxt-install.md)elolvasásával.
* Megtudhatja, hogyan vásárolhatja meg az Azure FXT Edge Filert az [Azure FXT Edge Filer termék lapján](https://azure.microsoft.com/services/fxt-edge-filer/).
