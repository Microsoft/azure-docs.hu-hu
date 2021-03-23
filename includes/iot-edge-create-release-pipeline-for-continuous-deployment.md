---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104803287"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Kiadási folyamat létrehozása a folyamatos üzembe helyezéshez

Ebben a szakaszban létrehoz egy kiadási folyamatot, amely úgy van beállítva, hogy automatikusan fusson, amikor a build-folyamat elveszíti az összetevőket, és az üzembe helyezési naplókat az Azure-folyamatokban fogja megjeleníteni.

Hozzon létre egy új folyamatot, és adjon hozzá egy új szakaszt:

1. A **kiadások** lapon a **folyamatok** területen válassza az **+ új folyamat** elemet. Ha már rendelkezik kiadási folyamatokkal, kattintson az **+ új** gombra, és válassza az **+ új kiadási folyamat** lehetőséget.  

    ![Kiadási folyamat hozzáadása az + új folyamat gomb használatával](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Amikor a rendszer rákérdez a sablon kiválasztására, válassza az **üres feladatokkal** lehetőséget.

    ![Kezdés a kiadási folyamathoz tartozó üres feladatokkal](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. Az új kiadási folyamat inicializálása egyetlen fázissal, az **1. fázis** néven. Nevezze át az 1. fázist a fejlesztési környezetének folyamatos üzembe helyezési folyamatként **való kezelésére** . A folyamatos üzembe helyezési folyamatok általában több szakaszból állnak **, beleértve a** fejlesztést, az **előkészítést** és a **gyártási** folyamatot. Különböző neveket használhat, és a DevOps gyakorlat alapján többet is létrehozhat. Az Átnevezés után a szakasz részletei ablak bezárásához.

   A kiadási folyamatot a felül található "új kiadási folyamat" szöveg kiválasztásával is átnevezheti.

4. Csatolja a kiadást a build-folyamat által közzétett Build-összetevőkhöz. Kattintson a **Hozzáadás** az összetevők területén elemre.

   ![Kattintson a Hozzáadás elemre a csatoló összetevők területén](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Az **összetevő hozzáadása lapon** válassza a **Létrehozás** a **forrás típusaként** lehetőséget. Válassza ki a projektet és a létrehozott Build-folyamatot. Ha szeretné, megváltoztathatja a **forrás aliast** valami más leíróra. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Az összetevő hozzáadása lapon válassza a Hozzáadás lehetőséget az összetevő létrehozásához.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Nyissa meg az összetevő-eseményindítókat, és válassza ki a váltást a folyamatos üzembe helyezési eseményindító engedélyezéséhez. Most új kiadás jön létre minden alkalommal, amikor új Build áll rendelkezésre.

   ![Az összetevő-eseményindítók és a váltógomb megnyitása a folyamatos üzembe helyezési eseményindító engedélyezéséhez](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. A **fejlesztői** fázis előre konfigurálva van egy feladattal és nulla tevékenységgel. A folyamat menüben válassza a **feladatok** lehetőséget, majd válassza ki a **fejlesztői** szakaszt. Válassza ki az **ügynököt** , és módosítsa a **megjelenítendő nevét** a **QA** értékre. Megadhatja az ügynök feladatával kapcsolatos adatokat, de az üzembe helyezési feladat a platformon nem kényes, így bármilyen **ügynök specifikációt** használhat a kiválasztott **ügynök készletében**.

   ![A fejlesztői fázis feladatainak megtekintése a feladatok lapon](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. A MINŐSÉGBIZTOSÍTÁSi feladatban válassza a pluszjelet ( **+** ) a két feladat hozzáadásához. Keresse meg és adja hozzá a **Azure IoT Edge** kétszer.

9. Válassza ki az első **Azure IoT Edge** feladatot, és konfigurálja a következő értékekkel:

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. |
    | Művelet | Válassza a(z) `Generate deployment manifest` lehetőséget. |
    | Fájl .template.js | Az elérési út megadása: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Az elérési út közzé lett téve a Build-folyamatból. |
    | Alapértelmezett platform | Válassza ki a megfelelő operációs rendszert a modulok számára a célként megadott IoT Edge eszköz alapján. |
    | Kimeneti elérési út| Helyezze el az elérési utat `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Ez az elérési út a végső IoT Edge telepítési jegyzékfájl. |

    Ezek a konfigurációk segítenek lecserélni a modul képurl-címeit a `deployment.template.json` fájlban. Az **üzembe helyezési jegyzék létrehozása** a fájlban megadott pontos értékkel is segíti a változók cseréjét `deployment.template.json` . A VS/VS kódban a tényleges értéket kell megadnia egy `.env` fájlban. Az Azure-folyamatokban a **kiadási folyamat változói** lapon állíthatja be a értéket. Váltson a **változók** lapra, és konfigurálja a nevet és az értéket a következőképpen:

    * **ACR_ADDRESS**: a Azure Container Registry **bejelentkezési kiszolgáló** értéke. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal.
    * **ACR_PASSWORD**: a Azure Container Registry jelszava.
    * **ACR_USER**: a Azure Container Registry felhasználóneve.

    Ha a projektben más változók is szerepelnek, akkor ebben a lapon megadhatja a nevet és az értéket. Az **üzembe helyezési jegyzék előállítása** csak az íz változókat ismeri fel `${VARIABLE}` . Győződjön meg arról, hogy ezt az ízt használja a `*.template.json` fájlokban.
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![Adja meg a kiadási folyamat változóit a változók lapon.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Válassza ki a második **Azure IoT Edge** feladatot, és konfigurálja a következő értékekkel:

    | Paraméter | Leírás |
    | --- | --- |
    | Megjelenített név | A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. |
    | Művelet | Válassza a(z) `Deploy to IoT Edge devices` lehetőséget. |
    | Telepítési fájl | Helyezze el az elérési utat `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Ez az elérési út a fájl IoT Edge telepítési jegyzékfájljának fájlja. |
    | Azure-előfizetés | Válassza ki az előfizetést, amely tartalmazza a IoT Hub.|
    | IoT Hub neve | Válassza ki az IoT hubot.|
    | Egyetlen/több eszköz kiválasztása | Adja meg, hogy a kiadási folyamat egy vagy több eszközre legyen-e telepítve. Ha egyetlen eszközre telepít központilag, adja meg **IoT Edge eszköz azonosítóját**. Ha több eszközre telepít üzembe helyezést, az eszköz **célját** kell megadnia. A célként megadott feltétel egy szűrő, amely a IoT Hub IoT Edge-eszközeinek felel meg. Ha az eszköz címkéit feltételként szeretné használni, frissítenie kell a megfelelő eszközök címkéit IoT Hub-eszköz Twin használatával. Frissítse a **IoT Edge központi telepítési azonosítót** és a **IoT Edge központi telepítési prioritást** a speciális beállítások között. További információ a központi telepítés több eszközhöz való létrehozásáról: [IoT Edge automatikus központi telepítések ismertetése](../articles/iot-edge/module-deployment-monitoring.md). |
    | Eszköz azonosítója vagy a cél állapota | Az előzetes kijelöléstől függően a több eszközre történő központi telepítéshez meg kell adnia egy eszköz-azonosítót vagy egy [célként megadott feltételt](../articles/iot-edge/module-deployment-monitoring.md#target-condition) . |
    | Felsőfokú | A IoT Edge központi telepítési AZONOSÍTÓnál válassza a következőt: `$(System.TeamProject)-$(Release.EnvironmentName)` . Ez a változó a projekt és a kiadás nevét a IoT Edge telepítési azonosítójával képezi le. |
    

    Ha a feladat a nyilvános felhőben nem látható privát Docker-beállításjegyzékben található rendszerképet használ, a  `true` képellenőrzés kihagyása érdekében beállíthatja a SKIP_MODULE_IMAGE_VALIDATION környezeti változót. 

    ![Azure IoT Edge feladatok hozzáadása a fejlesztői fázishoz](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. A **Mentés** gombra kattintva mentheti a módosításokat az új kiadási folyamatba. Térjen vissza a folyamat nézethez a menüből válassza a **folyamat** fület.
