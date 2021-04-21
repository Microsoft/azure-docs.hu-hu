---
title: BACPAC-fájl importálása adatbázis létrehozásához a Azure SQL Database
description: Hozzon létre egy új adatbázist a Azure SQL Database vagy Azure SQL Managed Instance BACPAC-fájlból.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: eddcab2c0a34ef437e4f2f1e2203fee9065133a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781882"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rövid útmutató: BACPAC-fájl importálása egy adatbázisba Azure SQL Database vagy Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Importálhat egy SQL Server adatbázist a Azure SQL Database vagy SQL Managed Instance [BACPAC-fájl használatával.](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Az adatokat importálhatja az Azure Blob Storage-ben tárolt BACPAC-fájlból (csak standard tárolás), vagy egy helyszíni tárolóból. Ha maximálisra szeretné növelni az importálási sebességet több, gyorsabb erőforrás biztosításával, skálázza az adatbázist magasabb szolgáltatási szintre és nagyobb számítási méretre az importálási folyamat során. A sikeres importálás után vertikálisan leskálázhatja őket.

> [!NOTE]
> Az importált adatbázis kompatibilitási szintje a forrásadatbázis kompatibilitási szintjétől függ.

> [!IMPORTANT]
> Az adatbázis importálása után dönthet úgy, hogy az aktuális kompatibilitási szinten (az AdventureWorks2008R2 adatbázis 100-as szintjén) vagy magasabb szinten működteti az adatbázist. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="using-azure-portal"></a>Az Azure Portal használata

Tekintse meg ezt a videót, amelyből láthatja, hogyan importálhat BACPAC-Azure Portal vagy folytathatja az alábbi olvasást:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

A [Azure Portal](https://portal.azure.com) *csak egyetlen* adatbázis létrehozását támogatja a  Azure SQL Database és csak az Azure Blob Storage-ban tárolt BACPAC-fájlból.

Ha adatbázist egy BACPAC Azure SQL Managed Instance fájlból Azure SQL Managed Instance át egy adatbázisba, használja az SQL Server Management Studio-t vagy az SQLPackage-et, ha a Azure Portal vagy Azure PowerShell használata jelenleg nem támogatott. [](../managed-instance/sql-managed-instance-paas-overview.md)

> [!NOTE]
> A Azure Portal vagy a PowerShell használatával elküldött importálási/exportálási kérelmeket feldolgozó gépeknek tárolnia kell a BACPAC-fájlt, valamint a Data-Tier Application Framework (DacFX) által létrehozott ideiglenes fájlokat. A szükséges lemezterület jelentősen változik az azonos méretű adatbázisok között, és akár az adatbázis méretének akár 3-szor nagyobb lemezterületet is igényelhet. Az importálási/exportálási kérelmet futtató gépeken csak 450 GB helyi lemezterület áll rendelkezésre. Ennek eredményeképpen egyes kérések meghiúsulhatnak a következő `There is not enough space on the disk` hibával: . Ebben az esetben az áthidaló megoldás az, hogy sqlpackage.exe helyi lemezterülettel is futtatja a virtuális gépet. Javasoljuk, hogy a probléma elkerülése érdekében az SqlPackage használatával importálja/exportálja a 150 GB-ot nagyobb méretű adatbázisokat.

1. Ha BACPAC-fájlból az adatbázist használó új adatbázisba Azure Portal, nyissa meg a megfelelő kiszolgálóoldalt, majd az eszköztáron válassza az **Adatbázis importálása lehetőséget.**  

   ![Adatbázis importálása1](./media/database-import/sql-server-import-database.png)

1. Válassza ki a BACPAC-fájl tárfiókját és tárolóját, majd válassza ki azt a BACPAC-fájlt, amelyből importálni kell.

1. Adja meg az új adatbázisméretet (általában megegyezik a forrással), és adja meg a SQL Server hitelesítő adatait. Az új adatbázis lehetséges értékeinek listájáért lásd: Adatbázis [Azure SQL Database.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)

   ![Adatbázis importálása2](./media/database-import/sql-server-import-database-settings.png)

1. Kattintson az **OK** gombra.

1. Az importálási folyamat figyelése érdekében nyissa meg az adatbázis kiszolgálóoldalát, és a Beállítások alatt válassza az **Importálási/Exportálási előzmények lehetőséget.** Sikeres művelet esetén az importálás **Befejezve állapotú** lesz.

   ![Adatbázis importálásának állapota](./media/database-import/sql-server-import-database-history.png)

1. Annak ellenőrzéséhez, hogy az adatbázis élő-e a kiszolgálón, válassza az **SQL-adatbázisok** lehetőséget, és ellenőrizze, hogy az új adatbázis Online **állapotú-e.**

## <a name="using-sqlpackage"></a>Az SqlPackage használata

Ha egy adatbázist SQL Server [az SqlPackage](/sql/tools/sqlpackage) parancssori segédprogrammal, tekintse meg a paraméterek és tulajdonságok [importálását.](/sql/tools/sqlpackage#import-parameters-and-properties) [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools az](/sql/ssdt/download-sql-server-data-tools-ssdt) SqlPackage Visual Studio tartalmazza. A legújabb [SqlPackage-et](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontból is letöltheti. 

A méretezés és a teljesítmény érdekében javasoljuk, hogy a legtöbb éles környezetben használja az SqlPackage-et a Azure Portal. A SQL Server használatával való áttelepítésről az ügyféltanácsadói csapat blogján a BACPAC-fájlokat használó SQL Server-Azure SQL Database `BACPAC` olvashat. [](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)

A DTU-alapú kiépítési modell támogatja az adatbázisok maximális méretértékének kiválasztását az egyes szintekhez. Adatbázis importálásakor használja [a következő támogatott értékek valamelyikét:](/sql/t-sql/statements/create-database-transact-sql). 

A következő SqlPackage parancs importálja az **AdventureWorks2008R2** adatbázist a helyi tárolóból egy **mynewserver20170403** nevű logikai SQL-kiszolgálóra. Létrehoz egy **myMigratedDatabase** nevű új adatbázist egy **Prémium** szolgáltatási szint és **egy P6 szolgáltatási** céllal. Módosítsa ezeket az értékeket a környezetének megfelelően.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ahhoz, hogy Azure SQL Database tűzfal mögül csatlakozzon, a tűzfalon nyitva kell lennie az 1433-as portnak. A SQL Managed Instance [pont–hely](../managed-instance/point-to-site-p2s-configure.md) kapcsolattal vagy Express Route-kapcsolattal kell lennie.

Ez a példa bemutatja, hogyan importálhat adatbázist az SqlPackage és az Active Directory hitelesítés használatával.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>A PowerShell használata

> [!NOTE]
> [A SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) jelenleg nem támogatja az adatbázisok egy példányadatbázisba való áttelepítését egy BACPAC-fájlból a Azure PowerShell. Az importáláshoz használja a SQL Managed Instance vagy az SQLPackage SQL Server Management Studio parancsokat.

> [!NOTE]
> A portálon vagy a PowerShellben elküldött importálási/exportálási kérelmeket feldolgozó gépeknek tárolnia kell a BACPAC-fájlt, valamint az Data-Tier Application Framework (DacFX) által létrehozott ideiglenes fájlokat. A szükséges lemezterület jelentősen változik az azonos méretű adatbázisok között, és az adatbázis méretének akár 3-szor is eltelhet. Az importálási/exportálási kérelmet futtató gépeken csak 450 GB helyi lemezterület áll rendelkezésre. Ennek eredményeképpen egyes kérések meghiúsulhatnak a "Nincs elég hely a lemezen" hibával. Ebben az esetben az áthidaló megoldás az, hogy sqlpackage.exe helyi lemezterülettel is futtatja a virtuális gépet. 150 GB-ot nagyobb adatbázisok importálásakor/exportálásakor használja az SqlPackage-et a probléma elkerüléséhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kihozva. Az AzureRM modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek. További információ a kompatibilitásukról: [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Az új Azure PowerShell Az modul).

A [New-AzSqlDatabaseImport parancsmag](/powershell/module/az.sql/new-azsqldatabaseimport) használatával küldjön importálási adatbázis-kérelmet az Azure-nak. Az importálás az adatbázis méretétől függően némi időt vehet igénybe. A DTU-alapú kiépítési modell támogatja az adatbázisok maximális méretértékének kiválasztását az egyes szintekhez. Adatbázis importálásakor [használja a következő támogatott értékek valamelyikét:](/sql/t-sql/statements/create-database-transact-sql). 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Az importálás előrehaladását a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmag használatával ellenőrizheti. A parancsmag közvetlenül a kérés után való futtatása általában a értéket adja `Status: InProgress` vissza. Az importálás akkor fejeződött be, amikor adatokat `Status: Succeeded` lát.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az-sql-db-import paranccsal](/cli/azure/sql/db#az_sql_db_import) küldjön be egy importálási adatbázisra vonatkozó kérést az Azure-ba. Az adatbázis méretétől függően az importálás némi időt vehet igénybe. A DTU-alapú kiépítési modell támogatja az adatbázisok maximális méretértékének kiválasztását az egyes szintekhez. Adatbázis importálásakor használja [a következő támogatott értékek valamelyikét:](/sql/t-sql/statements/create-database-transact-sql). 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Egy másik példaszk szkriptért lásd: [Adatbázis importálása BACPAC-fájlból.](scripts/import-from-bacpac-powershell.md)

## <a name="cancel-the-import-request"></a>Az importálási kérelem megszakítása

Használja az [Adatbázis-műveletek – Cancel API-t](/rest/api/sql/databaseoperations/cancel) vagy a PowerShell [Stop-AzSqlDatabaseActivity parancsot,](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity)amely egy példa a PowerShell-parancsra.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Korlátozások

- A rugalmas készletben lévő adatbázisokba importálás nem támogatott. Az adatokat importálhatja egy önálló adatbázisba, majd az adatbázist áthelyezheti egy rugalmas készletbe.
- Az Import Export Service nem működik, ha az Azure-szolgáltatásokhoz való hozzáférés engedélyezése ki van kapcsolva. A probléma megoldásához azonban manuálisan futtathat sqlpackage.exe egy Azure-beli virtuális gépről, vagy közvetlenül a kódban végezheti el az exportálást a DACFx API használatával.
- Az importálás nem támogatja a biztonsági másolatok tárhelyredundansának megadását új adatbázis létrehozásakor, és az alapértelmezett georedundáns biztonsági mentési tárhelyredundanssal történő létrehozást. Megkerülő megoldásként először hozzon létre egy üres adatbázist a kívánt biztonsági mentési tárhely-redundanciával a Azure Portal vagy a PowerShell használatával, majd importálja a BACPAC-et ebbe az üres adatbázisba. 

> [!NOTE]
> Azure SQL Database Backup Storage redundancia konfigurálása jelenleg nyilvános előzetes verzióban érhető el a délkelet-ázsiai Azure-régióban.

## <a name="import-using-wizards"></a>Importálás varázslók használatával

Ezeket a varázslókat is használhatja.

- [Adatrétegbeli alkalmazás importálása varázsló a SQL Server Management Studio.](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)
- [SQL Server Importálás és exportálás varázsló.](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>Következő lépések

- Az adatbázisokhoz való csatlakozásról és a lekérdezésről az Azure SQL Database gyorsútmutató: [Azure SQL Database:](connect-query-ssms.md)Csatlakozás és adatlekérdezés SQL Server Management Studio használatával.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes adatbázis-SQL Server, beleértve a teljesítménnyel kapcsolatos javaslatokat, a SQL Server áttelepítése a [Azure SQL Database.](migrate-to-database-from-sql-server.md)
- A tárkulcsok és a közös hozzáférésű jogosultságok biztonságos kezeléséhez és megosztásához tekintse meg az [Azure Storage biztonsági útmutatóját.](../../storage/blobs/security-recommendations.md)