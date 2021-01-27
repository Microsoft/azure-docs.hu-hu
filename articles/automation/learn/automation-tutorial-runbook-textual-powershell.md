---
title: PowerShell-runbook létrehozása a Azure Automationban
description: Ez a cikk egy egyszerű PowerShell-runbook létrehozását, tesztelését és közzétételét ismerteti.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 896b4db433164471f41aa09791ede5d677028bfb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896613"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Oktatóanyag: PowerShell-runbook létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](../automation-runbook-types.md#powershell-runbooks). A PowerShell-runbookok a Windows PowerShellen alapulnak. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.

> [!div class="checklist"]
> * Egyszerű PowerShell-runbook létrehozása
> * A runbook tesztelése és közzététele
> * A runbook-feladatokhoz tartozó állapot futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép runbook paraméterekkel való elindításához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](../automation-quickstart-create-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása óta nem lehet üzemi virtuális gép.
* Szükség esetén [importálja az Azure-modulokat](../shared-resources/modules.md) vagy a [frissítési modulokat](../automation-update-azure-modules.md) a használt parancsmagok alapján.

## <a name="differences-from-powershell-workflow-runbooks"></a>A PowerShell-munkafolyamat runbookok származó különbségek

A PowerShell-runbookok a PowerShell-munkafolyamat runbookok azonos életciklussal, képességekkel és felügyelettel rendelkeznek. Van azonban néhány eltérés és korlátozás.

| Jellemző  | PowerShell-Runbookok | PowerShell-munkafolyamat Runbookok |
| ------ | ----- | ----- |
| Sebesség | Gyors Futtatás, ha nem használ fordítási lépést. | Lassabban fusson. |
| Ellenőrzőpontok | Az ellenőrzőpontok nem támogatottak. A PowerShell-runbook csak a kezdetektől folytathatja a műveletet. | Használjon ellenőrzőpontokat, amelyek lehetővé teszik, hogy a munkafüzetek bármely pontról folytassanak műveletet. |
| Parancs végrehajtása | Csak a soros végrehajtás támogatása. | Támogatja mind a soros, mind a párhuzamos végrehajtást.|
| RunSpace | Egyetlen RunSpace mindent futtat egy parancsfájlban. | Egy tevékenységhez, parancshoz vagy parancsfájl-blokkhoz külön RunSpace lehet használni. |

Ezen eltérések mellett a PowerShell-runbookok a PowerShell-munkafolyamatok runbookok származó [szintaktikai különbségeket](/previous-versions/technet-magazine/dn151046(v=msdn.10)) is tartalmaz.

## <a name="step-1---create-runbook"></a>1. lépés – Runbook létrehozása

Először hozzon létre egy egyszerű runbook, amely kiírja a szöveget `Hello World` .

1. Az Azure Portalon nyissa meg az Automation-fiókját.

2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

3. Hozzon létre egy új runbook a **Runbook létrehozása** lehetőség kiválasztásával.

4. Adja a forgatókönyvnek a **MyFirstRunbook-PowerShell** nevet.

5. Ebben az esetben létre kell hoznia egy PowerShell- [runbook](../automation-runbook-types.md#powershell-runbooks). Válassza a **PowerShell** lehetőséget a **Runbook típushoz**.

6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben az oktatóanyagban közvetlenül a runbook írja be a kódot.

1. A runbook jelenleg üres. Írja be a `Write-Output "Hello World"` parancsfájl törzsét.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> 3. lépés – a runbook tesztelése

Mielőtt közzéteszi a runbook, hogy az éles környezetben elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megjelenítését.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.

3. Vegye figyelembe, hogy létrejön egy [runbook-feladatok](../automation-runbook-execution.md) , és az állapota megjelenik a panelen.

   A feladatok állapota a várólistára kerül, ami azt jelzi, hogy a runbook-feldolgozó a felhőben elérhetővé válására vár. Az állapot akkor változik, ha egy feldolgozó a feladatot állítja be. Végül az állapot akkor fut le, amikor a runbook ténylegesen elindul.

4. Ha a runbook-feladatok befejeződik, a teszt ablaktábla megjeleníti a kimenetet. Ebben az esetben a következőt látja: `Hello World` .

   ![Teszt panel kimenete](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.

2. Görgessen balra a runbook megtekintéséhez a Runbookok lapon, és vegye figyelembe, hogy a **szerzői állapot** értéke **közzétételre** van állítva.

3. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** oldalának megtekintéséhez.
   
   A felső beállítások lehetővé teszik a runbook elindítását, a jövőbeli kezdési idő beírását, vagy [webhook](../automation-webhooks.md) létrehozását, hogy a runbook http-hívással is elindítható legyen.

4. Kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer felszólítja a runbook elindítására. 

5. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Bár bezárhatja ezt a panelt, hagyja megnyitva most, hogy megtekintse a feladatok előrehaladását. A feladatok állapota a **feladatok összegzése** területen látható, a lehetséges állapotok pedig a runbook teszteléséhez szükségesek.

   ![Feladat összegzése](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Miután a runbook állapota megjelenik, kattintson a **kimenet** elemre a kimenet lap megnyitásához, ahol `Hello World` megjelenik a megjelenített üzenet.

   ![Feladat kimenete](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. A kimeneti oldal bezárásához.

8. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak `Hello World` a kimeneti adatfolyamban látható.

    Vegye figyelembe, hogy a streamek panel más streameket jeleníthet meg egy runbook-feladatokhoz, például a részletes és a hiba-adatfolyamokhoz, ha a runbook ezeket írja.

   ![Minden napló](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. A MyFirstRunbook-PowerShell lapra való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.

10. A **részletek** területen kattintson a **feladatok** elemre a runbook feladatok lapjának megnyitásához. Ezen az oldalon a runbook által létrehozott összes feladat szerepel. Csak egy feladatot kell látnia, mert csak egyszer futtatta a feladatot.

   ![Feladatlista](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Kattintson a feladatokra, hogy megnyissa a runbook elindításakor megtekintett feladatok ablaktábláját. Ezen ablaktábla használatával megtekintheti a runbook létrehozott feladatok részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ehhez a runbook képesnek kell lennie hitelesíteni az Automation-fiók létrehozásakor automatikusan létrehozott futtató fiók használatával.

Ahogy az alábbi példában is látható, a futtató kapcsolat a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal történik. Ha több előfizetésen keresztül kezeli az erőforrásokat, a `AzContext` paramétert a [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext)használatával kell használnia.

> [!NOTE]
> A PowerShell-runbookok esetében a `Add-AzAccount` és a `Add-AzureRMAccount` álneve a következőhöz: `Connect-AzAccount` . Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](../automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

1. A MyFirstRunbook-PowerShell oldalon található **Szerkesztés** gombra kattintva nyissa meg a szöveges szerkesztőt.

2. Nincs szükség a `Write-Output` sorra. Csak ugorjon, és törölje.

3. Írja be vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókkal.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

4. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.

5. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződik, a következőhöz hasonló kimenetnek kell megjelennie, amely a fiók alapvető információit jeleníti meg. Ez a kimenet megerősíti, hogy a futtató fiók érvényes.

   ![Hitelesítés](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítést végez az Azure-előfizetésében, kezelheti az erőforrásokat. Hozzunk létre egy parancsot egy virtuális gép indításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már csak a runbook található kódot használhatja.

1. A runbook-szkripthez adja hozzá a [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) parancsmagot a virtuális gép elindításához. Ahogy az alábbi ábrán is látható, a parancsmag egy nevű nevű virtuális gépet indít el a névvel `VMName` és egy nevű erőforráscsoportot `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

2. Mentse a runbook, majd kattintson a **teszt panelre** , hogy tesztelni tudja.

3. A teszt **elindításához** kattintson a Start gombra. A művelet befejezését követően győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-7---add-an-input-parameter"></a>7. lépés – bemeneti paraméter hozzáadása

A runbook jelenleg elindítja a virtuális gépet, amelyet a runbook rögzített. A runbook akkor hasznos, ha a runbook indításakor megadja a virtuális gépet. Adja hozzá a runbook bemeneti paramétereit a funkció megadásához.

1. A szöveges szerkesztőben módosítsa a `Start-AzVM` parancsmagot úgy, hogy a paraméterekhez és ahoz változókat használjon `VMName` `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

7. Írja be a **VMNAME** és a **RESOURCEGROUPNAME** értékeket az elindítani kívánt virtuális géphez, majd kattintson **az OK** gombra.

    ![Paraméter átadása](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. A runbook befejezésekor ellenőrizze, hogy elindult-e a virtuális gép.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-lel kapcsolatos további információk, beleértve a nyelvi referenciákat és a tanulási modulokat, lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
* A grafikus runbookok megkezdéséhez tekintse meg [a grafikus Runbook létrehozását](automation-tutorial-runbook-graphical.md)ismertető témakört.
* A PowerShell-munkafolyamat runbookok megkezdéséhez tekintse meg [a PowerShell-munkafolyamat Runbook létrehozása](automation-tutorial-runbook-textual.md)című témakört.
* Ha többet szeretne megtudni a runbook típusairól és azok előnyeiről és korlátairól, tekintse meg a [Azure Automation runbook-típusok](../automation-runbook-types.md)című témakört.
* További információ a PowerShell-parancsfájl támogatásáról: [natív PowerShell-parancsfájl-támogatás Azure Automationban](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
