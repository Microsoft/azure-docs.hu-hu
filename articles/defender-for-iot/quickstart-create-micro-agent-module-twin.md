---
title: 'Gyors útmutató: Defender IoT Micro Agent-modul létrehozása twin (előzetes verzió)'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egyedi DefenderIotMicroAgent-modult az új eszközökhöz.
ms.date: 1/20/2021
ms.topic: quickstart
ms.openlocfilehash: e1bcaa76e9bfbe417bfd0d4b539b8e0c7ff95c7b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384511"
---
# <a name="quickstart-create-a-defender-iot-micro-agent-module-twin-preview"></a>Gyors útmutató: Defender IoT Micro Agent-modul létrehozása twin (előzetes verzió)

Az új eszközökhöz egyedi **DefenderIotMicroAgent** -modult hozhat létre. Az ikrek modult a IoT Hubban lévő összes eszközhöz is létrehozhatja. 

## <a name="prerequisites"></a>Előfeltételek

- Nincsenek

## <a name="device-twins"></a>Eszköz ikrek 

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is. 

A IoT Defender teljes mértékben integrálható a meglévő IoT-eszközkezelés platformmal. A teljes integráció lehetővé teszi az eszköz biztonsági állapotának kezelését, és lehetővé teszi a meglévő eszköz-vezérlési képességek használatát. Az integráció a IoT Hub Twin mechanizmus használatával érhető el. 

Tudjon meg többet az Azure IoT Hub [eszközön található ikrek](../iot-hub/iot-hub-devguide-device-twins.md)koncepciójának megismeréséről   . 

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-Agent ikrek 

A IoT Defender a Defender-IoT-Micro-Agent Twin eszközöket használja minden eszközhöz. A Defender-IoT-Micro-Agent Twin tartalmazza az eszköz biztonságával kapcsolatos összes információt a megoldás minden egyes eszközén. Az eszköz biztonsági tulajdonságai egy dedikált Defender-IoT-Micro-Agent Twin használatával konfigurálhatók a biztonságosabb kommunikációhoz, a frissítések engedélyezéséhez és a kevesebb erőforrást igénylő karbantartáshoz. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Az ikrek DefenderIotMicroAgent-moduljának ismertetése 

Az eszközök az Azure-ba beépített IoT-megoldások esetében is kulcsszerepet játszanak az eszközök kezelésében és a folyamatok automatizálásában.

A IoT Defender lehetővé teszi a meglévő IoT-eszközkezelés platform teljes integrálását, így kezelheti az eszköz biztonsági állapotát, és használhatja a meglévő eszköz-vezérlési képességeket. Az IoT Hub Twin mechanizmus használatával integrálhatja a Defendert a IoT.  

Ha többet szeretne megtudni az Azure IoT Hub-modulban található ikrek általános koncepciójának megismeréséről, tekintse meg a [IoT hub modul ikrek](../iot-hub/iot-hub-devguide-module-twins.md)című témakört.

A IoT Defender a modul Twin mechanizmusát használja, és `DefenderIotMicroAgent` minden egyes eszközhöz fenntart egy Defender-IoT-Micro-Agent nevű külön nevet. 

Ahhoz, hogy teljes mértékben ki tudja használni a IoT összes Defender-szolgáltatását, létre kell hoznia, konfigurálnia és használniuk kell a Defender-IoT-Micro-Agent ikreket a szolgáltatás minden eszközén. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Twin DefenderIotMicroAgent-modul létrehozása 

A **DefenderIotMicroAgent** -modul ikrek az egyes modulok egyenkénti szerkesztésével hozhatók létre, hogy az egyes eszközök meghatározott konfigurációit tartalmazzák. 

Új **DefenderIotMicroAgent** -modul manuális létrehozása egy eszközhöz: 

1. A IoT Hub keresse meg és válassza ki azt az eszközt, amelyen létre szeretné hozni a Defender-IoT-Micro-Agent Twin elemet. 

1. Válassza a **modul-identitás hozzáadása** lehetőséget. 

1. A **modul identitásának neve**   mezőbe, és írja be a (z  `DefenderIotMicroAgent` ) értéket. 

1. Válassza a **Mentés** lehetőséget. 

## <a name="verify-the-creation-of-a-module-twin"></a>Két modul létrehozásának ellenőrzése 

Annak ellenőrzése, hogy egy Defender-IoT-Micro-Agent Twin létezik-e egy adott eszközhöz: 

1. Az Azure-IoT Hub válassza a **IoT eszközök** lehetőséget   a **felfedezők**   menüből. 

1. Adja meg az eszköz AZONOSÍTÓját, vagy válasszon ki egy beállítást a **lekérdezési eszköz** mezőben, majd válassza az **eszközök lekérdezése** lehetőséget.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Válassza ki a lekérdezési eszközök lehetőséget az eszközök listájának lekéréséhez.":::

1. Válassza ki az eszközt, és nyissa meg az **eszköz adatai** lapot. 

1. Válassza a **modul-identitások**   menüt, és erősítse meg a **DefenderIotMicroAgent** modul létezését az eszközhöz társított modul-identitások listájában.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Válassza ki a modul identitásait a lapról.":::

## <a name="next-steps"></a>Következő lépések 

> [!div class="nextstepaction"]
> [biztonsági javaslatok vizsgálata](quickstart-investigate-security-recommendations.md)
