---
title: Enyhítések – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: A lehetséges megoldások kiemelésének Microsoft Threat Modeling Tool a leginkább kitett fenyegetésekre.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728036"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool enyhítések

A Threat Modeling Tool a Microsoft biztonsági fejlesztési életciklus (SDL) alapvető eleme. Lehetővé teszi a szoftverfejlesztők számára, hogy korán azonosítsák és csökkentsék a potenciális biztonsági problémákat, amikor viszonylag könnyen és költséghatékonyan oldják meg a megoldást. Ennek eredményeképpen jelentős mértékben csökkenti a teljes fejlesztési költségeket. Azt is megtervezték, hogy az eszközt nem biztonsági szakértők is megalakították, így a fenyegetések modellezése minden fejlesztő számára egyszerűbb, mivel egyértelmű útmutatást nyújt a veszélyforrások létrehozásához és elemzéséhez.

Látogasson el a **[Threat Modeling toolra](threat-modeling-tool.md)** , és kezdje el még ma!

## <a name="mitigation-categories"></a>Kockázatcsökkentő kategóriák

A Threat Modeling Tool enyhítése a webalkalmazás biztonsági keretének megfelelően van kategorizálva, amely a következőkből áll:

| Kategória | Leírás |
| -------- | ----------- |
| **[Naplózás és naplózás](threat-modeling-tool-auditing-and-logging.md)** | Kinek és Mikor? A naplózás és a naplózás arra utal, hogy az alkalmazás hogyan rögzíti a biztonsággal kapcsolatos eseményeket |
| **[Hitelesítés](threat-modeling-tool-authentication.md)** | Melyik szerepbe tartozik? A hitelesítés az a folyamat, amelyben egy entitás igazolja egy másik entitás identitását, jellemzően hitelesítő adatokkal, például felhasználónévvel és jelszóval. |
| **[Engedélyezés](threat-modeling-tool-authorization.md)** | Mit tehet? Az engedélyezés az, hogy az alkalmazás hogyan biztosít hozzáférés-vezérlést az erőforrásokhoz és a műveletekhez |
| **[Kommunikáció biztonsága](threat-modeling-tool-communication-security.md)** | Kik beszélnek? A kommunikáció biztonsága biztosítja, hogy minden kommunikáció a lehető legbiztonságosabb legyen |
| **[Konfiguráció kezelése](threat-modeling-tool-configuration-management.md)** | Ki futtatja az alkalmazást? Mely adatbázisokhoz csatlakozik? Hogyan történik az alkalmazás felügyelete? Hogyan biztosítják ezeket a beállításokat? A konfiguráció kezelése arra utal, hogy az alkalmazás hogyan kezeli ezeket az üzemeltetési problémákat |
| **[Titkosítás](threat-modeling-tool-cryptography.md)** | Hogyan tartja titokban a titkokat? Hogyan módosítható az adatok vagy a kódtárak (integritás) ellenőrzése? Hogyan biztosítanak olyan magokat a véletlenszerű értékekhez, amelyeknek titkosítási szempontból erősnek kell lenniük? A kriptográfia arra utal, hogy az alkalmazás hogyan alkalmazza a titkosságot és az integritást |
| **[Kivételek kezelése](threat-modeling-tool-exception-management.md)** | Ha egy metódus hívása meghiúsul az alkalmazásban, mit tesz az alkalmazása? Mennyit tárt fel? A végfelhasználók rövid hibaüzeneteket adnak vissza? Értékes kivételi adatokat ad vissza a hívónak? Az alkalmazása szabályosan leáll? |
| **[Bemeneti ellenőrzés](threat-modeling-tool-input-validation.md)** | Honnan tudhatja, hogy az alkalmazás által fogadott adatok érvényesek és biztonságosak? A bemeneti ellenőrzés arra utal, hogy az alkalmazás hogyan szűri vagy elutasítja a bemenetet további feldolgozás előtt. A belépési pontokon keresztüli bevitel korlátozásával és a kilépési pontokon keresztüli kódolással érdemes megfontolni. Megbízhatónak minősíti az adatforrásokat, például az adatbázisokat és a fájlmegosztást? |
| **[Bizalmas adatok](threat-modeling-tool-sensitive-data.md)** | Hogyan kezeli az alkalmazása a bizalmas adatokat? A bizalmas adatok arra utalnak, hogy az alkalmazás hogyan kezeli a memóriában, a hálózaton vagy az állandó tárolókban védeni kívánt adatokat. |
| **[Munkamenet-kezelés](threat-modeling-tool-session-management.md)** | Hogyan kezeli az alkalmazás a felhasználói munkameneteket? A munkamenet egy adott felhasználó és a webalkalmazás közötti kapcsolódó interakciók sorozatára utal. |

Ez segít a következők azonosításában:

* Hol találhatók a leggyakoribb hibák
* Hol találhatók a leghatékonyabb fejlesztési műveletek

Ennek eredményeképpen ezeket a kategóriákat felhasználva koncentrálhatja és rangsorolhatja a biztonsági munkát, így ha ismeri a bemeneti ellenőrzés, a hitelesítés és az engedélyezési kategóriák legelterjedtebb biztonsági problémáit, elindíthatja. További információért látogasson el a **[szabadalmi hivatkozásra](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Következő lépések

Látogasson el **[Threat Modeling Tool fenyegetésekre](threat-modeling-tool-threats.md)** , ha többet szeretne megtudni az eszköz által a lehetséges tervezési fenyegetések létrehozásához használt veszélyforrás-kategóriákról.