---
title: Az Azure Percept DK és a IoT Edge általános problémáinak elhárítása
description: Hibaelhárítási tippek az Azure Percept DK leggyakoribb problémáira
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c9c62ec07873272b956877ec51d8765ae0bbd100
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605637"
---
# <a name="azure-percept-dk-troubleshooting"></a>Azure Percept DK – hibaelhárítás

Az Azure Percept DK általános hibaelhárítási tippekért tekintse meg az alábbi útmutatást.

## <a name="general-troubleshooting-commands"></a>Általános hibaelhárítási parancsok

A parancsok futtatásához [SSH-t a fejlesztői készletbe](./how-to-ssh-into-percept-dk.md) , és írja be a PARANCSOKAT az SSH-ügyfél parancssorba.

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

```[local host file path]``` arra a helyre hivatkozik a gazdagépen lévő számítógépen, amelyre másolni szeretné a. txt fájlt. ```[remote username]``` a [telepítési élmény](./quickstart-percept-dk-set-up.md)során kiválasztott SSH-Felhasználónév.

További információ a Azure IoT Edge parancsokról: [Azure IoT Edge eszköz hibaelhárítási dokumentációja](../iot-edge/troubleshoot.md).

|Kategória:         |Parancs                    |Függvény                  |
|------------------|----------------------------|---------------------------|
|Operációs rendszer                |```cat /etc/os-release```         |a Mariner-rendszerkép verziójának keresése |
|Operációs rendszer                |```cat /etc/os-subrelease```      |származtatott rendszerkép verziójának bejelölése |
|Operációs rendszer                |```cat /etc/adu-version```        |a ADU verziójának keresése |
|Hőmérséklet       |```cat /sys/class/thermal/thermal_zone0/temp``` |fejlesztői készlet hőmérsékletének ellenõrzése |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |SoftAP-naplók keresése|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |Wi-Fi Services-naplók keresése |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |Wi-Fi Zero Touch kiépítési szolgáltatás naplófájljainak keresése |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |a Mariner hálózati verem naplófájljainak keresése |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |a WiFi hozzáférési pont konfigurációjának részleteinek megtekintése |
|OOBE              |```sudo journalctl -u oobe -b```       |OOBE-naplók keresése |
|Telemetria         |```sudo azure-device-health-id```      |egyedi telemetria megkeresése HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |konfigurációs és kapcsolati ellenőrzések futtatása gyakori problémák esetén |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |Tekintse meg a tároló naplóit, például a beszéd-és a látási modulokat |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |modul-naplók gyűjtése, Azure IoT Edge Security Manager-naplók, tároló-kezelő naplók, ```iotedge check``` JSON-kimenet és egyéb hasznos hibakeresési információk az elmúlt órában |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |a Azure IoT Edge Security Manager naplóinak megtekintése |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |a Azure IoT Edge biztonsági démon újraindítása |
|Azure IoT Edge          |```sudo iotedge list```           |az üzembe helyezett Azure IoT Edge modulok listázása |
|Egyéb             |```df [option] [file]```          |a megadott fájlrendszer (ek) rendelkezésre álló/teljes területére vonatkozó információk megjelenítése |
|Egyéb             |`ip route get 1.1.1.1`        |eszköz IP-címének és felületi adatainak megjelenítése |
|Egyéb             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |csak az eszköz IP-címének megjelenítése |


A ```journalctl``` Wi-Fi parancsok a következő egyetlen paranccsal kombinálhatók:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker hibaelhárítási parancsai

|Parancs                        |Függvény                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[Megjeleníti, hogy mely tárolók futnak](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[Megjeleníti, hogy mely képek vannak az eszközön](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[rendszerkép törlése az eszközről](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[a megadott modul tároló naplóit veszi át](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[az összes lógó kép eltávolítása](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |Docker-tároló letöltési állapotának keresése |

## <a name="usb-updates"></a>USB-frissítések

|Hiba:                                    |Megoldás:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX USB flash-n keresztül a UUU-on keresztül |Ez a hiba az USB-kapcsolat meghibásodásának eredménye a UUU frissítése során. Ha az USB-kábel nincs megfelelően csatlakoztatva a számítógép USB-portjához vagy a Percept DK-szállítói táblához, akkor az űrlap hibát jelez. Próbálja meg leválasztani és újracsatlakoztatni az USB-kábel mindkét végét, és az orsós kábelt a biztonságos kapcsolat biztosítása érdekében. Ez szinte mindig megoldja a problémát. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Az Azure Percept DK Carrier Board VEZETTE állapotai

Három kisméretű LED van a Carrier Board-ház tetején. A LED 1 ikon mellett megjelenik egy Wi-Fi ikon a LED 2 elem mellett, és egy felkiáltójel ikon jelenik meg a LED 3 mellett. Az egyes LED-állapotokkal kapcsolatos információkért tekintse meg az alábbi táblázatot.

|VEZETETT             |Állam      |Leírás                      |
|----------------|-----------|---------------------------------|
|1. LED (IoT Hub) |Bekapcsolva (Solid) |Az eszköz egy IoT Hubhoz van csatlakoztatva. |
|2. LED (Wi-Fi)   |Lassú villogás |Az eszköz készen áll a Wi-Fi Easy-kapcsolat konfigurálására, és bejelenti annak jelenlétét egy konfigurátorban. |
|2. LED (Wi-Fi)   |Gyors villogás |A hitelesítés sikeres volt, az eszköz társítása folyamatban van. |
|2. LED (Wi-Fi)   |Bekapcsolva (Solid) |A hitelesítés és a társítás sikeres volt; az eszköz Wi-Fi hálózathoz csatlakozik. |
|3. LED           |NA         |A LED nem használatban van. |