---
title: PowerShell munkafolyamat-runbook létrehozása Azure Automationban
description: Ez a cikk egy egyszerű PowerShell munkafolyamat-runbook létrehozását, tesztelését és közzétételét ismerteti.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e822e17f154b72854c5150e071301d7bbd53072e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890815"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Oktatóanyag: PowerShell munkafolyamat-runbook létrehozása

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](../automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. A PowerShell-munkafolyamat runbookok a Windows PowerShell-munkafolyamaton alapuló szöveges runbookok. A runbook kódját a Azure Portal szövegszerkesztő használatával hozhatja létre és szerkesztheti. 

> [!div class="checklist"]
> * Egyszerű PowerShell munkafolyamat-runbook létrehozása
> * A runbook tesztelése és közzététele
> * A runbook-feladatokhoz tartozó állapot futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép runbook paraméterekkel való elindításához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](../index.yml) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása óta nem lehet üzemi virtuális gép.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása

Először hozzon létre egy egyszerű runbook, amely kiírja a szöveget `Hello World` .

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezeknek az eszközöknek a túlnyomó része automatikusan egy új Automation-fiókba kerül. Az előfizetéshez társított hitelesítőadat-eszközt is meg kell adni.
 
2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

3. Hozzon létre egy új runbook a **Runbook létrehozása** lehetőség kiválasztásával.

4. Adja a forgatókönyvnek a **MyFirstRunbook-Workflow** nevet.

5. Ebben az esetben létre fog hozni egy [PowerShell munkafolyamat-runbook](../automation-runbook-types.md#powershell-workflow-runbooks). Válassza ki a **PowerShell-munkafolyamatot** a **Runbook típushoz**.

6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz

Közvetlenül is írhat kódot a runbook, vagy kijelölhet parancsmagokat, runbookok és eszközöket a könyvtár vezérlőelemből, és hozzáadhatja őket a runbook a kapcsolódó paraméterekkel. Ebben az oktatóanyagban közvetlenül a runbook írja be a kódot.

1. A runbook jelenleg csak a kötelező `Workflow` kulcsszóval, a runbook nevével és a teljes munkafolyamatot tartalmazó kapcsos zárójelekkel van ellátva.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Adja meg `Write-Output "Hello World"` a kapcsos zárójelek közötti értéket.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbook, hogy az éles környezetben elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megjelenítését.

1. A teszt panel megnyitásához válassza a **teszt panelt** .

2. Kattintson a **Start** gombra a teszt elindításához, az egyetlen engedélyezett lehetőség tesztelésével.

3. Vegye figyelembe, hogy létrejön egy [runbook-feladatok](../automation-runbook-execution.md) , és az állapota megjelenik a panelen.

   A feladatok állapota a várólistára kerül, ami azt jelzi, hogy a runbook-feldolgozó a felhőben elérhetővé válására vár. Az állapot akkor változik, ha egy feldolgozó a feladatot állítja be. Végül az állapot akkor fut le, amikor a runbook ténylegesen elindul.

4. Ha a runbook-feladatok befejeződik, a teszt ablaktábla megjeleníti a kimenetet. Ebben az esetben a következőt látja: `Hello World` .

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tennie. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.

2. Görgessen balra, hogy megtekintse a runbook a **runbookok** lapon, és figyelje meg, hogy a **szerzői állapot** mező **közzé** van-e téve.

3. Görgessen vissza jobbra a **MyFirstRunbook-workflow** oldal megtekintéséhez.

   A felső beállítások lehetővé teszik a runbook elindítását, a jövőbeli kezdési idő beírását, vagy [webhook](../automation-webhooks.md) létrehozását, hogy a runbook http-hívással is elindítható legyen.

4. Kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer felszólítja a runbook elindítására.

   ![Forgatókönyv indítása](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Ebben az esetben hagyja megnyitva a panelt, hogy megtekintse a feladatok állapotát.

6. Vegye figyelembe, hogy a feladatok állapota a **feladatok összegzése** területen látható. Ez az állapot megegyezik a runbook tesztelésekor meglátott állapotokkal.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Képernyőkép a runbook-feladat panelről, amely a feladat összegzése szakaszra van kiemelve.":::

7. Miután a runbook állapota megjelenik, kattintson a **kimenet** elemre. Megnyílik a kimenet lap, ahol megtekintheti az `Hello World` üzenetet.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Képernyőkép a runbook-feladat panelről, amely a Kiemelt kimenet gombot mutatja.":::

8. A kimeneti oldal bezárásához.

9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak `Hello World` a kimeneti adatfolyamban látható. Vegye figyelembe, hogy a streamek panel más streameket jeleníthet meg egy runbook-feladatokhoz, például a részletes és a hiba-adatfolyamokhoz, ha a runbook ezeket írja.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Képernyőkép a runbook-feladat panelről, amelyen a minden napló gomb ki van emelve.":::

10. A MyFirstRunbook lapra való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.

11. A runbook feladatok lapjának megnyitásához kattintson az **erőforrások** alatt lévő **feladatok** elemre. Ezen az oldalon a runbook által létrehozott összes feladat szerepel. Csak egy feladatot kell látnia, mert csak egyszer futtatta a feladatot.

   ![Feladatok](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Kattintson a feladatokra, hogy megnyissa a runbook elindításakor megtekintett feladatok ablaktábláját. Ezen ablaktábla használatával megtekintheti a runbook létrehozott feladatok részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ez a művelet csak akkor hajtható végre, ha az előfizetés hitelesítő adataival hitelesíti magát. A hitelesítés a [kapcsolat-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot használja.

>[!NOTE]
>A PowerShell-runbookok esetében a `Add-AzAccount` és a `Add-AzureRMAccount` álneve a következőhöz: `Connect-AzAccount` . Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](../automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

1. Navigáljon a MyFirstRunbook-Workflow lapra, és nyissa meg a szöveges szerkesztőt a **Szerkesztés** gombra kattintva.

2. Törölje a `Write-Output` sort.

3. Vigye a kurzort egy üres sorra a zárójelek között.

4. Írja be vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókkal.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.

6. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződik, a következőhöz hasonló kimenetnek kell megjelennie, amely a fiók alapvető információit jeleníti meg. Ez a művelet megerősíti, hogy a hitelesítő adat érvényes.

   ![Hitelesítés](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítést végez az Azure-előfizetésben, kezelheti az erőforrásokat. Hozzunk létre egy parancsot egy virtuális gép indításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már rögzítjük ezt a nevet a runbook. Ha több előfizetésen keresztül kezeli az erőforrásokat, akkor a `AzContext` paramétert a [Get-AzContext](/powershell/module/az.accounts/get-azcontext) parancsmaggal kell használnia.

1. Adja meg a kezdéshez a virtuális gép nevét és erőforráscsoport-nevét, majd írja be a [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) parancsmag meghívását az alábbi ábrán látható módon. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Mentse a runbook, majd kattintson a **teszt panelre** , hogy tesztelni tudja.

3. Kattintson az **Indítás** gombra a teszt elindításához. Miután befejeződött, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz

A runbook jelenleg elindítja azt a virtuális gépet, amelyet a runbook hardcoded. Hasznos lehet, ha megadhatja a virtuális gépet a runbook indításakor. Adja hozzá a runbook bemeneti paramétereit a funkció megadásához.

1. Adja hozzá a és a paraméterek változóit `VMName` `ResourceGroupName` a runbook, és használja a változókat az `Start-AzVM` alább látható parancsmaggal.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a tesztben szereplő két bemeneti változóhoz.

3. Zárja be a Teszt panelt.

4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.

5. Állítsa le az elindított virtuális gépet.

6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. 

7. Írja be a **VMNAME** és a **RESOURCEGROUPNAME** értékeit az elindítani kívánt virtuális gépre vonatkozóan.

   ![Forgatókönyv indítása](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. A runbook befejezésekor ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
* A grafikus runbookok megkezdéséhez tekintse meg [a grafikus Runbook létrehozását](automation-tutorial-runbook-graphical.md)ismertető témakört.
* A PowerShell-runbookok megkezdéséhez tekintse meg [a PowerShell-Runbook létrehozása](automation-tutorial-runbook-textual-powershell.md)című témakört.
* További információ a runbook típusairól és azok előnyeiről és korlátairól: [Azure Automation runbook-típusok](../automation-runbook-types.md).
* A PowerShell-parancsfájlok támogatásával kapcsolatos további információkért lásd: [natív PowerShell-parancsfájl-támogatás Azure Automationban](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
