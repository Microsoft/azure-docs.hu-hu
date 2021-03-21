---
title: Honosítás – Azure Active Directory B2C
description: A Azure Active Directory B2C egyéni szabályzatának honosítási elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453121"
---
# <a name="localization-element"></a>Honosítási elem

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy a felhasználói útvonalakhoz tartozó szabályzatban több területi beállítást vagy nyelvet támogasson. A házirendek honosítási támogatása a következőket teszi lehetővé:

- Állítsa be a szabályzat támogatott nyelveinek explicit listáját, és válasszon ki egy alapértelmezett nyelvet.
- Adja meg a nyelvspecifikus karakterláncokat és gyűjteményeket.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

A **honosítási** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Engedélyezve | No | Lehetséges értékek: `true` vagy `false` . |

A **honosítási** elem a következő XML-elemeket tartalmazza

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | A támogatott nyelvek listája. |
| LocalizedResources | 0: n | A honosított erőforrások listája. |

## <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DefaultLanguage | Yes | A honosított erőforrások alapértelmezettként használandó nyelve. |
| MergeBehavior | No | Olyan értékek enumerálási értékei, amelyek össze vannak egyesítve egy olyan ClaimType, amely ugyanabban az azonosítóban szerepel egy szülő házirendben. Ezt az attribútumot akkor használja, ha az alapházirendben megadott jogcímet felülírja. Lehetséges értékek: `Append` , `Prepend` , vagy `ReplaceAll` . Az `Append` érték azt határozza meg, hogy az aktuális adatgyűjteményt a szülő házirendben megadott gyűjtemény végéhez kell fűzni. Az `Prepend` érték azt határozza meg, hogy a rendszer a szülő házirendben megadott gyűjtemény előtt hozzá lehessen adni a jelen lévő adatgyűjteményt. Az `ReplaceAll` érték azt határozza meg, hogy a fölérendelt házirendben definiált adatgyűjtést figyelmen kívül kell hagyni, ehelyett az aktuális házirendben definiált adatmennyiséget kell használnia. |

### <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Megjeleníti az RFC 5646-címkék nyelvi címkéjének megfelelő tartalmat a nyelvek azonosításához. |

## <a name="localizedresources"></a>LocalizedResources

A **LocalizedResources** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | Egy azonosító, amely a honosított erőforrások egyedi azonosítására szolgál. |

A **LocalizedResources** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Meghatározza a különböző kultúrákban található teljes gyűjteményeket. A gyűjtemények különböző számú elemet és különböző sztringeket tartalmazhatnak különböző kultúrákban. Ilyen gyűjtemény például a jogcím-típusokban megjelenő enumerálások. Például egy ország/régió lista jelenik meg a felhasználó számára egy legördülő listában. |
| LocalizedStrings | 0: n | Meghatározza az összes karakterláncot, kivéve a gyűjteményekben megjelenő karakterláncokat különböző kultúrákban. |

### <a name="localizedcollections"></a>LocalizedCollections

A **LocalizedCollections** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | A támogatott nyelvek listája. |

#### <a name="localizedcollection"></a>LocalizedCollection

A **LocalizedCollection** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ElementType | Yes | Egy ClaimType elemre vagy egy felhasználói felületi elemre hivatkozik a házirend fájljában. |
| ElementId | Yes | A ClaimsSchema szakaszban már definiált jogcím-típusra mutató hivatkozást tartalmazó karakterlánc, amely akkor használatos, ha a **ElementType** claimType van beállítva. |
| TargetCollection | Yes | A célhely gyűjteménye. |

A **LocalizedCollection** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| Elem | 0: n | Meghatároz egy elérhető lehetőséget, amellyel a felhasználó kiválaszthat egy jogcímet a felhasználói felületen, például egy értéket a legördülő menüben. |

Az **Item** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Yes | Az ehhez a beállításhoz tartozó felhasználói felületen megjeleníteni kívánt felhasználóbarát megjelenítési karakterlánc. |
| Érték | Yes | A beállítás kiválasztásához társított karakterlánc-jogcím értéke. |
| SelectByDefault | No | Azt jelzi, hogy ez a beállítás alapértelmezés szerint ki van-e választva a felhasználói felületen. Lehetséges értékek: true vagy FALSE. |

A következő példa a **LocalizedCollections** elem használatát mutatja be. Két **LocalizedCollection** elemet tartalmaz, amelyek közül az egyik az angol, a másik pedig a spanyol. Állítsa be a jogcím **korlátozási** gyűjteményét az `Gender` angol és a spanyol nyelvű elemek listájával.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

A **LocalizedStrings** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Honosított karakterlánc. |

A **LocalizedString** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ElementType | Yes | Lehetséges értékek: [ClaimsProvider](#claimsprovider), [claimType](#claimtype), [errorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype), [predikátum](#predicate), [InputValidation](#inputvalidation)vagy [UxElement](#uxelement).   | 
| ElementId | Yes | Ha a **ElementType** értéke `ClaimType` , `Predicate` , vagy `InputValidation` , ez az elem a ClaimsSchema szakaszban már definiált jogcím-típusra mutató hivatkozást tartalmaz. |
| Elemtípusú | Yes | Ha a **ElementType** beállítás értéke `ClaimType` , ez az elem egy jogcím típusú attribútumra mutató hivatkozást tartalmaz. Lehetséges értékek: `DisplayName` , `AdminHelpText` , vagy `PatternHelpText` . Az `DisplayName` érték a jogcím megjelenítendő nevének megadására szolgál. Az `AdminHelpText` érték a jogcím felhasználójának Súgó szövegének megadására szolgál. Az `PatternHelpText` érték a jogcím mintájának Súgó szövegének megadására szolgál. Ha a **ElementType** értéke `UxElement` , ez az elem egy felhasználói felületi elem attribútumára mutató hivatkozást tartalmaz. Ha a **ElementType** értéke `ErrorMessage` , akkor ez az elem egy hibaüzenet azonosítóját adja meg. Az azonosítók teljes listájáért tekintse meg a [honosítási karakterlánc-azonosítókat](localization-string-ids.md) `UxElement` .|

## <a name="elementtype"></a>ElementType

A ElementType, a jogcím-átalakításra vagy a házirend felhasználói felületi elemére való hivatkozás honosítható.

| Honosított elem | ElementType | ElementId |Elemtípusú |
| --------- | -------- | ----------- |----------- |
| Identitás-szolgáltató neve |`ClaimsProvider`| | A ClaimsExchange elem azonosítója|
| Jogcím típusának attribútumai|`ClaimType`|A jogcím típusának neve| A honosítani kívánt jogcím attribútuma. Lehetséges értékek: `AdminHelpText` ,,, `DisplayName` `PatternHelpText` és `UserHelpText` .|
|Hibaüzenet|`ErrorMessage`||A hibaüzenet azonosítója |
|Honosított karakterláncok másolása jogcímekre|`GetLocalizedStringsTra nsformationClaimType`||A kimeneti jogcím neve|
|Predikátum felhasználói üzenet|`Predicate`|A predikátum neve| A honosítani kívánt predikátum attribútuma. Lehetséges értékek: `HelpText` .|
|Predikátum-csoport felhasználói üzenete|`InputValidation`|A PredicateValidation elem azonosítója.|A PredicateGroup elem azonosítója. A predikátum-csoportnak a predikátum-ellenőrzési elem gyermekének kell lennie a ElementId definiált módon.|
|Felhasználó felület elemei |`UxElement` | | A honosítani kívánt felhasználói felületi elem azonosítója.|
|[Vezérlőelem megjelenítése](display-controls.md) |`DisplayControl` |A megjelenítési vezérlő azonosítója. | A honosítani kívánt felhasználói felületi elem azonosítója.|

## <a name="examples"></a>Példák

### <a name="claimsprovider"></a>ClaimsProvider

A ClaimsProvider érték az egyik jogcím-szolgáltató megjelenített nevének honosítására szolgál. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

Az alábbi példa bemutatja, hogyan honosíthatja meg a jogcím-szolgáltatók megjelenítendő nevét.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

A ClaimType érték az egyik jogcím-attribútum honosítására szolgál. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Az alábbi példa azt szemlélteti, hogyan honosítható meg az e-mail-jogcím típusának DisplayName, UserHelpText és PatternHelpText attribútuma.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

A ErrorMessage érték a rendszerüzenetek egyikének honosítására szolgál. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Az alábbi példa bemutatja, hogyan honosíthatja meg a UserMessageIfClaimsPrincipalAlreadyExists hibaüzenetét.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatLocalizedStringTransformationClaimType

A FormatLocalizedStringTransformationClaimType érték a jogcímek honosított karakterláncba való formázására szolgál. További információ: [FormatLocalizedString jogcímek átalakítása](string-transformations.md#formatlocalizedstring)


```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Az alábbi példa bemutatja, hogyan honosítható a FormatLocalizedStringTransformationClaimType-jogcímek átalakításának karakterlánc-formátuma.

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

A GetLocalizedStringsTransformationClaimType érték a honosított karakterláncok jogcímbe másolására szolgál. További információ: [GetLocalizedStringsTransformation jogcímek átalakítása](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

Az alábbi példa bemutatja, hogyan lehet honosítani a GetLocalizedStringsTransformation jogcímek átalakításának kimeneti jogcímeit.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikátum

A predikátum értéke a [predikátumok](predicates.md) egyik hibaüzenetének honosítására szolgál. 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Az alábbi példa bemutatja, hogyan honosítható meg a predikátumok súgó szövege.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

A InputValidation érték a [PredicateValidation](predicates.md) csoport egyik hibaüzenetének honosítására szolgál. 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Az alábbi példa bemutatja, hogyan lehet honosítani egy predikátum-ellenőrzési csoportot segítő szöveget.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

A UxElement érték a felhasználói felület egyik elemének honosítására szolgál. Az alábbi példa bemutatja, hogyan honosítható a folytatás és a Mégse gomb.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>DisplayControl

A DisplayControl érték a [megjelenítési vezérlő](display-controls.md) felhasználói felületének egyik elemének honosítására szolgál. Ha ez a beállítás engedélyezve van, a megjelenítési vezérlő localizedStrings ***elsőbbséget** élvez a _ *UxElement** StringIDs, például a **ver_but_send**, a **ver_but_edit**, a **ver_but_resend** és a **ver_but_verify** esetében. Az alábbi példa bemutatja, hogyan honosítható a Küldés és ellenőrzés gomb. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

Az önérvényesített műszaki profilok metaadatok szakaszában a hivatkozott ContentDefinition-nek a DataUri 2.1.0 vagy újabb [verzióra](page-layout.md) kell beállítania. Például:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Következő lépések

A honosítási példákat a következő cikkekben találja:

- [Nyelvi Testreszabás egyéni házirenddel Azure Active Directory B2C](language-customization.md)
- [Nyelvi Testreszabás felhasználói folyamatokkal Azure Active Directory B2C](language-customization.md)
