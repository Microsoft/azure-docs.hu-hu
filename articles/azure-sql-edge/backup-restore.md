---
title: Adatbázisok biztonsági mentése és visszaállítása – Azure SQL Edge
description: Az Azure SQL Edge biztonsági mentési és visszaállítási képességeinek megismerése.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395240"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Adatbázisok biztonsági mentése és visszaállítása az Azure SQL Edge-ben 

Az Azure SQL Edge a Microsoft SQL Database motor legújabb verzióira épül. Hasonló biztonsági mentési és helyreállítási adatbázis-képességeket biztosít, mint a SQL Server on Linux és SQL Server tárolókban futók. A biztonsági mentési és visszaállítási összetevő alapvető védelmet biztosít az Azure SQL Edge-adatbázisokban tárolt adatok védelméhez. 

A katasztrofális adatvesztés kockázatának csökkentése érdekében rendszeresen készítsen biztonsági másolatot az adatbázisokról, hogy rendszeresen őrizze meg az adatait. Egy jól megtervezett biztonsági mentési és visszaállítási stratégia segít megvédeni az adatbázisokat a különböző hibák által okozott adatvesztéstől. Tesztelje a stratégiát úgy, hogy visszaállítja a biztonsági mentéseket, majd helyreállítja az adatbázist, hogy felkészüljön a vészhelyzetre való reagálásra.

További információ a biztonsági másolatok fontos okairól: [SQL Server adatbázisok biztonsági mentése és visszaállítása](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Az Azure SQL Edge lehetővé teszi a helyi tárterületről és az Azure-blobokról történő biztonsági mentést és visszaállítást. További információ: [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) -ban, és [SQL Server biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Adatbázis biztonsági mentése az Azure SQL Edge-ben

Az Azure SQL Edge ugyanazokat a biztonsági mentési típusokat támogatja, mint a SQL Server. A teljes listát lásd: a [biztonsági mentés áttekintése](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Az Azure SQL Edge-ben létrehozott adatbázisok alapértelmezés szerint az egyszerű helyreállítási modellt használják. Ezért nem végezheti el a naplók biztonsági mentését ezeken az adatbázisokon. Ehhez szükség van egy rendszergazdára, hogy módosítsa az adatbázis helyreállítási modelljét a teljes helyreállítási modellre. A SQL Server által támogatott helyreállítási modellek teljes listáját a [helyreállítási modell áttekintése](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)című témakörben tekintheti meg.

### <a name="back-up-to-local-disk"></a>Biztonsági mentés helyi lemezre

A következő példában a `BACKUP DATABASE` Transact-SQL parancs használatával hozza létre az adatbázis biztonsági másolatát a tárolóban. Ebben a példában egy *biztonsági másolat* nevű új mappát hoz létre a biztonságimásolat-fájlok tárolásához.

1. Hozzon létre egy mappát a biztonsági mentésekhez. Futtassa ezt a parancsot azon a gazdagépen, amelyen az Azure SQL Edge-tárolója fut. A következő parancsban cserélje le a **<AzureSQLEdge_Container_Name>** az Azure SQL Edge-tároló nevére a telepítésben.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Kapcsolódjon az Azure SQL Edge-példányhoz SQL Server Management Studio (SSMS) vagy a Azure Data Studio használatával. Futtassa a `BACKUP DATABASE` parancsot a felhasználói adatbázis biztonsági mentésének elvégzéséhez. A következő példában a *IronOreSilicaPrediction* -adatbázis biztonsági mentését végzi.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Ha a parancs futtatása után az adatbázis biztonsági mentése sikeres volt, a SSMS vagy a Azure Data Studio eredmények szakaszában a következőhöz hasonló üzenetek jelennek meg.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>Biztonsági mentés az URL-címre

Az Azure SQL Edge a blobok és a Blobok blokkolására is támogatja a biztonsági mentést. További információ: [biztonsági mentés a blob vs oldal blobjának letiltásához](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob). A következő példában az adatbázis *IronOreSilicaPrediction* biztonsági másolat készül egy blokk-blobba. 

1. A Blobok blokkolása érdekében a biztonsági másolatok konfigurálásához először hozzon létre egy közös hozzáférésű aláírási (SAS) tokent, amelynek segítségével SQL Server hitelesítő adatokat hozhat létre az Azure SQL Edge-ben. A parancsfájl egy tárolt hozzáférési házirenddel társított SAS-t hoz létre. További információt [a közös hozzáférésű aláírások, 1. rész: az SAS-modell megismerése című részben](../storage/common/storage-sas-overview.md)talál. A parancsfájl a T-SQL-parancsot is írja a hitelesítő adatok SQL Server való létrehozásához. A következő parancsfájl azt feltételezi, hogy már rendelkezik egy Storage-fiókkal rendelkező Azure-előfizetéssel, valamint a biztonsági mentések tárolására szolgáló tárolóval.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    A szkript sikeres futtatása után másolja a `CREATE CREDENTIAL` parancsot egy lekérdezési eszközre. Ezután kapcsolódjon SQL Server-példányhoz, és futtassa a parancsot a hitelesítő adatok SAS-vel való létrehozásához.

2. Kapcsolódjon az Azure SQL Edge-példányhoz a SSMS vagy a Azure Data Studio használatával, és hozza létre a hitelesítő adatokat az előző lépésben megadott paranccsal. Ügyeljen arra, hogy a `CREATE CREDENTIAL` parancsot az előző lépésből származó tényleges kimenetre cserélje.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. A következő parancs a *IronOreSilicaPrediction* biztonsági mentését végzi az Azure Storage-tárolóban.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Adatbázis visszaállítása az Azure SQL Edge-ben

Az Azure SQL Edge-ben egy helyi lemezről, egy hálózati helyről vagy egy Azure Blob Storage-fiókból lehet visszaállítani. A SQL Server visszaállításával és helyreállításával kapcsolatos további információkért tekintse meg a [visszaállítás és helyreállítás áttekintése](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server)című témakört. Az SQL Server egyszerű helyreállítási modelljének áttekintését lásd: az adatbázis-visszaállítások [befejezése (egyszerű helyreállítási modell)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> Az Azure SQL Edge-ben létrehozott adatbázisok nem állíthatók vissza Microsoft SQL Server vagy Azure SQL-példányon. Emellett a Microsoft SQL Server vagy az Azure SQL-ben létrehozott adatbázis visszaállítható az Azure SQL Edge-ben, ha az adatbázis nem tartalmazza az Azure SQL Edge által nem támogatott szolgáltatásokat. 

### <a name="restore-from-a-local-disk"></a>Visszaállítás helyi lemezről

Ez a példa az előző példában létrehozott *IronOreSilicaPrediction* biztonsági mentést használja. Most visszaállíthatja egy másik néven új adatbázisként.

1. Ha az adatbázis biztonságimásolat-fájlja még nem szerepel a tárolóban, a következő paranccsal másolhatja a fájlt a tárolóba. Az alábbi példa azt feltételezi, hogy a biztonságimásolat-fájl megtalálható a helyi gazdagépen, és a rendszer átmásolja a/var/opt/MSSQL/Backup mappába egy *sql1* nevű Azure SQL Edge-tárolóba.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Kapcsolódjon az Azure SQL Edge-példányhoz a SSMS vagy a Azure Data Studio használatával a Restore parancs futtatásához. A következő példában a **IronOrePredictDB. bak** vissza lett állítva egy új adatbázis létrehozásához, **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. A Restore parancs futtatása után, ha a visszaállítási művelet sikeres volt, a kimenet ablakban a következőhöz hasonló üzenetek jelennek meg. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Visszaállítás az URL-címről

Az Azure SQL Edge támogatja az adatbázisok Azure Storage-fiókból való visszaállítását is. A visszaállítást a Blobok vagy a Blobok blobos biztonsági mentései közül lehet visszaállítani. A következő példában a rendszer visszaállítja a *IronOreSilicaPrediction_2020_04_16. bak* adatbázis biztonságimásolat-fájlját egy blokk-blobon az adatbázis létrehozásához, *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```