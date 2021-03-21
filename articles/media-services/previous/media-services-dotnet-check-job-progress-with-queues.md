---
title: Az Azure üzenetsor-tároló használata Media Services feladatok értesítéseinek figyeléséhez a .NET-tel | Microsoft Docs
description: Megtudhatja, hogyan használhatja az Azure üzenetsor-tárolót Media Services feladatok értesítéseinek figyelésére. A kód mintája C# nyelven íródott, és a .NET-hez készült Media Services SDK-t használja.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: dc6ab94b3685e55125032b0afa52076eae72ba5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016219"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Media Services feladatok értesítéseinek figyelése az Azure üzenetsor-tárolóval a .NET használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A kódolási feladatok futtatásakor gyakran szükség van a feladat előrehaladásának nyomon követésére. A Media Services konfigurálható úgy, hogy értesítéseket továbbítson az [Azure üzenetsor-tárolóba](../../storage/queues/storage-dotnet-how-to-use-queues.md). A feladatok előrehaladását úgy figyelheti, hogy beolvassa az értesítéseket a várólista-tárolóból. 

A várólista-tárolóba küldött üzenetek a világ bármely pontján elérhetők. A várólista-tároló üzenetkezelési architektúrája megbízható és rugalmasan méretezhető. Az üzenetek lekérdezési várólistán való tárolását más módszerekkel ajánlott használni.

A Media Services értesítések figyelésének egyik gyakori forgatókönyve, ha olyan tartalomkezelő rendszer fejlesztését végzi, amely egy kódolási feladat befejezése után további feladatokat kell elvégeznie (például egy munkafolyamat következő lépésének elindításához vagy a tartalom közzétételéhez).

Ez a cikk bemutatja, hogyan kérhet le értesítési üzeneteket a várólista-tárolóból.  

## <a name="considerations"></a>Megfontolandó szempontok
A várólista-tárolót használó Media Services alkalmazások fejlesztésekor vegye figyelembe a következőket:

* A várólista-tárolás nem garantálja, hogy az első kimenő (FIFO) rendezett kézbesítés nem garantált. További információ: az [Azure Queues és a Azure Service Bus Queues](/previous-versions/azure/hh767287(v=azure.100))összevetése és összehasonlítása.
* A várólista-tároló nem leküldéses szolgáltatás. Le kell kérdezni a várólistát.
* Tetszőleges számú várólistát használhat. További információ: üzenetsor- [kezelési REST API](/rest/api/storageservices/queue-service-rest-api).
* A várólista-tároló bizonyos korlátozásokkal és sajátosságokkal rendelkezik. Ezeket az [Azure Queues és a Azure Service Bus Queues összevetése és kontrasztos](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)összevetése írja le.

## <a name="net-code-example"></a>.NET-kód – példa

Az ebben a szakaszban szereplő kódrészlet a következő műveleteket végzi el:

1. Meghatározza a **EncodingJobMessage** osztályt, amely az értesítési üzenet formátumára van leképezve. A kód deszerializálja a sorból fogadott üzeneteket a **EncodingJobMessage** -típus objektumaiba.
2. Betölti a Media Services és a Storage-fiók adatait a app.config fájlból. A kód példa ezt az információt használja a **csatlakozáshoz szükséges cloudmediacontext** és a **CloudQueue** objektumok létrehozásához.
3. Létrehozza azt a várólistát, amely értesítési üzeneteket fogad a kódolási feladatokról.
4. Létrehozza a várólistára leképezett értesítési végpontot.
5. Csatolja az értesítés végpontját a feladatokhoz, és elküldi a kódolási feladatot. A feladatokhoz több értesítési végpont is tartozhat.
6. A **NotificationJobState. FinalStatesOnly** átadja a **AddNew** metódusnak. (Ebben a példában csak a feladatok feldolgozásának végső állapota érdekli.)

    ```csharp
    job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
    ```

7. Ha átadja a **NotificationJobState. All**-t, a következő állapotváltozás-értesítések mindegyike megjelenik: üzenetsor, ütemezett, feldolgozás és Befejezés. A korábban említettek szerint azonban a várólista-tároló nem garantálja a rendezett kézbesítést. Az üzenetek megrendeléséhez használja az **időbélyegző** tulajdonságot (az alábbi példában az **EncodingJobMessage** típusban van megadva). Ismétlődő üzenetek is lehetségesek. Az ismétlődések kereséséhez használja a **ETAG tulajdonságot** (a **EncodingJobMessage** típusban definiálva). Az is lehetséges, hogy egyes állapot-módosítási értesítések kimaradnak.
8. Megvárja, amíg a feladatoknak kész állapotba kell jutnia a várólista 10 másodpercenkénti ellenőrzésével. A feldolgozás után törli az üzeneteket.
9. Törli az üzenetsor és az értesítési végpontot.

> [!NOTE]
> A feladatok állapotának figyelésére ajánlott módszer az értesítési üzenetek figyelése, ahogy az az alábbi példában is látható:
>
> Azt is megteheti, hogy a **IJob. State** tulajdonság használatával a feladatok állapotát is megtekintheti.  A feladatok befejezésére vonatkozó értesítési üzenet a **IJob** állapotának **Befejezettre** állítása előtt is megérkezik. A **IJob. State**  tulajdonság a pontos állapotot mutatja kis késleltetéssel.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 
2. Hozzon létre egy új mappát (a mappa bárhol lehet a helyi meghajtón), és másoljon egy. MP4-fájlt, amelyet szeretne kódolni és továbbítani vagy fokozatosan letölteni. Ebben a példában a "C:\Media" útvonalat használja a rendszer.
3. Adjon hozzá egy hivatkozást a **System. Runtime. szerializálási** könyvtárhoz.

### <a name="code"></a>Code

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Az előző példa a következő kimenetet hozta létre: az értékek eltérőek lesznek.

```output
Created assetFile BigBuckBunny.mp4
Upload BigBuckBunny.mp4
Done uploading of BigBuckBunny.mp4

EventType: NotificationEndPointRegistration
MessageVersion: 1.0
ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
TimeStamp: 2013-05-14T20:22:37
    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
    State: Registered
    Name: dde957b2-006e-41f2-9869-a978870ac620
    Created: 2013-05-14T20:22:35

EventType: JobStateChange
MessageVersion: 1.0
ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
TimeStamp: 2013-05-14T20:24:40
    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
    JobName: My MP4 to Smooth Streaming encoding job
    NewState: Finished
    OldState: Processing
    AccountName: westeuropewamsaccount
job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
State: Finished
```

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
