---
title: Fájlok feltöltése egy Media Services-fiókba .NET használatával | Microsoft Docs
description: Megtudhatja, hogyan szerezhet be médiatartalmakat Media Servicesba az eszközök .NET használatával történő létrehozásával és feltöltésével.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013722"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Fájlok feltöltése Media Services-fiókba a .NET használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. Az **eszköz** entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is).  A fájlok feltöltése után a rendszer biztonságosan tárolja a tartalmat a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Az adategységben található fájlokat **adategység-fájloknak** nevezzük. A **AssetFile** példány és a tényleges médiafájl két különálló objektum. A AssetFile-példány metaadatokat tartalmaz a médiafájlról, míg a médiafájl tartalmazza a tényleges médiatartalom tartalmát.

## <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe venni:
 
 * A Media Services a IAssetFile.Name tulajdonság értékét használja a streaming tartalom URL-címeinek létrehozásakor (például http://{AMSAccount}. Origin. Mediaservices. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Emiatt a százalékos kódolás nem engedélyezett. A **Name (név** ) tulajdonság értéke nem lehet a következő [százalék-kódolásra fenntartott karakterek](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)egyike:! * ' ();: @ &= + $,/?% # [] ". Emellett a fájlnévkiterjesztés csak egy "." lehet.
* A név hossza nem lehet nagyobb, mint 260 karakter.
* A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.
* A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Az eszközök létrehozásakor megadhatja a következő titkosítási beállításokat:

* **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Ha ezt a beállítást használja, a tartalmat a rendszer nem védi az átvitelben vagy a tárolás során.
  Ha az MP4-t a progresszív letöltés használatával tervezi, használja ezt a lehetőséget: 
* **CommonEncryption** – ezt a lehetőséget akkor használja, ha olyan tartalmat tölt fel, amely már titkosítva van, és Common encryption vagy PlayReady DRM-mel védett (például a PlayReady DRM-mel védett Smooth streaming).
* **EnvelopeEncrypted** – ezt a beállítást akkor használja, ha AES-titkosítással titkosított HLS tölt fel. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.
* **StorageEncrypted** – titkosítja a tartalmakat helyileg AES-256 bites titkosítással, majd feltölti az Azure Storage-ba, ahol a tárolása titkosított állapotban van. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
  
    A Media Services lemezes tárterület-titkosítást biztosít az adategységekhez, és nem több mint a digitális jogkezelési (DRM) eszközön.
  
    Ha az adategységen tárolótitkosítást alkalmaz, konfigurálnia kell az adategység továbbítási házirendjét. További információ: az [eszközök kézbesítési házirendjének konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

Ha azt adja meg, hogy az eszköz **CommonEncrypted** vagy **EnvelopeEncrypted** -beállítással legyen titkosítva, akkor az eszközt egy **ContentKey** kell rendelnie. További információt a [ContentKey létrehozása](media-services-dotnet-create-contentkey.md)című témakörben talál. 

Ha azt adja meg, hogy az eszköz **StorageEncrypted** -beállítással legyen titkosítva, a .net-hez készült Media Services SDK létrehoz egy **StorageEncrypted** - **ContentKey** az eszközhöz.

Ez a cikk bemutatja, hogyan használhatók a Media Services .NET SDK, valamint Media Services .NET SDK-bővítmények a fájlok Media Services eszközre való feltöltéséhez.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Egyetlen fájl feltöltése Media Services .NET SDK-val

A következő kód a .NET-et használja egyetlen fájl feltöltéséhez. A AccessPolicy és a lokátort a feltöltési függvény hozza létre és semmisíti meg. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Több fájl feltöltése Media Services .NET SDK-val
A következő kód bemutatja, hogyan hozhat létre egy objektumot, és hogyan tölthet fel több fájlt.

A kód a következő műveleteket végzi el:

* Egy üres eszközt hoz létre az előző lépésben meghatározott CreateEmptyAsset metódus használatával.
* Létrehoz egy **AccessPolicy** -példányt, amely meghatározza az eszközhöz való hozzáférés engedélyeit és időtartamát.
* Létrehoz egy **lokátor** -példányt, amely hozzáférést biztosít az eszközhöz.
* Létrehoz egy **BlobTransferClient** -példányt. Ez a típus olyan ügyfelet jelöl, amely az Azure-blobokon működik. Ebben a példában az ügyfél figyeli a feltöltés folyamatát. 
* A megadott könyvtár fájljain keresztül enumerálja a fájlokat, és minden fájlhoz létrehoz egy **AssetFile** -példányt.
* Feltölti a fájlokat Media Services a **UploadAsync** metódus használatával. 

> [!NOTE]
> A UploadAsync metódus használatával győződjön meg arról, hogy a hívások nem blokkolják a fájlokat, és a fájlok feltöltése párhuzamosan történik.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Nagy mennyiségű eszköz feltöltésekor vegye figyelembe a következőket:

* Hozzon létre egy új **csatlakozáshoz szükséges cloudmediacontext** objektumot egy szálon. A **csatlakozáshoz szükséges cloudmediacontext** osztály nem biztonságos.
* Növelje az NumberOfConcurrentTransfers az alapértelmezett 2 értékről magasabb értékre (például 5). A tulajdonság beállítása hatással van a **csatlakozáshoz szükséges cloudmediacontext** összes példányára. 
* Tartsa a ParallelTransferThreadCount az alapértelmezett 10 értéknél.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Eszközök tömeges betöltése Media Services .NET SDK használatával
A nagyméretű adatfájlok feltöltése szűk keresztmetszetet jelenthet az eszközök létrehozása során. Az adategységek tömeges vagy "tömeges betöltéssel" történő betöltése magában foglalja a feltöltési folyamat által létrehozott eszközök létrehozását. Ha tömeges betöltési módszert szeretne használni, hozzon létre egy olyan jegyzékfájlt (IngestManifest), amely leírja az eszközt és a hozzá tartozó fájlokat. Ezután használja a kívánt feltöltési módszert a kapcsolódó fájlok a jegyzékfájl blob-tárolóba való feltöltéséhez. Microsoft Azure Media Services figyeli a jegyzékfájlhoz társított BLOB-tárolót. Miután feltölt egy fájlt a blob-tárolóba, Microsoft Azure Media Services befejezi az eszköz létrehozását a jegyzékfájlban (IngestManifestAsset) lévő eszköz konfigurációja alapján.

Új IngestManifest létrehozásához hívja meg a IngestManifests gyűjtemény által elérhető Create metódust a csatlakozáshoz szükséges cloudmediacontext. Ez a metódus új IngestManifest hoz létre a megadott jegyzékfájl nevével.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Hozza létre a tömeges IngestManifest társított eszközöket. Konfigurálja a kívánt titkosítási beállításokat az eszközön a tömeges betöltéshez.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Az IngestManifestAsset egy ömlesztett IngestManifest rendelkező eszközt társít a tömeges betöltéshez. Emellett az egyes eszközöket alkotó AssetFiles is társítja. IngestManifestAsset létrehozásához használja a Create metódust a kiszolgálói környezetben.

Az alábbi példa két új IngestManifestAssets hozzáadását mutatja be, amelyek a korábban a tömeges betöltési jegyzékfájlhoz létrehozott két eszközt rendelik hozzá. Minden IngestManifestAsset a tömeges betöltés során az egyes eszközökre feltöltött fájlok készletét is társítja.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

A IngestManifest **IIngestManifest. BlobStorageUriForUpload** tulajdonsága által biztosított blob Storage-tároló URI-fájljához bármilyen nagy sebességű ügyfélalkalmazás feltölthető. 

A következő kód bemutatja, hogyan tölthetők fel az eszközök fájljai a .NET SDK használatával.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Az adategység fájljainak az ebben a cikkben használt mintához való feltöltésére szolgáló kód a következő kódrészletben látható:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

A **IngestManifest** statisztikai tulajdonságának lekérdezésével meghatározhatja, hogy a tömeges betöltés milyen hatással van a **IngestManifest** társított összes eszközre. Az előrehaladási információk frissítéséhez minden alkalommal új **csatlakozáshoz szükséges cloudmediacontext** kell használnia, amikor lekérdezi a statisztikai tulajdonságot.

Az alábbi példa bemutatja, hogyan kérdezi le az IngestManifest az **azonosítója** alapján.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Fájlok feltöltése a .NET SDK-bővítményekkel
Az alábbi példa bemutatja, hogyan tölthet fel egyetlen fájlt a .NET SDK-bővítmények használatával. Ebben az esetben a rendszer a **CreateFromFile** metódust használja, de az aszinkron verzió is elérhető (**CreateFromFileAsync**). A **CreateFromFile** metódussal megadhatja a fájl nevét, a titkosítási beállítást és a visszahívást, hogy jelentést készítsen a fájl feltöltési folyamatáról.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

A következő példa a UploadFile függvényt hívja meg, és a tárolási titkosítást adja meg az eszköz létrehozása lehetőségként.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Következő lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Most, hogy feltöltött egy adategységet Media Servicesre, lépjen a [Media Processor beszerzése][How to Get a Media Processor] című cikkhez.

[How to Get a Media Processor]: media-services-get-media-processor.md
