---
title: A sikeres üzembe helyezés hibába való visszaállítása
description: Azt határozza meg, hogy egy sikertelen központi telepítés visszaálljon-e egy sikeres üzembe helyezésre.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492212"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Hiba visszaállítása a sikeres központi telepítéshez

Ha egy telepítés meghiúsul, automatikusan újratelepítheti a korábbi, sikeres telepítést az üzembe helyezési előzményekből. Ez a funkció akkor hasznos, ha az infrastruktúra központi telepítésének ismert jó állapota van, és ezt az állapotot szeretné visszaállítani. Megadhat egy adott korábbi telepítést vagy az utolsó sikeres telepítést.

> [!IMPORTANT]
> Ez a szolgáltatás egy korábbi üzemelő példány újbóli üzembe helyezésével visszaállít egy sikertelen telepítést. Ez az eredmény eltérő lehet, mint amit a sikertelen telepítés visszavonása elvár. Győződjön meg arról, hogy megérti a korábbi központi telepítés újratelepítésének módját.

## <a name="considerations-for-redeploying"></a>Az újratelepítéssel kapcsolatos megfontolások

A szolgáltatás használata előtt tekintse át az alábbi adatokat az újratelepítés kezelésével kapcsolatban:

- Az előző üzemelő példány a [teljes mód](./deployment-modes.md#complete-mode)használatával fut, még akkor is, ha a korábbi telepítés során [növekményes módot](./deployment-modes.md#incremental-mode) használt. A teljes módban történő ismételt üzembe helyezés váratlan eredményeket eredményezhet, ha a korábbi központi telepítés növekményes használatot használ. A teljes mód azt jelenti, hogy az előző üzemelő példányban nem szereplő összes erőforrás törlődik. Adja meg az erőforráscsoport egyik korábbi központi telepítését, amely az összes erőforrást és azok állapotait képviseli. További információ: [telepítési módok](./deployment-modes.md).
- Az újratelepítést a rendszer pontosan úgy futtatja, ahogy korábban ugyanazzal a paraméterekkel futtatta. A paraméterek nem módosíthatók.
- Az újratelepítés csak az erőforrásokat befolyásolja, az adatváltozások nincsenek hatással.
- Ezt a funkciót csak az erőforráscsoport-telepítések esetén használhatja. Nem támogatja az előfizetés, a felügyeleti csoport vagy a bérlői szintű üzemelő példányok használatát. Az előfizetési szintű telepítéssel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](./deploy-to-subscription.md).
- Ezt a lehetőséget csak gyökérszintű központi telepítések esetén használhatja. A beágyazott sablonból történő központi telepítések nem érhetők el az újratelepítéshez.

Ha ezt a beállítást szeretné használni, a központi telepítéseknek egyedi névvel kell rendelkezniük az üzembe helyezési előzményekben. Csak olyan egyedi nevekkel rendelkezhet, amelyeket egy adott központi telepítés azonosítható. Ha nem rendelkezik egyedi névvel, akkor a sikertelen központi telepítés felülírhatja a sikeres telepítést az előzményekben.

Ha olyan korábbi központi telepítést ad meg, amely nem létezik az üzembe helyezési előzményekben, a visszaállítás hibát jelez.

## <a name="powershell"></a>PowerShell

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez adja hozzá a `-RollbackToLastDeployment` paramétert jelzőként.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Egy adott központi telepítés újbóli üzembe helyezéséhez használja a `-RollBackDeploymentName` paramétert, és adja meg a központi telepítés nevét. A megadott központi telepítésnek sikeresnek kell lennie.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez adja hozzá a `--rollback-on-error` paramétert jelzőként.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Egy adott központi telepítés újbóli üzembe helyezéséhez használja a `--rollback-on-error` paramétert, és adja meg a központi telepítés nevét. A megadott központi telepítésnek sikeresnek kell lennie.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez, ha az aktuális telepítés meghiúsul, használja a következőt:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Ha az aktuális telepítés meghiúsulása esetén a megadott központi telepítés újratelepítése sikertelen, használja a következőt:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A megadott központi telepítésnek sikeresnek kell lennie.

## <a name="next-steps"></a>Következő lépések

- A teljes és a növekményes mód megismeréséhez tekintse meg [Azure Resource Manager telepítési módokat](deployment-modes.md).
- Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](template-syntax.md)ismertető témakört.
