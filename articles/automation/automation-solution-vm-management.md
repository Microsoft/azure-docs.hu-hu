---
title: Azure Automation Start/Stop VMs during off-hours áttekintése
description: Ez a cikk ismerteti a Start/Stop VMs during off-hours funkciót, amely egy ütemezett virtuális gépek indítását vagy leállítását mutatja be, és proaktív módon figyeli őket Azure Monitor naplókból.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2020
ms.topic: conceptual
ms.openlocfilehash: e2f23f4045f0326ffea14ddeb4d588261872188f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743710"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Start/Stop VMs during off-hours áttekintése

Az Start/Stop VMs during off-hours funkció elindítja vagy leállítja az Azure-beli virtuális gépeket. A felhasználó által meghatározott ütemterveken megkezdi vagy leállítja a gépeket, bepillantást Azure Monitor naplókba, és a választható e-maileket a [műveleti csoportok](../azure-monitor/platform/action-groups.md)használatával küldi el. A szolgáltatás a legtöbb forgatókönyv esetében engedélyezhető a Azure Resource Manager és a klasszikus virtuális gépeken is. 

Ez a szolgáltatás a [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) parancsmag használatával indítja el a virtuális gépeket. A [stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) használatával állítja le a virtuális gépeket.

> [!NOTE]
> A Start/Stop VMs during off-hours frissült, hogy támogassa az elérhető Azure-modulok legújabb verzióit. A szolgáltatás a piactéren elérhető frissített verziója nem támogatja a AzureRM modulokat, mert áttelepítettük a AzureRM-ből az az modulokba.

A szolgáltatás egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azon felhasználók számára, akik optimalizálni szeretnék a virtuális gépek költségeit. A szolgáltatással a következőket végezheti el:

- [Virtuális gépek indítása és leállítása](automation-solution-vm-management-config.md#schedule).
- A virtuális gépeket úgy ütemezhet, hogy az Azure- [címkék használatával](automation-solution-vm-management-config.md#tags)növekvő sorrendben induljon el és álljon le. Ez a tevékenység klasszikus virtuális gépek esetén nem támogatott.
- Virtuális gépek alapleállása [alacsony CPU-használat](automation-solution-vm-management-config.md#cpuutil)alapján.

A jelenlegi szolgáltatás korlátai a következők:

- Bármely régióban felügyeli a virtuális gépeket, de csak a Azure Automation-fiókkal megegyező előfizetésben használható.
- Az Azure-ban és Azure Government bármely olyan régióban elérhető, amely támogatja a Log Analytics-munkaterületet, a Azure Automation-fiókot és a riasztásokat. Azure Government régió jelenleg nem támogatja az e-mail-funkciókat.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gépek indítása és leállítása a munkaidőn kívül funkció runbookok egy Azure-beli [futtató fiókkal](automation-create-runas-account.md)működik. A futtató fiók az előnyben részesített hitelesítési módszer, mert a tanúsítvány-hitelesítést használ olyan jelszó helyett, amely esetleg lejáró vagy gyakran változhat.

Javasoljuk, hogy használjon külön Automation-fiókot a Start/Stop VMs during off-hours funkció számára engedélyezett virtuális gépek használatához. Az Azure-modulok verziói gyakran frissülnek, és a paraméterek változhatnak. A szolgáltatás nem frissül ugyanazon a ritmuson, és előfordulhat, hogy az általa használt parancsmagok újabb verziói nem működnek. Javasoljuk, hogy tesztelje a modul frissítéseit egy tesztelési Automation-fiókban, mielőtt importálja őket az üzemi Automation-fiókba (k).

## <a name="permissions"></a>Engedélyek

Bizonyos engedélyekkel kell rendelkeznie a virtuális gépek Start/Stop VMs during off-hours szolgáltatáshoz való engedélyezéséhez. Az engedélyek eltérőek attól függően, hogy a szolgáltatás egy előre létrehozott Automation-fiókot és Log Analytics munkaterületet használ-e, vagy új fiókot és munkaterületet hoz létre. 

Nem kell konfigurálnia az engedélyeket, ha Ön közreműködő az előfizetésben, és egy globális rendszergazda a Azure Active Directory (AD) bérlőben. Ha nem rendelkezik ezekkel a jogosultságokkal, vagy egyéni szerepkört kell konfigurálnia, győződjön meg arról, hogy rendelkezik az alább ismertetett engedélyekkel.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>A meglévő Automation-fiókra és Log Analytics-munkaterületre vonatkozó engedélyek

Ha egy meglévő Automation-fiókkal és Log Analytics munkaterülettel kívánja engedélyezni a virtuális gépeket a Start/Stop VMs during off-hours szolgáltatáshoz, a következő engedélyekre van szükség az erőforráscsoport hatókörén. A szerepkörökkel kapcsolatos további tudnivalókért tekintse meg [Az Azure-erőforrások egyéni szerepkörei](../role-based-access-control/custom-roles.md)című témakört.

| Engedély | Hatókör|
| --- | --- |
| Microsoft. Automation/automationAccounts/READ | Resource Group |
| Microsoft. Automation/automationAccounts/változók/írás | Resource Group |
| Microsoft. Automation/automationAccounts/ütemterv/írás | Resource Group |
| Microsoft. Automation/automationAccounts/runbookok/Write | Resource Group |
| Microsoft. Automation/automationAccounts/kapcsolatok/írás | Resource Group |
| Microsoft. Automation/automationAccounts/tanúsítványok/írás | Resource Group |
| Microsoft. Automation/automationAccounts/modulok/írás | Resource Group |
| Microsoft. Automation/automationAccounts/modulok/olvasás | Resource Group |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Resource Group |
| Microsoft. Automation/automationAccounts/feladatok/írás | Resource Group |
| Microsoft. Automation/automationAccounts/feladatok/olvasás | Resource Group |
| Microsoft. OperationsManagement/megoldások/írás | Resource Group |
| Microsoft. OperationalInsights/munkaterületek/* | Resource Group |
| Microsoft. bepillantások/diagnosticSettings/írás | Resource Group |
| Microsoft. bepillantások/ActionGroups/írás | Resource Group |
| Microsoft. bepillantások/ActionGroups/olvasás | Resource Group |
| Microsoft. Resources/Subscriptions/resourceGroups/READ | Resource Group |
| Microsoft. Resources/üzemelő példány/* | Resource Group |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Engedélyek új Automation-fiókhoz és új Log Analytics munkaterület

Az új Automation-fiókkal és Log Analytics munkaterülettel engedélyezheti a virtuális gépeket a Start/Stop VMs during off-hours szolgáltatáshoz. Ebben az esetben az előző szakaszban meghatározott engedélyekre, valamint az ebben a szakaszban meghatározott engedélyekre van szükség. A következő szerepköröket is meg kell adnia:

- Az előfizetés rendszergazdája. Ez a szerepkör szükséges a klasszikus futtató fiók létrehozásához, ha a klasszikus virtuális gépeket fogja kezelni. Alapértelmezés szerint a [klasszikus futtató fiókok](automation-create-standalone-account.md#create-a-classic-run-as-account) már nem jönnek létre.
- Tagság az [Azure ad](../active-directory/users-groups-roles/directory-assign-admin-roles.md) -alkalmazás fejlesztői szerepkörében. A futtató fiókok konfigurálásával kapcsolatos további információkért lásd: [engedélyek a futtató fiókok konfigurálására](manage-runas-account.md#permissions).
- Közreműködő az előfizetésen vagy a következő engedélyeken.

| Engedély |Hatókör|
| --- | --- |
| Microsoft. Authorization/Operations/READ | Előfizetés|
| Microsoft. Authorization/engedélyek/olvasás |Előfizetés|
| Microsoft. Authorization/roleAssignments/olvasás | Előfizetés |
| Microsoft.Authorization/roleAssignments/write | Előfizetés |
| Microsoft. Authorization/roleAssignments/delete | Előfizetés || Microsoft. Automation/automationAccounts/kapcsolatok/olvasás | Resource Group |
| Microsoft. Automation/automationAccounts/tanúsítványok/olvasás | Resource Group |
| Microsoft. Automation/automationAccounts/írás | Resource Group |
| Microsoft. OperationalInsights/munkaterületek/írás | Resource Group |

## <a name="components"></a>Összetevők

Az Start/Stop VMs during off-hours szolgáltatás előre konfigurált runbookok, ütemezett és integrációs Azure Monitor-naplókkal rendelkezik. Ezekkel az elemekkel testre szabhatja a virtuális gépek indítását és leállítását az üzleti igényeknek megfelelően.

### <a name="runbooks"></a>Runbookok

A következő táblázat felsorolja azokat a runbookok, amelyeket a szolgáltatás üzembe helyez az Automation-fiókjában. Ne módosítsa a runbook-kódot. Ehelyett saját runbook írhat az új funkciókhoz.

> [!IMPORTANT]
> Ne futtasson közvetlenül olyan runbook, amelynek a **gyermeke** hozzá van fűzve a nevéhez.

Az összes szülő runbookok tartalmazza a `WhatIf` paramétert. Ha igaz értékre van állítva, a paraméter támogatja a runbook által a paraméter nélkül futtatott pontos viselkedést, és ellenőrzi, hogy a megfelelő virtuális gépek célozva vannak-e. A runbook csak akkor hajtja végre a definiált műveleteit, ha a `WhatIf` paraméter értéke hamis.

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Meghívva a szülő runbook. Ez a runbook a riasztásokat erőforrás-alapon hozza létre az automatikus leállítási forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true vagy FALSE  | Létrehozza vagy frissíti az Azure riasztási szabályokat a célként megadott előfizetésben vagy erőforráscsoportok virtuális gépeken. <br> `VMList`a a virtuális gépek vesszővel tagolt listája. Például: `vm1, vm2, vm3`.<br> `WhatIf`lehetővé teszi a runbook logika érvényesítését a végrehajtás nélkül.|
|AutoStop_Disable | Nincs | Letiltja az automatikus leállítási riasztásokat és az alapértelmezett ütemtervet.|
|AutoStop_VM_Child | WebHookData | Meghívva a szülő runbook. A riasztási szabályok meghívja ezt a runbook egy klasszikus virtuális gép leállításához.|
|AutoStop_VM_Child_ARM | WebHookData |Meghívva a szülő runbook. A riasztási szabályok meghívja ezt a runbook egy virtuális gép leállítására.  |
|ScheduledStartStop_Base_Classic | Felhőszolgáltatásneve<br> Művelet: indítás vagy leállítás<br> VMList  | Elvégzi a művelet indítását vagy leállítását a klasszikus virtuálisgép-csoportban Cloud Services alapján. |
|ScheduledStartStop_Child | VMName <br> Művelet: indítás vagy leállítás <br> ResourceGroupName | Meghívva a szülő runbook. Végrehajt egy indítási vagy leállítási műveletet az ütemezett leállítás előtt.|
|ScheduledStartStop_Child_Classic | VMName<br> Művelet: indítás vagy leállítás<br> ResourceGroupName | Meghívva a szülő runbook. Indítási vagy leállítási műveletet hajt végre a klasszikus virtuális gépek ütemezett leállításakor. |
|ScheduledStartStop_Parent | Művelet: indítás vagy leállítás <br>VMList <br> WhatIf: true vagy FALSE | Elindítja vagy leállítja az előfizetésben lévő összes virtuális gépet. Szerkessze a változókat `External_Start_ResourceGroupNames` , és `External_Stop_ResourceGroupNames` csak ezekre a célcsoportokra legyen végrehajtva. Az egyes virtuális gépeket a változó frissítésével is kizárhatja `External_ExcludeVMNames` .|
|SequencedStartStop_Parent | Művelet: indítás vagy leállítás <br> WhatIf: true vagy FALSE<br>VMList| A **sequencestart** és a **sequencestop** nevű címkéket hoz létre minden olyan virtuális gépen, amelynél le kívánja állítani az indítási/leállítási tevékenységet. A címkék nevei megkülönböztetik a kis-és nagybetűket. A címke értékének pozitív egész számnak (1, 2, 3) kell lennie, amely megfelel az elindítani vagy leállítani kívánt sorrendnek. <br>**Megjegyzés**: a virtuális gépeknek a (z `External_Start_ResourceGroupNames` ), `External_Stop_ResourceGroupNames` , és változóban definiált erőforráscsoport-csoportokon belül kell lenniük `External_ExcludeVMNames` . A megfelelő címkékkel kell rendelkezniük a műveletek életbe léptetéséhez.|

### <a name="variables"></a>Változók

A következő táblázat felsorolja az Automation-fiókban létrehozott változókat. Csak az előtaggal rendelkező változók módosítása `External` . Az előrögzített változók módosítása nem `Internal` kívánt hatásokat okoz.

> [!NOTE]
> A virtuális gép nevének és az erőforráscsoport korlátozásai nagyrészt a változó méretének köszönhető. Lásd: [változó eszközök Azure Automationban](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | Egy riasztás elindítása előtt a feltétel konfigurálásához szükséges feltételes operátor. Elfogadható értékek:,, `GreaterThan` `GreaterThanOrEqual` `LessThan` és `LessThanOrEqual` .|
|External_AutoStop_Description | A virtuális gép leállítására vonatkozó riasztás, ha a CPU-hányad meghaladja a küszöbértéket.|
|External_AutoStop_Frequency | A szabály kiértékelésének gyakorisága Ez a paraméter TimeSpan formátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között vannak. |
|External_AutoStop_MetricName | Annak a teljesítménymutatónak a neve, amelyhez az Azure-riasztási szabályt konfigurálni kell.|
|External_AutoStop_Severity | A metrikai riasztás súlyossága, amely 0 és 4 között lehet. |
|External_AutoStop_Threshold | A változóban megadott Azure riasztási szabály küszöbértéke `External_AutoStop_MetricName` . A százalékos értékek 1-től 100-ig terjedhetnek.|
|External_AutoStop_TimeAggregationOperator | A kiválasztott ablak méretére alkalmazott időösszesítési operátor a feltétel kiértékeléséhez. Elfogadható értékek:,,, `Average` `Minimum` `Maximum` `Total` és `Last` .|
|External_AutoStop_TimeWindow | Annak az ablaknak a mérete, amely alatt az Azure elemzi a kiválasztott mérőszámokat a riasztások aktiválásához. Ez a paraméter TimeSpan formátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között vannak.|
|External_EnableClassicVMs| Az érték azt határozza meg, hogy a klasszikus virtuális gépeket a szolgáltatás célozza-e. Az alapértelmezett érték TRUE (igaz). Ezt a változót állítsa hamis értékre Azure Cloud Solution Provider (CSP) előfizetésekhez. A klasszikus virtuális gépek [klasszikus futtató fiókot](automation-create-standalone-account.md#create-a-classic-run-as-account)igényelnek.|
|External_ExcludeVMNames | A kizárni kívánt virtuálisgép-nevek vesszővel tagolt listája, amely 140 virtuális GÉPRE korlátozódik. Ha több mint 140 virtuális gépet ad hozzá a listához, előfordulhat, hogy a kizárni kívánt virtuális gépeket véletlenül elindítják vagy leállították.|
|External_Start_ResourceGroupNames | Egy vagy több indítási műveletre irányuló erőforráscsoport vesszővel tagolt listája.|
|External_Stop_ResourceGroupNames | Egy vagy több leállítási műveletet célzó erőforráscsoport vesszővel tagolt listája.|
|External_WaitTimeForVMRetrySeconds |Az a várakozási idő másodpercben, ameddig a virtuális gépeken végrehajtandó műveletek a **SequencedStartStop_Parent** runbook. Ez a változó lehetővé teszi a runbook számára, hogy a következő művelet folytatása előtt megvárja a gyermek műveleteit a megadott számú másodpercig. A maximális várakozási idő 10800, vagy három óra. Az alapértelmezett érték 2100 másodperc.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_ARM_WebhookURI | A webhook URI-ja a virtuális gépek autostop forgatókönyvéhez lett meghívva.|
|Internal_AutoSnooze_WebhookUri | A webhook URI-ja a klasszikus virtuális gépekhez tartozó autostop forgatókönyvhöz lett meghívva.|
|Internal_AzureSubscriptionId | Az Azure-előfizetés azonosítója.|
|Internal_ResourceGroupName | Az Automation-fiók erőforráscsoport-neve.|

>[!NOTE]
>A változó esetében `External_WaitTimeForVMRetryInSeconds` az alapértelmezett érték 600 és 2100 között frissült. 

Minden forgatókönyvben a `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` virtuális gépek megcélzásához szükséges változók, valamint a **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**és **ScheduledStartStop_Parent** runbookok vesszővel elválasztott virtuálisgép-listája szerepel. Ez azt eredményezi, hogy a virtuális gépeknek meg kell egyezniük a cél erőforráscsoporthoz az indítási és leállítási műveletek elvégzéséhez. A logika a Azure Policyhoz hasonlóan működik, amelyekben megcélozhatja az előfizetést vagy az erőforráscsoportot, valamint az újonnan létrehozott virtuális gépek által örökölt műveleteket is. Ezzel a módszerrel elkerülhető, hogy minden virtuális gép esetében külön ütemtervet kell fenntartani, és a felügyelet megkezdődik, és a méretezési folyamat leáll.

### <a name="schedules"></a>Ütemezések

A következő táblázat az Automation-fiókban létrehozott alapértelmezett ütemterveket sorolja fel.Módosíthatók, vagy létrehozhatók saját egyéni ütemtervek is.Alapértelmezés szerint az összes ütemterv le van tiltva, kivéve a **Scheduled_StartVM** és az **Scheduled_StopVM** ütemtervet.

Ne engedélyezze az összes ütemtervet, mert ez az átfedésben lévő ütemezett műveleteket is létrehozhatja. Érdemes eldönteni, hogy mely optimalizálásokat kívánja elvégezni, és ennek megfelelően módosítania kell azokat. További magyarázatért tekintse meg az Áttekintés szakaszban található példákat.

|Ütemterv neve | Gyakoriság | Leírás|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | A **AutoStop_CreateAlert_Parent** runbook 8 óránként futtatja, ami viszont leállítja a virtuálisgép-alapú értékeket `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` és `External_ExcludeVMNames` változókat. Másik lehetőségként megadhatja a virtuális gépek vesszővel tagolt listáját a `VMList` paraméter használatával.|
|Scheduled_StopVM | Felhasználó által definiált, napi | A **ScheduledStopStart_Parent** runbook minden nap paraméterével futtatja `Stop` a megadott időpontban.A automatikusan leállítja az összes olyan virtuális gépet, amely megfelel a változó eszközök által meghatározott szabályoknak.A kapcsolódó ütemezett ütemezés engedélyezése **– StartVM**.|
|Scheduled_StartVM | Felhasználó által definiált, napi | A **ScheduledStopStart_Parent** runbook a `Start` megadott időpontban minden nap paraméter értékével futtatja. A automatikusan elindítja az összes olyan virtuális gépet, amely megfelel a változó eszközök által meghatározott szabályoknak.A kapcsolódó ütemezett ütemezés engedélyezése **– StopVM**.|
|Sorozatos – StopVM | 1:00 AM (UTC), minden pénteken | A **Sequenced_StopStop_Parent** runbook a `Stop` megadott időpontban minden pénteken található paraméter értékkel futtatja.A szekvenciálisan (növekvő) leállítja az összes virtuális gépet a megfelelő változók által definiált **SequenceStop** címkével. További információ a címkézési értékekről és az adategység változókról: [runbookok](#runbooks).Engedélyezze a kapcsolódó ütemezéseket, a **Sequenced-StartVM**.|
|Sorozatos – StartVM | 1:00 PM (UTC), minden hétfőn | A **SequencedStopStart_Parent** runbook a `Start` megadott időponthoz tartozó minden hétfőn lévő paraméter értékével futtatja. A szekvenciálisan (csökkenő) elindítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStart** . További információ a címkék értékeiről és a változó eszközökről: [runbookok](#runbooks). Engedélyezze a kapcsolódó ütemezéseket, a **Sequenced-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>A szolgáltatás használata klasszikus virtuális gépekkel

Ha a klasszikus virtuális gépekhez a Start/Stop VMs during off-hours funkciót használja, az Automation minden virtuális gépet egymás után dolgozza fel a Cloud Service-ben. A virtuális gépek továbbra is párhuzamosan lesznek feldolgozva a különböző felhőalapú szolgáltatások között. 

A klasszikus virtuális gépekkel rendelkező szolgáltatás használatához klasszikus futtató fiókra van szükség, amely alapértelmezés szerint nem jön létre. Klasszikus futtató fiók létrehozásával kapcsolatos utasításokért lásd: [klasszikus futtató fiók létrehozása](automation-create-standalone-account.md#create-a-classic-run-as-account).

Ha a Cloud Service-ben több mint 20 virtuális gép van, a következő javaslatok közül választhat:

* Hozzon létre több ütemtervet a szülő runbook **ScheduledStartStop_Parent** és ütemezzen 20 virtuális gépet. 
* Az ütemterv tulajdonságainál a paraméter használatával `VMList` vesszővel tagolt listaként adhatja meg a virtuális gépek nevét. 

Ellenkező esetben, ha a szolgáltatás automatizálási feladata több mint három órát futtat, átmenetileg el lett távolítva vagy leállítva a [méltányos megosztási](automation-runbook-execution.md#fair-share) korláton belül.

Az Azure CSP-előfizetések csak a Azure Resource Manager modellt támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha a Start/Stop VMs during off-hours funkció fut, előfordulhat, hogy hibákat kap, mivel a klasszikus erőforrások kezeléséhez parancsmagokkal rendelkezik. A CSP-vel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört. Ha CSP-előfizetést használ, a [External_EnableClassicVMs](#variables) változót false értékre kell állítania az üzembe helyezés után.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-feature"></a>A szolgáltatás engedélyezése

A szolgáltatás használatának megkezdéséhez kövesse a [Start/Stop VMS During off-hours engedélyezése](automation-solution-vm-management-enable.md)című témakör lépéseit.

## <a name="view-the-feature"></a>A szolgáltatás megtekintése

Az engedélyezett funkció eléréséhez használja a következő mechanizmusok egyikét:

* Az Automation-fiókjából válassza a **virtuális gép indítása/leállítása** **kapcsolódó erőforrások**lehetőséget. A virtuális gép indítása/leállítása lapon válassza **a megoldás kezelése a virtuálisgép** - **megoldások indítása/leállítása**lehetőséget.

* Navigáljon az Automation-fiókjához társított Log Analytics munkaterületre. Miután kiválasztotta a munkaterületet, válassza a bal oldali ablaktábla **megoldások** elemét. A megoldások lapon válassza a **Start-Stop-VM [munkaterület]** elemet a listából.  

A funkció kiválasztásával a Start-Stop-VM [munkaterület] lap jelenik meg. Itt áttekintheti a fontos adatokat, például a **StartStopVM** csempén található információkat. A Log Analytics munkaterülethez hasonlóan ez a csempe a runbook feladatok számát és grafikus megjelenítését jeleníti meg az elindított és sikeresen befejezett szolgáltatáshoz.

![Automation-Update Management lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A munkarekordok további elemzését a fánk csempére kattintva végezheti el. Az irányítópulton a feladatok előzményei és az előre definiált naplók keresési lekérdezései láthatók. Váltson a log Analytics speciális portálra a keresési lekérdezések alapján történő kereséshez.

## <a name="update-the-feature"></a>A szolgáltatás frissítése

Ha telepítette a Start/Stop VMs during off-hours egy korábbi verzióját, törölje azt a fiókból a frissített kiadás telepítése előtt. Kövesse a [szolgáltatás eltávolításához](#remove-the-feature) szükséges lépéseket, majd kövesse a lépéseket az [engedélyezéséhez](automation-solution-vm-management-enable.md).

## <a name="remove-the-feature"></a>A szolgáltatás eltávolítása

Ha már nincs szüksége a szolgáltatás használatára, törölheti az Automation-fiókból. A szolgáltatás törlése csak a társított runbookok távolítja el. Nem törli a szolgáltatás hozzáadásakor létrehozott ütemterveket és változókat. 

Start/Stop VMs during off-hours törlése:

1. Az Automation-fiókban válassza a **kapcsolódó erőforrások**alatt található **csatolt munkaterület** lehetőséget.

2. Válassza **a ugrás munkaterületre**lehetőséget.

3. Kattintson az **általános**területen a **megoldások** elemre. 

4. A megoldások lapon válassza a **Start-Stop-VM [munkaterület]** elemet. 

5. A VMManagementSolution [munkaterület] lapon válassza a **Törlés** lehetőséget a menüből.<br><br> ![Virtuálisgép-felügyeleti szolgáltatás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. A megoldás törlése ablakban erősítse meg, hogy törölni kívánja a szolgáltatást.

7. Az információk ellenőrzése és a szolgáltatás törlése után nyomon követheti a menüből kiválasztott **értesítések**szakaszban található előrehaladást. Az eltávolítási folyamat után visszatért a megoldások oldalára.

8. Az Automation-fiók és Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megtartani a Log Analytics munkaterületet, manuálisan kell törölnie a Azure Portalból:

    1. Keresse meg és válassza ki **log Analytics munkaterületeket**.

    2. A Log Analytics munkaterület lapon válassza ki a munkaterületet.

    3. A menüből válassza a **Törlés** lehetőséget.

    4. Ha nem szeretné megtartani a Azure Automation fiók [funkciójának összetevőit](#components), manuálisan is törölheti őket.

## <a name="next-steps"></a>További lépések

Ha engedélyezni szeretné a szolgáltatást a környezetben lévő virtuális gépeken, tekintse meg a [Start/Stop VMS During off-hours engedélyezése](automation-solution-vm-management-enable.md)című témakört.
