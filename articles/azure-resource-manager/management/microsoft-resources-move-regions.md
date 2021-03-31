---
title: Régiók áthelyezése a Microsoft. Resources erőforrásaiba
description: Bemutatjuk, hogyan helyezheti át a Microsoft. Resources névtérben lévő erőforrásokat új régiókba.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88951053"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>A Microsoft. Resources erőforrásainak áthelyezése új régióba

Lehetséges, hogy egy meglévő erőforrást egy új régióba kell áthelyeznie. Ez a cikk bemutatja, hogyan helyezhet át két erőforrástípust – templateSpecs és deploymentScripts –, amelyek a Microsoft. Resources névtérben találhatók.

## <a name="move-template-specs-to-new-region"></a>Sablon specifikációinak áthelyezése új régióba

Ha az egyik régióban van egy [sablon](../templates/template-specs.md) , és szeretné áthelyezni az új régióba, exportálhatja a sablon specifikációját, és újból üzembe helyezheti azt.

1. Egy meglévő sablon exportálásához használja az parancsot. A paraméterek értékeinek megadásához adja meg az exportálni kívánt sablonhoz tartozó specifikációnak megfelelő értékeket.

   Azure PowerShell esetén használja a következőt:

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

1. Az exportált sablon specifikációjának használatával hozzon létre egy új sablont. Az alábbi példák az új régiót mutatják be, de megadhatja `westus` a kívánt régiót.

   Azure PowerShell esetén használja a következőt:

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

## <a name="move-deployment-scripts-to-new-region"></a>Üzembe helyezési parancsfájlok áthelyezése új régióba

1. Válassza ki azt az erőforráscsoportot, amely az új régióba áthelyezni kívánt telepítési parancsfájlt tartalmazza.

1. [Exportálja a sablont](../templates/export-template-portal.md). Exportáláskor válassza ki a telepítési parancsfájlt és a többi szükséges erőforrást.

1. Az exportált sablonban törölje a következő tulajdonságokat:

   * tenantId
   * principalId
   * ügyfél-azonosító

1. Az exportált sablon hardcoded értékkel rendelkezik az üzembehelyezési parancsfájl régiójában.

   ```json
   "location": "westus2",
   ```

   Módosítsa a sablont úgy, hogy engedélyezze a paramétert a hely beállításához. További információ: [erőforrás helyének beállítása az ARM-sablonban](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Telepítse az exportált sablont](../templates/deploy-powershell.md) , és adjon meg egy új régiót az üzembe helyezési parancsfájlhoz.

## <a name="next-steps"></a>Következő lépések

* Az erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos további információkért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).
* Az erőforrások új régióba való áthelyezésével kapcsolatos további információkért lásd: [Azure-erőforrások áthelyezése régiók között](move-region.md).
