---
title: Az Azure Percept hang-és beszédfelismerési moduljaival kapcsolatos problémák elhárítása
description: Hibaelhárítási tippek a beléptetési folyamat során észlelt leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a3877ea680e7b4c705f127c54e0fa10c45d3b51d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097975"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Az Azure Percept hang-és beszédfelismerési moduljának hibaelhárítása

Használja az alábbi irányelveket a hangsegéd alkalmazással kapcsolatos problémák elhárításához.

## <a name="collecting-speech-module-logs"></a>Beszédfelismerési modul naplóinak gyűjtése

A parancsok futtatásához [csatlakozzon az Azure PERCEPT DK Wi-Fi hozzáférési ponthoz, és csatlakozzon SSH-kapcsolaton keresztül a fejlesztői csomaghoz](./how-to-ssh-into-percept-dk.md) , és adja meg az SSH-terminálban található parancsokat.

```console
 iotedge logs azureearspeechclientmodule
```

Ha további elemzés céljából át szeretné irányítani a kimenetet egy. txt fájlba, használja a következő szintaxist:

```console
[command] > [file name].txt
```

Ha a kimenetet egy. txt fájlba irányítja át, másolja a fájlt a gazdagép SZÁMÍTÓGÉPére SCP használatával:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[a helyi gazdagép fájljának elérési útja] arra a helyre hivatkozik, amelyre a. txt fájlt át szeretné másolni. [távoli Felhasználónév] a beléptetési [élmény](./quickstart-percept-dk-set-up.md)során kiválasztott SSH-Felhasználónév. Ha nem állított be SSH-bejelentkezést az Azure Percept DK beléptetési felületén, akkor a távoli Felhasználónév a root.

## <a name="checking-runtime-status-of-the-speech-module"></a>A beszédfelismerési modul futtatókörnyezeti állapotának ellenőrzése

Ellenőrizze, hogy **fut**-e a **azureearspeechclientmodule** futásidejű állapota. Az eszköz moduljainak futtatókörnyezeti állapotának megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) , és navigáljon az **összes erőforráshoz**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kattintson a **modulok** lapra az összes telepített modul futtatókörnyezeti állapotának megtekintéséhez.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="A Azure Portal peremhálózati eszköz lapja.":::

Ha a **azureearspeechclientmodule** futásidejű állapota nem **futként** van felsorolva, kattintson a **modulok beállítása**  ->  **azureearspeechclientmodule** elemre. A **modul beállításai** lapon állítsa a **kívánt állapotot** a **Futtatás** elemre, majd kattintson a **frissítés** gombra.

## <a name="understanding-ear-som-led-indicators"></a>A EAR SoM LED-indikátorok ismertetése

A LED-mutatók segítségével megtudhatja, milyen állapotban van az eszköz. Általában körülbelül 2 percet vesz igénybe, hogy a modul teljesen inicializáljon a *bekapcsolás* után. Ahogy halad az inicializálás lépésein, látni fogja a következőket:

1. 1 Közép fehér LED – az eszköz be van kapcsolva. 
2. 1 Közép-fehér LED-alapú villogás – a hitelesítés folyamatban van. 
3. Mindhárom LED kéken változik, ha az eszköz hitelesítése megtörtént, és használatra kész.

|VEZETETT|   LED-állapot|  EAR SoM állapota|
|---|------------|----------------| 
|L02|   1x fehér, statikus bekapcsolás |Bekapcsolás |
|L02|   1x fehér, 0,5 Hz-es villogás|  Hitelesítés folyamatban |
|L01 & L02 & L03|   3x kék, statikus bekapcsolva|     Várakozás a kulcsszóra|
|L01 & L02 & L03|   LED-tömb villogása, 20fps | Figyelés vagy beszéd|
|L01 & L02 & L03|   LED array Racing, 20fps|    Gondolkodás|
|L01 & L02 & L03|   3x piros, statikus bekapcsolva | Némító|

## <a name="next-steps"></a>Következő lépések

Az Azure Percept DK hibaelhárításával kapcsolatos további információkért tekintse meg az [általános hibaelhárítási útmutatót](./troubleshoot-dev-kit.md) .
