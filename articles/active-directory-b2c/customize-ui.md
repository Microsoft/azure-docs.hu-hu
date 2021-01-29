---
title: A kezelőfelület testreszabása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a Azure Active Directory B2Ct használó alkalmazásai felhasználói felületét.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055861"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C felhasználói felületének testreszabása

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure Active Directory B2C (Azure AD B2C) felhasználói felületének védjegyezése és testreszabása az ügyfelek számára, így zökkenőmentes felhasználói élményt biztosíthat az alkalmazásban. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a profil szerkesztése és a jelszó-visszaállítás. Ebben a cikkben a Azure AD B2C lapjait a sablon és a vállalati arculat alapján szabhatja testre. 

> [!TIP]
> Ha testre szeretné szabni a felhasználói folyamatok oldalain az oldal sablonján, a szalagcím emblémáján, a háttérképen vagy a háttérszínen kívül más szempontokat is, tekintse meg [a felhasználói felület testreszabása HTML-sablonnal](customize-ui-with-html.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Áttekintés

Azure AD B2C több beépített sablont is biztosít, amelyek közül választhat, hogy professzionális arculatot ad a felhasználói élménynek. Ezek a webhelysablonok a [vállalati arculati](#company-branding) funkció használatával is kiindulási pontként szolgálhatnak a saját testreszabásához.

### <a name="ocean-blue"></a>Ocean Blue

Példa az Ocean Blue sablonra, amely a bejelentkezési oldalon jelenik meg:

![Az Ocean Blue sablon képernyőképe](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Szürke pala

Példa a bejelentkező bejelentkezési oldalon megjelenített szürke sablonra:

![Szürke sablon](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Klasszikus

Példa a regisztrálási bejelentkezési oldalon megjelenített klasszikus sablonra:

![Képernyőkép a klasszikus sablonról](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Vállalati védjegyezés

A Azure AD B2C lapokat a szalagcím emblémával, a háttérképgel és a háttérszínsel testreszabhatja Azure Active Directory [vállalati védjegyezés](../active-directory/fundamentals/customize-branding.md)használatával. A vállalat arculata magában foglalja a regisztrációt, a bejelentkezést, a profil szerkesztését és a jelszó-visszaállítást. 

Az alábbi példa egy *regisztrációs és bejelentkezési* oldalt mutat be egyéni emblémával, háttérképgel, az Ocean Blue sablon használatával:

![A Azure AD B2C által kiszolgált márkás regisztrációs/bejelentkezési oldal](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Oldal sablonjának kiválasztása

::: zone pivot="b2c-user-flow"

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelyet testre szeretne szabni.
1. A bal oldali menü **Testreszabás** **területén válassza a lapelrendezések lehetőséget** , majd válasszon egy **sablont**.
    ![Sablon kiválasztása legördülő menü a Azure Portal felhasználói folyamat lapján](./media/customize-ui/select-page-template.png)

A sablon kiválasztásakor a rendszer a kiválasztott sablont a felhasználói folyamat összes lapjára alkalmazza. Az egyes lapok URI-ja látható az **Egyéni lap URI** mezőjében.

::: zone-end

::: zone pivot="b2c-custom-policy"

Oldalsablon kiválasztásához állítsa be a `LoadUri` [tartalmi definíciók](contentdefinitions.md)elemét. A következő példa a Content definition-azonosítókat és a hozzájuk tartozó adatokat mutatja `LoadUri` . 

Ocean Blue:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Szürke pala:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Klasszikus 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Vállalati arculat konfigurálása

A felhasználói folyamatok lapjain való testreszabáshoz először a vállalati arculatot kell konfigurálnia Azure Active Directoryban, majd a Azure AD B2C a felhasználói folyamatokban engedélyezi azt.

Először állítsa be a szalagcím emblémáját, a háttérképet és a háttérszínt a **vállalati arculaton** belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **kezelés** területen válassza a **vállalati védjegyezés** lehetőséget.
1. Kövesse a [branding hozzáadása a szervezet Azure Active Directory bejelentkezési oldalához](../active-directory/fundamentals/customize-branding.md)című témakör lépéseit.

Tartsa szem előtt ezeket a dolgokat, amikor a vállalati arculatot konfigurálja Azure AD B2Cban:

* A Azure AD B2C vállalati arculata jelenleg a **háttérképre**, a **szalagcím emblémára** és a **háttérszínek** testreszabására korlátozódik. A vállalati arculati panel egyéb tulajdonságai (például a **Speciális beállítások**) *nem támogatottak*.
* A felhasználói folyamatok oldalain a háttér színe látható a háttérkép betöltése előtt. Javasoljuk, hogy olyan háttérszínt válasszon, amely szorosan illeszkedik a háttérkép színeihez a zökkenőmentes betöltési élmény érdekében.
* A szalagcím embléma a felhasználóknak küldött ellenőrző e-mailekben jelenik meg, amikor kezdeményeznek egy regisztrációs felhasználói folyamatot.



## <a name="enable-company-branding-in-user-flow-pages"></a>Vállalati arculat engedélyezése a felhasználói folyamatok oldalain

::: zone pivot="b2c-user-flow"

Miután konfigurálta a vállalati arculatot, engedélyezze azt a felhasználói folyamatokban.

1. A Azure Portal bal oldali menüjében válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amely számára engedélyezni szeretné a vállalati arculatot. A vállalati védjegyezés **nem támogatott** a szabványos *bejelentkezéshez* és a normál *profilhoz* tartozó felhasználói folyamatok szerkesztéséhez.
1. A **Testreszabás** területen válassza a lapelrendezések lehetőséget, majd válassza ki azt a **lapot, amelyet** szeretne felvenni. Válassza például az **egyesített regisztráció vagy a bejelentkezés lapot**.
1. A lapelrendezés **verziója (előzetes verzió)** területen válassza a **1.2.0** vagy újabb verzió elemet.
1. Kattintson a **Mentés** gombra.

Ha a felhasználói folyamat összes lapját szeretné felvenni, állítsa be az oldalelrendezés verzióját a felhasználói folyamat minden egyes lapjához.

![Az oldal elrendezésének kiválasztása Azure AD B2C a Azure Portal](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Miután konfigurálta a vállalati arculatot, engedélyezze azt az egyéni szabályzatban. Konfigurálja a lapelrendezés [verzióját](contentdefinitions.md#migrating-to-page-layout) az oldal `contract` verziójára az egyéni szabályzat *összes* tartalmi definíciója esetében. Az érték formátumának tartalmaznia kell a szót `contract` : _urn: com: Microsoft: HRE: B2C: Elements:**Szerződés**:p Age-Name: Version_. A régi **DataUri** értéket használó egyéni szabályzatokban megjelenő lapelrendezés megadása. További információkért tekintse meg az oldal- [elrendezésre való áttelepítést](contentdefinitions.md#migrating-to-page-layout) a lap verziójával.

Az alábbi példa a tartalmi definíciókat mutatja be a kapcsolódó oldal-szerződéssel, valamint az *Ocean Blue* -oldal sablonnal: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával Azure Active Directory B2Cban](customize-ui-with-html.md).
