---
title: Az Apache Sqoop-feladatok futtatása a PowerShell és az Azure HDInsight használatával
description: Megtudhatja, hogyan használhatja az Azure Powershellt egy munkaállomásról futtatása az Apache Sqoop-importálás és exportálása az Apache Hadoop-fürt és a egy Azure SQL database között.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 49375b00607f1f23a99855f8b80f5f5c3c4dac22
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686660"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Az Apache Sqoop-feladatok futtatása HDInsight az Apache Hadoophoz készült Azure PowerShell használatával
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Útmutató egy HDInsight-fürt és a egy Azure SQL database vagy SQL Server-adatbázis közötti adatok importálása és exportálása az Azure HDInsight az Apache sqoop használatával feladatok futtatása az Azure PowerShell használatával. Ebben a példában adatokat exportál `/tutorials/usesqoop/data/sample.log` az alapértelmezett tárfiókot, és importálja, hogy a tábla nevű `log4jlogs` SQL Server-adatbázisban. Ez a cikk a fenntartása [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Egy munkaállomás Azure PowerShell-lel [AZ modul](https://docs.microsoft.com/powershell/azure/overview) telepítve.

* Megvalósításának [tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) a [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](./hdinsight-use-sqoop.md).


## <a name="run-apache-sqoop-by-using-powershell"></a>Az Apache sqoop használatával futtassa a PowerShell használatával
A következő PowerShell-parancsfájl előzetesen feldolgozza a forrásfájl és majd exportálja azt egy Azure SQL database tábla `log4jlogs`. Cserélje le `CLUSTERNAME`, `CLUSTERPASSWORD`, és `SQLPASSWORD` azokra az értékekre, az előfeltételként szükséges használták.

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
Linux-alapú HDInsight mutat be a következő korlátozások vonatkoznak:

* Tömeges exportálását: Amellyel a Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása a Sqoop-összekötő jelenleg nem támogatja a tömeges beszúrás.

* Kötegelés: Használatával a `-batch` mikor váltson végrehajt a beszúrások, a sqoop használatával hajt végre több beszúrás helyett a beszúrási műveletek kötegelése. 

## <a name="next-steps"></a>További lépések
Most már megtanulhatta, hogyan használható a sqoop használatával. További tudnivalókért lásd:

* [Az Apache Oozie használata a HDInsight](../hdinsight-use-oozie-linux-mac.md): Az Oozie-munkafolyamatokkal Sqoop műveletet használja.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): Keresse meg a HDInsight vagy Azure Blob storage-ba történő feltöltéséhez más módszerekkel.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
