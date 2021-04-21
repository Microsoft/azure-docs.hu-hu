---
title: PowerShell-munkafolyamati runbook létrehozása a Azure Automation
description: Ez a cikk egy egyszerű PowerShell-munkafolyamat-runbook létrehozására, tesztelésére és közzétételére tanítja be.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 45c7bb19e3f48fedf93fbb147a3014e09914a475
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831275"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Oktatóanyag: PowerShell-munkafolyamati runbook létrehozása

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](../automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. A PowerShell-alapú munkafolyamat-runbookok szöveges runbookok, amelyek a Windows PowerShell alapulnak. A forgatókönyv kódját a forgatókönyv szövegszerkesztőjével hozhatja létre és szerkesztheti a Azure Portal. 

> [!div class="checklist"]
> * Egyszerű PowerShell-munkafolyamati runbook létrehozása
> * A runbook tesztelése és közzététele
> * Runbook-feladat futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép runbook-paraméterekkel való futtatásához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](../index.yml) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Mivel ezt a gépet leállítja és elindítja, nem lehet éles virtuális gép.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása

Első lépésként hozzon létre egy egyszerű runbookot, amely kiadja a következő szöveget: `Hello World` .

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezen eszközök nagy része automatikusan bekerül egy új Automation-fiókba. Emellett rendelkeznie kell az előfizetéséhez társított hitelesítőadat-adatsokkal is.
 
2. A **Runbookok listájának** megnyitásához válassza a **Folyamatok** automatizálása alatt található Runbookok lehetőséget.

3. Hozzon létre egy új runbookot **a Runbook létrehozása lehetőség kiválasztásával.**

4. Adja a forgatókönyvnek a **MyFirstRunbook-Workflow** nevet.

5. Ebben az esetben egy [PowerShell-munkafolyamati runbookot fog létrehozni.](../automation-runbook-types.md#powershell-workflow-runbooks) Runbooktípusként válassza a **PowerShell-munkafolyamat** **lehetőséget.**

6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz

Begépelheti a kódot közvetlenül a runbookba, vagy kiválaszthatja a parancsmagokat, runbookokat és eszközöket a Könyvtár vezérlőből, és hozzáadhatja őket a runbookhoz a kapcsolódó paraméterekkel együtt. Ebben az oktatóanyagban közvetlenül a runbookba írja be a kódot.

1. A runbook jelenleg üres, csak a kötelező kulcsszóval, a runbook nevével és a teljes munkafolyamatot kapcsos `Workflow` zárójelekkel.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. A `Write-Output "Hello World"` kapcsos zárójelek közé írja be a következőt: .

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbookot, hogy éles környezetben is elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Draft (Vázlat) verziót futtatja, és lehetővé teszi a kimenet interaktív megtekintését.

1. Válassza **a Teszt panelt** a Teszt panel megnyitásához.

2. Kattintson **a Start** gombra a teszt elkezdéshez, és tesztelje az egyetlen engedélyezett beállítást.

3. Vegye figyelembe, hogy a [runbook-feladat](../automation-runbook-execution.md) létrejön, és az állapota megjelenik a panelen.

   A feladat állapota Várólistán állapotú, ami azt jelzi, hogy a feladat arra vár, hogy a felhőben lévő runbook-feldolgozó elérhetővé váljon. Az állapot Indítás értékűre változik, amikor egy feldolgozó a feladatot jogcímre állítja. Végül az állapot Fut lesz, amikor a runbook ténylegesen elindul.

4. Amikor a runbook-feladat befejeződik, a Teszt panel megjeleníti annak kimenetét. Ebben az esetben a következőt fogja `Hello World` látni: .

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook továbbra is Piszkozat módban van. Éles környezetben való futtatás előtt közzé kell tennie. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.

2. Görgessen balra a runbook megtekintéséhez a **Runbookok** lapon, és figyelje meg, hogy a Szerzői **állapot** mező közzétettre **van állítva.**

3. Görgessen vissza jobbra a **MyFirstRunbook-Workflow oldalának megtekintéséhez.**

   A felül található beállításokkal elindíthatja a runbookot, ütemezhet egy jövőbeli kezdési időt, vagy létrehozhat egy [webhookot,](../automation-webhooks.md) hogy a runbook http-híváson keresztül elindítható legyen.

4. Amikor **a rendszer** kéri, hogy indítsa el a runbookot, válassza az Indítás, majd az Igen lehetőséget. 

   ![Forgatókönyv indítása](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Megnyílik egy Feladat panel a létrehozott runbook-feladathoz. Ebben az esetben hagyja megnyitva a panelt, hogy figyelni tudja a feladat előrehaladását.

6. Vegye figyelembe, hogy a feladat állapota a Feladat **összegzése alatt látható.** Ez az állapot megegyezik a runbook tesztelése során látott állapotokkal.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Képernyőkép a Runbook-feladat panelről, melyen a Feladat összegzése szakasz van kiemelve.":::

7. Ha a runbook állapota Befejezve, kattintson a **Kimenet elemre.** Megnyílik a Kimenet lap, ahol láthatja az `Hello World` üzenetet.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="A Runbook Feladat panelének képernyőképe, kiemelve a Kimenet gombot.":::

8. Zárja be a Kimenet lapot.

9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a kimeneti `Hello World` streamben kell látnia. Vegye figyelembe, hogy a Streamek panelen más streamek is meg tudnak mutatni egy runbook-feladathoz, például részletes és hibastreameket, ha a runbook rájuk ír.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="A Runbook Feladat panelének képernyőképe, kiemelve a Minden napló gombot.":::

10. Zárja be a Streamek panelt és a Feladat panelt, hogy visszatérjen a MyFirstRunbook lapra.

11. Kattintson **az Erőforrások** alatti Feladatok **elemre** a runbook Feladatok oldalának megnyitásához. Ezen az oldalon a runbook által létrehozott összes feladat fel van sorolva. A listán csak egy feladatnak kell látszanának, mivel a feladatot csak egyszer futtatta.

   ![Feladatok](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Kattintson a feladat nevére, és nyissa meg ugyanazt a Feladat panelt, amely a runbook elkezdődött. Ezen a panelen megtekintheti a runbookhoz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ez csak akkor használhatja ezt a lehetőséget, ha az előfizetés hitelesítő adataival hitelesíti magát. A hitelesítés a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot használja.

>[!NOTE]
>A PowerShell-runbookok és `Add-AzAccount` `Add-AzureRMAccount` a a `Connect-AzAccount` aliasai. Használhatja ezeket a parancsmagokat, [](../automation-update-azure-modules.md) vagy frissítheti az Automation-fiókjában található modulokat a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha most hozott létre új Automation-fiókot.

1. Lépjen a MyFirstRunbook-Workflow oldalára, és nyissa meg a szöveges szerkesztőt a **Szerkesztés gombra kattintva.**

2. Törölje a `Write-Output` sort.

3. Vigye a kurzort egy üres sorra a zárójelek között.

4. Írja be vagy másolja be a következő kódot, amely az Automation-alapú futtatás fiókjával való hitelesítést kezeli.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Kattintson **a Teszt panelre,** hogy tesztelni tudja a runbookot.

6. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződött, az alábbihoz hasonló kimenetnek kell jelen lennie, amely a fiók alapvető adatait jeleníti meg. Ez a művelet megerősíti, hogy a hitelesítő adatok érvényesek.

   ![Hitelesítés](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesíti magát az Azure-előfizetésben, kezelheti az erőforrásokat. Adjunk hozzá egy parancsot a virtuális gép elindítani. Bármelyik virtuális gépet választhatja az Azure-előfizetésében, és most ezt a nevet szoftveres ként kódolta a runbookban. Ha több előfizetésben kezeli az erőforrásokat, a paramétert a `AzContext` [Get-AzContext parancsmaggal kell](/powershell/module/az.accounts/get-azcontext) használnia.

1. Adja meg a virtuális gép nevét és erőforráscsoport-nevét. Először adja meg a [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) parancsmag hívását az alább látható módon. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Mentse a runbookot, majd kattintson a Teszt **panelre,** hogy tesztelni tudja.

3. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződött, ellenőrizze, hogy elindult-e a virtuális gép.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz

A runbook jelenleg elindítja azt a virtuális gépet, amely szoftveres a runbookban. Ez hasznosabb lesz, ha megadhatja a virtuális gépet a runbook elindítottkor. Adjunk bemeneti paramétereket a runbookhoz, hogy biztosítsuk ezt a funkciót.

1. Adja hozzá a és a paraméter változóit a runbookhoz, és használja a változókat a `VMName` parancsmaggal az alább látható `ResourceGroupName` `Start-AzVM` módon.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már meg lehet adni értékeket a tesztben megadott két bemeneti változóhoz.

3. Zárja be a Teszt panelt.

4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.

5. Állítsa le az elindított virtuális gépet.

6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. 

7. Írja be az elindított virtuális gép **VMNAME** és **RESOURCEGROUPNAME** értékeit.

   ![Forgatókönyv indítása](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Amikor a runbook befejeződik, ellenőrizze, hogy elindult-e a virtuális gép.

## <a name="next-steps"></a>Következő lépések

* A PowerShellről, beleértve a nyelvi referenciákat és a tanulási modulokat, a [PowerShell-dokumentumokban található további információ.](/powershell/scripting/overview)
* A PowerShell-parancsmagok referenciáiért lásd: [Az.Automation](/powershell/module/az.automation).
* A grafikus runbookok első lépésekért [lásd: Grafikus runbook létrehozása.](automation-tutorial-runbook-graphical.md)
* A PowerShell-runbookok első lépésekért [lásd: PowerShell-runbook létrehozása.](automation-tutorial-runbook-textual-powershell.md)
* A Runbook-típusokkal, azok előnyeivel és korlátozásaival kapcsolatos további információkért lásd: [Azure Automation runbooktípusok.](../automation-runbook-types.md)
* A PowerShell-parancsfájlok támogatási funkcióival kapcsolatos további információkért lásd: Natív PowerShell-parancsfájlok támogatása [a Azure Automation.](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
