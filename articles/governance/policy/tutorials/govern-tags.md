---
title: 'Oktatóanyag: a címke irányításának kezelése'
description: Ebben az oktatóanyagban a Azure Policy módosításának hatása alapján létrehozhatja és érvényesítheti a címke irányítási modelljét az új és a meglévő erőforrásokon.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 1e8850dd6fe380b0ea38a2a1722734d917c63d0c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093282"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Oktatóanyag: a címke szabályozásának kezelése a Azure Policy

A [címkék](../../../azure-resource-manager/management/tag-resources.md) kulcsfontosságú részét képezik az Azure-erőforrások taxonómiai rendszerezésének. A [címkézési felügyelettel kapcsolatos ajánlott eljárások](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)követése során a címkék az üzleti szabályzatok alkalmazásának alapjául szolgálnak Azure Policy vagy [követési költségekkel Cost Managementával](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources).
Függetlenül attól, hogy hogyan vagy miért használja a címkéket, fontos, hogy gyorsan hozzá lehessen adni, módosítani és eltávolítani ezeket a címkéket az Azure-erőforrásokon. Ha szeretné megtudni, hogy az Azure-erőforrás támogatja-e a címkézést, lásd: [támogatás](../../../azure-resource-manager/management/tag-support.md)címkézése.

Azure Policy [módosításának](../concepts/effects.md#modify) hatása úgy van kialakítva, hogy segítse a címkék irányítását, függetlenül attól, hogy az erőforrás-szabályozás milyen szakaszában van. A **módosítás** a következőkhöz nyújt segítséget:

- Most ismerkedik a felhővel, és nincs címkézési szabályozása
- Már több ezer erőforrása van a címke szabályozása nélkül
- Már van olyan meglévő Taxonómia, amelyet módosítania kell

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> - Az üzleti követelmények meghatározása
> - Minden követelmény hozzárendelése egy szabályzat-definícióhoz
> - Címkézési szabályzatok csoportosítása egy kezdeményezésbe

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="identify-requirements"></a>Követelmények azonosítása

Az irányítási szabályozások megfelelő megvalósításához hasonlóan a követelményeknek az üzleti igényektől kell származnia, és a technikai szabályozások létrehozása előtt jól érthetőnek kell lenniük. Ebben az oktatóanyagban a következő elemek az üzleti követelmények:

- Két kötelező címke az összes erőforráson: _CostCenter_ és _env_
- A _CostCenter_ minden tárolón és egyedi erőforráson léteznie kell
  - Az erőforrások öröklik a tárolóban lévő erőforrásokat, de előfordulhat, hogy a rendszer külön felülbírálja őket.
- Az _env_ -nek minden tárolón és egyedi erőforráson léteznie kell
  - Az erőforrások a tároló-elnevezési séma alapján határozzák meg a környezetet, és előfordulhat, hogy nincs felülbírálva
  - A tárolóban lévő összes erőforrás ugyanahhoz a környezethez tartozik

## <a name="configure-the-costcenter-tag"></a>A CostCenter címke konfigurálása

A Azure Policy által felügyelt Azure-környezetekre vonatkozó feltételek a _CostCenter_ -címkézési követelmények a következő eredményekre vonatkoznak:

- Az erőforráscsoportok megtagadása elemből hiányzik a _CostCenter_ címke
- Erőforrások módosítása a _CostCenter_ címke hozzáadásához a szülő erőforráscsoporthoz, ha hiányzik

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Az erőforráscsoportok megtagadása elemből hiányzik a CostCenter címke

Mivel az _CostCenter_ nem határozható meg az erőforráscsoport neve, az erőforráscsoport létrehozásához a kérelemben definiált címkének kell szerepelnie. A [Megtagadás](../concepts/effects.md#deny) hatású házirend-szabály megakadályozza a _CostCenter_ címkével nem rendelkező erőforráscsoportok létrehozását vagy frissítését:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Mivel ez a házirend-szabály egy erőforráscsoportot _céloz meg, a házirend_ -definícióban az "all" értéknek kell lennie az "indexelt" helyett.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Erőforrások módosítása, hogy a CostCenter címke örökölje a hiányzó

A második _CostCenter_ szükséges, hogy a szülő erőforráscsoport címkéje örökölje az összes erőforrást, ha az nincs megadva. Ha a címke már definiálva van az erőforráson, még akkor is, ha a szülő erőforráscsoporthoz eltér, egyedül kell maradnia. A következő házirend-szabály a [módosítást](../concepts/effects.md#modify)használja:

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Ez a házirend-szabály a **Hozzáadás** műveletet használja a **addOrReplace** helyett, mivel nem szeretnénk módosítani a címke értékét, ha a meglévő erőforrások [szervizelését](../how-to/remediate-resources.md) van. A `[resourcegroup()]` sablon függvényt is használja a szülő erőforráscsoporthoz tartozó címke értékének beolvasásához.

> [!NOTE]
> Mivel ez a házirend-szabály a címkéket támogató erőforrásokat céloz meg, a házirend-definícióban szereplő _módnak_ "indexelt" értéknek kell lennie. Ez a konfiguráció azt is biztosítja, hogy ez a szabályzat kihagyja az erőforráscsoportokat.

## <a name="configure-the-env-tag"></a>Az ENV címke konfigurálása

Az Azure Policy által felügyelt Azure-környezetekre vonatkozó követelmények az _env_ címke követelményei a következő eredmények meghívását jelentik:

- Módosítsa az erőforrás-csoport _env_ címkéjét az erőforráscsoport elnevezési sémája alapján.
- Módosítsa az összes erőforráshoz tartozó _env_ címkét a szülő erőforráscsoporthoz megegyező értékkel.

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Erőforráscsoportok env-címke módosítása a név alapján

Az Azure-környezetben található minden környezethez szükség van egy [módosítási](../concepts/effects.md#modify) házirendre. A házirend-definícióhoz hasonlóan a szabályzat módosítása:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Mivel ez a házirend-szabály egy erőforráscsoportot _céloz meg, a házirend_ -definícióban az "all" értéknek kell lennie az "indexelt" helyett.

Ez a szabályzat csak az üzemi erőforrásokhoz használt minta elnevezési sémával rendelkező erőforráscsoportokat felel meg `prd-` . Az összetettebb elnevezési sémák több **egyeztetési** feltétellel is elérhetők, **mint** például ebben a példában.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Erőforrások módosítása az ENV címke örökléséhez

Az üzleti követelmény azt kéri, hogy az összes erőforrás rendelkezzen a szülő erőforráscsoporthoz tartozó _env_ címkével. Ezt a címkét nem lehet felülbírálni, ezért a [módosítás](../concepts/effects.md#modify) hatásával használjuk a **addOrReplace** műveletet. A minta-módosítási házirend a következő szabályhoz hasonlóan néz ki:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Mivel ez a házirend-szabály a címkéket támogató erőforrásokat céloz meg, a házirend-definícióban szereplő _módnak_ "indexelt" értéknek kell lennie. Ez a konfiguráció azt is biztosítja, hogy ez a szabályzat kihagyja az erőforráscsoportokat.

Ez a házirend-szabály olyan erőforrást keres, amely nem rendelkezik szülő erőforráscsoport értékkel az _env_ címkéhez, vagy hiányzik az _env_ címke. A megfeleltetési erőforrásokhoz az _env_ kódelem van beállítva a szülő erőforráscsoport értékre, még akkor is, ha a címke már létezik az erőforráson, de más értékkel rendelkezik.

## <a name="assign-the-initiative-and-remediate-resources"></a>A kezdeményezés kiosztása és az erőforrások szervizelése

Miután létrehozta a fenti címkézési szabályzatokat, csatlakoztassa azokat egyetlen kezdeményezéshez a szabályozás címkézéséhez, és rendeljen hozzá egy felügyeleti csoporthoz vagy előfizetéshez. A kezdeményezés és a belefoglalt házirendek ezt követően értékelik a meglévő erőforrások megfelelőségét, és az új vagy frissített erőforrásokra vonatkozó kéréseket módosítják, amelyek megfelelnek az **IF** tulajdonságnak a házirend-szabályban. A szabályzat azonban nem frissíti automatikusan a meglévő, nem megfelelő erőforrásokat a definiált címke módosításaival.

A [deployIfNotExists](../concepts/effects.md#deployifnotexists) -szabályzatokhoz hasonlóan a **módosítási** házirend szervizelési feladatokat használ a meglévő nem megfelelő erőforrások módosításához. Kövesse az [erőforrások szervizelésének](../how-to/remediate-resources.md) utasításait a nem megfelelő erőforrás- **módosítási** erőforrások azonosításához, és javítsa ki a címkéket a definiált besorolásban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyag erőforrásainak használatával, a következő lépésekkel törölheti a fent létrehozott hozzárendeléseket vagy definíciókat:

1. Válassza a **definíciók** **(vagy** hozzárendelések) lehetőséget a Azure Policy lap bal oldalán található **authoring (szerzői műveletek** ) elemnél.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="review"></a>Áttekintés

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> - Az üzleti igények azonosítása
> - Minden követelmény leképezve egy házirend-definícióba
> - A címkézési házirendek csoportosítása egy kezdeményezésbe

## <a name="next-steps"></a>Következő lépések

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)