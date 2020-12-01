---
title: Az Azure RTOS biztonsági moduljának alapjai – elvi magyarázat
description: Ismerje meg az Azure RTOS-fogalmak és-munkafolyamatok biztonsági moduljának alapjait.
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
ms.openlocfilehash: 8f521bd593369509fd520831f90ce9c601227f09
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340050"
---
# <a name="security-module-for-azure-rtos-preview"></a>Biztonsági modul az Azure RTOS (előzetes verzió)

Ebből a cikkből megismerheti az Azure RTOS biztonsági moduljának jobb megismerését, beleértve a funkciókat és az előnyöket, valamint a megfelelő konfigurációs és hivatkozási erőforrásokra mutató hivatkozásokat. 

## <a name="azure-rtos-iot-security-module"></a>Azure RTOS IoT biztonsági modul

Az Azure RTOS biztonsági modulja átfogó biztonsági megoldást nyújt az Azure RTOS-eszközökhöz a NetX Duo-ajánlat részeként. A NetX Duo ajánlatában az Azure RTOS a beépített Azure IoT biztonsági modullal rendelkezik, és az aktiválást követően a valós idejű operációs rendszer eszközeinek általános veszélyforrásait biztosítja. 

Az Azure RTOS biztonsági modulja a háttérben fut, és zökkenőmentes felhasználói élményt nyújt, miközben biztonsági üzeneteket küld a IoT Hubhoz tartozó ügyfelek egyedi kapcsolatainak használatával. Az Azure RTOS biztonsági modulja alapértelmezés szerint engedélyezve van.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Az Azure RTOS NetX Duo egy fejlett, nagyvállalati szintű TCP/IP hálózati verem, amely kifejezetten a mélyen beágyazott valós idejű és IoT alkalmazásokhoz készült. Az Azure RTOS NetX Duo egy kettős IPv4-és IPv6-alapú hálózati verem, amely a protokollok széles választékát biztosítja, beleértve a biztonságot és a felhőt is. További információ az [Azure RTOS NetX Duo](/azure/rtos/netx-duo/) megoldásairól.

A modul a következő funkciókat kínálja:

- **Kártékony hálózati tevékenységek észlelése**
- **Az eszköz viselkedési alapkonfigurációi az egyéni riasztások alapján**
- **Az eszköz biztonsági higiéniájának javítása**

## <a name="security-module-for-azure-rtos-architecture"></a>Biztonsági modul az Azure RTOS architektúrához

Az Azure RTOS biztonsági modulját az Azure IoT middleware-platformja inicializálja, és IoT Hub-ügyfeleket használ a biztonsági telemetria elküldésére a központba.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT biztonsági modul állapotának diagramja és információáramlás":::

Az Azure RTOS biztonsági modulja három gyűjtő használatával figyeli a következő eszköz-tevékenységeket és-információkat:
- Eszköz hálózati tevékenységének **TCP**, **UDP** és **ICM**
- Rendszerinformációk **Threadx** és **NetX Duo** verziókként
- Szívverési események

Minden gyűjtő egy prioritási csoporthoz van csatolva, és minden prioritási csoportnak saját intervalluma van, amely **alacsony**, **közepes** és **magas** lehetséges értékeket tartalmaz. Az intervallumok befolyásolják az adatok gyűjtésének és elküldéseinak időintervallumát.

Minden időintervallum konfigurálható, és a IoT-összekötők engedélyezhetők és letilthatók a megoldás további [testreszabása](how-to-azure-rtos-security-module.md)érdekében. 

## <a name="supported-security-alerts-and-recommendations"></a>Támogatott biztonsági riasztások és javaslatok

Az Azure RTOS biztonsági modulja támogatja a konkrét biztonsági riasztásokat és javaslatokat. A kezdeti konfiguráció befejezése után ellenőrizze [és szabja testre a szolgáltatás vonatkozó riasztási és javaslati értékeit](concept-rtos-security-alerts-recommendations.md) .

## <a name="ready-to-begin"></a>Készen áll a kezdésre?

Az Azure RTOS biztonsági modulja ingyenes letöltést biztosít a IoT-eszközökhöz. A IoT Cloud Service Defender egy 30 napos próbaidőszakot biztosít az Azure-előfizetések esetében. [Töltse le most a biztonsági modult](https://github.com/azure-rtos/azure-iot-preview/releases) , és ismerkedjen meg az első lépésekkel. 

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg az Azure RTOS [előfeltételeinek és beállításának](quickstart-azure-rtos-security-module.md)biztonsági moduljának használatába.
- További információ az Azure RTOS biztonsági [riasztások és javaslatok támogatásával](concept-rtos-security-alerts-recommendations.md)kapcsolatos biztonsági modulról. 
- Használja az Azure RTOS [Reference API](azure-rtos-security-module-api.md)biztonsági modulját.