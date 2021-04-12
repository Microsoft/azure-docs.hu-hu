---
title: Azure Automation Start/Stop VMs during off-hours problémák elhárítása
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Start/Stop VMs during off-hours funkció használata során felmerülő problémákat.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: f6d2696a68643f87de0fcaf2e723da9365d9953e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953850"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Start/Stop VMs during off-hours problémák elhárítása

Ez a cikk a Azure Automation Start/Stop VMs during off-hours szolgáltatás virtuális gépeken való üzembe helyezése során felmerülő problémák elhárításával és megoldásával kapcsolatos információkat tartalmaz. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Forgatókönyv: a Start/Stop VMs during off-hours nem tudja megfelelően telepíteni

### <a name="issue"></a>Probléma

[Start/Stop VMS During off-hours](../automation-solution-vm-management.md)telepítésekor a következő hibák valamelyike jelenik meg:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Ok

A központi telepítések a következő okok egyike miatt sikertelenek lehetnek:

- Már van ilyen nevű Automation-fiók a kiválasztott régióban.
- Egy házirend nem engedélyezi a Start/Stop VMs during off-hours telepítését.
- A `Microsoft.OperationsManagement` , `Microsoft.Insights` vagy az `Microsoft.Automation` erőforrástípus nincs regisztrálva.
- A Log Analytics munkaterület zárolva van.
- A AzureRM-modulok elavult verziója vagy a Start/Stop VMs during off-hours funkció szerepel.

### <a name="resolution"></a>Feloldás

Tekintse át a következő javításokat a lehetséges megoldásokhoz:

* Az Automation-fiókoknak egyedi Azure-régión belül kell lenniük, még akkor is, ha különböző erőforráscsoportok. Keresse meg a meglévő Automation-fiókokat a célhelyen.
* Egy meglévő szabályzat megakadályozza a Start/Stop VMs during off-hours telepítéséhez szükséges erőforrást. Lépjen a Azure Portal házirend-hozzárendeléseire, és győződjön meg arról, hogy rendelkezik-e olyan szabályzat-hozzárendeléssel, amely nem engedélyezi az erőforrás központi telepítését. További információ: [RequestDisallowedByPolicy hiba](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Start/Stop VMs during off-hours üzembe helyezéséhez az előfizetést regisztrálni kell a következő Azure-beli erőforrás-névterekben:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Ha többet szeretne megtudni a szolgáltatók regisztrálásával kapcsolatos hibákról, tekintse meg [az erőforrás-szolgáltatói regisztráció hibáinak elhárítása](../../azure-resource-manager/templates/error-register-resource-provider.md)című témakört.
* Ha zárolva van a Log Analytics munkaterületen, lépjen a munkaterületre a Azure Portalban, és távolítsa el az erőforrás összes zárolását.
* Ha ezek a megoldások nem oldják meg a problémát, kövesse az Start/Stop VMs during off-hours újratelepítésére szolgáló [szolgáltatás frissítése](../automation-solution-vm-management.md#update-the-feature) című témakör útmutatását.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Forgatókönyv: az összes virtuális gép elindítása vagy leállítása sikertelen

### <a name="issue"></a>Probléma

Konfigurálta Start/Stop VMs during off-hours, de nem indítja el vagy nem állítja le az összes virtuális gépet.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok egyike okozhatta:

- Az ütemterv nincs megfelelően konfigurálva.
- Lehet, hogy a futtató fiók nem megfelelően van konfigurálva.
- Lehetséges, hogy egy runbook hibák léptek fel.
- Lehetséges, hogy a virtuális gépek ki lettek zárva.

### <a name="resolution"></a>Feloldás

Tekintse át az alábbi listát a lehetséges megoldásokról:

* Győződjön meg arról, hogy megfelelően konfigurálta a Start/Stop VMs during off-hourshoz tartozó ütemtervet. Az ütemtervek konfigurálásának megismeréséhez lásd: [ütemtervek](../shared-resources/schedules.md).

* Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#job-statuses) . Keressen feladatokat a következő runbookok egyikéről:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ellenőrizze, hogy a [futtató fiókja](../automation-security-overview.md#run-as-accounts) rendelkezik-e megfelelő engedélyekkel az elindítani vagy leállítani kívánt virtuális gépekhez. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjával. Válassza a **fiók beállításai** területen a **futtató fiókok** lehetőséget, majd válassza ki a megfelelő futtató fiókot.

* Előfordulhat, hogy a virtuális gépeket nem lehet elindítani vagy leállítani, ha explicit módon ki vannak zárva. A kizárt virtuális gépeket abban az `External_ExcludeVMNames` Automation-fiókban lévő változóban kell beállítani, amelyre a szolgáltatás telepítve van. Az alábbi példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Forgatókönyv: a virtuális gépek némelyike nem indul el vagy leáll

### <a name="issue"></a>Probléma

Konfigurálta Start/Stop VMs during off-hours, de nem indul el vagy nem állítja le a konfigurált virtuális gépek némelyikét.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok egyike okozhatta:

- A Sequence forgatókönyvben előfordulhat, hogy a címke hiányzik vagy helytelen.
- Lehet, hogy a virtuális gép ki van zárva.
- Előfordulhat, hogy a futtató fiók nem rendelkezik megfelelő engedélyekkel a virtuális gépen.
- A virtuális gép rendelkezhet olyan hibával, amely leállt az indítástól vagy a leállítástól.

### <a name="resolution"></a>Feloldás

Tekintse át az alábbi listát a lehetséges megoldásokról:

* Ha a Start/Stop VMs during off-hours [sorozatot](../automation-solution-vm-management.md) használja, meg kell győződnie arról, hogy minden elindítani vagy leállítani kívánt virtuális gép rendelkezik a megfelelő címkével. Győződjön meg arról, hogy az elindítani kívánt virtuális gépek rendelkeznek a `sequencestart` címkével és a leállítani kívánt virtuális gépekkel `sequencestop` . Mindkét címkéhez pozitív egész értéket kell megadni. Az alábbi példához hasonló lekérdezéssel megkeresheti az összes virtuális gépet a címkékkel és azok értékeivel.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Előfordulhat, hogy a virtuális gépeket nem lehet elindítani vagy leállítani, ha explicit módon ki vannak zárva. A kizárt virtuális gépeket abban az `External_ExcludeVMNames` Automation-fiókban lévő változóban kell beállítani, amelyre a szolgáltatás telepítve van. Az alábbi példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* A virtuális gépek indításához és leállításához az Automation-fiókhoz tartozó futtató fióknak megfelelő engedélyekkel kell rendelkeznie a virtuális géphez. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjával. Válassza a **fiók beállításai** területen a **futtató fiókok** lehetőséget, majd válassza ki a megfelelő futtató fiókot.
* Ha a virtuális gép a probléma megkezdése vagy felszabadítása során problémákba ütközik, előfordulhat, hogy a virtuális gépen probléma merül fel. Ilyenek például egy olyan frissítés, amely akkor lép életbe, amikor a virtuális gép le van állítva, és a szolgáltatás leáll. Nyissa meg a virtuális gép erőforrását, **és ellenőrizze, hogy vannak-e** hibák a naplókban. Előfordulhat, hogy megpróbál bejelentkezni a virtuális gépre, hogy ellenőrizze, van-e hiba az eseménynaplókban. A virtuális gép hibaelhárításával kapcsolatos további tudnivalókért tekintse meg az Azure-beli [virtuális gépek hibaelhárítása](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)című témakört.
* Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#job-statuses) . A portálon nyissa meg az Automation-fiókját, és válassza a **feladatok** **automatizálása** lehetőséget.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Forgatókönyv: az egyéni runbook nem sikerül elindítani vagy leállítani a virtuális gépeket

### <a name="issue"></a>Probléma

Létrehozott egy egyéni runbook, vagy letöltött egyet a PowerShell-galériaból, és nem működik megfelelően.

### <a name="cause"></a>Ok

A hiba számos oka lehet. Nyissa meg az Automation-fiókját a Azure Portal, és válassza a **feladatok** **automatizálása** lehetőséget. A **feladatok** lapon keresse meg a runbook feladatait, és tekintse meg a feladatok hibáit.

### <a name="resolution"></a>Feloldás

A következő megoldást javasoljuk:

* A [Start/Stop VMS During off-hours](../automation-solution-vm-management.md) használatával a virtuális gépeket elindíthatja és leállíthatja a Azure Automationban. 
* Vegye figyelembe, hogy a Microsoft nem támogatja az egyéni runbookok. Előfordulhat, hogy a [runbook kapcsolatos problémák elhárítása](runbooks.md)során megtalálhatja az egyéni runbook vonatkozó felbontást. Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#job-statuses) . 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Forgatókönyv: a virtuális gépek nem indulnak el vagy leállnak a megfelelő sorozatban

### <a name="issue"></a>Probléma

A szolgáltatáshoz engedélyezett virtuális gépek nem indulnak el vagy nem állnak le a megfelelő sorozatban.

### <a name="cause"></a>Ok

Ezt a problémát a virtuális gépek helytelen címkézése okozza.

### <a name="resolution"></a>Feloldás

A következő lépések végrehajtásával győződjön meg arról, hogy a szolgáltatás helyesen van engedélyezve:

1. Győződjön meg arról, hogy az összes elindítható vagy leállított virtuális gép rendelkezik a `sequencestart` `sequencestop` helyzettől függően a vagy a címkével. Ezeknek a címkéknek pozitív egész számnak kell lennie az értékként. A virtuális gépek az érték alapján növekvő sorrendben lesznek feldolgozva.
1. Ügyeljen arra, hogy a virtuális gépek elindításához vagy leállításához használt erőforráscsoportok a `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` helyzettől függően a vagy a változóban legyenek.
1. Tesztelje a módosításokat úgy, hogy az **SequencedStartStop_Parent** Runbook az `WHATIF` igaz értékre állítja, hogy megtekintse a módosításokat.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Forgatókönyv: Start/Stop VMs during off-hours a feladattal meghiúsul a 403 Tiltott hiba

### <a name="issue"></a>Probléma

`403 forbidden`Start/Stop VMS During off-hours runbookok hibával meghiúsult feladatok találhatók.

### <a name="cause"></a>Ok

Ezt a problémát egy nem megfelelően konfigurált vagy lejárt futtató fiók okozhatja. Az is előfordulhat, hogy a futtató fiók nem rendelkezik megfelelő engedélyekkel a virtuális gép erőforrásaihoz.

### <a name="resolution"></a>Feloldás

Annak ellenőrzéséhez, hogy a futtató fiók megfelelően van-e konfigurálva, lépjen a Azure Portal Automation-fiókjába, és válassza a **fiók beállításai** alatt a **futtató fiókok** elemet. Ha egy futtató fiók helytelenül van konfigurálva vagy lejárt, az állapot megjeleníti a feltételt.

Ha a futtató fiók helytelenül van konfigurálva, törölje és hozza létre újra a futtató fiókot. További információ: [Azure Automation futtató fiókok](../automation-security-overview.md#run-as-accounts).

Ha a tanúsítvány lejárt a futtató fiókhoz, kövesse az [önaláírt tanúsítvány megújításának](../manage-runas-account.md#cert-renewal) lépéseit a tanúsítvány megújításához.

Ha hiányoznak az engedélyek, tekintse [meg a rövid útmutató: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md)című témakört. Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjával. Válassza a **fiók beállításai** területen a **futtató fiókok** lehetőséget, majd válassza ki a megfelelő futtató fiókot.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Forgatókönyv: a probléma nem szerepel a listán

### <a name="issue"></a>Probléma

Ha olyan Start/Stop VMs during off-hours használ, amely nem szerepel ezen a lapon, probléma vagy váratlan eredmény tapasztalható.

### <a name="cause"></a>Ok

A szolgáltatás régi és elavult verziója sokszor hibákat okozhat.

> [!NOTE]
> A Start/Stop VMs during off-hours funkció az Automation-fiókba importált Azure-modulokkal lett tesztelve, amikor a szolgáltatást a virtuális gépeken telepíti. A szolgáltatás jelenleg nem működik az Azure-modul újabb verzióival. Ez a korlátozás csak a Start/Stop VMs during off-hours futtatásához használt Automation-fiókot érinti. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban a következő témakörben leírtak szerint: [Azure PowerShell-modulok frissítése](../automation-update-azure-modules.md).

### <a name="resolution"></a>Feloldás

Sok hiba elhárításához távolítsa el és [frissítse Start/Stop VMS During off-hours](../automation-solution-vm-management.md#update-the-feature). A [feladatok adatfolyamait](../automation-runbook-execution.md#job-statuses) is megtekintheti, ha hibákat keres. 

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.