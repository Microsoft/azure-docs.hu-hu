---
title: Üzembehelyezési módok
description: Leírja, hogy miként lehet megállapítani, hogy a teljes vagy növekményes központi telepítési módot használja-e Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 3f1f74c0495e0d43671712281a35a7e74fd7d821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104888838"
---
# <a name="azure-resource-manager-deployment-modes"></a>Az Azure Resource Manager üzembe helyezési módjai

Az erőforrások telepítésekor meg kell adnia, hogy a központi telepítés Növekményes frissítés vagy teljes frissítés. A két mód közötti különbség az, hogy az erőforrás-kezelő hogyan kezelje a sablonban nem szereplő erőforrás-csoport meglévő erőforrásait.

A Resource Manager mindkét módban megpróbálja létrehozni a sablonban megadott összes erőforrást. Ha az erőforrás már létezik az erőforráscsoporthoz, és a beállításai nem változnak, akkor a rendszer nem végez műveletet az adott erőforráshoz. Ha módosítja egy erőforrás tulajdonságérték értékét, az erőforrás frissül az új értékekkel. Ha egy meglévő erőforrás helyét vagy típusát kísérli meg frissíteni, a központi telepítés hibát jelez. Ehelyett helyezzen üzembe egy új erőforrást a szükséges hely vagy típus szerint.

Az alapértelmezett mód növekményes.

## <a name="complete-mode"></a>Teljes mód

Teljes módban a Resource Manager **törli** az erőforráscsoport meglévő erőforrásait, de nincs megadva a sablonban.

> [!NOTE]
> A sablonok teljes módban való üzembe helyezése előtt mindig használja a [mi-if műveletet](template-deploy-what-if.md) . Mi a teendő, hogy mely erőforrások lesznek létrehozva, törölve vagy módosítva. A mi – if használatával elkerülhető az erőforrások akaratlan törlése.

Ha a sablon olyan erőforrást tartalmaz, amely nincs központilag telepítve, mert a [feltétel](conditional-resource-deployment.md) hamis értéket ad vissza, akkor az eredmény attól függ, hogy melyik REST API-verziót használja a sablon telepítéséhez. Ha 2019-05-10-nál korábbi verziót használ, az erőforrás **nem törlődik**. A 2019-05-10-es vagy újabb verziókban az erőforrás **törölve lesz**. Az Azure PowerShell és az Azure CLI legújabb verziói törlik az erőforrást.

Ügyeljen arra, hogy a teljes mód legyen a [másolási hurkokkal](copy-resources.md). A rendszer a másolási hurok feloldása után nem a sablonban megadott erőforrásokat törli.

Ha [egy sablonban több erőforráscsoporthoz](./deploy-to-resource-group.md)is telepít, a telepítési műveletben megadott erőforráscsoport erőforrásai törölhetők. A másodlagos erőforráscsoportok erőforrásai nem törlődnek.

Az erőforrástípusok eltérő módon kezelik a teljes módú törlést. A fölérendelt erőforrások automatikusan törlődnek, ha nem egy teljes módban telepített sablonban vannak. Néhány alárendelt erőforrás nem törlődik automatikusan, ha nem a sablonban. Ezeket a gyermek erőforrásokat azonban törli a rendszer, ha törli a fölérendelt erőforrást.

Ha például az erőforráscsoport egy DNS-zónát ( `Microsoft.Network/dnsZones` erőforrástípust) és egy CNAME rekordot ( `Microsoft.Network/dnsZones/CNAME` erőforrástípus) tartalmaz, a DNS-zóna a CNAME rekord szülő erőforrása. Ha a üzembe helyezése teljes módban történik, és nem tartalmazza a DNS-zónát a sablonban, a DNS-zónát és a CNAME-rekordot is törli a rendszer. Ha belefoglalja a DNS-zónát a sablonba, de nem tartalmazza a CNAME rekordot, a CNAME nem törlődik.

Az erőforrástípusok törlésének módjával kapcsolatban lásd: [Az Azure-erőforrások törlése a teljes módú telepítésekhez](complete-mode-deletion.md).

Ha az erőforráscsoport [zárolva](../management/lock-resources.md)van, a teljes mód nem törli az erőforrásokat.

> [!NOTE]
> Csak a legfelső szintű sablonok támogatják a teljes telepítési módot. [Csatolt vagy beágyazott sablonok](linked-templates.md)esetén a növekményes módot kell használnia.
>
> Az [előfizetési szintű központi telepítések](deploy-to-subscription.md) nem támogatják a teljes üzemmódot.
>
> A portál jelenleg nem támogatja a teljes üzemmódot.
>

## <a name="incremental-mode"></a>Növekményes mód

A növekményes módban a Resource Manager változatlan erőforrásokat **hagy** az erőforráscsoporthoz, de nincs megadva a sablonban. A sablon erőforrásai **hozzáadódnak** az erőforráscsoporthoz.

> [!NOTE]
> Ha egy meglévő erőforrást növekményes módban telepít át, a rendszer az összes tulajdonságot újra alkalmazza. A **Tulajdonságok Növekményesen nem vehetők** fel. Gyakori félreértés a sablonban nem megadott tulajdonságok változatlanul maradnak. Ha nem ad meg bizonyos tulajdonságokat, a Resource Manager a központi telepítést úgy értelmezi, hogy felülírja ezeket az értékeket. A sablonban nem szereplő tulajdonságok visszaállnak az alapértelmezett értékekre. Itt adhatja meg az erőforrás nem alapértelmezett értékeit, nem csak a frissíteni kívánt értékeket. A sablon erőforrás-definíciója mindig az erőforrás végső állapotát tartalmazza. Nem jelenthet részleges frissítést egy meglévő erőforráshoz.
>
> Ritka esetekben az erőforráshoz megadott tulajdonságok ténylegesen alárendelt erőforrásként valósulnak meg. Ha például egy webalkalmazáshoz adja meg a hely konfigurációs értékeit, ezeket az értékeket a gyermek erőforrástípus adja meg `Microsoft.Web/sites/config` . Ha újra telepíti a webalkalmazást, és megad egy üres objektumot a hely konfigurációs értékeihez, a gyermek erőforrás nem frissül. Ha azonban új hely konfigurációs értékeket ad meg, a gyermek erőforrástípus frissül.

## <a name="example-result"></a>Példa eredménye

A növekményes és a teljes üzemmód közötti különbség szemléltetéséhez vegye figyelembe a következő helyzetet.

**Erőforráscsoport** :

* A erőforrás
* B erőforrás
* C erőforrás

A **sablon** tartalma:

* A erőforrás
* B erőforrás
* D erőforrás

**Növekményes** módban való üzembe helyezéskor az erőforráscsoport a következőket végzi el:

* A erőforrás
* B erőforrás
* C erőforrás
* D erőforrás

A **teljes** módban való üzembe helyezéskor a C erőforrás törlődik. Az erőforráscsoport a következőket tartalmazta:

* A erőforrás
* B erőforrás
* D erőforrás

## <a name="set-deployment-mode"></a>Telepítési mód beállítása

A PowerShell használatával történő üzembe helyezési mód beállításához használja a `Mode` paramétert.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Az Azure CLI-vel történő üzembe helyezési mód beállításához használja a `mode` paramétert.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A következő példa egy olyan csatolt sablont mutat be, amely növekményes üzembe helyezési módra van beállítva:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>Következő lépések

* A Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* Az erőforrások üzembe helyezésével kapcsolatos további tudnivalókért lásd: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md).
* Az erőforrás-szolgáltató műveleteinek megtekintéséhez lásd: [Azure REST API](/rest/api/).
