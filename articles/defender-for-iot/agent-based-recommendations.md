---
title: Ügynök-alapú javaslatok
description: Ismerje meg a biztonsági javaslatok fogalmát, valamint azt, hogyan használják a Defender for IoT-eszközöket.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a29957f459edad8b768daea7bc4567d77c80b165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784985"
---
# <a name="security-recommendations-for-iot-devices"></a>Biztonsági javaslatok a IoT-eszközökhöz

A Defender for IoT megvizsgálja az Azure-erőforrásokat és IoT eszközöket, és biztonsági javaslatokat tesz a támadási felület csökkentése érdekében.
A biztonsági javaslatok működésre készek, és célja, hogy az ügyfelek számára segítséget nyújtsanak az ajánlott biztonsági eljárásoknak való megfelelésben.

Ebben a cikkben megtalálja a javaslatok listáját, amelyek a IoT-eszközökön indíthatók el.

## <a name="agent-based-recommendations"></a>Ügynök-alapú javaslatok

Az eszközökre vonatkozó javaslatok betekintést és javaslatokat nyújtanak az eszközök biztonsági helyzetének javítására.

| Súlyosság | Name | Adatforrás | Leírás |
|--|--|--|--|
| Közepes | Portok megnyitása az eszközön | Klasszikus Defender – IoT-Micro-Agent| Figyelő végpont található az eszközön. |
| Közepes | Az egyik láncban megtalálhatók a megengedhető tűzfal-szabályzatok. | Klasszikus Defender – IoT-Micro-Agent| Engedélyezett tűzfal-házirend található (bemenet/kimenet). A tűzfal házirendjének alapértelmezés szerint el kell utasítania az összes forgalmat, és meg kell határoznia azokat a szabályokat, amelyek lehetővé teszik a szükséges kommunikációt az eszközön |
| Közepes | A bemeneti láncban található engedékeny tűzfalszabályok találhatók | Klasszikus Defender – IoT-Micro-Agent| A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz. |
| Közepes | A rendszer a kimeneti láncban található engedékeny tűzfalszabály | Klasszikus Defender – IoT-Micro-Agent| A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz. |
| Közepes | Az operációs rendszerek alapkonfigurációjának ellenőrzése nem sikerült | Klasszikus Defender – IoT-Micro-Agent| Az eszköz nem felel meg a [CIS Linux-referenciaértékeknek](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Ügynök-alapú operatív javaslatok

A működési javaslatok betekintést és javaslatokat nyújtanak a biztonsági ügynökök konfigurációjának javítására.

| Súlyosság | Name | Adatforrás | Leírás |
|--|--|--|--|
| Alacsony | Az ügynök nem használt üzeneteket küld | Klasszikus Defender – IoT-Micro-Agent | az elmúlt 24 órában 10% vagy több biztonsági üzenet kisebb volt, mint 4 KB. |
| Alacsony | A biztonsági Twin konfiguráció nem optimális | Klasszikus Defender – IoT-Micro-Agent | A biztonsági Twin konfiguráció nem optimális. |
| Alacsony | Biztonsági kettős konfiguráció ütközése | Klasszikus Defender – IoT-Micro-Agent | Ütközések voltak azonosítva a biztonsági Twin konfigurációban. |

## <a name="next-steps"></a>Következő lépések

- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
