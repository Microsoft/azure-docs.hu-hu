---
title: Azure rövid útmutató – Azure Automation-runbook létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Automation runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987287"
---
# <a name="create-an-azure-automation-runbook"></a>Azure Automation-runbook létrehozása

Az Azure Automation-runbookok az Azure-on keresztül hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Automation-runbookok létrehozásához. Ez a rövid útmutató az Automation PowerShell-runbookok létrehozásán, szerkesztésén, tesztelésén és közzétételén vezeti át.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="create-the-runbook"></a>A runbook létrehozása

Először hozzunk létre egy runbookot. Az ebben a rövid útmutatóban létrehozott minta runbook alapértelmezett kimenete: `Hello World`.

1. Nyissa meg az Automation-fiókját.

1. Kattintson a **runbookok** elemre a **folyamat automatizálása** alatt. Megjelenik a runbookok listája.

1. Kattintson a **Runbook létrehozása** elemre a lista tetején.

1. Adja meg a `Hello-World` runbook nevét a **név** mezőben, majd válassza a **PowerShell** lehetőséget a **runbook típusa** mezőben. 

   ![Adja meg az Automation-runbook információit az oldalon](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kattintson a **Létrehozás** lehetőségre. Létrejön a runbook, és megnyílik a PowerShell-runbook szerkesztése oldal.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Képernyőkép a PowerShell-Runbook szerkesztése lapról.":::

1. Írja be vagy másolja és illessze be a következő kódot a szerkesztés panelre. Egy opcionális `Name` , alapértelmezett értékkel rendelkező bemeneti paramétert hoz létre `World` , amely a következő bemeneti értéket használó karakterláncot jeleníti meg:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kattintson a **Save (Mentés** ) gombra a runbook piszkozatának mentéséhez.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Képernyőkép a PowerShell-Runbook szerkesztése oldalon a jobb oldali ablakban található kóddal.":::

## <a name="test-the-runbook"></a>A runbook tesztelése

A runbook létrehozása után tesztelje a runbook, és ellenőrizze, hogy működik-e.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

1. Írjon be egy értéket a **Név** mezőbe, és kattintson az **Indítás** gombra. Elindul a tesztfeladat, és megjelenik a feladat állapota és kimenete.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Képernyőkép a teszt panelről, egy példa értékkel a név mezőben.":::

1. A jobb felső sarokban található **X** gombra kattintva zárjuk be a teszt panelt. A megjelenő felugró ablakban válassza az **OK** lehetőséget.

1. A PowerShell-runbook szerkesztése oldalon kattintson a **Közzététel** lehetőségre, hogy a runbookot a fiókban lévő runbook hivatalos verziójaként tegye közzé.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Képernyőkép a PowerShell-Runbook szerkesztése oldalról, amelyen a közzététel gomb van kiválasztva.":::

## <a name="run-the-runbook"></a>A runbook futtatása

A runbook közzététele után megjelenik az áttekintő oldal.

1. A runbook áttekintése oldalon kattintson az **Indítás** gombra a runbook Runbook indítása konfigurációs oldalának megnyitásához.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Képernyőkép a Runbook-konfiguráció elindítása oldalról.":::

1. Hagyja üresen a **Név** mezőt, hogy az alapértelmezett értéket használja, és kattintson az **OK** gombra. A rendszer elküldte a runbook-feladatot, és megjelenik a feladatok lap.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Képernyőkép a feladatok oldaláról, amely a kijelölt kimenet gombot mutatja.":::

1. Ha a feladatok állapota `Running` vagy `Completed` , kattintson a **kimenet** gombra a kimenet ablaktábla megnyitásához, és tekintse meg a runbook kimenetét.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Képernyőkép a kimenet panelről, amely a runbook kimenetét mutatja.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, szerkesztett, tesztelt és közzétett egy runbookot, és elindított egy runbookfeladatot. Az Automation-runbookokkal kapcsolatos további információkért folytassa az Automationben létrehozható és használható különböző runbooktípusokkal foglalkozó cikkel.

> [!div class="nextstepaction"]
> [Azure Automation runbook-típusok](./automation-runbook-types.md)
