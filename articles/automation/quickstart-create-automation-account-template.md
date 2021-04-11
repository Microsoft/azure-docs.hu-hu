---
title: 'Rövid útmutató: Automation-fiók létrehozása – Azure-sablon'
titleSuffix: Azure Automation
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Automation-fiókot a Azure Resource Manager sablon használatával.
services: automation
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: dda2e2e34ab1f0c8facafe62a4fc0b9c5116f9d9
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063168"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Rövid útmutató: Automation-fiók létrehozása ARM-sablon használatával

A Azure Automation felhőalapú automatizálási és konfigurációs szolgáltatást biztosít, amely támogatja az Azure-beli és nem Azure-beli környezetek egységes felügyeletét. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Automation-fiókot létrehozó Azure Resource Manager-sablont (ARM-sablont). Az ARM-sablonok használata kevesebb lépést vesz igénybe, mint a többi üzembe helyezési módszer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon a következőket hajtja végre:

* Automatizálja Azure Monitor Log Analytics munkaterület létrehozását.
* Automatizálja Azure Automation fiók létrehozását.
* Az Automation-fiók csatolása a Log Analytics munkaterülethez.
* A fiókhoz hozzáadja a minta Automation-runbookok.

>[!NOTE]
>ARM-sablon használata esetén nem támogatott az Automation futtató fiók létrehozása. Ha manuálisan szeretne létrehozni egy futtató fiókot a portálon vagy a PowerShell-lel, tekintse meg a [futtató fiók létrehozása](create-run-as-account.md)című témakört.

A lépések elvégzése után be kell állítania az Automation-fiókhoz tartozó [diagnosztikai beállításokat](automation-manage-send-joblogs-log-analytics.md) , hogy a runbook-feladatok állapotát és a feladatok adatfolyamait a csatolt log Analytics munkaterületre küldje.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-automation/) közül származik.

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API-verziók

A következő táblázat felsorolja az ebben a példában használt erőforrások API-verzióját.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| [Munkaterület](/azure/templates/microsoft.operationalinsights/workspaces) | munkaterületek | 2020-03-01 – előzetes verzió |
| [Automation-fiók](/azure/templates/microsoft.automation/automationaccounts) | automatizálás | 2020-01-13 – előzetes verzió |
| [Munkaterület-társított szolgáltatások](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | munkaterületek | 2020-03-01 – előzetes verzió |

### <a name="before-you-use-the-template"></a>A sablon használata előtt

A JSON-paraméterek sablonját a következő beállítással adhatja meg:

* A munkaterület neve.
* Az a régió, amelyben létre kívánja hozni a munkaterületet.
* Az Automation-fiók neve.
* Az a régió, amelybe az Automation-fiókot létre kell hozni.

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* az *SKU* alapértelmezett értéke az április 2018 díjszabási modellben megjelent GB-os díjszabási szinten.
* a *dataRetention* alapértelmezett értéke 30 nap.

>[!WARNING]
>Ha egy Log Analytics munkaterületet szeretne létrehozni vagy konfigurálni egy olyan előfizetésben, amely az áprilisi 2018 díjszabási modellre van kiválasztva, akkor az egyetlen érvényes Log Analytics díjszabási csomag *PerGB2018*.
>

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amely valószínűleg a környezetben megszokott konfigurációként lesz használva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-vel](../azure-resource-manager/templates/deploy-cli.md).

Ha még nem Azure Automation és Azure Monitor, fontos, hogy megértse a következő konfigurációs adatokat. Az új Automation-fiókhoz kapcsolódó Log Analytics munkaterületek létrehozásakor, konfigurálásakor és használatakor elkerülheti a hibák elhárítását.

* [További részletekért](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) tekintse át a munkaterület-konfigurációs beállításokat, például a hozzáférés-vezérlési módot, a díjszabási szintet, a megőrzést és a kapacitás foglalási szintjét.

* Tekintse át a [munkaterület-leképezéseket](how-to/region-mappings.md) a támogatott régiók beágyazott vagy egy paraméterérték megadásához. Egy Log Analytics munkaterület és egy Automation-fiók összekapcsolása csak bizonyos régiókban támogatott az előfizetésben.

* Ha még nem Azure Monitor naplókat, és már nem telepített munkaterületet, tekintse át a [munkaterület kialakítására vonatkozó útmutatást](../azure-monitor/logs/design-logs-deployment.md). Segít megismerni a hozzáférés-vezérlést, és megismerheti a szervezete számára ajánlott kialakítási stratégiákat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Automation fiókot, egy Log Analytics munkaterületet, és összeköti az Automation-fiókot a munkaterülettel.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Adja meg az értékeket.

3. Az üzembe helyezés eltarthat néhány percig. Ha elkészült, a kimenet a következőhöz hasonló:

    ![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal nyissa meg az imént létrehozott Automation-fiókot. 

3. A bal oldali panelen válassza a **runbookok** lehetőséget. A **runbookok** lapon a lista három oktatóanyag-runbookok hozott létre az Automation-fiókkal.

    ![Az Automation-fiókkal létrehozott runbookok oktatóanyag](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. A bal oldali panelen válassza a **csatolt munkaterület** lehetőséget. A **csatolt munkaterület** lapon megjelenik az Automation-fiókhoz korábban megadott log Analytics munkaterület.

    ![Az Log Analytics munkaterülethez csatolt Automation-fiók](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, válassza le az Automation-fiókot a Log Analytics munkaterületről, majd törölje az Automation-fiókot és-munkaterületet.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Automation-fiókot, egy Log Analytics munkaterületet, és összekapcsolta őket egymással.

További információért folytassa a Azure Automation oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Automation](learn/automation-tutorial-runbook-graphical.md)