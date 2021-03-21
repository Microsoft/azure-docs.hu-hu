---
title: Az ügynök portfoliójának áttekintése és az operációs rendszer támogatása (előzetes verzió)
description: A IoT készült Azure Defender az eszközök típusától függően az ügynökök nagy portfólióját biztosítja.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: f731b034b5d4f795bae51107e9ff4e2e90788d7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120147"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Az ügynök portfoliójának áttekintése és az operációs rendszer támogatása (előzetes verzió)

A IoT készült Azure Defender az eszközök típusától függően az ügynökök nagy portfólióját biztosítja. 

## <a name="standalone-agent"></a>Önálló ügynök

Az önálló ügynök a Linux operációs rendszerek nagy részét fedi le, amelyek bináris csomagként vagy a belső vezérlőprogram részeként beépíthető forráskódként telepíthetők, és lehetővé teszik az ügyfelek igényei alapján történő módosítást és testreszabást. Példa az operációs rendszer támogatására: 

| Operációs rendszer | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20,04 | ✓ |  |

További részletekért, az operációs rendszer támogatásához vagy a forráskódhoz való hozzáférés kéréséhez, hogy az eszköz belső vezérlőprogramja részeként beilleszkedjen, lépjen kapcsolatba a fiók kezelőjével, vagy küldjön egy e-mailt a címre <defender_micro_agent@microsoft.com> . 

## <a name="azure-rtos-micro-agent"></a>Azure RTOS Micro-ügynök

A IoT Micro Agent Azure Defender átfogó és könnyű biztonsági megoldást kínál az Azure RTOS-t használó eszközökhöz. Az Azure Defender for IoT Micro Agent a gyakori fenyegetésekre, valamint a valós idejű operációs rendszer (RTOS) eszközeire vonatkozó lehetséges kártékony tevékenységek lefedettségét biztosítja. A Micro Agent az Azure RTOS NetX Duo összetevő részeként készült, és figyeli az eszköz hálózati tevékenységét. 

Az Azure Defender for IoT Micro Agent az Azure RTOS NetX Duo összetevő részeként készült, és figyeli az eszköz hálózati tevékenységét. A Micro Agent egy átfogó és könnyű biztonsági megoldásból áll, amely lefedettséget biztosít a gyakori fenyegetésekkel szemben, valamint a valós idejű operációs rendszerek (RTOS-eszközök) lehetséges kártékony tevékenységeit.

## <a name="next-steps"></a>Következő lépések

További információ az [önálló Micro Agent áttekintése (előzetes verzió)](concept-standalone-micro-agent-overview.md).