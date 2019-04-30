---
title: Megismerheti az Azure Security Center az IoT biztonsági modul twins előzetes verzió |} A Microsoft Docs
description: Ismerje meg a biztonsági ikermodulokkal és azok hogyan használhatók az Azure Security Centerben az IoT fogalmát.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c5814b188c73ea03094d7dae565e40ca09e705c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358814"
---
# <a name="security-module"></a>Biztonsági modul

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk bemutatja, hogyan használja az Azure Security Center (ASC) az IoT ikereszközök és a modulok. 

## <a name="device-twins"></a>Ikereszközök

IoT-megoldások az Azure-ban fejlesztett ikereszközök Eszközkezelés és a folyamatok automatizálásával kulcsfontosságú szerepet játszanak.  

Az IoT ASC kínál szolgáltatás teljesen integrálható a meglévő IoT device-kezelési platform, az, hogy az eszköz biztonsági állapotát kezelése lehetővé teszi az eszközök vezérlési meglévő képességeit használja gyártmány és. Integráció érhető el az IoT Hub végrehajtását ikereszköz mechanizmust.  

További információ fogalma [ikereszközök](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) az Azure IoT Hub. 

## <a name="security-module-twins"></a>Biztonsági ikermodulokkal

ASC IOT egy biztonsági ikermodul a szolgáltatás minden egyes eszközhöz fenntart.
Biztonsági ikermodulja tárolja a megoldás megfelelő az eszköz biztonsági egyes adott eszközhöz tartozó összes információt.
A kijelölt biztonsági ikermodul biztonságosabb kommunikációhoz és a frissítések és karbantartás kevesebb erőforrást igénylő engedélyezése biztonsági eszköztulajdonságok karbantartása.  

Lásd: [létrehozás biztonsági ikermodul](quickstart-create-security-twin.md) és [biztonsági ügynökök konfigurálása](how-to-agent-configuration.md) megtudhatja, hogyan hozhat létre, testre szabhatja, és állítsa be az ikereszköz. Lásd: [ismertetése ikermodulokkal](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) további információ az IoT Hub ikermodulokkal fogalma. 
 

## <a name="see-also"></a>Lásd még
- [ASC for IoT-előzetes verzió](overview.md)
- [Biztonsági ügynökök telepítése](how-to-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerek](concept-security-agent-authentication-methods.md)