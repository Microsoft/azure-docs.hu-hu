---
title: Az Azure SQL felügyelt példányain az online áttelepítéssel kapcsolatos ismert problémák és korlátozások
description: Ismerje meg az Azure SQL felügyelt példányának online áttelepítésével kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98695533"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Ismert problémák/áttelepítési korlátozások az Azure SQL felügyelt példányaihoz való online áttelepítéssel

Az SQL Serverról az Azure SQL felügyelt példányra való online áttelepítéssel kapcsolatos ismert problémák és korlátozások alább olvashatók.

> [!IMPORTANT]
> A SQL Server online áttelepítésével Azure SQL Database a SQL_variant adattípusok áttelepítése nem támogatott.

## <a name="backup-requirements"></a>Biztonsági mentési követelmények

- **Biztonsági mentések ellenőrzőösszeggel**

    A Azure Database Migration Service a biztonsági mentés és visszaállítás módszert használja a helyszíni adatbázisok SQL felügyelt példányra történő átirányításához. Azure Database Migration Service csak az ellenőrzőösszeg használatával létrehozott biztonsági mentéseket támogatja.

    A biztonsági mentés [vagy a visszaállítás során engedélyezze vagy tiltsa le a biztonsági másolatok ellenőrzőösszegét (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Ha az adatbázis biztonsági másolatait tömörítéssel végzi el, az ellenőrzőösszeg alapértelmezett viselkedés, kivéve, ha explicit módon le van tiltva.

    Ha az offline áttelepítést választja, akkor a **Azure Database Migration Service.**. Azure Database Migration Service. lehetőséget választva a rendszer engedélyezi az adatbázis biztonsági mentését az ellenőrzőösszeg beállítással.

- **Adathordozó biztonsági mentése**

    Ügyeljen arra, hogy minden biztonsági mentést készítsen egy különálló biztonsági mentési adathordozón (biztonságimásolat-fájl). A Azure Database Migration Service nem támogatja az egyetlen biztonságimásolat-fájlhoz fűzött biztonsági mentéseket. Készítsen teljes biztonsági mentést, és készítsen biztonsági másolatot a biztonságimásolat-fájlokról.

## <a name="data-and-log-file-layout"></a>Az adatfájlok és a naplófájlok elrendezése

- **Naplófájlok száma**

    Azure Database Migration Service nem támogatja több naplófájlt tartalmazó adatbázisok használatát. Ha több naplófájlja is van, akkor egyetlen tranzakciós naplófájlba kell bontania és átrendeznie őket. Mivel nem lehet távolról olyan naplófájlokat létrehozni, amelyek nem üresek, először a naplófájlt kell biztonsági másolatot készíteni.

## <a name="sql-server-features"></a>SQL Server funkciók

- **FileStream/objektumnak**

    A felügyelt SQL-példány jelenleg nem támogatja a FileStream és a objektumnak. A funkcióktól függő munkaterhelések esetében javasoljuk, hogy az Azure-beli virtuális gépeken futó SQL-kiszolgálókat Azure-célként válassza.

- **Memóriában tárolt táblák**

    A memóriában tárolt OLTP az SQL által felügyelt példány prémium és üzletileg kritikus szintjein érhető el; a általános célúi réteg nem támogatja a memóriabeli OLTP.

## <a name="migration-resets"></a>Áttelepítési alaphelyzetbe állítás

- **Központi telepítés**

    Az SQL felügyelt példánya egy olyan Pásti szolgáltatás, amely automatikus javítással és verzióval frissül. Az SQL felügyelt példányának áttelepítése során a nem kritikus frissítések akár 36 óráig is tarthatók. Ezt követően (és a kritikus frissítések esetében), ha az áttelepítés megszakad, a folyamat visszaállítja a teljes visszaállítási állapotot.

    Az áttelepítési átváltás csak a teljes biztonsági mentés visszaállítása után hívhatók meg, és a rendszer az összes naplózott biztonsági mentést felhasználja. Ha az éles környezetbe történő áttelepítés átállásos érintett, forduljon az [Azure DMS visszajelzési aliasához](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>SMB-fájlmegosztás kapcsolata

Az SMB-fájlmegosztás kapcsolódási problémáit valószínűleg egy engedély okozza. 

Az SMB-fájlmegosztás kapcsolatának teszteléséhez kövesse az alábbi lépéseket: 

1. Mentse az SMB-fájlmegosztás biztonsági másolatát. 
1. Ellenőrizze a hálózati kapcsolatot a Azure Database Migration Service alhálózata és a forrás SQL Server között. Ennek a legegyszerűbb módja, ha SQL Server virtuális gépet telepít a DMS-alhálózatra, és a SQL Server Management Studio használatával csatlakozik a forrás SQL Server. 
1. Állítsa vissza a forrás SQL Server fejlécét a fájlmegosztás található biztonsági másolatból: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Ha nem tud csatlakozni a fájlmegosztás számára, konfigurálja az engedélyeket a következő lépésekkel: 

1. Navigáljon a fájlmegosztást a fájlkezelő használatával. 
1. Kattintson a jobb gombbal a fájlmegosztás elemre, és válassza a tulajdonságok lehetőséget. 
1. Válassza a **megosztás** fület, és válassza a **speciális megosztás** lehetőséget. 
1. Adja hozzá az áttelepítéshez használt Windows-fiókot, és rendelje hozzá teljes hozzáférés-vezérlést. 
1. Adja hozzá a SQL Server-szolgáltatásfiókot, és rendelje hozzá teljes hozzáférés-vezérlést. Ha nem biztos benne, hogy melyik fiókot használja, ellenőrizze a SQL Server-szolgáltatásfiók **SQL Server Konfigurációkezelőét** . 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Teljes hozzáférés biztosítása az áttelepítéshez használt Windows-fiókokhoz és a SQL Server szolgáltatásfiók számára. ":::

