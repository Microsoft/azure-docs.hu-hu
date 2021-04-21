---
title: Grafikus runbookok hibáinak Azure Automation kezelhetők
description: Ez a cikk bemutatja, hogyan implementálja a hibakezelési logikát a grafikus runbookban.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00ed73a1b95558f256caa01ad8eafbefe11b0f32
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830483"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Hibák kezelése a grafikus runbookokban

A grafikus Azure Automation tervezésének egyik fő alapelve a runbook végrehajtása során esetleg tapasztalt problémák azonosítása. Az ilyen helyzetek közé tartoznak a sikeres műveletek, a várt hibaállapotok és a váratlan hibafeltételek.

Gyakran előfordul, hogy ha egy Runbook-tevékenység megszakítást nem követő hiba lép fel, a Windows PowerShell a következő tevékenységek feldolgozásával kezeli a tevékenységet, a hibától függetlenül. A hiba valószínűleg kivételt hoz létre, a lényeg azonban az, hogy a következő tevékenység is lefuthat.

A grafikus runbooknak tartalmaznia kell hibakezelő kódot a végrehajtási problémák kezeléshez. Egy tevékenység kimenetének ellenőrzéséhez vagy egy hiba kezeléséhez használhat egy PowerShell-kódtevékenységet, definiálhat feltételes logikát a tevékenység kimeneti hivatkozásán, vagy alkalmazhat egy másik metódust.

Az Azure Automation grafikus runbookok kiegészültek a hibakezelés képességével is. A kivételeket mostantól nem megszakító hibákká változtathatja, valamint hibahivatkozásokat hozhat létre a tevékenységek között. A továbbfejlesztett folyamat lehetővé teszi, hogy a runbook felismerjék a hibákat, és kezelni tudja a felismert vagy váratlan feltételeket. 

## <a name="powershell-error-types"></a>PowerShell-hibatípusok

A Runbook végrehajtása során előforduló PowerShell-hibák a megszakítást és a megszakítás nélküli hibákat jelentik.
 
### <a name="terminating-error"></a>Megszakítást jelező hiba

A megszakítást okozó hiba a végrehajtás során súlyos hiba, amely teljesen leállítja a parancsok vagy szkriptek végrehajtását. Ilyenek például a nem létező parancsmagok, a parancsmagok futását megakadályozó szintaktikai hibák és egyéb végzetes hibák.

### <a name="non-terminating-error"></a>Megszakítást nem meg nem végződő hiba

A megszakítást nem okozó hiba nem súlyos hiba, amely lehetővé teszi a végrehajtás folytatását a hiba feltétele ellenére. Ilyenek például a működési hibák, például a fájl nem található hibái és az engedélyekkel kapcsolatos problémák.

## <a name="when-to-use-error-handling"></a>Mikor érdemes hibakezelést használni?

Akkor használjon hibakezelést a runbookban, ha egy kritikus tevékenység hibát vagy kivételt jelez. Fontos, hogy megakadályozza a runbook következő tevékenységének feldolgozását, és megfelelően kezelje a hibát. A hiba kezelése különösen akkor kritikus fontosságú, ha a runbookok üzleti vagy szolgáltatási üzemeltetési folyamatot támogatnak.

## <a name="add-error-links"></a>Hibahivatkozások hozzáadása

Minden olyan tevékenységhez, amely hibát jelezhet, hozzáadhat egy hibahivatkozást, amely bármely más tevékenységre mutat. A céltevékenység bármilyen típusú lehet, beleértve a kódtevékenységet, a parancsmagok meghívását, egy másik runbook meghívását stb. A céltevékenység kimenő hivatkozásokat is tartalmaz, normál vagy hibahivatkozásokkal. A hivatkozások lehetővé teszik, hogy a runbook összetett hibakezelési logikát implementálja anélkül, hogy kódtevékenységre vált volna.

Az ajánlott eljárás egy általános funkciókkal használható, dedikált hibakezelési runbook létrehozása, de ez a gyakorlat nem kötelező. Tekintsünk például egy runbookot, amely megpróbál elindítani egy virtuális gépet, és telepíteni rajta egy alkalmazást. Ha a virtuális gép nem indul el megfelelően, a következőt kell használnia:

1. Értesítést küld erről a problémáról.
2. Elindít egy másik runbookot, amely automatikusan kiad egy új virtuális gépet.

Az egyik megoldás az, hogy a runbookban található hibahivatkozás egy olyan tevékenységre mutat, amely az első lépést kezeli. A runbook például csatlakoztathatja a parancsmagot a második lépéshez egy tevékenységhez, például a `Write-Warning` [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) parancsmaghoz.

Ezt a viselkedést számos runbookban való használatra is általánosíthatja, ha ezt a két tevékenységet külön hibakezelési runbookba szervezi. Mielőtt az eredeti runbook ezt a hibakezelési runbookot hívja meg, létrehozhat egy egyéni üzenetet az adataiból, majd paraméterként továbbadhatja a hibakezelési runbooknak.

## <a name="turn-exceptions-into-non-terminating-errors"></a>A kivételek megszakítást nem végződő hibákat okozhatnak

A runbook minden tevékenysége rendelkezik egy konfigurációs beállítással, amely a kivételeket megszakítást nem végződő hibákkal váltja fel. Alapértelmezés szerint ez a beállítás le van tiltva. Javasoljuk, hogy ezt a beállítást minden olyan tevékenységhez engedélyeznie kell, amelyben a runbook kezeli a hibákat. Ez a beállítás biztosítja, hogy a runbook megszakítást nem végződő hibákként kezelje a tevékenység megszakítást nem végződő hibáit egy hibahivatkozás használatával.  

A konfigurációs beállítás engedélyezése után a runbooknak létre kell hoznia egy tevékenységet, amely kezeli a hibát. Ha a tevékenység hibát jelez, a rendszer a kimenő hibahivatkozásokat követi. A normál hivatkozások nem követik, még akkor sem, ha a tevékenység normál kimenetet is hoz létre.<br><br> ![Példa egy Automation runbook hibahivatkozásra](media/automation-runbook-graphical-error-handling/error-link-example.png)

A következő példában egy runbook lekér egy változót, amely egy virtuális gép számítógépnevét tartalmazza. Ezután megkísérli elindítani a virtuális gépet a következő tevékenységgel.<br><br> ![Példa Automation-runbook hibakezelésre](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A `Get-AutomationVariable` tevékenység és a [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) parancsmag úgy van konfigurálva, hogy a kivételeket hibákká konvertálja. Ha probléma merül fel a változó lekért vagy a virtuális gép indítása során, a kód hibákat generál.<br><br> ![Automation-runbook hibakezelési ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) tevékenységbeállítása.

A hibahivatkozások ezekből a tevékenységekből egyetlen `error management` kódtevékenységbe áramlnak. Ez a tevékenység egy egyszerű PowerShell-kifejezéssel van konfigurálva, amely a kulcsszóval leállítja a feldolgozást, valamint lekérte az aktuális `throw` `$Error.Exception.Message` kivételt leíró üzenetet.<br><br> ![Példa Automation-runbook hibakezelési kódra](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Következő lépések

* További információ a grafikus runbookhibák megoldásáról: [Runbookokkal kapcsolatos problémák elhárítása.](troubleshoot/runbooks.md)
