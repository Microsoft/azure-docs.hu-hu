---
title: Szöveges runbookok szerkesztése a Azure Automation
description: Ez a cikk bemutatja, hogyan használhatja a Azure Automation szövegszerkesztőt PowerShell- és PowerShell-munkafolyamat-runbookok használatára.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 296d45fae4d59553b54a1b68923c91be4168d3a5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829385"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Szöveges runbookok szerkesztése a Azure Automation

A [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks) és a [PowerShell Azure Automation-munkafolyamat-runbookok](automation-runbook-types.md#powershell-workflow-runbooks)szerkesztéséhez használhatja az alkalmazás szövegszerkesztőjét. Ez a szerkesztő más kódszerkesztők, például az IntelliSense tipikus funkcióit is tartalmazza. Színkódolást és további speciális funkciókat is használ a runbookok közös erőforrásainak eléréséhez. 

A szöveges szerkesztő tartalmaz egy funkciót, amely kódot szúr be a parancsmagok, az eszközök és a gyermek runbookok számára egy runbookba. Ahelyett, hogy saját maga gépelje be a kódot, választhat az elérhető erőforrások listájából, és a szerkesztő beszúrja a megfelelő kódot a runbookba.

A Azure Automation runbook két verzióval rendelkezik: Draft és Published. Szerkessze a runbook Vázlat verzióját, majd tegye közzé, hogy végrehajtható legyen. A Közzétett verzió nem szerkeszthető. További információ: [Runbook közzététele.](manage-runbooks.md#publish-a-runbook)

Ez a cikk a különböző függvények szerkesztővel való végrehajtásához nyújt részletes lépéseket. Ezek a grafikus [runbookok esetén nem alkalmazhatók.](automation-runbook-types.md#graphical-runbooks) A runbookok használatával kapcsolatban tekintse meg a grafikus tartalom [a](automation-graphical-authoring-intro.md)Azure Automation.

## <a name="edit-a-runbook-with-the-azure-portal"></a>Runbook szerkesztése a Azure Portal

1. A Azure Portal válassza ki Automation-fiókját.
2. A **FOLYAMATAUTOMATIZÁLÁS alatt** **válassza a Runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Válassza ki a szerkeszteni szeretné runbookot, majd kattintson az **Edit (Szerkesztés) gombra.**
4. Szerkessze a runbookot.
5. Ha **a módosítások befejeződtek,** kattintson a Mentés gombra.
6. Kattintson **a Közzététel** gombra, ha közzé szeretné tenni a runbook legújabb piszkozatverzióját.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Parancsmag beszúrása runbookba

1. A szöveges szerkesztő vásznán helyezze a kurzort arra a helyre, ahová a parancsmagot el szeretné helyezze.
2. **Bontsa ki a Parancsmagok csomópontot** a Könyvtár vezérlőben.
3. Bontsa ki a használni használt parancsmagot tartalmazó modult.
4. Kattintson a jobb gombbal a beszúrni kívánt parancsmag nevére, majd válassza a **Hozzáadás a vászonhoz lehetőséget.** Ha a parancsmag több paraméterkészletet is rendelkezik, a szerkesztő hozzáadja az alapértelmezett készletet. Másik paraméterkészlet kiválasztásához a parancsmagot is kibonthatja.
5. Vegye figyelembe, hogy a parancsmag kódja a paraméterek teljes listájával együtt van beszúrva.
6. Adjon meg egy megfelelő értéket a szögletes zárójelek (<>) által körülvett érték helyett a szükséges paraméterekhez. Távolítsa el a nem szükséges paramétereket.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Gyermek runbook kódának beszúrása runbookba

1. A szöveges szerkesztő vásznán helyezze a kurzort arra a helyre, ahová a gyermek runbook kódját [szeretné.](automation-child-runbooks.md)
2. **Bontsa ki a Runbookok csomópontot** a Könyvtár vezérlőben.
3. Kattintson a jobb gombbal a beszúrni kívánt runbookra, majd válassza **a Hozzáadás a vászonhoz lehetőséget.**
4. A rendszer beszúrja a gyermek runbook kódját a Runbook-paraméterek helyőrzőivel.
5. Cserélje le a helyőrzőket az egyes paraméterek megfelelő értékeire.

### <a name="insert-an-asset-into-a-runbook"></a>Eszköz beszúrása runbookba

1. A szöveges szerkesztő Vászon vezérlőelemében helyezze a kurzort arra a helyre, ahová a gyermek runbook kódját el szeretné irányítani.
2. Bontsa **ki az Assets (Eszközök)** csomópontot a Könyvtár vezérlőben.
3. Bontsa ki a kívánt eszköztípus csomópontját.
4. Kattintson a jobb gombbal a beszúrni kívánt eszköz nevére, majd **válassza a Hozzáadás a vászonhoz lehetőséget.** A [változóeszközök esetében](./shared-resources/variables.md)válassza a **"Változó leése"** hozzáadása a vászonhoz vagy a **"Változó beállítása"** lehetőséget a vászonhoz attól függően, hogy le szeretné-e kapni vagy be szeretné állítani a változót.
5. Vegye figyelembe, hogy az eszköz kódja be lesz szúrva a runbookba.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Runbook Azure Automation szerkesztése a Windows PowerShell

Ha egy runbookot úgy Windows PowerShell, használja a választott szerkesztőt, és mentse a runbookot **egy .ps1 fájlba.** Az [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) parancsmag használatával lekérheti a runbook tartalmát. Az  [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) parancsmaggal lecserélheti a meglévő runbook piszkozatát a módosítottra.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának lekérése a Windows PowerShell

A következő mintaparancsok bemutatják, hogyan kérhető le egy runbook parancsfájlja és menthető egy parancsfájlba. Ez a példa a vázlatverzió lekérését mutatja be. A runbook Közzétett verzióját is lekérheti, bár ez a verzió nem módosítható.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának módosítása a Windows PowerShell

Az alábbi mintaparancsok azt mutatják be, hogyan cserélheti le egy runbook meglévő tartalmát egy szkriptfájl tartalmára. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

* [Runbookok kezelése a Azure Automation.](manage-runbooks.md)
* [A PowerShell-munkafolyamat elsajátítása.](automation-powershell-workflow.md)
* [Grafikus tartalom a Azure Automation.](automation-graphical-authoring-intro.md)
* [Tanúsítványok.](./shared-resources/certificates.md)
* [Kapcsolatok:](automation-connections.md).
* [Azonosító adatok](./shared-resources/credentials.md).
* [Ütemezi a következőt:](./shared-resources/schedules.md).
* [Változók:](./shared-resources/variables.md).
* [PowerShell-parancsmagok referenciája.](/powershell/module/az.automation)
