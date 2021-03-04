---
title: Azure Percept-jövőkép Adatlapja
description: Tekintse meg az Azure Percept-jövőkép adatlapját az eszköz részletes leírásához
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 54dae3341910bf863de7e2a2cef832ae670ca09a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097567"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept-jövőkép Adatlapja

Az alább felsorolt specifikációk az Azure [PERCEPT DK](./azure-percept-dk-datasheet.md)-ban található Azure Percept látási eszközre vonatkoznak.

|Termékleírás           |Érték     |
|--------------------------------|---------------------|
|Megcélzott iparágak               |Gyártás <br> Intelligens épületek <br> Automatikus <br> Retail |
|Hero-forgatókönyvek                  |Vásárlói elemzés <br> Polcon rendelkezésre állás <br> Csökkentés csökkentése <br> Munkahelyi monitorozás|
|Dimenziók                      |42mm x 42mm x 40mm (Azure Percept-jövőkép SoM Assembly és Housing) <br> 42mm x 42mm x 6mm (víziós SoM chip)|
|Felügyeleti vezérlő síkja        |Azure-eszköz frissítése (ADU)          |
|Támogatott szoftverek és szolgáltatások |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure-eszköz frissítése |
|AI-gyorsítás                 |Intel Movidius számtalan X (MA2085) látási feldolgozó egység (VPU) az Intel Camera ISP integrált, 0,7 TOPS |
|Érzékelők és vizuális mutatók   |Sony IMX219 kamera-érzékelő 6P-lencsével<br>Megoldás: 8MP on 30FPS, Distance: 50cm-Infinity<br>FoV: 120 fokos átló, szín: széles dinamikus tartomány, rögzített fókuszú redőny|
|Kamera-támogatás                  |RGB (jelenleg) és IR (a jövőben) <br> 2 kamera egyidejűleg is futtatható |
|Biztonsági Crypto-Controller      |ST-Micro STM32L462CE      |
|Verziószámozási/azonosító összetevő       |64 kb EEPROM |
|Memória                          |LPDDR4 2 GB     |
|Energiaellátás                           |3,5 W     |
|Portok                           |1x USB 3,0 C típus <br> 2x MIPI 4 sáv (legfeljebb 1,5 Gbps/sáv)     |
|Vezérlési felületek              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x óra, 2x frame Sync, 2x használaton kívül) <br> 2x tartalék GPIO |
|Tanúsítvány                   |FCC <br> IC <br> RoHS <br> REACH <br> UL   |
|Működési hőmérséklet           |0 – 27 fok (Azure Percept-jövőkép SoM-szerelvény ház használatával) <br> -10 – 70 fok C (víziós SoM chip) |
|Érintési hőmérséklet               |<= 48 °c |
|Relatív páratartalom               |8% – 90%    |
