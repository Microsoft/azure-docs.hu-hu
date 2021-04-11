---
title: Tartalmak továbbítása igény szerint a .NET használatával | Microsoft Docs
description: Ez az útmutató lépésről lépésre ismerteti, hogyan valósíthat meg egy igény szerinti tartalomtovábbító alkalmazást a .NET-keretrendszert használó Azure Media Services segítségével.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7edee25c5b52ea1ba1092daa354a4523861bcbb5
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227109"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Tartalmak továbbítása igény szerint a .NET SDK használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure Media Services .NET SDK segítségével, az Azure Media Services (AMS) alkalmazással.

## <a name="prerequisites"></a>Előfeltételek

Az ismertetett eljárás végrehajtásához a következők szükségesek:

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* A .NET-keretrendszer 4.0-s vagy újabb verziója.
* A Visual Studióval.

Az oktatóanyag a következő feladatokat tartalmazza:

1. A streamvégpont elindítása (az Azure Portal használatával).
2. Egy Visual Studio-projekt létrehozása és konfigurálása.
3. A Media Services-fiókhoz való csatlakozás.
2. Videofájl feltöltése
3. A forrásfájl kódolása egy adaptív sávszélességű MP4-fájlsorozattá.
4. Az objektum közzététele, majd a streamelési és a progresszív letöltési URL-cím lekérése  
5. Tartalom lejátszása

## <a name="overview"></a>Áttekintés
Ez az útmutató lépésről lépésre bemutatja, hogyan valósíthat meg egy Video-on-Demand (VoD) tartalomtovábbító alkalmazást a .NET-keretrendszerhez készült Azure Media Services (AMS) SDK segítségével.

Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóprogram elvégzése után képes lesz adatfolyamot továbbítani vagy fokozatosan letölteni egy saját maga által feltöltött, kódolt és letöltött példa médiafájlt.

### <a name="ams-model"></a>AMS-modell

A következő kép a Media Services OData-modellen alapuló VoD-alkalmazásfejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

[![Diagram, amely a Azure Media Services Object adatmodell leggyakrabban használt objektumainak egy részét mutatja be a videók igény szerinti alkalmazásokban való fejlesztéséhez.](./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png)](./media/media-services-dotnet-get-started/media-services-overview-object-model.png#lightbox)

A teljes modellt [itt](https://m.eet.com/media/1170326/ms-part1.pdf) tekintheti meg.  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>A streamvégpont elindítása az Azure Portal használatával

Az Azure Media Services egyik leggyakrabban használt funkciója a videók továbbítása az adaptív sávszélességű streamelés használatával. A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

A streamvégpont elindításához tegye a következőket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/).
2. Kattintson a Settings (Beállítások) ablak Streaming endpoints (Streamvégpontok) elemére.
3. Kattintson az alapértelmezett streamvégpontra.

    Megjelenik a DEFAULT STREAMING ENDPOINT DETAILS (Alapértelmezett streamvégpont adatai) ablak.

4. Kattintson a Start ikonra.
5. Mentse a módosításokat a Save (Mentés) gombra kattintva.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 
2. Hozzon létre egy új mappát (a mappa a helyi meghajtón bárhol lehet), és másoljon bele egy .mp4-fájlt, amelyet szeretne kódolni vagy fokozatosan letölteni. Ebben a példában a „C:\VideoFiles” elérési utat használjuk.

## <a name="connect-to-the-media-services-account"></a>Csatlakozás a Media Services-fiókhoz

A .NET-keretrendszerű Media Services-szolgáltatások használatakor a **CloudMediaContext** osztályt kell használnia a legtöbb Media Services-programozási feladathoz – ilyenek például a Media Services-fiókhoz való csatlakozás, továbbá az adategységek, adategységfájlok, feladatok, hozzáférési házirendek, keresők és egyebek létrehozása, frissítése, elérése és törlése.

Írja felül az alapértelmezett Program osztályt a következő kóddal: A kód bemutatja, hogyan olvashatja be a csatlakozási értékeket az App.config fájlból, és hogyan hozhatja létre a Media Services-csatlakozáshoz szükséges **CloudMediaContext** objektumot. További információ: [Connecting to the Media Services API](media-services-use-aad-auth-to-access-ams-api.md) (Csatlakozás a Media Services API-hoz).

Ne feledje frissíteni a fájl nevét és elérési útvonalát a médiafájl helyének megfelelően.

A **Fő** függvény olyan módszereket hív meg, amelyek jelen szakasz során később lesznek meghatározva.

> [!NOTE]
> Amíg nem veszi fel a jelen cikk későbbi szakaszaiban meghatározott összes függvénydefiníciót, a rendszer fordítási hibát jelez.

```csharp
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

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Új adategység létrehozása és videofájl feltöltése

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. Az **eszköz** entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is).  A fájlok feltöltése után a rendszer biztonságosan tárolja a tartalmat a felhőben a további feldolgozáshoz és folyamatos átvitelhez. Az adategységben található fájlokat **adategység-fájloknak** nevezzük.

Az alábbiakban meghatározott **UploadFile** módszer a **CreateFromFile** módszert hívja meg (amely a .NET SDK-bővítmények között van meghatározva). A **CreateFromFile** létrehoz egy új adategységet, amelybe a megadott forrásfájl fel lesz töltve.

A **CreateFromFile** metódus **assetcreationoptions alapján határozhatja** fogad, amely lehetővé teszi a következő eszköz-létrehozási lehetőségek egyikének megadását:

* **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Ügyeljen arra, hogy ezen lehetőség használatakor a tartalom sem átvitel, sem tárolás közben nincs védve.
  Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
* **StorageEncrypted** – Ezen lehetőség használatakor a tiszta tartalom helyileg, 256 bites Advanced Encryption Standard (AES) titkosítással lesz titkosítva, és így kerül feltöltésre az Azure Storage tárolóba, ahol titkosítva lesz tárolva. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
* **CommonEncryptionProtected** – Használja ezt a lehetőséget, ha olyan tartalmat tölt fel, amely már korábban titkosítva és védve lett általános titkosítás vagy a PlayReady DRM által (például egy PlayReady DRM titkosítással védett Smooth Streaming-fájlt).
* **EnvelopeEncryptionProtected** – Használja ezt a lehetőséget, ha AES által titkosított HLS tartalmakat tölt fel. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.

A **CreateFromFile** módszer használatával egy visszahívást is megadhat, amely visszajelzést ad a fájl feltöltési folyamatáról.

A következő példában az adategység lehetőségei közt a **Nincs** választ adtuk meg.

Adja hozzá a Program osztályhoz a következő módszert.

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

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Forrásfájl kódolása adaptív sávszélességű MP4-fájlsorozattá
Miután az adategységek bevitele a Media Services szolgáltatásba megtörtént, a médiatartalmak többek között kódolhatók, transzmultiplexálás végezhető rajtuk vagy vízjelezhetők, mielőtt továbbítva lennének az ügyfelek felé. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük. Minden egyes feladat több részműveletből áll, ezek végzik el a valódi munkát az adategységfájlon.

Mint azt korábban már említettük, az Azure Media Services használatának egyik leggyakoribb forgatókönyve az adaptív sávszélességű streamelés az ügyfelek felé. A Media Services az adaptív sávszélességű MP4-fájlokat a következő formátumokba tudja dinamikusan csomagolni: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH.

A dinamikus csomagolás kihasználásához kódolja a mezzanine forrásfájlt egy adaptív sávszélességű MP4- vagy Smooth Streaming-fájlsorozattá.  

A következő kód bemutatja, hogyan küldhet el egy kódolási feladatot. A feladat egyetlen műveletet tartalmaz, amely azért felel, hogy a mezzazine-fájlt egy adaptív sávszélességű MP4-fájlsorozattá kódolódjon át a **Media Encoder Standard** használatával. A kód elküldi a feladatot, és vár, amíg az befejeződik.

Ha a feladat befejeződött, lehetővé válik az adategység továbbítása vagy az átkódolás során létrejött MP4-fájlok progresszív letöltése.

Adja hozzá a Program osztályhoz a következő módszert.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Adatkészlet közzététele és az adatfolyam-továbbításhoz és progresszív letöltéshez szükséges URL-címek lekérése

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a hozzáférési jogosultságkód (SAS)-keresők pedig médiafájlok letöltéséhez.

### <a name="some-details-about-url-formats"></a>Néhány információ az URL-formátumokról

A keresők létrehozása után összeállíthatja a fájlok továbbításához vagy letöltéséhez használandó URL-címeket. Az oktatóanyagban lévő minta kimenetei olyan URL-címek, amelyek a megfelelő böngészőkbe beilleszthetők. Ez a szakasz néhány rövid példán mutatja be a különféle formátumokat.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Egy MPEG DASH-továbbítási URL-címnek a következő formátumban kell lennie:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Egy HLS-továbbítási URL-címnek a következő formátumban kell lennie:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Egy Smooth Streaming URL-címnek a következő formátumban kell lennie:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Egy fájlok letöltéséhez használt SAS URL-címnek a következő formátumban kell lennie:

{blob-tároló neve}/{adategység neve}/{fájlnév}/{SAS-aláírás}

A Media Services .NET SDK-bővítmények olyan kényelmes segédmódszereket biztosítanak, amelyek formázott URL-címeket adnak meg a közzétett adategységekhez.

A következő kód .NET SDK-bővítmények használatával hoz létre keresőket és kér le adatfolyam-továbbítási és progresszív letöltési URL-címeket. A kód azt is bemutatja, hogyan tölthetők le fájlok egy helyi mappába.

Adja hozzá a Program osztályhoz a következő módszert.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>A tartalom lejátszhatóságának tesztelése

Az előző szakaszban meghatározott program futtatásakor a konzolablakban a következőkhöz hasonló URL-címek jelennek meg:

Adaptív adatfolyam-továbbítási URL-címek:

Smooth Streaming

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

Fokozatos letöltési URL-címek (audió és videó).

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`


A videótovábbításhoz illessze be az URL-címet az [Azure Media Services Player](https://aka.ms/azuremediaplayer) lejátszó URL szövegmezőjébe.

A progresszív letöltés teszteléséhez másoljon egy URL-címet a böngészőjébe (például az Internet Explorerbe, Chrome-ba vagy Safariba).

További információkért tekintse át a következők témaköröket:

- [A tartalom lejátszása meglévő lejátszókkal](media-services-playback-content-with-existing-players.md)
- [MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Minta letöltése
Az oktatóanyagban létrehozott kódot a következő kód tartalmazza: [minta](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
