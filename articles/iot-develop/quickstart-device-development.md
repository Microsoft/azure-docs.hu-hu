---
title: Azure IoT Embedded-eszközfejlesztés – rövid útmutató
description: Rövid útmutató, amely bemutatja, hogyan lehet beágyazott eszközfejlesztést Azure RTOS Azure IoT használatával.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 536b9092a7bddd353b58874e7aa97291839bafcc
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589533"
---
# <a name="getting-started-with-azure-iot-embedded-device-development"></a>Ismerkedés az Azure IoT embedded-eszközfejlesztéssel

**A következőkre vonatkozik:** [Beágyazott eszközfejlesztés](about-iot-develop.md#embedded-device-development)

Ez az első lépések útmutatója rövid útmutatókat tartalmaz, amelyek bemutatja, hogyan kezdheti el a munkát a beágyazott eszközökkel és az Azure IoT-val. 

Minden rövid útmutatóban a következő alapszintű feladatokat kell elvégeznie:
* Beágyazott fejlesztői eszközök telepítése egy adott eszköz C nyelvben való programozáshoz
* Készítsen egy olyan rendszerképet, amely Azure RTOS összetevőket és mintákat tartalmaz, majd egy eszközt flash
* Eszköz biztonságos csatlakoztatása az Azure IoT-hez
* Eszköz-telemetria megtekintése, tulajdonságok megtekintése és felhő-eszköz metódusok meghívása

## <a name="quickstarts"></a>Rövid útmutatók
Az első lépéseket ismertető útmutató a következő oktatóanyagokat tartalmazza:

|Gyorsútmutató|Eszköz|
|---------------|-----|
|[Az ST Mikroszolgáltatások B-L475E-IOT01 felderítési készlet – első lépések](https://go.microsoft.com/fwlink/p/?linkid=2129536) |[ST-mikroszolgáltatások B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)|
|[Az ST Mikroszolgáltatások B-L4S5I-IOT01 felderítési készlet – első lépések](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+) |[ST-mikroszolgáltatások B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)|
|[Az NXP MIMXRT1060-EVK evaluation készlet első lépések](https://go.microsoft.com/fwlink/p/?linkid=2129821) |[NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)|
|[Az NXP MIMXRT1050-EVKB kiértékelési készlet első lépések](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB) |[NXP MIMXRT1050-EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK)|
|[A Microchip ATSAME54-XPRO értékelési készlet első lépések](https://go.microsoft.com/fwlink/p/?linkid=2129537) |[Microchip ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)|
|[Az MXChip AZ3166 IoT DevKit első lépések](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166) |[MXChip AZ3166 IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)|
|[Ismerkedés a Renesas Starter Kit+ for RX65N-2MB csomaggal](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB) |[Renesas Starter Kit+ for RX65N-2MB](https://www.renesas.com/us/en/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)|

## <a name="next-steps"></a>Következő lépések
Miután befejezte a jelen útmutató eszközspecifikus rövid útmutatóját, tekintse meg a következő eszközspecifikus cikkeket és mintákat a Azure RTOS tárban:
* [Ismerkedés a Azure RTOS És az Azure IoT használatával](https://github.com/azure-rtos/getting-started#getting-started-with-azure-rtos-and-azure-iot)