---
title: JavaScript-és lapelrendezés-verziók
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti a JavaScriptet, és hogyan használhatja a Azure Active Directory B2C a lapelrendezések verzióját.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d234e57f7c11b0d9f2cd212bde93a8b8e478ef41
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781364"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript-és lapelrendezés-verziók a Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

A Azure AD B2C a felhasználói felület és az egyéni házirendek felhasználói felületi elemeihez tartalmaz HTML-, CSS-és JavaScript-készletet tartalmazó csomagolt tartalmat. JavaScript engedélyezése az alkalmazásokhoz:

::: zone pivot="b2c-user-flow"

* [Lapelrendezés kiválasztása](page-layout.md)
* A felhasználói folyamat engedélyezése a Azure Portal használatával
* [B2clogin.com](b2clogin.md) használata a kérésekben

::: zone-end

::: zone pivot="b2c-custom-policy"

* [Lapelrendezés kiválasztása](page-layout.md)
* Elem hozzáadása az [Egyéni szabályzathoz](custom-policy-overview.md)
* [B2clogin.com](b2clogin.md) használata a kérésekben

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Lapelrendezés verziójának kiválasztása

Ha engedélyezni kívánja a JavaScript ügyféloldali kódját, a JavaScript-alapú elemek nem változtathatók meg. Ha nem változtathatók meg, a módosítások váratlan viselkedést okozhatnak a felhasználói oldalakon. Ezen problémák megelőzése érdekében kényszerítse ki a lapelrendezés használatát, és adja meg az oldalelrendezés verzióját, hogy a JavaScript-alapú tartalom-definíciók nem változtathatók meg. Ha nem kívánja engedélyezni a JavaScriptet, megadhatja a lapokhoz tartozó lapelrendezés-verziót is.

::: zone pivot="b2c-user-flow"

A felhasználói folyamat lapjaihoz tartozó lapelrendezés verziójának megadása: 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza **ki a lapelrendezések elemet**. Válassza ki az **elrendezés nevét**, majd válassza ki a lapelrendezés **verzióját (előzetes verzió)**.

További információ a különböző lapelrendezés-verziókról: az [oldalelrendezés verziójának változási naplója](page-layout.md).

![Oldalelrendezés beállításai a portálon, a lapelrendezés verziószámának legördülő menüje](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Válassza ki az alkalmazás felhasználói felületi elemeinek [lapelrendezés](contentdefinitions.md#select-a-page-layout) elemet.

Definiáljon egy oldal- [elrendezési verziót](contentdefinitions.md#migrating-to-page-layout) az `contract` Egyéni szabályzat *összes* tartalmi definíciójában. Az érték formátumának tartalmaznia kell a szót `contract` : _urn: com: Microsoft: HRE: B2C: Elements:**Szerződés**:p Age-Name: Version_. Megtudhatja, hogyan [telepítheti át az oldal elrendezését](contentdefinitions.md#migrating-to-page-layout) az oldal verziójával.

A következő példa a Content definition-azonosítókat és a hozzá tartozó **DataUri** mutatja be: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>JavaScript engedélyezése

::: zone pivot="b2c-user-flow"

A felhasználói folyamat **tulajdonságainál** engedélyezheti a JavaScriptet. A JavaScript engedélyezése is kikényszeríti a lapelrendezés használatát. Ezután a következő szakaszban leírtak szerint beállíthatja a felhasználói folyamathoz tartozó lapelrendezés verzióját.

![Felhasználói folyamat tulajdonságai lap a JavaScript engedélyezése beállítás kiemelve](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

A szkriptek végrehajtásának engedélyezéséhez adja hozzá a **ScriptExecution** elemet a [RelyingParty](relyingparty.md) elemhez.

1. Nyissa meg az egyéni házirend-fájlt. Például *SignUpOrSignin.xml*.
1. Adja hozzá a **ScriptExecution** elemet a **RelyingParty** elemhez:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Mentse és töltse fel a fájlt.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Útmutató a JavaScript használatához

Kövesse az alábbi irányelveket, amikor testreszabja az alkalmazás felületét a JavaScript használatával:

- Ne hozzon létre egy kattintásos eseményt `<a>` HTML-elemeken.
- Ne vegyen fel függőséget Azure AD B2C kód vagy Megjegyzés alapján.
- Ne módosítsa Azure AD B2C HTML-elemek sorrendjét vagy hierarchiáját. A felhasználói felületi elemek sorrendjének szabályozásához használjon Azure AD B2C szabályzatot.
- A következő megfontolásokkal hívhat meg bármilyen REST-szolgáltatást:
    - Előfordulhat, hogy az ügyféloldali HTTP-hívások engedélyezéséhez be kell állítania a REST szolgáltatás CORS.
    - Győződjön meg arról, hogy a REST szolgáltatás biztonságos, és csak a HTTPS protokollt használja.
    - Ne használja közvetlenül a JavaScriptet Azure AD B2C végpontok meghívásához.
- Beágyazhatja a JavaScriptet, vagy külső JavaScript-fájlokra mutató hivatkozásokat is használhat. Külső JavaScript-fájl használata esetén ügyeljen arra, hogy az abszolút URL-címet használja, nem pedig relatív URL-címet.
- JavaScript-keretrendszerek:
    - Azure AD B2C a [jQuery egy adott verzióját](page-layout.md#jquery-version)használja. Ne adja meg a jQuery egy másik verzióját. Ha egynél több verziót használ ugyanazon a lapon, problémákat okoz.
    - A RequireJS használata nem támogatott.
    - Azure AD B2C nem támogatja a legtöbb JavaScript-keretrendszert.
- Azure AD B2C beállítások a következő módon olvashatók: hívás `window.SETTINGS` , `window.CONTENT` objektumok, például az aktuális felhasználói felület nyelve. Ne módosítsa az objektumok értékét.
- A Azure AD B2C-hibaüzenet testreszabásához használjon honosítást egy házirendben.
- Ha bármilyen adat egy szabályzat használatával érhető el, általában az ajánlott módszer.

## <a name="javascript-samples"></a>JavaScript-minták

### <a name="show-or-hide-a-password"></a>Jelszó megjelenítése vagy elrejtése

Az ügyfelek számára a regisztráció sikerességének egyik gyakori módja, hogy meglássuk, mit adtak meg a jelszavuk. Ezzel a beállítással a felhasználók regisztrálhatnak, így egyszerűen megtekinthetik és módosíthatják a jelszavukat, ha szükséges. A jelszó típusú mezőknél megjelenik egy jelölőnégyzet a **jelszó megjelenítése** címkével.  Ez lehetővé teszi, hogy a felhasználó egyszerű szövegként tekintse meg a jelszót. Adja meg ezt a kódrészletet a regisztrációs vagy bejelentkezési sablonban egy önérvényesített oldal számára:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Használati feltételek hozzáadása

Adja meg a következő kódot az oldalra, ahol használni szeretné a használati **feltételeket** jelölőnégyzetet. Ezt a jelölőnégyzetet általában a helyi fiók regisztrálásához és a közösségi fiók regisztrációs oldalain kell megadni.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

A kódban cserélje le a `termsOfUseUrl` kifejezést a használati feltételekre vonatkozó szerződésre mutató hivatkozásra. A címtárhoz hozzon létre egy **termsOfUse** nevű új felhasználói attribútumot, majd adja hozzá a **termsOfUse** felhasználói attribútumként.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával Azure Active Directory B2Cban](customize-ui-with-html.md).
