---
title: Azure rövid útmutató – Azure Automation-runbook létrehozása | Microsoft Docs
description: Ez a cikk segítséget nyújt a runbookok Azure Automation létrehozásában.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: 151199d8389130b85e917a09fd8e8d7b5f56d90c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536558"
---
# <a name="create-an-azure-automation-runbook"></a>Azure Automation-runbook létrehozása

Az Azure Automation-runbookok az Azure-on keresztül hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Automation-runbookok létrehozásához. Ez a rövid útmutató az Automation PowerShell-runbookok létrehozásán, szerkesztésén, tesztelésén és közzétételén vezeti át.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a következő https://portal.azure.com portálon: .

## <a name="create-the-runbook"></a>A runbook létrehozása

Először hozzunk létre egy runbookot. Az ebben a rövid útmutatóban létrehozott minta runbook alapértelmezett kimenete: `Hello World`.

1. Nyissa meg az Automation-fiókját.

1. Kattintson **a Folyamatok automatizálása** alatt a **Runbookok elemre.** Megjelenik a runbookok listája.

1. Kattintson **a runbook létrehozása** gombra a lista tetején.

1. A Név mezőben adja meg a nevet a runbook neveként, és a Forgatókönyv típusa mezőben válassza a `Hello-World` **PowerShell**  lehetőséget.  

   ![Adja meg az Automation-runbook információit az oldalon](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kattintson a **Létrehozás** lehetőségre. Létrejön a runbook, és megnyílik a PowerShell-runbook szerkesztése oldal.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="A PowerShell-runbook szerkesztése oldal képernyőképe.":::

1. Írja be vagy másolja és illessze be a következő kódot a szerkesztés panelre. Létrehoz egy nevű választható bemeneti paramétert az alapértelmezett értékkel, és egy sztringet `Name` `World` ad ki, amely ezt a bemeneti értéket használja:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kattintson **a Mentés** gombra a runbook vázlatos másolatának mentéshez.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="A PowerShell-runbook szerkesztése oldal képernyőképe a jobb oldali ablakban látható példakóddal.":::

## <a name="test-the-runbook"></a>A runbook tesztelése

A runbook létrehozása után tesztelnie kell a runbook működését.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

1. Írjon be egy értéket a **Név** mezőbe, és kattintson az **Indítás** gombra. Elindul a tesztfeladat, és megjelenik a feladat állapota és kimenete.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Képernyőkép a Teszt panelről, a név mezőben egy példaértékkel.":::

1. Zárja be a Teszt panelt a jobb felső **sarokban található X** gombra kattintva. A megjelenő felugró ablakban válassza az **OK** lehetőséget.

1. A PowerShell-runbook szerkesztése oldalon kattintson a **Közzététel** lehetőségre, hogy a runbookot a fiókban lévő runbook hivatalos verziójaként tegye közzé.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="A PowerShell-runbook szerkesztése oldal képernyőképe a kijelölt Közzététel gombbal.":::

## <a name="run-the-runbook"></a>A runbook futtatása

A runbook közzététele után megjelenik az áttekintő oldal.

1. A runbook áttekintése oldalon kattintson az **Indítás** gombra a runbook Runbook indítása konfigurációs oldalának megnyitásához.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Képernyőkép a Runbook kezdése konfigurációs lapról.":::

1. Hagyja üresen a **Név** mezőt, hogy az alapértelmezett értéket használja, és kattintson az **OK** gombra. A runbook-feladat el lesz elküldve, és megjelenik a Feladat lap.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Képernyőkép a Feladat lapról a kiválasztott Kimenet gombbal.":::

1. Ha a feladat állapota vagy , kattintson a Kimenet elemre a Kimenet panel megnyitásához és a `Running` `Completed` runbook kimenetének megtekintéséhez. 

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Képernyőkép a Runbook kimenetét megjelenítő Kimenet panelről.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, szerkesztett, tesztelt és közzétett egy runbookot, és elindított egy runbookfeladatot. Az Automation-runbookokkal kapcsolatos további információkért folytassa az Automationben létrehozható és használható különböző runbooktípusokkal foglalkozó cikkel.

> [!div class="nextstepaction"]
> [Azure Automation runbooktípusok](./automation-runbook-types.md)
