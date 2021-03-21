---
title: Riasztások használata az érzékelőn
description: Riasztások használata a hálózat biztonságának és működésének javítása érdekében.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523549"
---
# <a name="work-with-alerts-on-your-sensor"></a>Riasztások használata az érzékelőn

Riasztások használata a hálózat biztonságának és működésének javítása érdekében. A riasztások az alábbiakkal kapcsolatos információkat biztosítanak:

- A hitelesítő hálózati tevékenységtől való eltérések

- Protokollok és működési rendellenességek

- Gyanús kártevő-forgalom

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="A címek vizsgálatának észlelése.":::

A riasztások kezelési lehetőségei lehetővé teszik a felhasználóknak:

- Utasítsa az érzékelőket a jóváhagyott forgalomként észlelt tevékenységek megismerésére.

- Nyugtázza a riasztás áttekintését.

- Az érzékelőket az azonos eszközökkel és hasonló adatforgalommal észlelt események némítására utasíthatja.

A riasztások vizsgálatának fokozása és meggyorsítása érdekében további eszközök is elérhetők. Például:

  - Adja meg a riasztási felülvizsgálók utasítási megjegyzéseit.

  - Riasztási csoportok létrehozása a SOC-megoldásokban való megjelenítéshez. 

  - Adott riasztások keresése; a kapcsolódó PCAP-fájlok áttekintése; Tekintse meg az észlelt eszközöket és az egyéb csatlakoztatott eszközöket az eszköz térképén, vagy küldjön riasztási adatokat a partneri rendszereknek.

  - Riasztások továbbítása a partnerek szállítóinak: SIEM-rendszerek, MSSP rendszerek és sok más.

## <a name="alerts-and-engines"></a>Riasztások és motorok

A riasztások akkor aktiválódnak, ha az érzékelő motorok a hálózati forgalom és a beavatkozást igénylő viselkedés változásait figyelik. Ez a cikk az egyes motorok által kiváltott riasztások típusait ismerteti.

| Riasztástípus | Description |
|-|-|
| Szabályzat megsértésével kapcsolatos riasztások | Akkor aktiválódik, ha a házirend-megsértési motor észleli a korábban megszerzett forgalomtól való eltérést. Például: <br /> – A rendszer új eszközt észlelt.  <br /> – Új konfiguráció észlelhető az eszközön. <br /> – A programozási eszközként nem definiált eszközök programozási változást végeznek. <br /> – A belső vezérlőprogram verziója megváltozott. |
| Protokollok megsértésével kapcsolatos riasztások | Akkor aktiválódik, ha a protokoll-megsértési motor olyan csomagok szerkezetét vagy mezőértékeket észlel, amelyek nem felelnek meg a protokoll specifikációjának. | 
| Működési riasztások | Akkor aktiválódik, ha az operatív motor észleli a hálózati működési incidenseket, vagy ha az eszköz meghibásodik. Például egy hálózati eszköz le lett állítva egy stop PLC-parancson keresztül, vagy egy érzékelő felülete leállította a forgalom figyelését. |
| Kártevő-riasztások | Akkor aktiválódik, ha a kártevő rendszer kártékony hálózati tevékenységet észlel. A motor például észlel egy ismert támadást, például a Conficker-et. |
| Anomáliák – riasztások | Akkor aktiválódik, ha a rendellenességet kezelő motor eltérést észlel. Egy eszköz például hálózati ellenőrzéseket végez, de nincs meghatározva ellenőrzési eszközként. |

Eszközök állnak rendelkezésre az érzékelős motorok engedélyezéséhez és letiltásához. A riasztásokat a rendszer nem aktiválja a letiltott motoroktól. Lásd: [a figyelt forgalom szabályozása](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Riasztások és érzékelő-jelentéskészítés

A riasztásokban tükröződő tevékenység kiszámítható az adatbányászat, a kockázatértékelés és a támadási vektoros jelentések létrehozásakor. Az események kezelésekor az érzékelő ennek megfelelően frissíti a jelentéseket.

Például:

  - Egy meghatározott alhálózatban lévő eszköz és az alhálózaton kívül található eszközök között nem engedélyezett kapcsolat jelenik meg az adatbányászati *internetes tevékenység* jelentésben és a kockázatértékelési *internetes kapcsolatok* szakaszban. Az eszközök engedélyezését követően a rendszer ezeket a jelentéseket a jelentések létrehozásakor számítja ki.

  - A hálózati eszközökön észlelt kártevő-eseményeket a kockázatértékelési jelentések jelentik. Ha a kártevő-eseményekre vonatkozó riasztások el vannak *némítva*, az érintett eszközök nem lesznek kiszámítva a kockázatértékelési jelentésben.

## <a name="see-also"></a>Lásd még

- [Tanulási és intelligens informatikai képzési módok](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [A riasztások által biztosított információk megtekintése](how-to-view-information-provided-in-alerts.md)
- [A riasztási esemény kezelése](how-to-manage-the-alert-event.md)
- [Riasztási munkafolyamatok felgyorsítása](how-to-accelerate-alert-incident-response.md)
