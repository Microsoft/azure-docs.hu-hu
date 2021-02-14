---
title: IoT Edge verziók navigációja és előzményei – Azure IoT Edge
description: Ismerje meg a IoT Edge újdonságait, és tekintse meg a legújabb kiadásokban található új funkciókkal és képességekkel kapcsolatos információkat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9db51fe9298b7f3329d35df375d027046e1f272e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366149"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge verziók és kibocsátási megjegyzések

A Azure IoT Edge a GitHubon üzemeltetett nyílt forráskódú IoT Edge projektből készült termék. Minden új kiadás elérhetővé válik a [Azure IoT Edge projektben](https://github.com/Azure/azure-iotedge). A [nyílt forráskódú IoT Edge projekthez](https://github.com/Azure/iotedge)hozzájárulások és hibajelentések is készíthetők.

## <a name="documented-versions"></a>Dokumentált verziók

A webhelyhez tartozó IoT Edge dokumentáció a termék két különböző verziójához érhető el, így kiválaszthatja a IoT Edge-környezethez tartozó tartalmat. Jelenleg a két támogatott verzió a következők:

* A **IoT Edge 1,1 (LTS)** a IoT Edge első hosszú távú támogatási (LTS) verziója. A jelen verzió dokumentációja a 1,1-on keresztül az összes korábbi verzió összes funkcióját és képességeit ismerteti. Ez a dokumentációs verzió az 1,1-es verzió támogatott élettartamán keresztül stabil lesz, és nem tükrözi a későbbi verziókban megjelent új funkciókat. Az 1,1-es kiadás a IoT Edge legújabb általánosan elérhető verziója.
* A **IoT Edge 1,2 (előzetes verzió)** további tartalmakat tartalmaz a legújabb előzetes kiadásban, [1,2-RC1-](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) ben elérhető funkciókhoz és képességekhez.
  * Habár a IoT Edge 1,2 előzetes verzióban érhető el, telepítenie kell a Release Candidate verzióit. További információ: [Offline vagy adott verzió telepítése](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation-optional).

További információ a IoT Edge kiadásokról: [Azure IoT Edge támogatott rendszerek](support.md).

## <a name="version-history"></a>Korábbi verziók

Ez a táblázat a IoT Edge csomagok kiadásának legújabb verzióit tartalmazza, és kiemeli az egyes verziókra vonatkozó dokumentációs frissítéseket.

| Kibocsátási megjegyzések és eszközök | Típus | Date | Legfontosabb tudnivalók |
| ------------------------ | ---- | ---- | ---------- |
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Hosszú távú támogatás (LTS) | 2021. február | [A hosszú távú támogatási csomag és a támogatott rendszerek frissítései](support.md) |
| [1,2 – RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Előnézet | 2020. november | [Az átjárók mögött IoT Edge eszközök](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT-közvetítő](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020. október | [UploadSupportBundle Direct metódus](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Futásidejű mérőszámok feltöltése](how-to-access-built-in-metrics.md)<br>[Útvonal prioritása és élettartama](module-composition.md#priority-and-time-to-live)<br>[Modul indítási sorrendje](module-composition.md#configure-modules)<br>[X. 509 manuális kiépítés](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020. március | [X. 509 automatikus kiépítés a DPS-vel](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct metódus](how-to-edgeagent-direct-method.md#restart-module)<br>[támogatás – Bundle parancs](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Következő lépések

* [Az összes Azure IoT Edge-kiadás megtekintése](https://github.com/Azure/azure-iotedge/releases)
* [Szolgáltatási kérelmek elkészítése vagy áttekintése a visszajelzési fórumban](https://feedback.azure.com/forums/907045-azure-iot-edge)