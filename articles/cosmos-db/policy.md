---
title: A Azure Policy használata Azure Cosmos DB erőforrások irányításának és szabályozásának megvalósításához
description: Megtudhatja, hogyan használhatja a Azure Policyt Azure Cosmos DB erőforrások irányításának és szabályozásának megvalósítására.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1390f5db6e0f0370788bef60d5a2cafee1e8a96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080651"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>A Azure Policy használata Azure Cosmos DB erőforrások irányításának és szabályozásának megvalósításához
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[Azure Policy](../governance/policy/overview.md) segítséget nyújt a szervezeti irányítási szabványok betartatásához, az erőforrás-megfelelőség értékeléséhez és az automatikus szervizelés megvalósításához. Gyakori használati esetek közé tartozik a biztonság, a Cost-kezelés és a konfiguráció konzisztenciája.

A Azure Policy beépített szabályzat-definíciókat biztosít. Létrehozhat egyéni szabályzat-definíciókat olyan forgatókönyvekhez, amelyeket a beépített szabályzat-definíciók nem céloznak meg. További részletekért tekintse meg a [Azure Policy dokumentációját](../governance/policy/overview.md) .

> [!IMPORTANT]
> Azure Policy az Azure-szolgáltatások erőforrás-szolgáltatói szintjén lesz kikényszerítve. Cosmos DB SDK-k a legtöbb felügyeleti műveletet elvégezhetik a Cosmos DB erőforrás-szolgáltatóját kikerülő adatbázison, tárolón és átviteli sebességen, így figyelmen kívül hagyva az Azure Policy használatával létrehozott házirendeket. A házirendek betartatásának biztosítása érdekében lásd: [a Azure Cosmos db SDK-k változásainak megakadályozása](role-based-access-control.md#prevent-sdk-changes)

## <a name="assign-a-built-in-policy-definition"></a>Beépített szabályzatdefiníció hozzárendelése

A szabályzat-definíciók írják le az erőforrás-megfelelőségi feltételeket, valamint azt, hogy egy adott feltétel teljesül-e. A szabályzat- _hozzárendelések_ a szabályzat- _definíciók_ alapján jönnek létre. A Azure Cosmos DB erőforrásaihoz beépített vagy egyéni szabályzat-definíciókat használhat. A szabályzat-hozzárendelések egy Azure felügyeleti csoportra, egy Azure-előfizetésre vagy egy erőforráscsoport hatókörére vonatkoznak, és a rendszer a kijelölt hatókörben lévő erőforrásokra alkalmazza őket. Igény szerint kizárhat bizonyos erőforrásokat a hatókörből.

Házirend-hozzárendeléseket a [Azure Portal](../governance/policy/assign-policy-portal.md), az [Azure PowerShell](../governance/policy/assign-policy-powershell.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md)vagy az [ARM sablon](../governance/policy/assign-policy-template.md)segítségével hozhat létre.

Ha a házirend-hozzárendelést a Azure Cosmos DB beépített szabályzat-definíciójában szeretné létrehozni, kövesse a [szabályzat-hozzárendelés létrehozása a Azure Portal](../governance/policy/assign-policy-portal.md) cikkben leírt lépéseket.

A házirend-definíció kiválasztásához írja be a `Cosmos DB` kifejezést a Keresés mezőbe az elérhető beépített szabályzat-definíciók listájának szűréséhez. Válassza ki a rendelkezésre álló beépített szabályzat-definíciók egyikét, majd válassza a **kijelölés** lehetőséget a szabályzat-hozzárendelés létrehozásához.

> [!TIP]
> A házirend-hozzárendelések létrehozásához használhatja a **rendelkezésre álló definíciók** ablaktáblán látható beépített szabályzat-definíciók nevét is Azure PowerShell, Azure CLI vagy ARM-sablonokkal.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Azure Cosmos DB beépített szabályzat-definíciók keresése":::

## <a name="create-a-custom-policy-definition"></a>Egyéni szabályzatdefiníció létrehozása

A beépített szabályzatok által nem tárgyalt konkrét forgatókönyvek esetén létrehozhat [egy egyéni szabályzat-definíciót](../governance/policy/tutorials/create-custom-policy-definition.md)is. Később létrehoz egy szabályzat- _hozzárendelést_ az egyéni szabályzat- _definícióból_.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tulajdonságok típusai és tulajdonság-aliasok a házirend-szabályokban

Az [egyéni házirend-definíció lépéseivel](../governance/policy/tutorials/create-custom-policy-definition.md) azonosíthatja az erőforrás-tulajdonságokat és a tulajdonság-aliasokat, amelyek szükségesek a házirend-szabályok létrehozásához.

Azure Cosmos DB adott tulajdonság-aliasok azonosításához használja a névteret az `Microsoft.DocumentDB` egyéni házirend-definíció lépései cikkben látható módszerek egyikével.

#### <a name="use-the-azure-cli"></a>Az Azure CLI használata:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Azure PowerShell használata:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Ezek a parancsok a Azure Cosmos DB tulajdonsághoz tartozó tulajdonságok aliasának listáját jelenítik meg. A következő egy részlet a kimenetből:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Az [egyéni házirend-definíciós szabályokban](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)bármelyik tulajdonság-alias nevet használhatja.

Az alábbi példa olyan házirend-definíciót mutat be, amely ellenőrzi, hogy egy Azure Cosmos DB-fiók több írási helyhez van-e konfigurálva. Az egyéni házirend-definíció két szabályt tartalmaz: az egyiket, hogy megkeresse az adott típusú tulajdonság-aliast, a második pedig a típus adott tulajdonságát, ebben az esetben a több írási hely beállítást tároló mezőt. Mindkét szabály az alias nevét használja.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

Az egyéni szabályzat-definíciók használatával házirend-hozzárendeléseket hozhat létre, ugyanúgy, mint a beépített szabályzat-definíciók.

## <a name="policy-compliance"></a>Szabályzatmegfelelőség

A szabályzat-hozzárendelések létrehozása után Azure Policy kiértékeli a hozzárendelés hatókörében lévő erőforrásokat. A rendszer minden erőforrás _megfelelőségét_ kivizsgálja a szabályzattal. A házirendben megadott _effektus_ a nem megfelelő erőforrásokra lesz alkalmazva.

A megfelelőségi eredményeket és a szervizelés részleteit a [Azure Portalban](../governance/policy/how-to/get-compliance-data.md#portal) , illetve az [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) -n vagy a [Azure monitor-naplókon](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)tekintheti át.

Az alábbi képernyőfelvételen két példa van a házirend-hozzárendelésekre.

Az egyik hozzárendelés egy beépített szabályzat-definíción alapul, amely ellenőrzi, hogy a Azure Cosmos DB-erőforrások csak az engedélyezett Azure-régiókra vannak-e telepítve. Az erőforrás-megfelelőség a szabályzat kiértékelésének eredményét (megfelelő vagy nem megfelelő) jeleníti meg a hatókörben lévő erőforrásokhoz.

A másik hozzárendelés egy egyéni szabályzat-definíción alapul. Ez a hozzárendelés ellenőrzi, hogy a Cosmos DB-fiókok több írási helyhez vannak-e konfigurálva.

A szabályzat-hozzárendelések telepítése után a megfelelőségi irányítópulton megjelennek a kiértékelés eredményei. Vegye figyelembe, hogy ez a szabályzat-hozzárendelés telepítése után akár 30 percet is igénybe vehet. Emellett [a szabályzat-kiértékelési vizsgálatok azonnal elindíthatók a szabályzat-](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) hozzárendelések létrehozása után is.

A képernyőképen a következő megfelelőségi kiértékelési eredmények láthatók a hatókörön belüli Azure Cosmos DB fiókok esetében:

- A két fiók közül nulla megfelel egy olyan házirendnek, amely Virtual Network (VNet) szűrést kell konfigurálni.
- A két fiók közül nulla megfelel egy olyan házirendnek, amely megköveteli, hogy a fiók több írási helyhez legyen konfigurálva
- A két fiók közül nulla megfelel egy olyan házirendnek, amelyet az erőforrások üzembe helyezése engedélyezett az Azure-régiók számára.

:::image type="content" source="./media/policy/compliance.png" alt-text="Megfelelési eredmények a felsorolt Azure Policy-hozzárendelésekhez":::

A nem megfelelő erőforrások javításához tekintse meg a következő témakört: [erőforrások szervizelése Azure Policysal](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Következő lépések

- [Tekintse át a Azure Cosmos db egyéni szabályzat-definícióit](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), beleértve a több írási helyet és a fentiekben látható VNet-szűrési házirendeket.
- [Szabályzat-hozzárendelés létrehozása a Azure Portalban](../governance/policy/assign-policy-portal.md)
- [Tekintse át Azure Policy beépített szabályzat-definíciókat Azure Cosmos DB](./policy-reference.md)