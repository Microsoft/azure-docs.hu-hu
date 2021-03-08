---
title: ClaimsProviders – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének ClaimsProvider elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 846394266b981c14788148be465912b14bc1fb3e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447911"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jogcímek biztosítják a különböző típusú felekkel való kommunikációhoz szükséges felületet a [technikai profiljain](technicalprofiles.md)keresztül. Minden jogcím-szolgáltatónak rendelkeznie kell egy vagy több olyan technikai profillal, amely meghatározza a végpontokat és a jogcím-szolgáltatóval való kommunikációhoz szükséges protokollokat. A jogcím-szolgáltató több technikai profillal is rendelkezhet. Például több technikai profil is definiálható, mert a jogcím-szolgáltató több protokollt támogat, különböző képességekkel rendelkező végpontokat, illetve különböző jogcímeket különböző megbízhatósági szinteken szabadít fel. Lehetséges, hogy a bizalmas jogcímeket egy felhasználói úton kell kibocsátani, de nem egy másikban.

A felhasználói út a technikai profilok meghívását az üzleti logikájának meghatározására szolgáló lépések segítségével ötvözi. 

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

A **ClaimsProviders** elem a következő elemet tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Egy akkreditált jogcím-szolgáltató, amely különböző felhasználói útvonalakon is kihasználható. |

## <a name="claimsprovider"></a>ClaimsProvider

A **ClaimsProvider** elem a következő alárendelt elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ---------- | ----------- |
| Tartomány | 0:1 | A jogcím-szolgáltató tartománynevét tartalmazó karakterlánc. Ha például a jogcímek szolgáltatója tartalmazza a Facebook technikai profilt, a tartománynév Facebook.com. Ezt a tartománynevet a rendszer a jogcím-szolgáltatóban definiált összes műszaki profilhoz használja, kivéve ha a technikai profil felülbírálja. A tartománynevet **domain_hint** is lehet hivatkozni. További információkért tekintse [meg a közvetlen bejelentkezés beállítása a Azure Active Directory B2C használatával](direct-signin.md)című témakör **átirányítása a közösségi szolgáltatónak** című szakaszát. |
| DisplayName | 1:1 | A jogcím-szolgáltató nevét tartalmazó karakterlánc. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | A jogcím-szolgáltató által támogatott technikai profilok készlete |

A **ClaimsProvider** szervezi, hogy a technikai profilok hogyan kapcsolódnak a jogcím-szolgáltatóhoz. A következő példa a Azure Active Directory jogcím-szolgáltatót mutatja be a Azure Active Directory technikai profiljaival:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

A következő példa a Facebook **-OAUTH** technikai profillal rendelkező Facebook-jogcímek szolgáltatóját mutatja be.

```xml
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
