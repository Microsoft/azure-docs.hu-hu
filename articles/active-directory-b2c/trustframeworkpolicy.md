---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének TrustFrameworkPolicy elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 354c6f9710b7cbd70e0631bc973b2482ea8d8bb3
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97386884"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyéni szabályzatok egy vagy több XML-formátumú fájlként jelennek meg, amelyek egy hierarchikus láncban egymásra hivatkoznak. Az XML-elemek határozzák meg a szabályzat elemeit, például a jogcímek sémáját, a jogcímek átalakítását, a tartalmi definíciókat, a jogcím-szolgáltatókat, a technikai profilokat, a felhasználói útvonalakat, valamint a Minden házirendfájl a házirendfájl legfelső szintű **TrustFrameworkPolicy** elemében van definiálva.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


A **TrustFrameworkPolicy** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Igen | A szabályzat végrehajtásához használandó séma verziója. Az értéknek meg kell felelnie `0.3.0.0` |
| TenantObjectId | Nem | A Azure Active Directory B2C (Azure AD B2C) bérlő egyedi objektumazonosító. |
| TenantId | Igen | Annak a bérlőnek az egyedi azonosítója, amelyhez ez a szabályzat tartozik. |
| PolicyId | Igen | A házirend egyedi azonosítója. Ezt az azonosítót *B2C_1A_* kell előállítani |
| PublicPolicyUri | Igen | A házirend URI-ja, amely a bérlői azonosító és a házirend-azonosító kombinációja. |
| Deploymentmode beállítása | Nem | Lehetséges értékek: `Production` , vagy `Development` . A `Production` az alapértelmezett formátum. Ezt a tulajdonságot használhatja a szabályzat hibakereséséhez. További információkért lásd: [naplók gyűjtése](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Nem | A végpont, amelyet a **deploymentmode beállítása** beállításakor használ a rendszer `Development` . Az értéknek a számnak kell lennie `urn:journeyrecorder:applicationinsights` . További információkért lásd: [naplók gyűjtése](troubleshoot-with-application-insights.md). |


Az alábbi példa bemutatja, hogyan adhatja meg a **TrustFrameworkPolicy** elemet:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

A **TrustFrameworkPolicy** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Egy alapházirend azonosítója. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | A szabályzat építőelemei. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Jogcím-szolgáltatók gyűjteménye. |
| [UserJourneys](userjourneys.md) | 0:1 | Felhasználói utazások gyűjteménye. |
| [RelyingParty](relyingparty.md) | 0:1 | A függő entitások házirendjének definíciója. |

Ha egy házirendet egy másik házirendből kíván örökölni, egy **BasePolicy** elemet kell deklarálnia a **TrustFrameworkPolicy** elem alatt. A **BasePolicy** elem azon alapházirendre hivatkozik, amelyből ez a házirend származik.

A **BasePolicy** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | --------|
| TenantId | 1:1 | A Azure AD B2C bérlő azonosítója. |
| PolicyId | 1:1 | A szülő házirend azonosítója. |


Az alábbi példa azt szemlélteti, hogyan lehet alapszintű szabályzatot megadni. Ez a **B2C_1A_TrustFrameworkExtensions** házirend a **B2C_1A_TrustFrameworkBase** házirendből származik.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

