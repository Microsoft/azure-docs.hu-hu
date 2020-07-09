---
title: IoT Edge eszköz helyi tárterületének használata modulból – Azure IoT Edge | Microsoft Docs
description: Használjon környezeti változókat, és hozzon létre beállításokat a modul IoT Edge eszköz helyi tárolóhoz való hozzáférésének engedélyezéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75434528"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Hozzáférés biztosítása modulok számára egy eszköz helyi tárterületéhez

Az adatok Azure Storage-szolgáltatásokkal vagy az eszköz tároló-tárolóban való tárolása mellett a tárolót is kioszthatja a gazdagépen IoT Edge eszközön is, így a megbízhatóságot, különösen a kapcsolat nélküli üzemmódban.

## <a name="link-module-storage-to-device-storage"></a>Modul tárterületének csatolása az eszköz tárterületéhez

Ha a modul Storage-ból a gazdagépen lévő tárolóra mutató hivatkozást szeretne engedélyezni, hozzon létre egy környezeti változót a modulhoz, amely a tároló egyik tárolási mappájára mutat. Ezt követően a létrehozási beállításokkal társítsa a Storage mappát a gazdaszámítógép egyik mappájához.

Ha például engedélyezni szeretné az IoT Edge hub számára az üzenetek tárolását az eszköz helyi tárolójában, és később lekéri őket, a környezeti változókat és a létrehozási beállításokat a Azure Portal a **futtatókörnyezet beállításai** szakaszban állíthatja be.

1. A IoT Edge hub és a IoT Edge Agent esetében is adjon hozzá egy **storageFolder** nevű környezeti változót, amely a modul egyik könyvtárára mutat.
1. A IoT Edge hub és a IoT Edge ügynök esetében adja hozzá a kötéseket, hogy a gazdagépen lévő helyi könyvtárat összekapcsolja a modul egyik könyvtárába. Például:

   ![Létrehozási beállítások és környezeti változók hozzáadása a helyi tárolóhoz](./media/how-to-access-host-storage-from-module/offline-storage.png)

A helyi tárolót közvetlenül a telepítési jegyzékben is konfigurálhatja. Például:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Cserélje `<HostStoragePath>` le `<ModuleStoragePath>` a és a értéket a gazdagép és a modul tárolási útjára; mindkét értéknek abszolút elérési útnak kell lennie.

A Linux rendszeren például `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` a gazdagép **/etc/iotedge/Storage** lévő könyvtárat a rendszer a tároló **/iotedge/Storage/** képezi le. Egy Windows rendszerű rendszeren, mint egy másik példa, `"Binds":["C:\\temp:C:\\contemp"]` azt jelenti, hogy a gazdagépen a **c: \\ temp** könyvtárat a rendszer a tárolóban lévő **c: \\ ** a következő könyvtárba rendeli.

Emellett a Linux-eszközökön ellenőrizze, hogy a modul felhasználói profilja rendelkezik-e a gazdagép rendszerkönyvtárához szükséges olvasási, írási és végrehajtási engedélyekkel. Visszatérve a korábbi példához, amely lehetővé teszi, hogy IoT Edge hub üzeneteket tároljon az eszköz helyi tárolójában, engedélyeket kell adnia a felhasználói profiljához, az UID 1000-hez. (A IoT Edge ügynök root-ként működik, ezért nincs szükség további engedélyekre.) A Linux rendszereken több módon is kezelhetők a címtár-engedélyek, beleértve a használatával a `chown` címtár tulajdonosának módosítását, majd `chmod` az engedélyek módosítását, például:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

További részleteket a [Docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)szolgáltatásbeli létrehozási lehetőségekről talál.

## <a name="next-steps"></a>További lépések

További példa a gazdagépek egy modulból való elérésére: [adatok tárolása az Azure Blob Storage on IoT Edge](how-to-store-data-blob.md).
