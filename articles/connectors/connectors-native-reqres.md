---
title: Hívások fogadása és válaszadás a HTTPS használatával
description: Külső szolgáltatásokból érkező bejövő HTTPS-kérések kezelése Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063012"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Bejövő HTTPS-kérések fogadása és válasza Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített kérelem-trigger és válasz művelettel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek HTTPS-en keresztül fogadhatnak bejövő kéréseket. A kimenő kérelmek küldéséhez használja a beépített [http-triggert vagy a http-műveletet](../connectors/connectors-native-http.md).

Használhatja például a logikai alkalmazást:

* Egy HTTPS-kérelem fogadása és megválaszolása egy helyszíni adatbázisban lévő adatszolgáltatáshoz.

* Munkafolyamat elindítása külső webhook-esemény bekövetkezésekor.

* Egy másik logikai alkalmazástól érkező HTTPS-hívás fogadása és megválaszolása.

Ez a cikk bemutatja, hogyan használható a kérelem-trigger és-válasz művelet, hogy a logikai alkalmazás fogadni tudjon és válaszoljon a bejövő hívásokra.

További információ a biztonságról: hitelesítés és titkosítás a logikai alkalmazásba érkező bejövő hívásokhoz, például [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL), [Azure Active Directory nyílt hitelesítés (Azure ad OAuth)](../active-directory/develop/index.yml), a logikai alkalmazás Azure-beli API Management való kimutatása, illetve a bejövő hívásokat kezdeményező IP-címek korlátozása: a [kérelmeken alapuló eseményindítók számára a biztonságos hozzáférés és az adatokhoz való hozzáférés](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md)?

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Kérelem-trigger hozzáadása

Ez a beépített trigger egy manuálisan hívható végpontot hoz létre, amely *csak* a bejövő kérelmeket képes kezelni HTTPS-en keresztül. Ha egy hívó kérelmet küld erre a végpontra, a [kérelem aktiválja](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) a logikai alkalmazást, és futtatja azt. További információ az trigger meghívásáról: [munkafolyamatok hívása, elindítása vagy beágyazása HTTPS-végpontokkal a Azure Logic Appsban](../logic-apps/logic-apps-http-endpoint.md).

A logikai alkalmazás csak [korlátozott ideig](../logic-apps/logic-apps-limits-and-config.md#http-limits)tart nyitva a bejövő kérelemben. Feltételezve, hogy a logikai alkalmazás [válaszra vonatkozó műveletet](#add-response)tartalmaz, ha a logikai alkalmazás nem küld vissza választ a hívónak az idő múlásával, a logikai alkalmazás egy `504 GATEWAY TIMEOUT` állapotot ad vissza a hívónak. Ha a logikai alkalmazás nem tartalmaz válasz műveletet, a logikai alkalmazás azonnal visszaadja a `202 ACCEPTED` hívónak az állapotot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. A Logic app Designer megnyitása után a keresőmezőbe írja be `http request` szűrőként a kifejezést. Az eseményindítók listából válassza ki a **http-kérelem fogadásának időpontját** .

   ![Kérelem triggerének kiválasztása](./media/connectors-native-reqres/select-request-trigger.png)

   A kérelem-trigger a következő tulajdonságokat jeleníti meg:

   ![Kérelem triggere](./media/connectors-native-reqres/request-trigger.png)

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL-CÍM** | nEz egy | Yes | A logikai alkalmazás mentése után generált végponti URL-cím, amely a logikai alkalmazás meghívására szolgál |
   | **Kérelem törzsének JSON-sémája** | `schema` | No | A bejövő kérelem törzsében található tulajdonságokat és értékeket leíró JSON-séma |
   |||||

1. A **kérelem törzse JSON-sémája** mezőben opcionálisan megadhat egy JSON-sémát, amely leírja a beérkező kérelem törzsét, például:

   ![Példa JSON-sémára](./media/connectors-native-reqres/provide-json-schema.png)

   A tervező ezt a sémát használja a kérelemben szereplő tulajdonságokhoz tartozó jogkivonatok létrehozásához. Ily módon a logikai alkalmazás elemezheti, felhasználhatja és átadhatja a kérelemből származó adatokkal a munkafolyamaton keresztüli triggert.

   Itt látható a minta séma:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Ha JSON-sémát ad meg, a tervező egy emlékeztetőt jelenít meg, amely tartalmazza a `Content-Type` fejlécet a kérelemben, és beállítja a fejléc értékét a következőre: `application/json` . További információ: [tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md).

   ![Emlékeztető a "Content-Type" fejléc belefoglalásához](./media/connectors-native-reqres/include-content-type.png)

   A fejléc a következőképpen néz ki, mint JSON formátumban:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   A várt adattartalom (adatok) alapján létrehozott JSON-séma létrehozásához használhat olyan eszközt, mint például a [JSONSchema.net](https://jsonschema.net), vagy a következő lépéseket teheti:

   1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

      ![Képernyőkép: "a minta hasznos adatok használata a séma létrehozásához" lehetőség van kiválasztva](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Adja meg a minta hasznos adatait, majd válassza a **kész** lehetőséget.

      ![Adja meg a minta hasznos adatait a séma létrehozásához](./media/connectors-native-reqres/enter-payload.png)

      Itt látható a minta hasznos adat:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Az alábbi lépéseket követve ellenőrizheti, hogy a bejövő hívás rendelkezik-e a megadott sémának megfelelő kérelem törzsével:

   1. A kérelem triggerének címsorában válassza az ellipszisek (**..**.) gombot.

   1. Az trigger beállításainak bekapcsolásához kapcsolja be a **séma érvényesítése** elemet, majd kattintson a **kész** gombra.

      Ha a bejövő hívás kérelmének törzse nem felel meg a sémának, az trigger hibát ad vissza `HTTP 400 Bad Request` .

1. További tulajdonságok megadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Metódus** | `method` | No | Az a módszer, amelyet a bejövő kérelemnek használnia kell a logikai alkalmazás meghívásához. |
   | **Relatív elérési út** | `relativePath` | No | Annak a paraméternek a relatív elérési útja, amelyet a logikai alkalmazás végpontjának URL-címe el tud fogadni |
   |||||

   Ez a példa hozzáadja a **Method** tulajdonságot:

   ![Metódus hozzáadása paraméter](./media/connectors-native-reqres/add-parameters.png)

   A **Method** tulajdonság megjelenik a triggerben, hogy kiválasszon egy metódust a listából.

   ![Módszer kiválasztása](./media/connectors-native-reqres/select-method.png)

1. Most adjon hozzá egy újabb műveletet a munkafolyamat következő lépéseként. Az trigger alatt válassza a **következő lépés** lehetőséget, hogy megtalálja a hozzáadni kívánt műveletet.

   Választhatja például a kérést [egy válasz művelet hozzáadásával](#add-response), amelyet egy testreszabott válasz visszaadására használhat, és a jelen témakör későbbi részében is ismertetjük.

   A logikai alkalmazás csak [korlátozott ideig](../logic-apps/logic-apps-limits-and-config.md#http-limits)tart nyitva a bejövő kérelemben. Feltételezve, hogy a logikai alkalmazás munkafolyamata tartalmaz egy választ, ha a logikai alkalmazás nem ad vissza választ az adott idő elteltével, a logikai alkalmazás a `504 GATEWAY TIMEOUT` hívót adja vissza. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válasz műveletet, a logikai alkalmazás azonnal visszaadja a `202 ACCEPTED` hívónak küldött választ.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

   Ez a lépés a logikai alkalmazást indító kérelem küldéséhez használandó URL-címet hozza létre. Az URL-cím másolásához válassza a másolás ikont az URL mellett.

   ![A logikai alkalmazás aktiválását használó URL-cím](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Ha a kivonatot vagy a font szimbólumot () az URI-ban szeretné felvenni a **#** kérelem-trigger hívása során, használja inkább a következő kódolt verziót: `%25%23`

1. A logikai alkalmazás teszteléséhez küldjön HTTP-kérést a generált URL-címre.

   Használhat például egy olyan eszközt, mint például a [Poster](https://www.getpostman.com/) a HTTP-kérelem elküldéséhez. Az trigger alapjául szolgáló JSON-definícióval és az trigger meghívásával kapcsolatos további információkért tekintse meg a következő témaköröket: [trigger típusának](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) és [hívásának, triggerének vagy beágyazásának munkafolyamatai http-végpontokkal Azure Logic apps](../logic-apps/logic-apps-http-endpoint.md).

További információ a biztonságról: hitelesítés és titkosítás a logikai alkalmazásba érkező bejövő hívásokhoz, például [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL), [Azure Active Directory nyílt hitelesítés (Azure ad OAuth)](../active-directory/develop/index.yml), a logikai alkalmazás Azure-beli API Management való kimutatása, illetve a bejövő hívásokat kezdeményező IP-címek korlátozása: a [kérelmeken alapuló eseményindítók számára a biztonságos hozzáférés és az adatokhoz való hozzáférés](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="trigger-outputs"></a>Trigger kimenetek

További információ a kérelmek trigger kimenetéről:

| JSON-tulajdonság neve | Adattípus | Leírás |
|--------------------|-----------|-------------|
| `headers` | Objektum | Egy JSON-objektum, amely leírja a kérelem fejléceit. |
| `body` | Objektum | Egy JSON-objektum, amely leírja a kérelem törzsének tartalmát |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Response művelet hozzáadása

Ha a kérelem triggert használja a bejövő kérések kezelésére, modellezheti a választ, és visszaküldheti a hasznos adatokat a hívónak a beépített [Válasz](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)használatával. A válasz művelet *csak* a kérelem triggerével használható. Ez a kombináció a kérelem-trigger és válasz művelettel hozza létre a [kérelem-válasz mintát](https://en.wikipedia.org/wiki/Request%E2%80%93response). A foreach hurkok, míg a hurkok és a párhuzamos ágak kivételével a munkafolyamat tetszőleges részén hozzáadhatja a válasz műveletet.

> [!IMPORTANT]
> Ha a Response művelet tartalmazza ezeket a fejléceket, Logic Apps eltávolítja ezeket a fejléceket a generált válaszüzenetből anélkül, hogy a rendszer figyelmeztetést vagy hibát mutat:
>
> * `Allow`
> * `Content-*` fejlécek, kivéve a `Content-Disposition` , `Content-Encoding` , és a `Content-Type` post és Put műveletek használatakor, de nem tartalmazza a Get műveletekhez
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Bár a Logic Apps nem fogja leállítani a logikai alkalmazások mentését, amelyekben a válasz művelettel rendelkezik, a Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

1. A Logic app Designerben abban a lépésben, amelyhez hozzá szeretne adni egy válasz műveletet, válassza az **új lépés** lehetőséget.

   Például a korábban megjelenő kérelem triggerének használatával:

   ![Új lépés hozzáadása](./media/connectors-native-reqres/add-response.png)

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen a keresőmezőbe írja be szűrőként a kifejezést, `response` majd válassza ki a **Válasz** műveletet.

   ![Válassza ki a válasz műveletet](./media/connectors-native-reqres/select-response-action.png)

   Ebben a példában az egyszerűség kedvéért a kérelem-trigger össze van csukva.

1. Adja meg a válaszüzenethez szükséges értékeket.

   Egyes mezőkben a szövegdobozokra kattintva megnyílik a dinamikus tartalmak listája. Ezután kiválaszthatja azokat a jogkivonatokat, amelyek a munkafolyamat előző lépéseiből származó elérhető kimeneteket jelölik. A korábbi példában megadott sémából származó tulajdonságok most megjelennek a dinamikus tartalmak listájában.

   Például a **fejlécek** mezőben adja `Content-Type` meg a kulcs nevét, és állítsa a kulcs értékét a `application/json` témakörben korábban említettek szerint. A **törzs** mezőben kiválaszthatja a dinamikus tartalom lista trigger törzsének kimenetét.

   ![Válasz művelet részletei](./media/connectors-native-reqres/response-details.png)

   Ha JSON formátumban szeretné megtekinteni a fejléceket, válassza a **váltás szöveges nézetre** lehetőséget.

   ![Fejlécek – váltás szöveges nézetre](./media/connectors-native-reqres/switch-to-text-view.png)

   A válasz műveletben megadható tulajdonságokkal kapcsolatos további információk.

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Állapotkód** | `statusCode` | Yes | A válaszban visszaadni kívánt állapotkód |
   | **Fejlécek** | `headers` | No | Egy JSON-objektum, amely egy vagy több, a válaszban szerepeltetni kívánt fejlécet ismertet. |
   | **Törzs** | `body` | No | A válasz törzse |
   |||||

1. Ha további tulajdonságokat szeretne megadni, például egy JSON-sémát a válasz törzséhez, nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Biztonságos hozzáférés és adathozzáférés a beérkező hívások számára a kérelmeken alapuló eseményindítók számára](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)
