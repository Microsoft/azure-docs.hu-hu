---
title: A Microsoft csatlakoztatott gyorsítótárának konfigurálása az Azure IoT Hub eszköz frissítéséhez | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Az Azure-beli eszköz-frissítéshez készült Microsoft csatlakoztatott gyorsítótár áttekintése IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662468"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>A Microsoft csatlakoztatott gyorsítótárának konfigurálása az Azure-beli eszközök frissítéséhez IoT Hub

A Microsoft csatlakoztatott gyorsítótár Azure IoT Edge átjárók Azure IoT Edge modulként van telepítve. A többi Azure IoT Edge-modulhoz hasonlóan az MCC modul telepítési környezeti változói és tároló-létrehozási beállításai is a Microsoft csatlakoztatott gyorsítótár-modulok konfigurálására szolgálnak.  Ez a szakasz azokat a környezeti változókat és tároló-létrehozási beállításokat határozza meg, amelyek szükségesek ahhoz, hogy az ügyfél sikeresen telepítse a Microsoft csatlakoztatott gyorsítótár-modult az Azure IoT Hub eszköz frissítése által használthoz.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft csatlakoztatott gyorsítótár Azure IoT Edge modul üzembe helyezésének részletei

A Microsoft csatlakoztatott gyorsítótár-moduljának elnevezése a rendszergazda belátása szerint történik. Nincs más modul vagy szolgáltatás-interakció, amely a modul nevére támaszkodik a kommunikációhoz. Emellett a Microsoft csatlakoztatott gyorsítótár-kiszolgálók szülő-gyermek kapcsolata nem függ ettől a modul nevétől, hanem annak a Azure IoT Edge-átjárónak a teljes tartománynevével vagy IP-címével, amelyet korábban már ismertetett.

A Microsoft csatlakoztatott gyorsítótár Azure IoT Edge moduljának környezeti változói az alapszintű modul azonosító adatait és a funkcionális modul beállításait adják át a tárolónak.

| Változó neve                 | Érték formátuma                           | Kötelező vagy nem kötelező | Funkció                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure-előfizetés AZONOSÍTÓjának GUID azonosítója             | Kötelező          | Ez az ügyfél kulcsa, amely biztonságos<br>gyorsítótár-csomópont hitelesítése kézbesítési optimalizálásra<br>Services. A modul működéséhez szükséges. |
| CACHE_NODE_ID                 | Gyorsítótár-csomópont AZONOSÍTÓjának GUID azonosítója                     | Kötelező          | A Microsoft csatlakoztatott gyorsítótár egyedi azonosítása<br>csomópont és a kézbesítés optimalizációs szolgáltatások. Szükséges sorrendben<br> a modul működéséhez. |
| CUSTOMER_KEY                  | Ügyfél kulcsának GUID azonosítója                     | Kötelező          | Ez az ügyfél kulcsa, amely biztonságos<br>a gyorsítótár-csomópont hitelesítése a kézbesítési optimalizálási szolgáltatásokhoz.<br>A modul működéséhez szükséges.|
| STORAGE_ *N* _SIZE_GB           | Ahol N a szükséges GB-os szám   | Kötelező          | Legfeljebb kilenc meghajtót kell megadnia a tartalom gyorsítótárazásához, és meg kell adni a<br>az egyes gyorsítótár-meghajtókon a tartalom számára a maximálisan megengedett lemezterület (GB). Angol nyelvű Példák:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>A meghajtó számának meg kell egyeznie a megadott gyorsítótár-meghajtó kötési értékeivel<br>a tároló létrehozási beállítása MicrosoftConnectedCache *N* érték|
| UPSTREAM_HOST                 | TELJES TARTOMÁNYNÉV/IP-CÍM                                | Választható          | Ez az érték megadhat egy felsőbb rétegbeli Microsoft-csatlakozást<br>Gyorsítótár-csomópont, amely proxyként funkcionál, ha a csatlakoztatott gyorsítótár csomópontja<br> nem kapcsolódik az internethez. Ezzel a beállítással lehet támogatni<br> a beágyazott IoT forgatókönyv.<br>**Megjegyzés:** A Microsoft Connected cache a 80-es http alapértelmezett portot figyeli.|
| UPSTREAM_PROXY                | FQDN/IP: PORT                           | Választható          | A kimenő internetes proxy.<br>Ez lehet az OT DMZ proxy is, ha egy ISA 95-hálózat. |
| CACHEABLE_CUSTOM_ *N* _HOST     | GAZDAGÉP/IP<br>FQDN                        | Választható          | Az egyéni csomagok adattárainak támogatásához szükséges.<br>A Tárházak helyileg vagy az interneten is üzemeltethető.<br>A konfigurálható egyéni gazdagépek száma nincs korlátozva.<br><br>Angol nyelvű Példák:<br>Név = CACHEABLE_CUSTOM_1_HOST érték = packages.foo.com<br> Név = CACHEABLE_CUSTOM_2_HOST érték = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Választható          | Az egyéni csomagok adattárainak támogatásához szükséges.<br>Ezt az értéket aliasként lehet használni, és a gyorsítótár-kiszolgáló fogja használni a hivatkozást<br>más DNS-nevek. Előfordulhat például, hogy a tárház tartalmának állomásneve packages.foo.com,<br>a különböző régiók esetében azonban lehet, hogy az állomásnévhez hozzáadott egy további előtag<br>például a westuscdn.packages.foo.com és a eastuscdn.packages.foo.com.<br>A kanonikus alias beállításával biztosíthatja, hogy a tartalom ne legyen duplikálva.<br>ugyanarról a gazdagépről, de különböző CDN-forrásokból származó tartalomhoz.<br>A kanonikus érték formátuma nem fontos, de egyedinek kell lennie a gazdagépen.<br>Lehet, hogy a legkönnyebb beállítani az értéket a gazdagép értékének megfelelően.<br><br>Példák az egyéni gazdagépek fenti példái alapján:<br>Név = CACHEABLE_CUSTOM_1_CANONICAL érték = foopackages<br> Név = CACHEABLE_CUSTOM_2_CANONICAL érték = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Igaz vagy hamis                          | Választható          | Lehetővé teszi az összegző jelentés megtekintését a helyi hálózaton vagy az interneten.<br>Ha True értékre van állítva, az összegző jelentés megtekintéséhez szükség van egy API-kulcs (később tárgyalt) használatára. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Igaz vagy hamis                          | Választható          | Lehetővé teszi az összegző jelentés megtekintését a helyi hálózaton vagy az interneten anélkül, hogy<br>az API-kulcs használata a hálózat bármely eszközéről. Akkor használja, ha nem szeretné zárolni a hozzáférést<br>a gyorsítótár-kiszolgáló összegző információinak megtekintése a böngésző használatával. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft csatlakoztatott gyorsítótár Azure IoT Edge modul-tároló létrehozási beállításai

A tároló-létrehozási beállítások az MCC modul telepítéséhez adja meg az ÜGYFÉLKÖZPONT-modul által használt tárterülettel és portokkal kapcsolatos beállításokat. Az MCC telepítéséhez használt kötelező tároló által létrehozott változók listája.

### <a name="container-to-host-os-drive-mappings"></a>Tároló az operációsrendszer-meghajtó-hozzárendelések futtatásához

A tároló tárolási helyének a lemezen lévő tárolóhelyre való leképezéséhez szükséges. < akár kilenc helyet is megadhat.

>[!Note]
>A meghajtó számának meg kell egyeznie a környezeti változóban megadott gyorsítótár-meghajtó kötési értékeivel STORAGE_ *N* _SIZE_GB érték, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Tároló a TCP-port hozzárendeléséhez

Ez a beállítás határozza meg azt a külső gépi http-portot, amelyet az MCC a tartalom iránti kérések esetén figyel. Az alapértelmezett HostPort a 80-es port, a többi port pedig jelenleg nem támogatott, mivel a ADU-ügyfél a 80-es porton keresztül kéri a kérelmeket. Az 8081-as TCP-port a belső tároló azon portja, amelyet az MCC figyel, és nem módosítható.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>A Container Service TCP-portjának leképezései

A Microsoft Connected cache modul rendelkezik egy .NET Core szolgáltatással, amelyet a különböző függvények számára a gyorsítótárazási motor használ.

>[!Note]
>Az Azure IoT beágyazott szegélyének támogatásához a HostPort nem állítható be 5000-re, mert a beállításjegyzék-proxy modul már figyel a 5000-es gazdagép-porton.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>A Microsoft csatlakoztatott gyorsítótárának összegző jelentése

Az összegző jelentés jelenleg az egyetlen módja annak, hogy az ügyfél megtekintse a Azure IoT Edge átjárók számára központilag telepített Microsoft-gyorsítótárbeli Példányok gyorsítótárazási szolgáltatásait. A jelentés 15 másodperces időközönként jön létre, és az időszakra vonatkozó átlagos statisztikát, valamint a modul élettartamának összesített statisztikáit tartalmazza. Az ügyfelek által érintett legfontosabb statisztikák a következők:

* **hitBytes** – a közvetlenül a gyorsítótárból kapott bájtok összege.
* **missBytes** – azt a bájtot adja meg, amelyet a Microsoft csatlakoztatott gyorsítótárának a CDN-ből való letöltéséhez le kellett tölteni a gyorsítótár megtekintéséhez.
* **eggressBytes** – ez a hitBytes és a missBytes összege, és az ügyfeleknek küldött összes bájt.
* **hitRatioBytes** – ez a hitBytes és a egressBytes közötti arány.  Ha a eggressBytes 100%-a egy adott időszakban megegyezett a hitBytes, ez például 1 lenne.

Az összegző jelentés a következő címen érhető el: `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (a jelentés láthatóságával kapcsolatos információkért lásd az alábbi környezeti változók részleteit).
