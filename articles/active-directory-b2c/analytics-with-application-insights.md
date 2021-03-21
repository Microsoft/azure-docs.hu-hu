---
title: Felhasználói viselkedés nyomon követése Application Insights használatával
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
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645925"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>A felhasználó viselkedésének nyomon követése Azure AD B2Cban Application Insights használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) esetében a Azure AD B2Choz megadott rendszerállapot-kulcs használatával közvetlenül [Application Insights](../azure-monitor/app/app-insights-overview.md) küldhet az események adataira. Application Insights technikai profillal részletes és testreszabott eseménynaplókat kaphat a felhasználói számára:

- A felhasználói viselkedés megállapítása.
- Saját házirendek fejlesztése a fejlesztésben vagy éles környezetben.
- Teljesítmény mérése.
- Értesítések létrehozása Application Insightsból.

## <a name="overview"></a>Áttekintés

Az egyéni eseménynaplók engedélyezéséhez adjon hozzá egy Application Insights technikai profilt. A technikai profilban meg kell határoznia a Application Insights kialakítási kulcsot, az esemény nevét és a rögzíteni kívánt jogcímeket. Egy esemény közzétételéhez adja hozzá a technikai profilt a [felhasználói utazás](userjourneys.md)előkészítési lépéseként.

Application Insights használatakor vegye figyelembe a következőket:

- Az új naplók a Application Insightsban való rendelkezésre állása előtt rövid késleltetéssel, jellemzően kevesebb, mint öt percet vesznek igénybe.
- Azure AD B2C lehetővé teszi, hogy kiválassza a rögzíteni kívánt jogcímeket. Ne tartalmazza a személyes adattal rendelkező jogcímeket.
- Felhasználói munkamenet rögzítéséhez használhat korrelációs azonosítót az események egyesítéséhez.
- Hívja meg a Application Insights műszaki profilt közvetlenül egy [felhasználói útra](userjourneys.md) vagy egy [alárendelt útra](subjourneys.md). Ne használjon Application Insights műszaki profilt [érvényesítési technikai profilként](validation-technical-profile.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Ha a Azure AD B2C használatával Application Insights használ, mindössze annyit kell tennie, hogy létrehoz egy erőforrást, és beolvassa a kialakítási kulcsot. További információ: [Application Insights erőforrás létrehozása](../azure-monitor/app/create-new-resource.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy az Azure-előfizetéssel rendelkező könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki az Azure-előfizetését tartalmazó könyvtárat. Ez a bérlő nem a Azure AD B2C bérlője.
1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában, majd keresse meg és válassza a **Application Insights** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. A **név** mezőben adja meg az erőforrás nevét.
1. Az **alkalmazás típusa** mezőben válassza a **ASP.net webalkalmazás** lehetőséget.
1. Az **erőforráscsoport** területen válasszon egy meglévő csoportot, vagy adjon meg egy új csoport nevét.
1. Válassza a **Létrehozás** lehetőséget.
1. Nyissa meg az új Application Insights erőforrást, bontsa ki az **Essentials** elemet, és másolja a kialakítási kulcsot.

![A rendszerállapot-kulcsot megjelenítő képernyőkép a Application Insights áttekintés lapján.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Jogcímek meghatározása

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A jogcímeket a [ClaimsSchema elemben](claimsschema.md)deklarálhatja.

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. A fájl a következőhöz hasonló lehet: `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha nem látja az elemet, adja hozzá.
1. Keresse meg a **ClaimsSchema** elemet. Ha nem látja az elemet, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema** elemhez:

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

A technikai profilok az egyéni szabályzatban szereplő függvények tekinthetők. Ezek a függvények a [technikai profilok bevonásának](technicalprofiles.md#include-technical-profile) módszerét használják, ahol a technikai profil egy másik technikai profilt tartalmaz, és megváltoztatja a beállításokat, vagy új funkciókat bővít. A következő táblázat a munkamenet megnyitásához és az események közzétételéhez használt technikai profilokat határozza meg.

| Technikai profil | Feladat |
| ----------------- | -----|
| AppInsights-Common | Az általános műszaki profil tipikus konfigurációval. Magában foglalja a Application Insights kialakítási kulcsot, a rekordokat, valamint a fejlesztői módot. A többi technikai profil magában foglalja a közös technikai profilt, és további jogcímeket, például az esemény nevét adja hozzá. |
| AppInsights-SignInRequest | Egy **SignInRequest** eseményt jegyez be, ha bejelentkezési kérés érkezett. |
| AppInsights-UserSignUp | **UserSignUp** -eseményt jegyez be, amikor a felhasználó a regisztrációs vagy bejelentkezési úton indítja el a regisztrálási lehetőséget. |
| AppInsights-SignInComplete | **SignInComplete** eseményt rögzít a sikeres hitelesítés után, amikor a rendszer elküldje egy jogkivonatot a függő entitás alkalmazásnak. |

Nyissa meg a *TrustFrameworkExtensions.xml* fájlt az alapszintű csomagból. Adja hozzá a technikai profilokat a **ClaimsProvider** elemhez:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Vegyen fel új, a technikai profilokra hivatkozó lépéseket.

> [!IMPORTANT]
> Az új előkészítési lépések hozzáadása után a lépéseket szekvenciálisan számozva, az 1 és N közötti egész számok kihagyása nélkül.

1. Hívás `AppInsights-SignInRequest` második előkészítési lépésként. Ez a lépés azt követi nyomon, hogy érkezett-e regisztrációs vagy bejelentkezési kérelem.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. A előkészítési `SendClaims` lépés előtt adjon hozzá egy új lépést, amely meghívja a-t `AppInsights-UserSignup` . Akkor aktiválódik, ha a felhasználó kiválasztja a regisztrációs vagy bejelentkezési úton való regisztráció gombot.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. A előkészítési lépés után hívja meg a következőt: `SendClaims` `AppInsights-SignInComplete` . Ez a lépés egy sikeres befejezett utazást mutat be.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Töltse fel a fájlt, futtassa a szabályzatot, és tekintse meg az eseményeket

Mentse és töltse fel a *TrustFrameworkExtensions.xml* fájlt. Ezután hívja meg a függő entitás házirendjét az alkalmazásból, vagy használja a **Futtatás most parancsot** a Azure Portal. Várja meg, hogy az események elérhetők legyenek Application Insightsban.

1. Nyissa meg a **Application Insights** erőforrást a Azure Active Directory-bérlőben.
1. Válassza a **használat**, majd az **események** lehetőséget.
1. Állítsa **be** az **elmúlt órában** és  **3 percen belül**. Előfordulhat, hogy frissítenie kell az ablakot az eredmények megtekintéséhez.

![Képernyőkép, amely Application Insights esemény statisztikáit jeleníti meg.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>További adatok gyűjtése

Az üzleti igényeknek megfelelően érdemes lehet további jogcímeket rögzíteni. Jogcímek hozzáadásához először [Definiáljon egy jogcímet](#define-claims), majd adja hozzá a jogcímet a bemeneti jogcímek gyűjteményéhez. A **AppInsights-közös** technikai profilhoz hozzáadott jogcímek minden eseményen megjelennek. Az adott technikai profilhoz hozzáadott jogcímek csak az adott eseményen jelennek meg. A bemeneti jogcím elem a következő attribútumokat tartalmazza:

- A **ClaimTypeReferenceId** a jogcím típusára mutató hivatkozás.
- A **PartnerClaimType** az Azure-adatfelismerésekben megjelenő tulajdonság neve. Használja a szintaxisát `{property:NAME}` , ahol az `NAME` az eseményhez hozzáadott tulajdonság.
- A **DefaultValue** a rögzítendő előre definiált érték, például egy esemény neve. Ha a felhasználói úton használt jogcímek üresek, az alapértelmezett érték lesz érvényben. Például a `identityProvider` jogcímet az összevonási technikai profilok, például a Facebook állítja be. Ha a jogcím üres, az azt jelzi, hogy a felhasználó helyi fiókkal jelentkezett be. Így az alapértelmezett érték a **helyi**. A [jogcímek feloldóját](claim-resolver-overview.md) egy környezetfüggő értékkel is rögzítheti, például az alkalmazás azonosítóját vagy a felhasználó IP-címét.

### <a name="manipulate-claims"></a>Jogcímek módosítása

A bemeneti jogcímek [átalakításával](custom-policy-trust-frameworks.md#manipulating-your-claims) módosíthatók a bemeneti jogcímek, vagy újakat hozhatnak elő, mielőtt elküldené őket a Application Insightsba. A következő példában a technikai profil tartalmazza a `CheckIsAdmin` bemeneti jogcímek átalakítását.

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

Esemény hozzáadásához hozzon létre egy új technikai profilt, amely tartalmazza a `AppInsights-Common` technikai profilt. Ezt követően adja hozzá az új technikai profilt egy előkészítési lépésként a [felhasználói útra](custom-policy-trust-frameworks.md#orchestration-steps). Ha elkészült, az [előfeltétel](userjourneys.md#preconditions) elem használatával aktiválhatja az eseményt. Például csak akkor jelentse az eseményt, ha a felhasználók többtényezős hitelesítéssel futnak.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Ha hozzáad egy eseményt a felhasználói útra, ne felejtse el átszámozni a lépéseket egymás után.

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

Ha az események definiálásához Application Insights használ, jelezheti, hogy engedélyezve van-e a fejlesztői mód. A fejlesztői mód vezérli az események pufferelését. A minimális méretű eseménnyel rendelkező fejlesztési környezetekben a fejlesztői üzemmód engedélyezésekor a rendszer azonnal elvégzi az események küldését Application Insights. Az alapértelmezett érték `false`. Ne engedélyezze a fejlesztői üzemmódot éles környezetekben.

A fejlesztői mód engedélyezéséhez módosítsa a `DeveloperMode` metaadatokat a `true` `AppInsights-Common` technikai profilban:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetria letiltása

Application Insights naplók letiltásához módosítsa a `DisableTelemetry` metaadatokat a `true` `AppInsights-Common` technikai profilban:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [hozhat létre egyéni KPI-irányítópultokat az Azure Application Insights használatával](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end