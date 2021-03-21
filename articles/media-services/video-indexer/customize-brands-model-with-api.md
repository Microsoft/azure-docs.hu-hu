---
title: Brands-modell testreszabása Video Indexer API-val
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a márkák modelljét a Video Indexer API-val.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: c3056415d3432dbe64dd1f2bcf974a676bbf6c6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586057"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Brands-modell testreszabása a Video Indexer API-val

A Video Indexer támogatja a beszédfelismerés és a vizualizáció szövegének felderítését a videó-és hangtartalom indexelése és újraindexelése során. A márka észlelési funkciója a Bing márkák adatbázisa által javasolt termékek, szolgáltatások és vállalatok megemlítését azonosítja. Ha például a Microsoft szerepel a videóban vagy a hangtartalomban, vagy ha a videó vizualizációs szövegben jelenik meg, akkor Video Indexer a tartalmat a tartalomban márkaként észleli. Az egyéni márkák modell lehetővé teszi, hogy kizárjon bizonyos márkákat a rendszerből, és olyan márkákat tartalmazzon, amelyek nem feltétlenül a Bing márkák adatbázisában találhatók. További információkért lásd az [Áttekintést](customize-brands-model-overview.md).

> [!NOTE]
> Ha a videó indexelve lett a Brand hozzáadása előtt, újra kell indexelni.

A jelen témakörben leírtak szerint a Video Indexer API-k használatával hozhat létre, használhat és szerkeszthet egyéni márkákat tartalmazó modelleket a videóban. A Video Indexer webhelyét a [Brands Model testreszabása a video Indexer webhelyről](customize-brands-model-with-api.md)című témakörben leírtak szerint is használhatja.

## <a name="create-a-brand"></a>Márka létrehozása

A [Brand API létrehozása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) létrehoz egy új egyéni márkát, és hozzáadja azt a megadott fiók egyéni márkák modelljéhez.

> [!NOTE]
> Ha a True (törzs) értékre állítja a beállítást, a rendszer a következőt adja meg a `enabled` *befoglalási* listán a video Indexer észleléséhez. A False (hamis) értékre állításával a `enabled` márka szerepel a *kizárási* listán, így video Indexer nem fogja felderíteni.

A törzsben beállítható egyéb paraméterek:

* Az `referenceUrl` érték a márka bármely hivatkozási webhelye lehet, például a wikipedia oldalára mutató hivatkozás.
* Az `tags` érték a márka címkéit sorolja fel. Ez a címke a Video Indexer webhely márka *Kategória* mezőjében jelenik meg. Például az "Azure" márka címkézhető vagy kategorizálható "Cloud"-ként.

### <a name="response"></a>Reagálás

A válasz információt nyújt az imént létrehozott márkáról az alábbi példa formátuma alapján.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Márka törlése

A [márka törlése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) API eltávolítja a megadott fiók egyéni márkák modelljéből származó márkát. A fiók meg van adva a `accountId` paraméterben. A sikeres hívás után a márka már nem *szerepel a* Brands *(márkák)* listában.

### <a name="response"></a>Reagálás

A márka törlését követően a rendszer nem adott vissza tartalmat.

## <a name="get-a-specific-brand"></a>Adott márka beszerzése

A [Brand API beolvasása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) lehetővé teszi, hogy a márka azonosítójának használatával megkeresse az egyéni márkák modellben található márka részleteit.

### <a name="response"></a>Reagálás

A válasz az alábbi példa formátumát követve információt nyújt a keresett márkáról (a márka AZONOSÍTÓjának használatával).

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled` a beállítás azt `true` jelzi, hogy a márka szerepel a *befoglalási* listán a video Indexer észleléséhez, és a `enabled` hamis érték azt jelzi, hogy a márka szerepel a *kizárási* listán, így video Indexer nem fogja felderíteni.

## <a name="update-a-specific-brand"></a>Adott márka frissítése

A [Brand API frissítése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) lehetővé teszi, hogy a márka azonosítójának használatával megkeresse az egyéni márkák modellben található márka részleteit.

### <a name="response"></a>Reagálás

A válasz az alábbi példa formátumát követve frissített információt nyújt a frissített adatokról.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Az összes márka beszerzése

A [minden márka beolvasása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API az egyéni márkák modellben lévő összes márkát visszaadja a megadott fiókhoz, függetlenül attól, hogy a márka a *befoglalási* vagy *kizárási* márkák listájában szerepel-e.

### <a name="response"></a>Reagálás

A válasz tartalmazza a fiókban lévő összes márka listáját, és az alábbi példa formátumát követve mindegyik részletet.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A *példaként* megadott márka a *befoglalások* listájában szerepel a video Indexer észleléséhez, és a *pelda2* nevű márka a *kizárási* listán található, így video Indexer nem fogja felderíteni.

## <a name="get-brands-model-settings"></a>A Brands modell beállításainak beolvasása

A [Brands beállítások beolvasása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) API a megadott fiók márkák modell beállításait adja vissza. A márkák modell beállításai azt jelzik, hogy engedélyezve van-e a Bing Brands adatbázisának észlelése. Ha a Bing-márkák nincsenek engedélyezve, a Video Indexer csak a megadott fiók Custom Brands modelljéből származó márkákat fogja felderíteni.

### <a name="response"></a>Reagálás

A válasz azt jelzi, hogy a Bing márkák engedélyezve vannak-e az alábbi példa formátuma szerint.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` az igaz érték azt jelenti, hogy a Bing-márkák engedélyezve vannak. Ha `useBuiltin` a hamis, a Bing-márkák le vannak tiltva. Az `state` érték figyelmen kívül hagyható, mert elavult.

## <a name="update-brands-model-settings"></a>A Brands modell beállításainak frissítése

A [márkák frissítése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) API frissíti a márkák modell beállításait a megadott fiókban. A márkák modell beállításai azt jelzik, hogy engedélyezve van-e a Bing Brands adatbázisának észlelése. Ha a Bing-márkák nincsenek engedélyezve, a Video Indexer csak a megadott fiók Custom Brands modelljéből származó márkákat fogja felderíteni.

A `useBuiltIn` jelző értéke TRUE (igaz) érték azt jelenti, hogy a Bing-márkák engedélyezve vannak. Ha `useBuiltin` a hamis, a Bing-márkák le vannak tiltva.

### <a name="response"></a>Reagálás

A Brands modell beállításának frissítése nem történt meg a visszaadott tartalomban.

## <a name="next-steps"></a>Következő lépések

[A Brands modell testreszabása webhely használatával](customize-brands-model-with-website.md)
