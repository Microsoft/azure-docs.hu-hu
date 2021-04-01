---
title: XEvent-esemény fájljának kódja
description: A PowerShell és a Transact-SQL egy kétfázisú kódrészletet biztosít, amely bemutatja az eseménynaplót egy Azure SQL Database kiterjesztett eseményében. Az Azure Storage a forgatókönyv kötelező része.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: d7a57f98551cf91ed87858caba0907471bcf6b12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96501226"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Az eseménynaplóban a kiterjesztett események Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Egy teljes kódrészletet szeretne, amely robusztus módszert biztosít a kiterjesztett események adatainak rögzítésére és jelentésére.

A Microsoft SQL Server az Event [file Target](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) használatával tárolja az esemény kimeneteit egy helyi merevlemez-fájlba. Ezek a fájlok azonban nem érhetők el Azure SQL Database számára. Ehelyett az Azure Storage szolgáltatást használjuk az Event file cél támogatásához.

Ez a témakör egy kétfázisú mintakód-mintát mutat be:

- PowerShell, Azure Storage-tároló létrehozása a felhőben.
- Transact-SQL:
  - Az Azure Storage-tároló társítása egy eseményvezérelt fájl céljához.
  - Az esemény-munkamenet létrehozásához és elindításához, és így tovább.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

- Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
- Bármely adatbázis, amelyben létrehozhat egy táblát.
  
  - Igény szerint percek alatt [létrehozhat egy **AdventureWorksLT** bemutató adatbázist](single-database-create-quickstart.md) .

- SQL Server Management Studio (ssms.exe), ideális esetben a legújabb havi frissítési verzió.
  A legújabb ssms.exe a következő címről töltheti le:
  
  - A című témakör a [letöltés SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  - [Közvetlen hivatkozás a letöltésre.](https://go.microsoft.com/fwlink/?linkid=616025)

- Telepítenie kell a [Azure PowerShell modulokat](https://go.microsoft.com/?linkid=9811175) .

  - A modulok olyan parancsokat biztosítanak, mint a- **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>1. fázis: az Azure Storage-tároló PowerShell-kódja

Ez a PowerShell a kétfázisú mintakód 1. fázisa.

A parancsfájl egy lehetséges korábbi Futtatás után megtisztított parancsokkal kezdődik, és a rerunnable.

1. Illessze be a PowerShell-szkriptet egy egyszerű szövegszerkesztőbe, például Notepad.exeba, és mentse a parancsfájlt fájlként a **. ps1** kiterjesztéssel.
2. Indítsa el a PowerShell ISE-t rendszergazdaként.
3. A parancssorba írja be a következőt:<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>majd nyomja le az ENTER billentyűt.
4. A PowerShell ISE-ben nyissa meg a **. ps1** fájlt. Futtassa a szkriptet.
5. A szkript először egy új ablakot indít el, amelyben bejelentkezik az Azure-ba.

   - Ha a munkamenet megszakítása nélkül Újrafuttatja a parancsfájlt, lehetősége van az **Add-AzureAccount** parancs megjegyzésére.

![A PowerShell ISE, amelyen telepítve van az Azure-modul, készen áll a szkript futtatására.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>PowerShell-kód

Ez a PowerShell-parancsfájl feltételezi, hogy már telepítette az az modult. További információ: [a Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Jegyezze fel a PowerShell-szkript végén kinyomtatott néhány elnevezett értéket. Ezeket az értékeket a 2. fázisként következő Transact-SQL-szkriptbe kell szerkesztenie.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> Az előző PowerShell-kódban például a kiterjesztett SQL-események nem kompatibilisek a ADLS Gen2 Storage-fiókokkal.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>2. fázis: az Azure Storage-tárolót használó Transact-SQL-kód

- A kód 1. fázisában egy PowerShell-szkriptet futtatott egy Azure Storage-tároló létrehozásához.
- A 2. fázisban a következő Transact-SQL-szkriptnek kell használnia a tárolót.

A parancsfájl egy lehetséges korábbi Futtatás után megtisztított parancsokkal kezdődik, és a rerunnable.

A PowerShell-parancsfájl néhány elnevezett értéket kinyomtatott, amikor véget ért. Az értékek használatához szerkesztenie kell a Transact-SQL parancsfájlt. Keresse meg a **teendőket** a Transact-SQL-szkriptben a szerkesztési pontok megkereséséhez.

1. Nyissa meg SQL Server Management Studio (ssms.exe).
2. Kapcsolódjon Azure SQL Database-adatbázishoz.
3. Kattintson ide egy új lekérdezési panel megnyitásához.
4. Illessze be a következő Transact-SQL-parancsfájlt a lekérdezési ablaktáblába.
5. Keresse meg az összes **teendőt** a parancsfájlban, és végezze el a megfelelő módosításokat.
6. Mentse, majd futtassa a parancsfájlt.

> [!WARNING]
> Az előző PowerShell-parancsfájl által generált SAS-kulcs értéke "?" kezdetű lehet. (kérdőjel). Ha a következő T-SQL-parancsfájlban az SAS-kulcsot használja, *el kell távolítania a vezetőt (?*). Ellenkező esetben előfordulhat, hogy a biztonság blokkolja a saját erőfeszítéseit.

### <a name="transact-sql-code"></a>Transact-SQL-kód

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Ha a cél nem tud kapcsolódni a futtatásakor, le kell állítania és újra kell indítania az esemény-munkamenetet:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Kimenet

Amikor a Transact-SQL parancsfájl befejeződik, kattintson egy cellára a **event_data_XML** oszlop fejlécében. Egy **\<event>** elem jelenik meg, amely egy frissítési utasítást mutat be.

Itt látható egy **\<event>** elem, amelyet a rendszer a tesztelés során generált:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Az előző Transact-SQL-szkript a következő rendszerfüggvényt használta a event_file olvasásához:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

A kiterjesztett események adatainak megtekintésére szolgáló speciális beállítások magyarázata a következő címen érhető el:

- [A megcélzott adatok speciális megtekintése a kiterjesztett eseményekről](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>A kód minta konvertálása SQL Serverre való futtatásra

Tegyük fel, hogy az előző Transact-SQL-mintát szeretné futtatni Microsoft SQL Serveron.

- Az egyszerűség kedvéért szeretné teljesen lecserélni az Azure Storage-tároló használatát egy egyszerű fájllal (például *C:\myeventdata.xel*). A fájl a SQL Servert futtató számítógép helyi merevlemezére íródik.
- Nem szükséges semmilyen Transact-SQL-utasítás a **főkulcs létrehozásához** és a **hitelesítő adatok létrehozásához**.
- Az **esemény-munkamenet létrehozása** utasításban, az **Add Target** záradékában a **filename =** számára hozzárendelt http-értéket cserélje le a teljes elérésiút-karakterlánccal (például *C:\myfile.xel*).
  
  - Nincs szükség Azure Storage-fiókra.

## <a name="more-information"></a>További információ

Az Azure Storage szolgáltatás fiókjaival és tárolókkal kapcsolatos további információkért lásd:

- [A blob Storage használata a .NET-keretrendszerrel](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [A legfelső szintű tároló használata](/rest/api/storageservices/Working-with-the-Root-Container)
- [1. lecke: tárolt hozzáférési szabályzat és közös hozzáférésű aláírás létrehozása Azure-tárolón](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [2. lecke: SQL Server hitelesítő adat létrehozása közös hozzáférési aláírás használatával](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Microsoft SQL Server bővített eseményei](/sql/relational-databases/extended-events/extended-events)