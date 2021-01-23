---
title: 'Rövid útmutató: a & REST használatával kezelheti a tudásbázist – QnA Maker'
description: Ez a rövid útmutató bemutatja, hogyan hozhatja létre, teheti közzé és kérdezheti le tudásbázisát a REST API-k használatával.
ms.date: 1/22/2021
ms.topic: quickstart
ms.openlocfilehash: d4c832c4a96ee1419b9e2f0accc1b86e9394ee02
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701287"
---
[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

* A [curl](https://curl.haxx.se/)jelenlegi verziója. A gyors útmutatóban számos parancssori kapcsolót használunk, amelyeket a [curl dokumentációjában](https://curl.haxx.se/docs/manpage.html)talál.
* A kulcs és az erőforrás nevének használatához [QnA Maker erőforrásra](../How-To/set-up-qnamaker-service-azure.md)van szükség. Az erőforrás **nevét** az erőforrás létrehozásakor adta meg, a kulcs pedig az Ön számára lett létrehozva. Az erőforrás neve a végpont altartománya lesz. A kulcs és az erőforrás nevének lekéréséhez **válassza a Azure Portal az** erőforráshoz tartozó rövid útmutató elemet. Az erőforrás neve a végpont URL-címének első altartománya:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> A következő BASH-példák a `\` sor folytatási karakterét használják. Ha a konzol vagy a terminál más sor folytatási karaktert használ, használja ezt a karaktert.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

* A [curl](https://curl.haxx.se/)jelenlegi verziója. A gyors útmutatóban számos parancssori kapcsolót használunk, amelyeket a [curl dokumentációjában](https://curl.haxx.se/docs/manpage.html)talál.
* A kulcs és az erőforrás nevének használatához [QnA Maker erőforrásra](../How-To/set-up-qnamaker-service-azure.md)van szükség. Az erőforrás **nevét** az erőforrás létrehozásakor adta meg, a kulcs pedig az Ön számára lett létrehozva. Az erőforrás neve a végpont altartománya lesz. A kulcs és az erőforrás nevének lekéréséhez **válassza a Azure Portal az** erőforráshoz tartozó rövid útmutató elemet. Az erőforrás neve a végpont URL-címének első altartománya:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1`

> [!CAUTION]
> A következő BASH-példák a `\` sor folytatási karakterét használják. Ha a konzol vagy a terminál más sor folytatási karaktert használ, használja ezt a karaktert.

---

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Ha a REST API-kkal és a cURLtel szeretne tudásbázist létrehozni, a következő információkra lesz szüksége:

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Az tudásbázist leíró JSON|`-d` param|[Példák](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) a JSON-ra|
|A JSON mérete bájtban|`-h`fejléc paraméterei `Content-Size`||

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás-nevével, az erőforrás kulcsával és a JSON-értékekkel és a JSON-mérettel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A QnA Makerból származó cURL-válasz tartalmazza a `operationId` , amely a [művelet állapotának beolvasásához](#get-status-of-operation)szükséges.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)


Ha a REST API-kkal és a cURLtel szeretne tudásbázist létrehozni, a következő információkra lesz szüksége:

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Az tudásbázist leíró JSON|`-d` param|[Példák](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) a JSON-ra|
|A JSON mérete bájtban|`-h`fejléc paraméterei `Content-Size`||

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás-nevével, az erőforrás kulcsával és a JSON-értékekkel és a JSON-mérettel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A QnA Makerból származó cURL-válasz tartalmazza a `operationId` , amely a [művelet állapotának beolvasásához](#get-status-of-operation)szükséges.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```
---

## <a name="get-status-of-operation"></a>Művelet állapotának beolvasása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Amikor létrehoz egy tudásbázist, mert a művelet aszinkron, a válasz az állapot meghatározására vonatkozó információkat tartalmaz.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|Művelet azonosítója|URL-útvonal|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás-nevével, az erőforrás kulcsával és a műveleti AZONOSÍTÓval.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A cURL válasza tartalmazza az állapotot. Ha a művelet állapota sikeres, akkor a `resourceLocation` tartalmazza a TUDÁSBÁZIS azonosítóját.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)


Amikor létrehoz egy tudásbázist, mert a művelet aszinkron, a válasz az állapot meghatározására vonatkozó információkat tartalmaz.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|Művelet azonosítója|URL-útvonal|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás-nevével, az erőforrás kulcsával és a műveleti AZONOSÍTÓval.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A cURL válasza tartalmazza az állapotot. Ha a művelet állapota sikeres, akkor a `resourceLocation` tartalmazza a TUDÁSBÁZIS azonosítóját.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

---

## <a name="publish-knowledge-base"></a>Tudásbázis közzététele

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A Tudásbázis lekérdezése előtt a következőket kell tennie:
* Tudásbázis közzététele
* A futásidejű végpont kulcsának beolvasása

Ez a feladat közzéteszi a tudásbázist. A futásidejű végpont kulcsának beolvasása [különálló feladat](#get-published-knowledge-bases-runtime-endpoint-key).

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Tudásbázis-azonosító|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás neve, az erőforrás kulcsa és a Tudásbázis-azonosító alapján.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

A válasz állapota 204, és nincs találat. A `-v` parancssori paraméter használatával megtekintheti a curl parancs részletes kimenetét. Ez magában foglalja a HTTP-állapotot is.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A Tudásbázis lekérdezése előtt a következőket kell tennie:
* Tudásbázis közzététele
* A futásidejű végpont kulcsának beolvasása

Ez a feladat közzéteszi a tudásbázist. A futásidejű végpont kulcsának beolvasása [különálló feladat](#get-published-knowledge-bases-runtime-endpoint-key).

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Tudásbázis-azonosító|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás neve, az erőforrás kulcsa és a Tudásbázis-azonosító alapján.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

A válasz állapota 204, és nincs találat. A `-v` parancssori paraméter használatával megtekintheti a curl parancs részletes kimenetét. Ez magában foglalja a HTTP-állapotot is.

---

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Közzétett Tudásbázis futásidejű végpontjának beolvasása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A Tudásbázis lekérdezése előtt a következőket kell tennie:
* Tudásbázis közzététele
* A futásidejű végpont kulcsának beolvasása

Ez a feladat lekéri a futásidejű végpont kulcsát. A Tudásbázis közzététele egy [külön feladat](#publish-knowledge-base).

A futásidejű végpont kulcsa a QnA Maker erőforrást használó összes tudásbázishoz tartozó kulcs.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás-nevével, az erőforrás kulcsával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A cURL válasza tartalmazza a futásidejű végpont kulcsait. Csak az egyik kulcsot használja a lekérdezéshez, hogy választ kapjon a Tudásbázisból.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A Tudásbázis lekérdezése előtt a következőket kell tennie:
* Tudásbázis közzététele
* A futásidejű végpont kulcsának beolvasása

Ez a feladat lekéri a futásidejű végpont kulcsát. A Tudásbázis közzététele egy [külön feladat](#publish-knowledge-base).

A futásidejű végpont kulcsa a QnA Maker erőforrást használó összes tudásbázishoz tartozó kulcs.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás-nevével, az erőforrás kulcsával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A cURL válasza tartalmazza a futásidejű végpont kulcsait. Csak az egyik kulcsot használja a lekérdezéshez, hogy választ kapjon a Tudásbázisból.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

---

## <a name="query-for-answer-from-published-knowledge-base"></a>A közzétett Tudásbázis válaszának lekérdezése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Az ismeretekből való válaszadás egy külön futtatókörnyezetből történik, mint a Tudásbázis kezelésekor. Mivel ez egy különálló futtatókörnyezet, egy futásidejű kulccsal kell hitelesítenie.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker futtatókörnyezet kulcsa|`-h`fejléc paraméterei `Authorization`|A kulcs egy olyan karakterlánc része, amely tartalmazza a szót `Endpointkey ` . Hitelesítés QnA Maker szolgáltatásban|
|Tudásbázis-azonosító|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|Lekérdezési JSON leírása|`-d` param|A [kérelem törzsének paraméterei](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) és a JSON- [példák](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|A JSON mérete bájtban|`-h`fejléc paraméterei `Content-Size`||

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás neve, az erőforrás kulcsa és a Tudásbázis-azonosító alapján.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

A sikeres válasz magába foglalja a legjobb választ, valamint az ügyfélalkalmazások, például a csevegési robotokat is, a felhasználónak adott választ kell megjelenítenie.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Az ismeretekből való válaszadás egy külön futtatókörnyezetből történik, mint a Tudásbázis kezelésekor. Mivel ez egy különálló futtatókörnyezet, egy futásidejű kulccsal kell hitelesítenie.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Tudásbázis-azonosító|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|Lekérdezési JSON leírása|`-d` param|A [kérelem törzsének paraméterei](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) és a JSON- [példák](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|A JSON mérete bájtban|`-h`fejléc paraméterei `Content-Size`||

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás neve, az erőforrás kulcsa és a Tudásbázis-azonosító alapján.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

A sikeres válasz magába foglalja a legjobb választ, valamint az ügyfélalkalmazások, például a csevegési robotokat is, a felhasználónak adott választ kell megjelenítenie.


---

## <a name="delete-knowledge-base"></a>Tudásbázis törlése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Ha elkészült a tudásbázissal, törölje azt.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Tudásbázis-azonosító|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás neve, az erőforrás kulcsa és a Tudásbázis-azonosító alapján.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A válasz állapota 204, és nincs találat. A `-v` parancssori paraméter használatával megtekintheti a curl parancs részletes kimenetét. Ez magában foglalja a HTTP-állapotot is.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Ha elkészült a tudásbázissal, törölje azt.

|Tájékoztatás|cURL-konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához használatos|
|QnA Maker erőforrás kulcsa|`-h`fejléc paraméterei `Ocp-Apim-Subscription-Key`|Hitelesítés QnA Maker szolgáltatásban|
|Tudásbázis-azonosító|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

A cURL parancsot egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezt a parancsot a saját erőforrás neve, az erőforrás kulcsa és a Tudásbázis-azonosító alapján.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A válasz állapota 204, és nincs találat. A `-v` parancssori paraméter használatával megtekintheti a curl parancs részletes kimenetét. Ez magában foglalja a HTTP-állapotot is.

---

## <a name="additional-resources"></a>További források

* [Szerzői műveletek](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) Dokumentáció
* [Futtatókörnyezet](/rest/api/cognitiveservices/qnamaker4.0/runtime) Dokumentáció
* [BASH-parancsfájlok minta a cURL használatával](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)