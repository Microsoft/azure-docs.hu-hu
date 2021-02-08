---
title: Egyéni biztonsági riasztások
description: Ismerje meg a testreszabható biztonsági riasztásokat, valamint ajánlott szervizelést a Defender használatával a IoT szolgáltatásainak és szolgáltatásainak használatához.
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
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809286"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Defender for IoT – egyéni biztonsági riasztások

A IoT Defender folyamatosan elemzi a IoT-megoldást a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztessen a rosszindulatú tevékenységekre.

Javasoljuk, hogy hozzon létre egyéni riasztásokat a várt eszköz viselkedésének ismerete alapján, hogy a riasztások a leghatékonyabb mutatók legyenek a lehető leghatékonyabban az egyedi szervezeti üzembe helyezés és a környezet szempontjából.

A IoT-riasztások következő listáját a várt IoT Hub és/vagy az eszköz viselkedése alapján meghatározhatja. További információ az egyes riasztások testreszabásáról: [egyéni riasztások létrehozása](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Beépített egyéni riasztások a IoT Hub

| Súlyosság | Riasztás neve | Adatforrás | Description | Javasolt szervizelés |
|--|--|--|--|--|
| Alacsony | Egyéni riasztás – az AMQP-protokollban az eszközökön lévő Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időszakon belül a felhőből az eszközre irányuló üzenetek (AMQP protokoll) száma a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az AMQP-protokollban lévő elutasított Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Az eszköz által visszautasított Felhőbeli üzenetek (AMQP protokoll) száma egy adott időszakon belül a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma az AMQP protokollon kívül esik az engedélyezett tartományon. | IoT Hub | Az eszközről a felhőbe irányuló üzenetek (AMQP protokoll) egy adott időintervallumon belül a jelenleg konfigurált és engedélyezett tartományon kívül esnek. |  |
| Alacsony | Egyéni riasztás – a meghívott közvetlen metódusok száma kívül esik az engedélyezett tartományon. | IoT Hub | A megadott időszakon belül meghívott közvetlen metódusok mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – a fájlfeltöltés száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időtartományon belüli fájlfeltöltés mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – a HTTP-protokollon belül a felhőben lévő üzenetek száma a megengedett tartományon kívül esik | IoT Hub | Egy időablakban a felhőből az eszközre irányuló üzenetek (HTTP-protokoll) mennyisége nem a konfigurált engedélyezett tartományba esik. |
| Alacsony | Egyéni riasztás – a HTTP-protokollon belül visszautasított Felhőbeli üzenetek száma nem az engedélyezett tartományba esik. | IoT Hub | A megadott időintervallumon belül a felhőből az eszközre irányuló üzenetek (HTTP-protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony | Egyéni riasztás – az eszköz és a felhő közötti üzenetek száma a HTTP-protokollon kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időintervallumon belül a Felhőbeli üzenetek (HTTP-protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az MQTT-protokollban az eszközökön lévő Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időszakon belül a felhőből az eszközre irányuló üzenetek (MQTT protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – az MQTT-protokollban lévő elutasított Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub | Az eszköz által az adott időszakon belül visszautasított felhő-eszköz üzenetek (MQTT protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma az MQTT protokollon kívül esik az engedélyezett tartományon. | IoT Hub | Az eszközről a felhőbe irányuló üzenetek (MQTT protokoll) egy adott időintervallumon belül a jelenleg konfigurált és engedélyezett tartományon kívül esnek. |
| Alacsony | Egyéni riasztás – az engedélyezett tartományon kívül eső parancssori törlések száma | IoT Hub | Egy adott időszakon belül a parancssori törlések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – a modul két frissítésének száma kívül esik az engedélyezett tartományon. | IoT Hub | Egy adott időszakon belül a modulhoz tartozó dupla frissítések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony | Egyéni riasztás – a jogosulatlan műveletek száma az engedélyezett tartományon kívül esik | IoT Hub | Egy adott időszakon belül nem engedélyezett műveletek mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |


## <a name="agent-based-security-custom-alerts"></a>Ügynök-alapú biztonsági egyéni riasztások

| Súlyosság | Riasztás neve | Adatforrás | Description | Javasolt szervizelés |
|--|--|--|--|--|
| Alacsony | Egyéni riasztás – az aktív kapcsolatok száma kívül esik az engedélyezett tartományon. | Klasszikus biztonsági modul, Azure RTOS | A megadott időszakon belüli aktív kapcsolatok száma a jelenleg konfigurált és engedélyezett tartományon kívül esik. | Vizsgálja meg az eszköz naplóit. Ismerje meg, hogy a kapcsolatok honnan származnak, és hogy milyen jóindulatú vagy rosszindulatú. Ha rosszindulatú, távolítsa el a lehetséges kártevőket, és értse a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett kapcsolatok listájához. |
| Alacsony | Egyéni riasztás – a nem engedélyezett IP-címhez létrehozott kimenő kapcsolatok | Klasszikus biztonsági modul, Azure RTOS | Egy kimenő kapcsolat egy olyan IP-címhez lett létrehozva, amely kívül esik az engedélyezett IP-listán. | Vizsgálja meg az eszköz naplóit. Ismerje meg, hogy a kapcsolatok honnan származnak, és hogy milyen jóindulatú vagy rosszindulatú. Ha rosszindulatú, távolítsa el a lehetséges kártevőket, és értse a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett IP-listához. |
| Alacsony | Egyéni riasztás – a sikertelen helyi bejelentkezések száma kívül esik az engedélyezett tartományon. | Klasszikus biztonsági modul, Azure RTOS | Egy adott időszakon belül a sikertelen helyi bejelentkezések száma a jelenleg konfigurált és engedélyezett tartományon kívül esik. |  |
| Alacsony | Egyéni riasztás – olyan felhasználó bejelentkezését, amely nem szerepel az engedélyezett felhasználók listáján | Klasszikus biztonsági modul, Azure RTOS | Az engedélyezett felhasználók listáján kívüli helyi felhasználó, amely be van jelentkezve az eszközre. | Nyers adatmentéskor keresse meg a log Analytics-fiókját, és az adataival vizsgálja meg az eszközt, azonosítsa a forrást, majd javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. Ha jelenleg nem menti a nyers adatmentést, nyissa meg az eszközt, és javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. |
| Alacsony | Egyéni riasztás – A folyamat végrehajtása nem engedélyezett | Klasszikus biztonsági modul, Azure RTOS | Nem engedélyezett folyamat lett végrehajtva az eszközön. | Nyers adatmentéskor keresse meg a log Analytics-fiókját, és az adataival vizsgálja meg az eszközt, azonosítsa a forrást, majd javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. Ha jelenleg nem menti a nyers adatmentést, nyissa meg az eszközt, és javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. |
|

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [riasztások testreszabásáról](quickstart-create-custom-alerts.md)
- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
