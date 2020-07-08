---
title: Apache Hive lekérdezések futtatása a HDInsight .NET SDK használatával – Azure
description: Megtudhatja, hogyan küldhet Apache Hadoop feladatokat az Azure HDInsight Apache Hadoop a HDInsight .NET SDK használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552491"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Apache Hive lekérdezések futtatása a HDInsight .NET SDK-val

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan küldhet Apache Hive-lekérdezéseket a HDInsight .NET SDK-val. Egy C#-programot ír a kaptár-táblák listázásához, és megjeleníti az eredményeket.

> [!NOTE]  
> A cikkben szereplő lépéseket Windows-ügyfélről kell végrehajtani. A Linux, OS X vagy UNIX rendszerű ügyfél a kaptárral való használatához való használatáról a cikk tetején látható Tab választót használhatja.

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt a következő elemeket kell megadnia:

* Egy Apache Hadoop-fürt a HDInsight-ben. Lásd: Ismerkedés [a Linux-alapú Hadoop a HDInsight-ben](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > 2017. szeptember 15-én a HDInsight .NET SDK csak az Azure Storage-fiókokból származó struktúra-lekérdezési eredmények visszaküldését támogatja. Ha ezt a példát egy olyan HDInsight-fürthöz használja, amely a Azure Data Lake Storage elsődleges tárolóként használja, a keresési eredmények nem kérhetők le a .NET SDK használatával.

* A [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013-es és újabb kiadásait. Legalább a munkaterhelés **.net Desktop fejlesztését** telepíteni kell.

## <a name="run-a-hive-query"></a>Struktúra-lekérdezés futtatása

A HDInsight .NET SDK .NET-ügyfélszoftvereket biztosít, ami megkönnyíti a HDInsight-fürtökkel való együttműködését a .NET-keretrendszerrel.

1. C# konzolos alkalmazás létrehozása a Visual Studióban.

1. A Nuget Package Manager konzolon futtassa a következő parancsot:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Szerkessze az alábbi kódot a változók értékeinek inicializálásához: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` . Ezután használja a felülvizsgált kódot a **program.cs** teljes tartalmára a Visual Studióban.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Az alkalmazás kimenetének a következőhöz hasonlónak kell lennie:

![HDInsight Hadoop-struktúra kimenete](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan küldhet Apache Hive lekérdezéseket a HDInsight .NET SDK-val. További információt a következő cikkekben talál:

* [Ismerkedés az Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK-dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Apache Sqoop használata a HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Nem interaktív hitelesítéssel ellátott .Net HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
