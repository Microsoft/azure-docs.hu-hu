---
title: Hibák kezelése Azure Automation grafikus runbookok
description: Ez a cikk azt ismerteti, hogyan valósítható meg a hibakezelés logikája a grafikus runbookok.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 24c7aaf08b4d22706bee8f37025b12a656ceaff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895900"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Hibák kezelése a grafikus runbookokban

A Azure Automation grafikus runbook figyelembe venni kívánt alapvető tervezési elv annak a problémának az azonosítása, amelyet a runbook a végrehajtás során felmerülhet. Az ilyen helyzetek közé tartoznak a sikeres műveletek, a várt hibaállapotok és a váratlan hibafeltételek.

Gyakran előfordul, hogy ha egy runbook-tevékenységgel kapcsolatos megszakítást nem okozó hiba történik, a Windows PowerShell az alábbi tevékenységek feldolgozásával kezeli a tevékenységet, függetlenül attól, hogy milyen hiba történt. A hiba valószínűleg kivételt hoz létre, a lényeg azonban az, hogy a következő tevékenység is lefuthat.

A grafikus runbook tartalmaznia kell a hibák kezelésére szolgáló kódot a végrehajtási problémák kezeléséhez. Ha ellenőrizni szeretné egy tevékenység kimenetét, vagy hibát észlel, használhat PowerShell-kód tevékenységet, feltételes logikát definiálhat a tevékenység kimeneti hivatkozására, vagy alkalmazhat egy másik módszert.

Az Azure Automation grafikus runbookok kiegészültek a hibakezelés képességével is. A kivételeket mostantól nem megszakító hibákká változtathatja, valamint hibahivatkozásokat hozhat létre a tevékenységek között. A továbbfejlesztett folyamat lehetővé teszi a runbook számára a hibák észlelését és a realizált vagy váratlan feltételek kezelését. 

## <a name="powershell-error-types"></a>PowerShell-hibák típusai

A runbook végrehajtása során fellépő PowerShell-hibák típusai leállnak a hibák és a megszakítást nem okozó hibák.
 
### <a name="terminating-error"></a>Megszakítási hiba

A leállítási hiba súlyos hiba a végrehajtás során, amely teljesen megszakítja a parancs vagy a parancsfájl végrehajtását. Ilyenek például a nem létező parancsmagok, a parancsmag futását megakadályozó szintaktikai hibák és egyéb végzetes hibák.

### <a name="non-terminating-error"></a>Megszakítást nem okozó hiba

A megszakítást nem okozó hiba egy nem súlyos hiba, amely lehetővé teszi, hogy a végrehajtás a hiba feltétele ellenére is folytatódjon. Ilyenek például a működési hibák, például a fájl nem található hibák és az engedélyek problémái.

## <a name="when-to-use-error-handling"></a>Mikor érdemes hibakezelést használni?

Ha egy kritikus tevékenység hibát vagy kivételt jelez, használja a runbook a hibakezelés során. Fontos, hogy megakadályozza a runbook következő tevékenységének feldolgozását, és megfelelően kezelni a hibát. A hiba kezelése különösen kritikus fontosságú, ha a runbookok üzleti vagy szolgáltatási műveleti folyamatot támogat.

## <a name="add-error-links"></a>Hibaüzenetek hozzáadása

Minden olyan tevékenységnél, amely hibát okozhat, bármilyen más tevékenységre mutató hibaüzenetet adhat hozzá. A célként megadott tevékenység bármilyen típusú lehet, beleértve a kód tevékenységét, a parancsmag meghívását, egy másik runbook meghívását stb. A célként megadott tevékenység kimenő hivatkozásokat is tartalmazhat, amelyek rendszeres vagy hibás hivatkozásokat tartalmaznak. A hivatkozások lehetővé teszik, hogy a runbook a kód tevékenysége nélkül implementálja az összetett hibák kezelésére szolgáló logikát.

Az ajánlott eljárás egy dedikált hibakezelés runbook létrehozása közös funkciókkal, de ez a gyakorlat nem kötelező. Vegyünk például egy olyan runbook, amely megpróbál elindítani egy virtuális gépet, és telepít egy alkalmazást. Ha a virtuális gép nem indul el megfelelően, a következőket kell tennie:

1. Értesítés küldése erről a problémáról.
2. Elindítja egy másik runbook, amely automatikusan kiépít egy új virtuális gépet.

Az egyik megoldás az, ha a runbook egy olyan tevékenységre mutat, amely az első lépést kezeli. A runbook például összekapcsolhatja a `Write-Warning` parancsmagot egy tevékenységgel a második lépésben, például a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) parancsmaggal.

Ezt a viselkedést is általánosíthatja számos runbookok való használathoz azáltal, hogy ezt a két tevékenységet külön hibakezelés runbook. Mielőtt az eredeti runbook meghívja ezt a hibát a runbook kezelésében, létrehozhat egy egyéni üzenetet az adataiból, majd átadhatja azt paraméterként a runbook kezeléséhez.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Kivételek bekapcsolása megszakítás nélküli hibákra

A runbook minden tevékenysége rendelkezik olyan konfigurációs beállítással, amely a kivételeket a nem lezáró hibákra kapcsolja. Alapértelmezés szerint ez a beállítás le van tiltva. Azt javasoljuk, hogy engedélyezze ezt a beállítást minden olyan tevékenységnél, amelynél a runbook kezeli a hibákat. Ezzel a beállítással biztosíthatja, hogy a runbook a tevékenységek megszakításával és megszakításával kapcsolatos hibákat a nem megszakítást okozó hibaként kezelje.  

A konfigurációs beállítás engedélyezése után a runbook létrehozhat egy tevékenységet, amely kezeli a hibát. Ha a tevékenység hibát okoz, a rendszer a kimenő hibák hivatkozásait követi. A normál kapcsolatok nem lesznek követve, még akkor is, ha a tevékenység a normál kimenetet is létrehozza.<br><br> ![Példa egy Automation runbook hibahivatkozásra](media/automation-runbook-graphical-error-handling/error-link-example.png)

A következő példában egy runbook egy olyan változót kér le, amely egy virtuális gép számítógépnevét tartalmazza. Ezután megpróbálja elindítani a virtuális gépet a következő tevékenységgel.<br><br> ![Automatizálási runbook – példa](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A `Get-AutomationVariable` tevékenység és a [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) parancsmag úgy van konfigurálva, hogy a kivételeket a hibákra konvertálja. Ha problémák merülnek fel a változó beszerzése vagy a virtuális gép elindítása során, a kód hibákat generál.<br><br> ![Automation runbook hiba – kezelési tevékenység beállításai ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) .

Hiba történt a folyamatok ezen tevékenységekből egyetlen `error management` kód tevékenységre való csatolásakor. Ez a tevékenység egy egyszerű PowerShell-kifejezéssel van konfigurálva, amely a `throw` kulcsszó használatával leállítja a feldolgozást, valamint az `$Error.Exception.Message` aktuális kivételt leíró üzenet beszerzéséhez.<br><br> ![Automatizálási runbook – példa a hibakód kezelésére](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Következő lépések

* A grafikus runbook kapcsolatos hibák elhárításával kapcsolatos további információkért lásd: [runbook kapcsolatos problémák](troubleshoot/runbooks.md)megoldása.
