---
title: Adatelemzési kód tesztelése az Azure DevOps Services szolgáltatással – csoportos adatelemzési folyamat
description: Adatelemzési kód tesztelése az Azure-ban az UCI Adult jövedelem előrejelzési adatkészlettel a csoportos adatelemzési folyamattal és az Azure DevOps Services szolgáltatással
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 66853b580ba1b619bf4cc995e05bd3cfaff07cfb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565869"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Adatelemzési kód tesztelése az Azure-ban a csoportos adatelemzési folyamattal és az Azure DevOps Services szolgáltatással
Ez a cikk az adatelemzési munkafolyamatok kódjának tesztelésére vonatkozó előzetes irányelveket ismerteti. Az ilyen tesztelések lehetővé teszik az adatszakértők számára, hogy szisztematikusan és hatékonyan ellenőrizzék a kód minőségét és várható eredményét. Olyan csoportos adatelemzési folyamat (TDSP) [projektjét használjuk, amely a korábban közzétett UCI Adult bevételi adatkészletet használja](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , hogy megmutassa, hogyan lehet elvégezni a kód tesztelését. 

## <a name="introduction-on-code-testing"></a>Bevezetés a kódok tesztelésére
A "Unit testing" a szoftverfejlesztés régóta bevált gyakorlata. Az adatelemzéshez azonban gyakran nem egyértelmű, hogy mit jelent az "egység tesztelése", és hogyan kell tesztelni az adatelemzési életciklus különböző szakaszaihoz tartozó kódokat, például:

* Adatok előkészítése
* Adatminőségi vizsgálat
* Modellezés
* Modell üzembe helyezése 

Ez a cikk az "egység tesztelése" kifejezést helyettesíti a "Code testing" kifejezéssel. Olyan függvények tesztelésére szolgál, amelyek segítségével megállapítható, hogy az adatelemzési életciklus egy adott lépése esetében a kód a várt módon eredményez-e eredményeket. A tesztet írást végző személy a függvény eredményétől függően meghatározza, hogy mi a várt érték, például az adatminőség-ellenőrzés vagy a modellezés.

Ez a cikk hasznos erőforrásként való hivatkozásokat tartalmaz.

## <a name="azure-devops-for-the-testing-framework"></a>Az Azure DevOps a tesztelési keretrendszerhez
Ez a cikk bemutatja, hogyan végezheti el és automatizálhatja a tesztelést az Azure DevOps használatával. Dönthet úgy is, hogy alternatív eszközöket használ. Azt is bemutatjuk, hogyan állíthat be automatikus buildet az Azure DevOps és a Build Agent használatával. A Build Agents esetében az Azure adatelemzési Virtual Machines (Dsvm) használjuk.

## <a name="flow-of-code-testing"></a>Kód tesztelésének folyamata
Az adatelemzési projektekben a kód tesztelésének általános munkafolyamata így néz ki: 

![Kód tesztelési folyamatának diagramja](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Részletes lépések

A következő lépésekkel állíthatja be és futtathatja a kód tesztelését és egy automatizált buildet egy Build-ügynök és az Azure DevOps használatával:

1. Hozzon létre egy projektet a Visual Studio asztali alkalmazásban:

    !["Új projekt létrehozása" képernyő a Visual Studióban](./media/code-test/create_project.PNG)

   A projekt létrehozása után Megoldáskezelő a jobb oldali ablaktáblában találja:
    
    ![A projekt létrehozásának lépései](./media/code-test/create_python_project_in_vs.PNG)

    ![Megoldáskezelő](./media/code-test/solution_explorer_in_vs.PNG)

1. A projekt kódjának megtáplálása az Azure DevOps Project Code adattárba: 

    ![Projekt kódjának tárháza](./media/code-test/create_repo.PNG)

1. Tegyük fel, hogy elvégezte az adatelőkészítési munkákat, például az adatfeldolgozást, a funkciók mérnöki működését és a felirat oszlopainak létrehozását. Győződjön meg arról, hogy a kód a várt eredmények generálására szolgál. Az alábbi kód segítségével tesztelheti, hogy az adatfeldolgozási kód megfelelően működik-e:

    * Győződjön meg arról, hogy az oszlopnevek helyesek:
    
      ![Az oszlopnevek megfeleltetésére szolgáló kód](./media/code-test/check_column_names.PNG)

    * Győződjön meg arról, hogy a válasz szintjei megfelelőek:

      ![Kód a megfelelő szintekhez](./media/code-test/check_response_levels.PNG)

    * Győződjön meg arról, hogy a válasz százalékos értéke ésszerű:

      ![A válasz százalékértékének kódja](./media/code-test/check_response_percentage.PNG)

    * Az adatoszlopok hiányzó arányának megkeresése:
    
      ![A hiányzó sebesség kódja](./media/code-test/check_missing_rate.PNG)


1. Miután elvégezte az adatfeldolgozási és-szolgáltatás-mérnöki munkát, és egy jó modellt készített, győződjön meg arról, hogy a betanított modell helyesen szerzi be az új adatkészleteket. A következő két teszttel ellenőrizhető a felirat értékeinek előrejelzési szintjei és eloszlása:

    * Előrejelzési szintek keresése:
    
      ![Az előrejelzési szintek ellenőrzésének kódja](./media/code-test/check_prediction_levels.PNG)

    * Előrejelzési értékek eloszlásának keresése:

      ![Az előrejelzési értékek ellenőrzésének kódja](./media/code-test/check_prediction_values.PNG)

1. Az összes teszt függvényt egyesítse egy **test_funcs. a.**:

    ![Python-szkript a test functions szolgáltatáshoz](./media/code-test/create_file_test_func.PNG)


1. A tesztelési kódok előkészítése után beállíthatja a tesztelési környezetet a Visual Studióban.

   Hozzon létre egy **test1.py** nevű Python-fájlt. Ebben a fájlban hozzon létre egy osztályt, amely tartalmazza az összes végrehajtani kívánt tesztet. Az alábbi példa hat tesztet mutat be:
    
    ![Python-fájl egy osztályon belüli tesztek listájával](./media/code-test/create_file_test1_class.PNG)

1. Ezeket a teszteket automatikusan felderítheti, ha az **codetest. testCase** az osztály neve után helyezi el. Nyissa meg a test Explorert a jobb oldali ablaktáblán, és válassza az **összes futtatása** lehetőséget. Az összes teszt szekvenciálisan fut, és azt fogja tudni, hogy a teszt sikeres-e.

    ![A tesztek futtatása](./media/code-test/run_tests.PNG)

1. Adja meg a kódot a Project adattárhoz a git-parancsok használatával. A legutóbbi munkája hamarosan megjelennek az Azure DevOps.

    ![A kód ellenőrzéséhez használható git-parancsok](./media/code-test/git_check_in.PNG)

    ![Az Azure DevOps legutóbbi munkája](./media/code-test/git_check_in_most_recent_work.PNG)

1. Automatikus létrehozás és tesztelés beállítása az Azure DevOps-ben:

    a. A projekt adattárában válassza a **Létrehozás és kiadás** lehetőséget, majd az új létrehozási folyamat létrehozásához válassza az **+ új** lehetőséget.

    ![Új build-folyamat indításának kiválasztása](./media/code-test/create_new_build.PNG)

    b. Az utasításokat követve válassza ki a forráskód helyét, a projekt nevét, a tárházat és az ág adatait.
    
    ![Forrás-, név-, adattár-és ág-információk](./media/code-test/fill_in_build_info.PNG)

    c. Válasszon sablont. Mivel nincs Python-projekt sablonja, kezdje az **üres folyamat** kiválasztásával. 

    ![Sablonok listája és "üres folyamat" gomb](./media/code-test/start_empty_process_template.PNG)

    d. Nevezze el a Build nevet, és válassza ki az ügynököt. Itt választhatja ki az alapértelmezett értéket, ha DSVM kíván használni a létrehozási folyamat befejezéséhez. Az ügynökök beállításával kapcsolatos további információkért lásd: [létrehozási és kiadási ügynökök](/azure/devops/pipelines/agents/agents).
    
    ![Létrehozás és ügynök kiválasztása](./media/code-test/select_agent.PNG)

    e. Válassza ki **+** a bal oldali ablaktáblán, hogy felvegyen egy feladatot ehhez a Build fázishoz. Mivel a Python-szkript **test1.py** az összes ellenőrzés végrehajtásához futtatjuk, ez a feladat egy PowerShell-parancsot használ a Python-kód futtatásához.
    
    !["Feladatok hozzáadása" ablaktábla a PowerShell-lel kijelölve](./media/code-test/add_task_powershell.PNG)

    f. A PowerShell részleteiben adja meg a szükséges adatokat, például a PowerShell nevét és verzióját. A típusként válassza a **beágyazott parancsfájl** lehetőséget. 
    
    A **beágyazott parancsfájl** alatt található mezőbe beírhatja a **Python-test1.py**. Győződjön meg arról, hogy a környezeti változó helyesen van beállítva a Pythonhoz. Ha a Python más verziójára vagy kernelére van szüksége, explicit módon megadhatja az elérési utat az ábrán látható módon: 
    
    ![PowerShell-részletek](./media/code-test/powershell_scripts.PNG)

    : Válassza a **mentés & üzenetsor** lehetőséget a Build folyamatának befejezéséhez.

    !["& üzenetsor mentése" gomb](./media/code-test/save_and_queue_build_definition.PNG)

Most, hogy minden alkalommal, amikor új véglegesíti a kódot a tárházba, a létrehozási folyamat automatikusan elindul. Bármely ágat megadhat. A folyamat futtatja a **test1.py** fájlt az ügynök számítógépén, hogy ellenőrizze, hogy a kódban megadott összes érték megfelelően fut-e. 

Ha a riasztások megfelelően vannak beállítva, értesítést fog kapni e-mailben, amikor elkészült a Build. A Build állapotát az Azure DevOps is megtekintheti. Ha nem sikerül, megtekintheti a Build részleteit, és megtudhatja, hogy melyik darabot sérült meg.

![A Build sikeres e-mail-értesítése](./media/code-test/email_build_succeed.PNG)

![A Build sikeres Azure DevOps-értesítése](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Következő lépések
* Az adatelemzési forgatókönyvek esetében lásd: az [UCI bevétel-előrejelző tárháza](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , amely konkrét példákat tartalmaz az egységek tesztelésére.
* Kövesse az előző vázlatot és példákat a saját adatelemzési projektjeiben lévő UCI bevétel-előrejelzési forgatókönyvből.

## <a name="references"></a>Hivatkozások
* [Csoportos adatelemzési folyamat](./index.yml)
* [Visual Studio-tesztelési eszközök](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps-tesztelési erőforrások](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)