---
title: MSAL.js és ADAL.js közötti különbségek | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for JavaScript (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) közötti különbségeket, valamint azt, hogy melyiket válassza ki.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a56685c830fc7aa717add3e826c68c04449e378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99580846"
---
# <a name="differences-between-msaljs-and-adaljs"></a>MSAL.js és ADAL.js közötti különbségek

Az Azure AD-entitások hitelesítéséhez és a jogkivonatok Azure AD-ból való igényléséhez mind a JavaScript (MSAL.js), mind a JavaScripthez készült Azure AD Authentication Library (ADAL.js) esetében egyaránt a Microsoft Authentication Library használható. Eddig a legtöbb fejlesztő dolgozott együtt az Azure ad for Developers (v 1.0) használatával az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez a ADAL-t használó jogkivonatok igénylésével. Mostantól a MSAL.js használatával a Microsoft Identity platformon keresztül hitelesítheti a Microsoft identitások (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi Azure AD B2C fiókok) szélesebb körét.

Ez a cikk a javascripthez készült Microsoft Authentication Library (MSAL.js) és az Azure AD Authentication Library for JavaScript (ADAL.js) közötti választást ismerteti, és összehasonlítja a két kódtárat.

## <a name="choosing-between-adaljs-and-msaljs"></a>Választás a ADAL.js és a MSAL.js között

A legtöbb esetben a Microsoft Identity platformot és MSAL.js-t szeretné használni, amely a Microsoft-hitelesítési kódtárak legújabb generációja. A MSAL.js használatával az Azure AD-vel (munkahelyi és iskolai fiókokkal), a Microsoft (személyes) fiókokkal (MSA) vagy a Azure AD B2Cekkel bejelentkezett felhasználók számára tokeneket kell beszerezni az alkalmazásba.

Ha már ismeri a v 1.0-s végpontot (és ADAL.js), érdemes elolvasnia, hogy [Mi a különbség a v 2.0-végpontról?](../azuread-dev/azure-ad-endpoint-comparison.md).

Azonban továbbra is ADAL.js kell használnia, ha az alkalmazásnak a [Active Directory összevonási szolgáltatások (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)korábbi verzióival kell bejelentkeznie a felhasználókba.

## <a name="key-differences-in-authentication-with-msaljs"></a>A hitelesítés főbb eltérései MSAL.js

### <a name="core-api"></a>Alapszintű API

* A ADAL.js a [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) -t használja az alkalmazás egy példányának az engedélyezési kiszolgálóhoz vagy az identitás-szolgáltatóhoz való, a hitelesítésszolgáltató URL-címén keresztül történő megjelenítéséhez. Éppen ellenkezőleg, MSAL.js API-t a felhasználói ügynök ügyfélalkalmazás (a nyilvános ügyfélalkalmazás olyan formája) alapján tervezték meg, amelyben az ügyfél kódját egy felhasználói ügynök, például egy webböngésző hajtja végre. Ez biztosítja azt az `UserAgentApplication` osztályt, amely az alkalmazás hitelesítési környezetének egy példányát jelöli az engedélyezési kiszolgálóval. További részletek: inicializálás a [MSAL.jshasználatával ](msal-js-initializing-client-applications.md).

* ADAL.js a jogkivonatok beszerzésére szolgáló metódusok a alkalmazásban beállított egyetlen szolgáltatóhoz vannak társítva `AuthenticationContext` . MSAL.js a jogkivonatok beszerzésére irányuló kérések eltérő jogosultságokat vehetnek fel, mint a-ben beállított értékek `UserAgentApplication` . Ez lehetővé teszi, hogy MSAL.js a jogkivonatok külön beszerzését és gyorsítótárazását ugyanazon alkalmazás több bérlője és felhasználói fiókja számára.

* A tokenek csendes beszerzésének és megújításának módszere a felhasználók értesítése nélkül `acquireToken` ADAL.js. A MSAL.js a metódus neve, `acquireTokenSilent` hogy a funkció ennél több leíró jellegű legyen.

### <a name="authority-value-common"></a>Hitelesítésszolgáltató értéke `common`

A 2.0-s verzióban a szolgáltató használatával a `https://login.microsoftonline.com/common` felhasználók bármely Azure ad-fiókkal bejelentkezhetnek (bármely szervezet esetében).

A 2.0-s verziójában a `https://login.microsoftonline.com/common` szolgáltató lehetővé teszi, hogy a felhasználók bármely Azure ad-szervezeti fiókkal vagy egy személyes Microsoft-fiókkal (MSA) jelentkezzenek be. Ha a bejelentkezést csak az Azure AD-fiókokra kívánja korlátozni (ugyanaz a viselkedés, mint a ADAL.js), használja a következőt: `https://login.microsoftonline.com/organizations` . Részletekért tekintse `authority` meg az inicializálás konfigurációs beállítását a [MSAL.jshasználatával ](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Jogkivonatok beszerzésének hatókörei
* A tokenek beszerzésére irányuló hitelesítési kérelmekben szereplő erőforrás-paraméter helyett a hatókör

    a v 2.0 protokoll hatóköröket használ a kérelmekben lévő erőforrások helyett. Más szóval, amikor az alkalmazásnak olyan jogkivonatokat kell igényelnie, amelyekhez engedélyek szükségesek egy adott erőforráshoz (például MS Graph), a függvénytár-metódusoknak átadott értékek különbsége a következő:

    1.0-s verzió: erőforrás = HTTPS \: //Graph.microsoft.com

    v 2.0: hatókör = HTTPS \: //Graph.microsoft.com/user.Read

    Az API URI-ja használatával bármilyen erőforrás-API-ra kérhet hatóköröket az alábbi formátumban: appidURI/scope például: https: \/ /mytenant.onmicrosoft.com/myapi/API.Read

    Csak az MS Graph API esetében a hatókör értékének `user.read` leképezése HTTPS-re: \/ /Graph.microsoft.com/user.Read, és használható szinonimaként.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dinamikus hatókörök a növekményes belehozatalhoz.

    Ha a 1.0-s verzióval hoz létre alkalmazásokat, regisztrálnia kell az alkalmazás által a bejelentkezéskor a felhasználó számára szükséges engedélyek (statikus hatókörök) teljes készletét. A 2.0-s verzióban a hatókör paraméterrel kérheti le az engedélyeket a kívánt időpontban. Ezeket dinamikus hatóköröknek nevezzük. Ez lehetővé teszi, hogy a felhasználó növekményes beleegyezett a hatókörökbe. Tehát ha az elején csak azt szeretné, hogy a felhasználó bejelentkezzen az alkalmazásba, és nincs szüksége semmilyen hozzáférésre, ezt megteheti. Ha később szükség van a felhasználó naptárának olvasására, a acquireToken metódusokban kérheti le a naptár hatókörét, és beolvashatja a felhasználó belefoglalását. Például:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Hatókörök a V 1.0 API-khoz

    Ha MSAL.js használatával állítanak be tokeneket a V 1.0 API-khoz, az API-hoz regisztrált összes statikus hatókört az `.default` API hatókörként való hozzáfűzésével kérheti le. Például:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Következő lépések
További információkért tekintse meg a [1.0-s és a 2.0-s verzió összehasonlítását](../azuread-dev/azure-ad-endpoint-comparison.md)ismertető témakört.
