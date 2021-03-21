---
title: A biztonságos pontszám nyomon követése Azure Security Center
description: Ismerje meg, hogyan érheti el és követheti nyomon a biztonságos pontszámot Azure Security Centerban.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107700"
---
# <a name="access-and-track-your-secure-score"></a>A biztonságos pontszám elérése és nyomon követése

Az alábbi szakaszokban leírtak szerint megtalálhatja a teljes biztonsági pontszámot, valamint a pontszám/előfizetés alapján a Azure Portal vagy a programozott módon:

> [!TIP]
> A pontszámok kiszámításának részletes ismertetését lásd: [számítások – a pontszám megértése](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>A portál biztonságos pontszámának beolvasása

Security Center a pontszám kiemelten jelenik meg a portálon: ez az első fő csempe az Security Center áttekintés oldalon. A csempe kiválasztásával a dedikált biztonságos pontszám oldalra kerül, ahol a pontszám az előfizetés alapján lebontva jelenik meg. Válasszon ki egy előfizetést, és tekintse meg a rangsorolt javaslatok részletes listáját, valamint azt, hogy milyen hatással lehet a szervizelését rájuk az előfizetés pontszáma. 

Az emlékeztetőhöz a biztonságos pontszám a Security Center portáljának oldalain a következő helyekről jelenik meg.

- Security Center **áttekintésében** lévő csempén (fő irányítópult):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A biztonságos pontszám Security Center irányítópultján":::

- A dedikált **biztonságos pontszám** lapon az előfizetés és a felügyeleti csoportok biztonságos pontszáma látható:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Az előfizetések biztonságos pontszáma Security Center biztonságos pontszám oldalon":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A felügyeleti csoportok biztonságos pontszáma Security Center biztonságos pontszám lapján":::

    > [!NOTE]
    > Azok a felügyeleti csoportok, amelyekhez nem rendelkezik megfelelő engedélyekkel, a pontszámát "korlátozott" értékként jelenítik meg. 

- A **javaslatok** lap tetején:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A biztonságos pontszám Security Center a javaslatok oldalon":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Szerezze be biztonságos pontszámát a REST API

A pontszám a Secure score API-n keresztül érhető el. Az API-módszerek lehetővé teszik az adatlekérdezés rugalmasságát és a biztonságos pontszámok saját jelentési mechanizmusának elkészítését az idő múlásával. Használhatja például a [Secure scores API](/rest/api/securitycenter/securescores) -t egy adott előfizetés pontszámának lekéréséhez. Emellett a [Secure score Controls API](/rest/api/securitycenter/securescorecontrols) használatával is listázhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát.

![Egyetlen biztonságos pontszám beolvasása az API-n keresztül](media/secure-score-security-controls/single-secure-score-via-api.png)

A biztonságos pontszám API-ra épülő eszközökre vonatkozó példákért tekintse meg [a GitHub-Közösség biztonságos pontszám területét](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Az Azure Resource Graph biztonságos pontszámának beolvasása

Az Azure Resource Graph azonnali hozzáférést biztosít az erőforrás-információkhoz a felhőalapú környezetekben, robusztus szűrési, csoportosítási és rendezési képességekkel. Az Azure-előfizetések programozott vagy a Azure Portalon keresztüli lekérdezésének gyors és hatékony módja. [További információ az Azure Resource Graph-ról](../governance/resource-graph/index.yml).

Több előfizetés biztonságos pontszámának elérése az Azure Resource Graph használatával:

1. A Azure Portal nyissa meg az **Azure Resource Graph Explorert**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Az Azure Resource Graph Explorer * * ajánlási oldalának elindítása" :::

1. Adja meg a Kusto-lekérdezést (az alábbi példák használatával útmutatást talál).

    - Ez a lekérdezés visszaadja az előfizetés-azonosítót, az aktuális pontszámot a pontokban és százalékban, valamint az előfizetés maximális pontszámát. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Ez a lekérdezés az összes biztonsági vezérlő állapotát adja vissza. Az egyes vezérlőknél a nem megfelelő állapotú erőforrások számát, az aktuális pontszámot és a maximális pontszámot kell megszereznie. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Válassza a **lekérdezés futtatása** lehetőséget.


## <a name="tracking-your-secure-score-over-time"></a>A biztonságos pontszám nyomon követése az idő függvényében

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Biztonságos pontszám az idő múlásával jelentés a munkafüzetek lapon

A Security Center munkafüzetek oldal egy kész jelentést tartalmaz, amely vizuálisan nyomon követheti az előfizetések és a biztonsági vezérlők pontszámait, és így tovább. További információ a Security Center-adatmennyiségű [, interaktív jelentések létrehozása című](custom-dashboards-azure-workbooks.md)témakörben található.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Az Azure Security Center munkafüzetek katalógusában a biztonságos pontszám időbeli jelentésének szakasza":::

### <a name="power-bi-pro-dashboards"></a>Irányítópultok Power BI Pro

Ha Ön egy Pro-fiókkal rendelkező Power BI felhasználó, akkor a biztonságos pontszámot az **idő múlásával** Power bi irányítópulton követheti nyomon a biztonságos pontszámot az idő múlásával, és megvizsgálhatja a módosításokat.

> [!TIP]
> Ezt az irányítópultot és más eszközöket is megtalálhatja a biztonságos programozott módon való munkavégzéshez a GitHubon a Azure Security Center Közösség dedikált területén: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Az irányítópulton a következő két jelentés található, amelyek segítenek a biztonsági állapot elemzésében:

- **Erőforrások összegzése** – az erőforrások állapotával kapcsolatos összesített információkat biztosít.
- **Biztonságos pontszámok összegzése** – összesített információkat biztosít a pontszám előrehaladásával kapcsolatban. A pontszám változásainak megtekintéséhez használja a "biztonságos pontszám az idő múlásával egy időben" diagramot. Ha drámai változást tapasztal a pontszámban, tekintse meg a "a biztonságos pontszámra hatással lehet a védett pontok" táblázatot a módosítást okozó lehetséges változásokhoz. Ez a táblázat a törölt erőforrásokat, az újonnan telepített erőforrásokat, illetve az egyik javaslatban a biztonsági állapotukban megváltoztatott erőforrásokat ismerteti.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="A választható biztonsági pontszám az idő múlásával Power BI irányítópult a biztonságos pontszám nyomon követéséhez az idő múlásával és a változások kivizsgálásával":::


## <a name="next-steps"></a>Következő lépések

Ez a cikk a biztonságos pontszám elérését és nyomon követését ismerteti. A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket:

- [Tudnivalók a javaslatok különböző elemeiről](security-center-recommendations.md)
- [Útmutató a javaslatok megoldásához](security-center-remediate-recommendations.md)
- [A GitHub-alapú eszközök megtekintése a biztonságos pontszám használatával történő programozott munkához](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)