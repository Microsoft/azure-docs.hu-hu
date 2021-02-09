---
title: Oktatóanyag – video Analytics IoT Edge-példány létrehozása az Azure-ban IoT Central (Linux rendszerű virtuális gép)
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Linux rendszerű virtuális gépen a video Analytics-IoT Edge példányt a video Analytics-Object és a Motion Detection alkalmazás sablonnal.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832623"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Oktatóanyag: IoT Edge-példány létrehozása videó-elemzéshez (linuxos virtuális gép)

A Azure IoT Edge egy teljes körűen felügyelt szolgáltatás, amely helyileg biztosítja a felhőalapú intelligenciát az üzembe helyezés és a Futtatás során:

* Egyéni logika
* Azure-szolgáltatások
* Mesterséges intelligencia

IoT Edge ezek a szolgáltatások közvetlenül futnak a platformfüggetlen IoT-eszközökön, így a IoT-megoldás biztonságosan és méretezhetően futtatható a felhőben vagy offline.

Ez az oktatóanyag bemutatja, hogyan készítheti elő IoT Edge-eszközét egy Azure-beli virtuális gépen. A IoT Edge példány futtatja az élő videó elemzési modulokat, amelyeket az Azure IoT Central video Analytics-Object és a Motion Detection alkalmazás sablonja használ.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure-beli virtuális gép létrehozása a telepített Azure IoT Edge futtatókörnyezettel
> * Készítse elő a IoT Edge telepítést az élő videó elemzési moduljának üzemeltetéséhez, és kapcsolódjon a IoT Central

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, végezze el az előző [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) vagy [hozzon létre egy videó-elemzést az Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) oktatóanyagban.

Szüksége lesz egy Azure-előfizetésre is. Ha nem rendelkezik Azure-előfizetéssel, az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription)ingyenesen létrehozhat egyet.

## <a name="deploy-azure-iot-edge"></a>Azure IoT Edge üzembe helyezése

Ha olyan Azure-beli virtuális gépet szeretne létrehozni, amelyen telepítve van a legújabb IoT Edge futtatókörnyezet és az élő video Analytics-modulok, válassza a következő gombot:

[![Az iotedge-vm-deploy sablon Üzembe helyezés az Azure-ban gombja](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

A következő táblázatban található információk segítségével fejezze be az **Egyéni központi telepítési** űrlapot:

| Mező | Érték |
| ----- | ----- |
| Előfizetés | Válassza ki Azure-előfizetését. |
| Erőforráscsoport | *LVA – RG* – az előző oktatóanyagban létrehozott erőforráscsoport. |
| Region       | *USA keleti régiója* |
| DNS-címke előtagja | Válasszon egyedi DNS-előtagot a virtuális géphez. Csak betűkből és betűkből állhat, és nem tartalmazhat számjegyeket vagy speciális karaktereket. |
| Rendszergazdai Felhasználónév | *AzureUser* |
| Rendszergazdai jelszó | Adjon meg egy jelszót. Jegyezze fel a jelszót a *scratchpad.txt* fájlban, amelyet később használni fog. |
| Hatókör-azonosító | A **Hatókör-azonosító** , amelyet az előző oktatóanyag *scratchpad.txt* fájljában adott meg, az átjáró-eszköz hozzáadásakor. |
| Eszközazonosító | *Gateway-001* – az előző oktatóanyagban létrehozott átjáró eszköz. |
| Eszköz kulcsa | Az **eszköz elsődleges kulcsa** az átjáró eszköz hozzáadásakor az előző oktatóanyag *scratchpad.txt* fájljában található. |
| IOT központi alkalmazás-gazdagép | Az **alkalmazás URL-címe** , amelyet az előző oktatóanyag *scratchpad.txt* fájljában jegyzett készített. Például: *Traders.azureiotcentral.com*. |
| IOT központi alkalmazás API-tokenje | A **kezelő API-tokent** , amelyet az előző oktatóanyag *scratchpad.txt* fájljában jegyzett készített. |
| IOT központi eszköz kiépítési kulcsa | Az **sas-IoT-Devices csoport elsődleges kulcsát** az előző oktatóanyag *scratchpad.txt* fájljában jegyezze fel. |
| Virtuális gép mérete | *Standard_DS1_v2* |
| Ubuntu operációs rendszer verziója | *18,04 – LTS* |
| Hely | *[resourceGroup (). location]* |

Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejezésekor válassza a **Létrehozás** lehetőséget. A telepítés befejezéséhez általában három percet vesz igénybe. Az üzembe helyezés befejezésekor navigáljon a Azure Portal **LVA-RG** erőforráscsoporthoz.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Győződjön meg arról, hogy a IoT Edge futtatókörnyezet betölti a modulokat

A Azure Portal navigáljon a **LVA-RG** erőforráscsoporthoz, és válassza ki a virtuális gépet. Ezután a **támogatás + hibaelhárítás** szakaszban válassza a **Serial Console** lehetőséget.

Az **ENTER** billentyű lenyomásával kérheti le `login:` . A virtuális gép létrehozásakor használt felhasználónévvel és jelszóval válassza a *azureuser* -t.

Futtassa a következő parancsot a IoT Edge futtatókörnyezet verziójának vizsgálatához. Az írás időpontjában a verzió 1.0.9:

```bash
sudo iotedge --version
```

Sorolja fel IoT Edge moduljait a parancs használatával:

```bash
sudo iotedge list
```

A központi telepítés a következő öt IoT Edge modul futtatását állította be:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

Az üzembe helyezés során létrehozott egy egyéni IoT Edge-környezetet az élő videó elemzéséhez szükséges modulokkal. A központi telepítés az alapértelmezett **config. YAML** frissítette, hogy a IoT Edge Runtime a IoT-eszköz kiépítési szolgáltatását használta a IoT Centralhoz való kapcsolódáshoz. Az üzembe helyezés egy **state.js** nevű fájlt is létrehozott a **/Data/Storage** mappában a modulok további konfigurációs információinak megadásához. További információ: [IoT Edge-példány létrehozása a video Analytics (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) oktatóanyaghoz.

A IoT Edge eszköz hibáinak megoldásához tekintse [meg a IoT Edge eszköz hibáinak megoldása](../../iot-edge/troubleshoot.md) című témakört.

## <a name="use-the-rtsp-simulator"></a>Az RTSP-szimulátor használata

Ha nem rendelkezik valódi kamera-eszközökkel a IoT Edge eszközhöz való kapcsolódáshoz, a video Analytics-alkalmazás sablonjában a két szimulált kamera eszközt használhatja. Ez a szakasz bemutatja, hogyan használható szimulált videó stream a IoT Edge eszközön.

Ezek az utasítások a [Live555 Media Servert](http://www.live555.com/mediaServer/) használják egy Docker-tárolóban lévő RTSP-szimulátorként.

> [!NOTE]
> A jelen tárházban található, harmadik féltől származó szoftverekre mutató hivatkozások csak tájékoztató jellegűek és kényelmi célokat szolgálnak. A Microsoft nem támogatja a harmadik féltől származó szoftverre vonatkozó jogosultságokat. További információ: [Live555 Media Server](http://www.live555.com/mediaServer/).

A következő parancs használatával futtassa a **rtspvideo** segédprogramot a IoT Edge virtuális gépen található Docker-tárolóban. A Docker-tároló egy háttérben futó RTSP streamet hoz létre:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

A Docker-tárolók listázásához használja az alábbi parancsot:

```bash
sudo docker ps
```

A lista egy **LIVE555** nevű tárolót tartalmaz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következőképpen távolíthatja el az összes létrehozott erőforrást:

1. A IoT Central alkalmazásban navigáljon az **alkalmazás** lapjára az **Adminisztráció** szakaszban. Ezután válassza a **Törlés** elemet.
1. A Azure Portal törölje a **LVA-RG** erőforráscsoportot.
1. A helyi gépen állítsa le az **amp-Viewer** Docker-tárolót.

## <a name="next-steps"></a>Következő lépések

Most telepítette a IoT Edge futtatókörnyezetet, a LVA modulokat és a Live555 szimulációs streamet egy Azure-on futó linuxos virtuális gépen.

A kamerák kezeléséhez kövesse a következő oktatóanyagot

> [!div class="nextstepaction"]
> [Video Analytics-objektum-és mozgásészlelési alkalmazás monitorozása és kezelése](./tutorial-video-analytics-manage.md)