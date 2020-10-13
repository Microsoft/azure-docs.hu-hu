---
title: Honosítási karakterlánc-azonosítók – Azure Active Directory B2C | Microsoft Docs
description: Határozza meg az API. signuporsignin AZONOSÍTÓJÚ tartalom-definíció azonosítóit a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f1b3e12bcf744ad0d762f686a908a01239fc4323
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949241"
---
# <a name="localization-string-ids"></a>Honosítási sztringazonosítók

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy a felhasználói útvonalakhoz tartozó szabályzatban több területi beállítást vagy nyelvet támogasson. Ez a cikk a szabályzatában használható honosítási azonosítók listáját tartalmazza. A felhasználói felület honosításának megismeréséhez lásd: [honosítás](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Regisztrációs vagy bejelentkezési oldal elemei

A következő azonosítókat használják egy `api.signuporsignin` , a és az [önérvényesített technikai profilhoz](self-asserted-technical-profile.md)tartozó tartalom-definícióhoz.

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **local_intro_email** | Bejelentkezés meglévő fiókkal |
| **logonIdentifier_email** | E-mail-cím |
| **requiredField_email** | Adja meg az e-mail-címét |
| **invalid_email** | Adjon meg egy érvényes e-mail-címet |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' '*+/=? ^ _ \` { \| } ~-] + @ [a-Za-z0-9-] + (?: \\ . [ a-zA-Z0-9-] +)*$ |
| **local_intro_username** | Jelentkezzen be a felhasználónevével |
| **logonIdentifier_username** | Felhasználónév |
| **requiredField_username** | Adja meg a felhasználónevét |
| **alaphelyzetbe állítása** | Jelszó |
| **requiredField_password** | Adja meg a jelszavát |
| **invalid_password** | A beírt jelszó nem a várt formátumban van. |
| **forgotpassword_link** | Elfelejtette a jelszavát? |
| **createaccount_intro** | Nincs fiókja? |
| **createaccount_link** | Regisztráljon most |
| **divider_title** | OR |
| **cancel_message** | A felhasználó elfelejtette a jelszavát |
| **button_signin** | Bejelentkezés |
| **social_intro** | Jelentkezzen be a közösségi fiókjával |
  **remember_me** |Bejelentkezve szeretnék maradni|
| **unknown_error** | Hiba történt a bejelentkezés során. Próbálkozzon újra később. |

A következő példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs vagy bejelentkezési oldalon:

![Regisztrációs vagy bejelentkezési oldal UX-elemek](./media/localization-string-ids/localization-susi.png)

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
| **UserMessageIfClaimsPrincipalDoesNotExist** | Úgy tűnik, hogy nem találja a fiókját. |
| **UserMessageIfOldPasswordUsed** | Úgy tűnik, hogy régi jelszót használt. |
| **DefaultMessage** | Érvénytelen felhasználónév vagy jelszó. |
| **UserMessageIfUserAccountDisabled** | A fiók zárolva van. A zárolás feloldásához forduljon a támogatási szolgálathoz, majd próbálkozzon újra. |
| **UserMessageIfUserAccountLocked** | A fiók átmenetileg zárolva van, hogy megakadályozza a jogosulatlan használatot. Próbálkozzon újra később. |
| **AADRequestsThrottled** | Jelenleg túl sok kérés van. Várjon egy ideig, és próbálkozzon újra. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Regisztráció és saját maga által érvényesített lapok felhasználói felületi elemei

A következő a tartalom-definíció azonosítóját `api.localaccountsignup` , vagy bármely olyan tartalom-definíciót tartalmaz, amely az-val kezdődik, például a és a és az `api.selfasserted` `api.selfasserted.profileupdate` `api.localaccountpasswordreset` [önérvényesített technikai profil](self-asserted-technical-profile.md).

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **ver_sent** | Az ellenőrző kód a következőre lett küldve: |
| **ver_but_default** | Alapértelmezett |
| **cancel_message** | A felhasználó megszakította az önérvényesített információk megadását |
| **preloader_alt** | várj, kérlek |
| **ver_but_send** | Ellenőrző kód küldése |
| **alert_yes** | Igen |
| **error_fieldIncorrect** | Egy vagy több mező kitöltése helytelen. Ellenőrizze a bejegyzéseket, és próbálkozzon újra. |
| **év** | Év |
| **verifying_blurb** | Várjon, amíg feldolgozzuk az adatokat. |
| **button_cancel** | Mégse |
| **ver_fail_no_retry** | Túl sok helytelen kísérlet történt. Próbálkozzon újra később. |
| **hónap** | Hónap |
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
| **alert_no** | Nem |
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
| **countryList** | Tekintse meg [az országok listáját](#countries-list). |
| **error_448** | A megadott telefonszám nem érhető el. |
| **error_449** | A felhasználó túllépte az újrapróbálkozási kísérletek számát. |
| **verification_code_input_placeholder_text** | Ellenőrző kód |

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be az MFA-regisztráció lapon:

![Telefonos faktor-hitelesítés – UX-elemek](./media/localization-string-ids/localization-mfa1.png)

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be az MFA-érvényesítési lapon:

![A telefonos faktor hitelesítése UX-elemek](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Ellenőrzési képernyő vezérlő felhasználói felület elemei

Az alábbi azonosítók egy [ellenőrző képernyő vezérlőelemhez](display-control-verification.md) tartoznak.

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|verification_control_but_change_claims |Módosítás |
|verification_control_fail_send_code |Nem sikerült elküldeni a kódot, próbálkozzon újra később. |
|verification_control_fail_verify_code |A kód ellenőrzése sikertelen volt, próbálkozzon újra később. |
|verification_control_but_send_code |Kód küldése |
|verification_control_but_send_new_code |Új kód küldése |
|verification_control_but_verify_code |Kód ellenőrzése |
|verification_control_code_sent| Az ellenőrző kód elküldése megtörtént. Másolja az alábbi beviteli mezőbe. |

### <a name="example"></a>Példa

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


### <a name="example"></a>Példa

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

## <a name="azure-mfa-error-messages"></a>Azure MFA-hibaüzenetek

Az [Azure MFA technikai profilhoz](multi-factor-auth-technical-profile.md) tartozó hibaüzenetek azonosítói a következők:

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Nem lehet SMS-t küldeni a telefonra, próbálkozzon másik telefonszámmal. |
|UserMessageIfInvalidFormat | A telefonszám formátuma érvénytelen, javítsa ki, és próbálkozzon újra.|
|UserMessageIfMaxAllowedCodeRetryReached | Nem megfelelő kód van megadva túl sokszor, próbálkozzon újra később.|
|UserMessageIfServerError | Az MFA szolgáltatás nem használható, próbálkozzon újra később.|
|UserMessageIfThrottled | A kérelem szabályozása megtörtént, próbálkozzon újra később.|
|UserMessageIfWrongCodeEntered|Helytelen kód van megadva, próbálkozzon újra.|

### <a name="example"></a>Példa

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


### <a name="example"></a>Példa

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

### <a name="example"></a>Példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
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

### <a name="example"></a>Példa

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="countries-list"></a>Országok listája

A multi-Factor Authentication által használt countryList-értékek a következők.

```JSON
{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}
```

## <a name="next-steps"></a>További lépések

A honosítási példákat a következő cikkekben találja:

- [Nyelvi Testreszabás egyéni házirenddel Azure Active Directory B2C](custom-policy-localization.md)
- [Nyelvi Testreszabás felhasználói folyamatokkal Azure Active Directory B2C](user-flow-language-customization.md)
