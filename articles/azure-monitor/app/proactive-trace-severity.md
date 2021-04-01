---
title: A nyomkövetés súlyossági arányának csökkenése – Azure Application Insights
description: Az alkalmazások nyomon követését az Azure Application Insights az intelligens észleléssel rendelkező nyomkövetési telemetria szokatlan mintázatait figyelheti.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 2b27860adfc1652b58fe9c51d4d0b0a6c271fc0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86539873"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>A nyomkövetés súlyossági arányának csökkenése (előzetes verzió)

A nyomkövetéseket széles körben használják az alkalmazásokban, mivel ezek segítenek megismerni a jelenetek mögötti történések történetét. Ha a dolgok helytelenek, a Nyomkövetések kritikus láthatóságot biztosítanak a nemkívánatos állapotot eredményező események sorrendjében. Míg a Nyomkövetések általában strukturálatlan, az egyik dolog, ami konkrétan megtudhatja tőlük a súlyossági szintet. Az alkalmazás állandó állapotában a "jó" nyomkövetési *adatok (információ* és *részletes* információk) és a "rossz" (*Figyelmeztetés*, *hiba* és *kritikus*) közötti arány marad a stabil működés érdekében. Ennek az az oka, hogy a "rossz" nyomon követések rendszeres időközönként, bizonyos számú ok miatt előfordulhatnak (átmeneti hálózati problémák esetén). De ha egy valós probléma egyre nő, az általában a "rossz", a "jó" nyomok relatív arányának növekedésével nyilvánul meg. Application Insights az intelligens észlelés automatikusan elemzi az alkalmazás által naplózott nyomokat, és figyelmezteti a szokatlan mintákat a nyomkövetési telemetria súlyosságára.

Ehhez a szolgáltatáshoz nem szükséges speciális beállítás, az alkalmazás nyomkövetési naplózásának konfigurálása nélkül (lásd: nyomkövetési naplók konfigurálása [.net](./asp-net-trace-logs.md) vagy [Java](./java-trace-logs.md)esetén). Akkor aktív, ha az alkalmazás elég kivételt hoz létre a telemetria.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapok ilyen típusú intelligens észlelési értesítést?
Ez a típusú értesítés akkor fordulhat elő, ha a "jó" nyomok (az információ vagy a *részletes* *adatokkal* naplózott Nyomkövetések) és a "rossz" nyomok közötti arány (a *Figyelmeztetési*, *hiba* vagy *végzetes* szintű Nyomkövetések) egy adott napon, az előző hét napban kiszámított alapértékkel összehasonlítva van.

## <a name="does-my-app-definitely-have-a-problem"></a>Az alkalmazásom biztosan probléma?
Nem, egy értesítés nem azt jelenti, hogy az alkalmazásnak feltétlenül van problémája. Bár a "jó" és a "rossz" nyomkövetési arány közötti csökkenés az alkalmazással kapcsolatos problémát jelezhet, a változás a arányban jóindulatú lehet. Például a növekedés oka az lehet, hogy az alkalmazás egy új folyamata több "rossz" nyomkövetést bocsát ki, mint a meglévő folyamatok esetében.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?
Az értesítések közé tartoznak a diagnosztikai információk, amelyek támogatják a diagnosztikai folyamatot:
1. **Osztályozás.** Az értesítésből megtudhatja, hogy hány műveletet érint. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör.** A probléma hatással van az összes forgalomra, vagy csak néhány műveletre? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztizálása.** A probléma további diagnosztizálásához a kapcsolódó elemek és jelentések hivatkozását használhatja a támogatási információkhoz.
