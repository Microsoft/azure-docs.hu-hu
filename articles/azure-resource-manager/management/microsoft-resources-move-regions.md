---
title: Erőforrások régióinak áthelyezése a Microsoft.Resources szolgáltatásban
description: A Microsoft.Resources névtérben található erőforrások áthelyezése új régiókba.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725871"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Microsoft.Resources-erőforrások áthelyezése új régióba

Előfordulhat, hogy egy meglévő erőforrást kell áthelyezni egy új régióba. Ez a cikk bemutatja, hogyan mozgathat két erőforrástípust ( templateSpecs és deploymentScripts) a Microsoft.Resources névtérben.

## <a name="move-template-specs-to-new-region"></a>Sablonsokdiák áthelyezése új régióba

Ha egy régióban [van](../templates/template-specs.md) egy sablonsokaját, és át szeretné áthelyezni egy új régióba, exportálhatja a sablon specifikációját, és újra üzembe is használhatja.

1. A paranccsal exportálhatja a meglévő sablonsokaját. A paraméterértékek értékeként adja meg az exportálni kívánt sablonsokkjának megfelelő értékeket.

   A Azure PowerShell használja a következőt:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Azure CLI esetén használja az alábbi parancsot:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Az exportált sablon specifikációi alapján hozzon létre egy új sablonsokaját. Az alábbi példák bemutatják `westus` az új régiót, de meg is használhatja a kívánt régiót.

   A Azure PowerShell használja a következőt:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Azure CLI esetén használja az alábbi parancsot:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Üzembe helyezési szkriptek áthelyezése új régióba

1. Válassza ki azt az erőforráscsoportot, amely az új régióba áthelyezni kívánt üzembe helyezési szkriptet tartalmazza.

1. [Exportálja a sablont.](../templates/export-template-portal.md) Exportáláskor válassza ki az üzembe helyezési szkriptet és az egyéb szükséges erőforrásokat.

1. Az exportált sablonban törölje a következő tulajdonságokat:

   * tenantId (bérlőazonosító)
   * principalId
   * ügyfél-azonosító

1. Az exportált sablon szoftveres értékkel rendelkezik az üzembe helyezési szkript régiójához.

   ```json
   "location": "westus2",
   ```

   Módosítsa a sablont úgy, hogy engedélyezze a hely beállításának paraméterét. További információ: Erőforrás [helyének beállítása az ARM-sablonban](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Telepítse az exportált sablont,](../templates/deploy-powershell.md) és adjon meg egy új régiót az üzembe helyezési szkript számára.

## <a name="next-steps"></a>Következő lépések

* További információ az erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezésével kapcsolatban: Erőforrások áthelyezése új [erőforráscsoportba vagy előfizetésbe.](move-resource-group-and-subscription.md)
* További információ az erőforrások új régióba való áthelyezésről: [Erőforrások áthelyezése régiók között.](move-resources-overview.md#move-resources-across-regions)
