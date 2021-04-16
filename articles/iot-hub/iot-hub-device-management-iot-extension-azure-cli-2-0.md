---
title: Azure IoT-eszközkezelés Az Azure CLI-hez és Azure CLI-hez | Microsoft Docs
description: Az Azure CLI IoT-bővítményével Azure IoT Hub eszközkezelést, amely a közvetlen metódusokat és az ikereszköz kívánt tulajdonságainak kezelési lehetőségeit is tartalmaz.
author: chrissie926
manager: ''
keywords: azure iot device management, azure iot hub device management, device management iot, iot hub device management
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: a5bc7e195efd62f430fdf2aa0cb606dbcff79528
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567196"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Az Azure CLI IoT-bővítményének használata Azure IoT Hub eszközkezeléshez

![Végpontok között diagram](media/iot-hub-get-started-e2e-diagram/2.png)

Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI IoT-bővítményét különböző felügyeleti lehetőségekkel a fejlesztői gépen. [Az Azure CLI IoT-bővítménye](https://github.com/Azure/azure-iot-cli-extension) egy nyílt forráskódú IoT-bővítmény, amely kibővíti az [Azure CLI képességeit.](/cli/azure/overview) Az Azure CLI a felügyeleti végpontokkal való Azure Resource Manager parancsokat tartalmaz. Az Azure CLI használatával például létrehozhat egy Azure-beli virtuális gépet vagy egy IoT Hubot. A CLI-bővítményekkel az Azure-szolgáltatások kibővítik az Azure CLI-t, így további szolgáltatásspecifikus képességeket biztosítanak. Az IoT-bővítmény parancssori hozzáférést biztosít az IoT-fejlesztőknek az összes IoT Hub, IoT Edge és IoT Hub Device Provisioning Service képességhez.

| Felügyeleti lehetőség          | Feladat  |
|----------------------------|-----------|
| Közvetlen metódusok             | Az eszközök úgy viselkednek, mint az üzenetek küldésének indítása vagy leállítása, vagy az eszköz újraindítása.                                        |
| Ikertulajdonságok    | Az eszközt bizonyos körülmények között helyezze el, például zöldre állítja a LED-et, vagy 30 percre állítja a telemetria küldési időközét.         |
| Az iker jelentett tulajdonságai   | Lekérte az eszköz jelentett állapotát. Az eszköz például azt jelenti, hogy a LED villog.                                    |
| Ikercímkék                  | Eszközspecifikus metaadatok tárolása a felhőben. Például egyautomata üzembe helyezési helye.                         |
| Ikereszköz-lekérdezések        | Az összes ikereszköz lekérdezése az ikereszközök tetszőleges feltételekkel való lekéréséhez, például a használatra elérhető eszközök azonosításához. |

A lehetőségek használatának különbségeit és útmutatóját [](iot-hub-devguide-d2c-guidance.md) az eszközről a felhőbe való kommunikációra vonatkozó útmutatóban és a felhőből az eszközre való kommunikációra vonatkozó [útmutatóban részletesen le kell tudni magyarázni.](iot-hub-devguide-c2d-guidance.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. IoT Hub az ikereszközt minden egyes hozzá csatlakozó eszköz számára. További információ az ikereszközről: [Ikereszköz – első lépések.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Előfeltételek

* Töltse ki a [Raspberry Pi online szimulátor oktatóanyagát](iot-hub-raspberry-pi-web-simulator-get-started.md) vagy az eszköz egyik oktatóanyagát. A [Raspberry Pi-hoz ](iot-hub-raspberry-pi-kit-node-get-started.md) például használhatja a node.jsvagy a [Telemetria](quickstart-send-telemetry-dotnet.md) küldése gyorsútmutatók valamelyikét. Ezek a cikkek a következő követelményeket fedik le:

  * Aktív Azure-előfizetés.
  * Egy Azure IoT Hub az előfizetés alatt.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT Hubnak.

* Az oktatóanyag során győződjön meg arról, hogy az eszköz az ügyfélalkalmazással együtt fut.

* [Python 2.7x vagy Python 3.x](https://www.python.org/downloads/)

* Az Azure CLI. Ha telepítenie kell, lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli) Az Azure CLI legalább 2.0.70-es vagy újabb verziójának kell lennie. A verziószámot az `az –version` paranccsal ellenőrizheti.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Telepítse az IoT-bővítményt. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. [Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) több módszert is ismertet a bővítmény telepítésére.

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

## <a name="device-twin-desired-properties"></a>Ikereszköz kívánt tulajdonságai

Állítsa be a kívánt tulajdonság interval = 3000 paraméterét a következő parancs futtatásával:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Ez a tulajdonság olvasható az eszközről.

## <a name="device-twin-reported-properties"></a>Az ikereszköz jelentett tulajdonságai

A következő parancs futtatásával lekérte az eszköz jelentett tulajdonságait:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Az ikereszköz által jelentett tulajdonságok egyike a $metadata.$lastUpdated, amely azt mutatja, hogy az eszközalkalmazás mikor frissítette utoljára a jelentett tulajdonságkészletet.

## <a name="device-twin-tags"></a>Ikereszközcímkék

Jelenítse meg az eszköz címkéit és tulajdonságait a következő parancs futtatásával:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adjon hozzá egy mezőszerepet = hőmérséklet&páratartalmat az eszközhöz a következő parancs futtatásával:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Ikereszköz-lekérdezések

A következő parancs futtatásával lekérdezheti a role = 'temperature&humidity' címkével az eszközöket:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

A következő parancs futtatásával az összes eszközt lekérdezheti a role = 'temperature&humidity' címkével nem:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe küldött üzeneteket, és hogyan küldhet felhőből az eszközre üzeneteket az IoT-eszköz és a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]