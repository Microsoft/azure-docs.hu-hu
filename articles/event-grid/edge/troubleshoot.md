---
title: Hibakeresés – Azure Event Grid IoT Edge | Microsoft Docs
description: Hibaelhárítás a IoT Edge Event Gridban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171346"
---
# <a name="common-issues"></a>Gyakori problémák

Ha a környezetében IoT Edge Azure Event Grid használatával kapcsolatos problémákat tapasztal, a hibaelhárításhoz és a megoldáshoz tekintse meg ezt a cikket.

## <a name="view-event-grid-module-logs"></a>Event Grid modul naplófájljainak megtekintése

A hibakereséshez lehetséges, hogy hozzá kell férnie Event Grid modul naplóihoz. Ehhez a modult telepítő virtuális gépen futtassa a következő parancsot:

Windows rendszeren

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Linux rendszeren

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Nem lehet HTTPS-kéréseket készíteni

* Először győződjön meg arról, Event Grid modul **bejövő: serverAuth: tlsPolicy** beállítása **szigorú** vagy **engedélyezett**.

* Ha a modul – modul kommunikációja, győződjön meg arról, hogy az **4438** -es porton hívja meg a hívást, és a modul neve megegyezik a telepített szolgáltatással. 

  Például ha Event Grid modult a **eventgridmodule** néven telepítette, akkor az URL-címnek a következőnek kell lennie: **https://eventgridmodule:4438** . Győződjön meg arról, hogy a burkolat és a portszám helyes.
    
* Ha nem IoT modulról van szó, győződjön meg róla, hogy Event Grid port a gazdagépre van leképezve az üzembe helyezés során, például:

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>A HTTP-kérések nem hajthatók végre

* Először győződjön meg arról, Event Grid modul **bejövő: serverAuth: tlsPolicy** **engedélyezve** vagy **Letiltva** értékre van állítva.

* Ha a modul – modul kommunikációja, győződjön meg arról, hogy az **5888** -es porton hívja meg a hívást, és a modul neve megegyezik a telepített szolgáltatással. 

  Például ha Event Grid modult a **eventgridmodule** néven telepítette, akkor az URL-címnek a következőnek kell lennie: **http://eventgridmodule:5888** . Győződjön meg arról, hogy a burkolat és a portszám helyes.
    
* Ha nem IoT modulról van szó, győződjön meg róla, hogy Event Grid port a gazdagépre van leképezve az üzembe helyezés során, például:

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>A tanúsítványláncot olyan szolgáltató adta ki, amely nem megbízható

Alapértelmezés szerint Event Grid modul úgy van konfigurálva, hogy hitelesítse az ügyfeleket a IoT Edge biztonsági démon által kiadott tanúsítvánnyal. Győződjön meg arról, hogy az ügyfél olyan tanúsítványt mutat be, amely a lánc gyökerében van.

A **IoTSecurity** osztály [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) azt mutatja be, hogyan kérhet le tanúsítványokat IoT Edge biztonsági démonból, és hogyan konfigurálhatja a kimenő hívásokat.

Ha nem éles környezetben van, lehetősége van az ügyfél-hitelesítés kikapcsolására. Ennek elvégzéséhez tekintse meg a [biztonsági és hitelesítési](security-authentication.md) tudnivalókat.

## <a name="debug-events-not-received-by-subscriber"></a>Az előfizető által nem fogadott hibakeresési események

Ennek jellemző okai a következők:

* Az eseményt nem sikerült elküldeni. Egy eseménynek a Event Grid modulba való feladásához 200 (OK) HTTP-StatusCode kell érkeznie.

* Ellenőrizze az esemény-előfizetést az ellenőrzéshez:
    * A végpont URL-címe érvényes
    * Az előfizetésben lévő szűrők nem okozzák az esemény eldobását.

* Annak ellenőrzése, hogy fut-e az előfizetői modul

* Jelentkezzen be arra a virtuális gépre, amelyen a Event Grid modul telepítve van, és tekintse meg a naplóit.

* A közvetítés naplózásának bekapcsolásához állítsa be a következőt **: logDeliverySuccess = True** , és telepítse újra Event Grid modult, és próbálkozzon újra a kéréssel. A naplózás bekapcsolásával az átviteli sebesség és a késés hatással lehet, így a hibakeresés befejezésekor a Javaslatunk visszakapcsolható a **közvetítőhöz: logDeliverySuccess = false**  (Event Grid modul újbóli üzembe helyezése).

* A metrikák bekapcsolásához állítsa be a **metrikákat: reportertype = Console** , és telepítse újra Event Grid modult. Az azt követő műveletek a Event Grid modul konzolján naplózzák a metrikákat, amelyek a további hibakereséshez használhatók. Javasoljuk, hogy a mérőszámok bekapcsolásához csak a hibakereséshez és a befejezéshez a metrikák beállításával kapcsolja ki a metrikákat **: reportertype = none** és Event Grid modul újbóli üzembe helyezése.

## <a name="next-steps"></a>Következő lépések

Bármilyen problémát jelenthet, a Event Grid használatával kapcsolatos javaslatok a IoT Edge címen [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .