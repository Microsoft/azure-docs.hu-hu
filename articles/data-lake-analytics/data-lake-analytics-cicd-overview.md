---
title: CI/CD-folyamat beállítása Azure Data Lake Analyticshoz
description: Ismerje meg, hogyan állíthatja be a Azure Data Lake Analytics folyamatos integrációját és folyamatos üzembe helyezését.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015264"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>CI/CD-folyamat beállítása Azure Data Lake Analyticshoz  

Ebből a cikkből megtudhatja, hogyan állíthatja be a folyamatos integrációs és üzembe helyezési (CI/CD) folyamatokat a U-SQL-feladatok és U-SQL-adatbázisok számára.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>CI/CD használata U-SQL-feladatokhoz

A Visual studióhoz készült Azure Data Lake Tools a u-SQL-projekt típusát kínálja, amely segít az U-SQL-parancsfájlok rendszerezésében. Ha a u-SQL-projektet használja az U-SQL-kód kezelésére, akkor a további CI/CD-forgatókönyvek is egyszerűbbek.

## <a name="build-a-u-sql-project"></a>U-SQL-projekt létrehozása

Egy U-SQL-projekt a Microsoft Build Engine (MSBuild) szolgáltatással építhető be a megfelelő paraméterek átadásával. A cikk lépéseit követve állítson be egy U-SQL-projekt létrehozási folyamatát.

### <a name="project-migration"></a>Projekt migrálása

Mielőtt felépítési feladatot állít be egy U-SQL-projekthez, ellenőrizze, hogy rendelkezik-e a U-SQL projekt legújabb verziójával. Nyissa meg a U-SQL projektfájlt a szerkesztőben, és ellenőrizze, hogy vannak-e ezek az importálási elemek:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Ha nem, két lehetőség közül választhat a projekt áttelepíthető:

- 1. lehetőség: módosítsa a régi importálási elemet az előzőre.
- 2. lehetőség: Nyissa meg a régi projektet a Visual studióhoz készült Azure Data Lake Tools eszközben. Használjon újabb verziót, mint a 2.3.3000.0. A régi projekt sablonját a rendszer automatikusan frissíti a legújabb verzióra. A 2.3.3000.0 újabb verziókkal létrehozott új projektek az új sablont használják.

### <a name="get-nuget"></a>NuGet beolvasása

Az MSBuild nem biztosít beépített támogatást az U-SQL-projektekhez. A támogatás megszerzéséhez hozzá kell adnia egy hivatkozást a megoldásához a [Microsoft. Azure. DataLake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-csomagjához, amely hozzáadja a szükséges nyelvi szolgáltatást.

A NuGet-csomag hivatkozásának hozzáadásához kattintson a jobb gombbal a megoldásra a Visual Studio Megoldáskezelő, majd válassza a **NuGet-csomagok kezelése** lehetőséget. Vagy hozzáadhat egy nevű fájlt `packages.config` a megoldás mappájához, és a következő tartalmakat helyezheti bele:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Az U-SQL Database-referenciák kezelése

A u-SQL-projektben található u-SQL-parancsfájlok lekérdezési utasításokkal rendelkezhetnek U-SQL Database-objektumokhoz. Ebben az esetben a megfelelő U-SQL adatbázis-projektre kell hivatkoznia, amely tartalmazza az objektum definícióját az U-SQL projekt létrehozása előtt. Ilyen például, ha egy U-SQL-táblázatot kérdez le, vagy egy szerelvényre hivatkozik.

További információ az [U-SQL Database-projektről](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> A DROP utasítás véletlen törlést eredményezhet. A DROP utasítás engedélyezéséhez explicit módon meg kell adnia az MSBuild argumentumokat. A **AllowDropStatement** lehetővé teszi a nem adathoz kapcsolódó drop műveletet, például a drop Assembly és a drop Table Valued függvényt. A **AllowDataDropStatement** lehetővé teszi az adatművelettel kapcsolatos drop műveletet, például a drop Table és a drop Schema. A AllowDataDropStatement használata előtt engedélyeznie kell a AllowDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>U-SQL-projekt létrehozása az MSBuild parancssorral

Először telepítse át a projektet, és szerezze be a NuGet csomagot. Ezután hívja meg a szabványos MSBuild parancssort a következő további argumentumokkal az U-SQL-projekt létrehozásához:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

Az argumentumok definíciója és értékei a következők:

- **USQLSDKPath = \<U-SQL Nuget package> \build\runtime**. Ez a paraméter az U-SQL nyelvi szolgáltatás NuGet-csomagjának telepítési útvonalára vonatkozik.
- **USQLTargetType = Merge vagy SyntaxCheck**:

  - **Egyesítés**. Az egyesítési mód kód mögé állítja a fájlokat. Ilyenek például a **. cs**, a **.** a és az **. r** fájlok. Az eredményül kapott felhasználó által megadott függvénytárat a U-SQL-parancsfájlba helyezi. Ilyenek például a dll bináris, a Python vagy az R-kód.

  - **SyntaxCheck**. A SyntaxCheck mód először a kód mögött lévő fájlokat egyesíti a U-SQL-parancsfájlba. Ezután lefordítja a U-SQL-szkriptet a kód érvényesítéséhez.

- **DataRoot = \<DataRoot path>**. A DataRoot csak SyntaxCheck mód esetén szükséges. Ha a parancsfájlt SyntaxCheck módban hozza létre, az MSBuild ellenőrzi a parancsfájlban található adatbázis-objektumokra mutató hivatkozásokat. A létrehozás előtt állítson be egy megfelelő helyi környezetet, amely tartalmazza a U-SQL-adatbázisból származó hivatkozott objektumokat a Build Machine DataRoot mappájában. Ezeket az adatbázis-függőségeket [egy U-SQL Database-projektre hivatkozva](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)is kezelheti. Az MSBuild csak az adatbázis-objektumok hivatkozásait ellenőrzi, a fájlokat nem.

- **EnableDeployment = True** vagy **false**. A EnableDeployment azt jelzi, hogy engedélyezett-e a hivatkozott U-SQL-adatbázisok telepítése a létrehozási folyamat során. Ha egy U-SQL adatbázis-projektre hivatkozik, és az adatbázis-objektumokat a U-SQL-parancsfájlban használja, a paramétert állítsa **igaz** értékre.

### <a name="continuous-integration-through-azure-pipelines"></a>Folyamatos integráció az Azure-folyamatokkal

A parancssoron kívül a Visual Studio Build vagy az MSBuild feladat használatával U-SQL-projekteket is létrehozhat az Azure-folyamatokban. A létrehozási folyamat létrehozásához vegyen fel két feladatot a build folyamatba: egy NuGet-visszaállítási feladatot és egy MSBuild-feladatot.

![MSBuild-feladat egy U-SQL-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Adjon hozzá egy NuGet-visszaállítási feladatot a megoldás által hivatkozott NuGet-csomag beszerzéséhez, hogy az az `Azure.DataLake.USQL.SDK` MSBuild megtalálja az U-SQL nyelvi célokat.   >   `$(Build.SourcesDirectory)/packages` Ha a 2. lépésben közvetlenül szeretné használni az MSBuild argumentumok mintát, állítsa be a speciális cél könyvtárat.

   ![NuGet-visszaállítási feladat egy U-SQL-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Állítsa be az MSBuild argumentumokat a Visual Studio Build eszközeiben vagy egy MSBuild-feladatban az alábbi példában látható módon. Ezen argumentumokhoz változókat is meghatározhat az Azure-folyamatok Build folyamatában.

   ![CI/CD MSBuild változók definiálása U-SQL-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>A U-SQL projekt Build kimenete

A Build futtatása után a U-SQL-projektben található összes parancsfájl a nevű zip-fájlba lett létrehozva és kimenetben `USQLProjectName.usqlpack` . A projektben lévő mappastruktúrát a tömörített Build kimenete tárolja.

> [!NOTE]
> Az egyes U-SQL-szkriptek programkódját a rendszer beágyazott utasításként egyesíti a szkript Build kimenetében.

## <a name="test-u-sql-scripts"></a>U-SQL-szkriptek tesztelése

Azure Data Lake a U-SQL-parancsfájlok és a C# UDO/UDAG/UDF tesztelési projektjeit biztosítja:

- Megtudhatja, hogyan [adhat hozzá tesztelési eseteket U-SQL-parancsfájlokhoz és bővített C#-kóddal](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

- Ismerje meg, hogyan [futtathat tesztelési eseteket az Azure-folyamatokban](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>U-SQL-feladatok üzembe helyezése

Miután megtörtént a kód ellenőrzése a létrehozási és tesztelési folyamat során, közvetlenül az Azure-folyamatokból küldhet be U-SQL-feladatokat egy Azure PowerShell feladaton keresztül. A parancsfájlt Azure Data Lake Store vagy Azure Blob Storage-ba is telepítheti, és [az ütemezett feladatokat Azure Data Factory használatával futtathatja](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>U-SQL-feladatok elküldése az Azure-folyamatokon keresztül

Az U-SQL projekt Build kimenete egy **USQLProjectName. usqlpack** nevű zip-fájl. A zip-fájl tartalmazza az összes U-SQL-parancsfájlt a projektben. A folyamatok [Azure PowerShell feladatát](/azure/devops/pipelines/tasks/deploy/azure-powershell) a következő PowerShell-szkripttel végezheti el, hogy közvetlenül az Azure-folyamatokból küldje el a U-SQL-feladatokat.

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> A parancsok: `Submit-AzDataLakeAnalyticsJob` és `Wait-AzDataLakeAnalyticsJob` mind Azure PowerShell parancsmagok a Azure Data Lake Analytics számára a Azure Resource Manager-keretrendszerben. Azure PowerShell telepített munkaállomást fog neeed. További parancsokat és példákat a parancsok [listáját ismertető témakörben](/powershell/module/Az.DataLakeAnalytics) talál.
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>U-SQL-feladatok központi telepítése Azure Data Factory

U-SQL-feladatokat küldhet közvetlenül az Azure-folyamatokból. A létrehozott szkripteket feltöltheti Azure Data Lake Store vagy Azure Blob Storage-ba, és [az ütemezett feladatokat Azure Data Factory használatával futtathatja](../data-factory/transform-data-using-data-lake-analytics.md).

Az alábbi PowerShell-parancsfájl használatával töltse fel az U-SQL-parancsfájlokat egy Azure Data Lake Store-fiókba az Azure-folyamatok [Azure PowerShell feladatával](/azure/devops/pipelines/tasks/deploy/azure-powershell) :

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD egy U-SQL-adatbázishoz

A Visual studióhoz készült Azure Data Lake Tools olyan U-SQL Database-sablonokat biztosít, amelyek segítenek az U-SQL-adatbázisok fejlesztésében, kezelésében és üzembe helyezésében. További információ a [U-SQL Database-projektről](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>U-SQL Database-projekt készítése

### <a name="get-the-nuget-package"></a>A NuGet-csomag beszerzése

Az MSBuild nem biztosít beépített támogatást a U-SQL Database-projektekhez. Ennek a képességnek a megszerzéséhez hozzá kell adnia egy hivatkozást a megoldásához a [Microsoft. Azure. DataLake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-csomagjához, amely hozzáadja a szükséges nyelvi szolgáltatást.

A NuGet-csomag hivatkozásának hozzáadásához kattintson a jobb gombbal a megoldásra a Visual Studio Megoldáskezelőban. Válassza a **NuGet-csomagok kezelése** lehetőséget. Ezután keresse meg és telepítse a NuGet csomagot. Vagy hozzáadhat egy **packages.config** nevű fájlt a megoldás mappájához, és a következő tartalmakat helyezheti bele:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Hozzon létre U-SQL a Database-projektet az MSBuild parancssorral

A U-SQL Database-projekt létrehozásához hívja meg a szabványos MSBuild parancssort, és adja át a U-SQL SDK NuGet-csomag hivatkozását további argumentumként. Lásd a következő példát: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Az argumentum az `USQLSDKPath=<U-SQL Nuget package>\build\runtime` U-SQL nyelvi szolgáltatás NuGet-csomagjának telepítési útvonalára hivatkozik.

### <a name="continuous-integration-with-azure-pipelines"></a>Folyamatos integráció az Azure Pipelinesszal

A parancssoron kívül a Visual Studio Build vagy az MSBuild feladat használatával U-SQL Database-projekteket hozhat létre az Azure-folyamatokban. Egy felépítési feladat beállításához ügyeljen arra, hogy két feladatot adjon hozzá a build folyamat: egy NuGet-visszaállítási feladat és egy MSBuild-feladat.

   ![CI/CD MSBuild feladat egy U-SQL-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Adjon hozzá egy NuGet-visszaállítási feladatot a megoldásra hivatkozott NuGet-csomag beszerzéséhez, amely magában foglalja az `Azure.DataLake.USQL.SDK` , hogy az MSBuild megtalálja az U-SQL nyelvi célokat.   >   `$(Build.SourcesDirectory)/packages` Ha a 2. lépésben közvetlenül szeretné használni az MSBuild argumentumok mintát, állítsa be a speciális cél könyvtárat.

   ![CI/CD NuGet feladat egy U-SQL-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Állítsa be az MSBuild argumentumokat a Visual Studio Build eszközeiben vagy egy MSBuild-feladatban az alábbi példában látható módon. Ezen argumentumokhoz változókat is meghatározhat az Azure-folyamatok Build folyamatában.

   ![CI/CD MSBuild változók definiálása U-SQL Database-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>A U-SQL Database projekt Build kimenete

A U-SQL adatbázis-projekthez tartozó Build kimenet egy U-SQL adatbázis-telepítési csomag, amelynek neve az utótag `.usqldbpack` . A `.usqldbpack` csomag egy zip-fájl, amely egyetlen U-SQL-parancsfájlban található DDL-utasítást tartalmaz egy DDL-mappában. Tartalmazza az összes **. dll** fájlt és a szerelvényhez tartozó további fájlokat a temp mappában.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Táblázat értékű függvények és tárolt eljárások tesztelése

A tábla értékű függvények és tárolt eljárások tesztelési eseteinek hozzáadása jelenleg nem támogatott. Megkerülő megoldásként létrehozhat egy u-SQL-projektet, amely olyan U-SQL-parancsfájlokat tartalmaz, amelyek meghívja ezeket a függvényeket, és tesztelési eseteket írnak rájuk. A következő lépésekkel állíthatja be a tábla értékű függvények és a U-SQL-adatbázis projektben definiált tárolt eljárások tesztelési eseteit:

1. Hozzon létre egy U-SQL-projektet tesztelési célra, és írjon U-SQL-szkripteket a táblázat értékű függvények és tárolt eljárások meghívásához.

1. Adjon hozzá egy adatbázis-hivatkozást a U-SQL projekthez. A tábla értékű függvény és a tárolt eljárás definíciójának lekéréséhez a DDL-utasítást tartalmazó adatbázis-projektre kell hivatkoznia. További információ az [adatbázis-hivatkozásokról](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Adja meg a tábla értékű függvényeket és tárolt eljárásokat meghívó U-SQL-parancsfájlok tesztelési eseteit. Útmutató a [U-SQL-parancsfájlok tesztelési eseteinek hozzáadásához](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>U-SQL-adatbázis üzembe helyezése az Azure-folyamatokon keresztül

`PackageDeploymentTool.exe` a olyan programozási és parancssori felületeket biztosít, amelyek segítenek az U-SQL Database központi telepítési csomagok, **. usqldbpack** üzembe helyezésében. Az SDK-t az [U-SQL SDK NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)tartalmazza, amely **Build/Runtime/PackageDeploymentTool.exe** helyen található. A használatával az `PackageDeploymentTool.exe` U-SQL-adatbázisokat Azure Data Lake Analytics és helyi fiókokra is telepítheti.

> [!NOTE]
>
> A PowerShell parancssori támogatás és az Azure-folyamatok kiadási feladatának támogatása a U-SQL-adatbázis üzembe helyezéséhez jelenleg függőben van.
>

Az alábbi lépéseket követve állíthatja be az adatbázis-telepítési feladatot az Azure-folyamatokban:

1. Adja hozzá a PowerShell-parancsfájl feladatát egy Build vagy Release folyamathoz, és hajtsa végre a következő PowerShell-parancsfájlt. Ez a feladat segít az Azure SDK függőségeinek beszerzésében a és a rendszerhez `PackageDeploymentTool.exe` `PackageDeploymentTool.exe` . A **-AzureSDK** és a **-DBDeploymentTool** paraméterek használatával betöltheti a függőségeket és a telepítési eszközt adott mappákba. A 2. lépésben adja át a- **AzureSDK** elérési utat `PackageDeploymentTool.exe` a **-AzureSDKPath** paraméternek.

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. Adja hozzá a **parancssori feladatot** egy Build vagy Release folyamathoz, és adja meg a parancsfájlt a hívásával `PackageDeploymentTool.exe` . `PackageDeploymentTool.exe` a a megadott **$DBDeploymentTool** mappában található. A minta parancsfájl a következő: 

   - U-SQL-adatbázis üzembe helyezése helyileg:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Egy U-SQL-adatbázis központi telepítése Azure Data Lake Analytics fiókba interaktív hitelesítési mód használatával:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - A **titkos** hitelesítés használatával helyezzen üzembe egy U-SQL-adatbázist egy Azure Data Lake Analytics fiókban:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - **Bizonyítványfájl** -hitelesítés használata U-SQL-adatbázis központi telepítése Azure Data Lake Analytics fiókba:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe paraméter leírása

#### <a name="common-parameters"></a>Gyakori paraméterek

| Paraméter | Leírás | Alapértelmezett érték | Kötelező |
|---------|-----------|-------------|--------|
|Csomag|A telepítendő U-SQL-adatbázis központi telepítési csomagjának elérési útja.|null|true|
|Adatbázis|A telepítendő vagy létrehozandó adatbázis neve.|master|hamis|
|Naplófájl|A fájl elérési útja a naplózáshoz. Alapértelmezett értéke a standard out (konzol).|null|hamis|
|Naplózási szint|Naplózási szint: részletes, normál, figyelmeztetés vagy hiba.|Naplózási szint. Normal|hamis|

#### <a name="parameter-for-local-deployment"></a>Paraméter a helyi telepítéshez

|Paraméter|Leírás|Alapértelmezett érték|Kötelező|
|---------|-----------|-------------|--------|
|DataRoot|A helyi adatgyökér mappájának elérési útja|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Azure Data Lake Analytics központi telepítésének paraméterei

|Paraméter|Leírás|Alapértelmezett érték|Kötelező|
|---------|-----------|-------------|--------|
|Fiók|Megadja, hogy melyik Azure Data Lake Analytics fiókot kell központilag telepíteni a fiók nevére.|null|true|
|ResourceGroup|Az Azure Data Lake Analytics fiók Azure-erőforráscsoport-neve.|null|true|
|SubscriptionId|A Azure Data Lake Analytics fiókhoz tartozó Azure-előfizetés azonosítója.|null|true|
|Bérlő|A bérlő neve a Azure Active Directory (Azure AD) tartomány neve. Keresse meg a Azure Portal előfizetés-kezelés lapján.|null|true|
|AzureSDKPath|Az Azure SDK-ban függő szerelvények keresésének elérési útja.|null|true|
|Interaktív|Azt határozza meg, hogy az interaktív módot használja-e a hitelesítéshez.|hamis|hamis|
|ClientID|A nem interaktív hitelesítéshez szükséges Azure AD-alkalmazás azonosítója.|null|Nem interaktív hitelesítéshez szükséges.|
|Titkos|A nem interaktív hitelesítéshez szükséges titkos kód vagy jelszó. Csak megbízható és biztonságos környezetben használható.|null|Nem interaktív hitelesítéshez szükséges, vagy más esetben használja a SecreteFile.|
|SecreteFile|A fájl elmenti a titkos kódot vagy a jelszót a nem interaktív hitelesítéshez. Ügyeljen arra, hogy csak az aktuális felhasználó legyen olvasható.|null|Nem interaktív hitelesítéshez szükséges, vagy más használja a titkos kulcsot.|
|Bizonyítványfájl|A fájl elmenti az X. 509 minősítést a nem interaktív hitelesítéshez. Az alapértelmezett érték az ügyfél titkos hitelesítésének használata.|null|hamis|
| JobPrefix | A U-SQL DDL-feladatok adatbázis-telepítésének előtagja. | Deploy_ + DateTime. Now | hamis |

## <a name="next-steps"></a>Következő lépések

- [A Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md).
- [Futtassa a U-SQL-szkriptet a helyi gépen](data-lake-analytics-data-lake-tools-local-run.md).
- [A u-SQL Database-projekt segítségével fejlesszen u-SQL-adatbázist](data-lake-analytics-data-lake-tools-develop-usql-database.md).