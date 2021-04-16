---
title: Mi a Azure IoT Edge Linux rendszeren a Windows | Microsoft Docs
description: A Linux rendszerű virtuális gépek moduljainak IoT Edge futtatása Windows 10 eszközökön
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538198"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>A Windowson futtatott Linux rendszerhez készült Azure IoT Edge bemutatása (előzetes verzió)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge Windows rendszeren futó linuxos alkalmazások lehetővé teszik a tárolóba telepített Linux számítási feladatok windowsos alkalmazások mellett történő futtatását Windows IoT-környezetben. Azok a vállalatok, amelyek Windows IoT-t használnak a peremhálózati eszközeikhez, most már kihasználhatja a Linuxban készült natív felhőbeli elemzési megoldásokat.

IoT Edge Windows rendszeren futó linuxos virtuális gép windowsos eszközön való futtatásával működik. A Linux rendszerű virtuális gép előre telepítve van a IoT Edge futtatáskor. Az IoT Edge telepített összes modul a virtuális gépen fut. Eközben a Windows rendszerű gazdaeszközön futó Windows-alkalmazások kommunikálhatnak a Linux rendszerű virtuális gépen futó modulokkal.

[Első lépések](how-to-install-iot-edge-on-windows.md) az előzetes verzióval még ma.

>[!NOTE]
>Vegye fontolóra a termékkel kapcsolatos [felmérésünket,](https://aka.ms/AzEFLOW-Registration) amely segít a Linux Azure IoT Edge windowsos környezetének javításában a IoT Edge és célok alapján. Ezzel a felméréssel linuxos bejelentésekre is regisztrálhat Azure IoT Edge windowsos bejelentésekre.

## <a name="components"></a>Összetevők

IoT Edge Windows rendszeren futó linuxos alkalmazások a következő összetevőkkel engedélyezik a Linux és Windows számítási feladatok egymás melletti futtatását és zökkenőmentes kommunikációját:

* **Egy** Azure IoT Edge-t futtató Linux rendszerű virtuális gép: A Microsoft első féltől származó [System System](https://github.com/microsoft/CBL-Mariner) operációs rendszerére épülő Linux rendszerű virtuális gép az IoT Edge-futtatókörnyezettel készült, és 1. rétegbeli támogatott környezetként van ellenőrizve IoT Edge számítási feladatokhoz.

* **Windows Admin Center:** A IoT Edge bővítménye Windows Admin Center a Linux rendszerű virtuális gép IoT Edge, konfigurálási és diagnosztikai szolgáltatását. Windows Admin Center linuxos IoT Edge a helyi eszközön, vagy csatlakozhat a céleszközökhöz, és távolról kezelheti őket.

* **Microsoft Update:** Az Microsoft Update a Windows-alapú futásidejű összetevőket, a Linux rendszerű Vm-et és IoT Edge naprakészen tartja.

![A Windows és a Linux rendszerű virtuális gép párhuzamosan fut, míg a Windows Admin Center mindkét összetevőt vezérli](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

A Windows-folyamat és a Linux rendszerű virtuális gép közötti kétirányú kommunikáció azt jelenti, hogy a Windows-folyamatok felhasználói felületeket vagy hardveres proxykat biztosítanak a Linux-tárolókban futó számítási feladatokhoz.

## <a name="samples"></a>Példák

IoT Edge linuxos windowsos verzióban a Linux és a Windows összetevői közötti együttműködésre van hangsúlyt fektetve.

A Windows-alkalmazások és a IoT Edge közötti kommunikációt szemléltető példákért lásd: [EFLOW & Windows 10 IoT-minták.](https://aka.ms/AzEFLOW-Samples)

## <a name="public-preview"></a>Nyilvános előzetes verzió

IoT Edge linuxos windowsos verziója jelenleg nyilvános [előzetes verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A telepítési és felügyeleti folyamatok eltérőek lehetnek az általánosan elérhető szolgáltatásoktól.

## <a name="support"></a>Támogatás

A IoT Edge és visszajelzési csatornákon segítséget kaphat a Linux IoT Edge Windows rendszeren való használatával kapcsolatban.

**Hibák jelentése** – A hibák [](https://github.com/azure/iotedge/issues) a nyílt forráskódú projekt IoT Edge lapján jelentéseket kaphatnak. A Linux Azure IoT Edge kapcsolatos hibákról a () [iotedge-eflow issues (iotedge-eflow-problémák) oldalon lehet jelentést látni.](https://aka.ms/AzEFLOW-Issues)

**Microsoft ügyféltámogatási csapat** – Azok [a](https://azure.microsoft.com/support/plans/) felhasználók, akik támogatási csomagokkal vannak, közvetlenül a támogatási csoportból származó támogatási jegy létrehozásával kapcsolatba [Azure Portal.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)

**Funkciókérések** – Azure IoT Edge termék a termék User Voice (Felhasználói hang) oldalán követi nyomon a [szolgáltatáskéréseket.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="next-steps"></a>Következő lépések

További [IoT Edge a Linuxhoz Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) a linuxos alkalmazásokban, és egy példa a művelethez.

Kövesse az [Install and provision Azure IoT Edge for Linux on a Windows device](how-to-install-iot-edge-on-windows.md) (Linux-eszközök telepítése és kiépítése Windows-eszközön) IoT Edge windowsos linuxos eszközzel.
