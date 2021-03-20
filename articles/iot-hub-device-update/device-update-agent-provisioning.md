---
title: Az eszköz frissítésének kiépítés az Azure IoT Hub-ügynökhöz | Microsoft Docs
description: Eszköz frissítésének kiépítés az Azure IoT Hub Agenthez
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097754"
---
# <a name="device-update-agent"></a>Eszköz frissítési ügynöke

A IoT Hub eszköz frissítése két frissítési formát támogat – a rendszerkép-alapú és a Package-alapú. 

* A rendszerkép frissítései magasabb szintű megbízhatóságot biztosítanak az eszköz állapotában. Általában könnyebb replikálni az éles üzem előtti környezet és az éles környezet közötti rendszerképek eredményeit, mivel az nem ugyanazokat a kihívásokat eredményezi, mint a csomagok és a függőségeik. Az Atom jellegéből adódóan az egyik A/B feladatátvételi modellt is könnyedén elvégezheti. 
* A Package-alapú frissítések olyan célzott frissítések, amelyek csak egy adott összetevőt vagy alkalmazást módosítanak az eszközön. Így alacsonyabb sávszélesség-fogyasztást eredményez, és segít csökkenteni a frissítés letöltésének és telepítésének idejét. A csomagok frissítései általában lehetővé teszik, hogy az eszközök kevesebb állásidőt alkalmazzanak, amikor frissítést alkalmaznak, és elkerülik a lemezképek létrehozásának terhelését. 

Kövesse az alábbi hivatkozásokat az eszköz frissítési ügynökének létrehozásával, futtatásával és módosításával kapcsolatban.

## <a name="build-the-device-update-agent"></a>Az eszköz frissítési ügynökének összeállítása

Kövesse az eszköz frissítési ügynökének forrásból való [létrehozásához](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) szükséges utasításokat.

## <a name="run-the-device-update-agent"></a>Az eszköz frissítési ügynökének futtatása

Az ügynök sikeres felépítése után ideje [futtatni](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) az ügynököt.

## <a name="modifying-the-device-update-agent"></a>Az eszköz frissítési ügynökének módosítása

Most végezze el a szükséges módosításokat, hogy az ügynököt beépítse a rendszerképbe.  Tekintse át az eszköz frissítési ügynökének [módosítását](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) ismertető útmutatót.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ha problémákba ütközik, tekintse át az eszköz frissítését IoT Hub [hibaelhárítási útmutatót](troubleshoot-device-update.md) , amely segítséget nyújt a lehetséges problémák blokkolásához és a szükséges információk összegyűjtéséhez a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

Az alábbi, előre elkészített lemezképek és bináris fájlok használatával egyszerűen bemutathatja IoT Hub eszköz frissítését.  

[Rendszerkép frissítése: Első lépések málna PI 3 B + hivatkozás Yocto képpel](device-update-raspberry-pi.md)

[Rendszerkép frissítése: Első lépések az Ubuntu (18,04 x64) szimulátor-hivatkozási ügynökkel](device-update-simulator.md)

[Csomag frissítése: Első lépések az Ubuntu Server 18,04 x64 Package Agent használatával](device-update-ubuntu-agent.md)

