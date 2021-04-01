---
title: A telefonos faktor technikai profiljának definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Adjon meg egy telefonos faktor műszaki profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950397"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>A telefonos faktor technikai profiljának definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a telefonszámok regisztrálásához és ellenőrzéséhez. Ez a technikai profil:

- Felhasználói felületet biztosít a felhasználóval, hogy ellenőrizze vagy regisztrálja a telefonszámot.
- A telefonos hívásokat és szöveges üzeneteket támogat a telefonszám ellenőrzéséhez.
- Több telefonszámot is támogat. A felhasználó választhatja ki az egyik telefonszámot az ellenőrzéshez.  
- Egy olyan jogcímet ad vissza, amely azt jelzi, hogy a felhasználó új telefonszámot adott-e meg. Ennek a jogcímnek a segítségével eldöntheti, hogy a telefonszámot meg kell-e őrizni a Azure AD B2C felhasználói profilban.  
- A [tartalom definícióját](contentdefinitions.md) használja a megjelenés és a működés szabályozására.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `Proprietary` . A **kezelő** attribútumnak tartalmaznia kell a Azure ad B2C által a telefonos faktorhoz használt protokollkezelő-szerelvény teljes nevét: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

A következő példa egy telefonos faktor technikai profilt mutat be a regisztráláshoz és az ellenőrzéshez:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Bemeneti jogcím-átalakítások

A InputClaimsTransformations elem tartalmazhat olyan bemeneti jogcím-átalakítások gyűjteményét, amelyek a bemeneti jogcímek módosításához vagy újak létrehozásához használatosak. A következő bemeneti jogcímek átalakítása olyan jogcímet hoz létre `UserId` , amelyet később a bemeneti jogcímek gyűjteménye használ.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Bemeneti jogcímek

A Szabályzattípushoz elemnek tartalmaznia kell a következő jogcímeket. A jogcím nevét a telefonos faktor technikai profilban definiált névre is leképezheti. 

|  Adattípus| Kötelező | Leírás |
| --------- | -------- | ----------- | 
| sztring| Yes | A felhasználó egyedi azonosítója. A jogcím nevét vagy PartnerClaimType kell beállítani `UserId` . Ez a jogcím nem tartalmazhat személyes azonosításra alkalmas adatokat.|
| sztring| Yes | A jogcím-típusok listája. Minden jogcím egy telefonszámot tartalmaz. Ha a bemeneti jogcímek bármelyike nem tartalmaz telefonszámot, a rendszer megkéri a felhasználót, hogy regisztrálja és ellenőrizze az új telefonszámot. Az érvényesített telefonszámot kimeneti jogcímként adja vissza a rendszer. Ha a bemeneti jogcímek egyike telefonszámot tartalmaz, a rendszer megkéri a felhasználót, hogy ellenőrizze. Ha több bemeneti jogcím is tartalmaz telefonszámot, a rendszer megkéri a felhasználót, hogy válasszon ki és ellenőrizze a telefonszámok egyikét. |

Az alábbi példa több telefonszám használatát mutatja be. További információ: [Sample Policy](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A OutputClaims elem a telefonos faktor technikai profil által visszaadott jogcímek listáját tartalmazza.

|  Adattípus| Kötelező | Leírás |
|  -------- | ----------- |----------- |
| boolean | Yes | Azt jelzi, hogy a felhasználó megadta-e az új telefonszámot. A jogcím nevét vagy PartnerClaimType úgy kell beállítani, hogy `newPhoneNumberEntered`|
| sztring| Yes | Az ellenőrzött telefonszám. A jogcím nevét vagy PartnerClaimType kell beállítani `Verified.OfficePhone` .|

A OutputClaimsTransformations elem olyan OutputClaimsTransformation-elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosításához vagy újak létrehozásához használatosak.

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem nincs használatban.


## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Yes | Az ehhez a technikai profilhoz társított [tartalom-definíció](contentdefinitions.md) azonosítója. |
| ManualPhoneNumberEntryAllowed| No | Adja meg, hogy a felhasználó számára engedélyezett-e a telefonszám manuális megadása. Lehetséges értékek: `true` , vagy `false` (alapértelmezett).|
| Setting. Authenticationmode tulajdonsághoz | No | A telefonszám érvényesítésének módszere. Lehetséges értékek: `sms` , `phone` , vagy `mixed` (alapértelmezett).|
| az automatikus tárcsázás beállítása| No| Annak megadása, hogy a technikai profil automatikusan tárcsázjon-e, vagy automatikusan küldjön-e SMS-t. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). Az automatikus tárcsázáshoz a `setting.authenticationMode` metaadatokat a vagy a értékre kell beállítani `sms` `phone` . A bemeneti jogcímek gyűjteményének egyetlen telefonszámmal kell rendelkeznie. |

### <a name="ui-elements"></a>Felhasználói felület elemei

A telefonos tényező hitelesítési lapja felhasználói felületi elemei [honosítható](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Következő lépések

- A [közösségi és helyi fiókokat a MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack csomaggal vizsgálhatja meg.
