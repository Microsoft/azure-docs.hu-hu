---
title: API-összekötők hozzáadása felhasználói folyamatokhoz (előzetes verzió)
description: A felhasználói folyamatokban használandó API-összekötő konfigurálása.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: b63db3d02b471a577586ecd54f56caa59af504d6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805512"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>API-összekötő hozzáadása egy regisztrációs felhasználói folyamathoz (előzetes verzió)

> [!IMPORTANT]
> A regisztrációhoz használható API-összekötők a Azure AD B2C nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az API- [Összekötők](api-connectors-overview.md)használatához először létre kell hoznia az API-összekötőt, majd engedélyeznie kell azt egy felhasználói folyamaton.

## <a name="create-an-api-connector"></a>API-összekötő létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget.
4. Válassza az **API-összekötők (előzetes verzió)** lehetőséget, majd válassza az **új API-összekötő** lehetőséget.

   ![Új API-összekötő hozzáadása](./media/add-api-connector/api-connector-new.png)

5. Adja meg a hívás megjelenítendő nevét. Például **érvényesítse a felhasználói adatokat**.
6. Adja meg az API **-hívás végpontjának URL-címét** .
7. Adja meg az API hitelesítési adatait.

   - Jelenleg csak az alapszintű hitelesítés támogatott. Ha alapszintű hitelesítés nélküli API-t szeretne használni fejlesztési célokra, egyszerűen adja meg az API által figyelmen kívül hagyható "dummy" **felhasználónevet** és **jelszót** . Ha egy API-kulccsal rendelkező Azure-függvényt használ, a kódot lekérdezési paraméterként is megadhatja a **végpont URL-címében** (például: `https://contoso.azurewebsites.net/api/endpoint?code=0123456789` ).

   ![Új API-összekötő konfigurálása](./media/add-api-connector/api-connector-config.png)
8. Kattintson a **Mentés** gombra.

## <a name="the-request-sent-to-your-api"></a>Az API-nak továbbított kérelem
Az API-összekötők **http post** -kérelemként valósulnak meg, felhasználói attribútumok ("jogcímek") küldésével kulcs-érték párokként egy JSON-törzsben. Az attribútumok a [Microsoft Graph](/graph/api/resources/user#properties) felhasználó tulajdonságaihoz hasonlóan lesznek szerializálva. 

**Példakérelem**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

A kérelemben csak a felhasználói tulajdonságok és az **Azure ad B2C**  >  **felhasználói attribútumok** című részben felsorolt egyéni attribútumok küldhetők el.

Az egyéni attribútumok a címtár **extension_ \<extensions-app-id> _CustomAttribute**  formátumában találhatók. Az API-nak meg kell várnia, hogy a jogcímeket ugyanabban a szerializált formátumban fogadja. Az egyéni attribútumokkal kapcsolatos további információkért lásd: [Egyéni attribútumok definiálása a Azure Active Directory B2Cban](user-flow-custom-attributes.md).

Emellett a **felhasználói felület területi beállítása ("ui_locales")** jogcímet alapértelmezés szerint minden kérelemben elküldjük. A felhasználó az eszközön konfigurált területi beállításokat biztosít, amelyeket az API használhat a nemzetközi válaszok visszaküldéséhez.

> [!IMPORTANT]
> Ha egy jogcím nem rendelkezik értékkel az API-végpont meghívásakor, a rendszer nem küldi el a jogcímet az API-nak. Az API-t úgy kell kialakítani, hogy explicit módon ellenőrizzék és kezeljék azt az esetet, amikor a kérelem nem szerepel a kérésben.

> [!TIP] 
> az API- [**k az identitások ("identitások")**](/graph/api/resources/objectidentity) és az **e-mail-cím ("e-mail")** jogcímek használatával azonosíthatják a felhasználókat, mielőtt a bérlőben fiókkal rendelkeznek. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>API-összekötő engedélyezése felhasználói folyamatokban

Az alábbi lépéseket követve hozzáadhat egy API-összekötőt egy regisztrációs felhasználói folyamathoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget.
4. Válassza a **felhasználói folyamatok** lehetőséget, majd válassza ki azt a felhasználói folyamatot, amelyhez hozzá kívánja adni az API-összekötőt.
5. Válassza az **API-összekötők** lehetőséget, majd válassza ki azokat az API-végpontokat, amelyeket a felhasználói folyamat következő lépéseiben szeretne meghívni:

   - **Az identitás-szolgáltatóval való bejelentkezés után**
   - **A felhasználó létrehozása előtt**

   ![API-k hozzáadása a felhasználói folyamathoz](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Kattintson a **Mentés** gombra.

## <a name="after-signing-in-with-an-identity-provider"></a>Az identitás-szolgáltatóval való bejelentkezés után

A regisztrálási folyamat ezen lépésében szereplő API-összekötőt azonnal meghívja a rendszer, miután a felhasználó hitelesítve lett az identitás-szolgáltatónál (például Google, Facebook, & Azure AD). Ez a lépés megelőzi az ***attribútum-gyűjtemény lapot***, amely a felhasználó számára a felhasználói attribútumok összegyűjtésére bemutatott űrlap. Ez a lépés nem kerül meghívásra, ha egy felhasználó helyi fiókkal van regisztrálva.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Példa erre a lépésre az API-nak küldendő kérelem
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Az API-nak küldött pontos jogcímek attól függnek, hogy az identitás-szolgáltató milyen információkat biztosít. az "e-mail" küldése mindig elküldve.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Várt válaszok a webes API-ból ebben a lépésben

Ha a webes API egy felhasználói folyamat során HTTP-kérést kap az Azure AD-től, a következő válaszokat adhatja vissza:

- Folytatási válasz
- Blokkoló válasz

#### <a name="continuation-response"></a>Folytatási válasz

A folytatólagos válasz azt jelzi, hogy a felhasználói folyamatnak továbbra is a következő lépésnek kell megjelennie: az attribútum-gyűjtemény lap.

A folytatási válaszban az API jogcímeket adhat vissza. Ha az API egy jogcímet ad vissza, a jogcím a következő műveleteket végzi el:

- Előzetesen kitölti a beviteli mezőt az attribútumok gyűjteménye lapon.

Tekintse meg a [folytatásra adott](#example-of-a-continuation-response)példát.

#### <a name="blocking-response"></a>Blokkoló válasz

A blokkolási válasz kilép a felhasználói folyamatból. Az API szándékosan kiállíthatja a felhasználói folyamat folytatását úgy, hogy egy blokk lapot jelenít meg a felhasználónak. A blokk oldal megjeleníti az `userMessage` API által biztosított értéket.

A [blokkolási válasz](#example-of-a-blocking-response)példáját lásd:.

## <a name="before-creating-the-user"></a>A felhasználó létrehozása előtt

A regisztrációs folyamat ezen lépésében található API-összekötőt a rendszer az attribútum-gyűjtemény lap után hívja meg, ha van ilyen. Ezt a lépést mindig a rendszer a felhasználói fiók létrehozása előtt hívja meg.

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Példa erre a lépésre az API-nak küldendő kérelem

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Az API-nak küldött pontos jogcímek attól függnek, hogy milyen adatokat gyűjt a rendszer a felhasználótól, vagy amelyet az identitás szolgáltatója biztosít.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Várt válaszok a webes API-ból ebben a lépésben

Ha a webes API egy felhasználói folyamat során HTTP-kérést kap az Azure AD-től, a következő válaszokat adhatja vissza:

- Folytatási válasz
- Blokkoló válasz
- Érvényesítési válasz

#### <a name="continuation-response"></a>Folytatási válasz

A folytatólagos válasz azt jelzi, hogy a felhasználói folyamatnak továbbra is a következő lépéssel kell megjelennie: hozza létre a felhasználót a címtárban.

A folytatási válaszban az API jogcímeket adhat vissza. Ha az API egy jogcímet ad vissza, a jogcím a következő műveleteket végzi el:

- Felülbírál minden olyan értéket, amely már hozzá van rendelve a jogcímhez az attribútum gyűjteménye lapon.

Tekintse meg a [folytatásra adott](#example-of-a-continuation-response)példát.

#### <a name="blocking-response"></a>Blokkoló válasz
A blokkolási válasz kilép a felhasználói folyamatból. Az API szándékosan kiállíthatja a felhasználói folyamat folytatását úgy, hogy egy blokk lapot jelenít meg a felhasználónak. A blokk oldal megjeleníti az `userMessage` API által biztosított értéket.

A [blokkolási válasz](#example-of-a-blocking-response)példáját lásd:.

### <a name="validation-error-response"></a>Ellenőrzés – hiba válasza
 Ha az API egy érvényesítési-hibaüzenettel válaszol, a felhasználói folyamat az attribútum-gyűjtemény oldalon marad, a `userMessage` pedig megjelenik a felhasználó számára. A felhasználó Ezután szerkesztheti és újra elküldheti az űrlapot. Ezt a típusú választ lehet használni a bemeneti ellenőrzéshez.

Tekintse meg az [érvényesítési hiba](#example-of-a-validation-error-response)példáját.

## <a name="example-responses"></a>Válaszok – példa

### <a name="example-of-a-continuation-response"></a>Folytatási válasz – példa

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Paraméter                                          | Típus              | Kötelező | Leírás                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | Sztring            | Yes      | Az API verziója.                                                                                                                                                                                                                                                                |
| művelet                                             | Sztring            | Yes      | Az értéknek a számnak kell lennie `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | A visszaadott értékek felülírhatják a felhasználó által összegyűjtött értékeket. A tokenben is visszaadhatók, ha **alkalmazási jogcímként** van kiválasztva.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | A jogcímnek nem kell tartalmaznia `_<extensions-app-id>_` . A visszaadott értékek felülírhatják a felhasználó által összegyűjtött értékeket. A tokenben is visszaadhatók, ha **alkalmazási jogcímként** van kiválasztva.  |

### <a name="example-of-a-blocking-response"></a>Blokkoló válasz – példa

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Paraméter   | Típus   | Kötelező | Leírás                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Sztring | Yes      | Az API verziója.                                                    |
| művelet      | Sztring | Yes      | Az értéknek szerepelnie kell `ShowBlockPage`                                              |
| userMessage | Sztring | Yes      | A felhasználónak megjelenítendő üzenet.                                            |

**Végfelhasználói élmény blokkoló válaszokkal**

![Példa blokk oldalra](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Érvényesítési hiba – példa



```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Paraméter   | Típus    | Kötelező | Leírás                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Sztring  | Yes      | Az API verziója.                                                    |
| művelet      | Sztring  | Yes      | Az értéknek a számnak kell lennie `ValidationError` .                                           |
| status      | Egész szám | Yes      | ValidationError-válasz értékének kell lennie `400` .                        |
| userMessage | Sztring  | Yes      | A felhasználónak megjelenítendő üzenet.                                            |

> [!NOTE]
> A válasz törzsében szereplő "status" érték mellett a HTTP-állapotkód "400" értékűnek kell lennie.

**Végfelhasználói élmény érvényesítéssel – hiba-válasz**

![Példa érvényesítési oldal](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Ajánlott eljárások és hibakeresés

### <a name="using-serverless-cloud-functions"></a>Kiszolgáló nélküli Felhőbeli függvények használata
A kiszolgáló nélküli függvények, például a Azure Functions HTTP-eseményindítók egyszerű módszert biztosítanak az API-összekötővel használható API-végpontok létrehozásához. Használhatja a kiszolgáló nélküli felhő függvényt, [például](code-samples.md#api-connectors)az érvényesítési logikát, és korlátozhatja a bejelentkezéseket adott e-mail tartományokra. A kiszolgáló nélküli felhő funkció összetettebb forgatókönyvekhez is meghívhat és meghívhat más webes API-kat, felhasználói áruházakat és egyéb felhőalapú szolgáltatásokat.

### <a name="best-practices"></a>Ajánlott eljárások
Győződjön meg a következőket:
* Az API-t az API-kérelem és a válasz-szerződések követik, a fentiekben ismertetett módon. 
* Az API **-összekötő végponti URL-** címe a megfelelő API-végpontra mutat.
* Az API explicit módon ellenőrzi a fogadott jogcímek null értékeit.
* Az API a lehető leggyorsabban reagál a folyadékok felhasználói élményének biztosítására.
    * Ha kiszolgáló nélküli függvényt vagy méretezhető webszolgáltatást használ, használjon olyan üzemeltetési tervet, amely az "ébren" vagy "Warm" API-t tartja. éles környezetben. Azure Functions esetén a [Prémium csomag](../azure-functions/functions-scale.md) használata ajánlott


### <a name="use-logging"></a>Naplózás használata
Általánosságban elmondható, hogy a webes API szolgáltatás által engedélyezett naplózási eszközöket használja, például az [Application bepillantást](../azure-functions/functions-monitoring.md), hogy FIGYELJE az API-t váratlan hibakódok, kivételek és gyenge teljesítmény érdekében.
* Olyan HTTP-állapotkódok figyelése, amelyek nem HTTP 200 vagy 400.
* A 401 vagy 403 HTTP-állapotkód általában azt jelzi, hogy probléma van a hitelesítéssel. Ellenőrizze az API hitelesítési rétegét és az API-összekötő megfelelő konfigurációját.
* Ha szükséges, használjon agresszívebb naplózási szintet (például "trace" vagy "debug") a fejlesztésben.
* Az API figyelése hosszú válaszidő esetén.

## <a name="next-steps"></a>Következő lépések
<!-- - Learn how to [add a custom approval workflow to sign-up](add-approvals.md) -->
- Ismerkedjen meg a [mintákkal](code-samples.md#api-connectors).
