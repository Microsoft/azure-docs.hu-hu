---
title: A Azure Automation Start/Stop VMs during off-hours hibaelhárítása
description: Ez a cikk azt mutatja be, hogyan háríthatja el a szolgáltatás használata során felmerülő Start/Stop VMs during off-hours megoldását.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b03481b41e879acac4c3b193d72594454f650525
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831185"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>A Start/Stop VMs during off-hours hibaelhárítása

Ez a cikk a virtuális gépeken a szolgáltatás üzembe helyezésekor felmerülő Azure Automation Start/Stop VMs during off-hours és megoldásáról nyújt tájékoztatást. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Forgatókönyv: Start/Stop VMs during off-hours üzembe helyezése sikertelen

### <a name="issue"></a>Probléma

A [(Start/Stop VMs during off-hours](../automation-solution-vm-management.md)telepítésekor a következő hibák valamelyike lép fel:

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

Az üzembe helyezés az alábbi okok valamelyike miatt meghiúsulhat:

- Már létezik egy azonos nevű Automation-fiók a kiválasztott régióban.
- A szabályzat nem teszi Start/Stop VMs during off-hours.
- A `Microsoft.OperationsManagement` , `Microsoft.Insights` vagy `Microsoft.Automation` erőforrástípus nincs regisztrálva.
- A Log Analytics-munkaterület zárolva van.
- Az AzureRM-modulok vagy a szolgáltatás elavult Start/Stop VMs during off-hours rendelkezik.

### <a name="resolution"></a>Feloldás

Tekintse át a következő javításokat a lehetséges megoldásokkal:

* Az Automation-fiókoknak egyedinek kell lennie egy Azure-régión belül, még akkor is, ha különböző erőforráscsoportokban vannak. Ellenőrizze meglévő Automation-fiókjait a célterületen.
* A meglévő szabályzatok megakadályozzák a Start/Stop VMs during off-hours telepítéséhez szükséges erőforrásokat. A szabályzat-hozzárendeléseket a Azure Portal, és ellenőrizze, hogy van-e olyan szabályzat-hozzárendelése, amely nem teszi meg az erőforrás üzembe helyezését. További információ: [RequestDisallowedByPolicy hiba.](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)
* A Start/Stop VMs during off-hours üzembe helyezéséhez az előfizetést regisztrálni kell a következő Azure-erőforrásnévterekbe:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   A szolgáltatók regisztrálása során előforduló hibákkal kapcsolatos további információkért lásd: [Erőforrás-szolgáltató regisztrációjával kapcsolatos hibák elhárítása.](../../azure-resource-manager/templates/error-register-resource-provider.md)
* Ha zárolta a Log Analytics-munkaterületet, lépjen a munkaterületre a Azure Portal és távolítsa el az erőforrás zárolását.
* Ha ezek a megoldási problémák nem oldják [](../automation-solution-vm-management.md#update-the-feature) meg a problémát, kövesse a Funkció frissítése a szolgáltatás ismételt üzembe Start/Stop VMs during off-hours.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Forgatókönyv: Az összes virtuális gép nem indul el vagy áll le

### <a name="issue"></a>Probléma

Már konfigurálta a Start/Stop VMs during off-hours, de nem indítja el vagy állítsa le az összes virtuális gépeket.

### <a name="cause"></a>Ok

Ezt a hibát az alábbi okok valamelyike okozhatja:

- Az ütemezés nincs megfelelően konfigurálva.
- Előfordulhat, hogy a futó fiók nincs megfelelően konfigurálva.
- Előfordulhat, hogy egy runbook hibákba ásott.
- Előfordulhat, hogy a virtuális gépek ki vannak zárva.

### <a name="resolution"></a>Feloldás

A lehetséges megoldásokat az alábbi lista tartalmazza:

* Ellenőrizze, hogy megfelelően konfigurálta-e az ütemezést a Start/Stop VMs during off-hours. Az ütemezések konfigurálásának elsajátításért lásd: [Ütemezések.](../shared-resources/schedules.md)

* Ellenőrizze a [feladatstreameket,](../automation-runbook-execution.md#job-statuses) és keresse meg a hibákat. A következő runbookok egyikében keresse meg a feladatokat:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ellenőrizze, hogy [a futó](../automation-security-overview.md#run-as-accounts) fiók rendelkezik-e a megfelelő engedélyekkel az elindítani vagy leállítani próbált virtuális gépekhez. Az erőforrás engedélyeinek ellenőrzésével kapcsolatos további információkért lásd: Rövid útmutató: Felhasználóhoz rendelt szerepkörök megtekintése [a Azure Portal.](../../role-based-access-control/check-access.md) Meg kell adnia a futó fiók által használt szolgáltatásnév alkalmazásazonosítóját. Ezt az értéket úgy használhatja, ha az Automation-fiókját a Azure Portal. A **Fiókbeállítások alatt válassza a** Futtatás fiókként **lehetőséget,** majd válassza ki a megfelelő fiókot.

* Előfordulhat, hogy a virtuális gépek nem lesznek elindítva vagy leállítva, ha explicit módon ki vannak zárva. A kizárt virtuális gépeket az Automation-fiók változója tartalmazza, amelyben a szolgáltatás `External_ExcludeVMNames` üzembe van állítva. Az alábbi példa bemutatja, hogyan lehet lekérdezni ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Forgatókönyv: Néhány virtuális gép nem indul el vagy áll le

### <a name="issue"></a>Probléma

Konfigurálta a Start/Stop VMs during off-hours, de nem indítja el vagy állítsa le a konfigurált virtuális gépek egy része.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok valamelyike okozhatja:

- A sorozat forgatókönyvében előfordulhat, hogy egy címke hiányzik vagy helytelen.
- Előfordulhat, hogy a virtuális gép ki van zárva.
- Előfordulhat, hogy a futó fiók nem rendelkezik elegendő engedéllyel a virtuális gépen.
- A virtuális gép indítása vagy leállítása leállt.

### <a name="resolution"></a>Feloldás

A lehetséges megoldásokat az alábbi lista tartalmazza:

* A [folyamatütemezési](../automation-solution-vm-management.md) forgatókönyv Start/Stop VMs during off-hours meg kell győződnie arról, hogy minden elindítani vagy leállítani kívánt virtuális gép rendelkezik a megfelelő címkével. Győződjön meg arról, hogy a elindítani kívánt virtuális gépek címkével, a leállítni kívánt virtuális gépek pedig `sequencestart` `sequencestop` címkével vannak megszabadulva. Mindkét címkéhez pozitív egész számra van szükség. Az alábbi példához hasonló lekérdezéssel keresse meg az összes címkével és azok értékeivel együtt használt virtuális gépeket.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Előfordulhat, hogy a virtuális gépek nem lesznek elindítva vagy leállítva, ha explicit módon ki vannak zárva. A kizárt virtuális gépek abban az Automation-fiókban beállított változóban vannak beállítva, amelyben a szolgáltatás `External_ExcludeVMNames` üzembe van állítva. Az alábbi példa bemutatja, hogyan lehet lekérdezni ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* A virtuális gépek indításához és leállításához az Automation-fiókhoz a megfelelő engedélyekkel kell rendelkeznie a virtuális géphez. Az erőforrás engedélyeinek ellenőrzésével kapcsolatos további információkért lásd: Rövid útmutató: Felhasználóhoz rendelt szerepkörök megtekintése [a Azure Portal.](../../role-based-access-control/check-access.md) Meg kell adnia a run as-fiók által használt szolgáltatásnév alkalmazásazonosítóját. Ezt az értéket úgy tudja lekérni, ha az Automation-fiókját a Azure Portal. A **Fiókbeállítások alatt válassza** a Futtatás fiókként **lehetőséget,** majd válassza ki a megfelelő fiókot.
* Ha a virtuális gép indítása vagy felszabadítása problémát okoz, akkor magára a virtuális gépre is probléma lehet. Ilyenek például azok a frissítések, amelyek akkor vannak alkalmazva, amikor a virtuális gép le próbál állni, egy lefagyó szolgáltatás stb. Tekintse meg a virtuális gép erőforrását, és ellenőrizze a **tevékenységnaplókat,** hogy nincsenek-e hibák a naplókban. Megpróbálhat bejelentkezni a virtuális gépre, hogy lássa, vannak-e hibák az eseménynaplókban. További információ a virtuális gépek hibaelhárításáról: Azure-beli [virtuális gépek hibaelhárítása.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
* Ellenőrizze a [feladatstreameket,](../automation-runbook-execution.md#job-statuses) és keresse meg a hibákat. A portálon kattintson az Automation-fiókjára, és válassza a **Feladatok lehetőséget** a **Folyamatautomatizálás alatt.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Forgatókönyv: Az egyéni runbook nem tudja elindítani vagy leállítani a virtuális gépeket

### <a name="issue"></a>Probléma

Létrehozott egy egyéni runbookot, vagy letöltött egyet a PowerShell-galéria, és nem működik megfelelően.

### <a name="cause"></a>Ok

A hiba számos oka lehet. Az automation-fiókhoz a Azure Portal válassza a **Feladatok** lehetőséget **a Folyamatautomatizálás alatt.** A Feladatok **lapon** keresse meg a runbookban található feladatokat a feladathibák megtekintéséhez.

### <a name="resolution"></a>Feloldás

A következő megoldást javasoljuk:

* A [Start/Stop VMs during off-hours](../automation-solution-vm-management.md) a virtuális gépek a virtuális gépek Azure Automation. 
* Vegye figyelembe, hogy a Microsoft nem támogatja az egyéni runbookokat. Az egyéni runbook megoldását a Runbookokkal kapcsolatos problémák elhárítása [témakörben találhatja.](runbooks.md) Ellenőrizze a [feladatstreameket,](../automation-runbook-execution.md#job-statuses) hogy vannak-e hibák. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Forgatókönyv: A virtuális gépek nem a megfelelő sorrendben indulnak el vagy leállnak

### <a name="issue"></a>Probléma

A funkcióhoz engedélyezett virtuális gépek nem a megfelelő sorrendben indulnak el vagy leállnak.

### <a name="cause"></a>Ok

Ezt a problémát a virtuális gépek helytelen címkézése okozza.

### <a name="resolution"></a>Feloldás

Az alábbi lépésekkel győződjön meg arról, hogy a funkció megfelelően van engedélyezve:

1. Az adott helyzettől függően győződjön meg arról, hogy az összes elindított vagy leállított virtuális gép vagy `sequencestart` `sequencestop` címkével van felcímkézve. Ezeknek a címkéknek pozitív egész számra van szükségük értékként. A virtuális gépeket a rendszer ezen érték alapján növekvő sorrendben feldolgozi.
1. Győződjön meg arról, hogy az elindított vagy leállított virtuális gépek erőforráscsoportja az adott helyzettől függően a vagy a `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` változóban van.
1. A módosítások előnézetének megtekintéséhez SequencedStartStop_Parent **runbook** futtatásával tesztelje a módosításokat az `WHATIF` Igaz paraméterrel.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Forgatókönyv: Start/Stop VMs during off-hours feladat 403-as tiltott hibával meghiúsul

### <a name="issue"></a>Probléma

Olyan feladatokat talál, amelyek meghiúsultak a `403 forbidden` runbookok Start/Stop VMs during off-hours hibával.

### <a name="cause"></a>Ok

Ezt a problémát egy nem megfelelően konfigurált vagy lejárt futási fiók okozhatja. Ennek az is lehet az oka, hogy a futó fiók nem rendelkezik megfelelő engedélyekkel a virtuális gép erőforrásaihoz.

### <a name="resolution"></a>Feloldás

A fiók megfelelő konfigurációját úgy ellenőrizheti, hogy az Automation-fiókját a  Azure Portal a Fiókbeállítások alatt válassza a Futtatás **fiókként lehetőséget.** Ha egy futó fiók nem megfelelően van konfigurálva vagy lejárt, az állapot a feltételt jeleníti meg.

Ha a futtatás fiókja helytelenül van konfigurálva, törölje, majd hozza létre újra a fiókot. További információ: Azure Automation [fiókok.](../automation-security-overview.md#run-as-accounts)

Ha a tanúsítvány lejárt a futtatott fiókhoz, a tanúsítvány megújítása érdekében kövesse az önaírt [tanúsítványmegújítás](../manage-runas-account.md#cert-renewal) lépéseit.

Hiányzó engedélyek esetén lásd: Rövid útmutató: Felhasználóhoz rendelt szerepkörök megtekintése [a Azure Portal.](../../role-based-access-control/check-access.md) Meg kell adnia a run as-fiók által használt szolgáltatásnév alkalmazásazonosítóját. Ezt az értéket úgy tudja lekérni, ha az Automation-fiókját a Azure Portal. A **Fiókbeállítások alatt válassza a** Futtatás fiókként **lehetőséget,** majd válassza ki a megfelelő fiókot.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Forgatókönyv: A problémám nem szerepel a listán

### <a name="issue"></a>Probléma

Olyan problémát vagy váratlan eredményt tapasztal, Start/Stop VMs during off-hours nem szerepel az oldalon.

### <a name="cause"></a>Ok

A hibákat sokszor a funkció régi és elavult verziójának használata okozhatja.

> [!NOTE]
> Az Start/Stop VMs during off-hours szolgáltatás tesztelve lett az Automation-fiókba importált Azure-modulokkal a szolgáltatás virtuális gépeken való üzembe helyezésekor. A funkció jelenleg nem működik az Azure-modul újabb verzióival. Ez a korlátozás csak arra az Automation-fiókra vonatkozik, amely a Start/Stop VMs during off-hours. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban, az [Update Azure PowerShell modulokban leírtak szerint.](../automation-update-azure-modules.md)

### <a name="resolution"></a>Feloldás

Számos hiba elhárításához távolítsa el és frissítse [a Start/Stop VMs during off-hours.](../automation-solution-vm-management.md#update-the-feature) A feladatstreameket [is ellenőrizheti,](../automation-runbook-execution.md#job-statuses) hogy vannak-e hibák. 

## <a name="next-steps"></a>Következő lépések

Ha itt nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével:

* Az Azure fórumain Azure-szakértőktől [kaphat választ.](https://azure.microsoft.com/support/forums/)
* Csatlakozzon [@AzureSupport](https://twitter.com/azuresupport) a -hez, Microsoft Azure a felhasználói élmény javítása érdekében. Azure ügyfélszolgálata összekapcsolja az Azure-közösséget válaszokkal, támogatással és szakértőkkel.
* Be kell Azure-támogatás incidenst. A webhelyre [Azure-támogatás,](https://azure.microsoft.com/support/options/)és válassza a **Támogatás et.**
