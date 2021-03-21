---
title: Ismerje meg az Azure IoT-eszközök fejlesztőinek csatlakoztatási lehetőségeit
description: Ismerje meg az Azure IoT-eszközök fejlesztőinek gyakran használt eszköz-csatlakoztatási lehetőségeit és eszközeit.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654365"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Áttekintés: az Azure IoT-eszközök fejlesztőinek csatlakoztatási lehetőségei
Az eszközökkel dolgozó fejlesztőknek több lehetősége van az Azure IoT-eszközök csatlakoztatására és felügyeletére. Ez a cikk áttekintést nyújt a leggyakrabban használt lehetőségekről és eszközökről az eszközök csatlakoztatásához és kezeléséhez.

Az eszközök Azure IoT-kapcsolódási lehetőségeinek kiértékelése során hasznos lehet a következő elemek összevetése:
- Azure IoT-eszközök alkalmazásának platformja
- Eszközök csatlakoztatásának és kezelésének eszközei

## <a name="application-platforms-iot-central-and-iot-hub"></a>Alkalmazás-platformok: IoT Central és IoT Hub
Az Azure IoT két olyan szolgáltatást tartalmaz, amelyek az eszközre képes felhőalapú alkalmazások platformja: az Azure IoT Central és az Azure IoT Hub. Az egyiket használhatja egy IoT-alkalmazás üzemeltetéséhez és az eszközök csatlakoztatásához.
- [IoT Central](../iot-central/core/overview-iot-central.md) úgy lett kialakítva, hogy csökkentse a IoT-megoldások használatának összetettségét és költségeit. Ez egy szolgáltatott szoftver (SaaS) alkalmazás, amely teljes platformot biztosít a IoT-alkalmazások üzemeltetéséhez. A IoT-alkalmazások létrehozásának és kezelésének teljes életciklusát a IoT Central webes felhasználói felület használatával egyszerűsítheti le. A webes felhasználói felület leegyszerűsíti az alkalmazások létrehozásának feladatait, valamint néhány akár több millió eszköz csatlakoztatását és felügyeletét. Az IoT Central a IoT Hub használatával hozza létre és kezeli az alkalmazásokat, de a részleteket a felhasználó számára transzparens módon tárolja. A IoT Central általában csökkenti az összetettséget és a fejlesztést, és egyszerűbbé teszi az eszközök kezelését a webes felhasználói felületen.
- A [IoT hub](../iot-hub/about-iot-hub.md) a IoT-alkalmazások és a csatlakoztatott eszközök közötti kétirányú kommunikációhoz központi üzenetsorként működik. Ez egy olyan platform-szolgáltatásként szolgáló alkalmazás, amely platformot is biztosít a IoT-alkalmazások üzemeltetéséhez. A IoT Centralhoz hasonlóan a több millió eszköz támogatására is méretezhető. Általánosságban elmondható, hogy IoT Hub nagyobb fokú irányítást és testre szabást biztosít az alkalmazás kialakításában, és a szolgáltatással való együttműködéshez több fejlesztői eszközre is lehetőséget nyújt a fejlesztés és a felügyelet bonyolultságának növelésével.

## <a name="tools-to-connect-and-manage-devices"></a>Eszközök csatlakoztatásához és kezeléséhez
Miután kiválasztotta IoT Hub vagy IoT Central a IoT-alkalmazás üzemeltetéséhez, több lehetőség közül választhat a fejlesztői eszközök közül. Ezekkel az eszközökkel csatlakozhat a kiválasztott IoT alkalmazás-platformhoz, valamint alkalmazásokat és eszközöket hozhat létre és kezelhet. Az alábbi táblázat összefoglalja az eszközök általános beállításait. 

> [!NOTE]
> Az alábbi eszközökön kívül a IoT-alkalmazások programozott módon is létrehozhatók és kezelhetők a REST API, az Azure SDK-k vagy a Azure Resource Manager-sablonok használatával. További információt a [IoT hub](../iot-hub/about-iot-hub.md) és [IoT Central](../iot-central/core/overview-iot-central.md) szolgáltatás dokumentációjában talál.

|Eszköz  |Támogatja a IoT platformot &nbsp; &nbsp; &nbsp;&nbsp; |Dokumentáció  |Description  |
|---------|---------|---------|---------|
|Központi webes felhasználói felület     | Közép | [Központi bevezetés](../iot-central/core/quick-deploy-iot-central.md) | Böngészőalapú portál a IoT Centralhoz. |
|Azure Portal     | Központ, Közép      | [Hozzon létre egy IoT hubot Azure Portalkal, és](../iot-hub/iot-hub-create-through-portal.md) [kezelje a IoT Central a Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Böngészőalapú portál IoT Hub és eszközökhöz. Más Azure-erőforrásokkal is együttműködik, beleértve a IoT Central. |
|Azure CLI     | Központ, Közép          | [IoT hub létrehozása a parancssori](../iot-hub/iot-hub-create-using-cli.md)felülettel, [IoT Central kezelése az Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) -vel | IoT-alkalmazások létrehozására és kezelésére szolgáló parancssori felület. |
|Azure PowerShell     | Központ, Közép   | [IoT hub létrehozása a PowerShell](../iot-hub/iot-hub-create-using-powershell.md)-lel, [IoT Central kezelése Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | PowerShell-felület IoT-alkalmazások létrehozásához és kezeléséhez |
|VS Code-hoz készült Azure IoT-eszközök  | Hub | [IoT hub létrehozása a VS Code eszközeivel](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code-bővítmény IoT Hub alkalmazásokhoz. |
|Azure IoT Explorer     | Hub | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) | IoT hubok nem hozhatók létre. Egy meglévő IoT-hubhoz csatlakozik az eszközök kezeléséhez. Gyakran használják a CLI vagy a Portal használatával.|

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az eszközök Azure IoT való csatlakoztatásának lehetőségeiről, tekintse meg az alábbi rövid útmutatókat:
- IoT Central: [Azure IoT Central-alkalmazás létrehozása](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [telemetria küldése az eszközről egy IoT hubhoz, és az Azure CLI-vel való figyelése](../iot-hub/quickstart-send-telemetry-cli.md)