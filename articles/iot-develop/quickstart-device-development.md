---
title: Azure IoT Embedded-eszközök fejlesztésének rövid útmutatója
description: Egy rövid útmutató, amely bemutatja, hogyan végezheti el a beágyazott eszközök fejlesztését az Azure RTOS és az Azure IoT használatával.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: bc8317e3402fa0185186c06ed1f89e062e99fd5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100654932"
---
# <a name="getting-started-with-azure-iot-embedded-device-development"></a>Az Azure IoT Embedded-eszközök fejlesztésének első lépései

**A** következőkre vonatkozik: [beágyazott eszközök fejlesztése](about-iot-develop.md#embedded-device-development)

Ez az első lépéseket ismertető útmutató néhány rövid útmutatót tartalmaz, amely bemutatja, hogyan kezdheti meg a beágyazott eszközök és az Azure IoT használatát. 

Az egyes gyors útmutatókban a következő alapvető feladatokat kell elvégeznie:
* Beágyazott fejlesztői eszközök telepítése egy adott eszköz programozásához C-ben
* Hozzon létre egy olyan rendszerképet, amely tartalmazza az Azure RTOS-összetevőket és-mintákat, majd egy eszközt
* Eszköz biztonságos csatlakoztatása az Azure IoT
* Az eszköz telemetria megtekintése, a tulajdonságok megtekintése és a felhőből az eszközre irányuló módszerek meghívása

## <a name="quickstarts"></a>Rövid útmutatók
Az első lépéseket ismertető útmutató a következő oktatóanyagokat tartalmazza:

|Gyorsútmutató|Eszköz|
|---------------|-----|
|[Első lépések a ST MICROELECTRONICS B-L475E-IOT01 Discovery Kit-vel](https://go.microsoft.com/fwlink/p/?linkid=2129536) |[ST MICROELECTRONICS B – L475E – IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)|
|[Első lépések a ST MICROELECTRONICS B-L4S5I-IOT01 Discovery Kit-vel](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+) |[ST MICROELECTRONICS B – L4S5I – IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)|
|[Első lépések a NXP MIMXRT1060-EVK próbaverziós csomaggal](https://go.microsoft.com/fwlink/p/?linkid=2129821) |[NXP MIMXRT1060 – EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)|
|[Első lépések a NXP MIMXRT1050-EVKB próbaverziós csomaggal](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB) |[NXP MIMXRT1050 – EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK)|
|[Első lépések a microchip ATSAME54-XPRO próbaverziós csomaggal](https://go.microsoft.com/fwlink/p/?linkid=2129537) |[Microchip ATSAME54 – XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)|
|[Első lépések a MXChip AZ3166 IoT fejlesztői készlet](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166) |[MXChip AZ3166 IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/)|
|[Első lépések a Renesas Starter Kit + for RX65N-2MB](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB) |[Renesas Starter Kit + for RX65N-2MB](https://www.renesas.com/us/en/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)|

## <a name="next-steps"></a>Következő lépések
Miután elvégezte a jelen útmutatóban szereplő, az eszközre vonatkozó rövid útmutatót, tekintse át az eszközre vonatkozó egyéb cikkeket és mintákat az Azure RTOS első lépések tárházában:
* [Ismerkedés az Azure RTOS és az Azure IoT](https://github.com/azure-rtos/getting-started)