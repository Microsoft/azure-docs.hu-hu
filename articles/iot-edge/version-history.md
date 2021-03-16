---
title: IoT Edge verziók navigációja és előzményei – Azure IoT Edge
description: Ismerje meg a IoT Edge újdonságait, és tekintse meg a legújabb kiadásokban található új funkciókkal és képességekkel kapcsolatos információkat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 877b488fc888b4ced8165c19bcd5c51b5f982745
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492506"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge verziók és kibocsátási megjegyzések

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Azure IoT Edge a GitHubon üzemeltetett nyílt forráskódú IoT Edge projektből készült termék. Minden új kiadás elérhetővé válik a [Azure IoT Edge projektben](https://github.com/Azure/azure-iotedge). A [nyílt forráskódú IoT Edge projekthez](https://github.com/Azure/iotedge)hozzájárulások és hibajelentések is készíthetők.

## <a name="documented-versions"></a>Dokumentált verziók

A webhelyhez tartozó IoT Edge dokumentáció a termék két különböző verziójához érhető el, így kiválaszthatja a IoT Edge-környezethez tartozó tartalmat. Jelenleg a két támogatott verzió a következők:

* A **IoT Edge 1,1 (LTS)** a IoT Edge első hosszú távú támogatási (LTS) verziója. A jelen verzió dokumentációja a 1,1-on keresztül az összes korábbi verzió összes funkcióját és képességeit ismerteti. Ez a dokumentációs verzió az 1,1-es verzió támogatott élettartamán keresztül stabil lesz, és nem tükrözi a későbbi verziókban megjelent új funkciókat. Az 1,1-es kiadás a IoT Edge legújabb általánosan elérhető verziója.
* A **IoT Edge 1,2 (előzetes verzió)** további tartalmakat tartalmaz a legújabb előzetes kiadásban ( [1,2 – RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4)) elérhető funkciókhoz és képességekhez.

További információ a IoT Edge kiadásokról: [Azure IoT Edge támogatott rendszerek](support.md).

## <a name="version-history"></a>Korábbi verziók

Ez a táblázat a IoT Edge csomagok kiadásának legújabb verzióit tartalmazza, és kiemeli az egyes verziókra vonatkozó dokumentációs frissítéseket.

| Kibocsátási megjegyzések és eszközök | Típus | Date | Legfontosabb tudnivalók |
| ------------------------ | ---- | ---- | ---------- |
| [1,2 – RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Előnézet | Március 2021 | Új IoT Edge-csomagok bevezetése, új telepítési és konfigurálási lépésekkel. További információ: [Update from 1,0 vagy 1,1 to 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Hosszú távú támogatás (LTS) | 2021. február | [A hosszú távú támogatási csomag és a támogatott rendszerek frissítései](support.md) |
| [1,2 – RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Előnézet | 2020. november | [Az átjárók mögött IoT Edge eszközök](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT-közvetítő](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020. október | [UploadSupportBundle Direct metódus](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Futásidejű mérőszámok feltöltése](how-to-access-built-in-metrics.md)<br>[Útvonal prioritása és élettartama](module-composition.md#priority-and-time-to-live)<br>[Modul indítási sorrendje](module-composition.md#configure-modules)<br>[X. 509 manuális kiépítés](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020. március | [X. 509 automatikus kiépítés a DPS-vel](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct metódus](how-to-edgeagent-direct-method.md#restart-module)<br>[támogatás – Bundle parancs](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Következő lépések

* [Az összes Azure IoT Edge-kiadás megtekintése](https://github.com/Azure/azure-iotedge/releases)
* [Szolgáltatási kérelmek elkészítése vagy áttekintése a visszajelzési fórumban](https://feedback.azure.com/forums/907045-azure-iot-edge)