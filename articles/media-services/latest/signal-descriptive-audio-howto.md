---
title: A jel leíró hangsávok a Media Services v3
description: Kövesse az oktatóanyag lépéseit egy fájl feltöltéséhez, a videó kódolásához, a leíró hangsávok hozzáadásához és a tartalom továbbításához a Media Services v3 használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3d029f23a094646d20dd6ae8cb6560aeef4aed54
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954512"
---
# <a name="signal-descriptive-audio-tracks"></a>Jel leíró hangsávok

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Kísérőszöveget adhat hozzá a videóhoz, hogy a kísérőszöveggel Hallgassa meg a vizuálisan korlátozott ügyfeleket a videó rögzítésének követéséhez. A Media Services v3-as verzióban a leíró hangsávokat a manifest-fájlban szereplő hangfelvételi jelekkel jelezheti.

Ebből a cikkből megtudhatja, hogyan kódolhat egy videót, töltsön fel egy, a kimeneti eszközre mutató hangalapú MP4-fájlt (AAC kodeket), és szerkessze az. ISM fájlt, hogy tartalmazza a leíró hangot.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](./access-api-howto.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.
- Tekintse át a [dinamikus csomagolást](dynamic-packaging-overview.md).
- Tekintse át a [videók feltöltése, kódolása és továbbítása](stream-files-tutorial-with-api.md) című oktatóanyagot.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba 

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Ez az **eszköz** a kódolási feladatnak való bemenetként szolgál. A Media Services v3-as verziójában a **feladatba** való bevitel lehet egy **eszköz**, vagy olyan tartalom is lehet, amelyet a Media Services-fiókja HTTPS URL-címeken keresztül elérhetővé tesz. 

Ha szeretné megismerni a HTTPS URL-címekről történő kódolást, tekintse meg [ezt a cikket](job-input-from-http-how-to.md) .  

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket hajtja végre:

* Létrehoz egy **eszközt** 
* Egy írható [sas URL-cím](../../storage/common/storage-sas-overview.md) beolvasása az eszköz tárolójában a [tárolóban](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Ha a létrehozott bemeneti eszköz nevét más módszerekre kell átadnia, ügyeljen arra, hogy a (z `Name` ) tulajdonságot használja a visszaadott objektumhoz `CreateInputAssetAsync` , például inputAsset.name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Kimeneti eszköz létrehozása a kódolási feladatok eredményének tárolásához

A kimeneti [objektum](/rest/api/media/assets) tárolja a kódolási feladat eredményeit. A következő függvény azt mutatja be, hogyan hozható létre kimeneti eszköz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Ha a létrehozott kimeneti eszköz nevét más módszerekre kell átadnia, ügyeljen arra, hogy a (z `Name` ) tulajdonságot használja a visszaadott objektumhoz `CreateIOutputAssetAsync` , például outputAsset.name. 

A jelen cikk esetében adja át az `outputAsset.Name` értéket a `SubmitJobAsync` és a `UploadAudioIntoOutputAsset` függvénynek.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Hozzon létre egy átalakítót és egy olyan feladatot, amely kódolja a feltöltött fájlt

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha új feladatokat küld minden új videóhoz, akkor ezt a receptet a könyvtárában lévő összes videóra alkalmazza. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információt az [átalakításokkal és feladatokkal](./transforms-jobs-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre. 

A következő példa létrehoz egy átalakítót (ha az egyik nem létezik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

A következő függvény elküld egy feladatot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Javasoljuk, hogy a Event Grid használatával várjon, amíg a feladatok befejeződik.

A feladatok általában a következő állapotokon haladnak át: **ütemezett**, **várólistán** lévő, **feldolgozás**, **befejezett** (végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

További információ: [Event Grid események feldolgozása](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>A csak hang MP4-fájl feltöltése

Töltse fel a hangvezérelt hanganyagot tartalmazó további, hang-csak MP4-fájlt (AAC-kodeket) a kimeneti eszközbe.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Íme egy példa a függvény hívására `UpoadAudioIntoOutputAsset` :

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Az. ISM fájl szerkesztése

Ha elkészült a kódolási feladatokkal, a kimeneti eszköz fogja tartalmazni a kódolási feladatokból generált fájlokat. 

1. A Azure Portal navigáljon a Media Services-fiókjához társított Storage-fiókhoz. 
1. Keresse meg a tárolót a kimeneti eszköz nevével. 
1. A tárolóban keresse meg az. ISM fájlt, és kattintson a jobb oldali ablakban található **blob szerkesztése** elemre. 
1. Szerkessze az. ISM-fájlt úgy, hogy felveszi a deformatív hanganyagot tartalmazó feltöltött, csak hangalapú MP4-fájl (AAC kodek) adatait, és a **Mentés** gombra kattint.

    A leíró hangsávok megjelenítéséhez hozzá kell adnia a "kisegítő adatok" és a "szerepkör" paramétereket az. ISM-fájlhoz. Az Ön felelőssége, hogy ezeket a paramétereket helyesen adja meg, hogy hangvételt jelezzen a hanganyagok leírásaként. Például az `<param name="accessibility" value="description" />` `<param name="role" value="alternate" />` alábbi példában látható módon vegyen fel egy adott hangsávhoz tartozó. ISM-fájlt, és adjon hozzá egy adott hangsávot.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Folyamatos átviteli lokátor beszerzése

A kódolás befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben hajthatja végre: először hozzon létre egy [adatfolyam-keresőt](/rest/api/media/streaminglocators), és Másodszor hozza létre az ügyfelek által használható Streaming URL-címeket. 

Az **adatfolyam-kereső** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint az **adatfolyam-kereső** azonnal érvényes az API-hívások létrehozása után, és addig tart, amíg meg nem történik a törlés, hacsak nem konfigurálja a nem kötelező kezdési és befejezési időpontokat. 

A [StreamingLocator](/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben a példában a folyamatos átvitelt (vagy nem titkosított tartalmat) fogja használni, így az előre definiált tiszta adatfolyam-szabályzatot (**PredefinedStreamingPolicy. ClearStreamingOnly**) használja a rendszer.

> [!IMPORTANT]
> Ha egyéni [folyamatos átviteli szabályzatot](/rest/api/media/streamingpolicies)használ, meg kell terveznie az ilyen szabályzatok korlátozott készletét a Media Service-fiókjához, és újra fel kell használni azokat a StreamingLocators, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. A Media Service-fiókhoz tartozik egy kvóta a folyamatos átviteli szabályzat bejegyzéseinek számára. Ne hozzon létre új folyamatos átviteli szabályzatot minden egyes adatfolyam-keresőhöz.

Az alábbi kód azt feltételezi, hogy a függvényt egy egyedi locatorName objektummal hívja meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Míg a témakörben szereplő minta a streaminget tárgyalja, ugyanazt a hívást használhatja a videó továbbításához a progresszív letöltés használatával.

### <a name="get-streaming-urls"></a>Streamelési URL-címek lekérdezése

Most, hogy létrejött a [folyamatos átviteli lokátor](/rest/api/media/streaminglocators) , beolvashatja a streaming URL-címeket, ahogy az a **GetStreamingURLs**-ban is látható. URL-cím létrehozásához összefűzni kell a [streaming Endpoint](/rest/api/media/streamingendpoints) Host nevét és a **folyamatos átviteli lokátor** elérési útját. Ebben a példában az *alapértelmezett* **adatfolyam-végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, az *alapértelmezett* **folyamatos átviteli végpont** leállított állapotba kerül, ezért meg kell hívnia a **Start** parancsot.

> [!NOTE]
> Ebben a metódusban szüksége lesz a kimeneti eszköz **folyamatos átviteli lokátorának** létrehozásakor használt locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, és navigáljon a következőhöz: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL:** mezőben illessze be az alkalmazásból kapott streaming URL-értékeket. 
 
     Az URL-címet HLS, Dash vagy Smooth formátumban is beillesztheti, és a Azure Media Player a megfelelő folyamatos átviteli protokollra vált az eszközön való automatikus lejátszás érdekében.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="next-steps"></a>Következő lépések

[Videók elemzése](analyze-videos-tutorial-with-api.md)
