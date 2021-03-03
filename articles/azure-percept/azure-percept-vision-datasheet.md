---
title: Azure Percept-jövőkép Adatlapja
description: Tekintse meg az Azure Percept-jövőkép adatlapját az eszköz részletes leírásához
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662305"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept-jövőkép Adatlapja

Az alább felsorolt specifikációk az Azure [PERCEPT DK](./azure-percept-dk-datasheet.md)-ban található Azure Percept látási eszközre vonatkoznak.

|Termékleírás           |Érték     |
|--------------------------------|---------------------|
|Megcélzott iparágak               |Gyártás <br> Intelligens épületek <br> Automatikus <br> Retail |
|Hero-forgatókönyvek                  |Vásárlói elemzés <br> Polcon rendelkezésre állás <br> Csökkentés csökkentése <br> Biztonság/felügyelet <br> Munkahelyi biztonság|
|Dimenziók                      |42mm x 42mm x 40mm (Azure Percept-jövőkép SoM Assembly és Housing) <br> 42mm x 42mm x 6mm (víziós SoM chip)|
|Felügyeleti vezérlő síkja        |Azure-eszköz frissítése (ADU)          |
|Támogatott szoftverek és szolgáltatások |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure-eszköz frissítése |
|AI-gyorsítás                 |Intel Movidius számtalan X (MA2085) látási feldolgozó egység (VPU) az Intel Camera ISP integrált, 0,7 TOPS |
|Érzékelők és vizuális mutatók   |Omni-jövőkép 5670 kamera <br> GSO GS8882AA-lencse (felbontás: 5MP, 30FPS, távolság: 50cm-Infinity, FoV: 120 fok, szín: széles dinamikus tartomány, rögzített fókuszú redőny)          |
|Kamera-támogatás                  |RGB, IR és mélységmérő kamerák <br> 2 kamera egyidejűleg is futtatható |
|Biztonsági Crypto-Controller      |ST-Micro STM32L462CE      |
|Verziószámozási/azonosító összetevő       |64 kb EEPROM |
|Memória                          |LPDDR4 2 GB     |
|Energiaellátás                           |3,5 W     |
|Portok                           |1x USB 3,0 C típus <br> 2x MIPI 4 sáv (legfeljebb 1,5 Gbps/sáv)     |
|Vezérlési felületek              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x óra, 2x frame Sync, 2x használaton kívül) <br> 2x tartalék GPIO |
|Tanúsítvány                   |CE <br> FCC      |
|Működési hőmérséklet           |0 – 27 fok (Azure Percept-jövőkép SoM-szerelvény ház használatával) <br> -10 – 70 fok C (víziós SoM chip) |
|Érintési hőmérséklet               |<= 48 °c |
|Relatív páratartalom               |8% – 90%    |