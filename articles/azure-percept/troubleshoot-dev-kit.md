---
title: Az Azure Percept DK és a IoT Edge általános problémáinak elhárítása
description: Hibaelhárítási tippek a beléptetési folyamat során észlelt leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: c8027b62c0c463e134817f589ba3e1957cea5b39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679576"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Az Azure Percept DK (dev Kit) hibaelhárítása

Az Azure Percept DK általános hibaelhárítási tippekért tekintse meg az alábbi útmutatást.

## <a name="general-troubleshooting-commands"></a>Általános hibaelhárítási parancsok

A parancsok futtatásához 
1. Kapcsolódás a [fejlesztői csomag Wi-Fi AP](./quickstart-percept-dk-set-up.md) -hez
1. [SSH a fejlesztői csomagba](./how-to-ssh-into-percept-dk.md)
1. Adja meg az SSH-terminál parancsait

Ha további elemzés céljából át szeretné irányítani a kimenetet egy. txt fájlba, használja a következő szintaxist:

```console
[command] > [file name].txt
```

Ha a kimenetet egy. txt fájlba irányítja át, másolja a fájlt a gazdagép SZÁMÍTÓGÉPére SCP használatával:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` arra a helyre hivatkozik a gazdagépen lévő számítógépen, amelyre másolni szeretné a. txt fájlt. ```[remote username]``` a [telepítési élmény](./quickstart-percept-dk-set-up.md)során kiválasztott SSH-Felhasználónév. Ha nem állított be SSH-bejelentkezést az OOBE során, a távoli Felhasználónév ```root``` .

További információ a Azure IoT Edge parancsokról: [Azure IoT Edge eszköz hibaelhárítási dokumentációja](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

|Kategória:         |Parancs                    |Függvény                  |
|------------------|----------------------------|---------------------------|
|Operációs rendszer                |```cat /etc/os-release```         |a Mariner-rendszerkép verziójának keresése |
|Operációs rendszer                |```cat /etc/os-subrelease```      |származtatott rendszerkép verziójának bejelölése |
|Operációs rendszer                |```cat /etc/adu-version```        |a ADU verziójának keresése |
|Hőmérséklet       |```cat /sys/class/thermal/thermal_zone0/temp``` |fejlesztői készlet hőmérsékletének ellenõrzése |
|Wi-Fi             |```journalctl -u hostapd.service``` |SoftAP-naplók keresése|
|Wi-Fi             |```journalctl -u wpa_supplicant.service``` |Wi-Fi Services-naplók keresése |
|Wi-Fi             |```journalctl -u ztpd.service```  |Wi-Fi Zero Touch kiépítési szolgáltatás naplófájljainak keresése |
|Wi-Fi             |```journalctl -u systemd-networkd``` |a Mariner hálózati verem naplófájljainak keresése |
|Wi-Fi             |```/data/misc/wifi/hostapd_virtual.conf``` |a WiFi hozzáférési pont konfigurációjának részleteinek megtekintése |
|OOBE              |```journalctl -u oobe -b```       |OOBE-naplók keresése |
|Telemetria         |```azure-device-health-id```      |egyedi telemetria megkeresése HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |konfigurációs és kapcsolati ellenőrzések futtatása gyakori problémák esetén |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |Tekintse meg a tároló naplóit, például a beszéd-és a látási modulokat |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |modul-naplók gyűjtése, Azure IoT Edge Security Manager-naplók, tároló-kezelő naplók, ```iotedge check``` JSON-kimenet és egyéb hasznos hibakeresési információk az elmúlt órában |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |a Azure IoT Edge Security Manager naplóinak megtekintése |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |a Azure IoT Edge biztonsági démon újraindítása |
|Azure IoT Edge          |```sudo iotedge list```           |az üzembe helyezett Azure IoT Edge modulok listázása |
|Egyéb             |```df [option] [file]```          |a megadott fájlrendszer (ek) rendelkezésre álló/teljes területére vonatkozó információk megjelenítése |
|Egyéb             |```ip route get 1.1.1.1```        |eszköz IP-címének és felületi adatainak megjelenítése |
|Egyéb             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |csak az eszköz IP-címének megjelenítése |


A ```journalctl``` Wi-Fi parancsok a következő egyetlen paranccsal kombinálhatók:

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker hibaelhárítási parancsai

|Parancs                        |Függvény                  |
|--------------------------------|---------------------------|
|```docker ps``` |[Megjeleníti, hogy mely tárolók futnak](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[Megjeleníti, hogy mely képek vannak az eszközön](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[rendszerkép törlése az eszközről](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[a megadott modul tároló naplóit veszi át](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[az összes lógó kép eltávolítása](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |Docker-tároló letöltési állapotának keresése |

## <a name="usb-updating"></a>USB-frissítés

|Hiba:                                    |Megoldás:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX USB flash-n keresztül a UUU-on keresztül |Ez a hiba az USB-kapcsolat meghibásodásának eredménye a UUU frissítése során. Ha az USB-kábel nincs megfelelően csatlakoztatva a számítógép USB-portjához vagy a PE-10X-hez, az űrlap egy hibával fog történni. Próbálja meg leválasztani és újracsatlakoztatni az USB-kábel mindkét végét, és az orsós kábelt a biztonságos kapcsolat biztosítása érdekében. Ez szinte mindig megoldja a problémát. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Az Azure Percept DK Carrier Board VEZETTE állapotai

Három kisméretű LED van a Carrier Board-ház tetején. A LED 1 ikon mellett megjelenik egy Wi-Fi ikon a LED 2 elem mellett, és egy felkiáltójel ikon jelenik meg a LED 3 mellett. Az egyes LED-állapotokkal kapcsolatos információkért tekintse meg az alábbi táblázatot.

|VEZETETT             |Állam      |Leírás                      |
|----------------|-----------|---------------------------------|
|1. LED (IoT Hub) |Bekapcsolva (Solid) |Az eszköz egy IoT Hubhoz van csatlakoztatva. |
|2. LED (Wi-Fi)   |Lassú villogás |Az eszköz hitelesítése folyamatban van. |
|2. LED (Wi-Fi)   |Gyors villogás |A hitelesítés sikeres volt, az eszköz társítása folyamatban van. |
|2. LED (Wi-Fi)   |Bekapcsolva (Solid) |A hitelesítés és a társítás sikeres volt; az eszköz Wi-Fi hálózathoz csatlakozik. |
|3. LED           |NA         |A LED nem használatban van. |


