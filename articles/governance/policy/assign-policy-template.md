---
title: Szabályzat-hozzárendelés létrehozása a Resource Manager-sablonnal
description: Ez a cikk végigvezeti a lépéseken egy Resource Manager-sablon használatával hozzon létre egy szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 03d15d30deabbc2bd34fbb4fba108be382f94fa1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65756511"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Gyors útmutató: Szabályzat-hozzárendelés nem megfelelő erőforrások azonosításához használatával a Resource Manager-sablon létrehozása

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából *nem megfelelőnek* minősülnek.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban a szabályzat-hozzárendelés létrehozása és hozzárendelése nevű beépített szabályzatdefiníciót *felügyelt lemezeket nem használó virtuális gépek naplózása*. Elérhető beépített szabályzatok részleges listáját lásd: [házirend minták](./samples/index.md).

Többféleképpen szabályzat-hozzárendelések létrehozására. Ez a rövid egy [gyorsindítási sablon](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Itt látható a sablon egy példányát:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Az Azure Policy szolgáltatást díjmentes.  További információkért lásd: [áttekintése az Azure Policy](./overview.md).

1. Válassza ki az alábbi képre kattintva jelentkezzen be az Azure Portalon, és nyissa meg a sablont:

   [![A házirend-sablon üzembe helyezése az Azure-bA](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket:

   | Name (Név) | Value |
   |------|-------|
   | Előfizetés | Válassza ki az Azure-előfizetését. |
   | Erőforráscsoport | Válassza ki **új létrehozása**, adjon meg egy nevet, és válassza **OK**. A képernyőképen az erőforráscsoport neve van *mypolicyquickstart\<MMDD dátum > rg*. |
   | Location egység | Válasszon régiót. Ha például **USA középső RÉGIÓJA**. |
   | Szabályzat-hozzárendelés neve | Adja meg a szabályzat-hozzárendelés neve. A szabályzat definíciója megjelenített is használhatja, ha azt szeretné. Ha például **felügyelt lemezeket nem használó virtuális gépek naplózása**. |
   | Rg neve | Adja meg, ahol szeretné a szabályzat hozzárendelése egy erőforráscsoport-nevet. Ez a rövid útmutatóban használja az alapértelmezett értéket **[resourceGroup () .név]**. **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)**  egy sablon-függvény, amely lekéri az erőforráscsoportot. |
   | Szabályzatdefiníció azonosítója | Specify **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Elfogadom a fenti feltételeket és kikötéseket | (Válasszon) |

1. Válassza a **Beszerzés** lehetőséget.

Néhány további erőforrást:

- További minták sablonok talál [Azure gyorsindítási sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Tekintse meg a tárfióksablonok referenciáját, lépjen a [Azure sablonreferenciája](/azure/templates/microsoft.authorization/allversions).
- Megtudhatja, hogyan fejleszthet Resource Manager-sablonokat, lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).
- Előfizetés-szintű üzembe helyezési kapcsolatban lásd: [erőforráscsoport és erőforrások létrehozásához az előfizetés szintjén](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

Válassza ki **megfelelőségi** az oldal bal oldalán található. Keresse meg a **felügyelt lemezeket nem használó virtuális gépek naplózása** létrehozott szabályzat-hozzárendelést.

![Házirendek megfelelőség – áttekintés oldalra](./media/assign-policy-template/policy-compliance.png)

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, azok jelennek meg **nem megfelelő erőforrások**.

További információkért lásd: [megfelelőségi működése](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelést eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a **felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést, és válassza ki **hozzárendelés törlése**.

   ![Hozzárendelés törlése a megfelelőség áttekintése lapon](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy beépített szabályzatdefiníciót a hatókör, és az eszközmegfelelőségi jelentés értékeli ki. A szabályzatdefiníció ellenőrzi, hogy megfelelő a hatókörben lévő összes erőforrást, és azonosítja a nem.

További információ a szabályzatok ellenőrzése, hogy az új erőforrások megfelelnek hozzárendeléséről, folytassa a következő oktatóanyagban:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)