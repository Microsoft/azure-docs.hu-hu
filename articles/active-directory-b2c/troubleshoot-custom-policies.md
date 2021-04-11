---
title: Egyéni házirendek – Azure Active Directory B2C
description: Útmutató a hibák megoldásához, amikor a Azure Active Directory B2C egyéni házirendjeivel dolgozik.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103909"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Egyéni házirendek Azure AD B2C hibáinak megoldása

Ha Azure Active Directory B2C (Azure AD B2C) [egyéni házirendeket](custom-policy-overview.md)használ, problémák léphetnek fel a házirend nyelvének XML-formátumával vagy a futásidejű problémákkal kapcsolatban. Ez a cikk néhány olyan eszközt és tippet ismertet, amelyek segíthetnek a problémák felderítésében és megoldásában. 

Ez a cikk a Azure AD B2C egyéni házirend-konfigurációjának hibaelhárítására koncentrál. Nem foglalkozik a függő entitás alkalmazásával vagy az azonosító könyvtárával.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Azure AD B2C korrelációs azonosító áttekintése

Azure AD B2C korrelációs azonosító egy egyedi azonosító érték, amely az engedélyezési kérelmekhez van csatolva. Áthalad a felhasználó által végrehajtott összes előkészítési lépésen. A korrelációs AZONOSÍTÓval a következőket teheti:

- Azonosíthatja a bejelentkezési tevékenységet az alkalmazásban, és nyomon követheti a szabályzat teljesítményét.
- Keresse meg a bejelentkezési kérés Azure Application Insights naplóit.
- Adja át a korrelációs azonosítót a REST API, és használja a bejelentkezési folyamat azonosításához. 

A korrelációs azonosító minden alkalommal módosul, amikor létrejön egy új munkamenet. A szabályzatok hibakeresése során győződjön meg arról, hogy a meglévő böngésző lapjai le vannak zárva. Vagy nyisson meg egy új, privát üzemmódú böngészőt.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>A Azure AD B2C korrelációs AZONOSÍTÓjának beolvasása

A korrelációs azonosítót a Azure AD B2C regisztrációs vagy bejelentkezési oldalán találja. A böngészőben válassza a **Forrás megtekintése** lehetőséget. A korreláció megjegyzésként jelenik meg az oldal tetején.

![Képernyőkép a Azure AD B2C bejelentkezési oldalának megtekintési forrásáról.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Másolja a korrelációs azonosítót, majd folytassa a bejelentkezési folyamatot. A bejelentkezési viselkedés megfigyeléséhez használja a korrelációs azonosítót. További információ: [Hibaelhárítás a Application Insightssal](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>A Azure AD B2C korrelációs AZONOSÍTÓjának visszhangja

A korrelációs azonosítót a Azure AD B2C-tokenekben is megadhatja. A korrelációs azonosító belefoglalása:

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a város jogcímet a **ClaimsSchema** elemhez.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Nyissa meg a szabályzat függő entitásának fájlját. Például: <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> fájl. A kimeneti jogcímet a rendszer sikeres felhasználói út és az alkalmazásba való elküldés után hozzáadja a jogkivonathoz. Módosítsa a technikai profil elemet a függő entitás szakaszban a város kimeneti jogcímként való hozzáadásához.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Hibaelhárítás a Application Insights

Az egyéni szabályzatokkal kapcsolatos problémák diagnosztizálásához használja a [Application Insights](troubleshoot-with-application-insights.md). Application Insights nyomon követi az egyéni házirend felhasználói utazásának tevékenységeit. Lehetővé teszi a kivételek diagnosztizálását és a jogcímek Azure AD B2C és a technikai profilok által meghatározott különböző jogcím-szolgáltatók közötti cseréjét, például az Identitáskezelő, az API-alapú szolgáltatások, a Azure AD B2C felhasználói könyvtár és más szolgáltatások számára.  

Javasoljuk, hogy a [vs Code](https://code.visualstudio.com/) [Azure ad B2C bővítményét](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) telepítse. A Azure AD B2C bővítménnyel a naplókat a szabályzat neve, a korrelációs azonosító (Application Insights a korrelációs azonosító első számjegye) és a napló időbélyegzője alapján rendezi. Ez a funkció segít megtalálni a megfelelő naplót a helyi időbélyeg alapján, és a Azure AD B2C által végrehajtott módon megtekintheti a felhasználói utat. 

> [!NOTE]
> - Az új naplók a Application Insightsban való megtekintése előtt rövid késleltetéssel, jellemzően kevesebb mint öt perc alatt elérhető.
> - A Közösség fejlesztette ki a Visual Studio Code bővítményt a Azure AD B2C számára, hogy segítse az identitás-fejlesztőket. A Microsoft nem támogatja a bővítményt, és a szolgáltatás szigorúan elérhetővé válik.

Az egyszeri bejelentkezési folyamat több Azure Application Insights nyomkövetést is kiállíthat. A következő képernyőképen a *B2C_1A_signup_signin* szabályzat három naplóval rendelkezik. Minden napló a bejelentkezési folyamat részét képezi.

Az alábbi képernyőfelvételen a VS Code Azure AD B2C bővítménye látható az Azure Application Insights Trace Explorerben.

![Képernyőkép a VS Code Azure AD B2C bővítményéről az Azure Application Insights nyomkövetéssel.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>A nyomkövetési napló szűrése

A Azure AD B2C nyomkövetési tallózójának fókuszában kezdje el beírni a korrelációs azonosító első számjegyét, vagy a keresett időt. Megjelenik egy, a Azure AD B2C nyomkövetési tallózójának jobb felső sarkában látható szűrő mező, amely azt mutatja, hogy az eddig beírt elemek és a nyomkövetési naplók egyeztetése ki lesz emelve.  

![Képernyőkép a Azure AD B2C bővítményről Azure AD B2C nyomkövetési Explorer szűrő kiemelése.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Ha a szűrő mező fölé viszi a szűrőt, és a **szűrő engedélyezése a típuson** lehetőségre kattint, a csak a nyomkövetési naplókat fogja megjeleníteni. Az **"X" Törlés gomb** használatával törölje a szűrőt.

![Képernyőkép a Azure AD B2C Extension Azure AD B2C nyomkövetési Explorer-szűrőről.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Application Insights nyomkövetési napló részletei

Ha kijelöl egy Azure Application Insights nyomkövetést, a bővítmény a következő információkkal nyitja meg a **Application Insights részletek** ablakot:

- **Application Insights** – általános információ a nyomkövetési naplóról, beleértve a szabályzat nevét, KORRELÁCIÓs azonosítóját, az Azure Application Insights NYOMKÖVETÉSi azonosítóját és a nyomkövetés időbélyegét.
- **Technikai profilok** – a nyomkövetési naplóban megjelenő műszaki profilok listája.
- **Jogcímek** – a nyomkövetési naplóban és azok értékein megjelenő jogcímek betűrendbe szedett listája. Ha egy jogcím többször is megjelenik a nyomkövetési naplóban eltérő értékekkel, a `=>` jel a legújabb értéket jelöli. A jogcímek áttekintésével megállapíthatja, hogy helyesen vannak-e beállítva a várt jogcím-értékek. Ha például egy olyan előfeltételt tartalmaz, amely egy jogcím értékét ellenőrzi, a jogcím szakasz segítséget nyújt annak meghatározásában, hogy a várt folyamat miért viselkedik másképp.
- **Jogcím-átalakítás** – a nyomkövetési naplóban megjelenő jogcímek átalakításának listája. Minden jogcím-átalakítás tartalmazza a bemeneti jogcímeket, a bemeneti paramétereket és a kimeneti jogcímeket. A jogcím-átalakítás szakasz betekintést nyújt a beérkező és a jogcímek átalakításának eredményeibe.
- **Tokenek** – a nyomkövetési naplóban megjelenő tokenek listája. A tokenek tartalmazzák a mögöttes összevont OAuth, és az OpenId Connect Identity Provider tokenjét. Az összevont identitás-szolgáltató tokenje részletesen ismerteti, hogy az Identitáskezelő hogyan adja vissza a jogcímeket Azure AD B2C, így leképezheti az identitás-szolgáltató technikai profiljának kimeneti jogcímeit. 
- **Kivételek** – a nyomkövetési naplóban megjelenő kivételek és végzetes hibák listája.
- **Application INSIGHTS JSON** – a Application Insightsból visszaadott nyers adatok.

Az alábbi képernyőképen egy példa látható a Application Insights nyomkövetési napló részletei ablakra.  

![Képernyőkép Azure AD B2C bővítmény Azure AD B2C nyomkövetési jelentésről.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>JWT-tokenek – problémamegoldás

A JWT-tokenek érvényesítéséhez és hibakereséséhez a JWTs egy olyan webhelyről tudja dekódolni, amely a következőt használja: [https://jwt.ms](https://jwt.ms) . Hozzon létre egy olyan tesztelési alkalmazást, amely átirányítható a `https://jwt.ms` jogkivonat-ellenőrzéshez. Ha még nem tette meg, [regisztráljon egy webalkalmazást](tutorial-register-applications.md), és [engedélyezze az azonosító jogkivonat implicit támogatását](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Képernyőkép az JWT token előzetes verziójáról.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

A **Futtatás most** paranccsal `https://jwt.ms` tesztelheti a házirendeket a webes vagy a mobil alkalmazástól függetlenül. Ez a webhely úgy viselkedik, mint egy függő entitás alkalmazása. Megjeleníti a Azure AD B2C szabályzat által generált JSON webes jogkivonat (JWT) tartalmát.

## <a name="troubleshoot-saml-protocol"></a>SAML-protokoll hibáinak megoldása

A szolgáltatóval való integráció konfigurálásához és hibakereséséhez használhat egy böngésző-bővítményt az SAML protokollhoz, például az [SAML devtools bővítményt](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) a Chrome, az [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) for Firefox, vagy a [Edge vagy az IE fejlesztői eszközökhöz](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

A következő képernyőfelvétel azt mutatja be, hogy a SAML DevTools bővítmény hogyan mutatja be az SAML-kérést, Azure AD B2C küld az identitás-szolgáltatónak, valamint az SAML-választ.

![Képernyőfelvétel az SAML protokoll nyomkövetési naplójáról.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Ezen eszközök használatával megtekintheti az alkalmazás és a Azure AD B2C közötti integrációt. Például:

- Ellenőrizze, hogy az SAML-kérelem tartalmaz-e aláírást, és határozza meg, hogy melyik algoritmust használja a rendszer az engedélyezési kérelemben való bejelentkezéshez.
- Ellenőrizze, hogy a Azure AD B2C hibaüzenetet ad-e vissza.
- Ellenőrizze, hogy az állítás szakasz titkosítva van-e.
- A jogcímek nevének lekérése az identitás-szolgáltató visszaküldéséhez.

Az üzenetek cseréjét is nyomon követheti az ügyféloldali böngésző és a Azure AD B2C között a [Hegedűs](https://www.telerik.com/fiddler)használatával. Ezzel a megoldással megtekintheti, hogy a felhasználói utazás miért sikertelen a folyamat lépésein.

## <a name="troubleshoot-policy-validity"></a>Házirend érvényességének hibája

Miután befejezte a szabályzat fejlesztését, töltse fel Azure AD B2Cre a szabályzatot. lehetséges, hogy problémák merülnek fel a szabályzattal kapcsolatban. A szabályzat integritásának/érvényességének biztosításához használja az alábbi módszereket.

Az egyéni házirendek beállításának leggyakoribb hibája az XML-fájl helytelen formázása. A megfelelő XML-szerkesztő szinte elengedhetetlen. Az XML natív módon, színkódokkal jelenik meg, előre kitölti a gyakori kifejezéseket, megőrzi az indexelt XML-elemeket, és érvényesíti az XML-sémákat.

Javasoljuk, hogy használja a [Visual Studio Code](https://code.visualstudio.com/)-ot. Ezután telepítsen egy XML-bővítményt, például a [Red Hat által támogatott XML-nyelvi támogatást](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). Az XML-bővítmény segítségével érvényesítheti az XML-sémát, mielőtt feltölti az XML-fájlt az egyéni házirend [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) -sémájának meghatározásával.

Az XML-fájl társítására szolgáló stratégia segítségével a következő beállításokat adhatja hozzá a VS Code-fájlhoz, ha az XSD XML-fájlt szeretné kötni `settings.json` . Ehhez tegye a következőket:

1. A VS Code-ból kattintson a **Beállítások** elemre. További információ: [felhasználói és munkaterület-beállítások](https://code.visualstudio.com/docs/getstarted/settings).
1. Keresse meg a **fileAssociations**, majd a **bővítmény** alatt válassza ki az **XML-** t.
1. Válassza **a szerkesztés lehetőséget settings.json**.

    ![Képernyőkép a VS Code XML-séma ellenőrzéséről.](./media/troubleshoot-custom-policies/xml-validation.png)
1. A settings.jsa (z) elemnél adja hozzá a következő JSON-kódot:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

Az alábbi példa egy XML-érvényesítési hibát mutat be. Ha az egérmutatót az elem neve fölé helyezi, a bővítmény felsorolja a várt elemeket.

![Képernyőfelvétel a VS Code XML-séma érvényesítési hibáinak jelzőjéről.](./media/troubleshoot-custom-policies/xml-validation-error.png)

A következő esetben a `DisplayName` elem helyes. De nem megfelelő sorrendben. A  `DisplayName` legyen az elem előtt `Protocol` . A probléma megoldásához vigye az egérmutatót az elem fölé az `DisplayName` elemek megfelelő sorrendjére.

![Képernyőkép a VS Code XML-séma érvényesítési sorrendjének hibája miatt.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Szabályzatok és házirend-érvényesítés feltöltése

Az XML-házirend fájljának érvényesítése automatikusan történik a feltöltéskor. A legtöbb hiba miatt a feltöltés sikertelen lesz. Az érvényesítés magában foglalja a feltöltött házirend-fájlt. Emellett magában foglalja a fájlok feltöltendő fájljának láncát is (a függő entitások házirendjének fájlját, a kiterjesztések fájlját és az alapfájlt).

> [!TIP]
> Azure AD B2C a függő entitásokra vonatkozó házirend további érvényesítését is futtatja. Ha a szabályzattal kapcsolatos probléma merül fel, még akkor is, ha csak a kiterjesztési szabályzatot szerkeszti, célszerű a függő entitásra vonatkozó szabályzatot is feltölteni. 

Ez a szakasz a gyakori érvényesítési hibákat és a lehetséges megoldásokat tartalmazza.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Séma-érvényesítési hiba található... a (z) {Name} alárendelt elem érvénytelen

A házirend érvénytelen XML-elemet tartalmaz, vagy az XML-elem érvényes, de úgy tűnik, hogy nem megfelelő sorrendben van. Az ilyen típusú hibák kijavításához tekintse meg a [szabályzatok érvényességének elhárítása](#troubleshoot-policy-validity) szakaszt.

### <a name="there-is-a-duplicate-key-sequence-number"></a>Duplikált kulcs: {Number} 

A felhasználói [út](userjourneys.md) vagy az [Alútvonal](subjourneys.md) a sorrendben végrehajtott előkészítési lépések rendezett listáját tartalmazza. Miután módosította az utat, a lépéseket egymás után számozza, és nem hagyhatja 1 és N közötti egész számot.

> [!TIP]
> A [vs Code](https://code.visualstudio.com/) -hoz készült [Azure ad B2C bővítmény](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) használatával `(Shift+Ctrl+r)` átszámozhatja az összes felhasználói utazást és a sub Journeys-előkészítési lépéseket a szabályzatban.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... a várt lépés a (z) {Number} rendeléssel, de nem található...

Keresse meg az előző hibát.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>A (z) {Name} felhasználói úton a (z) {Number} előkészítési sorrendet... ezt követi a jogcím-szolgáltató kiválasztási lépése, és a jogcím-Exchange-nek kell lennie, de típusa...

A előkészítési lépés típusa `ClaimsProviderSelection` , és `CombinedSignInAndSignUp` tartalmazza a felhasználók által választható lehetőségek listáját. `ClaimsExchange`Egy vagy több jogcím-Exchange-típussal kell követnie.

A következő összehangoló lépések ezt a típust vagy hibát okozzák. A második előkészítési lépésnek a következőnek kell lennie: `ClaimsExchange` , nem `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... {Number} lépés, 2 jogcím-csere esetén. A jogcím-szolgáltató kiválasztása előtt meg kell határozni, hogy mely jogcímeket lehet használni

Az előkészítési lépés típusa csak `ClaimsExchange` egyetlen lehet `ClaimsExchange` , ha az előző lépés típusa `ClaimsProviderSelection` vagy `CombinedSignInAndSignUp` . A következő előkészítési lépések okozzák ezt a típusú hibát. A hatodik lépés két jogcím-Exchange-t tartalmaz. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Az ilyen típusú hibák kijavításához használjon két előkészítési lépést. Minden előkészítési lépés egy jogcím-Exchange-sel.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Duplikált kulcs ({Name})

Egy útra több is van `ClaimsExchange` `Id` . Az alábbi lépések ezt a típusú hibát okozzák. Az azonosító *AADUserWrite* kétszer jelenik meg a felhasználói úton.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Az ilyen típusú hibák kijavításához módosítsa a nyolcadik lépéseket a jogcím-csere egyedi névre, például a *Call-REST-API-* ra.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... hivatkozik a (z) {jogcím neve} azonosítójú ClaimType, de sem a házirend, sem annak alapszabályzata nem tartalmaz ilyen elemet.

Ez a típusú hiba akkor fordul elő, ha a házirend olyan jogcímre hivatkozik, amely nincs deklarálva a [jogcím-sémában](claimsschema.md). A jogcímeket a szabályzatban szereplő fájlok legalább egyikében meg kell adni. 

Például egy műszaki profil a *schoolId* kimeneti jogcímet. A kimeneti jogcímek *schoolId* azonban soha nem deklarálják a házirendben vagy egy Előd-házirendben.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Az ilyen típusú hibák kijavításához győződjön meg arról, hogy az `ClaimTypeReferenceId` érték hibásan van-e beállítva, vagy nem létezik a sémában. Ha a jogcím a bővítmények házirendben van definiálva, de az alapszabályzatban is használatban van. Győződjön meg arról, hogy a jogcím a használatban lévő szabályzatban vagy egy felső szintű szabályzatban van definiálva.

Ha hozzáadja a jogcímet a jogcím-sémához, azzal megoldja az ilyen típusú hibát.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... egy ClaimsTransformation mutató hivatkozást tesz elérhetővé az AZONOSÍTÓval...

A hiba oka a jogcím hibája esetében hasonló. Keresse meg az előző hibát.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>A felhasználó jelenleg "yourtenant.onmicrosoft.com" bérlőként van naplózva...

Olyan fiókkal kell bejelentkeznie, amely nem egyezik meg a feltölteni kívánt szabályzattal. Ha például bejelentkezik a szolgáltatásba admin@contoso.onmicrosoft.com , a házirend beállítása a következőre: `TenantId` `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Győződjön meg arról, hogy a `TenantId` `<TrustFrameworkPolicy\>` és az elemek értéke `<BasePolicy\>` megegyezik a cél Azure ad B2C Bérlővel.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>A jogcím típusa ({Name}) a függő entitás technikai profiljának kimeneti jogcíme, de nem a felhasználói utazás lépéseinek egyik lépése sem a kimeneti jogcím...

A függő entitások házirendjében hozzáadott egy kimeneti jogcímet, de a kimeneti jogcímek nem a felhasználói út bármely lépésében kimeneti jogcímek. Azure AD B2C nem tudja olvasni a jogcím értékét a jogcímek táskából.

A következő példában a *schoolId* jogcím a függő entitás technikai profiljának kimeneti jogcíme, de a *SignUpOrSignIn* felhasználói utazás egyik lépéseiben sem a kimeneti jogcímek.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Az ilyen típusú hibák kijavításához győződjön meg arról, hogy a kimeneti jogcímek legalább egy, a technikai profil kimeneti jogcímek gyűjteményében jelennek meg. Ha a felhasználói utazás nem tudja kiállítani a jogcímet, a függő entitás technikai profiljában állítson be egy alapértelmezett értéket, például üres karakterláncot.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>A bemeneti karakterlánc formátuma nem megfelelő.

Helytelen típusú értéket állít be egy másik típusú jogcímre. Meghatározhatja például az egész szám típusú jogcímet.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Ezután megpróbálja beállítani a sztring értékét:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Az ilyen típusú hibák kijavításához ügyeljen arra, hogy a helyes értéket adja meg, például: `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>A (z) {Name} bérlő már rendelkezik egy {Name} azonosítójú házirenddel. Egy másik, ugyanazzal az azonosítóval rendelkező szabályzat nem tárolható

Megpróbál feltölteni egy szabályzatot a bérlőbe, de már fel van töltve egy azonos nevű szabályzat a bérlőbe. 

Az ilyen típusú hibák kijavításához, amikor feltölti a szabályzatot, jelölje be az **egyéni házirend felülírása, ha már létezik** jelölőnégyzetet.

![Képernyőkép, amely bemutatja, hogyan írhatja felül az egyéni házirendet, ha az már létezik.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [gyűjthet Azure Active Directory B2C naplókat a Application Insights](troubleshoot-with-application-insights.md).

