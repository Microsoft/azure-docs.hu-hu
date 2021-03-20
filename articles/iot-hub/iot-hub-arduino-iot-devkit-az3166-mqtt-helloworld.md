---
title: Üzenetek küldése egy MQTT-kiszolgálónak az Azure MQTT ügyféloldali kódtár használatával
description: Ismerje meg, hogyan küldhet üzeneteket egy MQTT-közvetítőnek a MQTT ügyféloldali kódtár használatával. Azt is megtudhatja, hogyan konfigurálhatja a mXChip IoT-fejlesztői készlet MQTT-ügyfélként.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: fb8bf593568825793a1a205a2955599b16fa78cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151763"
---
# <a name="send-messages-to-an-mqtt-server"></a>Üzenetek küldése egy MQTT-kiszolgálónak

A eszközök internetes hálózata-(IoT-) rendszerek gyakran időszakos, gyenge minőségű vagy lassú internetkapcsolattal foglalkoznak. A MQTT egy, a gép és a gép közötti (MESTERi) kapcsolati protokoll, amely az ilyen jellegű kihívásokkal lett kifejlesztve. 

Az itt használt MQTT-ügyfél-függvénytár az [Eclipse PAHO](https://www.eclipse.org/paho/) projekt része, amely API-kat biztosít a MQTT több szállítóeszközön való használatához.

## <a name="what-you-learn"></a>Ismertetett témák

Ebben a projektben a következőket tanulhatja meg:
- A MQTT ügyféloldali kódtár használata üzenetek küldésére egy MQTT-közvetítőnek.
- A MXChip IOT fejlesztői készlet konfigurálása MQTT-ügyfélként.

## <a name="what-you-need"></a>Amire szükség lesz

Fejezze be a [első lépések útmutatót](./iot-hub-arduino-iot-devkit-az3166-get-started.md) a következőre:

* A fejlesztői készlet csatlakoztatva van Wi-Fi
* A fejlesztési környezet előkészítése

## <a name="open-the-project-folder"></a>A Project mappa megnyitása

1. Ha a fejlesztői készlet már csatlakozik a számítógéphez, válassza le azt.

2. Indítsa el a VS Code-ot.

3. Kapcsolódjon a fejlesztői készlet a számítógéphez.

## <a name="open-the-mqttclient-sample"></a>A MQTTClient minta megnyitása

Bontsa ki a bal oldali **ARDUINO-példák** szakaszt, és keresse meg a **PÉLDÁKAT a MXCHIP AZ3166 > MQTT**, és válassza a **MQTTClient** lehetőséget. Megnyílik egy új VS Code ablak a projekt mappájával.

> [!NOTE]
> Megnyithatja például a parancssorból is. `Ctrl+Shift+P`A (MacOS: `Cmd+Shift+P` ) paranccsal nyissa meg a parancssort, írja be az **Arduino** parancsot, majd keresse meg és válassza ki az **Arduino: példák** elemet.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Az Arduino-vázlat felépítése és feltöltése a fejlesztői készlet

`Ctrl+P`A futtatáshoz írja be a (MacOS: `Cmd+P` ) parancsot `task device-upload` . A feltöltés befejezése után a fejlesztői készlet újraindítja és futtatja a vázlatot.

![A képernyőképen egy parancssori ablak jelenik meg, amely feltölti és futtatja az Arduino-vázlatot.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> A "hiba: AZ3166: ismeretlen csomag" hibaüzenet jelenhet meg. Ez a hiba akkor fordul elő, ha a tábla csomagjának indexe nem frissül megfelelően. A hiba megoldásához tekintse meg a [IoT fejlesztői készlet – gyakori kérdések című témakör fejlesztés szakaszát](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

A VS Code-ban kövesse ezt az eljárást a soros figyelő megnyitásához és beállításához:

1. Kattintson az `COM[X]` állapotsorban található szóra a megfelelő com-port beállításához `STMicroelectronics` a ![ következővel: képernyőkép a Visual Studio Code-t jeleníti meg a COM8 S T Micro Electronics elem kiválasztásával.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. A soros figyelő megnyitásához kattintson az állapotsorban található Power plugin ikonra: a ![ képernyőképen látható a kiadás összegzése és a Power dugó ikonja az állapotsorban.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Az állapotsoron kattintson a Baud-arányt jelölő számra, és állítsa be a `115200` ![ következőre: képernyőkép a Bitsebesség beállítását mutatja a Visual Studio Code-ban.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

A soros figyelő megjeleníti a minta vázlat által küldött összes üzenetet. A vázlat csatlakoztatja a fejlesztői készlet a Wi-Fi-hez. Ha a Wi-Fi-kapcsolódás sikeres volt, a vázlat üzenetet küld a MQTT-közvetítőnek. Ezután a minta ismételten két "iot.eclipse.org" üzenetet küld a QoS 0 és a QoS 1 használatával.

![A képernyőfelvételen a vázlat által küldött üzeneteket megjelenítő soros figyelő látható.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, tekintse meg a [IoT fejlesztői készlet kapcsolatos gyakori kérdéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) , vagy kapcsolódjon a következő csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Lásd még

* [A IoT fejlesztői készlet AZ3166 összekötése az Azure IoT Hub a felhőben](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake for a tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan konfigurálhatja a MXChip IOT fejlesztői készlet MQTT-ügyfélként, és az MQTT-ügyfél kódtár használatával küldhet üzeneteket egy MQTT-közvetítőnek, itt látható a következő lépés: az [Azure IOT távoli monitorozási megoldásának gyorsítása – áttekintés](/azure/iot-suite/)