---
title: Azure SQL Database rugalmasfeladat-ügynök létrehozása a PowerShell használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre rugalmasfeladat-ügynököt a PowerShell használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 064d55b96c8817f4b7ccc5f0925eeecfaf310424
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550518"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Rugalmasfeladat-ügynök létrehozása a PowerShell használatával

A [rugalmas feladatok](sql-database-job-automation-overview.md#elastic-database-jobs) lehetővé teszik egy vagy több Transact-SQL- (T-SQL-) szkript több adatbázisban történő, párhuzamos futtatását.

Ebben az oktatóanyagban megtudhatja, hogy milyen lépéseket kell végrehajtani a lekérdezések több adatbázishoz való futtatásához:

> [!div class="checklist"]
> * Rugalmasfeladat-ügynök létrehozása
> * A feladatok hitelesítő adatainak létrehozása, amelyek segítségével a feladatok a célhelyen tudják végrehajtani a szkripteket
> * A feladatok futtatási célhelyeinek (kiszolgálók, rugalmas készletek, adatbázisok, szegmenstérképek) meghatározása
> * Adatbázishoz kötődő hitelesítő adatok létrehozása a céladatbázisokban, amelyekhez az ügynök csatlakozni tud a feladatok végrehajtásához
> * Feladat létrehozása
> * Feladatlépések hozzáadása egy feladathoz
> * Feladat-végrehajtás indítása
> * Feladat monitorozása

## <a name="prerequisites"></a>Előfeltételek

A rugalmas adatbázis-feladatok frissített verziója a PowerShell-parancsmagok új készletét használja az áttelepítés során való használatra. Ezek az új parancsmagok az összes meglévő feladat hitelesítő adatait, célpontját (beleértve az adatbázisokat, a kiszolgálókat, az egyéni gyűjteményeket), a feladatok eseményindítóit, a feladatok ütemterveit, a feladatok tartalmát és a feladatokat egy új rugalmas feladat-ügynökre helyezik át.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>A legújabb rugalmas feladatok parancsmagjának telepítése

Ha még nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladat megkezdése előtt.

Telepítse az az **. SQL** 1.1.1-Preview modult a legújabb rugalmas feladatok parancsmagok beszerzéséhez. Futtassa az alábbi parancsokat a PowerShellben rendszergazdai jogosultsággal.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

- Az az **. SQL** 1.1.1-Preview modulon kívül az oktatóanyaghoz a *SQLServer* PowerShell-modulra is szükség van. Részletekért lásd: [SQL Server PowerShell-modul telepítése](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module).


## <a name="create-required-resources"></a>A szükséges erőforrások létrehozása

A rugalmasfeladat-ügynök létrehozásához [feladat-adatbázisként](sql-database-job-automation-overview.md#job-database) használható (S0 vagy magasabb szintű) adatbázisra van szükség. 

*Az alábbi szkript egy új erőforráscsoportot, kiszolgálót és feladat-adatbázisként használható adatbázist hoz létre. Az alábbi szkript egy második kiszolgálót is létrehoz két üres adatbázissal a feladatok végrehajtásához.*

A rugalmas feladatokhoz nem tartoznak külön elnevezési követelmények, így tetszőleges elnevezési konvenciót alkalmazhat, ha az megfelel [az Azure követelményeinek](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

```powershell
# Sign in to your Azure account
Connect-AzAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>A rugalmas feladatok előzetes verzióinak engedélyezése az előfizetéséhez

Rugalmas feladatok használatához regisztrálja a szolgáltatást az Azure-előfizetésben a következő parancs futtatásával. Egyszer futtassa ezt a parancsot ahhoz az előfizetéshez, amelyben létre kívánja hozni a rugalmas feladatokhoz tartozó ügynököt. Azokat az előfizetéseket, amelyek csak a feladatütemezés adatbázisait tartalmazzák, nem kell regisztrálniuk.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Rugalmasfeladat-ügynök létrehozása

A rugalmasfeladat-ügynök a feladatok létrehozásához, futtatásához és kezeléséhez használt Azure-erőforrás. Az ügynök ütemezés szerint vagy egyszeri alkalommal hajtja végre a feladatokat.

A **New-AzSqlElasticJobAgent** parancsmaghoz egy Azure SQL Database-adatbázisra van szükség, így a *ResourceGroupName*, a *servername*és a *databasename* paramétereknek mind a meglévő erőforrásokra kell mutatniuk.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>A feladatok hitelesítő adatainak létrehozása, amelyek segítségével a feladatok a célhelyen tudják végrehajtani a szkripteket

A feladatok adatbázishoz kötődő hitelesítő adatokat használnak a célcsoport által végrehajtáskor meghatározott céladatbázisokhoz történő csatlakozáshoz. A rendszer szintén ezeket az adatbázishoz kötődő hitelesítő adatokat használja a master adatbázishoz való csatlakozáshoz, az adott kiszolgálón vagy rugalmas készletben lévő adatbázisok enumerálására, ha a célcsoporttagtípus ezek valamelyike.

Az adatbázishoz kötődő hitelesítő adatokat a feladat-adatbázisban kell létrehozni.  
A feladat sikeres befejezéséhez minden céladatbázisnak rendelkeznie kell egy megfelelő engedélyeket tartalmazó bejelentkezési hozzáféréssel.

![Rugalmas feladatokhoz tartozó hitelesítő adatok](media/elastic-jobs-overview/job-credentials.png)

A képen szereplő hitelesítő adatok áttekintésén kívül vegye figyelembe azt is, hogy az alábbi szkriptben *GRANT*-parancsok is szerepelnek. Ezek az engedélyek szükségesek a jelen példafeladathoz kiválasztott szkript számára. Mivel ez a példa egy új táblát hoz létre a céladatbázisokban, a sikeres futtatáshoz mindegyik céladatbázisnak rendelkeznie kell a megfelelő engedélyekkel.

A feladatokhoz tartozó hitelesítő adatok (feladat-adatbázisban történő) létrehozásához futtassa a következő szkriptet:

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Határozza meg azokat a céladatbázisokat, amelyeken a feladatot futtatni szeretné

A [célcsoport](sql-database-job-automation-overview.md#target-group) határozza meg azt az egy vagy több adatbázist, amely(ek)en az adott feladatlépés végre lesz hajtva. 

A következő kódrészlet két célcsoportot hoz létre: *ServerGroup*és *ServerGroupExcludingDb2*. A *ServerGroup* csoport céladatbázisa a kiszolgálón a végrehajtás időpontjában megtalálható összes adatbázis, a *ServerGroupExcludingDb2* csoporté pedig a kiszolgálón megtalálható összes adatbázis, a *TargetDb2* kivételével:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>Feladat létrehozása

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Feladatlépés létrehozása

Ez a példa két feladatlépést határoz meg a futtatni kívánt feladathoz. Az első lépésben (*step1*) egy új táblát (*Step1Table*) hozunk létre a *ServerGroup* célcsoport összes adatbázisában. A második lépésben (*step2*) egy új táblát (*Step2Table*) hozunk létre a *TargetDb2* kivételével az összes adatbázisban, mivel a [korábban meghatározott](#define-the-target-databases-you-want-to-run-the-job-against) célcsoport esetében azt kizártuk.

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>A feladat futtatása

Futtassa a következő parancsot a feladat azonnali futtatásához:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzSqlElasticJob
$JobExecution
```

A művelet sikeres befejezését követően két új tábla jelenik meg a TargetDb1 adatbázisban, és csak egy új tábla a TargetDb2 adatbázisban:


   ![az új táblák ellenőrzése az SSMS-ben](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>A feladat-végrehajtások állapotának monitorozása

A feladat-végrehajtások részletes adatai a következő kódrészlettel kérdezhetők le:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

### <a name="job-execution-states"></a>Feladatok végrehajtási állapotai

A következő táblázat a lehetséges feladatok végrehajtásának állapotait sorolja fel:

|Állapot|Leírás|
|:---|:---|
|**Létrehozott** | A feladatok végrehajtása éppen létrejött, és még nincs folyamatban.|
|**Folyamatban** | A feladatok végrehajtása jelenleg folyamatban van.|
|**WaitingForRetry** | A feladat végrehajtása nem tudta befejezni a műveletet, és az újrapróbálkozásra vár.|
|**Sikerült** | A feladatok végrehajtása sikeresen befejeződött.|
|**SucceededWithSkipped** | A feladatok végrehajtása sikeresen befejeződött, de néhány gyermeke ki lett hagyva.|
|**Sikertelen** | A feladatok végrehajtása sikertelen volt, és kimerítette az újrapróbálkozásait.|
|**TimedOut** | A feladatok végrehajtása túllépte az időkorlátot.|
|**Visszavont** | A feladat végrehajtása meg lett szakítva.|
|**Kihagyva** | A rendszer kihagyta a feladatok végrehajtását, mert ugyanazon a feladattípusnak egy másik végrehajtása már fut ugyanazon a célhelyen.|
|**WaitingForChildJobExecutions** | A feladatok végrehajtása a gyermek végrehajtásának befejeződésére vár.|

## <a name="schedule-the-job-to-run-later"></a>A feladat ütemezése későbbi futtatáshoz

A következő paranccsal ütemezheti az adott feladat futtatását egy későbbi időpontra:

```powershell
# Run every hour starting from now
$Job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen oktatóanyagban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el.

> [!TIP]
> Ha azt tervezi, hogy ezekkel a feladatokkal dolgozik tovább, akkor ne törölje az ebben a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor az alábbi lépésekkel törölheti az ebben a cikkben létrehozott összes erőforrást.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban több adatbázisban fog futtatni egy Transact-SQL-szkriptet.  Megismerte, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Rugalmasfeladat-ügynök létrehozása
> * A feladatok hitelesítő adatainak létrehozása, amelyek segítségével a feladatok a célhelyen tudják végrehajtani a szkripteket
> * A feladatok futtatási célhelyeinek (kiszolgálók, rugalmas készletek, adatbázisok, szegmenstérképek) meghatározása
> * Adatbázishoz kötődő hitelesítő adatok létrehozása a céladatbázisokban, amelyekhez az ügynök csatlakozni tud a feladatok végrehajtásához
> * Feladat létrehozása
> * Feladatlépés hozzáadása a feladathoz
> * Feladat-végrehajtás indítása
> * A feladat figyelése

> [!div class="nextstepaction"]
>[Rugalmas feladatok kezelése Transact-SQL használatával](elastic-jobs-tsql.md)
