---
title: Élő stream Media Services v3
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan közvetítheti élőben a Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: b92d26da837cab72a4c4404a7b5b3de5d3116480
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043355"
---
# <a name="tutorial-stream-live-with-media-services"></a>Oktatóanyag: élő közvetítés Media Services

> [!NOTE]
> Annak ellenére, hogy az oktatóanyag [.net SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) -példákat használ, az általános lépések ugyanazok a [REST API](/rest/api/media/liveevents), a [CLI](/cli/azure/ams/live-event?view=azure-cli-latest)vagy más támogatott [SDK](media-services-apis-overview.md#sdks)-k esetén.

Azure Media Services az élő adatfolyam-tartalmak feldolgozásához az [élő események](/rest/api/media/liveevents) felelősek. Az élő esemény egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet aztán egy élő kódolóhoz biztosít. Az élő esemény fogadja az élő kódoló élő bemeneti streamjét, és egy vagy több [folyamatos átviteli végponton](/rest/api/media/streamingendpoints)keresztül teszi elérhetővé a folyamatos átvitelt. Az élő események egy előzetes verziójú végpontot (előzetes verziójú URL-címet) is biztosítanak, amelyet a további feldolgozás és a továbbítás előtt a stream előzetes verziójának megtekintéséhez és ellenőrzéséhez használhat. Ez az oktatóprogram bemutatja, hogyan hozhat létre **átmenő** típusú élő eseményt a .NET Core használatával.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a következő témakörben ismertetett minta alkalmazást:.
> * Vizsgálja meg az élő közvetítést végző kódot.
> * Tekintse meg az eseményt [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) címen [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Az erőforrások eltávolítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének feltételei a következők:

- Telepítse a Visual Studio Code vagy a Visual Studio alkalmazást.
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).<br/>Ügyeljen arra, hogy jegyezze fel az erőforráscsoport neveként használt értékeket, és Media Services a fiók nevét.
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](./access-api-howto.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.
- Egy olyan kamera vagy eszköz (például laptop), amely az események közvetítésére szolgál.
- Helyszíni élő kódoló, amely a kamerából származó jeleket átalakítja a Media Services élő közvetítési szolgáltatásnak küldött adatfolyamokra: [ajánlott helyszíni élő kódolók](recommended-on-premises-live-encoders.md). A streamnek **RTMP** vagy **Smooth Streaming** formátumúnak kell lennie.

> [!TIP]
> Mielőtt folytatná, mindenképp tekintse át [a Media Services 3-as verziójával megvalósított élő streamelést](live-streaming-overview.md) bemutató cikket. 

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Az elő streamelési minta az [Élő](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) mappában található.

Nyissa meg [appsettings.jsa](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) letöltött projektben. Cserélje le az értékeket az API-khoz [való hozzáféréshez](./access-api-howto.md)kapott hitelesítő adatokkal.

> [!IMPORTANT]
> Ez a minta minden erőforráshoz egyedi utótagot használ. Ha megszakítja a hibakeresést, vagy leállítja az alkalmazást a rendszeren keresztül, akkor a fiókjában több élő esemény fog megjelenni. <br/>Győződjön meg arról, hogy leállítja a futó élő eseményeket. Ellenkező esetben a **számlázás**!

## <a name="examine-the-code-that-performs-live-streaming"></a>Az élő streamelést végrehajtó kód vizsgálata

Ez a szakasz a *MediaV3LiveApp* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta egyedi utótagot hoz létre minden erőforráshoz, hogy ne legyenek a nevek ütközései, ha a mintát többször is futtatja a tisztítás nélkül.

> [!IMPORTANT]
> Ez a minta minden erőforráshoz egyedi utótagot használ. Ha megszakítja a hibakeresést, vagy leállítja az alkalmazást a rendszeren keresztül, akkor a fiókjában több élő esemény fog megjelenni. <br/>
> Győződjön meg arról, hogy leállítja a futó élő eseményeket. Ellenkező esetben a **számlázás**!

### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Élő esemény létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre **áteresztő** típusú élő eseményt (a LiveEventEncodingType beállítása none). További információ a rendelkezésre álló élő események típusairól: [élő eseménytípus](live-events-outputs-concept.md#live-event-types). 
 
Néhány dolog, amit érdemes megadnia az élő esemény létrehozásakor:

* Media Services hely.
* Az élő esemény folyamatos átviteli protokollja (jelenleg az RTMP és a Smooth Streaming protokollok támogatottak).<br/>A protokoll beállítás nem módosítható, amíg az élő esemény vagy a hozzá tartozó élő kimenet fut. Ha eltérő protokollokra van szüksége, hozzon létre külön élő eseményt az egyes streaming protokollokhoz.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek számára engedélyezett az élő esemény Videójának beolvasása. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nincs megadva IP-cím, és nincs szabály definíciója, akkor a rendszer nem engedélyezi az IP-címet. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címnek a következő formátumok egyikében kell lennie: IpV4-cím, amely négy számot vagy CIDR-címtartományt tartalmaz.
* Az esemény létrehozásakor megadhatja az automatikus indítást. <br/>Ha az autostart értéke TRUE (igaz), a rendszer az élő eseményt a létrehozás után indítja el. Ez azt jelenti, hogy a számlázás azonnal elindul, amint az élő esemény fut. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását. További információ: [élő események állapota és számlázása](live-event-states-billing.md).
* Ahhoz, hogy a betöltési URL-cím prediktív, állítsa a "Vanity" módot. Részletes információk: élő események betöltésének [URL-címei](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

Az élő esemény létrehozása után betöltheti a betöltési URL-címeket, amelyeket az élő kódolóhoz fog adni. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Az előnézeti URL-cím lekérése

A previewEndpoint segítségével tekintse meg az előnézetet, és győződjön meg róla, hogy a kódolóról érkező bemenet valóban beérkezik.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előnézet URL-címére áramlik.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Élő események és élő kimenetek létrehozása és kezelése

Ha a stream az élő eseménybe áramlik, megkezdheti a folyamatos átviteli eseményt egy eszköz, egy élő kimenet és a folyamatos átviteli lokátor létrehozásával. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára.

#### <a name="create-an-asset"></a>Adategység létrehozása

Hozzon létre egy eszközt a használni kívánt élő kimenethez.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Élő kimenet létrehozása

Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. Ha törli az élő kimenetet, nem törli az objektum mögöttes eszközét és tartalmát.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Adatfolyam-kereső létrehozása

> [!NOTE]
> A Media Services-fiók létrehozásakor a rendszer egy **alapértelmezett** folyamatos átviteli végpontot ad hozzá a fiókhoz a **leállított** állapotban. A tartalom folyamatos átvitelének megkezdéséhez, valamint a [dinamikus csomagolás](dynamic-packaging-overview.md) és a dinamikus titkosítás kihasználásához a adatfolyam-továbbítási végpontnak **futó** állapotban kell lennie.

Ha az élő kimeneti eszközt egy folyamatos átviteli lokátor használatával teszi közzé, az élő esemény (a DVR-ablak hosszára számítva) továbbra is megtekinthető marad, amíg a streaming-kereső lejárata vagy törlése nem történik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>A Media Services-fiók erőforrásainak eltávolítása

Ha végzett a folyamatos átviteli eseményekkel, és törölni szeretné a korábban kiépített erőforrásokat, kövesse az alábbi eljárást:

* Állítsa le a stream továbbítását a kódolóban.
* Állítsa le az élő eseményt. Az élő esemény leállítása után nem számítunk fel díjat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. Ha az élő esemény leállított állapotban van, nem számítunk fel díjat.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Esemény megtekintése

Az esemény megtekintéséhez másolja az adatfolyam-kereső létrehozása című témakörben leírtak szerint kapott streaming URL-címet. Az Ön által választott médialejátszó is használható. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) elérhető a stream teszteléséhez https://ampdemo.azureedge.net .

Az élő esemény automatikusan átalakítja az eseményeket az igény szerinti tartalomba, ha leállt. Az esemény leállítása és törlése után a felhasználók az archivált tartalmat igény szerint videóként is továbbíthatja, feltéve, hogy nem törli az eszközt. Egy eszköz nem törölhető, ha egy esemény használja. először törölni kell az eseményt.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Az élő esemény elhagyása a számlázási költségekkel. Vegye figyelembe, hogy ha a projekt/program összeomlik vagy valamilyen okból bezárult, akkor az élő eseményt számlázási állapotban hagyhatja.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

[Videofájlok streamelése](stream-files-tutorial-with-api.md)
 
