---
title: A Linux rendszerhez készült Azure IoT Edge a Windows rendszeren | Microsoft Docs
description: A Linux IoT Edge-modulok Windows 10-es eszközökön való futtatásának áttekintése
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492574"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>A Windowson futtatott Linux rendszerhez készült Azure IoT Edge bemutatása (előzetes verzió)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

A Windows rendszerhez készült Linux-Azure IoT Edge lehetővé teszi, hogy a Windows IoT üzemelő példányokon a Windows-alkalmazások mellett is futtasson tárolós Linux-feladatokat. Azok a vállalatok, amelyek a Windows IoT használják a peremhálózat-eszközeiket, mostantól kihasználhatják a Linux rendszerbe épített felhőalapú elemzési megoldások előnyeit.

A Windows operációs rendszerhez készült Linux rendszerű virtuális gépek Windows-eszközökön való futtatásával IoT Edge. A Linux rendszerű virtuális gép előre telepítve van a IoT Edge futtatókörnyezettel. Az eszközön üzembe helyezett összes IoT Edge modul a virtuális gépen belül fut. Eközben a Windows-gazdagépen futó Windows-alkalmazások kommunikálhatnak a linuxos virtuális gépen futó modulok használatával.

[Ismerkedjen](how-to-install-iot-edge-on-windows.md) meg még ma az előzetes verzióval.

>[!NOTE]
>Vegye fontolóra a [termék felmérését](https://aka.ms/AzEFLOW-Registration) , hogy a IoT Edge hátterének és céljainak megfelelően fejlesszen a Linux rendszerhez Azure IoT Edge. Ezt a kérdőívet arra is használhatja, hogy regisztráljon a Linux rendszerhez készült jövőbeli Azure IoT Edge Windows-hirdetményekben.

## <a name="components"></a>Összetevők

A Windows rendszerhez készült Linux rendszerhez készült IoT Edge a következő összetevőket használja a Linux és a Windows rendszerű számítási feladatok párhuzamos futtatásához, és zökkenőmentes kommunikációt tesznek lehetővé:

* **Azure IoT Edge rendszert futtató linuxos virtuális gép**: a Microsoft első fél [Mariner](https://github.com/microsoft/CBL-Mariner) operációs rendszerén alapuló linuxos virtuális gép a IoT Edge futtatókörnyezettel van kialakítva, és a IoT Edge számítási feladatokhoz 1. szintű támogatott környezetként van érvényesítve.

* **Windows felügyeleti központ**: a Windows felügyeleti központ IoT Edge-bővítménye megkönnyíti a Linux rendszerű virtuális gépen a IoT Edge telepítését, konfigurálását és diagnosztizálását. A Windows felügyeleti központ IoT Edge Linux rendszerre telepíthető a helyi eszközön, vagy csatlakozhat a megcélzott eszközökhöz, és távolról is kezelheti azokat.

* **Microsoft Update**: a Microsoft Update integrációja megtartja a Windows Runtime összetevőit, a Mariner linuxos virtuális gépet, és IoT Edge naprakészen.

![A Windows és a Linux rendszerű virtuális gép párhuzamosan fut, míg a Windows felügyeleti központ mindkét összetevőt vezérli](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

A Windows-folyamat és a Linux rendszerű virtuális gép közötti kétirányú kommunikáció azt jelenti, hogy a Windows-folyamatok felhasználói felületet vagy hardveres proxykat biztosíthatnak a számítási feladatok futtatásához a Linux-tárolókban.

## <a name="samples"></a>Példák

A Windows rendszerhez készült Linux-IoT Edge a Linux-és Windows-összetevők közötti együttműködési képességet emeli ki.

A Windows-alkalmazások és a IoT Edge modulok közötti kommunikációt bemutató Példákért lásd: [Windows 10 IoT-minták](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Nyilvános előzetes verzió

A Linux rendszerhez készült IoT Edge jelenleg [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el. A telepítési és felügyeleti folyamatok eltérhetnek az általánosan elérhető funkcióktól.

## <a name="support"></a>Támogatás

A IoT Edge támogatási és visszajelzési csatornák használatával segítséget kaphat a Linux rendszerhez készült IoT Edge a Windows rendszeren.

**Hibajelentések** – a hibákat a IoT Edge nyílt forráskódú projekt [problémák lapján](https://github.com/azure/iotedge/issues) lehet jelenteni. A Windows rendszerhez készült Linux Azure IoT Edge kapcsolatos hibák a [iotedge-eFlow problémák oldalon](https://github.com/azure/iotedge-eflow/issues)olvashatók be.

A **Microsoft ügyfélszolgálati csapata** – a [támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkező felhasználók a támogatási jegyet közvetlenül a [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)hozhatják létre a Microsoft ügyfélszolgálati csapatának.

**Szolgáltatásra vonatkozó kérelmek** – a Azure IoT Edge termék a termék [felhasználói hangoldalán](https://feedback.azure.com/forums/907045-azure-iot-edge)keresztül követi nyomon a szolgáltatásra vonatkozó kéréseket.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Linux rendszerhez készült IoT Edget a Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) -ban további információért és a minta működéséhez.

A Windowsos eszközökön a Linux rendszerhez készült [Azure IoT Edge telepítése és kiépítése](how-to-install-iot-edge-on-windows.md) című témakör lépéseit követve állíthatja be az eszközt a Windows rendszeren futó linuxos IoT Edge.
