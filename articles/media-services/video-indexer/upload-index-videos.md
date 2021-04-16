---
title: 'Példa: Videók feltöltése és indexelése a Video Indexerrel'
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használhat API-kat a videók feltöltésére és indexelésére a Video Indexerrel.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 90fca4342b1fe04adef97a1a4c1c2166ca7ec51e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532481"
---
# <a name="upload-and-index-your-videos"></a>Videók feltöltése és indexelése  

A videó feltöltése után a Video Indexer (opcionálisan) kódolja a videót (ezt a cikkben tárgyaljuk). A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőség használata esetén létre kell hoznia egy Video Indexer-fiókot, amely [össze van kapcsolva az Azure-előfizetésével és egy Azure Media Services-fiókkal](connect-to-azure.md). Az indexelt percekért fizet, további információért tekintse meg a [díjszabást Media Services.](https://azure.microsoft.com/pricing/details/media-services/)

Amikor videókat tölt fel a Video Indexer API-val, az alábbi feltöltési lehetőségek állnak rendelkezésére: 

* videó feltöltése egy URL-címről (előnyben részesített),
* a videofájl küldése bájttömbként a kérelem törzsében,
* A meglévő Azure Media Services-objektum használata az [objektumazonosító](../latest/assets-concept.md) megadásával (csak a fizetős fiókokban támogatott).

A cikk bemutatja, hogyan tölthet fel és indexelhet videókat az alábbi beállításokkal:

* [A Video Indexer webhely](#upload-and-index-a-video-using-the-video-indexer-website) 
* [A Video Indexer API-k](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Támogatott fájlformátumok a Video Indexer

A [tárolókhoz és fájlokhoz](../latest/encode-media-encoder-standard-formats-reference.md) használható fájlformátumok listáját a bemeneti tárolók/fájlformátumok Video Indexer.

## <a name="video-files-storage"></a>Videofájlok tárolása

- A fizetős Video Indexer fiókkal létrehozhat egy azure Video Indexer- és egy Azure Media Services fiókot. További információ: [Azure-hoz Video Indexer fiók létrehozása.](connect-to-azure.md)
- A videófájlokat a rendszer az Azure Storage-ban Azure Media Services. Nincs időkorlát.
- A videó- és hangfájlokat, valamint az abból kinyert metaadatokat és elemzéseket bármikor törölheti Video Indexer. Miután töröl egy fájlt a Video Indexer, a fájl, annak metaadatai és elemzési adatai véglegesen el lesznek távolítva a Video Indexer. Ha azonban saját biztonsági mentési megoldást valósított meg az Azure Storage-ban, a fájl az Azure Storage-ban marad.
- A videók állandósodása azonos, függetlenül attól, hogy a feltöltés megtörtént-e a Video Indexer vagy az Upload API használatával.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Videó feltöltése és indexelése a Video Indexer használatával

> [!NOTE]
> A videó nevének hossza nem haladhatja meg a 80 karaktert.

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
1. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Feltöltés":::
1. A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="A feltöltés állapota":::
1. Ha Video Indexer végzett az elemzéssel, kap egy e-mailt, amely tartalmazza a videóra mutató hivatkozást és a videóban talált elemek rövid leírását. Például: személyek, témák, OCR-ek.

## <a name="upload-and-index-with-api"></a>Feltöltés és indexelés API-val

A Videó [feltöltése API-val](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) URL-cím alapján töltheti fel és indexelheti videóit. A következő kódminta tartalmazza a megjegyzésbe tett kódot, amely bemutatja, hogyan töltheti fel a bájttömböt. 

### <a name="configurations-and-params"></a>Konfigurációk és paraméterek

Ez a szakasz ismertet néhány választható paramétert, és leírja, hogy mikor érdemes beállítani őket. A legfrissebb paraméterekkel kapcsolatos információkért tekintse meg a Videó [feltöltése API-t.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

#### <a name="externalid"></a>externalID 

Ezzel a paraméterrel megadhat egy azonosítót, amely társítva lesz a videóhoz. Az azonosítót a külső „Video Content Management” (VCM) rendszer-integrációra is alkalmazni lehet. A Video Indexer portálon található videók a megadott külső azonosító használatával kereshetők.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>További szempontok

- Video Indexer az eredeti URL-címben megadott meglévő paramétereket adja vissza.
- A megadott URL-címet kódolni kell.

#### <a name="indexingpreset"></a>indexingPreset

Ezzel a paraméterrel adhatja meg a hang- vagy videofájlra alkalmazni kívánt AI-csomagot. Ez a paraméter az indexelési folyamat konfigurálására szolgál. A következő értékeket adhatja meg:

- `AudioOnly` – Elemzéseket indexelhet és nyerhet ki csak hanganyagokkal (a videó figyelmen kívül hagyása).
- `VideoOnly` – Elemzéseket indexelhet és nyerhet ki csak videóval (a hanganyag figyelmen kívül hagyása).
- `Default` – Indexelhet és kinyerhet elemzéseket hang- és videóelemzések használatával.
- `DefaultWithNoiseReduction` – Indexelhet és kinyerhet elemzéseket hang- és videóelemzések alapján, miközben zajcsökkentési algoritmusokat alkalmaz a hangstreamre.

    Az `DefaultWithNoiseReduction` érték mostantól az alapértelmezett beállításkészletre van leképezve (elavult).
- `BasicAudio` – Elemzések indexelése és kinyerése csak hanganyagokkal (videó figyelmen kívül hagyása), beleértve csak az alapvető hangszolgáltatásokat (átírás, fordítás, kimeneti feliratok és feliratok formázása).
 - `AdvancedAudio` – Elemzések indexelése és kinyerése csak hanganyagokkal (videó figyelmen kívül hagyása), beleértve a speciális hangszolgáltatásokat (hangesemények észlelése) a szabványos hangelemzés mellett.

> [!NOTE]
> Video Indexer legfeljebb két hangsávot fed le. Ha több hangsáv található a fájlban, a rendszer egyetlen sávként kezeli őket.<br/>
Ha a sávokat külön szeretné indexelni, ki kell bontanunk a megfelelő hangfájlt, és a következőként kell indexelnünk: `AudioOnly` .

Az árat a kiválasztott indexelési lehetőség határozza meg. További információért tekintse meg a [díjszabást Media Services.](https://azure.microsoft.com/pricing/details/media-services/)

#### <a name="priority"></a>Prioritás

A videók indexelése Video Indexer prioritásuknak megfelelően. Az index **prioritásának** megadásához használja a priority paramétert. A következő értékek érvényesek: **Alacsony,** **Normál** (alapértelmezett) és **Magas.**

**A Priority** paraméter csak fizetős fiókok esetén támogatott.

#### <a name="streamingpreset"></a>streamingPreset

A videó feltöltése után a Video Indexer opcionálisan kódolja a videót. Ezután továbblép a videó indexelésére és elemzésére. Amikor a Video Indexer végzett az elemzéssel, kapni fog egy értesítést, benne a videó azonosítójával.  

A [Videó feltöltése](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) vagy a [Videó újraindexelése](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API használatakor a `streamingPreset` az egyik választható paraméter. Ha a `streamingPreset` paramétert `Default`, `SingleBitrate` vagy `AdaptiveBitrate` értékre állítja, a kódolási folyamat aktiválódik. Az indexelési és a kódolási feladatok befejezése után a rendszer közzéteszi a videót, amely streamelhető is. A videó streameléséhez használt streamvégpontnak **Futó** állapotban kell lennie.

SingleBitrate esetén a standard kódoló költsége lesz alkalmazva kimenetenként. Ha a videó magassága nagyobb vagy egyenlő, mint 720, a Video Indexer 1280x720 formátumban kódolja. Ellenkező esetben 640x468.
Az Alapértelmezett beállítás a [tartalomkezelő kódolás.](../latest/encode-content-aware-concept.md)

Az indexelési és kódolási feladatok futtatásához a [Video Indexer-fiókjához csatlakoztatott Azure Media Services-fióknak](connect-to-azure.md) fenntartott egységekre van szüksége. További információért lásd a [médiafeldolgozás skálázását](../previous/media-services-scale-media-processing-overview.md) ismertető cikket. Mivel ezek nagy számításigényű feladatok, határozottan ajánlott az S3-as egységtípus. A kérelemegységek száma meghatározza a párhuzamosan futtatható feladatok maximális számát. Az általános javaslat 10 S3 kérelemegység. 

Ha csak indexelni szeretné a videót, és nem kívánja kódolni, állítsa a `streamingPreset` paramétert `NoStreaming` értékre.

#### <a name="videourl"></a>videoUrl

Az indexelni kívánt video-/hangfájl URL-címe. Az URL-címnek egy médiafájlra kell mutatnia (a HTML-lapok nem támogatottak). A fájl védelme az URI részeként biztosított hozzáférési jogkivonattal biztosítható, a fájlt szolgáltató végpontot pedig TLS 1.2-es vagy újabb verzióval kell védeni. Az URL-címet kódolni kell. 

Ha a `videoUrl` paraméter nincs meghatározva, a Video Indexer elvárja, hogy többrészes/többformátumú törzstartalomként adja át a fájlt.

### <a name="code-sample"></a>Kódminta

Az alábbi C#-kódrészlet a Video Indexer API-k együttes használatát mutatja be.

**Útmutatás a következő kódminta futtatásához**

Miután átmásolta ezt a kódot a fejlesztői platformra, két paramétert kell adnia: a API Management kulcs és a videó URL-címe.

* API-kulcs – Az API-kulcs a személyes API Management előfizetői kulcsa, amely lehetővé teszi egy hozzáférési jogkivonat lekért használatát a Video Indexer műveletek végrehajtásához. 

    Az API-kulcs lekért folyamatának végig kell mennie:

    * Navigáljon ide: https://api-portal.videoindexer.ai/
    * Bejelentkezés
    * Ugrás a **termékek engedélyezési**  ->    ->  **előfizetésére**
    * Az elsődleges **kulcs másolása**
* Videó URL-címe – Az indexelni kívánt video-/hangfájl URL-címe. Az URL-címnek egy médiafájlra kell mutatnia (a HTML-lapok nem támogatottak). A fájl védelme az URI részeként biztosított hozzáférési jogkivonattal biztosítható, a fájlt szolgáltató végpontot pedig TLS 1.2-es vagy újabb verzióval kell védeni. Az URL-címet kódolni kell.

A kódminta sikeres futtatásának eredménye tartalmazni fog egy elemzési vezérlő URL-címét és egy lejátszó-widget URL-címét, amely lehetővé teszi az elemzések és a feltöltött videók vizsgálatát. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Gyakori hibák

A Feltöltés művelet által visszaadott lehetséges állapotkódok az alábbi táblázatban láthatók.

|Állapotkód|Hibatípus (a válasz törzsében)|Leírás|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Ugyanannak a videónak a feldolgozása már folyamatban van az adott fiókban.|
|400|VIDEO_ALREADY_FAILED|Ugyanannak a videónak a feldolgozása már meghiúsult az adott fiókban 2 órán belül. Az API-ügyfeleknek legalább 2 órát várniuk kell a videó ismételt feltöltése előtt.|
|429||A próbafiókok percenként 5 feltöltést engedélyezettek. A fizetős fiókok percenként 50 feltöltést engedélyezettek.|

## <a name="uploading-considerations-and-limitations"></a>A feltöltéssel kapcsolatos megfontolandó szempontok és korlátozások
 
- A videó nevének hossza nem haladhatja meg a 80 karaktert.
- A videó URL-cím alapján történő feltöltésekor (ez az előnyben részesített megoldás) a végpontot a TLS 1.2-es (vagy újabb) verziójával kell védeni.
- Az URL-címről való feltöltés használata esetén a feltöltött videó mérete legfeljebb 30 GB lehet.
- A kérés URL-címének hossza legfeljebb 6144 karakter lehet, amelyben a lekérdezési sztring URL-címe legfeljebb 4096 karakter hosszúságú lehet.
- A bájttömb használata esetén a feltöltött videó mérete legfeljebb 2 GB lehet.
- A bájttömbként történő küldés időkorlátja 30 perc.
- A paraméterben megadott `videoURL` URL-címet kódolni kell.
- A Media Services-objektumok indexelésére az URL-címből történő indexeléssel megegyező korlátozások vonatkoznak.
- A Video Indexerben egy adott fájlra 4 órás korlát érvényes.
- Az URL-címnek elérhetőnek kell lennie (például nyilvános URL-nek kell lennie). 

    Ha az URL-cím magánjellegű, a hozzáférési jogkivonatot meg kell adni a kérésben.
- Az URL-címnek érvényes médiafájlra kell hivatkozni, nem pedig weblapra, például az oldalra mutató `www.youtube.com` hivatkozásra.
- Fizetős fiókban percenként legfeljebb 50 videót tölthet fel, próbaverziós fiókban pedig percenként legfeljebb 5 videót.

> [!Tip]
> Javasoljuk, hogy a .NET-keretrendszer 4.6.2-es vagy újabb verzióját használja, mivel a régebbi verziók nem a TLS 1.2-t használják alapértelmezés szerint.
>
> Ha régebbi verziót kell használnia, adjon hozzá egy sort a kódban a REST API-hívás indítása előtt:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Következő lépések

[Az API által előállított Azure Video Indexer-kimenet vizsgálata](video-indexer-output-json-v2.md)
