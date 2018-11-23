---
title: Át Azure Video Indexer API v1, v2
titlesuffix: Azure Media Services
description: Ez a témakör azt ismerteti, hogyan migrálhat az Azure Video Indexer API v1, v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ad2e2dace7d94c196d46beed957b39f3953246b5
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292395"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>A Video Indexer API v1, v2 át

> [!Note]
> A Video Indexer API 1-es verziója 2018. augusztus 1-jével elavult. Használja a Video Indexer API 2-es verzióját. <br/>A Video Indexer API-k 2-es verziójával való fejlesztéshez tekintse át az [itt](https://api-portal.videoindexer.ai/) található útmutatót. 

Ez a cikk ismerteti a v2-ben bevezetett módosítások.  

## <a name="api-changes"></a>API-módosítás

### <a name="authorization-and-operations"></a>Engedélyezési és műveletek

A v2 verzióban a Video Indexer módosítani a hitelesítési és engedélyezési modellje, az API-t. Nincsenek az API-k két készlet: 

* Engedélyezés 
* Műveletek

A **engedélyezési** API meghívása a hozzáférési tokenek beszerzése érdekében használja a **műveletek** API-t. A **műveletek** API tartalmaz minden a Video Indexer API-k, például videó feltöltése, a Get insights és az egyéb műveletek.

Egyszer, [előfizetés](video-indexer-get-started.md) , a **engedélyezési** API-t, akkor fog tudni hozzáférési tokenek beszerzése az előfizetési kulcs átadásával (ahogy v1-ben.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Első és a hozzáférési jogkivonat használata műveletek API-k

Hívásakor a **műveletek** API-k, az előfizetési kulcs többé nem fogja használni. Ehelyett a kapott hozzáférési jogkivonatok fogja továbbítani az **engedélyezési** API-t. 

Minden kérelmet el kell nevezni a hozzáférési szintet az API-t hívja meg a megfelelő érvényes token. A felhasználó, például az első a fiókok az operations például a felhasználói hozzáférési tokent igényelnek. A fiók műveleteket, például a listát az összes videó szintű kötelező, -fiók hozzáférési jogkivonatának. A videók, például reindex video-műveletekhez szükség van, vagy-fiók hozzáférési jogkivonatának, vagy a videó hozzáférési tokent.

Egyszerűbbé tételéhez, használhatja a **engedélyezési** API > **GetAccounts** beolvasni a fiókok a felhasználó beszerzése nélkül jogkivonatot először. Lekérheti az érvényes jogkivonattal rendelkező fiókokat is, így nem kell még egy hívással fiókjogkivonatot lekérnie.

A másik hozzáférési jogkivonatok kapcsolatos további információkért lásd: [használata Azure Video Indexer API](video-indexer-use-apis.md).

### <a name="locations"></a>Helyek

Az API minden meghívásához tartalmaznia kell a Video Indexer-fiókot a helyét. A hely nélkül vagy rossz helyre API-hívás sikertelen lesz.

Az alábbi táblázatban szereplő értékek érvényesek. A **Paraméter értéke** az API használatakor megadott érték.

|**Name (Név)**|**Paraméter értéke**|**Leírás**|
|---|---|---|
|Próbaverzió|trial|Próbafiókokhoz használható. Például: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|USA nyugati régiója|westus2|Az Azure USA 2. nyugati régiójában használható.  Például: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Észak-Európa |northeurope|Az Azure Észak-Európa régiójában használható. Például: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Kelet-Ázsia|eastasia|Az Azure Kelet-Ázsia régiójában használható. Például: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Adatmodell

Video Indexer – most már rendelkezik egy egyszerűsített adatmodellt sok világosabb elemzéseket kínálnak. A kimenet a v2 API által előállított kapcsolatos további információkért lásd: [a a v2 API által előállított Videóindexelő kimenetének vizsgálata](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

A Video Indexer API-definíciók ennek megfelelően frissíti és keresztül letöltésével [Video Indexer fejlesztői portál](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>V1 vagy V2-példák

Az új V2 API-k 3 fő paraméterek között:

1. [Helye] – a fent leírt módon. Próbaverzió, westus2, northeurope vagy eastasia.
2. [YOUR_ACCOUNT_ID] – a fiók A Guid-azonosítója. Olvassa be az összes fiókok (lásd alább) beolvasásakor.
3. [YOUR_VIDEO_ID] – a videót (például "d4fa369abc") azonosítója. Videó feltöltése közben vagy videók keresése adott vissza.

#### <a name="uploading-a-video-in-v1"></a>Videó kedvelésének jelzése a V1 feltöltése:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>A v2-ben a videó feltöltése:

1. Hozzáférési jogkivonat beszerzése az feltöltési kérés:

  Minden fiók és a hozzáférési tokenek vagy kaphat:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Vagy a fiók hozzáférési jogkivonat beszerzése:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Videó feltöltése:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Elemzések lekérése a V1-ben:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Elemzések lekérése a v2-ben:

1. A fiók hozzáférési jogkivonatának használjon, vagy egy videó az Objektumszintű hozzáférési token beszerzése:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Elemzések lekérése:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Videó feldolgozási állapota első V1-ben:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Kezdeti lépéseket ismertető videó feldolgozási állapota a v2-ben:

Az API v2-ben a feldolgozási állapot az első videó Index API részeként adja vissza.

1. A fiók hozzáférési jogkivonatának használjon, vagy egy videó az Objektumszintű hozzáférési token beszerzése:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Elemzések lekérése:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>További lépések

[Az Azure Video Indexer API használata](video-indexer-use-apis.md)

