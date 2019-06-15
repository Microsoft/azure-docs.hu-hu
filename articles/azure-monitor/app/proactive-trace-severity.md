---
title: Intelligens detektálás – teljesítménycsökkenés a nyomkövetési súlyossági arány, az Azure Application Insights |} A Microsoft Docs
description: Figyelheti az alkalmazások nyomkövetéseit az Azure Application Insights – híváslánc-telemetria a szokatlan mintákat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 10b909fd5239546047aa4696a1f6a68a703778c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306394"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Csökkenés a következőben nyomkövetési súlyossági arány (előzetes verzió)

Nyomkövetési széles körben használt alkalmazások esetén, segít a történet, mi történik a háttérben. Problémák esetén, a nyomkövetéseket idézte események sorozatát kulcsfontosságú betekintést biztosítanak. Általában strukturálatlan nyomkövetési, amelyek van egy dolog, amely konkrétan is adatnézetből származó őket – az a súlyossági szintet. Egy alkalmazás az egyenletes állapotban várhatóan lenne a "good" nyomkövetési aránya (*Info* és *részletes*) és a "rossz" nyomkövetési (*figyelmeztetés*, *hiba*, és *kritikus*) az állandó marad. Feltételezzük, hogy "rossz" nyomkövetési fordulhat elő, számtalan miatt bizonyos mértékben rendszeres időközönként (például átmeneti hálózati hibák esetén). De amikor problémát megkezdődik az egyre növekvő, azt általában jegyzékfájlok növekedése a "rossz" nyomkövetési vs "good" nyomkövetési relatív arányát. Application Insights intelligens észlelés automatikusan elemzi a nyomkövetéseket az alkalmazás által naplózott, és képes figyelmeztessen a szokatlan minták súlyossági szintje a híváslánc-telemetriát.

Ez a funkció nem speciális beállítás, az alkalmazás nyomkövetési adatainak naplózása konfigurálása nem szükséges (lásd: how to egy nyomkövetési napló figyelő konfigurálása [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) vagy [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Ez akkor aktív, ha az alkalmazása elég kivételtelemetria hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha szeretné be intelligens detektálás értesítést az ilyen típusú?
Ez az értesítés típusát fordulhatnak elő, ha a "good" nyomkövetési aránya (szintű naplózott nyomkövetési *Info* vagy *részletes*) és a "rossz" nyomkövetési (szintű naplózott nyomkövetési *Figyelmeztetés*, *Hiba*, vagy *végzetes*) egy adott nap, az előző hét nap vetítve alapkonfigurációhoz képest van gyorsítótárazhatók.

## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképp rendelkezik a probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás mindenképp van probléma. Egy "jó" és "rossz" nyomkövetési aránya csökkenése alkalmazás esetleg utalhatnak, bár előfordulhat, hogy ez a változtatás arány jóindulatú. Például a növekedés lehet egy új folyamatot, az alkalmazás úgy, mint a meglévő folyamatok további "rossz" nyomkövetési miatt).

## <a name="how-do-i-fix-it"></a>Hogyan javíthatom?
Az értesítések közé tartoznak a diagnosztikai adatokat a diagnosztika során támogatja:
1. **Osztályozási.** Az értesítés megtudhatja, hány műveletek érintettek. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** A probléma érintő minden forgalmat, vagy csak néhány művelet? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Használhatja a kapcsolódó elemek és jelentések összekapcsolása támogatási információk, további segítséget a probléma diagnosztizálásához.


