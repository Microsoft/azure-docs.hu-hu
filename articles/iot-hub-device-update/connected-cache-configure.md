---
title: A Microsoft Csatlakoztatott gyorsítótár konfigurálása az eszközfrissítéshez Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: A Microsoft Csatlakoztatott gyorsítótár for Device Update for Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811830"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>A Microsoft Csatlakoztatott gyorsítótár konfigurálása az eszközfrissítéshez Azure IoT Hub

A Microsoft Csatlakoztatott gyorsítótár-átjárókban Azure IoT Edge a Microsoft Azure IoT Edge modulként. A többi Azure IoT Edge- és modulhoz hasonló módon az MCC-modulok üzembe helyezési környezeti változói és tároló-létrehozási beállításai is a Microsoft Csatlakoztatott gyorsítótár konfigurálására.  Ez a szakasz azokat a környezeti változókat és tároló-létrehozási beállításokat határozza meg, amelyek ahhoz szükségesek, hogy az ügyfél sikeresen üzembe helyez egy Microsoft Csatlakoztatott gyorsítótár-modult az eszközfrissítési Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>A Microsoft Csatlakoztatott gyorsítótár Azure IoT Edge modul üzembe helyezésének részletei

A Microsoft Csatlakoztatott gyorsítótár a rendszergazda saját belátása szerint nevezi el. Nincsenek más olyan modul- vagy szolgáltatás-interakciók, amelyek a modul nevére támaszkodnak a kommunikációhoz. Emellett a Microsoft Csatlakoztatott gyorsítótár-kiszolgálók szülő gyermekkapcsolata nem ennek a modulnak a nevétől függ, hanem a korábban említett módon konfigurált Azure IoT Edge-átjáró teljes tartománynevének vagy IP-címének.

A Microsoft Csatlakoztatott gyorsítótár Azure IoT Edge Modulkörnyezet változói alapszintű modulidentitási információkat és funkcionális modulbeállításokat adnak át a tárolónak.

| Változó neve                 | Értékformátum                           | Kötelező vagy nem kötelező | Funkció                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure-előfizetés GUID azonosítója             | Kötelező          | Ez az ügyfél kulcsa, amely biztonságos<br>a gyorsítótár-csomópont hitelesítése Kézbesítésoptimalizálás<br>Szolgáltatások.<br>A modul működéséhez szükséges. |
| CACHE_NODE_ID                 | Gyorsítótár-csomópont guid azonosítója                     | Kötelező          | Egyedileg azonosítja a Microsoft Csatlakoztatott gyorsítótár<br>csomópontot a Kézbesítésoptimalizálás szolgáltatásokhoz.<br>Sorrendben kötelező<br> a modul működéséhez. |
| CUSTOMER_KEY                  | Ügyfélkulcs GUID-ja                     | Kötelező          | Ez az ügyfél kulcsa, amely biztonságos<br>a gyorsítótár-csomópont hitelesítése a Kézbesítésoptimalizálás számára.<br>A modul működéséhez szükséges.|
| STORAGE_ *N _SIZE_GB*           | Ahol N a gyorsítótár meghajtója   | Kötelező          | Legfeljebb 9 meghajtó megadása a tartalom gyorsítótárazása és a maximális lemezterület megadása<br>Gigabájtok lefoglalása az egyes gyorsítótár-meghajtón lévő tartalmak számára. Angol nyelvű Példák:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>A meghajtó számának egyeznie kell a gyorsítótár-meghajtó megadott kötési értékeivel<br>a MicrosoftConnectedCache N *tároló-létrehozási beállításban*<br>A gyorsítótár minimális mérete 10 GB.|
| UPSTREAM_HOST                 | FQDN/IP                                | Választható          | Ez az érték egy microsofthoz csatlakoztatott, fentről csatlakozó kapcsolatot is megadhat<br>Proxyként viselkedő gyorsítótár-csomópont, ha a Csatlakoztatott gyorsítótár csomópont<br> nem csatlakozik az internethez. Ez a beállítás a támogatáshoz használható<br> a beágyazott IoT-forgatókönyv.<br>**Megjegyzés:** A Microsoft Csatlakoztatott gyorsítótár http alapértelmezett 80-as portot figyel.|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | Választható          | A kimenő internetproxy.<br>Ez lehet az OT DMZ-proxy is, ha van ISA 95-hálózat. |
| CACHEABLE_CUSTOM_ *N _HOST*     | GAZDAGÉP/IP<br>FQDN                        | Választható          | Az egyéni csomagtárak támogatásához szükséges.<br>Az adattárak helyileg vagy az interneten is üzemeltetve vannak.<br>A konfigurálható egyéni gazdagépek száma nincs korlátozva.<br><br>Angol nyelvű Példák:<br>Name = CACHEABLE_CUSTOM_1_HOST Value = packages.foo.com<br> Name = CACHEABLE_CUSTOM_2_HOST Value = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N _CANONICAL*| Alias                                  | Választható          | Az egyéni csomagtárak támogatásához szükséges.<br>Ez az érték használható aliasként, és a gyorsítótár-kiszolgáló ezt fogja használni a hivatkozáshoz<br>különböző DNS-nevek. Előfordulhat például, hogy az adattár tartalmának gazdaneve packages.foo.com,<br>de a különböző régiókban lehet egy további előtag is, amely hozzá van adva a gazdagépnévhez<br>például westuscdn.packages.foo.com és eastuscdn.packages.foo.com.<br>A canonical alias beállításával biztosíthatja, hogy a tartalom ne legyen duplikálva<br>az ugyanattól a gazdagéptől, de különböző CDN-forrásokból származó tartalmakhoz.<br>A canonical érték formátuma nem fontos, de egyedinek kell lennie a gazdagépen.<br>Előfordulhat, hogy az értéket a legegyszerűbb úgy beállítani, hogy megfeleljen a gazdagép értékének.<br><br>Példák az egyéni gazdagépre vonatkozó fenti példák alapján:<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = foopackages<br> Name = CACHEABLE_CUSTOM_2_CANONICAL Value = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Igaz vagy hamis                          | Választható          | Lehetővé teszi az összegző jelentés megtekintését a helyi hálózaton vagy az interneten.<br>Ha true (igaz) értékre van állítva, az összefoglaló jelentés megtekintéséhez API-kulcsot kell használnia (ezt később tárgyaljuk). |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Igaz vagy hamis                          | Választható          | Lehetővé teszi az összegző jelentés megtekintését a helyi hálózaton vagy az interneten anélkül<br>API-kulcs használata a hálózat bármely eszközéről. Akkor használja, ha nem szeretné zárolni a hozzáférést<br>a gyorsítótár-kiszolgáló összegző adatainak megtekintéséhez a böngészőben. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>A Microsoft Csatlakoztatott gyorsítótár Azure IoT Edge modultároló létrehozási lehetőségei

Az MCC-modulok üzembe helyezéséhez szükséges tároló-létrehozási lehetőségek biztosítják az MCC-modul által használt tárolókkal és portokkal kapcsolatos beállítások szabályozását. Ez az MCC üzembe helyezéséhez szükséges, tárolók által létrehozott változók listája.

### <a name="container-to-host-os-drive-mappings"></a>Tároló az operációs rendszer meghajtóleképezései számára

A tároló tárolási helyének a disk.< tárolóhelyre való leképezéséhez szükséges. < legfeljebb kilenc hely lehet megadva.

>[!Note]
>A meghajtó számának meg kell egyeznie a gyorsítótár-meghajtó azon kötési értékeivel, amelyek a környezeti változóban STORAGE_ *N* _SIZE_GB értékkel, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Tároló a TCP-portleképezések számára

Ez a beállítás azt a külső gép HTTP-portját adja meg, amely alapján az MCC a tartalomkéréseket figyeli. Az alapértelmezett HostPort a 80-as port, és más portok jelenleg nem támogatottak, mivel az ADU-ügyfél jelenleg a 80-as porton kér kérelmeket. A 8081-es TCP-port az a belső tárolóport, amely felett az MCC figyel, és nem módosítható.

### <a name="container-service-tcp-port-mappings"></a>Tárolószolgáltatás TCP-portleképezései

A Microsoft Csatlakoztatott gyorsítótár modul egy .NET Core szolgáltatással rendelkezik, amelyet a gyorsítótárazó motor használ különböző funkciókhoz.

>[!Note]
>Az Azure IoT Nested Edge támogatásához a HostPortot nem szabad 5000-re állítani, mert a beállításjegyzék proxymodulja már figyel az 5000-es gazdaporton.


Tároló létrehozási lehetőségeinek mintája

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft Csatlakoztatott gyorsítótár összegző jelentés

Jelenleg az összegző jelentés az egyetlen módja annak, hogy az ügyfél megtekintse az átjárókra telepített Microsoft Csatlakoztatott gyorsítótár-példányok gyorsítótárazó Azure IoT Edge adatait. A jelentés 15 másodperces időközönként jön létre, és az időszakra vonatkozó átlagos statisztikákat, valamint a modul élettartama során összesített statisztikákat tartalmaz. A legfontosabb statisztikák, amelyek érdekelni fogják az ügyfeleket:

* **hitBytes** – Ez a közvetlenül a gyorsítótárból származó bájtok száma.
* **missBytes** – Ez az az összeg, amelyet a Microsoft Csatlakoztatott gyorsítótár a CDN-ről kellett letöltenie a gyorsítótárhoz.
* **eggressBytes** – Ez a hitBytes és a missBytes összege, valamint az ügyfeleknek kézbesített összes bájt.
* **hitRatioBytes** – Ez a hitBytes és a egressBytes aránya.  Ha egy időszakban az eggressBájtok 100%-a megegyezik a hitBytes értékekkel, ez például 1 lenne.


Az összegző jelentés a Csere alatt érhető el az átjáró `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` \<Azure IoT Edge Gateway IP\> IP-címére vagy állomásnevére IoT Edge számára. (A jelentés láthatóságával kapcsolatos információkért lásd a környezeti változó részleteit).