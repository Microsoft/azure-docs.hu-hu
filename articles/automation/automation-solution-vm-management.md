---
title: Azure Automation Start/Stop VMs during off-hours áttekintés
description: Ez a cikk a Start/Stop VMs during off-hours szolgáltatást ismerteti, amely ütemezés szerint elindítja vagy leállítja a virtuális gépeket, és proaktívan figyeli őket a Azure Monitor naplókból.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b28367aa242d5fab71dc5046ff6188c634883f03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834515"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Start/Stop VMs during off-hours áttekintés

A Start/Stop VMs during off-hours szolgáltatás elindul vagy leállítja az engedélyezett Azure-beli virtuális gépeket. Elindítja vagy leállítja a gépeket felhasználó által megadott ütemezés szerint, betekintést nyújt Azure Monitor naplókba, és nem kötelező e-maileket küld [műveletcsoportok használatával.](../azure-monitor/alerts/action-groups.md) A funkció a legtöbb forgatókönyvhöz Azure Resource Manager virtuális gépeken is engedélyezhető.

Ez a funkció [a Start-AzVm](/powershell/module/az.compute/start-azvm) parancsmag használatával indítja el a virtuális gépeket. A virtuális [gépek leállításához a Stop-AzVM-et](/powershell/module/az.compute/stop-azvm) használja.

> [!NOTE]
> Bár a runbookok frissítve vannak az új Azure Az modul parancsmagok használatára, az AzureRM-előtag aliast használják.

> [!NOTE]
> Start/Stop VMs during off-hours frissítve lett, hogy támogassa az Elérhető Azure-modulok legújabb verzióit. A marketplace-en elérhető frissített verzió nem támogatja az AzureRM-modulokat, mert migráltunk az AzureRM modulból az Az modulba.

Ez a funkció decentralizált, alacsony költségű automatizálási lehetőséget kínál a virtuális gépek költségeinek optimalizálására kívánó felhasználók számára. A funkció a következőre használható:

- [Virtuális gépek ütemezése a indításhoz és leállításhoz.](automation-solution-vm-management-config.md#schedule)
- Virtuális gépek ütemezése az Azure Tags használatával növekvő sorrendben való [indításra és leállításra.](automation-solution-vm-management-config.md#tags) Ez a tevékenység klasszikus virtuális gépek esetén nem támogatott.
- Virtuális gépek automatikus leállítása alacsony [PROCESSZORhasználat alapján.](automation-solution-vm-management-config.md#cpuutil)

Az alábbi korlátozások vonatkoznak az aktuális funkcióra:

- Bármely régióban kezeli a virtuális gépeket, de csak ugyanabban az előfizetésben használhatók, mint a Azure Automation fiók.
- Az Azure-ban érhető el, Azure Government log Analytics-munkaterületet, fiókfiókot és riasztásokat támogató bármely régióban Azure Automation érhető el. Azure Government-régiók jelenleg nem támogatják az e-mail-funkciókat.

> [!NOTE]
> Mielőtt telepítenék ezt a verziót, szeretnénk, ha ismerné a következő verziót, amely jelenleg előzetes verzióként érhető el. [](https://github.com/microsoft/startstopv2-deployments)  Ez az új verzió (V2) ugyanazt a funkciót kínálja, mint ez, de úgy tervezték, hogy kihasználja az Azure újabb technológiáját. Hozzáad néhányat az ügyfelektől gyakran kért funkciókból, például a több előfizetéses támogatást egyetlen Start/Stop példányból.

## <a name="prerequisites"></a>Előfeltételek

- A Start-Stop VMs during off hours (Virtuális gépek munkaidőn túli indítás/leállítás) funkció runbookja egy [Azure-beli run as-fiókkal működik.](./automation-security-overview.md#run-as-accounts) Az előnyben részesített hitelesítési módszer a futó fiók, mivel tanúsítványhitelesítést használ az esetleg elévülő vagy gyakran megváltozó jelszó helyett.

- Egy [Azure Monitor Log Analytics-munkaterület,](../azure-monitor/logs/design-logs-deployment.md) amely a runbook feladatnaplóit és a feladatstreamet tárolja, a lekérdezés és elemzés eredményeit egy munkaterületen. Az Automation-fiók összekapcsolható egy új vagy meglévő Log Analytics-munkaterülettel, és mindkét erőforrásnak ugyanabban az erőforráscsoportban kell lennie.

Javasoljuk, hogy használjon egy külön Automation-fiókot az engedélyezett virtuális gépekhez a Start/Stop VMs during off-hours funkcióhoz. Az Azure-modulverziókat gyakran frissítik, és a paramétereik változhatnak. A szolgáltatás nem azonos ütemben van frissítve, és előfordulhat, hogy nem működik az által használt parancsmagok újabb verzióival. Mielőtt importálja a frissített modulokat az éles Automation-fiókba, javasoljuk, hogy importálja őket egy Automation-tesztfiókba, hogy ellenőrizze, nincsenek-e kompatibilitási problémák.

## <a name="permissions"></a>Engedélyek

Bizonyos engedélyekkel kell rendelkeznie a virtuális gépek engedélyezéséhez a Start/Stop VMs during off-hours funkcióhoz. Az engedélyek eltérőek attól függően, hogy a funkció előre létrehozott Automation-fiókot és Log Analytics-munkaterületet használ-e, vagy új fiókot és munkaterületet hoz létre.

Nem kell konfigurálnia az engedélyeket, ha Közreműködő az előfizetésben, és globális rendszergazda a Azure Active Directory (AD) bérlőben. Ha nem rendelkezik ezekkel a jogosultságokkal, vagy egyéni szerepkört kell konfigurálnia, győződjön meg arról, hogy rendelkezik az alább ismertetett engedélyekkel.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Engedélyek meglévő Automation-fiókhoz és Log Analytics-munkaterülethez

Ha egy meglévő Automation-fiókkal Start/Stop VMs during off-hours Log Analytics-munkaterülettel szeretné engedélyezni a virtuális gépeket az Start/Stop VMs during off-hours funkcióhoz, a következő engedélyekkel kell rendelkeznie az erőforráscsoport hatókörében. További információ a szerepkörökről: [Egyéni Azure-szerepkörök.](../role-based-access-control/custom-roles.md)

| Engedély | Hatókör|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/variables/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/schedules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/runbooks/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/connections/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/modules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/modules/read | Erőforráscsoport |
| Microsoft.automation/automationAccounts/jobSchedules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/jobs/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/jobs/read | Erőforráscsoport |
| Microsoft.OperationsManagement/solutions/write | Erőforráscsoport |
| Microsoft.OperationalInsights/workspaces/* | Erőforráscsoport |
| Microsoft.Insights/diagnosticSettings/write | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/Write | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/read | Erőforráscsoport |
| Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoport |
| Microsoft.Resources/deployments/* | Erőforráscsoport |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Engedélyek az új Automation-fiókhoz és az új Log Analytics-munkaterülethez

Az új Automation-fiókkal és Log Analytics-munkaterülettel engedélyezheti a virtuális Start/Stop VMs during off-hours a virtuális gépeket a szolgáltatáshoz. Ebben az esetben az előző szakaszban meghatározott engedélyekre, valamint az ebben a szakaszban meghatározott engedélyekre van szüksége. Emellett a következő szerepkörökre is szükség van:

- Co-Administrator előfizetésben. Erre a szerepkörre szükség van a klasszikus futtatású fiók létrehozásához, ha klasszikus virtuális gépeket fog felügyelni. [A klasszikus futtatású fiókok](automation-create-standalone-account.md#create-a-classic-run-as-account) alapértelmezés szerint már nem jön létre.
- Tagság az [Azure AD-alkalmazásfejlesztői](../active-directory/roles/permissions-reference.md) szerepkörben. A futó fiókok konfigurálásával kapcsolatos további információkért tekintse meg a következőt: Permissions to configure Run As accounts (A futtatott fiókok [konfigurálásához szükséges engedélyek).](automation-security-overview.md#permissions)
- Közreműködő az előfizetésben vagy az alábbi engedélyekben.

| Engedély |Hatókör|
| --- | --- |
| Microsoft.Authorization/Operations/read | Előfizetés|
| Microsoft.Authorization/permissions/read |Előfizetés|
| Microsoft.Authorization/roleAssignments/read | Előfizetés |
| Microsoft.Authorization/roleAssignments/write | Előfizetés |
| Microsoft.Authorization/roleAssignments/delete | Előfizetés |
| Microsoft.Automation/automationAccounts/connections/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/write | Erőforráscsoport |
| Microsoft.OperationalInsights/workspaces/write | Erőforráscsoport |

## <a name="components"></a>Összetevők

Az Start/Stop VMs during off-hours funkció előre konfigurált runbookokat, ütemezéseket és integrációt tartalmaz a Azure Monitor naplókba. Ezekkel az elemekkel üzleti igényeinek megfelelően testre szabhatja a virtuális gépek indítását és leállítását.

### <a name="runbooks"></a>Runbookok

Az alábbi táblázat azokat a runbookokat sorolja fel, amelyek üzembe helyezése az Automation-fiókban lefut. NE módosítsa a runbook kódját. Ehelyett saját runbookot kell írnia az új funkciókhoz.

> [!IMPORTANT]
> Ne futtason közvetlenül olyan runbookot, **amely** a gyermek nevéhez van hozzáfűzve.

Minden szülő runbook tartalmazza a `WhatIf` paramétert. Ha True (Igaz) érték van beállítva, a paraméter támogatja a runbook pontos viselkedésének részletezését, amikor a runbook a paraméter nélkül fut, és ellenőrzi, hogy a megfelelő virtuális gépek vannak-e megcélzva. A runbook csak akkor végzi el a meghatározott műveleteket, ha a `WhatIf` paraméter false (Hamis) értékre van állítva.

|Forgatókönyv | Paraméterek | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | A szülő runbookból hívva. Ez a runbook erőforrásonként hoz létre riasztásokat az automatikus leállítási forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Igaz vagy Hamis  | Azure-riasztási szabályokat hoz létre vagy frissíti a célként megcélzott előfizetésben vagy erőforráscsoportokban. <br> `VMList` A a virtuális gépek vesszővel elválasztott listája (szóközök nélkül), `vm1,vm2,vm3` például: .<br> `WhatIf` A lehetővé teszi a runbook logikájának érvényesítését végrehajtás nélkül.|
|AutoStop_Disable | Nincsenek | Letiltja az automatikus leállítási riasztásokat és az alapértelmezett ütemezést.|
|AutoStop_VM_Child | WebHookData | A szülő runbookból hívva. A riasztási szabályok ezt a runbookot hívják meg egy klasszikus virtuális gép leállítása érdekében.|
|AutoStop_VM_Child_ARM | WebHookData |A szülő runbookból hívva. A riasztási szabályok ezt a runbookot hívják meg a virtuális gép leállítása érdekében.  |
|ScheduledStartStop_Base_Classic | CloudServiceName (Felhőszolgáltatás neve)<br> Művelet: Indítás vagy leállítás<br> VMList  | Végrehajtja a klasszikus virtuálisgép-csoportban az indítási vagy leállítási műveletet Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Művelet: Indítás vagy leállítás <br> ResourceGroupName | A szülő runbookból hívva. Végrehajt egy indítási vagy leállítási műveletet az ütemezett leállításhoz.|
|ScheduledStartStop_Child_Classic | VMName<br> Művelet: Indítás vagy leállítás<br> ResourceGroupName | A szülő runbookból hívva. Végrehajt egy indítási vagy leállítási műveletet a klasszikus virtuális gépek ütemezett leállítási műveletére. |
|ScheduledStartStop_Parent | Művelet: Indítás vagy leállítás <br>VMList <br> WhatIf: Igaz vagy Hamis | Elindítja vagy leállítja az előfizetésben található összes virtuális gépét. Szerkessze a és a `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` változót, hogy csak ezeken a megcélzott erőforráscsoportokon hajtson végre végrehajtást. A változó frissítésével adott virtuális gépeket is `External_ExcludeVMNames` kizárhat.|
|SequencedStartStop_Parent | Művelet: Indítás vagy leállítás <br> WhatIf: Igaz vagy Hamis<br>VMList| Létrehozza a **sequencestart és** **a sequencestop nevű címkéket** minden olyan virtuális gépen, amelyhez el szeretné kezdeni/le szeretné állítani a tevékenységet. Ezek a címkenevek megkülönböztetik a kis- és nagybetűket. A címke értékének pozitív egész számokat (például ) kell listának lennie, amely az indítás vagy leállítás sorrendjének `1,2,3` felel meg. <br>**Megjegyzés:** A virtuális gépeknek a , és változókban definiált `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` erőforráscsoportokban kell lennie. A műveletek effektushoz megfelelő címkékkel kell rendelkezik.|

### <a name="variables"></a>Változók

Az alábbi táblázat az Automation-fiókban létrehozott változókat sorolja fel. Csak a előtaggal előtagú változókat `External` módosítsa. A változók előtaggal való módosítása nem kívánt `Internal` hatásokat okoz.

> [!NOTE]
> A virtuális gépek nevére és az erőforráscsoportra vonatkozó korlátozások nagyrészt a változók méretének következménye. Lásd: [Változóeszközök a Azure Automation.](./shared-resources/variables.md)

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | A feltétel konfigurálásához szükséges feltételes operátor a riasztás aktiválása előtt. Elfogadható értékek: `GreaterThan` , `GreaterThanOrEqual` , és `LessThan` `LessThanOrEqual` .|
|External_AutoStop_Description | A virtuális gép leállítási riasztása, ha a processzorhasználat meghaladja a küszöbértéket.|
|External_AutoStop_Frequency | A szabály kiértékelési gyakorisága. Ez a paraméter időformátumban fogadja el a bemenetet. Lehetséges értékek: 5 perctől 6 óráig. |
|External_AutoStop_MetricName | Annak a teljesítménymetrikának a neve, amelyhez az Azure-riasztási szabályt konfigurálni kell.|
|External_AutoStop_Severity | A metrikariasztás súlyossága, amely 0 és 4 között lehet. |
|External_AutoStop_Threshold | A változóban megadott Azure Alert-szabály `External_AutoStop_MetricName` küszöbértéke. A százalékos értékek 1 és 100 között vannak.|
|External_AutoStop_TimeAggregationOperator | A kiválasztott ablakméretre alkalmazott időösszesítő operátor a feltétel kiértékeléséhez. Elfogadható értékek: `Average` , , , és `Minimum` `Maximum` `Total` `Last` .|
|External_AutoStop_TimeWindow | Annak az ablaknak a mérete, amely alatt az Azure elemzi a riasztás aktiválására kiválasztott metrikákat. Ez a paraméter a bemenetet időformátumban fogadja el. A lehetséges értékek 5 perctől 6 óráig esnek.|
|External_EnableClassicVMs| Érték, amely meghatározza, hogy a klasszikus virtuális gépekre a funkció van-e megcélzva. Az alapértelmezett érték a True (Igaz). Állítsa ezt a változót False (Hamis) Azure-felhőszolgáltató (CSP) előfizetések esetében. A klasszikus virtuális gépekhez [klasszikusan futó fiók szükséges.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | A kizárható virtuálisgép-nevek vesszővel elválasztott listája, legfeljebb 140 virtuális gépre korlátozva. Ha több mint 140 virtuális gép van hozzáadva a listához, előfordulhat, hogy a kizáráshoz megadott virtuális gépek véletlenül elindulnak vagy leállnak.|
|External_Start_ResourceGroupNames | Indítási műveletekhez megcélzott egy vagy több erőforráscsoport vesszővel elválasztott listája.|
|External_Stop_ResourceGroupNames | A leállítási műveletekre célzott egy vagy több erőforráscsoport vesszővel elválasztott listája.|
|External_WaitTimeForVMRetrySeconds |Az a várakozási idő másodpercben, amíg a műveletek a runbook virtuális **gépei** SequencedStartStop_Parent lesznek. Ez a változó lehetővé teszi, hogy a runbook adott számú másodpercig várjon a gyermekműveleteket, mielőtt továbblépne a következő műveletre. A maximális várakozási idő 10800, vagyis három óra. Az alapértelmezett érték 2100 másodperc.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_ARM_WebhookURI | A virtuális gépek automatikus leállítási forgatókönyvéhez hívott webhook URI.|
|Internal_AutoSnooze_WebhookUri | A klasszikus virtuális gépek automatikus leállítási forgatókönyvéhez hívott webhook URI.|
|Internal_AzureSubscriptionId | Az Azure-előfizetés azonosítója.|
|Internal_ResourceGroupName | Az Automation-fiók erőforráscsoport-neve.|

>[!NOTE]
>A változó `External_WaitTimeForVMRetryInSeconds` esetében az alapértelmezett érték 600-ból 2100-ra frissült.

Az összes forgatókönyvben a , és változók szükségesek a virtuális gépek célként való megcélzása érdekében, kivéve a AutoStop_CreateAlert_Parent , SequencedStartStop_Parent és ScheduledStartStop_Parent `External_Start_ResourceGroupNames` virtuálisgép-listák `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` esetében.    Ez azt jelenti, hogy a virtuális gépeknek a cél erőforráscsoportokhoz kell tartozni, hogy indítási és leállítási műveleteket indítsunk el. A logika az Azure Policy hasonlóan működik, hogy megcélzhatja az előfizetést vagy az erőforráscsoportot, és az újonnan létrehozott virtuális gépek öröklik a műveleteket. Ezzel a megközelítéssel elkerülhető, hogy minden virtuális géphez külön ütemezést kelljen fenntartani, és a kezelés nagy léptékben indul el és áll le.

### <a name="schedules"></a>Ütemezések

Az alábbi táblázat az Automation-fiókban létrehozott alapértelmezett ütemezéseket sorolja fel. Módosíthatja őket, vagy létrehozhatja saját egyéni ütemezését. Alapértelmezés szerint minden ütemezés le van tiltva a Scheduled_StartVM **és** Scheduled_StopVM **kivételével.**

Ne engedélyezzen minden ütemezést, mert ez átfedésben lévő ütemezési műveleteket hozhat létre. A legjobb meghatározni, hogy mely optimalizálásokat szeretné tenni, és ennek megfelelően módosítani őket. További magyarázatért tekintse meg az áttekintés szakaszban található példaforgatókönyveket.

|Ütemezés neve | Gyakoriság | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | 8 **óránként AutoStop_CreateAlert_Parent** futtatja a runbookot, ami leállítja a , és változók virtuálisgép-alapú `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` értékeit. Másik lehetőségként megadhatja a virtuális gépek vesszővel elválasztott listáját a `VMList` paraméterrel.|
|Scheduled_StopVM | Felhasználó által megadott, naponta | Futtatja **ScheduledStopStart_Parent** runbookot a megadott `Stop` időpontban minden nap paraméterrel. Automatikusan leállítja az összes olyan virtuális gépét, amely megfelel a változó eszközök által meghatározott szabályoknak. Engedélyezze a kapcsolódó **Scheduled-StartVM ütemezést.**|
|Scheduled_StartVM | Felhasználó által megadott, naponta | Futtatja **ScheduledStopStart_Parent** runbookot a megadott `Start` időpontban minden nap paraméterértékével. Automatikusan elindítja az összes olyan virtuális gép, amely megfelel a változó eszközök által meghatározott szabályoknak. Engedélyezze a kapcsolódó **Scheduled-StopVM ütemezést.**|
|Sequenced-StopVM | Minden péntek 1:00-kor (UTC) | Futtatja **Sequenced_StopStop_Parent** runbookot a megadott időpontban minden péntek `Stop` paraméterértékkel. A szekvenciálisan (növekvő) leállítja az összes olyan virtuális gépét, amely a megfelelő változók által meghatározott **SequenceStop** címkével van megadva. A címkeértékekkel és az eszközváltozóval kapcsolatos további információkért lásd: [Runbookok.](#runbooks) Engedélyezze a kapcsolódó **ütemezést(Sequenced-StartVM).**|
|Sequenced-StartVM | Minden hétfőn 13:00 (UTC) | Futtatja **SequencedStopStart_Parent** runbookot a megadott `Start` időpontban minden hétfői paraméterértékkel. A szekvenciálisan (csökkenő) sorrendben elindítja az összes virtuális gépeket a megfelelő változók által meghatározott **SequenceStart** címkével. A címkeértékekkel és a változóeszközökkel kapcsolatos további információkért lásd: [Runbookok.](#runbooks) Engedélyezze a kapcsolódó **sequenced-stopVM ütemezést.**|

## <a name="use-the-feature-with-classic-vms"></a>A funkció használata klasszikus virtuális gépekkel

Ha a klasszikus virtuális Start/Stop VMs during off-hours használja, az Automation az összes virtuális gépét egymás után dolgozza fel felhőszolgáltatásonként. A virtuális gépeket továbbra is párhuzamosan, különböző felhőszolgáltatásokban kell feldolgozni. 

A funkció klasszikus virtuális gépeken való használatához klasszikus futtatású fiókra van szükség, amely alapértelmezés szerint nem jön létre. A klasszikusan futó fiókok létrehozásával vonatkozó utasításokért [lásd: Klasszikus futtatási fiók létrehozása.](automation-create-standalone-account.md#create-a-classic-run-as-account)

Ha több mint 20 virtuális gépe van felhőszolgáltatásonként, íme néhány javaslat:

* Hozzon létre több ütemezést a szülő runbook **ScheduledStartStop_Parent** és ütemezés szerint 20 virtuális gép megadása.
* Az ütemezés tulajdonságai között a paraméterrel vesszővel elválasztott listaként adhatja meg a virtuális gépek nevét `VMList` (szóközök nélkül).

Ellenkező esetben, ha a szolgáltatás Automation-feladata három óránál tovább fut, akkor ideiglenesen eltávolítja vagy le lesz állítva az igazságos [megosztási korlát szerint.](automation-runbook-execution.md#fair-share)

Azure CSP előfizetések csak a Azure Resource Manager támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. A Start/Stop VMs during off-hours szolgáltatás futtatásakor hibákat kaphat, mivel a klasszikus erőforrások kezelésére használható parancsmagokkal rendelkezik. További információ a CSP-ről: [Elérhető szolgáltatások CSP-előfizetésben.](/azure/cloud-solution-provider/overview/azure-csp-available-services) HA CSP-előfizetést használ, az üzembe helyezés után állítsa a External_EnableClassicVMs [változót](#variables) False (Hamis) értékre.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>A funkció megtekintése

Az engedélyezett funkció eléréséhez használja az alábbi mechanizmusok egyikét:

* Az Automation-fiókjában válassza a **Start/Stop VM (Virtuális gép létrehozása/leállítása)** lehetőséget a **Related Resources (Kapcsolódó erőforrások) területen.** A Start-Stop VM lapon válassza a **Manage the solution** (Megoldás kezelése) lehetőséget a Manage **Start/Stop VM Solutions (Virtuálisgép-megoldások kezelése) területen.**

* Lépjen az Automation-fiókjához csatolt Log Analytics-munkaterületre. A munkaterület kiválasztása után a bal **oldali panelen** válassza a Megoldások lehetőséget. A Megoldások lapon válassza a **Start-Stop-VM[workspace]** listában.  

A funkció kiválasztásakor megjelenik **a Start-Stop-VM[workspace]** lap. Itt áttekintheti a fontos részleteket, például a **StartStopVM csempén található** információkat. Ahogy a Log Analytics-munkaterületen is, ez a csempe megjeleníti azon runbook-feladatok számát és grafikus ábrázolásait, amelyek elindultak és sikeresen befejeződöttek.

![Automation Update Management lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A feladatrekordok további elemzését a fánkcsempére kattintva végezheti el. Az irányítópult megjeleníti a feladatelőzményeket és az előre meghatározott naplókeresési lekérdezéseket. Váltson a Log Analytics speciális portálra a keresési lekérdezések alapján való kereséshez.

## <a name="update-the-feature"></a>A funkció frissítése

Ha az alkalmazás egy korábbi verzióját telepítette, Start/Stop VMs during off-hours a frissített kiadás üzembe helyezése előtt törölje azt a fiókjából. A szolgáltatás eltávolításához kövesse [a](automation-solution-vm-management-remove.md#delete-the-feature) lépéseket, majd az [engedélyezéséhez kövesse az alábbi lépéseket.](automation-solution-vm-management-enable.md)

## <a name="next-steps"></a>Következő lépések

A szolgáltatásnak a környezetben virtuális gépeken való engedélyezéséhez lásd: [Enable Start/Stop VMs during off-hours](automation-solution-vm-management-enable.md).
