---
title: Nyelvi Testreszabás a Azure Active Directory B2Cban
description: Ismerje meg, hogyan szabhatja testre a felhasználói folyamatok nyelvi élményét Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686410"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Nyelvi Testreszabás a Azure Active Directory B2Cban

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure Active Directory B2C (Azure AD B2C) nyelvi testreszabása lehetővé teszi, hogy a felhasználói folyamat különböző nyelveket biztosítson az ügyfelek igényeinek megfelelően. A Microsoft a [36 nyelvekhez](#supported-languages)biztosít fordításokat, de bármilyen nyelven saját fordításokat is megadhat. Ha a felhasználói élmény csak egyetlen nyelven érhető el, testreszabhatja a lapokon található bármely szöveget.

## <a name="how-language-customization-works"></a>A nyelvi Testreszabás működése

A nyelvi testreszabással kiválaszthatja, hogy a felhasználói folyamat mely nyelveken érhető el a alkalmazásban. A szolgáltatás engedélyezése után megadhatja az alkalmazásból a lekérdezési karakterlánc paraméterét `ui_locales` . Amikor behívja a Azure AD B2Ct, a rendszer lefordítja a lapot a jelzett területi beállításra. Ez a típusú konfiguráció lehetővé teszi a felhasználói folyamat nyelveinek teljes körű ellenőrzését, és figyelmen kívül hagyja az ügyfél böngészőjének nyelvi beállításait.

Előfordulhat, hogy nincs szüksége arra, hogy milyen nyelveken látja az ügyfelet. Ha nem ad meg `ui_locales` paramétert, az ügyfél felhasználói élményét a böngésző beállításai határozzák meg. Továbbra is szabályozhatja, hogy a felhasználói folyamat mely nyelveket fordítsa le, ha azt támogatott nyelvként adja hozzá. Ha egy ügyfél böngészője olyan nyelvet mutat be, amelyet nem szeretne támogatni, akkor a támogatott kultúrákban alapértelmezettként kiválasztott nyelv jelenik meg.

* **felhasználói felület – megadott nyelv**: a nyelvi Testreszabás engedélyezése után a rendszer lefordítja a felhasználói folyamatot az itt megadott nyelvre.
* **Böngésző által kért nyelv**: Ha nincs `ui_locales` megadva paraméter, a felhasználói folyamat a böngésző által kért nyelvre lesz lefordítva, *Ha a nyelv támogatott*.
* **Házirend alapértelmezett nyelve**: Ha a böngésző nem ad meg nyelvet, vagy egy nem támogatott értéket ad meg, a felhasználói folyamat fordítása a felhasználói folyamat alapértelmezett nyelvére történik.

> [!NOTE]
> Ha egyéni felhasználói attribútumokat használ, saját fordításokat kell megadnia. További információ: [a karakterláncok testreszabása](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

A honosításhoz három lépés szükséges: 

1. A támogatott nyelvek explicit listájának beállítása
1. Nyelvspecifikus karakterláncok és gyűjtemények megadása
1. Szerkessze az oldal [tartalmának definícióját](contentdefinitions.md) . 

::: zone-end 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>A ui_locales kért nyelveinek támogatása

A nyelvi Testreszabás általánosan elérhetővé tétele előtt létrehozott szabályzatoknak először engedélyeznie kell ezt a funkciót. Az azt követően létrehozott házirendek és felhasználói folyamatok, hogy a nyelvi Testreszabás alapértelmezés szerint engedélyezve van.

Ha engedélyezi a nyelvi testreszabást egy felhasználói folyamaton, a paraméter hozzáadásával szabályozhatja a felhasználói folyamat nyelvét `ui_locales` .

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyet engedélyezni szeretne a fordítások számára.
1. Válassza a **nyelvek** lehetőséget.
1. Válassza a **nyelvi Testreszabás engedélyezése** lehetőséget.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Válassza ki, hogy a felhasználói folyamat mely nyelveken legyen engedélyezve

Lehetővé teheti a felhasználói folyamat fordítását, ha a böngésző a paraméter nélkül kéri le `ui_locales` .

1. Győződjön meg arról, hogy a felhasználói folyamat rendelkezik a korábbi utasításokban engedélyezett nyelvi testreszabással.
1. A felhasználói folyamat **nyelvek** lapján válassza ki a támogatni kívánt nyelvet.
1. A Tulajdonságok ablaktáblán az **enabled** ( **Igen**) értékre vált.
1. Válassza a **Mentés** lehetőséget a Tulajdonságok ablaktábla tetején.

>[!NOTE]
>Ha a `ui_locales` paraméter nincs megadva, a lap csak akkor lesz lefordítva az ügyfél böngésző nyelvére, ha engedélyezve van.
>

## <a name="customize-your-strings"></a>A karakterláncok testreszabása

A nyelvi Testreszabás lehetővé teszi a felhasználói folyamat bármely karakterláncának testreszabását.

1. Győződjön meg arról, hogy a felhasználói folyamat rendelkezik az előző utasításokban engedélyezett nyelvi testreszabással.
1. A felhasználói folyamat **nyelvek** lapján válassza ki a testreszabni kívánt nyelvet.
1. Az **oldal szintű erőforrások fájljai** területen válassza ki a szerkeszteni kívánt oldalt.
1. Válassza az **Alapértelmezések letöltése** (vagy a **felülbírálások letöltése** lehetőséget, ha korábban már szerkesztette ezt a nyelvet).

Ezek a lépések egy JSON-fájlt biztosítanak a karakterláncok szerkesztésének megkezdéséhez.

### <a name="change-any-string-on-the-page"></a>Az oldalon lévő bármelyik sztring módosítása

1. Nyissa meg a JSON-szerkesztő korábbi utasításait letöltő JSON-fájlt.
1. Keresse meg a módosítani kívánt elemet. Keresse meg `StringId` a keresett karakterláncot, vagy keresse meg a `Value` módosítani kívánt attribútumot.
1. Frissítse az `Value` attribútumot, hogy mit szeretne megjeleníteni.
1. Minden módosítani kívánt sztring esetében váltson a következőre: `Override` `true` .
1. Mentse a fájlt, és töltse fel a módosításokat. (A feltöltési vezérlőt a JSON-fájl letöltésével megegyező helyen találja.)

> [!IMPORTANT]
> Ha felül kell bírálnia egy karakterláncot, ügyeljen arra, hogy a értékre állítsa be a `Override` értéket `true` . Ha az érték nem változik, a rendszer figyelmen kívül hagyja a bejegyzést.

### <a name="change-extension-attributes"></a>Bővítmény attribútumainak módosítása

Ha módosítani szeretné egy egyéni felhasználói attribútumhoz tartozó karakterláncot, vagy az egyiket szeretné hozzáadni a JSON-hoz, a következő formátumban kell megadnia:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

A helyére írja `<ExtensionAttribute>` be az egyéni felhasználói attribútum nevét.

Cserélje le `<ExtensionAttributeValue>` a szöveget a megjelenítendő új sztringre.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Adja meg az értékek listáját a LocalizedCollections használatával

Ha meg szeretné adni a válaszok értékeinek listáját, létre kell hoznia egy `LocalizedCollections` attribútumot. `LocalizedCollections` a és a párok tömbje `Name` `Value` . Az elemek sorrendje a megjelenő sorrendben jelenik meg. A hozzáadáshoz `LocalizedCollections` használja a következő formátumot:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` a felhasználó attribútuma, amelyhez ez az `LocalizedCollections` attribútum válaszol.
* `Name` a felhasználó számára megjelenített érték.
* `Value` Ha ez a beállítás be van jelölve, a rendszer a jogcímben visszaadott értéket adja vissza.

### <a name="upload-your-changes"></a>A módosítások feltöltése

1. A JSON-fájl módosításainak befejezése után lépjen vissza a B2C-bérlőre.
1. Válassza a **felhasználói folyamatok** lehetőséget, majd kattintson arra a felhasználói folyamatra, amelyet engedélyezni szeretne a fordítások számára.
1. Válassza a **nyelvek** lehetőséget.
1. Válassza ki a nyelvet, amelyet le szeretne fordítani.
1. Válassza ki azt a lapot, amelyen a fordításokat meg szeretné adni.
1. Válassza ki a mappa ikont, és válassza ki a feltölteni kívánt JSON-fájlt.

A módosításokat a rendszer automatikusan menti a felhasználói folyamatba.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Az oldal felhasználói felületének testreszabása a nyelvi Testreszabás használatával

A [HTML-tartalmak](customize-ui-with-html.md)honosítása kétféleképpen lehetséges. Az egyik módszer a [nyelvi Testreszabás](language-customization.md)bekapcsolása. A funkció engedélyezése lehetővé teszi, hogy Azure AD B2C továbbítsa az OpenID Connect paramétert a `ui-locales` végpontnak. A tartalom-kiszolgáló ezzel a paraméterrel testreszabott, nyelvspecifikus HTML-lapokat adhat meg.

Azt is megteheti, hogy a használt területi beállítás alapján más helyekről is lehívhatja a tartalmat. A CORS-kompatibilis végponton beállíthatja, hogy a mappa szerkezete bizonyos nyelvekhez biztosítson tartalmat. Ha a helyettesítő karaktert használja, hívja meg a megfelelőt `{Culture:RFC5646}` . Tegyük fel például, hogy ez az egyéni oldal URI-ja:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

A lapot betöltheti a alkalmazásban `fr` . Amikor az oldal lekéri a HTML-és CSS-tartalmakat, a következőről indul:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Egyéni nyelvek hozzáadása

Hozzáadhat olyan nyelveket is, amelyeket a Microsoft jelenleg nem biztosít fordításokat a szolgáltatáshoz. A felhasználói folyamat összes karakterláncához meg kell adnia a fordításokat. A nyelvi és területi beállítási kódok az ISO 639-1 szabványban foglalt értékekre korlátozódnak. A területi beállítás formátuma például a következő: "ISO_639-1_code"-"országhívószám" `en-GB` . A területi azonosító formátumával kapcsolatos további információkért tekintse meg a következőt: https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
2. Kattintson arra a felhasználói folyamatra, amelyhez egyéni nyelveket kíván hozzáadni, majd kattintson a **nyelvek** elemre.
3. Válassza az **Egyéni nyelv hozzáadása** lehetőséget az oldal tetején.
4. A megnyíló környezet ablaktáblán azonosítsa, hogy egy érvényes területi beállítás megadásával melyik nyelvet használja a fordítások számára.
5. Minden oldalon letöltheti az angol nyelvű felülbírálások készletét, és dolgozhat a fordításokon is.
6. Miután elkészült a JSON-fájlokkal, feltöltheti őket az egyes lapokra.
7. Válassza az **Engedélyezés** lehetőséget, és a felhasználói folyamat mostantól megjelenítheti a felhasználók számára a nyelvet.
8. Mentse a nyelvet.

>[!IMPORTANT]
>A Mentés előtt engedélyeznie kell az egyéni nyelveket, vagy fel kell töltenie a felülbírálásokat.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>A támogatott nyelvek listájának beállítása

Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a `Localization` elemet a támogatott nyelvekhez: angol (alapértelmezett) és spanyol.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Nyelvspecifikus címkék megadása

Az elem [LocalizedResources](localization.md#localizedresources) `Localization` a honosított karakterláncok listáját tartalmazza. A honosított erőforrások elem rendelkezik egy azonosítóval, amely a honosított erőforrások egyedi azonosítására szolgál. Ezt a termékazonosító később használják a [Content definition](contentdefinitions.md) elemben.

A honosított erőforrások elemeit a tartalom definíciójában és a támogatni kívánt nyelveken konfigurálhatja. Az angol és a spanyol nyelvű egyesített regisztrációs vagy bejelentkezési lapok testreszabásához az `LocalizedResources` elem lezárása után adja hozzá a következő elemeket `</SupportedLanguages>` .

> [!NOTE]
> Az alábbi példában az `#` egyes sorok elején a font szimbólumot vesszük fel, így könnyen megtalálhatja a honosított címkéket a képernyőn.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>A tartalom definíciójának szerkesztése a honosítással

Illessze be a ContentDefinitions elem teljes tartalmát, amelyet a BuildingBlocks elem gyermekeiként másolt.

A következő példában az angol (en) és a spanyol (es) egyéni sztringek hozzáadódnak a regisztrációs vagy bejelentkezési laphoz, valamint a helyi fiók regisztrációs lapjához. Az egyes **LocalizedResourcesReference** **LocalizedResourcesReferenceId** megegyeznek a területi beállításokkal, de az azonosítóhoz bármilyen karakterláncot használhat. Minden nyelv és oldal kombináció esetében mutasson a korábban létrehozott megfelelő **LocalizedResources** .

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>A frissített egyéni szabályzat feltöltése és tesztelése

### <a name="upload-the-custom-policy"></a>Egyéni szabályzat feltöltése

1. Mentse a bővítmények fájlt.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza az **egyéni házirend feltöltése** lehetőséget.
1. Töltse fel a korábban módosított kiterjesztéseket tartalmazó fájlt.

### <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a **Futtatás most** használatával

1. Válassza ki a feltöltött szabályzatot, majd válassza a **Futtatás most** lehetőséget.
1. Látnia kell a honosított regisztrációs vagy bejelentkezési oldalt.
1. Kattintson a regisztrációs hivatkozásra, és tekintse meg a honosított regisztrációs lapot.
1. A böngésző alapértelmezett nyelvének átváltása spanyolra. A lekérdezési karakterlánc paramétert is hozzáadhatja `ui_locales` az engedélyezési kérelemhez. Például: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>További információ

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Oldal felhasználói felületének testreszabása feliratok felülbírálásként

Ha engedélyezi a nyelvi testreszabást, az oldal felhasználói felületének testreszabását használó címkék korábbi szerkesztési adatai megmaradnak az angol (en) JSON-fájlban. A nyelvi testreszabási nyelvi erőforrások feltöltésével továbbra is módosíthatja a címkéket és más karakterláncokat.

::: zone-end

### <a name="up-to-date-translations"></a>Naprakész fordítások

A Microsoft elkötelezte magát arra, hogy a legnaprakészebb fordításokat biztosítson a használathoz. A Microsoft folyamatosan fejleszti a fordításokat, és megfelel az Ön számára. A Microsoft azonosítja a hibákat és a globális terminológiában bekövetkező változásokat, és olyan frissítéseket tesz elérhetővé, amelyek zökkenőmentesen fognak működni a felhasználói folyamat során.

### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása

A Microsoft jelenleg nem nyújt támogatást a jobbról balra író nyelvekhez. Ezt az egyéni területi beállítások és a CSS használatával végezheti el a karakterláncok megjelenítésének megváltoztatásához. Ha erre a szolgáltatásra van szüksége, szavazzon rá az [Azure-visszajelzésre](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Közösségi identitás-szolgáltatói fordítások

A Microsoft biztosítja a `ui_locales` OIDC paramétert a közösségi bejelentkezésekhez. De egyes közösségi identitás-szolgáltatók, például a Facebook és a Google is, ne tartsák tiszteletben őket.

### <a name="browser-behavior"></a>Böngésző viselkedése

A Chrome és a Firefox is a beállított nyelvre irányuló kérelem. Ha ez egy támogatott nyelv, az alapértelmezés szerint megjelenik. A Microsoft Edge jelenleg nem kér nyelvet, és egyenesen az alapértelmezett nyelvre lép.

## <a name="supported-languages"></a>Támogatott nyelvek

A Azure AD B2C a következő nyelvek támogatását tartalmazza. A felhasználói folyamat nyelveit Azure AD B2C biztosítjuk. A multi-Factor Authentication (MFA) értesítési nyelveit az [Azure ad MFA](../active-directory/authentication/concept-mfa-howitworks.md)nyújtja.

| Nyelv              | Nyelvkód | Felhasználói folyamatok         | MFA-értesítések  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arab                | AR            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Bolgár             | BG            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Bangla                | Mrd USD            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| Katalán               | CA            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Cseh                 | CS            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Dán                | da            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Német                | de            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Görög                 | el            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Angolul               | hu            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| spanyol               | es            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Észt              | et            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Baszk                | EU            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| finn               | Fi            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Francia                | fr            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Gallego              | GL            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| gudzsaráti              | Gu            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| héber                | ő            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | szia            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Horvát              | ó            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Magyar             | hu            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Indonéz            | id            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Olasz               | Ez            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Japán              | Japán            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Kazak                | KK            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| kannada               | KN            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| Koreai                | Ko            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Litván            | lt            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Lett               | lv            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| malajálam             | ml            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| marathi               | Mr            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| Maláj                 | MS            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Norvég bokmal      | NB            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| Holland                 | nl            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Norvég             | nem            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| pandzsábi               | PA            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| Lengyel                | pl            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Portugál - Brazília   | pt-br         | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Portugál - Portugália | pt-pt         | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Román              | ro            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Orosz               | ru            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Szlovák                | sk            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Szlovén             | SL            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Szerb – cirillbetűs    | SR-cryl-cs    | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Szerb – latinbetűs       | SR-Latn-cs    | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| svéd               | Sv            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| tamil                 | ta            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| telugu                | ttelepítése            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) |
| Thai                  | adik            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Török               | TR            | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Ukrán             | uk            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Vietnámi            | VI            | ![X, jelezve: nem.](./media/user-flow-language-customization/no.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Kínai – egyszerűsített  | zh-Hans       | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |
| Kínai – hagyományos | zh-Hant       | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) | ![Zöld pipa.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Következő lépések

::: zone pivot="b2c-user-flow"

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával Azure Active Directory B2Cban](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- További információ a [honosítási](localization.md) elemről a IEF-hivatkozásban.
- Tekintse meg a Azure AD B2Cban elérhető [honosítási karakterlánc-azonosítók](localization-string-ids.md) listáját.

::: zone-end 
