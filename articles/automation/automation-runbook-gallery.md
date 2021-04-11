---
title: A PowerShell-galéria Azure Automation runbookok és moduljainak használata
description: Ez a cikk azt ismerteti, hogyan használhatók a Microsoft GitHub-repók és a PowerShell-galéria runbookok és moduljai.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030633"
---
# <a name="use-existing-runbooks-and-modules"></a>Meglévő runbookok és modulok használata

Ahelyett, hogy az Azure Automation saját runbookok és moduljait hozza létre, a Microsoft és a Közösség által már létrehozott forgatókönyvekhez is hozzáférhet. Az Azure-hoz kapcsolódó PowerShell-és Python-runbookok a Azure Portal Runbook-galériájában, valamint a PowerShell-galériaból a [modulokat](#modules-in-the-powershell-gallery) és a [runbookok](#runbooks-in-the-powershell-gallery) (amelyek az Azure-ra nem jellemzőek lehetnek). A közösséghez a [fejlesztés alatt álló forgatókönyvek](#contribute-to-the-community)megosztásával is hozzájárulhat.

> [!NOTE]
> A TechNet Script Center kivonásra kerül. A Runbook-katalógusban található runbookok származó összes adat át lett helyezve az [Automation GitHub-szervezetbe](https://github.com/azureautomation) további információért lásd: [Azure Automation Runbookok áttérés a githubra](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Runbookok importálása a GitHubról a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza a **runbookok** katalógus lehetőséget a **folyamat automatizálása** alatt.
3. **Forrás kiválasztása: GitHub**.
4. A lista feletti szűrők használatával szűkítheti a megjelenítést a közzétevő, a típus és a rendezés alapján. Keresse meg a kívánt katalógus-elemet, és válassza ki azt a részletek megtekintéséhez.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="A runbook-gyűjtemény tallózása." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Egy elem importálásához kattintson az **Importálás** gombra a Részletek lapon.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Gyűjtemény-elem importálása.":::

6. Igény szerint módosítsa a runbook nevét az importálás panelen, majd kattintson az **OK** gombra a runbook importálásához.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Katalógusbeli elem importálása panel":::

7. A runbook megjelenik az Automation-fiók **runbookok** lapján.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbookok a PowerShell-galéria

> [!IMPORTANT]
> Ellenőrizze, hogy a PowerShell-galériaból származó összes runbookok tartalma érvényesítve van-e. Éles környezetben is rendkívül körültekintően telepítheti és futtathatja őket.

A [PowerShell-Galéria](https://www.powershellgallery.com/packages) különböző runbookok kínál a Microsofttól és a Közösségtől, amelyet importálhat a Azure Automationba. Ha szeretné használni, töltsön le egy runbook a katalógusból, vagy közvetlenül importálhatja a runbookok a katalógusból vagy az Automation-fiókjából a Azure Portal.

> [!NOTE]
> PowerShell-galéria nem támogatja a grafikus runbookok.

A Azure Portal használatával csak közvetlenül a PowerShell-galéria importálható. Ez a függvény nem hajtható végre a PowerShell használatával. Az eljárás ugyanaz, mint az [Runbookok importálása a githubról a Azure Portal, azzal](#import-runbooks-from-github-with-the-azure-portal)a különbséggel, hogy a **forrás** **PowerShell-Galéria** lesz.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="A runbook-gyűjtemény forrásának kiválasztása." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>A PowerShell-galéria moduljai

A PowerShell-modulok olyan parancsmagokat tartalmaznak, amelyeket használhat a runbookok. A Azure Automation telepíthető meglévő modulok a [PowerShell-Galéria](https://www.powershellgallery.com)érhetők el. Ezt a katalógust elindíthatja a Azure Portalból, és közvetlenül telepítheti a modulokat Azure Automationba, vagy manuálisan is letöltheti és telepítheti őket.

A Azure Portal importálási modulokat is találhat. Az Automation-fiókjához a **megosztott erőforrások** alatt található **modulok galériájában** vannak felsorolva.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>A PowerShell-galériaben elérhető gyakori forgatókönyvek

Az alábbi lista néhány olyan runbookok tartalmaz, amelyek támogatják a gyakori forgatókönyveket. Az Azure Automation csapat által létrehozott runbookok teljes listáját lásd: [AzureAutomationTeam-profil](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – egy Automation-fiókban lévő összes modul legújabb verziójának importálása PowerShell-galériaból.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – Azure Diagnostics és log Analytics konfigurálása a feladatok állapotát és a feladatok folyamait tartalmazó Azure Automation naplók fogadásához.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – távoli fájl másolása egy Windows Azure-beli virtuális gépről.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – helyi fájl másolása egy Azure-beli virtuális gépre.

## <a name="contribute-to-the-community"></a>Hozzájárulás a Közösséghez

Nyomatékosan javasoljuk, hogy járuljon hozzá a Azure Automation Közösség növeléséhez és fejlesztéséhez. Ossza meg a Közösséggel létrehozott csodálatos runbookok. Az Ön hozzájárulása MÉLTÁNYOLVA lesz!

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Runbook hozzáadása a GitHub Runbook-Galériához

Ehhez a GitHub-munkafolyamathoz hozzáadhat új PowerShell-vagy Python-runbookok a Runbook-gyűjteményhez.

1. Hozzon létre egy nyilvános tárházat a GitHubon, és adja hozzá a runbook és az egyéb szükséges fájlokat (például readme.md, leírás stb.).
1. Adja hozzá a `azureautomationrunbookgallery` következő témakört, amely gondoskodik arról, hogy a szolgáltatás a tárházat észlelje, így az Automation Runbook-katalógusban is megjeleníthető.
1. Ha a létrehozott runbook egy PowerShell-munkafolyamat, adja hozzá a következő témakört: `PowerShellWorkflow` . Ha ez egy Python 3 runbook, adja hozzá a t `Python3` . Az Azure runbookok nem kell más konkrét témákat megadnia, de javasoljuk, hogy adjon hozzá további témákat, amelyek kategorizálásra és keresésre használhatók a Runbook-galériában.

   >[!NOTE]
   >Tekintse meg a meglévő runbookok a katalógusban olyan dolgokhoz, mint például a formázás, a fejlécek és a meglévő címkék, amelyeket használhat (például `Azure Automation` vagy `Linux Azure Virtual Machines` ).

Ha egy meglévő runbook módosítását kívánja javasolni, egy lekéréses kérelmet kell lekérnie a fájlra. 

Ha úgy dönt, hogy egy meglévő runbook klónozását és szerkesztését választja, akkor az ajánlott eljárás az, hogy más nevet adjon neki. Ha újra felhasználja a régi nevet, a Runbook-katalógusban kétszer jelenik meg.

>[!NOTE]
>Engedélyezze a GitHub és az Automation Runbook-katalógus közötti szinkronizálást legalább 12 óráig mind a frissített, mind az új runbookok.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>PowerShell-runbook hozzáadása a PowerShell-Galériához

A Microsoft azt javasolja, hogy runbookok adjon hozzá a PowerShell-galériahoz, amelyet úgy gondol, hogy más ügyfelek számára hasznos lenne. A PowerShell-galéria PowerShell-modulokat és PowerShell-parancsfájlokat fogad el. Runbook hozzáadásához [töltse fel azt a PowerShell-galériaba](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Modul importálása a Azure Portal modulok galériájában

1. Az Azure Portalon nyissa meg az Automation-fiókját.
1. A **megosztott erőforrások** területen válassza a **modulok gyűjtemény** lehetőséget a modulok listájának megnyitásához.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="A modul gyűjteményének nézete" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. A Tallózás a katalógusban lapon a következő mezőkben kereshet:

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

## <a name="request-a-runbook-or-module"></a>Runbook vagy modul igénylése

A [felhasználói hangra](https://feedback.azure.com/forums/246290-azure-automation/)vonatkozó kéréseket küldhet.  Ha segítségre van szüksége a runbook írásához vagy a PowerShell-lel kapcsolatos kérdésben, tegye fel kérdéseit a [Microsoft Q&egy kérdés oldalára](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Következő lépések

* A PowerShell-runbookok megkezdéséhez tekintse meg az [oktatóanyag: PowerShell-Runbook létrehozása](learn/automation-tutorial-runbook-textual-powershell.md)című témakört.
* A runbookok használatával kapcsolatban lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* A PowerShell-parancsfájlok futtatásával kapcsolatos további információkért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
