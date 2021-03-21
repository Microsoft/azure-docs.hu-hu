---
title: Tartalom továbbítása az ügyfeleknek
description: Ez a témakör áttekintést nyújt arról, hogy mi vesz részt a tartalomnak a Azure Media Servicessal való megvalósításában.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1ad89345a2779766fde4559758e61dff92023741
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016610"
---
# <a name="deliver-content-to-customers"></a>Tartalom továbbítása az ügyfeleknek

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ha streaming-vagy igény szerinti tartalmat továbbít az ügyfeleknek, a cél az, hogy magas színvonalú videót nyújtson különféle eszközökhöz különböző hálózati körülmények között.

A cél elérése érdekében a következőket teheti:

* Kódolja a streamet egy többszörös sávszélességű (adaptív sávszélességű) video streambe. Ez gondoskodik a minőségi és hálózati feltételekről.
* Microsoft Azure Media Services [dinamikus csomagolás](media-services-dynamic-packaging-overview.md) használatával dinamikusan újracsomagolhatja az adatfolyamot különböző protokollokra. Ez gondoskodik a különböző eszközökön található folyamatos átvitelről. Media Services támogatja a következő adaptív sávszélességű adatfolyam-továbbítási technológiák szállítását: <br/>
    * **Http Live Streaming** (HLS) – adja hozzá a "(Format = m3u8-AAPL)" elérési utat az URL-cím "/manifest" részéhez, hogy a folyamatos átviteli forrás kiszolgáló visszaadja a HLS-tartalmat az **Apple iOS** natív eszközökön (részletekért lásd: [lokátorok](#locators) és [URL-címek](#URLs)),
    * **MPEG-Dash** -add "(Format = mpd-Time-CSF)" az URL "/manifest" részének elérési útját, hogy az adatfolyam-küldő kiszolgáló visszaadja az MPEG-Dash-t (részletekért lásd: [lokátorok](#locators) és [URL-címek](#URLs)),
    * **Smooth streaming**.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

Ez a cikk áttekintést nyújt a tartalommal kapcsolatos fontos fogalmakról.

Az ismert problémák megtekintéséhez tekintse meg az [ismert problémák](media-services-deliver-content-overview.md#known-issues)című témakört.

## <a name="dynamic-packaging"></a>Dinamikus csomagolás
A Media Services által biztosított dinamikus csomagolással az adaptív sávszélességű MP4-vagy Smooth Streaming-kódolású tartalmakat Media Services (MPEG-DASH, HLS, Smooth Streaming) által támogatott adatfolyam-formátumokba helyezheti el anélkül, hogy újra kellene csomagolnia ezeket a streaming formátumokba. Javasoljuk, hogy a tartalmat dinamikus csomagolással kézbesítse.

A dinamikus csomagolás kihasználásához kódolni kell a köztes (forrás) fájlt egy adaptív sávszélességű MP4-vagy adaptív sávszélesség-Smooth Streaming fájlokra.

A dinamikus csomagolással egyetlen tárolási formátumban tárolhatja és fizetheti a fájlokat. A Media Services a kérések alapján kiépíti és kiszolgálja a megfelelő választ.

A dinamikus csomagolás a standard és a prémium szintű streaming végpontok számára érhető el. 

További információ: [dinamikus csomagolás](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek
Megadhatja az eszközökhöz tartozó szűrőket Media Services használatával. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek segítenek az ügyfeleknek olyan dolgokban, mint például a videó egy adott szakaszának lejátszása, vagy az ügyfél által kezelhető hang-és videó-átadások részhalmazának meghatározása (az adategységhez társított összes kiadatás helyett). Ez a szűrés olyan *dinamikus jegyzékfájlokon* keresztül érhető el, amelyek akkor jönnek létre, amikor az ügyfél egy vagy több megadott szűrőn alapuló videót továbbít.

További információ: [szűrők és dinamikus jegyzékfájlok](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a name="locators"></a>Keresők
Ahhoz, hogy a felhasználó számára elérhető legyen egy URL-cím, amely használható a tartalom továbbításához vagy letöltéséhez, először közzé kell tennie az eszközt egy lokátor létrehozásával. A lokátor egy belépési pontot biztosít az adategységben található fájlok eléréséhez. A Media Services két lokátortípust támogat:

* OnDemandOrigin-lokátorok. Ezek az adathordozók (például MPEG-DASH, HLS vagy Smooth Streaming) vagy fokozatosan letöltött fájlok továbbítására szolgálnak.
* Közös hozzáférésű aláírási (SAS) URL-lokátorok. Ezek a médiafájlok a helyi számítógépre való letöltésére szolgálnak.

A *hozzáférési szabályzatok* határozzák meg az engedélyeket (például olvasás, írás és Listázás) és az időtartamot, amelyhez az ügyfél hozzáfér egy adott eszközhöz. Vegye figyelembe, hogy a List engedély (AccessPermissions. list) nem használható OnDemandOrigin-lokátor létrehozásához.

A lokátor lejárati dátummal rendelkezik. A Azure Portal 100 év lejárati dátumot állít be a jövőben a lokátorok számára.

> [!NOTE]
> Ha a Azure Portalt az 2015. március előtt a lokátorok létrehozásához használta, a lokátorok két év után lejárnak.
> 
> 

A lokátor lejárati idejének módosításához használjon [REST](/rest/api/media/operations/locator#update_a_locator) vagy [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator) API-t. Ne feledje, hogy a SAS-lokátor lejárati idejének módosításával az URL-cím is megváltozik.

A lokátorok nem úgy vannak kialakítva, hogy a felhasználónkénti hozzáférés-vezérlést kezeljék. A digitális Rights Management (DRM) megoldások használatával különböző hozzáférési jogosultságokat biztosíthat az egyes felhasználók számára. További információ: az [adathordozó biztonságossá tétele](/previous-versions/azure/dn282272(v=azure.100)).

A lokátor létrehozásakor az Azure Storage-ban szükséges tárolási és terjesztési folyamatok miatt 30 másodperces késés is lehet.

## <a name="adaptive-streaming"></a>Adaptív streaming
Az adaptív sávszélességű technológiák lehetővé teszik a videolejátszó alkalmazások számára a hálózati feltételek meghatározását, és több bitráta közül választhatnak. Ha a hálózati kommunikáció romlik, az ügyfél kiválaszthat egy alacsonyabb sávszélességet, így a lejátszás továbbra is csökkentheti a videó minőségét. Ahogy a hálózati feltételek javulnak, az ügyfél magasabb sávszélességre válthat, és javítja a videó minőségét. A Azure Media Services a következő adaptív sávszélességű technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG-DASH.

A streaming URL-címeket használó felhasználók számára először létre kell hoznia egy OnDemandOrigin-lokátort. A lokátor létrehozása megadja az objektum alap elérési útját, amely a továbbítani kívánt tartalmat tartalmazza. Ennek a tartalomnak a továbbításához azonban tovább kell módosítania ezt az elérési utat. A folyamatos átviteli jegyzékfájl fájljának teljes URL-címének létrehozásához össze kell fűzve a lokátor Path értékét és a jegyzékfájl (fájlnév. ISM) fájlnevét. Ezután fűzze hozzá a **/manifest** és a megfelelő formátumot (ha szükséges) a lokátor elérési útjához.

> [!NOTE]
> A tartalmat TLS-kapcsolaton keresztül is továbbíthatja. Ehhez győződjön meg arról, hogy a streaming URL-címek a HTTPS protokollal kezdődnek. Fontos megjegyezni, hogy az AMS jelenleg nem támogatja a TLS-t egyéni tartományokkal.  
> 

Csak akkor továbbíthatja a TLS-t, ha a streaming végpontot, amelyről a tartalmat a 2014 szeptember 10. után hozták létre. Ha a folyamatos átviteli URL-címek a 2014. szeptember 10. után létrehozott streaming-végpontokon alapulnak, az URL-cím "streaming.mediaservices.windows.net"-t tartalmaz. A "origin.mediaservices.windows.net" (a régi) formátumot tartalmazó streaming URL-címek nem támogatják a TLS-t. Ha az URL-cím a régi formátumban van, és szeretné továbbítani a TLS-t, hozzon létre egy új streaming-végpontot. Az új adatfolyam-végponton alapuló URL-címek használatával továbbíthatja a tartalmat a TLS protokollon keresztül.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Streaming URL-formátumok

### <a name="mpeg-dash-format"></a>MPEG-DASH formátum
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = mpd-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) v4 formátum
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = m3u8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) v3 formátum
{streaming Endpoint Name-Media Services-fiók neve}. streaming. Mediaservices. Windows. net/{kereső azonosítója} ISM/manifest (Format = m3u8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = m3u8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) formátum csak hang szűrővel
Alapértelmezés szerint a HLS jegyzékfájl tartalmazza a csak hangként megadott számokat. Ez a mobil hálózatok Apple Store-tanúsítványához szükséges. Ebben az esetben, ha az ügyfél nem rendelkezik elegendő sávszélességgel, vagy egy 2G-kapcsolaton keresztül csatlakozik, a lejátszás csak hangalapúra vált. Ez lehetővé teszi a tartalom folyamatos átvitelét pufferelés nélkül, de nincs videó. Bizonyos esetekben előfordulhat, hogy a lejátszó pufferelése előnyben részesített a csak hangon. Ha el szeretné távolítani a csak hangalapú nyomon követési sávot, az URL-címhez adja hozzá a **csak hang = FALSE értéket** .

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = m3u8-AAPL-v3, csak hang = hamis)

További információkért lásd: [dinamikus jegyzékfájl-összeállítás támogatása és HLS kimenete további funkciók](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming formátum
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

Példa:

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2,0 manifest (örökölt jegyzékfájl)
Alapértelmezés szerint Smooth Streaming jegyzékfájl formátuma az ismétlés címkét (r-tag) tartalmazza. Bizonyos játékosok azonban nem támogatják az r-címkét. Az ezekkel a játékosokkal rendelkező ügyfelek használhatnak olyan formátumot, amely letiltja az r-címkét:

{streaming Endpoint Name-Media Services-fiók neve}. streaming. Mediaservices. Windows. net/{kereső azonosítója} ISM/manifest (Format = FMP4-V20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Progresszív letöltés
A progresszív letöltéssel a teljes fájl letöltése előtt elindíthatja az adathordozók lejátszását. Az. ISM * (ismv, ISMA, ismt vagy ISMC) fájlok nem tölthetők le fokozatosan.

A tartalom fokozatos letöltéséhez használja a lokátor OnDemandOrigin típusát. A következő példa a lokátor OnDemandOrigin-típusán alapuló URL-címet jeleníti meg:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

A progresszív letöltéshez vissza kell fejtenie a forrás-szolgáltatásból adatfolyamként továbbítani kívánt összes Storage-titkosítású eszközt.

## <a name="download"></a>Letöltés
Ha szeretné letölteni a tartalmat egy ügyfél-eszközre, létre kell hoznia egy SAS-lokátort. Az SAS-lokátor hozzáférést biztosít az Azure Storage-tárolóhoz, ahol a fájl található. A letöltési URL-cím létrehozásához be kell ágyaznia a fájlnevet a gazdagép és a SAS aláírása között.

A következő példa a SAS-lokátoron alapuló URL-címet jeleníti meg:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

A következő szempontokat kell figyelembe venni:

* A progresszív letöltéshez vissza kell fejtenie a forrás-szolgáltatásból adatfolyamként továbbítani kívánt összes Storage-titkosítású eszközt.
* A 12 órán belül nem befejezett letöltés sikertelen lesz.

## <a name="streaming-endpoints"></a>Streamvégpontok

A streaming-végpont olyan folyamatos átviteli szolgáltatást jelent, amely közvetlenül egy ügyfél-lejátszó alkalmazásnak vagy egy Content Delivery Network (CDN) szolgáltatásnak továbbítja a tartalmat a további terjesztéshez. A streaming Endpoint szolgáltatás kimenő adatfolyama lehet élő stream vagy igény szerinti videós eszköz a Media Services-fiókban. A folyamatos átviteli végpontok két típusa létezik: **standard** és **prémium**. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

## <a name="known-issues"></a>Ismert problémák
### <a name="changes-to-smooth-streaming-manifest-version"></a>Smooth Streaming jegyzékfájl verziójának módosításai
A júliusi 2016-es kiadás előtt – ha a Media Encoder Standard, Media Encoder Premium Workflow vagy a korábbi Azure Media Encodereket a dinamikus csomagolás használatával továbbították, akkor a visszaadott Smooth Streaming jegyzékfájl megfelel a 2,0-es verziónak. Az 2,0-es verzióban a töredékek időtartama nem használja az úgynevezett REPEAT ("r") címkéket. Például:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" n="0" />
        <c d="2000" />
        <c d="2000" />
        <c d="2000" />
    </StreamIndex>
</SmoothStreamingMedia>
```

A júliusi 2016-es kiadásban a generált Smooth Streaming jegyzékfájl az 2,2-es verzióra vonatkozik, és a töredékek időtartamát használja az ismételt címkék használatával. Például:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Előfordulhat, hogy az örökölt Smooth Streaming ügyfelek némelyike nem támogatja az Ismétlési címkéket, és nem fogja betölteni a jegyzékfájlt. A probléma megoldásához használhatja a Legacy manifest Format paramétert **(Format = FMP4-V20)** , vagy frissítheti az ügyfelet a legújabb verzióra, amely támogatja az ismételt címkéket. További információ: [Smooth Streaming 2,0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Media Services lokátorok frissítése a működés közbeni tárolási kulcsok után](media-services-roll-storage-access-keys.md)