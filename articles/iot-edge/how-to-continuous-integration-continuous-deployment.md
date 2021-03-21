---
title: Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge eszközökön – Azure IoT Edge
description: Folyamatos integráció és folyamatos üzembe helyezés beállítása az Azure DevOps, Azure-folyamatokkal rendelkező YAML-Azure IoT Edge használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9db9997af20fee226214eb12ad32729cab55caa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199254"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge eszközökön

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure-folyamatok beépített Azure IoT Edge feladataival könnyedén elvégezheti a DevOps a Azure IoT Edge alkalmazásaiban. Ez a cikk bemutatja, hogyan használhatja az Azure-folyamatok folyamatos integrációját és folyamatos üzembe helyezését az alkalmazások gyors és hatékony üzembe helyezéséhez, teszteléséhez és központi telepítéséhez a Azure IoT Edge YAML használatával. Másik lehetőségként [használhatja a klasszikus szerkesztőt](how-to-continuous-integration-continuous-deployment-classic.md)is.

![Diagram – CI és CD ágak fejlesztési és termelési célokra](./media/how-to-continuous-integration-continuous-deployment/model.png)

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-folyamatok beépített [Azure IoT Edge feladatait](/azure/devops/pipelines/tasks/build/azure-iot-edge) a IoT Edge-megoldáshoz tartozó Build és kiadási folyamatok létrehozásához. A folyamathoz hozzáadott minden Azure IoT Edge feladat a következő négy művelet egyikét valósítja meg:

 | Művelet | Leírás |
 | --- | --- |
 | Modul lemezképének összeállítása | A IoT Edge megoldás kódját hozza létre, és létrehozza a tároló lemezképeit.|
 | Leküldéses modul képei | Leküldi a modul rendszerképeit a megadott tároló-beállításjegyzékbe. |
 | Üzembe helyezési jegyzék előállítása | deployment.template.jsa fájlra és a változókra, majd létrehozza a végső IoT Edge telepítési jegyzékfájlt. |
 | Üzembe helyezés IoT Edge-eszközökön | IoT Edge központi telepítéseket hoz létre egy vagy több IoT Edge eszközre. |

Ha másként nincs megadva, az ebben a cikkben ismertetett eljárások nem tárgyalják a feladat paramétereinek használatával elérhető funkciókat. További információkat a következő cikkekben talál:

* [Feladat verziója](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Speciális** – ha alkalmazható, olyan modulokat adhat meg, amelyeket nem szeretne felépíteni.
* [Vezérlési beállítások](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Környezeti változók](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Kimeneti változók](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Repos-tárház. Ha még nem rendelkezik ilyennel, [létrehozhat egy új git-tárházat a projektben](/azure/devops/repos/git/create-new-repo). Ebben a cikkben egy **IoTEdgeRepo** nevű tárházat hoztunk létre.
* Egy IoT Edge-megoldás véglegesítve lett, és leküldve a tárházba. Ha a cikk teszteléséhez új mintavételi megoldást szeretne létrehozni, kövesse a [modulok fejlesztése és hibakeresése a Visual Studio Code](how-to-vs-code-develop-module.md) -ban című témakör lépéseit, illetve [C#-modulok fejlesztése és hibakeresése a Visual Studióban](./how-to-visual-studio-develop-module.md)című témakört. Ebben a cikkben létrehozunk egy megoldást a **IoTEdgeSolution** nevű adattárban, amely egy **filtermodule** nevű modul kódját tartalmaz.

   Ehhez a cikkhez mindössze annyit kell tennie, hogy a Visual Studio Code vagy a Visual Studio IoT Edge sablonjai által létrehozott megoldási mappát hozza létre. A továbblépés előtt nem kell ezt a kódot felépíteni, leküldeni, telepíteni vagy hibakeresést végeznie. Ezeket a folyamatokat az Azure-folyamatokban fogja beállítani.

   Ha új megoldást hoz létre, először a tárházat klónozással. Ezután a megoldás létrehozásakor megadhatja, hogy közvetlenül a tárház mappájába hozza létre. Egyszerűen véglegesítheti és leküldheti az új fájlokat.

* Egy tároló-beállításjegyzék, amelyen leküldéses modul képei láthatók. [Azure Container Registry](../container-registry/index.yml) vagy külső gyártótól származó beállításjegyzéket is használhat.
* Egy aktív Azure [IoT hub](../iot-hub/iot-hub-create-through-portal.md) legalább két IoT Edge eszközzel a különböző tesztelési és éles üzembe helyezési fázisok teszteléséhez. Az IoT Edge-eszköz [Linux](quickstart-linux.md) vagy [Windows](quickstart.md) rendszeren való létrehozásához kövesse a rövid útmutató cikkeit

További információ az Azure Repos használatáról: [kód megosztása a Visual Studióval és az Azure repostel](/azure/devops/repos/git/share-your-code-in-git-vs)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Build folyamat létrehozása a folyamatos integrációhoz

Ebben a szakaszban egy új Build-folyamatot hoz létre. A folyamat automatikusan fut, ha bejelentkezik a minta IoT Edge a megoldásban történt módosításokat, és közzéteszi a létrehozási naplókat.

1. Jelentkezzen be az Azure DevOps-szervezetbe ( `https://dev.azure.com/{your organization}` ), és nyissa meg a IoT Edge megoldás tárházát tartalmazó projektet.

   ![A DevOps-projekt megnyitása](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. A projekt bal oldali ablaktáblájának menüjében válassza a **folyamatok** elemet. Válassza a **folyamat létrehozása** lehetőséget az oldal közepénél. Ha már rendelkezik Build-folyamatokkal, kattintson a jobb felső sarokban található **új folyamat** gombra.

    ![Új létrehozási folyamat létrehozása az új folyamat gomb használatával](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. A **Hol található a kód?** lapon válassza az **Azure Repos git `YAML`** lehetőséget. Ha a klasszikus szerkesztővel szeretné létrehozni a projekt összeállítási folyamatait, tekintse meg a [klasszikus szerkesztési útmutatót](how-to-continuous-integration-continuous-deployment-classic.md).

4. Válassza ki azt a tárházat, amelyhez folyamatot hoz létre.

    ![Válassza ki a felépítési folyamat tárházát](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. A **folyamat konfigurálása** lapon válassza a **kezdő folyamat** lehetőséget. Ha már rendelkezik egy meglévő Azure-folyamattal, amelyet a folyamat létrehozásához kíván használni, kiválaszthatja a **meglévő Azure-folyamatok YAML-fájlját** , és megadhatja a tárházban található ágat és elérési utat a fájl YAML.

    ![Válassza a kezdő folyamat vagy a meglévő Azure-folyamatok YAML-fájl lehetőséget a létrehozási folyamat megkezdéséhez](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. A folyamat **YAML áttekintése** lapon az alapértelmezett névre kattintva `azure-pipelines.yml` átnevezheti a folyamat konfigurációs fájlját.

   Válassza a **Segéd megjelenítése** lehetőséget a **feladatok** paletta megnyitásához.

    ![Válassza a Segéd megjelenítése lehetőséget a feladatok paletta megnyitásához](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Feladat hozzáadásához vigye a kurzort a YAML végére, vagy bárhová szeretné felvenni a feladathoz tartozó utasításokat. Keresse meg és válassza ki a **Azure IoT Edge**. Adja meg a feladat paramétereit az alábbiak szerint. Ezután válassza a **Hozzáadás** lehetőséget.

   | Paraméter | Leírás |
   | --- | --- |
   | Művelet | Válassza a **modul-lemezképek létrehozása** lehetőséget. |
   | Fájl .template.js | Adja meg a IoT Edge-megoldást tartalmazó tárházban található fájl **deployment.template.js** elérési útját. |
   | Alapértelmezett platform | Válassza ki a megfelelő operációs rendszert a modulok számára a célként megadott IoT Edge eszköz alapján. |

    ![Feladatok hozzáadása a folyamathoz a feladatok paletta használatával](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Az egyes feladatok hozzáadása után a szerkesztő automatikusan kiemeli a hozzáadott sorokat. A véletlen felülírás megelőzése érdekében törölje a sorokat, és adjon meg egy új helyet a következő feladathoz, mielőtt további feladatokat adna hozzá.

8. Ismételje meg ezt a folyamatot, ha három további feladatot szeretne hozzáadni a következő paraméterekkel:

   * Feladat: **Azure IoT Edge**

       | Paraméter | Leírás |
       | --- | --- |
       | Művelet | Válassza ki a **leküldéses modul lemezképeit**. |
       | Tároló beállításjegyzékének típusa | Használja az alapértelmezett típust: **Azure Container Registry**. |
       | Azure-előfizetés | Válassza ki előfizetését. |
       | Azure Container Registry | Válassza ki a folyamathoz használni kívánt beállításjegyzéket. |
       | Fájl .template.js | Adja meg a IoT Edge-megoldást tartalmazó tárházban található fájl **deployment.template.js** elérési útját. |
       | Alapértelmezett platform | Válassza ki a megfelelő operációs rendszert a modulok számára a célként megadott IoT Edge eszköz alapján. |

   * Feladat: **fájlok másolása**

       | Paraméter | Leírás |
       | --- | --- |
       | Forrás mappája | A másolandó forrás mappája. Az üres érték a tárház gyökere. Használjon változókat, ha a fájlok nincsenek a tárházban. Példa: `$(agent.builddirectory)`.
       | Tartalom | Adjon hozzá két sort: `deployment.template.json` és `**/module.json` . |
       | Célmappa | Határozza meg a változót `$(Build.ArtifactStagingDirectory)` . A leírással kapcsolatos további tudnivalókért lásd: [Build változók](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) . |

   * Feladat: **Build** -összetevők közzététele

       | Paraméter | Leírás |
       | --- | --- |
       | Közzététel elérési útja | Határozza meg a változót `$(Build.ArtifactStagingDirectory)` . A leírással kapcsolatos további tudnivalókért lásd: [Build változók](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) . |
       | Összetevő neve | Adja meg az alapértelmezett nevet: `drop` |
       | Összetevő közzétételi helye | Használja az alapértelmezett helyet: `Azure Pipelines` |

9. Kattintson a Save ( **Mentés** ) gombra a **Mentés és Futtatás** legördülő menüből a jobb felső sarokban.

10. A folyamatos integráció triggere alapértelmezés szerint engedélyezve van a YAML folyamat számára. Ha szerkeszteni szeretné ezeket a beállításokat, válassza ki a folyamatot, és kattintson a jobb felső sarokban található **Szerkesztés** gombra. Válassza a jobb felső sarokban található **Futtatás** gomb melletti **További műveletek** elemet, és lépjen az **Eseményindítók** elemre. A **folyamatos integráció** a folyamat neve alatt engedélyezettként jelenik meg. Ha meg szeretné tekinteni az trigger részleteit, jelölje be a **YAML folyamatos integrációs trigger felülbírálása itt** jelölőnégyzetet.

    ![A folyamat eseményindító-beállításainak áttekintését lásd: triggerek további műveletek alatt](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Folytassa a következő szakasszal a kiadási folyamat felépítéséhez.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Következő lépések

* IoT Edge DevOps – ajánlott eljárásokat ismertető példa az [Azure DevOps Starter for IoT Edge](how-to-devops-starter.md)
* A IoT Edge központi telepítésének megismerése az [egyes eszközök IoT Edge központi telepítések megismeréséhez](module-deployment-monitoring.md)
* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-at-scale.md).