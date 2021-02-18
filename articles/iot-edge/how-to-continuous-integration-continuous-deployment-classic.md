---
title: Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge eszközökön (klasszikus szerkesztő) – Azure IoT Edge
description: Folyamatos integráció és folyamatos üzembe helyezés beállítása a klasszikus szerkesztő-Azure IoT Edge az Azure DevOps, az Azure-folyamatok használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e38b3c617ded9c0001b01e481d4d3c1120be62ef
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634753"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge eszközökön (klasszikus szerkesztő)

Az Azure-folyamatok beépített Azure IoT Edge feladataival könnyedén elvégezheti a DevOps a Azure IoT Edge alkalmazásaiban. Ez a cikk bemutatja, hogyan használhatja az Azure-folyamatok folyamatos integrációját és folyamatos üzembe helyezési funkcióit az alkalmazások gyors és hatékony üzembe helyezéséhez, teszteléséhez és központi telepítéséhez a Azure IoT Edge a klasszikus szerkesztő használatával. Azt is megteheti, hogy a YAML-t [használja](how-to-continuous-integration-continuous-deployment.md).

![Diagram – CI és CD ágak fejlesztési és termelési célokra](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-folyamatok beépített [Azure IoT Edge feladatait](/azure/devops/pipelines/tasks/build/azure-iot-edge) a IoT Edge-megoldáshoz tartozó Build és kiadási folyamatok létrehozásához. A folyamathoz hozzáadott minden Azure IoT Edge feladat a következő négy művelet egyikét valósítja meg:

 | Művelet | Leírás |
 | --- | --- |
 | Modul lemezképének összeállítása | A IoT Edge megoldás kódját hozza létre, és létrehozza a tároló lemezképeit.|
 | Leküldéses modul képei | Leküldi a modul rendszerképeit a megadott tároló-beállításjegyzékbe. |
 | Üzembe helyezési jegyzék előállítása | deployment.template.jsa fájlra és a változókra, majd létrehozza a végső IoT Edge telepítési jegyzékfájlt. |
 | Üzembe helyezés IoT Edge-eszközökön | IoT Edge központi telepítéseket hoz létre egy vagy több IoT Edge eszközre. |

Ha másként nincs megadva, az ebben a cikkben ismertetett eljárások nem tárgyalják a feladat paramétereinek használatával elérhető funkciókat. További információkat a következő cikkekben talál:

* [Feladat verziója](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Speciális** – ha alkalmazható, olyan modulokat adhat meg, amelyeket nem szeretne felépíteni.
* [Vezérlési beállítások](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Környezeti változók](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Kimeneti változók](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Repos-tárház. Ha még nem rendelkezik ilyennel, [létrehozhat egy új git-tárházat a projektben](/azure/devops/repos/git/create-new-repo). Ebben a cikkben egy **IoTEdgeRepo** nevű tárházat hoztunk létre.
* Egy IoT Edge-megoldás véglegesítve lett, és leküldve a tárházba. Ha a cikk teszteléséhez új mintavételi megoldást szeretne létrehozni, kövesse a [modulok fejlesztése és hibakeresése a Visual Studio Code](how-to-vs-code-develop-module.md) -ban című témakör lépéseit, illetve [C#-modulok fejlesztése és hibakeresése a Visual Studióban](./how-to-visual-studio-develop-module.md)című témakört. Ebben a cikkben létrehozunk egy megoldást a **IoTEdgeSolution** nevű adattárban, amely egy **filtermodule** nevű modul kódját tartalmaz.

   Ehhez a cikkhez mindössze annyit kell tennie, hogy a Visual Studio Code vagy a Visual Studio IoT Edge sablonjai által létrehozott megoldási mappát hozza létre. A továbblépés előtt nem kell ezt a kódot felépíteni, leküldeni, telepíteni vagy hibakeresést végeznie. Ezeket a folyamatokat az Azure-folyamatokban fogja beállítani.

   Ha új megoldást hoz létre, először a tárházat klónozással. Ezután a megoldás létrehozásakor megadhatja, hogy közvetlenül a tárház mappájába hozza létre. Egyszerűen véglegesítheti és leküldheti az új fájlokat.

* Egy tároló-beállításjegyzék, amelyen leküldéses modul képei láthatók. [Azure Container Registry](../container-registry/index.yml) vagy külső gyártótól származó beállításjegyzéket is használhat.
* Egy aktív Azure [IoT hub](../iot-hub/iot-hub-create-through-portal.md) legalább két IoT Edge eszközzel a különböző tesztelési és éles üzembe helyezési fázisok teszteléséhez. Az IoT Edge-eszköz [Linux](quickstart-linux.md) vagy [Windows](quickstart.md) rendszeren való létrehozásához kövesse a rövid útmutató cikkeit

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Build folyamat létrehozása a folyamatos integrációhoz

Ebben a szakaszban egy új Build-folyamatot hoz létre. A folyamat automatikusan fut, ha bejelentkezik a minta IoT Edge a megoldásban történt módosításokat, és közzéteszi a létrehozási naplókat.

1. Jelentkezzen be az Azure DevOps-szervezetbe ( `https://dev.azure.com/{your organization}` ), és nyissa meg a IoT Edge megoldás tárházát tartalmazó projektet.

    ![A DevOps-projekt megnyitása](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. A projekt bal oldali ablaktáblájának menüjében válassza a **folyamatok** elemet. Válassza a **folyamat létrehozása** lehetőséget az oldal közepénél. Ha már rendelkezik Build-folyamatokkal, kattintson a jobb felső sarokban található **új folyamat** gombra.

    ![Új buildfolyamat létrehozása](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. A **Hol található a kód?** oldal alján válassza **a klasszikus szerkesztő használata** lehetőséget. Ha a YAML segítségével szeretné létrehozni a projekt Build folyamatait, tekintse meg a [YAML útmutatót](how-to-continuous-integration-continuous-deployment.md).

    ![Válassza a klasszikus szerkesztő használata lehetőséget.](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. A folyamat létrehozásához kövesse az utasításokat.

   1. Adja meg az új build-folyamat forrásának adatait. Válassza az **Azure Repos git** lehetőséget forrásként, majd válassza ki azt a projektet, tárházat és ágat, ahol a IoT Edge-megoldás kódja található. Ezután válassza a **Folytatás** lehetőséget.

      ![Válassza ki a folyamat forrását](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Sablon helyett válassza az **üres feladatot** .

      ![Kezdjen el egy üres feladatokkal a felépítési folyamathoz](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. A folyamat létrehozása után a rendszer a folyamat-szerkesztőt veszi át. Itt módosíthatja a folyamat nevét, az ügynök készletét és az ügynök specifikációját.

   Kiválaszthat egy Microsoft által üzemeltetett készletet vagy egy Ön által felügyelt saját üzemeltetésű készletet.

   A folyamat leírásában válassza ki a megfelelő ügynök-specifikációt a cél platform alapján:

   * Ha a Linux-tárolók platform amd64-es verziójában szeretné létrehozni a modulokat, válassza az **Ubuntu-16,04**

   * Ha a Windows 1809-tárolók esetében szeretné felépíteni a modulokat a platform amd64-ben, akkor a [Windowsban saját üzemeltetésű ügynököt kell beállítania](/azure/devops/pipelines/agents/v2-windows).

   * Ha a modulokat a platform arm32v7 vagy a arm64 for Linux-tárolók számára szeretné felépíteni, a saját üzemeltetésű [ügynököt Linux rendszeren kell beállítania](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Build-ügynök specifikációjának konfigurálása](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. A folyamat előre konfigurálva van egy **Agent Job 1** nevű feladatokkal. Válassza a pluszjelet ( **+** ) a feladathoz négy feladat hozzáadásához: **Azure IoT Edge** kétszer, egyszer **másolja a fájlokat** , és csak egyszer **tegye közzé a Build** -összetevőket. Keresse meg az egyes feladatokat, és vigye a kurzort a feladat nevére a **Hozzáadás** gomb megjelenítéséhez.

   ![Azure IoT Edge feladat hozzáadása](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Ha mind a négy feladat hozzá lett adva, az ügynök feladata a következő példához hasonlóan néz ki:

   ![Négy feladat az összeállítási folyamatban](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Válassza ki az első **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat a megoldás összes modulját felépíti a megadott megcélzott platformra. Szerkessze a feladatot a következő értékekkel:

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. |
    | Művelet | Válassza a **modul-lemezképek létrehozása** lehetőséget. |
    | Fájl .template.js | Kattintson a három pontra (**..**.), és navigáljon a IoT Edge-megoldást tartalmazó tárházban található fájl **deployment.template.js** . |
    | Alapértelmezett platform | Válassza ki a megfelelő operációs rendszert a modulok számára a célként megadott IoT Edge eszköz alapján. |
    | Kimeneti változók | Adja meg a fájl elérési útjával társítandó hivatkozási nevet, ahol a deployment.jsfájl generál, például **Edge**. |

   Ezek a konfigurációk a képtárat és a fájlban definiált címkét használják a `module.json` modul rendszerképének elnevezéséhez és címkézéséhez. A **Build-modul lemezképei** is segítenek a változók a fájlban megadott pontos értékkel való lecserélésében `module.json` . A Visual Studióban vagy a Visual Studio Code-ban a tényleges értéket kell megadnia egy `.env` fájlban. Az Azure-folyamatokban az értéket a **folyamat változói** lapon állíthatja be. A folyamat-szerkesztő menüben válassza a **változók** fület, és konfigurálja a nevet és az értéket a következő módon:

    * **ACR_ADDRESS**: a Azure Container Registry **bejelentkezési kiszolgáló** értéke. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal.

    Ha a projektben más változók is szerepelnek, megadhatja a név és az érték értéket ezen a lapon. a **Build-modul lemezképei** csak a Format változókat észlelik `${VARIABLE}` . Győződjön meg arról, hogy ezt a formátumot használja a `**/module.json` fájlokban.

8. Válassza ki a második **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat leküldi az összes modul lemezképét a kiválasztott tároló-beállításjegyzékbe.

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. |
    | Művelet | Válassza ki a **leküldéses modul lemezképeit**. |
    | Tároló beállításjegyzékének típusa | Használja az alapértelmezett típust: `Azure Container Registry` . |
    | Azure-előfizetés | Válassza ki az előfizetését. |
    | Azure Container Registry | Válassza ki a modul rendszerképeinek tárolására használt tároló-beállításjegyzék típusát. Attól függően, hogy melyik beállításjegyzék-típust választja, az űrlap megváltoznak. Ha a **Azure Container Registry** lehetőséget választja, a legördülő listák használatával válassza ki az Azure-előfizetést és a tároló-beállításjegyzék nevét. Ha az **általános Container Registry** lehetőséget választja, akkor az **új** elemre kattintva hozzon létre egy beállításjegyzék-szolgáltatáshoz való kapcsolódást. |
    | Fájl .template.js | Kattintson a három pontra (**..**.), és navigáljon a IoT Edge-megoldást tartalmazó tárházban található fájl **deployment.template.js** . |
    | Alapértelmezett platform | Válassza ki a megfelelő operációs rendszert a modulok számára a célként megadott IoT Edge eszköz alapján. |
    | Beállításjegyzékbeli hitelesítő adatok hozzáadása az üzembe helyezési jegyzékhez | Adja meg az igaz értéket a Docker-rendszerképek üzembe helyezési jegyzékbe való küldéséhez szükséges beállításjegyzékbeli hitelesítő adatok hozzáadása |

   Ha több tároló-nyilvántartóval rendelkezik a modul lemezképének üzemeltetéséhez, ezt a feladatot duplikálni kell, válassza a másik tároló-beállításjegyzék lehetőséget, majd a **speciális** beállítások **megkerüléséhez a modul (ok)** megkerüléséhez használja az adott beállításjegyzékhez nem tartozó lemezképeket.

9. A szerkesztéshez válassza a **fájlok másolása** feladatot. Ezzel a feladattal másolhat fájlokat az összetevő-előkészítési könyvtárba.

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | Az alapértelmezett név vagy a Testreszabás használata |
    | Forrás mappája | A másolandó fájlokat tartalmazó mappa. |
    | Tartalom | Adjon hozzá két sort: `deployment.template.json` és `**/module.json` . Ez a két fájl bemenetként szolgál a IoT Edge üzembe helyezési jegyzék létrehozásához. |
    | Célmappa | Határozza meg a változót `$(Build.ArtifactStagingDirectory)` . A leírással kapcsolatos további tudnivalókért lásd: [Build változók](/azure/devops/pipelines/build/variables#build-variables) . |

10. A szerkesztéshez válassza a Build-összetevők **közzététele** feladatot. Adja meg a feladat átmeneti könyvtárának elérési útját a feladathoz, hogy az elérési út közzétehető legyen a kiadási folyamatban.

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | Használja az alapértelmezett nevet vagy a testreszabást. |
    | Közzététel elérési útja | Határozza meg a változót `$(Build.ArtifactStagingDirectory)` . További információ: [Build változók](/azure/devops/pipelines/build/variables#build-variables) . |
    | Összetevő neve | Az alapértelmezett név használata: **drop** |
    | Összetevő közzétételi helye | Az alapértelmezett hely használata: **Azure-folyamatok** |

11. Nyissa meg az **Eseményindítók** lapot, és jelölje be a jelölőnégyzetet a **folyamatos integráció engedélyezéséhez**. Győződjön meg arról, hogy a kódot tartalmazó ág szerepel benne.

    ![Folyamatos integrációs trigger bekapcsolása](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Válassza **a mentés lehetőséget** a **Mentés & üzenetsor** legördülő menüből.

Ez a folyamat most úgy van konfigurálva, hogy automatikusan fusson, amikor új kódot küld a tárházba. Az utolsó feladat, amely közzéteszi a folyamat összetevőit, elindítja a kiadási folyamatokat. Folytassa a következő szakasszal a kiadási folyamat felépítéséhez.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Ha rétegzett üzemelő **példányokat** kíván használni a folyamatában, a rétegzett központi telepítések még nem támogatottak az Azure DevOps Azure IoT Edge feladataiban.
>
>Az [Azure DevOps-ben azonban Azure CLI-feladattal](/azure/devops/pipelines/tasks/deploy/azure-cli) is létrehozhatja az üzembe helyezést rétegzett telepítésként. A **beágyazott parancsfájl** értéke az az [IOT Edge Deployment Create paranccsal](/cli/azure/ext/azure-iot/iot/edge/deployment)végezhető el:
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Következő lépések

* IoT Edge DevOps – ajánlott eljárásokat ismertető példa az [Azure DevOps Starter for IoT Edge](how-to-devops-starter.md)
* A IoT Edge központi telepítésének megismerése az [egyes eszközök IoT Edge központi telepítések megismeréséhez](module-deployment-monitoring.md)
* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-at-scale.md).