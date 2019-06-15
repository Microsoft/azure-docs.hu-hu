---
title: Hogyan darabolásos átviteli hang Stream |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Darabolásos trasfer audio-adatfolyamot küldeni a Bing Speech service használatával
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d9796cf60e2695c21d781131c935d24891401efa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515010"
---
# <a name="chunked-transfer-encoding"></a>Darabolásos átviteli kódolás

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Microsoft beszédfelismerési API lefényképezze beszédfelismerés, lehetővé teszi a hanganyag elküldeni egy teljes adattömbök, vagy a hanganyag részeket felosztása kisebb tömbökre. A hatékony hang streaming, és csökkenti a késéseket beszédátírási, ajánlott használt [darabolásos átviteli kódolás](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) a szolgáltatásnak a hanganyag streamelésére. Más felhasználói által tapasztalt késés magasabb eredményezhet. További információkért lásd: a [Audio-adatfolyamokat](../concepts.md#audio-streams) lapot.

> [!NOTE]
> Nem tölthet fel hang bármely egy kérelem több mint 10 másodpercet, és a kérelmek teljes időtartama nem haladhatja meg a 14 másodperc.
> [!NOTE]
> Meg kell adnia a darabolásos átvitel kódolási csak akkor, ha használja a [REST API-k](../GetStarted/GetStartedREST.md) a beszédfelismerési szolgáltatás hívásához. Használó alkalmazások [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) nem szükséges konfigurálni a darabolásos átvitel kódolási.

A következő kód bemutatja, hogyan állítsa be a darabolásos átvitel kódolási és 1024 bájt méretű adattömbökbe folyamatban darabolásos hangfájl küldéséhez.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
