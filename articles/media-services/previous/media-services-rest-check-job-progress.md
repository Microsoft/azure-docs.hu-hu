---
title: A feladatok előrehaladásának ellenőrzéséhez REST API használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan ellenőrizhető a feladatok előrehaladása Azure Media Services v2 REST API használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2d5f3382965d9166fabf99ac650e707e29c38a72
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057095"
---
# <a name="how-to-check-job-progress"></a>Útmutató: a feladatok előrehaladásának ellenőrzéséhez

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

A feladatok futtatásakor gyakran szükség van a feladat előrehaladásának nyomon követésére. A feladatok állapotát a feladatok állapot tulajdonságával derítheti fel. További információ az állapot tulajdonságról: a [feladatok entitásának tulajdonságai](https://docs.microsoft.com/rest/api/media/operations/job#job_entity_properties).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="check-job-progress"></a>A feladat előrehaladásának ellenőrzése

Kérés:

```console
GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
```

Válasz:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 450
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains

{"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még

[Media Services Operations REST API áttekintése](media-services-rest-how-to-use.md)
