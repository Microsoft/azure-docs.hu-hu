---
title: IoT-Plug and Play használata korlátozott eszközökön | Microsoft Docs
description: Ismerje meg, hogyan valósíthat meg IoT Plug and Play a korlátozott eszközökön a beágyazott C vagy Azure RTOS SDK-val.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfa71e02688d5bf63d894ae42dabab7da1d4174c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057314"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>IoT-Plug and Play implementálása korlátozott eszközökön

Ha *korlátozott eszközöket* fejleszt, használhatja a IoT Plug and Playt az Azure SDK-val a [beágyazott C IoT ügyféloldali kódtárak](https://aka.ms/embeddedcsdk) vagy az [Azure RTOS](/azure/rtos/overview-rtos)használatával. Ez a cikk a korlátozott forgatókönyvek hivatkozásait és erőforrásait tartalmazza.

## <a name="use-the-sdk-for-embedded-c"></a>Az SDK használata beágyazott C-hez

A Embedded C-hez készült SDK egy könnyű megoldást kínál a korlátozott eszközök Azure IoT-szolgáltatásokhoz való csatlakoztatására, beleértve a [IoT Plug and Play konvenciókat](concepts-convention.md)is. Az alábbi hivatkozások a valódi eszközre és az oktatási és hibakeresési célokra szolgáló mintákat tartalmaznak.

### <a name="use-a-real-device"></a>Valós eszköz használata

A beágyazott C-hez készült SDK-val, a Device kiépítési szolgáltatással és a IoT Plug and Playával kapcsolatos teljes körű oktatóanyagért tekintse meg a [repurpose PIC-IoT WX fejlesztői Tanácsának az Azure-hoz való kapcsolódást IoT hub Device Provisioning Service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)használatával című témakört.

### <a name="introductory-samples"></a>Bevezető minták

A beágyazott C tárház SDK-je [számos mintát](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) tartalmaz, amelyek bemutatják, hogyan használhatja a IoT Plug and Play:

> [!NOTE]
> Ezek a minták a Windows és Linux rendszereken, oktatási és hibakeresési célokra futnak. Éles környezetben a minták kizárólag korlátozott eszközökhöz készültek.

- [Termosztát – példa a Embedded C-hez készült SDK-val](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Példa a hőmérséklet-vezérlőre a Embedded C SDK-val](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Az Azure RTOS használata

Az Azure RTOS egy egyszerűsített réteget tartalmaz, amely natív kapcsolatot biztosít az Azure IoT Cloud Services szolgáltatással. Ez a réteg egy egyszerű mechanizmust biztosít a korlátozott eszközök Azure-IoT való csatlakoztatásához az Azure RTOS speciális funkcióinak használatakor. További információ: [What is Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Eszközlánccal

Az Azure RTOS-minták különböző típusú IDE-és toolchain-kombinációkkal rendelkeznek, például:

- IAR: IAR [beágyazott Workbench](https://www.iar.com/iar-embedded-workbench/) ide
- GCC/CMak: a nyílt forráskódú [CMAK](https://cmake.org/) -Build rendszerre és a [GNU ARM Embedded toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)épül.
- MCUExpresso: NXP [MCUXPRESSO ide](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronic [STM32CUBE ide](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: mikrochip [MPLAB X ide](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>Példák

A különböző eszközök Azure RTOS és IoT Plug and Play használatával történő megkezdését bemutató példákért tekintse meg a következő táblázatot:

Gyártó | Eszköz | Példák |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMak](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B – L475E – IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B – L4S5I – IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMAK](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a IoT Plug and Play a korlátozott eszközökön való megvalósításának lehetőségeit, a következő lépés a [IoT Plug and Play konvencióinak](concepts-convention.md)megismerése.