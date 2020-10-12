---
title: Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa
description: Az Azure-fájlmegosztás biztonsági mentésekor a támogatási beállítások és korlátozások összegzését tartalmazza.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 6381170df93fdf52c2d0dc7059ad47bbff734025
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378032"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa

Az Azure-fájlmegosztás biztonsági mentését a [Azure Backup szolgáltatással](./backup-overview.md) végezheti el. Ez a cikk összefoglalja a támogatási beállításokat, amikor biztonsági mentést készít az Azure-fájlmegosztás Azure Backup.

## <a name="supported-regions"></a>Támogatott régiók

### <a name="ga-regions-for-azure-file-shares-backup"></a>Az Azure file shares biztonsági mentésének GA régiói

Az Azure-fájlmegosztás biztonsági mentése minden régióban elérhető, **kivéve** a következőt: Németország középső régiója (szuverén), Északkelet-Németország (szuverén), Kelet-Kína, Kelet-Kína 2, Észak-Kína, Észak-Kína 2, US gov Iowa

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

| Storage-fiók adatai | Támogatás                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Fiók típusa            | A Azure Backup támogatja az általános célú v1, általános célú v2 és file Storage típusú Storage-fiókokban lévő Azure-fájlmegosztás használatát |
| Teljesítmény              | A Azure Backup a standard és a Premium Storage fiókban egyaránt támogatja a fájlmegosztást |
| Replikáció              | A Storage-fiókokban bármely replikációs típussal rendelkező Azure-fájlmegosztás támogatott |
| Tűzfal engedélyezve         | Az Azure-fájlmegosztás olyan tűzfalszabályok esetében támogatott, amelyek lehetővé teszik Microsoft Azure szolgáltatások elérését a Storage-fiókhoz.|

## <a name="supported-file-shares"></a>Támogatott fájlmegosztás

| Fájlmegosztás típusa                                   | Támogatás   |
| -------------------------------------------------- | --------- |
| Standard                                           | Támogatott |
| Nagy                                              | Támogatott |
| Prémium                                            | Támogatott |
| Az Azure file Sync szolgáltatással összekapcsolt fájlmegosztás | Támogatott |

## <a name="protection-limits"></a>Védelmi korlátok

| Beállítás                                                      | Korlát |
| ------------------------------------------------------------ | ----- |
| A naponta védetté tehető fájlmegosztás maximális száma| 200   |
| A tár naponta regisztrálható tárolási fiókok maximális száma | 50    |
| A tár által védhető fájlmegosztás maximális száma | 2000   |
| A tár által regisztrálható tárolási fiókok maximális száma | 200   |

## <a name="backup-limits"></a>Biztonsági mentési korlátok

| Beállítás                                      | Korlát |
| -------------------------------------------- | ----- |
| Igény szerinti biztonsági másolatok maximális száma naponta | 10   |
| Ütemezett biztonsági mentések maximális száma naponta | 1     |

## <a name="restore-limits"></a>Visszaállítási korlátok

| Beállítás                                                      | Korlát   |
| ------------------------------------------------------------ | ------- |
| Napi visszaállítások maximális száma                           | 10      |
| Fájlok maximális száma visszaállításkor                         | 10      |
| A nagyméretű fájlmegosztás esetében a maximálisan ajánlott visszaállítási méret | 15 TiB |

## <a name="retention-limits"></a>Megőrzési korlátok

| Beállítás                                                      | Korlát    |
| ------------------------------------------------------------ | -------- |
| A fájlmegosztás összes helyreállítási pontjának maximális száma az adott időpontban | 200      |
| Igény szerinti biztonsági mentés által létrehozott helyreállítási pont maximális megőrzése | 10 év |
| A napi helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén| 200 nap |
| A heti helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén | 200 hét |
| A havi helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén | 120 hónap |
| Az éves helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén | 10 év |

## <a name="supported-restore-methods"></a>Támogatott visszaállítási módszerek

| Restore metódus     | Részletek                                                      |
| ------------------ | ------------------------------------------------------------ |
| Teljes megosztás visszaállítása | A teljes fájlmegosztást visszaállíthatja az eredeti vagy egy másik helyre. |
| Elemszintű visszaállítás | Az egyes fájlokat és mappákat az eredetire vagy egy másik helyre állíthatja vissza |

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést az Azure file shares](backup-afs.md) -ről
* Ismerje meg, hogyan [állíthatja vissza az Azure-fájlmegosztást](restore-afs.md)
* Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait](manage-afs-backup.md)
