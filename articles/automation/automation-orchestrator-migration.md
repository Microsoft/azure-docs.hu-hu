---
title: Migrálás az Orchestratorből az Azure Automationbe (bétaverzió)
description: Ez a cikk bemutatja, hogyan migrálhat runbookokat és integrációs csomagokat az Orchestratorból a Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c929781772b510639e1e5e47eed19927f408d16a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830501"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrálás az Orchestratorből az Azure Automationbe (bétaverzió)

A System Center [2012](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) – Orchestrator forgatókönyvei kifejezetten az Orchestrator számára írt integrációs csomagok tevékenységein alapulnak, míg a Azure Automation forgatókönyvei Windows PowerShell. [A grafikus runbookok](automation-runbook-types.md#graphical-runbooks) hasonló Azure Automation, mint az Orchestrator-runbookok, és tevékenységeik PowerShell-parancsmagokat, gyermek runbookokat és eszközöket képviselnek. A runbookok átalakítása mellett a runbookok által használt tevékenységekkel együtt kell átalakítania az integrációs csomagokat az integrációs modulokká Windows PowerShell parancsmagokkal. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) runbookokat tárol és futtat a helyi adatközpontban, például az Orchestratorban, és ugyanazt az integrációs modult használja, mint Azure Automation. A Runbook Converter az Orchestrator-runbookokat grafikus runbookokká alakítja, amelyeket az SMA nem támogat. Továbbra is telepítheti a Standard tevékenységek modult, és System Center Az Orchestrator integrációs moduljait az SMA-ba, de manuálisan át kell írnia a [forgatókönyveket.](/system-center/sma/authoring-automation-runbooks)

## <a name="download-the-orchestrator-migration-toolkit"></a>Az Orchestrator migrálási eszközkészletének letöltése

A migrálás első lépéseként töltse le a System Center [Orchestrator Migration Toolkit eszközt.](https://www.microsoft.com/download/details.aspx?id=47323) Ez az eszközkészlet olyan eszközöket tartalmaz, amelyek segítséget nyújtanak a runbookok Orchestratorról Azure Automation.  

## <a name="import-the-standard-activities-module"></a>A Standard tevékenységek modul importálása

Importálja [a Standard tevékenységek modult a](/system-center/orchestrator/standard-activities) Azure Automation. Ez magában foglalja a szabványos Orchestrator-tevékenységek konvertált verzióit, amelyek a konvertált grafikus runbookokat használhatják.

## <a name="import-orchestrator-integration-modules"></a>Orchestrator-integrációs modulok importálása

A Microsoft [integrációs csomagokat](/previous-versions/system-center/packs/hh295851(v=technet.10)) biztosít a runbookok automatizálható System Center összetevők és egyéb termékek automatizálására. Ezen integrációs csomagok némelyike jelenleg OIT-alapú, de ismert problémák miatt jelenleg nem konvertálható integrációs modulokká. Importálja [System Center Orchestrator-integrációs](https://www.microsoft.com/download/details.aspx?id=49555) modulokat Azure Automation a runbookok által használt integrációs csomagok System Center. Ez a csomag tartalmazza az integrációs csomagok konvertált verzióit, amelyek importálhatók a Azure Automation és Service Management Automation.  

## <a name="convert-integration-packs"></a>Integrációs csomagok konvertálása

Az [Integration Pack Converter](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) használatával az Orchestrator Integration Toolkit [(OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) használatával létrehozott integrációs csomagokat PowerShell-alapú integrációs modulokká konvertálhatja, amelyek importálhatók Azure Automation vagy Service Management Automation. Az Integration Pack Converter futtatásakor jelennek meg egy varázsló, amely lehetővé teszi egy integrációs csomag (.oip) fájl kiválasztását. A varázsló ezután felsorolja az integrációs csomagban található tevékenységeket, és lehetővé teszi az átemelni kívánt tevékenységek kiválasztását. A varázsló befejezésekor létrejön egy integrációs modul, amely tartalmazza az eredeti integrációs csomagban található összes tevékenységhez tartozó parancsmagot.

> [!NOTE]
> Az Integration Pack Converter nem használható az OIT-val nem létrehozott integrációs csomagok átalakítására. Íme néhány, a Microsoft által biztosított integrációs csomag, amelyet jelenleg nem lehet konvertálni ezzel az eszközzel. Ezeknek az integrációs csomagoknak a konvertált verziói letölthetők, így telepíthetők Azure Automation vagy Service Management Automation.

### <a name="parameters"></a>Paraméterek

Az integrációs csomagban található tevékenységek bármely tulajdonsága az integrációs modulban a megfelelő parancsmag paramétereire lesz konvertálva.  Windows PowerShell parancsmagok közös paraméterkészletekkel [](/powershell/module/microsoft.powershell.core/about/about_commonparameters) rendelkezik, amelyek az összes parancsmaggal használhatók. Például a -Verbose paraméter hatására egy parancsmag részletes információkat ad ki a műveletről.  Egyetlen parancsmagnak sem lehet közös paraméterrel azonos nevű paramétere. Ha egy tevékenység rendelkezik olyan tulajdonsággal, amely neve megegyezik egy közös paraméter nevével, a varázsló kérni fogja, hogy adjon meg egy másik nevet a paraméterhez.

### <a name="monitor-activities"></a>Tevékenységek monitorozása

Az Orchestrator runbookok figyelése egy figyelő tevékenységgel kezdődik, és folyamatosan fut egy adott esemény meghívására várva. [](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) Azure Automation nem támogatja a runbookok figyelését, így az integrációs csomagban nem konvertálja a figyelt tevékenységeket. Ehelyett a rendszer létrehoz egy helyőrző parancsmagot az integrációs modulban a figyelő tevékenységhez.  Ez a parancsmag nem rendelkezik funkcióval, de lehetővé teszi az azt használó konvertált runbookok telepítését. Ez a runbook nem futtatható a Azure Automation, de telepíthető a módosításához.

Az Orchestrator olyan [](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) szabványos tevékenységeket tartalmaz, amelyek nem szerepelnek az integrációs csomagokban, de sok runbook használja azokat.  A Standard tevékenységek modul egy integrációs modul, amely tartalmazza az egyes tevékenységekhez megfelelő parancsmagot. Ezt az integrációs modult a Azure Automation kell telepítenie, mielőtt importálja a szabványos tevékenységet használó konvertált runbookokat.

A konvertált runbookok támogatása mellett a szabványos tevékenységek modul parancsmagját egy, az Orchestratort jól küldő ismerős is használhatja az új runbookok Azure Automation. Bár az összes szabványos tevékenység működése elvégezhető parancsmagokkal, ezek eltérő módon működhetnek. A konvertált szabványos tevékenységek modul parancsmagja ugyanúgy működik, mint a hozzájuk tartozó tevékenységek, és ugyanazok a paraméterek használhatók. Ez segíthet a runbookok Azure Automation való áttérésben.

## <a name="convert-orchestrator-runbooks"></a>Orchestrator-runbookok konvertálása

Az Orchestrator Runbook Converter az Orchestrator-runbookokat grafikus [runbookokká](automation-runbook-types.md#graphical-runbooks) alakítja, amelyek importálhatók a Azure Automation. A Runbook Converter PowerShell-modulként van megvalósítva az átalakítást küldő `ConvertFrom-SCORunbook` parancsmaggal. Az átalakító telepítésekor létrejön egy parancsikon egy PowerShell-munkamenethez, amely betölti a parancsmagot.   

A runbookok konvertálásának és a runbookok importálásának alapvető lépései a Azure Automation. A parancsmag használatával kapcsolatos részleteket a szakasz későbbi részében talál.

1. Exportálhat egy vagy több runbookot az Orchestratorból.
2. Integrációs modulok beszerzése a runbook összes tevékenységéhez.
3. Konvertálja az Orchestrator-runbookokat az exportált fájlban.
4. Tekintse át a naplókban található információkat az átalakítás ellenőrzéséhez és a szükséges manuális feladatok meghatározásához.
5. Importálja a konvertált runbookokat Azure Automation.
6. Hozza létre a szükséges Azure Automation.
7. Szerkessze a runbookot a Azure Automation a szükséges tevékenységek módosításához.

A `ConvertFrom-SCORunbook` szintaxisa a következő:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – A konvertálni kívánt runbookokat tartalmazó exportálási fájl elérési útja.
* Modul – A runbookokban tevékenységeket tartalmazó integrációs modulok vesszővel tagolt listája.
* OutputFolder – A konvertált grafikus runbookok létrehozásához használt mappa elérési útja.

A következő példaparancs egy nevű exportálási fájlban konvertálja a **runbookokat MyRunbooks.ois_export.**  Ezek a runbookok az integrációs Active Directory és Data Protection Manager használják.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Runbook Converter naplófájlok használata

A Runbook Converter az alábbi naplófájlokat a konvertált runbook helyével azonos helyen hozza létre.  Ha a fájlok már léteznek, a rendszer felülírja őket az utolsó átalakításból származó információkkal.

| Fájl | Tartalom |
|:--- |:--- |
| Runbook Converter – Progress.log |Az átalakítás részletes lépései, beleértve az egyes sikeresen konvertált tevékenységekre vonatkozó információkat és a nem konvertált tevékenységekre vonatkozó figyelmeztetést. |
| Runbook Converter – Summary.log |Az utolsó átalakítás összegzése, beleértve a szükséges figyelmeztetéseket és követő feladatokat, például a konvertált runbookhoz szükséges változó létrehozását. |

### <a name="export-runbooks-from-orchestrator"></a>Runbookok exportálása az Orchestratorból

A Runbook Converter az Orchestrator egy vagy több runbookot tartalmazó exportálási fájlját is tartalmazza.  Létrehoz egy megfelelő Azure Automation runbookot az exportálási fájlban az egyes Orchestrator-runbookok számára.  

Ha runbookot exportál az Orchestratorból, kattintson a jobb gombbal a runbook nevére a Runbook Designer **exportálása lehetőséget.**  Egy mappa összes runbookjának exportáláshoz kattintson a jobb gombbal a mappa nevére, és válassza az **Exportálás lehetőséget.**

### <a name="convert-runbook-activities"></a>Runbook-tevékenységek konvertálása

A Runbook Converter az Orchestrator-runbook minden tevékenységét egy megfelelő tevékenységgé alakítja a Azure Automation.  A nem konvertálható tevékenységekhez egy helyőrző tevékenység jön létre a runbookban figyelmeztető szöveggel.  Miután importálta a konvertált runbookot Azure Automation, ezeket a tevékenységeket olyan érvényes tevékenységekre kell cserélnie, amelyek elvégzik a szükséges funkciókat.

A standard tevékenységek modulban található összes Orchestrator-tevékenység át lesz alakítva. Vannak azonban olyan szabványos Orchestrator-tevékenységek, amelyek nem ebben a modulban vannak, és nincsenek konvertálva. A például `Send Platform Event` nem rendelkezik megfelelő Azure Automation, mivel az esemény csak az Orchestratorra vonatkozik.

[A figyelő](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) tevékenységek nincsenek konvertálva, mivel a figyelőben nem egyezik Azure Automation. A kivételek a helyőrző tevékenységgé konvertált integrációs csomagokban lévő tevékenységek figyelése.

A konvertált integrációs csomag minden tevékenysége konvertálva lesz, ha meg van adva az integrációs modul elérési útja a `modules` paraméterrel. A System Center csomagokhoz használhatja az System Center Orchestrator integrációs moduljait.

### <a name="manage-orchestrator-resources"></a>Orchestrator-erőforrások kezelése

A Runbook Converter csak runbookokat konvertál, más Orchestrator-erőforrásokat, például számlálókat, változókat vagy kapcsolatokat nem.  A számlálók nem támogatottak a Azure Automation.  A változók és kapcsolatok támogatottak, de manuálisan kell létrehozni őket. A naplófájlok értesítik, ha a runbooknak ilyen erőforrásokra van szüksége, és meg kell adnia a megfelelő erőforrásokat, amelyek a Azure Automation a konvertált runbook megfelelő működéséhez.

Előfordulhat például, hogy egy runbook változóval tölti fel egy tevékenység egy adott értékét.  A konvertált runbook konvertálja a tevékenységet, és megad egy változóeszközt a Azure Automation az Orchestrator változóval azonos névvel. Ez a művelet az átalakítás után létrehozott **Runbook Converter – Summary.log** fájlban van feljegyezve. Ezt a változóeszközt manuálisan kell létrehoznia Azure Automation a runbook használata előtt.

### <a name="work-with-orchestrator-input-parameters"></a>Az Orchestrator bemeneti paramétereinek használatával

Az Orchestrator runbookai bemeneti paramétereket fogadnak el a `Initialize Data` tevékenységgel együtt.  Ha a konvertált runbook tartalmazza ezt [a](automation-graphical-authoring-intro.md#handle-runbook-input) tevékenységet, akkor a rendszer létrehoz egy bemeneti paramétert a Azure Automation runbookban a tevékenység minden paraméteréhez.  A [munkafolyamat-szkript vezérlőtevékenysége](automation-graphical-authoring-intro.md#use-activities) a konvertált runbookban jön létre, amely lekéri és visszaadja az egyes paramétereket. A runbookban a bemeneti paramétert használó összes tevékenység a tevékenység kimenetére hivatkozik.

Ennek a stratégiának az az oka, hogy a legjobban tükrözi az Orchestrator-runbook funkcióit.  Az új grafikus runbookok tevékenységeinek közvetlenül kell hivatkozni a bemeneti paraméterekre egy Runbook bemeneti adatforrás használatával.

### <a name="invoke-runbook-activity"></a>Runbook-tevékenység meghívása

Az Orchestrator runbookai más runbookokat is elindítnak a `Invoke Runbook` tevékenységgel. Ha a konvertálni kívánt runbook tartalmazza ezt a tevékenységet, és be van állítva a beállítás, akkor a rendszer létrehoz hozzá egy Runbook-tevékenységet a `Wait for completion` konvertált runbookban.  Ha a `Wait for completion` beállítás nincs beállítva, akkor létrejön egy munkafolyamat-szkript tevékenység, amely a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) használatával indítja el a runbookot. Miután importálta a konvertált runbookot Azure Automation, ezt a tevékenységet a tevékenységben megadott információkkal kell módosítania.

## <a name="create-orchestrator-assets"></a>Orchestrator-eszközök létrehozása

A Runbook Converter nem konvertálja az Orchestrator-eszközöket. A szükséges Orchestrator-eszközöket manuálisan kell létrehoznia a Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Hibrid runbook-feldolgozó konfigurálása

Az Orchestrator egy adatbázis-kiszolgálón tárolja a runbookokat, és runbook-kiszolgálókon futtatja őket, mind a helyi adatközpontban. A Azure Automation a runbookok az Azure-felhőben vannak tárolva, és egy hibrid runbook-feldolgozóval futtathatók a helyi [adatközpontban.](automation-hybrid-runbook-worker.md) Konfigurálja a feldolgozót az Orchestratorról konvertált runbookok futtatására, mivel helyi kiszolgálókon való futtatásra és helyi erőforrások eléréséhez tervezték őket.

## <a name="related-articles"></a>Kapcsolódó cikkek

* Az Orchestrator részleteiért [lásd: System Center 2012 – Orchestrator.](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))
* További információ a szolgáltatások felügyeletének automatizálásról a [Service Management Automation.](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12))
* Az Orchestrator-tevékenységek részletei az [Orchestrator Standard-tevékenységeit tartalmazó részen találhatók.](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))
* Az Orchestrator migrálási eszközkészlet beszerzéséhez lásd: Download System Center Orchestrator Migration Toolkit ( Az [Orchestrator migration Toolkit letöltése).](https://www.microsoft.com/download/details.aspx?id=47323)
* A hibrid runbook-feldolgozó Azure Automation áttekintését lásd: Hibrid [runbook-feldolgozó áttekintése.](automation-hybrid-runbook-worker.md)
