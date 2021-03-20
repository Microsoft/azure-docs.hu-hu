---
title: Azure Active Directory B2C felhasználói felület beágyazása az alkalmazásba egyéni szabályzattal
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan ágyazhat be Azure Active Directory B2C felhasználói felületet az alkalmazásba egyéni szabályzattal
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1255c4962de1fce19efa9c0b0e1d28fc348463ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580147"
---
# <a name="embedded-sign-in-experience"></a>Beágyazott bejelentkezési élmény

Az egyszerűbb bejelentkezési élmény érdekében elkerülheti a felhasználók átirányítását egy külön bejelentkezési oldalra, vagy előugró ablakot hozhat létre. A beágyazott keret elem használatával közvetlenül a `<iframe>` webalkalmazásba ágyazhatja be a Azure ad B2C bejelentkezési felhasználói felületét.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Webalkalmazás beágyazott bejelentkezési lapja

A beágyazott keret elem a `<iframe>` dokumentumok HTML5-weblapokon való beágyazására szolgál. Az IFRAME elem használatával közvetlenül a webalkalmazásba ágyazhatja be a Azure AD B2C bejelentkezési felhasználói felületét, ahogy az alábbi példában is látható:

![Bejelentkezés a lebegő DIV-felülettel](media/embedded-login/login-hovering.png)

IFrame használatakor vegye figyelembe a következőket:

- A beágyazott bejelentkezés csak a helyi fiókokat támogatja. A legtöbb közösségi identitás-szolgáltató (például a Google és a Facebook) blokkolja a bejelentkezési oldalaikat a beágyazott keretek között.
- Mivel az IFRAME-en belüli Azure AD B2C munkamenet-cookie-k harmadik féltől származó cookie-k, bizonyos böngészők (például a Safari vagy a Chrome inkognitóban módban), vagy letilthatják vagy törölhetik ezeket a cookie-kat, ami nem kívánatos felhasználói élményt eredményez. A probléma elkerülése érdekében győződjön meg arról, hogy az alkalmazás tartományneve és a Azure AD B2C tartománya *azonos eredetű*. Ha ugyanazt a forrást szeretné használni, [engedélyezze az egyéni tartományokat](custom-domain.md) Azure ad B2C bérlő számára, majd konfigurálja a webalkalmazást ugyanazzal a forrással. Például a on futtatott alkalmazásnak https://app.contoso.com ugyanaz a forrása, mint a Azure ad B2C fut https://login.contoso.com .

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre az [első lépések az egyéni házirendek Active Directory B2Cban](custom-policy-get-started.md)című témakör lépéseit.
* [Engedélyezze az egyéni tartományokat](custom-domain.md) a szabályzatok számára.

## <a name="configure-your-policy"></a>A házirend konfigurálása

Annak engedélyezéséhez, hogy a Azure AD B2C felhasználói felülete iframe-be legyen ágyazva, meg kell adni a tartalom biztonsági házirendjét `Content-Security-Policy` és a keret beállításait `X-Frame-Options` a Azure ad B2C http-válasz fejlécei között. Ezek a fejlécek lehetővé teszik, hogy az Azure AD B2C felhasználói felülete az alkalmazás tartománynevénél fusson.

Adjon hozzá egy **JourneyFraming** elemet a [RelyingParty](relyingparty.md) elemen belül.  A **UserJourneyBehaviors** elemnek követnie kell a **DefaultUserJourney**. A **UserJourneyBehaviors** elemnek a következő példához hasonlóan kell kinéznie:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

A **Sources** attribútum a WEBalkalmazás URI-ját tartalmazza. Adjon meg egy szóközt az URI-k között. Minden URI-nak a következő követelményeknek kell megfelelnie:

- Az URI-nak megbízhatónak kell lennie, és az alkalmazás tulajdonában kell lennie.
- Az URI-nak a https-sémát kell használnia.  
- Meg kell adni a webalkalmazás teljes URI-JÁT. Helyettesítő karakterek nem használhatók.

Emellett azt is javasoljuk, hogy a saját tartománynevet egy iframe-be ágyazza be úgy, hogy az alkalmazás lapjain a Content-Security-Policy és az X-frame-Options fejléceket állítja be. Ez csökkenti az iframe elemek beágyazott beágyazásával kapcsolatos, régebbi böngészőkre vonatkozó biztonsági problémákat.

## <a name="adjust-policy-user-interface"></a>Házirend felhasználói felületének módosítása

Azure AD B2C [felhasználói felület testreszabásával](customize-ui.md)szinte teljes mértékben szabályozhatja a felhasználók számára megjelenített HTML-és CSS-tartalmat. A HTML-oldalak tartalmi definíciók használatával történő testreszabásához kövesse a következő lépéseket:. Ha az Azure AD B2C felhasználói felületét az IFRAME-mérethez szeretné igazítani, adjon meg tiszta HTML-oldalt háttér nélkül, és extra szóközöket.  

A következő CSS-kód elrejti a Azure AD B2C HTML-elemeket, és az IFRAME kitöltéséhez módosítja a panel méretét.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

Bizonyos esetekben érdemes lehet értesíteni az alkalmazást, hogy mely Azure AD B2C oldal jelenleg be van mutatva. Ha például egy felhasználó kiválasztja a regisztrációs lehetőséget, akkor az alkalmazásnak válaszolnia kell a közösségi fiókkal való bejelentkezésre mutató hivatkozások elrejtésével vagy az IFRAME méretének módosításával.

Az aktuális Azure AD B2C oldal alkalmazásának értesítéséhez [engedélyezze a szabályzatot a javascripthez](./javascript-and-page-layout.md), majd használja a HTML5 post-üzeneteket. A következő JavaScript-kód post-üzenetet küld az alkalmazásnak a következővel `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Webalkalmazás konfigurálása

Amikor a felhasználó kiválasztja a bejelentkezés gombot, a [webalkalmazás](code-samples.md#web-apps-and-apis) létrehoz egy engedélyezési kérelmet, amely a felhasználót a bejelentkezési élmény Azure ad B2C. A bejelentkezés befejeződése után a Azure AD B2C egy azonosító tokent vagy egy engedélyezési kódot ad vissza az alkalmazásban konfigurált átirányítási URI-hoz.

A beágyazott bejelentkezés támogatásához az IFRAME **src** tulajdonság a bejelentkezési vezérlőre mutat, például `/account/SignUpSignIn` az, amely létrehozza az engedélyezési kérelmet, és átirányítja a felhasználót Azure ad B2C házirendre.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Miután az azonosító tokent megkapta és érvényesített az alkalmazás, az engedélyezési folyamat befejeződött, és az alkalmazás felismeri és megbízik a felhasználónál. Mivel az engedélyezési folyamat az IFRAME-kereten belül történik, újra kell töltenie a Főoldalt. A lap újrabetöltése után a bejelentkezési gomb a "kijelentkezés" értékre változik, és a Felhasználónév a felhasználói felületen jelenik meg.  

Az alábbi példa azt szemlélteti, hogyan frissíthető a bejelentkezési átirányítási URI a főoldalra:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Közösségi fiókkal rendelkező bejelentkezés hozzáadása egy webalkalmazáshoz

A közösségi identitás-szolgáltatók blokkolják a bejelentkezési oldalaikat a beágyazott keretek megjelenítésében. Használhat külön szabályzatot a közösségi fiókok számára, vagy használhat egyetlen szabályzatot is a bejelentkezéshez és a helyi és közösségi fiókokkal való regisztrációhoz. Ezután használhatja a `domain_hint` lekérdezési karakterlánc paramétert. A domain hint paraméter közvetlenül a közösségi identitás szolgáltatójának bejelentkezési oldalára viszi a felhasználót.

Az alkalmazásban vegye fel a bejelentkezést a közösségi fiók gombjaival. Ha a felhasználó a közösségi fiókok egyik gombjára kattint, a vezérlőnek módosítania kell a házirend nevét, vagy a domain hint paramétert kell beállítania.

<!-- TBD: add a diagram -->

Az átirányítási URI az IFRAME által használt átirányítási URI lehet. Kihagyhatja az oldal újratöltését.

## <a name="configure-a-single-page-application"></a>Egyoldalas alkalmazás konfigurálása

Egyoldalas alkalmazások esetén egy második "bejelentkezési" HTML-oldalt is be kell írni, amely betöltődik az IFRAME elembe. Ez a bejelentkezési oldal a hitelesítési függvénytár kódját tárolja, amely létrehozza az engedélyezési kódot, és visszaadja a tokent.

Ha az egyoldalas alkalmazásnak szüksége van a hozzáférési jogkivonatra, a JavaScript-kód használatával szerezze be a hozzáférési jogkivonatot az azt tartalmazó iframe-ből és objektumból.

> [!NOTE]
> A MSAL 2,0 egy iframe-ben való futtatása jelenleg nem támogatott.

A következő kód egy példa, amely a főoldalon fut, és meghívja az IFRAME JavaScript-kódját:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő kapcsolódó cikkeket:

- [A felhasználói felület testreszabása](customize-ui.md)
- [RelyingParty](relyingparty.md) -elem leírása
- [Szabályzat engedélyezése a JavaScripthez](./javascript-and-page-layout.md)
- [Kódminták](code-samples.md)