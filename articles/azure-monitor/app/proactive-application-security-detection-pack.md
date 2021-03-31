---
title: Biztonsági észlelési csomag az Azure Application Insights
description: Az alkalmazások monitorozása az Azure Application Insights és a potenciális biztonsági problémák intelligens észlelése révén.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 1ed304e903bb50591e61e294b6701f8268f9d8e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87081627"
---
# <a name="application-security-detection-pack-preview"></a>Alkalmazás biztonsági észlelési csomagja (előzetes verzió)

Application Insights automatikusan elemzi az alkalmazás által generált telemetria, és észleli a lehetséges biztonsági problémákat. Ez a funkció lehetővé teszi az esetleges biztonsági problémák azonosítását, valamint az alkalmazás kijavításával vagy a szükséges biztonsági intézkedések meghozatalával történő kezelését.

Ehhez a szolgáltatáshoz nem szükséges speciális beállítás, [az alkalmazás nem konfigurálható telemetria küldéséhez](./usage-overview.md).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapok ilyen típusú intelligens észlelési értesítést?
A rendszer háromféle biztonsági problémát észlelt:
1. Nem biztonságos URL-hozzáférés: az alkalmazásban lévő URL-cím a HTTP és a HTTPS protokollon keresztül érhető el. A HTTPS-kérelmeket fogadó URL-cím általában nem fogadhat el HTTP-kérelmeket. Ez az alkalmazásban felmerülő hibákra vagy biztonsági problémákra utalhat.
2. Nem biztonságos űrlap: az alkalmazásban lévő űrlap (vagy más "POST" kérelem) HTTPS helyett HTTP-t használ. A HTTP használata veszélyeztetheti az űrlap által elküldhető felhasználói adattípusokat.
3. Gyanús felhasználói tevékenység: az alkalmazás több országból vagy régióból érhető el, és ugyanaz a felhasználó nagyjából egy időben. Például ugyanaz a felhasználó az alkalmazást Spanyolországból és a Egyesült Államok egy órán belül elérte. Ez az észlelés egy potenciálisan rosszindulatú hozzáférési kísérletet jelez az alkalmazáshoz.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Az alkalmazásom határozottan biztonsági probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazásnak határozottan biztonsági probléma van. A fenti forgatókönyvek bármelyikének észlelése számos esetben biztonsági problémát jelezhet. Az észlelés azonban természetes üzleti indoklással rendelkezhet, és figyelmen kívül hagyható.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hogyan javítsa ki a "nem biztonságos URL-hozzáférés" észlelését?
1. **Osztályozás.** Az értesítés megadja a nem biztonságos URL-címekhez hozzáférő felhasználók számát, valamint a nem biztonságos hozzáférés által a leginkább érintett URL-címet. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör.** A felhasználók hány százaléka fér hozzá a nem biztonságos URL-címekhez? Hány URL-cím érintett? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztizálása.** Az észlelés biztosítja a nem biztonságos kérelmek listáját, valamint az érintett URL-címek és felhasználók listáját, hogy segítsen a probléma további diagnosztizálásában.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hogyan javítsa ki a "nem biztonságos űrlap" észlelést?
1. **Osztályozás.** Az értesítés megadja a nem biztonságos űrlapok számát és azon felhasználók számát, akiknek az adatsérülése veszélyben volt. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör.** Melyik űrlap vett részt a legtöbb nem biztonságos átvitelben, és milyen a nem biztonságos átvitelek eloszlása az idő múlásával? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztizálása.** Az észlelés biztosítja a nem biztonságos űrlapok listáját, valamint az egyes űrlapok nem biztonságos átvitelének lebontását, hogy segítsen a probléma további diagnosztizálásában.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hogyan javítsa ki a "gyanús felhasználói tevékenység" észlelését?
1. **Osztályozás.** Az értesítés a gyanús viselkedést kiállító különböző felhasználók számát adja meg. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör.** Mely országokból/régiókból származnak a gyanús kérelmek? Melyik felhasználó volt a leggyanús? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztizálása.** Az észlelés megjeleníti a gyanús felhasználók listáját, valamint az egyes felhasználók országait/régióit, hogy segítsen a probléma további diagnosztizálásában.
