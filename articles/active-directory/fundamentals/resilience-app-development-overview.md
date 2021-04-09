---
title: Növelje a fejlesztés alatt álló hitelesítési és engedélyezési alkalmazások rugalmasságát
titleSuffix: Microsoft identity platform
description: A Azure Active Directory és a Microsoft Identity platform használatával való alkalmazásfejlesztés rugalmassági útmutatójának áttekintése
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96317352"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Növelje a fejlesztés alatt álló hitelesítési és engedélyezési alkalmazások rugalmasságát

A Microsoft Identity modern, jogkivonat-alapú hitelesítést és engedélyezést használ. Ez azt jelenti, hogy egy alkalmazás jogkivonatokat vásárol az identitás-szolgáltatótól a felhasználó hitelesítéséhez és az alkalmazás engedélyezéséhez a védett API-k meghívásához.

Egy jogkivonat egy bizonyos ideig érvényes, mielőtt az alkalmazásnak újat kell bekérnie. Ritkán előfordul, hogy a token lekérésére irányuló hívás egy probléma, például a hálózati vagy infrastrukturális hiba vagy a hitelesítési szolgáltatás kimaradása miatt sikertelen lehet. Ebben a dokumentumban felvázoljuk a fejlesztőknek az alkalmazásokban való rugalmasság növelésének lépéseit, ha jogkivonat-beszerzési hiba történik.

Ez a cikk útmutatást nyújt az alkalmazások rugalmasságának növeléséhez a Microsoft Identity platform és a Azure Active Directory használatával. A bejelentkezett felhasználó nevében dolgozó ügyfélalkalmazások, valamint a saját nevében működő Daemon-alkalmazások is útmutatást nyújtanak. Ajánlott eljárásokat tartalmaznak a jogkivonatok használatához, valamint az erőforrások meghívásához.

- [Rugalmasság kiépítése a bejelentkezési felhasználókat használó alkalmazásokba](resilience-client-app.md)
- [Rugalmasság létrehozása a felhasználók nélküli alkalmazásokban](resilience-daemon-app.md)
- [Hozzon létre rugalmasságot az identitás-és hozzáférés-kezelési infrastruktúrában](resilience-in-infrastructure.md)
- [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)
