---
title: Helyi IoT Edge tároló használata modulból – Azure IoT Edge | Microsoft Docs
description: Környezeti változók használatával és beállításokkal engedélyezheti a modul IoT Edge helyi tárolóhoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 78752d4da42fe07461ae0e82b10343dc7219ad91
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482058"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Hozzáférés biztosítása modulok számára egy eszköz helyi tárterületéhez

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Amellett, hogy az adatokat Azure Storage-szolgáltatásokkal vagy az eszköz tárolótárolóján tárolja, a jobb megbízhatóság érdekében magának a gazdagépnek a tárolóját is dedikálhatja IoT Edge-eszközön, különösen offline működés esetén.

## <a name="link-module-storage-to-device-storage"></a>Modultároló csatolása eszköztárolóhoz

Ha engedélyezni szeretné a modultárolóból a gazdarendszer tárolójába mutató hivatkozást, hozzon létre egy környezeti változót a modulhoz, amely a tároló egyik tárolómappjére mutat. Ezután a létrehozási beállítások használatával kösse a tárolómappát a gazdagép egyik mappájába.

Ha például engedélyezni szeretné a IoT Edge Hub számára az üzenetek tárolását az eszköz helyi tárolójában, majd később lekérni azokat, konfigurálhatja a környezeti változókat és a létrehozási beállításokat a Azure Portal-ban a Futtatás beállításai **szakaszban.**

1. A IoT Edge és a IoT Edge-ügynökhöz adjon hozzá egy **storageFolder** nevű környezeti változót, amely a modul egy könyvtárára mutat.
1. A IoT Edge és a IoT Edge ügynökhöz is adjon hozzá kötéseket a gazdagép helyi könyvtárának a modul egyik könyvtárához való csatlakoztatásához. Például:

   ![Létrehozási beállítások és környezeti változók hozzáadása helyi tárolóhoz](./media/how-to-access-host-storage-from-module/offline-storage.png)

A helyi tárolót közvetlenül az üzembe helyezési jegyzékben is konfigurálhatja. Például:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
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
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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

Cserélje le a és a helyére a gazdagép és a modul tárolási `<HostStoragePath>` `<ModuleStoragePath>` útvonalát; mindkét értéknek abszolút elérési útnak kell lennie.

Linux rendszeren például az azt jelenti, hogy a `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` **gazdarendszeren az /etc/iotedge/storage** könyvtár az **/iotedge/storage/** könyvtárra van leképezve a tárolóban. Windows rendszeren egy másik példa azt jelenti, hogy a C: temp a gazdarendszeren a `"Binds":["C:\\temp:C:\\contemp"]` **\\ C:** **\\ contemp** könyvtárra van leképezve a tárolóban.

Emellett Linux-eszközökön győződjön meg arról, hogy a modul felhasználói profilja rendelkezik a szükséges olvasási, írási és végrehajtási engedélyekkel a gazdarendszer könyvtárához. Visszatérve a korábbi példához, amely azt IoT Edge, hogy a IoT Edge Hub engedélyezi az üzenetek tárolását az eszköz helyi tárolójában, engedélyeket kell adnunk a felhasználói profiljának(UID 1000). (A IoT Edge ügynök gyökérként működik, így nincs szükség további engedélyekre.) Linux rendszereken többféleképpen is kezelheti a címtárengedélyeket, például a használatával módosíthatja a címtár tulajdonosát, majd módosíthatja az `chown` `chmod` engedélyeket, például:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

A létrehozási lehetőségekről a [Docker-dokumentumokban talál további részleteket.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="encrypted-data-in-module-storage"></a>Titkosított adatok a modultárolóban

Amikor a modulok meghívják a IoT Edge számítási feladat API-ját az adatok titkosításához, a titkosítási kulcs a modul azonosítójával és a modul generációazonosítójával lesz származtatva. A generációazonosítók a titkos kulcsok védelmére használhatók, ha egy modult eltávolítunk az üzemelő példányból, majd később egy másik modult helyezünk üzembe ugyanennek az eszköznek ugyanazokkal a modulazonosítóval. A modulok generációazonosítóját az [az iot hub module-identity show Azure CLI-paranccsal tudja megtekinteni.](/cli/azure/iot/hub/module-identity)

Ha generációk között szeretne fájlokat megosztani a modulok között, azok nem tartalmazhatnak titkos kódokat, vagy a visszafejtése sikertelen lesz.

## <a name="next-steps"></a>Következő lépések

A gazdatároló modulból való elérésére vonatkozó további példáért lásd: Adatok tárolása a peremhálózaton Azure Blob Storage [a IoT Edge.](how-to-store-data-blob.md)
