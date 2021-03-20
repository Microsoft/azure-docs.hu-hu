---
title: Rendellenes növekedés a kivételek mennyiségében – Azure Application Insights
description: Alkalmazás-kivételek figyelése az Azure Application Insights az intelligens észleléssel a kivételt okozó kötet szokatlan mintázata esetén.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499426"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Rendellenes növekedés a kivétel mennyisége (előzetes verzió)

Application Insights automatikusan elemzi az alkalmazásban eldobott kivételeket, és figyelmezteti a kivételek telemetria szokatlan mintázatokra.

Ehhez a szolgáltatáshoz nem szükséges speciális beállítás, kivéve az alkalmazásra vonatkozó [kivétel-jelentéskészítés konfigurálását](./asp-net-exceptions.md#set-up-exception-reporting) . Akkor aktív, ha az alkalmazás elég kivételt hoz létre a telemetria.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapok ilyen típusú intelligens észlelési értesítést?
Ez a típusú értesítés akkor fordulhat elő, ha az alkalmazás egy nap során rendellenes növekedést mutat egy adott típusú kivételek számának az elmúlt hét napban számított alaptervhez képest.
A rendszer gépi tanulási algoritmusokat használ a kivételek számának növekedésének észlelésére, miközben figyelembe veszi az alkalmazás használatának természetes növekedését.

## <a name="does-my-app-definitely-have-a-problem"></a>Az alkalmazásom biztosan probléma?
Nem, egy értesítés nem azt jelenti, hogy az alkalmazásnak feltétlenül van problémája. Bár a kivételek túlzott száma általában egy alkalmazás hibáját jelzi, a kivételek jóindulatú lehetnek, és az alkalmazás megfelelően kezeli őket.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?
Az értesítések közé tartoznak a diagnosztikai információk, amelyek támogatják a diagnosztikai folyamatot:
1. **Osztályozás.** Az értesítés azt mutatja, hogy hány felhasználó vagy hány kérelem van hatással. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör.** A probléma hatással van az összes forgalomra, vagy csak néhány műveletre? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztizálása.** Az észlelés a kivétel eldobásának módszerével, valamint a kivétel típusával kapcsolatos információkat tartalmaz. A probléma további diagnosztizálásához a kapcsolódó elemeket és jelentéseket is használhatja a támogatási információkhoz.
