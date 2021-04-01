---
title: Térbeli elemzést tartalmazó webalkalmazás üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a térbeli elemzést egy webalkalmazásban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: de011fb0f827ea90efe33e237bbf1c5100dc76a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98183472"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Útmutató: webes alkalmazások leltározása

Ebből a cikkből megtudhatja, hogyan integrálhatja a térbeli elemzést egy olyan webalkalmazásba, amely megérti a személyek mozgását, és figyeli a fizikai helyet foglaló személyek számát. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

* A térbeli elemzési tároló üzembe helyezése
* A művelet és a kamera konfigurálása
* A IoT Hub-kapcsolat konfigurálása a webalkalmazásban
* A webalkalmazás üzembe helyezése és tesztelése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Az Azure IoT Edge telepítési konfigurációk és az [Azure IoT hub](../../iot-hub/index.yml) alapszintű ismerete
* Egy konfigurált [gazdaszámítógép](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>A térbeli elemzési tároló üzembe helyezése

Töltse ki a [kérési alkalmazást](https://aka.ms/csgate) , hogy hozzáférjen a tároló futtatásához. 

A gazdaszámítógép [beállításával](./spatial-analysis-container.md) konfigurálja a gazdaszámítógépet, és csatlakoztasson egy IoT Edge eszközt az Azure IoT hubhoz. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Azure IoT Hub-szolgáltatás üzembe helyezése az előfizetésben

Először hozzon létre egy Azure IoT Hub szolgáltatás egy példányát a standard szintű árképzési szinttel (S1) vagy az ingyenes szinttel (S0). Kövesse ezeket az utasításokat a példány létrehozásához az Azure CLI használatával.

Adja meg a szükséges paramétereket:
* Előfizetés: az Azure-előfizetés neve vagy azonosítója
* Erőforráscsoport: az erőforráscsoport nevének létrehozása
* IOT hub neve: hozza létre a IoT Hub nevét
* IoTHub neve: a létrehozott IoT Hub neve 
* Edge-eszköz neve: hozzon létre egy nevet a peremhálózati eszköz számára

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>A tároló üzembe helyezése Azure IoT Edge számítógépen a gazdaszámítógépen

Telepítse a térbeli elemzési tárolót IoT-modulként a gazdaszámítógépen az Azure CLI használatával. Az üzembe helyezési folyamathoz egy telepítési jegyzékfájl szükséges, amely az üzemelő példányhoz szükséges tárolókat, változókat és konfigurációkat ismerteti. MegAzure Stack találhatja az [Edge-specifikus üzembe helyezési jegyzékfájl](https://go.microsoft.com/fwlink/?linkid=2142179), a [nem Azure stack Edge-specifikus üzembe helyezési jegyzék](https://go.microsoft.com/fwlink/?linkid=2152189), valamint az Azure-beli virtuális gép, amely a [GPU-specifikus üzembe helyezési jegyzékfájlt](https://go.microsoft.com/fwlink/?linkid=2152189) tartalmaz a githubon, amely tartalmazza a *térbeli elemzési* tároló alapszintű telepítési konfigurációját 

Azt is megteheti, hogy a Visual Studio Code-hoz készült Azure IoT-bővítményekkel műveleteket hajt végre az IoT hub használatával. További információért nyissa meg a [Visual Studio Code Azure IoT Edge-modulok üzembe helyezése című](../../iot-edge/how-to-deploy-modules-vscode.md) témakört.

> [!NOTE] 
> A *térbeli analízis – a-Graf* és a *térbeli analízis – diagnosztikai* tárolók nem kötelezőek. Dönthet úgy, hogy eltávolítja őket a fájl *DeploymentManifest.js* . További információ: [telemetria és hibaelhárítási](./spatial-analysis-logging.md) cikk. Három minta *DeploymentManifest.jsa* githubon található fájlokhoz, [Azure stack Edge-eszközökhöz](https://go.microsoft.com/fwlink/?linkid=2142179), [asztali gépekhez](https://go.microsoft.com/fwlink/?linkid=2152189)vagy GPU- [val rendelkező Azure-beli virtuális géphez](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Környezeti változók beállítása

A IoT Edge modul **környezeti változóinak** többsége már be van állítva a minta *DeploymentManifest.jsa* fent hivatkozott fájlokban. A fájlban keresse meg az `BILLING_ENDPOINT` `API_KEY` alábbi ábrán látható és környezeti változókat. Cserélje le az értékeket a végpont URI-ja és a korábban létrehozott API-kulcs helyére. Győződjön meg arról, hogy a végfelhasználói licencszerződés értéke "elfogadás". 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>A műveleti paraméterek konfigurálása

Most, hogy a *térbeli elemzési* tároló kezdeti konfigurációja befejeződött, a következő lépés az operatív paraméterek konfigurálása és a telepítéshez való hozzáadása. 

Első lépésként frissítenie kell a fent hivatkozott minta üzembe helyezési jegyzékfájlt, és konfigurálnia kell a operationId az `cognitiveservices.vision.spatialanalysis-personcount` alábbiak szerint:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Az üzembe helyezési jegyzék frissítése után kövesse a kamera gyártójának utasításait a kamera telepítéséhez, a kamera URL-címének konfigurálásához, valamint a Felhasználónév és a jelszó konfigurálásához. 

Ezután állítsa a `VIDEO_URL` kamera RTSP URL-címét és a kamerához való kapcsolódáshoz szükséges hitelesítő adatokat.

Ha a peremhálózati eszköz egynél több GPU-val rendelkezik, válassza ki azt a GPU-t, amelyen futtatni szeretné a műveletet. Ügyeljen arra, hogy terheléselosztást hajtson végre azokon a műveleteken, amelyeken egyszerre legfeljebb 8 művelet fut egyetlen GPU-ban.  

Ezután konfigurálja azt a zónát, amelyben meg szeretné számolni a személyeket. A zóna sokszögének konfigurálásához először kövesse a gyártó utasításait egy keret a kamerából való lekéréséhez. A sokszög minden csúcspontjának meghatározásához jelöljön ki egy pontot a kereten, a pont x, y képpont koordinátáit állítsa a keret bal oldalára, a szegély felső sarkához viszonyítva, és ossza meg a megfelelő keret méreteivel. Az eredményeket a csúcspont x, y koordinátáiként állíthatja be. Megadhatja a zóna sokszögének konfigurációját a `SPACEANALYTICS_CONFIG` mezőben.

Ez egy minta videó keret, amely bemutatja, hogyan számítja ki a csúcspont koordinátáit a 1920/1080 méretű kerethez.
![Minta videó keret](./media/spatial-analysis/sample-video-frame.jpg)

Kiválaszthat egy megbízhatósági küszöbértéket is, ha a rendszer észleli az észlelt személyeket, és létrehozza az eseményeket. Állítsa 0 értékre a küszöbértéket, ha az összes eseményt kimenetként szeretné megadni.

### <a name="execute-the-deployment"></a>Az üzembe helyezés végrehajtása

Most, hogy befejeződött az üzembe helyezési jegyzék, ezzel a paranccsal az Azure CLI-ben helyezheti üzembe a tárolót a gazdagépen IoT Edge modulként.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Adja meg a szükséges paramétereket:

* IoT Hub neve: az Azure IoT Hub neve
* DeploymentManifest.json: a telepítési fájl neve
* IoT Edge eszköznév: a gazdaszámítógép IoT Edge eszközének neve
* Előfizetés: az előfizetés azonosítója vagy neve

Ez a parancs elindítja az üzemelő példányt, és megtekintheti az üzembe helyezési állapotot az Azure IoT Hub-példányban a Azure Portalban. Az állapot *417-ként jelenhet meg – az eszköz telepítési konfigurációja nincs beállítva* , amíg az eszköz be nem fejeződik a tároló lemezképének letöltése, és nem indul el.

### <a name="validate-that-the-deployment-was-successful"></a>Annak ellenőrzése, hogy a központi telepítés sikeres volt-e

Keresse meg a *futásidejű állapotot* a Azure Portal IoT hub példányának IoT Edge moduljának beállításaiban a térbeli elemzés modulban. A *futásidejű állapot* **kívánt értékének** és **jelentett értékének** a következőnek kell lennie: `Running` . Lásd alább, hogy mit fog kinézni a Azure Portal.

![Példa a központi telepítés ellenőrzésére](./media/spatial-analysis/deployment-verification.png)

Ezen a ponton a térbeli elemzési tároló futtatja a műveletet. AI-megállapításokat bocsát ki a `cognitiveservices.vision.spatialanalysis-personcount` művelethez, és az Azure IoT hub-példányának telemetria átirányítja ezeket az ismereteket. További fényképezőgépek konfigurálásához frissítse az üzembe helyezési jegyzékfájlt, és futtassa újra a telepítést.

## <a name="person-counting-web-application"></a>Személy számlálása webalkalmazás

Ez a személy a webalkalmazások számlálásával gyorsan konfigurálhat egy minta webalkalmazást, és üzemeltetheti azt az Azure-környezetben.

### <a name="get-the-person-counting-app-container"></a>A személy számlálása alkalmazás tárolójának beolvasása

Az alkalmazás tároló űrlapja elérhető a Azure Container Registry. A letöltéshez használja a következő Docker pull-parancsot. Forduljon a Microsofthoz projectarchon@microsoft.com a hozzáférési jogkivonathoz.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Küldje le a tárolót a Azure Container Registryba (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

A tároló telepítéséhez hozzon létre egy új Azure-Web App for Containers, és töltse ki a szükséges paramétereket. Ezután lépjen a **Docker** lapra, és válassza az **egyetlen tároló** lehetőséget, majd **Azure Container Registry**. Használja Azure Container Registry-példányát, ahol a fenti képet leküldte.

![Adja meg a rendszerkép részleteit](./media/spatial-analysis/solution-app-create-screen.png)

A fenti paraméterek megadása után kattintson a **felülvizsgálat + létrehozás** lehetőségre, és hozza létre az alkalmazást.

### <a name="configure-the-app"></a>Az alkalmazás konfigurálása 

Várjon, amíg a telepítés befejeződik, és navigáljon az erőforráshoz a Azure Portal. Nyissa meg a **konfigurációs** szakaszt, és adja hozzá az alábbi két **Alkalmazásbeállítások** közül.

* `EventHubConsumerGroup` – Az Azure-IoT Hub lévő fogyasztói csoport karakterláncának neve, létrehozhat egy új fogyasztói csoportot a IoT Hub, vagy használhatja az alapértelmezett csoportot. 
* `IotHubConnectionString`– Az Azure-IoT Hub kapcsolódási karakterlánca, amely az Azure IoT Hub erőforrás-konfigurálási paramétereinek kulcsok részéből kérhető le. ![](./media/spatial-analysis/solution-app-config-page.png)

A 2 beállítás hozzáadása után kattintson a **Mentés** gombra. Ezután kattintson a **hitelesítés/engedélyezés** elemre a bal oldali navigációs menüben, és frissítse a kívánt hitelesítési szinttel. A Azure Active Directory (Azure AD) Express használatát javasoljuk. 

### <a name="test-the-app"></a>Az alkalmazás tesztelése

Nyissa meg az Azure-webalkalmazást, és ellenőrizze, hogy a telepítés sikeres volt-e, és hogy fut-e a webalkalmazás. Navigáljon a konfigurált URL-címre: `<yourapp>.azurewebsites.net` megtekintheti a futó alkalmazást.

![Az üzemelő példány tesztelése](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>A PersonCount forráskódjának beolvasása
Ha szeretné megtekinteni vagy módosítani az alkalmazás forráskódját, megkeresheti azt [a githubon](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Következő lépések

* [Térbeli elemzési műveletek konfigurálása](./spatial-analysis-operations.md)
* [Naplózás és hibaelhárítás](spatial-analysis-logging.md)
* [Kamera elhelyezése – útmutató](spatial-analysis-camera-placement.md)
* [A zóna és a vonal elhelyezési útmutatója](spatial-analysis-zone-line-placement.md)
