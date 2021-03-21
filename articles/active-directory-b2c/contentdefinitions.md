---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C egyéni házirendjének ContentDefinitions elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62bae22b6a4bb06b1e97c18e52ad614fd2439902
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489321"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Testre szabhatja a [saját maga által vezérelt technikai profilok](self-asserted-technical-profile.md)megjelenését és működését. Azure Active Directory B2C (Azure AD B2C) kódot futtat az ügyfél böngészőjében, és egy modern megközelítést használ az eltérő eredetű erőforrás-megosztás (CORS) néven.

A felhasználói felület testreszabásához a testreszabott HTML-tartalommal rendelkező **ContentDefinition** elemben meg kell adnia egy URL-címet. Az önérvényesített technikai profilban vagy **OrchestrationStep** mutasson erre a tartalom-definíciós azonosítóra. A tartalom definíciója tartalmazhat egy **LocalizedResourcesReferences** elemet, amely meghatározza a betöltendő honosított erőforrások listáját. Azure AD B2C egyesíti a felhasználói felület elemeit az URL-címről betöltött HTML-tartalommal, majd megjeleníti a lapot a felhasználónak.

A **ContentDefinitions** elem a HTML5-sablonok URL-címeit tartalmazza, amelyeket a felhasználói utazás során használhat. A HTML5-lap URI-ja a megadott felhasználói felületi lépéshez használatos. Például a bejelentkezés vagy a regisztráció, a jelszó alaphelyzetbe állítása vagy a hibák lapja. A megjelenést és a működést úgy változtathatja meg, hogy felülbírálja a HTML5-fájl Tartalomdefinícióban. Az igényeknek megfelelően új tartalmi definíciókat hozhat létre. Ez az elem honosított erőforrás-hivatkozást tartalmazhat a [honosítási](localization.md) elemben megadott honosítási azonosítóra.

Az alábbi példa a Content definition azonosítóját és a honosított erőforrások definícióját mutatja be:

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

A **LocalAccountSignUpWithLogonEmail** önérvényesített technikai profil metaadatai a **ContentDefinitionReferenceId** beállított tartalom-definíciós azonosítót tartalmazzák `api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

A **ContentDefinition** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A tartalom definíciójának azonosítója. Az érték a **tartalom-definíciós azonosítók** szakaszban, a lap későbbi részében van megadva. |

A **ContentDefinition** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| Tartalomdefinícióban | 1:1 | Egy karakterlánc, amely tartalmazza a tartalom definíciójának HTML5-oldalának URL-címét. |
| RecoveryUri | 1:1 | Egy karakterlánc, amely tartalmazza a tartalom-definícióval kapcsolatos hibát megjelenítő HTML-oldal URL-címét. Jelenleg nincs használatban, az értéknek kell lennie `~/common/default_page_error.html` . |
| DataUri | 1:1 | Egy olyan karakterlánc, amely a lépéshez szükséges felhasználói élményt biztosító HTML-fájl relatív URL-címét tartalmazza. |
| Metaadatok | 0:1 | Kulcs/érték párok gyűjteménye, amely a tartalom definíciójában használt metaadatokat tartalmazza. |
| LocalizedResourcesReferences | 0:1 | Honosított erőforrások gyűjteménye. Ezzel az elemmel testreszabható a felhasználói felület és a jogcím attribútum honosítása. |

### <a name="datauri"></a>DataUri

A **DataUri** elem az oldal azonosítójának megadására szolgál. Azure AD B2C az oldal azonosítóját használja a felhasználói felületi elemek és az ügyféloldali JavaScript betöltéséhez és elindításához. Az érték formátuma: `urn:com:microsoft:aad:b2c:elements:page-name:version` . A következő táblázat felsorolja a használható oldal-azonosítókat.

| Oldal azonosítója | Description |
| ----- | ----------- |
| `globalexception` | Hibaüzenetet jelenít meg, ha kivétel vagy hiba történt. |
| `providerselection`, `idpselection` | Felsorolja azokat az identitás-szolgáltatókat, amelyeket a felhasználók a bejelentkezés során választhatnak.  |
| `unifiedssp` | Megjeleníti a helyi fiókkal való bejelentkezéshez használt űrlapot, amely egy e-mail-cím vagy egy Felhasználónév alapján jelenik meg. Ez az érték a "bejelentkezési funkciók megtartása" funkciót és az "elfelejtette jelszavát?" is tartalmazza hivatkozással. |
| `unifiedssd` | Megjeleníti a helyi fiókkal való bejelentkezéshez használt űrlapot, amely egy e-mail-cím vagy egy Felhasználónév alapján jelenik meg. |
| `multifactor` | Megerősíti a telefonszámokat szöveg vagy hang használatával a regisztráció vagy a bejelentkezés során. |
| `selfasserted` | Megjeleníti a felhasználótól származó adatok gyűjtésére szolgáló űrlapot. Például lehetővé teszi a felhasználók számára, hogy saját profiljaikat hozzanak létre vagy frissítsenek. |

### <a name="select-a-page-layout"></a>Lapelrendezés kiválasztása

A [JavaScript-ügyféloldali kód](javascript-and-page-layout.md) engedélyezéséhez szúrja `contract` `elements` be a és az oldal típusát. Például: `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A [verzió](page-layout.md) részét képező része a `DataUri` szabályzatban szereplő felhasználói felület elemeinek HTML, CSS és JavaScript tartalmú csomagja. Ha engedélyezni kívánja a JavaScript ügyféloldali kódját, a JavaScript-alapú elemek nem változtathatók meg. Ha nem változtathatók meg, a módosítások váratlan viselkedést okozhatnak a felhasználói oldalakon. A problémák megelőzése érdekében kényszerítse ki a lapelrendezés használatát, és adja meg a lapelrendezés verzióját. Így biztosíthatja, hogy a JavaScripten alapuló összes tartalmi definíció nem változtatható. Ha nem kívánja engedélyezni a JavaScriptet, akkor is meg kell adnia a lapok elrendezésének verzióját.

A következő példa a **DataUri** mutatja be `selfasserted` `1.2.0` :

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Áttelepítés az oldal elrendezésére

Az érték formátumának tartalmaznia kell a szót `contract` : _urn: com: Microsoft: HRE: B2C: Elements:**Szerződés**:p Age-Name: Version_. Ha a régi **DataUri** értéket használó egyéni házirendekben szeretne megadni egy lapelrendezést, a következő táblázat segítségével váltson át az új formátumra.

| Régi DataUri érték | Új DataUri érték |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |

A következő példa a Content definition-azonosítókat és a hozzá tartozó **DataUri** mutatja be a legújabb verzióra: 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Metaadatok

A **metaadat** -elemek a következő elemeket tartalmazzák:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| Elem | 0: n | A tartalom-definícióhoz kapcsolódó metaadatok |

A **metaadatok** elem **elem eleme a** következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Kulcs | Yes | A metaadat-kulcs.  |

#### <a name="metadata-keys"></a>Metaadat-kulcsok

A Content Definition a következő metaadat-elemeket támogatja:

| Kulcs | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DisplayName | No | Egy karakterlánc, amely tartalmazza a tartalom definíciójának nevét. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

A **LocalizedResourcesReferences** elem a következő elemeket tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | A tartalom definíciójának honosított erőforrás-hivatkozásainak listája. |

A **LocalizedResourcesReference** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Nyelv | Yes | Egy karakterlánc, amely az RFC 5646-címkék által támogatott nyelvet tartalmaz a nyelvek azonosításához. |
| LocalizedResourcesReferenceId | Yes | A **LocalizedResources** elem azonosítója. |

Az alábbi példa egy regisztrációs vagy bejelentkezési tartalom definícióját mutatja be az angol, francia és spanyol nyelv honosítására való hivatkozással:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Ha szeretné megtudni, hogyan adhat honosított támogatást a tartalmi definíciók számára, tekintse meg a [honosítás](localization.md)című témakört.

## <a name="content-definition-ids"></a>Tartalom-definíciós azonosítók

A **ContentDefinition** elem ID attribútuma határozza meg a tartalom-definícióhoz kapcsolódó oldal típusát. Az elem határozza meg azt a környezetet, amelyet az egyéni HTML5/CSS-sablon alkalmazni fog. A következő táblázat ismerteti az identitási élmény keretrendszere által felismert tartalmi definíciós azonosítókat, valamint az azokhoz kapcsolódó lapokat. Saját tartalmi definíciókat tetszőleges AZONOSÍTÓval hozhat létre.

| ID (Azonosító) | Alapértelmezett sablon | Description |
| -- | ---------------- | ----------- |
| **API. error** | [kivétel. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hiba lap** – hibaüzenetet jelenít meg, ha kivétel vagy hiba történt. |
| **API. idpselections** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitás-szolgáltató kiválasztása lap** – felsorolja azokat az azonosítókat, amelyeket a felhasználók a bejelentkezés során választhatnak. A lehetőségek általában a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| **API. idpselections. regisztráció** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Az Identitáskezelő kiválasztása a regisztrációhoz** – felsorolja azokat az identitás-szolgáltatókat, amelyeket a felhasználók a regisztráció során választhatnak. A lehetőségek általában a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| **API. localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszó lap** – megjelenít egy űrlapot, amelyet a felhasználóknak be kell fejezniük a jelszó-visszaállítás elindításához. |
| **API. localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési oldala** – megjelenít egy űrlapot, amely egy olyan helyi fiókkal jelentkezik be, amely e-mail-cím vagy Felhasználónév alapján jelenik meg. Az űrlap tartalmazhatja a szövegbeviteli és a jelszó-beviteli mezőt is. |
| **API. localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók regisztrálása lap** – megjelenít egy űrlapot, amely az e-mail-cím vagy Felhasználónév alapján létrehozott helyi fiókra regisztrál. Az űrlap különböző beviteli vezérlőket tartalmazhat, például a következőket: szövegbeviteli mező, jelszó-beviteli mező, választógomb, egyszeres kijelölés legördülő lista és többszörös kijelölés jelölőnégyzet. |
| **API. phonefactor** | [többtényezős – 1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication lap** – a telefonszámok vagy a hangfelvételek, valamint a bejelentkezés vagy a bejelentkezés során ellenőrzi a telefonszámokat. |
| **API. selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiók regisztrálása lap** – megjeleníti azt az űrlapot, amelyet a felhasználóknak be kell fejezniük, amikor regisztrálnak egy meglévő fiókkal egy közösségi identitás-szolgáltatótól. Ez az oldal hasonló az előző közösségi fiók regisztráció lapjához, a jelszó-beviteli mezők kivételével. |
| **API. selfasserted. profileUpdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil frissítése lap** – megjelenít egy űrlapot, amelyet a felhasználók a profiljuk frissítéséhez tudnak elérni. Ez az oldal hasonló a közösségi fiók regisztrálása oldalhoz, a jelszó-beviteli mezők kivételével. |
| **API. signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési oldal** – kezeli a felhasználói regisztrációt és a bejelentkezési folyamatot. A felhasználók a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy a helyi fiókok használatával használhatják a felhasználókat. |

## <a name="next-steps"></a>Következő lépések

A felhasználói felület tartalmi definíciók használatával történő testreszabására példát a következő témakörben talál:

[Az alkalmazás felhasználói felületének testreszabása egyéni házirend használatával](customize-ui-with-html.md)
