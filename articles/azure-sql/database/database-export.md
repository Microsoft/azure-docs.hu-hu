---
title: Azure SQL Database exportálása BACPAC-fájlba (a Azure Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Adatbázis exportálása BACPAC-fájlba a Azure Portal használatával.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 1e08f3bd40dbd51a31eb6a78f102c12ab26e2790
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519724"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Exportálás BACPAC-fájlba – Azure SQL Database és az Azure SQL felügyelt példánya

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ha archiválni vagy egy másik platformra szeretne áthelyezni egy adatbázist, exportálhatja az adatbázis-sémát és az [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) -fájlba. A BACPAC-fájl egy ZIP-fájl, amelynek kiterjesztése BACPAC, amely tartalmazza a metaadatokat és az adatokat az adatbázisból. A BACPAC-fájlok az Azure Blob Storage-ban vagy helyi tárolóban tárolhatók, és később importálhatók Azure SQL Databaseba, az Azure SQL felügyelt példányaiba vagy egy SQL Server példányba.

## <a name="considerations"></a>Megfontolandó szempontok

- Ahhoz, hogy az Exportálás tranzakciós szempontból konzisztens legyen, gondoskodnia kell arról, hogy az exportálás során ne történjen írási tevékenység, vagy hogy az adatbázis [tranzakciós szempontból konzisztens másolata](database-copy.md) legyen exportálva.
- Ha blob Storage-ba exportál, a BACPAC-fájlok maximális mérete 200 GB. Egy nagyobb BACPAC-fájl archiválásához exportálja a helyi tárolóba.
- BACPAC-fájlnak az Azure Premium Storage-ba való exportálása a cikkben ismertetett módszerek használatával nem támogatott.
- A tűzfal mögötti tárterület jelenleg nem támogatott.
- A tárolási fájlnévnek vagy a StorageURI bemeneti értékének 128 karakternél rövidebbnek kell lennie, és nem végződhet "." karakterrel, és nem tartalmazhat speciális karaktereket, például szóközt vagy "<, >, *,%, &,:, \, /,?". 
- Ha az exportálási művelet meghaladja a 20 órát, előfordulhat, hogy megszakítja a műveletet. Az exportálás során a teljesítmény növeléséhez a következőket teheti:

  - Ideiglenesen növelje a számítási méretet.
  - Az exportálás során minden olvasási és írási tevékenység megszűnik.
  - Használjon nem null értékű [fürtözött indexet](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) az összes nagyméretű táblán. Fürtözött indexek nélkül az Exportálás sikertelen lehet, ha 6-12 óránál hosszabb időt vesz igénybe. Ennek az az oka, hogy az exportálási szolgáltatásnak egy táblázatos vizsgálatot kell végrehajtania a teljes tábla exportálásához. Egy jó módszer annak megállapítására, hogy a táblák exportálásra vannak-e optimalizálva, hogy **DBCC SHOW_STATISTICS** fusson, és győződjön meg arról, hogy a *RANGE_HI_KEY* nem null értékű, és az értéke jó eloszlású. Részletekért lásd: [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> A BACPACs nem használhatók biztonsági mentési és visszaállítási műveletekhez. Az Azure automatikusan készít biztonsági másolatokat minden felhasználói adatbázishoz. Részletekért lásd: [Üzletmenet-folytonosság áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md) és [SQL Database biztonsági mentések](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Azure Portal

Az [Azure SQL felügyelt példányairól](../managed-instance/sql-managed-instance-paas-overview.md) származó BACPAC exportálása a Azure Portal használatával jelenleg nem támogatott. Ehelyett használjon SQL Server Management Studio vagy SQLPackage.

> [!NOTE]
> A Azure Portal vagy PowerShell által küldött importálási/exportálási kérelmeket feldolgozó gépeknek a BACPAC-fájlt, valamint a Data-Tier Application Framework (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének háromszorosára lehet szükség. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy néhány kérelem meghiúsul a hibával `There is not enough space on the disk` . Ebben az esetben a megkerülő megoldás az sqlpackage.exe futtatása egy olyan gépen, amely elegendő helyi lemezterülettel rendelkezik. A probléma elkerülése érdekében javasoljuk, hogy a 150GB-nál nagyobb adatbázisok importálására/exportálására [SqlPackage](#sqlpackage-utility) használjon.

1. Ha a [Azure Portal](https://portal.azure.com)segítségével szeretne exportálni egy adatbázist, nyissa meg az adatbázis lapját, és kattintson az **Exportálás** gombra az eszköztáron.

   ![Képernyőkép, amely kiemeli az Exportálás gombot.](./media/database-export/database-export1.png)

2. Adja meg a BACPAC fájlnevét, válasszon ki egy meglévő Azure Storage-fiókot és-tárolót az exportáláshoz, majd adja meg a megfelelő hitelesítő adatokat a forrás-adatbázishoz való hozzáféréshez. Ha Ön az Azure-rendszergazda, az Azure-rendszergazda nem **rendelkezik rendszergazdai** engedélyekkel az Azure SQL Database vagy az Azure SQL felügyelt példányában.

    ![Adatbázis-exportálás](./media/database-export/database-export2.png)

3. Kattintson az **OK** gombra.

4. Az exportálási művelet előrehaladásának figyeléséhez nyissa meg az exportálni kívánt adatbázist tartalmazó kiszolgáló lapját. A **Beállítások** területen kattintson az **Importálás/exportálás előzmények** elemre.

   ![exportálási előzmények](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage segédprogram

Ha a [SqlPackage](/sql/tools/sqlpackage) parancssori segédprogrammal szeretne exportálni egy adatbázist a SQL Databaseban, tekintse meg a [paraméterek és tulajdonságok exportálása](/sql/tools/sqlpackage#export-parameters-and-properties)című témakört. A SQLPackage segédprogram a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és a SQL Server Data Tools legújabb verzióit tartalmazza a [Visual studióhoz](/sql/ssdt/download-sql-server-data-tools-ssdt), vagy letöltheti a [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) legújabb verzióját közvetlenül a Microsoft letöltőközpontból.

A legtöbb éles környezetben a SQLPackage segédprogram használatát javasoljuk a méretezéshez és a teljesítményhez. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Ez a példa azt mutatja be, hogyan lehet exportálni egy adatbázist a SqlPackage.exe használatával Active Directory univerzális hitelesítéssel:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

SQL Server Management Studio legújabb verziói egy varázslót biztosítanak egy adatbázis exportálásához Azure SQL Database vagy SQL felügyelt példány-adatbázisból egy BACPAC-fájlba. Lásd: [adatrétegbeli alkalmazás exportálása](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> Az [Azure SQL felügyelt példánya](../managed-instance/sql-managed-instance-paas-overview.md) jelenleg nem támogatja az adatbázisok exportálását egy BACPAC-fájlba Azure PowerShell használatával. Felügyelt példány BACPAC-fájlba való exportálásához használja a SQL Server Management Studio vagy a SQLPackage.

A [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) parancsmag használatával küldje el az exportálási adatbázisra vonatkozó kérelmet a Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az exportálási művelet végrehajtása hosszabb időt is igénybe vehet.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Az exportálási kérelem állapotának megtekintéséhez használja a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmagot. Ezt azonnal futtatva, miután a kérelem általában visszaadja az **állapotot: Inprogress**. Amikor megjelenik az **Állapot: sikeres** az Exportálás befejeződött.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```
## <a name="cancel-the-export-request"></a>Az exportálási kérelem megszakítása

Az [adatbázis-műveletek – az API megszakítása](https://docs.microsoft.com/rest/api/sql/databaseoperations/cancel) vagy a PowerShell [stop-AzSqlDatabaseActivity parancs](https://docs.microsoft.com/powershell/module/az.sql/Stop-AzSqlDatabaseActivity?view=azps-5.5.0)használata – példa a PowerShell parancsra.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```

## <a name="next-steps"></a>Következő lépések

- Az adatbázisok archiválási célú exportálásának alternatívájaként a biztonsági másolatok hosszú távú [megőrzése](long-term-retention-overview.md)című témakörből megismerheti az önálló adatbázisok és a készletezett adatbázisok hosszú távú biztonsági mentését. Az SQL-ügynök feladatainak használatával ütemezheti az [adatbázis-másolatok biztonsági mentését](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) a hosszú távú biztonsági mentés alternatívájaként.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- A BACPAC SQL Server adatbázisba való importálásával kapcsolatos további tudnivalókért lásd: [BACPAC importálása SQL Server-adatbázisba](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- A BACPAC SQL Server adatbázisból való exportálásával kapcsolatos információkért lásd: [adatrétegbeli alkalmazás exportálása](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Ha szeretne többet megtudni arról, hogyan használható az adatáttelepítési szolgáltatás egy adatbázis áttelepítéséhez, tekintse meg az [áttelepítés SQL Serverról Azure SQL Database offline állapotba a DMS használatával](../../dms/tutorial-sql-server-to-azure-sql.md)című
- Ha az SQL Server-ból exportálja a Azure SQL Databaseba való áttelepítést, olvassa el [a SQL Server adatbázis áttelepítése Azure SQL Databasere](migrate-to-database-from-sql-server.md)című témakört.
- A tárolási kulcsok és a közös hozzáférési aláírások biztonságos kezelésével és megosztásával kapcsolatos további információkért lásd: az [Azure Storage biztonsági útmutatója](../../storage/blobs/security-recommendations.md).
