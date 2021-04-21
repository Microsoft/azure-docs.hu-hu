---
title: PowerShell-runbook létrehozása a Azure Automation
description: Ez a cikk egy egyszerű PowerShell-runbook létrehozására, tesztelésére és közzétételére tanítja be.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 958cbf8b7b03e2dd9e4b0e03ba178ffe180bfb48
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831257"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Oktatóanyag: PowerShell-runbook létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](../automation-runbook-types.md#powershell-runbooks). A PowerShell-runbookok a Windows PowerShell. A forgatókönyv kódját közvetlenül szerkesztheti a forgatókönyv szövegszerkesztőjével a Azure Portal.

> [!div class="checklist"]
> * Egyszerű PowerShell-runbook létrehozása
> * A runbook tesztelése és közzététele
> * Runbook-feladat futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép runbook-paraméterekkel való futtatásához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](../automation-quickstart-create-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Mivel ezt a gépet leállítja és elindítja, nem lehet éles virtuális gép.
* Ha szükséges, [](../automation-update-azure-modules.md) [importálja az Azure-modulokat,](../shared-resources/modules.md) vagy frissítse a modulokat a használt parancsmagok alapján.

## <a name="differences-from-powershell-workflow-runbooks"></a>Különbségek a PowerShell munkafolyamat-runbookokhoz valókhoz

A PowerShell-runbookok ugyanolyan életciklussal, képességekkel és felügyeletgel rendelkeznek, mint a PowerShell munkafolyamat-runbookok. Vannak azonban különbségek és korlátozások.

| Jellemző  | PowerShell-runbookok | PowerShell munkafolyamat-runbookok |
| ------ | ----- | ----- |
| Sebesség | Gyorsan futhat, mivel nem használ fordítási lépést. | Lassabban fut. |
| Ellenőrzőpontok | Nem támogatják az ellenőrzőpontokat. A PowerShell-runbookok csak az elejétől folytathatják a műveletet. | Használjon ellenőrzőpontokat, amelyek lehetővé teszik, hogy egy munkafüzet bármely pontról folytatja a műveletet. |
| Parancs végrehajtása | Csak a soros végrehajtás támogatása. | Támogatja a soros és a párhuzamos végrehajtást is.|
| Runspace (Futtatás) | Egyetlen futási tér futtat mindent egy szkriptben. | Egy tevékenységhez, parancshoz vagy szkriptblokkhoz külön runspace használható. |

Ezen eltérések mellett a PowerShell-runbookok [szintaktikai](/previous-versions/technet-magazine/dn151046(v=msdn.10)) eltérésekkel is fel vannak ásva a PowerShell-munkafolyamat-runbookokhoz.

## <a name="step-1---create-runbook"></a>1. lépés – Runbook létrehozása

Első lépésként hozzon létre egy egyszerű runbookot, amely a következő szöveget kiadja: `Hello World` .

1. Az Azure Portalon nyissa meg az Automation-fiókját.

2. A **Runbookok listájának** megnyitásához válassza a **Folyamatok** automatizálása alatt található Runbookok lehetőséget.

3. Hozzon létre egy új runbookot **a Runbook létrehozása lehetőség kiválasztásával.**

4. Adja a forgatókönyvnek a **MyFirstRunbook-PowerShell** nevet.

5. Ebben az esetben egy [PowerShell-runbookot fog létrehozni.](../automation-runbook-types.md#powershell-runbooks) **Runbooktípusként** válassza a PowerShell **lehetőséget.**

6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben az oktatóanyagban közvetlenül a runbookba írja be a kódot.

1. A runbook jelenleg üres. Írja `Write-Output "Hello World"` be a szkript törzsét.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> 3. lépés – A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles környezetben is elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Draft (Vázlat) verziót futtatja, és lehetővé teszi a kimenet interaktív megtekintését.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.

3. Vegye figyelembe, hogy a [runbook-feladat](../automation-runbook-execution.md) létrejön, és az állapota megjelenik a panelen.

   A feladat állapota Várólistán állapottal kezdődik, ami azt jelzi, hogy a feladat arra vár, hogy a felhőben lévő runbook-feldolgozó elérhetővé váljon. Az állapot Indítás értékűre változik, amikor egy feldolgozó jogcímet ad a feladatnak. Végül az állapot Fut lesz, amikor a runbook ténylegesen elindul.

4. Amikor a runbook-feladat befejeződik, a Teszt panel megjeleníti annak kimenetét. Ebben az esetben a következőt fogja `Hello World` látni: .

   ![Teszt panel kimenete](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook továbbra is Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.

2. Görgessen balra a runbook megtekintéséhez a Runbookok lapon, és figyelje meg, hogy a Szerzői állapot értéke **Közzétett.** 

3. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell oldalának megtekintéséhez.**
   
   A felül található beállításokkal elindíthatja a runbookot, ütemezhet egy jövőbeli kezdési időt, vagy létrehozhat egy [webhookot,](../automation-webhooks.md) hogy a runbook elindítható legyen egy HTTP-híváson keresztül.

4. Amikor **a rendszer** kéri, hogy indítsa el a runbookot, válassza az Indítás, majd az Igen lehetőséget.  

5. Megnyílik egy Feladat panel a létrehozott runbook-feladathoz. Bár bezárhatja ezt a panelt, hagyja megnyitva most, hogy figyelni tudja a feladat előrehaladását. A feladat állapota a Feladat összegzése alatt **látható,** a lehetséges állapotok pedig a runbook tesztelése során leírtak szerint.

   ![Feladat összegzése](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Ha a runbook állapota Befejezve, kattintson a **Kimenet** elemre a Kimenet lap megnyitásához, ahol `Hello World` megjelenik.

   ![Feladat kimenete](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Zárja be a Kimenet lapot.

8. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a kimeneti `Hello World` streamben kell látnia.

    Vegye figyelembe, hogy a Streamek panelen más streamek is meg tudnak mutatni egy runbook-feladathoz, például részletes és hibastreameket, ha a runbook rájuk ír.

   ![Minden napló](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Zárja be a Streamek panelt és a Feladat panelt, hogy visszatérjen MyFirstRunbook-PowerShell lapra.

10. A **Részletek alatt** kattintson a Feladatok **elemre** a runbook Feladatok oldalának megnyitásához. Ezen az oldalon a runbook által létrehozott összes feladat fel van sorolva. A listán csak egy feladatnak kell látsza, mivel csak egyszer futtatta a feladatot.

   ![Feladatlista](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Kattintson a feladat nevére, és nyissa meg ugyanazt a Feladat panelt, amely a runbook elkezdődött. Ezen a panelen megtekintheti a runbookhoz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ehhez a runbooknak képesnek kell lennie a hitelesítésre az Automation-fiók létrehozásakor automatikusan létrehozott fiókkal.

Ahogy az alábbi példában látható, a run as (Futtatás) kapcsolat a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal létesített kapcsolat. Ha több előfizetésben kezeli az erőforrásokat, a paramétert a `AzContext` [Get-AzContext használatával kell használnia.](/powershell/module/Az.Accounts/Get-AzContext)

> [!NOTE]
> A PowerShell-runbookok és `Add-AzAccount` `Add-AzureRMAccount` a a `Connect-AzAccount` aliasai. Használhatja ezeket a parancsmagokat, [](../automation-update-azure-modules.md) vagy frissítheti az Automation-fiókjában található modulokat a legújabb verziókra. Előfordulhat, hogy akkor is frissítenie kell a modulokat, ha most hozott létre egy új Automation-fiókot.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Nyissa meg a szöveges  szerkesztőt az oldal Szerkesztés MyFirstRunbook-PowerShell kattintva.

2. A sorra már `Write-Output` nincs szükség. Csak törölje azt.

3. Írja be vagy másolja be a következő kódot, amely az Automation-alapú futtatás fiókjával való hitelesítést kezeli.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Kattintson **a Teszt panelre,** hogy tesztelni tudja a runbookot.

5. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződött, az alábbihoz hasonló kimenetnek kell jelen lennie, amely a fiók alapvető adatait jeleníti meg. Ez a kimenet megerősíti, hogy a futtatott fiók érvényes.

   ![Hitelesítés](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesíti magát az Azure-előfizetésében, kezelheti az erőforrásokat. Adjunk hozzá egy parancsot a virtuális gép elindítani. Az Azure-előfizetésében bármelyik virtuális gépet választhatja, és a runbookban ezt a nevet most csak a kódba kell kódba felvennie.

1. A runbook-szkripthez adja hozzá a [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) parancsmagot a virtuális gép indításhoz. Ahogy az alább látható, a parancsmag elindít egy nevű és nevű `VMName` erőforráscsoporttal egy virtuális `ResourceGroupName` gépet.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Mentse a runbookot, majd kattintson a **Teszt panelre,** hogy tesztelni tudja.

3. A **teszt megkezdéséhez** kattintson az Indítás gombra. Ha befejeződött, ellenőrizze, hogy elindult-e a virtuális gép.

## <a name="step-7---add-an-input-parameter"></a>7. lépés – Bemeneti paraméter hozzáadása

A runbook jelenleg elindítja a runbookban nem kódolt virtuális gépet. A runbook akkor hasznosabb, ha megadja a virtuális gépet a runbook elindultkor. Adjunk bemeneti paramétereket a runbookhoz, hogy biztosítsuk ezt a funkciót.

1. A szöveges szerkesztőben módosítsa a parancsmagot úgy, hogy változókat használjon `Start-AzVM` a és `VMName` `ResourceGroupName` paraméterekhez. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.

3. Zárja be a Teszt panelt.

4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.

5. Állítsa le a korábban elindított virtuális gépet.

6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. 

7. Írja be az elindított virtuális gép **VMNAME** és **RESOURCEGROUPNAME** értékeit, majd kattintson az **OK gombra.**

    ![Paraméter átadása](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Amikor a runbook befejeződik, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="next-steps"></a>Következő lépések

* A PowerShellről, beleértve a nyelvi referenciát és a tanulási modulokat, a [PowerShell-dokumentumokban található további információ.](/powershell/scripting/overview)
* A PowerShell-parancsmagok referenciáiért lásd: [Az.Automation.](/powershell/module/az.automation)
* A grafikus runbookok első lépésekért [lásd: Grafikus runbook létrehozása.](automation-tutorial-runbook-graphical.md)
* A PowerShell munkafolyamat-forgatókönyvekkel való első lépésekért [lásd: PowerShell-hez való munkafolyamat-runbook létrehozása.](automation-tutorial-runbook-textual.md)
* A Runbook-típusokkal, azok előnyeivel és korlátozásokkal kapcsolatos további információkért lásd: Azure Automation [runbooktípusok.](../automation-runbook-types.md)
* A PowerShell-parancsfájlok támogatási funkcióval kapcsolatos további információkért lásd: Natív PowerShell-parancsfájlok támogatása [a Azure Automation.](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
