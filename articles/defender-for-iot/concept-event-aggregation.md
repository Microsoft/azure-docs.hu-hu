---
title: Esemény összesítése (előzetes verzió)
description: A Defender for IoT biztonsági ügynökök adatokat és rendszereseményeket gyűjtenek a helyi eszközről, és az adatokat az Azure-felhőbe küldik feldolgozásra és elemzésre.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779324"
---
# <a name="event-aggregation-preview"></a>Esemény összesítése (előzetes verzió)

A Defender for IoT biztonsági ügynökök adatokat és rendszereseményeket gyűjtenek a helyi eszközről, és az adatokat az Azure-felhőbe küldik feldolgozásra és elemzésre. A IoT Micro Agent Defender számos típusú eszköz-eseményt gyűjt, beleértve az új folyamatokat és az összes új kapcsolatfelvételi eseményt. Az új folyamat és az új kapcsolatfelvételi események is gyakran előfordulnak egy adott eszközön egy másodpercen belül. Ez a képesség fontos az átfogó biztonság szempontjából, azonban az üzenetek biztonsági ügynökei által küldött üzenetek száma gyorsan teljesíthető, vagy túllépheti a IoT Hub kvótát és a költségeket. Ezek az események azonban rendkívül értékes biztonsági információkat tartalmaznak, amelyek elengedhetetlenek az eszköz védelméhez. 

A további kvóta csökkentése és a költségek az eszközök védelmének megőrzése mellett a Defender for IoT-ügynökök összesítik az alábbi típusú eseményeket: 

- ProcessCreate (csak Linux) 

- ConnectionCreate (csak Azure RTOS) 

## <a name="how-does-event-aggregation-work"></a>Hogyan működik az események összesítése? 

A Defender for IoT-ügynökök az intervallum időtartamára vagy az időablakra vonatkozó eseményeket összesítik. Az intervallum lejárta után az ügynök további elemzés céljából elküldi az összesített eseményeket az Azure-felhőbe. Az összesített eseményeket a rendszer a memóriában tárolja, amíg el nem érkezik az Azure-felhőbe. 

Ha az ügynök azonos eseményt gyűjt az egyikhez, amely már a memóriában van, az ügynök növeli az adott esemény találati számát, hogy csökkentse az ügynök memóriájának helyigényét. Az összesítési idő ablakának átadásakor az ügynök elküldi az összes bekövetkezett esemény találati számát. Az események összesítése egyszerűen az egyes összegyűjtött események találati számának összesítése. 

## <a name="process-events"></a>Események feldolgozása 

A folyamat eseményei jelenleg csak Linux operációs rendszereken támogatottak. 

A folyamat eseményei azonosnak tekintendők, ha a *parancssor* és a  *userid*   azonos. 

A folyamat eseményeinek alapértelmezett puffere 32 folyamat, amely után a puffer ciklusba kerül, és a legrégebbi folyamat eseményei el lesznek vetve, hogy helyet szabadítson fel az új folyamat eseményeihez.  

## <a name="network-connection-events"></a>Hálózati kapcsolatok eseményei 

A hálózati kapcsolatok eseményei jelenleg csak az Azure RTOS támogatottak. 

A hálózati kapcsolat eseményei azonosnak számítanak, ha a *helyi port*, a  *távoli port*, a  *átviteli protokoll*, a  *helyi címek* és a  *távoli címek* azonosak. 

A hálózati kapcsolatok eseményeinek alapértelmezett puffere a 64. A rendszer a következő gyűjtési ciklusig nem gyorsítótárazza az új hálózati eseményeket. A gyorsítótár méretének növelésére vonatkozó figyelmeztetést a rendszer naplózza.

## <a name="next-steps"></a>Következő lépések

Győződjön meg róla [, hogy a Defender IoT biztonsági riasztásokat](concept-security-alerts.md).
