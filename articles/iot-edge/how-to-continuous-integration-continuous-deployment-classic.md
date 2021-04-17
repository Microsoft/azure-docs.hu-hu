---
title: Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge eszközökre (klasszikus szerkesztő) – Azure IoT Edge
description: Folyamatos integráció és folyamatos üzembe helyezés beállítása a klasszikus szerkesztővel – Azure IoT Edge Azure DevOps és az Azure Pipelines használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484421"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge eszközökre (klasszikus szerkesztő)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure Pipelines beépített Azure IoT Edge feladatokkal könnyedén beveheti a DevOpsot Azure IoT Edge alkalmazásokkal. Ez a cikk bemutatja, hogyan használhatja az Azure Pipelines folyamatos integrációs és folyamatos üzembe helyezési funkcióit alkalmazások gyors és hatékony építéséhez, teszteléséhez és üzembe helyezéséhez a Azure IoT Edge a klasszikus szerkesztővel. Alternatív megoldásként használhatja a [YAML-t is.](how-to-continuous-integration-continuous-deployment.md)

![Diagram – CI- és CD-ágak fejlesztéshez és éles környezethez](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Ebből a cikkből megtudhatja, hogyan [](/azure/devops/pipelines/tasks/build/azure-iot-edge) használhatja az Azure Pipelines beépített Azure IoT Edge-feladatait build- és kiadási folyamatok létrehozásához a IoT Edge megoldáshoz. A Azure IoT Edge hozzáadott összes feladat a következő négy művelet egyikét valósítja meg:

 | Művelet | Leírás |
 | --- | --- |
 | Modul rendszerképének összeállítása | A IoT Edge a megoldás kódját, és felépíti a tároló rendszerképeket.|
 | Modulképek leküldése | Modul rendszerképeket ad le a megadott tároló-beállításjegyzékbe. |
 | Üzembe helyezési jegyzék létrehozása | A fájl deployment.template.jsváltozóit, majd létrehozza az üzembehely IoT Edge jegyzékfájlját. |
 | Üzembe helyezés IoT Edge-eszközökön | Központi IoT Edge hoz létre egy vagy több IoT Edge eszközre. |

Ha másként nincs megadva, a cikkben található eljárások nem tárják fel a feladatparaméterekkel elérhető összes funkciót. További információkat a következő cikkekben talál:

* [Feladat verziója](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Speciális** – Ha alkalmazható, adja meg azokat a modulokat, amelyekhez nem szeretne beépítettet használni.
* [Vezérlési beállítások](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Környezeti változók](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Kimeneti változók](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Repos-adattár. Ha még nem használhatja, létrehozhat egy új [Git-adattárat a projektben.](/azure/devops/repos/git/create-new-repo) Ebben a cikkben létrehoztunk egy **IoTEdgeRepo nevű adattárat.**
* Egy IoT Edge megoldás, amely le van kötötte és lekérte az adattárba. Ha új mintamegoldást szeretne létrehozni a cikk teszteléséhez, kövesse a Modulok fejlesztése és hibakeresése az [Visual Studio Code-ban](how-to-vs-code-develop-module.md) vagy A [C#-modulok](./how-to-visual-studio-develop-module.md)fejlesztése és hibakeresése a Visual Studio. Ebben a cikkben létrehoztunk egy **IoTEdgeSolution** nevű megoldást az adattárban, amely tartalmazza a **filtermodule nevű modul kódját.**

   Ebben a cikkben csak arra a megoldásmappra van szükség, amelyet az IoT Edge Code vagy a Visual Studio sablonjai hoztak Visual Studio. A folytatás előtt nem kell buildelni, leküldést végeznie, üzembe helyeznie vagy hibakeresést végeznie a kódon. Ezeket a folyamatokat az Azure Pipelinesban fogja beállítani.

   Ha új megoldást hoz létre, először helyileg klónozza az adattárat. Ezután a megoldás létrehozásakor közvetlenül az adattár mappájában is létrehozhatja azt. Innen könnyedén véglegesheti és lekulálhatja az új fájlokat.

* Egy tároló-beállításjegyzék, amelyben modul-rendszerképeket lehet lekultálni. Használhat egy [Azure Container Registry](../container-registry/index.yml) harmadik féltől származó beállításjegyzéket is.
* Aktív Azure [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) legalább két IoT Edge a különálló tesztelési és éles üzembe helyezési szakaszok teszteléséhez. A rövid útmutatóban található cikkek alapján létrehozhat egy virtuális IoT Edge [Linux vagy](quickstart-linux.md) [Windows rendszeren.](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Build folyamat létrehozása folyamatos integrációhoz

Ebben a szakaszban egy új build folyamatot hoz létre. A folyamat konfigurálható úgy, hogy automatikusan fusson, amikor bejelentkezik a mintaalkalmazás megoldásának IoT Edge, és közzéteszi a buildnaplókat.

1. Jelentkezzen be az Azure DevOps-szervezetbe ( ), és nyissa meg a saját IoT Edge `https://dev.azure.com/{your organization}` tárházát tartalmazó projektet.

    ![A DevOps-projekt megnyitása](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. A projekt bal oldali panel menüjében válassza a **Folyamatok lehetőséget.** Az **oldal közepén** válassza a Folyamat létrehozása lehetőséget. Ha már rendelkezik build-folyamatokkal, válassza a jobb felső **sarokban** található Új folyamat gombot.

    ![Új buildfolyamat létrehozása](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. A Hol található **a kód?** oldal alján válassza a **Klasszikus szerkesztő használata lehetőséget.** Ha YAML használatával szeretné létrehozni a projekt build folyamatát, tekintse meg a [YAML-útmutatót.](how-to-continuous-integration-continuous-deployment.md)

    ![Válassza a Klasszikus szerkesztő használata lehetőséget](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Kövesse az utasításokat a folyamat létrehozásához.

   1. Adja meg az új build-folyamat forrásinformációját. Válassza **ki forrásként az Azure Repos Git** lehetőséget, majd válassza ki a projektet, az adattárat és az ágat, ahol IoT Edge a megoldás kódja. Ezután válassza a **Folytatás lehetőséget.**

      ![Válassza ki a folyamat forrását](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Sablon **helyett válassza** az Üres feladat lehetőséget.

      ![Kezdés a build-folyamat üres feladatával](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. A folyamat létrehozása után a folyamatszerkesztőbe lesz ásva. Itt módosíthatja a folyamat nevét, ügynökkészletét és ügynökspecifikációját.

   Kiválaszthat egy Microsoft által üzemeltetett készletet vagy egy saját által üzemeltetett készletet.

   A folyamat leírásában válassza ki a megfelelő ügynökspecifikációt a célplatform alapján:

   * Ha Linux-tárolókhoz az amd64 platformon szeretné felépíteni moduljait, válassza az **ubuntu-16.04 lehetőséget**

   * Ha windowsos 1809-tárolókhoz az amd64 platformon szeretné felépíteni a modulokat, saját maga által üzemeltetett ügynököt kell [beállítania a Windows rendszeren.](/azure/devops/pipelines/agents/v2-windows)

   * Ha az arm32v7 vagy arm64 platformon szeretné buildelni a modulokat Linux-tárolókhoz, saját üzemeltetett ügynököt kell [beállítania Linux rendszeren.](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)

    ![A buildügynök specifikációinak konfigurálása](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. A folyamathoz előre konfigurálva van egy **1. ügynöki feladat nevű feladat.** Válassza a pluszjelet ( ) négy tevékenység hozzáadásához **+** a feladathoz: Azure IoT Edge, egyszer a **Fájlok** másolása és a  **Build-összetevők közzététele** egyszer. Keresse meg az egyes feladatokat, és vigye az egérmutatót a tevékenység nevére a **Hozzáadás gomb megjelenítéséhez.**

   ![Új Azure IoT Edge hozzáadása](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Ha mind a négy tevékenység hozzá van adva, az ügynökfeladat az alábbi példához hasonlít:

   ![Négy feladat a build-folyamatban](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Válassza ki az **Azure IoT Edge** a szerkesztéséhez. Ez a feladat a megoldás összes modulját felépíti a megadott célplatformmal. Szerkessze a feladatot a következő értékekkel:

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | A megjelenített név automatikusan frissül, ha a Művelet mező megváltozik. |
    | Művelet | Válassza **a Modul-rendszerképek összeállítása lehetőséget.** |
    | .template.jsfájlban | Válassza a három pont **(...**)  lehetőséget, és lépjen adeployment.template.js-megoldást tartalmazó adattárban található IoT Edge fájlhoz. |
    | Alapértelmezett platform | Válassza ki a moduloknak megfelelő operációs rendszert a céleszköz IoT Edge alapján. |
    | Kimeneti változók | Adjon meg egy referencianevet, amely ahhoz a fájlútvonalhoz lesz társítva, deployment.jsa fájlban található fájl neve, például **edge.** |

   Ezek a konfigurációk a fájlban definiált képtárat és címkét használják a modul rendszerképének `module.json` elnevezéséhez és címkézéséhez. **A buildmodul-rendszerképek** a változóknak a fájlban megadott pontos értékre való cseréjében is `module.json` segítenek. A Visual Studio vagy Visual Studio Code-ban a tényleges értéket adja meg egy `.env` fájlban. Az Azure Pipelinesban a Pipeline Variables (Folyamatváltozók) lapon **állíthatja** be az értéket. Válassza a **Változók lapot** a folyamatszerkesztő menüben, és konfigurálja a nevet és az értéket az alábbiak szerint:

    * **ACR_ADDRESS:** A Azure Container Registry bejelentkezési **kiszolgáló értéke.** A bejelentkezési kiszolgálót a tároló-beállításjegyzék Áttekintés lapján olvashatja be a Azure Portal.

    Ha más változók is vannak a projektben, ezen a lapon adhatja meg a nevet és az értéket. A **Buildmodul-képek** csak a formátumú változókat `${VARIABLE}` ismerik fel. Ügyeljen rá, hogy ezt a formátumot használja a `**/module.json` fájlokban.

8. Válassza ki a **Azure IoT Edge** a szerkesztési feladatot. Ez a feladat lekultálja az összes modul rendszerképét a kiválasztott tároló-beállításjegyzékbe.

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | A megjelenített név automatikusan frissül, amikor a Művelet mező megváltozik. |
    | Művelet | Válassza **a Modulképek leküldése lehetőséget.** |
    | Tároló-beállításjegyzék típusa | Használja az alapértelmezett típust: `Azure Container Registry` . |
    | Azure-előfizetés | Válassza ki az előfizetését. |
    | Azure Container Registry | Válassza ki a tároló-beállításjegyzék típusát, amely a modul rendszerképének tárolására használható. A választott beállításjegyzék-típustól függően az űrlap módosul. Ha a **Azure Container Registry** választ, a legördülő listákkal válassza ki az Azure-előfizetést és a tároló-beállításjegyzék nevét. Ha az **Általános** Container Registry lehetőséget választja, válassza az **Új** lehetőséget egy beállításjegyzék-szolgáltatáskapcsolat létrehozásához. |
    | .template.jsfájlban | Válassza a három pont **(...**)  lehetőséget, és lépjen adeployment.template.js-megoldást tartalmazó adattárban található IoT Edge fájlhoz. |
    | Alapértelmezett platform | Válassza ki a moduloknak megfelelő operációs rendszert a céleszköz IoT Edge alapján. |
    | Beállításjegyzékbeli hitelesítő adatok hozzáadása az üzembe helyezési jegyzékhez | A true (igaz) érték megadásával adja hozzá a regisztrációs adatbázis hitelesítő adatait a Docker-lemezképek üzembe helyezési jegyzékbe való lekultálása érdekében. |

   Ha több tárolóregisztrálóval is tárolja a modul rendszerképeket, duplikálhatja ezt a feladatot, másik  tárolóregisztrjegyzéket kell választania, és a Speciális beállítások Mellőzött **modul(k)** használatával meg kell kerülnie azokat a rendszerképeket, amelyek nem ehhez az adott beállításjegyzékhez vannak használva.

9. A **szerkesztéshez** válassza a Fájlok másolása feladatot. Ezzel a feladattal másolhatja a fájlokat az összetevő előkészítési könyvtárába.

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | Az alapértelmezett név használata vagy testreszabás |
    | Forrásmappa | A másolni kívánt fájlokat tartalmazó mappa. |
    | Tartalom | Adjon hozzá két sort: `deployment.template.json` és `**/module.json` . Ez a két fájl szolgál bemenetként az üzembehely IoT Edge létrehozásához. |
    | Célmappa | Adja meg a `$(Build.ArtifactStagingDirectory)` változót. A [leírással kapcsolatban lásd: Változók](/azure/devops/pipelines/build/variables#build-variables) összeállítása. |

10. A szerkesztéshez válassza a **Build-összetevők** közzététele feladatot. Adja meg az összetevő átmeneti könyvtárának elérési útját a feladathoz, hogy az elérési út közzé tehetők legyen a kiadási folyamatban.

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | Használja az alapértelmezett nevet, vagy szabja testre. |
    | Közzétételi útvonal | Adja meg a `$(Build.ArtifactStagingDirectory)` változót. További [információ: Változók](/azure/devops/pipelines/build/variables#build-variables) összeállítása. |
    | Összetevő neve | Használja az alapértelmezett nevet: **drop** |
    | Összetevő közzétételi helye | Használja az alapértelmezett helyet: **Azure Pipelines** |

11. Nyissa meg **az Eseményindítók** lapot, és jelölje be a **Folyamatos integráció engedélyezése jelölőnégyzetet.** Győződjön meg arról, hogy a kódot tartalmazó ág szerepel benne.

    ![Folyamatos integrációs eseményindító bekapcsolása](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Válassza **a Save** (Mentés) lehetőséget a Save & queue **(Üzenetsor mentése)** legördülő menüből.

Ez a folyamat úgy van konfigurálva, hogy automatikusan fusson, amikor új kódot ad le az adattúdóba. Az utolsó feladat, amely közzéteszi a folyamat-összetevőket, elindít egy kiadási folyamatot. Folytassa a következő szakaszokkal a kiadási folyamat felépítéséhez.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Ha többrétegű üzembe **helyezéseket** szeretne használni a folyamatban, a többrétegű üzembe helyezések még nem támogatottak az Azure DevOps Azure IoT Edge feladatokban.
>
>Az [Azure DevOpsban](/azure/devops/pipelines/tasks/deploy/azure-cli) azonban egy Azure CLI-feladattal réteges üzembe helyezésként hozhatja létre az üzemelő példányokat. A Beágyazott **szkript értékeként** használhatja az [az iot edge deployment create parancsot:](/cli/azure/iot/edge/deployment)
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Következő lépések

* IoT Edge DevOps ajánlott eljárások mintája az [Azure DevOps Starter for IoT Edge](how-to-devops-starter.md)
* A IoT Edge az egyetlen IoT Edge és a nagy léptékű üzembe helyezéssel kapcsolatos [útmutatóban](module-deployment-monitoring.md)
* Az üzemelő példányok létrehozásához, frissítéséhez vagy törléséhez kövesse az Üzembe helyezés és figyelése IoT Edge [modulokat nagy méretekben.](how-to-deploy-at-scale.md)