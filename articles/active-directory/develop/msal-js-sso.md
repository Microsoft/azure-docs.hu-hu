---
title: Egyszeri bejelentkezés (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az egyszeri bejelentkezési élmények létrehozását a JavaScripthez készült Microsoft Authentication Library (MSAL.js) használatával.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690778"
---
# <a name="single-sign-on-with-msaljs"></a>Egyszeri bejelentkezés az MSAL.js-sel

Az egyszeri Sign-On (SSO) lehetővé teszi, hogy a felhasználók egyszer megadják a hitelesítő adataikat a bejelentkezéshez, és olyan munkamenetet hozzon létre, amely többször is felhasználható több alkalmazásban anélkül, hogy újra kellene hitelesíteni. Ez zökkenőmentes felhasználói élményt nyújt a felhasználónak, és csökkenti a hitelesítő adatok ismételt megadását.

Az Azure AD egyszeri bejelentkezéses képességeket biztosít az alkalmazásokhoz, ha beállítja a munkamenet-cookie-t, amikor a felhasználó első alkalommal hitelesíti magát. A MSAL.js könyvtár segítségével az alkalmazások többféleképpen is kihasználhatják ezt.

## <a name="sso-between-browser-tabs"></a>Egyszeri bejelentkezés a böngésző lapjai között

Ha az alkalmazás több lapon van megnyitva, és először bejelentkezik egy lapra, a felhasználó a további lapokon is bejelentkezett, és nem kell megadnia. MSAL.js gyorsítótárazza a felhasználó azonosító tokenjét a böngészőben `localStorage` , és a többi megnyitott lapon aláírja a felhasználót az alkalmazásba.

Alapértelmezés szerint `sessionStorage` a MSAL.js olyant használ, amely nem engedélyezi a munkamenetet a lapok közötti megosztásra. Ha SSO-t szeretne beolvasni a lapok között, ügyeljen arra, hogy a `cacheLocation` MSAL.js az `localStorage` alább látható módon állítsa be.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Egyszeri bejelentkezés az alkalmazások között

Amikor egy felhasználó hitelesíti magát, egy munkamenet-cookie van beállítva az Azure AD-tartományban a böngészőben. A MSAL.js ezen a munkamenet-cookie-on keresztül biztosítja az egyszeri bejelentkezést a felhasználók számára a különböző alkalmazások között. A MSAL.js gyorsítótárazza a felhasználó azonosító jogkivonatait és hozzáférési jogkivonatait a böngésző tárterületén az alkalmazás tartományában. Ennek eredményeképpen az egyszeri bejelentkezés viselkedése különböző esetekben változhat:  

### <a name="applications-on-the-same-domain"></a>Azonos tartományban lévő alkalmazások

Ha az alkalmazások ugyanazon a tartományon futnak, a felhasználó egyszer tud bejelentkezni egy alkalmazásba, majd kérés nélkül hitelesíti a többi alkalmazást. MSAL.js kihasználja a felhasználó számára gyorsítótárazott tokeneket a tartományban az egyszeri bejelentkezés biztosításához.

### <a name="applications-on-different-domain"></a>Különböző tartományba tartozó alkalmazások

Ha az alkalmazások különböző tartományokon futnak, az A tartományon gyorsítótárazott tokenek nem érhetők el MSAL.js a B tartományban.

Ez azt jelenti, hogy amikor a (z) tartományba bejelentkezett felhasználók megnyitnak egy alkalmazást a B tartományban, a rendszer átirányítja vagy megkéri az Azure AD-oldalát. Mivel az Azure AD továbbra is a felhasználói munkamenet cookie-val rendelkezik, be fog jelentkezni a felhasználóba, és nem kell újra megadnia a hitelesítő adatokat. Ha a felhasználó több felhasználói fiókkal rendelkezik az Azure AD-vel való munkamenetben, a rendszer felszólítja a felhasználót, hogy válassza ki a megfelelő fiókot a bejelentkezéshez.

### <a name="automatically-select-account-on-azure-ad"></a>Fiók automatikus kiválasztása az Azure AD-ben

Bizonyos esetekben az alkalmazás hozzáfér a felhasználó hitelesítési környezetéhez, és szeretné elkerülni az Azure AD-fiók kijelölését, ha több fiók van bejelentkezve.  Ezt többféleképpen is elvégezheti:

**Munkamenet-azonosító (SID) használata**

A munkamenet-azonosító egy [opcionális jogcím](active-directory-optional-claims.md) , amely konfigurálható az azonosító jogkivonatokban. Ez a jogcím lehetővé teszi az alkalmazás számára, hogy azonosítsa a felhasználó Azure AD-munkamenetét a felhasználó fiókjának nevétől vagy felhasználónevétől függetlenül. A kérés paramétereinek átadhatja a biztonsági azonosítót a `acquireTokenSilent` híváshoz. Ez lehetővé teszi, hogy az Azure AD megkerüljék a fiók kijelölését. A SID a munkamenet-cookie-hoz van kötve, és nem fog több böngészőbeli kontextust felvenni.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> A SID csak a MSAL.js hívásával végzett csendes hitelesítési kérések esetén használható `acquireTokenSilent` .
A választható jogcímek konfigurálásának lépései [itt](active-directory-optional-claims.md)találhatók az alkalmazás jegyzékfájljában.

**A login hint használata**

Ha nincs konfigurálva SID-jogcím, vagy meg kell kerülnie a fiók kiválasztására vonatkozó kérést az interaktív hitelesítési hívásokban, ezt megteheti a `login_hint` kérelem paramétereinek és opcionálisan `domain_hint` a `extraQueryParameters` MSAL.js interaktív metódusokban ( `loginPopup` , `loginRedirect` `acquireTokenPopup` és `acquireTokenRedirect` ). Például:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Login_hint és domain_hint értékeit a felhasználó azonosító jogkivonatában visszaadott jogcímek beolvasásával érheti el.

* a **loginHint** a jogcímnek kell lennie `preferred_username` az azonosító jogkivonatban.

* **domain_hint** csak akkor szükséges, ha az/gyakori hibák-szolgáltatót használja. A tartomány-emlékeztetőt a bérlői azonosító (TID) határozza meg.  Ha az `tid` azonosító jogkivonatban szereplő jogcím a `9188040d-6c67-4c5b-b112-36a304b66dad` fogyasztó. Egyéb esetben ez a szervezet.

Az [itt](v2-oauth2-implicit-grant-flow.md) olvasható további információ a bejelentkezési és a tartományi emlékeztető értékeiről.

> [!Note]
> A SID és a login_hint nem adható át egyszerre. Ez hibaüzenetet eredményez.

## <a name="sso-without-msaljs-login"></a>Egyszeri bejelentkezés MSAL.js bejelentkezés nélkül

A tervezés szerint a MSAL.js megköveteli, hogy egy bejelentkezési metódust hozzon létre a felhasználói környezet létrehozásához az API-k jogkivonatának beolvasása előtt. Mivel a bejelentkezési módszerek interaktívak, a felhasználó egy kérést lát.

Bizonyos esetekben előfordulhat, hogy az alkalmazások egy másik alkalmazásban kezdeményezett hitelesítésen keresztül férhetnek hozzá a hitelesített felhasználó környezetéhez vagy azonosító jogkivonatához, és az egyszeri bejelentkezést szeretnék használni a jogkivonatok beszerzéséhez, anélkül, hogy az első bejelentkezés MSAL.js.

Példa erre: A felhasználó egy szülő webalkalmazásba van bejelentkezve, amely bővítményként vagy beépülő modulként futó másik JavaScript-alkalmazást üzemeltet.

Ebben az esetben az egyszeri bejelentkezés felhasználói felülete a következőképpen érhető el:

Adja át az `sid` IF elérhető (vagy `login_hint` opcionális `domain_hint` ) paraméterként a MSAL.js hívást a következő módon `acquireTokenSilent` :

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Egyszeri bejelentkezés ADAL.js MSAL.js frissítéshez

A MSAL.js a ADAL.js Azure AD-hitelesítési forgatókönyvek esetében a szolgáltatás-paritást hozza elérhetővé. Annak érdekében, hogy az áttelepítést ADAL.jsról MSAL.js egyszerű állapotba hozza, és elkerülje, hogy a felhasználók ne kelljen újra bejelentkezniük, a könyvtár beolvassa a felhasználó munkamenetét jelképező azonosító tokent ADAL.js gyorsítótárban, és zökkenőmentesen aláírja a felhasználót a MSAL.jsban.  

Ha az egyszeri bejelentkezés (SSO) viselkedését szeretné kihasználni ADAL.jsról való frissítéskor, gondoskodnia kell arról, hogy a kódtárak `localStorage` gyorsítótárazási jogkivonatokat használjanak. Állítsa be `cacheLocation` a `localStorage` (z) beállítást a MSAL.js és az ADAL.js konfigurációnál az inicializáláskor a következőképpen:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Ha ez konfigurálva van, MSAL.js beolvashatja a hitelesített felhasználó gyorsítótárazott állapotát ADAL.js és használhatja az SSO-t a MSAL.jsban.

## <a name="next-steps"></a>Következő lépések

További információ az [egyszeri bejelentkezési munkamenet és a jogkivonat élettartamának](active-directory-configurable-token-lifetimes.md) értékeiről az Azure ad-ben.
