---
title: Azure Automation runbooktípusok
description: Ez a cikk a Azure Automation használható runbookok típusait és szempontjait ismerteti.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3e4f90372c2da22e8df3430ce340477352e5033b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830447"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbooktípusok

A Azure Automation folyamatautomatizálási szolgáltatás számos runbooktípust támogat az alábbi táblázatban meghatározottak szerint. A folyamatautomatizálási környezettel kapcsolatos további információkért lásd: Runbook végrehajtása [a Azure Automation.](automation-runbook-execution.md)

| Típus | Description |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|Grafikus runbook a Windows PowerShell, és a grafikus szerkesztőben hozta létre és szerkesztette a Azure Portal. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|Grafikus runbook a munkafolyamaton Windows PowerShell, és teljes egészében a grafikus szerkesztőben jött létre és szerkesztette Azure Portal. |
| [PowerShell](#powershell-runbooks) |Szöveges runbook a Windows PowerShell alapján. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|A munkafolyamat-szkriptek Windows PowerShell alapuló szöveges runbook. |
| [Python](#python-runbooks) |Python-szkriptek alapján készült szöveges runbook. |

Egy adott runbook típusának meghatározásakor vegye figyelembe az alábbi szempontokat.

* A runbookok nem konvertálhatók grafikusról szöveges típusra vagy más módon.
* A különböző típusú runbookok gyermek runbookként való használata korlátozott. További információ: [Gyermek runbookok a Azure Automation.](automation-child-runbooks.md)

## <a name="graphical-runbooks"></a>Grafikus runbookok

Grafikus és grafikus PowerShell-munkafolyamat-runbookokat hozhat létre és szerkeszthet az alkalmazás grafikus Azure Portal. Az ilyen típusú runbookok azonban nem hozhatók létre és nem szerkeszthetők más eszközzel. A grafikus runbookok fő jellemzői:

* Fájlokba exportálva az Automation-fiókjában, majd egy másik Automation-fiókba importálva.
* PowerShell-kód létrehozása.
* Grafikus PowerShell-munkafolyamati runbookokká vagy onnan konvertálva az importálás során.

### <a name="advantages"></a>Előnyök

* Használja a vizuális insert-link-configure szerzői modellt.
* Összpontosítson arra, hogyan áramlnak végig az adatok a folyamaton.
* Vizuálisan ábrázolja a felügyeleti folyamatokat.
* A magas szintű munkafolyamatok létrehozásához gyermek runbookokként más runbookokat is tartalmazhat.
* A moduláris programozás ösztönzése.

### <a name="limitations"></a>Korlátozások

* Nem hozhat létre és nem szerkeszthet a Azure Portal.
* Összetett logika végrehajtásához PowerShell-kódot tartalmazó kódtevékenységre lehet szükség.
* A szöveges formátumok egyikére sem [konvertálható,](automation-runbook-types.md)és a szöveges runbookok sem konvertálhatók grafikus formátumra. 
* Nem lehet megtekinteni vagy közvetlenül szerkeszteni a grafikus munkafolyamat által létrehozott PowerShell-kódot. A létrehozott kódot bármilyen kódtevékenységben megtekintheti.
* Nem lehet runbookokat futtatni linuxos hibrid runbook-feldolgozón. Lásd: Erőforrások automatizálása az adatközpontban vagy a [felhőben hibrid runbook-feldolgozó használatával.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>PowerShell-runbookok

A PowerShell-runbookok a Windows PowerShell. Közvetlenül szerkesztheti a runbook kódját a forgatókönyv szövegszerkesztőjével a Azure Portal.  Bármilyen offline szövegszerkesztőt is használhat, és importálhatja a [runbookot](manage-runbooks.md) a Azure Automation.

### <a name="advantages"></a>Előnyök

* Implementálja az összes összetett logikát PowerShell-kóddal a PowerShell-munkafolyamat egyéb összetettségei nélkül.
* Gyorsabban kezdhet, mint a PowerShell munkafolyamat-runbookok, mivel nem kell őket lefordítani a futtatás előtt.
* Futtatás az Azure-ban és hibrid runbook-dolgozókon Windows és Linux rendszeren is.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-parancsprogramozást.
* A runbookok nem használhatnak párhuzamos [feldolgozást](automation-powershell-workflow.md#use-parallel-processing) több művelet párhuzamos végrehajtásához.
* Hiba esetén a runbookok nem tudnak ellenőrzőpontokat használni a runbook folytatásához. [](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)
* Gyermekrunbookként csak PowerShell-munkafolyamat-runbookokat és grafikus runbookokat foglalhat bele a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) parancsmag használatával, amely új feladatot hoz létre.

### <a name="known-issues"></a>Ismert problémák

A PowerShell-runbookok jelenlegi ismert problémái a következők:

* A PowerShell-runbookok nem tudnak lekérni egy titkosítatlan, null értékű [változóeszközt.](./shared-resources/variables.md)
* A PowerShell-runbookok nem tudnak változóeszközt lekérni a `*~*` névvel.
* Egy [PowerShell-runbook](/powershell/module/microsoft.powershell.management/get-process) hurokban futó Get-Process művelete körülbelül 80 iteráció után összeomlhat.
* Egy PowerShell-runbook meghiúsulhat, ha egyszerre próbál nagy mennyiségű adatot írni a kimeneti streambe. Általában úgy lehet a probléma megoldásához, ha a runbook kimenete csak a nagy objektumokhoz szükséges információk. Ahelyett például, hogy a parancsmagot korlátozások nélkül használta volna, a parancsmag kimenete csak a szükséges paramétereket adhatja meg, például a `Get-Process` parancsmagban. `Get-Process | Select ProcessName, CPU`

## <a name="powershell-workflow-runbooks"></a>PowerShell munkafolyamat-runbookok

A PowerShell-alapú munkafolyamat-runbookok szöveges runbookok, amelyek a [Windows PowerShell alapulnak.](automation-powershell-workflow.md) A forgatókönyv kódját közvetlenül szerkesztheti a forgatókönyv szövegszerkesztőjével a Azure Portal. Bármilyen offline szövegszerkesztőt is használhat, és [importálhatja a runbookot a](manage-runbooks.md) Azure Automation.

### <a name="advantages"></a>Előnyök

* Implementálja az összes összetett logikát a PowerShell-munkafolyamat kódjának használatával.
* Hiba [esetén használjon ellenőrzőpontokat](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) a művelet folytatásához.
* Párhuzamos [feldolgozás használata](automation-powershell-workflow.md#use-parallel-processing) több művelet párhuzamos használhatja.
* További grafikus runbookokat és PowerShell-munkafolyamat-runbookokat tartalmazhat gyermek runbookokként magas szintű munkafolyamatok létrehozásához.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-munkafolyamatot.
* A runbookok a PowerShell-munkafolyamatok további összetettségét kell kezelniük, például a [deserializált objektumokat.](automation-powershell-workflow.md#deserialized-objects)
* A runbookok futtatása tovább tart, mint a PowerShell-runbookok futtatása, mivel a futtatás előtt le kell fordítani őket.
* A PowerShell-runbookok csak gyermek runbookokként foglalhatóak bele a `Start-AzAutomationRunbook` parancsmag használatával.
* A runbookok nem futtathatók linuxos hibrid runbook-feldolgozón.

## <a name="python-runbooks"></a>Python-runbookok

A Python-runbookok a Python 2 és a Python 3 alatt vannak lefordítva. A Python 3-runbookok jelenleg előzetes verzióban érhetőek el. A forgatókönyv kódját közvetlenül szerkesztheti a forgatókönyv szövegszerkesztőjével a Azure Portal. Offline szövegszerkesztőt is használhat, és importálhatja a [runbookot](manage-runbooks.md) a Azure Automation.

A Python 3-runbookok a következő globális Azure-infrastruktúrákban támogatottak:

* Globális Azure
* Azure Government

### <a name="advantages"></a>Előnyök

* Használja a robusztus Python-kódtárakat.
* Futtatható az Azure-ban vagy hibrid runbook-dolgozókon.
* Python 2 esetén a windowsos hibrid runbook-dolgozók támogatottak a [telepített Python 2.7-es](https://www.python.org/downloads/release/latest/python2) verzióval.
* Python 3 Cloud Jobs esetén a Python 3.8-as verziója támogatott. A bármely 3.x verzióból származó szkriptek és csomagok akkor működnek, ha a kód kompatibilis a különböző verziókban.  
* Windows rendszerű gépeken a Python 3 hibrid feladatok esetében bármelyik 3.x verziót telepítheti.  
* A Linux rendszerű gépeken futó Python 3 hibrid feladatok esetében a DSC OMSConfig és a Linux hibrid feldolgozó futtatásához a gépen telepített Python 3-as verziótól függünk. Javasoljuk, hogy Linux rendszerű gépeken telepítse a 3.6-os rendszert. A különböző verzióknak azonban akkor is működniük kell, ha a Metódus-aláírások vagy a Python 3-verziók közötti szerződések nem különböziknek.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a Python-szkripteket.
* Külső kódtárak használata esetén importálni kell [a csomagokat](python-packages.md) az Automation-fiókba.
* Nem **működik a Start-AutomationRunbook** parancsmag használata   a PowerShell-/PowerShell-munkafolyamatban Python 3-runbook (előzetes verzió) elindítani. Ennek a korlátozásnak a megoldásához használhatja az Az.Automation modul **Start-AzAutomationRunbook** parancsmagját vagy az AzureRm.Automation modul **Start-AzureRmAutomationRunbook** parancsmagját.  
* A Python 3-runbookok (előzetes verzió) és a csomagok nem működnek a PowerShell-sel.
* Azure Automation nem támogatja a **sys.stderr fájlt.**

### <a name="known-issues"></a>Ismert problémák

A Python 3-feladatok időnként meghiúsulnak egy kivételüzenettel, *amely érvénytelen értelmező végrehajtható elérési út.* Ez a kivétel akkor lehet látható, ha egy feladat késik, 10 percnél tovább tart, vagy a **Start-AutomationRunbook** használatával elindítja a Python 3-runbookokat. Ha a feladat késik, elegendő a runbook újraindítása.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-runbookokkal kapcsolatos további információkért [lásd: Oktatóanyag: PowerShell-runbook létrehozása.](learn/automation-tutorial-runbook-textual-powershell.md)
* A PowerShell-munkafolyamat-runbookokkal kapcsolatos további információkért [lásd: Oktatóanyag: PowerShell-munkafolyamat-runbook létrehozása.](learn/automation-tutorial-runbook-textual.md)
* A grafikus runbookokkal kapcsolatos további információkért [lásd: Oktatóanyag: Grafikus runbook létrehozása.](learn/automation-tutorial-runbook-graphical.md)
* A Python-runbookokkal kapcsolatos további információkért [lásd: Oktatóanyag: Python-runbook létrehozása.](learn/automation-tutorial-runbook-textual-python2.md)
