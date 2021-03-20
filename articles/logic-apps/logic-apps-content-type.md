---
title: Tartalomtípusok kezelése
description: Megtudhatja, hogyan kezelheti a munkafolyamatokban a különböző tartalomtípusokat a tervezési idő és a Futtatás ideje alatt Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 04642e69adba82cf33b933829d72c51109ad2ee1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578181"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Tartalomtípusok kezelése Azure Logic Appsban

A különböző tartalomtípusok egy logikai alkalmazáson keresztül, például JSON-, XML-, egyszerű és bináris adatokat is elhelyezhetnek. Míg a Logic Apps támogatja az összes tartalomtípust, néhány natív támogatással rendelkezik, és nem igényel öntést vagy átalakítást a logikai alkalmazásokban. Más típusok esetén szükség lehet a castingra vagy a konverzióra. Ez a cikk azt ismerteti, hogyan kezeli a Logic Apps a tartalomtípusokat, és hogy miként lehet ezeket a típusokat megfelelően feldolgozni vagy átalakítani, ha szükséges.

A tartalomtípusok kezelésére szolgáló megfelelő módszer meghatározásához Logic Apps a `Content-Type` http-hívások fejlécének értékére támaszkodik, például:

* [alkalmazás/JSON](#application-json) (natív típus)
* [szöveg/egyszerű](#text-plain) (natív típus)
* [alkalmazás/XML és alkalmazás/oktett – Stream](#application-xml-octet-stream)
* [Egyéb tartalomtípusok](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

A Logic Apps az *Application/JSON* tartalomtípusú kérelmeket JavaScript-jelölésű (JSON) objektumként tárolja és kezeli. Alapértelmezés szerint a JSON-tartalom elemzése nélkül is elemezhető. Egy "Application/JSON" típusú fejléctel rendelkező kérelem elemzéséhez használhat kifejezést. Ez a példa a tömb értékét adja vissza a `dog` `animal-type` Casting nélkül: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Ha olyan JSON-adattal dolgozik, amely nem ad meg fejlécet, a JSON [() függvény](../logic-apps/workflow-definition-language-functions-reference.md#json)használatával manuálisan is elvégezheti ezeket az adatátvitelt a JSON-ban, például: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tokenek létrehozása JSON-tulajdonságokhoz

A Logic Apps lehetővé teszi a JSON-tartalomban szereplő tulajdonságokat jelképező felhasználóbarát tokenek létrehozását, hogy a logikai alkalmazás munkafolyamataiban könnyebben hivatkozhat és használhassa ezeket a tulajdonságokat.

* **Kérelem triggere**

  Ha ezt az triggert a Logic app Designerben használja, megadhat egy JSON-sémát, amely leírja a várt adattartalmat. 
  A tervező elemzi a JSON-tartalmat a séma használatával, és olyan felhasználóbarát jogkivonatokat hoz létre, amelyek a JSON-tartalomban szereplő tulajdonságokat jelölik. 
  Ezután egyszerűen hivatkozhat és felhasználhatja ezeket a tulajdonságokat a logikai alkalmazás munkafolyamataiban. 
  
  Ha nem rendelkezik sémával, előállíthatja a sémát. 
  
  1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.  
  
  2. Az **írja be vagy illessze be a minta JSON-adattartalmat** területen adja meg a minta hasznos adatait, majd válassza a **kész** lehetőséget. Például: 

     ![Képernyőkép: "HTTP-kérelem fogadása" művelet, amely egy minta JSON-adattartalmat mutat be.](./media/logic-apps-content-type/request-trigger.png)

     A létrehozott séma most megjelenik az triggerben.

     ![Minta JSON-adattartalom megadása](./media/logic-apps-content-type/generated-schema.png)

     Itt látható a kérelem triggerének alapjául szolgáló definíció a Code View Editorban:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. A kérelemben győződjön meg róla, hogy tartalmaz egy `Content-Type` fejlécet, és állítsa be a fejléc értékét a következőre: `application/json` .

* **JSON-művelet elemzése**

  Ha ezt a műveletet a Logic app Designerben használja, elemezheti a JSON-kimenetet, és létrehozhat olyan felhasználóbarát tokeneket, amelyek a JSON-tartalomban szereplő tulajdonságokat jelölik. 
  Ezután egyszerűen hivatkozhat és felhasználhatja ezeket a tulajdonságokat a logikai alkalmazás munkafolyamataiban. A kérelem triggerhez hasonlóan egy JSON-séma is megadható vagy létrehozható, amely leírja az elemezni kívánt JSON-tartalmat. 
  Így könnyebben használhatja fel Azure Service Bus, Azure Cosmos DB stb. adatait.

  ![JSON értelmezése](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>szöveg/egyszerű

Ha a logikai alkalmazás olyan HTTP-üzeneteket kap, amelyeken a `Content-Type` fejléc van beállítva `text/plain` , a logikai alkalmazás ezeket az üzeneteket nyers formában tárolja. Ha ezeket az üzeneteket a következő műveletekben a casting nélkül is felveszi, a kérések a `Content-Type` fejlécre vannak beállítva `text/plain` . 

Ha például egy egyszerű fájllal dolgozik, HTTP-kérést kap, amelynek a `Content-Type` fejléce a `text/plain` következő:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Ha ezt követően egy későbbi műveletben elküldi ezt a kérést, mint egy másik kérelem törzse, akkor a `@body('flatfile')` második kéréshez egy fejléc is tartozik, amely a következőre van `Content-Type` beállítva: `text/plain` . Ha egyszerű szöveggel dolgozik, de nem adott meg fejlécet, manuálisan is elvégezheti az adat szövegbe írását a [karakterlánc () függvény](../logic-apps/workflow-definition-language-functions-reference.md#string) használatával, például a következő kifejezéssel: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>alkalmazás/XML és alkalmazás/oktett – Stream

Logic Apps mindig megőrzi a `Content-Type` kapott HTTP-kérést vagy választ. Így ha a logikai alkalmazás a készlettel együtt fogadja a tartalmat `Content-Type` `application/octet-stream` , és egy későbbi műveletbe belefoglalja a tartalmat, a kimenő kérelem is `Content-Type` be van állítva `application/octet-stream` . Így a Logic Apps garantálhatja, hogy az adatvesztés a munkafolyamaton keresztül nem vész el. A műveleti állapotot, a bemeneteket és a kimeneteket azonban egy JSON-objektum tárolja, miközben az állapot áthalad a munkafolyamaton. 

## <a name="converter-functions"></a>Átalakító függvények

Egyes adattípusok megőrzése érdekében Logic Apps átalakítja a tartalmat egy bináris Base64 kódolású karakterlánccá a megfelelő metaadatokkal, amelyek megőrzik mind a `$content` hasznos adatokat, mind a-t `$content-type` , amelyeket a rendszer automatikusan konvertál. 

Ez a lista leírja, hogyan alakítja át a Logic Apps tartalmakat a [függvények](../logic-apps/workflow-definition-language-functions-reference.md)használatakor:

* `json()`: Az adatátvitelt a következőre `application/json`
* `xml()`: Az adatátvitelt a következőre `application/xml`
* `binary()`: Az adatátvitelt a következőre `application/octet-stream`
* `string()`: Az adatátvitelt a következőre `text/plain`
* `base64()`: Tartalom konvertálása Base64 kódolású karakterlánccá
* `base64toString()`: Base64 kódolású karakterlánc konvertálása `text/plain`
* `base64toBinary()`: Base64 kódolású karakterlánc konvertálása `application/octet-stream`
* `dataUri()`: Sztring átalakítása adaturi-ra
* `dataUriToBinary()`: Az adaturi-t bináris karakterlánccá alakítja
* `dataUriToString()`: Az adaturi-t karakterlánccá alakítja át.

Ha például olyan HTTP-kérést kap, ahol a `Content-Type` be van állítva `application/xml` , például a következő tartalom:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Ezt a tartalmat a és a függvények kifejezés használatával is elvégezheti `@xml(triggerBody())` `xml()` `triggerBody()` , majd később is használhatja ezt a tartalmat. Vagy használhatja a `@xpath(xml(triggerBody()), '/CustomerName')` kifejezést a `xpath()` és a `xml()` függvényekkel. 

## <a name="other-content-types"></a>Egyéb tartalomtípusok

A Logic Apps együttműködik a szolgáltatással, és támogatja a többi tartalomtípust is, de előfordulhat, hogy manuálisan kell lekérnie az üzenet törzsét a változó dekódolásával `$content` .

Tegyük fel például, hogy a logikai alkalmazás egy, a tartalomtípussal ellátott kérelemben aktiválódik `application/x-www-url-formencoded` . Az összes adat megőrzése érdekében a `$content` kérelem törzsében lévő változó egy Base64-karakterláncként kódolt adattartalmat tartalmaz:

`CustomerName=Frank&Address=123+Avenue`

Mivel a kérés nem egyszerű szöveg vagy JSON, a kérelem a következő módon tárolódik a műveletben:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

A Logic Apps natív funkciókat biztosít az űrlapadatok kezeléséhez, például: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Vagy manuálisan is elérheti az adatelérést egy kifejezéssel, például a következő példával:

`@string(body('formdataAction'))` 

Ha azt szeretné, hogy a kimenő kérelem ugyanazzal a `application/x-www-url-formencoded` tartalomtípus-fejléccel rendelkezzen, a kérést felveheti a művelet törzsébe anélkül, hogy olyan kifejezést kellene használnia, amely például a következő: `@body('formdataAction')` . Ez a módszer azonban csak akkor működik, ha a törzs az egyetlen paraméter a `body` bemenetben. Ha `@body('formdataAction')` egy kérelemben megpróbálja használni a kifejezést `application/json` , futásidejű hibaüzenetet kap, mert a törzs küldése kódolt.
