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
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fad29c32731ee2470354a51acf32e350eb0c4cfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384872"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C naplók gyűjtése Application Insights

Ez a cikk a naplók Active Directory B2Cból (Azure AD B2C) való összegyűjtésének lépéseit ismerteti, így diagnosztizálhatja az egyéni házirendekkel kapcsolatos problémákat. Application Insights lehetővé teszi a kivételek diagnosztizálását és az alkalmazások teljesítményével kapcsolatos problémák megjelenítését. Azure AD B2C tartalmaz egy funkciót, amely az adatok Application Insights történő küldésére használható.

Az itt leírt részletes tevékenység-naplókat **csak** az egyéni szabályzatok fejlesztése során lehet engedélyezni.

> [!WARNING]
> A fejlesztési üzemmódot ne engedélyezze éles környezetben. A naplók begyűjtik az identitás-szolgáltatóktól érkező és az azoktól érkező összes jogcímet Ön, mint a fejlesztő vállalja a felelősséget a Application Insights naplókban gyűjtött személyes adatokért. A részletes naplók gyűjtése csak akkor történik meg, ha a házirend **fejlesztői módba**kerül.

## <a name="set-up-application-insights"></a>Application Insights beállítása

Ha még nem rendelkezik ilyennel, hozzon létre egy Application Insights példányt az előfizetésében.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely tartalmazza az Azure-előfizetését (nem a Azure ad B2C könyvtárat).
1. Válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs menüben.
1. Keresse meg és válassza ki **Application Insights**, majd válassza a **Létrehozás**lehetőséget.
1. Fejezze be az űrlapot, válassza a **felülvizsgálat + létrehozás**lehetőséget, majd kattintson a **Létrehozás**gombra.
1. Az üzembe helyezés befejezése után válassza az **Ugrás erőforráshoz**lehetőséget.
1. A **konfigurálás** Application Insights menüben válassza a **Tulajdonságok**lehetőséget.
1. Jegyezze fel a kialakítási **kulcsot** egy későbbi lépésben való használatra.

## <a name="configure-the-custom-policy"></a>Egyéni házirend konfigurálása

1. Nyissa meg a függő entitás (RP) fájlját, például *SignUpOrSignin.xml*.
1. Adja hozzá a következő attribútumokat a `<TrustFrameworkPolicy>` elemhez:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Ha még nem létezik, adjon hozzá egy `<UserJourneyBehaviors>` alárendelt csomópontot a `<RelyingParty>` csomóponthoz. Ezt közvetlenül a után kell elhelyezni `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Adja hozzá a következő csomópontot az elem gyermekének `<UserJourneyBehaviors>` . Ügyeljen arra, hogy `{Your Application Insights Key}` a korábban feljegyzett Application Insights rendszerállapot- **kulcsot** cserélje le.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`azt jelzi, hogy a ApplicationInsights felgyorsítja a telemetria a feldolgozási folyamaton keresztül. Kiválóan használható a fejlesztéshez, de nagy mennyiségekben korlátozott.
    * `ClientEnabled="true"`a ApplicationInsights ügyféloldali parancsfájlt küld a követési oldal nézetre és az ügyféloldali hibákra. Ezeket a **browserTimings** táblában tekintheti meg a Application Insights-portálon. A beállítással `ClientEnabled= "true"` Application Insightsokat adhat hozzá az oldal parancsfájljaihoz, és megtekintheti az oldal betöltésének és az Ajax-hívások, a számok, a böngészőbeli kivételek és az Ajax-hibák részleteit, valamint a felhasználók és a munkamenetek számát. Ez a mező nem **kötelező**, és alapértelmezés szerint be van állítva `false` .
    * `ServerEnabled="true"`a meglévő UserJourneyRecorder JSON-t egy egyéni eseményként küldi a Application Insights.

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
1. Az **Áttekintés** menüben válassza az **elemzés**lehetőséget.
1. Nyisson meg egy új fület Application Insights.

Az alábbi lista felsorolja a naplók megtekintésére használható lekérdezéseket:

| Lekérdezés | Description |
|---------------------|--------------------|
`traces` | A Azure AD B2C által generált naplók megtekintése |
`traces | where timestamp > ago(1d)` | Az elmúlt nap Azure AD B2C által generált naplók megtekintése

Előfordulhat, hogy a bejegyzések hosszúak. Exportálás CSV-be alaposabb keresés céljából.

A lekérdezéssel kapcsolatos további információkért lásd: [Azure monitorban található lekérdezések áttekintése](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>További lépések

A Közösség kidolgozta a felhasználói útvonalak megjelenítőjét, hogy segítse az identitás-fejlesztőket. Beolvassa a Application Insights példányát, és jól strukturált áttekintést nyújt a felhasználói útvonalak eseményeiről. Szerezze be a forráskódot, és telepítse azt a saját megoldásában.

A Microsoft nem támogatja a felhasználói utazási lejátszót, és a szolgáltatás szigorúan elérhetővé válik.

Megtalálhatja a megtekintő azon verzióját, amely a GitHubon Application Insights eseményeket olvas be:

[Azure-Samples/Active-Directory-B2C-Advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
