---
title: Migrálás az Orchestratorből az Azure Automationbe (bétaverzió)
description: Ez a cikk azt ismerteti, hogyan lehet áttelepíteni a runbookok és az integrációs csomagokat a Orchestrator-ből a Azure Automationba.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896935"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrálás az Orchestratorből az Azure Automationbe (bétaverzió)

A [System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) runbookok az integrációs csomagok azon tevékenységein alapulnak, amelyek kifejezetten a Orchestrator vannak írva, míg a Azure Automation a Windows powershellen alapulnak. A Azure Automation [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) hasonló megjelenéssel rendelkeznek a Orchestrator-runbookok, és azok tevékenységei a PowerShell-parancsmagokat, a gyermek runbookok és az eszközöket jelképezik. A runbookok konvertálásán kívül az integrációs csomagokat olyan tevékenységekkel kell konvertálnia, amelyeket a runbookok a Windows PowerShell-parancsmagokkal rendelkező integrációs modulokhoz használ. 

A [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) a helyi adatközpontban (például a Orchestrator) tárolja és futtatja a runbookok, és ugyanazokat az integrációs modulokat használja, mint a Azure Automation. A Runbook Converter a Orchestrator-runbookok grafikus runbookok alakítja át, amelyek nem támogatottak az SMA-ban. Továbbra is telepítheti a standard tevékenységek modult és a System Center Orchestrator integrációs modulokat SMA-ba, de manuálisan kell [újraírnia a runbookok](/system-center/sma/authoring-automation-runbooks).

## <a name="download-the-orchestrator-migration-toolkit"></a>A Orchestrator áttelepítési eszközkészlet letöltése

Az áttelepítés első lépése a [System Center Orchestrator áttelepítési eszközkészlet](https://www.microsoft.com/download/details.aspx?id=47323)letöltése. Ez az eszközkészlet olyan eszközöket tartalmaz, amelyek segítséget nyújtanak a runbookok Orchestrator-ről Azure Automationra való átalakításában.  

## <a name="import-the-standard-activities-module"></a>A standard tevékenységek modul importálása

Importálja a [standard tevékenységek modult](/system-center/orchestrator/standard-activities) Azure Automationba. Ide tartoznak a szabványos Orchestrator-tevékenységek konvertált verziói, amelyek a grafikus runbookok konvertálhatók.

## <a name="import-orchestrator-integration-modules"></a>Orchestrator-integrációs modulok importálása

A Microsoft [integrációs csomagokat](/previous-versions/system-center/packs/hh295851(v=technet.10)) biztosít a System Center összetevőinek és más termékeinek automatizálására szolgáló runbookok létrehozásához. Ezen integrációs csomagok némelyike jelenleg a OIT alapul, de az ismert problémák miatt jelenleg nem alakítható át integrációs modulokra. Importálja a [System Center Orchestrator integrációs modulokat](https://www.microsoft.com/download/details.aspx?id=49555) a Azure Automationba a System Centerhez hozzáférő runbookok által használt integrációs csomagokba. Ez a csomag tartalmazza az integrációs csomagok konvertált verzióit, amelyek importálhatók Azure Automationba és Service Management Automationba.  

## <a name="convert-integration-packs"></a>Integrációs csomagok konvertálása

Az [Integration Pack Converter](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) használatával a [Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) használatával létrehozott integrációs csomagokat átalakíthatja PowerShell-alapú integrációs modulokba, amelyek Azure Automation vagy Service Management Automationba importálhatók. Az integrációs csomag átalakítójának futtatásakor egy varázsló jelenik meg, amely lehetővé teszi egy integrációs csomag (. OIP) fájljának kiválasztását. A varázsló ezután felsorolja az integrációs csomagban szereplő tevékenységeket, és lehetővé teszi az áttelepíteni kívánt tevékenységek kiválasztását. A varázsló befejezése után létrehoz egy integrációs modult, amely tartalmazza a megfelelő parancsmagot az eredeti integrációs csomagban lévő összes tevékenységhez.

> [!NOTE]
> Az integrációs csomag átalakítója nem használható olyan integrációs csomagok átalakítására, amelyek nem a OIT-mel lettek létrehozva. A Microsoft olyan integrációs csomagokat is biztosít, amelyek jelenleg nem alakíthatók át ezzel az eszközzel. Az integrációs csomagok konvertált verziói letölthetők, így Azure Automation vagy Service Management Automation telepíthetik őket.

### <a name="parameters"></a>Paraméterek

Az integrációs csomagban lévő tevékenységek bármely tulajdonsága az integrációs modul megfelelő parancsmagjának paramétereinek megfelelően lesz konvertálva.  A Windows PowerShell-parancsmagok olyan [általános paraméterekkel](/powershell/module/microsoft.powershell.core/about/about_commonparameters) rendelkeznek, amelyek az összes parancsmaggal használhatók. A-verbose paraméter például egy parancsmagot eredményez a művelet részletes adatainak kiírásához.  Egyetlen parancsmag sem rendelkezhet ugyanazzal a névvel, mint a Common paraméterrel. Ha egy tevékenységnek ugyanaz a neve, mint a közös paraméternek, a varázsló megkéri, hogy adjon meg egy másik nevet a paraméter számára.

### <a name="monitor-activities"></a>Tevékenységek monitorozása

A Orchestrator runbookok figyelése egy [figyelő tevékenységgel](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) kezdődik, és folyamatosan várakozik, hogy egy adott esemény meghívja őket. A Azure Automation nem támogatja a runbookok figyelését, így az integrációs csomagban lévő figyelési tevékenységek nem konvertálódnak. Ehelyett helyőrző parancsmag jön létre a figyelő tevékenység integrációs moduljában.  Ez a parancsmag nem rendelkezik funkcionalitással, de lehetővé teszi az azt használó átalakított runbook telepítését. Ez a runbook nem futtatható Azure Automationban, de telepíthető úgy, hogy módosítható legyen.

A Orchestrator olyan [szabványos tevékenységeket](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) tartalmaz, amelyek nem szerepelnek az integrációs csomagban, de számos runbookok használják.  A standard szintű tevékenységek modul egy integrációs modul, amely minden egyes tevékenységhez megfelelő parancsmagot tartalmaz. A standard tevékenységeket használó konvertált runbookok importálása előtt telepítenie kell ezt az integrációs modult Azure Automation.

Az átalakított runbookok támogatásán kívül a standard szintű tevékenységek modulban található parancsmagok a Orchestrator által ismert, új runbookok létrehozására használhatók a Azure Automationban. Habár az összes szabványos tevékenység funkcióit parancsmagokkal is elvégezheti, különböző műveleteket hajthat végre. Az átalakított szabványos tevékenységek modul parancsmagjai ugyanúgy működnek, mint a kapcsolódó tevékenységek, és ugyanazokat a paramétereket használják. Ez segítséget nyújt a Azure Automation runbookok való áttéréshez.

## <a name="convert-orchestrator-runbooks"></a>Orchestrator-runbookok konvertálása

A Orchestrator Runbook Converter a Orchestrator runbookok a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) alakítja át, amely importálható Azure Automationba. A Runbook átalakító PowerShell-modulként van megvalósítva az átalakítást végző parancsmaggal `ConvertFrom-SCORunbook` . A konverter telepítésekor létrehoz egy parancsikont egy PowerShell-munkamenethez, amely betölti a parancsmagot.   

Az alábbiakban a runbook konvertálásának és a Azure Automationba való importálásának alapvető lépései láthatók. A parancsmag használatának részleteit a szakasz későbbi részében találja.

1. Egy vagy több runbookok exportálása a Orchestrator-ből.
2. Integrációs modulok beszerzése az runbook lévő összes tevékenységhez.
3. Alakítsa át a Orchestrator runbookok az exportált fájlban.
4. Az átalakítás ellenőrzéséhez és a szükséges manuális feladatok meghatározásához tekintse át a naplók információit.
5. Konvertált runbookok importálása Azure Automationba.
6. Hozza létre a szükséges eszközöket a Azure Automationban.
7. A szükséges tevékenységek módosításához szerkessze Azure Automation runbook.

A szintaxisa a következő `ConvertFrom-SCORunbook` :

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – a konvertálandó runbookok tartalmazó exportfájl elérési útja.
* Modul – a runbookok lévő tevékenységeket tartalmazó integrációs modulok vesszővel tagolt listája.
* OutputFolder – a mappa elérési útja a konvertált grafikus runbookok létrehozásához.

A következő példában szereplő parancs egy **MyRunbooks.ois_export** nevű exportálási fájlba konvertálja a runbookok.  Ezek a runbookok a Active Directory és Data Protection Manager integrációs csomagokat használják.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>A Runbook Converter naplófájljainak használata

A Runbook-átalakító a következő naplófájlokat hozza létre a konvertált Runbook megegyező helyen.  Ha a fájlok már léteznek, a rendszer felülírja őket az utolsó konverzió információi alapján.

| Fájl | Tartalom |
|:--- |:--- |
| Runbook-átalakító – Progress. log |Az átalakítás részletes lépései, beleértve az egyes tevékenységek átalakított és figyelmeztetési információit, amelyeket a rendszer nem konvertált. |
| Runbook-átalakító – összefoglalás. log |Az utolsó konverzió összefoglalása, beleértve az összes figyelmeztetést és nyomon követő feladatokat, amelyeket el kell végeznie, például egy változó létrehozásához, amely szükséges a konvertált runbook. |

### <a name="export-runbooks-from-orchestrator"></a>Runbookok exportálása a Orchestrator

A Runbook-átalakító egy vagy több runbookok tartalmazó Orchestrator származó exportálási fájllal működik.  Létrehoz egy megfelelő Azure Automation runbook az exportálási fájl minden egyes Orchestrator-runbook.  

A runbook Orchestrator-ből való exportálásához kattintson a jobb gombbal a runbook nevére a Runbook Designer, majd válassza az **Exportálás** lehetőséget.  A mappában lévő összes runbookok exportálásához kattintson a jobb gombbal a mappa nevére, és válassza az **Exportálás** lehetőséget.

### <a name="convert-runbook-activities"></a>Runbook-tevékenységek konvertálása

A Runbook Converter a Orchestrator Runbook minden tevékenységét egy megfelelő tevékenységre konvertálja Azure Automationban.  Azon tevékenységek esetében, amelyek nem alakíthatók át, a runbook figyelmeztető szöveggel hozza létre a helyőrző tevékenységet.  A konvertált runbook Azure Automationba való importálása után a tevékenységek bármelyikét le kell cserélnie a szükséges funkciókat végrehajtó érvényes tevékenységekkel.

A standard szintű tevékenységek modulban minden Orchestrator-tevékenység át lesz konvertálva. Vannak olyan általános Orchestrator tevékenységek, amelyek nem szerepelnek ebben a modulban, de nem lesznek konvertálva. Például `Send Platform Event` nincs Azure Automation egyenértékű, mert az esemény a Orchestrator jellemző.

A [figyelési tevékenységeket](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) a rendszer nem konvertálja, mert a Azure Automationban nincsenek egyenértékűek. A kivételek a helyőrző tevékenységre konvertált konvertált integrációs csomagok figyelési tevékenységei.

Az átalakított integrációs csomagból származó tevékenységek konvertálása akkor történik meg, ha az integrációs modul elérési útját megadja a `modules` paraméterrel. A System Center integrációs csomagjaihoz használhatja a System Center Orchestrator integrációs modulokat.

### <a name="manage-orchestrator-resources"></a>Orchestrator-erőforrások kezelése

A Runbook Converter csak a runbookok konvertálja, nem más Orchestrator-erőforrásokat, például számlálókat, változókat vagy kapcsolatokat.  Azure Automation nem támogatottak a számlálók.  A változók és a kapcsolatok támogatottak, de ezeket manuálisan kell létrehoznia. A naplófájlok arról tájékoztatnak, ha a runbook szüksége van ilyen erőforrásokra, és meg kell adnia a Azure Automation létrehozásához szükséges megfelelő erőforrásokat, hogy a konvertált runbook megfelelően működjön.

Előfordulhat például, hogy egy runbook változót használ egy tevékenység egy adott értékének feltöltéséhez.  Az átalakított runbook átalakítja a tevékenységet, és megadja Azure Automation változó értékét a Orchestrator változóval megegyező névvel. Ezt a műveletet az átalakítás után létrehozott **Runbook Converter-Summary. log** fájlban kell megállapítani. A runbook használata előtt manuálisan kell létrehoznia ezt a változót az Azure Automationban.

### <a name="work-with-orchestrator-input-parameters"></a>Orchestrator bemeneti paraméterek használata

A Orchestrator runbookok fogadja a bemeneti paramétereket a `Initialize Data` tevékenységgel.  Ha az átalakítás alatt álló runbook tartalmazza ezt a tevékenységet, akkor a rendszer a tevékenység minden paramétere számára létrehoz egy [bemeneti paramétert](automation-graphical-authoring-intro.md#handle-runbook-input) a Azure Automation runbook.  A rendszer létrehoz egy [munkafolyamat-parancsfájl-vezérlési](automation-graphical-authoring-intro.md#use-activities) tevékenységet a konvertált runbook, amely az egyes paramétereket lekéri és visszaadja. A runbook összes olyan tevékenysége, amely egy bemeneti paramétert használ, a tevékenység kimenetére hivatkozik.

Ennek a stratégiának az az oka, hogy a legjobban tükrözze a Orchestrator runbook funkcióit.  Az új grafikus runbookok lévő tevékenységeknek közvetlenül a bemeneti paraméterekre kell hivatkoznia egy Runbook bemeneti adatforrás használatával.

### <a name="invoke-runbook-activity"></a>Runbook-tevékenység meghívása

A runbookok a Orchestrator más runbookok indítanak el a `Invoke Runbook` tevékenységgel. Ha az átalakítás alatt álló runbook tartalmazza ezt a tevékenységet `Wait for completion` , és a beállítás be van állítva, akkor létrejön egy runbook-tevékenység a konvertált runbook.  Ha a `Wait for completion` beállítás nincs megadva, akkor létrejön egy munkafolyamat-parancsfájl tevékenység, amely a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) használatával indítja el a runbook. A konvertált runbook Azure Automationba való importálása után módosítania kell a tevékenységet a tevékenységben megadott adatokkal.

## <a name="create-orchestrator-assets"></a>Orchestrator-eszközök létrehozása

A Runbook-átalakító nem alakítja át a Orchestrator-eszközöket. A szükséges Orchestrator-eszközöket manuálisan kell létrehoznia Azure Automationban.

## <a name="configure-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó konfigurálása

A Orchestrator az adatbázis-kiszolgálón tárolja a runbookok, és a runbook-kiszolgálókon futtatja azokat a helyi adatközpontban. A Azure Automationban lévő runbookok az Azure-felhőben tárolódnak, és a helyi adatközpontban futtathatók [hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md)használatával. Konfiguráljon egy feldolgozót, hogy futtassa a Orchestrator-ből konvertált runbookok, mivel azok helyi kiszolgálókon futnak, és helyi erőforrásokhoz férnek hozzá.

## <a name="related-articles"></a>Kapcsolódó cikkek

* A Orchestrator részleteiért lásd: [System Center 2012-Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* További információ a [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12))szolgáltatásainak kezelésének automatizálásáról.
* A Orchestrator tevékenységek részletei a [Orchestrator standard tevékenységekben](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))találhatók.
* A Orchestrator áttelepítési eszközkészletének beszerzéséhez lásd: a [System Center Orchestrator áttelepítési eszközkészlet letöltése](https://www.microsoft.com/download/details.aspx?id=47323).
* A Azure Automation Hybrid Runbook Worker áttekintését lásd: a [hibrid Runbook Worker áttekintése](automation-hybrid-runbook-worker.md).
