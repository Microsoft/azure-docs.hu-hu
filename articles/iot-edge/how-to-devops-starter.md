---
title: CI/CD-folyamat az Azure DevOps Starter-Azure IoT Edge | Microsoft Docs
description: Az Azure DevOps Starter megkönnyíti az Azure-ban való ismerkedést. Segítségével néhány gyors lépésben elindíthat egy Azure IoT Edge alkalmazást.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a23041a714089c447ab35ee05aae0de0c9c33d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201157"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>CI/CD-folyamat létrehozása IoT Edgehoz az Azure DevOps Starter-vel

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A folyamatos integráció (CI) és a folyamatos kézbesítés (CD) konfigurálása a IoT Edge alkalmazáshoz DevOps Projects. A DevOps Starter leegyszerűsíti az Azure-folyamatok létrehozási és kiadási folyamatának kezdeti konfigurációját.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure DevOps. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Starter Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása** lehetőséget, majd keresse meg a **DevOps Starter** elemet.  

1. Válassza a **Létrehozás** lehetőséget.

1. Alapértelmezés szerint a DevOps Starter beállítása a GitHub használatával történik. A jelen útmutató funkcióinak kihasználásához váltson a DevOps Starter-re az Azure DevOps használatával történő beállításhoz. Kövesse a **change Settings (beállítások módosítása** ) hivatkozást.

   ![Válassza a beállítások módosítása lehetőséget a GitHubról az Azure DevOps való váltáshoz](./media/how-to-devops-starter/create-with-github-change-settings.png)

1. A jobb oldali ablaktáblában válassza az **Azure DevOps** csempét, majd kattintson a **kész** gombra.

   ![Válassza ki az Azure DevOps a DevOps alapszintű létrehozásához](./media/how-to-devops-starter/select-azure-devops.png)

   Ekkor látnia kell, hogy a DevOps Starter beállítása az Azure DevOps.

## <a name="create-a-new-application-pipeline"></a>Új alkalmazás-folyamat létrehozása

1. A Azure IoT Edge modul (ok) a [C#](tutorial-csharp-module.md), a [Node.js](tutorial-node-module.md), a [Python](tutorial-python-module.md), a [C](tutorial-c-module.md) és a [Java](tutorial-java-module.md)nyelven is írható. Válassza ki a kívánt nyelvet egy új alkalmazás indításához: **.net**, **Node.js**, **Python**, **C** vagy **Java**. A folytatáshoz válassza a **Tovább** gombot.

   ![Új alkalmazás létrehozásához válassza a Language (nyelv) lehetőséget.](./media/how-to-devops-starter/select-language.png)

2. Válassza az **egyszerű IoT** lehetőséget az alkalmazás-keretrendszerként, majd kattintson a **tovább** gombra.

   ![Egyszerű IoT-keretrendszer kiválasztása](media/how-to-devops-starter/select-iot.png)

3. Válassza a **IoT Edge** lehetőséget az alkalmazást üzembe helyező Azure-szolgáltatásként, majd kattintson a **tovább** gombra.

   ![IoT Edge szolgáltatás kiválasztása](media/how-to-devops-starter/select-iot-edge.png)

4. Hozzon létre egy új ingyenes Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet.

   1. Adja meg a projekt nevét.

   2. Válassza ki az Azure DevOps-szervezetét. Ha nem rendelkezik meglévő szervezettel, válassza a **További beállítások** lehetőséget, hogy újat hozzon létre.

   3. Válassza ki Azure-előfizetését.

   4. Használja a projekt neve alapján létrehozott IoT Hub nevet, vagy adja meg a sajátját.

   5. Fogadja el az alapértelmezett helyet, vagy válasszon egyet a bezáráshoz.

   6. Válassza a **További beállítások** lehetőséget, hogy konfigurálja az DevOps által az Ön nevében létrehozott Azure-erőforrásokat.

   7. A projekt létrehozásának befejezéséhez kattintson a **kész** gombra.

   ![Projekt neve és létrehozása](media/how-to-devops-starter/create-project.png)

Néhány perc elteltével a DevOps Starter irányítópultja megjelenik a Azure Portal. Válassza ki a projekt nevét a folyamat előrehaladásának megtekintéséhez. Lehetséges, hogy frissítenie kell az oldalt. Egy minta IoT Edge alkalmazás van beállítva az Azure DevOps-szervezetben lévő adattárban, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi a IoT Edge eszközön. Ez az irányítópult a Code adattárba, a CI/CD-folyamatba és az Azure-beli alkalmazásba is betekintést nyújt.

   ![Projekt megtekintése Azure Portal](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Starter létrehozta a projekthez tartozó git-tárházat az Azure Reposban. Ebben a szakaszban megtekintheti az adattárat, és programkód-módosításokat hajthat végre az alkalmazásban.

1. Ha a projekthez létrehozott tárházra szeretne navigálni, válassza a **tárolók** lehetőséget a projekt irányítópultjának menüjében. Ez a hivatkozás megnyitja a böngésző fület és az új projekthez tartozó Azure DevOps-tárházat.

   ![Az Azure Reposben létrehozott adattár megtekintése](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > A következő lépések végigvezetik a böngészőben a kód módosításának lépésein. Ha a tárházat helyileg szeretné klónozottként használni, válassza a **klónozás** lehetőséget az ablak jobb felső sarkában. Használja a megadott URL-címet a git-tárház klónozásához a Visual Studio Code-ban vagy az előnyben részesített fejlesztői eszközben.

2. A tárház már tartalmaz egy **FilterModule** nevű modul kódját a létrehozási folyamat során kiválasztott nyelv alapján. Nyissa meg a **modules/FilterModule/module.js** fájlt.

   ![module.jsmegnyitása a fájlon az Azure Reposban](./media/how-to-devops-starter/open-module-json.png)

3. Figyelje meg, hogy ez a fájl az [Azure DevOps Build változókat](/azure/devops/pipelines/build/variables#build-variables) használja a **Version** paraméterben. Ez a konfiguráció biztosítja, hogy a rendszer minden új Build futtatásakor létrehozza a modul új verzióját.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

Az előző szakaszban az Azure DevOps Starter automatikusan konfigurálta a IoT Edge alkalmazás teljes CI/CD-folyamatát. Most a folyamat igény szerinti megismeréséhez és testreszabásához. Az alábbi lépésekkel megismerheti az Azure DevOps Build és Release folyamatait.

1. Ha meg szeretné tekinteni a DevOps projekt összeállítási folyamatait, válassza a **folyamat létrehozása** lehetőséget a projekt irányítópultjának menüjében. Ez a hivatkozás egy böngésző fület és az Azure DevOps Build folyamatát nyitja meg az új projekthez.

   ![Build-folyamatok megtekintése az Azure-folyamatokban](./media/how-to-devops-starter/view-build-pipelines.png)

2. Nyissa meg az automatikusan generált Build folyamatot, és válassza a **Szerkesztés** lehetőséget a jobb felső sarokban.

    ![Build folyamat szerkesztése](media/how-to-devops-starter/click-edit-button.png)

3. A megnyíló panelen megtekintheti a létrehozási folyamat futtatásakor felmerülő feladatokat. A létrehozási folyamat különféle feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, IoT Edge modul lemezképeit fejleszti, leküldi IoT Edge modulokat egy tároló-beállításjegyzékbe, és közzéteszi az üzembe helyezéshez használt kimeneteket. Ha többet szeretne megtudni az Azure DevOps Azure IoT Edge feladatairól, tekintse meg [Az Azure-folyamatok folyamatos integrációhoz való konfigurálásával foglalkozó](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration)témakört.

4. A folyamat részleteinek megnyitásához válassza a **folyamat fejlécét** a build folyamat tetején. Módosítsa a buildelési folyamat nevét egy közérthetőbb névre.

   ![A folyamat részleteinek szerkesztése](./media/how-to-devops-starter/edit-build-pipeline.png)

5. Válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra. A megjegyzés nem kötelező.

6. Válassza az **Eseményindítók** lehetőséget a folyamat összeállítása menüből. A DevOps Starter automatikusan létrehozta a CI-triggert, és az adattárba való minden kötelezettség új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

7. Válassza a **Megtartást**. Kövesse a hivatkozást, és irányítsa át a projekt beállításait, ahol az adatmegőrzési szabályzatok találhatók. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

8. Válassza a **History (előzmények**) lehetőséget. Az előzmények panel a Build legutóbbi változásainak naplózási nyomvonalát tartalmazza. Az Azure-folyamatok nyomon követik a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszik a verziók összehasonlítását.

9. Amikor elkészült a Build folyamattal, navigáljon a megfelelő kiadási folyamathoz. Válassza a **kiadások** a **folyamatok** alatt lehetőséget, majd kattintson a **Szerkesztés** elemre a folyamat részleteinek megtekintéséhez.

    ![Kiadási folyamat megtekintése](media/how-to-devops-starter/release-pipeline.png)

10. Az **Összetevők** alatt válassza az **Elvetést**. Az összetevő által figyelt forrás az előző lépésekben megvizsgált összeállítási folyamat kimenete.

11. A **drop** ikon mellett válassza ki a **folyamatos üzembe helyezési triggert** , amely a villámcsapáshoz hasonlít. Ez a kiadási folyamat engedélyezte az aktiválást, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek.  

12. A kiadási folyamat menüjében válassza a **feladatok** lehetőséget, majd válassza ki a **fejlesztői** szakaszt a legördülő listából. DevOps Projects létrehozott egy kiadási szakaszt, amely létrehoz egy IoT hubot, létrehoz egy IoT Edge eszközt az adott központban, üzembe helyezi a minta modult a Build folyamatból, és kiépíti a virtuális gépet, hogy IoT Edge eszközként fusson. Ha többet szeretne megtudni a CD Azure IoT Edge feladatairól, tekintse meg [Az Azure-folyamatok folyamatos üzembe helyezéshez való konfigurálását](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment)ismertető témakört.

    ![Folyamatos üzembe helyezési feladatok megtekintése](media/how-to-devops-starter/choose-release.png)

13. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget. Ebben a nézetben a kiadások előzményei jelennek meg.

14. Válassza ki a kiadás nevét a további információk megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat, amelyeket akkor hozott létre, ha már nincs rá szükség. Használja a **delete** funkciót a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg az Azure DevOps Azure IoT Edgeával kapcsolatos feladatokkal a [folyamatos integráció és a folyamatos üzembe helyezés Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* A IoT Edge központi telepítésének megismerése az [egyes eszközök IoT Edge központi telepítések megismeréséhez](module-deployment-monitoring.md)
* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-at-scale.md).
