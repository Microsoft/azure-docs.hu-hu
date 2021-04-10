---
title: Azure-Blobok biztonsági mentésének támogatási mátrixa
description: A támogatási beállítások és korlátozások összefoglalása az Azure-Blobok biztonsági mentésekor (előzetes verzió)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561899"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Azure-Blobok biztonsági mentésének támogatási mátrixa (előzetes verzió)

Ez a cikk összefoglalja a helyi rendelkezésre állást, a támogatott forgatókönyveket és a Blobok működési biztonsági mentésének korlátozásait.

## <a name="supported-regions"></a>Támogatott régiók

A Blobok működési biztonsági mentése jelenleg a következő régiókban érhető el: Ausztrália középső, Kelet-Ausztrália, Dél-Brazília, Közép-India, Közép-USA, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Középnyugat-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Észak-Európa, az USA déli középső régiója, Észak-Svájc Kelet-Ázsia Dél-Európa , USA nyugati régiója, USA 2. nyugati régiója

## <a name="limitations"></a>Korlátozások

A Blobok működési biztonsági mentése a blob-alapú visszaállítást, a blob verziószámozását, a Blobok helyreállított törlését, a Blobok adatcsatornájának módosítását és a zárolás törlését használja a helyi biztonsági mentési megoldás biztosításához. Az ezekre a képességekre vonatkozó korlátozások az operatív biztonsági mentésre is érvényesek.

**Támogatott forgatókönyvek:** Az operatív biztonsági mentés csak a standard általános célú v2 Storage-fiókok esetében támogatja a blokkos blobokat. Így ADLS Gen2 fiókok nem támogatottak. Emellett a Storage-fiókban található Blobok, hozzáfűzési blobok és prémium Blobok nem állíthatók vissza, és csak a Blobok blokkolása lesz visszaállítva.

**Egyéb korlátozások:**

- Ha törölt egy tárolót a megőrzési időszak alatt, a tároló nem állítható vissza az időponthoz tartozó visszaállítási művelettel. Ha olyan Blobok egy tartományát kísérli meg visszaállítani, amelyek blobokat tartalmaznak egy törölt tárolóban, az időponthoz tartozó visszaállítási művelet sikertelen lesz. További információ a tárolók törlésből való védelméről: [tárolók törlésének törlése (előzetes verzió)](../storage/blobs/soft-delete-container-overview.md).
- Ha egy blob a jelen pillanatban és a visszaállítási pont közötti időszakban a gyors és a lassú elérési szint között mozgott, a blob vissza lesz állítva az előző szintjére. A blokk-Blobok archiválási szinten való visszaállítása nem támogatott. Ha például a forró rétegben lévő blobot két nappal ezelőtt áthelyezték az archiválási rétegbe, és a visszaállítási művelet három nappal ezelőtt visszaállítja a-t, a blob nem állítható vissza a gyors szintre. Archivált blob visszaállításához először helyezze át az archiválási szintről. További információkért lásd: [Blobok adatainak rehidratálása az archív szintről](../storage/blobs/storage-blob-rehydration.md).
- Olyan blokk, amely egy [put blokkon](/rest/api/storageservices/put-block) keresztül lett feltöltve, vagy az [URL-címről](/rest/api/storageservices/put-block-from-url), de nem a [put blokkon](/rest/api/storageservices/put-block-list)keresztül véglegesítve lett elvégezve, nem egy blob része, ezért nem állítható vissza visszaállítási művelet részeként.
- Az aktív bérlettel rendelkező Blobok nem állíthatók vissza. Ha egy aktív bérlettel rendelkező blob szerepel a visszaállítani kívánt Blobok tartományában, a visszaállítási művelet automatikusan meghiúsul. A visszaállítási művelet megkezdése előtt szüntesse meg az aktív címbérletek megadását.
- A pillanatképek létrehozása vagy törlése a visszaállítási művelet részeként nem történt meg. A rendszer csak az alap blobot állítja vissza az előző állapotába.

## <a name="next-steps"></a>Következő lépések

- [Azure-Blobok operatív biztonsági mentésének áttekintése (előzetes verzió)](blob-backup-overview.md)