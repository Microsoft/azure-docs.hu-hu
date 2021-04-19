---
title: Egyéni biztonsági szabályzatok létrehozása a Azure Security Center | Microsoft Docs
description: Egyéni Azure-szabályzatdefiníciók monitorozása a Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a41696ba92757550f9cbaa08ccf78d9a5da528d2
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718898"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Egyéni biztonsági kezdeményezések és szabályzatok létrehozása

A rendszerek és a környezet biztonságossá Azure Security Center biztonsági javaslatokat hoz létre. Ezek az ajánlások az iparág ajánlott eljárásán alapulnak, amelyek be vannak építve az összes ügyfél számára biztosított általános, alapértelmezett biztonsági szabályzatba. Emellett az iparági és Security Center szabványokkal kapcsolatos tudását is kihasználhatja.

Ezzel a funkcióval saját egyéni kezdeményezéseket *adhat* hozzá. Ezután javaslatokat kap, ha a környezet nem követi a létrehozott szabályzatokat. Az ön által létrehozott egyéni kezdeményezések a szabályozási megfelelőségi irányítópult beépített kezdeményezései mellett jelennek meg, amint az a jogszabályi megfelelőség javítását ismertető [oktatóanyagban szerepel.](security-center-compliance-dashboard.md)

Ahogy azt a Azure Policy [is](../governance/policy/concepts/definition-structure.md#definition-location)tárgyalja, amikor megad egy helyet az egyéni kezdeményezéshez, annak felügyeleti csoportnak vagy előfizetésnek kell lennie. 

> [!TIP]
> Az ezen az oldalon található fő fogalmak áttekintését lásd: Mik azok a biztonsági szabályzatok, kezdeményezések [és javaslatok?](security-policy-concept.md).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Egyéni kezdeményezés hozzáadása az előfizetéshez 

1. A Security Center oldalsávján nyissa meg a Biztonsági **szabályzat** lapot.

1. Válasszon ki egy előfizetést vagy felügyeleti csoportot, amelyhez egyéni kezdeményezést szeretne hozzáadni.

    [![Olyan előfizetés kiválasztása, amelyhez létre fogja hozni az egyéni szabályzatot](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Egyéni szabványokat kell hozzáadnia az előfizetés szintjén (vagy magasabb szinten), hogy azok kiértékelése és megjelenítése a Security Center. 
    >
    > Amikor hozzáad egy egyéni szabványt, az hozzárendel egy *kezdeményezést ehhez* a hatókörhez. Ezért javasoljuk, hogy válassza ki a hozzárendeléshez szükséges legszélesebb hatókört.

1. A Biztonsági szabályzat lap Egyéni kezdeményezések területén kattintson az **Egyéni kezdeményezés hozzáadása elemre.**

    [![Kattintson az Egyéni kezdeményezés hozzáadása elemre.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Az alábbi oldal nyílik meg:

    ![Szabályzat létrehozása vagy hozzáadása](media/custom-security-policies/create-or-add-custom-policy.png)

1. Az Egyéni kezdeményezések hozzáadása lapon tekintse át a szervezetben már létrehozott egyéni szabályzatok listáját. Ha azt látja, amit hozzá szeretne rendelni az előfizetéséhez, kattintson az **Add (Hozzáadás) gombra.** Ha a listában nincs olyan kezdeményezés, amely megfelel az igényeinek, hagyja ki ezt a lépést.

1. Új egyéni kezdeményezés létrehozása:

    1. Kattintson az **Új létrehozása elemre.**
    1. Adja meg a definíció helyét és nevét.
    1. Válassza ki a felvenni kívánt szabályzatokat, majd kattintson a **Hozzáadás gombra.**
    1. Adja meg a kívánt paramétereket.
    1. Kattintson a **Mentés** gombra.
    1. Az Egyéni kezdeményezések hozzáadása lapon kattintson a Frissítés elemre. Az új kezdeményezés elérhetőként jelenik meg.
    1. Kattintson **a Hozzáadás gombra,** és rendelje hozzá az előfizetéséhez.

    > [!NOTE]
    > Új kezdeményezések létrehozásához előfizetés-tulajdonosi hitelesítő adatokra van szükség. További információ az Azure-szerepkörökről: [Engedélyek](security-center-permissions.md)a Azure Security Center.

    Az új kezdeményezés életbe lép, és a hatást az alábbi két módon láthatja:

    * A Security Center oldalsáv Szabályzatok és megfelelőség & területén válassza a **Jogszabályi megfelelőség lehetőséget.** Megnyílik a megfelelőségi irányítópult, és megjeleníti az új egyéni kezdeményezést a beépített kezdeményezésekkel együtt.
    
    * Javaslatokat kap, ha a környezet nem követi a megadott szabályzatokat.

1. A szabályzatra vonatkozó, eredményül  kapott javaslatokért kattintson az oldalsáv Javaslatok gombjára a javaslatok oldalának megnyitásához. A javaslatok "Egyéni" címkével jelennek meg, és körülbelül egy órán belül elérhetők lesznek.

    [![Egyéni javaslatok](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Biztonsági szabályzat konfigurálása a Azure Policy a REST API

A natív integráció részeként a Azure Policy Azure Security Center lehetővé teszi, hogy kihasználja Azure Policy házirend-hozzárendelések REST API előnyeit. Az alábbi utasítások végigkik a szabályzat-hozzárendelések létrehozásának, valamint a meglévő hozzárendelések testreszabásának folyamatán. 

Fontos fogalmak a Azure Policy: 

- A **szabályzatdefiníció** egy szabály 

- A **kezdeményezés** szabályzatdefiníciók (szabályok) gyűjteménye 

- A **hozzárendelés** egy kezdeményezés vagy szabályzat alkalmazása egy adott hatókörre (felügyeleti csoport, előfizetés stb.) 

Security Center rendelkezik egy [beépített Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)kezdeményezéssel, amely az összes biztonsági szabályzatát tartalmazza. A Security Center Azure-erőforrásokra vonatkozó szabályzatok értékeléséhez létre kell hoznia egy hozzárendelést az értékelni kívánt felügyeleti csoporthoz vagy előfizetéshez.

A beépített kezdeményezés alapértelmezés szerint Security Center összes szabályzata engedélyezve van. Dönthet úgy, hogy letilt bizonyos szabályzatokat a beépített kezdeményezésből. Ha például a webalkalmazási tűzfal Security Center alkalmazni az összes szabályzatát, módosítsa a szabályzat hatásparaméterének értékét **Letiltva értékre.**

## <a name="api-examples"></a>API-példák

A következő példákban cserélje le ezeket a változókat:

- **{scope}** adja meg annak a felügyeleti csoportnak vagy előfizetésnek a nevét, amelyre a szabályzatot alkalmazza
- **{policyAssignmentName}** adja meg a megfelelő szabályzat-hozzárendelés nevét
- **{name}** adja meg a nevét vagy a szabályzatváltozást jóváhagyó rendszergazda nevét

Ez a példa bemutatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez vagy felügyeleti csoporthoz
 
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

Ez a példa bemutatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez úgy, hogy a következő szabályzatok le vannak tiltva: 

- Rendszerfrissítések ("systemUpdatesMonitoringEffect") 

- Biztonsági konfigurációk ("systemConfigurationsMonitoringEffect") 

- Végpontvédelem ("endpointProtectionMonitoringEffect") 

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
Ez a példa bemutatja, hogyan távolíthat el egy hozzárendelést:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Egyéni javaslatok továbbfejlesztve részletes információkkal

A beépített javaslatok között olyan Azure Security Center, mint a súlyossági szintek és a szervizelési utasítások. Ha az ilyen típusú információkat hozzá szeretné adni az egyéni javaslatokhoz, hogy az megjelenjen a Azure Portal vagy bárhol, ahol hozzáfér a javaslatokhoz, a következőt kell REST API. 

Kétféle információt adhat hozzá:

- **RemediationDescription – Sztring**
- **Súlyosság** – Felsorolás [Alacsony, Közepes, Magas]

A metaadatokat hozzá kell adni az egyéni kezdeményezés részét képezi szabályzatdefinícióhoz. Ennek a "securityCenter" tulajdonságban kell lennie, ahogy az alábbi képen is látható:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Az alábbiakban egy példa látható egy egyéni szabályzatra, amely tartalmazza a metadata/securityCenter tulajdonságot:

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

A securityCenter tulajdonság használatának egy másik példáját a dokumentáció ezen [REST API találhatja.](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egyéni biztonsági szabályzatokat. 

Egyéb kapcsolódó anyagokért tekintse meg a következő cikkeket: 

- [A biztonsági szabályzatok áttekintése](tutorial-security-policy.md)
- [A beépített biztonsági szabályzatok listája](./policy-reference.md)