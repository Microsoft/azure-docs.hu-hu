---
title: Az Azure DevOps használata CI/CD-folyamat létrehozásához Stream Analytics feladatokhoz
description: Ez a cikk azt ismerteti, hogyan lehet folyamatos integrációs és üzembe helyezési (CI/CD) folyamatokat beállítani egy Azure Stream Analytics feladatokhoz az Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: f62b4c354ffa90bf1a03651fccf8780074344e46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466422"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Az Azure DevOps használata CI/CD-folyamat létrehozásához Stream Analytics feladatokhoz

Ebből a cikkből megtudhatja, hogyan hozhat létre [Azure DevOps-létrehozási](/azure/devops/pipelines/get-started/pipelines-get-started) és- [kiadási](/azure/devops/pipelines/release/define-multistage-release-process) folyamatokat Azure stream Analytics CI/CD-eszközök használatával.

## <a name="commit-your-stream-analytics-project"></a>Stream Analytics projekt véglegesíte

Mielőtt elkezdené, véglegesítse a teljes Stream Analytics-projekteket forrásfájlként egy [Azure DevOps](/azure/devops/user-guide/source-control) adattárba. Hivatkozhat erre a [minta adattárra](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) , és [stream Analytics a projekt forráskódját](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) az Azure-folyamatokban.

A cikkben ismertetett lépések egy Stream Analytics Visual Studio Code-projektet használnak. Ha Visual Studio-projektet használ, kövesse a [Azure stream Analytics feladatok automatizálása, tesztelése és központi telepítése a CI/CD-eszközök használatával](cicd-tools.md)című témakör lépéseit.

## <a name="create-a-build-pipeline"></a>Buildelési folyamat létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre Build-folyamatot. A minta [automatikus létrehozási és tesztelési folyamatát](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) az Azure DevOps is hivatkozhatjuk.

1. Nyisson meg egy webböngészőt, és navigáljon a projekthez az Azure DevOps.  

1. A bal oldali navigációs menü **folyamatok** területén válassza a **buildek** lehetőséget. Ezután válassza az **új folyamat** lehetőséget.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Új Azure-folyamat létrehozása":::

1. Válassza **a klasszikus szerkesztő használata** lehetőséget a YAML nélküli folyamat létrehozásához.

1. Válassza ki a forrás típusát, a csapat projektjét és a tárházat. Ezután válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Azure Stream Analytics projekt kiválasztása":::

1. A **sablon választása** lapon válassza az **üres feladatot**.

## <a name="install-npm-package"></a>NPM-csomag telepítése

1. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat** mellett. Adja meg a *NPM* a feladat keresése területen, majd válassza a **NPM** lehetőséget.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="NPM feladat kiválasztása":::

2. Adja meg a feladat **megjelenítendő nevét**. Módosítsa a **parancsot** *egyénire* , és adja meg a következő parancsot a **parancsban és argumentumokban**. Hagyja meg a többi alapértelmezett beállítást.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Adja meg a NPM feladat konfigurációit":::

Ha üzemeltetett linuxos ügynököt szeretne használni, kövesse az alábbi lépéseket:
1.  Az **ügynök specifikációjának** kiválasztása
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="Képernyőkép az ügynök specifikációjának kiválasztásáról.":::

2.  A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat** mellett. Adja meg a *parancssort* a feladat keresése területen, és válassza a **parancssor** lehetőséget.
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="Képernyőkép a parancssori feladat kereséséről. ":::

3.  Adja meg a feladat **megjelenítendő nevét**. adja meg a következő parancsot a **szkriptben**. Hagyja meg a többi alapértelmezett beállítást.

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="Képernyőkép a cmd-feladat parancsfájljának beírásáról.":::

## <a name="add-a-build-task"></a>Felépítési feladat hozzáadása

1. A **változók** lapon válassza a **+ Hozzáadás** a **folyamat változói** lehetőséget. Adja hozzá a következő változókat. Állítsa be a következő értékeket a beállításnak megfelelően:

   |Változó neve|Érték|
   |-|-|
   |projectRootPath|YourProjectName|
   |outputPath|Kimenet|
   |deployPath|Üzembe helyezés|

2. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat** mellett. Keresse meg a **parancssort**.

3. Adja meg a feladat **megjelenítendő nevét** , és adja meg a következő parancsfájlt. Módosítsa a szkriptet az adattár nevével és a projekt nevével.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   Az alábbi kép egy Stream Analytics Visual Studio Code-projektet használ példaként.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="A parancssori feladathoz tartozó Visual Studio Code-hoz tartozó konfigurációk megadása":::

## <a name="add-a-test-task"></a>Teszt feladat hozzáadása

1. A **változók** lapon válassza a **+ Hozzáadás** a **folyamat változói** lehetőséget. Adja hozzá a következő változókat. Módosítsa az értékeket a kimeneti elérési úttal és a tárház nevével.

   |Változó neve|Érték|
   |-|-|
   |testPath|Tesztelés|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Folyamat változóinak hozzáadása":::

2. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat** mellett. Keresse meg a **parancssort**.

3. Adja meg a feladat **megjelenítendő nevét** , és adja meg a következő parancsfájlt. Módosítsa a parancsfájlt a projekt fájljának nevével és a teszt konfigurációs fájljának elérési útjával. 

   A tesztelési esetek hozzáadásával és konfigurálásával kapcsolatos részletekért tekintse meg az [automatizált tesztelési utasításokat](cicd-tools.md#automated-test) .

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="A parancssori feladathoz tartozó konfigurációk megadása":::

## <a name="add-a-copy-files-task"></a>Fájl másolása feladat hozzáadása

A fájl másolása feladatot fel kell vennie, hogy átmásolja a teszt összefoglaló fájlját, és Azure Resource Manager sablonfájlokat az összetevő mappájába. 

1. A **feladatok** lapon válassza a **+** következőt az **ügynök feladatához: 1**. **Fájlok másolásának** keresése. Ezután adja meg a következő konfigurációkat. A `**` **tartalom** hozzárendelésével a rendszer a teszteredmények összes fájlját átmásolja.

   |Paraméter|Bevitel|
   |-|-|
   |Megjelenített név|Fájlok másolása ide: $ (Build. artifactstagingdirectory)|
   |Forrás mappája|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Tartalom| `**` |
   |Célmappa| `$(build.artifactstagingdirectory)`|

2. A **vezérlési beállítások** kibontása. **Akkor is válassza ki, ha egy korábbi feladat meghiúsult, kivéve, ha a buildet megszakították** a **feladat futtatásakor**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="A másolási feladathoz tartozó konfigurációk megadása":::

## <a name="add-a-publish-build-artifacts-task"></a>Közzétételi összetevők közzététele feladat hozzáadása

1. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat** mellett. Keressen rá a **közzétett Build** -összetevők keresése elemre, és válassza a fekete nyíl ikont.

2. A **vezérlési beállítások** kibontása. **Akkor is válassza ki, ha egy korábbi feladat meghiúsult, kivéve, ha a buildet megszakították** a **feladat futtatásakor**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Adja meg a közzétételi feladat konfigurációit":::

## <a name="save-and-run"></a>Mentés és Futtatás

Ha végzett a NPM-csomag, a parancssor, a fájlok másolása és a Build-összetevők közzététele feladatok hozzáadásával, válassza a **mentés & üzenetsor** lehetőséget. Amikor a rendszer kéri, adjon meg egy mentési megjegyzést, és válassza a **Mentés és Futtatás** lehetőséget. A tesztelési eredményeket a folyamat **Összefoglalás** oldaláról töltheti le.

## <a name="check-the-build-and-test-results"></a>A létrehozási és tesztelési eredmények ellenőrzése

A tesztelési összesítő fájl és a Azure Resource Manager sablonfájlok a **közzétett** mappában találhatók.

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Build és teszt eredményének ellenőrzése":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Összetevők keresése":::

## <a name="release-with-azure-pipelines"></a>Kiadás Azure-folyamatokkal

Ebből a szakaszból megtudhatja, hogyan hozhat létre kiadási folyamatokat. Hivatkozhat erre a minta [kiadási folyamatra](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) az Azure DevOps-ben.

Nyisson meg egy webböngészőt, és navigáljon a Azure Stream Analytics Visual Studio Code projekthez.

1. A bal oldali navigációs menü **folyamatok** területén válassza a **kiadások** elemet. Ezután válassza az **új folyamat** lehetőséget.

2. Válassza **az indítás üres feladatokkal** lehetőséget.

3. Az összetevők **mezőben válassza az** **+ összetevő hozzáadása** elemet. A **forrás** területen válassza ki a létrehozott Build folyamatot, és válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Adja meg a folyamat létrehozása összetevőt":::

4. Módosítsa az 1. **fázis** nevét a **feladatok tesztelési környezetbe való telepítéséhez**.

5. Vegyen fel egy új szakaszt, és nevezze el az **üzembe helyezési feladatot éles környezetbe**.

### <a name="add-deploy-tasks"></a>Üzembe helyezési feladatok hozzáadása

1. A feladatok legördülő menüben válassza a **feladat üzembe helyezése a tesztkörnyezet teszteléséhez** lehetőséget.

2. Válassza ki a következőt az ügynök feladatainál **+** , és keressen az **ARM-sablon üzembe helyezése** **lehetőségre** . Adja meg a következő paramétereket:

   |Paraméter|Érték|
   |-|-|
   |Megjelenített név| *MyASAProject üzembe helyezése*|
   |Azure-előfizetés| Válassza ki az előfizetését.|
   |Művelet| *Erőforráscsoport létrehozása vagy frissítése*|
   |Erőforráscsoport| Válassza ki a Stream Analytics feladatot tartalmazó tesztelési erőforráscsoport nevét.|
   |Hely|Válassza ki a tesztelési erőforráscsoport helyét.|
   |Sablon helye| Társított összetevő|
   |Sablon| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-vel-demo-CI-üzembe helyezés/eldobás/myASAProject.JobTemplate.jsbekapcsolva |
   |Sablon paraméterei|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-vel-demo-CI-üzembe helyezés/eldobás/myASAProject.JobTemplate.parameters.jsbekapcsolva |
   |Sablon paramétereinek felülbírálása|-<arm_template_parameter> "az érték". A paramétereket **változók** használatával is meghatározhatja.|
   |Üzembehelyezési mód|Növekvő|

3. A feladatok legördülő menüben válassza a **feladat üzembe helyezése éles környezetben** lehetőséget.

4. Válassza ki a következőt az ügynök feladatainál **+** , és keressen az *ARM-sablon üzembe helyezése* **lehetőségre** . Adja meg a következő paramétereket:

   |Paraméter|Érték|
   |-|-|
   |Megjelenített név| *MyASAProject üzembe helyezése*|
   |Azure-előfizetés| Válassza ki az előfizetését.|
   |Művelet| *Erőforráscsoport létrehozása vagy frissítése*|
   |Erőforráscsoport| Válassza ki az üzemi erőforráscsoport nevét, amely a Stream Analytics feladatot fogja tartalmazni.|
   |Hely|Válassza ki az üzemi erőforráscsoport helyét.|
   |Sablon helye| *Társított összetevő*|
   |Sablon| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-vel-demo-CI-üzembe helyezés/eldobás/myASAProject.JobTemplate.jsbekapcsolva |
   |Sablon paraméterei|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-vel-demo-CI-üzembe helyezés/eldobás/myASAProject.JobTemplate.parameters.jsbekapcsolva |
   |Sablon paramétereinek felülbírálása|-<arm_template_parameter> "az Ön értéke"|
   |Üzembehelyezési mód|Növekvő|

### <a name="create-a-release"></a>Kiadás létrehozása

A kiadás létrehozásához válassza a jobb felső sarokban található **kiadás létrehozása** elemet.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Kiadás létrehozása az Azure-folyamatokkal":::

## <a name="next-steps"></a>Következő lépések

* [Folyamatos integráció és folyamatos üzembe helyezés a Azure Stream Analytics számára](cicd-overview.md)
* [Azure Stream Analytics feladatok felépítésének, tesztelésének és üzembe helyezésének automatizálása CI/CD-eszközök használatával](cicd-tools.md)