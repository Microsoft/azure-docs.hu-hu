---
title: Beépített & egyéni riasztások listája
description: Ismerje meg a biztonsági riasztásokat és az ajánlott szervizelést a Defender használatával IoT Hub funkcióit és szolgáltatásait.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784560"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Defender IoT Hub biztonsági riasztások

A IoT Defender folyamatosan elemzi a IoT-megoldást a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztessen a rosszindulatú tevékenységekre.
Emellett egyéni riasztásokat is létrehozhat a várt eszköz működésének ismerete alapján.
A riasztás a lehetséges kompromisszumok indikátorként működik, és meg kell vizsgálni és szervizelni kell.

Ebben a cikkben megtalálja a beépített riasztások listáját, amelyek a IoT Hub indíthatók el.
A beépített riasztások mellett a Defender for IoT lehetővé teszi egyéni riasztások definiálását a várt IoT Hub és/vagy az eszköz viselkedése alapján.
További információ: [testreszabható riasztások](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Beépített riasztások a IoT Hubhoz

| Súlyosság | Név | Leírás | Javasolt szervizelés |
|--|--|--|--|
| **Közepes** súlyosság |  |  |  |
| Új tanúsítvány lett hozzáadva egy IoT Hubhoz | Közepes | A \' (z)% {DescCertificateName} nevű tanúsítvány \' hozzá lett adva a (z) \' % {DescIoTHubName} IoT hub \' . Ha ezt a műveletet egy jogosulatlan fél hozta létre, akkor rosszindulatú tevékenységet jelezhet. | 1. Győződjön meg arról, hogy a tanúsítványt egy, a hitelesítő fél adta hozzá. <br> 2. Ha nem adtak hozzá egy nem megfelelő fél, távolítsa el a tanúsítványt, és a riasztást a szervezeti biztonsági csapattal bővítse. |
| A tanúsítvány törölve lett egy IoT Hubból | Közepes | A \' (z)% {DescCertificateName} nevű tanúsítvány \' törölve lett a (z) \' % {DescIoTHubName} IoT hub \' . Ha ezt a műveletet egy jogosulatlan fél hozta létre, akkor rosszindulatú tevékenységet jelezhet. | 1. Győződjön meg arról, hogy a tanúsítványt egy, a hitelesítő fél eltávolította. <br> 2. Ha a tanúsítványt nem a hitelesítő fél törölte, adja hozzá újra a tanúsítványt, és a riasztást a szervezeti biztonsági csapathoz. |
| Sikertelen kísérlet történt tanúsítvány hozzáadására egy IoT Hub | Közepes | Sikertelen kísérlet történt a \' (z)% {DescCertificateName} tanúsítvány hozzáadására \' a \' (z)% {DescIoTHubName} IoT hub \' . Ha ezt a műveletet egy jogosulatlan fél hozta létre, akkor rosszindulatú tevékenységet jelezhet. | Győződjön meg arról, hogy csak a jogosult felek kapnak engedélyeket a tanúsítványok módosításához. |
| Sikertelen kísérlet történt a tanúsítvány törlésére egy IoT Hub | Közepes | Sikertelen kísérlet történt a (z)% {DescCertificateName} tanúsítvány törlésére a (z)% \' \' \' {DescIoTHubName} IoT hub \' . Ha ezt a műveletet egy jogosulatlan fél hozta létre, akkor rosszindulatú tevékenységet jelezhet. | Győződjön meg arról, hogy a tanúsítványok módosítására vonatkozó engedélyek csak egy jogosult fél számára vannak megadva. |
| az x. 509 eszköz tanúsítványának ujjlenyomata nem egyezik | Közepes | az x. 509 eszköz tanúsítványának ujjlenyomata nem felelt meg a konfigurációnak. | Tekintse át a riasztásokat az eszközökön. Nincs szükség további műveletre. |
| az x. 509 tanúsítvány lejárt | Közepes | Az X. 509 eszköz tanúsítványa lejárt. | Ez lehet egy lejárt tanúsítvánnyal rendelkező megbízható eszköz vagy egy megbízható eszköz megszemélyesítésére tett kísérlet. Ha a megbízható eszköz jelenleg megfelelően kommunikál, ez valószínűleg megszemélyesítési kísérlet. |
| **Alacsony** súlyosság |  |  |  |
| Kísérlet történt az észlelt IoT Hub diagnosztikai beállításainak hozzáadására vagy szerkesztésére | Alacsony | A rendszer a IoT Hub diagnosztikai beállításainak hozzáadására vagy szerkesztésére tett kísérletet észlelt. A diagnosztikai beállítások lehetővé teszik a tevékenységi nyomvonalak újbóli létrehozását vizsgálat céljára, amikor biztonsági incidens következik be, vagy a hálózat biztonsága sérül. Ha ezt a műveletet nem egy engedélyező fél hozta létre, akkor kártékony tevékenységet jelezhet. | 1. Győződjön meg arról, hogy a tanúsítványt egy, a hitelesítő fél eltávolította.<br> 2. Ha a tanúsítványt nem a hitelesítő fél törölte, adja hozzá újra a tanúsítványt, és a riasztást a tájékoztatási biztonsági csapatnak. |
| Kísérlet történt diagnosztikai beállítás törlésére egy észlelt IoT Hub | Alacsony | A (z)% {DescAttemptStatusMessage}% { \' DescDiagnosticSettingName} diagnosztikai beállítást próbált felvenni vagy szerkeszteni a (z) \' \' \' % {DescIoTHubName} IoT hub \' . A diagnosztikai beállítás lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. Ha ezt a műveletet nem egy engedélyező fél hajtotta végre, akkor előfordulhat, hogy rosszindulatú tevékenységet jelez. | Győződjön meg arról, hogy a diagnosztikai beállítások módosítására vonatkozó engedélyek csak egy jogosult fél számára vannak megadva. |
| Lejárt SAS-jogkivonat | Alacsony | Az eszköz által használt lejárt SAS-jogkivonat | A jogszerű eszköz lehet lejárt jogkivonattal, vagy egy megbízható eszköz megszemélyesítésére tett kísérlet. Ha a megbízható eszköz jelenleg megfelelően kommunikál, ez valószínűleg megszemélyesítési kísérlet. |
| Érvénytelen SAS-jogkivonat aláírása | Alacsony | Az eszköz által használt SAS-token aláírása érvénytelen. Az aláírás nem felel meg az elsődleges vagy a másodlagos kulcsnak sem. | Tekintse át a riasztásokat az eszközökön. Nincs szükség további műveletre. |

## <a name="next-steps"></a>Következő lépések

- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
