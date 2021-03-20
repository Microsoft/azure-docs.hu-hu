---
title: Rugalmas interfészek külső folyamatokkal Azure AD B2C használatával | Microsoft Docs
description: Rugalmas felületek külső folyamatokkal való kiépítésének módszerei
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724407"
---
# <a name="resilient-interfaces-with-external-processes"></a>Rugalmas felületek külső folyamatokkal

Ebből a cikkből megtudhatja, hogyan tervezze meg és implementálja a REST API-kat a felhasználói úton, és hogy az alkalmazása rugalmasabb legyen az API-hibákhoz.

![A képen láthatók a külső folyamatok összetevőivel rendelkező felületek](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Az API-k helyes elhelyezésének biztosítása

A IEF-szabályzatok lehetővé teszik külső rendszer meghívását egy [REST API technikai profil](../../active-directory-b2c/restful-technical-profile.md)használatával. A külső rendszereket nem a IEF futásidejű környezete vezérli, és egy lehetséges meghibásodási pont.

### <a name="how-to-manage-external-systems-using-apis"></a>Külső rendszerek kezelése API-k használatával

- Az egyes adatelemek elérésére szolgáló felület meghívása során győződjön meg arról, hogy az adatküldés a hitelesítési döntést fogja-e vezetni. Mérje fel, hogy az információ elengedhetetlen-e az alkalmazás alapvető funkcióihoz. Például egy e-kereskedelmi és egy másodlagos funkció, például egy adminisztráció. Ha az adatok nem szükségesek a hitelesítéshez, és csak másodlagos forgatókönyvekhez szükségesek, érdemes lehet áthelyezni a hívást az alkalmazás logikába.

- Ha a hitelesítéshez szükséges adatok viszonylag statikusak és kicsik, és nincs más üzleti oka a címtárból való külső használatra, akkor érdemes azt a címtárban megfontolni.

- Ha lehetséges, távolítsa el az API-hívásokat az előre hitelesített elérési útról. Ha nem tudja, szigorú védelmet kell biztosítania a szolgáltatásmegtagadási (DoS) és az elosztott szolgáltatásmegtagadási (DDoS) támadások ellen az API-k előtt. A támadók betölthetik a bejelentkezési oldalt, és megpróbálják elárasztani az API-t DoS-támadásokkal, és megbénult az alkalmazás. Ha például a bejelentkezéshez a CAPTCHA-t használja, a regisztráció folyamata segíthet.

- A [beépített regisztrációs felhasználói folyamat API-összekötőit](../../active-directory-b2c/api-connectors-overview.md) használhatja, ahol lehetséges a webes API-k integrálása, vagy az identitás-szolgáltatóval való bejelentkezés, vagy a felhasználó létrehozása előtt. Mivel a felhasználói folyamatokat már alaposan tesztelik, valószínű, hogy nem kell végrehajtania a felhasználói folyamatok működési szintjének, teljesítményének vagy méretezésének tesztelését. Továbbra is tesztelni kell az alkalmazásokat a funkciók, a teljesítmény és a skálázás érdekében.

- Az Azure AD REST API [technikai profiljai](../../active-directory-b2c/restful-technical-profile.md) nem biztosítanak gyorsítótárazási viselkedést. Ehelyett a REST API-profil megvalósítja az újrapróbálkozási logikát és a szabályzatba beépített időtúllépést.

- Az adatírást igénylő API-k esetében a feladatokat egy adott háttérben dolgozó feldolgozó hajtja végre. A szolgáltatások, például az [Azure Queues](../../storage/queues/storage-queues-introduction.md) használhatók. Ennek hatására a rendszer hatékonyan visszaállíthatja az API-t a házirend-végrehajtás teljesítményének növelése érdekében.  

## <a name="api-error-handling"></a>API-hibakezelés

Mivel az API-k a Azure AD B2C rendszeren kívül vannak, a technikai profilban megfelelő hibakezelés szükséges. Győződjön meg arról, hogy a végfelhasználót megfelelően tájékoztatták, és az alkalmazás szabályosan képes kezelni a hibát.

### <a name="how-to-gracefully-handle-api-errors"></a>API-hibák szabályosan történő kezelése

- Az API-k különböző okok miatt sikertelenek lehetnek, így rugalmasan teheti az alkalmazást az ilyen hibákra. [Http-4XX hibaüzenet küldése](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) , ha az API nem tudja befejezni a kérelmet. A Azure AD B2C szabályzatban próbálja meg szabályosan kezelni az API-k nem rendelkezésre állását, és lehet, hogy kisebb felhasználói élményt nyújt.

- Az [átmeneti hibák kezelése szabályosan](../../active-directory-b2c/restful-technical-profile.md#error-handling). A REST API-profil lehetővé teszi a különböző [áramkör-megszakítók](/azure/architecture/patterns/circuit-breaker)számára vonatkozó hibaüzenetek konfigurálását.

- Proaktívan figyelheti és használhatja a folyamatos integrációt/folyamatos kézbesítést (vel), elforgathatja az API-hozzáférési hitelesítő adatokat, például a [technikai profil motorja](../../active-directory-b2c/restful-technical-profile.md)által használt jelszavakat és tanúsítványokat.

## <a name="api-management---best-practices"></a>API Management – ajánlott eljárások

A REST API-k üzembe helyezése és a REST-es technikai profil konfigurálása során a javasolt ajánlott eljárások alapján nem végezheti el a gyakori hibákat és a feleslegesen megjelenő dolgokat.

### <a name="how-to-manage-apis"></a>API-k kezelése

- API Management (APIM) az API-k közzétételét, kezelését és elemzését végzi. A APIM a hitelesítést is kezeli, hogy biztonságos hozzáférést biztosítson a háttérbeli szolgáltatásokhoz és a szolgáltatásokhoz. API-átjáró használatával bővítheti az API-telepítéseket, a gyorsítótárazást és a terheléselosztást.

- Javasoljuk, hogy az egyes API-k többszöri meghívása és az [Azure APIM API biztonságossá tétele](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga)helyett a megfelelő tokent kapja meg a felhasználói út elején.

## <a name="next-steps"></a>Következő lépések

- [Rugalmassági erőforrások Azure AD B2C-fejlesztőknek](resilience-b2c.md)
  - [Rugalmas végfelhasználói élmény](resilient-end-user-experience.md)
  - [Rugalmasság a fejlesztőknek ajánlott eljárások révén](resilience-b2c-developer-best-practices.md)
  - [Rugalmasság a monitorozás és az elemzés révén](resilience-with-monitoring-alerting.md)
- [Rugalmasság kialakítása a hitelesítési infrastruktúrában](resilience-in-infrastructure.md)
- [A hitelesítés és az engedélyezés rugalmasságának fokozása az alkalmazásokban](resilience-app-development-overview.md)