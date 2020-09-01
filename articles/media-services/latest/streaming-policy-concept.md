---
title: Streaming-szabályzatok a Azure Media Servicesban | Microsoft Docs
description: Ez a cikk ismerteti a folyamatos átviteli házirendeket, valamint azt, hogy a Azure Media Services hogyan használják őket.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 529d27a831945123e1d2eda225e40864a81ee18e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257568"
---
# <a name="streaming-policies"></a>Streamelési szabályok

Azure Media Services v3 esetén a [folyamatos átviteli házirendek](/rest/api/media/streamingpolicies) lehetővé teszik a folyamatos átviteli protokollok és titkosítási beállítások megadását a [streaming-lokátorok](streaming-locators-concept.md)számára. A Media Services v3 néhány előre definiált adatfolyam-szabályzatot biztosít, hogy azokat közvetlenül próbaverzióhoz vagy éles üzemhez lehessen használni. 

A jelenleg elérhető előre definiált folyamatos átviteli szabályzatok:<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* "Predefined_DownloadAndClearStreaming"
* "Predefined_ClearKey"
* "Predefined_MultiDrmCencStreaming" 
* "Predefined_MultiDrmStreaming"

A következő "döntési fa" segítségével kiválaszthatja a forgatókönyvhöz előre definiált adatfolyam-szabályzatot.

> [!IMPORTANT]
> * A DateTime típusú **adatfolyam-házirendek** tulajdonságai mindig UTC formátumban jelennek meg.
> * A Media Service-fiókhoz korlátozott számú szabályzatot kell terveznie, és újra fel kell használni őket a streaming-lokátorok számára, amikor ugyanazok a beállítások szükségesek. További információ: [kvóták és korlátozások](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Döntési fa

Kattintson a képre a teljes méretű megjelenítéshez.  

[![Egy olyan döntési fát ábrázoló diagram, amelynek célja, hogy segítsen kiválasztani egy előre definiált adatfolyam-szabályzatot a forgatókönyvhöz.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Ha titkosítja a tartalmat, létre kell hoznia egy [tartalmi kulcsra vonatkozó házirendet](content-key-policy-concept.md), a **tartalmi kulcs házirendje** nem szükséges a folyamatos átvitelhez vagy a letöltéshez. 

Ha speciális követelményekkel rendelkezik (például ha különböző protokollokat szeretne megadni, egyéni kulcsú kézbesítési szolgáltatást kell használnia, vagy tiszta hangsávot kell használnia), [létrehozhat](/rest/api/media/streamingpolicies/create) egyéni adatfolyam-házirendet. 

## <a name="get-a-streaming-policy-definition"></a>Folyamatos átviteli szabályzat definíciójának beolvasása  

Ha meg szeretné tekinteni az adatfolyam-szabályzat definícióját, használja a [Get](/rest/api/media/streamingpolicies/get) lehetőséget, és adja meg a szabályzat nevét. Például:

### <a name="rest"></a>REST

Kérés:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Válasz:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Szűrés, rendezés, lapozás

Lásd: [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata](protect-with-aes128.md)
* [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
