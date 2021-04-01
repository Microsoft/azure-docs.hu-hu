---
title: Az StorSimple-eszközök kezelése Azure Automation Runbook
description: Ismerje meg, hogyan kezelheti Azure Automation Runbook a StorSimple 8000 Series-eszköz Azure Portal-ban való kezeléséhez.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021078"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Az StorSimple-eszközök kezelése Azure Automation runbookok

Ez a cikk azt ismerteti, hogyan használhatók Azure Automation runbookok a StorSimple 8000 Series-eszköz Azure Portal-ban való kezeléséhez. Egy minta runbook a környezet konfigurálásának lépésein a runbook végrehajtásához.


## <a name="configure-add-and-run-azure-runbook"></a>Azure-runbook konfigurálása, hozzáadása és futtatása

Ez a szakasz egy Windows PowerShell-parancsfájlt tartalmaz a StorSimple, és részletezi a szkript runbook történő importálásához szükséges lépéseket, majd közzéteszi és végrehajtja a runbook.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

* a StorSimple Eszközkezelő szolgáltatáshoz társított aktív Azure-előfizetés StorSimple 8000 sorozatú eszközzel van regisztrálva.

* A Windows PowerShell 5,0 telepítve van a számítógépen (vagy a Windows Server-gazdagépen a StorSimple, ha az egyiket használja).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Automation runbook-modul létrehozása a Windows PowerShellben

Az StorSimple 8000 sorozatú eszközkezelés automatizálási moduljának létrehozásához hajtsa végre a következő lépéseket:

1. Indítsa el a Windows PowerShellt. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappába.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Töltse le az NUGET CLI](https://www.nuget.org/downloads) -t az előző lépésben létrehozott mappában. A _nuget.exe_ különböző verziói vannak. Válassza ki az SDK-nak megfelelő verziót. Minden letöltési hivatkozás közvetlenül egy _. exe_ -fájlra mutat. Győződjön meg arról, hogy a jobb gombbal rákattint, és mentse a fájlt a számítógépre ahelyett, hogy a böngészőből futtatná.

    A következő parancs futtatásával is letöltheti és tárolhatja a parancsfájlt ugyanabban a mappában, amelyet korábban hozott létre.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Töltse le a függő SDK-t.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Töltse le a szkriptet a minta GitHub-projektből.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Hozzon létre egy Azure Automation Runbook modult a StorSimple 8000 sorozatú eszközök felügyeletéhez. A Windows PowerShell ablakban írja be a következő parancsokat:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Győződjön meg arról, hogy az Automation-modul ZIP-fájlja létrejön a alkalmazásban `C:\scripts\StorSimpleSDKTools` .

    ![A képernyőképen a StorSimpleSDKTools mappa látható az Automation modul ZIP-fájljával.](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A következő kimenet jelenik meg, amikor az Automation-modul a Windows PowerShell használatával jön létre.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Automatizálási runbook importálása, közzététele és futtatása

1. Hozzon létre egy Azure-beli futtató Automation-fiókot a Azure Portal. Ehhez lépjen az **Azure marketplace > minden** elemre, és keressen rá az **automatizálás** kifejezésre. Válassza az **Automation-fiókok** lehetőséget.

    ![Keresés – automatizálás](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Az **Automation-fiók hozzáadása** panelen:

   1. Adja meg az Automation-fiók **nevét** .
   2. Válassza ki a StorSimple Eszközkezelő szolgáltatáshoz társított **előfizetést** .
   3. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot.
   4. Válasszon egy **helyet** (ha lehetséges, ugyanaz, mint ahol a szolgáltatás fut).
   5. Hagyja bejelölve az alapértelmezett **futtató fiók létrehozása** lehetőséget.
   6. **A rögzítés az irányítópulton is** megadható. Kattintson a **Létrehozás** lehetőségre.

       ![Képernyőfelvétel: az Automation-fiók hozzáadása panel az előfizetéshez tartozó értékekkel, Azure-beli futtató fiók létrehozása és 2. létrehozás](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Az Automation-fiók sikeres létrehozása után értesítést kap. Az Automation-fiókok létrehozásával kapcsolatos további információkért nyissa meg a [futtató fiók létrehozása](../automation/manage-runas-account.md)című témakört.

3. Annak biztosítása érdekében, hogy a létrehozott Automation-fiók hozzáférhessen a StorSimple Eszközkezelő szolgáltatáshoz, hozzá kell rendelnie a megfelelő engedélyeket az Automation-fiókhoz. A StorSimple Eszközkezelő szolgáltatásában nyissa meg a **hozzáférés-vezérlést** . Kattintson a **+ Hozzáadás** elemre, és adja meg Azure Automation fiókjának nevét. **Mentse** a beállításokat.

    ![Add-permissions-Automation-Account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Az újonnan létrehozott fiókban nyissa meg a **megosztott erőforrások > modulokat** , és kattintson a **+ modul hozzáadása** lehetőségre.

5. A **modul hozzáadása** panelen keresse meg a tömörített modul helyét, és válassza ki, majd nyissa meg a modult. Kattintson az **OK** gombra.

    ![Képernyőfelvétel – a modul hozzáadása panel a fájl feltöltése értékkel és az OK gomb](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Nyissa meg a **Process Automation > runbookok, és kattintson a + Runbook hozzáadása elemre**. A **Runbook hozzáadása** panelen kattintson a **meglévő runbook importálása** elemre. Mutasson a **Runbook fájl** Windows PowerShell-fájljára. A runbook típusa automatikusan ki van választva. Adja meg a runbook nevét és leírását (nem kötelező). Kattintson a **Létrehozás** lehetőségre.

    ![modul hozzáadása](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. A rendszer hozzáadja a runbook a runbookok listájához. Válassza ki ezt a runbook, és kattintson rá.

    ![kattintson a-New-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Szerkessze a runbook, és kattintson a **teszt panel** elemre. Adja meg a paramétereket, például a StorSimple nevét Eszközkezelő szolgáltatást, a StorSimple-eszköz nevét és az előfizetést. **Indítsa el** a tesztet. A jelentés a Futtatás befejezésekor jön létre. További információért látogasson el a [runbook tesztelésének módjára](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Képernyőfelvétel a canenter és a tesztek elindítására szolgáló tesztelési serpenyőről](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Ellenőrizze a kimenetet a runbook a teszt ablaktáblán. Ha teljesül, zárjuk be a panelt. Kattintson a **Közzététel** gombra, és amikor a rendszer megkéri a runbook megerősítésére, megerősítésére és közzétételére.

    ![A képernyőképen a PowerShell-Runbook szerkesztése panel jelenik meg, amely felszólítja, hogy folytassa a Runbook közzétételével](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Következő lépések

[A StorSimple-eszköz kezeléséhez használja a StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md).