---
title: Azure Automation használata feladatok elindításához StorSimple Data Manager
description: Megtudhatja, hogyan használhatja a Azure Automationt StorSimple Data Manager feladatok elindításához
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3d0103857b6f759560129dbe8e693ec6c0d7291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961209"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Az Azure Automation használata feladat indításához

Ez a cikk azt ismerteti, hogyan használható a StorSimple Data Manager szolgáltatás Adatátalakítási funkciója az StorSimple átalakításához. Az Adatátalakítási feladatok kétféleképpen indíthatók el: 

 - A .NET SDK használata
 - Azure Automation runbook használata
 
Ez a cikk részletesen ismerteti, hogyan hozhat létre Azure Automation runbook, majd hogyan kezdeményezheti az Adatátalakítási feladatokat. Ha többet szeretne megtudni arról, hogyan indíthatja el az adatátalakítást a .NET SDK-n keresztül, ugorjon a [.net SDK használata Adatátalakítási feladatok](storsimple-data-manager-dotnet-jobs.md)elindításához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

*   Azure PowerShell telepítve van az ügyfélszámítógépen. [Azure PowerShell letöltése](/powershell/azure/azurerm/install-azurerm-ps).
*   Egy adott erőforráscsoport egy StorSimple Data Manager szolgáltatásának megfelelően konfigurált feladatdefiníció.
*   Töltse le  [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) a fájlt a GitHub-adattárból. 
*   [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)Parancsfájl letöltése a GitHub-adattárból.

## <a name="step-by-step-procedure"></a>Lépésről lépésre haladó eljárás

### <a name="set-up-the-automation-account"></a>Az Automation-fiók beállítása

1. Hozzon létre egy Azure-beli futtató Automation-fiókot a Azure Portal. Ehhez lépjen az **Azure marketplace > minden** elemre, és keressen rá az **automatizálás** kifejezésre. Válassza az **Automation-fiókok** lehetőséget.

    ![Futtató Automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Új Automation-fiók hozzáadásához kattintson a **+ Hozzáadás** gombra.

    ![Futtató Automation-fiók létrehozása 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Az **Automation hozzáadása**:

   1. Adja meg az Automation-fiók **nevét** .
   2. Válassza ki az StorSimple Data Manager szolgáltatáshoz társított **előfizetést** .
   3. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot.
   4. Válasszon egy **helyet**.
   5. Hagyja bejelölve az alapértelmezett **futtató fiók létrehozása** lehetőséget.
   6. Az irányítópulton való gyors hozzáférésre mutató hivatkozás beszerzéséhez tekintse meg a **rögzítés az irányítópulton** lehetőséget. Kattintson a **Létrehozás** lehetőségre.

      ![3. futtató Automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Az Automation-fiók sikeres létrehozása után értesítést kap.
    
      ![Az Automation-fiók üzembe helyezésére vonatkozó értesítés](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      További információért nyissa meg a [futtató fiók létrehozása](../automation/manage-runas-account.md)című témakört.

3. Az újonnan létrehozott fiókban nyissa meg a **megosztott erőforrások > modulokat** , és kattintson a **+ modul hozzáadása** lehetőségre.

    ![1. modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Tallózással keresse meg a `DataTransformationApp.zip` fájl helyét a helyi számítógépen, és válassza ki és nyissa meg a modult. A modul importálásához kattintson **az OK** gombra.

    ![2. modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Amikor Azure Automation importál egy modult a fiókjába, kibontja a modulhoz tartozó metaadatokat. A művelet eltarthat néhány percig.

   ![4. modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Értesítést kap arról, hogy a modul üzembe helyezése folyamatban van, és egy másik értesítés a folyamat befejezésekor.  A **modulok** állapota **elérhető** értékre változik.

    ![5. modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Automatizálási runbook importálása, közzététele és futtatása

Az alábbi lépések végrehajtásával importálhatja, közzéteheti és futtathatja a runbook a feladatdefiníció elindításához.

1. Az Azure Portalon nyissa meg az Automation-fiókját. Nyissa meg a **Process Automation > runbookok** , és kattintson **a + runbook hozzáadása** elemre.

    ![1. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. A **Runbook hozzáadása** területen kattintson a **meglévő runbook importálása** elemre.

3. Mutasson a `Trigger-DataTransformation-Job.ps1` **Runbook fájl** Azure PowerShell parancsfájlra. A runbook típusa automatikusan ki van választva. Adja meg a runbook nevét és leírását (nem kötelező). Kattintson a **Létrehozás** lehetőségre.

    ![2. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Az új runbook megjelenik az Automation-fiók runbookok listájában. Válassza ki ezt a runbook, és kattintson rá.

    ![3. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Szerkessze a runbook, és kattintson a **teszt** panel elemre.

    ![4. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Adja meg a paramétereket, például a StorSimple Data Manager szolgáltatás nevét, a társított erőforráscsoportot és a feladatdefiníció nevét. **Indítsa el** a tesztet. A jelentés a Futtatás befejezésekor jön létre. További információért látogasson el a [runbook tesztelésének](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook)módjára.

    ![Runbook hozzáadása 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Ellenőrizze a kimenetet a runbook a teszt ablaktáblán. Ha teljesül, zárjuk be a panelt. Kattintson a **Közzététel** gombra, és amikor a rendszer megkéri a runbook megerősítésére, megerősítésére és közzétételére.

    ![6. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Lépjen vissza a **runbookok** elemre, és válassza ki az újonnan létrehozott runbook.

    ![7. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Indítsa el** a runbook. A **Start runbook** mezőben adja meg az összes paramétert. Az Adatátalakítási feladatok elküldéséhez és elindításához kattintson **az OK** gombra.

10. A Azure Portalben a feladat előrehaladásának figyeléséhez lépjen a **feladatok** elemre a StorSimple Data Manager szolgáltatásban. Válassza ki, majd kattintson a feladatra a feladatok részleteinek megtekintéséhez.

    ![10. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Következő lépések

[Az adatátalakításhoz használja StorSimple Data Manager felhasználói felületét](storsimple-data-manager-ui.md).