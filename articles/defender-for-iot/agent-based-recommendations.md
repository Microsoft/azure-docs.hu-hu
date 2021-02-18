---
title: Ügynök-alapú javaslatok
titleSuffix: Azure Defender for IoT
description: Ismerje meg a biztonsági javaslatok fogalmát, valamint azt, hogyan használják a Defender for IoT-eszközöket.
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
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642533"
---
# <a name="security-recommendations-for-iot-devices"></a>Biztonsági javaslatok a IoT-eszközökhöz

A Defender for IoT megvizsgálja az Azure-erőforrásokat és IoT eszközöket, és biztonsági javaslatokat tesz a támadási felület csökkentése érdekében.
A biztonsági javaslatok működésre készek, és célja, hogy az ügyfelek számára segítséget nyújtsanak az ajánlott biztonsági eljárásoknak való megfelelésben.

Ebben a cikkben megtalálja a javaslatok listáját, amelyek a IoT-eszközökön indíthatók el.

## <a name="agent-based-recommendations"></a>Ügynök-alapú javaslatok

Az eszközökre vonatkozó javaslatok betekintést és javaslatokat nyújtanak az eszközök biztonsági helyzetének javítására.

| Súlyosság | Name | Adatforrás | Description |
|--|--|--|--|
| Közepes | Portok megnyitása az eszközön | Klasszikus biztonsági modul | Figyelő végpont található az eszközön. |
| Közepes | Az egyik láncban megtalálhatók a megengedhető tűzfal-szabályzatok. | Klasszikus biztonsági modul | Engedélyezett tűzfal-házirend található (bemenet/kimenet). A tűzfal házirendjének alapértelmezés szerint el kell utasítania az összes forgalmat, és meg kell határoznia azokat a szabályokat, amelyek lehetővé teszik a szükséges kommunikációt az eszközön |
| Közepes | A bemeneti láncban található engedékeny tűzfalszabályok találhatók | Klasszikus biztonsági modul | A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz. |
| Közepes | A rendszer a kimeneti láncban található engedékeny tűzfalszabály | Klasszikus biztonsági modul | A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz. |
| Közepes | Az operációs rendszerek alapkonfigurációjának ellenőrzése nem sikerült | Klasszikus biztonsági modul | Az eszköz nem felel meg a [CIS Linux-referenciaértékeknek](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Ügynök-alapú operatív javaslatok

A működési javaslatok betekintést és javaslatokat nyújtanak a biztonsági ügynökök konfigurációjának javítására.

| Súlyosság | Name | Adatforrás | Description |
|--|--|--|--|
| Alacsony | Az ügynök nem használt üzeneteket küld | Klasszikus biztonsági modul | az elmúlt 24 órában 10% vagy több biztonsági üzenet kisebb volt, mint 4 KB. |
| Alacsony | A biztonsági Twin konfiguráció nem optimális | Klasszikus biztonsági modul | A biztonsági Twin konfiguráció nem optimális. |
| Alacsony | Biztonsági kettős konfiguráció ütközése | Klasszikus biztonsági modul | Ütközések voltak azonosítva a biztonsági Twin konfigurációban. |  |

## <a name="next-steps"></a>Következő lépések

- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
