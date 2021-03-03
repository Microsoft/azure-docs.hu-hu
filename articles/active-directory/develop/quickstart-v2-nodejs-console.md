---
title: 'Gyors útmutató: Microsoft Graph hívása egy Node.js Console-alkalmazásból | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a rövid útmutatóban egy olyan mintakód letöltését és futtatását mutatja be, amely bemutatja, hogyan szerezhet be egy Node.js Console-alkalmazás hozzáférési jogkivonatot, és hogyan hívhat meg egy Microsoft Identity platform-végpont által védett API-t az alkalmazás saját identitásával
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653745"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Gyors útmutató: token beszerzése és Microsoft Graph API meghívása egy Node.js Console-alkalmazásból az alkalmazás identitásával

Ebben a rövid útmutatóban egy olyan mintakód letöltését és futtatását mutatja be, amely bemutatja, hogyan szerezhet be egy Node.js Console-alkalmazás hozzáférési tokent az alkalmazás identitásával a Microsoft Graph API meghívásához és a címtárban lévő [felhasználók listájának](/graph/api/user-list) megjelenítéséhez. A kód minta azt mutatja be, hogy a felügyelet nélküli feladatok vagy a Windows-szolgáltatások alkalmazás-identitással futtathatók a felhasználó identitása helyett.

Ez a rövid útmutató a [Microsoft hitelesítési függvénytárát használja a Node.js (MSAL node) számára](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) az [ügyfél hitelesítő adatainak megadásakor](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>A minta alkalmazás regisztrálása és letöltése
>
> Az első lépésekhez kövesse az alábbi lépéseket.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>1. lépés: az alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `msal-node-cli` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.
> 1. Az **ügyfél** titkos kulcsa területen válassza az **új ügyfél titka** elemet, adjon meg egy nevet, majd válassza a **Hozzáadás** lehetőséget. A titkos értéket egy biztonságos helyen rögzítheti egy későbbi lépésben való használatra.
> 1. A **kezelés** területen válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**. Válassza a **Microsoft Graph** lehetőséget.
> 1. Válassza ki az **alkalmazás engedélyeit**.
> 1. A **felhasználó** csomópont alatt válassza a **felhasználó. olvasás. mind** lehetőséget, majd kattintson az **engedélyek hozzáadása** lehetőségre.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>A minta alkalmazás letöltése és konfigurálása
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1. lépés: az alkalmazás konfigurálása Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, létre kell hoznia egy ügyfél-titkos kulcsot, és hozzá kell adnia Graph API **felhasználóját. Read. All** Application engedély.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-nodejs-sample-project"></a>2. lépés: a Node.js minta projekt letöltése

> [!div renderon="docs"]
> [A mintakód letöltése](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>3. lépés: a Node.js minta projekt konfigurálása
>
> 1. Bontsa ki a zip-fájlt egy helyi mappába a lemez gyökeréhez (például *C:/Azure-Samples*).
> 1. Szerkessze a *. env* -t, és cserélje le a mezők `TENANT_ID` , `CLIENT_ID` és a `CLIENT_SECRET` következő kódrészlet értékét:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Ahol:
>    - `Enter_the_Application_Id_Here` – a korábban regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** . Keresse meg ezt az azonosítót az Azure Portal az alkalmazás regisztrációjának **Áttekintés** paneljén.
>    - `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).  Ezeket az értékeket a Azure Portal az alkalmazás regisztrációjának **Áttekintés** paneljén találja meg.
>    - `Enter_the_Client_Secret_Here` – cserélje le ezt az értéket a korábban létrehozott ügyfél-titkos kulcsra. Új kulcs létrehozásához használja a **tanúsítványok & Secrets** lehetőséget az Azure Portal alkalmazás regisztrációs beállításaiban.
>
> > [!WARNING]
> > A forráskódban található bármilyen szöveges titok nagyobb biztonsági kockázatot jelent. Ez a cikk egyszerű szöveges ügyfél-titkos kulcsot használ az egyszerűség kedvéért. [Hitelesítő adatok](active-directory-certificate-credentials.md) használata a bizalmas ügyfélalkalmazások ügyfeleinek titkos kulcsa helyett, különösen azokat az alkalmazásokat, amelyeket az éles környezetben kíván üzembe helyezni.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: rendszergazdai engedély

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: rendszergazdai engedély

Ha ezen a ponton próbálja meg futtatni az alkalmazást, *HTTP 403-Tiltott* hibaüzenetet kap: `Insufficient privileges to complete the operation` . Ez a hiba azért fordul elő, mert bármely *csak alkalmazásra vonatkozó engedélyhez* **rendszergazdai hozzájárulás** szükséges: a címtár globális rendszergazdájának hozzájárulást kell adnia az alkalmazáshoz. Válassza ki az alábbi lehetőségek egyikét a szerepkörtől függően:

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Ön globális bérlői rendszergazda, nyissa meg az **API-engedélyek** lapot a Azure Portal alkalmazás-regisztrációjában, és válassza a rendszergazdai jóváhagyás megadása a (z) **{bérlő neve} számára** lehetőséget (ahol a (z) {bérlő neve} a címtár neve).

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, lépjen az API- **engedélyek** oldalra, és válassza **a rendszergazdai jóváhagyás megadása Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek oldalra]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő általános jogú felhasználója, kérje meg a globális rendszergazdát, hogy adja meg az alkalmazáshoz tartozó **rendszergazdai jóváhagyást** . Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Ahol:
>> * `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: az alkalmazás futtatása

Keresse meg a minta gyökérkönyvtárát (ahol `package.json` található) a parancssorban vagy a konzolon. A minta függőségeit egyszer kell telepítenie:

```console
npm install
```

Ezután futtassa az alkalmazást a parancssorból vagy a konzolon keresztül:

```console
node . --op getUsers
```

Az Azure AD-címtárban lévő felhasználók listáját jelképező JSON-kódrészletnek a konzolon kell megjelennie.

## <a name="about-the-code"></a>A kód ismertetése

Az alábbiakban a minta alkalmazás néhány fontos aspektusát tárgyaljuk.

### <a name="msal-node"></a>MSAL csomópont

A [MSAL csomópont](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. A leírtak szerint ez a rövid útmutató a delegált engedélyek helyett alkalmazás-engedélyek alapján kéri le a jogkivonatokat (az alkalmazás saját identitásának használatával). Az ebben az esetben használt hitelesítési folyamat [OAuth 2,0 ügyfél-hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md). A MSAL-csomópont Daemon-alkalmazásokkal való használatáról további információt a [forgatókönyv: Daemon Application](scenario-daemon-overview.md)című témakörben talál.

 A MSAL csomópontot a következő NPM-parancs futtatásával telepítheti.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```javascript
const msal = require('@azure/msal-node');
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Ahol: |Leírás |
> |---------|---------|
> | `clientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `authority`    | A felhasználó által hitelesítendő STS-végpont. A nyilvános felhő esetében általában a (z) `https://login.microsoftonline.com/{tenant}` {bérlő} a bérlő vagy a bérlői azonosító neve.|
> | `clientSecret` | Az Azure Portalon az alkalmazáshoz létrehozott ügyfél-titkos kulcs. |

További információkért tekintse [meg `ConfidentialClientApplication` ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) a következő dokumentációt:

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Ha a tokent az alkalmazás identitásával szeretné kérelmezni, használja a következő `acquireTokenByClientCredential` metódust:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Ahol:| Leírás |
> |---------|---------|
> | `tokenRequest` | A kért hatóköröket tartalmazza. A bizalmas ügyfelek esetében a hasonló formátumot kell használnia, hogy `{Application ID URI}/.default` jelezze, hogy a kért hatókörök az Azure Portalon beállított app Object (Microsoft Graph, `{Application ID URI}` pont –) számára statikusan meghatározottak `https://graph.microsoft.com` . Az egyéni webes API- `{Application ID URI}` k esetében az Azure Portal alkalmazás-regisztrációjában az **API közzététele** című szakaszban van definiálva. |
> | `tokenResponse` | A válasz tartalmaz egy hozzáférési jogkivonatot a kért hatókörökhöz. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Daemon/Console alkalmazás fejlesztéséről az MSAL csomóponttal, tekintse meg az oktatóanyagot:

> [!div class="nextstepaction"]
> [Webes API-kat meghívó Daemon-alkalmazás](tutorial-v2-nodejs-console.md)