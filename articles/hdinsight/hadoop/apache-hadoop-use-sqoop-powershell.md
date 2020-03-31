---
title: Apache Sqoop-feladatok futtatása a PowerShell és az Azure HDInsight használatával
description: Ismerje meg, hogyan használhatja az Azure PowerShellt munkaállomásról az Apache Sqoop importálásának és exportálásának futtatásához egy Apache Hadoop-fürt és egy Azure SQL-adatbázis között.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: f39b595adf249b7412cb9b6b48f86b6fbd2c5e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263404"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Apache Sqoop-feladatok futtatása az Azure PowerShell for Apache Hadoop használatával a HDInsightban

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Megtudhatja, hogy az Azure PowerShell használatával hogyan futtathatja az Apache Sqoop-feladatokat az Azure HDInsightban egy HDInsight-fürt és egy Azure SQL-adatbázis vagy EGY Azure SQL Server-adatbázis közötti adatok importálásához és exportálásához.  Ez a cikk az Apache Sqoop használatának folytatása a [Hadoop-mal a HDInsight-ban.](./hdinsight-use-sqoop.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy munkaállomás, amelyen telepítve van az Azure PowerShell [AZ modul.](https://docs.microsoft.com/powershell/azure/overview)

* A [Tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) az [Apache Sqoop használata a Hadoop segítségével a HDInsight ban](./hdinsight-use-sqoop.md).

* A Sqoop ismerete. További információ: [Sqoop User Guide](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="sqoop-export"></a>Sqoop export

A Hive-tól az SQL Serverkiszolgálóig.

Ez a példa exportálja `hivesampletable` az `mobiledata` adatokat a Hive táblából az SQL Database táblájába. Állítsa be az alábbi változók értékeit, majd hajtsa végre a parancsot.

```powershell
$hdinsightClusterName = ""
$httpPassword = ''
$sqlDatabasePassword = ''

# These values only need to be changed if the template was not followed.
$httpUserName = "admin"
$sqlServerLogin = "sqluser"
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerName.database.windows.net;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# start export
New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table mobiledata --hcatalog-table hivesampletable" `
    | Start-AzHDInsightJob `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential
```

### <a name="alternative-execution"></a>Alternatív végrehajtás

1. Az alábbi kód ugyanazt az exportálást hajtja végre; azonban lehetővé teszi a kimeneti naplók olvasását. Az exportálás megkezdéséhez hajtsa végre a kódot.

    ```powershell
    $sqoopCommand = "export --connect $connectionString --table mobiledata --hcatalog-table hivesampletable"
    
    $sqoopDef = New-AzHDInsightSqoopJobDefinition `
        -Command $sqoopCommand
    
    $sqoopJob = Start-AzHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef
    ```

1. Az alábbi kód a kimeneti naplókat jeleníti meg. Hajtsa végre az alábbi kódot:

    ```powershell
    Get-AzHDInsightJobOutput `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Get-AzHDInsightJobOutput `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    ```

Ha a hibaüzenet megjelenik, `The specified blob does not exist.`próbálkozzon újra néhány perc múlva.

## <a name="sqoop-import"></a>Sqoop import

Az SQL Servertől az Azure Storage-ig. Ez a példa `mobiledata` adatokat importál az `wasb:///tutorials/usesqoop/importeddata` SQL Database táblájából a HDInsight könyvtárába. Az adatok mezőit tabulátorkarakter választja el egymástól, a sorokat pedig egy új sorkarakter szakítja meg. Ez a példa feltételezi, hogy befejezte a korábbi példát.

```powershell
$sqoopCommand = "import --connect $connectionString --table mobiledata --target-dir wasb:///tutorials/usesqoop/importeddata --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1"


$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command $sqoopCommand

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Get-AzHDInsightJobOutput `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Get-AzHDInsightJobOutput `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

```

## <a name="additional-sqoop-export-example"></a>További Sqoop exportálási példa

Ez egy megbízható példa, `/tutorials/usesqoop/data/sample.log` amely adatokat exportál az alapértelmezett tárfiókból, majd importálja azSQL Server-adatbázisban hívott `log4jlogs` táblába. Ez a példa nem függ a korábbi példáktól.

A következő PowerShell-parancsfájl elődolgozza a forrásfájlt, majd exportálja `log4jlogs`egy Azure SQL-adatbázisba a táblába. Cserélje `CLUSTERNAME` `CLUSTERPASSWORD`le `SQLPASSWORD` a , és az előfeltételből használt értékekre.

```powershell
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Korlátozások

A Linux-alapú HDInsight a következő korlátozásokat mutatja be:

* Tömeges exportálás: A Sqoop-összekötő, amely adatok Microsoft SQL Server vagy Azure SQL Database exportálására szolgál, jelenleg nem támogatja a tömeges beszúrásokat.

* Kötegelés: A `-batch` kapcsoló használatával, amikor lapkákat hajt végre, a Sqoop több lapkát hajt végre a lapkaműveletek kötegelése helyett.

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan kell használni Sqoop. További tudnivalókért lásd:

* [Az Apache Oozie használata a HDInsight segítségével:](../hdinsight-use-oozie-linux-mac.md)Sqoop művelet használata Oozie-munkafolyamatokban.
* [Adatok feltöltése a HDInsightba:](../hdinsight-upload-data.md)Más módszereket is kereshet az adatok HDInsightba vagy az Azure Blob storage-ba való feltöltéséhez.
