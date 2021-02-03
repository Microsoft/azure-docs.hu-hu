---
title: Trendek és statisztikai jelentések készítése
description: Ismerje meg a hálózati tevékenységet, a statisztikát és a trendeket a Defender IoT trendek és statisztika widgetek használatával.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 20b47204ea8f64bc430f436a9bcf1b2311409aa6
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509486"
---
# <a name="sensor-trends-and-statistics-reports"></a>Érzékelő trendek és statisztikai jelentések

## <a name="about-sensor-trends-and-statistics-reports"></a>Az érzékelő trendjeiről és a statisztikai jelentésekről

Létrehozhat widget-diagramokat és kördiagramokat, és betekintést nyerhet a hálózat trendjeibe és statisztikába. A widgetek a felhasználó által definiált irányítópultok alatt csoportosíthatók.

> [!NOTE]
> A rendszergazdák és a biztonsági elemzők trendeket és statisztikai jelentéseket hozhatnak létre.

Az irányítópult olyan widgeteket tartalmaz, amelyek grafikusan leírják a következő típusú információkat:

- Forgalom port alapján
- Legfelső szintű forgalom port alapján
- Csatorna sávszélessége
- Teljes sávszélesség
- Aktív TCP-kapcsolatok
- Legfelső szintű sávszélesség VLAN-nal
- Eszközök
  - Új eszközök
  - Foglalt eszközök
  - Eszközök szállító szerint
  - Eszközök operációs rendszer szerint
  - Eszközök száma VLAN-onként
  - Leválasztott eszközök
- A kapcsolat órákig csökken
- Incidensek típusú riasztások típus szerint
- Adatbázistábla-hozzáférés
- Protokollok metszetének widgetje
- DELTAV
  - DeltaV Roc-műveletek eloszlása
  - DeltaV Roc-események név szerint
  - DeltaV események idő szerint
- AMS
  - AMS-forgalom kiszolgáló portja szerint
  - AMS-forgalom parancs alapján
- Ethernet és IP-cím:
  - Ethernet-és IP-címek forgalma a CIP szolgáltatással
  - Ethernet és IP-címek forgalma a CIP-osztály szerint
  - Az Ethernet és az IP-cím forgalmának parancs alapján
- OPC
  - OPC felső szintű felügyeleti műveletek
  - OPC felső I/O-műveletek
- Siemens S7:
  - S7-es forgalom vezérlési funkcióval
  - S7-es forgalom alfüggvény alapján
- VLAN
  - Eszközök száma VLAN-onként
  - Legfelső szintű sávszélesség VLAN-nal
- 60870-5-104
  - IEC-60870 forgalom ASDU szerint
- BACNET
  - BACnet szolgáltatások
- DNP3
  - DNP3-forgalom függvény szerint
- SRTP
  - SRTP-forgalom
  - SRTP hibák naponta
- SuiteLink:
  - SuiteLink Top lekérdezett Címkék
  - SuiteLink numerikus címke viselkedése
- IEC-60870 forgalom ASDU szerint
- DNP3-forgalom függvény szerint
- MMS-forgalom szolgáltatás szerint
- Modbus-forgalom függvény szerint
- OPC – UA forgalom szolgáltatás szerint

> [!NOTE]
>  A widgetek ideje az érzékelő ideje szerint van beállítva.

## <a name="create-reports"></a>Jelentések létrehozása

Az irányítópultok és a widgetek megtekintéséhez:

Az oldalsó menüben válassza a **trendek & statisztikát** .

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Képernyőkép egy vizsgálatról.":::

Alapértelmezés szerint az eredmények az elmúlt 7 napban észlelhetők. A szűrési eszközök használatával módosíthatja ezt a tartományt. Például egy ingyenes szöveges keresés.

## <a name="see-also"></a>Lásd még

[Kockázatértékelési jelentéskészítés](how-to-create-risk-assessment-reports.md) 
 [Érzékelő adatbányászati lekérdezések](how-to-create-data-mining-queries.md) 
 [Támadási vektorok jelentése](how-to-create-attack-vector-reports.md)