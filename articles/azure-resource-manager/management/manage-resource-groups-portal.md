---
title: Erőforráscsoportok kezelése – Azure Portal
description: Az erőforráscsoportok Azure Resource Manager használatával történő kezeléséhez használja a Azure Portal. Megjeleníti az erőforráscsoportok létrehozását, listázását és törlését.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093687"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure Resource Manager erőforráscsoportok kezelése a Azure Portal használatával

Ismerje meg, hogyan kezelheti az Azure-erőforráscsoportokat a [Azure Portal](https://portal.azure.com) és a [Azure Resource Manager](overview.md) használatával. Az Azure-erőforrások kezelésével kapcsolatban lásd: [Az Azure-erőforrások kezelése a Azure Portal használatával](manage-resources-portal.md).

Az erőforráscsoportok kezelésével kapcsolatos további cikkek:

- [Azure-erőforráscsoportok kezelése az Azure CLI használatával](manage-resources-cli.md)
- [Azure-erőforráscsoportok kezelése Azure PowerShell használatával](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport?

Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában olyan erőforrásokat adjon hozzá, amelyek ugyanazt az életciklust használják ugyanahhoz az erőforráscsoporthoz, így egyszerűen üzembe helyezheti, frissítheti és törölheti őket csoportként.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.


## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. **Erőforráscsoportok** kiválasztása

    ![erőforráscsoport hozzáadása](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Válassza a **Hozzáadás** lehetőséget.
4. Írja be a következő értékeket:

   - **Előfizetés**: Válassza ki az Azure-előfizetést. 
   - **Erőforráscsoport**: adjon meg egy új erőforráscsoport-nevet. 
   - **Régió**: válasszon ki egy Azure-helyet, például az **USA középső** régióját.

     ![erőforráscsoport létrehozása](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Válassza a **felülvizsgálat + létrehozás** lehetőséget
6. Válassza a **Létrehozás** lehetőséget. Egy erőforráscsoport létrehozása eltarthat néhány másodpercig.
7. A felső menüben válassza a **frissítés** lehetőséget az erőforráscsoport listájának frissítéséhez, majd válassza ki az újonnan létrehozott erőforráscsoportot a megnyitásához. Vagy válassza a felül az **értesítés**(harang ikon) lehetőséget, majd válassza az **Ugrás az erőforrás-csoportba** lehetőséget az újonnan létrehozott erőforráscsoport megnyitásához.

    ![erőforrás-csoport megugrása](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az erőforráscsoportok listázásához válassza az **erőforráscsoportok** lehetőséget.

    ![erőforráscsoportok tallózása](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Az erőforráscsoportok számára megjelenített információk testreszabásához válassza az **Oszlopok szerkesztése** lehetőséget. A következő képernyőfelvételen a megjeleníthető hozzáadási oszlopok láthatók:

## <a name="open-resource-groups"></a>Erőforráscsoportok megnyitása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a megnyitni kívánt erőforráscsoportot.

## <a name="delete-resource-groups"></a>Erőforráscsoportok törlése

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [erőforráscsoportok megnyitása](#open-resource-groups).
2. Válassza az **Erőforráscsoport törlése** elemet.

    ![Azure-erőforráscsoport törlése](./media/manage-resource-groups-portal/delete-group.png)

További információ arról, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését: [Azure Resource Manager erőforráscsoport törlése](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése erőforráscsoporthoz

A Resource Manager-sablonok létrehozása után a Azure Portal használatával üzembe helyezheti Azure-erőforrásait. Sablon létrehozásához tekintse meg [a rövid útmutató: Azure Resource Manager sablonok létrehozása és telepítése a Azure Portal használatával](../templates/quickstart-create-templates-use-the-portal.md)című témakört. Ha a portál használatával helyez üzembe egy sablont, tekintse meg az [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure Portalokkal](../templates/deploy-portal.md)című témakört.

## <a name="move-to-another-resource-group-or-subscription"></a>Áthelyezés másik erőforráscsoporthoz vagy előfizetésbe

A csoportban található erőforrásokat áthelyezheti egy másik erőforráscsoporthoz. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Erőforráscsoportok zárolása

A zárolás megakadályozza a szervezet más felhasználói számára a kritikus erőforrások, például az Azure-előfizetés, az erőforráscsoport vagy az erőforrás véletlen törlését vagy módosítását. 

1. Nyissa meg a zárolni kívánt erőforráscsoportot.  Lásd: [erőforráscsoportok megnyitása](#open-resource-groups).
2. A bal oldali ablaktáblán válassza a **zárolások** lehetőséget.
3. Ha zárolást szeretne hozzáadni az erőforráscsoporthoz, válassza a **Hozzáadás** lehetőséget.
4. Adja meg a **zárolás nevét**, a **zárolás típusát** és a **megjegyzéseket**. A zárolási típusok tartalmazzák az **írásvédett** és a **törlési** típust.

    ![Azure-erőforráscsoport zárolása](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

További információ: [erőforrások zárolása a váratlan változások megelőzése érdekében](lock-resources.md).

## <a name="tag-resource-groups"></a>Erőforráscsoportok címkézése

Címkéket alkalmazhat az erőforráscsoportok és az erőforrások számára, hogy logikailag szervezze az eszközöket. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokba

További információ a sablonok exportálásáról: [egy-és többerőforrásos exportálás sablonba – portál](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportok hozzáférésének kezelése

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) segítségével kezelheti az Azure-beli erőforrásokhoz való hozzáférést. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Következő lépések

- Azure Resource Manager megismeréséhez tekintse meg a [Azure Resource Manager áttekintése](overview.md)című témakört.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../templates/template-syntax.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](../index.yml).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).
