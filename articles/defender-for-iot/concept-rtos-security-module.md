---
title: Az Azure RTOS Defender-IoT-Micro-Agent alapjainak fogalmi magyarázata
description: Ismerje meg a Defender-IoT-Micro-Agent Azure RTOS-fogalmakkal és-munkafolyamattal kapcsolatos alapismereteket.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 04a499f1feae630d3436c75ae2081413789c0ca3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494234"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-Micro-Agent Azure RTOS (előzetes verzió)

Ennek a cikknek a segítségével jobban megismerheti az Azure RTOS védő-IoT-Micro-Agent szolgáltatásait, beleértve a funkciókat és az előnyöket, valamint a megfelelő konfigurációs és hivatkozási erőforrásokra mutató hivatkozásokat. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender – IoT-Micro-Agent

A Defender-IoT-Micro-Agent for Azure RTOS átfogó biztonsági megoldást nyújt az Azure RTOS-eszközökhöz a NetX Duo-ajánlat részeként. A NetX Duo ajánlatában az Azure RTOS az Azure IoT Defender-IoT-Micro-Agent beépített szolgáltatásával rendelkezik, és az aktiválást követően a valós idejű operációs rendszer eszközeinek általános veszélyforrásait biztosítja. 

Az Azure RTOS készült Defender-IoT-Micro-Agent a háttérben fut, és zökkenőmentes felhasználói élményt nyújt, miközben biztonsági üzeneteket küld az ügyfél egyedi kapcsolatainak a IoT Hub. Az Azure RTOS tartozó Defender-IoT-Micro-Agent alapértelmezés szerint engedélyezve van.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Az Azure RTOS NetX Duo egy fejlett, nagyvállalati szintű TCP/IP hálózati verem, amely kifejezetten a mélyen beágyazott valós idejű és IoT alkalmazásokhoz készült. Az Azure RTOS NetX Duo egy kettős IPv4-és IPv6-alapú hálózati verem, amely a protokollok széles választékát biztosítja, beleértve a biztonságot és a felhőt is. További információ az [Azure RTOS NetX Duo](/azure/rtos/netx-duo/) megoldásairól.

A modul a következő funkciókat kínálja:

- **Kártékony hálózati tevékenységek észlelése**
- **Az eszköz viselkedési alapkonfigurációi az egyéni riasztások alapján**
- **Az eszköz biztonsági higiéniájának javítása**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-Micro-Agent Azure RTOS architektúrához

Az Azure RTOS készült Defender-IoT-Micro-Agent inicializálása az Azure IoT middleware platformjának használatával történik, és IoT Hub-ügyfelekkel küld biztonsági telemetria a központba.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender – IoT – Micro-Agent állapot diagram és információáramlás":::

Az Azure RTOS készült Defender-IoT-Micro-Agent a következő eszköz-tevékenységeket és-információkat figyeli három gyűjtő használatával:
- Eszköz hálózati tevékenységének **TCP**, **UDP** és **ICM**
- Rendszerinformációk **Threadx** és **NetX Duo** verziókként
- Szívverési események

Minden gyűjtő egy prioritási csoporthoz van csatolva, és minden prioritási csoportnak saját intervalluma van, amely **alacsony**, **közepes** és **magas** lehetséges értékeket tartalmaz. Az intervallumok befolyásolják az adatok gyűjtésének és elküldéseinak időintervallumát.

Minden időintervallum konfigurálható, és a IoT-összekötők engedélyezhetők és letilthatók a megoldás további [testreszabása](how-to-azure-rtos-security-module.md)érdekében. 

## <a name="supported-security-alerts-and-recommendations"></a>Támogatott biztonsági riasztások és javaslatok

Az Azure RTOS Defender-IoT-Micro-Agent speciális biztonsági riasztásokat és javaslatokat támogat. A kezdeti konfiguráció befejezése után ellenőrizze [és szabja testre a szolgáltatás vonatkozó riasztási és javaslati értékeit](concept-rtos-security-alerts-recommendations.md) .

## <a name="ready-to-begin"></a>Készen áll a kezdésre?

Az Azure RTOS készült Defender-IoT-Micro-Agent ingyenes letöltést biztosít a IoT-eszközökhöz. A IoT Cloud Service Defender egy 30 napos próbaidőszakot biztosít az Azure-előfizetések esetében. [Töltse le most a Defender-IoT-Micro-agentet](https://github.com/azure-rtos/azure-iot-preview/releases) , és ismerkedjen meg az első lépésekkel. 

## <a name="next-steps"></a>Következő lépések

- Ismerkedés a Defender-IoT-Micro-Agent Azure RTOS [előfeltételeivel és beállításával](quickstart-azure-rtos-security-module.md).
- További információ a Defender-IoT-Micro-Agent Azure RTOS [biztonsági riasztásokról és a javaslatok támogatásáról](concept-rtos-security-alerts-recommendations.md). 
- A Defender-IoT-Micro-Agent használata az Azure RTOS [Reference API](azure-rtos-security-module-api.md)-hoz.