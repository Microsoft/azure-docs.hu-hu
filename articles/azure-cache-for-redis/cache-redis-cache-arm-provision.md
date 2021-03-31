---
title: Az Azure cache üzembe helyezése a Redis Azure Resource Manager sablon használatával
description: Megtudhatja, hogyan használhat egy Azure Resource Manager sablont (ARM-sablon) egy Azure cache Redis-erőforráshoz való üzembe helyezéséhez. A sablonok a gyakori forgatókönyvek esetében érhetők el.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92735978"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Rövid útmutató: Azure cache létrehozása Redis ARM-sablon használatával

Megtudhatja, hogyan hozhat létre olyan Azure Resource Manager-sablont (ARM-sablon), amely üzembe helyez egy Azure-gyorsítótárat a Redis. A gyorsítótárat meglévő Storage-fiókkal is használhatja a diagnosztikai adatkezelés érdekében. Azt is megtudhatja, hogyan határozhatja meg, hogy mely erőforrások legyenek telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Jelenleg a diagnosztikai beállítások az adott régióban lévő összes gyorsítótárhoz meg vannak osztva egy előfizetéshez. A régió egyik gyorsítótárának frissítése hatással van a régió összes többi gyorsítótárára.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Egy Storage-fiók**: hozzon létre egy [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)című témakört. A Storage-fiók diagnosztikai adatként van használatban.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-redis-cache/) közül származik.

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

A következő erőforrások vannak definiálva a sablonban:

* [Microsoft. cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft. bepillantások/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Az új [prémium szintű](cache-overview.md#service-tiers) Resource Manager-sablonok is elérhetők.

* [Prémium szintű Azure cache létrehozása a fürtözést biztosító Redis](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Prémium szintű Azure cache létrehozása a Redis adatmegőrzési szolgáltatásával](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Virtual Network üzembe helyezett prémium szintű Redis Cache létrehozása](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

A legújabb sablonok kereséséhez tekintse meg az Azure rövid útmutató [sablonjait](https://azure.microsoft.com/documentation/templates/) , és keressen rá az _Azure cache Redis_.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez és a sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Válassza ki vagy adja meg a következő értékeket:

    * **Előfizetés**: válassza ki az adatmegosztás és a többi erőforrás létrehozásához használt Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához vagy egy meglévő erőforráscsoport kiválasztásához.
    * **Hely**: válasszon egy helyet az erőforráscsoportnak. A Storage-fióknak és a Redis-gyorsítótárnak ugyanabban a régióban kell lennie. Alapértelmezés szerint a Redis gyorsítótár ugyanazt a helyet használja, mint az erőforráscsoport. Ezért a Storage-fiókkal megegyező helyet kell megadnia.
    * **Redis cache neve**: adja meg a Redis-gyorsítótár nevét.
    * **Meglévő diagnosztikai Storage-fiók**: adja meg a Storage-fiók erőforrás-azonosítóját. A szintaxis a következő: `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Használja az alapértelmezett értéket a többi beállításhoz.
1. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**, valamint a **vásárlás** lehetőséget.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg a létrehozott Redis cache-gyorsítótárat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Azure Resource Manager-sablont, amely üzembe helyez egy Azure-gyorsítótárat a Redis. Ha meg szeretné tudni, hogyan hozhat létre olyan Azure Resource Manager-sablont, amely egy Azure-webalkalmazást telepít az Azure cache for Redis szolgáltatásban, tekintse meg [a webalkalmazások és az Azure cache létrehozása a Redis sablon használatával](./cache-web-app-arm-with-redis-cache-provision.md)című témakört.
