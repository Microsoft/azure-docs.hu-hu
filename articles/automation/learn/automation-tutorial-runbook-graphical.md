---
title: Grafikus runbook létrehozása a Azure Automation
description: Ez a cikk egy egyszerű grafikus runbook létrehozására, tesztelésére és közzétételére tanítja be a Azure Automation.
keywords: runbook, runbook-sablon, runbook automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 668f661d2c8f349f4dac3f850800d83890d2f4d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831437"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Oktatóanyag: Grafikus runbook létrehozása

Egy Azure Automation [grafikus forgatókönyv](../automation-runbook-types.md#graphical-runbooks) létrehozását bemutató oktatóanyag. Grafikus és grafikus PowerShell-munkafolyamat-runbookokat hozhat létre és szerkeszthet az alkalmazás grafikus Azure Portal. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyszerű grafikus runbook létrehozása
> * A runbook tesztelése és közzététele
> * Run and track the status of the runbook job (Runbook-feladat futtatása és állapotának nyomon követése)
> * A runbook frissítése egy Azure-beli virtuális gép indításhoz runbook-paraméterekkel és feltételes hivatkozásokkal

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free).
* [Automation-fiók](../index.yml) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Mivel ezt a gépet leállítja és elindítja, nem lehet éles virtuális gép.
* Ha szükséges, [importálja az](../shared-resources/modules.md) Azure-modulokat, vagy frissítse a modulokat a használt parancsmagok alapján. [](../automation-update-azure-modules.md)

## <a name="step-1---create-runbook"></a>1. lépés – Runbook létrehozása

Első lépésként hozzon létre egy egyszerű runbookot, amely a következő szöveget kiadja: `Hello World` .

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. A legtöbb ilyen eszköz az új Automation-fiók automatikusan tartalmazza a modulokat. Rendelkeznie kell az előfizetéséhez társított hitelesítőadat-adatt is.

2. A **Runbookok listájának** megnyitásához válassza a **Folyamatok** automatizálása alatt található Runbookok lehetőséget.

3. Hozzon létre egy új runbookot **a Runbook létrehozása lehetőség kiválasztásával.**

4. Adja a forgatókönyvnek a **MyFirstRunbook-Graphical** nevet.

5. Ebben az esetben egy grafikus [runbookot fog létrehozni.](../automation-graphical-authoring-intro.md) **Runbooktípusként** válassza **a Grafikus lehetőséget.**

    ![Új forgatókönyv](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. A forgatókönyv létrehozásához és a grafikus szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-activities"></a>2. lépés – Tevékenységek hozzáadása

A szerkesztő bal oldalán levő Könyvtárvezérlés segítségével kiválaszthatja a forgatókönyvhöz hozzáadni kívánt tevékenységeket. Hozzá fog adni egy parancsmagot, hogy szöveget adjon ki a `Write-Output` runbookból.

1. A Könyvtár vezérlőben kattintson a keresőmezőbe, és írja be a következőt: `write-output` . A keresési eredmények az alábbi képen láthatók.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Görgessen le a lista aljához. Kattintson a jobb gombbal **a Write-Output elemre,** és válassza **a Hozzáadás a vászonhoz lehetőséget.** Másik lehetőségként kattintson a parancsmag neve melletti három pontra (...), majd válassza a Hozzáadás **a vászonhoz lehetőséget.**

3. A vásznon kattintson a **Write-Output** tevékenységre. Ez a művelet megnyitja a Konfiguráció vezérlőoldalt, amelyen konfigurálhatja a tevékenységet.

4. A **Címke** mező alapértelmezett neve a parancsmag neve, de módosíthatja valami felhasználóbarátabbra. Módosítsa a következőre: `Write Hello World to output`.

5. A **Paraméterek** elemre kattintva megadhatja a parancsmag paramétereinek értékét.

   Egyes parancsmagok több paraméterkészletet is használhatnak, és ki kell választania, hogy melyiket használja. Ebben az esetben a `Write-Output` csak egy paraméterkészletet rendelkezik.

6. Válassza ki a `InputObject` paramétert. Ezzel a paraméterrel adhatja meg a kimeneti streambe küldend kívánt szöveget.

7. Az **Adatforrás legördülő** menü olyan forrásokat tartalmaz, amelyek a paraméterértékek feltöltéséhez használhatók. Ebben a menüben válassza a **PowerShell-kifejezés lehetőséget.**

   Használhatja az ilyen forrásokból származó kimeneteket, például egy másik tevékenységet, egy Automation-eszközt vagy egy PowerShell-kifejezést. Ebben az esetben a kimenet csak `Hello World` . Megadhat egy sztringet egy PowerShell-kifejezéssel is.

8. A Kifejezés **mezőbe írja** be a kifejezést, majd kattintson kétszer `"Hello World"` az **OK** gombra a vászonra való visszatéréshez.

9. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbookot, hogy éles környezetben is elérhetővé tegye, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megtekintését.

1. Válassza **a Teszt panelt** a Teszt panel megnyitásához.

2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.

3. Vegye figyelembe, hogy a [runbook-feladat](../automation-runbook-execution.md) létrejön, és az állapota megjelenik a panelen.

   A feladat állapota , ami azt jelzi, hogy a feladat arra vár, hogy egy felhőben futó `Queued` runbook-feldolgozó elérhetővé váljon. Az állapot a következőre `Starting` változik: amikor egy feldolgozó a feladatot jogcímbe állítja. Végül az állapot akkor lesz, amikor a `Running` runbook ténylegesen elindul.

4. Amikor a runbook-feladat befejeződik, a Teszt panel megjeleníti annak kimenetét. Ebben az esetben a következőt fogja `Hello World` látni: .

    ![Hello World runbook kimenete](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook továbbra is Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. Válassza **a Közzététel** lehetőséget a runbook közzétételéhez, majd amikor a rendszer kéri, kattintson az **Igen** gombra.

2. Görgessen balra a runbook megtekintéséhez a Runbookok lapon, és figyelje meg, hogy a Szerzői **állapot** értéke **Közzétett.**

3. Görgessen vissza jobbra a **MyFirstRunbook-Graphical oldalának megtekintéséhez.**

   A felül található beállításokkal elindíthatja a runbookot, ütemezhet egy jövőbeli kezdési időt, vagy létrehozhat egy [webhookot,](../automation-webhooks.md) hogy a runbook http-híváson keresztül elindítható legyen.

4. Amikor **a rendszer** kéri, hogy indítsa el a runbookot, válassza az Indítás, majd az Igen lehetőséget. 

5. Megnyílik egy Feladat panel a létrehozott runbook-feladathoz. Ellenőrizze, hogy a **Feladat állapota mezőben** a **Befejezve jelenik-e meg.**

6. Kattintson **a Kimenet** elemre a Kimenet lap megnyitásához, ahol `Hello World` megjelenik.

7. Zárja be a Kimenet lapot.

8. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a kimeneti `Hello World` streamben kell látnia.

    Vegye figyelembe, hogy a Streamek panelen egy runbook-feladat egyéb adatfolyamai is láthatóak, például részletes és hibastreamek, ha a runbook ezekbe ír.

9. Zárja be a Streamek panelt és a Feladat panelt, hogy visszatérjen MyFirstRunbook-Graphical lapra.

10. A runbook összes feladatának megtekintéséhez válassza a **Resources (Erőforrások) alatt a Jobs** (Feladatok) **lehetőséget.** A Feladatok lapon a runbook által létrehozott összes feladat fel van sorolva. A listán csak egy feladatnak kell látsza, mivel csak egyszer futtatta azt.

11. Kattintson a feladat nevére, és nyissa meg ugyanazt a Feladat panelt, amely a runbook elkezdődött. Ezen a panelen megtekintheti a runbookhoz létrehozott összes feladat részleteit.

## <a name="step-5---create-variable-assets"></a>5. lépés: Változó adategységek létrehozása

Tesztelte és közzétette a runbookot, de egyelőre semmi hasznosat nem csinál az Azure-erőforrások kezeléséhez. A runbook hitelesítésre való konfigurálása előtt létre kell hoznia egy változót, amely az előfizetés-azonosítót fogja tartalmazni, be kell állítania egy tevékenységet a hitelesítéshez, majd hivatkozni kell a változóra. Az előfizetési környezetre való hivatkozással könnyedén dolgozhat több előfizetéssel.

1. Másolja ki az előfizetés-azonosítóját **a** Navigációs panel Előfizetések lehetőségéből.

2. Az Automation-fiókok lapon a **Megosztott** erőforrások alatt válassza a **Változók lehetőséget.**

3. Válassza **a Változó hozzáadása lehetőséget.**

4. Az Új változó lapon a megadott mezőkben tegye a következőket.

    * **Név –** írja be a `AzureSubscriptionId` nevet.
    * **Érték** – adja meg az előfizetés azonosítóját.
    * **Írja be a** következőt: – tartsa meg a sztringet kiválasztva.
    * **Titkosítás** – használja az alapértelmezett értéket.

5. A változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="step-6---add-authentication"></a>6. lépés – Hitelesítés hozzáadása

Most, hogy rendelkezik egy változóval az előfizetés-azonosítóhoz, konfigurálhatja úgy a runbookot, hogy az az előfizetéshez használt run as hitelesítő adatokkal hitelesítse magát. Ehhez adja hozzá az Azure-beli futási kapcsolatot adateszközként. A [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmagot és a [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) parancsmagot is hozzá kell adni a vászonhoz.

>[!NOTE]
>A PowerShell-runbookok és `Add-AzAccount` `Add-AzureRMAccount` a a `Connect-AzAccount` aliasai. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok számára. A grafikus runbookok csak saját magát `Connect-AzAccount` használhatják.

1. Lépjen a runbookra, és válassza a Szerkesztés **lehetőséget** MyFirstRunbook-Graphical oldalon.

2. A bejegyzésre már `Write Hello World to output` nincs szükség. Kattintson a három pontra, és válassza a **Törlés lehetőséget.**

3. A Könyvtár vezérlőben **bontsa ki az ASSETS (ESZKÖZÖK) et,** majd a **Connections (Kapcsolatok) et.** Adja `AzureRunAsConnection` hozzá a vászonhoz a Hozzáadás a **vászonhoz lehetőség kiválasztásával.**

4. Nevezze át a `AzureRunAsConnection` nevet `Get Run As Connection` a névre.

5. A Könyvtár vezérlőbe írja be a `Connect-AzAccount` keresőmezőt.

6. Adja `Connect-AzAccount` hozzá a gombra a vásznon.

7. Vigye az egérmutatót addig, amíg meg nem jelenik egy kör az `Get Run As Connection` alakzat alján. Kattintson a körre, és húzza a nyilat `Connect-AzAccount` a elemhez, hogy egy hivatkozást hoz létre. A runbook a következővel `Get Run As Connection` kezdődik, majd futtatja a `Connect-AzAccount` következőt: .

    ![Hivatkozás létrehozása a tevékenységek között](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. A vásznon válassza a `Connect-AzAccount` lehetőséget. A Konfiguráció vezérlőpanelen írja be a Bejelentkezés az **Azure-ba** nevet a **Címke mezőbe.**

9. Kattintson **a Paraméterek** elemre, és megjelenik a Tevékenységparaméter konfigurálása lap.

10. A parancsmag több paraméterkészlettel rendelkezik, és a paraméterértékek megadása előtt ki kell `Connect-AzAccount` választania egyet. Kattintson **a Paraméterkészlet elemre,** majd válassza a **ServicePrincipalCertificateWithSubscriptionId kódot.**

11. A paraméterkészlet paraméterei a Tevékenységparaméter-konfiguráció lapon jelennek meg. Kattintson az **APPLICATIONID** elemre.

    ![Azure-fiók paramétereinek hozzáadása](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. A Paraméter értéke lapon használja a következő beállításokat, majd kattintson az **OK gombra.**

   * **Adatforrás –** válassza a **Tevékenység kimenete lehetőséget.**
   * Adatforráslista – válassza az **Automation-kapcsolat bejőt.**
   * **Mező elérési** útja – írja be a következőt: `ApplicationId` . Azért adja meg a mező elérési útjának tulajdonságnevét, mert a tevékenység egy több tulajdonsággal rendelkező objektumot ad ki.

13. Kattintson **a CERTIFICATETHUMBPRINT** elemre, majd a Paraméter értéke lapon tegye a következőket, majd kattintson az **OK gombra.**

    * **Adatforrás –** válassza a **Tevékenység kimenete lehetőséget.**
    * Adatforráslista – válassza az **Automation-kapcsolat bejőt.**
    * **Mező elérési** útja – írja be a következőt: `CertificateThumbprint` .

14. Kattintson **a SERVICEPRINCIPAL** elemre, majd a Paraméter értéke lapon válassza a **ConstantValue** értéket az **Adatforrás mezőben.** Kattintson a True **(Igaz)** lehetőségre, majd az **OK gombra.**

15. Kattintson **a TENANTID** elemre, és a Paraméter értéke lapon tegye a következőket. Ha végzett, kattintson kétszer az **OK** gombra.

    * **Adatforrás –** válassza a **Tevékenység kimenete lehetőséget.**
    * Adatforráslista – válassza az **Automation-kapcsolat bejőt.**
    * **Mező elérési** útja – írja be a következőt: `TenantId` .

16. A Könyvtár vezérlőbe írja be a `Set-AzContext` keresőmezőt.

17. Adja `Set-AzContext` hozzá a gombra a vásznon.

18. Válassza `Set-AzContext` a lehetőséget a vásznon. A Konfiguráció vezérlőpanelen a Címke mezőbe írja be `Specify Subscription Id` **a következőt:** .

19. Kattintson **a Paraméterek elemre,** és megjelenik a Tevékenységparaméter konfigurációja lap.

20. A parancsmag több paraméterkészlettel rendelkezik, és a paraméterértékek megadása előtt ki kell `Set-AzContext` választania egyet. Kattintson a **Paraméterkészlet elemre,** majd válassza a **SubscriptionId lehetőséget.**

21. A paraméterkészlet paraméterei a Tevékenységparaméter-konfiguráció lapon jelennek meg. Kattintson a **SubscriptionID elemre.**

22. A Paraméter értéke lapon válassza a **Változó** adattár lehetőséget az **Adatforrás** mezőben, majd a forráslistából válassza az **AzureSubscriptionId** lehetőséget. Ha végzett, kattintson kétszer **az OK** gombra.

23. Vigye az egérmutatót addig, amíg meg nem jelenik egy kör az `Login to Azure` alakzat alján. Kattintson a körre, és húzza a nyilat a `Specify Subscription Id` következőre: . Ezen a ponton a runbooknak az alábbihoz kell hasonlíta.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Képernyőkép a runbookról, miután a nyilat az &quot;Előfizetés azonosítójának megadása&quot; elemhez húzta.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7. lépés – Virtuális gépet elindító tevékenység hozzáadása

Most hozzá kell adni egy tevékenységet a `Start-AzVM` virtuális gép indításhoz. Bármelyik virtuális gépet választhatja az Azure-előfizetésében, és most a nevét a [Start-AzVM](/powershell/module/az.compute/start-azvm) parancsmagba kódoltuk.

1. A Könyvtár vezérlőbe írja be a `Start-Az` keresőmezőt.

2. Adja `Start-AzVM` hozzá a et a vászonhoz, majd kattintson rá, és húzza `Specify Subscription Id` alá.

3. Vigye az egérmutatót addig, amíg meg nem jelenik egy kör az `Specify Subscription Id` alakzat alján. Kattintson a körre, és húzza a nyilat a `Start-AzVM` következőre: .

4. Válassza a(z) `Start-AzVM` lehetőséget. Kattintson **a Paraméterek,** majd a **Paraméterkészlet** elemre a tevékenység készletének megtekintéséhez.

5. Válassza **a ResourceGroupNameParameterSetName paraméterkészletet** a paraméterkészlethez. A **ResourceGroupName és** **a Name** mezők mellett felkiáltójel jelzi, hogy kötelező paraméterek. Vegye figyelembe, hogy mindkét mező sztringértékeket vár.

6. Válassza ki a **Name** paramétert. Az Adatforrás mezőben válassza a **PowerShell-kifejezés** lehetőséget.  A runbook futtatásához használt virtuális géphez írja be a gép nevét idézőjelek között. Kattintson az **OK** gombra.

7. Válassza a **ResourceGroupName** elemet. Használja a **PowerShell-kifejezés** értéket az **Adatforrás** mezőben, és írja be az erőforráscsoport nevét idézőjelek között. Kattintson az **OK** gombra.

8. Kattintson **a Teszt panelre,** hogy tesztelni tudja a runbookot.

9. A **teszt megkezdéséhez** kattintson az Indítás gombra. Ha befejeződött, győződjön meg arról, hogy a virtuális gép elindult. A runbooknak ezen a ponton az alábbihoz hasonlónak kell lennie.

    ![Runbook Start-AzVM kimenete](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8. lépés – További bemeneti paraméterek hozzáadása

A runbook jelenleg elindítja a parancsmaghoz megadott erőforráscsoportban lévő `Start-AzVM` virtuális gépet. A runbook akkor lesz hasznosabb, ha a runbook elindításakor a nevet és az erőforráscsoportot is megadja. Adjunk bemeneti paramétereket a runbookhoz, hogy biztosítsuk ezt a funkciót.

1. Nyissa meg a  grafikus szerkesztőt az oldal Szerkesztés MyFirstRunbook-Graphical kattintva.

2. Válassza **a Bemenet és kimenet,** majd a Bemenet **hozzáadása** lehetőséget a Runbook bemeneti paramétere panel megnyitásához.

3. A megadott mezőkben tegye a következő beállításokat, majd kattintson az **OK gombra.**
   * **Név –** adja meg a következőt: `VMName` .
   * **Írja be a** következőt: – tartsa meg a sztringbeállítást.
   * **Kötelező –** módosítsa az értéket Igen **értékre.**

4. Hozzon létre egy második kötelező bemeneti paramétert néven, majd kattintson az `ResourceGroupName` **OK gombra** a Bemenet és kimenet panel bezárása érdekében.

    ![Forgatókönyv bemeneti paraméterei](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Válassza ki `Start-AzVM` a tevékenységet, majd kattintson a **Paraméterek elemre.**

6. Módosítsa a **Name (Név)** **Adatforrás mezőjét** **Runbook input (Runbook-bemenet) névre.** Ezután válassza a **VMName lehetőséget.**

7. Módosítsa **a** **ResourceGroupName** Adatforrás mezőjét **Runbook-bemenetre,** majd válassza a **ResourceGroupName lehetőséget.**

    ![Start-AzVM paraméterek](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.

9. Zárja be a Teszt panelt.

10. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.

11. Állítsa le a korábban elindított virtuális gépet.

12. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be az elindított virtuális gép és `VMName` `ResourceGroupName` értékeit.

13. Amikor a runbook befejeződik, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-9---create-a-conditional-link"></a>9. lépés – Feltételes hivatkozás létrehozása

Most már módosíthatja a runbookot, hogy csak akkor próbálja meg elindítani a virtuális gépet, ha még nem indult el. Ehhez adja hozzá a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) parancsmagot, amely lekéri a virtuális gép példányszintű állapotát. Ezután hozzáadhat egy nevű PowerShell-munkafolyamati kódmodult egy PowerShell-kódrészlet használatával annak megállapításához, hogy a virtuális gép állapota fut vagy `Get Status` le van állítva. A modulból származó feltételes hivatkozás `Get Status` csak akkor `Start-AzVM` fut, ha az aktuális futó állapot le van állítva. Az eljárás végén a runbook a parancsmagot használja a kimenetre, amely tájékoztatja, hogy a virtuális gép `Write-Output` sikeresen elindult-e.

1. Nyissa **meg a MyFirstRunbook-Graphical** et a grafikus szerkesztőben.

2. Távolítsa el a és a közötti `Specify Subscription Id` `Start-AzVM` hivatkozást. Kattintson rá, majd nyomja le a **Delete (Törlés) gombot.**

3. A Könyvtár vezérlőbe írja be a `Get-Az` keresőmezőt.

4. Adja `Get-AzVM` hozzá a gombra a vásznon.

5. Válassza `Get-AzVM` ki, majd kattintson **a Paraméterkészlet elemre** a parancsmag készletének megtekintéséhez.

6. Válassza ki a **GetVirtualMachineInResourceGroupNameParamSet** paraméterkészletet. A **ResourceGroupName és** **a Name** mezők mellett felkiáltójel jelzi, hogy kötelező paramétereket határoznak meg. Vegye figyelembe, hogy mindkét mező sztringértékeket vár.

7. A **Name (Név) adatforrása** **területen** válassza a **Runbook input (Forgatókönyv bemenete)** lehetőséget, majd **a VMName lehetőséget.** Kattintson az **OK** gombra.

8. A **ResourceGroupName** **adatforrása** alatt válassza a **Runbook input**, majd a **ResourceGroupName lehetőséget.** Kattintson az **OK** gombra.

9. Az Állapot **adatforrása alatt** válassza a **Konstans érték,** majd az **Igaz lehetőséget.**  Kattintson az **OK** gombra.

10. Hozzon létre egy hivatkozást a és `Specify Subscription Id` a `Get-AzVM` között.

11. A Könyvtár vezérlőben bontsa ki a **Runbook-vezérlő bontsa** ki, és adja **hozzá a vászonhoz** a kódot.  

12. Hozzon létre egy hivatkozást a és `Get-AzVM` a `Code` között.  

13. Kattintson a elemre, majd a Konfiguráció panelen módosítsa a címkét `Code` **állapotra.**

14. Válassza `Code` ki a kódot, és megjelenik a Kódszerkesztő lap.  

15. Illessze be az alábbi kódrészletet a szerkesztőoldalra.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Hozzon létre egy hivatkozást a és `Get Status` a `Start-AzVM` között.

    ![Forgatókönyv kódmodullal](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Válassza ki a hivatkozást, és a Konfiguráció panelen módosítsa **a Feltétel alkalmazása** beállítást **Igenre.** Vegye figyelembe, hogy a hivatkozás szaggatott vonalként jelenik meg, ami azt jelzi, hogy a céltevékenység csak akkor fut le, ha a feltétel igazra oldódik fel.  

18. A **Feltételkifejezéshez írja be** a következőt: `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` most csak akkor fut, ha a virtuális gép le van állítva.

19. A Könyvtár vezérlőben bontsa ki a **Parancsmagok** elemet, és válassza a **Microsoft.PowerShell.Utility** lehetőséget.

20. Adja `Write-Output` hozzá kétszer a hozzáadása a vászonhoz.

21. Az első `Write-Output` vezérlőnél kattintson a Parameters (Paraméterek) **elemre,** és módosítsa a **Label (Címke)** értékét **a következőre: Notify VM Started (Virtuális gép értesítése elindítva).**

22. Az **InputObject esetén módosítsa** az **Adatforrást** a **PowerShell-kifejezésre,** és írja be a `$VMName successfully started.` kifejezést.

23. A második vezérlőn kattintson a Parameters (Paraméterek) elemre, és módosítsa a Label (Címke) értékét `Write-Output` **a Következőre: Notify VM Start Failed (A virtuális gép indításának értesítése sikertelen).**  

24. Az **InputObject esetén módosítsa** az **Adatforrást** a **PowerShell-kifejezésre,** és írja be a `$VMName could not start` kifejezést.

25. A és a `Start-AzVM` `Notify VM Started` hivatkozásaiból hozzon létre `Notify VM Start Failed` hivatkozásokat.

26. Válassza ki a hivatkozást, `Notify VM Started` és módosítsa a Feltétel alkalmazása **lehetőséget** true (igaz) állapotra.

27. A Feltétel **kifejezéshez írja be a** következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Ez `Write-Output` a vezérlő most csak akkor fut, ha a virtuális gép sikeresen elindul.

28. Válassza ki a hivatkozást, `Notify VM Start Failed` és módosítsa a Feltétel alkalmazása **beállítását** true (igaz) állapotra.

29. A **Feltételkifejezés mezőbe írja** be a következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Ez a vezérlő most csak akkor fut, ha a virtuális `Write-Output` gép nem indult el sikeresen. A runbooknak az alábbi rendszerképhez kell hasonlíta.

    ![Forgatókönyv Write-Output parancsmaggal](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.

31. Indítsa el a runbookot úgy, hogy a virtuális gép le van állítva, és a gépnek el kell indulni.

## <a name="next-steps"></a>Következő lépések

* A grafikus formában való írásról további információt [a Grafikus runbook írása a](../automation-graphical-authoring-intro.md)Azure Automation.
* A PowerShell-runbookok első lépésekhez [lásd: PowerShell-runbook létrehozása.](automation-tutorial-runbook-textual-powershell.md)
* A PowerShell munkafolyamat-forgatókönyvekkel való első lépésekért [lásd: PowerShell-munkafolyamat-runbook létrehozása.](automation-tutorial-runbook-textual.md)
* A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
