---
title: Videóátirata-felülvizsgálatok létrehozása a .NET-Content Moderator használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre videóátirata-felülvizsgálatokat a .NET-hez készült Azure Cognitive Services Content Moderator SDK-val.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853458"
---
# <a name="create-video-transcript-reviews-using-net"></a>Videóátirata-felülvizsgálatok létrehozása a .NET használatával

Ez a cikk olyan információkat és kódokat tartalmaz, amelyek segítségével gyorsan megkezdheti az [Content MODERATOR SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való ismerkedést a C# használatával:

- Videó-áttekintés létrehozása az emberi moderátorok számára
- Moderált átirat hozzáadása a felülvizsgálathoz
- A felülvizsgálat közzététele

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem tette meg, jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.
- Ez a cikk azt feltételezi, hogy [moderálta a videót](video-moderation-api.md) , és [létrehozta a videó-felülvizsgálatot](video-reviews-quickstart-dotnet.md) az emberi döntéshozatalra szolgáló felülvizsgálati eszközben. Most a felülvizsgálati eszközben hozzá kívánja adni a moderált videó átiratait.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Győződjön meg arról, hogy az API-kulcs hívhatja a felülvizsgálati API-t (a feladatok létrehozása)

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot.

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

## <a name="prepare-your-video-for-review"></a>Készítse elő a videót véleményezésre

Adja hozzá az átiratot egy videós felülvizsgálathoz. A videót online közzé kell tenni. Szüksége van a folyamatos átviteli végpontra. A streaming végpont lehetővé teszi a felülvizsgálati eszköz videolejátszó számára a videó lejátszását.

![Videó bemutató miniatűrje](images/ams-video-demo-view.PNG)

- Másolja az **URL-címet** ezen az [Azure Media Services bemutató](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) oldalon a jegyzékfájl URL-címéhez.

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

1. Nevezze el a projekt **VideoTranscriptReviews**.

1. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Telepítse a következő NuGet-csomagokat a TermLists projekthez.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Módosítsa a program utasításait az alábbiak szerint.


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Privát tulajdonságok hozzáadása

Adja hozzá a következő privát tulajdonságokat a névtér **VideoTranscriptReviews**, az osztály **programhoz**. Frissítse a `AzureEndpoint` és a `CMSubscriptionKey` mezőket a végpont URL-címének és előfizetési kulcsának értékeivel. Ezeket a Azure Portalban található erőforrás **gyors üzembe helyezés** lapján találja.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Content Moderator ügyfél-objektum létrehozása

Adja hozzá a következő metódus-definíciót a Namespace VideoTranscriptReviews, a Class programhoz.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Videó felülvizsgálatának létrehozása

Hozzon létre egy videó-áttekintést a **ContentModeratorClient. Reviews. CreateVideoReviews**. További információkért lásd az [API-referenciát](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

A **CreateVideoReviews** a következő szükséges paraméterekkel rendelkezik:
1. Egy MIME-típust tartalmazó karakterlánc, amelynek "Application/JSON" típusúnak kell lennie. 
1. Az Content Moderator-csoport neve.
1. Egy **IList \<CreateVideoReviewsBodyItem>** objektum. Minden **CreateVideoReviewsBodyItem** -objektum egy videó-áttekintést képvisel. Ez a rövid útmutató egyszerre egy felülvizsgálatot hoz létre.

A **CreateVideoReviewsBodyItem** több tulajdonsággal rendelkezik. Legalább a következő tulajdonságokat kell beállítania:
- **Tartalom**. Az áttekinteni kívánt videó URL-címe.
- **ContentId**. A videó felülvizsgálatához hozzárendelni kívánt azonosító.
- **Állapot**. Állítsa az értéket "közzététel előtt" értékre. Ha nem állítja be, a rendszer alapértelmezés szerint "függőben" állapotba kerül, ami azt jelenti, hogy a videó felülvizsgálata közzé van téve, és az emberi felülvizsgálat függőben van. A videó felülvizsgálatának közzététele után már nem adhat hozzá képkockákat, átiratokat vagy átiratok moderálási eredményét.

> [!NOTE]
> A **CreateVideoReviews** egy IList ad vissza \<string> . Ezen karakterláncok mindegyike tartalmaz egy videó-felülvizsgálati azonosítót. Ezek az azonosítók GUID-azonosítók, és nem egyeznek meg a **ContentId** tulajdonság értékével.

Adja hozzá a következő metódus-definíciót a Namespace VideoReviews, a Class programhoz.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> A Content Moderator szolgáltatáskulcs egy másodpercenkénti kérelmekre (RPS-re) vonatkozó korláttal rendelkezik. Ha túllépi ezt a korlátot, az SDK 429-es hibakódú kivételt jelez.
>
> Az ingyenes szint kulcsai egy RPS-korláttal bírnak.

## <a name="add-transcript-to-video-review"></a>Átiratok hozzáadása a videó felülvizsgálatához

A **ContentModeratorClient. Reviews. AddVideoTranscript**. A **AddVideoTranscript** a következő szükséges paraméterekkel rendelkezik:
1. Az Content Moderator-csoport azonosítója.
1. A **CreateVideoReviews** által visszaadott videó-felülvizsgálati azonosító.
1. Az átiratot tartalmazó **stream** -objektum.

Az átiratnak WebVTT formátumúnak kell lennie. További információkért lásd [: WebVTT: a webes videó szövegének nyomon követési formátuma](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> A program a VTT formátumban használja a minta átiratát. Egy valós megoldásban a Azure Media Indexer szolgáltatással [készíthet átiratot](../../media-services/previous/media-services-index-content.md) egy videóból.

Adja hozzá a következő metódus-definíciót a Namespace VideotranscriptReviews, a Class programhoz.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Átiratok moderálási eredményének hozzáadása a videó felülvizsgálatához

Az átiratok videó-áttekintéshez való hozzáadásán kívül az átiratok moderálásának eredményét is hozzáadhatja. Ezt a **ContentModeratorClient. Reviews. AddVideoTranscriptModerationResult**. További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

A **AddVideoTranscriptModerationResult** a következő szükséges paraméterekkel rendelkezik:
1. Egy MIME-típust tartalmazó karakterlánc, amelynek "Application/JSON" típusúnak kell lennie. 
1. Az Content Moderator-csoport neve.
1. A **CreateVideoReviews** által visszaadott videó-felülvizsgálati azonosító.
1. Egy IList \<TranscriptModerationBodyItem> . A **TranscriptModerationBodyItem** a következő tulajdonságokkal rendelkezik:
1. **Feltételek**. Egy IList \<TranscriptModerationBodyItemTermsItem> . A **TranscriptModerationBodyItemTermsItem** a következő tulajdonságokkal rendelkezik:
1. **Index**. A kifejezés nulla alapú indexe.
1. **Kifejezés**. A kifejezést tartalmazó karakterlánc.
1. **Időbélyeg**. Az a karakterlánc, amely másodpercben tartalmazza a kifejezéseket tartalmazó átirat időpontját.

Az átiratnak WebVTT formátumúnak kell lennie. További információkért lásd [: WebVTT: a webes videó szövegének nyomon követési formátuma](https://www.w3.org/TR/webvtt1/).

Adja hozzá a következő metódus-definíciót a Namespace VideoTranscriptReviews, a Class programhoz. Ez a metódus elküld egy átiratot a **ContentModeratorClient. TextModeration. ScreenText** metódusnak. Emellett lefordítja az eredményt egy IList \<TranscriptModerationBodyItem> , és beküldi a **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Videó közzétételének áttekintése

A videós felülvizsgálatot a **ContentModeratorClient. Reviews. PublishVideoReview** címen teheti közzé. A **PublishVideoReview** a következő szükséges paraméterekkel rendelkezik:
1. Az Content Moderator-csoport neve.
1. A **CreateVideoReviews** által visszaadott videó-felülvizsgálati azonosító.

Adja hozzá a következő metódus-definíciót a Namespace VideoReviews, a Class programhoz.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Végső összeállítás

Adja hozzá a **fő** metódus definícióját a Namespace VideoTranscriptReviews, a Class programhoz. Végül zárjuk be a program osztályt és a VideoTranscriptReviews névteret.

> [!NOTE]
> A program a VTT formátumban használja a minta átiratát. Egy valós megoldásban a Azure Media Indexer szolgáltatással [készíthet átiratot](../../media-services/previous/media-services-index-content.md) egy videóból.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

Az alkalmazás futtatásakor a következő sorok kimenete jelenik meg:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navigáljon a videó átiratának áttekintéséhez

Nyissa meg a videó átiratának áttekintését az Content moderator felülvizsgálati eszközben a  > **videó** > **átiratának** áttekintése képernyőn.

A következő funkciók jelennek meg:
- A hozzáadott átirat két sora
- A szöveges moderálási szolgáltatás által észlelt és Kiemelt trágár kifejezés
- Az átírási szöveg kiválasztása elindítja a videót az időbélyegből

![Az emberi moderátorok videós átiratának áttekintése](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Következő lépések

Szerezze be a [Content moderator .net SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) -t és a [Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ehhez és egyéb Content moderator a .net-hez készült gyors útmutatóhoz.

Megtudhatja, hogyan hozhatja ki a [videós](video-reviews-quickstart-dotnet.md) felülvizsgálatokat a felülvizsgálati eszközben.
