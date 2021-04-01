---
title: Vezérlőelem-hivatkozás megjelenítése
titleSuffix: Azure AD B2C
description: Azure AD B2C megjelenítési vezérlőkre mutató hivatkozás. A megjelenítési vezérlők használatával testreszabhatja az egyéni házirendekben definiált felhasználói útvonalakat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97387054"
---
# <a name="display-controls"></a>Vezérlőelemek megjelenítése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **megjelenítési vezérlő** egy olyan felhasználói felületi elem, amely speciális funkciókkal rendelkezik, és együttműködik a Azure Active Directory B2C (Azure ad B2C) háttér-szolgáltatással. Lehetővé teszi a felhasználó számára, hogy műveleteket hajtson végre az oldalon, amely a háttérbeli [érvényesítési technikai profilt](validation-technical-profile.md) hívja meg. A megjelenítési vezérlők az oldalon jelennek meg, és egy [önérvényesített technikai profil](self-asserted-technical-profile.md)hivatkozik rá.

Az alábbi ábrán egy önjelölt regisztrációs oldal látható, amelyben két megjelenítési vezérlőelem van, amelyek egy elsődleges és egy másodlagos e-mail-címet érvényesítenek.

![Példa megjelenített megjelenítési vezérlőelemre](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Előfeltételek

 Az [önérvényesített műszaki profilok](self-asserted-technical-profile.md) [metaadatok](self-asserted-technical-profile.md#metadata) szakaszában a hivatkozott [ContentDefinition](contentdefinitions.md) `DataUri` 2.0.0 vagy újabb értékűnek kell lennie. Például:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Megjelenítési vezérlők definiálása

A **DisplayControl** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A megjelenítési vezérlőelemhez használt azonosító. Erre [hivatkozhat](#referencing-display-controls). |
| UserInterfaceControlType | Yes | A megjelenítési vezérlőelem típusa Jelenleg támogatott a [VerificationControl](display-control-verification.md) |

A **DisplayControl** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Szabályzattípushoz | 0:1 | A **szabályzattípushoz** a felhasználó által összegyűjtött jogcímek értékének előre való feltöltésére szolgálnak. További információ: [szabályzattípushoz](technicalprofiles.md#input-claims) elem. |
| DisplayClaims | 0:1 | A **DisplayClaims** a felhasználótól gyűjtött jogcímek ábrázolására szolgálnak. További információ: [DisplayClaim](technicalprofiles.md#displayclaim) elem.|
| OutputClaims | 0:1 | A **OutputClaims** a **DisplayControl** ideiglenesen menteni kívánt jogcímeket jelölik. További információ: [OutputClaims](technicalprofiles.md#output-claims) elem.|
| Műveletek | 0:1 | A **műveletekkel** listázhatja az ellenőrzési technikai profilokat, amelyeket a rendszer az előtér felhasználói műveleteihez hív meg. |

### <a name="input-claims"></a>Bemeneti jogcímek

A megjelenítési vezérlőkben a **szabályzattípushoz** elemek használatával előre kitöltheti a felhasználók által az oldalon gyűjtött jogcímek értékét. Bármely **InputClaimsTransformations** megadható az önérvényesített technikai profilban, amely erre a megjelenítési vezérlőelemre hivatkozik.

A következő példa előre feltölti az e-mail-címet, hogy ellenőrizni lehessen a már meglévő címmel.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Jogcímek megjelenítése

A megjelenítési vezérlők mindegyik típusának különböző megjelenítési jogcímeket, [kimeneti jogcímeket](#output-claims)és végrehajtandó [műveleteket](#display-control-actions) kell megadnia.

Az [önérvényesített technikai profilban](self-asserted-technical-profile.md#display-claims)definiált **megjelenítési jogcímek** esetében a megjelenítési jogcímek a felhasználó által a megjelenítési vezérlőn belül összegyűjtött jogcímeket jelölik. A hivatkozott **claimType** elemnek meg kell adnia a Azure ad B2C által támogatott felhasználói beviteli típushoz tartozó **UserInputType** elemet, például: `TextBox` vagy `DropdownSingleSelect` . Ha egy **műveletnek** egy megjelenítési jogcím értékét kell megadnia, állítsa be a **kötelező** attribútumot arra, hogy `true` kényszerítse a felhasználót az adott megjelenítési jogcím értékének megadására.

Bizonyos megjelenítési jogcímek a megjelenítési vezérlők bizonyos típusaihoz szükségesek. Például **VerificationCode** szükséges a **VerificationControl** típusú megjelenítési vezérlőelemhez. A **ControlClaimType** attribútum használatával megtudhatja, hogy melyik DisplayClaim van kijelölve ehhez a szükséges jogcímhez. Például:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Kimeneti jogcímek

A megjelenítési vezérlő **kimeneti jogcímeit** a rendszer nem küldi el a következő előkészítési lépéshez. Ezeket a rendszer ideiglenesen csak az aktuális megjelenítési vezérlő munkamenet számára menti. Ezek az ideiglenes jogcímek megoszthatók ugyanazon megjelenítési vezérlő különböző műveletei között.

Ha ki szeretné próbálni a kimeneti jogcímeket a következő előkészítési lépésre, használja a tényleges önérvényesített technikai profil **OutputClaims** , amely erre a megjelenítési vezérlőelemre hivatkozik.

### <a name="display-control-actions"></a>Vezérlési műveletek megjelenítése

A megjelenítési vezérlők **műveletei** olyan eljárások, amelyek a Azure ad B2C háttérben történnek, amikor egy felhasználó egy bizonyos műveletet hajt végre az ügyfél oldalán (a böngészőben). Például az a művelet, amelyet akkor kell végrehajtania, amikor a felhasználó kiválaszt egy gombot az oldalon.

Egy művelet meghatározza az **érvényesítési műszaki profilok** listáját. Ezek a megjelenítési vezérlők egy vagy több megjelenítési jogcíme érvényesítésére szolgálnak. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, és hibát jelez a felhasználó számára. A **ContinueOnError**, a **ContinueOnSuccess** és az **előfeltételeket** a Display Control művelethez hasonlóan használhatja, mint ahogyan azokat az [érvényesítési technikai](validation-technical-profile.md) profilokban használják egy önjelölt technikai profilban.

#### <a name="actions"></a>Műveletek

A **Actions** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Művelet | 1: n | A végrehajtandó műveletek listája. |

#### <a name="action"></a>Művelet

A **műveleti** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A művelet típusa. Lehetséges értékek: `SendCode` vagy `VerifyCode` . Az `SendCode` érték egy kódot küld a felhasználónak. Ez a művelet két érvényesítési technikai profilt tartalmazhat: az egyiket a kód létrehozásához, az egyiket pedig a küldéshez. Az `VerifyCode` érték ellenőrzi a felhasználó által beírt kódot a beviteli szövegmezőben. |

A **műveleti** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Azon műszaki profilok azonosítói, amelyek a hivatkozó technikai profil megjelenítési jogcímek egy részének vagy egészének ellenőrzésére szolgálnak. A hivatkozott technikai profil összes bemeneti jogcímének szerepelnie kell a hivatkozó technikai profil megjelenítési jogcímeiben. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

A **ValidationClaimsExchange** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | A hivatkozó technikai profil megjelenítési jogcímeinek érvényesítésére szolgáló technikai profil. |

A **ValidationTechnicalProfile** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Yes | A házirend vagy a szülő házirendben már definiált technikai profil azonosítója. |
|ContinueOnError|No| Azt jelzi, hogy a további érvényesítési műszaki profilok érvényesítése folytatódjon-e, ha az érvényesítési technikai profil hibát jelez. Lehetséges értékek: `true` vagy `false` (alapértelmezés szerint a további ellenőrzési profilok feldolgozása leáll, és a rendszer hibaüzenetet ad vissza). |
|ContinueOnSuccess | No | Azt jelzi, hogy a további ellenőrzési profilok érvényesítése folytatódjon-e, ha az érvényesítési technikai profil sikeres. Lehetséges értékek: `true` vagy `false` . Az alapértelmezett érték az `true` , ami azt jelenti, hogy a további ellenőrzési profilok feldolgozása továbbra is fennáll. |

A **ValidationTechnicalProfile** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltételei | 0:1 | Azon előfeltételek listája, amelyeknek meg kell felelniük az érvényesítési technikai profil végrehajtásához. |

Az **előfeltétel** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Az előfeltételként végrehajtandó ellenőrzés vagy lekérdezés típusa. Lehetséges értékek: `ClaimsExist` vagy `ClaimEquals` . `ClaimsExist` Megadja, hogy a rendszer végrehajtja a műveleteket, ha a megadott jogcímek a felhasználó aktuális jogcímek készletében vannak. `ClaimEquals` Megadja, hogy a rendszer végrehajtja a műveleteket, ha a megadott jogcím létezik, és annak értéke megegyezik a megadott értékkel. |
| `ExecuteActionsIf` | Yes | Azt jelzi, hogy az előfeltételben szereplő műveleteket kell-e végrehajtani, ha a teszt igaz vagy hamis. |

Az **előfeltétel** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Érték | 1: n | Az ellenőrzés által használt adatértékek. Ha ez a jelölőnégyzet be van jelölve `ClaimsExist` , akkor ez a mező egy ClaimTypeReferenceId határoz meg a lekérdezéshez. Ha az ellenőrzési típus értéke `ClaimEquals` , ez a mező egy ClaimTypeReferenceId határoz meg a lekérdezéshez. Itt adhatja meg a másik érték elemben ellenőrizendő értéket.|
| Művelet | 1:1 | Az a művelet, amelyet akkor kell elvégezni, ha az előkészítési lépésen belüli előfeltétel-ellenőrzési érték igaz. A **művelet** értéke `SkipThisValidationTechnicalProfile` , amely megadja, hogy a társított érvényesítési technikai profil nem hajtható végre. |

Az alábbi példa az e-mail-címet az [Azure ad SSPR technikai profiljának](aad-sspr-technical-profile.md)használatával küldi el és ellenőrzi.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

A következő példa egy kódot küld e-mailben vagy SMS-ben, a felhasználó az **mfaType** jogcím kiválasztásával, előfeltételekkel.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>A megjelenítési vezérlőkre hivatkozik

A megjelenítési vezérlőkre az [önérvényesített technikai profil](self-asserted-technical-profile.md) [megjelenítési jogcímeiben](self-asserted-technical-profile.md#display-claims) hivatkozunk.

Például:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Következő lépések

A megjelenítési vezérlőt használó minták esetében lásd: 

- [Egyéni e-mail-ellenőrzés a mailjet](custom-email-mailjet.md)
- [Egyéni e-mail-ellenőrzés a SendGrid](custom-email-sendgrid.md)
