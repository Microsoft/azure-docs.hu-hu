---
title: Biztonsági javaslatok
description: Ismerje meg a biztonsági javaslatok fogalmát, valamint azt, hogyan használják őket a Defender a IoT.
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
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: d11154e26e2ffe97a1ea102a11dddb746db9433a
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809269"
---
# <a name="security-recommendations"></a>Biztonsági javaslatok

A Defender for IoT megvizsgálja az Azure-erőforrásokat és IoT eszközöket, és biztonsági javaslatokat tesz a támadási felület csökkentése érdekében.
A biztonsági javaslatok működésre készek, és célja, hogy az ügyfelek számára segítséget nyújtsanak az ajánlott biztonsági eljárásoknak való megfelelésben.

Ebben a cikkben a javaslatok listáját találhatja, amelyek a IoT Hub és/vagy IoT eszközökön indíthatók el.

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


## <a name="built-in-recommendations-in-iot-hub"></a>Beépített javaslatok a IoT Hub

Az ajánlási riasztások betekintést és javaslatokat nyújtanak a környezet biztonsági helyzetének javítására szolgáló műveletekhez.

| Súlyosság | Name | Adatforrás | Description |
|--|--|--|--|
| Magas | Több eszköz által használt azonos hitelesítési hitelesítő adatok | IoT Hub | IoT Hub hitelesítési hitelesítő adatokat több eszköz is használja. Ez a folyamat egy jogszerű eszközt megszemélyesítő törvénytelen eszközt jelezhet. Az ismétlődő hitelesítő adatok használata növeli az eszköz megszemélyesítésének kockázatát egy rosszindulatú színész által. |
| Közepes | Az alapértelmezett IP-szűrési házirendet meg kell tagadni | IoT Hub | Az IP-szűrési konfigurációnak meg kell határoznia az engedélyezett forgalomra vonatkozó szabályokat, és alapértelmezés szerint meg kell tagadnia az összes többi forgalmat. |
| Közepes | Az IP-szűrési szabály nagyméretű IP-címtartományt tartalmaz | IoT Hub | Az IP-szűrési szabály forrás IP-tartományának engedélyezése túl nagy. A túlzottan megengedhető szabályok az IoT-hubot kártékony szereplőkkel tehetik ki. |
| Alacsony | Diagnosztikai naplók engedélyezése IoT Hub | IoT Hub | Engedélyezheti a naplókat, és akár egy évig is megtarthatja őket. A naplók megőrzése lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidensek bekövetkezésekor vagy a hálózat biztonsága szempontjából. |


## <a name="next-steps"></a>Következő lépések

- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
