---
title: A felhasználó viselkedésének nyomon követése Application Insights
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti az eseménynaplókat a Application Insights Azure AD B2C felhasználói útvonalakon.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218553"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>A felhasználó viselkedésének nyomon követése Azure Active Directory B2C a Application Insights használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

A Azure Active Directory B2C (Azure AD B2C) a Azure AD B2C számára biztosított rendszerállapot-kulcs használatával támogatja az esemény-adatok közvetlenül a [Application Insightsre](../azure-monitor/app/app-insights-overview.md) való küldését. Application Insights technikai profillal részletes és testreszabott eseménynaplókat kaphat a felhasználói számára:

* A felhasználói viselkedés megállapítása.
* Saját házirendek fejlesztése a fejlesztésben vagy éles környezetben.
* Teljesítmény mérése.
* Értesítések létrehozása Application Insightsból.

## <a name="overview"></a>Áttekintés

Az egyéni eseménynaplók engedélyezéséhez vegyen fel egy Application Insights technikai profilt. A technikai profilban meg kell határoznia a Application Insights rendszerállapot-kulcsot, az esemény nevét és a rögzíteni kívánt jogcímeket. Egy esemény elküldéséhez a technikai profilt a rendszer a [felhasználói úton](userjourneys.md)történő előkészítési lépésként adja hozzá.

A Application Insights használatakor vegye figyelembe a következőket:

- Az új naplók a Application Insightsban való használata előtt rövid késleltetéssel, jellemzően kevesebb mint öt perc alatt elérhetők.
- Azure AD B2C lehetővé teszi a rögzítendő jogcímek kiválasztását. Ne tartalmazza a személyes adattal rendelkező jogcímeket.
- Egy felhasználói munkamenet rögzítéséhez a korrelációs azonosító használatával lehet egyesíteni az eseményeket. 
- Hívja meg a Application Insights műszaki profilt közvetlenül egy [felhasználói útra](userjourneys.md) vagy egy [alárendelt utazásra](subjourneys.md). Ne használja a Application Insights műszaki profilt [érvényesítési technikai profilként](validation-technical-profile.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Ha a Azure AD B2C használatával Application Insightst használ, mindössze annyit kell tennie, hogy létrehoz egy erőforrást, és beolvassa a kialakítási kulcsot. További információ: [Application Insights erőforrás létrehozása](../azure-monitor/app/create-new-resource.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure-előfizetését tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az előfizetést tartalmazó könyvtárat. Ez a bérlő nem az Ön Azure AD B2C bérlője.
3. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában, majd keresse meg és válassza a **Application Insights** lehetőséget.
4. Kattintson a **Létrehozás** lehetőségre.
5. Adja meg az erőforrás **nevét** .
6. Az **alkalmazás típusa** mezőben válassza a **ASP.net webalkalmazás** lehetőséget.
7. Az **erőforráscsoport** területen válasszon egy meglévő csoportot, vagy adjon meg egy új csoport nevét.
8. Kattintson a **Létrehozás** lehetőségre.
4. A Application Insights erőforrás létrehozása után nyissa meg, bontsa ki az **Essentials** csomópontot, és másolja a kialakítási kulcsot.

![Application Insights áttekintése és kialakítási kulcsa](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Jogcímek meghatározása

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A [jogcím-séma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket.

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema** elemhez. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Új technikai profilok hozzáadása

A technikai profilok az egyéni szabályzatban szereplő függvények tekinthetők. Ez a táblázat a munkamenetek megnyitásához és az események közzétételéhez használt technikai profilokat határozza meg. A megoldás a [technikai profil integrációs](technicalprofiles.md#include-technical-profile) módszerét használja. Ha a technikai profil egy másik technikai profilt tartalmaz a beállítások módosításához vagy új funkció hozzáadásához.

| Technikai profil | Feladat |
| ----------------- | -----|
| AppInsights-Common | A közös műszaki profil a konfiguráció közös készletével. Beleértve a Application Insights rendszerállapot-kulcsot, a felvenni kívánt jogcímek gyűjteményét és a fejlesztői módot. A következő technikai profilok tartalmazzák a közös technikai profilt, és további jogcímeket (például az esemény nevét) adhatnak hozzá. |
| AppInsights-SignInRequest | Egy olyan eseményt rögzít, amely `SignInRequest` jogcímeket tartalmaz, ha bejelentkezési kérés érkezett. |
| AppInsights-UserSignUp | Egy `UserSignUp` eseményt jegyez be, amikor a felhasználó elindítja a bejelentkezési lehetőséget egy bejelentkezési vagy bejelentkezési úton. |
| AppInsights-SignInComplete | Egy `SignInComplete` hitelesítés sikeres befejezésére vonatkozó eseményt rögzít, ha a rendszer elküldje egy jogkivonatot a függő entitás alkalmazásnak. |

Adja hozzá a profilokat a *TrustFrameworkExtensions.xml* fájlhoz az alapszintű csomagból. Adja hozzá ezeket az elemeket a **ClaimsProviders** elemhez:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Módosítsa a kialakítási kulcsot a `AppInsights-Common` technikai profilban a Application Insights erőforrás által biztosított GUID azonosítóra.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Technikai profilok hozzáadása előkészítési lépésként

Hívja meg a `AppInsights-SignInRequest` 2. lépést a beléptetési/regisztrációs kérések nyomon követéséhez:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Közvetlenül a előkészítési lépés *előtt* `SendClaims` adjon hozzá egy új lépést, amely meghívja a-t `AppInsights-UserSignup` . Akkor aktiválódik, ha a felhasználó kiválasztja a regisztrációs vagy bejelentkezési útvonalon a regisztráció gombot.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Közvetlenül a előkészítési lépés után hívja meg a következőt: `SendClaims` `AppInsights-SignInComplete` . Ez a lépés egy sikeres befejezett utazást mutat be.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Az új előkészítési lépések hozzáadása után a lépéseket szekvenciálisan számozva, az 1 és N közötti egész számok kihagyása nélkül.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Töltse fel a fájlt, futtassa a szabályzatot, és tekintse meg az eseményeket

Mentse és töltse fel a *TrustFrameworkExtensions.xml* fájlt. Ezután hívja meg a függő entitásra vonatkozó házirendet az alkalmazásból, vagy használja a **Futtatás most parancsot** a Azure Portal. Várjon egy percet, és az események Application Insights lesznek elérhetők.

1. Nyissa meg a **Application Insights** erőforrást a Azure Active Directory-bérlőben.
2. Válassza a **használat**, majd az **események** lehetőséget.
3. Állítsa **be** az **elmúlt órában** és  **3 percen belül**.  Előfordulhat, hogy a **frissítés** elemre kell kattintania az eredmények megtekintéséhez.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>További adatok gyűjtése

Az üzleti igényeknek megfelelően érdemes több jogcímet rögzíteni. Jogcímek hozzáadásához először [Definiáljon egy jogcímet](#define-claims), majd adja hozzá a jogcímet a bemeneti jogcímek gyűjteményéhez. A *AppInsights-közös* technikai profilhoz hozzáadott jogcímek az összes eseményben megjelennek. Az adott technikai profilhoz hozzáadott jogcímek csak az adott eseményen fognak megjelenni. A bemeneti jogcím elem a következő attribútumokat tartalmazza:

- **ClaimTypeReferenceId** – a jogcím típusára mutató hivatkozás. 
- **PartnerClaimType** – az Azure-eredményekben megjelenő tulajdonság neve. Használja a szintaxisát `{property:NAME}` , ahol a `NAME` tulajdonságot az eseményhez adja a rendszer.
- **DefaultValue** – a rögzítendő előre definiált érték, például az esemény neve. A felhasználói úton használt jogcím, például az identitás-szolgáltató neve. Ha a jogcím üres, a rendszer az alapértelmezett értéket fogja használni. Például a `identityProvider` jogcímet az összevonási technikai profilok, például a Facebook állítja be. Ha a jogcím üres, az azt jelzi, hogy a felhasználó helyi fiókkal jelentkezik be. Így az alapértelmezett érték a *helyi*. Egy [jogcímet](claim-resolver-overview.md) is rögzíthet egy környezetfüggő értékkel, például az alkalmazás azonosítójával vagy a felhasználó IP-címével.

### <a name="manipulating-claims"></a>Jogcímek módosítása

A bemeneti jogcímek [átalakításával](custom-policy-trust-frameworks.md#manipulating-your-claims) módosíthatók a bemeneti jogcímek, vagy újakat hozhatnak elő, mielőtt elküldené a Application Insights. A következő példában a technikai profil tartalmazza a *CheckIsAdmin* bemeneti jogcímek átalakítását. 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Események hozzáadása

Esemény hozzáadásához hozzon létre egy új technikai profilt, amely tartalmazza a *AppInsights-közös* technikai profilt. Ezután adja hozzá a technikai profilt a [felhasználói útra](custom-policy-trust-frameworks.md#orchestration-steps)való előkészítési lépésként. Ha szükséges, az [előfeltétel](userjourneys.md#preconditions) használatával aktiválja az eseményt. Például csak akkor jelentse az eseményt, ha a felhasználók az MFA-n keresztül futnak.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Most, hogy rendelkezik egy technikai profillal, adja hozzá az eseményt a felhasználói útra. Ezután a lépéseket egymás után számozza, és nem hagyhatja 1 és N közötti egész számot.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Fejlesztői üzemmód engedélyezése

Ha a Application Insights az események definiálására használja, jelezheti, hogy engedélyezve van-e a fejlesztői mód. A fejlesztői mód vezérli az események pufferelését. A minimális méretű eseménnyel rendelkező fejlesztési környezetekben a fejlesztői üzemmód engedélyezésekor a rendszer azonnal elvégzi az események küldését Application Insights. Az alapértelmezett érték `false`. Ne engedélyezze a fejlesztői üzemmódot éles környezetekben.

A fejlesztői mód engedélyezéséhez a *AppInsights-Common* Technical profilban módosítsa a `DeveloperMode` metaadatokat a következőre `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetria letiltása

Az alkalmazás-betekintési naplók letiltásához a *AppInsights-Common* Technical profilban módosítsa a metaadatokat a következőre `DisableTelemetry` `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre egyéni KPI-irányítópultokat az Azure Application Insights használatával](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end