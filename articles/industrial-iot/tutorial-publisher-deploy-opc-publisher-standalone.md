---
title: A Microsoft OPC-közzétevő üzembe helyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe az OPC-közzétevőt önálló módban.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787689"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Oktatóanyag: az OPC-közzétevő üzembe helyezése

Az OPC-közzétevő egy teljes mértékben támogatott Microsoft-termék, amely az iparági eszközök és a Microsoft Azure-felhő közötti szakadék áthidalása. Így az OPC UA-kompatibilis eszközökhöz vagy ipari kapcsolati szoftverekhez való csatlakozással, valamint a telemetria-adatoknak az [Azure-IoT hub](https://azure.microsoft.com/services/iot-hub/) különböző formátumokban való közzétételével, beleértve az IEC62541 OPC ua közzététel standard formátumot (az 2,6-es verziótól kezdve).

Modulként vagy egyszerű Docker-tárolóként [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) fut. Mivel a [.net platformfüggetlen futtatókörnyezetet](https://docs.microsoft.com/dotnet/core/introduction)használja, natív módon fut a Linux és a Windows 10 rendszereken is.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az OPC-közzétevő üzembe helyezése
> * Az OPC-közzétevő legújabb kiadott verziójának futtatása tárolóként
> * A tároló létrehozási beállításainak megadása a Azure Portal

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes próbaverziós fiókot

## <a name="prerequisites"></a>Előfeltételek

- Létre kell hozni egy IoT Hub
- Létre kell hozni egy IoT Edge eszközt

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Az OPC-közzétevő üzembe helyezése az Azure Marketplace-en

1. Válassza ki a használni kívánt Azure-előfizetést. Ha nem áll rendelkezésre Azure-előfizetés, létre kell hoznia egyet.
2. Válassza ki azt IoT Hub, hogy az OPC-közzétevőnek el kellene küldenie az adatküldést. Ha nincs elérhető IoT Hub, egyet kell létrehoznia.
3. Válassza ki azt a IoT Edge eszközt, amelyre az OPC-közzétevőnek futnia kell (vagy adja meg a létrehozandó új IoT Edge eszköz nevét).
4. Kattintson a Létrehozás gombra. Megnyílik a kiválasztott IoT Edge eszközhöz tartozó modulok beállítása lapon.
5. A "OPCPublisher" elemre kattintva nyissa meg az OPC-közzétevő "Update IoT Edge Module" lapját, majd válassza a "tároló-létrehozási beállítások" lehetőséget.
6. Az OPC-közzétevő használata alapján további tároló-létrehozási beállításokat adhat meg, lásd alább a következő szakaszt.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>A Microsoft Container Registry Docker-tárolók manuális elérése az OPC-közzétevőhöz

Az OPC-közzétevő legújabb kiadását manuálisan is futtathatja a használatával:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Ahol a "Name" a tároló neve.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Tároló létrehozási beállításainak megadása a Azure Portal
Ha az OPC-közzétevőt az Azure Portalon keresztül telepíti, a tároló létrehozása lehetőség megadható az OPC-közzétevő frissítési IoT Edge moduljának frissítése lapján. A létrehozási lehetőségeknek JSON formátumúnak kell lenniük. Az OPC-közzétevő parancssori argumentumai a cmd kulcson keresztül adhatók meg, például:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Az OPC-közzétevő IoT Edge modul-tároló létrehozási lehetőségeinek egy tipikus készlete a következő:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Ezen beállítások megadása esetén az OPC-közzétevő beolvassa a konfigurációs fájlt `./pn.json` . Az OPC-közzétevő munkakönyvtára indításra van beállítva, `/appdata` így az OPC-közzétevő beolvassa a fájlt a `/appdata/pn.json` Docker-tárolóján belül. Az OPC-közzétevő naplófájlját a rendszer `/appdata` a `CertificateStores` (OPC ua-tanúsítványokhoz használt) könyvtárba fogja írni, és ebben a könyvtárban is létrejön. Ahhoz, hogy a fájlok elérhetők legyenek a IoT Edge gazdagép fájlrendszerében, a tároló konfigurációjának kötési csatlakoztatási kötetre van szüksége. A `/iiotedge:/appdata` kötés leképezi a könyvtárat a `/appdata` gazdagép könyvtárára `/iiotedge` (amelyet a IoT Edge futtatókörnyezet hoz létre, ha az nem létezik).
**A kötési csatlakoztatási kötet nélkül minden OPC-közzétevő konfigurációs fájl el fog veszni a tároló újraindításakor.**

Egy, a hálózaton konfigurált DNS-kiszolgáló nélkül egy OPC UA-kiszolgálóval létesített kapcsolat a következő `ExtraHosts` szakaszba való bejegyzés hozzáadásával érhető el `HostConfig` :

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Következő lépések 
Most, hogy üzembe helyezte az OPC-közzétevő Edge-modulját, a következő lépés a Konfigurálás:

> [!div class="nextstepaction"]
> [Az OPC-közzétevő konfigurálása](tutorial-publisher-configure-opc-publisher.md)