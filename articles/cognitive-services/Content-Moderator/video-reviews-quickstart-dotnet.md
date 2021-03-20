---
title: Videós felülvizsgálatok létrehozása a .NET-Content Moderator használatával
titleSuffix: Azure Cognitive Services
description: Ez a cikk információkat és kódokat tartalmaz, amelyek segítségével gyorsan megkezdheti az első lépéseket a Content Moderator SDK és a C# használatával a videós felülvizsgálatok létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: d9f80de5a18e27de4a9f8e85613e3c2eee6c111c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853475"
---
# <a name="create-video-reviews-using-net"></a>Videós felülvizsgálatok létrehozása a .NET használatával

Ez a cikk olyan információkat és kódokat tartalmaz, amelyek segítségével gyorsan megkezdheti az [Content MODERATOR SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való ismerkedést a C# használatával:

- Videó-áttekintés létrehozása az emberi moderátorok számára
- Keretek hozzáadása a felülvizsgálathoz
- A felülvizsgálat keretének beolvasása
- A felülvizsgálat állapotának és részleteinek beolvasása
- A felülvizsgálat közzététele

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.
- Ez a cikk azt feltételezi, hogy [moderálta a videót (lásd](video-moderation-api.md) a gyors üzembe helyezést), és a válaszokat tartalmazza. Szüksége lesz rá az emberi moderátorok frame-alapú felülvizsgálatának létrehozásához.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Arról való gondoskodás, hogy az API-kulcs meg tudja hívni a felülvizsgálati API-t a felülvizsgálat létrehozásához

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot.

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Videó és képkockák előkészítése a felülvizsgálathoz

Az áttekinteni kívánt videó-és minta-képkockákat online közzé kell tenni, mert szüksége van az URL-címekre.

> [!NOTE]
> A program manuálisan mentett képernyőképeket használ a videóból véletlenszerű felnőtt/zamatos pontszámokkal a felülvizsgálati API használatának szemléltetése érdekében. Valós helyzetben a [videó-moderálás kimenet](video-moderation-api.md#run-the-program-and-review-the-output) használatával képeket hozhat létre, és pontszámokat rendelhet hozzá. 

A videóhoz szükség van egy folyamatos átviteli végpontra, hogy a felülvizsgálati eszköz játssza le a videót a Player nézetben.

![Videó bemutató miniatűrje](images/ams-video-demo-view.PNG)

- Másolja az **URL-címet** ezen az [Azure Media Services bemutató](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) oldalon a jegyzékfájl URL-címéhez.

A képkockák (képek) esetében használja a következő képeket:

![Videó keretének miniatűrje 1](images/ams-video-frame-thumbnails-1.PNG) | ![Videó keretének miniatűrje 2](images/ams-video-frame-thumbnails-2.PNG) | ![Videó keretének miniatűrje 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[1. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [2. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [3. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

1. Nevezze el a projekt **VideoReviews**.

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

Adja hozzá a következő privát tulajdonságokat a névtér **VideoReviews**, az osztály **programhoz**. Frissítse a `AzureEndpoint` és a `CMSubscriptionKey` mezőket a végpont URL-címének és előfizetési kulcsának értékeivel. Ezeket a Azure Portalban található erőforrás **gyors üzembe helyezés** lapján találja.


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

Adja hozzá a következő metódus-definíciót a Namespace **VideoReviews**, a Class **programhoz**.

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
> A Content Moderator-szolgáltatáskulcs rendelkezik egy RPS-alapú (kérések másodpercenkénti száma) sebességkorláttal, amelyet ha túllép, az SDK egy 429-es hibakódú kivételt jelez.
>
> Az ingyenes szint kulcsai egy RPS-korláttal bírnak.

## <a name="add-video-frames-to-the-video-review"></a>Videó-képkockák hozzáadása a videó felülvizsgálatához

A video-képkockákat a **ContentModeratorClient. Reviews. AddVideoFrameUrl** (ha a videós keretek online állapotban vannak) vagy a **ContentModeratorClient. Reviews. AddVideoFrameStream** (ha a videós keretek helyileg vannak tárolva). Ez a rövid útmutató feltételezi, hogy a videós keretek online állapotban vannak, és így használja a **AddVideoFrameUrl**-t. További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

A **AddVideoFrameUrl** a következő szükséges paraméterekkel rendelkezik:
1. Egy MIME-típust tartalmazó karakterlánc, amelynek "Application/JSON" típusúnak kell lennie.
1. Az Content Moderator-csoport neve.
1. A **CreateVideoReviews** által visszaadott videó-felülvizsgálati azonosító.
1. Egy **IList \<VideoFrameBodyItem>** objektum. Minden **VideoFrameBodyItem** objektum képkockát jelöl.

A **VideoFrameBodyItem** a következő tulajdonságokkal rendelkezik:
- **Időbélyeg**. Egy karakterlánc, amely másodpercek alatt azt a videót tartalmazza, amelyből a videó keretet készített.
- **FrameImage**. A videó keretének URL-címe
- **Metaadatok**. Egy IList \<VideoFrameBodyItemMetadataItem> . A **VideoFrameBodyItemMetadataItem** egyszerűen egy kulcs/érték pár. Az érvényes kulcsok a következők:
- **reviewRecommended**. Igaz, ha a videó keretének emberi felülvizsgálata ajánlott.
- **adultScore**. 0 és 1 közötti érték, amely a videó keretében lévő felnőtt tartalom súlyosságát értékeli.
- **a**. Igaz, ha a videó felnőtt tartalmat tartalmaz.
- **racyScore**. 0 és 1 közötti érték, amely a videó keretében a zamatos tartalom súlyosságát értékeli.
- **r**. Értéke true, ha a videó kerete tartalmaz zamatos tartalmat.
- **ReviewerResultTags**. Egy IList \<VideoFrameBodyItemReviewerResultTagsItem> . A **VideoFrameBodyItemReviewerResultTagsItem** egyszerűen egy kulcs/érték pár. Egy alkalmazás ezeket a címkéket használhatja a képkockák rendszerezéséhez.

> [!NOTE]
> Ez a rövid útmutató véletlenszerű értékeket hoz létre a **adultScore** és a **racyScore** tulajdonsághoz. Éles alkalmazásokban ezeket az értékeket az Azure Media Service-ben üzembe helyezett [videó-moderálási szolgáltatásból](video-moderation-api.md)szerezheti be.

Adja hozzá a következő metódus-definíciókat a Namespace VideoReviews, a Class programhoz.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Videó-képkockák áttekintése

A videós áttekintést a **ContentModeratorClient. Reviews. GetVideoFrames** használatával szerezheti be. A **GetVideoFrames** a következő szükséges paraméterekkel rendelkezik:
1. Az Content Moderator-csoport neve.
1. A **CreateVideoReviews** által visszaadott videó-felülvizsgálati azonosító.
1. Az első videó keretének nulla alapú indexe a lekéréshez.
1. A lekérdezni kívánt képkockák száma.

Adja hozzá a következő metódus-definíciót a Namespace VideoReviews, a Class programhoz.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Videó-felülvizsgálati információk beolvasása

A **ContentModeratorClient. Reviews. GetReview**. A **GetReview** a következő szükséges paraméterekkel rendelkezik:
1. Az Content Moderator-csoport neve.
1. A **CreateVideoReviews** által visszaadott videó-felülvizsgálati azonosító.

Adja hozzá a következő metódus-definíciót a Namespace VideoReviews, a Class programhoz.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

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

Adja hozzá a **fő** metódus definícióját a Namespace VideoReviews, a Class programhoz. Végül zárjuk be a program osztályt és a VideoReviews névteret.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

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

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Tekintse meg a videó felülvizsgálatát

Végezetül tekintse meg a videó felülvizsgálatot a Content moderator felülvizsgálati eszköz fiókjában a  > **videó** áttekintése képernyőn.

![Az emberi moderátorok videós áttekintése](images/ams-video-review.PNG)

## <a name="next-steps"></a>Következő lépések

Szerezze be a [Content moderator .net SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) -t és a [Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ehhez és egyéb Content moderator a .net-hez készült gyors útmutatóhoz.
