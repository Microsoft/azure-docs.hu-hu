---
title: Azure Automation runbook kapcsolatos problémák elhárítása
description: Ez a cikk a Azure Automation runbookok kapcsolatos hibák elhárítását és megoldását ismerteti.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref
ms.openlocfilehash: ea9d8a4899b0d725c9791192d68373b44acee11f
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168739"
---
# <a name="troubleshoot-runbook-issues"></a>Runbookkal kapcsolatos hibák elhárítása

 Ez a cikk az esetlegesen előforduló runbook problémákat és azok megoldását ismerteti. Általános információk: [Runbook végrehajtása Azure Automationban](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Runbook problémák diagnosztizálása

Ha a Azure Automation runbook végrehajtása során hibákat kap, a következő lépésekkel diagnosztizálhatja a problémákat:

1. Győződjön meg arról, hogy a runbook-parancsfájl sikeresen lefut a helyi gépen.

    A nyelvi referenciák és a tanulási modulok a [PowerShell docs](/powershell/scripting/overview) vagy a [Python docs](https://docs.python.org/3/)című részben olvashatók. A szkript helyi futtatása képes felderíteni és elhárítani a gyakori hibákat, például:

      * Hiányzó modulok
      * Szintaktikai hibák
      * Logikai hibák

1. Vizsgálja meg a runbook- [hibák folyamait](../automation-runbook-output-and-messages.md#runbook-output).

    Tekintse meg ezeket a streameket adott üzenetekhez, és hasonlítsa össze azokat a cikkben leírt hibákkal.

1. Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület rendelkezik a szükséges modulokkal.

    Ha a runbook bármely modult importál, ellenőrizze, hogy elérhetők-e az Automation-fiókjához az [importálási modulok](../shared-resources/modules.md#import-modules)lépéseinek használatával. Frissítse a PowerShell-modulokat a legújabb verzióra a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md)című részben található utasításokat követve. További hibaelhárítási információkért lásd: [modulok hibaelhárítása](shared-resources.md#modules).

1. Ha a runbook felfüggesztve vagy váratlanul leáll:

    * Ha a futtató fiók lejárt, [újítsa meg a tanúsítványt](../manage-runas-account.md#cert-renewal) .
    * [Újítsa meg a webhookot](../automation-webhooks.md#renew-a-webhook) , ha a runbook elindításához egy lejárt webhookot próbál használni.
    * [Ellenőrizze a feladatok állapotát](../automation-runbook-execution.md#job-statuses) az aktuális runbook állapotának meghatározásához és a probléma lehetséges okainak megállapításához.
    * [További kimenet hozzáadása](../automation-runbook-output-and-messages.md#working-with-message-streams) a runbook annak azonosításához, hogy mi történjen a runbook felfüggesztése előtt.
    * [Kezelje](../automation-runbook-execution.md#exceptions) a feladatokban felmerülő kivételeket.

1. Ezt a lépést akkor hajtsa végre, ha a hibrid Runbook-feldolgozó runbook-feladata vagy-környezete nem válaszol.

    Ha Azure Automation helyett hibrid Runbook-feldolgozón futtatja a runbookok, előfordulhat, hogy [a hibrid feldolgozót is el kell hárítania](hybrid-runbook-worker.md).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Forgatókönyv: a Runbook nem tud engedélyt vagy tiltott 403 hibát végrehajtani

### <a name="issue"></a>Probléma

A runbook nem tud engedélyt vagy tiltott 403 hibát vagy ezzel egyenértékű műveletet végrehajtani.

### <a name="cause"></a>Ok

Előfordulhat, hogy a futtató fiókok nem rendelkeznek azonos engedélyekkel az Azure-erőforrásokkal szemben az aktuális Automation-fiókkal. 

### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a futtató fiók rendelkezik a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel](../../role-based-access-control/role-assignments-portal.md) .

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Forgatókönyv: nem sikerült az Azure-fiókba való bejelentkezés

### <a name="issue"></a>Probléma

A parancsmag használatakor a következő hibák valamelyike jelenik meg `Connect-AzAccount` :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Ok

Ezek a hibák akkor jelentkeznek, ha a hitelesítőadat-eszköz neve érvénytelen. Az is előfordulhat, hogy az Automation hitelesítőadat-eszköz beállításához használt Felhasználónév és jelszó nem érvényes.

### <a name="resolution"></a>Feloldás

A probléma okának megállapításához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy nincs speciális karaktere. A karakterek közé tartozik az `\@` Automation hitelesítőadat-eszköz neve, amelyet az Azure-hoz való kapcsolódáshoz használ.
1. Ellenőrizze, hogy használhatja-e a Azure Automation hitelesítő adatokban tárolt felhasználónevet és jelszót a helyi PowerShell ISE-szerkesztőben. Futtassa a következő parancsmagokat a PowerShell ISE-ben.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Ha a hitelesítés helyileg meghiúsul, nem állította be megfelelően a Azure Active Directory (Azure AD) hitelesítő adatait. Az Azure AD-fiók megfelelő beállításához tekintse meg a következő cikket: hitelesítés az Azure-ban a [Azure Active Directory használatával](../automation-use-azure-ad.md).

1. Ha a hiba úgy tűnik, hogy átmeneti, próbálkozzon újra az újrapróbálkozási logikával a hitelesítési rutinhoz, hogy megbízhatóbb legyen a hitelesítés.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Forgatókönyv: Login-AzureRMAccount futtatása a bejelentkezéshez

### <a name="issue"></a>Probléma

A runbook futtatásakor a következő hibaüzenet jelenik meg:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha nem futtató fiókot használ, vagy lejárt a futtató fiók. További információ: [Azure Automation futtató fiókok áttekintése](../automation-security-overview.md#run-as-accounts).

Ennek a hibának két elsődleges oka van:

* A AzureRM vagy az a modul különböző verziói vannak.
* Egy külön előfizetésben próbál hozzáférni az erőforrásokhoz.

### <a name="resolution"></a>Feloldás

Ha ezt a hibaüzenetet egy AzureRM vagy az modul frissítése után kapja meg, frissítse az összes modult ugyanarra a verzióra.

Ha egy másik előfizetésben lévő erőforrásokhoz próbál hozzáférni, kövesse az alábbi lépéseket az engedélyek konfigurálásához:

1. Nyissa meg az Automation futtató fiókot, és másolja az **alkalmazás azonosítóját** és **ujjlenyomatát**.

    ![Alkalmazás AZONOSÍTÓjának és ujjlenyomatának másolása](../media/troubleshoot-runbooks/collect-app-id.png)

1. Nyissa meg az előfizetés **hozzáférés-vezérlését** , ahol az Automation-fiók *nincs üzemeltetve* , és adjon hozzá egy új szerepkör-hozzárendelést.

    ![Hozzáférés-vezérlés](../media/troubleshoot-runbooks/access-control.png)

1. Adja hozzá a korábban összegyűjtött **alkalmazás-azonosítót** . Válassza a **közreműködői** engedélyek lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Másolja az előfizetés nevét.

1. Mostantól a következő runbook-kóddal tesztelheti az Automation-fiókjából a másik előfizetésre vonatkozó engedélyeket. Cserélje le az `<CertificateThumbprint>` értéket az 1. lépésben átmásolt értékre. Cserélje le a `"<SubscriptionName>"` értéket a 4. lépésben átmásolt értékre.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Forgatókönyv: nem található az Azure-előfizetés

### <a name="issue"></a>Probléma

A (z), `Select-AzureSubscription` `Select-AzureRMSubscription` , vagy parancsmag használatakor a következő hibaüzenet jelenik meg `Select-AzSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hiba

Ez a hiba akkor fordulhat elő, ha:

* Az előfizetés neve érvénytelen.
* Az előfizetést beolvasni próbáló Azure AD-felhasználó nem az előfizetés rendszergazdájaként van konfigurálva.
* A parancsmag nem érhető el.

### <a name="resolution"></a>Feloldás

Az alábbi lépéseket követve megállapíthatja, hogy hitelesített-e az Azure-ban, és hozzáfér-e a kiválasztani kívánt előfizetéshez:

1. Annak ellenőrzéséhez, hogy a parancsfájl önállóan működik-e, tesztelje a Azure Automationon kívül.
1. A parancsmag futtatása előtt győződjön meg arról, hogy a parancsfájl a [AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) parancsmagot futtatja `Select-*` .
1. Hozzáadás a `Disable-AzContextAutosave -Scope Process` runbook elejéhez. Ez a parancsmag biztosítja, hogy a hitelesítő adatok csak az aktuális runbook végrehajtásához legyenek érvényesek.
1. Ha továbbra is megjelenik a hibaüzenet, módosítsa a kódját a paraméter hozzáadásával `AzContext` `Connect-AzAccount` , majd hajtsa végre a kódot.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Forgatókönyv: a Runbookok sikertelen a több előfizetés kezelésekor

### <a name="issue"></a>Probléma

A runbookok végrehajtásakor a runbook nem tudja kezelni az Azure-erőforrásokat.

### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetet használja a futtatásakor. Ennek az lehet az oka, hogy a runbook véletlenül megpróbál hozzáférni a helytelen előfizetéshez.

A következőhöz hasonló hibák jelenhetnek meg:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Feloldás

Előfordulhat, hogy az előfizetési környezet elvész, ha egy runbook több runbookok hív meg. A helytelen előfizetéshez való véletlen próbálkozás elkerüléséhez kövesse az alábbi útmutatást.

* Ha el szeretné kerülni a helytelen előfizetés hivatkozását, tiltsa le az Automation-runbookok a környezetek mentését az egyes runbook elején lévő következő kód használatával.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* A Azure PowerShell parancsmagok támogatják a `-DefaultProfile` paramétert. Ez az összes és a AzureRm parancsmaghoz lett hozzáadva, hogy támogassa több PowerShell-parancsfájl futtatását ugyanabban a folyamatban, így megadhatja a környezetet és az egyes parancsmagokhoz használni kívánt előfizetést. A runbookok mentse a környezeti objektumot a runbook a runbook létrehozásakor (azaz amikor egy fiók bejelentkezik), és minden alkalommal, amikor módosul, és hivatkozzon a környezetre az az parancsmag megadásakor.

   > [!NOTE]
   > Akkor is át kell adnia egy környezeti objektumot, ha a kontextust közvetlenül a parancsmagok, például a [set-AzContext](/powershell/module/az.accounts/Set-AzContext) vagy a [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription)használatával kezeli.

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
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Forgatókönyv: az Azure-hitelesítés sikertelen, mert a többtényezős hitelesítés engedélyezve van

### <a name="issue"></a>Probléma

Az Azure-beli felhasználónevével és jelszavával történő hitelesítéskor a következő hibaüzenet jelenik meg:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Ok

Ha az Azure-fiókjában többtényezős hitelesítés van, nem használhat Azure Active Directory felhasználót az Azure-beli hitelesítéshez. Ehelyett tanúsítványt vagy szolgáltatásnevet kell használnia a hitelesítéshez.

### <a name="resolution"></a>Feloldás

Ha klasszikus futtató fiókot szeretne használni a klasszikus Azure üzembehelyezési modell-parancsmagokkal, tekintse [meg a klasszikus futtató fiók létrehozása az Azure-szolgáltatások kezeléséhez](../automation-create-standalone-account.md#create-a-classic-run-as-account)című témakört. Ha Azure Resource Manager-parancsmagokkal szeretne szolgáltatásnevet használni, tekintse meg az [egyszerű szolgáltatásnév létrehozása Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) és [a szolgáltatásnév hitelesítése az Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)használatával című témakört.

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Forgatókönyv: A Runbook meghiúsult "A feladat megszakadt" hibaüzenet jelenik meg

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Ok

Ezt a hibát az elavult Azure-modulok használatával lehet okozni.

### <a name="resolution"></a>Feloldás

A hiba megoldásához frissítse az Azure-modulokat a legújabb verzióra:

1. Az Automation-fiókban válassza a **modulok** lehetőséget, majd válassza az **Azure-modulok frissítése** lehetőséget.
1. A frissítés nagyjából 15 percet vesz igénybe. A művelet befejezése után futtassa újra a sikertelen runbook.

A modulok frissítésével kapcsolatos további információkért lásd: [Azure-modulok frissítése Azure Automationban](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Forgatókönyv: a kifejezés nem ismerhető fel parancsmag, függvény vagy parancsfájl neveként

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A parancsmagot tartalmazó modul nem lett importálva az Automation-fiókba.
* A parancsmagot tartalmazó modul importálva van, de elavult.

### <a name="resolution"></a>Feloldás

A hiba megoldásához hajtsa végre az alábbi műveletek egyikét:

* Az Azure-modulokkal kapcsolatban lásd: [Azure PowerShell-modulok frissítése Azure Automationban](../automation-update-azure-modules.md) , hogy megtudja, hogyan frissítheti a modulokat az Automation-fiókban.
* Nem Azure-modul esetén győződjön meg arról, hogy a modul az Automation-fiókjába lett importálva.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Forgatókönyv: a parancsmag meghiúsul a PnP PowerShell-runbook Azure Automation

### <a name="issue"></a>Probléma

Amikor egy runbook egy PnP PowerShell által generált objektumot ír közvetlenül a Azure Automation kimenetre, a parancsmag kimenete nem tud visszaküldeni az Automation szolgáltatásba.

### <a name="cause"></a>Ok

Ez a probléma leggyakrabban akkor fordul elő, amikor a Azure Automation feldolgozza a PnP PowerShell-parancsmagokat meghívó runbookok, például `add-pnplistitem` a visszatérési objektumok kifogása nélkül.

### <a name="resolution"></a>Feloldás

Szerkessze a parancsfájlokat úgy, hogy bármilyen visszatérési értéket rendeljen a változókhoz, hogy a parancsmagok ne próbáljanak teljes objektumokat írni a standard kimenetre. Egy parancsfájl átirányíthatja a kimeneti adatfolyamot egy parancsmagba az itt látható módon.

```azurecli
  $null = add-pnplistitem
```

Ha a parancsfájl a parancsmag kimenetét elemzi, a szkriptnek egy változóban kell tárolnia a kimenetet, és a változót csak a kimenet továbbítása helyett kell módosítania.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Forgatókönyv: a parancsmag nem ismerhető fel egy runbook végrehajtásakor

### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Ok

Ezt a hibát akkor okozza a rendszer, ha a PowerShell-motor nem találja a runbook használt parancsmagot. Lehetséges, hogy a parancsmagot tartalmazó modul hiányzik a fiókból, a név ütközik a runbook nevével, vagy a parancsmag egy másik modulban is létezik, és az Automation nem tudja feloldani a nevet.

### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások bármelyikét:

* Győződjön meg arról, hogy helyesen adta meg a parancsmag nevét.
* Győződjön meg arról, hogy a parancsmag létezik az Automation-fiókban, és nincsenek ütközések. Annak ellenőrzéséhez, hogy a parancsmag megtalálható-e, nyisson meg egy runbook szerkesztési módban, és keresse meg a könyvtárban megkeresni kívánt parancsmagot, vagy futtassa a parancsot `Get-Command <CommandName>` . Miután ellenőrizte, hogy a parancsmag elérhető-e a fiók számára, és hogy a név nem ütközik más parancsmagokkal vagy runbookok, adja hozzá a parancsmagot a vászonhoz. Győződjön meg arról, hogy érvényes paramétert használ a runbook.
* Ha a név ütközik, és a parancsmag két különböző modulban érhető el, oldja meg a problémát a parancsmag teljes nevének használatával. Így használhatja például a következőt: `ModuleName\CmdletName`.
* Ha a helyszíni runbook egy hibrid feldolgozói csoportban hajtja végre, győződjön meg arról, hogy a modul és a parancsmag telepítve van a hibrid feldolgozót futtató számítógépen.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Forgatókönyv: helytelen objektum-hivatkozás a Add-AzAccount hívásakor

### <a name="issue"></a>Probléma

Ez a hibaüzenet akkor jelenik meg, ha együttműködik `Add-AzAccount` a (z) `Connect-AzAccount` parancsmaggal:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a runbook nem hajtja végre a megfelelő lépéseket az `Add-AzAccount` Automation-fiók hozzáadásának meghívása előtt. A szükséges lépések egyikének példája egy futtató fiókkal való bejelentkezés. A runbook használt helyes műveletekért lásd: [runbook-végrehajtás a Azure Automation-ben](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Forgatókönyv: az objektum hivatkozása nem az objektum egy példányára van beállítva

### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor egy gyermek runbook hív meg a `Wait` paraméterrel, és a kimeneti adatfolyam tartalmaz egy objektumot:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

Ha az adatfolyam objektumokat tartalmaz, `Start-AzAutomationRunbook` nem kezeli megfelelően a kimeneti adatfolyamot.

### <a name="resolution"></a>Feloldás

Hozzon létre egy lekérdezési logikát, és használja a [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancsmagot a kimenet lekéréséhez. Ennek a logikának a mintája itt van definiálva:

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Forgatókönyv: a Runbook nem sikerül deszerializált objektum miatt

### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Ok

Ha a runbook egy PowerShell-munkafolyamat, akkor az összetett objektumokat egy deszerializált formátumban tárolja, hogy a runbook állapot maradjon, ha a munkafolyamat fel van függesztve.

### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások bármelyikét:

* Ha a komplex objektumokat az egyik parancsmagból egy másikba végzi, a parancsmagokat egy `InlineScript` tevékenységbe csomagolja.
* Adja meg a szükséges nevet vagy értéket a komplex objektumból a teljes objektum átadása helyett.
* PowerShell-munkafolyamatok runbook helyett használjon PowerShell-runbook.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Forgatókönyv: 400 hibás kérés állapota webhook meghívásakor

### <a name="issue"></a>Probléma

Amikor egy Azure Automation runbook próbál meg meghívni egy webhookot, a következő hibaüzenet jelenik meg:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Ok

A meghívni próbált webhook le van tiltva vagy lejárt. 

### <a name="resolution"></a>Feloldás

Ha a webhook le van tiltva, akkor újra engedélyezheti a Azure Portalon keresztül. Ha a webhook lejárt, törölnie kell, majd újra létre kell hoznia. Csak akkor [újíthat meg egy webhookot](../automation-webhooks.md#renew-a-webhook) , ha még nem járt le. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Forgatókönyv: 429: a kérelmek aránya jelenleg túl nagy

### <a name="issue"></a>Probléma

A parancsmag futtatásakor a következő hibaüzenet jelenik meg `Get-AzAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a feladatok kimenetét olyan runbook kérdezi le, amely sok [részletes adatfolyammal](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)rendelkezik.

### <a name="resolution"></a>Feloldás

A hiba elhárításához hajtsa végre az alábbi műveletek egyikét:

* Szerkessze a runbook, és csökkentse az általa kibocsátott feladatokhoz tartozó adatfolyamok számát.
* Csökkentse a lekérdezni kívánt adatfolyamok számát a parancsmag futtatásakor. Ehhez a `Stream` [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancsmag paraméterének értékét állíthatja be csak kimeneti adatfolyamok lekéréséhez. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Forgatókönyv: a Runbook feladata meghiúsult, mert túllépte a lefoglalt kvótát

### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladatok végrehajtása meghaladja a fiókhoz tartozó 500 perces ingyenes kvótát. Ez a kvóta a feladat-végrehajtási feladatok összes típusára vonatkozik. Ezek közül néhány feladat teszteli a feladatot, elindítja a feladatot a portálról, webhookok használatával hajt végre feladatot, vagy a Azure Portal vagy az adatközpont használatával végrehajtja a feladat ütemezését. További információ az automatizálás díjszabásáról: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Feloldás

Ha havonta több mint 500 perces feldolgozást szeretne használni, módosítsa az előfizetést az ingyenes szintről az alapszintű csomagra:

1. Jelentkezzen be az Azure-előfizetésbe.
1. Válassza ki a frissítendő Automation-fiókot.
1. Válassza a **Beállítások**, majd a **díjszabás** lehetőséget.
1. Kattintson az **Engedélyezés** elemre az oldal alján a fiók alapszintű szintre való frissítéséhez.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Forgatókönyv: az 1 MB-nál nagyobb kimeneti adatfolyam Runbook

### <a name="issue"></a>Probléma

Az Azure-beli homokozóban futó runbook a következő hibával meghiúsul:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Ok

Ez a hiba azért fordul elő, mert a runbook túl sok kivételi adatokat próbált meg írni a kimeneti adatfolyamba.

### <a name="resolution"></a>Feloldás

A feladatok kimeneti adatfolyamának 1 MB-os korlátja van. Győződjön meg arról, hogy a runbook egy végrehajtható vagy alfolyamathoz tartozó hívásokat csatol a `try` és a `catch` blokk használatával. Ha a műveletek kivételt jeleznek, a kódnak kell megírnia az üzenetet a kivételből egy Automation-változóba. Ezzel a technikával megakadályozható, hogy a rendszer beírja az üzenetet a feladatok kimeneti adatfolyamba. A hibrid Runbook-feldolgozói feladatok végrehajtásához a kimeneti adatfolyam 1 MB-ra csonkítva hibaüzenet nélkül jelenik meg.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Forgatókönyv: a Runbook-feladatok megkezdése háromszor próbálkozott, de minden alkalommal nem indul el

### <a name="issue"></a>Probléma

A runbook a következő hiba miatt meghiúsul:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Ok

Ez a hiba az alábbi problémák egyike miatt fordul elő:

* **Memória korlátja** Egy feladat meghiúsulhat, ha több mint 400 MB memóriát használ. A sandbox számára lefoglalt memória dokumentált korlátai az [Automation szolgáltatás korlátain](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)találhatók. 

* **Hálózati szoftvercsatornák.** Az Azure-beli munkaterületek 1 000 egyidejű hálózati szoftvercsatornára korlátozódnak. További információ: az [Automation szolgáltatás korlátai](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **A modul nem kompatibilis.** Előfordulhat, hogy a modul függőségei nem megfelelőek. Ebben az esetben a runbook általában egy vagy egy `Command not found` üzenetet ad vissza `Cannot bind parameter` .

* **Nincs hitelesítés Active Directory a homokozóban.** A runbook megpróbált meghívni egy Azure-beli homokozóban futó végrehajtható fájlt vagy alfolyamatot. A runbookok konfigurálása az Azure AD-val való hitelesítéshez az Azure Active Directory Authentication Library (ADAL) használatával nem támogatott.

### <a name="resolution"></a>Feloldás

* **Memória korlátja, hálózati szoftvercsatorna.** A memória korlátain belüli munkaterhelések használatának javasolt módjai a munkaterhelés több runbookok közötti felosztása, a memóriában kevesebb adat feldolgozása, a felesleges kimenetek írása a runbookok, valamint a PowerShell-munkafolyamatok runbookok való beírásának gyakorisága. A Clear metódussal, például a paranccsal `$myVar.clear` törölheti a változókat, `[GC]::Collect` és azonnal futtathatja a szemetet. Ezek a műveletek csökkentik a runbook memória-lábnyomát futtatókörnyezet közben.

* **A modul nem kompatibilis.** Frissítse az Azure-modulokat a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md)című témakör lépéseit követve.

* **Nincs hitelesítés Active Directory a homokozóban.** Ha az Azure AD-t egy runbook hitelesíti, győződjön meg arról, hogy az Azure AD-modul elérhető az Automation-fiókjában. Ügyeljen arra, hogy a futtató fióknak meg kell adnia a szükséges engedélyeket a runbook automatizálható feladatok végrehajtásához.

  Ha a runbook nem hívhat meg egy Azure-beli homokozóban futó végrehajtható fájlt vagy alfolyamatot, használja a runbook [hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md). A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek által használt memória-és hálózati korlátokra.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Forgatókönyv: a PowerShell-feladatok meghiúsulnak "a metódus nem hívható meg" hibaüzenet jelenik meg

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor elindít egy PowerShell-feladatot egy Azure-ban futó runbook:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Ok

Ez a hiba arra utalhat, hogy az Azure-beli homokozóban futó runbookok nem futtatható [teljes nyelvi módban](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Feloldás

A hiba kétféleképpen oldható fel:

* A [Start-Job](/powershell/module/microsoft.powershell.core/start-job)használata helyett a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) használatával indítsa el a runbook.
* Próbálja meg futtatni a runbook egy hibrid Runbook-feldolgozón.

Ha többet szeretne megtudni erről a viselkedésről és a Azure Automation runbookok egyéb viselkedéséről, tekintse meg a következő témakört: [Runbook-végrehajtás Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Forgatókönyv: A hosszan futó runbook nem fejeződik be

### <a name="issue"></a>Probléma

A runbook három órán belül leállított állapotban jelenik meg. Előfordulhat, hogy a következő hibaüzenet is megjelenik:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Ez a viselkedés az Azure-beli munkafolyamatok tervezésekor, a Azure Automationon belüli folyamatok [valós megosztásának](../automation-runbook-execution.md#fair-share) figyelése miatt. Ha egy folyamat három óránál hosszabb ideig fut, a valós megosztás automatikusan leállítja a runbook. A valós megosztási időkorláttal meghaladó runbook állapota eltér a runbook típustól. A PowerShell és a Python runbookok leállított állapotra vannak állítva. A PowerShell-munkafolyamat runbookok beállítása sikertelen.

### <a name="cause"></a>Ok

A runbook egy Azure-beli homokozóban a méltányos megosztás által engedélyezett három órás korlátot futtatott.

### <a name="resolution"></a>Feloldás

Egy javasolt megoldás, ha a runbook egy [hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md)futtatja. A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek három órás, méltányos megosztási runbook-korlátozására. A hibrid Runbook-feldolgozókon futó runbookok úgy kell kialakítani, hogy támogassa az újraindítási viselkedést, ha váratlan helyi infrastruktúra-problémák merülnek fel.

Egy másik megoldás a runbook optimalizálása [gyermek runbookok](../automation-child-runbooks.md)létrehozásával. Ha a runbook több erőforráson ugyanazt a függvényt futtatja, például egy adatbázis-műveletben több adatbázison, áthelyezheti a függvényt egy alárendelt runbook. Minden gyermek runbook párhuzamosan, külön folyamatban hajtja végre. Ez a viselkedés csökkenti a szülő runbook befejezésének teljes időtartamát.

A gyermek runbook-forgatókönyvet engedélyező PowerShell-parancsmagok a következők:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Ez a parancsmag lehetővé teszi, hogy elindítson egy runbookot, és paramétereket adjon át a számára.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Ha vannak olyan műveletek, amelyeket a gyermek runbook befejezését követően kell végrehajtani, ez a parancsmag lehetővé teszi, hogy minden gyermeknél ellenőrizze a feladatok állapotát.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Forgatókönyv: hiba történt a get_SerializationSettings metódussal kapcsolatos feladatokban

### <a name="issue"></a>Probléma

A runbook a következő hiba jelenik meg a feladatokban:

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

Ezt a hibát valószínűleg az okozza, hogy a AzureRM-ből a runbook-ben lévő moduloknak nem teljes áttelepítést használ. Ez a helyzet azt okozhatja, hogy Azure Automation egy runbook-feladatot csak AzureRM-modulok használatával indíthat el, majd egy másik feladatot csak az az modulok használatával indít el, amely egy sandbox-összeomláshoz vezet.

### <a name="resolution"></a>Feloldás

Az az és a AzureRM parancsmagok használata nem ajánlott ugyanazon a runbook. Ha többet szeretne megtudni a modulok helyes használatáról, tekintse meg az [áttelepítés az az modulokra](../shared-resources/modules.md#migrate-to-az-modules)című témakört.

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Forgatókönyv: hozzáférés megtagadva az Azure-beli homokozó runbook vagy alkalmazáshoz való használata esetén

### <a name="issue"></a>Probléma

Ha a runbook vagy alkalmazás egy Azure-beli homokozóban próbál futni, a környezet megtagadja a hozzáférést.

### <a name="cause"></a>Ok

Ez a probléma azért fordulhat elő, mert az Azure-beli munkaterületek megakadályozzák az összes folyamaton kívüli COM-kiszolgáló elérését. Például egy munkapéldányos alkalmazás vagy runbook nem hívható meg Windows Management Instrumentation (WMI) vagy a Windows Installer szolgáltatásba (msiserver.exe).

### <a name="resolution"></a>Feloldás

Az Azure-beli munkaterületek használatáról további részleteket a [Runbook végrehajtási környezetében](../automation-runbook-execution.md#runbook-execution-environment)talál.

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Forgatókönyv: érvénytelen tiltott állapotkód Key Vault runbook való használatakor

### <a name="issue"></a>Probléma

Ha Azure Automation runbook keresztül próbál Azure Key Vault hozzáférni, a következő hibaüzenet jelenik meg:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Ok

A probléma lehetséges okai a következők:

* Nem használ futtató fiókot.
* Nem megfelelő engedélyek.

### <a name="resolution"></a>Feloldás

#### <a name="not-using-a-run-as-account"></a>Nem futtató fiók használata

Kövesse az [5. lépés – hitelesítés hozzáadása az Azure-erőforrások kezeléséhez című témakört](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) , amely biztosítja, hogy a futtató fiókot használja a Key Vault eléréséhez.

#### <a name="insufficient-permissions"></a>Nem megfelelő engedélyek

[Adja hozzá az Key Vaulthoz szükséges engedélyeket](../manage-runas-account.md#add-permissions-to-key-vault) annak biztosításához, hogy a futtató fiók megfelelő engedélyekkel rendelkezik a Key Vault eléréséhez.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

* [Runbook végrehajtás a Azure Automationban](../automation-runbook-execution.md)
* [Runbook indítása Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Következő lépések

Ha nem találja a problémát, vagy nem tudja feloldani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-közösséghez köti.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.
