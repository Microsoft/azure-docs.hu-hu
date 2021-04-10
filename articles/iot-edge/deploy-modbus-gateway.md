---
title: Modbus protokollok fordítása átjárókkal – Azure IoT Edge | Microsoft Docs
description: Modbus TCP-t használó eszközök Azure IoT Hubbal való kommunikációjának engedélyezése egy IoT Edge-átjáróeszköz létrehozásával
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 0388520903e208b3225375d5cee81e8321740a1b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103487734"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-eszközök csatlakoztatása IoT Edge Device Gateway-átjárón keresztül

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Ha Modbus TCP-vagy RTU-protokollt használó IoT-eszközöket szeretne csatlakoztatni egy Azure IoT hub-hoz, használhat egy IoT Edge eszközt átjáróként. Az átjáróeszköz beolvassa az adatokat a Modbus-eszközökből, majd elküldi ezeket az adatokat a felhőbe egy támogatott protokoll használatával.

![Modbus-eszközök csatlakoztatása IoT Hub IoT Edge átjárón keresztül](./media/deploy-modbus-gateway/diagram.png)

Ez a cikk bemutatja, hogyan hozható létre saját tárolórendszerkép egy Modbus-modulhoz (vagy használhat előre létrehozott mintát), és ezután ez hogyan helyezhető üzembe az átjáróként szolgáló IoT Edge-eszközön.

A cikk azt feltételezi, hogy Modbus TCP protokollt használ. További információ arról, hogyan konfigurálható a modul a Modbus RTU támogatásához: a GitHubon a [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projektje.

## <a name="prerequisites"></a>Előfeltételek

* Azure IoT Edge-eszköz. A beállításával kapcsolatban lásd: [Azure IoT Edge telepítése Windows](quickstart.md) vagy [Linux](quickstart-linux.md)rendszeren.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.
* A Modbus TCP-t támogató fizikai vagy szimulált Modbus-eszköz. Ismernie kell az IPv4-címeit.

## <a name="prepare-a-modbus-container"></a>Modbus-tároló előkészítése

Ha tesztelni kívánja a Modbus-átjáró funkcionalitását, a Microsoft rendelkezik egy mintamodullal, amelyet használhat. A modult az Azure Marketplace-en, a [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)-ban vagy a rendszerkép URI-ja segítségével érheti el `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Ha saját maga szeretné létrehozni a modult, és testre szeretné szabni a környezetében, akkor a GitHubon van egy nyílt forráskódú [Azure IoT Edge Modbus modul](https://github.com/Azure/iot-edge-modbus) -projekt. Kövesse a projektben található útmutatót saját tárolórendszerkép létrehozásához. A tárolók rendszerképének létrehozásával kapcsolatban lásd [: C#-modulok fejlesztése a Visual Studióban](./how-to-visual-studio-develop-module.md) , illetve [modulok fejlesztése a Visual Studio Code-ban](how-to-vs-code-develop-module.md). Ezek a cikkek útmutatást nyújtanak az új modulok létrehozásához és a tároló-lemezképek beállításjegyzékbe való közzétételéhez.

## <a name="try-the-solution"></a>Próbálja ki a megoldást

Ez a szakasz végigvezeti a Microsoft minta-Modbus moduljának a IoT Edge eszközön való üzembe helyezésén.

1. Az [Azure Portalon](https://portal.azure.com/) lépjen az IoT hubhoz.

2. Lépjen a **IoT Edgera** , és kattintson a IoT Edge eszközre.

3. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

4. A **IoT Edge-modulok** szakaszban adja hozzá a Modbus modult:

   1. Kattintson a legördülő **listára** , és válassza a **piactér modul** elemet.
   2. Keresse meg `Modbus` és válassza ki a **Modbus TCP-modult** a Microsoft által.
   3. A modul automatikusan konfigurálva van a IoT Hubhoz, és megjelenik a IoT Edge modulok listájában. Az útvonalak is automatikusan konfigurálva vannak. Válassza az **Áttekintés + létrehozás** lehetőséget.
   4. Tekintse át a központi telepítési jegyzéket, és válassza a **Létrehozás** lehetőséget.

5. Válassza ki a Modbus modult, `ModbusTCPModule` majd a listából, és válassza ki a **modul Twin beállítások** lapot. A modulhoz tartozó, dupla kívánt tulajdonságok esetében a szükséges JSON automatikusan ki van töltve.

6. Keresse meg a **SlaveConnection** tulajdonságot a JSON-ban, és állítsa be az értékét a Modbus-eszköz IPv4-címeként.

7. Válassza a **Frissítés** lehetőséget.

8. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a központi telepítést, majd válassza a **Létrehozás** lehetőséget.

9. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Az új `ModbusTCPModule` modulnak a IoT Edge futtatókörnyezettel együtt kell megjelennie.

## <a name="view-data"></a>Adatok megtekintése

A Modbus modulon keresztül érkező adatforgalom megtekintése:

```cmd/sh
iotedge logs modbus
```

Megtekintheti azt is, hogy az eszköz telemetria az [azure IoT hub-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábban Azure IoT Toolkit Extension) használatával küldi el.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni arról, hogy IoT Edge eszközök hogyan működhetnek átjáróként, tekintse meg [az olyan IoT Edge-eszköz létrehozását, amely transzparens átjáróként működik](./how-to-create-transparent-gateway.md).
* További információ a IoT Edge moduljainak működéséről: [Azure IoT Edge modulok megismerése](iot-edge-modules.md).