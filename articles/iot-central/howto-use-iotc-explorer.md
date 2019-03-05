---
title: Az Azure IoT Central Explorer használatával eszközkapcsolatok figyelése
description: Figyelheti az eszközre, és tekintse meg az eszközön ikereszköz megváltoztatja az IoT Central Explorer CLI-n keresztül.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39359054818b81c0f5f2cdc9413ae2a0c1dc8c65
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310267"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Az Azure IoT Central Explorer használatával eszközkapcsolatok figyelése

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Az IoT Central Explorer CLI segítségével megtekintheti az eszközök IoT-központ küldi, és vizsgálja meg az IoT Hub ikereszköz változásai üzeneteket. A nyílt forráskódú eszköz segítségével mélyebb betekintést az eszköz kapcsolati állapotát, és diagnosztizálhatja a problémákat, az eszköz üzenetek nem éri el a felhőben vagy az eszköz nem válaszol az ikereszköz módosításokat.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Látogasson el a iotc-explorer-adattárat a Githubon](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Előfeltételek
+ NODE.js-verzió 8.x vagy újabb - https://nodejs.org
+ Szüksége lesz az alkalmazás a rendszergazdát, hogy a iotc-explorer hozzáférési jogkivonat létrehozása

## <a name="installing-iotc-explorer"></a>Iotc-kezelő telepítése

Futtassa a következő parancsot a parancssorból történő telepítéséhez:

```
npm install -g iotc-explorer
```

> [!NOTE]
> Futtassa a telepítési parancsot a általában kell `sudo` Unix-hoz hasonló környezetekben.

## <a name="running-iotc-explorer"></a>Futó iotc-Explorerrel

Az alábbiakban néhány parancsok és a gyakori beállítások használata esetén futtatható `iotc-explorer`. A teljes körű parancsok és beállítások megtekintéséhez, amelyet továbbíthat `--help` való `iotc-explorer` vagy az alparancsok.

### <a name="login"></a>Bejelentkezés

Mielőtt kezdheti, szüksége lesz az IoT-központ alkalmazás használatra hozzáférési jogkivonatot kapjon a rendszergazda. A rendszergazda hajtja végre az alábbi lépéseket:
1. Lépjen a **felügyeleti/hozzáférési jogkivonatok**. 
1. Válassza ki **készítése**.
![Hozzáférési jogkivonat lap képernyőképe](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Adja meg a jogkivonat nevét, válassza ki **tovább**, és **másolja ki a Token értékét**.
    > [!NOTE]
    > A jogkivonat értéke csak jelenik meg egyszer, a párbeszédpanel bezárása előtt lehet másolni. A párbeszédpanel bezárása, után ez soha nem jelenik meg újra.

    ![Másolja a hozzáférési jogkivonat párbeszédpanel képernyőképe](media/howto-use-iotc-explorer/copyaccesstoken.png)

Ezután használhatja ezt a jogkivonatot, jelentkezzen be a parancssori felület futtatásával:

```sh
iotc-explorer login "<Token value>"
```

Ha inkább nem kell a jogkivonat rendszerhéj előzményekben megőrzött, hagyhatja, hogy a jogkivonat útvonalakat, és Ehelyett adja meg, amikor a rendszer kéri:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>A figyelő eszközüzenetek

Megtekintheti az egy adott eszköz vagy az alkalmazást a lévő összes eszközre érkező üzeneteket a `monitor-messages` parancsot. Ez a figyelő, amely folyamatosan kimeneti új üzeneteket, azok megérkeznek, indítja el.

Tekintse meg az alkalmazás összes eszközt, futtassa a következő parancsot:

```
iotc-explorer monitor-messages
```
Kimenet: ![figyelő-üzenetek parancs kimenete](media/howto-use-iotc-explorer/monitormessages.PNG)

Tekintse meg egy adott eszközre, csak adja hozzá az eszköz azonosítója, a parancs végén:

```
iotc-explorer monitor-messages <your-device-id>
```

A parancskimenet több gép mobilbarát formátum hozzáadásával is lehet a `--raw` lehetőség a következő paranccsal:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Ikereszköz beolvasása

Használhatja a `get-twin` parancsot az IoT-központ eszköz az ikereszköz tartalmának lekérése. Ehhez futtassa a következő parancsot:

```
iotc-explorer get-twin <your-device-id>
```

Kimenet: ![get-twin parancs kimenete](media/howto-use-iotc-explorer/getdevicetwin.PNG)

A `monitor-messages`, egy további gép mobilbarát átadásával kimeneti beszerezheti a `--raw` lehetőséget:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>További lépések
Most, hogy megtanulhatta, hogyan használhatja az IoT Central Explorer, a javasolt következő lépésre megismeréséhez [IoT Central-eszközök kezelésének](howto-manage-devices.md).
