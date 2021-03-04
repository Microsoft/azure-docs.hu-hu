---
title: Egyéni biztonsági szabályzatok létrehozása a Azure Security Centerban | Microsoft Docs
description: A Azure Security Center által figyelt egyéni Azure-házirend-definíciók.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a39b79c6c209c0fc66edac846d5458475ec75810
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100865"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Egyéni biztonsági kezdeményezések és szabályzatok létrehozása

A rendszerek és a környezet biztonságossá tételéhez Azure Security Center biztonsági javaslatokat hoz létre. Ezek a javaslatok az iparági ajánlott eljárásokon alapulnak, amelyek az összes ügyfél számára biztosított általános, alapértelmezett biztonsági szabályzatba vannak beépítve. Security Center az iparági és a szabályozási szabványok ismeretében is származhatnak.

Ezzel a funkcióval saját *Egyéni* kezdeményezéseket adhat hozzá. Ezután javaslatokat fog kapni, ha a környezet nem követi a létrehozott házirendeket. Az Ön által létrehozott egyéni kezdeményezések a szabályozási megfelelőségi irányítópult beépített kezdeményezései mellett jelennek meg, [az oktatóanyagban](security-center-compliance-dashboard.md)ismertetett módon.

Amint azt [a Azure Policy dokumentációban](../governance/policy/concepts/definition-structure.md#definition-location)tárgyaljuk, az egyéni kezdeményezés helyének megadásakor felügyeleti csoportnak vagy előfizetésnek kell lennie. 

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Egyéni kezdeményezés hozzáadása az előfizetéshez 

1. A Security Center oldalsávján nyissa meg a **biztonsági házirend** lapot.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez egyéni kezdeményezést szeretne hozzáadni.

    [![Válassza ki azt az előfizetést, amelyhez létre kívánja hozni az egyéni szabályzatot](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Az előfizetési szinten (vagy magasabban) egyéni szabványokat kell hozzáadnia ahhoz, hogy kiértékeljék és megjelenjenek Security Centerban. 
    >
    > Egyéni szabvány hozzáadásakor egy *kezdeményezést* rendel hozzá ehhez a hatókörhöz. Ezért javasoljuk, hogy válassza ki az adott hozzárendeléshez szükséges legszélesebb hatókört.

1. A biztonsági szabályzat lap egyéni kezdeményezések területén kattintson az **Egyéni kezdeményezés hozzáadása** lehetőségre.

    [![Kattintson az egyéni kezdeményezés hozzáadása lehetőségre.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Az alábbi oldal nyílik meg:

    ![Szabályzat létrehozása vagy hozzáadása](media/custom-security-policies/create-or-add-custom-policy.png)

1. Az egyéni kezdeményezések hozzáadása oldalon tekintse át a szervezetében már létrehozott egyéni szabályzatok listáját. Ha megjelenik az előfizetéshez hozzárendelni kívánt fiók, kattintson a **Hozzáadás** gombra. Ha nincs olyan kezdeményezés a listában, amely megfelel az igényeinek, hagyja ki ezt a lépést.

1. Új egyéni kezdeményezés létrehozása:

    1. Kattintson az **új létrehozása** gombra.
    1. Adja meg a definíció helyét és nevét.
    1. Válassza ki a felvenni kívánt házirendeket, majd kattintson a **Hozzáadás** gombra.
    1. Adja meg a kívánt paramétereket.
    1. Kattintson a **Mentés** gombra.
    1. Az egyéni kezdeményezések hozzáadása lapon kattintson a frissítés elemre. Az új kezdeményezés elérhetőként jelenik meg.
    1. Kattintson a **Hozzáadás** gombra, és rendelje hozzá az előfizetéséhez.

    > [!NOTE]
    > Az új kezdeményezések létrehozásához előfizetés-tulajdonosi hitelesítő adatokra van szükség. Az Azure-szerepkörökkel kapcsolatos további információkért tekintse [meg a Azure Security Center engedélyeit](security-center-permissions.md).

    Az új kezdeményezés érvénybe lép, és a következő két módon láthatja a hatást:

    * A Security Center oldalsávon a szabályzat & megfelelőség területen válassza a **jogszabályi megfelelőség** lehetőséget. Megnyílik a megfelelőségi irányítópult, amely megjeleníti az új egyéni kezdeményezést a beépített kezdeményezésekkel együtt.
    
    * Ha a környezet nem követi a definiált szabályzatokat, megkezdheti a javaslatok fogadását.

1. A Szabályzathoz kapcsolódó javaslatok megtekintéséhez kattintson a **javaslatok** elemre az oldalsávon, és nyissa meg a javaslatok lapot. A javaslatok az "egyéni" címkével jelennek meg, és körülbelül egy órán belül elérhetők lesznek.

    [![Egyéni javaslatok](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Biztonsági házirend konfigurálása Azure Policy a REST API használatával

A Azure Policyrel való natív integráció részeként Azure Security Center lehetővé teszi, hogy kihasználhassa a szabályzat-hozzárendelések létrehozásához szükséges Azure Policy REST API előnyeit. A következő utasítások végigvezetik a szabályzat-hozzárendelések létrehozásán, valamint a meglévő hozzárendelések testreszabásán. 

Fontos fogalmak a Azure Policyban: 

- A **házirend-definíció** egy szabály 

- A **kezdeményezés** szabályzat-definíciók (szabályok) gyűjteménye. 

- A **hozzárendelés** egy adott hatókörre irányuló kezdeményezés vagy házirend alkalmazása (felügyeleti csoport, előfizetés stb.) 

Security Center rendelkezik egy beépített kezdeményezéssel, az Azure biztonsági teljesítményteszttel, amely tartalmazza az összes biztonsági házirendjét. Az Security Center az Azure-erőforrásokra vonatkozó házirendjeinek értékeléséhez létre kell hoznia egy hozzárendelést a felügyeleti csoporton, vagy az előfizetést, amelyet fel szeretne mérni.

A beépített kezdeményezés alapértelmezés szerint a Security Center összes házirendjét engedélyezte. Dönthet úgy, hogy letilt bizonyos házirendeket a beépített kezdeményezésből. Ha például a **webalkalmazási tűzfal** kivételével a Security Center összes házirendjét szeretné alkalmazni, módosítsa a házirend Effect paraméterének értékét **Letiltva** értékre.

## <a name="api-examples"></a>API-példák

Az alábbi példákban cserélje le ezeket a változókat:

- **{scope}** adja meg annak a felügyeleti csoportnak vagy előfizetésnek a nevét, amelyre alkalmazni kívánja a szabályzatot
- **{policyAssignmentName}** adja meg a megfelelő szabályzat-hozzárendelés nevét
- **{Name}** adja meg a nevét, vagy annak a rendszergazdának a nevét, aki jóváhagyta a szabályzat módosítását

Ebből a példából megtudhatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez vagy egy felügyeleti csoporthoz.
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Ebből a példából megtudhatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez, a következő szabályzatok letiltásával: 

- Rendszerfrissítések ("systemUpdatesMonitoringEffect") 

- Biztonsági konfigurációk ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Ebből a példából megtudhatja, hogyan távolíthat el egy hozzárendelést:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Részletes információkkal bővítheti egyéni javaslatait

A Azure Security Centerhez mellékelt beépített ajánlások olyan részleteket tartalmaznak, mint például a súlyossági szintek és a Szervizelési utasítások. Ha ezt a típusú információt szeretné felvenni az egyéni javaslataiba, hogy az megjelenjen a Azure Portalban, vagy bárhol hozzáférjen a javaslataihoz, akkor a REST API kell használnia. 

A felvehető két típusú információ a következőkből áll:

- **RemediationDescription** – sztring
- **Súlyosság** – enumerálás [alacsony, közepes, magas]

A metaadatokat fel kell venni az egyéni kezdeményezés részét képező szabályzathoz tartozó szabályzat-definícióba. A "securityCenter" tulajdonságnak kell lennie, ahogy az alábbi ábrán látható:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Az alábbi példa egy egyéni házirendre mutat példát, beleértve a metaadatok/securityCenter tulajdonságot:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

A securityCenter tulajdonság használatának egy másik példáját a [REST API dokumentációjának ezen szakasza](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)tartalmazza.


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egyéni biztonsági házirendeket. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [A biztonsági házirendek áttekintése](tutorial-security-policy.md)
- [A beépített biztonsági szabályzatok listája](./policy-reference.md)