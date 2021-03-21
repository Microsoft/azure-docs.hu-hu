---
title: A Microsoft csatlakoztatott gyorsítótára egy Azure IoT Edgeon belül az ipari IoT-konfigurációhoz | Microsoft Docs
description: A Microsoft csatlakoztatott gyorsítótára egy Azure IoT Edgeon belül az ipari IoT konfigurációjának oktatóanyagához
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101664779"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>A Microsoft csatlakoztatott gyorsítótár előzetes telepítési forgatókönyvének mintája: Microsoft csatlakoztatott gyorsítótár egy Azure IoT Edge az ipari IoT-konfigurációhoz

A gyártási hálózatok gyakran hierarchikus rétegekben vannak rendezve a [Purdue hálózati modell](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (az [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) és az [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) szabvány része) után. Ezekben a hálózatokban csak a legfelső réteg kapcsolódik a felhőhöz, és a hierarchia alsó rétegei csak a szomszédos Észak-és Dél-rétegekkel tudnak kommunikálni.

Ez a GitHub-példa az [ipari IoT Azure IoT Edge](https://github.com/Azure-Samples/iot-edge-for-iiot)a következőket helyezi üzembe:

* Szimulált Purdue-hálózat az Azure-ban
* Ipari eszközök 
* Azure IoT Edge átjárók hierarchiája
  
Ezek az összetevők az ipari adatok beszerzésére és a felhőbe való biztonságos feltöltésére szolgálnak a hálózat biztonságának veszélyeztetése nélkül. A Microsoft csatlakoztatott gyorsítótára telepíthető úgy, hogy támogassa a tartalom letöltését az ISA 95-kompatibilis hálózaton belül minden szinten.

A Microsoft csatlakoztatott gyorsítótárak ISA 95-kompatibilis hálózaton belüli központi telepítésének kulcsa az OT *-proxy és* a felsőbb rétegbeli gazdagép konfigurálása az L3 IoT Edge-átjárón.

1. Konfigurálja a Microsoft csatlakoztatott gyorsítótár-telepítéseit az L5 és L4 szintjén, ahogy az a Two-Level nested IoT Edge Gateway-minta című cikkben leírtak szerint 
2. Az L3 IoT Edge átjárón üzemelő példánynak a következőket kell megadnia:
   
   * UPSTREAM_HOST – a 4 – 4. IoT Edge átjáró IP-címe/teljes tartományneve, amelyhez a Microsoft által csatlakoztatott gyorsítótár a tartalmat fogja kérni.
   * UPSTREAM_PROXY – az IP/FQDN: az OT proxykiszolgáló PORTja.

3. Az OT-proxynak hozzá kell adnia a L4 MCC FQDN/IP-címet az engedélyezési listához.

Annak ellenőrzéséhez, hogy a Microsoft csatlakoztatott gyorsítótár megfelelően működik-e, hajtsa végre a következő parancsot a IoT Edge eszköz terminálján, amely a modult vagy a hálózaton lévő összes eszközt üzemelteti.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```