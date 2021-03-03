---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Arkóz Labs használatával
titleSuffix: Azure AD B2C
description: Oktatóanyag a Azure Active Directory B2C a Arkóz Labs szolgáltatással való konfigurálásához a kockázatos és a csalárd felhasználók azonosításához
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 04492abc0f235c2dc6139adbe543bcce82f7f7b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646861"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Oktatóanyag: a Arkóz Labs konfigurálása Azure Active Directory B2C

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure Active Directory (AD) B2C-hitelesítését a [Arkóz Labs szolgáltatással](https://www.arkoselabs.com/). A Arkóz Labs segítséget nyújt a szervezeteknek a robotok elleni támadásokkal, a fiókok átvételi támadásokkal és a csalárd fiókok megnyitásával kapcsolatban.  

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

- Egy [Arkóz Labs](https://www.arkoselabs.com/book-a-demo/) -fiók.

## <a name="scenario-description"></a>Forgatókönyv leírása

A Arkóz Labs-integráció a következő összetevőket tartalmazza:

- **Arkóz Labs** – csalási és visszaélési szolgáltatás a robotok és más automatizált visszaélések elleni védelemhez.

- **Azure ad B2C regisztrációs felhasználói folyamat** – a Arkóz Labs szolgáltatást használó regisztrációs élmény. Az egyéni HTML-és JavaScript-és API-összekötőket fogja használni az Arkóz Labs szolgáltatással való integrációhoz.

- **Azure functions** – az API-összekötők szolgáltatással működtetett API-végpont. Ez az API felelős a Arkóz Labs munkamenet-token kiszolgálóoldali ellenőrzésének végrehajtásához.

Az alábbi ábra azt ismerteti, hogyan integrálható a Arkóz Labs Azure AD B2C.

![A kép a Arkóz Labs architektúra diagramját mutatja](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Lépés  | Leírás |
|---|---|
|1     | A felhasználó feliratkozik, és létrehoz egy fiókot. Amikor a felhasználó kiválasztja a Submit (küldés) lehetőséget, megjelenik egy Arkóz Labs-kényszerítési kérdés.         |
|2     |  Miután a felhasználó befejezte a problémát, Azure AD B2C elküldi az állapotot a Arkóz Labs számára a jogkivonat létrehozásához. |
|3     |  A Arkóz Labs létrehoz egy jogkivonatot, és visszaküldi a Azure AD B2Cnak.   |
|4     |  Azure AD B2C egy közbenső webes API meghívásával továbbítja a regisztrációs űrlapot.      |
|5     |  A köztes webes API a regisztrációs űrlapot küldi el a Arkóz Lab számára a jogkivonat-ellenőrzéshez.    |
|6     | A Arkóz labor feldolgozza és visszaküldi az ellenőrzési eredményeket a közbenső webes API-nak.|
|7     | A közbenső webes API elküldi a probléma sikerességét vagy sikertelenségét a Azure AD B2C. |
|8     | Ha a probléma sikeresen befejeződött, a rendszer elküld egy regisztrációs űrlapot Azure AD B2Cba, és Azure AD B2C befejezi a hitelesítést.|

## <a name="onboard-with-arkose-labs"></a>A Arkóz Labs üzembe helyezése

1. Forduljon a [Arkóz](https://www.arkoselabs.com/book-a-demo/) -hoz, és hozzon létre egy fiókot.

2. A fiók létrehozása után navigáljon a következőre: https://dashboard.arkoselabs.com/login  

3. Az irányítópulton navigáljon a hely beállításaihoz, és keresse meg a nyilvános kulcsot és a titkos kulcsot. Erre az információra később szükség lesz a Azure AD B2C konfigurálásához. A nyilvános és a titkos kulcsok értékeit `ARKOSE_PUBLIC_KEY` a mintakód és a `ARKOSE_PRIVATE_KEY` [kód](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)is említi.

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>1. rész – egyéni ArkoseSessionToken-attribútum létrehozása

Egyéni attribútum létrehozásához kövesse az alábbi lépéseket:  

1. Ugrás a **Azure Portal**  >  **Azure ad B2C**

2. **Felhasználói attribútumok** kiválasztása

3. Válassza a **Hozzáadás** lehetőséget

4. Adja meg a **ArkoseSessionToken** az attribútum neveként

5. Kattintson a **Létrehozás** elemre.

További információ az [Egyéni attribútumokról](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-custom-attributes?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>2. rész – felhasználói folyamat létrehozása

A felhasználói folyamat lehet akár **regisztráció** , akár **Bejelentkezés** , vagy csak **regisztráció**. A Arkóz Labs felhasználói folyamata csak a regisztráció során jelenik meg.

1. Tekintse meg a felhasználói folyamat létrehozásának [utasításait](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) . Ha meglévő felhasználói folyamatot használ, az **ajánlott (következő generációs előzetes verzió)** verziószámú kell, hogy legyen.

2. A felhasználói folyamat beállításainál lépjen a **felhasználói attribútumok** elemre, és válassza ki a **ArkoseSessionToken** jogcímet.

![A képen látható, hogyan lehet egyéni attribútumokat kiválasztani](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>3. rész – egyéni HTML-, JavaScript-és lapelrendezések konfigurálása

Nyissa meg a megadott [HTML-szkriptet](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html). A fájl egy JavaScript-címkékkel rendelkező HTML-sablont tartalmaz `<script>` , amely három dolgot tesz:

1. Töltse be a Arkóz Labs-szkriptet, amely a Arkóz Labs widgetet jeleníti meg, és ügyféloldali Arkóz Labs-ellenőrzést végez.

2. Elrejti a `extension_ArkoseSessionToken` bemeneti elemet és a címkét, amely az `ArkoseSessionToken` Egyéni attribútumnak felel meg a felhasználó számára megjelenített felhasználói felületen.

3. Amikor egy felhasználó befejezi a Arkóz Labs-problémát, a Arkóz Labs ellenőrzi a felhasználó válaszát, és létrehoz egy jogkivonatot. Az `arkoseCallback` Egyéni JavaScript visszahívás értéke a `extension_ArkoseSessionToken` generált jogkivonat értékének értékét állítja be. Ezt az értéket a következő szakaszban leírtak szerint küldi el az API-végpontnak.

    Tekintse meg [ezt a cikket](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) a Arkóz Labs ügyféloldali ellenőrzésének megismeréséhez.

Kövesse az egyéni HTML és JavaScript használata felhasználói folyamathoz című szakasz lépéseit.

1. Módosítsa [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) -fájlt úgy `<ARKOSE_PUBLIC_KEY>` , hogy az megfeleljen az ügyféloldali ellenőrzéshez generált értéknek, és a Arkóz Labs-parancsfájlnak a fiókjához való betöltéséhez használható.

2. A HTML-oldal üzemeltetése egy több eredetű erőforrás-megosztást (CORS) használó webes végponton. [Hozzon létre egy Azure Blob Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) , és [konfigurálja a CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Ha saját egyéni HTML-kódot használ, másolja és illessze be az `<script>` elemeket a HTML-oldalra.

3. A lapelrendezések konfigurálásához kövesse az alábbi lépéseket

   a. A Azure Portal lépjen a **Azure ad B2C**

   b. Navigáljon a **felhasználói folyamatokhoz** , és válassza ki a felhasználói folyamatot

   c. Lapelrendezések **kiválasztása**

   d. **Helyi fiók regisztrálása lap elrendezésének** kiválasztása

   e. **Egyéni lap tartalmának használata** az **Igen** értékre

   f. Illessze be azt az URI-t, amelyben az egyéni HTML- **tartalom egyéni oldal tartalma használatban** van

   : Ha közösségi identitás-szolgáltatót használ, ismételje meg az e és az **f** **lépést** a **közösségi fiók regisztrációs oldalának** elrendezésében.

   ![a lap elrendezéseit ábrázoló kép](media/partner-arkose-labs/page-layouts.png)

4. A felhasználói folyamatból válassza a **Tulajdonságok** lehetőséget, és jelölje be a JavaScript érvényesítésének **engedélyezése** oldal elrendezése (előzetes verzió) jelölőnégyzetet. További információért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-user-flow) .

### <a name="part-4---create-and-deploy-your-api"></a>4. rész – az API létrehozása és üzembe helyezése

Telepítse az [Azure functions-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a Visual Studio Code-hoz.

>[!Note]
>Az ebben a szakaszban említett lépések feltételezik, hogy Visual Studio Code-ot használ az Azure-függvény üzembe helyezéséhez. Az üzembe helyezéshez használhatja a Azure Portal, a Terminal vagy a parancssort, illetve bármely más Kódszerkesztő alkalmazást is.

#### <a name="run-the-api-locally"></a>Az API helyi futtatása

1. Navigáljon az Azure-bővítményhez a Visual Studio Code-ban a bal oldali navigációs sávon. Válassza ki a helyi Azure-függvényt jelölő **helyi projekt** mappát.

2. Nyomja le az **F5** billentyűt, vagy **használja a hibakeresés**  >  **indítása** menüt a hibakereső elindításához és a Azure functions gazdagéphez való csatolásához. Ez a parancs automatikusan az Azure-függvény által létrehozott egyetlen hibakeresési konfigurációt használja.

3. Az Azure Function bővítménnyel automatikusan néhány fájl fog létrejönni a helyi fejlesztéshez, a függőségek telepítéséhez és a Function Core-eszközök telepítéséhez, ha még nem létezik. Ezek az eszközök segítenek a hibakeresésben.

4. A Function Core eszköz kimenete megjelenik a Visual Studio Code **terminál** paneljén. Miután a gazdagép elindult, az **ALT + kattintson** a kimenetben látható helyi URL-címre a böngésző megnyitásához és a függvény futtatásához. A Azure Functions Explorerben kattintson a jobb gombbal a függvényre a helyileg üzemeltetett függvény URL-címének megtekintéséhez.

Ha újra szeretné telepíteni a helyi példányt a tesztelés során, ismételje meg az 1 – 4. lépést.

#### <a name="add-environment-variables"></a>Környezeti változók hozzáadása

Ez a minta a webes API-végpontot a [http egyszerű hitelesítés](https://tools.ietf.org/html/rfc7617)használatával védi.

A Felhasználónév és a jelszó környezeti változókként van tárolva, nem pedig a tárház részeként. További információért lásd: [local.settings.js](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=macos%2Ccsharp%2Cbash#local-settings-file) fájl.

1. local.settings.jslétrehozása a gyökérkönyvtárban található fájlon

2. Másolja és illessze be az alábbi kódot a fájlba:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
A **BASIC_AUTH_USERNAME** és **BASIC_AUTH_PASSWORD** érték lesz az API-hívás Azure-függvényhez való hitelesítéséhez használt hitelesítő adatok. Válassza ki a kívánt értékeket.

A a `<ARKOSE_PRIVATE_KEY>` Arkóz Labs szolgáltatásban létrehozott kiszolgálóoldali titok. A [Arkóz Labs kiszolgálóoldali ellenőrzési API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) meghívására szolgál az `ArkoseSessionToken` előtér által generált érték ellenőrzéséhez.

A az `<B2C_EXTENSIONS_APP_ID>` Azure ad B2C által használt alkalmazás azonosítóját használja az egyéni attribútumok tárolására a címtárban. Ez az alkalmazás-azonosító a Alkalmazásregisztrációk, a B2C-Extensions-app keresése, valamint az alkalmazás (ügyfél) AZONOSÍTÓjának az **Áttekintés** panelről történő másolásával található meg. Távolítsa el a `-` karaktereket.

![A képen az alkalmazás azonosítója alapján történő keresés látható](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Az alkalmazás üzembe helyezése a weben

1. Kövesse a [jelen](https://docs.microsoft.com/azure/javascript/tutorial-vscode-serverless-node-04) útmutatóban említett lépéseket az Azure-függvény Felhőbeli üzembe helyezéséhez. Másolja az Azure-függvény Endpoint web URL-címét.

2. A telepítés után válassza a **feltöltési beállítások** lehetőséget. A környezeti változókat az App Service [Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#application-settings-in-azure) szerint tölti fel. Ezek [az Alkalmazásbeállítások a Azure Portal](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) használatával is konfigurálhatók vagy kezelhetők.

[Ebből a cikkből](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#republish-project-files) megtudhatja, hogyan hozhatja Azure functions a Visual Studio Code fejlesztését.

#### <a name="configure-and-enable-the-api-connector"></a>Az API-összekötő konfigurálása és engedélyezése

[Hozzon létre egy API-összekötőt](https://docs.microsoft.com/azure/active-directory-b2c/add-api-connector) , és engedélyezze azt a felhasználói folyamat számára. Az API-összekötő konfigurációjának a következőhöz hasonlóan kell kinéznie:

![A képen az alkalmazás azonosítója alapján történő keresés látható](media/partner-arkose-labs/configure-api-connector.png)

- **Végpont URL-címe** – az Azure-függvény üzembe helyezése során korábban átmásolt függvény URL-címe.

- **Felhasználónév és jelszó** – a korábban környezeti változókként megadott Felhasználónév és jelszó.

Az API-összekötő engedélyezéséhez a felhasználói folyamat **API-összekötő** beállításainál válassza ki a MEGhívott API-összekötőt a felhasználói lépés **létrehozása előtt** . Ez meghívja az API-t, amikor egy felhasználó kiválasztja a **Létrehozás** lehetőséget a regisztrálási folyamat során. Az API a `ArkoseSessionToken` Arkóz widget visszahívása által beállított érték kiszolgálóoldali érvényesítését végzi el `arkoseCallback` .

![A képen az API-összekötő engedélyezése látható](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza a **felhasználói folyamatok** lehetőséget.

2. Válassza ki a korábban létrehozott felhasználói folyamatot.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. Alkalmazás: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. Válasz URL-címe: válassza ki az átirányítási URL-címet

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Lépjen a regisztrációs folyamatra, és hozzon létre egy fiókot

5. Kijelentkezés

6. A bejelentkezési folyamat átugrása  

7. A **Folytatás** gombra kattintva megjelenik egy Arkóz Labs-puzzle.

## <a name="additional-resources"></a>További források

- [Mintakód](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) Azure ad B2C regisztrációs felhasználói folyamathoz

- [Egyéni szabályzatok az Azure AD B2C-ben](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
