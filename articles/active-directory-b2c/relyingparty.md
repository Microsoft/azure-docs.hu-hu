---
title: RelyingParty – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének RelyingParty elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 488065b0a1865484e96ea574b3031f2bf61869dd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120589"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **RelyingParty** elem határozza meg, hogy a felhasználó milyen útra kényszeríti az aktuális kérelem Azure Active Directory B2C (Azure ad B2C). Azon jogcímek listáját is meghatározza, amelyeknek a függő entitás (RP) alkalmazásnak a kiállított jogkivonat részeként kell szerepelnie. Egy RP-alkalmazás, például web-, mobil-vagy asztali alkalmazás, meghívja az RP-házirend fájlját. Az RP-házirend fájl egy adott feladatot hajt végre, például bejelentkezik, jelszó alaphelyzetbe állítása vagy profil szerkesztése. Több alkalmazás is használhatja ugyanazt az RP-szabályzatot, és egyetlen alkalmazás több házirendet is használhat. Minden RP-alkalmazás ugyanazt a jogkivonatot fogadja a jogcímekkel, és a felhasználó ugyanazon a felhasználói úton halad.

A következő példa egy **RelyingParty** elemet mutat be a *B2C_1A_signup_signin* -házirend fájljában:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

A választható **RelyingParty** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Az RP-alkalmazás alapértelmezett felhasználói útja. |
| Végpontok | 0:1 | A végpontok listája. További információ: UserInfo- [végpont](userinfo-endpoint.md). |
| UserJourneyBehaviors | 0:1 | A felhasználói út viselkedésének hatóköre. |
| Kivonatjogcím | 1:1 | Az RP-alkalmazás által támogatott technikai profil. A technikai profil egy szerződést biztosít az RP-alkalmazásnak a Azure AD B2C való kapcsolatfelvételhez. |

## <a name="endpoints"></a>Végpontok

A **végpontok** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Végpont | 1:1 | Egy végpontra mutató hivatkozás.|

A **végpont** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Igen | A végpont egyedi azonosítója.|
| UserJourneyReferenceId | Igen | A felhasználó által a szabályzatban megjelenő út azonosítója. További információ: [felhasználói útvonalak](userjourneys.md)  | 

Az alábbi példa egy függő entitást mutat be a [UserInfo-végponttal](userinfo-endpoint.md):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>DefaultUserJourney

Az `DefaultUserJourney` elem az alap-vagy a kiterjesztési szabályzatban általában meghatározott felhasználói út azonosítójának hivatkozását adja meg. Az alábbi példák a **RelyingParty** elemben megadott regisztrációs vagy bejelentkezési felhasználói utat mutatják be:

*B2C_1A_signup_signin* házirend:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* vagy *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

A **DefaultUserJourney** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A felhasználó által a szabályzatban megjelenő út azonosítója. További információ: [felhasználói útvonalak](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

A **UserJourneyBehaviors** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Az egyszeri bejelentkezés (SSO) munkamenet-viselkedésének hatóköre a felhasználói úton. |
| SessionExpiryType |0:1 | A munkamenet hitelesítési viselkedése. Lehetséges értékek: `Rolling` vagy `Absolute` . Az `Rolling` érték (alapértelmezett) azt jelzi, hogy a felhasználó addig marad bejelentkezve, amíg a felhasználó folyamatosan aktív az alkalmazásban. Az `Absolute` érték azt jelzi, hogy a felhasználónak az alkalmazás-munkamenet élettartama által megadott időszak után újra hitelesítenie kell magát. |
| SessionExpiryInSeconds | 0:1 | Az Azure AD B2C's munkamenet-cookie élettartama a sikeres hitelesítés után a felhasználó böngészőjében tárolt egész számként van megadva. |
| JourneyInsights | 0:1 | A használni kívánt Azure Application Insights-kialakítási kulcs. |
| ContentDefinitionParameters | 0:1 | A Content definition Load URI-hoz hozzáfűzni kívánt kulcs érték párok listája. |
|ScriptExecution| 0:1| A [JavaScript](javascript-and-page-layout.md) támogatott végrehajtási módjai. Lehetséges értékek: `Allow` vagy `Disallow` (alapértelmezett).

### <a name="singlesignon"></a>SingleSignOn

A **SingleSignon** elem a következő attribútumban szerepel:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Hatókör | Igen | Az egyszeri bejelentkezés viselkedésének hatóköre. Lehetséges értékek: `Suppressed` ,,, `Tenant` `Application` vagy `Policy` . Az `Suppressed` érték azt jelzi, hogy a rendszer letiltja a viselkedést, és a felhasználó mindig kéri az identitás-szolgáltató kijelölését.  Az `Tenant` érték azt jelzi, hogy a rendszer a bérlő összes házirendjére alkalmazza a viselkedést. Ha például egy felhasználó két házirend-útvonalon keresztül navigál a bérlőhöz, a rendszer nem kéri az identitás-szolgáltató kijelölését. Az `Application` érték azt jelzi, hogy a rendszer alkalmazza a viselkedést a kérést kérő alkalmazás összes szabályzatára. Ha például egy alkalmazásra két házirend-útvonalon navigál, a rendszer nem kéri az identitás-szolgáltató kijelölését. Az `Policy` érték azt jelzi, hogy a viselkedés csak egy házirendre vonatkozik. Egy megbízhatósági keretrendszer két házirend-útvonalon való navigálásakor például a rendszer a házirendek közötti váltáskor megkéri az identitás-szolgáltató kijelölését. |
| KeepAliveInDays | Nem | Meghatározza, hogy a felhasználó mennyi ideig maradjon bejelentkezve. Ha a 0 értéket állítja be, kikapcsolja a KMSI funkciót. További információt a [bejelentkezett adatok megtartása](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi)című témakörben talál. |
|EnforceIdTokenHintOnLogout| Nem|  Egy korábban kiállított azonosító token átadásának kényszerítése a kijelentkezési végpontra, amely a végfelhasználó aktuálisan hitelesített munkamenetével kapcsolatos mutatóként szolgál a-ügyféllel. Lehetséges értékek: `false` (alapértelmezett) vagy `true` . További információ: [webes bejelentkezés OpenID-kapcsolattal](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

A **JourneyInsights** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| TelemetryEngine | Igen | Az értéknek a számnak kell lennie `ApplicationInsights` . |
| InstrumentationKey | Igen | Az Application ininsights elem kialakítási kulcsát tartalmazó karakterlánc. |
| DeveloperMode | Igen | Lehetséges értékek: `true` vagy `false` . Ha a `true` Application Insights felgyorsítja a telemetria a feldolgozási folyamaton keresztül. Ez a beállítás jó a fejlesztéshez, de nagy mennyiségű korlátozott. A részletes tevékenységi naplók csak az egyéni szabályzatok fejlesztéséhez nyújtanak segítséget. Ne használja a fejlesztési módot éles környezetben. A naplók összegyűjtik az identitás-szolgáltatók által és a fejlesztés során eljuttatott összes jogcímet. Ha éles környezetben használja, a fejlesztő vállalja a személyes azonosításra alkalmas információk beszedésének felelősségét az App bepillantást tartalmazó naplóban. Ezeket a részletes naplókat csak akkor gyűjti a rendszer, ha ez az érték be van állítva `true` .|
| ClientEnabled | Igen | Lehetséges értékek: `true` vagy `false` . Ha `true` a a Application Insights ügyféloldali parancsfájlt küld a nyomkövetési oldal és az ügyféloldali hibák nyomon követéséhez. |
| ServerEnabled | Igen | Lehetséges értékek: `true` vagy `false` . Ha `true` a, a a meglévő USERJOURNEYRECORDER JSON-t a Application Insights egyéni eseményként küldi el. |
| TelemetryVersion | Igen | Az értéknek a számnak kell lennie `1.0.0` . |

További információkért lásd: [naplók gyűjtése](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

A Azure AD B2Cban lévő egyéni szabályzatok használatával a paramétereket egy lekérdezési karakterláncban lehet elküldeni. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. Azure AD B2C átadja a lekérdezési karakterlánc paramétereit a dinamikus HTML-fájlba, például az aspx-fájlba.

A következő példa egy nevű paramétert ad `campaignId` át `hawaii` a lekérdezési karakterláncban szereplő értékkel:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

A **ContentDefinitionParameters** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Egy olyan karakterlánc, amely tartalmazza a Content definition URI-azonosító lekérdezési karakterláncához fűzött kulcs érték párt. |

A **ContentDefinitionParameter** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Név | Igen | A kulcs-érték párok neve. |

További információ: [a felhasználói felület konfigurálása dinamikus tartalommal egyéni házirendek használatával](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Kivonatjogcím

A **kivonatjogcím** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Igen | Az értéknek a számnak kell lennie `PolicyProfile` . |

A **kivonatjogcím** a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | A technikai profil nevét tartalmazó karakterlánc. |
| Leírás | 0:1 | A technikai profil leírását tartalmazó karakterlánc. |
| Protokoll | 1:1 | Az összevonáshoz használt protokoll. |
| Metaadatok | 0:1 | A protokoll által a végponttal való kommunikációhoz használt kulcs/érték párokból álló *elemek* gyűjteménye a függő entitás és más közösségi résztvevők közötti interakció konfigurálásához. |
| OutputClaims | 1:1 | A technikai profilban kimenetként elkészített jogcím-típusok listája. Ezen elemek mindegyike egy olyan **claimType** hivatkozik, amely már definiálva van a **ClaimsSchema** szakaszban vagy egy olyan házirendben, amelyből a házirend-fájl örököl. |
| SubjectNamingInfo | 1:1 | A jogkivonatokban használt tulajdonos neve. |

A **protokoll** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Név | Igen | A technikai profil részeként használt Azure AD B2C által támogatott érvényes protokoll neve. Lehetséges értékek: `OpenIdConnect` vagy `SAML2` . Az `OpenIdConnect` érték az OpenID Connect 1,0 protokoll standard értékét jelöli az OpenID Foundation-specifikációnak megfelelően. A az `SAML2` SAML 2,0 protokoll szabványát jelöli az Oasis-specifikációnak megfelelően. |

### <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | A várt jogcím-típus neve a támogatott listában ahhoz a Szabályzathoz, amelyre a függő entitás előfizet. Ez a jogcím a technikai profil kimenetét szolgálja. |

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | A házirend fájljának **ClaimsSchema** szakaszában már definiált **claimType** mutató hivatkozás. |
| DefaultValue | Nem | Alapértelmezett érték, amely akkor használható, ha a jogcím értéke üres. |
| PartnerClaimType | Nem | A jogcímet egy másik néven küldi el a ClaimType-definícióban megadott módon. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

A **SubjectNameingInfo** elemmel szabályozhatja a jogkivonat tulajdonosának értékét:
- **JWT-jogkivonat** – a `sub` jogcím. Ez az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat, például egy alkalmazás felhasználóját érvényesíti. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. Felhasználható a biztonságos engedélyezési ellenőrzések elvégzésére, például ha a jogkivonat egy erőforrás elérésére szolgál. Alapértelmezés szerint a tulajdonos jogcímet a rendszer a címtárban lévő felhasználó objektumazonosító alapján tölti fel. További információ: [jogkivonat, munkamenet és egyszeri bejelentkezés konfigurálása](session-behavior.md).
- **SAML-jogkivonat** – a `<Subject><NameID>` Tárgy elemet azonosító elem. A NameId formátum módosítható.

A **SubjectNamingInfo** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimType | Igen | A kimeneti jogcímek **PartnerClaimType** mutató hivatkozás. A kimeneti jogcímeket a függő entitások házirendjének **OutputClaims** -gyűjteményében kell meghatározni. |
| Formátum | Nem | Az SAML függő entitások esetében az SAML-állításban visszaadott **NameId formátum** beállítására szolgál. |

Az alábbi példa bemutatja, hogyan határozhat meg egy OpenID Connect függő entitást. A tulajdonos neve információ a következőképpen van konfigurálva `objectId` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Az JWT token tartalmazza a `sub` felhasználó objectId rendelkező jogcímet:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

Az alábbi példa bemutatja, hogyan határozhat meg egy SAML függő entitást. A tulajdonos neve információ konfigurálva van `objectId` , és a NameId meg `format` lett adva:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
