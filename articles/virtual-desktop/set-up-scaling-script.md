---
title: Munkamenet-gazdagépek méretezése Azure Automation – Azure
description: A Windows rendszerű virtuális asztali munkamenet-gazdagépek automatikus méretezése Azure Automation használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f60341ea51f1cf4e856b1b4598887da3dc37ebb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613119"
---
# <a name="scale-session-hosts-using-azure-automation"></a>A munkamenet-gazdagépek méretezése Azure Automation használatával

A virtuális gépek (VM-EK) skálázásával csökkentheti a Windows rendszerű virtuális asztali környezet teljes üzembe helyezési költségeit. Ez azt jelenti, hogy leállítja és felszabadítja a munkamenet-gazda virtuális gépeket a használaton kívüli órákban, majd visszakapcsolja őket a csúcsidőben, majd újra kiosztja őket.

Ebből a cikkből megtudhatja, hogyan épül fel a Azure Automation fiókkal és az Azure Logic apps szolgáltatással létrehozott skálázási eszköz, amely automatikusan méretezi a munkamenet-gazdagép virtuális gépeket a Windows rendszerű virtuális asztali környezetben. Ha szeretné megtudni, hogyan használhatja a skálázási eszközt, ugorjon előre az [Előfeltételek](#prerequisites)elemre.

## <a name="how-the-scaling-tool-works"></a>A skálázási eszköz működése

A skálázási eszköz alacsony költségű automatizálási lehetőséget biztosít azon ügyfelek számára, akik szeretnék optimalizálni a munkamenet-gazda virtuális gépek költségeit.

A skálázási eszköz használatával a következőket végezheti el:

- A virtuális gépeket a maximális és a Off-Peak munkaidő alapján kell elindítani és leállítani.
- Virtuális gépek vertikális felskálázása a munkamenetek száma alapján a CPU Core-ban.
- Off-Peak óra alatt méretezheti a virtuális gépeket, így a munkamenet-gazdagépek minimális száma fut.

A skálázási eszköz egy Azure Automation-fiók, egy PowerShell-runbook, egy webhook és az Azure Logic app függvényének kombinációját használja. Az eszköz futtatásakor az Azure Logic app meghívja a webhookot a Azure Automation runbook elindításához. A runbook Ezután létrehoz egy feladatot.

A maximális kihasználtság ideje alatt a feladattípus ellenőrzi a munkamenetek aktuális számát és az aktuálisan futó munkamenet-gazdagép virtuálisgép-kapacitását az egyes gazdagépek esetében. Ezt az információt használja annak kiszámításához, hogy a futó munkamenet-gazda virtuális gépek támogatják-e a meglévő munkameneteket a **CreateOrUpdateAzLogicApp.ps1** fájlhoz definiált *SessionThresholdPerCPU* paraméter alapján. Ha a munkamenet-gazdagép virtuális gépei nem támogatják a meglévő munkameneteket, a feladatokban további munkamenet-gazda virtuális gépek indulnak a gazdagép-készletben.

>[!NOTE]
>A *SessionThresholdPerCPU* nem korlátozza a virtuális gépen futó munkamenetek számát. Ez a paraméter csak azt határozza meg, hogy mikor kell elindítani az új virtuális gépeket a kapcsolatok terheléselosztásához. A munkamenetek számának korlátozásához kövesse az [Update-AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing) utasításait, hogy ennek megfelelően konfigurálja a *MaxSessionLimit* paramétert.

A maximális kihasználtsági idő alatt a feladattípus határozza meg, hogy hány virtuális gépet kell leállítani a *MinimumNumberOfRDSH* paraméter alapján. Ha a *LimitSecondsToForceLogOffUser* paramétert nem nulla értékű pozitív értékre állítja, a feladatokban a munkamenet-gazda virtuális gépek leeresztő módba lesznek állítva, hogy az új munkamenetek ne kapcsolódjanak a gazdagépekhez. Ekkor a rendszer értesíti a jelenleg bejelentkezett felhasználókat, hogy mentse a munkáját, várjon a beállított időtartamra, majd kényszerítse ki a felhasználókat a kijelentkezésre. Ha a munkamenet-gazda virtuális gépen lévő összes felhasználói munkamenet ki lett jelentkezve, a rendszer leállítja a virtuális gépet. A virtuális gép leállítása után a rendszer visszaállítja a munkamenet-gazdagép kiürítési módját.

>[!NOTE]
>Ha manuálisan állítja be a munkamenet-gazda virtuális gépet a kiürítési módba, a feladatok nem fogják kezelni a munkamenet-gazda virtuális gépet. Ha a munkamenet-gazda virtuális gép fut, és a kiürítési módba van állítva, akkor a rendszer nem elérhetőként kezeli, így a feladatok további virtuális gépeket indítanak a terhelés kezeléséhez. Javasoljuk, hogy minden Azure-beli virtuális gépet felcímkéz, mielőtt manuálisan beállította a kiürítési módba. A címkét a *MaintenanceTagName* paraméterrel nevezheti el az Azure Logic apps Scheduler későbbi létrehozásakor. A címkék segítségével megkülönböztetheti ezeket a virtuális gépeket a méretezési eszköz által felügyelt eszközöktől. A karbantartási címke beállítása azt is megakadályozza, hogy a méretezési eszköz addig ne módosítsa a virtuális gépet, amíg el nem távolítja a címkét.

Ha a *LimitSecondsToForceLogOffUser* paramétert nulla értékre állítja, a feladatok lehetővé teszik a megadott csoportházirendek munkamenet-konfigurációs beállítását a felhasználói munkamenetek kijelentkezésének kezeléséhez. A csoportházirendek megjelenítéséhez nyissa meg a **számítógép-konfigurációs**  >  **házirendek**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztal a munkamenet-kiszolgáló**  >  **munkamenet-korlátait**. Ha egy munkamenet-gazda virtuális gépen vannak aktív munkamenetek, a feladatokból a munkamenet-gazda virtuális gép fut. Ha nincs aktív munkamenet, a rendszer leállítja a munkamenet-gazda virtuális gépet.

A (z) feladatokban a gazdagép *MaxSessionLimit* is elvégezheti annak megállapítására, hogy a munkamenetek jelenlegi száma meghaladja-e a maximális kapacitás 90%-át. Ha igen, a feladatok további munkamenet-gazda virtuális gépeket indítanak el.

A feladatot rendszeresen futtatja egy beállított Ismétlődési intervallum alapján. Ezt az időközt a Windows rendszerű virtuális asztali környezet méretétől függően módosíthatja, de ne feledje, hogy a virtuális gépek indítása és leállítása hosszabb időt is igénybe vehet, ezért ne feledje, hogy a késést. Ajánlott 15 percenként beállítani az ismétlődési időközt.

Az eszköz azonban a következő korlátozásokkal is rendelkezik:

- Ez a megoldás csak a készletezett, több munkamenetet futtató munkamenet-gazda virtuális gépekre vonatkozik.
- Ez a megoldás bármely régióban felügyeli a virtuális gépeket, de csak az Azure Automation-fiókkal és az Azure Logic App-alkalmazással megegyező előfizetésben használható.
- A runbook-feladatok maximális futtatókörnyezete 3 óra. Ha a gazdagépen lévő virtuális gépek elindítása vagy leállítása hosszabb időt vesz igénybe, a feladat sikertelen lesz. További részletekért tekintse meg a [megosztott erőforrások](../automation/automation-runbook-execution.md#fair-share)című témakört.
- Legalább egy virtuális gépet vagy egy munkamenet-gazdagépet be kell kapcsolni a skálázási algoritmus megfelelő működéséhez.
- A skálázási eszköz nem támogatja a processzor vagy a memória alapján történő skálázást.
- A skálázás csak a gazdagép meglévő gazdagépei esetében működik. A skálázási eszköz nem támogatja az új munkamenet-gazdagépek skálázását.

>[!NOTE]
>A skálázási eszköz szabályozza a jelenleg méretezés alatt álló gazdagép terheléselosztási módját. Az eszköz a szélesség – első terheléselosztási módot használja mind a csúcs-, mind a kétórás időpontra.

## <a name="prerequisites"></a>Előfeltételek

A skálázási eszköz beállításának megkezdése előtt győződjön meg arról, hogy a következők állnak készen:

- Egy [Windows rendszerű virtuális asztali címkészlet](create-host-pools-azure-marketplace.md)
- A Windows rendszerű virtuális asztali szolgáltatásban konfigurált és regisztrált munkamenet-gazdagépek készletei
- Az Azure-előfizetés [közreműködői hozzáféréssel](../role-based-access-control/role-assignments-portal.md) rendelkező felhasználója

Az eszköz telepítéséhez használt gépnek rendelkeznie kell a következővel:

- Windows PowerShell 5,1 vagy újabb
- A Microsoft az PowerShell-modul

Ha minden készen áll, kezdjük a kezdéssel.

## <a name="create-or-update-an-azure-automation-account"></a>Azure Automation fiók létrehozása vagy frissítése

>[!NOTE]
>Ha már rendelkezik egy Azure Automation fiókkal egy olyan runbook, amely a skálázási parancsfájl egy régebbi verzióját futtatja, mindössze annyit kell tennie, hogy meggyőződjön arról, hogy frissítve van.

Először is szüksége lesz egy Azure Automation fiókra a PowerShell-runbook futtatásához. Az ebben a szakaszban ismertetett folyamat akkor is érvényes, ha rendelkezik egy meglévő Azure Automation-fiókkal, amelyet a PowerShell-runbook beállításához kíván használni. A következőképpen állíthatja be:

1. Nyissa meg a Windows PowerShellt.

2. A következő parancsmag futtatásával jelentkezzen be az Azure-fiókjába.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >A fióknak közreműködői jogosultságokkal kell rendelkeznie azon az Azure-előfizetésen, ahol a skálázási eszközt telepíteni szeretné.

3. Futtassa a következő parancsmagot a Azure Automation fiók létrehozásához szükséges parancsfájl letöltéséhez:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Futtassa a következő parancsmagot a parancsfájl végrehajtásához és a Azure Automation fiók létrehozásához. Kitöltheti a paraméterek értékeit, vagy megadhatja őket az alapértelmezett értékek használatára.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. A parancsmag kimenete tartalmaz egy webhook URI-t. Ügyeljen arra, hogy az URI rekordját tárolja, mert az Azure logikai alkalmazás végrehajtási ütemtervének beállításakor paraméterként fogja használni.

6. Ha a Log Analytics **WorkspaceName** paramétert adta meg, a parancsmag kimenete tartalmazza a log Analytics munkaterület azonosítóját és az elsődleges kulcsát is. Ügyeljen arra, hogy az URI-t jegyezze fel, mert később az Azure logikai alkalmazás végrehajtási ütemtervének beállításakor paraméterként kell használni.

7. Miután beállította Azure Automation-fiókját, jelentkezzen be az Azure-előfizetésbe, és ellenőrizze, hogy a Azure Automation-fiókja és a kapcsolódó runbook szerepeltek-e a megadott erőforráscsoporthoz, ahogy az alábbi képen is látható:

    >[!div class="mx-imgBorder"]
    >![Az Azure Áttekintés oldalának képe, amely az újonnan létrehozott Azure Automation fiókot és runbook jeleníti meg.](media/automation-account.png)

    Ha szeretné megnézni, hogy a webhook hol kell legyen, válassza ki a runbook nevét. Ezután nyissa meg a runbook erőforrásai szakaszt, és válassza a **webhookok** lehetőséget.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók létrehozása

Most, hogy már rendelkezik egy Azure Automation fiókkal, létre kell hoznia egy Azure Automation futtató fiókot is, ha még nem rendelkezik ilyennel. Ez a fiók lehetővé teszi az eszköz számára az Azure-erőforrások elérését.

Egy [Azure Automation futtató fiók](../automation/manage-runas-account.md) hitelesítést biztosít az Azure-beli erőforrások Azure-parancsmagokkal való kezeléséhez. Amikor létrehoz egy futtató fiókot, létrehoz egy új egyszerű szolgáltatásnevet a Azure Active Directoryban, és hozzárendeli a közreműködői szerepkört az egyszerű szolgáltatásnév-felhasználóhoz az előfizetés szintjén. Az Azure-beli futtató fiókok segítségével biztonságosan hitelesítheti a tanúsítványokat és az egyszerű szolgáltatásnév nevét anélkül, hogy a Felhasználónév és a Jelszó tárolása a hitelesítőadat-objektumban megtörténjen. További információ a futtató fiók hitelesítéséről: a [futtató fiók engedélyeinek korlátozása](../automation/manage-runas-account.md#limit-run-as-account-permissions).

Minden olyan felhasználó, aki tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés rendszergazdája is létrehozhat egy futtató fiókot.

Futtató fiók létrehozása a Azure Automation fiókban:

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájában adja meg és válassza az **Automation-fiókok** elemet.

2. Az **Automation-fiókok** lapon válassza ki a Azure Automation fiókjának nevét.

3. Az ablak bal oldalán lévő ablaktáblán válassza a **futtató fiókok** lehetőséget a **Fiókbeállítások** szakaszban.

4. Válassza az Azure-beli **futtató fiók** lehetőséget. Amikor megjelenik az Azure-beli **futtató fiók hozzáadása** panel, tekintse át az áttekintő információkat, majd kattintson a **Létrehozás** elemre a fiók létrehozási folyamatának elindításához.

5. Várjon néhány percet, amíg az Azure létrehozza a futtató fiókot. A létrehozási folyamat nyomon követhető a menüben az értesítések területen.

6. A folyamat befejezésekor a rendszer létrehoz egy **azurerunasconnection elemet** nevű eszközt a megadott Azure Automation fiókban. Válassza az Azure-beli **futtató fiók** lehetőséget. A szolgáltatás tartalmazza az alkalmazás AZONOSÍTÓját, a bérlő AZONOSÍTÓját, az előfizetés AZONOSÍTÓját és a tanúsítvány ujjlenyomatát. Ugyanezeket az információkat a **kapcsolatok** oldalon is megtalálhatja. A lap megnyitásához az ablak bal oldalán lévő ablaktáblán válassza a **kapcsolatok** lehetőséget a **megosztott erőforrások** szakaszban, és kattintson a **azurerunasconnection elemet** nevű kapcsolati eszközre.

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Az Azure Logic app és a végrehajtási ütemterv létrehozása

Végezetül létre kell hoznia az Azure logikai alkalmazást, és be kell állítania egy végrehajtási ütemtervet az új méretezési eszközhöz. Először töltse le és importálja a PowerShell-munkamenetben használni kívánt [asztali virtualizálási PowerShell-modult](powershell-module.md) , ha még nem tette meg.

1. Nyissa meg a Windows PowerShellt.

2. A következő parancsmag futtatásával jelentkezzen be az Azure-fiókjába.

    ```powershell
    Login-AzAccount
    ```

3. Futtassa az alábbi parancsmagot az Azure Logic app létrehozásához szükséges parancsfájl letöltéséhez.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. A következő PowerShell-szkript futtatásával hozza létre az Azure Logic app-alkalmazást és a futtatási ütemtervet a gazdagép készletéhez

    >[!NOTE]
    >Ezt a parancsfájlt minden olyan gazdagéphez futtatnia kell, amelyet szeretne használni az autoskálázáshoz, de csak egy Azure Automation fiókra van szüksége.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    A szkript futtatása után az Azure logikai alkalmazásnak szerepelnie kell egy erőforráscsoporthoz az alábbi ábrán látható módon.

    >[!div class="mx-imgBorder"]
    >![Egy példa az Azure logikai alkalmazás áttekintés lapjára.](media/logic-app.png)

    A végrehajtás ütemezésének módosításához, például az Ismétlődési intervallum vagy az időzóna módosításához nyissa meg az Azure Logic app autoscale schedulert, és válassza a **Szerkesztés** lehetőséget az Azure Logic app Designerben való ugráshoz.

    >[!div class="mx-imgBorder"]
    >![Az Azure Logic app Designer képe. A felhasználó ismétlődési idejének módosítására és a webhook fájljának megnyitására szolgáló ismétlődési és webhook-menük.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Méretezési eszköz kezelése

Most, hogy létrehoztuk a skálázási eszközt, elérheti a kimenetét. Ez a szakasz néhány olyan funkciót ismertet, amelyek hasznosak lehetnek.

### <a name="view-job-status"></a>Feladat állapotának megtekintése

Megtekintheti az összes runbook-feladat összegzett állapotát, vagy megtekintheti egy adott runbook-feladat alaposabb állapotát a Azure Portal.

A kiválasztott Azure Automation-fiók jobb oldalán, a "feladat statisztikái" alatt megtekintheti az összes runbook-feladat összefoglalóit tartalmazó listát. Az ablak bal oldalán a **feladatok** lap megnyitása megjeleníti a feladat aktuális állapotát, a kezdési időpontokat és a befejezési időpontokat.

>[!div class="mx-imgBorder"]
>![A feladatok állapota lap képernyőképe.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Naplók és méretezési eszköz kimenetének megtekintése

A kibővíthető és a skálázási műveletek naplóit megtekintheti a runbook megnyitásával és a feladatok kiválasztásával.

Navigáljon a Azure Automation fiókot futtató erőforráscsoport runbook, és válassza az **Áttekintés** lehetőséget. Az Áttekintés lapon válasszon ki egy feladatot a **legutóbbi feladatok** területen a méretezési eszköz kimenetének megtekintéséhez, ahogy az alábbi képen is látható.

>[!div class="mx-imgBorder"]
>![A skálázási eszköz kimeneti ablakának képe.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>A runbook parancsfájl verziószámának keresése

A runbook Azure Automation-fájl megnyitásához nyissa meg a runbook-parancsfájlt, és válassza a **nézet** lehetőséget. A képernyő jobb oldalán megjelenik egy parancsfájl a runbook. A szkriptben a verziószámot a szakasz alatti formátumban fogja látni `v#.#.#` `SYNOPSIS` . [Itt](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1)megtekintheti a legújabb verziószámot. Ha nem lát verziószámot a runbook-szkriptben, ez azt jelenti, hogy a parancsfájl egy korábbi verzióját futtatja, és azonnal frissítenie kell. Ha frissítenie kell a runbook-parancsfájlt, kövesse az [Azure Automation fiók létrehozása vagy frissítése](#create-or-update-an-azure-automation-account)című témakör utasításait.

### <a name="reporting-issues"></a>Jelentéskészítési problémák

Ha problémát jelent, a következő információkat kell megadnia a hibák megoldásához:

- A problémát okozó feladatok **összes napló** lapján található teljes napló. A napló beszerzésének megismeréséhez kövesse a [naplók megtekintése és a skálázási eszköz kimenete](#view-logs-and-scaling-tool-output)című témakör utasításait. Ha a naplóban bármilyen bizalmas vagy magánjellegű információ található, akkor azt a probléma elküldése előtt el is távolíthatja.

- Az Ön által használt runbook-szkript verziója. Ha szeretné megtudni, hogyan kérheti le a verziószámot, tekintse meg [a runbook-parancsfájl verziószámát](#check-the-runbook-script-version-number) .

- A Azure Automation-fiókba telepített alábbi PowerShell-modulok verziószáma. Ezeknek a moduloknak a megkereséséhez nyissa meg Azure Automation fiókot, válassza ki a **modulok** lehetőséget az ablak bal oldalán található **megosztott erőforrások** szakaszban, majd keresse meg a modul nevét.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Az.DesktopVirtualization

- A [futtató fiók](#create-an-azure-automation-run-as-account)lejárati dátuma. Ennek megkereséséhez nyissa meg Azure Automation-fiókját, majd válassza a **fiók beállításai** alatt a **futtató fiókok** lehetőséget az ablak bal oldalán lévő ablaktáblán. A lejárati dátumnak az Azure-beli **futtató fiók** alatt kell lennie.

### <a name="log-analytics"></a>Log Analytics

Ha úgy döntött, hogy Log Analytics használ, a Log Analytics munkaterület **naplók** nézetében megtekintheti az összes naplózási adatát egy **WVDTenantScale_CL** nevű egyéni naplóban az **egyéni naplók** területen. Felsorolunk néhány, hasznosnak talált minta lekérdezést.

- A gazdagépek összes naplójának megtekintéséhez adja meg a következő lekérdezést:

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- A jelenleg futó munkamenet-gazdagépek és az aktív felhasználói munkamenetek teljes számának megtekintéséhez a gazdagép-készletben adja meg a következő lekérdezést:

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- A gazdagépen futó összes virtuális gép állapotának megtekintéséhez adja meg a következő lekérdezést:

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- A hibák és figyelmeztetések megtekintéséhez írja be a következő lekérdezést

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Problémák bejelentése

A skálázási eszközre vonatkozó jelentéseket jelenleg Microsoft ügyfélszolgálata kezeli. Amikor jelentést készít, mindenképp kövesse a [jelentéskészítéssel kapcsolatos problémák](#reporting-issues)című témakör utasításait. Ha visszajelzést kér az eszközről, vagy új szolgáltatásokat szeretne kérni, nyisson meg egy "4-WVD-skálázás – eszköz" címkével ellátott GitHub-problémát az [RDS GitHub oldalán](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool).
