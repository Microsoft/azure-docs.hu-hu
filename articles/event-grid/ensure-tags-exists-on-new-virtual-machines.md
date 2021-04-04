---
title: Az Azure Automation integrálása az Event Griddel | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá automatikusan címkéket az új virtuális gépek létrehozásakor, valamint küldhet értesítést a Microsoft Teamsbe.
keywords: automation, runbook, teams, event grid, virtuális gép, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 3b9b49a4d38566891f442a3d2d7eac9bf1d36465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87462003"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Oktatóanyag: Azure Automation integrálása a Event Grid és a Microsoft Teams szolgáltatással

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Event Grid-runbookminta importálása.
> * Opcionális Microsoft Teams-webhook létrehozása.
> * Webhook létrehozása a runbookhoz.
> * Event Grid-előfizetés létrehozása.
> * A runbookot aktiváló virtuális gép létrehozása.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Az oktatóanyag elvégzéséhez egy [Azure Automation-fiók](../automation/index.yml) szükséges a runbook tárolásához, amelyet az Azure Event Grid-előfizetésből fogunk aktiválni.

* Az `AzureRM.Tags` modult be kell tölteni az Automation-fiókba. A modulok az Azure Automationbe való importálásával kapcsolatban lásd [a modulok az Azure Automationbe való importálását](../automation/automation-update-azure-modules.md) bemutató cikket.

## <a name="import-an-event-grid-sample-runbook"></a>Event Grid-runbookminta importálása

1. Válassza ki az Automation-fiókot, és lépjen a **Runbookok** lapra.

   ![Runbookok kiválasztása](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Kattintson a **Tallózás a katalógusban** gombra.

3. Keressen rá az **Event Grid** kifejezésre, és válassza **Az Azure Automation integrálása az Event Griddel** lehetőséget.

    ![Katalógusrunbook importálása](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Válassza az **Importálás** lehetőséget, és nevezze el a **Watch-VMWrite** néven.

5. Az importálás befejeztével válassza a **Szerkesztés** lehetőséget a runbook forrásának megtekintéséhez. 
6. Frissítse a (74) sort a parancsfájlban a `Tag` helyett `Tags` .

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Válassza ki a **Közzététel** gombot.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Opcionális Microsoft Teams-webhook létrehozása

1. A Microsoft Teamsben válassza a **További lehetőségek** elemet a csatorna neve mellett, majd válassza az **Összekötők** lehetőséget.

    ![Microsoft Teams-kapcsolatok](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Görgesse végig az összekötők listáját a **Bejövő webhook** elemig, és válassza a **Hozzáadás** lehetőséget.

3. Adja a webhooknak az **AzureAutomationIntegration** nevet, és válassza a **Létrehozás** lehetőséget.

4. Másolja a webhook URL-címét a vágólapra, és mentse. A rendszer a webhook URL-címének segítségével információkat küld a Microsoft Teamsnek.

5. A webhook mentéséhez válassza a **Kész** lehetőséget.

## <a name="create-a-webhook-for-the-runbook"></a>Webhook létrehozása a runbookhoz

1. Nyissa meg a Watch-VMWrite runbookot.

2. Válassza a **Webhookok** lehetőséget, és kattintson a **Webhook hozzáadása** gombra.

3. Adja a webhooknak a **WatchVMEventGrid** nevet. Másolja az URL-címet a vágólapra, és mentse.

    ![Webhook nevének konfigurálása](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Válassza a **Paraméterek és futtatási beállítások konfigurálása** lehetőséget, és adja meg a Microsoft Teams-webhook URL-címét a **CHANNELURL** mezőben. Hagyja a **WEBHOOKDATA** mezőt üresen.

    ![Webhook paramétereinek konfigurálása](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Az Automation-runbook webhookjának létrehozásához kattintson a **Létrehozás** gombra.

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

1. Az **Automation-fiók** áttekintő lapján válassza az **Event Grid** lehetőséget.

    ![Event Grid kiválasztása](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Kattintson a **+ Esemény-előfizetés** elemre.

3. Konfigurálja az előfizetést az alábbi információkkal:
    1. A **Témakörtípus** mezőben válassza az **Azure-előfizetések** lehetőséget.
    2. Törölje az **Előfizetés az összes eseménytípusra** jelölőnégyzet jelölését.
    3. Adja az előfizetésnek az **AzureAutomation** nevet.
    4. A **Definiált eseménytípusok** legördülő menüben az **Erőforrás írása sikeres** lehetőség kivételével törölje az összes többi jelölőnégyzet jelölését.

        > [!NOTE] 
        > Azure Resource Manager jelenleg nem tesz különbséget a létrehozás és a frissítés között, ezért ezt az oktatóanyagot az összes Microsoft. Resources. ResourceWriteSuccess az Azure-előfizetésében lévő események nagy mennyiségű hívást eredményezhetnek.
    1. A **Végpont típusa** mezőben válassza a **Webhook** lehetőséget.
    2. Kattintson a **Végpont kiválasztása** gombra. A megnyíló **Webhook kiválasztása** lapon illessze be a Watch-VMWrite runbookhoz létrehozott webhook URL-címét.
    3. A **SZŰRŐK** területen adja meg azt az előfizetést és az erőforráscsoportot, ahol az újonnan létrehozott virtuális gépeket figyelni szeretné. Ennek így kell kinéznie: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Kattintson a **Létrehozás** gombra az Event Grid-előfizetés mentéséhez.

## <a name="create-a-vm-that-triggers-the-runbook"></a>A runbookot aktiváló virtuális gép létrehozása

1. Hozzon létre egy új virtuális gépet az Event Grid-előfizetés előtagszűrőjében megadott erőforráscsoportban.

2. A Watch-VMWrite runbookot meg kell hívni, és egy új címkét hozzá kell adni a virtuális géphez.

    ![Virtuális gép címkéje](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. A rendszer egy új üzenetet küld a Microsoft Teams-csatornára.

    ![Microsoft Teams-értesítés](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Event Grid és az Automation közötti integrációt állította be. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Event Grid-runbookminta importálása.
> * Opcionális Microsoft Teams-webhook létrehozása.
> * Webhook létrehozása a runbookhoz.
> * Event Grid-előfizetés létrehozása.
> * A runbookot aktiváló virtuális gép létrehozása.

> [!div class="nextstepaction"]
> [Egyéni események létrehozása és átirányítása az Event Griddel](../event-grid/custom-event-quickstart.md)
