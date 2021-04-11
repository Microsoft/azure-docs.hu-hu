---
title: Erőforrás, előfizetés, felügyeleti csoport és biztonságos pontszám Azure Security Center javaslatának kivétele
description: Megtudhatja, hogyan hozhat létre olyan szabályokat, amelyek az előfizetések vagy a felügyeleti csoportok biztonsági javaslatai alól mentesülnek, és megakadályozzák, hogy a biztonságos pontszámot befolyásolják
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d3627f6bcda7a18204c24fc2a1347c4a512c5369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149732"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Erőforrások és javaslatok kizárása a biztonságos pontszám alól 

Minden biztonsági csapat alapvető prioritása annak biztosítása, hogy az elemzők a szervezet számára fontos feladatokra és incidensekre összpontosítsanak. A Security Center számos funkcióval rendelkezik, amelyekkel testre szabhatja a felhasználói élményt, és biztosíthatja, hogy a biztonságos pontszám a szervezet biztonsági prioritásait tükrözze. A **kivételt** képező lehetőség egy ilyen szolgáltatás.

Amikor megvizsgálják a biztonsági javaslatait a Azure Security Centerban, az elsőként áttekintett információk egyike az érintett erőforrások listája.

Időnként előfordulhat, hogy egy erőforrás jelenik meg, amelyet nem tartalmaz. Vagy egy javaslat olyan hatókörben fog megjelenni, amelyben úgy érzi, hogy nem tartozik hozzá. Lehetséges, hogy az erőforrást egy Security Center által nyomon követett folyamat szervizelte. Előfordulhat, hogy a javaslat nem megfelelő egy adott előfizetéshez. Vagy talán a szervezete egyszerűen úgy döntött, hogy elfogadja az adott erőforrással vagy javaslattal kapcsolatos kockázatokat.

Ilyen esetekben a következőket hozhatja létre a javaslatok alól:

- **Kivételt** képeznek az erőforrások, hogy a jövőben ne jelenjen meg a nem megfelelő állapotú erőforrások, és nem befolyásolja a biztonságos pontszámot. Az erőforrás nem alkalmazhatóként jelenik meg, és az OK "kivételként" jelenik meg a kiválasztott indoklással.

- **Kivételt képez az előfizetés vagy a felügyeleti csoport alól** , hogy a javaslat ne befolyásolja a biztonságos pontszámot, és a jövőben ne jelenjen meg az előfizetés vagy a felügyeleti csoport számára. Ez a meglévő erőforrásokhoz és a jövőben létrehozott mindenhez kapcsolódik. A javaslat a kiválasztott hatókörhöz megadott indoklással lesz megjelölve.

## <a name="availability"></a>Rendelkezésre állás

| Szempont                          | Részletek                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadás állapota:                  | Előnézet<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Árképzési                        | Ez egy prémium szintű Azure Policy-képesség, amely az Azure Defender-ügyfelek számára ingyenes, díjmentesen használható. Más felhasználók esetében a jövőben a díjak is vonatkozhatnak.                                                                                                                                                                 |
| Szükséges szerepkörök és engedélyek: | **Előfizetés tulajdonosának** vagy **szabályzatának közreműködője** a kivétel létrehozásához<br>Szabály létrehozásához engedélyekre van szükség a házirendek szerkesztéséhez Azure Policy.<br>További információ az [Azure RBAC engedélyeiről Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Korlátozások:                    | Kivételeket csak az Security Center alapértelmezett kezdeményezésében, az Azure biztonsági Teljesítménytesztben vagy a mellékelt standard szintű kezdeményezésekben foglalt javaslatok esetében lehet létrehozni. Az egyéni kezdeményezésekből létrehozott javaslatok nem mentesülnek. További információ a [házirendek, a kezdeményezések és a javaslatok](security-policy-concept.md)közötti kapcsolatokról. |
| Felhők                         | ![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Kivétel meghatározása

Az előfizetések, a felügyeleti csoportok és az erőforrások Security Center által nyújtott biztonsági javaslatok finomhangolásához hozzon létre egy kivételi szabályt a következőhöz:

- Egy konkrét **javaslat** vagy "enyhített" vagy "kockázat elfogadva" jelölésű. Egy előfizetéshez, több előfizetéshez vagy egy teljes felügyeleti csoporthoz is létrehozhat ajánlási kivételeket.
- Egy **vagy több erőforrás** megjelölése "enyhített" vagy "kockázat elfogadva" egy adott javaslathoz.

> [!NOTE]
> Kivételeket csak az Security Center alapértelmezett kezdeményezésében, az Azure biztonsági Teljesítménytesztben vagy a standard szintű szabványos kezdeményezésekben foglalt javaslatok esetében lehet létrehozni. Az előfizetésekhez rendelt egyéni kezdeményezésekből létrehozott javaslatok nem mentesülnek. További információ a [házirendek, a kezdeményezések és a javaslatok](security-policy-concept.md)közötti kapcsolatokról.

> [!TIP]
> Az API használatával kivételeket is létrehozhat. Példa a JSON-ra, és a megfelelő struktúrák magyarázatát lásd: [Azure Policy kivétel struktúrája](../governance/policy/concepts/exemption-structure.md).

Kivételi szabály létrehozása:

1. Nyissa meg a javaslat részleteit tartalmazó oldalt az adott javaslathoz.

1. A lap tetején található eszköztáron válassza a **mentesített** lehetőséget.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Egy előfizetésből vagy felügyeleti csoportból kivételt képező javaslathoz hozzon létre kivételi szabályt.":::

1. A **kivételek** ablaktáblán:
    1. Válassza ki a hatókört a kivételi szabályhoz:
        - Ha kijelöl egy felügyeleti csoportot, akkor a javaslat a csoporton belüli összes előfizetésre vonatkozik.
        - Ha létrehoz egy vagy több erőforrást a javaslatból, válassza a "kiválasztott erőforrások" lehetőséget, és válassza ki a megfelelőket a listából.

    1. Adja meg a kivételi szabály nevét.
    1. Szükség esetén megadhat egy lejárati dátumot is.
    1. Válassza ki a kivétel kategóriáját:
        - Harmadik **fél által feloldva (enyhített)** – ha olyan külső szolgáltatást használ, amelyet Security Center még nem azonosított. 

            > [!NOTE]
            > Ha a felszámított ajánlást kizárja, nem kap pontot a biztonságos pontszám felé. De mivel a nem kifogástalan erőforrásokhoz nem *távolítanak el* pontokat, az eredmény az, hogy a pontszám növekedni fog.

        - **Kockázat elfogadva (lemondás)** – ha úgy döntött, hogy elfogadja a javaslat nem enyhítésének kockázatát
    1. Igény szerint megadhat egy leírást.
    1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="A kivételt képező szabály létrehozásához szükséges lépések az előfizetésből vagy a felügyeleti csoportból származó javaslat alól":::

    Ha a kivétel érvénybe lép (akár 30 percet is igénybe vehet):
    - A javaslat vagy az erőforrások nem befolyásolják a biztonságos pontszámot.
    - Ha mentesítette az adott erőforrásokat, a rendszer a javaslat részletei lap **nem alkalmazható** lapján sorolja fel.
    - Ha már mentesített egy javaslatot, a rendszer alapértelmezés szerint a Security Center ajánlásai oldalon elrejti. Ennek az az oka, hogy az adott oldalon az **ajánlási állapot** szűrő alapértelmezett beállításai a **nem alkalmazható** javaslatok kizárására szolgálnak. Ugyanez érvényes abban az esetben is, ha az összes javaslatot egy biztonsági vezérlőben kizárja.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="A Azure Security Center ajánlásai lapon lévő alapértelmezett szűrők elrejtik a nem alkalmazható ajánlásokat és biztonsági vezérlőket":::

    - A javaslat részletei oldal tetején található információs szalag frissíti a mentesített erőforrások számát:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Mentesített erőforrások száma":::

1. A mentesített erőforrások áttekintéséhez nyissa meg a **nem alkalmazható** fület:

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Kivétel módosítása":::

    Az egyes kivételek okát a táblázat tartalmazza (1).

    A kivételek módosításához vagy törléséhez válassza a három pontot ("...") a látható módon (2).

1. Az előfizetésre vonatkozó összes kivételi szabály áttekintéséhez válassza a **kivételek megtekintése** az információs szalagról lehetőséget:

    > [!IMPORTANT]
    > Ha meg szeretné tekinteni az egyik javaslathoz tartozó konkrét kivételeket, a vonatkozó hatókör és javaslat neve szerint szűrje a listát.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy kivételének lapja":::

    > [!TIP]
    > Alternatív megoldásként az [Azure Resource Graph használatával is megtalálhatja a kivételekkel kapcsolatos javaslatokat](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Az előfizetésekben létrehozott kivételek figyelése

A fentiekben leírtaknak megfelelően a kivételi szabályok hatékony eszközt biztosítanak az előfizetésekben és a felügyeleti csoportokban lévő erőforrásokat érintő javaslatok részletes ellenőrzéséhez. 

Ahhoz, hogy nyomon tudja követni, hogy a felhasználók hogyan használják ezt a képességet, létrehoztunk egy Azure Resource Manager (ARM) sablont, amely egy logikai alkalmazás forgatókönyvének és az összes szükséges API-kapcsolat üzembe helyezésével értesíti Önt, ha létrejött a kivétel.

- Ha többet szeretne megtudni a forgatókönyvről, tekintse meg a technikai Közösség blogbejegyzést, amely [ismerteti az erőforrás-kivételek nyomon követését Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Az ARM-sablon a [Azure Security Center GitHub-tárházban](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption) található.
- Az összes szükséges összetevő üzembe helyezéséhez [használja ezt az automatikus folyamatot](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Javaslatok keresése kivételekkel az Azure Resource Graph használatával

Az Azure Resource Graph (ARG) a Felhőbeli környezetekben az erőforrás-információkhoz való azonnali hozzáférést biztosít robusztus szűrési, csoportosítási és rendezési képességekkel. Az Azure-előfizetések programozott vagy a Azure Portalon keresztüli lekérdezésének gyors és hatékony módja.

A kivételi szabályokkal rendelkező összes javaslat megtekintése:

1. Nyissa meg az **Azure Resource Graph Explorert**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Az Azure Resource Graph Explorer * * ajánlási oldalának elindítása" :::

1. Adja meg a következő lekérdezést, és válassza a **lekérdezés futtatása** lehetőséget.

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


További információt a következő lapokon olvashat:
- [További információ az Azure Resource Graph-ról](../governance/resource-graph/index.yml).
- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)
- [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Gyakori kérdések – kivételi szabályok

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Mi történik, ha az egyik javaslat több házirend-kezdeményezésre is vonatkozik?

Előfordulhat, hogy egy biztonsági javaslat több házirend-kezdeményezésben is megjelenik. Ha ugyanaz az ajánlás több példánya is hozzá van rendelve ugyanahhoz az előfizetéshez, és kivételt hoz létre a javaslathoz, akkor az minden olyan kezdeményezést érint, amelynek a szerkesztésére jogosult. 

A * * * * javaslat például az összes Azure-előfizetéshez hozzárendelt alapértelmezett házirend-kezdeményezés részét képezi Azure Security Center. XXXXX-ben is.

Ha a javaslathoz nem hoz létre kivételt, akkor a következő két üzenet egyike jelenik meg:

- Ha rendelkezik a szükséges engedélyekkel mindkét kezdeményezés szerkesztéséhez, a következőt fogja látni:

    *Ezt a javaslatot számos házirend-kezdeményezés tartalmazza: [a kezdeményezés neve vesszővel elválasztva]. Kivételek jönnek létre mindegyiken.*  

- Ha nem rendelkezik megfelelő engedélyekkel mindkét kezdeményezéshez, a következő üzenet jelenik meg:

    *Korlátozott engedélyekkel rendelkezik a kivételek az összes házirend-kezdeményezésre való alkalmazásához, a kivételeket csak a megfelelő engedélyekkel rendelkező kezdeményezésekhez hozza létre a rendszer.*


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan mentesített egy erőforrást egy javaslatból, hogy az ne befolyásolja a biztonságos pontszámot. A biztonságos pontszámról további információt a következő témakörben talál:

- [Biztonsági pontszám az Azure Security Centerben](secure-score-security-controls.md)