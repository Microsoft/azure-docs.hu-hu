---
title: Defender-IoT-Micro-Agent konfigurálása és testreszabása az Azure RTOS
description: Ismerje meg, hogyan konfigurálhatja és szabhatja testre a Defender-IoT-Micro-Agent Azure-RTOS.
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: afab823b6bb187c9a7b7529f52efc37b20e8c66f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778984"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-Micro-Agent konfigurálása és testreszabása az Azure RTOS (előzetes verzió)

Ez a cikk azt ismerteti, hogyan konfigurálható a Defender-IoT-Micro-Agent az Azure RTOS-eszközhöz, hogy megfeleljen a hálózat, a sávszélesség és a memória követelményeinek.

Ki kell választania egy kiterjesztésű cél terjesztési fájlt `*.dist` a `netxduo/addons/azure_iot/azure_iot_security_module/configs` címtárból.  

A CMak-fordítási környezet használatakor a kiválasztott értékhez be kell állítania egy parancssori paramétert `IOT_SECURITY_MODULE_DIST_TARGET` . Például: `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

Az IAR vagy más nem CMak-fordítási környezetekben fel kell vennie az `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` elérési utat bármilyen ismert elérési útra. Például: `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Az eszköz viselkedésének konfigurálásához használja a következő fájlt.

**netxduo/addons/azure_iot/azure_iot_security_module/Inc/configs/ \<target distribution> /asc_config. h**

A CMak-fordítási környezetben a fájl szerkesztésével módosítania kell az alapértelmezett konfigurációt `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` . Használja a következő CMak-formátumot `set(ASC_XXX ON)` vagy az `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` összes többi környezethez tartozó következő fájlt. Például: `#define ASC_XXX`.

Az egyes konfigurációk alapértelmezett viselkedését az alábbi táblázatokban ismertetjük: 

## <a name="general"></a>Általános kérdések

| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Sztring | Defender-IOT-Micro-Agent | Az eszköz egyedi azonosítója.  |
| SECURITY_MODULE_VERSION_ (FŐ) (MÁSODLAGOS) (JAVÍTÁS)  | Szám | 3.2.1 | A verzió. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Szám  | 3 | Az az időtartam, ameddig a Defender-IoT-Micro-Agent elvégzi a biztonsági üzenet küldését a hiba után. (másodperc) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Szám | 300 | A Defender-IoT-Micro-Agent függőben lévő idő (másodpercben). Az állapot felfüggesztve állapotúra változik, ha túllépi az időkorlátot. |

## <a name="collection"></a>Gyűjtemény

| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Szám  | 30  | A gyűjtő indítási gyűjtési intervallumának eltolása Az indítás során a rendszer hozzáadja az értéket a rendszer gyűjteményéhez, hogy ne lehessen egyszerre több eszközről üzeneteket küldeni.  |
| ASC_HIGH_PRIORITY_INTERVAL | Szám | 10 | A gyűjtő magas prioritású csoportjának időköze (másodpercben). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Szám | 30 | A gyűjtő közepes prioritású csoportjának időköze (másodpercben). |
| ASC_LOW_PRIORITY_INTERVAL | Szám | 145 440  | A gyűjtő alacsony prioritású csoportjának időköze (másodpercben). |

#### <a name="collector-network-activity"></a>Adatgyűjtő hálózati tevékenység

A gyűjtő hálózati tevékenység konfigurációjának testreszabásához használja a következőt:

| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Logikai | hamis | Kiszűri a `TCP` hálózati tevékenységet. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Logikai | hamis | A `UDP` hálózati tevékenység eseményeinek szűrése. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Logikai | hamis | A `ICMP` hálózati tevékenység eseményeinek szűrése. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Logikai | true | Csak az egyedi küldésű bejövő csomagokat rögzíti. Ha false értékre van állítva, akkor a szórást és a csoportos küldést is rögzíti. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Logikai  | hamis  | A gyűjtő üres eseményeit küldi el. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Szám | 64 | A memóriában tárolni kívánt IPv4 hálózati események maximális száma. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Szám | 64  | A memóriában tárolni kívánt IPv6-alapú hálózati események maximális száma. |

### <a name="collectors"></a>Naplógyűjtők
| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Logikai | ON | Engedélyezi a szívverés-gyűjtőt. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Logikai | ON | Engedélyezi a hálózati tevékenység gyűjtőjét. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Logikai | ON | Engedélyezi a Rendszerinformáció-gyűjtőt.  |

A többi konfigurációs jelző speciális, és nem támogatott funkciókkal rendelkezik. További információért forduljon az ügyfélszolgálathoz.
 
## <a name="supported-security-alerts-and-recommendations"></a>Támogatott biztonsági riasztások és javaslatok

Az Azure RTOS Defender-IoT-Micro-Agent speciális biztonsági riasztásokat és javaslatokat támogat. Győződjön meg arról, hogy a szolgáltatásra [vonatkozó riasztási és ajánlási értékek áttekintése és testreszabása](concept-rtos-security-alerts-recommendations.md) megtörténjen.

## <a name="log-analytics-optional"></a>Log Analytics (nem kötelező)

Az eszközök eseményeinek és tevékenységeinek vizsgálatához Log Analytics engedélyezheti és konfigurálhatja. További információért olvassa el, hogyan telepítheti és használhatja [log Analytics a Defender for IoT szolgáltatással](how-to-security-data-access.md#log-analytics) . 

## <a name="next-steps"></a>Következő lépések


- A Defender-IoT-Micro-Agent Azure RTOS [biztonsági riasztások és javaslatok](concept-rtos-security-alerts-recommendations.md) áttekintése és testreszabása
- Szükség szerint tekintse meg a [Defender-IoT-Micro-Agent Azure RTOS API](azure-rtos-security-module-api.md) -t.
