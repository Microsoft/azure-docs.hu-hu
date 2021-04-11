---
title: A házirend-mentesítési struktúra részletei
description: Ismerteti az Azure Policy által a kezdeményezések vagy definíciók kiértékelésével kapcsolatos erőforrások mentesítéséhez használt házirend-kivételi definíciót.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: ecf956d7507dfa2168e4f2591e4b661423801365
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091735"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy kivétel struktúrája

A Azure Policy kivételek (előzetes verzió) funkció használatával egy erőforrás-hierarchia vagy egy egyedi erőforrás _mentesül_ a kezdeményezések vagy definíciók kiértékelésével. A _kivételt_ képező erőforrások a teljes megfelelőségnek számítanak, de nem értékelhető ki, illetve nem rendelkezhet ideiglenes mentesítéssel. További információ: [a hatókör megismerése Azure Policy](./scope.md). Azure Policy a kivételek csak [Resource Manager-módokon](./definition-structure.md#resource-manager-modes) működnek, és nem működnek az [erőforrás-szolgáltatói módokkal](./definition-structure.md#resource-provider-modes).

> [!IMPORTANT]
> Ez a funkció **előzetes** verzióban ingyenes. A díjszabással kapcsolatos részletekért tekintse meg a [Azure Policy díjszabását](https://azure.microsoft.com/pricing/details/azure-policy/). További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A JSON használatával házirend-kivételt hozhat létre. A szabályzat alóli kivétel a következő elemeket tartalmazza:

- megjelenítendő név
- leírás
- metaadatok
- szabályzat-hozzárendelés
- házirend-definíciók egy kezdeményezésen belül
- kivétel kategóriája
- lejárati

> [!NOTE]
> A szabályzat alóli kivételt az erőforrás-hierarchiában, vagy a kivételt élvező egyedi erőforrásként hozza létre a rendszer, így a cél nem szerepel a kivétel definíciójában.

A következő JSON például a házirend alóli kivételt jeleníti meg egy erőforrás **lemondási** kategóriájában egy nevű kezdeményezési hozzárendeléshez `resourceShouldBeCompliantInit` . Az erőforrás csak két házirend-definíció _alól mentesül_ a kezdeményezésben, az `customOrgPolicy` egyéni házirend-definícióban (hivatkozás `requiredTags` ) és az "engedélyezett helyszínek" beépített szabályzat-definíciójában (azonosító: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , hivatkozás `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

A kapcsolódó kezdeményezés kódrészlete, amely a `policyDefinitionReferenceIds` szabályzat alóli kivételt használja:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **DisplayName** és a **Leírás** használatával azonosíthatja a házirend-kivételt, és megadhatja a környezetét az adott erőforrással való használathoz. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

## <a name="metadata"></a>Metaadatok

A **metaadatok** tulajdonság lehetővé teszi a megfelelő információk tárolásához szükséges gyermek-tulajdonságok létrehozását. A fenti példában a Properties **requestedBy**, a **approvedBy**, a **approvedOn** és a **ticketRef** tartalmazza az ügyfelek értékeit, hogy információt szolgáltasson arról, hogy ki és mikor hagyta jóvá a kivételt, és mikor, valamint egy belső követési jegyet a kéréshez. Ezek a **metaadat** -tulajdonságok példák, de nem kötelezőek, és a **metaadatok** nem korlátozódnak ezekre a gyermek tulajdonságokra.

## <a name="policy-assignment-id"></a>Szabályzat-hozzárendelés azonosítója

Ebben a mezőben egy szabályzat-hozzárendelés vagy egy kezdeményezés-hozzárendelés teljes elérési útjaként kell szerepelnie.
`policyAssignmentId` karakterlánc, nem tömb. Ez a tulajdonság határozza meg, hogy a szülő erőforrás-hierarchia vagy az egyes erőforrások melyik hozzárendelés _alól mentesülnek_ .

## <a name="policy-definition-ids"></a>Szabályzat-definíciós azonosítók

Ha az `policyAssignmentId` egy kezdeményezési hozzárendelésre vonatkozik, a `policyDefinitionReferenceIds` tulajdonság segítségével megadhatja, hogy mely házirend-definíció (k) szerepel a kezdeményezésben, amelynek a tulajdonosi erőforrásának kivétele van. Mivel az erőforrás egy vagy több belefoglalt szabályzat-definícióból is kivételt képez, ez a tulajdonság egy _tömb_. Az értékeknek egyezniük kell a mezőkben szereplő kezdeményezési definíció értékeivel `policyDefinitions.policyDefinitionReferenceId` .

## <a name="exemption-category"></a>Kivétel kategóriája

Két kivételi kategória létezik, és a rendszer a kivételek csoportosítására használja:

- **Enyhített**: a rendszer megadta a kivételt, mert a házirend célja egy másik módszerrel teljesül.
- **Lemondás**: a kivételt a rendszer azért adja meg, mert az erőforrás nem megfelelőségi állapota átmenetileg el van fogadva. Egy másik ok, hogy ezt a kategóriát olyan erőforrás-vagy erőforrás-hierarchiára használja, amelyet egy kezdeményezés egy vagy több definíciójában ki kell zárni, de a teljes kezdeményezésből nem zárható ki.

## <a name="expiration"></a>Lejárat

Állítsa be a tulajdonságot, ha egy erőforrás-hierarchia vagy egy adott erőforrás már nem _mentesül_ egy hozzárendeléshez `expiresOn` . Ennek a választható tulajdonságnak az univerzális ISO 8601 DateTime formátumban kell lennie `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> A házirend alóli kivételek nem törlődnek a `expiresOn` dátum elérésekor. Az objektum megmarad a nyilvántartás megőrzése érdekében, de a kivétel már nem teljesül.

## <a name="required-permissions"></a>Szükséges engedélyek

A szabályzat-mentesítési objektumok kezeléséhez szükséges Azure RBAC-engedélyek a `Microsoft.Authorization/policyExemptions` műveleti csoportban találhatók. A beépített szerepkörök erőforrás- [házirend közreműködője](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) és [biztonsági rendszergazdája](../../../role-based-access-control/built-in-roles.md#security-admin) egyaránt rendelkezik a `read` és az `write` engedélyek és a [házirend-elemzések adatírója (előzetes](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) verzió) `read` engedéllyel.

A kivételek a kivételek megadásának következményei miatt további biztonsági intézkedésekkel bírnak. A `Microsoft.Authorization/policyExemptions/write` műveletnek az erőforrás-hierarchián vagy az egyedi erőforráson túl kell lennie ahhoz, hogy a kivétel a `exempt/Action` cél-hozzárendelésen legyen.

## <a name="next-steps"></a>Következő lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).