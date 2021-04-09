---
title: Azure Automation runbook-típusok
description: Ez a cikk ismerteti azokat a runbookok, amelyeket a Azure Automation használhat, és figyelembe kell venni a használandó típus meghatározásának szempontjait.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: 067096943cd95913077ada817c94640ff5264520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634889"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbook-típusok

A Azure Automation Process Automation szolgáltatás számos runbookok támogat, az alábbi táblázatban meghatározottak szerint. A folyamat-automatizálási környezettel kapcsolatos további tudnivalókért lásd: [Runbook végrehajtása Azure Automationban](automation-runbook-execution.md).

| Típus | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|A Windows PowerShellen alapuló grafikus runbook, és a Azure Portal grafikus szerkesztőjében teljesen elkészült és szerkeszthető. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|A grafikus runbook a Windows PowerShell-munkafolyamaton alapul, és a Azure Portal grafikus szerkesztőjében teljesen elkészült és szerkeszthető. |
| [PowerShell](#powershell-runbooks) |Szöveges runbook Windows PowerShell-parancsfájlok alapján. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|A Windows PowerShell munkafolyamat-parancsfájlkezelésen alapuló szöveges runbook. |
| [Python](#python-runbooks) |A szöveges runbook Python-parancsfájlok alapján. |

Vegye figyelembe az alábbi szempontokat, amikor meghatározza, hogy melyik típust kell használni egy adott runbook.

* A runbookok nem konvertálható grafikus formátumból szöveg típusúra, vagy fordítva.
* A különböző típusú runbookok a gyermek runbookok való használata korlátozásokkal jár. További információ: [gyermek runbookok Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafikus runbookok

Grafikus és grafikus PowerShell-munkafolyamati runbookok hozhat létre és szerkeszthet a Azure Portal grafikus szerkesztőjének használatával. Ilyen típusú runbook azonban nem hozhat létre és nem szerkeszthet más eszközzel. A grafikus runbookok főbb jellemzői:

* Az Automation-fiókban lévő fájlokra exportálja, majd importálja egy másik Automation-fiókba.
* PowerShell-kód előállítása.
* Az importálás során grafikus PowerShell-munkafolyamati runbookok konvertálva.

### <a name="advantages"></a>Előnyök

* A Visual INSERT-link-configure authoring Model használata.
* Koncentráljon arra, hogy az adatforgalom Hogyan zajlik a folyamaton.
* A felügyeleti folyamatok vizuális ábrázolása.
* A magas szintű munkafolyamatok létrehozásához gyermek runbookok más runbookok is tartalmazhat.
* A moduláris programozás ösztönzése.

### <a name="limitations"></a>Korlátozások

* A Azure Portalon kívül nem hozható létre és nem szerkeszthető.
* Az összetett logika végrehajtásához a PowerShell-kódot tartalmazó programkód-tevékenységre lehet szükség.
* Nem lehet konvertálni az egyik [szöveges formátumra](automation-runbook-types.md)sem, és nem alakíthat át szöveges runbook grafikus formátumra. 
* Nem lehet megtekinteni vagy közvetlenül szerkeszteni a grafikus munkafolyamat által létrehozott PowerShell-kódot. Megtekintheti a létrehozott kódot bármely kód tevékenységben.
* A runbookok nem futtatható Linux hibrid Runbook-feldolgozón. [A hibrid Runbook-feldolgozók használatával lásd: erőforrások automatizálása az adatközpontban vagy a felhőben](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runbookok

A PowerShell-runbookok a Windows PowerShellen alapulnak. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.  Használhatja az offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* Az összes összetett logikát PowerShell-kóddal implementálhatja a PowerShell-munkafolyamat egyéb bonyolultsága nélkül.
* Gyorsabb a PowerShell-munkafolyamatok runbookok, mert a futtatása előtt nem kell lefordítani őket.
* Az Azure-ban és a hibrid Runbook-feldolgozókon is futtatható Windows és Linux rendszereken.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-parancsfájlokat.
* A runbookok nem használhat [párhuzamos feldolgozást](automation-powershell-workflow.md#use-parallel-processing) több művelet párhuzamos végrehajtásához.
* A runbookok nem tud [ellenőrzőpontokat](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) használni a runbook folytatásához, ha hiba történt.
* A [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) parancsmaggal csak a PowerShell-munkafolyamatok runbookok és a grafikus runbookok lehet felvenni, amely új feladatot hoz létre.

### <a name="known-issues"></a>Ismert problémák

A PowerShell-runbookok jelenleg ismert problémák a következők:

* A PowerShell-runbookok nem tud beolvasni egy titkosítatlan [változó](./shared-resources/variables.md) objektumot NULL értékkel.
* A PowerShell-runbookok nem tud beolvasni egy változót a `*~*` névben.
* A PowerShell-runbook egy hurokban való [lekéréses](/powershell/module/microsoft.powershell.management/get-process) művelete a 80-es iteráció után összeomlik.
* A PowerShell-runbook sikertelenek lehetnek, ha nagy mennyiségű adatokat próbálnak írni a kimeneti adatfolyamba. Ezt a problémát általában úgy is megkerülheti, ha a runbook kimenete csak a nagyméretű objektumokkal való működéshez szükséges információ. A korlátozás nélküli használata helyett például `Get-Process` a parancsmag kimenete csak a szükséges paramétereket eredményezi `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>PowerShell-munkafolyamat runbookok

A PowerShell-munkafolyamat runbookok a [Windows PowerShell-munkafolyamaton](automation-powershell-workflow.md)alapuló szöveges runbookok. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával. Használhatja az offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* Az összes összetett logika implementálása a PowerShell-munkafolyamat kódjával.
* Ha hiba történt, használja az [ellenőrzőpontokat](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) a művelet folytatásához.
* Párhuzamos [feldolgozás](automation-powershell-workflow.md#use-parallel-processing) használata több művelet párhuzamos végrehajtásához.
* Más grafikus runbookok és PowerShell-munkafolyamatok runbookok is tartalmazhat, mint a magas szintű munkafolyamatok létrehozására szolgáló runbookok.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-munkafolyamatot.
* A runbookok-nek a PowerShell-munkafolyamatok további összetettségét kell megfelelnie, például [deszerializált objektumokat](automation-powershell-workflow.md#deserialized-objects).
* A runbookok hosszabb időt vesz igénybe, mint a PowerShell-runbookok, mivel azokat a Futtatás előtt le kell fordítani.
* A parancsmag használatával csak a PowerShell-runbookok lehet alárendelt runbookok `Start-AzAutomationRunbook` .
* A runbookok nem futtatható Linux hibrid Runbook-feldolgozón.

## <a name="python-runbooks"></a>Python-runbookok

A Python runbookok a Python 2 és a Python 3 alatt fordítható le. A Python 3 runbookok jelenleg előzetes verzióban érhető el. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával. Használhat egy offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

A Python 3 runbookok a következő Azure globális infrastruktúrák támogatják:

* Azure globális
* Azure Government

### <a name="advantages"></a>Előnyök

* Használja a robusztus Python-kódtárakat.
* Az Azure-ban vagy hibrid Runbook-feldolgozókon is futtatható.
* A Python 2 esetében a Windows Hybrid Runbook-feldolgozók a [python 2,7](https://www.python.org/downloads/release/latest/python2) -es verzióval támogatottak.
* Python 3 felhőalapú feladatokhoz a Python 3,8 verziója támogatott. A 3. x verzióból származó parancsfájlok és csomagok akkor működhetnek, ha a kód a különböző verziókon is kompatibilis.  
* A Windows rendszerű gépeken futó Python 3 hibrid feladatok esetében választhat, hogy a használni kívánt 3. x verziót is telepíti.  
* A Python 3 hibrid feladatokhoz Linux rendszerű gépeken a számítógépen telepített Python 3 verziójától függ, hogy a DSC OMSConfig és a Linux Hybrid Worker fusson. Javasoljuk, hogy telepítse a 3,6-es Linux rendszerű gépeket. Azonban a különböző verzióknak is működniük kell, ha a metódus aláírása vagy a Python 3 verziói között nem történt meg a változások megszakítása.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a Python-parancsfájlokat.
* A külső gyártótól származó kódtárak használatához [importálnia kell a csomagokat](python-packages.md) az Automation-fiókba.
* A **Start-AutomationRunbook**   parancsmag használata a PowerShell/PowerShell-munkafolyamatban a Python 3 runbook (előzetes verzió) elindításához nem működik. A **AzAutomationRunbook** parancsmagot az az. Automation modulból vagy a **Start-AzureRmAutomationRunbook** parancsmagból is használhatja a AzureRm. Automation modulból, hogy megkerülje ezt a korlátozást.  
* A Python 3 runbookok (előzetes verzió) és a csomagok nem működnek a PowerShell-lel.
* A Azure Automation nem támogatja a **sys. stderr**.

### <a name="known-issues"></a>Ismert problémák

A Python 3 feladat időnként meghiúsul, és a rendszer *érvénytelen értelmező végrehajtható elérési úttal* rendelkező kivételi üzenetet küld. Ezt a kivételt akkor láthatja, ha a feladatok késleltetve vannak, és több mint 10 percet vesznek igénybe, vagy a **Start-AutomationRunbook** használatával elindítják a Python 3 runbookok. Ha a feladatot késleltetik, a runbook újraindításának elegendőnek kell lennie.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-runbookok kapcsolatos további tudnivalókért lásd [: oktatóanyag: PowerShell-Runbook létrehozása](learn/automation-tutorial-runbook-textual-powershell.md).
* A PowerShell-munkafolyamatok runbookok kapcsolatos tudnivalókat a következő témakörben talál [: oktatóanyag: PowerShell munkafolyamat-Runbook létrehozása](learn/automation-tutorial-runbook-textual.md).
* A grafikus runbookok kapcsolatos további tudnivalókért lásd az [oktatóanyag: grafikus Runbook létrehozása](learn/automation-tutorial-runbook-graphical.md)című témakört.
* A Python runbookok kapcsolatos további tudnivalókért lásd [: oktatóanyag: Python-Runbook létrehozása](learn/automation-tutorial-runbook-textual-python2.md).
