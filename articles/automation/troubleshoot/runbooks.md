---
title: Runbook Azure Automation problémák elhárítása
description: Ez a cikk bemutatja, hogyan háríthatja el és háríthatja el a runbookokkal Azure Automation problémákat.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 7964bc62aefc912a0f61744841784600575c98de
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831221"
---
# <a name="troubleshoot-runbook-issues"></a>Runbookkal kapcsolatos hibák elhárítása

 Ez a cikk a runbookok lehetséges problémáit és azok megoldását ismerteti. Általános információkért lásd: [Runbook végrehajtása a Azure Automation.](../automation-runbook-execution.md)

## <a name="diagnose-runbook-issues"></a>Runbookokkal kapcsolatos problémák diagnosztizálása

Ha hibákat kap a runbook Azure Automation, a következő lépésekkel diagnosztizálhatja a problémákat:

1. Győződjön meg arról, hogy a Runbook-szkript sikeresen lefut a helyi gépen.

    Nyelvi referencia- és tanulási modulokért lásd: [PowerShell Docs](/powershell/scripting/overview) vagy [Python Docs.](https://docs.python.org/3/) A szkript helyi futtatása felderítheti és megoldhatja a gyakori hibákat, például:

      * Hiányzó modulok
      * Szintaktikai hibák
      * Logikai hibák

1. Vizsgálja meg a runbook [hibastreameket.](../automation-runbook-output-and-messages.md#runbook-output)

    Nézze meg ezeket a streameket adott üzenetekért, és hasonlítsa össze őket a cikkben dokumentált hibákkal.

1. Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület tartalmazza a szükséges modulokat.

    Ha a runbook importál modulokat, a Modulok importálása lépéseit követve ellenőrizze, hogy elérhetők-e az [Automation-fiók számára.](../shared-resources/modules.md#import-modules) Frissítse a PowerShell-modulokat a legújabb verzióra az Update Azure PowerShell modules in Azure Automation ( [frissítés) Azure Automation.](../automation-update-azure-modules.md) További hibaelhárítási információkért lásd: [Modulok hibaelhárítása.](shared-resources.md#modules)

1. Ha a runbook fel van függesztve vagy váratlanul meghiúsul:

    * [Ha a futó fiók](../manage-runas-account.md#cert-renewal) lejárt, újítsa meg a tanúsítványt.
    * [Ha lejárt webhookot](../automation-webhooks.md#renew-a-webhook) próbál használni a runbook elindítani, újítsa meg a webhookot.
    * [Ellenőrizze a feladat állapotát](../automation-runbook-execution.md#job-statuses) a runbook aktuális állapotának és a probléma néhány lehetséges okának megállapításához.
    * [Adjon hozzá további kimenetet](../automation-runbook-output-and-messages.md#working-with-message-streams) a runbookhoz, hogy megállapítsa, mi történik a runbook felfüggesztése előtt.
    * [Kezelje a](../automation-runbook-execution.md#exceptions) feladat által okozott kivételeket.

1. Ezt a lépést akkor kell megtennie, ha a runbook-feladat vagy a hibrid runbook-feldolgozó környezete nem válaszol.

    Ha a runbookokat egy hibrid runbook-feldolgozón futtatja a Azure Automation helyett, előfordulhat, hogy magának a hibrid feldolgozónak a hibaelhárítását is el kell [hárítani.](hybrid-runbook-worker.md)

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Forgatókönyv: A runbook "Nincs engedély" vagy Tiltott 403 hiba miatt meghiúsul

### <a name="issue"></a>Probléma

A runbook "No permission" (Nincs engedély) vagy Forbidden 403 (Tiltott 403) hibával vagy ezzel egyenértékű hibával meghiúsul.

### <a name="cause"></a>Ok

Előfordulhat, hogy a futtatott fiókok nem ugyanazokkal az engedélyekkel rendelkezik az Azure-erőforrásokon, mint az aktuális Automation-fiók. 

### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a futtatott fiók rendelkezik a szkriptben használt [erőforrások](../../role-based-access-control/role-assignments-portal.md) elérésére vonatkozó engedélyekkel.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Forgatókönyv: Nem sikerült bejelentkezni az Azure-fiókba

### <a name="issue"></a>Probléma

A parancsmaggal való munka során a következő hibák `Connect-AzAccount` valamelyike lép fel:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Ok

Ezek a hibák akkor fordulnak elő, ha a hitelesítő adateszköz neve érvénytelen. Akkor is előfordulhatnak, ha az Automation hitelesítőadat-eszköz beállításához használt felhasználónév és jelszó érvénytelen.

### <a name="resolution"></a>Feloldás

A hiba megállapításához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy nem tartalmaz különleges karaktereket. Ezek a karakterek tartalmazzák az Automation hitelesítőadat-eszköz nevének az `\@` Azure-hoz való csatlakozáshoz használt karakterét.
1. Ellenőrizze, hogy használhatja-e a helyi PowerShell ISE-szerkesztőben az Azure Automation hitelesítő adataiban tárolt felhasználónevet és jelszót. Futtassa a következő parancsmagokat a PowerShell ISE-ben.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Ha a hitelesítés helyileg sikertelen, nem megfelelően beállította Azure Active Directory hitelesítő adatait az Azure AD-hez. Az Azure AD-fiók megfelelő beállításával a Hitelesítés az [Azure-ban](../automation-use-azure-ad.md)a következő Azure Active Directory.

1. Ha a hiba átmenetinek tűnik, próbáljon újrapróbálkozási logikát hozzáadni a hitelesítési rutinhoz, hogy még robusztusabb legyen a hitelesítés.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Forgatókönyv: A Login-AzureRMAccount futtatása a bejelentkezéshez

### <a name="issue"></a>Probléma

Runbook futtatásakor a következő hibaüzenet jelenik meg:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha nem használ egy futó fiókot, vagy a fiók lejárt. További információkért lásd a Azure Automation fiókok áttekintését ismertető [témakört.](../automation-security-overview.md#run-as-accounts)

Ennek a hibának két fő oka van:

* Az AzureRM vagy az Az modulnak különböző verziói vannak.
* Egy külön előfizetésben található erőforrásokhoz próbál hozzáférni.

### <a name="resolution"></a>Feloldás

Ha ezt a hibaüzenetet kapja egy AzureRM- vagy Az-modul frissítése után, frissítse az összes modult ugyanerre a verzióra.

Ha egy másik előfizetés erőforrásaihoz próbál hozzáférni, kövesse az alábbi lépéseket az engedélyek konfigurálásához:

1. Ugrás az Automation-ként futó fiókra, és másolja ki az **alkalmazásazonosítót** és az **ujjlenyomatot.**

    ![Alkalmazásazonosító és ujjlenyomat másolása](../media/troubleshoot-runbooks/collect-app-id.png)

1. Ha az Automation-fiókot nem üzemelteti, az előfizetés hozzáférés-vezérléséhez kell hozzáférnie, és adjon hozzá egy új szerepkör-hozzárendelést.  

    ![Hozzáférés-vezérlés](../media/troubleshoot-runbooks/access-control.png)

1. Adja hozzá **a korábban** összegyűjtött alkalmazásazonosítót. Válassza **a Közreműködői** engedélyek lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Másolja ki az előfizetés nevét.

1. Most már használhatja a következő Runbook-kódot az Automation-fiók és a másik előfizetés engedélyeinek teszteléséhez. Cserélje `<CertificateThumbprint>` le a helyére az 1. lépésben másolt értéket. Cserélje `"<SubscriptionName>"` le a helyére a 4. lépésben másolt értéket.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Forgatókönyv: Nem található az Azure-előfizetés

### <a name="issue"></a>Probléma

A , vagy parancsmaggal való munka során a következő hibaüzenet `Select-AzureSubscription` `Select-AzureRMSubscription` jelenik `Select-AzSubscription` meg:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hiba

Ez a hiba a következő esetben fordulhat elő:

* Az előfizetés neve érvénytelen.
* Az előfizetési adatokat lekért Azure AD-felhasználó nincs konfigurálva az előfizetés rendszergazdájaként.
* A parancsmag nem érhető el.

### <a name="resolution"></a>Feloldás

Kövesse az alábbi lépéseket annak megállapításához, hogy hitelesített-e az Azure-ban, és rendelkezik-e hozzáféréssel a kiválasztani kívánt előfizetéshez:

1. Annak érdekében, hogy a szkript különállóan is használható legyen, tesztelje a Azure Automation.
1. A parancsmag futtatása előtt győződjön meg arról, hogy a szkript futtatja a [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) `Select-*` parancsmagot.
1. Adja `Disable-AzContextAutosave -Scope Process` hozzá a következőt a runbook elejéhez: . Ez a parancsmag biztosítja, hogy minden hitelesítő adat csak az aktuális runbook végrehajtására vonatkozik.
1. Ha továbbra is megjelenik a hibaüzenet, módosítsa a kódot a paraméterének hozzáadásával, majd `AzContext` `Connect-AzAccount` hajtsa végre a kódot.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Forgatókönyv: Több előfizetés esetén a runbookok meghiúsulnak

### <a name="issue"></a>Probléma

Runbookok végrehajtásakor a runbook nem tudja kezelni az Azure-erőforrásokat.

### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetet használja a futtatásakor. Ennek az lehet az oka, hogy a runbook véletlenül a helytelen előfizetéshez próbál hozzáférni.

Ehhez hasonló hibákat láthat:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Feloldás

Az előfizetési környezet elveszhet, ha egy runbook több runbookot hív meg. Annak érdekében, hogy véletlenül ne próbálja meg elérni a helytelen előfizetést, kövesse az alábbi útmutatást.

* A nem megfelelő előfizetésre való hivatkozás elkerülése érdekében tiltsa le a környezet mentését az Automation-runbookok között az egyes runbookok elején a következő kóddal.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* A Azure PowerShell parancsmagok támogatják a `-DefaultProfile` paramétert. Ez az összes Az és AzureRm parancsmaghoz hozzá lett adva, hogy támogassa több PowerShell-szkript futtatását ugyanabban a folyamatban, így megadhatja a környezetet és az egyes parancsmagok által használni kívánt előfizetést. A runbookok esetében a runbook létrehozásakor (azaz amikor egy fiók bejelentkezik) és minden egyes alkalommal mentenie kell a környezeti objektumot a runbookba, és minden alkalommal, amikor módosul, és hivatkozni kell a környezetre az Az parancsmag megadásakor.

   > [!NOTE]
   > Környezeti objektumot akkor is át kell adni, ha a környezetet közvetlenül a [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) vagy a [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription)parancsmagokkal kell manipulálni.

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Forgatókönyv: Az Azure-hitelesítés meghiúsul, mert a többtényezős hitelesítés engedélyezve van

### <a name="issue"></a>Probléma

Az Azure-ban az Azure-beli felhasználónévvel és jelszóval való hitelesítéskor a következő hibaüzenet jelenik meg:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Ok

Ha azure-fiókjában többtényezős hitelesítést használ, nem használhat többtényezős Azure Active Directory az Azure-ban való hitelesítéshez. Ehelyett tanúsítványt vagy egyszerű szolgáltatást kell használnia a hitelesítéshez.

### <a name="resolution"></a>Feloldás

Ha klasszikus futtatású fiókot használ a klasszikus Azure-beli üzembe helyezési modell parancsmagokkal, tekintse meg a klasszikus run as-fiók Azure-szolgáltatások kezeléséhez való [létrehozásáról való használatát.](../automation-create-standalone-account.md#create-a-classic-run-as-account) Ha szolgáltatásnévvel vagy parancsmagokkal Azure Resource Manager, tekintse [](../../active-directory/develop/howto-create-service-principal-portal.md) meg a Szolgáltatásnév létrehozása a Azure Portal használatával és a Szolgáltatásnév hitelesítése [a](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)Azure Resource Manager.

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Forgatókönyv: A runbook "Egy feladat megszakadt" hibaüzenettel meghiúsul

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Ok

Ezt a hibát az elavult Azure-modulok okozhatják.

### <a name="resolution"></a>Feloldás

Ezt a hibát az Azure-modulok legújabb verzióra való frissítésével oldhatja meg:

1. Az Automation-fiókjában válassza a **Modulok,** majd az **Azure-modulok frissítése lehetőséget.**
1. A frissítés körülbelül 15 percet vesz igénybe. Ha befejeződött, futtassa újra a sikertelen runbookot.

További információ a modulok frissítéséről: [Azure-modulok frissítése a Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Forgatókönyv: A kifejezés nem ismerhető fel egy parancsmag, függvény vagy szkript neveként

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A parancsmagot tartalmazó modult a rendszer nem importálja az Automation-fiókba.
* A parancsmagot tartalmazó modul importálva van, de elavult.

### <a name="resolution"></a>Feloldás

A hiba elhárításához tegye a következők egyikét:

* Az Azure-modulokkal kapcsolatban lásd: [Azure PowerShell](../automation-update-azure-modules.md) frissítése a Azure Automation moduljainak frissítéséhez az Automation-fiókban.
* Nem Azure-modul esetén győződjön meg arról, hogy a modul importálva van az Automation-fiókjába.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Forgatókönyv: A parancsmag futtatása meghiúsul a PnP PowerShell-runbookban a Azure Automation

### <a name="issue"></a>Probléma

Amikor egy runbook közvetlenül ír egy PnP PowerShell által létrehozott objektumot a Azure Automation kimenetébe, a parancsmag kimenete nem streamelhető vissza az Automationbe.

### <a name="cause"></a>Ok

Ez a probléma leggyakrabban akkor fordul elő Azure Automation olyan runbookokat, amelyek PnP PowerShell-parancsmagokat (például ) hívnak meg a visszaadott objektumok `add-pnplistitem` elfogása nélkül.

### <a name="resolution"></a>Feloldás

Szerkessze a szkripteket úgy, hogy bármilyen visszatérési értéket hozzárendeljen a változókhoz, hogy a parancsmagok ne próbáljanak egész objektumokat írni a standard kimenetbe. A szkript átirányíthatja a kimeneti streamet egy parancsmagra, ahogy az itt látható.

```azurecli
  $null = add-pnplistitem
```

Ha a szkript a parancsmag kimenetét elemezi, a szkriptnek egy változóban kell tárolnia a kimenetet, és módosítania kell a változót ahelyett, hogy egyszerűen streamelnie kell a kimenetet.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Forgatókönyv: A parancsmag nem ismerhető fel runbook végrehajtásakor

### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Ok

Ezt a hibát az okozza, hogy a PowerShell-motor nem találja a forgatókönyvben használt parancsmagot. Lehetséges, hogy a parancsmagot tartalmazó modul hiányzik a fiókból, névütközés áll fenn egy runbook nevével, vagy a parancsmag egy másik modulban is létezik, és az Automation nem tudja feloldani a nevet.

### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások bármelyikét:

* Győződjön meg arról, hogy helyesen adta meg a parancsmag nevét.
* Győződjön meg arról, hogy a parancsmag létezik az Automation-fiókjában, és hogy nincsenek ütközések. Annak ellenőrzéséhez, hogy a parancsmag jelen van-e, nyisson meg egy runbookot szerkesztési módban, és keresse meg a könyvtárban keresni kívánt parancsmagot, vagy futtassa a következőt: `Get-Command <CommandName>` . Miután ellenőrzi, hogy a parancsmag elérhető-e a fiók számára, és hogy nincsenek-e névütközések más parancsmagokkal vagy runbookokkal, adja hozzá a parancsmagot a vászonhoz. Győződjön meg arról, hogy érvényes paraméterkészletet használ a runbookban.
* Ha névütközés történik, és a parancsmag két különböző modulban érhető el, oldja meg a problémát a parancsmag teljes nevével. Így használhatja például a következőt: `ModuleName\CmdletName`.
* Ha a runbookot a helyszínen, egy hibrid feldolgozói csoportban futtatja, győződjön meg arról, hogy a modul és a parancsmag telepítve van a hibrid feldolgozót tartalmazó gépen.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Forgatókönyv: Helytelen objektumhivatkozás a Add-AzAccount

### <a name="issue"></a>Probléma

Ez a hiba akkor jelenik meg, ha a parancsmaggal dolgozik, amely `Add-AzAccount` a `Connect-AzAccount` parancsmag aliasa:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a runbook nem a megfelelő lépéseket adja meg az `Add-AzAccount` Automation-fiók hozzáadásához szükséges hívás előtt. A szükséges lépések egyike lehet például a futási fiókkal való bejelentkezés. A runbookban használható megfelelő műveletekért lásd: Runbook végrehajtása a [Azure Automation.](../automation-runbook-execution.md)

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Forgatókönyv: Az objektumhivatkozás nincs beállítva egy objektumpéldányra

### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor meghív egy gyermek runbookot a paraméterrel, és a `Wait` Kimeneti stream tartalmaz egy objektumot:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

Ha a stream objektumokat tartalmaz, `Start-AzAutomationRunbook` a nem megfelelően kezeli a kimeneti adatfolyamot.

### <a name="resolution"></a>Feloldás

Hajtson végre egy lekérdezési logikát, és használja a [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancsmagot a kimenet lekérésére. Ennek a logikának egy mintája itt van definiálva:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Forgatókönyv: A runbook deserializált objektum miatt meghiúsul

### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Ok

Ha a runbook egy PowerShell-munkafolyamat, akkor deserializált formátumban tárolja az összetett objektumokat, hogy a munkafolyamat felfüggesztése esetén is megmaradtassa a runbook állapotát.

### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások bármelyikét:

* Ha összetett objektumokat hoz létre egy parancsmagból egy másikba, burkolja ezeket a parancsmagokat egy `InlineScript` tevékenységbe.
* A teljes objektum átadása helyett adja át a szükséges nevet vagy értéket a komplex objektumból.
* PowerShell-forgatókönyvet használjon PowerShell-munkafolyamat-runbook helyett.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Forgatókönyv: 400 Hibás kérés állapota webhook hívkor

### <a name="issue"></a>Probléma

Amikor webhookot próbál meghívni egy Azure Automation runbookhoz, a következő hibaüzenet jelenik meg:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Ok

A hívni próbált webhook le van tiltva vagy lejárt. 

### <a name="resolution"></a>Feloldás

Ha a webhook le van tiltva, újra engedélyezheti a Azure Portal. Ha a webhook lejárt, törölnie kell, majd újra létre kell hoznia. A [webhookokat csak akkor újíthatja meg,](../automation-webhooks.md#renew-a-webhook) ha az még nem járt le. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Forgatókönyv: 429: A kérelmek sebessége jelenleg túl nagy

### <a name="issue"></a>Probléma

A parancsmag futtatásakor a következő hibaüzenet `Get-AzAutomationJobOutput` jelenik meg:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha olyan runbookból kap feladatkimenetet, amely számos [részletes adatfolyamot tartalmaz.](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)

### <a name="resolution"></a>Feloldás

A hiba elhárításához tegye a következők egyikét:

* Szerkessze a runbookot, és csökkentse az általa kibocsátott feladatstreamek számát.
* Csökkentse a parancsmag futtatásakor lekért streamek számát. Ehhez beállíthatja a `Stream` [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancsmag paraméterének értékét, hogy csak a kimeneti streameket olvassa be. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Forgatókönyv: A runbook-feladat meghiúsul a lefoglalt kvóta túllépése miatt

### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladat végrehajtása meghaladja a fiók 500 perces ingyenes kvótáját. Ez a kvóta a feladat-végrehajtási tevékenységek minden típusára vonatkozik. Ezen tevékenységek némelyike egy feladat tesztelése, egy feladat indítása a portálról, egy feladat végrehajtása webhookokkal, vagy egy feladat ütemezése a végrehajtásra a Azure Portal vagy az adatközpont használatával. Az Automation díjszabásával kapcsolatos további információkért lásd: [Az Automation díjszabása.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Feloldás

Ha havonta több mint 500 percnyi feldolgozást szeretne használni, módosítsa előfizetését az Ingyenes szintről az Alapszintű szintre:

1. Jelentkezzen be az Azure-előfizetésbe.
1. Válassza ki a frissíteni kívánt Automation-fiókot.
1. Válassza **a Beállítások,** majd a **Díjszabás lehetőséget.**
1. Válassza **az Engedélyezés** lehetőséget az oldal alján, hogy a fiókját az Alapszintű szintre frissítse.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Forgatókönyv: Runbook 1 MB-nál nagyobb kimeneti streame

### <a name="issue"></a>Probléma

Az Azure-beli védőfalon futó runbook a következő hibával meghiúsul:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Ok

Ez a hiba azért fordul elő, mert a runbook túl sok kivételadatot próbált meg írni a kimeneti streambe.

### <a name="resolution"></a>Feloldás

A feladat kimeneti streamjére 1 MB-os korlát van korlátozva. Győződjön meg arról, hogy a runbook a és a blokkok használatával egy végrehajtható vagy alfolyamathoz csatolja a `try` `catch` hívásokat. Ha a műveletek kivételt vetnek fel, a kódnak a kivétel üzenetét kell írnia egy Automation-változóba. Ez a technika megakadályozza, hogy az üzenet beírható a feladat kimeneti streamjére. A hibrid runbook-feldolgozó feladatok végrehajtásakor a 1 MB-osra csonkolt kimeneti stream hibaüzenet nélkül jelenik meg.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Forgatókönyv: A runbook-feladat háromszor próbálkozik, de nem indul el minden alkalommal

### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Ok

Ez a hiba a következő problémák valamelyike miatt fordul elő:

* **Memóriakorlát.** A feladat meghiúsulhat, ha több mint 400 MB memóriát használ. A sandbox számára lefoglalt memória dokumentált korlátait az Automation szolgáltatás [korlátainál talál.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* **Hálózati szoftvercsatornák.** Az Azure-védőfal egyidejűleg legfeljebb 1000 hálózati szoftvercsatornát használhat. További információ: [Automation szolgáltatáskorlátok.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)

* **A modul nem kompatibilis.** Előfordulhat, hogy a modulfüggőségek helytelenek. Ebben az esetben a runbook általában egy vagy egy `Command not found` üzenetet ad `Cannot bind parameter` vissza.

* **Nincs hitelesítés a Active Directory a védőfalhoz.** A runbook egy Azure-beli sandboxban futó végrehajtható vagy alfolyamatot próbált meg hívni. A runbookok nem konfigurálhatóak az Azure AD-val való hitelesítésre a Azure Active Directory Authentication Library (ADAL) használatával.

### <a name="resolution"></a>Feloldás

* **Memóriakorlát, hálózati szoftvercsatornák.** A memóriakorláton belüli munka javasolt módja a számítási feladatok felosztása több runbook között, kevesebb adat feldolgozása a memóriában, a runbookok szükségtelen kimenetének írása, valamint annak figyelembe vennie, hogy hány ellenőrzőpont van beírva a PowerShell-alapú munkafolyamat-runbookba. Használja a clear metódust( például ) a változók ürítéhez, és a használatával `$myVar.clear` `[GC]::Collect` azonnal futtassa a szemétgyűjtést. Ezek a műveletek csökkentik a runbook memóriaigényét a futtatás során.

* **A modul nem kompatibilis.** Frissítse azure-moduljait a következő cikk lépéseit [követve: Azure PowerShell frissítése a Azure Automation.](../automation-update-azure-modules.md)

* **Nincs hitelesítés a Active Directory a védőfalhoz.** Amikor runbook használatával hitelesít az Azure AD-be, győződjön meg arról, hogy az Azure AD-modul elérhető az Automation-fiókjában. Győződjön meg arról, hogy a runbook által automatizált feladatok végrehajtásához szükséges engedélyeket adja meg a runbooknak.

  Ha a runbook nem tud azure-védőfalon futó végrehajtható vagy alfolyamatot hívni, használja a hibrid [runbook-feldolgozó runbookját.](../automation-hrw-run-runbooks.md) A hibrid dolgozókat nem korlátozza az Azure-beli védőfal memória- és hálózati korlátai.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Forgatókönyv: A PowerShell-feladat "A metódus nem hívható meg" hibaüzenettel meghiúsul

### <a name="issue"></a>Probléma

Az Azure-ban futó runbookban a Következő hibaüzenet jelenik meg, amikor elindít egy PowerShell-feladatot:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Ok

Ez a hiba azt jelezheti, hogy az Azure-védőfalon futó runbookok nem futtathatók teljes nyelvi [módban.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Feloldás

A hiba kétféleképpen oldható meg:

* A [Start-Job](/powershell/module/microsoft.powershell.core/start-job)helyett a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) használatával indítsa el a runbookot.
* Próbálja meg futtatni a runbookot egy hibrid runbook-feldolgozón.

További információ erről a viselkedésről és a runbookok Azure Automation viselkedéséről: Runbook végrehajtása a [Azure Automation.](../automation-runbook-execution.md)

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Forgatókönyv: Egy hosszú ideig futó runbook nem fejeződik be

### <a name="issue"></a>Probléma

A runbook leállított állapotban jelenik meg a futtatás után három órán át. A következő hibaüzenetet is kaphatja:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Ez a viselkedés az Azure-védőfalon belüli tervezés miatt van így, mert a környezeten belüli folyamatok igazságosan oszlnak Azure Automation. [](../automation-runbook-execution.md#fair-share) Ha egy folyamat három óránál hosszabb ideig fut, az igazságos megosztás automatikusan leállítja a runbookot. Az igazságos időkorláton túllépő runbookok állapota runbooktípusonként eltérő. A PowerShell- és Python-runbookok Leállítva állapotra vannak állítva. A PowerShell munkafolyamat-runbookok beállítása Sikertelen.

### <a name="cause"></a>Ok

A runbook túllépte az Azure-beli sandbox igazságos megosztásának háromórás korlátját.

### <a name="resolution"></a>Feloldás

Az egyik ajánlott megoldás a runbook futtatása egy hibrid [runbook-feldolgozón.](../automation-hrw-run-runbooks.md) A hibrid dolgozókra nincs korlátozva az Azure-beli sandboxes háromórás igazságos megosztási runbook-korlátja. A hibrid runbook-dolgozókon futó runbookokat úgy kell fejleszteni, hogy támogassák az újraindítási viselkedést, ha váratlan helyi infrastruktúra-problémák lépnek fel.

Egy másik megoldás a runbook optimalizálása gyermek [runbookok létrehozásával.](../automation-child-runbooks.md) Ha a runbook több erőforráson is végiglépked ugyanazon a függvényen, például egy több adatbázison futó adatbázis-műveletben, a függvényt áthelyezheti egy gyermek runbookba. Minden gyermek runbook párhuzamosan, külön folyamattal fut. Ez a viselkedés csökkenti a szülő runbook befejezéséhez szükséges teljes időt.

A gyermek runbook-forgatókönyvet engedélyező PowerShell-parancsmagok a következőek:

* [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Ez a parancsmag lehetővé teszi, hogy elindítson egy runbookot, és paramétereket adjon át a számára.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Ha a gyermek runbook befejezése után végre kell hajtanunk néhány műveletet, ez a parancsmag lehetővé teszi az egyes gyermekek feladatállapotának ellenőrzését.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Forgatókönyv: Hiba a feladatstreamben a get_SerializationSettings metódussal kapcsolatban

### <a name="issue"></a>Probléma

Egy runbook feladatstreamjében a következő hiba jelenik meg:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Ok

Ezt a hibát valószínűleg a forgatókönyvben az AzureRM modulból az Az modulba való hiányos migrálás okozza. Ez a helyzet azt okozhatja, Azure Automation egy runbook-feladatot csak AzureRM-modulokkal indít el, majd egy másik feladatot csak az Az modulokkal indít el, ami a sandbox összeomlását eredményezi.

### <a name="resolution"></a>Feloldás

Nem javasoljuk az Az és az AzureRM parancsmagok használatát ugyanabban a runbookban. A modulok helyes használatával kapcsolatos további információkért lásd: [Mibavallás az Az modulokba.](../shared-resources/modules.md#migrate-to-az-modules)

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Forgatókönyv: Hozzáférés megtagadva, ha Azure-védőfalat használ runbookhoz vagy alkalmazáshoz

### <a name="issue"></a>Probléma

Amikor a runbook vagy az alkalmazás megpróbál futni egy Azure-védőkörnyezetben, a környezet megtagadja a hozzáférést.

### <a name="cause"></a>Ok

Ez a probléma azért fordulhat elő, mert az Azure-védőfal megakadályozza a hozzáférést az összes folyamatban lévő COM-kiszolgálóhoz. Például egy sandboxed application or runbook can't call into Windows Management Instrumentation (WMI) or into the Windows Installer service (msiserver.exe).

### <a name="resolution"></a>Feloldás

Az Azure-védőfal használatával kapcsolatos részletekért lásd: [Runbook végrehajtási környezet.](../automation-runbook-execution.md#runbook-execution-environment)

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Forgatókönyv: Érvénytelen Tiltott állapotkód Key Vault runbookon belüli kód használata esetén

### <a name="issue"></a>Probléma

Amikor egy Azure Key Vault runbookon Azure Automation próbál hozzáférni, a következő hibaüzenet jelenik meg:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Ok

A probléma lehetséges okai a következőek:

* Nem használ futtatott fiókot.
* Nem megfelelő engedélyek.

### <a name="resolution"></a>Feloldás

#### <a name="not-using-a-run-as-account"></a>Nincs használatban futó fiók

Kövesse [az 5. lépés –](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez lépését annak biztosításához, hogy a fiókokat egy Key Vault.

#### <a name="insufficient-permissions"></a>Nem megfelelő engedélyek

[Adjon hozzá engedélyeket a Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) annak biztosításához, hogy a futó fiók megfelelő engedélyekkel rendelkezik a Key Vault.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

* [Runbook végrehajtása a Azure Automation](../automation-runbook-execution.md)
* [Runbook indítása a Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Következő lépések

Ha itt nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével:

* Az Azure fórumain Azure-szakértőktől [kaphat választ.](https://azure.microsoft.com/support/forums/)
* Csatlakozzon [@AzureSupport](https://twitter.com/azuresupport) a -hez, Microsoft Azure a felhasználói élmény javítása érdekében. Azure ügyfélszolgálata választ, támogatást és szakértőket az Azure közösségéhez csatlakoztatja.
* Ha további segítségre van szüksége, bekért egy Azure-támogatás incidenst. A webhelyre [Azure-támogatás,](https://azure.microsoft.com/support/options/)és válassza a **Támogatás et.**
