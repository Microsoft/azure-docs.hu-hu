---
title: v1 – v2 API-Migrálás
titleSuffix: Azure Cognitive Services
description: Az 1-es verziójú végpont és a szerzői Language Understanding API-k elavultak. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 2. verziójú végpontra és az API-k létrehozására.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018752"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1 – v2 áttelepítési útmutató LUIS-alkalmazásokhoz
Az 1-es verziójú [végpont](https://aka.ms/v1-endpoint-api-docs) és a [szerzői](https://aka.ms/v1-authoring-api-docs) API-k elavultak. Ebből az útmutatóból megtudhatja, hogyan telepítheti át a 2. verziójú [végpontra](https://go.microsoft.com/fwlink/?linkid=2092356) és az API-k [létrehozására](https://go.microsoft.com/fwlink/?linkid=2092087) .

## <a name="new-azure-regions"></a>Új Azure-régiók
A LUIS API-kkal új [régiók](./luis-reference-regions.md) vannak megadva. A LUIS egy másik portált biztosít a régiók csoportjai számára. Az alkalmazást a lekérdezni kívánt régióban kell létrehozni. Az alkalmazások nem telepítik át automatikusan a régiókat. Exportálja az alkalmazást az egyik régióból, majd importálja egy másikba, hogy elérhető legyen egy új régióban.

## <a name="authoring-route-changes"></a>Az útvonal változásainak létrehozása
A szerzői művelet API-útvonala megváltozott a **prog** útvonal használatával az **API** -útvonal használatával.


| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps|
|2|/Luis/**API**-/v2.0/apps|


## <a name="endpoint-route-changes"></a>Végpont útvonalának módosításai
A Endpoint API új lekérdezési karakterlánc-paramétereket és egy másik választ is tartalmaz. Ha a részletes jelző értéke TRUE (igaz), az összes szándék, a pontszámtól függetlenül, a rendszer a topScoringIntent mellett egy szándék nevű tömböt ad vissza.

| version | Útvonal lekérése |
|--|--|
|1|/Luis/v1/Application? ID = {appId} &q = {q}|
|2|/luis/v2.0/apps/{appId}? q = {q} [&timezoneOffset] [&részletesen] [&helyesírás] [&előkészítés] [&Bing-Spell-Check-előfizetés-Key] [&log]|


v1-végpont sikerességi válasza:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2-végpont sikerességi válasza:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>A kulcskezelő szolgáltatás már nem az API-ban
Az előfizetési végponti kulcs API-jai elavultak, a visszaadott 410.

| version | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Az Azure- [végpont kulcsai](luis-how-to-azure-subscription.md) a Azure Portalban jönnek létre. A kulcsot a **[közzétételi](luis-how-to-azure-subscription.md)** oldalon található Luis-alkalmazáshoz rendeli. Nem kell tudnia a tényleges kulcs értékét. LUIS az előfizetés nevét használja a hozzárendelés elvégzéséhez.

## <a name="new-versioning-route"></a>Új verziószámozási útvonal
A v2 modell már egy [verzióban](luis-how-to-manage-versions.md)található. A verzió neve 10 karakter az útvonalon. Az alapértelmezett verzió: "0,1".

| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps/{AppID}/Entities|
|2|/Luis/**API**/v2.0/apps/{AppID}/-**verziók**/{versionId}/Entities|

## <a name="metadata-renamed"></a>A metaadatok átnevezve
Számos, a LUIS-metaadatokat visszaadó API új névvel rendelkezik.

| v1 útvonal neve | v2 útvonal neve |
|--|--|
|PersonalAssistantApps |asszisztensek|
|applicationcultures|kulturális környezetek|
|applicationdomains|tartományok|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Sample" átnevezve erre: "javaslat"
A LUIS a meglévő [végpontok hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) hosszúságú kimondott szöveg javasolja, amelyek növelhetik a modellt. Az előző verzióban a neve **minta** volt. Az új verzióban a név a **minta alapján módosul.** Ezt nevezzük **[felülvizsgálati végpont hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md)** a Luis webhelyén.

| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps/{AppID}/Entities/{entityId}/**minta**|
|1|/Luis/v1.0/**prog**/apps/{AppID}/intents/{intentId}/**minta**|
|2|/Luis/**API**/v2.0/apps/{AppID}/-**verziók**/{versionId}/Entities/{entityId}/**javaslat**|
|2|/Luis/**API**/v2.0/apps/{AppID}/-**verziók**/{versionId}/intents/{intentId}/**javaslat**|


## <a name="create-app-from-prebuilt-domains"></a>Alkalmazás létrehozása előre elkészített tartományokból
Az előre [elkészített tartományok](./howto-add-prebuilt-models.md) előre definiált tartományi modellt biztosítanak. Az előre elkészített tartományok lehetővé teszik, hogy gyorsan fejlessze a LUIS alkalmazást a közös tartományokhoz. Ez az API lehetővé teszi, hogy egy előre elkészített tartományon alapuló új alkalmazást hozzon létre. A válasz az új appID.

|v2 útvonal|művelet|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |Letöltés, közzététel|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>1. x alkalmazás importálása 2. x-re
Az exportált 1. x alkalmazás JSON-je néhány olyan területtel rendelkezik, amelyet a [LUIS][LUIS] 2,0-ba való importálás előtt módosítania kell.

### <a name="prebuilt-entities"></a>Előre összeállított entitások
Az [előre elkészített entitások](./howto-add-prebuilt-models.md) módosultak. Győződjön meg arról, hogy a v2 előre elkészített entitásokat használja. Ide tartozik a [datetimeV2](luis-reference-prebuilt-datetimev2.md)használata a DateTime helyett.

### <a name="actions"></a>Műveletek
A műveletek tulajdonság már nem érvényes. Üresnek kell lennie

### <a name="labeled-utterances"></a>Címkézett hosszúságú kimondott szöveg
A v1 engedélyezett címkével ellátott hosszúságú kimondott szöveg a szó vagy kifejezés elején vagy végén szóközöket is tartalmazhat. A szóközök el lettek távolítva.

## <a name="common-reasons-for-http-response-status-codes"></a>A HTTP-válasz állapotkódok gyakori okai
Lásd: [Luis API Response Codes](luis-reference-response-codes.md).

## <a name="next-steps"></a>Következő lépések

A v2 API dokumentációjának használatával frissítheti a meglévő REST-hívásokat a LUIS- [végpontra](https://go.microsoft.com/fwlink/?linkid=2092356) és a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) API-kra.

[LUIS]: ./luis-reference-regions.md