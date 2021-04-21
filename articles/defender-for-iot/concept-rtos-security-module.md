---
title: A Defender-IoT-mikroügynök alapszintű magyarázata a Azure RTOS
description: Megismeri a Defender-IoT-mikroügynökkel kapcsolatos alapismereteket a Azure RTOS és munkafolyamatokhoz.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750481"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-mikroügynök Azure RTOS (előzetes verzió)

Ebből a cikkből jobban megértjük a Defender-IoT-micro-agent for Azure RTOS-t, beleértve a funkciókat és előnyöket, valamint a releváns konfigurációkra és referencia-erőforrásokra mutató hivatkozásokat. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-mikroügynök

A Defender-IoT-micro-agent for Azure RTOS átfogó biztonsági megoldást kínál Azure RTOS eszközökhöz a NetXPlex ajánlat részeként. A NetXPlex ajánlatban a Azure RTOS beépített Azure IoT Defender-IoT-mikroügynökkel rendelkezik, és az aktiválás után védelmet nyújt a valós idejű operációsrendszer-eszközökre leselkedő gyakori fenyegetésekre.

A Defender-IoT-micro-agent for Azure RTOS a háttérben fut, és zökkenőmentes felhasználói élményt biztosít, miközben az egyes ügyfelek egyedi kapcsolatainak használatával biztonsági üzeneteket küld a IoT Hub. A Defender-IoT-micro-agent Azure RTOS alapértelmezés szerint engedélyezve van.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetXPlex egy speciális, ipari szintű TCP/IP hálózati verem, amelyet kifejezetten mélyen beágyazott valós idejű és IoT-alkalmazásokhoz terveztek. Azure RTOS NetXPlex egy kettős IPv4- és IPv6-alapú hálózati verem, amely számos protokollt biztosít, beleértve a biztonságot és a felhőt. További információ a [NetX Azure RTOS Megoldásokkal kapcsolatban.](/azure/rtos/netx-duo/)

A modul a következő funkciókat kínálja:

- **Rosszindulatú hálózati tevékenységek észlelése**
- **Az eszköz viselkedési alapkonfigurációi egyéni riasztások alapján**
- **Az eszköz biztonsági biztonságának javítása**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-mikroügynök Azure RTOS architektúrához

A Defender-IoT-micro-agent for Azure RTOS-t az Azure IoT middleware platform inicializálja, és IoT Hub-ügyfelek használatával küld biztonsági telemetriát a Hubnak.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-mikroügynök állapotdiagramja és információs folyamat":::

A Defender-IoT-micro-agent for Azure RTOS a következő eszköztevékenységeket és információkat figyeli három gyűjtő használatával:
- Eszközhálózati **tevékenység TCP,** **UDP** és **ICM**
- **Rendszerinformációk Threadx- és** **NetXPlex-verzióként**
- Szívverési események

Minden egyes gyűjtő egy prioritáscsoporthoz van kapcsolva, és minden prioritáscsoport saját intervallummal rendelkezik, a lehetséges **Alacsony,** **Közepes** és **Magas értékekkel.** Az intervallumok befolyásolják az adatok gyűjtésének és elküldének időközét.

A megoldás további testreszabásához minden időintervallum konfigurálható, és az IoT-összekötők engedélyezhetők és [letilthatók.](how-to-azure-rtos-security-module.md) 

## <a name="supported-security-alerts-and-recommendations"></a>Támogatott biztonsági riasztások és javaslatok

A Defender-IoT-micro-agent for Azure RTOS támogatja az adott biztonsági riasztásokat és javaslatokat. A kezdeti konfiguráció [befejezése](concept-rtos-security-alerts-recommendations.md) után mindenképpen tekintse át és szabja testre a szolgáltatáshoz kapcsolódó riasztási és javaslati értékeket.

## <a name="ready-to-begin"></a>Készen áll a kezdésre?

A Defender-IoT-micro-agent for Azure RTOS ingyenes letöltést biztosítunk az IoT-eszközökhöz. A Defender for IoT felhőszolgáltatás Azure-előfizetésenként 30 napos próbaverzióval érhető el. [Töltse le a Defender-IoT-micro-agent ügynököt,](https://github.com/azure-rtos/azure-iot-preview/releases) és ássunk neki. 

## <a name="next-steps"></a>Következő lépések

- A Defender-IoT-micro-agent első lépései a Azure RTOS [és beállításához.](quickstart-azure-rtos-security-module.md)
- További információ a Biztonsági riasztások és javaslatok támogatásához Azure RTOS Defender-IoT-mikroügynökről. [](concept-rtos-security-alerts-recommendations.md) 
- A Referencia API-hoz használja a Defender-IoT-mikroügynök [Azure RTOS api-t.](azure-rtos-security-module-api.md)