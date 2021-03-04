---
title: Egy SAML-kiállító technikai profiljának meghatározása egyéni házirendben
titleSuffix: Azure AD B2C
description: Definiáljon egy Security Assertion Markup Language token (SAML) kiállító technikai profilját egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54869c14cf7c5a7e43f34102f5c95e37689dfee8
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095340"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Az SAML-token kiállítójának műszaki profiljának meghatározása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú biztonsági jogkivonatot bocsát ki, mivel az egyes hitelesítési folyamatokat dolgozza fel. Az SAML-token kiállítójának műszaki profilja olyan SAML-jogkivonatot bocsát ki, amely vissza lesz visszaküldve a függő entitás alkalmazásának (szolgáltatónak). Ez a technikai profil általában a felhasználói út utolsó előkészítési lépése.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `SAML2` . Állítsa a **OutputTokenFormat** elemet a következőre: `SAML2` .

A következő példa egy technikai profilt mutat be `Saml2AssertionIssuer` :

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és megőrzési jogcímek

A **szabályzattípushoz**, a **OutputClaims** és a **PersistClaims** elemek üresek vagy hiányoznak. A **InutputClaimsTransformations** és a **OutputClaimsTransformations** elemek is hiányoznak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| IssuerUri | Nem | Az SAML-válaszban megjelenő kiállító neve. Az értéknek meg kell egyeznie a függő entitás alkalmazásban konfigurált névvel. |
| XmlSignatureAlgorithm | Nem | Az a módszer, amelyet a Azure AD B2C az SAML-állítás aláírására használ. Lehetséges értékek: `Sha256` ,,, `Sha384` `Sha512` vagy `Sha1` . Győződjön meg arról, hogy az aláírási algoritmus mindkét oldalon ugyanazzal az értékkel van konfigurálva. Csak a tanúsítvány által támogatott algoritmust használja. Az SAML-válasz konfigurálásához tekintse meg az [SAML-alkalmazások regisztrálásának lehetőségeit](saml-service-provider.md) .|
|TokenNotBeforeSkewInSeconds| Nem| Meghatározza az érvényességi időszak kezdetét jelölő időbélyegző egész számként való eldöntését. Minél nagyobb ez a szám, az érvényességi időszak további visszalépésének időpontja a jogcímek a függő entitáshoz való kibocsátásának időpontjára vonatkozóan kezdődik. Ha például a TokenNotBeforeSkewInSeconds 60 másodpercre van állítva, ha a tokent 13:05:10 UTC alapján állították ki, a jogkivonat a 13:04:10 UTC-től érvényes. Az alapértelmezett érték a 0. A maximális érték 3600 (egy óra). |
|TokenLifeTimeInSeconds| Nem| Megadja az SAML-állítás élettartamát. Ez az érték másodpercben van a fent hivatkozott NotBefore értéktől. Az alapértelmezett érték 300 másodperc (5 perc). |


## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A CryptographicKeys elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| MetadataSigning | Igen | Az SAML-metaadatok aláírásához használt X509-tanúsítvány (RSA-kulcs). Azure AD B2C ezt a kulcsot használja a metaadatok aláírásához. |
| SamlMessageSigning| Igen| Adja meg az SAML-üzenetek aláírásához használandó X509-tanúsítványt (RSA-kulcs). Azure AD B2C ezt a kulcsot használja a `<samlp:Response>` függő entitásnak küldött válasz aláírására.|

## <a name="session-management"></a>Munkamenet-kezelés

Az Azure AD B2C SAML-munkamenetek konfigurálásához egy függő entitás alkalmazása, az elem attribútuma `UseTechnicalProfileForSessionManagement` , a [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-munkamenetre való hivatkozás.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan használhatja az SAML kiállítói technikai profilt:

- [SAML-alkalmazás regisztrálása Azure AD B2C](saml-service-provider.md)

