---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763394"
---
A Microsoft Authentication Library (MSAL) alkalmazásai olyan naplófájlokat hoznak elő, amelyek segíthetnek a problémák diagnosztizálásában. Egy alkalmazás több sornyi kóddal is konfigurálhatja a naplózást, és egyéni vezérléssel láthatja el a részletességi szintet, valamint azt, hogy a rendszer naplózza-e a személyes és a szervezeti adatokat. Javasoljuk, hogy hozzon létre egy MSAL-naplózási visszahívást, és adja meg a felhasználók számára a naplók küldését a hitelesítési problémák esetén.

## <a name="logging-levels"></a>Naplózási szintek

A MSAL számos naplózási részletességi szintet biztosít:

- Hiba: azt jelzi, hogy valami hiba történt, és a rendszer hibát generált. Hibakeresésre és a problémák azonosítására szolgál.
- Figyelmeztetés: nem szükségszerűen hiba vagy hiba történt, de a diagnosztika és a problémák megoldására szolgál.
- Információ: a MSAL a tájékoztató célokra szánt eseményeket naplózza, nem feltétlenül a hibakereséshez.
- Részletes: alapértelmezett. A MSAL a könyvtár működésének részletes adatait naplózza.

## <a name="personal-and-organizational-data"></a>Személyes és szervezeti adatkezelés

Alapértelmezés szerint a MSAL-naplózó nem gyűjt kényes személyes vagy szervezeti adatokat. A könyvtár lehetővé teszi a személyes és szervezeti adatnaplózás engedélyezését, ha úgy dönt, hogy erre van szükség.

A következő szakaszokban további részleteket talál az alkalmazás MSAL-naplózásával kapcsolatban.