---
title: A Video Indexer API használata
titleSuffix: Azure Media Services
description: Ez a cikk az API Azure Media Services Video Indexer első Azure Media Services Video Indexer ismerteti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: ac0b206a86edf3157141b56e0c2623a8429b0c7a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785522"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Oktatóanyag: A Video Indexer API használata

Video Indexer a Microsoft által kínált különböző hang- és videó-mesterségesintelligencia- (AI-) technológiákat egyetlen integrált szolgáltatásban egyesíti, ami egyszerűbbé teszi a fejlesztést. Az API-kat arra tervezték, hogy a fejlesztők a média AI-technológiáinak felhasználásával összpontosítva ne kell aggódniuk a felhőplatformok méretezése, globális elérhetősége, rendelkezésre állása és megbízhatósága miatt. Az API-val feltöltheti a fájlokat, részletes videóelemzéseket kaphat, lekérte a beágyazható elemzéseket és lejátszó widgeteket stb.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. Fizetős lehetőséggel létrehozhat egy azure Video Indexer fiókot, amely az Azure-előfizetéséhez csatlakozik, és egy Azure Media Services [fiókkal.](connect-to-azure.md) Az indexelt percekért fizet, további információért tekintse meg a [Media Services díjszabását.](https://azure.microsoft.com/pricing/details/media-services/)

Ez a cikk azt mutatja be, hogyan használhatják ki a fejlesztők a [Video Indexer API](https://api-portal.videoindexer.ai/) előnyeit.

## <a name="subscribe-to-the-api"></a>Feliratkozás az API-ra

1. Jelentkezzen be a [Video Indexer fejlesztői portálra](https://api-portal.videoindexer.ai/).

    Tekintse át a bejelentkezési adatok kibocsátási [megjegyzését.](release-notes.md#october-2020)
    
     ![Jelentkezzen be Video Indexer fejlesztői portálra](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Használja ugyanazt a szolgáltatót, amelyet a Video Indexerre való regisztráláskor használt.
   > * A személyes Google- és Microsoft-fiókok (Outlook/Live) csak próbafiókok esetében használhatók. Az Azure-hoz kapcsolódó fiókokhoz Azure AD-ra van szükség.
   > * E-mailben csak egy aktív fiók lehet. Ha egy felhasználó megpróbál bejelentkezni a LinkedInbe, majd később a Google-be, az utóbbi egy hibaüzenetet jelenít meg, amely szerint a felhasználó user@gmail.com user@gmail.com már létezik.

2. Iratkozzon fel.

   Válassza a [Termékek lapot.](https://api-portal.videoindexer.ai/products) Ezután válassza az Engedélyezés lehetőséget, és iratkozzon fel.
    
   ![Termékek lap a Video Indexer portálon](./media/video-indexer-use-apis/authorization.png)

   > [!NOTE]
   > Az új felhasználók automatikusan feliratkoznak az Engedélyezési API-ra.
    
   A feliratkozás után az előfizetést a Products Authorization (Termékek engedélyezése) **alatt**  ->  **találja.** Az előfizetési oldalon megtalálja az elsődleges és a másodlagos kulcsot. A kulcsok védelmet igényelnek. Csak a kiszolgálókód használhatja a kulcsokat. Nem szabad elérhetővé tenni őket az ügyféloldalon (.js, .html stb.).

   ![Előfizetés és kulcsok a Video Indexer portálon](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Egy Video Indexer-felhasználó egyetlen előfizetői kulccsal több Video Indexer-fiókhoz is csatlakozhat. Ezek a Video Indexer-fiókok pedig különböző Media Services-fiókokhoz társíthatók.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hozzáférési jogkivonat beszerzése az Engedélyezési API-val

Az engedélyezési API-előfizetés után hozzáférési jogkivonatokat szerezhet be. Ezen hozzáférési jogkivonatok segítségével történik a hitelesítés a Műveleti API-n.

A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

- Felhasználói szint: A felhasználói szintű hozzáférési jogkivonatokkal felhasználói szinten végezhet **műveleteket.** Lekérhet például társított fiókokat.
- Fiókszint: A fiókszintű hozzáférési jogkivonatokkal  fiók- vagy videószinten végezhet **műveleteket.** Feltölthet például videókat, felsorolhatja az összes videót, lekért videóelemzéseket stb.
- Videószint: A videószintű hozzáférési jogkivonatokkal műveleteket végezhet egy adott **videón.** Lekért például videóelemzéseket, feliratokat tölthet le, widgeteket tölthet le stb.

A jogkivonatok engedélyszintje kétféleképpen szabályozható:

* Fiók-jogkivonatok esetén használhatja a **Fiók-hozzáférési** jogkivonat le kérni engedélyekkel API-t, és megadhatja az engedély típusát **(Olvasó**  / **közreműködő** / **MyAccessManager** / **tulajdonos).**
* Minden jogkivonattípushoz (beleértve a fióktokeneket is) megadhatja **az allowEdit=true/false értéket.**  **A false** (hamis) az Olvasó engedély **megfelelője** (csak olvasható), a **true** (igaz) pedig a **Közreműködői** engedély (olvasási/írási) engedélynek felel meg.

A legtöbb kiszolgáló–kiszolgáló forgatókönyv esetében valószínűleg ugyanazt a  fiók-jogkivonatot  fogja használni, mivel a fiókműveleteket és a videóműveleteket is lefedi.  Ha azonban ügyféloldali hívásokat szeretne kezdeményezni az Video Indexer-hoz (például JavaScriptből), egy  videó-hozzáférési jogkivonattal megakadályozhatja, hogy az ügyfelek a teljes fiókhoz hozzáférjenek. Ez az oka annak is, hogy amikor Video Indexer ügyfélkódot ágyaz be az  ügyfélprogramba (például az Elemzések lehívása widgettel vagy **a** **Lejátszó** lehívása vezérlővel), meg kell adnia egy videó-hozzáférési jogkivonatot.

A művelet leegyszerűsítése érdekében használhatja az **Engedélyezési** API **GetAccounts** parancsát, ha a fiókjait a felhasználói jogkivonat beszerzése előtt szeretné lekérni. Lekérheti az érvényes jogkivonattal rendelkező fiókokat is, így nem kell még egy hívással fiókjogkivonatot lekérnie.

A hozzáférési jogkivonatok 1 óráig érvényesek. A Műveleti API használata előtt ellenőrizze, hogy érvényes-e a hozzáférési jogkivonata. Ha lejár, hívja meg újra az engedélyezési API-t egy új hozzáférési jogkivonat lehívásához.

Készen áll az API integrálására. Itt megtalálhatja [az egyes Video Indexer REST API-k részletes leírását](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Fiókazonosító

A Fiókazonosító paraméterre minden műveleti API-híváshoz szükség van. A fiókazonosító egy GUID, és az alábbi módokon szerezhető be:

* A fiókazonosító lekérése a **Video Indexer webhelyen**:

    1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
    2. Lépjen a **Settings** (Beállítások) lapra.
    3. Másolja a fiókazonosítót.

        ![Video Indexer beállítások és a fiókazonosító](./media/video-indexer-use-apis/account-id.png)

* A fiókazonosító lekérése szoftveresen a **Video Indexer fejlesztői portálon**.

    Használja a [Fiók lekért](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) API-ját.

    > [!TIP]
    > A `generateAccessTokens=true` meghatározásával létrehozhat hozzáférési jogkivonatokat a fiókokhoz.

* Kérje le a fiókazonosítót a fiókja valamely lejátszóoldalának URL-címéből.

    Amikor megtekint egy videót, az azonosító megjelenik az `accounts` szakasz után, de még a `videos` szakasz előtt.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Javaslatok

Ebben a részben a Video Indexer API használatára vonatkozó javaslatokat talál.

- Ha videó feltöltését tervezi, javasoljuk, hogy a fájlt egy nyilvános hálózati helyen helyezze el (például egy Azure Blob Storage fiókban). Kérje le a videóra mutatkozó hivatkozást, és adja meg az URL-címet a feltöltendő fájl paramétereként.

    A Video Indexer számára megadott URL-címnek egy médiafájlra (audió- vagy videófájlra) kell mutatnia. Az URL-cím (vagy SAS URL-cím) egyszerű ellenőrzése az, hogy beilleszti azt egy böngészőbe, ha a fájl lejátszása/letöltése megkezdődik, akkor valószínűleg jó URL-cím. Ha a böngésző valamilyen vizualizációt renderel, az valószínűleg nem egy fájlra mutató hivatkozás, hanem egy HTML-oldal.

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

Miután végzett ezzel az oktatóanyaggal, törölje azokat az erőforrásokat, amelyek használatát nem tervezi.

## <a name="see-also"></a>Lásd még

- [A Video Indexer áttekintése](video-indexer-overview.md)
- [Régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Következő lépések

- [A kimeneti JSON részleteinek vizsgálata](video-indexer-output-json-v2.md)
- Tekintse meg a videó [feltöltésének](https://github.com/Azure-Samples/media-services-video-indexer) és indexelésének fontos aspektusát bemutató mintakódot. A kód bemutatja, hogyan használhatja az API-t az alapszintű funkciókhoz. Olvassa el a beágyazott megjegyzéseket, és figyelje meg az ajánlott eljárásokkal kapcsolatos tanácsokat.

