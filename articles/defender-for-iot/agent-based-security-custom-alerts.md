---
title: Ügynök-alapú biztonsági egyéni riasztások
titleSuffix: Azure Defender for IoT
description: Ismerje meg a testreszabható biztonsági riasztásokat, valamint ajánlott szervizelést a Defender használatával a IoT-eszköz funkcióinak és szolgáltatásainak használatához.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: e885566dd067d70fd4800ca96b8729494464da85
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642518"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Defender IoT-eszközökhöz egyéni biztonsági riasztások

A IoT Defender folyamatosan elemzi a IoT-megoldást a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztessen a rosszindulatú tevékenységekre.

Javasoljuk, hogy hozzon létre egyéni riasztásokat a várt eszköz viselkedésének ismerete alapján, hogy a riasztások a leghatékonyabb mutatók legyenek a lehető leghatékonyabban az egyedi szervezeti üzembe helyezés és a környezet szempontjából.

A IoT-riasztások következő listáját a várt IoT-eszköz viselkedése alapján meghatározhatja. További információ az egyes riasztások testreszabásáról: [egyéni riasztások létrehozása](quickstart-create-custom-alerts.md).

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
