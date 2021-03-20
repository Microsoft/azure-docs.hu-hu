---
title: Az áttelepítési útmutató kódolása
description: Ez a cikk olyan forgatókönyv-alapú útmutatást biztosít, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 915fdcb059d9e7bf9e1853040b90b82a0457652e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598405"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Kódolási forgatókönyv-alapú áttelepítési útmutató

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk olyan forgatókönyv-alapú útmutatást biztosít, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.

## <a name="prerequisites"></a>Előfeltételek

A kódolási munkafolyamat módosítása előtt javasoljuk, hogy Ismerje meg, hogyan kezeli a rendszer a tárterület kezelését.  Az AMS V3 esetében az Azure Storage API a Media Services fiókjához társított Storage-fiók (ok) kezelésére szolgál.

> [!NOTE]
> A v2-ben létrehozott feladatok és feladatok nem jelennek meg a v3-as verzióban, mivel nincsenek átalakítók társítva. A javaslat a v3 átalakításokra és feladatokra vált.

## <a name="encoding-workflow-comparison"></a>Kódolási munkafolyamat-összehasonlítás

Szánjon néhány percet az alábbi folyamatábrára a v2 és v3 kódolási munkafolyamatok vizuális összehasonlításához.

### <a name="v2-encoding-workflow"></a>V2 kódolási munkafolyamat

Az alábbi képre kattintva megtekintheti a nagyobb verziót.

[![A v2 ](./media/migration-guide/V2-pretty.svg) kódolási munkafolyamata ](./media/migration-guide/V2-pretty.svg#lightbox)

1. Telepítés
    1. Hozzon létre egy eszközt, vagy használja a és a meglévő eszközt. Ha új eszközt használ, töltse fel a tartalmat az adott eszközre. Ha meglévő eszközt használ, az objektumban már létező fájlokat kell kódolnia.
    2. A következő elemek értékeinek lekérése:
        - Adathordozó-feldolgozó azonosítója vagy objektum
        - A használni kívánt kódoló kódoló sztringje (neve)
        - Az új eszköz vagy a meglévő eszköz AZONOSÍTÓjának azonosítója
    3. Figyeléshez hozzon létre egy feladatot vagy egy feladat szintű értesítési előfizetést vagy egy SDK-eseménykezelőt
2. Hozza létre a feladatot vagy feladatokat tartalmazó feladatot. Minden feladatnak tartalmaznia kell a fenti elemeket és:
    - Az a direktíva, amelyet kimeneti eszköz létrehozásához létre kell hozni.  A kimeneti eszközt a rendszer hozza létre.
    - A kimeneti eszköz nem kötelező neve
3. Küldje el a feladatot.
4. Figyelje a feladatot.

### <a name="v3-encoding-workflow"></a>V3 kódolási munkafolyamat

[![A v3 kódolási munkafolyamat](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Beállítás
    1. Hozzon létre egy eszközt, vagy használjon egy meglévő eszközt. Ha új eszközt használ, töltse fel a tartalmat az adott eszközre. Ha meglévő eszközt használ, az objektumban már létező fájlokat kell kódolnia. *Ne töltsön fel további tartalmat az adott eszközre.*
    1. Kimeneti eszköz létrehozása  A kimeneti eszköz a kódolt fájlok, valamint a bemeneti és kimeneti metaadatok tárolására szolgál.
    1. Az átalakítás értékeinek beolvasása:
        - Standard szintű kódoló készlete
        - AMS-erőforráscsoport
        - AMS-fiók neve
    1. Hozza létre az átalakítást, vagy használjon egy meglévő átalakítót.  Az átalakítások újrafelhasználhatók. Nem szükséges új átalakítót létrehozni minden alkalommal, amikor egy feladatot szeretne elküldeni.
1. Feladat létrehozása
    1. A feladatokhoz szerezze be a következő elemek értékeit:
        - Átalakítás neve
        - Az eszköz SAS URL-címének alap-URI-ja, a fájlmegosztás HTTPs-forrásának elérési útja vagy a fájlok helyi elérési útja. A az `JobInputAsset` eszköz nevét is használhatja bemenetként.
        - Fájlnév (ek)
        - Kimeneti eszköz (ek)
        - Egy erőforráscsoport
        - AMS-fiók neve  
1. A feladatok figyeléséhez használja a [Event Grid](monitoring/monitor-events-portal-how-to.md) .
1. Küldje el a feladatot.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Egyéni készletek v2 és v3 közötti kódolásra

Ha a v2-kód a standard kódolót egyéni beállításkészlettel látja el, először létre kell hoznia egy új átalakítást az egyéni szabványos kódoló készlettel a feladatok elküldése előtt.

Az egyéni beállításkészletek mostantól JSON formátumúak, és már nem XML-alapúak. Hozza létre újra az beállításkészletet a JSON-ban az [átalakítás Open API (hencegés)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) dokumentációjában meghatározott egyéni előre beállított séma alapján.

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Bemeneti és kimeneti metaadat-fájlok kódolási feladatból

A v2-ben az XML bemeneti és kimeneti metaadat-fájljai a kódolási feladatok eredményeképpen jönnek létre. A v3-as verzióban a metaadatok formátuma XML-ből JSON-ra változott. A metaadatokkal kapcsolatos további információkért lásd a [bemeneti metaadatok](input-metadata-schema.md) és a [kimeneti metaadatok](output-metadata-schema.md)című témakört.

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Prémium szintű kódoló v3 szabványú kódolóhoz vagy partner-alapú megoldásokhoz

A v2 API már nem támogatja a prémium szintű kódolót. Ha korábban a HEVC-kódoláshoz használt munkafolyamat-alapú prémium kódolót használta, a HEVC-kódolás támogatásával telepítse át az új v3 [szabványú kódolóba](media-encoder-standard-formats.md) .

Ha a prémium szintű kódoló speciális munkafolyamat-funkciói szükségesek, javasoljuk, hogy az [Imagine Communications](https://imaginecommunications.com), a [stream](https://www.telestream.net)vagy a [Bitmovin](https://bitmovin.com)használatával kezdje el használni az Azure Advanced Encoding partneri megoldást.

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>A HTTPS-alapú URL-címeken lévő bemenetekkel rendelkező feladatok

Mostantól az Azure Storage-ban, helyileg tárolt vagy külső webkiszolgálókon tárolt fájlokból is elküldheti a feladatokat a [http (S) feladatok bemenetének támogatásával](job-input-from-http-how-to.md).

Ha korábban már használta a munkafolyamatokat fájlok Azure Blob-fájlokból való másolásához a feladatok elküldése előtt, akkor lehetséges, hogy leegyszerűsíti a munkafolyamatot úgy, hogy az Azure Blob Storage-ban közvetlenül a feladatba továbbítja a fájl SAS URL-címét.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Indexelő v1 hang átírása az új AudioAnalyzer "alapszintű mód"

A v2 API-ban indexelő v1 processzort használó ügyfelek számára létre kell hoznia egy átalakítást, amely az `AudioAnalyzer` [alapszintű módban](how-to-create-basic-audio-transform.md) meghívja az új feladatot a feladat elküldése előtt.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>A kódolási, átalakítási és feladatokkal kapcsolatos fogalmak, oktatóanyagok és útmutatók

### <a name="concepts"></a>Alapelvek

- [Videó és hang kódolása Media Services](encoding-concept.md)
- [Szabványos kódoló-formátumok és-kodekek](media-encoder-standard-formats.md)
- [Kódolás egy automatikusan létrehozott bitráta-létrával](autogen-bitrate-ladder.md)
- [Az adott megoldás optimális bitráta-értékének megkereséséhez használja a Content-Aware kódolási beállításkészletet](content-aware-encoding.md)
- [Media szolgáltatás számára fenntartott egységek](concept-media-reserved-units.md)
- [Bemeneti metaadatok](input-metadata-schema.md)
- [Kimeneti metaadatok](output-metadata-schema.md)
- [Dinamikus csomagolás a Media Services v3-ben: hangkodekek](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Oktatóanyagok

- [Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a video-.NET](stream-files-dotnet-quickstart.md)
- [Oktatóanyag: videók feltöltése, kódolása és továbbítása a Media Services v3 segítségével](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Útmutatók

- [Feladathoz tartozó bemenet létrehozása HTTPS URL-címről](job-input-from-http-how-to.md)
- [Feladathoz tartozó bevitel létrehozása helyi fájlból](job-input-from-local-file-how-to.md)
- [Alapszintű hang átalakításának létrehozása](how-to-create-basic-audio-transform.md)
- .NET-tel
  - [Kódolás egyéni átalakítással – .NET](customize-encoder-presets-how-to.md)
  - [Átfedés létrehozása a Media Encoder Standard használatával](how-to-create-overlay.md)
  - [Miniatűrök készítése a .NET-es kódoló standard használatával](media-services-generate-thumbnails-dotnet.md)
- Az Azure CLI-vel
  - [Kódolás egyéni átalakítással – Azure CLI](custom-preset-cli-howto.md)
- A REST-tel
  - [Kódolás egyéni átalakítással – REST](custom-preset-rest-howto.md)
  - [Bélyegképek előállítása a Encoder standard és a REST használatával](media-services-generate-thumbnails-rest.md)
- [Videó kivágása Media Services-.NET-kódolással](subclip-video-dotnet-howto.md)
- [Videó kivágása Media Services-REST kódolással](subclip-video-rest-howto.md)

## <a name="samples"></a>Példák

A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
