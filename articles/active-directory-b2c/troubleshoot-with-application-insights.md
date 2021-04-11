---
title: Egyéni házirendek – Application Insights
titleSuffix: Azure AD B2C
description: Application Insights beállítása az egyéni házirendek végrehajtásának nyomon követéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028743"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C naplók gyűjtése Application Insights

Ez a cikk a naplók Active Directory B2Cból (Azure AD B2C) való összegyűjtésének lépéseit ismerteti, így diagnosztizálhatja az egyéni házirendekkel kapcsolatos problémákat. Az Application Insights lehetőséget nyújt a kivételek diagnosztizálására és az alkalmazás teljesítményproblémáinak vizualizálására. Az Azure AD B2C tartalmaz egy funkciót az adatok Application Insightsba való elküldéséhez.

Az itt leírt részletes tevékenység-naplókat **csak** az egyéni szabályzatok fejlesztése során lehet engedélyezni.

> [!WARNING]
> Ne állítsa be `DeploymentMode` `Development` éles környezetekben. A naplók begyűjtik az identitás-szolgáltatóktól érkező és az azoktól érkező összes jogcímet Ön, mint a fejlesztő vállalja a felelősséget a Application Insights naplókban gyűjtött személyes adatokért. A részletes naplók gyűjtése csak akkor történik meg, ha a házirend **fejlesztői módba** kerül.

## <a name="set-up-application-insights"></a>Application Insights beállítása

Ha még nem rendelkezik ilyennel, hozzon létre egy Application Insights példányt az előfizetésében.

> [!TIP]
> Application Insights egyetlen példánya használható több Azure AD B2C bérlőhöz. Ezután a lekérdezésben szűrheti a bérlő vagy a szabályzat nevét. További információkért [tekintse meg a Application Insights](#see-the-logs-in-application-insights) mintákban található naplókat.

Ha Application Insights kilépő példányát szeretné használni az előfizetésében, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely tartalmazza az Azure-előfizetését (nem a Azure ad B2C könyvtárat).
1. Nyissa meg a korábban létrehozott Application Insights erőforrást.
1. Az **Áttekintés** lapon jegyezze fel a kialakítási **kulcsot**

Az előfizetéshez tartozó Application Insights példányának létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely tartalmazza az Azure-előfizetését (nem a Azure ad B2C könyvtárat).
1. Válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs menüben.
1. Keresse meg és válassza ki **Application Insights**, majd válassza a **Létrehozás** lehetőséget.
1. Fejezze be az űrlapot, válassza a **felülvizsgálat + létrehozás** lehetőséget, majd kattintson a **Létrehozás** gombra.
1. Az üzembe helyezés befejezése után válassza az **Ugrás erőforráshoz** lehetőséget.
1. A **konfigurálás** Application Insights menüben válassza a **Tulajdonságok** lehetőséget.
1. Jegyezze fel a kialakítási **kulcsot** egy későbbi lépésben való használatra.

## <a name="configure-the-custom-policy"></a>Egyéni házirend konfigurálása

1. Nyissa meg a függő entitás (RP) fájlját, például *SignUpOrSignin.xml*.
1. Adja hozzá a következő attribútumokat a `<TrustFrameworkPolicy>` elemhez:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Ha még nem létezik, adjon hozzá egy `<UserJourneyBehaviors>` alárendelt csomópontot a `<RelyingParty>` csomóponthoz. Ezt követően kell elhelyezni `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Adja hozzá a következő csomópontot az elem gyermekének `<UserJourneyBehaviors>` . Ügyeljen arra, hogy `{Your Application Insights Key}` a korábban feljegyzett Application Insights rendszerállapot- **kulcsot** cserélje le.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` azt jelzi, hogy a ApplicationInsights felgyorsítja a telemetria a feldolgozási folyamaton keresztül. Kiválóan használható a fejlesztéshez, de nagy mennyiségekben korlátozott. Az éles környezetben állítsa be a következőt: `DeveloperMode` `false` .
    * `ClientEnabled="true"` a ApplicationInsights ügyféloldali parancsfájlt küld a követési oldal nézetre és az ügyféloldali hibákra. Ezeket a **browserTimings** táblában tekintheti meg a Application Insights-portálon. A beállítással `ClientEnabled= "true"` Application Insightsokat adhat hozzá az oldal parancsfájljaihoz, és megtekintheti az oldal betöltésének és az Ajax-hívások, a számok, a böngészőbeli kivételek és az Ajax-hibák részleteit, valamint a felhasználók és a munkamenetek számát. Ez a mező nem **kötelező**, és alapértelmezés szerint be van állítva `false` .
    * `ServerEnabled="true"` a meglévő UserJourneyRecorder JSON-t egy egyéni eseményként küldi a Application Insights.

    Például:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Töltse fel a szabályzatot.

## <a name="see-the-logs-in-application-insights"></a>A naplók megtekintése Application Insights

Az új naplók a Application Insightsban való megtekintése előtt rövid késleltetéssel, jellemzően kevesebb mint öt perc alatt elérhető.

1. Nyissa meg a [Azure Portalban](https://portal.azure.com)létrehozott Application Insights erőforrást.
1. Az **Áttekintés** lapon válassza a **naplók** lehetőséget.
1. Nyisson meg egy új fület Application Insights.

Az alábbi lista felsorolja a naplók megtekintésére használható lekérdezéseket:

| Lekérdezés | Description |
|---------------------|--------------------|
| `traces` | Az Azure AD B2C által generált összes napló lekérése |
| `traces | where timestamp > ago(1d)` | Az elmúlt nap során Azure AD B2C által generált naplók beolvasása.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Az elmúlt két órában hibákat tartalmazó összes napló beolvasása.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Szerezze be Azure AD B2C *contoso.onmicrosoft.com* -bérlő által generált összes naplót, és a felhasználói út *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| A Azure AD B2C által generált összes napló beolvasása egy korrelációs AZONOSÍTÓhoz. Cserélje le a korrelációs azonosítót a korrelációs AZONOSÍTÓra. | 

Előfordulhat, hogy a bejegyzések hosszúak. Exportálás CSV-be alaposabb keresés céljából.

A lekérdezéssel kapcsolatos további információkért lásd: [Azure monitorban található lekérdezések áttekintése](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>A VS Code bővítménnyel kapcsolatos naplók megtekintése

Javasoljuk, hogy telepítse a [vs Code](https://code.visualstudio.com/)-hoz készült [Azure ad B2C-bővítményt](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) . A Azure AD B2C bővítménnyel a naplók a szabályzat neve, a korrelációs azonosító (az Application-elemzések a korrelációs azonosító első számjegyét) és a napló időbélyegzője alapján vannak rendszerezve. Ezzel a funkcióval a helyi időbélyeg alapján megtalálhatja a megfelelő naplót, és a Azure AD B2C által végrehajtott módon láthatja a felhasználói utat.

> [!NOTE]
> A Közösség a vs Code-bővítményt fejlesztette ki a Azure AD B2C számára, hogy segítse az identitás-fejlesztőket. A Microsoft nem támogatja a bővítményt, és a szolgáltatás szigorúan elérhetővé válik.

### <a name="set-application-insights-api-access"></a>Application Insights API-hozzáférés beállítása

A Application Insights beállítása és az egyéni házirend konfigurálása után be kell szereznie a Application Insights **API-azonosítót**, és létre kell hoznia az **API-kulcsot**. Azure AD B2C bővítmény az API-azonosítót és az API-kulcsot is használja, hogy beolvassa a Application Insights eseményeket (telemetriáiról). Az API-kulcsokat, például jelszavakat kell kezelni. Titok megőrzése.

> [!NOTE]
> Application Insights rendszerállapot-kulcsot, amelyet a Create korábbi Azure AD B2C használ a telemetriáiról küldéséhez a Application Insights. A kialakítási kulcsot csak az Azure AD B2C-házirendben használja, nem pedig a vs Code bővítményben.

Application Insights-azonosító és-kulcs beszerzése:

1. A Azure Portalban nyissa meg az alkalmazás Application Insights erőforrását.
1. Válassza a **Beállítások**, majd az **API-hozzáférés** lehetőséget.
1. Az **alkalmazás azonosítójának** másolása
1. Válassza az **API-kulcs létrehozása** lehetőséget.
1. Jelölje be az **olvasási telemetria** jelölőnégyzetet.
1. Másolja a **kulcsot** az API-kulcs létrehozása panel bezárása előtt, és mentse biztonságos helyre. Ha elveszíti a kulcsot, létre kell hoznia egy másikat.

    ![Képernyőkép, amely bemutatja, hogyan hozhat létre API-hozzáférési kulcsot.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Azure AD B2C VS Code bővítmény beállítása

Most, hogy már rendelkezik Azure Application betekintő API-AZONOSÍTÓval és kulccsal, beállíthatja a vs Code bővítményt a naplók olvasásához. A Azure AD B2C VS Code bővítmény két hatókört biztosít a beállításokhoz:

- **Felhasználói globális beállítások** – olyan beállítások, amelyek globálisan érvényesek a vs Code bármely példányára.
- **Munkaterület-beállítások** – a munkaterületen belül tárolt beállítások, és csak a munkaterület megnyitásakor érvényesek (a vs Code **Open mappa** használatával).

1. A **Azure ad B2C nyomkövetési** Explorerben kattintson a **Beállítások** ikonra.

    ![Képernyőkép, amely bemutatja az Application bepillantást.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Adja meg az Azure Application Insights **azonosítóját** és **kulcsát**.
1. Kattintson a **Mentés** gombra.

A beállítások mentése után az Application Insight-naplók megjelennek a **Azure ad B2C nyomkövetés (alkalmazás elemzése)** ablakban.

![Képernyőkép a vscode Azure AD B2C-bővítményéről, amely az Azure Application ininsight-nyomkövetést mutatja be.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>Application Insights konfigurálása éles környezetben

Az éles környezet teljesítményének és jobb felhasználói élményének javítása érdekében fontos, hogy a házirendet úgy konfigurálja, hogy figyelmen kívül hagyja a nem fontos üzeneteket. A következő konfiguráció használatával csak a kritikus hibaüzeneteket küldheti el a Application Insights. 

1. Állítsa be a `DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) attribútumát a következőre: `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Állítsa be a JourneyInsights a következőre: `DeveloperMode` [](relyingparty.md#journeyinsights) `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Töltse fel és tesztelje a szabályzatot.



## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet [elhárítani az egyéni házirendeket Azure ad B2C](troubleshoot-custom-policies.md)
