---
title: 'Rövid útmutató: Megosztott lekérdezés létrehozása sablonokkal'
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) fog használni egy megosztott Resource Graph, amely operációs rendszer szerint számolja a virtuális gépeket.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: ac736d4371531bb38a8cd2cf095acbdfbc7c08a1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535783"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Rövid útmutató: Megosztott lekérdezés létrehozása ARM-sablonnal

Resource Graph lekérdezések privát lekérdezésként _vagy_ megosztott lekérdezésként _menthetők._ A privát lekérdezések az egyéni felhasználók portáljának profiljába lesznek mentve, és mások számára nem láthatók. A megosztott lekérdezés egy Resource Manager, amely engedélyekkel és szerepköralapú hozzáféréssel megosztható másokkal. A megosztott lekérdezések az erőforrás-felderítés közös és konzisztens végrehajtását biztosítják. Ez a rövid útmutató egy Azure Resource Manager sablont (ARM-sablont) használ egy megosztott lekérdezés létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése megosztott lekérdezés létrehozásához az Azure-ban" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Ebben a rövid útmutatóban egy Virtuális gépek száma operációs rendszer szerint nevű megosztott _lekérdezést hoz létre._ A lekérdezés SDK-ban vagy a portálon az Resource Graph Explorerrel való kipróbálását lásd: Minták – Virtuális gépek száma [operációsrendszer-típus szerint.](./samples/starter.md#count-os)

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/) közül származik.

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

A sablonban definiált erőforrás a következő:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

> [!NOTE]
> Azure Resource Graph szolgáltatás ingyenes. További információ: [Overview of Azure Resource Graph](./overview.md).

1. Az alábbi képre kattintva jelentkezzen be az Azure Portalra, és nyissa meg a sablont:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése megosztott lekérdezés létrehozásához az Azure-ban" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Válassza ki vagy adja meg a következő értékeket:

   | Name | Érték |
   |------|-------|
   | Előfizetés | Válassza ki Azure-előfizetését. |
   | Erőforráscsoport | Válassza **az Új létrehozása** lehetőséget, adjon meg egy nevet, majd kattintson az OK **gombra.** |
   | Hely | Válasszon régiót. Például: **USA középső régiója**. |
   | Lekérdezés neve | Hagyja meg az alapértelmezett értéket: **Virtuális gépek száma operációs rendszer szerint.** |
   | Kód lekérdezése | Hagyja meg az alapértelmezett értéket: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Lekérdezés leírása | Hagyja meg az alapértelmezett értéket: Ez a **megosztott lekérdezés megszámol minden** virtuálisgép-erőforrást, és az operációs rendszer típusa alapján összegzi. |
   | Elfogadom a fenti feltételeket és feltételeket | (Kijelölés) |

1. Válassza a **Beszerzés** lehetőséget.

Néhány további forrás:

- További mintasablonokért lásd: [Azure gyorsindítási sablon.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)
- A sablonreferenciát az [Azure-sablonreferenciában láthatja.](/azure/templates/microsoft.resourcegraph/allversions)
- Az ARM-sablonok fejlesztésével kapcsolatos további információkért tekintse meg [a Azure Resource Manager dokumentációját.](../../azure-resource-manager/management/overview.md)
- Az előfizetés-szintű üzembe helyezéssel kapcsolatos további információkért lásd: Erőforráscsoportok és erőforrások [létrehozása előfizetési szinten.](../../azure-resource-manager/templates/deploy-to-subscription.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az új megosztott lekérdezés futtatásához kövesse az alábbi lépéseket:

1. A portál keresősávjában keressen rá a Resource Graph **és** válassza ki.

1. Válassza ki a **Virtuális** gépek száma operációs rendszer  szerint nevű megosztott lekérdezést, majd válassza az Eredmények lapot az **Áttekintés lapon.**

A megosztott lekérdezés az Explorer Resource Graph is megnyitható:

1. A portál keresősávjában keressen rá az **Resource Graph explorer kifejezésre,** és válassza ki.

1. Válassza **a Lekérdezés megnyitása gombot.**

1. Módosítsa **a Típust** _Megosztott lekérdezésekre._ Ha a listában nem látja a Virtuális gépek száma **operációs** rendszer szerint listában, a szűrőmezővel korlátozhatja az eredményeket. Ha a **Virtuális gépek száma operációs rendszer** szerint megosztott lekérdezés látható, válassza ki a nevét.

1. A lekérdezés betöltése után kattintson a **Lekérdezés futtatása gombra.** Az eredmények az Eredmények **lapon jelennek** meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott megosztott lekérdezés eltávolításához kövesse az alábbi lépéseket:

1. A portál keresősávjában keressen rá a Resource Graph **és** válassza ki.

1. Jelölje be a Virtuális gépek száma operációs rendszer szerint nevű megosztott lekérdezés **melletti jelölőnégyzetet.**

1. Kattintson **a Törlés** gombra az oldal tetején.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy megosztott Resource Graph lekérdezést.

A megosztott lekérdezésekkel kapcsolatos további információkért folytassa a következő oktatóanyagokkal:

> [!div class="nextstepaction"]
> [Lekérdezések kezelése a Azure Portal](./tutorials/create-share-query.md)
