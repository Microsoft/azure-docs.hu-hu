---
title: Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure PowerShell a Data Lake Analytics fiókok, adatforrások, felhasználók és & feladatok kezelésére.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/29/2018
ms.openlocfilehash: 2f56fc285ed263b4b58985b5df620740a33cb3f5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092351"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti, hogyan kezelheti Azure Data Lake Analytics fiókokat, adatforrásokat, felhasználókat és feladatokat Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell és a Data Lake Analytics összegyűjtése a következő adatokat gyűjti össze:

* **Előfizetés azonosítója**: az Data Lake Analytics-fiókot tartalmazó Azure-előfizetés azonosítója.
* **Erőforráscsoport**: az Data Lake Analytics-fiókot tartalmazó Azure-erőforráscsoport neve.
* **Data Lake Analytics fiók neve**: a Data Lake Analytics-fiók neve.
* **Alapértelmezett Data Lake Store fiók neve**: minden Data Lake Analytics fiókhoz tartozik egy alapértelmezett Data Lake Store fiók.
* **Hely**: a Data Lake Analytics fiókjának helye, például "az USA 2. keleti régiója" vagy más támogatott hely.

A jelen oktatóanyagban szereplő PowerShell-kódrészletek ezeket a változókat használják adattárolásra

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

### <a name="log-in-using-interactive-user-authentication"></a>Bejelentkezés az interaktív felhasználói hitelesítés használatával

Bejelentkezés előfizetés-AZONOSÍTÓval vagy előfizetés nevével

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname
```

## <a name="saving-authentication-context"></a>Hitelesítési környezet mentése

A `Connect-AzAccount` parancsmag mindig kéri a hitelesítő adatokat. A következő parancsmagok használatával elkerülhető a kérés:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Bejelentkezés egyszerű szolgáltatásnév (SPI) használatával

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname"
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Fiókok kezelése

### <a name="list-accounts"></a>Fiókok listája

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Fiók létrehozása

A naplók tárolásához minden Data Lake Analytics-fiók esetében szükség van egy alapértelmezett Data Lake Store-fiókra. Felhasználhat egy meglévő fiókot, vagy létrehozhat egy fiókot.

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Fiókadatok beolvasása

Egy fiók adatainak beolvasása.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Annak ellenőrzése, hogy létezik-e fiók

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Azure Data Lake Analytics jelenleg a következő adatforrásokat támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Minden Data Lake Analytics fiók rendelkezik alapértelmezett Data Lake Store-fiókkal. Az alapértelmezett Data Lake Store fiók a feladatok metaadatainak és a feladatok naplóinak tárolására szolgál.

### <a name="find-the-default-data-lake-store-account"></a>Az alapértelmezett Data Lake Store fiók megkeresése

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Az alapértelmezett Data Lake Store fiókot az adatforrások listájának szűrésével keresheti meg a `IsDefault` tulajdonság szerint:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault }
```

### <a name="add-a-data-source"></a>Adatforrás hozzáadása

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName
```

### <a name="list-data-sources"></a>Adatforrások listázása

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL-feladatok küldése

### <a name="submit-a-string-as-a-u-sql-job"></a>Karakterlánc küldése U-SQL-feladatokként

```powershell
$script = @"
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Fájl elküldése U-SQL-feladatokként

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Feladatok listázása

A kimenet tartalmazza az aktuálisan futó és a nemrégiben befejezett feladatokat is.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>A legfontosabb N feladat listázása

Alapértelmezés szerint a feladatok listája a beküldési idő szerint van rendezve. Így a legutóbb elküldött feladatok először megjelennek. Alapértelmezés szerint a ADLA-fiók 180 napig visszaveszi a feladatokat, de a Get-AdlJob parancsmag alapértelmezés szerint csak az első 500 értéket adja vissza. A-Top paraméterrel meghatározott számú feladatot listázhat.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Feladatok listázása feladat állapota szerint

A `-State` paraméter használata. A következő értékek bármelyikét kombinálhatja:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Feladatok listázása feladat eredménye alapján

A `-Result` paraméter használatával ellenőrizze, hogy a Befejezett feladatok sikeresen befejeződtek-e. Ezek az értékek:

* Lemondva
* Sikertelen
* Nincs
* Sikeres

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Feladatok listázása a feladat elküldő alapján

A `-Submitter` paraméter segítségével azonosíthatja, hogy ki küldte el a feladatot.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Feladatok listázása beküldési idő szerint

A `-SubmittedAfter` hasznos az időtartományok szűrésére.

```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Feladatok állapotának beolvasása

Kérje le egy adott feladat állapotát.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="cancel-a-job"></a>Feladat megszakítása

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Várakozás a feladatok befejeződésére

Ahelyett, hogy megismétli a `Get-AdlAnalyticsJob` feladatot, a `Wait-AdlJob` parancsmaggal megvárhatja, hogy a feladatok véget ér.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>A feladatok előzményeinek elemzése

A Data Lake Analyticsben futtatott feladatok előzményeinek elemzése Azure PowerShell használatával hatékony módszer. A használatával betekintést nyerhet a használatba és a költséghatékonyságba. További információ: a [Job History Analysis Sample repo](https://github.com/jpalbright31/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Feladatokhoz tartozó folyamatok és ismétlődések listázása

A `Get-AdlJobPipeline` parancsmag használatával megtekintheti a korábban elküldött feladatok folyamatát.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

A `Get-AdlJobRecurrence` korábban elküldött feladatok ismétlődési adatainak megtekintéséhez használja a parancsmagot.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="manage-compute-policies"></a>Számítási házirendek kezelése

### <a name="list-existing-compute-policies"></a>Meglévő számítási szabályzatok listázása

A `Get-AdlAnalyticsComputePolicy` parancsmag egy Data Lake Analytics fiók számítási házirendjeivel kapcsolatos információkat kér le.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Számítási szabályzat létrehozása

A `New-AdlAnalyticsComputePolicy` parancsmag új számítási szabályzatot hoz létre egy Data Lake Analytics-fiókhoz. Ez a példa a megadott felhasználó számára elérhető maximális au-t 50-re állítja be, a feladat minimális prioritása pedig 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="manage-files"></a>Fájlok kezelése

### <a name="check-for-the-existence-of-a-file"></a>Fájl létezésének keresése

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Feltöltés és letöltés

Töltsön fel egy fájlt.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv"
```

Töltse fel a teljes mappát rekurzív módon.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Fájl letöltése.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Töltse le a teljes mappát rekurzív módon.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Ha a feltöltési vagy letöltési folyamat megszakad, a parancsmag újbóli futtatásával próbálkozhat a művelet folytatásával ``-Resume`` .

## <a name="manage-the-u-sql-catalog"></a>Az U-SQL-katalógus kezelése

A U-SQL-katalógus az adat és a kód szerkezetét használja, így U-SQL-parancsfájlok is megoszthatók. A katalógus lehetővé teszi, hogy a lehető legnagyobb teljesítmény legyen elérhető a Azure Data Lakeban lévő adataival. További információk: [Use U-SQL catalog](./data-lake-analytics-u-sql-get-started.md) (U-SQL-katalógus használata).

### <a name="list-items-in-the-u-sql-catalog"></a>Elemek listázása az U-SQL-katalógusban

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>A U-SQL katalógus összes szerelvényének listázása

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Katalógus-elemek részleteinek beolvasása

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Hitelesítő adatok tárolása a katalógusban

Egy U-SQL-adatbázison belül hozzon létre egy hitelesítő objektumot az Azure-ban üzemeltetett adatbázishoz. Jelenleg a U-SQL hitelesítő adatok csak a PowerShell használatával hozhatók létre.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Tűzfalszabályok kezelése

### <a name="list-firewall-rules"></a>Tűzfalszabályok listázása

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Tűzfalszabály hozzáadása

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Tűzfalszabály módosítása

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Tűzfalszabály eltávolítása

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Azure IP-címek engedélyezése

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="working-with-azure"></a>Az Azure használata

### <a name="get-error-details"></a>Hiba részleteinek beolvasása

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Annak ellenőrzése, hogy rendszergazdaként fut-e a Windows rendszerű gépen

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>TenantID keresése

Előfizetés neve:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Előfizetés-AZONOSÍTÓból:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Egy tartományi címről, például "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Az előfizetések és a bérlői azonosítók listázása

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Data Lake Analytics fiók létrehozása sablon használatával

Azure-erőforráscsoport-sablont is használhat a következő minta használatával: [Data Lake Analytics fiók létrehozása sablon használatával](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Következő lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* A Data Lake Analytics használatának első lépései az [](data-lake-analytics-get-started-portal.md)  |  [](data-lake-analytics-get-started-powershell.md)  |  [Azure parancssori](data-lake-analytics-get-started-cli.md) felületének Azure Portal Azure PowerShell
* Azure Data Lake Analytics kezelése [Azure Portal](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [CLI](data-lake-analytics-manage-use-cli.md) használatával