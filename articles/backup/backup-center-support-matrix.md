---
title: Támogatási mátrix a Backup Centerhez
description: Ez a cikk a biztonsági mentési központ által az egyes munkaterhelés-típusokhoz támogatott forgatókönyveket összegzi
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: d6e5d34e201edda4fd1e9fda85f210fb88211e28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504507"
---
# <a name="support-matrix-for-backup-center"></a>Támogatási mátrix a Backup Centerhez

A Backup Center egyetlen üvegtábla-panelt biztosít a vállalatok számára a nagy [léptékű biztonsági másolatok szabályozására, figyelésére, üzemeltetésére és elemzésére](backup-center-overview.md). Ez a cikk a biztonsági mentési központ által az egyes munkaterhelés-típusokhoz támogatott forgatókönyveket foglalja össze.

## <a name="supported-scenarios"></a>Támogatott esetek

| **Kategória** | **Forgatókönyv**  | **Támogatott számítási feladatok**  | **Korlátok** |
| -------------| ------------- | ----------------------- |------------|
| Figyelés   | Az összes feladat megtekintése | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | <li> az elérhető feladatok közül 7 nap áll rendelkezésre a dobozból. <br> <li> Minden szűrő/legördülő lista legfeljebb 1000 elemet támogat. Így a Backup Center legfeljebb 1000 előfizetés és 1000-tár figyelésére használható a bérlők között. |
| Figyelés | Az összes biztonsági mentési példány megtekintése | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Lásd fent |
| Figyelés | Az összes biztonsági mentési szabályzat megtekintése | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Lásd fent |
| Figyelés | Az összes tár megtekintése | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Lásd fent |
| Műveletek | Biztonsági mentés konfigurálása | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Az Azure-beli [virtuális gépek biztonsági mentésének](./backup-support-matrix-iaas.md) és a [Azure Database for PostgreSQL kiszolgáló biztonsági mentésének](backup-azure-database-postgresql.md#support-matrix) támogatási mátrixait lásd: |
| Műveletek | Biztonsági mentési példány visszaállítása | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Az Azure-beli [virtuális gépek biztonsági mentésének](./backup-support-matrix-iaas.md) és a [Azure Database for PostgreSQL kiszolgáló biztonsági mentésének](backup-azure-database-postgresql.md#support-matrix) támogatási mátrixait lásd: |
| Műveletek | Tár létrehozása | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Lásd: [Recovery Services](./backup-support-matrix.md#vault-support) -tár támogatási mátrixai |
| Műveletek | Biztonsági mentési szabályzat létrehozása | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Lásd: [Recovery Services](./backup-support-matrix.md#vault-support) -tár támogatási mátrixai |
| Műveletek | Igény szerinti biztonsági mentés végrehajtása biztonsági mentési példány esetén | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Az Azure-beli [virtuális gépek biztonsági mentésének](./backup-support-matrix-iaas.md) és a [Azure Database for PostgreSQL kiszolgáló biztonsági mentésének](backup-azure-database-postgresql.md#support-matrix) támogatási mátrixait lásd: |
| Műveletek | Biztonsági mentés leállítása biztonsági mentési példány esetén | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files<br/><br/> <li>Azure Blobs<br/><br/> <li>Azure Managed Disks | Az Azure-beli [virtuális gépek biztonsági mentésének](./backup-support-matrix-iaas.md) és a [Azure Database for PostgreSQL kiszolgáló biztonsági mentésének](backup-azure-database-postgresql.md#support-matrix) támogatási mátrixait lásd: |
| Insights | Biztonsági mentési jelentések megtekintése | <li> Azure-beli virtuális gép <br><br> <li> SQL az Azure-beli virtuális gépen <br><br> <li> SAP HANA az Azure-beli virtuális gépen <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure Backup ügynök (MARS) <br><br> <li> Azure Backup Server (MABS) | Tekintse át a [biztonsági mentési jelentések támogatott forgatókönyveit](./configure-reports.md#supported-scenarios) |
| Szabályozás | Beépített és egyéni Azure-szabályzatok megtekintése és társítása a "Backup" kategóriában | N.A. | N.A. |
| Szabályozás | A biztonsági mentéshez nem konfigurált adatforrások megtekintése | <li> Azure-beli virtuális gép <br><br> <li> Azure Database for PostgreSQL kiszolgáló | N/A |

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

| **Kategória** | **Forgatókönyv**  |
|--------------|---------------|
| Figyelés | Nagy méretű riasztások megtekintése |
| Műveletek | Tár beállításainak konfigurálása méretezéskor |
| Műveletek | Régiók közötti visszaállítási feladatok végrehajtása a Backup Centerben |

## <a name="next-steps"></a>Következő lépések

* [A Azure Backup támogatási mátrixának áttekintése](./backup-support-matrix.md)
* [Tekintse át az Azure virtuális gép biztonsági mentésének támogatási mátrixát](./backup-support-matrix-iaas.md)
* [Azure Database for PostgreSQL kiszolgáló biztonsági mentésének támogatási mátrixának áttekintése](backup-azure-database-postgresql.md#support-matrix)