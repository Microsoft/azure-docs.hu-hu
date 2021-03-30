---
title: SaaS-teljesítési API-k a Microsoft kereskedelmi piactéren
description: A bevezetési API-k, amelyek lehetővé teszik a SaaS-ajánlatok integrálását Microsoft AppSource és az Azure piactéren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037524"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>SaaS-teljesítési API-k a Microsoft kereskedelmi piactéren

A SaaS-teljesítési API-k lehetővé teszik a kiadók, más néven a független szoftvergyártók számára, hogy a Microsoft AppSource, az Azure Marketplace-en és a Azure Portalon keresztül tegyenek közzé és értékesítsenek SaaS-alkalmazásaikat. Ezek az API-k lehetővé teszik az ISV-alkalmazások számára az összes kereskedelmi támogatással rendelkező csatorna részvételét: közvetlen, partner által vezetett (viszonteladói) és mező-vezérelt.  Az ezekkel az API-kkal való integráció követelmény a transacter SaaS-ajánlatok partner Centerben történő létrehozásához és közzétételéhez.

Az ISV-k a következő API-folyamatokat implementálják az SaaS-szolgáltatás kódjának hozzáadásával, hogy ugyanazt az előfizetési állapotot használják az ISV-k és a Microsoft számára:

* Kezdőlap folyamata: a Microsoft értesíti a közzétevőt arról, hogy a piactéren egy ügyfél vásárolta meg a közzétevő SaaS-ajánlatát.
* Aktiválási folyamat: a közzétevő értesíti a Microsoftot arról, hogy az újonnan megvásárolt SaaS-fiók konfigurálva van a közzétevő oldalán.
* Folyamat frissítése: a megvásárolt csomag módosítása és/vagy a megvásárolt helyek száma.
* Folyamat felfüggesztése és visszaállítása: a megvásárolt SaaS-ajánlat felfüggesztése arra az esetre, ha az ügyfél fizetési módja már nem érvényes. A felfüggesztett ajánlat visszaállítható, ha megoldódik a fizetési mód problémája.
* Webhook-folyamatok: a Microsoft értesíti a közzétevőt a SaaS-előfizetések változásairól, valamint az ügyfél által a Microsoft oldaláról kiváltott törlésről.

A megvásárolt SaaS-előfizetés lemondása esetén az integráció nem kötelező, mivel az ügyfél a Microsoft oldaláról is elvégezhető.

A SaaS-teljesítési API-kkal való megfelelő integráció elengedhetetlen annak biztosításához, hogy

* a kiadó SaaS-ajánlatát megvásárló végfelhasználók számlázása a Microsoft által megfelelően történik.
* a végfelhasználók a piactéren vásárolt SaaS-előfizetések megvásárlását, konfigurálását, használatát és felügyeletét végzik.

Ezek az API-k lehetővé teszik, hogy a kiadó ajánlatai részt vegyenek az összes kereskedelmi támogató csatornán:

* közvetlen
* partner által vezetett (viszonteladó, CSP)
* mező – LED

A viszonteladói (CSP) forgatókönyvben a CSP a végfelhasználó nevében vásárolja meg az SaaS-ajánlatot. Az ügyfélnek a SaaS-ajánlatot kell használnia, de a CSP az a entitás, amely a következőket végzi el:

* az ügyfél számlázása
* előfizetési csomagok módosítása/a megvásárolt helyek mennyisége
* az előfizetések megszakítása

Ehhez a forgatókönyvhöz nem szükséges az API-hívások különböző folyamatainak implementálása.

A CSP-vel kapcsolatos további információkért tekintse meg a következőt: https://partner.microsoft.com/licensing .

>[!Warning]
>Az API jelenlegi verziója 2. verzió, amelyet minden új SaaS-ajánlathoz használni kell. Az API 1. verziója elavult, és a rendszer fenntartja a meglévő ajánlatok támogatását.

>[!Note]
>A SaaS-teljesítési API-k csak a közzétevő háttér-szolgáltatásának meghívására szolgálnak. Az API-kkal való közvetlen integráció nem támogatott a közzétevő weblapján. Csak a szolgáltatás és a szolgáltatás közötti hitelesítési folyamat használható.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, regisztráljon az SaaS-alkalmazást a [Azure Portal](https://ms.portal.azure.com) az [Azure ad-alkalmazás regisztrálása](./pc-saas-registration.md)című részben leírtak szerint.  Ezt követően használja az interfész legújabb verzióját a fejlesztéshez: [SaaS beteljesülés API 2-es verziója](./pc-saas-fulfillment-api-v2.md).
