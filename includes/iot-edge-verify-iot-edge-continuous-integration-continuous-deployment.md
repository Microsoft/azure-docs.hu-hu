---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104803477"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD ellenőrzése a létrehozási és kiadási folyamatokkal

A létrehozási feladatok elindításához leküldheti a véglegesítést a forráskód-adattárba, vagy manuálisan is aktiválhatja azt. Ebben a szakaszban manuálisan indítja el a CI/CD folyamatot a működésének ellenőrzéséhez. Ezután ellenőrizze, hogy az üzembe helyezés sikeres volt-e.

1. A bal oldali ablaktábla menüjében válassza a **folyamatok** lehetőséget, majd nyissa meg a cikk elején létrehozott Build folyamatot.

2. A létrehozási folyamat során a jobb felső sarokban található **folyamat futtatása** gombra kattintva aktiválhat egy felépítési feladatot.

    ![A létrehozási folyamat manuális elindítása a folyamat futtatása gomb használatával](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Tekintse át a **folyamat futtatása** beállításait. Ezután válassza a **Futtatás** lehetőséget.

    ![A futtatási folyamat beállításainak megadása és a Futtatás kiválasztása](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. A Futtatás előrehaladásának megtekintéséhez válassza az **1. ügynök feladata** lehetőséget. A feladatokra kattintva áttekintheti a feladatok kimenetének naplóit. 

    ![Tekintse át a feladatok naplójának kimenetét](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Ha az összeállítási folyamat sikeresen befejeződött, elindítja a kiadást a **fejlesztői** fázisba. A sikeres **fejlesztői** kiadás IoT Edge központi telepítést hoz létre IoT Edge eszközök megcélzásához.

    ![Kiadás a dev-be](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. A kiadási naplók megjelenítéséhez kattintson a **fejlesztői** fázis elemre.

    ![Kiadási naplók](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. Ha a folyamat meghiúsul, először tekintse meg a naplókat. A naplók megtekintéséhez navigáljon a folyamat futtatása összegzésre, és válassza ki a feladatot és a feladatot. Ha egy bizonyos feladat meghiúsul, ellenőrizze a feladat naplóit. A naplók konfigurálásával és használatával kapcsolatos részletes utasításokért lásd: [naplók áttekintése a folyamatok diagnosztizálásához](/azure/devops/pipelines/troubleshooting/review-logs).
