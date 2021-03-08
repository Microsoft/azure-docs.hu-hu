---
title: Azure Notification Hubs-regisztrációk tömeges exportálása és importálása | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Notification Hubs tömeges támogatást nagy számú művelet végrehajtásához egy értesítési központban, illetve az összes regisztráció exportálásához.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 73c19b72c75d9638213f3c813a708a272cbe2a1b
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452339"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Azure Notification Hubs-regisztrációk tömeges exportálása és importálása

Vannak olyan helyzetek, amikor nagy számú regisztrációt kell létrehozni vagy módosítani egy értesítési központban. Ezen forgatókönyvek némelyike a Batch-számításokat követő, vagy egy meglévő leküldéses implementáció áttelepítését teszi lehetővé az Azure Notification Hubs használatához.

Ez a cikk azt ismerteti, hogyan hajtható végre nagy számú művelet egy értesítési központban, vagy az összes regisztrációt tömegesen exportálhatja.

## <a name="high-level-flow"></a>Magas szintű folyamat

A Batch-támogatás úgy lett kialakítva, hogy támogassa a hosszú ideig futó, több millió regisztrációt igénylő feladatokat. A skála megvalósítása érdekében a Batch-támogatás az Azure Storage használatával tárolja a feladatok részleteit és kimeneteit. Tömeges frissítési műveletek esetén a felhasználónak egy blob-tárolóban kell létrehoznia egy fájlt, amelynek tartalma a regisztrációs frissítési műveletek listája. A feladatok indításakor a felhasználó a bemeneti blob URL-címét, valamint egy kimeneti könyvtár URL-címét (blob-tárolóban is) jeleníti meg. A feladatoknak a megkezdése után a felhasználó megtekintheti az állapotot a feladatokhoz megadott URL-cím lekérdezésével. Egy adott feladathoz csak adott típusú műveletek hajthatók végre (létrehozás, frissítés vagy törlés). Az exportálási műveletek végrehajtása analogously történik.

## <a name="import"></a>Importálás

### <a name="set-up"></a>Beállítás

Ez a szakasz feltételezi, hogy rendelkezik a következő entitásokkal:

- Egy kiépített értesítési központ.
- Egy Azure Storage blob-tároló.
- Az [Azure Storage NuGet-csomagra](https://www.nuget.org/packages/windowsazure.storage/) és [Notification Hubs NuGet-csomagra](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)mutató hivatkozások.

### <a name="create-input-file-and-store-it-in-a-blob"></a>Bemeneti fájl létrehozása és tárolása blobban

A bemeneti fájl tartalmazza az XML-ben szerializált regisztrációk listáját, soronként egy sorba. Az Azure SDK-val a következő mintakód szemlélteti a regisztrációk szerializálását és a blob-tárolóba való feltöltését:

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Az előző kód szerializálja a regisztrációkat a memóriában, majd feltölti a teljes streamet egy blobba. Ha több, mint néhány megabájtos fájlt töltött fel, tekintse meg az Azure Blob útmutatását a lépések végrehajtásáról. Például tiltsa le a [blobokat](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>URL-tokenek létrehozása

A bemeneti fájl feltöltése után hozza elő az URL-címeket, hogy a bemeneti fájl és a kimeneti könyvtár egyaránt megadja az értesítési központot. A bemenetekhez és kimenetekhez két különböző BLOB-tárolót használhat.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Feladat küldése

A két bemeneti és kimeneti URL-cím segítségével most már elindíthatja a Batch-feladatot.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

A bemeneti és a kimeneti URL-címeken kívül ez a példa egy objektumot `NotificationHubJob` tartalmazó objektumot hoz létre `JobType` , amely a következő típusok egyike lehet:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

A hívás befejezését követően az értesítési központ folytatja a feladatot, és megtekintheti annak állapotát a [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync)hívásával.

A feladatok elvégzése után a kimeneti könyvtár következő fájljainak megtekintésével ellenőrizheti az eredményeket:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Ezek a fájlok tartalmazzák a Batch sikeres és sikertelen műveleteinek listáját. A fájlformátum `.cvs` , amelyben minden sorban szerepel az eredeti bemeneti fájl sorszáma, valamint a művelet kimenete (általában a létrehozott vagy frissített regisztráció leírása).

### <a name="full-sample-code"></a>Teljes mintakód

Az alábbi mintakód egy értesítési központba importálja a regisztrációkat.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exportálás

A regisztráció exportálásának az importáláshoz hasonlónak kell lennie, a következő eltérésekkel:

- Csak a kimeneti URL-címet kell megadnia.
- Létre kell hoznia egy ExportRegistrations típusú NotificationHubJob.

### <a name="sample-code-snippet"></a>Minta kódrészlet

A következő kódrészlet a regisztrációk exportálására szolgál a javában:

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Következő lépések

A regisztrációval kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Regisztrációs címkék](notification-hubs-tags-segment-push-message.md)
- [Sablonregisztrációk](notification-hubs-templates-cross-platform-push-messages.md)
