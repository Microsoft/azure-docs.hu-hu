---
title: Egyéni riasztások létrehozása
description: Egyéni eszközök értesítéseinek megismerése, létrehozása és kiosztása az Azure Defender for IoT biztonsági szolgáltatáshoz.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: e59bcca9561c33610170f4bc3674eef307cf0de9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809044"
---
# <a name="create-custom-alerts"></a>Egyéni riasztások létrehozása

Az egyéni biztonsági csoportok és riasztások használata révén teljes mértékben kihasználhatja a teljes körű biztonsági információkat és a kategorikus eszköz ismereteit, így biztosítva a jobb biztonságot a IoT-megoldáson belül.

## <a name="why-use-custom-alerts"></a>Miért érdemes egyéni riasztásokat használni?

Ismeri a IoT-eszközeit a legjobban.

Azon ügyfelek esetében, akik teljes mértékben értik a várt eszköz viselkedését, a Defender for IoT lehetővé teszi az eszköz viselkedési szabályzatának és a várttól, a normál működéstől való bármilyen eltéréstől való megismertetését.

## <a name="security-groups"></a>Biztonsági csoportok

A biztonsági csoportok lehetővé teszik, hogy az eszközök logikai csoportjait definiálja, és központi módon kezelhesse a biztonsági állapotukat.

Ezek a csoportok az adott hardverrel rendelkező eszközöket, az adott helyen üzembe helyezett eszközöket, vagy bármely más, az adott igényeknek megfelelő csoportot jelenthetnek.

A biztonsági csoportokat a **SecurityGroup** nevű Device Twin tag tulajdonság határozza meg. Alapértelmezés szerint a IoT Hub minden IoT-megoldása egy **alapértelmezett** nevű biztonsági csoporttal rendelkezik. Módosítsa a **SecurityGroup** tulajdonság értékét egy eszköz biztonsági csoportjának megváltoztatásához.

Például:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Biztonsági csoportok használatával csoportosíthatja az eszközöket logikai kategóriákba. A csoportok létrehozása után hozzárendelheti azokat a választott egyéni riasztásokhoz a leghatékonyabb végpontok közötti IoT biztonsági megoldáshoz.

## <a name="customize-an-alert"></a>Riasztás testreszabása

1. Nyissa meg a IoT Hub, és válassza a **Beállítások** lehetőséget a **Biztonság** menüben.

1. Válassza az **egyéni riasztások** lehetőséget.

1. Válassza ki azt a biztonsági csoportot, amelyre alkalmazni kívánja a testreszabást.

1. Válassza **az egyéni riasztás hozzáadása** lehetőséget.

1. Válasszon ki egy egyéni riasztást a legördülő listából.

1. Szerkessze a szükséges tulajdonságokat, majd kattintson **az OK gombra**.

1. Ügyeljen arra, hogy válassza a **Mentés** lehetőséget. Az új riasztás mentése nélkül a rendszer törli a riasztást, amikor legközelebb IoT Hub.

## <a name="alerts-available-for-customization"></a>Testreszabáshoz elérhető riasztások

A IoT Defender nagy számú riasztást kínál, amelyek az adott igényeknek megfelelően testreszabhatók. Tekintse át a riasztás súlyosságára, az adatforrásra, a leírásra és a javasolt szervizelési lépésekre vonatkozó [testreszabható riasztási táblázatot](concept-customizable-security-alerts.md) , ha és amikor az egyes riasztások fogadása történik.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan telepíthet biztonsági ügynököt...

> [!div class="nextstepaction"]
> [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
