---
title: Üzembe Azure Cache for Redis sablonnal Azure Resource Manager üzembe
description: Megtudhatja, hogyan helyezhet üzembe egy Azure Resource Manager-sablont (ARM-sablont) egy Azure Cache for Redis üzembe. A sablonok a gyakori forgatókönyvekhez biztosítanak sablonokat.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 08/18/2020
ms.openlocfilehash: 81940d23ebfcc017455974981649023351b6eeb3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835055"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Rövid útmutató: Azure Cache for Redis létrehozása ARM-sablonnal

Megtudhatja, hogyan hozhat létre Azure Resource Manager sablont (ARM-sablont), amely üzembe helyez egy Azure Cache for Redis. A gyorsítótár egy meglévő tárfiókkal együtt használható a diagnosztikai adatok tárolásához. Azt is megtudhatja, hogyan határozhatja meg az üzembe helyezett erőforrásokat, és hogyan határozhatja meg az üzembe helyezés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Jelenleg a diagnosztikai beállítások meg vannak osztva az előfizetéshez ugyanabban a régióban lévő összes gyorsítótárhoz. A régióban lévő egyik gyorsítótár frissítése hatással van a régióban lévő összes többi gyorsítótárra.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Tárfiók:** A fiók létrehozásához lásd: [Azure Storage-fiók létrehozása.](../storage/common/storage-account-create.md?tabs=azure-portal) A tárfiók diagnosztikai adatokhoz használható.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-redis-cache/) közül származik.

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

A sablon a következő erőforrásokat definiálja:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager prémium szintű csomaghoz [](cache-overview.md#service-tiers) is elérhetők további sablonok.

* [Prémium szintű Azure Cache for Redis létrehozása fürtözéssel](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Prémium szintű Azure Cache for Redis adatmegőrzéssel](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Premium Redis Cache üzembe helyezett prémium szintű Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

A legújabb sablonok kereséséhez lásd: [Azure gyorsindítási sablonok,](https://azure.microsoft.com/documentation/templates/) és keressen rá a _Azure Cache for Redis._

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az alábbi kép kiválasztásával jelentkezzen be az Azure-ba, és nyissa meg a sablont.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Válassza ki vagy adja meg a következő értékeket:

    * **Előfizetés:** válasszon ki egy Azure-előfizetést, amely az adat megosztás és a többi erőforrás létrehozásához szükséges.
    * **Erőforráscsoport:** új **erőforráscsoport létrehozásához** válassza az Új létrehozása lehetőséget, vagy válasszon ki egy meglévő erőforráscsoportot.
    * **Hely**: válasszon egy helyet az erőforráscsoportnak. A tárfióknak és a Redis Cache-gyorsítótárnak ugyanabban a régióban kell lennie. Alapértelmezés szerint a Redis Cache ugyanazt a helyet használja, mint az erőforráscsoport. Ezért adja meg ugyanazt a helyet, mint a tárfiók.
    * **Redis Cache Név:** adja meg a Redis Cache nevét.
    * **Meglévő diagnosztikai tárfiók:** adja meg egy tárfiók erőforrás-azonosítóját. A szintaxis a következő: `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    A többi beállításnál használja az alapértelmezett értéket.
1. válassza **az Elfogadom a fenti feltételeket** és feltételeket lehetőséget, majd válassza a Vásárlás **lehetőséget.**

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg a létrehozott Redis Cache-t.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure Resource Manager sablont, amely üzembe helyez egy Azure Cache for Redis. Az Azure-webalkalmazást az Azure Cache for Redis-val üzembe helyező Azure Resource Manager-sablon létrehozásáról a Webalkalmazás létrehozása és Azure Cache for Redis sablon használatával cikkből [tájékozódhat.](./cache-web-app-arm-with-redis-cache-provision.md)
