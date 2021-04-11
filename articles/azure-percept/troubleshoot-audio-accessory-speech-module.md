---
title: Az Azure Percept hang és a beszédfelismerési modul problémáinak elhárítása
description: Hibaelhárítási tippek az Azure Percept hang-és azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605654"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Az Azure Percept hang-és beszédfelismerési moduljának hibaelhárítása

Használja az alábbi irányelveket a hangsegéd alkalmazással kapcsolatos problémák elhárításához.

## <a name="collecting-speech-module-logs"></a>Beszédfelismerési modul naplóinak gyűjtése

A parancsok futtatásához [SSH-t a fejlesztői készletbe](./how-to-ssh-into-percept-dk.md) , és írja be a PARANCSOKAT az SSH-ügyfél parancssorba.

A beszédfelismerési modul naplóinak gyűjtése:

```console
sudo iotedge logs azureearspeechclientmodule
```

Ha további elemzés céljából át szeretné irányítani a kimenetet egy. txt fájlba, használja a következő szintaxist:

```console
sudo [command] > [file name].txt
```

Módosítsa a. txt fájl engedélyeit a másoláshoz:

```console
sudo chmod 666 [file name].txt
```

Ha a kimenetet egy. txt fájlba irányítja át, másolja a fájlt a gazdagép SZÁMÍTÓGÉPére SCP használatával:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[a helyi gazdagép fájljának elérési útja] arra a helyre hivatkozik, amelyre a. txt fájlt át szeretné másolni. [távoli Felhasználónév] a [telepítési élményben](./quickstart-percept-dk-set-up.md)kiválasztott SSH-Felhasználónév.

## <a name="checking-runtime-status-of-the-speech-module"></a>A beszédfelismerési modul futtatókörnyezeti állapotának ellenőrzése

Ellenőrizze, hogy **fut**-e a **azureearspeechclientmodule** futásidejű állapota. Az eszköz moduljainak futtatókörnyezeti állapotának megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) , és keresse meg az **összes erőforrást**  ->  **[az IoT hub]**  ->  **IoT Edge**  ->  **[az eszköz azonosítóját]**. Kattintson a **modulok** lapra az összes telepített modul futtatókörnyezeti állapotának megtekintéséhez.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="A Azure Portal peremhálózati eszköz lapja.":::

Ha a **azureearspeechclientmodule** futásidejű állapota nem **futként** van felsorolva, kattintson a **modulok beállítása**  ->  **azureearspeechclientmodule** elemre. A **modul beállításai** lapon állítsa a **kívánt állapotot** a **Futtatás** elemre, majd kattintson a **frissítés** gombra.

## <a name="understanding-ear-som-led-indicators"></a>A EAR SoM LED-indikátorok ismertetése

A LED-mutatók segítségével megtudhatja, milyen állapotban van az eszköz. Általában körülbelül 2 percet vesz igénybe, hogy a modul teljesen inicializáljon, miután az eszköz bekapcsolta a jogosultságokat. Az inicializálási lépések elvégzése során a következőt fogja látni:

1. Középen fehér LED (statikus): az eszköz be van kapcsolva.
2. Közép-fehér LED bekapcsolva (villogó): a hitelesítés folyamatban van.
3. Mindhárom LED kéken változik, ha az eszköz hitelesítése megtörtént, és használatra kész.

|VEZETETT|LED-állapot|EAR SoM állapota|
|---|---------|--------------|
|L02|1x fehér, statikus bekapcsolás|Bekapcsolás |
|L02|1x fehér, 0,5 Hz-es villogás|Hitelesítés folyamatban |
|L01 & L02 & L03|3x kék, statikus bekapcsolva|Várakozás a kulcsszóra|
|L01 & L02 & L03|LED-tömb villogása, 20fps |Figyelés vagy beszéd|
|L01 & L02 & L03|LED array Racing, 20fps|Gondolkodás|
|L01 & L02 & L03|3x piros, statikus bekapcsolva |Némító|

## <a name="next-steps"></a>Következő lépések

Az Azure Percept DK hibaelhárításával kapcsolatos további információkért tekintse meg az [általános hibaelhárítási útmutatót](./troubleshoot-dev-kit.md) .
