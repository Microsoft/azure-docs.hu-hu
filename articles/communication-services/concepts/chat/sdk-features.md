---
title: Csevegő ügyféloldali kódtár – áttekintés az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg az Azure kommunikációs szolgáltatások csevegési ügyfelének könyvtárát.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fd62a7fed0c6dec818bf4e2558945fb24c9d5261
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947150"
---
# <a name="chat-client-library-overview"></a>Csevegő ügyféloldali kódtár – áttekintés

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások csevegési ügyfélszoftverei gazdag, valós idejű csevegést vehetnek igénybe az alkalmazásaihoz.

## <a name="chat-client-library-capabilities"></a>Csevegési ügyféloldali kódtár képességei

A következő lista a kommunikációs szolgáltatások csevegési ügyféloldali könyvtáraiban jelenleg elérhető funkciókat mutatja be.

| Szolgáltatások csoportja | Képesség                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Alapvető képességek | Csevegési szál létrehozása 2 vagy több felhasználó között (legfeljebb 250 felhasználó)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Csevegési szál témakörének frissítése                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Csevegési szál tagjainak hozzáadása vagy eltávolítása                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Adja meg, hogy meg szeretné-e osztani a csevegési előzményeket az újonnan hozzáadott tagokkal – *az összes/none/akár bizonyos ideig* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Az összes csevegési tag szál listájának beolvasása                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Csevegési szál törlése                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | A felhasználók csevegési szál tagsági listájának beolvasása                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Egy adott csevegési szál adatainak beolvasása                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Üzenetek küldése és fogadása csevegési szálban                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Üzenet tartalmának szerkesztése az elküldés után                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Üzenet törlése                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Az üzenet küldése a küldéskor normál vagy magas prioritással                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Olvasási visszaigazolások küldése és fogadása a tagok által olvasott üzenetekhez <br/> *Nem érhető el, ha több mint 20 tag van egy csevegési szálban*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Értesítés küldése és fogadása, ha egy tag aktívan beírja az üzenetet egy csevegési szálba <br/> *Nem érhető el, ha több mint 20 tag van egy csevegési szálban*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Csevegési szál összes üzenetének beolvasása <br/> *Támogatott Unicode-hangulatjelek*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Hangulatjelek küldése az üzenet tartalmának részeként                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Valós idejű jelzés (a tulajdonosi jelző csomaggal engedélyezve)| Értesítés küldése, ha egy felhasználó olyan csevegési szálban kap új üzenetet, amelyik tagja                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Értesítés küldése, ha egy üzenetet egy másik tag szerkeszt egy csevegési szálban, amely tagja a                | ✔️   | ❌    | ❌    | ❌  |
|                    | Értesítés küldése, ha egy csevegési szál egy másik tag törölte egy üzenetet.                | ✔️   | ❌    | ❌    | ❌  |
|                    | Értesítés küldése, ha egy másik csevegési szál tagja begépel                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Értesítés küldése, ha egy másik tag beolvasott egy üzenetet (olvasási visszaigazolás) a csevegési szálban                               | ✔️   | ❌    | ❌    | ❌  |
| Események             | Event Grid használata a csevegési szálakkal kapcsolatos felhasználói tevékenységre való előfizetéshez és az egyéni értesítési szolgáltatások és az üzleti logika integrálásához     | ✔️   | ✔️  | ✔️    | ✔️  |
| Figyelés        | Használat figyelése az elküldött üzenetek tekintetében                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Az alkalmazás által végzett API-kérelmek minőségének és állapotának, valamint a riasztások a portálon keresztüli konfigurálásának monitorozása                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|További funkciók | A [Cognitive Services API-k](https://docs.microsoft.com/azure/cognitive-services/) és a csevegő ügyféloldali kódtár használatával is lehetővé teszi az intelligens funkciók – *nyelvi fordítás & a beérkező üzenet hangulati elemzését egy ügyfélen, a szöveg átalakítását egy üzenet összeállításához, miközben a tag beszél stb.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a csevegéssel](../../quickstarts/chat/get-started.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg a [csevegési fogalmakkal](../chat/concepts.md)
