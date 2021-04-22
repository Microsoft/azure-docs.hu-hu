---
title: Az IoT-Plug and Play használata korlátozott eszközökön | Microsoft Docs
description: Megtudhatja, hogyan implementálja az IoT-Plug and Play korlátozott eszközökön az Embedded C SDK vagy a Azure RTOS.
author: EliotSeattle
ms.author: eliotgra
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6c792fbbb44b7dc769e7cdc56850684bd69ea40b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864136"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>IoT-Plug and Play korlátozott eszközökön

Ha korlátozott eszközökhöz fejleszt, használhatja az IoT Plug and Play-t az [Azure SDK for Embedded C IoT ügyfélkódtárakkal](https://aka.ms/embeddedcsdk) vagy a [Azure RTOS.](/azure/rtos/overview-rtos) Ez a cikk hivatkozásokat és erőforrásokat tartalmaz ezekre a korlátozott forgatókönyvekre.

## <a name="use-the-sdk-for-embedded-c"></a>Az Embedded C SDK használata

Az Embedded C SDK egyszerű megoldást kínál a korlátozott eszközök Azure IoT-szolgáltatásokhoz való csatlakoztatásához, beleértve az [IoT Plug and Play konvencióit.](concepts-convention.md) Az alábbi hivatkozások valós eszközökre, valamint oktatási és hibakeresési célokra használható mintákat tartalmaznak.

### <a name="use-a-real-device"></a>Valós eszköz használata

Az Embedded C SDK, a Device Provisioning Service és az IoT Plug and Play valós eszközön való használatával kapcsolatos teljes körű oktatóanyagért lásd: [Repurpose PIC-IoT Wx Development Board to Connect to Azure](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)through IoT Hub Device Provisioning Service ..

### <a name="introductory-samples"></a>Bevezető minták

Az Embedded C-adattár SDK-ja számos mintát tartalmaz, amelyek megmutatják, hogyan használhatja az IoT Plug and Play: [](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample)

> [!NOTE]
> Ezek a minták Oktatási és hibakeresési célokból Windows és Linux rendszeren futnak. Éles környezetben a minták csak korlátozott eszközökhöz használhatók.

- [Termosztátos példa az Embedded C-hez készült SDK-val](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Hőmérsékletvezérlő-példa az Embedded C SDK-val](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>A Azure RTOS

Azure RTOS réteg egy egyszerűsített réteget tartalmaz, amely natív kapcsolatot biztosít az Azure IoT-felhőszolgáltatásokhoz. Ez a réteg egy egyszerű mechanizmust biztosít a korlátozott eszközök Azure IoT-hez való csatlakoztatásához, miközben a korlátozott eszközök Azure RTOS. További információ: [What is Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Eszközláncok

A Azure RTOS minta különböző IDE- és eszközlánckombinációkat biztosít, például:

- IAR: IAR Embedded [Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: A nyílt forráskódú [CMake](https://cmake.org/) buildrendszerre és a [GNU Arm Embedded eszközláncra épít.](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)
- MCUExpresso: NXP [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroeleckup [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: Microchip [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>Példák

Az alábbi táblázatban olyan példákat láthat, amelyekből Azure RTOS és IoT-Plug and Play különböző eszközökön való első lépéseket:

Gyártó | Eszköz | Példák |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| Nxp | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelecházs | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelecházs | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelecházs | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az IoT Plug and Play korlátozott eszközökön való megvalósításának lehetőségeit, javasolt következő lépésként megismeri az IoT Plug and Play [konvencióit.](concepts-convention.md)