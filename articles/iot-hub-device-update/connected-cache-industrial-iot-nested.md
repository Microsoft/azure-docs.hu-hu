---
title: A Microsoft Csatlakoztatott gyorsítótár egy ipari IoT Azure IoT Edge konfigurációs | Microsoft Docs
description: A Microsoft Csatlakoztatott gyorsítótár egy ipari ioT Azure IoT Edge konfigurációs oktatóanyagban
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811811"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>A Microsoft Csatlakoztatott gyorsítótár előzetes verzió üzembe helyezési forgatókönyvének mintája: A Microsoft Csatlakoztatott gyorsítótár ipari IoT Azure IoT Edge konfigurációhoz Azure IoT Edge környezetben

A gyártási hálózatokat gyakran hierarchikus rétegekbe szervezik a [Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) hálózati modell alapján (az [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) és [AZ ISA 99 szabványokban](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) szerepelnek). Ezekben a hálózatokban csak a felső réteg rendelkezik kapcsolattal a felhőhöz, és a hierarchia alsó rétegei csak a szomszédos északi és déli rétegekkel kommunikálhatnak.

Ez a GitHub-Azure IoT Edge [ipari IoT-hez,](https://github.com/Azure-Samples/iot-edge-for-iiot)a következőt telepíti:

* Szimulált purdue hálózat az Azure-ban
* Ipari eszközök 
* Átjárók Azure IoT Edge hierarchiája
  
Ezekkel az összetevőkkel ipari adatokat szerezhet be, és biztonságosan feltöltheti őket a felhőbe a hálózat biztonságának veszélyeztetése nélkül. A Microsoft Csatlakoztatott gyorsítótár az ISA 95-kompatibilis hálózat minden szintjén támogatja a tartalom letöltését.

A Microsoft Csatlakoztatott gyorsítótár isA 95-kompatibilis hálózaton belüli üzembe helyezésének kulcsa az *OT-proxy* és a felfelé irányuló gazdagép konfigurálása az L3 IoT Edge átjárón.

1. Konfigurálja a Microsoft Csatlakoztatott gyorsítótár 5. és 4. szintű üzemelő példányokat a beágyazott Two-Level átjárómintában IoT Edge leírtak szerint 
2. A 3. IoT Edge átjárón való üzembe helyezésnek a következőket kell megadnia:
   
   * UPSTREAM_HOST – Az L4 IoT Edge átjáró IP-címe/FQDN-e, amelyet a Microsoft L3 Csatlakoztatott gyorsítótár le fog kérni.
   * UPSTREAM_PROXY – Az EGYSZERI proxykiszolgáló IP/FQDN:PORTja.

3. Az ot proxynak hozzá kell adnia az L4 MCC FQDN/IP-címet az engedélyezési listához.

A Microsoft Csatlakoztatott gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot a IoT Edge-eszköz terminálán, amely a modult vagy a hálózaton található bármely eszközt futtatja. Cserélje le a helyére a saját \<Azure IoT Edge Gateway IP\> átjárója IP-címét IoT Edge állomásnevét. (A jelentés láthatóságával kapcsolatos információkért lásd a környezeti változó részleteit).

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```