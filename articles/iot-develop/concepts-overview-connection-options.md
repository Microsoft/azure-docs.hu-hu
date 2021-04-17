---
title: Tudnivalók az Azure IoT-eszközfejlesztők csatlakozási lehetőségeiről
description: Ismerje meg az Azure IoT-eszközfejlesztők által gyakran használt eszközkapcsolati lehetőségeket és eszközöket.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589550"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Áttekintés: Csatlakozási lehetőségek Azure IoT-eszközfejlesztők számára
Az eszközökkel együttműködő fejlesztőként számos lehetőség áll rendelkezésre az Azure IoT-eszközök csatlakoztatásához és kezeléséhez. Ez a cikk az eszközök csatlakoztatásához és kezeléséhez leggyakrabban használt lehetőségeket és eszközöket áttekintést nyújt.

Az Eszközök Azure IoT-kapcsolati lehetőségeinek kiértékelése esetén hasznos lehet összehasonlítani a következő elemeket:
- Azure IoT-eszközalkalmazás-platformok
- Eszközök csatlakoztatásához és kezeléséhez

## <a name="application-platforms-iot-central-and-iot-hub"></a>Alkalmazásplatformok: IoT Central és IoT Hub
Az Azure IoT két olyan szolgáltatást tartalmaz, amelyek az eszközalapú felhőalkalmazások platformjai: Azure IoT Central és Azure IoT Hub. Bármelyiket használhatja Egy IoT-alkalmazás gazdagépként való használatára és eszközök csatlakoztatásához.
- [IoT Central](../iot-central/core/overview-iot-central.md) úgy lett kialakítva, hogy csökkentse az IoT-megoldások használatának összetettségét és költségeit. Ez egy szolgáltatott szoftver (SaaS) alkalmazás, amely teljes platformot biztosít az IoT-alkalmazások üzemeltetéséhez. A webes felhasználói IoT Central használatával egyszerűsítheti az IoT-alkalmazások létrehozásának és kezelésének teljes életciklusát. A webes felhasználói felület leegyszerűsíti az alkalmazások létrehozását, valamint néhány vagy akár több millió eszköz csatlakoztatását és felügyeletét. IoT Central a IoT Hub hoz létre és kezel alkalmazásokat, de a részleteket átláthatóan tartja a felhasználó számára. A webes IoT Central összetettséget és fejlesztési erőfeszítéseket tesz lehetővé, és leegyszerűsíti az eszközkezelést.
- [IoT Hub](../iot-hub/about-iot-hub.md) központi üzenetközpontként működik az IoT-alkalmazások és a csatlakoztatott eszközök közötti kétirányú kommunikációhoz. Ez egy szolgáltatásként is használható (PaaS)-alkalmazás, amely az IoT-alkalmazások üzemeltetési platformját is biztosítja. A IoT Central több millió eszköz támogatásához méretezhető. Általánosságban elmondható, IoT Hub nagyobb mértékű vezérlést és testreszabást kínál az alkalmazástervezéshez, és több fejlesztői eszköz áll rendelkezésre a szolgáltatás használata során, a fejlesztés és a felügyelet összetettségének növekedése árán.

## <a name="tools-to-connect-and-manage-devices"></a>Eszközök az eszközök csatlakoztatásához és kezeléséhez
Miután kiválasztotta IoT Hub IoT IoT Central-alkalmazás gazdagépként való beállítását, számos fejlesztői eszköz közül választhat. Ezekkel az eszközökkel csatlakozhat a kiválasztott IoT-alkalmazásplatformhoz, valamint alkalmazásokat és eszközöket hozhat létre és kezelhet. Az alábbi táblázat a gyakori eszközbeállításokat foglalja össze. 

> [!NOTE]
> A következő eszközök mellett programozott módon hozhat létre és kezelhet IoT-alkalmazásokat az REST API, AzureK-k vagy Azure Resource Manager használatával. További információért olvassa el a IoT Hub [és](../iot-hub/about-iot-hub.md) [IoT Central](../iot-central/core/overview-iot-central.md) dokumentációját.

|Eszköz  |Támogatja az IoT-platformot &nbsp; &nbsp; &nbsp;&nbsp; |Dokumentáció  |Description  |
|---------|---------|---------|---------|
|Központi webes felhasználói felület     | Közép | [Központi rövid útmutató](../iot-central/core/quick-deploy-iot-central.md) | Böngészőalapú portál a IoT Central. |
|Azure Portal     | Központ, Középső      | [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)létrehozása a következő Azure Portal: , [IoT Central kezelése a Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Böngészőalapú portál az IoT Hub és eszközökhöz. Más Azure-erőforrásokkal is működik, beleértve a IoT Central. |
|Azure IoT Explorer     | Hub | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Nem hozhatók létre IoT Hubok. Egy meglévő IoT Hubhoz csatlakozik az eszközök kezeléséhez. Gyakran használják a CLI-hez vagy a Portálhoz.|
|Azure CLI     | Központ, Középső          | [IoT Hub létrehozása a CLI-val,](../iot-hub/iot-hub-create-using-cli.md) [IoT Central Azure CLI-ről](../iot-central/core/howto-manage-iot-central-from-cli.md) | Parancssori felület IoT-alkalmazások létrehozásához és kezeléséhez. |
|Azure PowerShell     | Központ, Középső   | [IoT Hub létrehozása a PowerShell](../iot-hub/iot-hub-create-using-powershell.md)használatával, a IoT Central [kezelése Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | PowerShell-felület IoT-alkalmazások létrehozásához és kezeléséhez |
|VS Code-hoz készült Azure IoT-eszközök  | Hub | [IoT Hub létrehozása a VS Code-hoz szükséges eszközökkel](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code-bővítmény IoT Hub alkalmazásokhoz. |

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az eszközök Azure IoT-hez való csatlakoztatásának lehetőségeiről, tekintse meg az alábbi rövid útmutatókat:
- IoT Central: Új [Azure IoT Central létrehozása](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [Telemetria küldése egy eszközről IoT Hubra,](../iot-hub/quickstart-send-telemetry-cli.md) és monitor az Azure CLI használatával