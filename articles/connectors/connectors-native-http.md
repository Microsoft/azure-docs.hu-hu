---
title: Szolgáltatási végpontok hívása HTTP vagy HTTPS használatával
description: Kimenő HTTP-vagy HTTPS-kérelmek küldése a szolgáltatási végpontoknak Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/18/2021
tags: connectors
ms.openlocfilehash: dab5b755347e46d8d509e8014bba8f496ca9c900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719440"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Szolgáltatásvégpontok HTTP-n vagy HTTPS-en keresztül történő meghívása az Azure Logic Appsből

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http-trigger vagy művelet használatával olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek a kimenő kérelmeket más szolgáltatásokban és rendszerekben, HTTP-n vagy HTTPS-en keresztül küldhetik a végpontoknak. Ha ehelyett a bejövő HTTPS-hívásokat szeretné fogadni és válaszolni, használja a beépített [kérelem-triggert és a válasz műveletet](../connectors/connectors-native-reqres.md).

Például figyelheti a webhelyhez tartozó szolgáltatási végpontot, ha ellenőrzi, hogy a végpont egy adott időpontban van-e. Ha a megadott esemény a végponton történik, például a webhely leállásakor, az esemény elindítja a logikai alkalmazás munkafolyamatát, és futtatja az adott munkafolyamat műveleteit.

* Ha ismétlődő ütemterv alapján szeretné megtekinteni vagy *lekérdezni* egy végpontot, [adja hozzá a http-triggert](#http-trigger) a munkafolyamat első lépéseként. Minden alkalommal, amikor a trigger ellenőrzi a végpontot, az trigger meghívja vagy elküld egy *kérést* a végpontnak. A végpont válasza határozza meg, hogy a logikai alkalmazás munkafolyamata fut-e. Az trigger bármilyen tartalmat továbbít a végpont válaszában a logikai alkalmazás műveleteire.

* Ha egy végpontot szeretne meghívni a munkafolyamatban bárhol máshol, [adja hozzá a http-műveletet](#http-action). A végpont válasza határozza meg, hogyan futnak a munkafolyamat hátralévő műveletei.

Ez a cikk bemutatja, hogyan használhatja a HTTP-triggert és a HTTP-műveletet úgy, hogy a logikai alkalmazás kimenő hívásokat küldjön más szolgáltatásoknak és rendszereknek.

További információ a logikai alkalmazásból kimenő hívások titkosításáról, biztonságáról és engedélyezéséről, például [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL), önaláírt tanúsítvány vagy [Azure Active Directory nyílt hitelesítés (Azure ad OAuth)](../active-directory/develop/index.yml): [biztonságos hozzáférés és adathozzáférés a kimenő hívások számára más szolgáltatásokhoz és rendszerekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A hívni kívánt cél végpont URL-címe

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md)?

* Az a logikai alkalmazás, amelyről meg szeretné hívni a célként megadott végpontot. A HTTP-trigger elindításához [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP-trigger hozzáadása

Ez a beépített trigger egy HTTP-hívást kezdeményez a végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervező keresési mezőjében válassza a **beépített** lehetőséget. A keresőmezőbe írja be `http` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki a **http** -eseményindítót.

   ![HTTP-trigger kiválasztása](./media/connectors-native-http/select-http-trigger.png)

   Ez a példa átnevezi a triggert a "HTTP trigger" névre, hogy a lépésnek legyen egy leíró neve. A példa később egy HTTP-műveletet is felvesz, és mindkét névnek egyedinek kell lennie.

1. Adja meg a http- [trigger azon paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) értékeit, amelyeket a célként megadott végpont felé irányuló hívásba kíván foglalni. Állítsa be az ismétlődést, hogy milyen gyakran szeretné, hogy az eseményindító ellenőrizzék a célként megadott végpontot.

   ![HTTP-trigger paramétereinek megadása](./media/connectors-native-http/http-trigger-parameters.png)

   Ha a **none** értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

   * [Hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Az erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP-művelet hozzáadása

Ez a beépített művelet HTTP-hívást kezdeményez egy végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP-triggert használja első lépésként.

1. Válassza ki azt a lépést, ahol a HTTP-műveletet hozzá szeretné adni, majd kattintson az **új lépés** gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be `http` szűrőként a kifejezést. A **műveletek** listából válassza ki a **http** -műveletet.

   ![HTTP-művelet kiválasztása](./media/connectors-native-http/select-http-action.png)

   Ez a példa átnevezi a műveletet "HTTP-művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg azoknak a [http-műveleti paramétereknek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) az értékeit, amelyeket fel szeretne venni a célként megadott végpontba.

   ![HTTP-műveleti paraméterek megadása](./media/connectors-native-http/http-action-parameters.png)

   Ha a **none** értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

   * [Hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Az erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

## <a name="trigger-and-action-outputs"></a>Trigger-és műveleti kimenetek

Itt talál további információt a HTTP-triggerből vagy-műveletből származó kimenetekről, ami visszaadja ezt az információt:

| Tulajdonság | Típus | Leírás |
|----------|------|-------------|
| `headers` | JSON-objektum | A kérelemben szereplő fejlécek |
| `body` | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| `status code` | Egész szám | A kérelemben szereplő állapotkód |
|||

| Állapotkód | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérelem |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="content-with-multipartform-data-type"></a>Tartalom multipart/form-adattípus

A `multipart/form-data` http-kérelmekben szereplő tartalom kezeléséhez hozzáadhat egy JSON-objektumot, amely tartalmazza a `$content-type` http- `$multipart` kérelem törzsének és attribútumainak a formátum használatával történő kezelését.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Tegyük fel például, hogy rendelkezik egy olyan logikai alkalmazással, amely egy, az adott hely API-ját támogató HTTP POST-kérelmet küld egy webhelyre, amely támogatja a `multipart/form-data` típust. A művelet a következő módon jelenhet meg:

![Többrészes űrlapadatok](./media/connectors-native-http/http-action-multipart.png)

Az alábbi példa a HTTP-művelet JSON-definícióját mutatja be az alapul szolgáló munkafolyamat-definícióban:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Tartalom az Application/x-www-Form-urlencoded típussal

Ahhoz, hogy urlencoded adatokat szolgáltasson a törzsben egy HTTP-kérelemhez, meg kell adnia, hogy az adatokat a tartalom típusa tartalmazza-e `application/x-www-form-urlencoded` . A HTTP-trigger vagy művelet területen adja hozzá a `content-type` fejlécet. Állítsa be a fejléc értékét a következőre: `application/x-www-form-urlencoded` .

Tegyük fel például, hogy rendelkezik egy logikai alkalmazással, amely HTTP POST-kérést küld egy webhelyre, amely támogatja a `application/x-www-form-urlencoded` típust. A művelet a következő módon jelenhet meg:

![Képernyőkép: "Content-Type" fejlécet tartalmazó HTTP-kérelem, amely az "application/x-www-Form-urlencoded" értékre van beállítva.](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Aszinkron kérelem – válasz viselkedése

Alapértelmezés szerint a Azure Logic Apps összes HTTP-alapú művelete követi a normál [aszinkron műveleti mintát](/azure/architecture/patterns/async-request-reply). Ez a minta azt adja meg, hogy egy HTTP-művelet vagy egy, a végpontra, szolgáltatásra, rendszerre vagy API-ra irányuló kérés küldése után a fogadó azonnal ["202 elfogadott"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) választ ad vissza. Ez a kód megerősíti, hogy a fogadó elfogadta a kérést, de nem fejezte be a feldolgozást. A válasz tartalmazhat egy `location` fejlécet, amely meghatározza az URL-címet és a frissítési azonosítót, amelyet a hívó használhat az aszinkron kérelem állapotának lekéréséhez vagy a fogadó leállításához, illetve az ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) sikerességi válasz vagy más nem 202 válasz visszaadását. A hívónak azonban nem kell megvárnia, amíg a kérés befejezte a feldolgozást, és továbbra is futtathatja a következő műveletet. További információ: az [aszinkron szolgáltatások integrációja a szolgáltatásbeli önállóságot kényszeríti](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* A Logic app Designerben a HTTP-művelet, de az trigger nem, **aszinkron mintázatú** beállítással rendelkezik, amely alapértelmezés szerint engedélyezve van. Ezzel a beállítással megadhatja, hogy a hívó ne várjon a feldolgozás befejezésére, és továbblép a következő műveletre, de továbbra is ellenőrzi az állapotot, amíg a feldolgozás leáll. Ha le van tiltva, ez a beállítás azt határozza meg, hogy a hívó a következő műveletre való áttérés előtt megvárja a feldolgozás befejeződését.

  A beállítás megkereséséhez kövesse az alábbi lépéseket:

  1. A HTTP-művelet címsorán válassza az ellipszisek (**..**.) gombot, amely megnyitja a művelet beállításait.

  1. Az **aszinkron minta** beállításának megkeresése.

     !["Aszinkron minta" beállítás](./media/connectors-native-http/asynchronous-pattern-setting.png)

* A HTTP-művelet alapjául szolgáló JavaScript Object Notation (JSON) definíciója implicit módon követi az aszinkron műveleti mintát.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Aszinkron műveletek letiltása

Időnként előfordulhat, hogy bizonyos helyzetekben a HTTP-művelet aszinkron viselkedését szeretné használni, például a következőket:

* [A hosszú ideig futó feladatok HTTP-időtúllépésének elkerülése](#avoid-http-timeouts)
* [Helyadatok ellenőrzésének letiltása](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>**Aszinkron mintázat** beállításának kikapcsolása

1. A Logic app Designerben a HTTP-művelet címsorán válassza az ellipszisek (**..**.) gombot, amely megnyitja a művelet beállításait.

1. Keresse meg az **aszinkron minta** beállítást, kapcsolja **ki** a beállítást, ha engedélyezve van, és válassza a **kész** lehetőséget.

   ![Az "aszinkron minta" beállítás letiltása](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Aszinkron minta letiltása a művelet JSON-definíciójában

A HTTP-művelet alapjául szolgáló JSON-definícióban [adja hozzá a `"DisableAsyncPattern"` művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) definícióját a művelethez, hogy a művelet a szinkron műveleti mintát követi. További információ: [műveletek futtatása szinkron műveleti mintában](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>A hosszú ideig futó feladatok HTTP-időtúllépésének elkerülése

A HTTP-kérelmek [időtúllépési korláttal](../logic-apps/logic-apps-limits-and-config.md#http-limits)rendelkeznek. Ha olyan hosszan futó HTTP-művelettel rendelkezik, amely túllépi a korlátot, a következő lehetőségek közül választhat:

* [Tiltsa le a http-művelet aszinkron műveleti mintáját](#disable-asynchronous-operations) , így a művelet nem kérdezi le folyamatosan a lekérdezést, vagy nem vizsgálja meg a kérelem állapotát. Ehelyett a művelet megvárja, amíg a fogadó válaszol az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

* Cserélje le a HTTP-műveletet a [http webhook műveletre](../connectors/connectors-native-webhook.md), amely arra vár, hogy a fogadó válaszoljon az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Helyadatok ellenőrzésének letiltása

Egyes végpontok, szolgáltatások, rendszerek vagy API-k "202 elfogadott" választ adnak vissza, amely nem tartalmaz `location` fejlécet. Ha el szeretné kerülni, hogy a HTTP-műveletek folyamatosan ellenőrizzék a kérés állapotát, ha a `location` fejléc nem létezik, a következő lehetőségek közül választhat:

* [Tiltsa le a http-művelet aszinkron műveleti mintáját](#disable-asynchronous-operations) , így a művelet nem kérdezi le folyamatosan a lekérdezést, vagy nem vizsgálja meg a kérelem állapotát. Ehelyett a művelet megvárja, amíg a fogadó válaszol az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

* Cserélje le a HTTP-műveletet a [http webhook műveletre](../connectors/connectors-native-webhook.md), amely arra vár, hogy a fogadó válaszoljon az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

## <a name="known-issues"></a>Ismert problémák

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Kihagyott HTTP-fejlécek

Ha egy HTTP-trigger vagy-művelet tartalmazza ezeket a fejléceket, Logic Apps eltávolítja ezeket a fejléceket a generált kérési üzenetből a figyelmeztetés vagy a hiba megjelenítése nélkül:

* `Accept-*` fejlécek, kivéve a következőt: `Accept-version`
* `Allow`
* `Content-*` a,, és rendszerű fejlécek, `Content-Disposition` `Content-Encoding` `Content-Type` amelyeket a post és Put műveletek használatakor tiszteletben tartanak. A lekérési művelet használatakor azonban Logic Apps eldobja ezeket a fejléceket.
* `Cookie` fejlécet, de Logic Apps a **cookie** tulajdonsággal megadott értéket.
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Bár a Logic Apps nem állítja le a HTTP-triggert vagy műveletet használó logikai alkalmazások mentését ezekkel a fejlécekkel, Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

## <a name="connector-reference"></a>Összekötő-referencia

Az trigger-és műveleti paraméterekkel kapcsolatos további információkért tekintse meg a következő részeket:

* [HTTP-trigger paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-műveleti paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>Következő lépések

* [Biztonságos hozzáférés és adathozzáférés a kimenő hívások más szolgáltatásokhoz és rendszerekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)
