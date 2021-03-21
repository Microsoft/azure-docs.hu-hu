---
title: PowerShell-minták Azure Monitor
description: A PowerShell használatával férhet hozzá olyan Azure Monitor-funkciókhoz, mint például az autoscale, a riasztások, a webhookok és a keresési tevékenységek naplói.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1aa0d44dcb73a925494c0bc4aae43dc93e5a33d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047060"
---
# <a name="azure-monitor-powershell-samples"></a>PowerShell-minták Azure Monitor
Ebből a cikkből megtudhatja, hogyan érheti el Azure Monitor szolgáltatásait a PowerShell-parancsok segítségével.

> [!NOTE]
> A Azure Monitor az "Azure-betekintő" nevű új név, 2016. szeptember 25-én. Azonban a névterek és így a következő parancsok továbbra is tartalmazzák a Word- *ismereteket*.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>A PowerShell beállítása
Ha még nem tette meg, állítsa be a PowerShellt úgy, hogy a számítógépen fusson. További információ: [a PowerShell telepítése és konfigurálása](/powershell/azure/).

## <a name="examples-in-this-article"></a>Példák ebben a cikkben
A cikkben szereplő példák bemutatják, hogyan használhatja Azure Monitor parancsmagokat. Áttekintheti Azure Monitor PowerShell-parancsmagok teljes listáját Azure Monitor (betekintő [) parancsmagok](/powershell/module/az.applicationinsights)esetén is.

## <a name="sign-in-and-use-subscriptions"></a>Bejelentkezés és előfizetés használata
Először jelentkezzen be az Azure-előfizetésbe.

```powershell
Connect-AzAccount
```

Ekkor megjelenik a bejelentkezési képernyő. Miután bejelentkezett a fiókjába, a TenantID és az alapértelmezett előfizetés-azonosító megjelenik. Az összes Azure-parancsmag az alapértelmezett előfizetés kontextusában működik. A következő parancs használatával tekintheti meg az Ön számára elérhető előfizetések listáját:

```powershell
Get-AzSubscription
```

A következő parancs futtatásával megtekintheti a munkakörnyezetét (mely előfizetéssel futtatja a parancsokat):

```powershell
Get-AzContext
```
Ha módosítani szeretné a munkakörnyezetét egy másik előfizetésre, használja a következő parancsot:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Tevékenység naplójának beolvasása
Használja a [Get-AzLog](/powershell/module/az.monitor/get-azlog) parancsmagot.  Az alábbiakban néhány gyakori példát talál. A Tevékenységnapló az elmúlt 90 nap műveleteit tárolja. Az idő előtti dátumok használata hibaüzenetet eredményez.  

Az alábbi parancsokban megtekintheti, hogy az aktuális dátum/idő milyen időpontot használjon:
```powershell
Get-Date
```

Naplóbejegyzések beolvasása ebből az időpontból/dátumból:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Naplóbejegyzések beolvasása egy idő/dátumtartomány között:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzések beolvasása egy adott erőforráscsoporthoz:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

A naplóbejegyzések beolvasása egy adott erőforrás-szolgáltatótól egy idő/dátumtartomány között:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Minden naplóbejegyzés beolvasása egy megadott hívóval:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

A következő parancs lekéri az utolsó 1000 eseményt a tevékenység naplójából:

```powershell
Get-AzLog -MaxRecord 1000
```

`Get-AzLog` számos más paramétert is támogat. `Get-AzLog`További információért tekintse meg a referenciát.

> [!NOTE]
> `Get-AzLog` csak 15 napos előzményt biztosít. 15 napon túl a **-MaxRecords** paraméterrel kérdezheti le az utolsó N eseményt. A 15 napnál régebbi események eléréséhez használja a REST API-t vagy az SDK-t (C#-példa az SDK-t használva). Ha nem adja meg a **StartTime** értékét, akkor az alapértelmezett érték az **EndTime** értéke mínusz egy óra. Ha nem adja meg az **EndTime** értékét, akkor az alapértelmezett érték az aktuális időpont. Minden időpont a UTC időzónában van megadva.
> 
> 

## <a name="retrieve-alerts-history"></a>Riasztások előzményeinek beolvasása
Az összes riasztási esemény megtekintéséhez a következő példákkal kérdezheti le a Azure Resource Manager naplókat.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Egy adott riasztási szabály előzményeinek megtekintéséhez használhatja a `Get-AzAlertHistory` parancsmagot, amely a riasztási szabály erőforrás-azonosítójában adható meg.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

A `Get-AzAlertHistory` parancsmag a különböző paramétereket támogatja. További információ: [Get-AlertHistory](/previous-versions/azure/mt282453(v=azure.100)).

## <a name="retrieve-information-on-alert-rules"></a>Riasztási szabályokkal kapcsolatos információk lekérése
A következő parancsok mindegyike egy "legszélesebb" nevű erőforráscsoport esetében működik.

A riasztási szabály összes tulajdonságának megtekintése:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Egy erőforráscsoport összes riasztásának beolvasása:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

A célként megadott erőforráshoz beállított összes riasztási szabály beolvasása. Például a virtuális gépen beállított összes riasztási szabály.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` más paramétereket is támogat. További információ: [Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) .

## <a name="create-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások létrehozása
A `Add-AlertRule` parancsmag használatával riasztási szabályt hozhat létre, frissíthet vagy tilthatja le.

E-mailek és webhook-tulajdonságok a és a használatával is létrehozhatók  `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook` . A riasztási szabály parancsmagban ezeket a tulajdonságokat adja hozzá műveletként a riasztási szabály **műveletek** tulajdonságához.

A következő táblázat a riasztások metrika használatával történő létrehozásához használt paramétereket és értékeket ismerteti.

| parameter | érték |
| --- | --- |
| Name |simpletestdiskwrite |
| A riasztási szabály helye |USA keleti régiója |
| ResourceGroup |legtöbbször |
| Targetresourceid azonosítója |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| A létrehozott riasztás MetricName |\PhysicalDisk (_Total) \ írás gyakorisága (művelet/s). Tekintse `Get-MetricDefinitions` meg a pontos metrikák nevének beolvasását ismertető parancsmagot. |
| operátor |GreaterThan |
| Küszöbérték (darabszám/mp a metrika esetében) |1 |
| WindowSize (óó: PP: SS formátum) |00:05:00 |
| gyűjtő (a metrika statisztikája, amely az átlagot használja, ebben az esetben) |Átlag |
| Egyéni e-mailek (karakterlánc-tömb) |'foo@example.com','bar@example.com' |
| e-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak |-SendToServiceOwners |

E-mail művelet létrehozása

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Webhook-művelet létrehozása

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Riasztási szabály létrehozása a CPU% metrika szolgáltatásban klasszikus virtuális gépen

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

A riasztási szabály beolvasása

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

A riasztás hozzáadása parancsmag szintén frissíti a szabályt, ha már létezik riasztási szabály a megadott tulajdonságokhoz. A riasztási szabályok letiltásához adja meg a **-DisableRule** paramétert.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>A riasztásokhoz elérhető metrikák listájának beolvasása
A `Get-AzMetricDefinition` parancsmag segítségével megtekintheti az adott erőforráshoz tartozó összes metrika listáját.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Az alábbi példa egy táblázatot hoz létre a metrika nevével és a hozzá tartozó egységgel.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

A rendelkezésre álló lehetőségek teljes listája a `Get-AzMetricDefinition` [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100))címen érhető el.

## <a name="create-and-manage-activity-log-alerts"></a>Műveletnapló-riasztások létrehozása és kezelése
A `Set-AzActivityLogAlert` parancsmag segítségével beállíthatja a műveletnapló riasztását. A műveletnapló riasztásához először meg kell határoznia a feltételeket szótárként, majd létre kell hoznia egy riasztást, amely ezeket a feltételeket használja.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

A további webhook-tulajdonságok nem kötelezőek. Egy műveletnapló-riasztás tartalmát a használatával kérheti vissza `Get-AzActivityLogAlert` .

## <a name="create-and-manage-autoscale-settings"></a>Az autoskálázási beállítások létrehozása és kezelése

> [!NOTE] 
> Cloud Services (Microsoft. ClassicCompute) esetében az autoskálázás 5 perces időtartamot (PT5M) támogat. A többi szolgáltatás esetében az autoscale legalább 1 perces időtartamot támogat (PT1M)

Egy erőforrás (webalkalmazás, virtuális gép, felhőalapú szolgáltatás vagy virtuálisgép-méretezési csoport) csak egy, a számára konfigurált méretezési beállítással rendelkezhet.
Az egyes autoskálázási beállítások azonban több profillal is rendelkezhetnek. Például egy a teljesítmény-alapú méretezési profilhoz, egy másik pedig egy ütemezett profilhoz. Minden profilhoz több szabály is konfigurálható. Az autoscale használatával kapcsolatos további információkért lásd az [alkalmazások autoskálázását](../cloud-services/cloud-services-how-to-scale-portal.md)ismertető témakört.

A következő lépéseket kell használni:

1. Szabály (ok) létrehozása.
2. Profil (ok) létrehozása a korábban létrehozott szabályoknak a profilokhoz való hozzárendelésével.
3. Nem kötelező: értesítések létrehozása az autoskálázáshoz webhook és e-mail-tulajdonságok konfigurálásával.
4. Az előző lépésekben létrehozott profilok és értesítések leképezésével hozzon létre egy, a cél erőforrásban található névvel rendelkező autoskálázási beállítást.

Az alábbi példák bemutatják, hogyan hozhat létre egy virtuálisgép-méretezési csoportra vonatkozó, a CPU-kihasználtság metrikája alapján egy Windows operációs rendszerhez készült, a virtuális gépek méretezésére szolgáló beállítást.

Először hozzon létre egy szabályt a felskálázáshoz, és növelje a példányszámot.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Ezután hozzon létre egy, a skálázáshoz használandó szabályt a példányszám csökkenésével.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Ezután hozzon létre egy profilt a szabályokhoz.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Hozzon létre egy webhook-tulajdonságot.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Hozza létre az értesítési tulajdonságot az autoskálázási beállításhoz, beleértve az e-maileket és a korábban létrehozott webhookot.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Végül hozza létre az autoskálázási beállítást a korábban létrehozott profil hozzáadásához. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Az autoscale-beállítások kezelésével kapcsolatos további információkért lásd: [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)).

## <a name="autoscale-history"></a>Az autoskálázás előzményei
Az alábbi példa bemutatja, hogyan tekintheti meg a legutóbbi méretezési és riasztási eseményeket. A tevékenység naplójának keresési funkciójának használatával megtekintheti az autoskálázási előzményeket.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

A `Get-AzAutoScaleHistory` parancsmag használatával lekérheti az automatikusan méretezhető előzményeket.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

További információ: [Get-AutoscaleHistory](/previous-versions/azure/mt282464(v=azure.100)).

### <a name="view-details-for-an-autoscale-setting"></a>Egy autoskálázási beállítás részleteinek megtekintése
A `Get-Autoscalesetting` parancsmaggal további információkat kérhet le az automatikusan méretezhető beállításról.

Az alábbi példa a "myrg1" erőforráscsoport összes autoskálázási beállításának részleteit mutatja be.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Az alábbi példa a "myrg1" erőforráscsoport összes autoskálázási beállításának részleteit mutatja be, pontosabban a "MyScaleVMSSSetting" nevű autoskálázási beállítást.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Egy autoskálázási beállítás eltávolítása
A `Remove-Autoscalesetting` parancsmag használatával törölheti az egy autoskálázási beállítást.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>A műveletnapló profiljainak kezelése
Létrehozhat egy log- *profilt* , és exportálhatja az adatait a tevékenység naplójából egy Storage-fiókba, és beállíthatja az adatmegőrzést. Igény szerint továbbíthatja az adatait az Event hub-ba is. Ez a szolgáltatás jelenleg előzetes verzióban érhető el, és előfizetéshez csak egy naplózási profilt hozhat létre. A következő parancsmagokat használhatja a jelenlegi előfizetésével a log profilok létrehozásához és kezeléséhez. Kiválaszthat egy adott előfizetést is. Habár a PowerShell alapértelmezés szerint a jelenlegi előfizetést használja, bármikor módosíthatja a következőt: `Set-AzContext` . A műveletnapló konfigurálásával az adott előfizetésen belül bármely Storage-fiókra vagy Event hub-ra irányíthatja az adatútvonalat. Az adatfájlok JSON formátumú blob-fájlként íródnak.

### <a name="get-a-log-profile"></a>Log-profil beolvasása
A meglévő log-profilok beolvasásához használja a `Get-AzLogProfile` parancsmagot.

### <a name="add-a-log-profile-without-data-retention"></a>Adatmegőrzés nélküli log-profil hozzáadása
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Log-profil eltávolítása
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adatmegőrzést biztosító log-profil hozzáadása
Megadhatja a **-RetentionInDays** tulajdonságot a napok számával, pozitív egészként, ahol az adat megmarad.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Naplózási profil hozzáadása a megőrzéssel és a EventHub
Az adatok Storage-fiókba való továbbítása mellett azt is megteheti, hogy egy Event hub-ba is továbbíthatja azt. Ebben az előzetes kiadásban a Storage-fiók konfigurációja kötelező, de az Event hub konfigurációja nem kötelező.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnosztikai naplók konfigurálása
Számos Azure-szolgáltatás további naplókat és telemetria biztosít, amelyek a következők közül választhatnak: 
 - konfigurálható az Azure Storage-fiókban tárolt adatmentéshez
 - elküldve Event Hubs
 - elküldjük egy Log Analytics munkaterületre. 

A műveletet csak erőforrás szintjén lehet végrehajtani. A Storage-fióknak vagy az Event hub-nek ugyanabban a régióban kell lennie, mint a diagnosztikai beállítást futtató célként megadott erőforrásnak.

### <a name="get-diagnostic-setting"></a>Diagnosztikai beállítás beolvasása
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnosztikai beállítások letiltása

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnosztikai beállítás engedélyezése megőrzés nélkül

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Diagnosztikai beállítás engedélyezése megőrzéssel

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnosztikai beállítás engedélyezése egy adott naplózási kategória megőrzésével

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnosztikai beállítás engedélyezése Event Hubs

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Diagnosztikai beállítás engedélyezése Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Vegye figyelembe, hogy a munkaterület azonosítója tulajdonság a munkaterület *erőforrás-azonosítóját* veszi át. A Log Analytics munkaterület erőforrás-AZONOSÍTÓját a következő paranccsal szerezheti be:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Ezek a parancsok kombinálhatók több célhelyre történő adatküldéshez is.
