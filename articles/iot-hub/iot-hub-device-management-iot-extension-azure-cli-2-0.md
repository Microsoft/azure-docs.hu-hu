---
title: Azure IoT-eszközkezelés az Azure CLI-hez készült IoT-bővítménnyel | Microsoft Docs
description: Használja a IoT bővítményt az Azure CLI-eszközhöz az Azure IoT Hub-eszközök felügyeletéhez, amely a közvetlen metódusokat és a Twin kívánt tulajdonságok kezelési lehetőségeit is megadja.
author: chrissie926
manager: ''
keywords: Azure IOT-eszközkezelés, Azure IOT hub-eszközkezelés, eszközkezelés IOT, IOT hub-eszközkezelés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 499c0c065cb38313c98ed9412ab1f08d70dbc654
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199526"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Az Azure CLI-hez készült IoT-bővítmény használata az Azure IoT Hub-eszközök felügyeletéhez

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) egy nyílt forráskódú IoT-bővítmény, amely az [Azure CLI](/cli/azure/overview)képességeihez járul hozzá. Az Azure CLI a Azure Resource Manager és a felügyeleti végpontokkal való interakcióra szolgáló parancsokat tartalmaz. Használhatja például az Azure CLI-t egy Azure-beli virtuális gép vagy egy IoT hub létrehozásához. A CLI-bővítmény lehetővé teszi, hogy egy Azure-szolgáltatás kibővítse az Azure CLI-t, így további szolgáltatás-specifikus funkciókhoz férhet hozzá. Az IoT bővítmény a IoT-fejlesztők számára biztosít parancssori hozzáférést az összes IoT Hub, IoT Edge és IoT Hub Device Provisioning Service képességhez.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti lehetőség          | Feladat  |
|----------------------------|-----------|
| Közvetlen metódusok             | Hajtson végre olyan eszközt, amely elindítja vagy leállítja az üzenetek küldését vagy az eszköz újraindítását.                                        |
| Twin kívánt tulajdonságok    | Helyezzen egy eszközt bizonyos állapotba, például állítsa be a LED-et zöldre, vagy állítsa a telemetria küldési intervallumát 30 percre.         |
| Twin jelentett tulajdonságok   | Egy eszköz jelentett állapotának beolvasása. Az eszköz például azt jelenti, hogy a LED azonnal villog.                                    |
| Dupla Címkék                  | Az eszközre jellemző metaadatok tárolása a felhőben. Például egy árusító gép üzembe helyezési helye.                         |
| Eszközök kettős lekérdezései        | Az összes eszköz összes ikrek általi lekérdezése tetszőleges feltételekkel, például a rendelkezésre álló eszközök azonosításával. |

További információ az ilyen beállításokkal kapcsolatos különbségekről és útmutatásról: az [eszközről a felhőbe irányuló kommunikációs útmutató](iot-hub-devguide-d2c-guidance.md) és a [felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. A IoT Hub minden olyan eszközön megtartja a különálló eszközt, amely csatlakozik hozzá. További információ az eszközök Twins-ról: Ismerkedés [az eszközök ikrekkel](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Ismertetett témák

Megtudhatja, hogyan használhatja az Azure CLI-hez készült IoT-bővítményt különböző felügyeleti lehetőségekkel a fejlesztői gépen.

## <a name="what-you-do"></a>Teendők

Futtassa az Azure CLI-t és a IoT-bővítményt az Azure CLI-hez különböző felügyeleti lehetőségekkel.

## <a name="what-you-need"></a>Amire szükség lesz

* Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például a [málna PI és a node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek az elemek a következő követelményeket fedik le:

  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub-nak.

* Az oktatóanyag során győződjön meg arról, hogy az eszközön fut az ügyfélalkalmazás.

* [Python 2.7 x vagy Python 3. x](https://www.python.org/downloads/)

* Az Azure CLI-vel. Ha telepítenie kell, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az –version` paranccsal ellenőrizheti.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Telepítse a IoT bővítményt. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. [Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) több módszert is ismertet a bővítmény telepítésére.

## <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Jelentkezzen be az Azure-fiókjába a következő parancs futtatásával:

```azurecli
az login
```

## <a name="direct-methods"></a>Közvetlen metódusok

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Eszköz Twin kívánt tulajdonságai

A következő parancs futtatásával állítsa be a kívánt tulajdonság intervallumát = 3000:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Ez a tulajdonság olvasható az eszközről.

## <a name="device-twin-reported-properties"></a>Eszköz kettős jelentett tulajdonságai

A következő parancs futtatásával szerezze be az eszköz jelentett tulajdonságait:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Az egyik két jelentett tulajdonság $metadata. $lastUpdated, amely azt mutatja, hogy az eszköz Mikor frissítette a jelentett tulajdonságot.

## <a name="device-twin-tags"></a>Eszköz – Twin Címkék

Jelenítse meg az eszköz címkéit és tulajdonságait a következő parancs futtatásával:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

A következő parancs futtatásával vegyen fel egy Field role = hőmérséklet&páratartalmat az eszközre:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Eszközök kettős lekérdezései

A következő parancs futtatásával kérdezheti le az eszközöket a role = "hőmérséklet&nedvességtartalma" címkével:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

A következő parancs futtatásával lekérdezheti az összes eszközt, kivéve a role = "hőmérséklet&nedvességtartalma" címkével rendelkezőket:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe irányuló üzeneteket, és hogyan küldhet a felhőből az eszközre irányuló üzeneteket a IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]