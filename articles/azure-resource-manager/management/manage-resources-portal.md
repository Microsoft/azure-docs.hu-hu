---
title: Erőforrások kezelése – Azure Portal
description: Az erőforrások kezeléséhez használja a Azure Portal és a Azure Resource Manager. Az erőforrások üzembe helyezésének és törlésének módját mutatja be.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 12d704b0a3d92aa0585fb120f969000def282396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092200"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure-erőforrások kezelése a Azure Portal használatával

Ismerje meg, hogyan kezelheti az Azure-erőforrásokat a [Azure Portal](https://portal.azure.com) és a [Azure Resource Manager](overview.md) használatával. Az erőforráscsoportok kezelésével kapcsolatban lásd: [Az Azure-erőforráscsoportok kezelése a Azure Portal használatával](manage-resource-groups-portal.md).

További cikkek az erőforrások kezelésével kapcsolatban:

- [Azure-erőforrások kezelése az Azure CLI használatával](manage-resources-cli.md)
- [Azure-erőforrások kezelése Azure PowerShell használatával](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése erőforráscsoporthoz

A Resource Manager-sablonok létrehozása után a Azure Portal használatával üzembe helyezheti Azure-erőforrásait. Sablon létrehozásához tekintse meg [a rövid útmutató: Azure Resource Manager sablonok létrehozása és telepítése a Azure Portal használatával](../templates/quickstart-create-templates-use-the-portal.md)című témakört. Ha a portál használatával helyez üzembe egy sablont, tekintse meg az [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure Portalokkal](../templates/deploy-portal.md)című témakört.

## <a name="open-resources"></a>Erőforrások megnyitása

Az Azure-erőforrások az Azure-szolgáltatások és az erőforráscsoportok szerint vannak rendszerezve. Az alábbi eljárások azt mutatják be, hogyan nyitható meg a **mystorage0207** nevű Storage-fiók. A virtuális gép egy **mystorage0207rg** nevű erőforráscsoporthoz tartozik.

Erőforrás megnyitása a szolgáltatás típusa szerint:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali ablaktáblán válassza ki az Azure-szolgáltatást. Ebben az esetben a **Storage-fiókok**.  Ha nem látja a felsorolt szolgáltatást, válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a szolgáltatás típusát.

    ![Az Azure-erőforrás megnyitása a portálon](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Válassza ki a megnyitni kívánt erőforrást.

    ![A kiválasztott erőforrást kiemelő képernyőkép.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    A Storage-fiók A következőképpen néz ki:

    ![A Storage-fiók megjelenését bemutató képernyőkép.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Erőforrás megnyitása erőforráscsoport szerint:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali ablaktáblában válassza az **erőforráscsoportok** lehetőséget a csoporton belüli erőforrás listázásához.
3. Válassza ki a megnyitni kívánt erőforrást. 

## <a name="manage-resources"></a>Erőforrások kezelése

Amikor megtekint egy erőforrást a portálon, megtekintheti az adott erőforrás kezelésének lehetőségeit.

![Azure-erőforrások kezelése](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A képernyőképen az Azure-beli virtuális gépek felügyeleti lehetőségei láthatók. Olyan műveleteket is végrehajthat, mint például a virtuális gépek indítása, újraindítása és leállítása.

## <a name="delete-resources"></a>Erőforrások törlése

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [erőforrások megnyitása](#open-resources).
2. Válassza a **Törlés** elemet. Az alábbi képernyőfelvételen egy virtuális gép felügyeleti lehetőségei láthatók.

    ![Azure-erőforrás törlése](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Írja be az erőforrás nevét a törlés megerősítéséhez, majd válassza a **Törlés** lehetőséget.

További információ arról, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését: [Azure Resource Manager erőforráscsoport törlése](delete-resource-group.md).

## <a name="move-resources"></a>Erőforrások áthelyezése

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [erőforrások megnyitása](#open-resources).
2. Válassza az **Áthelyezés** lehetőséget. Az alábbi képernyőfelvételen egy Storage-fiók felügyeleti lehetőségei láthatók.

    ![Azure-erőforrás áthelyezése](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Válassza az **Áthelyezés másik erőforráscsoporthoz** lehetőséget, vagy **váltson egy másik előfizetésre** az igényeitől függően.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Erőforrások zárolása

A zárolás megakadályozza a szervezet más felhasználói számára a kritikus erőforrások, például az Azure-előfizetés, az erőforráscsoport vagy az erőforrás véletlen törlését vagy módosítását. 

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [erőforrások megnyitása](#open-resources).
2. Válassza a **zárolások** lehetőséget. Az alábbi képernyőfelvételen egy Storage-fiók felügyeleti lehetőségei láthatók.

    ![Azure-erőforrás zárolása](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Válassza a **Hozzáadás** lehetőséget, majd adja meg a zárolási tulajdonságokat.

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

A címkézés segítségével logikailag rendszerezheti az erőforráscsoportot és az erőforrásokat. 

1. Nyissa meg az erőforrást a portálon. A lépésekért lásd: [erőforrások megnyitása](#open-resources).
2. Válassza a **Címkék** lehetőséget. Az alábbi képernyőfelvételen egy Storage-fiók felügyeleti lehetőségei láthatók.

    ![Azure-erőforrás címkézése](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Adja meg a címke tulajdonságait, majd kattintson a **Mentés** gombra.

További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md#portal).

## <a name="monitor-resources"></a>Erőforrások megfigyelése

Amikor megnyit egy erőforrást, a portál alapértelmezett gráfokat és táblázatokat jelenít meg az erőforrástípus figyeléséhez. A következő képernyőképen egy virtuális gép diagramjai láthatók:

![Azure-erőforrás figyelése](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

A diagram jobb felső sarkában található rögzítés ikont választva rögzítheti a diagramot az irányítópulton. További információ az irányítópultok használatáról: [irányítópultok létrehozása és megosztása a Azure Portalban](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Erőforrásokhoz való hozzáférés kezelése

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) segítségével kezelheti az Azure-beli erőforrásokhoz való hozzáférést. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Következő lépések

- Azure Resource Manager megismeréséhez tekintse meg a [Azure Resource Manager áttekintése](overview.md)című témakört.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../templates/template-syntax.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](../index.yml).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).
