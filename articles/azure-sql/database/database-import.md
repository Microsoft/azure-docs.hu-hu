---
title: BACPAC-fájl importálása egy adatbázis létrehozásához Azure SQL Database
description: Hozzon létre egy új adatbázist Azure SQL Database vagy Azure SQL felügyelt példányban egy BACPAC-fájlból.
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
ms.openlocfilehash: 2412d3d2851d1b4d251b50ff3068b7dcafcccee8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642048"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Gyors útmutató: BACPAC-fájl importálása Azure SQL Database vagy Azure SQL felügyelt példányban található adatbázisba
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -fájl használatával importálhat egy SQL Server adatbázist Azure SQL Database vagy SQL felügyelt példányba. Az adatokat importálhatja az Azure Blob Storage-ben tárolt BACPAC-fájlból (csak standard tárolás), vagy egy helyszíni tárolóból. Ha maximálisra szeretné növelni az importálási sebességet több, gyorsabb erőforrás biztosításával, skálázza az adatbázist magasabb szolgáltatási szintre és nagyobb számítási méretre az importálási folyamat során. A sikeres importálás után vertikálisan leskálázhatja őket.

> [!NOTE]
> Az importált adatbázis kompatibilitási szintje a forrás adatbázis kompatibilitási szintjén alapul.

> [!IMPORTANT]
> Az adatbázis importálása után úgy is dönthet, hogy az adatbázist az aktuális kompatibilitási szinten (a AdventureWorks2008R2-adatbázis 100-as szintjén) vagy magasabb szinten szeretné használni. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="using-azure-portal"></a>Az Azure Portal használata

Tekintse meg ezt a videót, amelyből megtudhatja, hogyan importálhat egy BACPAC-fájlból a Azure Portal vagy folytathatja az alábbi olvasást:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

A [Azure Portal](https://portal.azure.com) *csak* az Azure Blob Storage-ban tárolt BACPAC *-* fájlokból hoz létre egyetlen adatbázist a Azure SQL Databaseban.

Ha az adatbázist egy BACPAC-fájlból egy [Azure SQL felügyelt példányba](../managed-instance/sql-managed-instance-paas-overview.md) kívánja áttelepíteni, használja a SQL Server Management Studio vagy a SQLPackage, a Azure Portal vagy a Azure PowerShell használata jelenleg nem támogatott.

> [!NOTE]
> A Azure Portal vagy PowerShell által küldött importálási/exportálási kérelmeket feldolgozó gépeknek a BACPAC-fájlt, valamint a Data-Tier Application Framework (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének háromszorosára lehet szükség. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy néhány kérelem meghiúsul a hibával `There is not enough space on the disk` . Ebben az esetben a megkerülő megoldás az sqlpackage.exe futtatása egy olyan gépen, amely elegendő helyi lemezterülettel rendelkezik. A probléma elkerülése érdekében javasoljuk, hogy a 150GB-nál nagyobb adatbázisok importálására/exportálására SqlPackage használjon.

1. Ha a Azure Portal használatával szeretne importálni egy BACPAC-fájlból egy új önálló adatbázisba, nyissa meg a megfelelő kiszolgáló lapot, majd az eszköztáron válassza az **adatbázis importálása** lehetőséget.  

   ![Adatbázis import1](./media/database-import/sql-server-import-database.png)

1. Válassza ki a Storage-fiókot és a tárolót a BACPAC fájlhoz, majd válassza ki azt a BACPAC-fájlt, amelyből importálni kívánja.

1. Adja meg az új adatbázis méretét (általában megegyezik a forrással), és adja meg a cél SQL Server hitelesítő adatokat. A Azure SQL Databaseban található új adatbázis lehetséges értékeinek listáját az [adatbázis létrehozása](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)című témakörben tekintheti meg.

   ![Adatbázis import2](./media/database-import/sql-server-import-database-settings.png)

1. Kattintson az **OK** gombra.

1. Az importálás előrehaladásának figyeléséhez nyissa meg az adatbázis kiszolgáló lapját, és a **Beállítások** területen válassza az **Importálás/exportálás előzmények** elemet. Ha a művelet sikeres, az importálás **Befejezve** állapotba kerül.

   ![Adatbázis-importálás állapota](./media/database-import/sql-server-import-database-history.png)

1. Annak ellenőrzéséhez, hogy az adatbázis él-e a kiszolgálón, válassza az **SQL-adatbázisok** lehetőséget, és ellenőrizze, hogy az új adatbázis **online állapotban** van-e.

## <a name="using-sqlpackage"></a>A SqlPackage használata

Ha SQL Server adatbázist szeretne importálni a [SqlPackage](/sql/tools/sqlpackage) parancssori segédprogrammal, tekintse meg a [paraméterek és tulajdonságok importálása](/sql/tools/sqlpackage#import-parameters-and-properties)című témakört. [A Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és SQL Server Data Tools tartalmazza a SqlPackage. A legújabb [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontból is letöltheti. 

A méretezés és a teljesítmény érdekében javasoljuk, hogy a SqlPackage a legtöbb éles környezetben használja a Azure Portal használata helyett. Az SQL Server Customer tanácsadó csapatának blogja a fájlok használatával történő áttelepítéssel kapcsolatban `BACPAC` lásd: [áttelepítés SQL Serverról Azure SQL DATABASEre BACPAC-fájlok használatával](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

A DTU-alapú kiépítési modell az egyes rétegek adatbázis-maximális méretének értékét támogatja. Az adatbázisok importálásakor a [támogatott értékek valamelyikét kell használnia](/sql/t-sql/statements/create-database-transact-sql). 

A következő SqlPackage-utasítás importálja a **AdventureWorks2008R2** -adatbázist a helyi tárolóból egy **MYNEWSERVER20170403** nevű logikai SQL Server-kiszolgálóra. Létrehoz egy **myMigratedDatabase** nevű új adatbázist egy **prémium** szintű szolgáltatási szinttel és egy **P6** szolgáltatás-célkitűzéssel. A környezetnek megfelelően módosítsa ezeket az értékeket.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ha vállalati tűzfal mögötti Azure SQL Database szeretne csatlakozni, a tűzfalnak meg kell nyitnia a 1433-as portot. A felügyelt SQL-példányhoz való kapcsolódáshoz [pont – hely kapcsolat](../managed-instance/point-to-site-p2s-configure.md) vagy Express Route-kapcsolat szükséges.

Ez a példa bemutatja, hogyan importálhat egy adatbázist az SqlPackage használatával Active Directory univerzális hitelesítéssel.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>A PowerShell használata

> [!NOTE]
> [A felügyelt SQL-példányok](../managed-instance/sql-managed-instance-paas-overview.md) jelenleg nem támogatják az adatbázisok példány-adatbázisba való áttelepítését egy BACPAC-fájlból Azure PowerShell használatával. SQL felügyelt példányba való importáláshoz használja a SQL Server Management Studio vagy a SQLPackage.

> [!NOTE]
> A portálon vagy a Powershellen keresztül küldött importálási/exportálási kérelmeket feldolgozó gépeknek a bacpac-fájlt, valamint a Data-Tier Application Framework (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentős mértékben változhat az azonos méretű adatbázisok között, és akár 3 alkalommal is eltarthat az adatbázis mérete. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy egyes kérések meghiúsulnak a "nincs elég hely a lemezen" hibaüzenet. Ebben az esetben a megkerülő megoldás az sqlpackage.exe futtatása egy olyan gépen, amely elegendő helyi lemezterülettel rendelkezik. A 150GB-nál nagyobb adatbázisok importálásakor/exportálásakor a SqlPackage használatával elkerülhető a probléma.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

A [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) parancsmag használatával küldje el az importálási adatbázisra vonatkozó kérelmet az Azure-ba. Az adatbázis méretétől függően az importálás hosszabb időt is igénybe vehet. A DTU-alapú kiépítési modell az egyes rétegek adatbázis-maximális méretének értékét támogatja. Az adatbázisok importálásakor a [támogatott értékek valamelyikét kell használnia](/sql/t-sql/statements/create-database-transact-sql). 

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

Az importálás folyamatát a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmaggal tekintheti meg. A parancsmag futtatása közvetlenül a kérelem után `Status: InProgress` . Az importálás akkor fejeződik be, amikor megjelenik `Status: Succeeded` .

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

Az az [-SQL-db-import](/cli/azure/sql/db#az-sql-db-import) paranccsal küldje el az importálási adatbázisra vonatkozó kérelmet az Azure-nak. Az adatbázis méretétől függően az importálás hosszabb időt is igénybe vehet. A DTU-alapú kiépítési modell az egyes rétegek adatbázis-maximális méretének értékét támogatja. Az adatbázisok importálásakor a [támogatott értékek valamelyikét kell használnia](/sql/t-sql/statements/create-database-transact-sql). 

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
> Egy másik parancsfájl-példa: [adatbázis importálása BACPAC-fájlból](scripts/import-from-bacpac-powershell.md).

## <a name="cancel-the-import-request"></a>Az importálási kérelem megszakítása

Az [adatbázis-műveletek – az API megszakítása](/rest/api/sql/databaseoperations/cancel) vagy a PowerShell [stop-AzSqlDatabaseActivity parancs](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity)használata – példa a PowerShell parancsra.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Korlátozások

- A rugalmas készletben lévő adatbázisokba importálás nem támogatott. Az adatokat importálhatja egy önálló adatbázisba, majd az adatbázist áthelyezheti egy rugalmas készletbe.
- Az importálási exportálási szolgáltatás nem működik, ha az Azure-szolgáltatásokhoz való hozzáférés engedélyezése beállítás ki van kapcsolva. Azonban a probléma megoldásához manuálisan is futtathatja sqlpackage.exe egy Azure-beli virtuális gépről, vagy közvetlenül a kódban végezheti el az exportálást a DACFx API használatával.
- Az importálás nem támogatja a biztonságimásolat-tárolási redundancia megadását egy új adatbázis létrehozásakor, és az alapértelmezett geo-redundáns biztonságimásolat-tárolási redundanciával hozza létre. A megkerülő megoldáshoz először hozzon létre egy üres adatbázist a kívánt biztonságimásolat-tárolási redundanciával Azure Portal vagy PowerShell használatával, majd importálja a BACPAC ebbe az üres adatbázisba. 

> [!NOTE]
> Azure SQL Database konfigurálható biztonsági mentési tár redundancia jelenleg csak nyilvános előzetes verzióban érhető el a Délkelet-ázsiai Azure-régióban.

## <a name="import-using-wizards"></a>Importálás varázslók használatával

Ezeket a varázslókat is használhatja.

- [Adatrétegbeli alkalmazás importálása varázsló SQL Server Management Studioban](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server Importálás és exportálás varázsló](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné tudni, hogyan csatlakozhat az adatbázishoz, és hogyan lehet lekérdezéseket Azure SQL Databaseban, tekintse meg a gyors útmutató [: Azure SQL Database: a SQL Server Management Studio használatával csatlakozhat az adatlekérdezéshez](connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server adatbázis-áttelepítési folyamatról, beleértve a teljesítménnyel kapcsolatos ajánlásokat is, tekintse meg a [SQL Server adatbázis áttelepítésének Azure SQL Database](migrate-to-database-from-sql-server.md).
- A tárolási kulcsok és a közös hozzáférési aláírások biztonságos kezelésével és megosztásával kapcsolatos további információkért lásd: az [Azure Storage biztonsági útmutatója](../../storage/blobs/security-recommendations.md).