---
title: Az Azure-eszközök ikrek használata a MXChip IoT fejlesztői készlet felhasználói LED vezérléséhez | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a fejlesztői készlet-állapotokat, és hogyan vezérelheti a felhasználókat az Azure IoT Hub-eszközökhöz tartozó ikrek használatával.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 8c43c8a0b9fdf30b5ce5ae6ecbf123b563099ff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148784"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Ezt a példát követve figyelheti a MXChip IoT fejlesztői készlet WiFi-információit és az érzékelő állapotát, és szabályozhatja a felhasználó LED-t az Azure IoT Hub-eszközökhöz tartozó ikrek használatával.

## <a name="what-you-learn"></a>Ismertetett témák

- A MXChip IoT fejlesztői készlet-érzékelő állapotának figyelése.

- Az Azure-eszközök ikrek használata a fejlesztői készlet RGB LED-ének színének szabályozására.

## <a name="what-you-need"></a>Amire szükség lesz

- Állítsa be a fejlesztési környezetet az [első lépések útmutatót](./iot-hub-arduino-iot-devkit-az3166-get-started.md)követve.

- A GitBash-terminál ablakban (vagy más git parancssori felületen) írja be a következő parancsokat:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

1. Kattintson a **feladatok** legördülő menüre a Visual Studio Code-ban, és válassza a **feladat futtatása...**  -  lehetőséget. **felhő – kiépítés**.

2. A folyamat megjelenik az **üdvözlő** panel **terminál** lapján.

3. Ha a rendszer rákérdez az üzenetre, *hogy milyen előfizetést szeretne kiválasztani*, válasszon ki egy előfizetést.

4. Válasszon ki vagy válasszon ki egy erőforráscsoportot. 
 
   > [!NOTE]
   > Ha már rendelkezik ingyenes IoT Hub, kihagyhatja ezt a lépést.

5. Ha a rendszer arra kéri, hogy az *IoT hub melyik* üzenetet szeretné kiválasztani, válasszon ki vagy hozzon létre egy IoT hub.

6. A következőhöz hasonló *: Function app Name: xxx*, üzenet jelenik meg. Jegyezze fel a Function alkalmazás nevét; ezt egy későbbi lépésben fogjuk használni.

7. Várjon, amíg befejeződik a Azure Resource Manager-sablon üzembe helyezése, amely akkor jelenik meg, ha az üzenet *Resource Manager-sablon telepítése: kész* felirat látható.

## <a name="deploy-function-app"></a>függvényalkalmazás üzembe helyezése

1. Kattintson a **feladatok** legördülő menüre a Visual Studio Code-ban, és válassza a **feladat futtatása...**  -  lehetőséget. **Felhőbeli üzembe helyezés**.

2. Várjon, amíg befejeződik a Function app Code feltöltési folyamata. megjelenik az üzenetküldési *funkció alkalmazás telepítése: kész* üzenet.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>IoT Hub eszköz-kapcsolatok karakterláncának konfigurálása a fejlesztői készlet-ben

1. Kapcsolódjon a MXChip IoT fejlesztői készlet a számítógéphez.

2. Kattintson a **feladatok** legördülő menüre a Visual Studio Code-ban, és válassza a **feladat futtatása...**  -  lehetőséget. **konfiguráció – eszköz – kapcsolatok**

3. A MXChip IoT fejlesztői készlet kattintson **a gombra,** és tartsa lenyomva a gombot, nyomja le az **Alaphelyzetbe állítás** gombot, majd az **a** gombra kattintva állítsa be a DekKit a konfigurációs módba.

4. Várjon, amíg a rendszer végrehajtja a kapcsolódási karakterlánc konfigurálási folyamatát.

## <a name="upload-arduino-code-to-devkit"></a>Arduino-kód feltöltése a fejlesztői készlet

A MXChip IoT fejlesztői készlet csatlakozik a számítógéphez:

1. Kattintson a Visual Studio Code **feladatok** legördülő menüjére, majd válassza a **Build feladat futtatása...** lehetőséget. Az Arduino-vázlat le van fordítva, és fel van töltve a fejlesztői készlet.

2. A vázlat feltöltése sikeres volt, a *Build & feltöltési vázlat: a sikeres* üzenet jelenik meg.

## <a name="monitor-devkit-state-in-browser"></a>Fejlesztői készlet állapotának figyelése a böngészőben

1. A böngészőben nyissa meg a `DevKitState\web\index.html` fájlt, amelyet a szükséges lépésben hozott létre.

2. A következő weblap jelenik meg:![Adja meg a Function alkalmazás nevét.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Adja meg a korábban írt Function app-nevet.

4. Kattintson a **kapcsolat** gombra

5. Néhány másodpercen belül az oldal frissül, és megjeleníti a fejlesztői készlet WiFi-kapcsolatok állapotát és az egyes bevezetési érzékelők állapotát.

## <a name="control-the-devkits-user-led"></a>A fejlesztői készlet felhasználó által vezetett vezérlésének szabályozása

1. Kattintson a felhasználó által vezetett grafikára a weblap illusztrációján.

2. Néhány másodpercen belül a képernyő frissül, és megjeleníti a felhasználó által vezetett aktuális szín állapotát.

3. Az RGB-csúszka vezérlők különböző helyeire kattintva próbálja meg módosítani az RGB LED színértékét.

## <a name="example-operation"></a>Példa a műveletre

![Példa tesztelési eljárásra](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> A Azure Portal: IoT hub- \> IoT Devices- \> *\<your device\>*  - \> Device Twin-eszközökön láthatók a Twin eszközök nyers adatkészletei.

## <a name="next-steps"></a>Következő lépések

Megtanulta a következőket:
- Csatlakoztasson egy MXChip-IoT fejlesztői készlet-eszközt az Azure IoT távoli figyelési megoldásának gyorsító eszközéhez.
- Használja az Azure IoT-eszköz Twins függvényét az fejlesztői készlet RGB LED-ének színének értelmezésére és szabályozására.

Íme a javasolt következő lépés: az [Azure IoT távoli monitorozási megoldásának gyorsítása – áttekintés](/azure/iot-suite/)