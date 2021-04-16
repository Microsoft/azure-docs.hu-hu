---
title: 'Rövid útmutató: Automation-fiók létrehozása – Azure-sablon'
titleSuffix: Azure Automation
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Automation-fiókot a Azure Resource Manager használatával.
services: automation
ms.author: magoedte
ms.date: 01/07/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 50a8c057a6fdf6350a18225cd2dc538942d6686d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538404"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Rövid útmutató: Automation-fiók létrehozása ARM-sablonnal

Azure Automation felhőalapú automatizálási és konfigurációs szolgáltatást nyújt, amely támogatja az Azure- és nem Azure-környezetek egységes felügyeletét. Ez a rövid útmutató bemutatja, hogyan helyezhet Azure Resource Manager Automation-fiókot létrehozó sablonsablont (ARM-sablont). Az ARM-sablon használata kevesebb lépést vesz igénybe, mint más üzembe helyezési módszerek.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Ez a mintasablon a következőket végzi el:

* Automatizálja egy Azure Monitor Log Analytics-munkaterületen.
* Automatizálja a Azure Automation létrehozását.
* Az Automation-fiókot a Log Analytics-munkaterülethez kapcsolódik.
* Automation-minta runbookokat ad hozzá a fiókhoz.

>[!NOTE]
>ARM-sablon használata esetén az Automation-run As-fiók létrehozása nem támogatott. Ha manuálisan, a portálról vagy a PowerShell-héjból is létre kell hoznia egy futó fiókot, tekintse meg a következőt: Create Run As account (Futtatás [fiók létrehozása).](create-run-as-account.md)

A lépések befejezése után konfigurálnia kell az Automation-fiók diagnosztikai beállításait, hogy runbook-feladat állapotát és feladatstreameit a csatolt Log Analytics-munkaterületre küldjék. [](automation-manage-send-joblogs-log-analytics.md)

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-automation/) közül származik.

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API-verziók

Az alábbi táblázat a példában használt erőforrások API-verzióját sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| [Munkaterület](/azure/templates/microsoft.operationalinsights/workspaces) | munkaterületek | 2020. 03. 01. előzetes verzió |
| [Automation-fiók](/azure/templates/microsoft.automation/automationaccounts) | automatizálás | 2020. 01. 13. előzetes verzió |
| [Munkaterülethez kapcsolódó szolgáltatások](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | munkaterületek | 2020. 03. 01. előzetes verzió |

### <a name="before-you-use-the-template"></a>A sablon használata előtt

A JSON-paramétersablon a következő beállítások megadására van konfigurálva:

* A munkaterület neve.
* A régió, ahol a munkaterületet létre kell hozni.
* Az Automation-fiók neve.
* Az a régió, ahol létre kell hoznia az Automation-fiókot.

A sablonban a következő paraméterek vannak beállítva a Log Analytics-munkaterület alapértelmezett értékével:

* *A termékváltozat* alapértelmezés szerint a 2018. áprilisi díjszabási modellben kiadott, GB-bankénti tarifacsomagot használja.
* *dataRetention (adatretention)* – alapértelmezés szerint 30 nap.

>[!WARNING]
>Ha olyan előfizetésben szeretne Log Analytics-munkaterületet létrehozni vagy konfigurálni, amely a 2018. áprilisi díjszabási modellt választotta, az egyetlen érvényes Log Analytics-tarifacsomag a *PerGB2018.*
>

A JSON-sablon alapértelmezett értéket ad meg a többi paraméterhez, amelyek valószínűleg szabványos konfigurációként lesznek használva a környezetben. A sablont egy Azure-tárfiókban tárolhatja a szervezeten belül megosztott hozzáféréshez. A sablonok használatával kapcsolatos további információkért lásd: Erőforrások üzembe helyezése [ARM-sablonokkal és az Azure CLI-val.](../azure-resource-manager/templates/deploy-cli.md)

Ha még csak most Azure Automation és Azure Monitor, fontos, hogy tisztában van az alábbi konfigurációs részletekkel. Segíthetnek elkerülni a hibákat, amikor az új Automation-fiókhoz kapcsolt Log Analytics-munkaterületet próbál létrehozni, konfigurálni és használni.

* További [részletek áttekintésével](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) teljes mértékben megértheti a munkaterület konfigurációs beállításait, például a hozzáférés-vezérlési módot, a tarifacsomagot, a megőrzést és a kapacitás foglalási szintjét.

* Tekintse [át a munkaterület-leképezéseket](how-to/region-mappings.md) a támogatott régiók beágyazott vagy paraméterfájlban való megadásához. Log Analytics-munkaterület és Automation-fiók előfizetésben való összekapcsolása csak bizonyos régiók esetében támogatott.

* Ha még nem Azure Monitor, és még nem helyezett üzembe munkaterületet, tekintse át a munkaterület tervezési [útmutatóját.](../azure-monitor/logs/design-logs-deployment.md) Segítségével megismerheti a hozzáférés-vezérlést, és megismerheti a szervezet számára ajánlott tervezési megvalósítási stratégiákat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Automation egy Log Analytics-munkaterületet, és a munkaterülethez összeköti az Automation-fiókot.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Adja meg az értékeket.

3. Az üzembe helyezés eltarthat néhány percig. Ha elkészült, a kimenet az alábbihoz hasonló:

    ![Példa eredmény az üzembe helyezés befejezésekor](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal nyissa meg az előbb létrehozott Automation-fiókot. 

3. A bal oldali panelen válassza a **Runbooks (Runbookok) lehetőséget.** A **Runbookok lapon három,** az Automation-fiókkal létrehozott oktatóanyag-runbook látható.

    ![Automation-fiókkal létrehozott oktatóanyag-runbookok](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. A bal oldali panelen válassza a Csatolt **munkaterület lehetőséget.** A Csatolt **munkaterület oldalon** a korábban megadott Log Analytics-munkaterület jelenik meg, az Automation-fiókhoz csatolva.

    ![A Log Analytics-munkaterülethez kapcsolt Automation-fiók](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, válassza le az Automation-fiókot a Log Analytics-munkaterületről, majd törölje az Automation-fiókot és -munkaterületet.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Automation-fiókot és egy Log Analytics-munkaterületet, és összekapcsolta őket.

További tudnivalókért folytassa az oktatóanyagokkal és Azure Automation.

> [!div class="nextstepaction"]
> [Azure Automation oktatóanyagok](learn/automation-tutorial-runbook-graphical.md)
