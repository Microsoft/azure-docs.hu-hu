---
title: CreateOptions írása modulok számára – Azure IoT Edge | Microsoft Docs
description: Az üzembe helyezési jegyzék createOptions használata a modulok futásidőben történő konfigurálásához
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200618"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Tároló-létrehozási beállítások konfigurálása IoT Edge modulokhoz

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az üzembe helyezési jegyzékben a **createOptions** paraméter lehetővé teszi a modul tárolóinak konfigurálását futásidőben. Ez a paraméter kibővíti a vezérlőket a modulok felett, és lehetővé teszi olyan feladatok végrehajtását, mint például a modul erőforrásaihoz való hozzáférés engedélyezése vagy korlátozása, illetve a hálózatkezelés konfigurálása.

IoT Edge modulokat Docker-kompatibilis tárolóként kell megvalósítani a IoT Edge eszközön. A Docker számos lehetőséget kínál a tárolók létrehozására, és ezek a beállítások a IoT Edge modulokra is érvényesek. További információkért lásd a [Docker-tároló létrehozási beállításait](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Létrehozási beállítások formázása

A IoT Edge üzembe helyezési jegyzékfájl a JSON formátummal formázott létrehozási beállításokat fogadja el. Tegyük fel például, hogy az egyes edgeHub-modulok automatikusan tartalmazzák a létrehozási beállításokat:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Ez a edgeHub-példa a **HostConfig. PortBindings** paraméter használatával képezi le a tárolón lévő, a gazdagépen lévő portokra mutató portot.

Ha a Visual studióhoz vagy a Visual Studio Code-hoz készült Azure IoT Tools-bővítményeket használja, a fájl **deployment.template.jsban** megírhatja a létrehozási beállításokat JSON formátumban. Ezután, amikor a bővítmény használatával felépíti a IoT Edge-megoldást, vagy létrehozza a telepítési jegyzéket, a JSON-t a IoT Edge futtatókörnyezet által várt formátumban fogja stringify. Például:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

A létrehozási beállítások írásához egy tippet kell használni a `docker inspect` parancs használatával. A fejlesztési folyamat részeként futtassa a modult helyileg a használatával `docker run <container name>` . Ha a modul a kívánt módon működik, futtassa a parancsot `docker inspect <container name>` . Ez a parancs JSON formátumban jeleníti meg a modul részleteit. Keresse meg a konfigurált paramétereket, és másolja a JSON-t. Például:

[![A Docker vizsgálatának eredményei edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A tároló-létrehozási lehetőségek számos forgatókönyvet tesznek lehetővé, de a leggyakrabban a IoT Edge megoldások kiépítésekor következik be:

* [Modulok elérésének biztosítása a gazdagép-tárolóhoz](how-to-access-host-storage-from-module.md)
* [Gazdagép portjának hozzárendelése a modul portjához](#map-host-port-to-module-port)
* [A modul memóriájának és CPU-használatának korlátozása](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Gazdagép portjának hozzárendelése a modul portjához

Ha a modulnak a IoT Edge-megoldáson kívüli szolgáltatással kell kommunikálnia, és nem használja az üzenet-útválasztást, akkor egy gazda portot kell leképeznie egy modul-portra.

>[!TIP]
>Ez a port-hozzárendelés nem szükséges a modul – modul típusú kommunikációhoz ugyanazon az eszközön. Ha A modulnak le kell kérdezni egy, a B modulban üzemeltetett API-t, a port leképezése nélkül is megteheti. A B modulnak ki kell mutatnia egy portot a Docker, például: `EXPOSE 8080` . Ezután az A modul lekérdezheti az API-t a B modul neve alapján, például: `http://ModuleB:8080/api` .

Először győződjön meg arról, hogy a modulon belüli port elérhető a kapcsolatok figyelése érdekében. Ezt a Docker teheti [elérhetővé teheti utasítás használatával](https://docs.docker.com/engine/reference/builder/#expose) . Például: `EXPOSE 8080`. Ha nincs megadva, a közzétett utasítás alapértelmezett értéke a TCP protokoll, vagy megadhatja az UDP-t.

Ezután használja a [Docker-tároló létrehozási beállításainak](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) **HostConfig** csoportjában található **PortBindings** beállítást a modulban lévő, a gazdagépen lévő porthoz tartozó port leképezéséhez. Ha például a modulban közzétette a 8080-es portot, és szeretné leképezni a gazdagép-eszköz 80-es portját, a fájl template.jsa létrehozás lehetőség a következő példához hasonlóan fog kinézni:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Az üzembe helyezési jegyzék sztringesített követően ugyanaz a konfiguráció a következő példához hasonlóan fog kinézni:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>A modul memóriájának és CPU-használatának korlátozása

Deklarálhatja, hogy a gazdagép erőforrásai mekkora részét használhatják a modulok. Ez a vezérlő hasznos annak biztosítására, hogy az egyik modul ne fogyasszon túl sok memóriát vagy CPU-használatot, és megakadályozza, hogy más folyamatok fussanak az eszközön. Ezeket a beállításokat a [Docker-tároló létrehozási lehetőségeivel](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) kezelheti a **HostConfig** csoportban, beleértve a következőket:

* **Memória**: a memória maximális mérete bájtban kifejezve. Például 268435456 bájt = 256 MB.
* **MemorySwap**: teljes memória korlátja (memória + felcserélés). Például 536870912 bájt = 512 MB
* **CpuPeriod**: a CPU-időszak hossza a másodpercenként. Az alapértelmezett érték 100000, így például a 25000 értéke korlátozza a tárolót a CPU-erőforrások 25%-ában.

A template.jsformátum esetében ezek az értékek a következő példához hasonlóan néznek ki:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

A végső üzembe helyezési jegyzék sztringesített követően ezek az értékek a következő példához hasonlóan fognak kinézni:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Következő lépések

Ha további példákat szeretne a műveletek létrehozásához, tekintse meg a következő IoT Edge mintákat:

* [Custom Vision és Azure IoT Edge egy málna PI 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [BLOB Storage-minta Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
