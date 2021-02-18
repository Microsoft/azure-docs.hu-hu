---
title: A IoT Hub biztonsági javaslatai
description: Ismerje meg a biztonsági javaslatok fogalmát, valamint azt, hogyan használják őket a Defender for IoT Hubban.
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
ms.openlocfilehash: 4ae479fcff0514b42d46aef455d8a32d6ebfaf23
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636543"
---
# <a name="security-recommendations-for-iot-hub"></a>A IoT Hub biztonsági javaslatai

A Defender for IoT megvizsgálja az Azure-erőforrásokat és IoT eszközöket, és biztonsági javaslatokat tesz a támadási felület csökkentése érdekében.
A biztonsági javaslatok működésre készek, és célja, hogy az ügyfelek számára segítséget nyújtsanak az ajánlott biztonsági eljárásoknak való megfelelésben.

Ebben a cikkben a javaslatok listáját találja, amely a IoT Hub indítható el.

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
