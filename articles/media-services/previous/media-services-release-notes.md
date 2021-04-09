---
title: Azure Media Services kibocsátási megjegyzések | Microsoft Docs
description: Ez a cikk a Microsoft Azure Media Services v2 kibocsátási megjegyzéseit tárgyalja.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: fe42b98813f1687c78827b355a707b0147d6d2a4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067282"
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services kibocsátási megjegyzések

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ezek a kibocsátási megjegyzések Azure Media Services a korábbi kiadásokból és ismert problémákról származó változásokat összegzik.

> [!NOTE]
> A Media Services v2 nem fog újabb funkciókkal bővülni. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Szeretnénk hallani ügyfeleinktől, hogy az Ön által érintett problémák megoldására összpontosítsanak. Ha problémát szeretne bejelenteni vagy kérdéseket feltenni, küldjön egy bejegyzést a [Azure Media Services MSDN-fórumon]. 

## <a name="known-issues"></a><a name="issues"></a>Ismert problémák
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Általános problémák Media Services

| Probléma | Leírás |
| --- | --- |
| A REST APIban több gyakori HTTP-fejléc sincs megadva. |Ha a REST API használatával fejleszt Media Services alkalmazásokat, úgy találja, hogy a HTTP-fejléc néhány gyakori mezője (beleértve az ügyfél-kérelem-azonosító, a kérelem-azonosító és a RETURN-CLIENT-Request-ID) nem támogatott. A fejlécek egy jövőbeli frissítésben lesznek hozzáadva. |
| Százalék – a kódolás nem engedélyezett. |A Media Services a IAssetFile.Name tulajdonság értékét használja a streaming tartalom URL-címeinek létrehozásakor (például: `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Emiatt a százalékos kódolás nem engedélyezett. A Name (név) tulajdonság értéke nem lehet a következő [százalék-kódolásra fenntartott karakterek](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)egyike:! * ' ();: @ &= + $,/?% # [] ". Emellett a fájlnévkiterjesztés csak egy "." lehet. |
| Az Azure Storage SDK 3. x verziójának részét képező ListBlobs metódus sikertelen. |A Media Services a [2012-02-12](/rest/api/storageservices/version-2012-02-12) -es verzió alapján hoz létre sas URL-címeket. Ha a Storage SDK-val szeretné kilistázni a blobokat egy blob-tárolóban, használja a [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) metódust, amely a Storage SDK 2. x verziójának részét képezi. |
| A Media Services szabályozási mechanizmus korlátozza az erőforrás-használatot olyan alkalmazások esetében, amelyek túlzott kérelmeket tesznek elérhetővé a szolgáltatás számára. Előfordulhat, hogy a szolgáltatás visszaküldi a "szolgáltatás nem érhető el" 503 HTTP-állapotkódot. |További információ: a 503-es HTTP-állapotkód leírása [Media Services hibakódokban](media-services-encoding-error-codes.md). |
| Az entitások lekérdezése esetén a 1 000-es számú entitást egyszerre adja vissza a rendszer, mert a nyilvános REST 2-es verziója korlátozza a lekérdezés eredményét 1 000 eredményre. |Használja a skip és a Take (.NET)/Top (REST) a [jelen .net-példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) leírtak szerint, és [Ez a REST API példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Egyes ügyfelek megismétlődnek a Smooth Streaming jegyzékfájlban. |További információkért tekintse meg [ezt a szakaszt](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objektumok nem szerializálható, és ennek eredményeként nem működnek az Azure cache for Redis. |Ha megpróbálja szerializálni az SDK AssetCollection objektumot, hogy hozzáadja azt az Azure cache-hez a Redis-hez, kivétel keletkezik. |
|A REST API a következő hibaüzenettel válaszol: "a szűrő nem érhető el a REST API jelen verziójában", amikor egy eszköz vagy fiók szintű szűrőt próbál beolvasni.|A szűrő egy újabb API-verzióval lett létrehozva vagy módosítva, mint amelyet a rendszer a szűrő beszerzésére használ. Ez akkor fordulhat elő, ha az ügyfél által használt kód vagy eszközök két API-verziót használnak.  A legjobb megoldás a kód vagy az eszközök frissítése az újabb vagy a két API-verzió használatára.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>REST API korábbi verziók
A Media Services REST API korábbi verzióival kapcsolatos információkért tekintse meg a [Azure Media Services REST API referenciát].

## <a name="february-2021"></a>2021. február

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services v2 API és SDK-k elavult hirdetmény

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Frissítse a Azure Media Services REST API és SDK-kat v3-ra 2024. február 29-én

Mivel a Azure Media Services REST API és a .NET-hez készült ügyféloldali SDK-k 3. verziója több funkciót kínál, mint a 2-es verzió, a Azure Media Services REST API és a .NET-hez és a Java-hoz készült ügyféloldali SDK-k 2. verzióját is kihasználjuk. Javasoljuk, hogy a váltást hamarabb növelje a Azure Media Services REST API és a .NET és a Java ügyféloldali SDK-k 3. verziójának gazdagabb előnyeivel.
A 3. verzió a következőket biztosítja:
 
- nonstop élő esemény támogatása
- ARM REST API-k, a .NET Core, a Node.js, a Python, a Java, a Go és a Ruby ügyféloldali SDK-jai.
- Ügyfél által felügyelt kulcsok, megbízható tárolók integrációja, privát hivatkozások támogatása és [egyebek](https://review.docs.microsoft.com/en-us/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Szükséges művelet:

A munkaterhelések megszakadásának minimalizálásához tekintse át az [áttelepítési útmutatót](../latest/migrate-v-2-v-3-migration-introduction.md) a kód 2 API-ról és SDK-ról a 3-as verziójú API-ra és SDK-ra való átváltásához a 2024. február 29. előtt.
**2024. február 29**-én a Azure Media Services a továbbiakban nem fogadja el a forgalmat a 2-es verziójú REST API, a ARM Management API-verzió 2015-10-01 vagy a 2. verziójú .net-ügyfél SDK-k használatával. Ez magában foglalja a harmadik féltől származó nyílt forráskódú ügyfél SDK-kat, amelyek meghívják a 2. verziójú API-t.  

Tekintse meg a hivatalos [Azure-frissítések bejelentését](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

## <a name="september-2020"></a>2020. szeptember

A következő v2 tulajdonságok többé nem lesznek feltöltve a korábbi feladatok előrehaladási adataival:

* [HistoricalEvents](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

A feladatok előzményeinek beszerzéséhez használja a v2 feladat értesítéseit webhookok vagy üzenetsor-üzenetek használatával az értesítési végpontokkal. További információkért lásd:

* [Az Azure üzenetsor-tároló használata Media Services feladatok értesítéseinek figyeléséhez](media-services-dotnet-check-job-progress-with-queues.md)
* [Az Azure webhookok használata Media Services feladatok értesítéseinek figyeléséhez](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>2020. február

Egyes elemzési adathordozó-processzorok ki lesznek vonva. A nyugdíjazási dátumokért tekintse meg az [örökölt összetevőkkel](legacy-components.md) foglalkozó témakört.

## <a name="september-2019"></a>2019. szeptember

### <a name="deprecation-of-media-processors"></a>Adathordozó-processzorok elavulása

Bejelentjük, hogy a *Azure Media Indexer* elavult, és *Azure Media Indexer 2 előzetes* verzió. A [Azure Media Services video Indexer](../video-indexer/index.yml) ezeket a régi adathordozó-processzorokat váltja fel.

A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](legacy-components.md) ismertető témakört.

Lásd még: [áttelepítés Azure Media Indexer és Azure Media Indexer 2 Azure Media Services video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>2019. augusztus

### <a name="deprecation-of-media-processors"></a>Adathordozó-processzorok elavulása

Bejelentjük a *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzorok elavulása. A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](legacy-components.md) ismertető témakört.

Részletekért lásd: [a Tamás Áttelepítésének Media Encoder standard](./migrate-windows-azure-media-encoder.md) és az [ame áttelepítésének Media Encoder standard](./migrate-azure-media-encoder.md).

## <a name="march-2019"></a>2019. március

A Azure Media Services Media Hyperlapse előzetes verziójának funkciója elavult.

## <a name="december-2018"></a>2018. december

A Azure Media Services Media Hyperlapse előzetes verziójának funkciója hamarosan megszűnik. A 2018. december 19-én kezdődően Media Services a média Hyperlapse többé nem fog változásokat vagy javítást végezni. 2019. március 29-én megszűnik, és már nem érhető el.

## <a name="october-2018"></a>2018. október

### <a name="cmaf-support"></a>CMAF-támogatás

A CMAF és a "CBCS" titkosítási támogatása az Apple HLS (iOS 11 +) és az MPEG-DASH-lejátszók számára, amelyek támogatják a CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatűr sprite

Mostantól használhatja a Media Servicest a web VTT miniatűr sprite létrehozásához a v2 API-k használatával. További információt a [miniatűr sprite létrehozása](generate-thumbnail-sprite.md)című témakörben talál.

## <a name="july-2018"></a>2018. július

A legújabb szervizcsomaggal a szolgáltatás által visszaadott hibaüzenetek kisebb formázási módosításai vannak, ha a feladatok meghiúsulnak, ami azt jelzi, hogy a két vagy több sorba van bontva.

## <a name="may-2018"></a>2018. május 

A 2018. május 12. után az élő csatornák már nem támogatják az RTP/MPEG-2 Transport stream betöltési protokollt. Telepítse át a következőt: RTP/MPEG-2 – RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollok.

## <a name="october-2017-release"></a>2017. októberi kiadás
> [!IMPORTANT] 
> Media Services elavult az Azure Access Control Service hitelesítési kulcsainak támogatása. 2018. június 22-én már nem végezhető el a hitelesítés a Access Control Service kulcsok használatával a kód segítségével a Media Services. Az [Azure ad-alapú hitelesítéshez](media-services-use-aad-auth-to-access-ams-api.md)tartozó Azure Active Directory (Azure ad) használatához frissítenie kell a kódot. Figyelje meg, hogy milyen figyelmeztetések jelennek meg a változással kapcsolatban a Azure Portalban.

### <a name="updates-for-october-2017"></a>Frissítések október 2017
#### <a name="sdks"></a>SDK-k
* A .NET SDK frissítve lett az Azure AD-hitelesítés támogatásához. Az Azure AD-be való gyorsabb áttelepítés érdekében a rendszer eltávolította a Access Control Service hitelesítés támogatását a Nuget.org legújabb .NET SDK-ból. 
* A JAVA SDK frissítve lett az Azure AD-hitelesítés támogatásához. Az Azure AD-hitelesítés támogatása hozzá lett adva a Java SDK-hoz. További információ a Java SDK és a Media Services használatáról: Ismerkedés [a Java ÜGYFÉLOLDALI SDK](media-services-java-how-to-use.md) -val Azure Media Services

#### <a name="file-based-encoding"></a>Fájl alapú kódolás
* Mostantól a prémium szintű kódoló használatával kódolhatja a tartalmat a H. 265 nagy hatékonyságú videó-kódolási (HEVC) videós kodekre. Ha a H. 265-et más codec-ként (például H. 264) választja, nem számítunk fel díjszabási hatást. További információ a HEVC szabadalmi licencekről: [online szolgáltatások használati feltételei](https://azure.microsoft.com/support/legal/).
* A H. 265 (HEVC) videós kodekkel kódolt forrás videóhoz, például a iOS11 vagy a GoPro HERO 6 használatával rögzített videóhoz mostantól használhatja a prémium szintű kódolót vagy a standard kódolót a videók kódolásához. További információ a szabadalmi licencekről: [online szolgáltatások használati feltételei](https://azure.microsoft.com/support/legal/).
* Több nyelvi hangsávot tartalmazó tartalom esetén a nyelvi értékeket a megfelelő fájlformátum-specifikációnak megfelelően kell megcímkézni (például ISO MP4). Ezután a standard kódolóval kódolhatja a tartalmat a streaminghez. Az eredményül kapott adatfolyam-kereső felsorolja az elérhető hang nyelveit.
* A standard kódoló mostantól két új, csak hangvezérelt rendszerkészletet támogat, az "AAC hang" és az "AAC jó minőségű hang". Mindkettő a sztereó, speciális hangkódolási (AAC) kimenetet, a 128 kbps és a 192 kbps sebességet eredményezi.
* A prémium szintű kódoló mostantól bemenetként támogatja a QuickTime/MOV fájlformátumokat. A videó codec-nek a [GitHub-cikkben felsorolt Apple ProRes-típusok](./media-services-media-encoder-standard-formats.md)egyikének kell lennie. A hangnak AAC vagy Pulse Code moduláció (PCM) értékűnek kell lennie. A prémium szintű kódoló nem támogatja például a QuickTime/MOV-fájlokban bemenetként becsomagolt KONKURENS/DVCPro videó használatát. A standard szintű kódoló támogatja ezeket a videó kodekeket.
* A kódolók a következő hibajavításokat végezték el:

    * Mostantól egy bemeneti eszköz használatával is elküldheti a feladatokat. A feladatok befejeződése után módosíthatja az eszközt (például hozzáadhat, törölhet vagy átnevezhet fájlokat az objektumon belül), és további feladatokat küldhet el.
    * A standard kódoló által létrehozott JPEG-miniatűrök minősége javult.
    * A standard szintű kódoló a bemeneti metaadatokat és a miniatűrt a rövid időtartamú videóknál jobban kezeli.
    * A standard kódolóban használt H. 264 dekóder fejlesztése bizonyos ritka összetevők kiiktatása. 

#### <a name="media-analytics"></a>Media Analytics
A Azure Media Redactor általános elérhetősége: Ez a névtelenítésével a kiválasztott személyek arcait elmossa, és ideális megoldás a közbiztonság és a hírek adathordozójának használatára. 

Az új processzor áttekintését ebben a [blogbejegyzésben](https://azure.microsoft.com/blog/azure-media-redactor/)találja. A dokumentációval és a beállításokkal kapcsolatos információkért lásd: [arcok kivonása a Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>2017. június kiadás

A Media Services mostantól támogatja az [Azure ad-alapú hitelesítést](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> A Media Services jelenleg a Access Control Service hitelesítési modellt támogatja. A Access Control Service engedélyezése 2018. június 1-jén elavulttá válik. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="march-2017-release"></a>Március 2017 kiadás

Mostantól a standard kódoló használatával [automatikusan létrehozhatja a bitráta-létrát](media-services-autogen-bitrate-ladder-with-mes.md) úgy, hogy a kódolási feladat létrehozásakor megadta az "adaptív adatfolyam" beállítású karakterláncot. Ha Media Services használatával szeretne adatfolyamként kódolni egy videót, használja az "adaptív streaming" beállításkészletet. Ha testre szeretné szabni az adott forgatókönyvhöz tartozó kódolási beállításkészletet, megkezdheti [ezeket a készleteket](media-services-mes-presets-overview.md).

Mostantól Media Encoder Standard vagy Media Encoder Premium Workflow használatával létrehozhat egy fMP4-adattömböket [generáló kódolási feladatot](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Február 2017 kiadás

2017. április 1-től kezdődően a fiókhoz tartozó, 90 napnál régebbi feladatokat automatikusan törli a rendszer, valamint a hozzá tartozó feladathoz tartozó rekordokat. A törlés akkor is előfordul, ha a rekordok teljes száma nem éri el a maximális kvótát. A feladat/feladat adatainak archiválásához használhatja az [eszközök és kapcsolódó entitások kezelése a Media Services .net SDK-val](media-services-dotnet-manage-entities.md)című témakörben ismertetett kódot.

## <a name="january-2017-release"></a>Január 2017 kiadás

Media Services a streaming-végpont olyan adatfolyam-szolgáltatást jelöl, amely közvetlenül egy ügyfél-lejátszó alkalmazásnak vagy egy Content Delivery Network (CDN) szolgáltatásnak továbbítja a tartalmat a további terjesztéshez. A Media Services zökkenőmentes Azure Content Delivery Network-integrációt is biztosít. A Streamvégpontok szolgáltatás kimenő adatfolyama lehet élő stream, igény szerinti videó vagy az eszköz progresszív letöltése a Media Services-fiókban. Minden Media Services fiók tartalmaz egy alapértelmezett adatfolyam-végpontot. A fiók alatt további folyamatos átviteli végpontok is létrehozhatók. 

Az adatfolyam-végpontok két verziója létezik, 1,0 és 2,0. 2017. január 10-től kezdődően minden újonnan létrehozott Media Services fiók tartalmazza az 2,0-es verziójú alapértelmezett folyamatos átviteli végpontot. Az ehhez a fiókhoz hozzáadott további streaming-végpontok a 2,0-es verzióban is elérhetők. Ez a módosítás nem érinti a meglévő fiókokat. A meglévő folyamatos átviteli végpontok 1,0-es verziójúak, és a 2,0-es verzióra frissíthetők. A módosítással viselkedés, Számlázás és szolgáltatások módosulnak. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

Az 2,15-es verziótól kezdődően Media Services a következő tulajdonságokat adta hozzá a streaming Endpoint entitáshoz:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

További információ ezekről a tulajdonságokról: [streamvégpontok](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>2016. decemberi kiadás

 A Media Services segítségével hozzáférhet a szolgáltatásaihoz tartozó telemetria/metrikai adatokhoz. Az élő csatorna, a folyamatos átviteli végpont és az archív entitások telemetria adatainak gyűjtéséhez használhatja a Media Services aktuális verzióját. További információ: [Media Services telemetria](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a name="july_changes16"></a>Július 2016 kiadás
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>A manifest-fájl (*) frissítései. ISM) kódolási feladatok által generált
Ha egy kódolási feladatot Media Encoder Standard vagy Media Encoder Premium rendszerbe küldenek, a kódolási feladat létrehoz egy [streaming manifest-fájlt](media-services-deliver-content-overview.md) (*. ISM) a kimeneti eszközön. A legújabb szervizcsomaggal a streaming manifest-fájl szintaxisa frissítve lett.

> [!NOTE]
> A streaming manifest (. ISM) fájl szintaxisa belső használatra van fenntartva. Ez a későbbi kiadásokban változhat. Ne módosítsa vagy módosítsa a fájl tartalmát.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Új ügyfél-jegyzékfájl (*. A ISMC-fájl a kimeneti eszközön jön létre, ha egy kódolási feladat egy vagy több MP4-fájlt ad ki
A legújabb szervizcsomaggal kezdődően egy vagy több MP4-fájlt létrehozó kódolási feladat befejezése után a kimeneti eszköz egy streaming Client manifest (*. ISMC) fájlt is tartalmaz. A. ISMC fájl segít a dinamikus adatfolyamok teljesítményének javításában. 

> [!NOTE]
> Az ügyfél-jegyzékfájl (. ISMC) fájljának szintaxisa belső használatra van fenntartva. Ez a későbbi kiadásokban változhat. Ne módosítsa vagy módosítsa a fájl tartalmát.
> 
> 

További információkért tekintse meg [ezt a blogot](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).

### <a name="known-issues"></a>Ismert problémák
Egyes ügyfelek megismétlődnek a Smooth Streaming jegyzékfájlban. További információkért tekintse meg [ezt a szakaszt](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Április 2016 kiadás
### <a name="media-analytics"></a>Media Analytics
 Media Services bevezette a Media Analytics hatékony videó-intelligenciát. További információ: [Media Services Analytics áttekintése](./legacy-components.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (előzetes verzió)
Mostantól a Media Services használatával dinamikusan titkosíthatja HTTP Live Streaming (HLS) tartalmát az Apple FairPlay. A Media Services-licenc kézbesítési szolgáltatásával FairPlay-licenceket is továbbíthat az ügyfeleknek. További információ: "a Azure Media Services használata az Apple FairPlay védett HLS-tartalmak továbbítására."

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Február 2016 kiadás
A .NET-hez készült Media Services SDK (3.5.3) legújabb verziója a Google Widevine kapcsolatos hibajavítást tartalmaz. Nem lehetett újra felhasználni a AssetDeliveryPolicy-t több, a Widevine-mel titkosított eszközre. A hibajavítás részeként a következő tulajdonságot adta hozzá az SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Január 2016 kiadás
A kódoláshoz fenntartott egységek át lettek nevezve a kódolók neveivel való összetévesztés csökkentése érdekében.

Az alapszintű, a standard és a Premium kódolásra fenntartott egységeket az S1, az S2 és az S3 számára fenntartott egységeknek nevezték át. Azok az ügyfelek, akik alapszintű kódolásra fenntartott egységeket használnak ma, a Azure Portalban (és a számlán) található címkével látják el. A standard és prémium szintű ügyfelek az S2 és az S3 címkét látják. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>2015. decemberi kiadás

### <a name="media-encoder-deprecation-announcement"></a>A Media Encoder elavult hirdetménye

 A Media Encoder a Media Encoder Standard kiadásától számítva körülbelül 12 hónapon belül elavulttá válik.

### <a name="azure-sdk-for-php"></a>PHP-hoz készült Azure SDK
Az Azure SDK csapata közzétette az [Azure SDK for php](https://github.com/Azure/azure-sdk-for-php) csomag új kiadását, amely a Media Services frissítéseit és új funkcióit tartalmazza. A PHP-hez készült Media Services SDK mostantól támogatja a legújabb [tartalomvédelem](media-services-content-protection-overview.md) -funkciókat. Ezek a funkciók dinamikus titkosítást biztosítanak az AES és a DRM használatával (PlayReady és Widevine), és nem tartalmaznak jogkivonat-korlátozásokat. Emellett támogatja a [kódolási egységek](media-services-dotnet-encoding-units.md)méretezését is.

További információkért lásd:

* A következő [kódrészletek](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) segítségével gyorsan elsajátíthatja az első lépéseket:
  * **vodworkflow_aes. php**: Ez a php-fájl bemutatja, hogyan használható az aes-128 Dynamic encryption és a Key Delivery Service. A .NET-minta az [AES-128 dinamikus titkosítás és a Key Delivery szolgáltatás használata](media-services-playready-license-template-overview.md)című cikkben ismertetett.
  * **vodworkflow_aes. php**: Ez a php-fájl bemutatja, hogyan használható a PlayReady Dynamic encryption és a License Delivery Service. A .NET-minta alapján a [PlayReady és/vagy a Widevine dinamikus közös titkosítás használata](media-services-protect-with-playready-widevine.md)című cikkben ismertetett.
  * **scale_encoding_units. php**: Ez a php-fájl bemutatja, hogyan méretezhetők a kódolásra fenntartott egységek.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>November 2015 kiadás
 Media Services most a Widevine-licenc kézbesítési szolgáltatását kínálja a felhőben. További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Tekintse meg [ezt az oktatóanyagot](media-services-protect-with-playready-widevine.md) és a [GitHub-tárházat](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)is. 

A Media Services által biztosított Widevine-szolgáltatási szolgáltatások előzetes verzióban érhetők el. További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Október 2015 kiadás
A Media Services jelenleg a következő adatközpontokban érhető el: Dél-Brazília, Nyugat-India, Dél-India és Közép-India. Most már használhatja a Azure Portal a [Media Service-fiókok létrehozásához](media-services-portal-create-account.md) és a [Media Services dokumentációs weblapján](https://azure.microsoft.com/documentation/services/media-services/)leírt különböző feladatok elvégzéséhez. Live Encoding nincs engedélyezve ezekben az adatközpontokban. Továbbá a kódoláshoz fenntartott egységek nem minden típusa érhető el ezekben az adatközpontokban.

* Dél-Brazília: csak a standard és az alapszintű kódolásra fenntartott egység érhető el.
* Nyugat-India, Dél-India és Közép-India: csak az alapszintű kódoláshoz fenntartott egységek érhetők el.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Szeptember 2015 kiadás
A Media Services mostantól igény szerint és élő streameket is képes biztosítani a Widevine moduláris DRM-technológiával. A Widevine-licencek kézbesítéséhez az alábbi szolgáltatási partnereket használhatja:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja a [Media Services .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy egy újabb verziót), vagy a REST API-t. 
* Media Services támogatja az Apple ProRes-videók támogatását. Most már feltöltheti az Apple ProRes vagy más kodekeket használó QuickTime-forrásfájlok fájljait. További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Most már használhatja a Media Encoder Standard a kibontáshoz és az élő archiváláshoz. További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* A következő szűrési frissítések történtek: 
  
  * Mostantól az Apple HLS formátumot is használhatja egy csak hang szűrővel. Ezzel a frissítéssel az URL-címben a (csak hang = hamis) érték megadásával távolíthatja el a csak hangfelvételi sávot.
  * Az eszközökhöz tartozó szűrők meghatározásakor mostantól egyszerre több (legfeljebb három) szűrőt is egyesítheti egyetlen URL-címben.
    
    További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* A Media Services mostantól támogatja az I-kockákat a HLS 4-es verziójában. Az I-frame-támogatás optimalizálja a gyors továbbítási és a visszatekerési műveleteket. Alapértelmezés szerint az összes HLS 4-es verziójú kimenete tartalmazza az I-frame lejátszási listát (EXT-X-I-FRAME-STREAM-INF).
További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Augusztus 2015 kiadás
* A Java-verzió 0.8.0 kiadásának Media Services SDK-je és az új minták mostantól elérhetők. További információkért lásd:
    
* A Azure Media Player multi-audio stream-támogatással lett frissítve. További információt [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)talál.

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Július 2015 kiadás
* A Media Encoder Standard általánosan elérhetővé vált. További információt [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)talál.
  
    A Media Encoder Standard a [jelen szakaszban](./media-services-mes-presets-overview.md)leírtak szerint használja a beállításkészletet. Ha a 4K-kódoláshoz készletet használ, szerezze be a prémium szintű fenntartott egység típusát. További információ: [méretezési kódolás](media-services-scale-media-processing-overview.md).
* Az élő valós idejű feliratok a Media Services és a Media Player használatával lettek használva. További információt [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)talál.

### <a name="media-services-net-sdk-updates"></a>.NET SDK-frissítések Media Services
A Media Services .NET SDK már verziója 3.4.0.0. A következő frissítések történtek: 

* A támogatás az élő archiváláshoz lett implementálva. Nem tölthető le élő archívumot tartalmazó eszköz.
* A támogatás a dinamikus szűrőkhöz lett implementálva.
* A funkciók úgy lettek implementálva, hogy a felhasználók megőrizzenek egy tárolót, miközben törölnek egy eszközt.
* Hibajavítás történt az újrapróbálkozási szabályzatokkal kapcsolatban a csatornákon.
* Media Encoder Premium Workflow engedélyezve.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>2015. június kiadás
### <a name="media-services-net-sdk-updates"></a>.NET SDK-frissítések Media Services
A Media Services .NET SDK már verziója 3.3.0.0. A következő frissítések történtek: 

* Támogatás lett hozzáadva az OpenId Connect Discovery specifikációhoz.
* Támogatás lett hozzáadva a kulcsok átváltásához az identitás-szolgáltató oldalán.

Ha olyan identitás-szolgáltatót használ, amely az OpenID Connect Discovery-dokumentumot (például az Azure AD, a Google és a Salesforce) teszi elérhetővé, utasíthatja Media Servicest a JSON webes tokenek (JWTs) érvényesítésére szolgáló aláíró kulcsok beszerzésére az OpenID Connect Discovery spec-ból. 

További információ: [a JSON-webkulcsok használata az OpenID Connect Discovery spec használatával a Media Services JWT-hitelesítéssel való együttműködéshez](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>2015. május kiadás
A következő új funkciók lettek bejelentve:

* [Az élő kódolás előnézete Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dinamikus jegyzékfájl](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Április 2015 kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítések
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) jelent meg.
* A Media Services REST 2,10-től kezdve a Real-Time üzenetküldési protokoll (RTMP) betöltésére konfigurált csatornák elsődleges és másodlagos betöltési URL-címekkel jönnek létre. További információ: Channel betöltési [konfigurációk](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer frissítve.
* A spanyol nyelv támogatása hozzá lett adva.
* Az XML-formátum új konfigurációja lett hozzáadva.

További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>.NET SDK-frissítések Media Services
A Media Services .NET SDK már verziója 3.2.0.0. A következő frissítések történtek:

* A változás megszakítása: TokenRestrictionTemplate. kibocsátó és TokenRestrictionTemplate. a célközönséget karakterlánc típusúra módosították.
* A rendszer az egyéni újrapróbálkozási szabályzatok létrehozásával kapcsolatos frissítéseket hozott létre.
* Hibajavítások történtek a fájlok feltöltésével és letöltésével kapcsolatban.
* A MediaServicesCredentials osztály most elfogadja az elsődleges és a másodlagos hozzáférés-vezérlési végpontokat a hitelesítéshez.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Március 2015 kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítések
* A Media Services mostantól Content Delivery Network-integrációt biztosít. Az integráció támogatásához a CdnEnabled tulajdonság hozzá lett adva a Streamvégpontok-hoz. A CdnEnabled a 2,9-es verziótól kezdődő REST API-kkal is használható. További információ: [streamvégpontok](/rest/api/media/operations/streamingendpoint). A CdnEnabled a .NET SDK-val is használható a 3.1.0.2 verziótól kezdődően. További információ: [streamvégpontok](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* A Media Encoder Premium Workflow bejelentve. További információ: [a Premium Encoding bemutatása Azure Media Servicesban](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Február 2015 kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítések
A Media Services REST API már 2,9-es verzió. Ettől a verziótól kezdve engedélyezheti a Content Delivery Network integrációját a streaming-végpontokkal. További információ: [streamvégpontok](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Január 2015 kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítések
Bejelentettük a tartalom védelmének általános rendelkezésre állását a dinamikus titkosítással. További információ: a [Media Services a DRM-technológia általános elérhetőségével javítja a folyamatos átvitelt](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>.NET SDK-frissítések Media Services
A Media Services .NET SDK már verziója 3.1.0.1.

Ez a kiadás a Microsoft. WindowsAzure. MediaServices. Client. ContentKeyAuthorization. TokenRestrictionTemplate konstruktort elavultként jelölte meg. Az új konstruktor a TokenType argumentumként veszi igénybe.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>2014. decemberi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítések
* Néhány frissítés és új funkció hozzá lett adva a Media Indexerhoz. További információ: [Azure Media Indexer Version 1.1.6.7 kibocsátási megjegyzései](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Új REST API lett hozzáadva, amely a kódoláshoz fenntartott egységek frissítésére használható. További információ: [EncodingReservedUnitType a REST](/rest/api/media/operations/encodingreservedunittype)-tel.
* CORS-támogatás lett hozzáadva a Key Delivery Service-hez.
* Teljesítménybeli tökéletesítések történtek az engedélyezési házirend beállításainak lekérdezése során.
* A kínai adatközpontban a [kulcs kézbesítési URL-címe](/rest/api/media/operations/contentkey#get_delivery_service_url) mostantól ügyfélként (akárcsak más adatközpontok esetében).
* A HLS automatikus megcélzási időtartama hozzá lett adva. Élő közvetítés esetén a HLS mindig dinamikusan van csomagolva. Alapértelmezés szerint a Media Services automatikusan kiszámítja a HLS szegmens csomagolási arányát (FragmentsPerSegment) a kulcsképek intervalluma (KeyFrameInterval) alapján. Ezt a metódust az élő kódolótól kapott Pictures (GOP) csoportnak is nevezzük. További információ: [Media Services élő közvetítés használata](/previous-versions/azure/dn783466(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a>.NET SDK-frissítések Media Services
A [Media Services .net SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) már verziója 3.1.0.0. A következő frissítések történtek:

* A .NET SDK-függőség frissítve lett a .NET 4,5-keretrendszerre.
* Új API, amely a kódoláshoz fenntartott egységek frissítésére használható. További információt a [fenntartott egység típusának frissítése és a kódoláshoz fenntartott egységek a .NET használatával történő növelését](media-services-dotnet-encoding-units.md)ismertető témakörben talál.
* JWT-támogatás lett hozzáadva a jogkivonat-hitelesítéshez. További információ: [JWT-jogkivonat hitelesítése Media Services és dinamikus titkosítással](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* A rendszer hozzáadta a BeginDate és a ExpirationDate relatív eltolásait a PlayReady-licenc sablonjában.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>November 2014 kiadás
* Most már használhatja a Media Servicest az élő Smooth Streaming (fMP4) tartalmak TLS-kapcsolaton keresztüli betöltéséhez. A TLS-kapcsolat betöltéséhez frissítse a betöltési URL-címet HTTPS-re. A Media Services jelenleg nem támogatja a TLS-t egyéni tartományokkal. További információ az élő közvetítésről: [Azure Media Services élő közvetítés használata](/previous-versions/azure/dn783466(v=azure.100)).
* Jelenleg nem lehet bevenni egy RTMP élő streamet TLS-kapcsolaton keresztül.
* A TLS-t csak akkor továbbíthatja, ha az adatfolyam-végpontot, amelyről a tartalmat a 2014 szeptember 10. után hozták létre. Ha a folyamatos átviteli URL-címek a 2014. szeptember 10. után létrehozott streaming-végpontokon alapulnak, az URL-cím "streaming.mediaservices.windows.net" (az új formátum) tartalmazza. A "origin.mediaservices.windows.net" (a régi) formátumot tartalmazó streaming URL-címek nem támogatják a TLS-t. Ha az URL-cím régi formátumú, és TLS protokollon keresztül szeretne továbbítani, [hozzon létre egy új folyamatos átviteli végpontot](media-services-portal-manage-streaming-endpoints.md). A tartalom TLS protokollon keresztüli továbbításához használja az új streaming végponton alapuló URL-címeket.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
A .NET-bővítmények Media Services SDK verziója mostantól 2.0.0.3.

A .NET-hez készült Media Services SDK már verziója 3.0.0.8. A következő frissítések történtek:

* Az újrabontás az újrapróbálkozási házirend osztályaiban lett implementálva.
* Felhasználói ügynök karakterlánca lett hozzáadva a HTTP-kérelmek fejlécéhez.
* Egy NuGet-visszaállítási Build lépés lett hozzáadva.
* A x509 tanúsítványnak az adattárból való használatára történtek a forgatókönyv-tesztek.
* Az érvényesítési beállítások lettek hozzáadva a csatorna-és a streaming-befejezési frissítéshez.

### <a name="new-github-repository-to-host-media-services-samples"></a>Új GitHub-adattár Media Services-minták üzemeltetéséhez
A minták a [Media Services Samples GitHub-tárházban](https://github.com/Azure/Azure-Media-Services-Samples)találhatók.

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Szeptember 2014 kiadás
A Media Services REST-metaadatok már 2,7-es verzióval rendelkeznek. A legújabb REST-frissítésekkel kapcsolatos további információkért tekintse meg a [Media Services REST API-referenciát](/rest/api/media/operations/azure-media-services-rest-api-reference).

A .NET-hez készült Media Services SDK már verziója 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Kompatibilitástörő változások
* A forrás átnevezve a következőre: [streamvégpontok].
* Az alapértelmezett viselkedés változása történt, amikor a Azure Portal használatával kódolja és közzéteszi az MP4-fájlokat.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Az általánosan elérhető kiadás részét képező új funkciók/forgatókönyvek
* A Media Indexer adathordozó-feldolgozó be lett vezetve. További információ: [médiafájlok indexelése a Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* A [streamvégpontok] entitás használatával egyéni tartomány (gazdagép) neve adható hozzá.
  
    Ha egyéni tartománynevet szeretne használni a Media Services streaming-végpont neveként, adja hozzá az egyéni állomásneveket a folyamatos átviteli végponthoz. Egyéni állomásnevek hozzáadásához használja a Media Services REST API-kat vagy a .NET SDK-t.
  
    A következő szempontokat kell figyelembe venni:
  
  * Az Egyéni tartománynév tulajdonosának kell lennie.
  * A tartománynév tulajdonjogát Media Services kell érvényesíteni. A tartomány ellenőrzéséhez hozzon létre egy CName-t, amely leképezi a MediaServicesAccountId-szülőtartomány a DNS-Mediaservices-DNS-zóna ellenőrzéséhez.
  * Létre kell hoznia egy másik CName-t, amely leképezi az egyéni állomásnevet (például sports.contoso.com) a Media Services Streamvégpontok-állomásnévre (például amstest.streaming.mediaservices.windows.net).

    További információkért tekintse meg a CustomHostNames tulajdonságot a [streamvégpontok](/rest/api/media/operations/streamingendpoint) cikkben.

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>A nyilvános előzetes kiadás részét képező új funkciók/forgatókönyvek
* Élő közvetítés előzetes verziója. További információ: [Media Services élő közvetítés használata](/previous-versions/azure/dn783466(v=azure.100)).
* Key Delivery szolgáltatás. További információ: [az AES-128 dinamikus titkosítás és a Key Delivery Service használata](/previous-versions/azure/dn783457(v=azure.100)).
* AES dinamikus titkosítás. További információ: [az AES-128 dinamikus titkosítás és a Key Delivery Service használata](/previous-versions/azure/dn783457(v=azure.100)).
* PlayReady-licenc kézbesítési szolgáltatása. 
* PlayReady dinamikus titkosítás. 
* Media Services PlayReady-licenc sablonja. További információ: [Media Services PlayReady-sablon áttekintése].
* Stream Storage – titkosított eszközök. További információ: [stream Storage – titkosított tartalom](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Augusztus 2014 kiadás
Az adategységek kódolásakor a rendszer kimeneti eszközt állít elő a kódolási feladatok befejeződése után. Ebben a kiadásban a Media Services kódoló metaadatokat készített a kimeneti eszközökről. Ettől a kiadástól kezdve a kódoló metaadatokat is létrehoz a bemeneti eszközökről. További információ: a [bemeneti metaadatok] és a [kimeneti metaadatok].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Július 2014 kiadás
A következő hibajavítások történtek a Azure Media Services csomagoló és titkosító számára:

* Ha egy élő archivált eszközt továbbítanak a HLS, csak a hanglejátszási adatok állnak vissza: a probléma kijavítva lett, és a hang és a videó is játszható.
* Ha egy eszköz HLS és AES 128 bites boríték-titkosításba van csomagolva, a csomagolt adatfolyamok nem kerülnek vissza Android-eszközökre: Ez a hiba javítva lett, és a csomagolt adatfolyam a HLS-t támogató Android-eszközökön is visszakerül.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>2014. május kiadás
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Általános Media Services frissítések
Mostantól használhatja a [dinamikus csomagolást] a HLS 3-as verziójának továbbítására. A HLS 3-as verziójának továbbításához adja hozzá a következő formátumot a forrás-lokátor elérési útjához: *. ISM/manifest (Format = m3u8-AAPL-v3). További információkért tekintse meg [ezt a fórumot](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

A dinamikus csomagolás mostantól támogatja a PlayReady-mel titkosított HLS (3-as verzió és 4-es verzió) használatát a PlayReady-mel statikusan titkosított Smooth Streaming alapján. A Smooth Streaming PlayReady való titkosításával kapcsolatos információkért lásd: [Smooth streaming védelme a PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>.NET SDK-frissítések Media Services
A Media Services .NET SDK már verziója 3.0.0.5. A következő frissítések történtek:

* A sebesség és a rugalmasság jobb, ha adathordozó-eszközöket tölt fel és tölt le.
* Az újrapróbálkozási logikával és az átmeneti kivételek kezelésére vonatkozó előrelépések történtek: 
  
  * A rendszer átmeneti hibák észlelését és újrapróbálkozási logikát fejlesztett ki a lekérdezés, a módosítások mentése és a fájlok feltöltése vagy letöltése során okozott kivételek esetében. 
  * Amikor webes kivételeket kap (például egy Access Control Service jogkivonat-kérelemben), a végzetes hibák gyorsabban meghiúsulnak.

További információ: [az újrapróbálkozási logika a .net-es Media Services SDK-ban].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Január/február 2014 kiadás
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 és 3.0.0.3
A 3.0.0.1 és a 3.0.0.2 változásai a következők:

* A OrderBy-utasításokkal rendelkező LINQ-lekérdezések használatával kapcsolatos problémák javítva lettek.
* A [githubon] lévő tesztelési megoldások egység-és forgatókönyv-alapú tesztek alapján lettek felosztva.

További információ a változásokról: [Media Services .net SDK 3.0.0.1 és 3.0.0.2 kiadások](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

A 3.0.0.3 verziójában a következő módosítások történtek:

* Az Azure Storage-függőségek frissítve lettek a 3.0.3.0 verzió használatára.
* Az 3,0-es verzióra visszamenőleges kompatibilitási probléma történt. *.* kiadások.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>2013. decemberi kiadás
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>.NET SDK-3.0.0.0 Media Services
> [!NOTE]
> A 3.0. x. x verziók nem kompatibilisek visszafelé a 2.4. x. x kiadásokkal.
> 
> 

A Media Services SDK legújabb verziója mostantól 3.0.0.0. A legújabb csomagot letöltheti a NuGet, vagy lekérheti a BITS-t a [githubról].

A Media Services SDK 3.0.0.0 kezdve újra felhasználhatja az [Azure AD Access Control Service](/previous-versions/azure/azure-services/hh147631(v=azure.100)) tokeneket. További információ: "Access Control Service tokenek újrafelhasználása" című rész a [Kapcsolódás a Media Serviceshoz a .net-hez készült Media Services SDK-val](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>.NET SDK-bővítmények 2.0.0.0 Media Services
 A Media Services .NET SDK bővítmények olyan bővítményi metódusok és segítő függvények, amelyek leegyszerűsítik a kódot, és megkönnyítik a fejlesztést a Media Servicesokkal. A legújabb BITS-t [Media Services .net SDK-bővítmények](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)segítségével szerezheti be.

## <a name="november-2013-release"></a><a id="november_changes_13"></a>November 2013 kiadás
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>.NET SDK-változások Media Services
Ettől a verziótól kezdve a .NET-hez készült Media Services SDK kezeli az átmeneti hibák hibáit, amelyek akkor fordulnak elő, amikor a rendszer a Media Services REST API réteget hívja meg.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Augusztus 2013 kiadás
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Az Azure SDK-eszközökben található PowerShell-parancsmagok Media Services
A következő Media Services PowerShell-parancsmagok már szerepelnek az [Azure SDK-eszközökben](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Például: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Például: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Például: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Például: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>2013. június kiadás
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Media Services változások
Az ebben a szakaszban említett módosítások a június 2013 Media Services kiadásában szereplő frissítések:

* Lehetőség több Storage-fiók összekapcsolására egy Media Service-fiókkal. 
    * StorageAccount
    * Asset. StorageAccountName és Asset. StorageAccount
* A feladat. prioritás frissítésének lehetősége. 
* Értesítésekkel kapcsolatos entitások és tulajdonságok: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Feladat
* Asset. URI 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>.NET SDK-változások Media Services
Az alábbi módosítások a június 2013 Media Services SDK kiadásában találhatók. A legújabb Media Services SDK a GitHubon érhető el.

* A 2.3.0.0 verziótól kezdődően a Media Services SDK támogatja több Storage-fiók összekapcsolását egy Media Services-fiókkal. A következő API-k támogatják ezt a funkciót:
  
    * IStorageAccount típusa
    * Microsoft. WindowsAzure. MediaServices. Client. csatlakozáshoz szükséges cloudmediacontext. StorageAccounts tulajdonság
    * StorageAccount tulajdonság
    * StorageAccountName tulajdonság
  
      További információ: Media Services- [eszközök kezelése több Storage-fiókon keresztül](/previous-versions/azure/dn271889(v=azure.100)).
* Értesítésekkel kapcsolatos API-k. A 2.2.0.0 verziótól kezdődően megfigyelheti az Azure üzenetsor Storage szolgáltatással kapcsolatos értesítéseit. További információ: a [Media Services feladatok kezelésével kapcsolatos értesítések kezelése](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Microsoft. WindowsAzure. MediaServices. Client. IJob. JobNotificationSubscriptions tulajdonság
    * Microsoft. WindowsAzure. MediaServices. Client. INotificationEndPoint típus
    * Microsoft. WindowsAzure. MediaServices. Client. IJobNotificationSubscription típus
    * Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointCollection típus
    * Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointType típus
* Függőség a Storage ügyféloldali SDK 2,0 (Microsoft.WindowsAzure.StorageClient.dll)
* Függőség a OData 5,5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>2012. decemberi kiadás
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>.NET SDK-változások Media Services
* IntelliSense: hiányzó IntelliSense-dokumentáció lett hozzáadva számos típushoz.
* Microsoft. Practices. TransientFaultHandling. Core: probléma merült fel, ha az SDK továbbra is függőséggel rendelkezik a szerelvény egy régebbi verziójával. Az SDK most a szerelvény verziójának 5.1.1209.1 hivatkozik.

Javítások a november 2012 SDK-ban talált problémákhoz:

* IAsset. Locators. Count: Ez a szám mostantól megfelelően jelentett az új IAsset-felületeken, miután az összes lokátor törölve lett.
* IAssetFile. ContentFileSize: ez az érték mostantól megfelelően be van állítva az IAssetFile. upload (filepath) feltöltés után.
* IAssetFile. ContentFileSize: ezt a tulajdonságot most már be lehet állítani az adategység létrehozásakor. Korábban csak olvasható.
* IAssetFile. upload (filepath): probléma merült fel, ha a szinkron feltöltési módszer a következő hibát dobta le, amikor több fájlt töltöttek fel az eszközre. A hiba a következő: "a kiszolgáló nem tudta hitelesíteni a kérelmet. Győződjön meg arról, hogy az engedélyezési fejléc értéke helyesen van kialakítva, beleértve az aláírást is. "
* IAssetFile. UploadAsync: a rendszer kijavított egy problémát, amely a fájlok egyidejű feltöltését öt fájlba korlátozza.
* IAssetFile. UploadProgressChanged: ezt az eseményt az SDK már megadja.
* IAssetFile. DownloadAsync (karakterlánc, BlobTransferClient, ILocator, CancellationToken): a metódus túlterheltsége már meg van megadva.
* IAssetFile. DownloadAsync: a rendszer kijavított egy problémát, amely a fájlok egyidejű letöltését öt fájlba korlátozza.
* IAssetFile. Delete (): hiba történt a törlés meghívásakor, ha egy fájlt nem töltöttek fel a IAssetFile.
* Feladatok: a probléma kijavítva lett, ha egy feladattal nem hoztak létre semmilyen feladatot a "MP4 – Smooth Streams" feladat "PlayReady-védelmi feladattal" való láncolásával.
* EncryptionUtils. GetCertificateFromStore (): Ez a metódus már nem tartalmaz null értékű hivatkozást, mert hiba történt a tanúsítvány konfigurációs problémái alapján történő megkeresése során.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>November 2012 kiadás
Az ebben a szakaszban említett módosítások a november 2012 (Version 2.0.0.0) SDK-ban is frissültek. Előfordulhat, hogy ezek a módosítások a június 2012 előzetes verziójú SDK kiadásához írt kódokat módosítják vagy átírják.

* Objektumok
  
    * A IAsset. Create (assetName) az *egyetlen* eszköz-létrehozási függvény. A IAsset. Create fájl már nem tölt fel fájlokat a metódus hívásának részeként. IAssetFile használata a feltöltéshez.
    * A IAsset. publish metódus és a AssetState. publish enumerálás értéke el lett távolítva a Services SDK-ból. Az ezen az értéken alapuló kódokat újra kell írni.
* FileInfo
  
    * Ez az osztály el lett távolítva, és lecserélte a IAssetFile.
  
* IAssetFiles
  
    * A IAssetFile lecseréli a FileInfo, és más viselkedéssel rendelkezik. A használatához hozza létre a IAssetFiles objektumot, majd a Media Services SDK vagy a Storage SDK használatával töltse fel a fájlt. A következő IAssetFile. a túlterhelések fel lesznek használva:
  
        * IAssetFile. upload (filePath): Ez a szinkron módszer blokkolja a szálat, és csak akkor ajánlott, ha egyetlen fájlt tölt fel.
        * IAssetFile. UploadAsync (filePath, blobTransferClient, lokátor, cancellationToken): ez az aszinkron módszer az előnyben részesített feltöltési mechanizmus. 
    
            Ismert hiba: Ha a lemondási jogkivonatot használja, a feltöltés meg lesz szakítva. A feladatok számos lemondási állapottal rendelkezhetnek. A kivételeket megfelelően kell elkapni és kezelni.
* Keresők
  
    * A forrás-specifikus verziók el lettek távolítva. Az SAS-specifikus környezet. A lokátorok. a CreateSasLocator (eszköz, accessPolicy) az általános elérhetőség alapján elavultként vagy eltávolítva lesznek megjelölve. A frissített viselkedésért tekintse meg a "új funkciók" szakaszban található "lokátorok" szakaszt.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Június 2012 előzetes kiadás
Az SDK novemberi kiadásában a következő funkciók voltak Újdonságok:

* Entitások törlése
  
    * A IAsset, a IAssetFile, a ILocator, a IAccessPolicy és a IContentKey objektum már törölve van az objektum szintjén, azaz a IObject. Delete () függvényt, ahelyett, hogy törölni kellene a gyűjteményben, azaz a csatlakozáshoz szükséges cloudmediacontext. ObjCollection. Delete (objInstance).
* Keresők
  
    * A lokátorokat most a CreateLocator metódus használatával kell létrehozni. Az LocatorType. SAS vagy a LocatorType. OnDemandOrigin enumerálási értékeket kell használniuk a létrehozni kívánt adott típusú lokátor argumentumként.
    * A rendszer új tulajdonságokat adott hozzá a lokátorokhoz, hogy könnyebb legyen a tartalomhoz használható URI-k beszerzése. A lokátorok újratervezése nagyobb rugalmasságot biztosít a jövőbeli külső bővítmények számára, és növeli a média-ügyfélalkalmazások egyszerű használatát.
* Aszinkron módszer támogatása
  
    * Az aszinkron támogatás minden metódushoz hozzá lett adva.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Azure Media Services REST API-hivatkozás]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Bemeneti metaadatok]: ./media-services-input-metadata-schema.md
[Kimeneti metaadatok]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[Streamvégpontok]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Media Services PlayReady-sablon áttekintése]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Dinamikus csomagolás]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Újrapróbálkozási logika a .NET-hez készült Media Services SDK-ban]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)