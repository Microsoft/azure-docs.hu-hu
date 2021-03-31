---
title: Szűrők definiálása a Azure Media Servicesban
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, hogy az ügyfél egy stream adott szakaszait továbbítsa. A Media Services dinamikus jegyzékfájlokat hoz létre a szelektív streaming eléréséhez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: bb5561ced93c3f5a899c6e48fdab0f14e52914bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89291551"
---
# <a name="filters"></a>Szűrők

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ha a tartalmat az ügyfeleknek (élő adatfolyam-továbbítási eseményeknek vagy igény szerinti videóknak) juttatja el, az ügyfélnek nagyobb rugalmasságra lehet szüksége, mint amit az alapértelmezett eszköz jegyzékfájljában ismertetünk. A Azure Media Services az előre definiált szűrők alapján [dinamikus jegyzékfájlokat](filters-dynamic-manifest-overview.md) kínál. 

A szűrők kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára a következő műveleteket: 

- Csak a videó egy szakaszát játssza le (a teljes videó lejátszása helyett). Például:
  - Csökkentse a jegyzékfájlt az élő események ("alklipek szűrése") alklipének megjelenítéséhez, vagy
  - Egy videó elejének vágása ("videó vágása").
- Csak a megadott kiadatások és/vagy a megadott nyelvi sávok továbbítására szolgálnak, amelyeket az eszköz a tartalom ("kiadatási szűrés") használatával támogat. 
- A bemutató ablak (DVR) módosítása annak érdekében, hogy korlátozott hosszúságú legyen a DVR ablak a lejátszóban ("bemutató ablakának módosítása").

A Media Services lehetővé teszi, hogy a tartalomhoz hozzon létre **fiók-szűrőket** és adategység- **szűrőket** . Emellett az előre létrehozott szűrőket is társíthatja egy **streaming lokátorhoz**.

## <a name="defining-filters"></a>Szűrők definiálása

A szűrők két típusa létezik: 

* [Fiókok szűrői](/rest/api/media/accountfilters) (globális) – a Azure Media Services fiókban található bármely eszközre alkalmazható, és a fiók élettartama is megoldható.
* Adategység- [szűrők](/rest/api/media/assetfilters) (helyi) – csak olyan objektumra alkalmazhatók, amelynél a szűrő a létrehozáskor hozzá volt rendelve, az eszköz élettartama. 

A **fiókok** és az **eszközök szűrőinek** típusai pontosan ugyanazok a tulajdonságok a szűrő definiálásához/leírásához. Az **eszközcsoport** létrehozásakor meg kell adnia azt az objektum nevét, amellyel hozzá szeretné rendelni a szűrőt.

A forgatókönyvtől függően eldöntheti, hogy a szűrő milyen típusú legyen alkalmasabb (az eszköz szűrője vagy a fiók szűrője). A fiókok szűrői megfelelőek az eszközök profiljaihoz (a kiadatási szűréshez), ahol az adategységek szűrői használhatók egy adott eszköz kivágásához.

A szűrők leírásához a következő tulajdonságokat használhatja. 

|Név|Leírás|
|---|---|
|firstQuality|A szűrő első minőségi bitrátája.|
|presentationTimeRange|A megjelenítési idő tartománya. Ez a tulajdonság a jegyzékfájl kezdő/záró pontjainak, a megjelenítési időszak hosszának és az élő indítási pozíciónak a szűrésére szolgál. <br/>További információ: [PresentationTimeRange](#presentationtimerange).|
|nyomon követi|A nyomon követi a kiválasztási feltételeket. További információ: [tracks](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Ezt a tulajdonságot használja az **Asset Filters** használatával. A tulajdonságot nem ajánlott a **fiók szűrőinek** beállításával beállítani.

|Név|Leírás|
|---|---|
|**endTimestamp**|Az igény szerinti videóra vonatkozik (VoD).<br/>Az élő közvetítés megjelenítéséhez a rendszer csendben figyelmen kívül hagyja és alkalmazza a bemutatót, amikor a bemutató véget ér, és az adatfolyam a VoD-ra változik.<br/>Ez egy hosszú érték, amely a bemutató abszolút végpontját jelöli, a legközelebbi következő GOP-kezdésig kerekítve. Az egység az időskála, így a 1800000000-es endTimestamp 3 percet vesz igénybe.<br/>A startTimestamp és a endTimestamp használatával vágja le a lista (manifest) töredékeit.<br/>Például a startTimestamp = 40000000 és a endTimestamp = 100000000 az alapértelmezett időskálával létrehoz egy olyan lejátszási listát, amely 4 másodperc és 10 másodperc közötti töredékeket tartalmaz a VoD-bemutatóból. Ha egy töredék átnyúlik a határt, a teljes töredék szerepelni fog a jegyzékfájlban.|
|**forceEndTimestamp**|Csak az élő közvetítésre vonatkozik.<br/>Azt jelzi, hogy a endTimestamp tulajdonságnak jelen kell-e lennie. Ha az értéke igaz, a endTimestamp meg kell adni, vagy rossz kérési kódot ad vissza.<br/>Megengedett értékek: false, True.|
|**liveBackoffDuration**|Csak az élő közvetítésre vonatkozik.<br/> Ez az érték határozza meg azt a legújabb élő pozíciót, amelyet az ügyfél tud keresni.<br/>Ezzel a tulajdonsággal késleltetheti az élő lejátszás pozícióját, és létrehozhatja a játékosok kiszolgálóoldali pufferét.<br/>Ennek a tulajdonságnak az egysége az időskála (lásd alább).<br/>A maximális élő visszatartási időtartam 300 másodperc (3000000000).<br/>A 2000000000 érték például azt jelenti, hogy a legfrissebb elérhető tartalom 20 másodperc van késleltetve a valós élő szélétől.|
|**presentationWindowDuration**|Csak az élő közvetítésre vonatkozik.<br/>A presentationWindowDuration használatával alkalmazhatja a töredékeket tartalmazó ablakokat egy lejátszási listára.<br/>Ennek a tulajdonságnak az egysége az időskála (lásd alább).<br/>Például állítsa be a presentationWindowDuration = 1200000000 két perces csúszó ablak alkalmazására. A lejátszási lista az élő szélétől számított 2 percen belül adathordozót fog tartalmazni. Ha egy töredék átnyúlik a határt, a teljes töredék szerepelni fog a lejátszási listán. A megjelenítési időszak minimális időtartama 60 másodperc.|
|**startTimestamp**|A video on demand (VoD) vagy az élő közvetítés szolgáltatásra vonatkozik.<br/>Ez egy hosszú érték, amely a stream abszolút kezdőpontját jelöli. Az érték a legközelebbi következő GOP-kezdésre lesz kerekítve. Az egység az időskála, így a 150000000-es startTimestamp 15 másodpercig tart.<br/>A startTimestamp és a endTimestampp használatával vágja le a lista (manifest) töredékeit.<br/>Például a startTimestamp = 40000000 és a endTimestamp = 100000000 az alapértelmezett időskálával létrehoz egy olyan lejátszási listát, amely 4 másodperc és 10 másodperc közötti töredékeket tartalmaz a VoD-bemutatóból. Ha egy töredék átnyúlik a határt, a teljes töredék szerepelni fog a jegyzékfájlban.|
|**időskála**|A bemutató időtartományában lévő összes időbélyegre és időtartamra vonatkozik, a növekmények száma egy másodpercben megadva.<br/>Az alapértelmezett érték 10000000-10 000 000, és egy másodperc alatt növekszik, ahol az egyes növekmények 100 nanoszekundumban hosszúak lesznek.<br/>Ha például 30 másodpercen belül szeretné beállítani a startTimestamp, a 300000000 értéket kell használnia az alapértelmezett időkeret használatakor.|

### <a name="tracks"></a>Nyomon követi

Itt adhatja meg a szűrési nyomon követési tulajdonságok (FilterTrackPropertyConditions) listáját, amely alapján a stream (élő adatfolyam vagy igény szerinti videó) nyomon követhető a dinamikusan létrehozott jegyzékbe. A szűrők logikai **és** és **vagy** művelet használatával kombinálhatók.

A szűrési tulajdonságok a következő táblázatban ismertetett típusok és műveletek (EQUAL, NotEqual) szerint vannak leírva: Track types (értékek). 

|Név|Leírás|
|---|---|
|**Sávszélességű**|Használja a nyomkövetési sebesség használatát a szűréshez.<br/><br/>A javasolt érték a bitsebességek tartománya, a bitek száma másodpercenként. Például: "0-2427000".<br/><br/>Megjegyzés: egy adott bitráta-érték (például 250000 (bit/másodperc) használata esetén ez a megközelítés nem ajánlott, mivel a pontos bitsebességek az egyik eszközről a másikra ingadoznak.|
|**FourCC**|A szűrési nyomon követéshez használja a FourCC értékét.<br/><br/>Az érték a codec-formátum első eleme, amely az [RFC 6381](https://tools.ietf.org/html/rfc6381)-ben van megadva. Jelenleg a következő kodekek támogatottak: <br/>Videó: "AVC1", "hev1", "hvc1"<br/>Hang: "mp4a", "EC-3"<br/><br/>Egy adott eszközön lévő zeneszámok FourCC-értékeinek meghatározásához szerezze be és vizsgálja meg a jegyzékfájlt.|
|**Nyelv**|A szűrési pálya nyelvét használja.<br/><br/>Az érték a felvenni kívánt nyelv címkéje, az RFC 5646-ben megadott módon. Például: "en".|
|**Név**|Használja a szűrési pálya nevét.|
|**Típus**|Használja a szűréshez használt nyomkövetési típust.<br/><br/>A következő értékek engedélyezettek: "videó", "hang" vagy "text".|

### <a name="example"></a>Példa

Az alábbi példa egy élő adatfolyam-szűrőt definiál: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Szűrők társítása a streaming Locator használatával

Megadhatja az [eszköz vagy a fiók szűrőinek](filters-concept.md) listáját a [folyamatos átviteli lokátorban](/rest/api/media/streaminglocators/create#request-body). A [dinamikus](dynamic-packaging-overview.md) csomagolás alkalmazza a szűrők listáját, valamint azokat, amelyeket az ügyfél az URL-címben megad. Ez a kombináció létrehoz egy [dinamikus jegyzékfájlt](filters-dynamic-manifest-overview.md), amely a streaming keresőben megadott URL + szűrők szűrői alapján történik. 

Lásd az alábbi példákat:

* [Szűrők hozzárendelése a streaming Locator-.NET-tel](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Szűrők hozzárendelése az adatfolyam-keresőhöz – parancssori felület](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Szűrők frissítése
 
A **folyamatos átviteli lokátor** nem frissíthető a szűrők frissítése közben. 

Nem ajánlott frissíteni egy aktívan közzétett **adatfolyam-keresőhöz** társított szűrők definícióját, különösen akkor, ha a CDN engedélyezve van. A streaming-kiszolgálók és a CDNs belső gyorsítótárral rendelkezhetnek, amelyek miatt a rendszer elavult gyorsítótárazott értékeket adhat vissza. 

Ha módosítani kell a szűrő definícióját, érdemes létrehozni egy új szűrőt, és hozzá kell adni azt a **streaming-lokátor** URL-címéhez, vagy közzé kell tennie egy új **streaming-keresőt** , amely közvetlenül hivatkozik a szűrőre.

## <a name="next-steps"></a>Következő lépések

A következő cikkek bemutatják, hogyan hozhat létre programozott módon szűrőket.  

- [Szűrők létrehozása REST API-kkal](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása a parancssori felület használatával](filters-dynamic-manifest-cli-howto.md)
