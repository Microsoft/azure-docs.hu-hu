---
title: élő átirat: Azure Media Services Leírás: Ismerkedjen meg Azure Media Services élő átírással.  
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' ' MS. Service: Media-Services MS. munkaterhelés: Media ms.tgt_pltfrm: na MS. devlang: ne MS. topic: útmutató MS. Date: 08/31/2020 MS. Author: inhenkel

---

# <a name="live-transcription-preview"></a>Élő átírás (előzetes verzió)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure Media Service videó-, hang-és szöveges szolgáltatásokat biztosít különböző protokollokban. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML-ban továbbítja az átmásolt szöveget. A kézbesítés az MPEG-4 rész 30 (ISO/IEC 14496-30) töredékbe van csomagolva. Ha a kézbesítést HLS/TS-n keresztül használja, a szöveg kidarabolt VTT lesz kézbesítve.

Ha az élő átírás be van kapcsolva, a további díjak érvényesek. Tekintse át a díjszabási információkat a [Media Services díjszabási oldalának](https://azure.microsoft.com/pricing/details/media-services/)élő videó szakaszában.

Ez a cikk azt ismerteti, hogyan engedélyezhető az élő átírás egy élő esemény Azure Media Services használatával való továbbításakor. A folytatás előtt győződjön meg arról, hogy ismeri a Media Services v3 REST API-k használatát (részletekért tekintse meg [ezt az oktatóanyagot](stream-files-tutorial-with-rest.md) ). Ismernie kell az [élő adatfolyam-továbbítási](live-streaming-overview.md) koncepciót is. Azt javasoljuk, hogy a [streamet a Media Services oktatóanyaggal élő közvetítéssel](stream-live-tutorial-with-api.md) fejezze be.

## <a name="live-transcription-preview-regions-and-languages"></a>Élő átiratok előzetes verziójának régiói és nyelvei

Az élő átírás a következő régiókban érhető el:

- Délkelet-Ázsia
- Nyugat-Európa
- Észak-Európa
- USA keleti régiója
- Az USA középső régiója
- USA déli középső régiója
- USA 2. nyugati régiója
- Dél-Brazília

Ez az elérhető nyelveket tartalmazó lista, az API-ban használt nyelvi kód használatával.

| Nyelv | Nyelvkód |
| -------- | ------------- |
| Katalán  | ca-ES |
| Dán (Dánia) | da-DK |
| Német (Németország) | de-DE |
| Angol (Ausztrália) | EN-AU |
| Angol (Kanada) | en-CA |
| angol (Egyesült Királyság) | en-GB |
| Angol (India) | EN-IN |
| Angol (Új-Zéland) | EN-NZ |
| angol (Egyesült Államok) | en-US |
| Spanyol (Spanyolország) | es-ES |
| Spanyol (Mexikó) | es-MX |
| Finn (Finnország) | fi-FI |
| Francia (Kanada) | fr – CA |
| Francia (Franciaország) | fr-FR |
| Olasz (Olaszország) | it-IT |
| Holland (Hollandia) | nl-NL |
| Portugál (Brazília) | pt-BR |
| Portugál (Portugália) | pt-PT |
| Svéd (Svédország) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Élő esemény létrehozása élő átírással

Ha az átírás bekapcsolásával élő eseményt szeretne létrehozni, küldje el a PUT műveletet a 2019-05-01-Preview API-verzióval, például:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A művelet a következő törzstel rendelkezik (ahol egy átmenő élő esemény jön létre az RTMP-vel a betöltési protokollként). Vegye figyelembe a transzkripciós tulajdonság hozzáadását.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Az élő esemény elindítása után az átirat elindítása vagy leállítása

Elindíthatja és leállíthatja az élő átírást, miközben az élő esemény fut állapotban van. Az élő események elindításával és leállításával kapcsolatos további információkért olvassa el a [Media Services V3 API](media-services-apis-overview.md#long-running-operations)-kkal végzett fejlesztés a hosszan futó műveletek című szakaszát.

Az élő átírások bekapcsolásához vagy az átírási nyelv frissítéséhez az élő eseményt az "átiratok" tulajdonsággal együtt kell megadnia. Az élő átírások kikapcsolásához távolítsa el a "átiratok" tulajdonságot az élő esemény objektumból.  

> [!NOTE]
> **Ha** az élő esemény során többször is be-vagy kikapcsolja az átírást, nem támogatott a forgatókönyv.

Ez az élő átírások bekapcsolására szolgáló példa.

JAVÍTÁS ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Transzkripciós kézbesítés és lejátszás

Tekintse át a [dinamikus csomagolás áttekintése című](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) cikket, amely bemutatja, hogyan használja a szolgáltatás a dinamikus csomagolást videó, hang és szöveg továbbítására különböző protokollokban. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML-ban továbbítja az átmásolt szöveget. Ez a kézbesítés az MPEG-4 rész 30 (ISO/IEC 14496-30) töredékbe van csomagolva. Ha a HLS/TS-n keresztül kézbesítést használ, a szöveg feldarabolt VTT lesz kézbesítve. A stream lejátszásához használhat egy weblejátszót, például a [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
> Azure Media Player használata esetén használja a 2.3.3-es vagy újabb verziót.

## <a name="known-issues"></a>Ismert problémák

Az előzetes verzióban az alábbi ismert problémák állnak az élő átirattal:

- Az alkalmazásoknak az előnézeti API-kat kell használniuk, amelyeket a [Media Services v3 OpenAPI-specifikáció](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)ismertet.
- A digitális jogkezelési (DRM) védelem nem vonatkozik a szöveges nyomon követésre, csak az AES-borítékok titkosítása lehetséges.

## <a name="next-steps"></a>Következő lépések

* [Media Services áttekintése](media-services-overview.md)
