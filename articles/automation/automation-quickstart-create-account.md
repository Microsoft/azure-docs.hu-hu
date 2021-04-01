---
title: Azure rövid útmutató– Azure Automation-fiók létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Automation fiókot, és hogyan futtathatja a runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83836702"
---
# <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

Létrehozhat egy Azure Automation fiókot az Azure-on keresztül, a Azure Portal használatával egy böngészőalapú felhasználói felülettel, amely lehetővé teszi számos erőforrás elérését. Egy Automation-fiók az adott bérlő összes régiójában és előfizetésében kezelheti az erőforrásokat. 

Ez a rövid útmutató végigvezeti egy Automation-fiók létrehozásán és egy runbook a fiókban való futtatásának lépésein. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[Bejelentkezés az Azure-ba](https://portal.azure.com).

## <a name="create-automation-account"></a>Automation-fiók létrehozása

1. Válassza ki az Azure-fiókja nevét. Az Automation-fiókok nevei régiónként és erőforráscsoporthoz egyediek. Előfordulhat, hogy a törölt Automation-fiókok neve nem érhető el azonnal.

    > [!NOTE]
    > A fiók neve nem módosítható, ha meg lett adva a felhasználói felületen. 

2. Kattintson a Azure Portal bal felső sarkában található **erőforrás létrehozása** gombra.

3. Válassza ki **& felügyeleti eszközöket**, majd válassza az **Automation** elemet.

4. Adja meg a fiók adatait, beleértve a kiválasztott fióknevet is. Az **Azure-beli futtató fiók létrehozása** területen válassza az **Igen** lehetőséget az Azure-beli hitelesítést leegyszerűsítő összetevők automatikus engedélyezéséhez. Ha az információk befejeződik, kattintson a **Létrehozás** gombra az Automation-fiók központi telepítésének elindításához.

    ![Adja meg az Automation-fiók információit az oldalon](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Az Automation-fiókkal üzembe helyezhető telephelyek frissített listáját a [régiók által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)című szakaszban tekintheti meg.

5. Ha a telepítés befejeződött, kattintson a **minden szolgáltatás** elemre.

6. Válassza az **Automation-fiókok** lehetőséget, majd válassza ki a létrehozott Automation-fiókot.

    ![Az Automation-fiókok áttekintése](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook futtatása

Futtassa az oktatóanyag egyik runbookját.

1. Kattintson a **runbookok** elemre a **folyamat automatizálása** alatt. Megjelenik a runbookok listája. Alapértelmezés szerint több oktatóanyag-runbookok is engedélyezve van a fiókban.

    ![Automation-fiók runbookok listája](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Válassza ki az **AzureAutomationTutorialScript** runbookot. Ezzel megnyílik a runbook áttekintési oldala.

    ![A runbook áttekintése](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Kattintson az **Indítás** gombra, és a Runbook indítása oldalon kattintson az **OK** gombra a runbook elindításához.

    ![Runbookfeladat oldal](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. A feladatok állapotának `Running` elvégzése után kattintson a **kimenet** vagy **az összes napló** lehetőségre a runbook-feladatok kimenetének megtekintéséhez. A jelen oktatóanyag runbookjának kimenete az Azure-erőforrások listája.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Automation-fiókot, elindított egy runbook feladatot, és megtekintette a feladat részleteit. Ha bővebb információra van szüksége az Azure Automationnel kapcsolatban, folytassa az első runbook létrehozásával foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Automation rövid útmutató – Azure Automation runbook létrehozása](./automation-quickstart-create-runbook.md)

