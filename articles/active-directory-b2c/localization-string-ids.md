---
title: Honosítási karakterlánc-azonosítók – Azure Active Directory B2C | Microsoft Docs
description: Határozza meg az API. signuporsignin AZONOSÍTÓJÚ tartalom-definíció azonosítóit a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b60b447d8302b89813ca462c3220603ef926eb26
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631183"
---
# <a name="localization-string-ids"></a>Honosítási sztringazonosítók

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy a felhasználói útvonalakhoz tartozó szabályzatban több területi beállítást vagy nyelvet támogasson. Ez a cikk a szabályzatában használható honosítási azonosítók listáját tartalmazza. A felhasználói felület honosításának megismeréséhez lásd: [honosítás](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Regisztrációs vagy bejelentkezési oldal elemei

A következő azonosítókat használják egy `api.signuporsignin` , a és az [önérvényesített technikai profilhoz](self-asserted-technical-profile.md)tartozó tartalom-definícióhoz.

| ID (Azonosító) | Alapértelmezett érték | Lapelrendezés verziója |
| -- | ------------- | ------ |
| **forgotpassword_link** | Elfelejtette a jelszavát? | `All` |
| **createaccount_intro** | Nincs fiókja? | `All` |
| **button_signin** | Bejelentkezés | `All` |
| **social_intro** | Jelentkezzen be a közösségi fiókjával | `All` |
| **remember_me** |Bejelentkezés megtartása. | `All` |
| **unknown_error** | Hiba történt a bejelentkezés során. Próbálkozzon újra később. | `All` |
| **divider_title** | OR | `All` |
| **local_intro_email** | Bejelentkezés meglévő fiókkal | `< 2.0.0` |
| **logonIdentifier_email** | E-mail-cím | `< 2.0.0` |
| **requiredField_email** | Adja meg az e-mail-címét | `< 2.0.0` |
| **invalid_email** | Adjon meg egy érvényes e-mail-címet | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' \* +/=? ^ \_ \` { \| } ~-] + @ [a-Za-z0-9-] + (?: \\ . [ a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Jelentkezzen be a felhasználónevével | `< 2.0.0` |
| **logonIdentifier_username** | Felhasználónév | `< 2.0.0` |
| **requiredField_username** | Adja meg a felhasználónevét | `< 2.0.0` |
| **alaphelyzetbe állítása** | Jelszó | `< 2.0.0` |
| **requiredField_password** | Adja meg a jelszavát | `< 2.0.0` |
| **createaccount_link** | Regisztráljon most | `< 2.0.0` |
| **cancel_message** | A felhasználó elfelejtette a jelszavát | `< 2.0.0` |
| **invalid_password** | A beírt jelszó nem a várt formátumban van. | `< 2.0.0` |
| **createaccount_one_link** | Regisztráljon most | `>= 2.0.0` |
| **createaccount_two_links** | Regisztráció a {0} vagy a {1} | `>= 2.0.0` |
| **createaccount_three_links** | Regisztráció a {0} , {1} vagy a {2} | `>= 2.0.0` |
| **local_intro_generic** | Jelentkezzen be a {0} | `>= 2.1.0` |
| **requiredField_generic** | Adja meg a {0} | `>= 2.1.0` |
| **invalid_generic** | Adjon meg érvényes értéket {0} | `>= 2.1.1` |
| **vámtarifaszám** | Bejelentkezés | `>= 2.1.1` |


> [!NOTE]
> * A (z {0} ) értékkel automatikusan kitöltik a helyőrzőket `DisplayName` `ClaimType` . 
> * A honosítással kapcsolatos információkért `ClaimType` lásd: [regisztráció vagy bejelentkezési példa](#signupsigninexample).

A következő példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs vagy bejelentkezési oldalon:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="A regisztrációs vagy bejelentkezési oldal U X elemeket megjelenítő képernyőkép.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Regisztrációs vagy bejelentkezési identitás-szolgáltatók

Az Identity Providers AZONOSÍTÓját a felhasználói út  **ClaimsExchange** eleme konfigurálja. Az identitás-szolgáltató címének honosítása érdekében a **ElementType** a értékre van állítva `ClaimsProvider` , míg a **elemtípusú** a (z) azonosítójának értéke `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az alábbi példa a Facebook-identitás szolgáltatóját az Arab nyelvre honosítja:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Regisztrációs vagy bejelentkezési hibaüzenetek

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Helytelen a jelszó. |
| **UserMessageIfPasswordExpired**| A jelszó lejárt.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Úgy tűnik, hogy nem találja a fiókját. |
| **UserMessageIfOldPasswordUsed** | Úgy tűnik, hogy régi jelszót használt. |
| **DefaultMessage** | Érvénytelen felhasználónév vagy jelszó. |
| **UserMessageIfUserAccountDisabled** | A fiók zárolva van. A zárolás feloldásához forduljon a támogatási szolgálathoz, majd próbálkozzon újra. |
| **UserMessageIfUserAccountLocked** | A fiók átmenetileg zárolva van, hogy megakadályozza a jogosulatlan használatot. Próbálkozzon újra később. |
| **AADRequestsThrottled** | Jelenleg túl sok kérés van. Várjon egy ideig, és próbálkozzon újra. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Regisztrációs vagy bejelentkezési példa

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Regisztráció és saját maga által érvényesített lapok felhasználói felületi elemei

A következő a tartalom-definíció azonosítóját `api.localaccountsignup` , vagy bármely olyan tartalom-definíciót tartalmaz, amely az-val kezdődik, például a és a és az `api.selfasserted` `api.selfasserted.profileupdate` `api.localaccountpasswordreset` [önérvényesített technikai profil](self-asserted-technical-profile.md).

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **ver_sent** | Az ellenőrző kód a következőre lett küldve: |
| **ver_but_default** | Alapértelmezett |
| **cancel_message** | A felhasználó megszakította az önérvényesített információk megadását |
| **preloader_alt** | várj, kérlek |
| **ver_but_send** | Ellenőrző kód küldése |
| **alert_yes** | Yes |
| **error_fieldIncorrect** | Egy vagy több mező kitöltése helytelen. Ellenőrizze a bejegyzéseket, és próbálkozzon újra. |
| **év** | Year |
| **verifying_blurb** | Várjon, amíg feldolgozzuk az adatokat. |
| **button_cancel** | Mégse |
| **ver_fail_no_retry** | Túl sok helytelen kísérlet történt. Próbálkozzon újra később. |
| **hónap** | Month (hónap) |
| **ver_success_msg** | Az E-mail cím ellenőrizve. Most már folytathatja. |
| **hónapok** | Január, február, március, április, május, június, július, augusztus, szeptember, október, november, december |
| **ver_fail_server** | Nem sikerül ellenőrizni az e-mail-címét. Adjon meg egy érvényes e-mail-címet, és próbálkozzon újra. |
| **error_requiredFieldMissing** | Hiányzik egy kötelező mező. Töltse ki az összes kötelező mezőt, és próbálkozzon újra. |
| **initial_intro** | Adja meg a következő adatokat. |
| **ver_but_resend** | Új kód küldése |
| **button_continue** | Létrehozás |
| **error_passwordEntryMismatch** | A jelszó-beviteli mezők nem egyeznek. Ugyanazt a jelszót adja meg mindkét mezőben, majd próbálkozzon újra. |
| **ver_incorrect_format** | Helytelen formátumú. |
| **ver_but_edit** | E-mail módosítása |
| **ver_but_verify** | Kód ellenőrzése |
| **alert_no** | No |
| **ver_info_msg** | Az ellenőrző kód el lett küldve a Beérkezett üzenetek mappájába. Másolja az alábbi beviteli mezőbe. |
| **nap** | Nap |
| **ver_fail_throttled** | Túl sok kérelem érkezett az e-mail-cím ellenőrzéséhez. Várjon egy kicsit, és próbálkozzon újra. |
| **helplink_text** | Mi ez? |
| **ver_fail_retry** | A kód helytelen. Próbálkozzon újra. |
| **alert_title** | A részletek megadásának megszakítása |
| **required_field** | Ezt az információt kötelező megadni. |
| **alert_message** | Biztosan meg kívánja szakítani a részletek beírását? |
| **ver_intro_msg** | Ellenőrzés szükséges. Kattintson a Küldés gombra. |
| **ver_input** | Ellenőrző kód |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Regisztráció és saját maga által vezérelt lapok hibaüzenetei

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Már létezik a megadott AZONOSÍTÓJÚ felhasználó. Válasszon másikat. |
| **UserMessageIfClaimNotVerified** | A jogcím nincs ellenőrizve: {0} |
| **UserMessageIfIncorrectPattern** | Helytelen minta a következőhöz: {0} |
| **UserMessageIfMissingRequiredElement** | Hiányzó kötelező elem: {0} |
| **UserMessageIfValidationError** | Hiba történt az érvényesítés során: {0} |
| **UserMessageIfInvalidInput** | {0} Érvénytelen bemenettel rendelkezik. |
| **ServiceThrottled** | Jelenleg túl sok kérés van. Várjon egy ideig, és próbálkozzon újra. |

A következő példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs oldalon:

![Regisztrációs oldal a felhasználói felületi elemek neveivel megcímkézve](./media/localization-string-ids/localization-sign-up.png)

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs oldalon, miután a felhasználó az ellenőrző kód küldése gombra kattint:

![Regisztrációs oldal e-mail ellenőrzése UX-elemek](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Regisztráció és saját maga által érvényesített lapok – példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>A telefonos faktor hitelesítés lapja felhasználói felület elemei

A következő azonosítók a tartalom-definíció azonosítóját `api.phonefactor` és a [telefonos faktor technikai profilt](phone-factor-technical-profile.md)használják.

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **button_verify** | hívj |
| **country_code_label** | Országkód |
| **cancel_message** | A felhasználó megszakította a többtényezős hitelesítést |
| **text_button_send_second_code** | új kód küldése |
| **code_pattern** | \\n{6} |
| **intro_mixed** | A következő számú rekordot vesszük igénybe. A hitelesítéshez SMS-ben vagy telefonon keresztül küldhetünk egy kódot. |
| **intro_mixed_p** | A következő számok szerepelnek a rekordban. Válassza ki azt a számot, amely telefonon vagy SMS-ben küldünk egy kódot a hitelesítéshez. |
| **button_verify_code** | Kód ellenőrzése |
| **requiredField_code** | Adja meg a kapott ellenőrző kódot |
| **invalid_code** | Adja meg a kapott 6 számjegyű kódot |
| **button_cancel** | Mégse |
| **local_number_input_placeholder_text** | Telefonszám |
| **button_retry** | Ismét |
| **alternative_text** | Nincs telefonom |
| **intro_phone_p** | A következő számok szerepelnek a rekordban. Válassza ki azt a számot, amelyet telefonon használhat a hitelesítéshez. |
| **intro_phone** | A következő számú rekordot vesszük igénybe. A rendszer telefonon hitelesíti Önt. |
| **enter_code_text_intro** | Adja meg az alábbi ellenőrző kódot, vagy  |
| **intro_entry_phone** | Adja meg az alábbi telefonszámot, amelyet telefonon használhat a hitelesítéshez. |
| **intro_entry_sms** | Írjon be egy számot lent, hogy SMS-ben küldhetünk egy kódot a hitelesítéshez. |
| **button_send_code** | Kód küldése |
| **invalid_number** | Adjon meg egy érvényes telefonszámot |
| **intro_sms** | A következő számú rekordot vesszük igénybe. SMS-ben küldünk egy kódot a hitelesítéshez. |
| **intro_entry_mixed** | Írjon be egy számot lent, hogy SMS-ben vagy telefonon keresztül küldjön egy kódot a hitelesítéshez. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |A következő számok szerepelnek a rekordban. Válassza ki azt a számot, amelynek használatával SMS-ben küldhetünk egy kódot a hitelesítéshez. |
| **requiredField_countryCode** | Válassza ki az országkódot |
| **requiredField_number** | Adja meg a telefonszámát |
| **country_code_input_placeholder_text** |Ország vagy régió |
| **number_label** | Telefonszám |
| **error_tryagain** | A megadott telefonszám foglalt vagy nem érhető el. Ellenőrizze a számot, és próbálkozzon újra. |
| **error_incorrect_code** | A beírt ellenőrző kód nem felel meg a rekordoknak. Próbálkozzon újra, vagy kérjen új kódot. |
| **countryList** | Tekintse meg [az országok listáját](#phone-factor-authentication-page-example). |
| **error_448** | A megadott telefonszám nem érhető el. |
| **error_449** | A felhasználó túllépte az újrapróbálkozási kísérletek számát. |
| **verification_code_input_placeholder_text** | Ellenőrző kód |

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be az MFA-regisztráció lapon:

![Telefonos faktor-hitelesítés – UX-elemek](./media/localization-string-ids/localization-mfa1.png)

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be az MFA-érvényesítési lapon:

![A telefonos faktor hitelesítése UX-elemek](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>A telefonos faktor hitelesítésének oldala – példa

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Ellenőrzési képernyő vezérlő felhasználói felület elemei

A következő egy [ellenőrzési megjelenítési vezérlő](display-control-verification.md) azonosítói a lapelrendezés 2.1.0 vagy újabb [verziójával](page-layout.md) .

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|intro_msg <sup>*</sup>| Ellenőrzés szükséges. Kattintson a Küldés gombra.|
|success_send_code_msg | Az ellenőrző kód elküldése megtörtént. Másolja az alábbi beviteli mezőbe.|
|failure_send_code_msg | Nem sikerül ellenőrizni az e-mail-címét. Adjon meg egy érvényes e-mail-címet, és próbálkozzon újra.|
|success_verify_code_msg | Az E-mail cím ellenőrizve. Most már folytathatja.|
|failure_verify_code_msg | Nem sikerül ellenőrizni az e-mail-címét. Próbálkozzon újra.|
|but_send_code | Ellenőrző kód küldése|
|but_verify_code | Kód ellenőrzése|
|but_send_new_code | Új kód küldése|
|but_change_claims | E-mail módosítása|

Megjegyzés: az `intro_msg` elem rejtett, és nem jelenik meg az önérvényesített oldalon. Ennek láthatóvá tételéhez használja a [HTML-customiztion](customize-ui-with-html.md) egymásra épülő stíluslapok. Például:
    
```css
.verificationInfoText div{display: block!important}
```

### <a name="verification-display-control-example"></a>Példa ellenőrző megjelenítési vezérlőelemre

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Ellenőrzési megjelenítés vezérlő felhasználói felület elemei (elavult)

A következő azonosítók egy [ellenőrzési megjelenítési vezérlőhöz](display-control-verification.md) tartoznak, [és a lapelrendezés 2.0.0](page-layout.md) .

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|verification_control_but_change_claims |Módosítás |
|verification_control_fail_send_code |Nem sikerült elküldeni a kódot, próbálkozzon újra később. |
|verification_control_fail_verify_code |A kód ellenőrzése sikertelen volt, próbálkozzon újra később. |
|verification_control_but_send_code |Kód küldése |
|verification_control_but_send_new_code |Új kód küldése |
|verification_control_but_verify_code |Kód ellenőrzése |
|verification_control_code_sent| Az ellenőrző kód elküldése megtörtént. Másolja az alábbi beviteli mezőbe. |

### <a name="verification-display-control-example-deprecated"></a>Ellenőrzési megjelenítési vezérlő – példa (elavult)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Rest-szolgáltatás hibaüzenetei

A [Rest-szolgáltatás technikai profiljának](restful-technical-profile.md) hibaüzenetei az alábbi azonosítók:

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Nem sikerült kapcsolatot létesíteni a REST-szolgáltatás végpontja felé. Rest-szolgáltatás URL-címe: {0} |
|UserMessageIfCircuitOpen | {0} Rest-szolgáltatás URL-címe: {1} |
|UserMessageIfDnsResolutionFailed | Nem sikerült feloldani a REST-szolgáltatás végpontjának állomásnevét. Rest-szolgáltatás URL-címe: {0} |
|UserMessageIfRequestTimeout | Nem sikerült kapcsolatot létesíteni a REST-szolgáltatás végpontjának időtúllépési korlátján belül {0} . Rest-szolgáltatás URL-címe: {1} |


### <a name="restful-service-example"></a>Rest-szolgáltatás – példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Azure AD MFA-hibaüzenetek

Az [Azure ad MFA technikai profil](multi-factor-auth-technical-profile.md) hibaüzenetének azonosítói a következők:

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Nem lehet SMS-t küldeni a telefonra, próbálkozzon másik telefonszámmal. |
|UserMessageIfInvalidFormat | A telefonszám formátuma érvénytelen, javítsa ki, és próbálkozzon újra.|
|UserMessageIfMaxAllowedCodeRetryReached | Nem megfelelő kód van megadva túl sokszor, próbálkozzon újra később.|
|UserMessageIfServerError | Az MFA szolgáltatás nem használható, próbálkozzon újra később.|
|UserMessageIfThrottled | A kérelem szabályozása megtörtént, próbálkozzon újra később.|
|UserMessageIfWrongCodeEntered|Helytelen kód van megadva, próbálkozzon újra.|

### <a name="azure-ad-mfa-example"></a>Azure AD MFA-példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

Az [Azure ad SSPR technikai profilhoz](aad-sspr-technical-profile.md) tartozó hibaüzenetek azonosítói a következők:

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|UserMessageIfChallengeExpired | A kód lejárt.|
|UserMessageIfInternalError | Az e-mail szolgáltatás belső hibát észlelt, próbálkozzon újra később.|
|UserMessageIfThrottled | Túl sok kérést küldött, próbálkozzon újra később.|
|UserMessageIfVerificationFailedNoRetry | Túllépte az ellenőrzési kísérletek maximális számát.|
|UserMessageIfVerificationFailedRetryAllowed | Az ellenőrzés sikertelen volt, próbálkozzon újra.|


### <a name="azure-ad-sspr-example"></a>Példa Azure AD-SSPR

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Egyszeri jelszó hibaüzenetei

A következő azonosítók egy [egyszeri jelszó technikai profiljának](one-time-password-technical-profile.md) hibaüzenetei

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |A megadott jelszó egyszeri ellenőrzése túllépte a kísérletek maximális számát |
|UserMessageIfSessionDoesNotExist |Egy jelszó-ellenőrzési munkamenet lejárt |
|UserMessageIfSessionConflict |Az egyszeri jelszó-ellenőrzési munkamenet ütközik |
|UserMessageIfInvalidCode |Az ellenőrzéshez megadott egyszeri jelszó helytelen |
|UserMessageIfVerificationFailedRetryAllowed |A kód helytelen. Próbálkozzon újra. | 

### <a name="one-time-password-example"></a>Egyszeri jelszó – példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>A jogcím-átalakítási hibaüzenetek

A jogcím-átalakítási hibaüzenetek azonosítói a következők:

| ID (Azonosító) | Jogcím-átalakítás | Alapértelmezett érték |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | A logikai jogcím értékének összehasonlítása nem sikerült a jogcím "inputClaim" típusához.| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | A jogcím-érték összehasonlítása sikertelen volt: a megadott bal oldali operandus nagyobb a jobb oldali operandusnál.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | A jogcím értékének összehasonlítása nem sikerült a (z) "OrdinalIgnoreCase" Stringcomparison argumentummal használatával.|

### <a name="claims-transformations-example"></a>Jogcím-átalakítások – példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Következő lépések

A honosítási példákat a következő cikkekben találja:

- [Nyelvi Testreszabás egyéni házirenddel Azure Active Directory B2C](language-customization.md)
- [Nyelvi Testreszabás felhasználói folyamatokkal Azure Active Directory B2C](language-customization.md)
