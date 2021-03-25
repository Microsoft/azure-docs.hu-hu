---
title: A foglalási kedvezmények Azure Storage szolgáltatásokra való alkalmazásának ismertetése | Microsoft Docs
description: Ismerje meg, hogy az Azure Blob Storage-ra, a Azure Filesra és a Azure Data Lake Storage Gen2 erőforrásokra milyen fenntartott kapacitási kedvezmények vonatkoznak.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024030"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>A foglalási kedvezmények Azure Storage-szolgáltatásokra való alkalmazásának ismertetése 
Az Azure Storage-szolgáltatások lehetővé teszik, hogy a kapacitás megtakarításával pénzt takarítson meg a tárolási költségekkel kapcsolatban. Az Azure Blob Storage, a Azure Files és a Azure Data Lake Storage 2. generációs tároló támogatja a tartalék példányokat. A fenntartott kapacitás megvásárlása után a foglalási kedvezmény automatikusan érvényes a foglalás feltételeinek megfelelő tárolási erőforrásokra. A foglalási kedvezmény csak a tárolási kapacitásra vonatkozik. A sávszélességért és a kérelmekért használatalapú díjat kell fizetnie.

További információ az Azure Blob Storage-ról és Azure Data Lake a 2. generációs fenntartott kapacitásról: a [blob Storage költségeinek optimalizálása a fenntartott kapacitással](../../storage/blobs/storage-blob-reserved-capacity.md). A Azure Files fenntartott kapacitással kapcsolatos további információkért tekintse [meg a fenntartott kapacitású Azure Files költségeinek optimalizálása](../../storage/files/files-reserve-capacity.md)című témakört.

További információ az Azure Blob Storage foglalási díjszabásáról: a [Blobok díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/) és a [2. generációs Azure Data Lake Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/data-lake/). További információ a Azure Files Storage foglalási díjszabásáról: [Azure Files díjszabása](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazásának menete
A fenntartott kapacitás kedvezményét óránként, a támogatott tárolási erőforrásokra alkalmazza a rendszer.

A fenntartott kapacitási kedvezmény a "use-this-Lose-it" kedvezmény. Ha nem rendelkezik blokk-Blobokkal, Azure-fájlmegosztás vagy Azure Data Lake Storage Gen2 olyan erőforrásokkal, amelyek megfelelnek a foglalás feltételeinek egy adott órában, akkor a foglalási mennyiség elvész az adott órában. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás törlésekor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-examples"></a>Példák a kedvezményre
Az alábbi példák bemutatják, hogyan vonatkozik a lefoglalt kapacitás kedvezménye a központi telepítéstől függően.

Tegyük fel, hogy az USA 2. nyugati régiójában megvásárolta a fenntartott kapacitás 100 TiB-t egy 1 éves időszakra. A foglalás a gyors elérésű rétegben a helyileg redundáns tárolás (LRS) blob Storage-tárolóhoz szükséges.

Tegyük fel, hogy a példa foglalás ára 18 540 dollár. Dönthet úgy, hogy a teljes összeget előre kifizeti, vagy fizetheti a következő tizenkét hónapban 1 545 dolláros fix havi részletekben is.

Tegyük fel, hogy feliratkozott egy havi foglalási fizetési konstrukcióra. A következő forgatókönyvek ismertetik, mi történik, ha a fenntartott kapacitás kihasználatlan vagy túlhasznált.

### <a name="underusing-your-capacity"></a>A kapacitás kihasználatlansága
Tegyük fel, hogy egy adott órában a foglalási időszakon belül csak 80 TiB-t használt a 100 TiB számára fenntartott kapacitással. A fennmaradó 20 TiB-t nem alkalmazza a rendszer az adott órára, és nem hajtja végre.

### <a name="overusing-your-capacity"></a>A kapacitás túlzott mértékű használata
Tegyük fel, hogy a foglalási időszakon belül egy adott órában a 101 TiB-ot használta. A foglalási kedvezmény az adatai 100 TiB-ra vonatkozik, a fennmaradó 1 TiB pedig az adott órára érvényes utólagos elszámolású díjszabás szerint történik. Ha a következő órában a használat a 100 TiB-ra változik, az összes használatot a foglalás szabályozza.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat
Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [A fenntartott kapacitású Blob Storage költségeinek optimalizálása](../../storage/blobs/storage-blob-reserved-capacity.md)
- [A fenntartott kapacitású Azure Files költségeinek optimalizálása](../../storage/files/files-reserve-capacity.md)
- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
