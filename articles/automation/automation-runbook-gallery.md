---
title: A PowerShell-galéria Azure Automation runbookok és moduljainak használata
description: Ez a cikk azt ismerteti, hogyan használhatók a Microsoft és a Közösség runbookok és moduljai a PowerShell-galéria.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452781"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Runbookok és modulok használata a PowerShell-galériában

Ahelyett, hogy az Azure Automation saját runbookok és moduljait hozza létre, a Microsoft és a Közösség által már létrehozott forgatókönyvekhez is hozzáférhet. PowerShell-runbookok és- [modulokat](#modules-in-powershell-gallery) a PowerShell-Galéria és a [Python Runbookok](#use-python-runbooks) a Azure Automation GitHub-szervezetből szerezhet be. A közösséghez a [fejlesztés alatt álló forgatókönyvek](#add-a-powershell-runbook-to-the-gallery)megosztásával is hozzájárulhat.

> [!NOTE]
> A TechNet Script Center kivonásra kerül. A Runbook-katalógusban található runbookok származó összes adat át lett helyezve az [Automation GitHub-szervezetbe](https://github.com/azureautomation) további információért lásd [itt](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="runbooks-in-powershell-gallery"></a>Runbookok PowerShell-galéria

A [PowerShell-Galéria](https://www.powershellgallery.com/packages) különböző runbookok kínál a Microsofttól és a Közösségtől, amelyet importálhat a Azure Automationba. Ha szeretné használni, töltsön le egy runbook a katalógusból, vagy közvetlenül importálhatja a runbookok a katalógusból vagy az Automation-fiókjából a Azure Portal.

> [!NOTE]
> PowerShell-galéria nem támogatja a grafikus runbookok.

A Azure Portal használatával csak közvetlenül a PowerShell-galéria importálható. Ez a függvény nem hajtható végre a PowerShell használatával.

> [!NOTE]
> Ellenőriznie kell a PowerShell-galéria kapott runbookok tartalmát, és rendkívül körültekintően kell eljárnia, ha éles környezetben telepíti és futtatja őket.

## <a name="modules-in-powershell-gallery"></a>A PowerShell-galéria moduljai

A PowerShell-modulok olyan parancsmagokat tartalmaznak, amelyeket használhat a runbookok. A Azure Automation telepíthető meglévő modulok a [PowerShell-Galéria](https://www.powershellgallery.com)érhetők el. Ezt a katalógust elindíthatja a Azure Portalból, és közvetlenül telepítheti a modulokat Azure Automationba, vagy manuálisan is letöltheti és telepítheti őket.

## <a name="common-scenarios-available-in-powershell-gallery"></a>A PowerShell-galériaben elérhető gyakori forgatókönyvek

Az alábbi lista néhány olyan runbookok tartalmaz, amelyek támogatják a gyakori forgatókönyveket. Az Azure Automation csapat által létrehozott runbookok teljes listáját lásd: [AzureAutomationTeam-profil](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – egy Automation-fiókban lévő összes modul legújabb verziójának importálása PowerShell-galériaból.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – Azure Diagnostics és log Analytics konfigurálása a feladatok állapotát és a feladatok folyamait tartalmazó Azure Automation naplók fogadásához.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – távoli fájl másolása egy Windows Azure-beli virtuális gépről.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – helyi fájl másolása egy Azure-beli virtuális gépre.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>PowerShell-runbook importálása a runbook-galériából a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
1. Válassza a **runbookok** katalógus lehetőséget a **folyamat automatizálása** alatt.
1. **Forrás kiválasztása: PowerShell-Galéria**. Itt láthatja a böngészhető elérhető runbookok listáját.
1. A lista feletti keresőmező használatával szűkítheti a listát, vagy a szűrők segítségével szűkítheti a megjelenítést a közzétevő, a típus és a rendezés alapján. Keresse meg a kívánt katalógus-elemet, és válassza ki azt a részletek megtekintéséhez.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Böngészés a runbook-galériában." lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Egy elem importálásához kattintson az **Importálás** gombra a részletek panelen.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Egy runbook-gyűjtemény elem részleteinek megjelenítése" lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Igény szerint módosítsa a runbook nevét, majd kattintson az **OK** gombra a runbook importálásához.
1. A runbook megjelenik az Automation-fiók **runbookok** lapján.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>PowerShell-runbook importálása a GitHubról a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
1. Válassza a **runbookok** katalógus lehetőséget a **folyamat automatizálása** alatt.
1. **Forrás kiválasztása: GitHub**.
1. A lista feletti szűrők használatával szűkítheti a megjelenítést a közzétevő, a típus és a rendezés alapján. Keresse meg a kívánt katalógus-elemet, és válassza ki azt a részletek megtekintéséhez.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Böngészés a GitHub-galériában." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Egy elem importálásához kattintson az **Importálás** gombra a részletek panelen.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Egy runbook részletes nézete a GitHub-galériából." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Igény szerint módosítsa a runbook nevét, majd kattintson az **OK** gombra a runbook importálásához.
1. A runbook megjelenik az Automation-fiók **runbookok** lapján.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>PowerShell-runbook hozzáadása a gyűjteményhez

A Microsoft azt javasolja, hogy runbookok adjon hozzá a PowerShell-galériahoz, amelyet úgy gondol, hogy más ügyfelek számára hasznos lenne. A PowerShell-galéria PowerShell-modulokat és PowerShell-parancsfájlokat fogad el. Runbook hozzáadásához [töltse fel azt a PowerShell-galériaba](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Modul importálása a modul-galériából a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
1. A modulok listájának megnyitásához válassza ki a **megosztott erőforrások** területen lévő **modulok** elemet.
1. Kattintson a lap tetején található **Tallózás** katalógus lehetőségre.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="A modul gyűjteményének nézete" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. A tallózási katalógus lapon a keresőmező segítségével megkeresheti a találatokat a következő mezők bármelyikében:

   * Modulnév
   * Címkék
   * Szerző
   * Parancsmag/DSC-erőforrás neve

1. Keresse meg a kívánt modult, és válassza ki, hogy megtekintse a részleteit.

   Egy adott modul részletezésével további információkat jeleníthet meg. Ez az információ tartalmaz egy hivatkozást a PowerShell-galériara, a szükséges függőségekre, valamint az összes olyan parancsmagra vagy DSC-erőforrásra, amelyet a modul tartalmaz.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Egy modul részletes nézete a katalógusból." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Ha közvetlenül a Azure Automation szeretné telepíteni a modult, kattintson az **Importálás** gombra.
1. Az importálás ablaktáblán láthatja az importálandó modul nevét. Ha az összes függőség telepítve van, az **OK** gomb aktiválva lesz. Ha a függőségek hiányoznak, ezeket a függőségeket importálnia kell, mielőtt importálni tudja a modult.
1. Az importálás ablaktáblán kattintson az **OK** gombra a modul importálásához. Míg Azure Automation importál egy modult a fiókjába, kibontja a modul és a parancsmagok metaadatait. Ez a művelet eltarthat néhány percig, amíg az egyes tevékenységeket ki kell nyerni.
1. A rendszer első értesítést kap arról, hogy a modul üzembe helyezése folyamatban van, valamint egy másik értesítés, ha befejeződött.
1. A modul importálása után láthatja a rendelkezésre álló tevékenységeket. A runbookok és a DSC-erőforrásokhoz is használhat modul-erőforrásokat.

> [!NOTE]
> A csak a PowerShell Core-t támogató modulok nem támogatottak Azure Automationban, és nem importálhatók a Azure Portal, vagy közvetlenül a PowerShell-galéria központilag telepíthetők.

## <a name="use-python-runbooks"></a>Python-runbookok használata

A Python-Runbookok a [Azure Automation GitHub-szervezetben](https://github.com/azureautomation)érhetők el. Ha hozzájárul a GitHub-tárházhoz, adja hozzá a címkét **(GitHub-témakör): Python3** , amikor feltölti a hozzájárulását.

## <a name="request-a-runbook-or-module"></a>Runbook vagy modul igénylése

A [felhasználói hangra](https://feedback.azure.com/forums/246290-azure-automation/)vonatkozó kéréseket küldhet.  Ha segítségre van szüksége a runbook írásához vagy a PowerShell-lel kapcsolatos kérdésben, tegye fel kérdéseit a [Microsoft Q&egy kérdés oldalára](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Következő lépések

* A PowerShell-runbook megkezdéséhez tekintse meg az [oktatóanyag: PowerShell-Runbook létrehozása](learn/automation-tutorial-runbook-textual-powershell.md)című témakört.
* A runbookok használatával kapcsolatban lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
