---
title: Logikai alkalmazások hívása, aktiválása vagy beágyazása kérelem-eseményindítók használatával
description: HTTPS-végpontok beállítása a logikai alkalmazások munkafolyamatainak meghívásához, elindításához vagy beágyazásához Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: b345168dad63b1846d46c12721587eaffb5f887e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94981204"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Logikai alkalmazások hívása, elindítása vagy beágyazása HTTPS-végpontok használatával Azure Logic Apps

Ahhoz, hogy a logikai alkalmazás meghívása egy URL-címen történjen, és képes legyen bejövő kéréseket fogadni más szolgáltatásokból, natív módon elérhetővé teheti a szinkron HTTPS-végpontot egy kérelem alapú trigger használatával a logikai alkalmazásban. Ezzel a képességgel más Logic apps-ből hívhatja a logikai alkalmazást, és meghívható végpontok mintáját is létrehozhatja. A bejövő hívások kezelésére szolgáló hívható végpont beállításához a következő típusú triggerek bármelyikét használhatja:

* [Kérés](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Felügyelt összekötő-eseményindítók, amelyek rendelkeznek a [ApiConnectionWebhook típussal](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) , és fogadhatnak Bejövő HTTPS-kéréseket

Ez a cikk bemutatja, hogyan hozhat létre egy hívható végpontot a logikai alkalmazáshoz a kérelem trigger használatával, és hogyan hívhatja meg a végpontot egy másik logikai alkalmazásból. Minden alapelv azonos módon érvényesül a bejövő kérések fogadására használható egyéb trigger-típusokkal.


További információ a biztonságról: hitelesítés és titkosítás a logikai alkalmazásba érkező bejövő hívásokhoz, például [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL), [Azure Active Directory nyílt hitelesítés (Azure ad OAuth)](../active-directory/develop/index.yml), a logikai alkalmazás Azure-beli API Management való kimutatása, illetve a bejövő hívásokat kezdeményező IP-címek korlátozása: a [kérelmeken alapuló eseményindítók számára a biztonságos hozzáférés és az adatokhoz való hozzáférés](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyben a triggert szeretné használni a hívható végpont létrehozásához. Elkezdheti egy üres logikai alkalmazás vagy egy meglévő logikai alkalmazás használatával, ahol lecserélheti az aktuális triggert. Ez a példa egy üres logikai alkalmazással kezdődik. Ha most ismerkedik a Logic apps szolgáltatással, tekintse meg a [Mi az a Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakört.

## <a name="create-a-callable-endpoint"></a>Hívható végpont létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Hozzon létre és nyisson meg egy üres logikai alkalmazást a Logic app Designerben.

1. A keresőmezőbe válassza a **beépített** lehetőséget. A keresőmezőbe írja be `request` szűrőként a kifejezést. Az eseményindítók listából válassza ki, **hogy mikor érkezik HTTP-kérelem**.

   ![A kérelem triggerének megkeresése és kiválasztása](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Ha szükséges, a **kérelem törzse JSON-sémája** mezőben megadhat egy JSON-sémát, amely leírja azt a hasznos adattartalmat vagy adatot, amelyre az indítás várható.

   A tervező ezt a sémát használja az aktiválási kimeneteket jelképező tokenek létrehozásához. Ezután egyszerűen hivatkozhat ezekre a kimenetekre a logikai alkalmazás munkafolyamata során. További információ a [JSON-sémák által generált tokenekről](#generated-tokens).

   Ebben a példában adja meg a következő sémát:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![JSON-séma megadása a kérelem műveletéhez](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Vagy létrehozhat egy JSON-sémát egy minta hasznos adat biztosításával:

   1. A **kérelem** triggerben válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

   1. Az **írja be vagy illessze be a minta JSON-adattartalmat** mezőbe írja be a minta hasznos adatait, például:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Ha elkészült, válassza a **kész** lehetőséget.

      A **kérelem törzse JSON-sémája** most már megjeleníti a generált sémát.

1. Mentse a logikai alkalmazást.

   A **http post URL-cím** mező most megjeleníti a generált visszahívási URL-címet, amelyet más szolgáltatások használhatnak a logikai alkalmazás meghívásához és elindításához. Ez az URL-cím olyan lekérdezési paramétereket tartalmaz, amelyek megadják a hitelesítéshez használt közös hozzáférésű aláírás (SAS) kulcsát.

   ![A végponthoz generált visszahívási URL-cím](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. A visszahívási URL-cím másolásához a következő lehetőségek közül választhat:

   * A **http post URL-cím** mezőtől jobbra válassza a **Másolás URL-cím** (fájlok másolása ikon) lehetőséget.

   * Ezt a hívást a kérelem-trigger által várt metódussal teheti meg. Ez a példa a következő `POST` metódust használja:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Másolja a visszahívási URL-címet a logikai alkalmazás **Áttekintés** paneljéről.

     1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

     1. Az **Összefoglalás** szakaszban válassza az **aktiválási előzmények megtekintése** lehetőséget.

        ![Végpont URL-címének lekérése Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. A **visszahívási URL [post]** alatt másolja ki az URL-címet:

        ![Végpont URL-címének másolása Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>A várt kérelem módszerének kiválasztása

Alapértelmezés szerint a kérelem triggere `POST` kérést vár. Megadhat azonban egy másik módszert, amelyet a hívónak használnia kell, de csak egyetlen metódussal.

1. A kérelem triggerben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **metódus** lehetőséget, amely hozzáadja ezt a tulajdonságot az triggerhez.

   ![A "Method" tulajdonság hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. A **metódus** listából válassza ki azt a metódust, amelyet az indítónak várnia kell. Vagy egyéni metódust is megadhat.

   Válassza például a **Get** metódust, hogy a végpont URL-címét később is tesztelje.

   ![Az trigger által várt kérelem módszerének kiválasztása](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Paraméterek továbbítása a végpont URL-címén keresztül

Ha a paraméter értékeit a végpont URL-címén keresztül szeretné elfogadni, a következő lehetőségek közül választhat:

* [Értékek elfogadása a Get paraméterek vagy az](#get-parameters) URL-paraméterek használatával.

  Ezeket az értékeket a rendszer név-érték párokként adja át a végpont URL-címében. Ehhez a beállításhoz a GET metódust kell használnia a kérelem triggerében. Egy későbbi művelet során a paraméter értékeit trigger kimenetként lehet beolvasni `triggerOutputs()` egy kifejezésben szereplő függvény használatával.

* Értékek elfogadása a kérelem-trigger paramétereinek [relatív elérési útján](#relative-path) .

  Ezeket az értékeket a rendszer a végpont URL-címében lévő relatív elérési úton adja át. Emellett explicit módon [ki kell választania azt a metódust](#select-method) , amelyet az trigger elvár. Egy későbbi művelet során a paraméterek értékét trigger kimenetként is lekérheti, ha közvetlenül a kimenetekre hivatkozik.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Értékek elfogadása a GET paraméterekkel

1. A kérelem triggerben nyissa meg az **új paraméter hozzáadása listát**, adja hozzá a **Method** tulajdonságot az triggerhez, majd válassza a **Get** metódust.

   További információ: a [várt kérelem módszerének kiválasztása](#select-method).

1. A kérelem trigger alatt adja meg azt a műveletet, amelyben használni szeretné a paraméter értékét. Ehhez a példához adja hozzá a **Válasz** műveletet.

   1. A kérelem trigger alatt válassza az **új lépés**  >  **művelet hozzáadása** lehetőséget.
   
   1. A **válasszon műveletet** területen, a keresőmezőbe írja be `response` szűrőként a kifejezést. A műveletek listából válassza ki a **Válasz** műveletet.

1. A `triggerOutputs()` paraméter értékét lekérő kifejezés létrehozásához kövesse az alábbi lépéseket:

   1. Kattintson a válasz tevékenység **törzs** tulajdonságára, hogy megjelenjen a dinamikus tartalom lista, és válassza a **kifejezés** lehetőséget.

   1. A **kifejezés** mezőbe írja be a kifejezést, cserélje `parameter-name` le a paraméter nevét, majd kattintson **az OK gombra**.

      `triggerOutputs()['queries']['parameter-name']`

      !["TriggerOutputs ()" kifejezés hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      A **Body (törzs** ) tulajdonságban a kifejezés feloldása a `triggerOutputs()` tokenhez.

      ![Megoldott "triggerOutputs ()" kifejezés](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Ha menti a logikai alkalmazást, navigáljon a tervezőtől, és térjen vissza a tervezőhöz, a token megjeleníti a megadott paraméter nevét, például:

      ![Paraméter neve feloldott kifejezés](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      A kód nézetben a **Body (törzs** ) tulajdonság a válasz művelet definíciójában jelenik meg a következő módon:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Tegyük fel például, hogy egy nevű paraméter értékét át szeretné adni `postalCode` . A **Body** tulajdonság megadja a karakterláncot `Postal Code: ` egy záró szóközzel, amelyet a megfelelő kifejezés követ:

      ![Példa "triggerOutputs ()" kifejezés hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. A hívható végpont teszteléséhez másolja a visszahívási URL-címet a kérelem-triggerből, és illessze be az URL-címet egy másik böngészőablakba. Az URL-címben adja hozzá a kérdőjelet () követő paraméter nevét és értékét az `?` URL-címhez a következő formátumban, majd nyomja le az ENTER billentyűt.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   A böngésző a következő szöveggel kapcsolatos választ ad vissza: `Postal Code: 123456`

   ![Válasz a visszahívási URL-címre küldött kérelemre](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Ha a paraméter nevét és értékét egy másik pozícióban szeretné elhelyezni az URL-címen belül, ügyeljen arra, hogy előtagként használja az jellel ( `&` ), például:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Ez a példa a visszahívási URL-címet mutatja a minta paraméter nevével és értékével `postalCode=123456` az URL-cím különböző helyein belül:

   * 1. pozíció: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * második pozíció: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Ha a kivonatot vagy a font szimbólumot () is bele kívánja foglalni **#** az URI-ba, használja inkább a következő kódolt verziót: `%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Értékek elfogadása relatív elérési úton

1. A kérelem triggerben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **relatív elérési út** lehetőséget, amely hozzáadja ezt a tulajdonságot az triggerhez.

   ![A "relatív elérési út" tulajdonság hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. A **relatív elérési út** tulajdonságnál adja meg a JSON-séma paraméterének relatív elérési útját, amelyet az URL-cím el szeretne fogadni, például: `/address/{postalCode}` .

   ![A paraméter relatív elérési útjának megadása](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. A kérelem trigger alatt adja meg azt a műveletet, amelyben használni szeretné a paraméter értékét. Ehhez a példához adja hozzá a **Válasz** műveletet.

   1. A kérelem trigger alatt válassza az **új lépés**  >  **művelet hozzáadása** lehetőséget.

   1. A **válasszon műveletet** területen, a keresőmezőbe írja be `response` szűrőként a kifejezést. A műveletek listából válassza ki a **Válasz** műveletet.

1. A válasz tevékenység **törzs** tulajdonságában adja meg az trigger relatív elérési útján megadott paramétert jelölő tokent.

   Tegyük fel például, hogy a válasz műveletét vissza szeretné állítani `Postal Code: {postalCode}` .

   1. A **Body (törzs** ) tulajdonságban adjon meg `Postal Code: ` egy záró szóközt. Tartsa a kurzort a szerkesztési mezőben, hogy a dinamikus tartalmak listája nyitva maradjon.

   1. A dinamikus tartalmak listájában, a **http-kérelem fogadásakor** szakaszban válassza ki az **Irányítószám** -tokent.

      ![A megadott paraméter hozzáadása a válasz törzséhez](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      A **Body (törzs** ) tulajdonság mostantól a kiválasztott paramétert is tartalmazza:

      ![Példa válasz törzsére paraméterrel](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Mentse a logikai alkalmazást.

   A kérelem-triggerben a visszahívási URL-cím frissül, és mostantól a relatív elérési utat is tartalmazza, például:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. A hívható végpont teszteléséhez másolja át a frissített visszahívási URL-címet a kérelem-triggerből, illessze be az URL-címet egy másik böngészőablakba, majd cserélje le az URL-címet a értékre `{postalCode}` `123456` , majd nyomja le az ENTER

   A böngésző a következő szöveggel kapcsolatos választ ad vissza: `Postal Code: 123456`

   ![Válasz a visszahívási URL-címre küldött kérelemre](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Ha a kivonatot vagy a font szimbólumot () is bele kívánja foglalni **#** az URI-ba, használja inkább a következő kódolt verziót: `%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>Logikai alkalmazás hívása a végpont URL-címén keresztül

Miután létrehozta a végpontot, aktiválhatja a logikai alkalmazást egy HTTPS-kérés küldésével a végpont teljes URL-címére. A Logic apps beépített támogatást nyújt a közvetlen elérésű végpontokhoz.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Séma alapján generált tokenek

Ha JSON-sémát ad meg a kérelem-triggerben, a Logic app Designer jogkivonatokat hoz létre a séma tulajdonságaihoz. Ezeket a jogkivonatokat használhatja a logikai alkalmazás munkafolyamatán keresztüli adattovábbításhoz.

Ha például további tulajdonságokat (például) ad hozzá `"suite"` a JSON-sémához, akkor ezekhez a tulajdonságokhoz tartozó jogkivonatok használhatók a logikai alkalmazás későbbi lépéseiben. Itt látható a teljes JSON-séma:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Beágyazott logikai alkalmazások létrehozása

A munkafolyamatokat a logikai alkalmazásba úgy ágyazhatja be, hogy más, a kérelmeket fogadó logikai alkalmazásokat is felvesz. A logikai alkalmazások belefoglalásához kövesse az alábbi lépéseket:

1. A lépés alatt, ahol másik logikai alkalmazást szeretne meghívni, válassza az **új lépés**  >  **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be `logic apps` szűrőként a kifejezést. A műveletek listából válassza ki **a Logic apps munkafolyamatot**.

   ![Logikai alkalmazás beágyazása az aktuális logikai alkalmazásba](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   A tervező megjeleníti a jogosult logikai alkalmazásokat a kiválasztáshoz.

1. Válassza ki az aktuális logikai alkalmazásból hívni kívánt logikai alkalmazást.

   ![Válassza ki az aktuális logikai alkalmazásból hívni kívánt logikai alkalmazást](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Hivatkozás egy bejövő kérelem tartalmára

Ha a bejövő kérelem tartalomtípusa, akkor `application/json` hivatkozhat a bejövő kérelemben szereplő tulajdonságokra. Ellenkező esetben ezt a tartalmat egyetlen bináris egységként kezeli a rendszer, amelyet átadhat más API-khoz. Ha ezt a tartalmat a logikai alkalmazás munkafolyamatán belül szeretné hivatkozni, először konvertálnia kell a tartalmat.

Ha például `application/xml` átadja a Type típusú tartalmat, a [ `@xpath()` kifejezéssel](../logic-apps/workflow-definition-language-functions-reference.md#xpath) XPath-kibontást is végrehajthat, vagy az XML-fájl JSON formátumra való átalakítására szolgáló [ `@json()` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#json) használhatja. További információ a támogatott [tartalomtípusok](../logic-apps/logic-apps-content-type.md)használatáról.

Egy bejövő kérelem kimenetének lekéréséhez használhatja a [ `@triggerOutputs` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Tegyük fel például, hogy olyan kimenettel rendelkezik, amely a következő példához hasonlít:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

A tulajdonság eléréséhez használhatja `body` a [ `@triggerBody()` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) parancsikonként.

## <a name="respond-to-requests"></a>Válaszadás a kérelmekre

Időnként a logikai alkalmazást kiváltó bizonyos kérelmekre szeretne válaszolni, ha tartalmat ad vissza a hívónak. A válaszhoz tartozó állapotkód, fejléc és törzs létrehozásához használja a válasz műveletet. Ez a művelet bárhol megjelenhet a logikai alkalmazásban, nem csak a munkafolyamat végén. Ha a logikai alkalmazás nem tartalmaz válasz műveletet, a végpont *azonnal* válaszol az **202 elfogadott** állapottal.

Ahhoz, hogy az eredeti hívó sikeresen lekérje a választ, a válaszhoz szükséges összes lépésnek a [kérés időkorlátján](./logic-apps-limits-and-config.md) belül kell lennie, kivéve, ha az aktivált logikai alkalmazást beágyazott logikai alkalmazásnak nevezzük. Ha ezen a korláton belül nem ad vissza választ, a bejövő kérelem időtúllépést okoz, és megkapja az **408-ügyfél időkorlát** -válaszát.

A beágyazott logikai alkalmazások esetében a szülő logikai alkalmazás továbbra is vár a válaszra, amíg az összes lépést be nem fejeződik, függetlenül attól, hogy mennyi idő szükséges.

### <a name="construct-the-response"></a>A válasz felépítése

A válasz törzsében több fejlécet és bármilyen típusú tartalmat is megadhat. A válasz fejléce például azt adja meg, hogy a válasz tartalomtípusa, `application/json` és hogy a törzs tartalmazza a és a tulajdonságok értékeit a `town` `postalCode` témakörben korábban ismertetett JSON-séma alapján a kérelem-triggerhez.

![Adja meg a válasz tartalmát a HTTPS-válasz művelethez](./media/logic-apps-http-endpoint/content-for-response-action.png)

A válaszok a következő tulajdonságokkal rendelkeznek:

| Tulajdonság (megjelenítés) | Tulajdonság (JSON) | Leírás |
|--------------------|-----------------|-------------|
| **Állapotkód** | `statusCode` | A bejövő kérelemre adott válaszban használandó HTTPS-állapotkód. Ez a kód bármely érvényes állapotkód lehet, amely 2xx, 4xx vagy 5xx kezdődik. A 3xx-állapotkódok azonban nem engedélyezettek. |
| **Fejlécek** | `headers` | Egy vagy több, a válaszban szerepeltetni kívánt fejléc |
| **Törzs** | `body` | Olyan szövegtörzs, amely lehet egy sztring, egy JSON-objektum, vagy akár egy előző lépésben hivatkozott bináris tartalom is. |
||||

A válasz művelet JSON-definíciójának és a logikai alkalmazás teljes JSON-definíciójának megtekintéséhez a Logic app Designer eszköztárán válassza a **kód nézet** lehetőséget.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Kérdések és válaszok

#### <a name="q-what-about-url-security"></a>K: Mi az URL-biztonság?

**A**: az Azure biztonságos módon generálja a logikai alkalmazás visszahívási URL-címeit [közös hozzáférésű aláírás (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature)használatával. Ez az aláírás lekérdezési paraméterként halad át, és a logikai alkalmazás futtatása előtt érvényesíteni kell. Az Azure egy titkos kulcs egyedi kombinációjával, a trigger nevével és a végrehajtott művelettel hozza létre az aláírást. Tehát ha valaki hozzáfér a titkos logikai alkalmazás kulcsához, nem tud érvényes aláírást előállítani.

> [!IMPORTANT]
> A termelési és a magasabb biztonsági rendszerek esetében erősen ajánljuk, hogy a logikai alkalmazás közvetlenül a böngészőből hívja meg a következő okokat:
>
> * A megosztott elérési kulcs megjelenik az URL-címben.
> * A biztonsági tartalmi házirendeket nem kezelheti Azure Logic Apps ügyfelek megosztott tartományai miatt.

További információ a biztonságról: hitelesítés és titkosítás a logikai alkalmazásba érkező bejövő hívásokhoz, például [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL), [Azure Active Directory nyílt hitelesítés (Azure ad OAuth)](../active-directory/develop/index.yml), a logikai alkalmazás Azure-beli API Management való kimutatása, illetve a bejövő hívásokat kezdeményező IP-címek korlátozása: a [kérelmeken alapuló eseményindítók számára a biztonságos hozzáférés és az adatokhoz való hozzáférés](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

#### <a name="q-can-i-configure-callable-endpoints-further"></a>K: lehet, hogy a hívható végpontok továbbra is konfigurálhatók?

**A**: igen, a https-végpontok az [Azure API Management](../api-management/api-management-key-concepts.md)segítségével fejlettebb konfigurációt támogatnak. Ezzel a szolgáltatással következetesen kezelheti az összes API-t, beleértve a Logic apps-t, egyéni tartományneveket állíthat be, több hitelesítési módszert használhat, többek között például:

* [A kérelem módszerének módosítása](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [A kérelem URL-szegmensének módosítása](../api-management/api-management-transformation-policies.md#RewriteURL)
* A API Management-tartományok beállítása a [Azure Portal](https://portal.azure.com/)
* Házirend beállítása az egyszerű hitelesítés kereséséhez

## <a name="next-steps"></a>Következő lépések

* [Bejövő HTTPS-hívások fogadása és válaszadás a Azure Logic Apps használatával](../connectors/connectors-native-reqres.md)
* [Biztonságos hozzáférés és adatAzure Logic Apps-hozzáférés a kérelmeken alapuló eseményindítók bejövő hívásainak számára](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
