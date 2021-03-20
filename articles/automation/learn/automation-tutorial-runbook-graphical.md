---
title: Grafikus runbook létrehozása Azure Automationban
description: Ez a cikk a Azure Automation egyszerű grafikus runbook létrehozását, tesztelését és közzétételét ismerteti.
keywords: runbook, runbook-sablon, runbook automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92899806"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Oktatóanyag: grafikus runbook létrehozása

Egy Azure Automation [grafikus forgatókönyv](../automation-runbook-types.md#graphical-runbooks) létrehozását bemutató oktatóanyag. Grafikus és grafikus PowerShell-munkafolyamati runbookok hozhat létre és szerkeszthet a Azure Portal grafikus szerkesztőjének használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyszerű grafikus runbook létrehozása
> * A runbook tesztelése és közzététele
> * A runbook-feladatokhoz tartozó állapot futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép elindításához runbook paraméterekkel és feltételes hivatkozásokkal

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free).
* [Automation-fiók](../index.yml) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása óta nem lehet üzemi virtuális gép.
* Szükség esetén [importálja az Azure-modulokat](../shared-resources/modules.md) vagy a [frissítési modulokat](../automation-update-azure-modules.md) a használt parancsmagok alapján.

## <a name="step-1---create-runbook"></a>1. lépés – Runbook létrehozása

Először hozzon létre egy egyszerű runbook, amely kiírja a szöveget `Hello World` .

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezeknek az eszközöknek a túlnyomó része automatikusan egy új Automation-fiókba kerül. Az előfizetéshez társított hitelesítőadat-eszközt is meg kell adni.

2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

3. Hozzon létre egy új runbook a **Runbook létrehozása** lehetőség kiválasztásával.

4. Adja a forgatókönyvnek a **MyFirstRunbook-Graphical** nevet.

5. Ebben az esetben egy [grafikus runbook](../automation-graphical-authoring-intro.md)fog létrehozni. A **Runbook típushoz** válassza a **grafikus** lehetőséget.

    ![Új forgatókönyv](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. A forgatókönyv létrehozásához és a grafikus szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-activities"></a>2. lépés – tevékenységek hozzáadása

A szerkesztő bal oldalán levő Könyvtárvezérlés segítségével kiválaszthatja a forgatókönyvhöz hozzáadni kívánt tevékenységeket. Egy `Write-Output` parancsmagot fog hozzáadni a runbook lévő szöveg kimenetéhez.

1. A könyvtár vezérlőelemben kattintson a Keresés mezőre, és írja be a következőt: `write-output` . A keresési eredmények az alábbi képen láthatók.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Görgessen le a lista aljához. Kattintson a jobb gombbal a **Write-output** elemre, és válassza **a Hozzáadás a vászonhoz** lehetőséget. Azt is megteheti, hogy rákattint a parancsmag neve melletti három pontra (...), majd kiválasztja a **Hozzáadás a vászonhoz** lehetőséget.

3. A vásznon kattintson a **Write-Output** tevékenységre. Ez a művelet megnyitja a konfigurációs vezérlő lapot, amely lehetővé teszi a tevékenység konfigurálását.

4. A **label (címke** ) mező alapértelmezett értéke a parancsmag neve, de megváltoztathatja valami barátságosabbra. Módosítsa a következőre: `Write Hello World to output`.

5. A **Paraméterek** elemre kattintva megadhatja a parancsmag paramétereinek értékét.

   Néhány parancsmagnak több paramétere is van, és ki kell választania, hogy melyiket szeretné használni. Ebben az esetben `Write-Output` csak egy paraméter van beállítva.

6. Válassza ki a `InputObject` paramétert. Ez az a paraméter, amelyet a kimeneti adatfolyamnak küldendő szöveg megadására használ.

7. Az **adatforrás** legördülő menüje olyan forrásokat biztosít, amelyek segítségével feltöltheti a paraméter értékét. Ebben a menüben válassza a **PowerShell-kifejezés** lehetőséget.

   Az ilyen forrásokból származó kimeneteket egy másik tevékenységként, egy Automation-eszközként vagy egy PowerShell-kifejezésként is használhatja. Ebben az esetben a kimenet csak `Hello World` . Megadhat egy sztringet egy PowerShell-kifejezéssel is.

8. A **kifejezés** mezőbe írja be a kifejezést, `"Hello World"` majd kattintson kétszer az **OK gombra** a vászonra való visszatéréshez.

9. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbook, hogy az éles környezetben elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megjelenítését.

1. A teszt panel megnyitásához válassza a **teszt panelt** .

2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.

3. Vegye figyelembe, hogy létrejön egy [runbook-feladatok](../automation-runbook-execution.md) , és az állapota megjelenik a panelen.

   A feladatok állapota a következőképpen kezdődik `Queued` , ami azt jelzi, hogy a runbook-feldolgozó a felhőben elérhetővé válására vár. Az állapot akkor változik, `Starting` Ha egy feldolgozó a feladatot állítja be. Végül az állapot akkor válik aktívvá, `Running` Amikor a runbook ténylegesen elindul.

4. Ha a runbook-feladatok befejeződik, a teszt ablaktábla megjeleníti a kimenetet. Ebben az esetben a következőt látja: `Hello World` .

    ![Hello World runbook kimenete](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. Válassza a **Közzététel** lehetőséget a runbook közzétételéhez, majd az **Igen** gombra, amikor a rendszer kéri.

2. Görgessen balra a runbook megtekintéséhez a Runbookok lapon, és vegye figyelembe, hogy a **szerzői állapot** értéke **közzétételre** van állítva.

3. Görgessen vissza jobbra a **MyFirstRunbook-grafikus** oldal megtekintéséhez.

   A felső beállítások lehetővé teszik a runbook elindítását, a jövőbeli kezdési idő beírását, vagy [webhook](../automation-webhooks.md) létrehozását, hogy a runbook http-hívással is elindítható legyen.

4. Kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer felszólítja a runbook elindítására.

5. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Ellenőrizze, hogy a feladatok állapota mező **kész** **állapotú** -e.

6. Kattintson a **kimenet** gombra a kimenet lap megnyitásához, ahol megtekintheti a `Hello World` megjelenített adatokat.

7. A kimeneti oldal bezárásához.

8. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak `Hello World` a kimeneti adatfolyamban látható.

    Vegye figyelembe, hogy a streamek panel más streameket jeleníthet meg egy runbook-feladatokhoz, például a részletes és a hiba-adatfolyamokhoz, ha a runbook ezeket írja.

9. A MyFirstRunbook-Graphical lapra való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.

10. A runbook összes feladatának megtekintéséhez válassza az **erőforrások** alatt lévő **feladatok** lehetőséget. A feladatok lap felsorolja a runbook által létrehozott összes feladatot. Csak egy feladattípus jelenik meg, mert csak egyszer futtatta a feladatot.

11. Kattintson a feladatokra, hogy megnyissa a runbook elindításakor megtekintett feladatok ablaktábláját. Ezen ablaktábla használatával megtekintheti a runbook létrehozott feladatok részleteit.

## <a name="step-5---create-variable-assets"></a>5. lépés: Változó adategységek létrehozása

Megvizsgálta és közzétette a runbook, de eddig nem csinál semmi hasznosat az Azure-erőforrások kezeléséhez. Mielőtt konfigurálja a runbook a hitelesítéshez, létre kell hoznia egy változót az előfizetés-azonosító tárolásához, egy tevékenységet kell beállítania a hitelesítéshez, majd hivatkoznia kell a változóra. Az előfizetési környezetre mutató hivatkozással könnyedén dolgozhat több előfizetéssel is.

1. Másolja az előfizetés-azonosítót a navigációs ablaktábla **előfizetések** lehetőségével.

2. Az Automation-fiókok lapon válassza a **változók** elemet a **megosztott erőforrások** területen.

3. Válassza **a változó hozzáadása** lehetőséget.

4. Az új változó lapon végezze el a következő beállításokat a megadott mezőkben.

    * **Név** – ENTER `AzureSubscriptionId` .
    * **Érték** – adja meg az előfizetés-azonosítóját.
    * **Típus** – hagyja kiválasztva a karakterláncot.
    * **Titkosítás** – az alapértelmezett értéket használja.

5. A változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="step-6---add-authentication"></a>6. lépés – hitelesítés hozzáadása

Most, hogy már rendelkezik egy változóval az előfizetés-azonosító tárolásához, beállíthatja, hogy a runbook az előfizetés futtató hitelesítő adataival hitelesítse magát. Ezt úgy teheti meg, hogy hozzáad egy eszközként az Azure-beli futtató összekötőt. Emellett hozzá kell adnia a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmagot és a [set-AzContext](/powershell/module/az.accounts/Set-AzContext) parancsmagot a vászonhoz.

>[!NOTE]
>A PowerShell-runbookok esetében a `Add-AzAccount` és a `Add-AzureRMAccount` álneve a következőhöz: `Connect-AzAccount` . Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok. A grafikus runbook csak `Connect-AzAccount` saját maguk használhatják.

1. Navigáljon a runbook, és válassza a **Szerkesztés** lehetőséget a MyFirstRunbook-Graphical oldalon.

2. Nincs szükség a `Write Hello World to output` bejegyzésre. Csak kattintson a három pontra, majd válassza a **Törlés** lehetőséget.

3. A könyvtár vezérlőben bontsa ki az **eszközök**, majd a **kapcsolatok** elemet. Adja hozzá `AzureRunAsConnection` a vászonhoz a **Hozzáadás a vászonhoz** lehetőség kiválasztásával.

4. Nevezze át a következőre: `AzureRunAsConnection` `Get Run As Connection` .

5. A könyvtár vezérlőben írja be `Connect-AzAccount` a kifejezést a Keresés mezőbe.

6. Hozzáadás `Connect-AzAccount` a vászonhoz.

7. Vigye az egérmutatót `Get Run As Connection` addig, amíg az alakzat alján egy kör nem jelenik meg. Kattintson a körre, és húzza a nyilat `Connect-AzAccount` egy hivatkozás megalkotása céljából. A runbook a `Get Run As Connection` és a futtatásával kezdődik `Connect-AzAccount` .

    ![Hivatkozás létrehozása a tevékenységek között](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. A vásznon válassza a elemet `Connect-AzAccount` . A konfigurálási vezérlő ablaktáblán írja be a **login az Azure** -ba a **label (címke** ) mezőbe.

9. Kattintson a **Paraméterek** elemre, és megjelenik a tevékenység paraméterének konfigurációja lap.

10. A `Connect-AzAccount` parancsmagnak több paramétere is van, és a paraméterek értékeinek megadása előtt ki kell választania egyet. Kattintson a **paraméter beállítása** elemre, majd válassza a **ServicePrincipalCertificateWithSubscriptionId** lehetőséget.

11. A paraméterhez tartozó paraméterek a tevékenység paraméterének konfiguráció lapján jelennek meg. Kattintson az **APPLICATIONID** elemre.

    ![Azure-fiók paramétereinek hozzáadása](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. A paraméter értéke lapon végezze el a következő beállításokat, majd kattintson az **OK** gombra.

   * **Adatforrás** – válassza a **tevékenység kimenete** elemet.
   * Adatforrás-lista – válassza az **Automation-kapcsolatok beolvasása** lehetőséget.
   * **Mező elérési útja** – típus `ApplicationId` . A mező elérési útjához tartozó tulajdonság nevét adja meg, mert a tevékenység több tulajdonsággal rendelkező objektumot ad eredményül.

13. Kattintson a **CERTIFICATETHUMBPRINT** elemre, majd a paraméter értéke lapon végezze el a következő beállításokat, majd kattintson az **OK** gombra.

    * **Adatforrás** – válassza a **tevékenység kimenete** elemet.
    * Adatforrás-lista – válassza az **Automation-kapcsolatok beolvasása** lehetőséget.
    * **Mező elérési útja** – típus `CertificateThumbprint` .

14. Kattintson a **SERVICEPRINCIPAL** elemre, majd a paraméter értéke lapon válassza a **ConstantValue** lehetőséget az **adatforrás** mezőhöz. Kattintson a **true (igaz**) lehetőségre, majd az **OK** gombra.

15. Kattintson a **TENANTID** elemre, és végezze el a következő beállításokat a paraméter értéke lapon. Ha elkészült, kattintson kétszer **az OK gombra** .

    * **Adatforrás** – válassza a **tevékenység kimenete** elemet.
    * Adatforrás-lista – válassza az **Automation-kapcsolatok beolvasása** lehetőséget.
    * **Mező elérési útja** – típus `TenantId` .

16. A könyvtár vezérlőben írja be `Set-AzContext` a kifejezést a Keresés mezőbe.

17. Hozzáadás `Set-AzContext` a vászonhoz.

18. Válassza ki a `Set-AzContext` vásznon. A konfigurálási vezérlő ablaktáblán adja meg a `Specify Subscription Id` **label (címke** ) mezőt.

19. Kattintson a **Paraméterek** elemre, és megjelenik a tevékenység paraméterének konfigurációja lap.

20. A `Set-AzContext` parancsmagnak több paramétere is van, és a paraméterek értékeinek megadása előtt ki kell választania egyet. Kattintson a **paraméter beállítása** elemre, majd válassza a **SubscriptionId** lehetőséget.

21. A paraméterhez tartozó paraméterek a tevékenység paraméterének konfiguráció lapján jelennek meg. Kattintson a **SubscriptionID** elemre.

22. A paraméter értéke lapon válassza a **változó eszköz** lehetőséget az **adatforrás** mezőben, majd válassza a **AzureSubscriptionId** lehetőséget a forrás listából. Ha elkészült, kattintson kétszer **az OK gombra** .

23. Vigye az egérmutatót `Login to Azure` addig, amíg az alakzat alján egy kör nem jelenik meg. Kattintson a körre, és húzza a nyilat a következőre: `Specify Subscription Id` . A runbook a következőhöz hasonlóan kell kinéznie.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Képernyőkép a runbook a nyílra húzva az &quot;előfizetés-azonosító meghatározása&quot; gombra.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7. lépés – Virtuális gépet elindító tevékenység hozzáadása

Most hozzá kell adnia egy `Start-AzVM` tevékenységet a virtuális gép elindításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már rögzítjük a nevét a [Start-AzVM](/powershell/module/az.compute/start-azvm) parancsmagba.

1. A könyvtár vezérlőben írja be `Start-Az` a kifejezést a Keresés mezőbe.

2. Adja hozzá `Start-AzVM` a vászonhoz az elemet, majd kattintson rá, és húzza azt alá `Specify Subscription Id` .

3. Vigye az egérmutatót `Specify Subscription Id` addig, amíg az alakzat alján egy kör nem jelenik meg. Kattintson a körre, és húzza a nyilat a következőre: `Start-AzVM` .

4. Válassza a(z) `Start-AzVM` lehetőséget. Kattintson a **Parameters (paraméterek** ), majd a **paraméter beállítása** elemre a tevékenység készletének megtekintéséhez.

5. Válassza a **ResourceGroupNameParameterSetName** lehetőséget. A **ResourceGroupName** és a **Name (név** ) mezők mellett felkiáltójelek is szerepelnek, így jelezve, hogy kötelező paraméterek. Vegye figyelembe, hogy mindkét mező sztring értékeket vár.

6. Válassza ki a **Name** paramétert. Válassza a **PowerShell-kifejezés** lehetőséget az **adatforrás** mezőhöz. A runbook elindításához használt virtuális gép esetében írja be a nevet idézőjelek közé. Kattintson az **OK** gombra.

7. Válassza a **ResourceGroupName** elemet. Használja az érték **PowerShell-kifejezést** az **adatforrás** mezőhöz, és írja be az erőforráscsoport nevét idézőjelek közé. Kattintson az **OK** gombra.

8. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.

9. A teszt **elindításához** kattintson a Start gombra. A művelet befejezését követően győződjön meg arról, hogy a virtuális gép elindult. A runbook a következőhöz hasonlóan kell kinéznie.

    ![Runbook Start-AzVM kimenet](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8. lépés – további bemeneti paraméterek hozzáadása

A runbook jelenleg a parancsmaghoz megadott erőforráscsoporthoz indítja el a virtuális gépet `Start-AzVM` . A runbook akkor hasznos, ha a runbook indításakor megadja a nevet és az erőforráscsoportot is. Adja hozzá a runbook bemeneti paramétereit a funkció megadásához.

1. A MyFirstRunbook-Graphical lapon a **Szerkesztés** gombra kattintva nyissa meg a grafikus szerkesztőt.

2. Válassza a **bemenet és kimenet** lehetőséget, majd **adja hozzá a bemenet** elemet a Runbook bemeneti paraméter panel megnyitásához.

3. Hajtsa végre a következő beállításokat a megadott mezőkben, majd kattintson az **OK** gombra.
   * **Név** – adja meg a nevet `VMName` .
   * **Típus** – megtartja a karakterlánc-beállítást.
   * **Kötelező** – módosítsa az értéket **Igen** értékre.

4. Hozzon létre egy második kötelező bemeneti paramétert `ResourceGroupName` , majd kattintson az **OK** gombra a bemeneti és a kimeneti ablaktábla bezárásához.

    ![Forgatókönyv bemeneti paraméterei](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Válassza ki a `Start-AzVM` tevékenységet, majd kattintson a **Parameters (paraméterek**) elemre.

6. Módosítsa az **adatforrás** mezőjét a  **Runbook bemenetre**. Ezután válassza a **VMName** lehetőséget.

7. Módosítsa a **ResourceGroupName** **adatforrás** mezőjét a **Runbook bemenetre** , majd válassza a **ResourceGroupName** lehetőséget.

    ![Start-AzVM paraméterek](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.

9. Zárja be a Teszt panelt.

10. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.

11. Állítsa le a korábban elindított virtuális gépet.

12. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be a és a értékeit az `VMName` `ResourceGroupName` elindítani kívánt virtuális géphez.

13. A runbook befejezését követően győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-9---create-a-conditional-link"></a>9. lépés – Feltételes hivatkozás létrehozása

Mostantól módosíthatja a runbook, hogy csak akkor próbálja meg elindítani a virtuális gépet, ha még nem indult el. Ezt úgy teheti meg, hogy hozzáad egy [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) parancsmagot, amely lekéri a virtuális gép példány-szintű állapotát. Ezután hozzáadhat egy PowerShell-kódrészlettel ellátott PowerShell `Get Status` -munkafolyamat-modult annak megállapításához, hogy a virtuális gép állapota fut vagy leállt. A modul feltételes hivatkozása `Get Status` csak akkor fut, `Start-AzVM` Ha az aktuálisan futó állapot le van állítva. Az eljárás végén a runbook a parancsmag használatával küld `Write-Output` egy üzenetet, amely tájékoztatja, hogy a virtuális gép sikeresen elindult-e.

1. Nyissa meg a **MyFirstRunbook** a grafikus szerkesztőben.

2. Távolítsa el a és a közötti kapcsolatot, `Specify Subscription Id` `Start-AzVM` és kattintson rá, majd nyomja le a **delete** billentyűt.

3. A könyvtár vezérlőben írja be `Get-Az` a kifejezést a Keresés mezőbe.

4. Hozzáadás `Get-AzVM` a vászonhoz.

5. Válassza ki `Get-AzVM` , majd kattintson a **paraméterérték** elemre a parancsmag készletének megtekintéséhez.

6. Válassza ki a **GetVirtualMachineInResourceGroupNameParamSet** paraméterkészletet. A **ResourceGroupName** és a **Name** mezőhöz felkiáltójelek tartoznak, ami azt jelzi, hogy a kötelező paramétereket határozzák meg. Vegye figyelembe, hogy mindkét mező sztring értékeket vár.

7. A **név** **adatforrás** területén válassza a **Runbook bemenet**, majd a **VMName** lehetőséget. Kattintson az **OK** gombra.

8. A **ResourceGroupName** **adatforrás** területén válassza a **Runbook bemenet**, majd a **ResourceGroupName** lehetőséget. Kattintson az **OK** gombra.

9. Az **állapot** elemnél az **adatforrás** területen válassza az **állandó érték**, majd az **igaz** értéket. Kattintson az **OK** gombra.

10. Hozzon létre egy hivatkozást a alkalmazásból `Specify Subscription Id` `Get-AzVM` .

11. A könyvtár vezérlőben bontsa ki a **Runbook vezérlő** elemet, és adjon hozzá egy **kódot** a vászonhoz.  

12. Hozzon létre egy hivatkozást a alkalmazásból `Get-AzVM` `Code` .  

13. Kattintson `Code` a és a lehetőségre a konfiguráció ablaktáblán, hogy lekérje a címkét az **állapot lekéréséhez**.

14. Válassza ki `Code` a elemet, és megjelenik a Kódszerkesztő oldal.  

15. Illessze be a következő kódrészletet a szerkesztő lapra.

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

16. Hozzon létre egy hivatkozást a alkalmazásból `Get Status` `Start-AzVM` .

    ![Forgatókönyv kódmodullal](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Válassza ki a hivatkozást, és a konfiguráció panelen módosítsa a **feltétel alkalmazása** **beállítást igen** értékre. Vegye figyelembe, hogy a hivatkozás szaggatott vonalba kerül, ami azt jelzi, hogy a célként megadott tevékenység csak akkor fut, ha a feltétel igaz értékre van feloldva.  

18. A **feltétel kifejezése** mezőbe írja be a következőt: `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` most csak akkor fut, ha a virtuális gép le van állítva.

19. A Könyvtár vezérlőben bontsa ki a **Parancsmagok** elemet, és válassza a **Microsoft.PowerShell.Utility** lehetőséget.

20. Kétszer adja hozzá `Write-Output` a vászonhoz.

21. Az első `Write-Output` vezérlőelem esetében kattintson a **Parameters (paraméterek** ) elemre, és módosítsa a **címke** értékét a **virtuális gép értesítéséhez**.

22. A **inputobject elemnél** módosítsa az **adatforrást** a **PowerShell-kifejezésre**, és írja be a kifejezést a kifejezésbe `$VMName successfully started.` .

23. A második `Write-Output` vezérlőn kattintson a **Paraméterek** elemre, és módosítsa a **címke** értéket a **virtuális gép értesítésének megkezdéséhez**.

24. A **inputobject elemnél** módosítsa az **adatforrást** a **PowerShell-kifejezésre**, és írja be a kifejezést a kifejezésbe `$VMName could not start` .

25. Hivatkozásokat hozhat létre a `Start-AzVM` és a között `Notify VM Started` `Notify VM Start Failed` .

26. Válassza ki a hivatkozást, `Notify VM Started` és módosítsa a **feltétel alkalmazása** igaz értéket.

27. A **feltétel kifejezéséhez** írja be a következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Ez a `Write-Output` vezérlő most csak akkor fut, ha a virtuális gép sikeresen elindul.

28. Válassza ki a hivatkozást, `Notify VM Start Failed` és módosítsa a **feltétel alkalmazása** igaz értéket.

29. A **feltétel kifejezés** mezőjébe írja be a következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Ez a `Write-Output` vezérlő most csak akkor fut, ha a virtuális gép nem indult el sikeresen. A runbook az alábbi képhez hasonlóan kell kinéznie.

    ![Forgatókönyv Write-Output parancsmaggal](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.

31. Indítsa el a runbook a virtuális gép leállításával, és a számítógépnek el kell indulnia.

## <a name="next-steps"></a>Következő lépések

* További információ a grafikus létrehozásról: [grafikus runbook készítése Azure Automationban](../automation-graphical-authoring-intro.md).
* A PowerShell-runbookok megkezdéséhez tekintse meg [a PowerShell-Runbook létrehozása](automation-tutorial-runbook-textual-powershell.md)című témakört.
* A PowerShell-munkafolyamat runbookok megkezdéséhez tekintse meg [a PowerShell-munkafolyamat Runbook létrehozása](automation-tutorial-runbook-textual.md)című témakört.
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
