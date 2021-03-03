---
title: Az Azure Percept hang-és beszédfelismerési moduljaival kapcsolatos problémák elhárítása
description: Hibaelhárítási tippek a beléptetési folyamat során észlelt leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679636"
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

[a helyi gazdagép fájljának elérési útja] arra a helyre hivatkozik, amelyre a. txt fájlt át szeretné másolni. [távoli Felhasználónév] a [telepítési élményben](./quickstart-percept-dk-set-up.md)kiválasztott SSH-Felhasználónév. Ha nem állított be SSH-bejelentkezést az OOBE során, a távoli Felhasználónév a root.

## <a name="checking-runtime-status-of-the-speech-module"></a>A beszédfelismerési modul futtatókörnyezeti állapotának ellenőrzése

Ellenőrizze, hogy **fut**-e a **azureearspeechclientmodule** futásidejű állapota. Az eszköz moduljainak futtatókörnyezeti állapotának megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) , és navigáljon az **összes erőforráshoz**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kattintson a **modulok** lapra az összes telepített modul futtatókörnyezeti állapotának megtekintéséhez.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="A Azure Portal peremhálózati eszköz lapja.":::

Ha a **azureearspeechclientmodule** futásidejű állapota nem **futként** van felsorolva, kattintson a **modulok beállítása**  ->  **azureearspeechclientmodule** elemre. A **modul beállításai** lapon állítsa a **kívánt állapotot** a **Futtatás** elemre, majd kattintson a **frissítés** gombra.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Adja meg a modulok képernyőjét a Azure Portalban.":::

## <a name="understanding-ear-som-led-indicators"></a>A EAR SoM LED-indikátorok ismertetése

A LED-mutatók segítségével megtudhatja, milyen állapotban van az eszköz. Általában körülbelül 2 percet vesz igénybe, hogy a modul teljesen inicializáljon a *bekapcsolás* után. Ahogy halad az inicializálás lépésein, látni fogja a következőket:

1. 1 balra zöld fény – az eszköz bekapcsolt állapotú. 
2. 1 balra zöld fény és középre LED villogó zöld-hitelesítés folyamatban van. 
3. Mindhárom LED kéken változik, ha az eszköz hitelesítése megtörtént, és használatra kész.

|LED-állapot                  |EAR SoM állapota            |
|----------------------------|---------------------------|
|1x zöld (bal oldali LED)         |bekapcsolás |
|1x zöld (bal oldali LED) <br> 1x villogó zöld (középső LED) |hitelesítés folyamatban |
|3x kikapcsolva                      |az inicializálás befejeződött |
|3x kék                     |használatra kész |
|3x villogó kék            |kulcsszó ismerhető fel |
|3x Racing Blue              |feldolgozási |
|3x piros                      |némító |

## <a name="next-steps"></a>Következő lépések

Az Azure Percept DK hibaelhárításával kapcsolatos további információkért tekintse meg az [általános hibaelhárítási útmutatót](./troubleshoot-dev-kit.md) .