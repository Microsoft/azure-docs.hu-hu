---
title: MapReduce-feladatok elküldése a HDInsight .NET SDK-val – Azure
description: Ismerje meg, hogyan küldhet MapReduce-feladatokat az Azure HDInsight Apache Hadoop a HDInsight .NET SDK használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76157050"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce feladatok futtatása HDInsight .NET SDK segítségével

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ismerje meg, hogyan küldhet MapReduce-feladatokat a HDInsight .NET SDK-val. A HDInsight-fürtök egy jar-fájllal rendelkeznek, néhány MapReduce-mintával. A jar-fájl `/example/jars/hadoop-mapreduce-examples.jar`.  A minták egyike a **WordCount**. WordCount-feladatok elküldéséhez C#-konzol alkalmazást fejleszthet.  A művelet beolvassa a `/example/data/gutenberg/davinci.txt` fájlt, és kiírja az `/example/data/davinciwordcount`eredményeket a következőre:.  Ha újra szeretné futtatni az alkalmazást, meg kell tisztítania a kimeneti mappát.

> [!NOTE]  
> A cikkben szereplő lépéseket Windows-ügyfélről kell végrehajtani. A Linux, OS X vagy UNIX rendszerű ügyfél a kaptárral való használatához való használatáról a cikk tetején látható Tab választót használhatja.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce-feladatok elküldése a HDInsight .NET SDK-val

A HDInsight .NET SDK .NET-ügyfélszoftvereket biztosít, amelyek megkönnyítik a .NET-HDInsight-fürtökkel való munkát.

1. Indítsa el a Visual studiót, és hozzon létre egy C# konzolos alkalmazást.

1. Lépjen az **eszközök** > **NuGet Package** > Manager**csomagkezelő konzolra** , és írja be a következő parancsot:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Másolja az alábbi kódot a **program.cs**. `existingClusterName`Ezután szerkessze a kódot a következő értékek megadásával `existingClusterPassword`: `defaultStorageAccountName`, `defaultStorageAccountKey`,, `defaultStorageContainerName`és.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

A művelet ismételt futtatásához módosítania kell a feladatok kimeneti mappájának nevét a mintában `/example/data/davinciwordcount`.

Amikor a feladatok sikeresen befejeződik, az alkalmazás kinyomtatja a kimeneti fájl `part-r-00000`tartalmát.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre HDInsight-fürtöt. További információt a következő cikkekben talál:

* A kaptár-feladatok elküldéséhez lásd: [Apache Hive lekérdezések futtatása a HDInsight .net SDK használatával](apache-hadoop-use-hive-dotnet-sdk.md).
* A HDInsight-fürtök létrehozásával kapcsolatban lásd: [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md).
* A HDInsight-fürtök kezelésével kapcsolatban lásd: [Apache Hadoop-fürtök kezelése a HDInsight-ben](../hdinsight-administer-use-portal-linux.md).
* A HDInsight .NET SDK megismeréséhez tekintse meg a [HDInsight .net SDK dokumentációját](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Az Azure-ban nem interaktív hitelesítéssel kapcsolatban lásd: [nem interaktív hitelesítésű .net HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).