---
title: Azure Automation GYIK | Microsoft Docs
description: Ez a cikk válaszokat ad a Azure Automationekkel kapcsolatos gyakori kérdésekre.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724272"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation gyakori kérdések

A Microsoft gyakori kérdései a Azure Automationkal kapcsolatos gyakori kérdések listája. Ha bármilyen egyéb kérdése van a képességeivel kapcsolatban, látogasson el a vitafórumra, és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, így gyorsan és egyszerűen megtalálhatja.

## <a name="update-management"></a>Frissítéskezelés

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozható a váratlan operációsrendszer-szintű frissítések?

Bizonyos Linux-változatoknál, például a Red Hat Enterprise Linux, az operációsrendszer-szintű frissítések a csomagokon keresztül is előfordulhatnak. Ennek eredményeként előfordulhat, hogy Update Management fut, amelyben az operációs rendszer verziószáma megváltozik. Mivel a Update Management ugyanazokat a módszereket használja, mint például a rendszergazdák által a Linux rendszerű gépen helyileg használt csomagok frissítése, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának Update Management központi telepítéseken keresztüli frissítését, használja a **kizárás** funkciót.

Red Hat Enterprise Linux a kizárni kívánt csomag neve `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Miért nem alkalmaz kritikus/biztonsági frissítéseket?

Ha Linux rendszerű gépre telepít frissítéseket, válassza a frissítési besorolások lehetőséget. Ez a beállítás a megadott feltételeknek megfelelő frissítéseket szűri. Ezt a szűrőt a rendszer helyileg alkalmazza a számítógépen a frissítés telepítésekor.

Mivel a Update Management a felhőben frissíti a frissítés bővítését, biztonsági hatással lehet Update Management egyes frissítéseire, még akkor is, ha a helyi gép nem rendelkezik ezekkel az információkkal. Ha a kritikus frissítéseket egy Linux rendszerű gépre alkalmazza, előfordulhat, hogy olyan frissítések vannak, amelyek nincsenek megjelölve biztonsági hatással a gépre, ezért nem alkalmazhatók. Előfordulhat azonban, hogy Update Management még mindig nem megfelelőként jelenti a gépet, mert további információkra van szükség a megfelelő frissítésről.

A frissítések besorolás szerinti központi telepítése nem működik a CentOS RTM-verzióin. A CentOS-frissítések megfelelő telepítéséhez válassza a minden besorolás lehetőséget, hogy a rendszer a frissítéseket alkalmazza. A SUSE esetében, ha csak **más frissítések** vannak kiválasztva, mert a besorolás más biztonsági frissítéseket is telepíthet, ha a Zypper (csomagkezelő) vagy annak függőségeire van szükség. Ez a viselkedés a Zypper korlátozása. Bizonyos esetekben előfordulhat, hogy újra kell futtatnia a frissítés központi telepítését, majd ellenőriznie kell a központi telepítést a frissítési naplóban.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Telepíthetek frissítéseket az Azure-bérlők között?

Ha olyan gépekkel rendelkezik, amelyeknek egy másik Azure-Bérlőnek kell bejelentkezniük Update Management, akkor a következő megkerülő megoldást kell használnia az ütemezett kezdéshez. A [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) parancsmagot használhatja a `ForUpdateConfiguration` megadott paraméterrel az ütemterv létrehozásához. Használhatja a [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) parancsmagot, és átadhatja a többi bérlőben lévő gépeket a `NonAzureComputer` paraméternek. Az alábbi példa azt szemlélteti, hogyan teheti ezt meg.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Folyamatok automatizálása – Python-runbookok

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Melyik Python 3 verziót támogatja a Azure Automation?

A felhőalapú feladatokhoz a Python 3,8 támogatott. A 3. x verzióból származó parancsfájlok és csomagok akkor működhetnek, ha a kód a különböző verziókon is kompatibilis.

Hibrid feladatokhoz Windows Hybrid Runbook-feldolgozók esetén a használni kívánt 3. x verziót is telepítheti. A Linux hibrid Runbook-feldolgozókon futó hibrid feladatokhoz a számítógépen telepített Python 3 verziójára a DSC OMSConfig és a Linux Hybrid Worker futtatására van szükség. Javasoljuk, hogy telepítse a 3,6-es verziót; azonban a különböző verzióknak is működniük kell, ha a metódus aláírása vagy a Python 3 verziói között nem történt meg a változások megszakítása.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>A Python 2 és a Python 3 runbookok ugyanazon Automation-fiókban futnak?

Igen, nincs korlátozás a Python 2 és a Python 3 runbookok azonos Automation-fiókban való használatára.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Mi a terv a meglévő Python 2 runbookok és csomagok a Python 3 rendszerbe való áttelepítésére?

Azure Automation nem tervezi a Python 2 runbookok és csomagok áttelepíteni a Python 3-ra. Ezt az áttelepítést saját kezűleg kell végrehajtania. A meglévő és az új Python 2 runbookok és csomagok továbbra is működni fognak.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Milyen csomagok támogatottak alapértelmezés szerint a Python 3 környezetben?

Az Azure Package 4.0.0 alapértelmezés szerint telepítve van a Python 3 Automation-környezetben. Az Azure-csomag magasabb verzióját manuálisan is importálhatja az alapértelmezett verzió felülbírálásához.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Mi a teendő, ha olyan Python 3 runbook futtatok, amely egy Python 2 csomagra hivatkozik, vagy fordítva?

A Python 2 és a Python 3 különböző végrehajtási környezettel rendelkezik. Míg a Python 2 runbook fut, csak a Python 2 csomagok importálhatók és hasonlók a Python 3-hoz.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Hogyan különbséget tenni a Python 2 és a Python 3 runbookok és csomagok között?

A Python 3 egy új runbook-definíció, amely megkülönbözteti a Python 2 és a Python 3 runbookok között. Hasonlóképpen, a Python 3 csomagok esetében egy másik Package-típus is elérhető.

## <a name="next-steps"></a>Következő lépések

Ha a kérdés itt nem válaszol, az alábbi forrásokra kattintva további kérdéseket és válaszokat kaphat.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Visszajelzési fórum](https://feedback.azure.com/forums/905242-update-management)
