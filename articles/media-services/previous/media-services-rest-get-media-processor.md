---
title: Adathordozó-feldolgozó példány beszerzése a REST használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre a médiafájlok kódolására, konvertálására, titkosítására és visszafejtésére szolgáló adathordozó-feldolgozó összetevőt Azure Media Services számára.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3d7b3922c9bb7eb79cd6436ba1b265714678fcc8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774912"
---
# <a name="how-to-get-a-media-processor-instance"></a>Adathordozó-feldolgozó példány beszerzése
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
A Media processors olyan összetevő, amely egy adott videó-vagy hangfeldolgozási feladatot kezel, például a kódolást, a konvertálást, a titkosítást vagy a médiatartalom visszafejtését. A Media Services elküldött összes feladatnak szüksége van egy adathordozó-processzorra a videó-vagy hangtartalom kódolásához, titkosításához vagy átalakításához. 

## <a name="azure-media-processors"></a>Azure Media processors 

A következő témakör az adathordozó-processzorok listáját tartalmazza:

* [Kódoló adathordozó-processzorok](scenarios-and-availability.md#encoding-media-processors)
* [Elemzési adathordozó processzorai](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Serviceshez

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Adathordozó-feldolgozó letöltése

A következő REST-hívás azt mutatja be, hogyan kérhető le a Media Processor-példányok neve (ebben az esetben **Media Encoder standard**). 

Kérés:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Válasz:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, hogyan szerezhet be egy Media Processor-példányt, ugorjon az adategységek [kódolása](media-services-rest-get-started.md) című cikkre, amely bemutatja, hogyan használható a Media Encoder standard egy eszköz kódolásához.

