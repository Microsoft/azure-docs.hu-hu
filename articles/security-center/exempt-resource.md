---
title: Egy Azure Security Center javaslat kivétele egy erőforrás, előfizetés, felügyeleti csoport és biztonsági pontszám alól
description: Megtudhatja, hogyan hozhat létre olyan szabályokat, amelyek mentesítik a biztonsági javaslatokat az előfizetések vagy felügyeleti csoportok alól, és megakadályozhatják, hogy azok hatással vannak a biztonsági pontszámra
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 13abb35d0fa9ad3ee949b6edf5205de601a02956
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718556"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Erőforrások és javaslatok kivétele a biztonsági pontszám alól 

Minden biztonsági csapat egyik fő prioritása annak biztosítása, hogy az elemzők a szervezet számára fontos feladatokra és incidensekre összpontosítva tudjanak koncentrálni. Security Center funkciókkal rendelkezik a felhasználói élmény testreszabására, és annak biztosítása érdekében, hogy a biztonsági pontszám tükrözze a szervezet biztonsági prioritásait. A **kivétel** lehetősége egy ilyen funkció.

Amikor megvizsgálja a biztonsági javaslatokat a Azure Security Center, az egyik első áttekintett információ az érintett erőforrások listája.

Időnként egy olyan erőforrás is megjelenik a listában, amelyről úgy érzi, hogy nem kellene szerepelnie a listán. Vagy egy javaslat olyan hatókörben fog jelenni, amelyhez úgy érzi, hogy nem tartozik. Előfordulhat, hogy az erőforrást olyan folyamat szervizálta, amelyet nem követnek nyomon Security Center. Előfordulhat, hogy a javaslat nem megfelelő egy adott előfizetéshez. Vagy lehet, hogy a szervezet egyszerűen úgy döntött, hogy elfogadja az adott erőforrással vagy javaslattal kapcsolatos kockázatokat.

Ilyen esetekben kivételt hozhat létre egy javaslathoz a következő esetekben:

- **Mentesítsen egy** erőforrást, hogy az ne legyen felsorolva a nem megfelelő erőforrásokkal a listában a jövőben, és nincs hatással a biztonsági pontszámra. Az erőforrás nem alkalmazhatóként jelenik meg, és az ok "kivételként" jelenik meg a kiválasztott indoklással együtt.

- **Mentesítsen egy előfizetést** vagy felügyeleti csoportot, hogy a javaslat ne befolyásolja a biztonsági pontszámot, és a jövőben ne legyen látható az előfizetés vagy a felügyeleti csoport számára. Ez a meglévő erőforrásokra és a jövőben létrehozott erőforrásokra vonatkozik. A javaslat a kiválasztott hatókörnek megfelelő indoklással lesz megjelölve.

## <a name="availability"></a>Rendelkezésre állás

| Szempont                          | Részletek                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadási állapot:                  | Előnézet<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Árképzés:                        | Ez egy prémium szintű Azure Policy funkció, amely további Azure Defender nélküli szolgáltatás. Más felhasználókra a jövőben további díjak is vonatkozhatnak.                                                                                                                                                                 |
| Szükséges szerepkörök és engedélyek: | **Tulajdonos** vagy **erőforrás-szabályzat közreműködője** kivétel létrehozásához<br>Szabály létrehozásához engedélyekre van szüksége a szabályzatok szerkesztéséhez a Azure Policy.<br>További információ: [Azure RBAC-engedélyek a Azure Policy.](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)                                            |
| Korlátozások:                    | Kivételeket csak a Security Center alapértelmezett kezdeményezésében, az Azure [Security Benchmarkban](https://docs.microsoft.com/security/benchmark/azure/introduction)vagy a megadott szabványos szabályozási kezdeményezésekben szereplő javaslatokhoz lehet létrehozni. Az egyéni kezdeményezésekből létrehozott javaslatok nem képezhetnek kivételt. További információ a szabályzatok, kezdeményezések [és javaslatok közötti kapcsolatokról.](security-policy-concept.md) |
| Felhők:                         | ![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Országos/szuverén (US Gov, china gov, other gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Kivétel meghatározása

Az előfizetések, felügyeleti csoportok vagy erőforrások Security Center biztonsági javaslatainak finomhangolása érdekében létrehozhat egy kivételszabályt a következőre:

- Megjelöl egy adott **javaslatot,** vagy "enyhített" vagy "kockázat elfogadva" megjelölést. Létrehozhat javaslati kivételeket egy előfizetéshez, több előfizetéshez vagy egy teljes felügyeleti csoporthoz.
- Jelölheti **meg egy vagy több erőforrást** "mérsékelt" vagy "elfogadott kockázatként" egy adott javaslathoz.

> [!NOTE]
> Kivételeket csak a Security Center alapértelmezett kezdeményezésében, az Azure Biztonsági teljesítménytesztben vagy a megadott szabványos szabályozási kezdeményezésekben szereplő javaslatokhoz lehet létrehozni. Az előfizetéséhez rendelt egyéni kezdeményezésekből létrehozott javaslatok nem képeznek kivételt. További információ a szabályzatok, kezdeményezések [és javaslatok közötti kapcsolatokról.](security-policy-concept.md)

> [!TIP]
> Az API-val kivételeket is létrehozhat. Egy példa JSON-t és a vonatkozó struktúrák magyarázatát a [kivételi struktúra Azure Policy meg.](../governance/policy/concepts/exemption-structure.md)

Kivételszabály létrehozása:

1. Nyissa meg az adott javaslathoz a javaslatok részleteit tartalmazó oldalt.

1. Az oldal tetején található eszköztáron válassza a Kivétel **lehetőséget.**

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Hozzon létre egy kivételszabályt, hogy egy javaslat mentesülni fog egy előfizetésből vagy felügyeleti csoportból.":::

1. A Kivétel **panelen:**
    1. Válassza ki a kivételszabály hatókörét:
        - Ha felügyeleti csoportot választ, a javaslat mentesül a csoporton belüli összes előfizetés alól
        - Ha ezt a szabályt úgy hoz létre, hogy egy vagy több erőforrást mentesítsen a javaslat alól, válassza a "Kiválasztott erőforrások" lehetőséget, és válassza ki a megfelelőket a listából

    1. Adja meg a kivételszabály nevét.
    1. Ha szükséges, állítson be lejárati dátumot.
    1. Válassza ki a kivétel kategóriáját:
        - Feloldva külső félen **keresztül (enyhítve)** – ha olyan külső szolgáltatást használ, Security Center nem azonosították. 

            > [!NOTE]
            > Ha enyhítettként mentesít egy javaslatot, akkor nem kap a biztonsági pontszámhoz vezető pontokat. Mivel azonban a  nem megfelelő erőforrásokkal nem távolít el pontokat, az eredmény az, hogy a pontszám növekedni fog.

        - **Kockázat elfogadva (ellenben)** – ha úgy döntött, hogy elfogadja a javaslat mérhetetlenségének kockázatát
    1. Ha szükséges, adjon meg egy leírást.
    1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="A kivételszabály létrehozására vonatkozó lépések, amelyek kivételt képeznek egy javaslat alól az előfizetés vagy a felügyeleti csoport alól":::

    A kivétel életbe lépése után (akár 30 percig is eltarthat):
    - A javaslat vagy az erőforrások nem befolyásolják a biztonsági pontszámot.
    - Ha adott erőforrásokat mentesített, azok a javaslat részleteit tartalmazó oldal **Nem** alkalmazható lapján lesznek felsorolva.
    - Ha kivételt adott ki egy javaslat alól, az alapértelmezés szerint el lesz rejtve Security Center javaslatok oldalán. Ennek az az oka, hogy az oldalon található **Javaslatállapot** szűrő alapértelmezett beállításai kizárják a **Nem alkalmazható javaslatokat.** Ugyanez igaz akkor is, ha egy biztonsági vezérlő minden ajánlását mentesíti.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="A javaslatok Azure Security Center lapon található alapértelmezett szűrők elrejtik a nem alkalmazható javaslatokat és biztonsági vezérlőket":::

    - A javaslat részleteit tartalmazó oldal tetején található információs sáv frissíti a mentesített erőforrások számát:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="A mentesített erőforrások száma":::

1. A mentesített erőforrások áttekintéshez nyissa meg a **Nem alkalmazható** lapot:

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Kivétel módosítása":::

    Az egyes kivételek okát a táblázat tartalmazza (1).

    Kivétel módosításához vagy törléséhez válassza a három pont (...) menüt az itt látható módon (2).

1. Az előfizetés összes kivételszabályának áttekintéséhez válassza a **Kivételek megtekintése** lehetőséget az információs sávon:

    > [!IMPORTANT]
    > Az egyes javaslatokhoz kapcsolódó kivételeket a megfelelő hatókör és javaslatnév alapján szűrheti.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy kivételoldala":::

    > [!TIP]
    > Másik lehetőségként [a Azure Resource Graph javaslatokat is találhat a kivételekhez.](#find-recommendations-with-exemptions-using-azure-resource-graph)

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Az előfizetésben létrehozott kivételek figyelése

Ahogy azt korábban említettük ezen a lapon, a kivételszabályok hatékony eszközök, amelyek részletesen szabályozhatóak az előfizetések és felügyeleti csoportok erőforrásait érintő javaslatok felett. 

Annak nyomon követéséhez, hogy a felhasználók hogyan teljesednek ki ezzel a képességgel, létrehoztunk egy Azure Resource Manager- (ARM-) sablont, amely üzembe helyez egy logikaialkalmazás-forgatókönyvet és minden szükséges API-kapcsolatot, amely értesíti Önt a kivétel létrejöttéről.

- A forgatókönyvvel kapcsolatos további információkért tekintse [](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580) meg az erőforrás-kivételek nyomon követését a műszaki közösség Azure Security Center
- Az ARM-sablont az Azure Security Center [GitHub-adattárban találja](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Az összes szükséges összetevő üzembe helyezéséhez használja [ezt az automatizált folyamatot](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Javaslatok megkeresése kivételekkel a Azure Resource Graph

Azure Resource Graph (ARG) azonnali hozzáférést biztosít az erőforrás-információkhoz a felhőalapú környezetekben robusztus szűrési, csoportosítási és rendezési képességekkel. Ez egy gyors és hatékony módja az információk lekérdezésének programozott módon vagy az Azure-előfizetésen belülről Azure Portal.

Az összes kivételi sszabályokkal kapcsolatos javaslat megtekintése:

1. Nyissa **meg Azure Resource Graph Explorert.**

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="A Azure Resource Graph Explorer** javaslatoldalának elindítása" :::

1. Írja be a következő lekérdezést, és válassza a **Lekérdezés futtatása lehetőséget.**

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


További információ a következő oldalakon található:
- [További információ a Azure Resource Graph.](../governance/resource-graph/index.yml)
- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)
- [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Gyakori kérdések – Kivételi szabályok

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Mi történik, ha egy javaslat több szabályzati kezdeményezésben is van?

Előfordulhat, hogy egy biztonsági javaslat több szabályzati kezdeményezésben is megjelenik. Ha ugyanannak a javaslatnak több példánya van hozzárendelve ugyanannak az előfizetésnek, és kivételt hoz létre a javaslathoz, az hatással lesz az összes olyan kezdeményezésre, amely szerkesztési engedéllyel rendelkezik. 

Például a javaslat ( ) az összes Azure-előfizetéshez hozzárendelt alapértelmezett szabályzati kezdeményezés része a Azure Security Center. Szintén XXXXX formátumban.

Ha megpróbál kivételt létrehozni ehhez a javaslathoz, a következő két üzenet egyike látható:

- Ha rendelkezik mindkét kezdeményezés szerkesztéséhez szükséges engedélyekkel, a következőt fogja látni:

    *Ez a javaslat több szabályzati kezdeményezésben is szerepel: [a kezdeményezésnevek vesszővel elválasztva]. Mindegyikhez létrejönnek kivételek.*  

- Ha nem rendelkezik megfelelő engedélyekkel mindkét kezdeményezéshez, ehelyett a következő üzenet jelenik meg:

    *Korlátozott engedélyekkel rendelkezik a kivétel minden szabályzati kezdeményezésre való alkalmazáshoz, a kivételek csak a megfelelő engedélyekkel rendelkező kezdeményezéseken lesznek létrehozva.*


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan mentesíthet egy erőforrást egy javaslat alól, hogy az ne befolyásolja a biztonsági pontszámot. További információ a biztonsági pontszámról:

- [Biztonsági pontszám az Azure Security Centerben](secure-score-security-controls.md)
