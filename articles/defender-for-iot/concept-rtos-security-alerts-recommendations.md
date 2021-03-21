---
title: Defender-IoT-Micro-Agent Azure RTOS beépített & testreszabható riasztások és javaslatok
description: Ismerje meg a biztonsági riasztásokat és az ajánlott szervizelést az Azure IoT Defender-IoT-Micro-Agent-RTOS használatával.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: d6dfeb01e174d71c926e9fcf7f5f1ee078524cf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494274"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender-IoT-Micro-Agent Azure RTOS biztonsági riasztások és javaslatok (előzetes verzió)

A Defender-IoT-Micro-Agent for Azure RTOS folyamatosan elemzi a IoT-megoldást a fejlett analitikai és fenyegetési intelligenciával, hogy figyelmeztesse Önt a lehetséges rosszindulatú tevékenységekre és a gyanús rendszermódosításokra. Egyéni riasztásokat is létrehozhat a várt eszköz viselkedésének és alapértékének ismerete alapján.

Az Azure RTOS-alapú Defender-IoT-Micro-Agent riasztás a lehetséges kompromisszumok jeleként működik, és meg kell vizsgálni és szervizelni kell. Az Azure RTOS-hez készült Defender-IoT-Micro-Agent ajánlás a gyenge biztonsági helyzet javítását és frissítését azonosítja. 

Ebben a cikkben a beépített riasztások és javaslatok listáját találja, amelyeket az alapértelmezett tartományok alapján indít el, és testreszabható a saját értékekkel a várt vagy az alapkonfiguráció alapján. 

Ha többet szeretne megtudni arról, hogyan működik a riasztások testreszabása a Defender for IoT szolgáltatásban, tekintse meg a [testreszabható riasztásokat](concept-customizable-security-alerts.md). Az Azure RTOS Defender-IoT-Micro-Agent használatakor az alábbi táblázatokban részletesen ismertetett riasztások és javaslatok érhetők el a testreszabáshoz. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-IoT-Micro-Agent Azure RTOS – támogatott biztonsági riasztások

### <a name="device-related-security-alerts"></a>Eszközhöz kapcsolódó biztonsági riasztások

|Eszközhöz kapcsolódó biztonsági riasztási tevékenység  |Riasztás neve  |
|---------|---------|
|IP-cím| A rendszer gyanús IP-címmel folytatott kommunikációt észlelt|
|X. 509 eszköz tanúsítványának ujjlenyomata|Az X. 509 eszköz tanúsítványának ujjlenyomata nem egyezik|
|X. 509 tanúsítvány| Az X. 509 tanúsítvány lejárt|
|SAS-token| Lejárt SAS-jogkivonat|
|SAS-token| Érvénytelen SAS-jogkivonat aláírása|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub kapcsolatos biztonsági riasztások

|IoT Hub biztonsági riasztási tevékenység  |Riasztás neve  |
|---------|---------|
|Tanúsítvány hozzáadása    |  A rendszer sikertelen kísérletet észlelt a tanúsítvány hozzáadásához egy IoT Hub       |
|Diagnosztikai beállítások hozzáadása vagy szerkesztése    | A rendszer egy IoT Hub diagnosztikai beállításainak hozzáadására vagy szerkesztésére tett kísérletet észlelt.      |
|Tanúsítvány törlése    |  A rendszer sikertelen kísérletet észlelt a tanúsítvány törlésére egy IoT Hub       |
|Diagnosztikai beállítások törlése    |  A rendszer a diagnosztikai beállítások törlését észlelte egy IoT Hub      |
|Tanúsítvány törölve    | Tanúsítvány észlelése egy IoT Hubból        |
|Új tanúsítvány     |  Új tanúsítvány hozzáadásának észlelése egy IoT Hub       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender-IoT-Micro-Agent Azure RTOS – támogatott testreszabható riasztások

### <a name="device-related-customizable-alerts"></a>Eszközhöz kapcsolódó testreszabható riasztások

|Eszközhöz kapcsolódó tevékenység |Riasztás neve  |
|---------|---------|
|Aktív kapcsolatok|Az aktív kapcsolatok száma nem a megengedett tartományba esik|
|Felhőbeli eszközök üzenetei a **MQTT** protokollban|A **MQTT** -protokollban lévő üzenetek Felhőbeli üzeneteinek száma nem a megengedett tartományba esik.|
|Kimenő kapcsolatok| Nem engedélyezett IP-címhez való kimenő kapcsolódás|

### <a name="hub-related-customizable-alerts"></a>Hubhoz kapcsolódó testreszabható riasztások 

|Hubhoz kapcsolódó tevékenység  |Riasztás neve  |
|---------|---------|
|A parancssori törlések     |  A várólista-kiürítések száma az engedélyezett tartományon kívül       |
|Felhőbeli eszközök üzenetei a **MQTT** protokollban    |  A **MQTT** protokollban az engedélyezett tartományon kívül eső Felhőbeli üzenetek száma       |
|Eszközről a felhőbe irányuló üzenetek a **MQTT** protokollban    | Az eszköz és a felhő üzeneteinek száma az **MQTT** protokollban az engedélyezett tartományon kívül        |
|A Direct metódus meghívja     |  Az engedélyezett tartományon kívülre meghívó közvetlen metódusok száma       |
|Elutasította a felhőből az eszközök üzeneteit a **MQTT** protokollban     |   Elutasított Felhőbeli üzenetek száma az **MQTT** protokollban az engedélyezett tartományon kívül      |
|A Twin modulok frissítései     |  Az engedélyezett tartományon kívüli Twin modulok frissítéseinek száma       |
|Jogosulatlan műveletek    |  Az engedélyezett tartományon kívüli jogosulatlan műveletek száma       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-IoT-Micro-Agent Azure RTOS – támogatott javaslatok

### <a name="device-related-recommendations"></a>Eszközhöz kapcsolódó javaslatok

|Eszközhöz kapcsolódó tevékenység  |Javaslat neve |
|---------|---------|
|Hitelesítő adatok    |  Több eszköz által használt azonos hitelesítési hitelesítő adatok       |

### <a name="hub-related-recommendations"></a>Hubhoz kapcsolódó javaslatok

|IoT Hub kapcsolódó tevékenység  |Javaslat neve |
|---------|---------|
|IP-szűrési házirend   |  Az alapértelmezett IP-szűrési házirendet a **Megtagadás** értékre kell állítani  |
|IP-szűrési szabály| Az IP-szűrési szabály nagyméretű IP-címtartományt tartalmaz|
|Diagnosztikai naplók|A diagnosztikai naplók engedélyezésének javaslata IoT Hub|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>IoT-riasztások és-javaslatok minden Defender számára

A IoT szolgáltatással kapcsolatos riasztások és javaslatok teljes listáját a IoT [biztonsági riasztások](concept-security-alerts.md), IoT biztonsági [javaslatok](concept-recommendations.md)című részben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

- [Gyors útmutató: Defender-IoT-Micro-Agent Azure RTOS](quickstart-azure-rtos-security-module.md)
- [Defender-IoT-Micro-Agent konfigurálása és testreszabása az Azure RTOS](how-to-azure-rtos-security-module.md)
- Tekintse meg a [Defender-IoT-Micro-Agent Azure RTOS API](azure-rtos-security-module-api.md) -t