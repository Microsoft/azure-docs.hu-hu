---
title: Szöveges runbookok szerkesztése Azure Automation
description: Ez a cikk azt ismerteti, hogyan használható a Azure Automation szöveges szerkesztő a PowerShell és a PowerShell munkafolyamat-runbookok való együttműködéshez.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d6b786ffaf309e147de27e8cd8be314a3d8a5fb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896986"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Szöveges runbookok szerkesztése Azure Automation

A Azure Automation szöveges szerkesztőjével szerkesztheti a PowerShell- [runbookok](automation-runbook-types.md#powershell-runbooks) és a [PowerShell-munkafolyamat runbookok](automation-runbook-types.md#powershell-workflow-runbooks). Ez a szerkesztő a más kód-szerkesztők, például az IntelliSense jellemző funkcióit tartalmazza. Emellett a színkódolást is használja további speciális funkciókkal, amelyek segítenek a runbookok-hez közös erőforrások elérésében. 

A szöveges szerkesztő tartalmaz egy funkciót, amely a parancsmagok, az eszközök és a gyermek runbookok kódját szúrja be egy runbook. A kód beírása helyett választhat az elérhető erőforrások listájából, és a szerkesztő beszúrja a megfelelő kódot a runbook.

Azure Automation minden runbook két verziója, piszkozata és közzététele van. Szerkessze a runbook Piszkozat verzióját, és tegye közzé azt, hogy végrehajtható legyen. A Közzétett verzió nem szerkeszthető. További információ: [Runbook közzététele](manage-runbooks.md#publish-a-runbook).

Ez a cikk részletesen ismerteti a különböző függvények a szerkesztővel való elvégzésének lépéseit. Ezek a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks)nem alkalmazhatók. Ezekkel a runbookok kapcsolatban lásd: [grafikus létrehozás Azure Automationban](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Runbook szerkesztése a Azure Portal

1. A Azure Portal válassza ki az Automation-fiókját.
2. A **folyamat automatizálása** területen válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Válassza ki a szerkeszteni kívánt runbook, majd kattintson a **Szerkesztés** gombra.
4. Szerkessze a runbook.
5. A Szerkesztés befejezéséhez kattintson a **Mentés** gombra.
6. Kattintson a **Közzététel** elemre, ha közzé szeretné tenni a runbook legújabb vázlatos verzióját.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Parancsmag beszúrása egy runbook

1. A szöveges szerkesztő vászonján vigye a kurzort arra a helyre, ahová a parancsmagot helyezni kívánja.
2. Bontsa ki a **parancsmagok** csomópontot a könyvtár vezérlőben.
3. Bontsa ki a használni kívánt parancsmagot tartalmazó modult.
4. Kattintson a jobb gombbal a parancsmag nevére a beszúráshoz, majd válassza a **Hozzáadás a vászonhoz** lehetőséget. Ha a parancsmag egynél több paraméterrel van beállítva, a szerkesztő hozzáadja az alapértelmezett készletet. A parancsmag kibontásával másik paramétert is kijelölhet.
5. Vegye figyelembe, hogy a parancsmag kódja a teljes paraméterekkel együtt van beszúrva.
6. Adja meg a megfelelő értéket a szögletes zárójelek (<>) körüli érték helyett a szükséges paraméterekhez. Távolítsa el a szükségtelen paramétereket.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Kód beszúrása egy runbook egy gyermek runbook

1. A szöveges szerkesztő vászonján vigye a kurzort arra a helyre, ahová a [gyermek runbook](automation-child-runbooks.md)kódot kívánja helyezni.
2. Bontsa ki a **runbookok** csomópontot a könyvtár vezérlőben.
3. Kattintson a jobb gombbal a beszúrandó runbook, és válassza a **Hozzáadás a vászonhoz** lehetőséget.
4. A gyermek runbook kódja minden runbook-paraméterhez be van beszúrva.
5. Cserélje le a helyőrzőket az egyes paraméterek megfelelő értékeivel.

### <a name="insert-an-asset-into-a-runbook"></a>Eszköz beszúrása egy runbook

1. A szöveges szerkesztő vászon vezérlőjében vigye a kurzort arra a helyre, ahová a gyermek runbook kódot kívánja helyezni.
2. Bontsa ki az **eszközök** csomópontot a könyvtár vezérlőben.
3. Bontsa ki a kívánt eszköz típusának csomópontját.
4. Kattintson a jobb gombbal a beszúrandó eszköz nevére, majd válassza a **Hozzáadás a vászonhoz** lehetőséget. [Változó eszközök](./shared-resources/variables.md)esetén válassza a **"változó beolvasása" lehetőséget a vászonhoz** , vagy **adja hozzá a "változó beállítása" beállítást a vászonhoz**, attól függően, hogy szeretné-e lekérni vagy beállítani a változót.
5. Vegye figyelembe, hogy az eszköz kódját a rendszer beszúrja a runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Azure Automation runbook szerkesztése a Windows PowerShell használatával

Ha a Windows PowerShell segítségével szeretne szerkeszteni egy runbook, használja a választott szerkesztőt, és mentse a runbook egy **. ps1** fájlba. A runbook tartalmának lekéréséhez az [export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) parancsmagot használhatja. Az  [import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) parancsmag használatával lecserélheti a meglévő Piszkozat runbook a módosítottra.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának beolvasása a Windows PowerShell használatával

A következő mintaparancsok bemutatják, hogyan kérhető le egy runbook parancsfájlja és menthető egy parancsfájlba. Ez a példa a vázlatverzió lekérését mutatja be. Lehetősége van a runbook közzétett verziójának lekérésére is, bár ez a verzió nem módosítható.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának módosítása a Windows PowerShell használatával

Az alábbi példák azt mutatják be, hogyan lehet lecserélni egy runbook meglévő tartalmát egy parancsfájl tartalmával. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

* [A Azure Automation Runbookok kezelése](manage-runbooks.md).
* [PowerShell-munkafolyamat betanítása](automation-powershell-workflow.md).
* [Grafikus szerzői műveletek Azure Automationban](automation-graphical-authoring-intro.md).
* [Tanúsítványok](./shared-resources/certificates.md).
* [Kapcsolatok](automation-connections.md).
* [Azonosító adatok](./shared-resources/credentials.md).
* [Ütemtervek](./shared-resources/schedules.md).
* [Változók](./shared-resources/variables.md).
* [PowerShell-parancsmagok leírása](/powershell/module/az.automation).
