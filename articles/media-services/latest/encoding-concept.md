---
title: Videó és hang kódolása Media Services
description: Ez a cikk a videó és a hang kódolását ismerteti Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6a486057a265b02ce30059940c8c98837ec43f8e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617641"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Videó és hang kódolása Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services kódolása a digitális videót és/vagy hangot tartalmazó fájlok egyik standard formátumból egy másikba való konvertálásának folyamatára vonatkozik, amelynek célja, hogy (a) csökkentse a fájlok méretét, és/vagy (b) olyan formátumot hozzon létre, amely kompatibilis az eszközök és alkalmazások széles körével. Ezt a folyamatot a videó tömörítésének vagy az átkódolásnak is nevezzük. Tekintse meg az [adattömörítést](https://en.wikipedia.org/wiki/Data_compression) és a [Mi a kódolás és az átkódolás?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) című témakört a fogalmak további megvitatására.

A videók általában [progresszív letöltéssel](https://en.wikipedia.org/wiki/Progressive_download) vagy [adaptív sávszélességű adatfolyam](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)használatával érkeznek meg az eszközökre és alkalmazásokra.

> [!IMPORTANT]
> A Media Services nem számláz a megszakított vagy hibás feladatokért. Például egy olyan feladat, amely elérte a 50%-os előrehaladást, és a megszakítása megszakadt, a feladat percének 50%-ában nem történik számlázás. Csak a befejezett feladatokért kell fizetnie.

* A progresszív letöltéssel történő továbbításhoz a Azure Media Services segítségével konvertálhat egy digitális médiafájlt [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) -fájlba, amely a [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) kodekkel kódolt videókat és az [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) -kodekkel kódolt hanganyagot tartalmaz. Ez az MP4-fájl a Storage-fiókban lévő eszközre íródik. A fájl közvetlen letöltéséhez használhatja az Azure Storage API-jait vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t). Ha a kimeneti eszközt egy adott tároló nevével hozta létre a tárolóban, használja ezt a helyet. Ellenkező esetben a Media Services használatával [listázhatja az objektum-tároló URL-címeit](/rest/api/media/assets/listcontainersas). 
* A tartalom adaptív sávszélességű adatfolyamként történő kézbesítésének előkészítéséhez a köztes fájlt több bitrátán kell kódolni (magasról alacsonyra). A minőség kecses átállásának biztosítása érdekében a videó felbontása alacsonyabb, mint a sávszélesség. Ez egy úgynevezett kódolási létrát eredményez, amely a felbontások és a bitráta (lásd: [automatikusan generált adaptív sávszélességű létrák](autogen-bitrate-ladder.md)) táblázata. Media Services használatával több bitrátán is kódolhatja a köztes fájlokat. Ennek során az MP4-fájlok és a hozzájuk tartozó, a Storage-fiókban lévő eszközre írt társított adatfolyam-konfigurációs fájlok készletét fogja kapni. Ezután a Media Services [dinamikus csomagolási](dynamic-packaging-overview.md) funkciójának használatával továbbíthatja a videót a streaming protokollok, például az [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) és a [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)segítségével. Ehhez létre kell hoznia egy [adatfolyam-keresőt](streaming-locators-concept.md) , és a támogatott protokolloknak megfelelő streaming URL-címeket kell létrehoznia, amelyek az eszközök és alkalmazások számára a képességeik alapján adhatók ki.

Az alábbi ábrán a dinamikus csomagolással rendelkező igény szerinti kódoláshoz tartozó munkafolyamat látható.

![Igény szerinti kódolásra szolgáló munkafolyamat dinamikus csomagolással](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Ez a témakör útmutatást nyújt a tartalmak Media Services v3-vel való kódolásához.

## <a name="transforms-and-jobs"></a>Átalakítások és feladatok

Media Services v3 kódoláshoz létre kell hoznia egy [átalakítót](/rest/api/media/transforms) és egy [feladatot](/rest/api/media/jobs). Az átalakítás egy receptet határoz meg a kódolási beállításokhoz és kimenetekhez; a feladattípus a recept egy példánya. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

Ha Media Services kódolást használ, az előállítók használatával tájékoztathatja a kódolót a bemeneti médiafájlok feldolgozásáról. A Media Services v3-as verziójában szabványos kódoló használatával kódolja a fájlokat. Megadhatja például a videó felbontását és/vagy a kívánt Hangcsatornák számát a kódolt tartalomban.

Az iparági ajánlott eljárások alapján gyorsan megkezdheti a javasolt beépített készletek egyikét, vagy dönthet úgy, hogy létrehoz egy egyéni beállításkészletet az adott forgatókönyv vagy eszköz követelményeinek megfelelően. További információ: [kódolás egyéni átalakítóval](customize-encoder-presets-how-to.md).

Az MP4-fájlok előállításához a standard kódolóval kezdődően a 2019-es és újabb, új. MPI-fájl jön létre, és hozzá lesz adva a kimeneti eszközhöz. Ez az MPI-fájl a [dinamikus csomagolás](dynamic-packaging-overview.md) és a folyamatos átviteli forgatókönyvek teljesítményének növelésére szolgál.

> [!NOTE]
> Ne módosítsa vagy távolítsa el az MPI-fájlt, vagy az ilyen fájl létezésére (vagy nem) való függőséget a szolgáltatásban.

### <a name="creating-job-input-from-an-https-url"></a>Feladathoz tartozó bemenet létrehozása HTTPS URL-címről

Amikor feladatokat küld a videók feldolgozásához, meg kell tudnia Media Services hol található a bemeneti videó. Az egyik lehetőség egy HTTPS URL-cím megadása feladatként. A Media Services v3 jelenleg nem támogatja a HTTPS-alapú URL-címekre vonatkozó darabolásos átvitel kódolását.

#### <a name="examples"></a>Példák

* [Kódolás HTTPS-alapú URL-címről a .NET-tel](stream-files-dotnet-quickstart.md)
* [Kódolás HTTPS URL-címről a REST használatával](stream-files-tutorial-with-rest.md)
* [Kódolás HTTPS URL-címről a parancssori felületről](stream-files-cli-quickstart.md)
* [Kódolás HTTPS URL-címről Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Feladathoz tartozó bemenet létrehozása helyi fájlból

A bemeneti videó adathordozó-szolgáltatási eszközként tárolható, ebben az esetben egy bemeneti eszközt hoz létre egy fájl alapján (helyileg vagy az Azure Blob Storage-ban tárolva).

#### <a name="examples"></a>Példák

[Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Feladathoz tartozó bemenet létrehozása kivágással

Videó kódolásakor megadhatja, hogy a forrásfájl kivágása vagy bevágása is megtörténjen, és olyan kimenetet hozzon létre, amely csak a bemeneti videó kívánt részével rendelkezik. Ez a funkció minden olyan [átalakítással](/rest/api/media/transforms) működik, amelyet a [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával készítettek.

Megadhatja, hogy létrehoz egy [feladatot](/rest/api/media/jobs/create) egyetlen videoklipet igény szerint vagy élő archívumban (rögzített esemény). A feladathoz tartozó bemenet lehet egy eszköz vagy egy HTTPS URL-cím is.

> [!TIP]
> Ha a videó újbóli kódolása nélkül szeretné továbbítani a videó sublip, érdemes lehet a [dinamikus csomagoló használatával előszűrési jegyzékeket](filters-dynamic-manifest-overview.md)használni.

#### <a name="examples"></a>Példák

Példák:

* [Videó kivágása a .NET-tel](subclip-video-dotnet-howto.md)
* [Videó kivágása a REST-tel](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Beépített beállításkészletek

Media Services a következő beépített kódolási beállításkészleteket támogatja:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

A [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) egy beépített beállításkészlet beállítására szolgál, amely a bemeneti videót a szabványos kódolóval kódolja.

A következő beépített beállításkészletek jelenleg támogatottak:

- **EncoderNamedPreset. AACGoodQualityAudio**: egyetlen MP4-fájlt hoz létre, amely kizárólag 192 kbps-nál kódolt sztereó hanganyagot tartalmaz.
- **EncoderNamedPreset. AdaptiveStreaming** (ajánlott): ez támogatja a H. 264 adaptív sávszélesség-kódolást. További információ: [a bitráta-létrák automatikus generálása](autogen-bitrate-ladder.md).
- **EncoderNamerPreset. H265AdaptiveStreaming** : a AdaptiveStreaming-készlethez hasonlóan, de a HEVC (H. 265) kodeket használja. A GOP-ra igazított MP4-fájlokat hoz létre H. 265 videó és sztereó AAC hanggal. Automatikusan létrehoz egy bitráta-létrát a bemeneti felbontás, a bitráta és a frame rate alapján. Az automatikusan létrehozott beállításkészlet soha nem lépi túl a bemeneti felbontást. Ha például a bemenet 720p, a kimenet 720p marad a legjobb esetben.
- **EncoderNamedPreset. ContentAwareEncoding**: a H. 264 Content-Aware kódoláshoz beállított készletet tesz elérhetővé. A szolgáltatás bármilyen bemeneti tartalmat megpróbál automatikusan meghatározni a rétegek optimális számának meghatározására, valamint a megfelelő bitrátát és feloldási beállításokat az adaptív adatfolyam-továbbítással. Az alapul szolgáló algoritmusok az idő múlásával továbbra is fejlődnek lesznek. A kimenet video-és hangalapú MP4-fájlokat fog tartalmazni. További információ: [Content-Aware kódolás](content-aware-encoding.md).
- **EncoderNamedPreset. H265ContentAwareEncoding**: a HEVC (H. 265) előre beállított kódolását teszi lehetővé. Hozzon létre egy GOP-ra igazított MP4 a Content-Aware kódolás használatával. A szolgáltatás a bemeneti tartalom kezdeti könnyű elemzését, valamint az eredmények használatával határozza meg a rétegek optimális számát, a megfelelő bitrátát és a megoldási beállításokat az adaptív adatfolyam-továbbítással. Ez a készlet különösen hatékony az alacsony és közepes komplexitású videók esetében, ahol a kimeneti fájlok alacsonyabb sávszélességű, de olyan minőségben, amely továbbra is jó élményt nyújt a nézők számára. A kimenet video-és hangalapú MP4-fájlokat fog tartalmazni.
  > [!NOTE]
  > Ügyeljen arra, hogy a **ContentAwareEncoding** ne ContentAwareEncodingExperimental, amely már elavult

- **EncoderNamedPreset. H264MultipleBitrate1080p**: nyolc GOP-igazítású MP4-fájlt állít elő, amely 6000 kbps-ról 400 kbps-ra, valamint sztereó AAC hangra mutat. A felbontás 1080p-kor kezdődik, és a 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: hat GOP-igazítású MP4-fájlt állít elő, amely 3400 kbps-ról 400 kbps-ra, valamint sztereó AAC hangra mutat. A megoldás 720p-kor kezdődik, és a 360p-re mutat.
- **EncoderNamedPreset. H264MultipleBitrateSD**: öt GOP-igazítású MP4-fájlt állít elő, amely 1600 kbps-ról 400 kbps-ra, valamint sztereó AAC hangra mutat. A megoldás a 480p-on kezdődik, és a 360p-re mutat.
- **EncoderNamedPreset. H264SingleBitrate1080p**: MP4-fájlt hoz létre, ahol a videó a H. 264 kodekkel van kódolva, 6750 kbps sebességgel, a kép magassága pedig 1080 képpont, és a sztereó hang AAC-LC kodekkel van kódolva, 64 kbps sebességgel.
- **EncoderNamedPreset. H264SingleBitrate720p**: MP4-fájlt hoz létre, ahol a videó a H. 264 kodekkel van kódolva, 4500 kbps sebességgel, a kép magassága pedig 720 képpont, és a sztereó hang AAC-LC kodekkel van kódolva, 64 kbps sebességgel.
- **EncoderNamedPreset. H264SingleBitrateSD**: MP4-fájlt hoz létre, ahol a videó a H. 264 kodekkel van kódolva, 2200 kbps sebességgel, a kép magassága pedig 480 képpont, és a sztereó hang AAC-LC kodekkel van kódolva, 64 kbps sebességgel.
- **EncoderNamedPreset. H265SingleBitrate720P**: MP4-fájlt hoz létre, ahol a videó a HEVC (H. 265) kodekkel van kódolva, 1800 kbps sebességgel, valamint 720 képpont magasságban, és a sztereó hang AAC-LC kodekkel van kódolva, 128 kbps sebességgel.
- **EncoderNamedPreset. H265SingleBitrate1080p**: MP4-fájlt hoz létre, ahol a videó a HEVC (H. 265) kodekkel van kódolva, 3500 kbps sebességgel, valamint 1080 képpont magasságban, és a sztereó hang AAC-LC kodekkel van kódolva, 128 kbps sebességgel.
- **EncoderNamedPreset. H265SingleBitrate4K**: MP4-fájlt hoz létre, ahol a videó a HEVC (H. 265) kodekkel van kódolva, 9500 kbps sebességgel, valamint 2160 képpont magasságban, és a sztereó hang AAC-LC kodekkel van kódolva, 128 kbps sebességgel.

A legfrissebb beállításkészletek listájának megtekintéséhez tekintse meg a [videók kódolásához használható beépített beállításkészleteket](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Az előzetes beállítások használatáról a [fájlok feltöltése, kódolása és folyamatos átvitele](stream-files-tutorial-with-api.md)című részben olvashat.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

A [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) a bemeneti videó standard kódolóval való kódolásakor használandó beállításokat ismerteti. Ezt az beállításkészletet az átalakítási előkészletek testreszabásakor használhatja.

#### <a name="considerations"></a>Megfontolandó szempontok

Egyéni beállításkészletek létrehozásakor a következő szempontokat kell figyelembe venni:

- Az AVC-tartalom magasságának és szélességének minden értékének négy többszörösének kell lennie.
- A Azure Media Services V3 esetében az összes kódolási bitrátát bit/másodpercben kell kiszámítani. Ez különbözik az előre beállított v2 API-kkal, amelyek az egységhez kilobit/másodpercet használnak. Ha például a v2-es bitrátát 128 (kilobit/másodperc) értékre adták, a v3-as verzióban a 128000 (BITS/Second) értéket adja meg.

### <a name="customizing-presets"></a>Előzetes beállítások testreszabása

Media Services teljes mértékben támogatja az előre beállított összes érték testreszabását az adott kódolási igények és követelmények kielégítése érdekében. A kódoló-beállításkészletek testreszabását bemutató példákért tekintse meg az alábbi listát:

#### <a name="examples"></a>Példák

- [Készletek testreszabása .NET-tel](customize-encoder-presets-how-to.md)
- [Beállításkészlet testreszabása a parancssori felülettel](custom-preset-cli-howto.md)
- [Beállítások testreszabása REST használatával](custom-preset-rest-howto.md)


## <a name="preset-schema"></a>Előre definiált séma

A Media Services v3-as verzióban az előzetes beállítások az API-ban erősen beírt entitások. Ezekhez az objektumokhoz a "séma" definíciója [nyílt API-specifikációban (vagy hencegés)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)található. A [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), a [.net SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) -ban (vagy más Media Services v3 SDK-dokumentációban) is megtekintheti az előre definiált definíciókat (például **StandardEncoderPreset**).

## <a name="scaling-encoding-in-v3"></a>A kódolás skálázása v3-ben

A médiafájlok feldolgozásának méretezéséhez lásd: [Méretezés a CLI-vel](media-reserved-units-cli-how-to.md).
Az API **2020-05-01** -es verziójában vagy a Azure Portalon létrehozott fiókok esetében a skálázás és a Media szolgáltatás számára fenntartott egységek már nem szükségesek. A méretezés automatikusan történik, és a szolgáltatás belsőleg kezeli.

## <a name="billing"></a>Számlázás

A Media Services nem számláz a megszakított vagy hibás feladatokért. Például egy olyan feladat, amely elérte a 50%-os előrehaladást, és a megszakítása megszakadt, a feladat percének 50%-ában nem történik számlázás. Csak a befejezett feladatokért kell fizetnie.

További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) tartalmaz.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

* [Feltöltés, kódolás és stream Media Services használatával](stream-files-tutorial-with-api.md).
* [KÓDOLÁS HTTPS-URL-címről beépített beállításkészletek használatával](job-input-from-http-how-to.md).
* [Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md).
* [Hozzon létre egy egyéni beállításkészletet az adott forgatókönyv vagy eszköz követelményeinek megcélzásához](customize-encoder-presets-how-to.md).
