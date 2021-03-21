---
title: A Video Indexer API használata
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan kezdheti meg a Azure Media Services Video Indexer API használatát.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: fcd194e2503610db314f6a975a4afb1d27962f8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028224"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Oktatóanyag: A Video Indexer API használata

Video Indexer összevonja a Microsoft által kínált különböző hang-és video mesterséges intelligencia (AI) technológiákat egy integrált szolgáltatásba, ami egyszerűbbé teszi a fejlesztést. Az API-k úgy lettek kialakítva, hogy lehetővé tegyék a fejlesztők számára a Media AI-technológiák felhasználását anélkül, hogy aggódniuk kellene a Felhőbeli platformok méretezésével, globális elérhetőségével, rendelkezésre állásával Az API-val fájlokat tölthet fel, részletes videó-elemzéseket kaphat, beolvashatja a beágyazható betekintést és a lejátszó widgetek URL-címeit.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. Fizetős megoldásként létrehozhat egy Video Indexer fiókot, amely az [Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik](connect-to-azure.md). Az indexelt percek megfizetése után további információért lásd a [Media Services díjszabását](https://azure.microsoft.com/pricing/details/media-services/).

Ez a cikk azt mutatja be, hogyan használhatják ki a fejlesztők a [Video Indexer API](https://api-portal.videoindexer.ai/) előnyeit.

## <a name="subscribe-to-the-api"></a>Feliratkozás az API-ra

1. Jelentkezzen be a [Video Indexer fejlesztői portálra](https://api-portal.videoindexer.ai/).

    Tekintse át a [bejelentkezési adatokat](release-notes.md#october-2020)érintő kibocsátási megjegyzéseit.
    
     ![Bejelentkezés Video Indexer fejlesztői portálra](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Használja ugyanazt a szolgáltatót, amelyet a Video Indexerre való regisztráláskor használt.
   > * A személyes Google-és Microsoft-(Outlook-/élő) fiókokat csak próbaverziós fiókokhoz lehet használni. Az Azure-hoz kapcsolódó fiókokhoz Azure AD-ra van szükség.
   > * E-mailben csak egy aktív fiók lehet. Ha a felhasználó a Google-hoz tartozó user@gmail.com LinkedIn-hez vagy újabbhoz próbál bejelentkezni user@gmail.com , akkor az utóbbi egy hibaüzenetet jelenít meg, amely azt jelzi, hogy a felhasználó már létezik.
2. Iratkozzon fel.

    Válassza a [termékek](https://api-portal.videoindexer.ai/products) lapot. Ezután válassza az engedélyezés és előfizetés lehetőséget.
    
    ![Termékek lap Video Indexer fejlesztői portálon](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Az új felhasználók automatikusan feliratkoznak az Engedélyezési API-ra.
    
    Az előfizetés után megtalálhatja az előfizetését a **termékek**  ->  **engedélyezése** lehetőség alatt. Az előfizetés lapon megtalálhatja az elsődleges és a másodlagos kulcsot. A kulcsok védelmet igényelnek. Csak a kiszolgálókód használhatja a kulcsokat. Nem lesznek elérhetők az ügyféloldali oldalon (. js,. html stb.).

    ![Előfizetés és kulcsok a Video Indexer fejlesztői portálon](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Egy Video Indexer-felhasználó egyetlen előfizetői kulccsal több Video Indexer-fiókhoz is csatlakozhat. Ezek a Video Indexer-fiókok pedig különböző Media Services-fiókokhoz társíthatók.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hozzáférési jogkivonat beszerzése az Engedélyezési API-val

Az engedélyezési API-ra való előfizetés után hozzáférési jogkivonatokat szerezhet be. Ezen hozzáférési jogkivonatok segítségével történik a hitelesítés a Műveleti API-n.

A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

- Felhasználói szint: a felhasználói szintű hozzáférési tokenek lehetővé teszik a **felhasználói** szinten végzett műveletek végrehajtását. Lekérhet például társított fiókokat.
- Fiók szintje: a fiók szintű hozzáférési tokenek lehetővé teszik, hogy a **fiók** szintjén vagy a **videó** szintjén hajtson végre műveleteket. Például a videó feltöltése, az összes videó listázása, a videó-felismerések beolvasása és így tovább.
- Videó szintje: a videó szintű hozzáférési tokenek lehetővé teszik a műveletek végrehajtását egy adott **videón**. Például lekérheti a videós ismereteket, letöltheti a feliratokat, lekérheti a widgeteket és így tovább.

Megadhatja, hogy ezek a tokenek csak olvashatók-e, vagy hogy a **allowEdit = TRUE/FALSE érték** megadásával engedélyezzék-e a szerkesztést.

A kiszolgálók közötti legtöbb forgatókönyv esetén valószínűleg ugyanazt a **fiókot** fogja használni, mivel a **fiók** -és a **videó** -műveleteket is magában foglalja. Ha azonban úgy tervezi, hogy az ügyféloldali hívásokat Video Indexer (például a JavaScriptből) szeretné használni, akkor a **videó** -hozzáférési token használatával megakadályozhatja, hogy az ügyfelek hozzáférjenek a teljes fiókhoz. Ez azt is indokolja, hogy amikor Video Indexer-ügyfél-kódot ágyaz be az ügyfélbe (például a **beolvasások beolvasása widget** vagy a **Player widget beszerzése**), meg kell adnia egy **videó** -hozzáférési jogkivonatot.

A művelet leegyszerűsítése érdekében használhatja az **Engedélyezési** API **GetAccounts** parancsát, ha a fiókjait a felhasználói jogkivonat beszerzése előtt szeretné lekérni. Lekérheti az érvényes jogkivonattal rendelkező fiókokat is, így nem kell még egy hívással fiókjogkivonatot lekérnie.

A hozzáférési jogkivonatok 1 óráig érvényesek. A Műveleti API használata előtt ellenőrizze, hogy érvényes-e a hozzáférési jogkivonata. Ha lejár, hívja meg újra az engedélyezési API-t egy új hozzáférési jogkivonat beszerzéséhez.

Készen áll az API-val való integráció megkezdésére. Itt megtalálhatja [az egyes Video Indexer REST API-k részletes leírását](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Fiókazonosító

A Fiókazonosító paraméterre minden műveleti API-híváshoz szükség van. A fiókazonosító egy GUID, és az alábbi módokon szerezhető be:

* A fiókazonosító lekérése a **Video Indexer webhelyen**:

    1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
    2. Lépjen a **Settings** (Beállítások) lapra.
    3. Másolja a fiókazonosítót.

        ![Video Indexer beállítások és fiókazonosító](./media/video-indexer-use-apis/account-id.png)

* A fiókazonosító lekérése szoftveresen a **Video Indexer fejlesztői portálon**.

    Használja a [fiók beolvasása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) API-t.

    > [!TIP]
    > A `generateAccessTokens=true` meghatározásával létrehozhat hozzáférési jogkivonatokat a fiókokhoz.

* Kérje le a fiókazonosítót a fiókja valamely lejátszóoldalának URL-címéből.

    Amikor megtekint egy videót, az azonosító megjelenik az `accounts` szakasz után, de még a `videos` szakasz előtt.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Javaslatok

Ebben a részben a Video Indexer API használatára vonatkozó javaslatokat talál.

- Ha egy videó feltöltését tervezi, azt javasoljuk, hogy a fájlt egy nyilvános hálózati helyen (például egy Azure Blob Storage-fiókban) helyezze el. Kérje le a videóra mutatkozó hivatkozást, és adja meg az URL-címet a feltöltendő fájl paramétereként.

    A Video Indexer számára megadott URL-címnek egy médiafájlra (audió- vagy videófájlra) kell mutatnia. Az URL-cím (vagy SAS URL-cím) egyszerű ellenőrzése, hogy egy böngészőben illessze be a fájlt, ha a fájl a lejátszást vagy a letöltést indítja el, valószínűleg jó URL-cím lesz. Ha a böngésző megjelenít valamilyen vizualizációt, valószínűleg nem egy fájlra, hanem egy HTML-lapra mutató hivatkozás.

- Amikor meghívja az adott videóhoz tartozó videóelemzéseket lekérő API-t, részletes JSON-kimenetet kap a válasz tartalmaként. [A visszaadott JSON-ról ebben a témakörben találhat részleteket](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Kódminta

Az alábbi C#-kódrészlet a Video Indexer API-k együttes használatát mutatja be.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az Oktatóanyaggal, törölje azokat az erőforrásokat, amelyeket nem szeretne használni.

## <a name="see-also"></a>Lásd még

- [A Video Indexer áttekintése](video-indexer-overview.md)
- [Régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Következő lépések

- [A kimeneti JSON részleteinek vizsgálata](video-indexer-output-json-v2.md)
- Tekintse meg a videó feltöltésének és indexelésének fontos aspektusát bemutató [mintakód](https://github.com/Azure-Samples/media-services-video-indexer) használatát. A kód követésével jól megtudhatja, hogyan használhatja API-ját alapszintű funkciókhoz. Olvassa el a beágyazott megjegyzéseket, és figyelje meg az ajánlott eljárásokkal kapcsolatos tanácsokat.

