---
title: Azure Automation GYIK | Microsoft Docs
description: Ez a cikk a gyakori kérdésekre ad választ a Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834227"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation gyakori kérdések

Ez a Microsoft GYIK a gyakori kérdések listáját tartalmazza a Azure Automation. Ha további kérdései vannak a képességeivel kapcsolatban, a vitafórumon tegye fel kérdéseit. Ha gyakran feltenek egy kérdést, azt hozzáadjuk ehhez a cikkhez, hogy gyorsan és egyszerűen megtalálható.

## <a name="update-management"></a>Frissítéskezelés

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozhatom a váratlan operációsrendszer-szintű frissítéseket?

Egyes Linux-változatok, például a Red Hat Enterprise Linux, az operációsrendszer-szintű frissítések csomagokon keresztül is előfordulhatnak. Ez ahhoz vezethet, Update Management fut, amelyben az operációs rendszer verziószáma megváltozik. Mivel Update Management a rendszergazdák által linuxos gépeken helyileg használt csomagokat ugyanazokkal a módszerekkel frissítik, ez a viselkedés szándékos.

Ha el kell kerülnie az operációs rendszer verziójának frissítését Update Management központi telepítések során, használja a **Kizárás** funkciót.

A Red Hat Enterprise Linux kizárni a csomag `redhat-release-server.x86_64` neve.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Miért nem alkalmazzák a kritikus/biztonsági frissítéseket?

Amikor frissítéseket telepít egy Linux rendszerű gépen, kiválaszthatja a frissítési besorolásokat. Ez a beállítás szűri a megadott feltételeknek megfelelő frissítéseket. Ez a szűrő helyileg lesz alkalmazva a gépen a frissítés telepítésekor.

Mivel Update Management frissítés gazdagítást végez a felhőben, a Update Management egyes frissítéseit biztonsági hatásként jelölheti meg annak ellenére, hogy a helyi gép nem tartalmaz ilyen információt. Ha kritikus frissítéseket alkalmaz egy Linux rendszerű gépre, előfordulhat, hogy vannak olyan frissítések, amelyek nincsenek biztonsági hatással az adott gépre, ezért nem lesznek alkalmazva. Előfordulhat azonban Update Management hogy a gép nem megfelelőként van jelentve, mert további információkat tartalmaz a vonatkozó frissítésről.

A frissítések frissítésbesorolással történő telepítése nem működik a CentOS RTM-verzióiban. A CentOS frissítésének megfelelő telepítéséhez válassza ki az összes besorolást, és győződjön meg arról, hogy a frissítések alkalmazva vannak. A SUSE esetén a **CSAK** az egyéb frissítések besorolásra való kiválasztása más biztonsági frissítéseket is telepíthet, ha azok a zypperhez (csomagkezelőhöz) vagy annak függőségeihez kapcsolódnak először. Ez a viselkedés a zypper korlátozása. Bizonyos esetekben szükség lehet a frissítéstelepítés újrafuttassa, majd ellenőrizni kell a telepítést a frissítési naplóban.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Üzembe helyezhetek frissítéseket az Azure-bérlők között?

Ha olyan gépekkel rendelkezik, amelyekhez javításra van szükség egy másik Azure-bérlő jelentéskészítési szolgáltatásában Update Management, a következő megkerülő megoldással ütemezni kell őket. Ütemezés létrehozásához használhatja a [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) parancsmagot `ForUpdateConfiguration` a megadott paraméterrel. Használhatja a [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) parancsmagot, és át is használhatja a másik bérlő gépeit a `NonAzureComputer` paraméternek. Az alábbi példa ezt mutatja be.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Folyamatautomatizálás – Python-runbookok

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Melyik Python 3-as verziót támogatja a Azure Automation?

Felhőalapú feladatok esetén a Python 3.8 támogatott. A bármely 3.x verzióból származó szkriptek és csomagok akkor működnek, ha a kód kompatibilis a különböző verziókban.

Hibrid feladatok esetén a Windows hibrid runbook-dolgozók esetében telepítheti a használni kívánt 3.x-es verziókat. Hibrid feladatok linuxos hibrid runbook-feldolgozókon való futtatásához a DSC OMSConfig és a Linux hibrid feldolgozó futtatásához a gépen telepített Python 3-as verzióra van szükségünk. Javasoljuk, hogy telepítse a 3.6-os verziót; A különböző verzióknak azonban akkor is működniük kell, ha nincsenek a metódusaláírások vagy a Python 3 verziói közötti szerződésekben.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Futtathatók Python 2- és Python 3-runbookok ugyanabban az Automation-fiókban?

Igen, a Python 2 és a Python 3 runbookok ugyanabban az Automation-fiókban való használatának nincs korlátozása.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Mi a terv a meglévő Python 2-runbookok és -csomagok Python 3-ra való áttelepítésére?

Azure Automation a Python 2-runbookok és -csomagok Python 3-ra való áttelepítését nem tervezi. Ezt a migrálást önnek kell elvégeznie. A meglévő és az új Python 2-runbookok és -csomagok továbbra is működni fognak.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Alapértelmezés szerint milyen csomagok támogatottak Python 3-környezetben?

Az Azure 4.0.0-s csomag alapértelmezés szerint telepítve van a Python 3 Automation-környezetben. Manuálisan importálhatja az Azure-csomag egy magasabb verzióját az alapértelmezett verzió felülbírálásaként.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Mi történik, ha olyan Python 3-runbookot futtatok, amely egy Python 2-csomagra hivatkozik, vagy fordítva?

A Python 2 és a Python 3 különböző végrehajtási környezetekkel is működik. Amíg egy Python 2-runbook fut, csak Python 2-csomagok importálhatók és hasonlók a Python 3-hoz.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Hogyan megkülönböztetni a Python 2-t és a Python 3-runbookokat és -csomagokat?

A Python 3 egy új runbookdefiníció, amely különbséget tesz a Python 2 és a Python 3 runbookok között. Ehhez hasonlóan a Python 3-csomagokhoz is bevezettünk egy másik csomagcsomagot.

## <a name="next-steps"></a>Következő lépések

Ha itt nem talál választ a kérdésére, további kérdésekért és válaszokért tekintse meg az alábbi forrásokat.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Visszajelzési fórum](https://feedback.azure.com/forums/905242-update-management)
