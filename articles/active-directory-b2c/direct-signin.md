---
title: Közvetlen bejelentkezés beállítása a Azure Active Directory B2C használatával | Microsoft Docs
description: Megtudhatja, hogyan lehet előre kitölteni a bejelentkezési nevet, vagy egyenesen átirányítani egy közösségi identitás-szolgáltatóra.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 35e8efa269ab72477b06e86824d368d0a3dced03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103197328"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ha Azure Active Directory (AD) B2C használatával állítja be az alkalmazásba való bejelentkezést, előre feltöltheti a bejelentkezési nevet vagy a közvetlen bejelentkezést egy adott közösségi identitás-szolgáltatóba, például a Facebook, a LinkedIn vagy egy Microsoft-fiók.

## <a name="prepopulate-the-sign-in-name"></a>A bejelentkezési név előre feltöltése

A bejelentkezési felhasználói úton a függő entitás alkalmazás egy adott felhasználót vagy tartománynevet is megcélozhat. Egy felhasználó megcélzásakor az alkalmazás megadhatja az engedélyezési kérelemben a `login_hint` lekérdezési paramétert a felhasználó bejelentkezési nevével. Azure AD B2C automatikusan feltölti a bejelentkezési nevet, míg a felhasználónak csak meg kell adnia a jelszót.

![Bejelentkezési oldal regisztrálása az URL-cím login_hint lekérdezési paraméterrel kiemelve](./media/direct-signin/login-hint.png)

A felhasználó módosíthatja a bejelentkezési szövegmezőben lévő értéket.

::: zone pivot="b2c-custom-policy"

A login hint paraméter támogatásához bírálja felül a `SelfAsserted-LocalAccountSignin-Email` technikai profilt. A `<InputClaims>` szakaszban állítsa be a signInName jogcím DefaultValue értékét a következőre: `{OIDC:LoginHint}` . A `{OIDC:LoginHint}` változó tartalmazza a `login_hint` paraméter értékét. Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegmezőt.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>Bejelentkezés átirányítása egy közösségi szolgáltatóba

Ha úgy konfigurálta a bejelentkezési utat, hogy az alkalmazás belefoglalja a közösségi fiókokat, például a Facebookot, a LinkedInt vagy a Google-t, megadhatja a `domain_hint` paramétert. Ez a lekérdezési paraméter egy olyan célzást tartalmaz, amely a bejelentkezéshez használandó közösségi identitás-szolgáltatóról Azure AD B2C. Ha például az alkalmazás megadja, a `domain_hint=facebook.com` Bejelentkezés közvetlenül a Facebook bejelentkezési oldalára kerül.

![Bejelentkezési oldal regisztrálása az URL-cím domain_hint lekérdezési paraméterrel kiemelve](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

A domain hint lekérdezési karakterlánc paramétere a következő tartományok egyikére állítható be:

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- Az [általános OpenID Connect](identity-provider-generic-openid-connect.md)esetében lásd: [domain hint](identity-provider-generic-openid-connect.md#response-mode).

::: zone-end

::: zone pivot="b2c-custom-policy"

A domain hint paraméter támogatásához a tartománynevet a `<Domain>domain name</Domain>` tetszőleges XML-elem használatával állíthatja be `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

