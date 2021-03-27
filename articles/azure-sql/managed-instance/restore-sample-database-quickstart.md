---
title: 'Gyors útmutató: biztonsági másolat visszaállítása (SSMS)'
titleSuffix: Azure SQL Managed Instance
description: Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja vissza az adatbázisok biztonsági mentését az Azure SQL felügyelt példányaira SQL Server Management Studio (SSMS) használatával.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 15aead5c9f93205baac28e8b93a88015c9689e3a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625313"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>Gyors útmutató: adatbázis visszaállítása az Azure SQL felügyelt példányára a SSMS használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebben a rövid útmutatóban SQL Server Management Studio (SSMS) használatával állítja vissza az adatbázist (a Wide World Importers-standard Backup fájlt) az Azure Blob Storage-ból az [Azure SQL felügyelt példányaira](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> A Azure Database Migration Service használatával történő áttelepítéssel kapcsolatos további információkért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
> A különböző áttelepítési módszerekkel kapcsolatos további információkért lásd: [SQL Server Migrálás az Azure SQL felügyelt példányára](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- Erőforrásokat használ a [felügyelt példány létrehozása](instance-create-quickstart.md) rövid útmutatóból.
- A [SSMS](/sql/ssms/sql-server-management-studio-ssms) legújabb verzióját kell telepíteni.
- A használatához a SSMS használata szükséges a felügyelt SQL-példányhoz való kapcsolódáshoz. A kapcsolódás menetét a következő útmutatókban tekintheti meg:
  - [Nyilvános végpont engedélyezése](public-endpoint-configure.md) az SQL felügyelt példányain – ez az oktatóanyag ajánlott megközelítése.
  - [Kapcsolódjon az SQL felügyelt példányhoz egy Azure-beli virtuális](connect-vm-instance-configure.md)gépről.
  - [Pont – hely kapcsolat konfigurálása az SQL felügyelt példányához a helyszínen](point-to-site-p2s-configure.md).

> [!NOTE]
> A SQL Server-adatbázisok Azure Blob Storage használatával történő biztonsági mentéséről és visszaállításáról további [információt a](../../storage/common/storage-sas-overview.md) [SQL Server biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url)című témakörben talál.

## <a name="restore-from-a-backup-file"></a>Visszaállítás biztonságimásolat-fájlból

A SQL Server Management Studio az alábbi lépéseket követve állítsa vissza a Wide World Importers-adatbázist az SQL felügyelt példányára. Az adatbázis biztonságimásolat-fájlját egy előre konfigurált Azure Blob Storage-fiók tárolja.

1. Nyissa meg a SSMS, és kapcsolódjon a felügyelt példányhoz.
2. A **Object Explorerban** kattintson a jobb gombbal a felügyelt példányra, és válassza az **Új lekérdezés** lehetőséget egy új lekérdezési ablak megnyitásához.
3. Futtassa a következő SQL-parancsfájlt, amely egy előre konfigurált Storage-fiókot és egy SAS-kulcsot használ [egy hitelesítő adat létrehozásához](/sql/t-sql/statements/create-credential-transact-sql) a felügyelt példányban.
 
   > [!IMPORTANT]
   > `CREDENTIAL` meg kell egyeznie a tároló elérési útjával, a kezdetével `https` , és nem tartalmazhat záró perjelet. `IDENTITY` kell lennie `SHARED ACCESS SIGNATURE` . `SECRET` a közös hozzáférésű aláírási tokennek kell lennie, és nem tartalmazhat vezetőt `?` .

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![hitelesítő adat létrehozása](./media/restore-sample-database-quickstart/credential.png)

4. A hitelesítő adatok ellenőrzéséhez futtassa a következő parancsfájlt, amely egy [tároló](https://azure.microsoft.com/services/container-instances/) URL-címet használ a biztonságimásolat-fájlok listájának lekéréséhez.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/restore-sample-database-quickstart/file-list.png)

5. Futtassa a következő szkriptet a Wide World importing-adatbázis visszaállításához.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![A képernyőképen a Object Explorer futó parancsfájl a sikert jelző üzenettel jelenik meg.](./media/restore-sample-database-quickstart/restore.png)

6. Futtassa a következő szkriptet a visszaállítás állapotának nyomon követéséhez.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Ha a visszaállítás befejeződött, tekintse meg az adatbázist Object Explorerban. Ellenőrizheti, hogy az adatbázis-visszaállítás a [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézet használatával fejeződött-e be.

> [!NOTE]
> Az adatbázis-visszaállítási művelet aszinkron és újrapróbálható. Előfordulhat, hogy a rendszer hibát jelez SQL Server Management Studio, ha a kapcsolatok megszakadnak, vagy lejár az időtúllépés. Azure SQL Database továbbra is megkísérli visszaállítani az adatbázist a háttérben, és a [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek használatával nyomon követheti a visszaállítás előrehaladását.
> A visszaállítási folyamat egyes fázisaiban egyedi azonosítót fog látni a rendszernézetek tényleges neve helyett. Ismerje meg az `RESTORE` utasítások viselkedésével kapcsolatos [](./transact-sql-tsql-differences-sql-server.md#restore-statement)eltéréseket.

## <a name="next-steps"></a>Következő lépések

- Ha az 5. lépésben az adatbázis-visszaállítás a 22003-es AZONOSÍTÓJÚ üzenettel megszűnik, hozzon létre egy új biztonságimásolat-fájlt, amely biztonsági mentési ellenőrzőösszegeket tartalmaz, és végezze el a visszaállítást. A biztonsági mentés vagy a visszaállítás során olvassa el a [biztonsági másolatok ellenőrzőösszegének engedélyezése vagy letiltása](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)című témakört.
- Az URL-címekre történő biztonsági mentés hibaelhárítását lásd: [SQL Server biztonsági mentés az URL-címekhez – ajánlott eljárások és hibaelhárítás](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Az alkalmazás-csatlakozási lehetőségek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Ha kedvenc eszközeit vagy nyelveit szeretné lekérdezni, tekintse meg a rövid útmutatók [: Azure SQL Database a kapcsolódás és a lekérdezés](../database/connect-query-content-reference-guide.md)című témakört.
