---
title: Azure bejárati ajtó – útválasztási architektúra | Microsoft Docs
description: Ez a cikk segít megismerni az első ajtó architektúrájának globális nézetének aspektusát.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449196"
---
# <a name="routing-architecture-overview"></a>Útválasztási architektúra – áttekintés

Amikor az Azure bejárati ajtaja megkapja az ügyfelek kérelmeit, két dolog közül kell elvégeznie. Ha engedélyezi a gyorsítótárazást, vagy megválaszolja őket a megfelelő alkalmazás-háttérre, fordított proxyként továbbítja őket.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Az előtérben lévő környezet kiválasztása a forgalmi útválasztáshoz

Az Azure-beli bejárati környezetek felé [irányuló](https://en.wikipedia.org/wiki/Anycast) forgalom a DNS (Tartománynévrendszer) és a http-(Hypertext Transfer Protocol-) forgalom számára egyaránt biztosít lehetőséget, amely lehetővé teszi, hogy a felhasználók a legkevesebb hálózati ugrásban elérjék a legközelebbi környezetet. Ez az architektúra jobb időpontot biztosít a végfelhasználók számára azáltal, hogy maximalizálja a felosztott TCP előnyeit. A bejárati ajtó a környezeteket elsődleges és tartalék "gyűrűkre" rendezi. A külső gyűrű olyan környezetekkel rendelkezik, amelyek közelebb vannak a felhasználókhoz, és kevesebb késést kínálnak.  A belső gyűrű olyan környezettel rendelkezik, amely képes kezelni a külső gyűrűs környezet feladatátvételét abban az esetben, ha bármilyen probléma történne. A külső kör az összes forgalom előnyben részesített célja, a belső gyűrű pedig a forgalom túlcsordulásának kezelése a külső gyűrűből. Minden előtér-gazdagép vagy-tartomány egy elsődleges virtuális IP-címet (Virtual Internet Protocol címeket) kap, melyet a belső és külső gyűrűben lévő környezetek jelentenek. A tartalék VIP-t csak a belső gyűrűben lévő környezetek jelentették be. 

Ez az architektúra biztosítja, hogy a végfelhasználók kérései mindig elérjék a legközelebb bejárati ajtós környezetet. Még akkor is, ha az elsődleges rendszerállapot-környezet állapota nem kifogástalan, a rendszer automatikusan a legközelebbi legközelebbi környezetbe helyezi át a forgalmat.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Csatlakozás az előtérben lévő környezethez (Split TCP)

A [felosztott TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) egy olyan módszer, amely csökkenti a késéseket és a TCP-problémákat azáltal, hogy egy olyan kapcsolódási ponttal szakítja meg a hozzáférést, amely a nagy adatátviteli idő kisebb Az előtér-környezetek a végfelhasználók számára közelebb vannak, a TCP-kapcsolatok az előtér-környezeten belül leállnak. Egy olyan TCP-kapcsolat, amely az alkalmazási háttér nagy időpontra (RTT) mutat, két különálló kapcsolatra oszlik. A "rövid kapcsolat" a végfelhasználó és az előtér-környezet között azt jelenti, hogy a kapcsolat három rövid oda-és bejárati idő alatt jön, és a késés megtakarítását eredményezi. A "hosszú kapcsolat" a bejárati ajtó környezete és a háttérrendszer között előre megnyitható, majd más végfelhasználók számára a kapcsolódási idő megtakarítása is felhasználható. Az SSL/TLS (Transport Layer Security) kapcsolat létesítése során a felosztott TCP hatása megszorul, mivel a kapcsolatok biztonságossá tételéhez több kör alakú út van.

## <a name="processing-request-to-match-a-routing-rule"></a>Útválasztási szabálynak megfelelő kérelem feldolgozása
A kapcsolatok létrehozása és a TLS-kézfogás elvégzése után az első lépés azt követően, hogy a kérelem egy bejárati környezetben landol, az az útválasztási szabálynak megfelelő. Az egyeztetést a bejárati ajtón lévő konfigurációk határozzák meg, amelyekhez az adott útválasztási szabálynak meg kell egyeznie a kérelemmel. További információért olvassa el, hogy a bevezető ajtó hogyan felel meg a [megfelelő útvonalnak](front-door-route-matching.md) .

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Az elérhető háttérrendszer meghatározása a háttér-készletben az útválasztási szabályhoz
Ha a bejárati ajtó megfelelt egy bejövő kérelem útválasztási szabályának, a következő lépés az útválasztási szabályhoz társított háttér-készlet állapotának lekérése, ha nincs gyorsítótárazás. További információért olvassa el, hogy a bejárati ajtó hogyan [figyeli a háttér](front-door-health-probes.md) -állapotot az állapotfigyelő tesztek használatával.

## <a name="forwarding-the-request-to-your-application-backend"></a>A kérelem továbbítása az alkalmazás-háttérbe
Végül, ha a gyorsítótárazás nincs konfigurálva, a rendszer továbbítja a felhasználói kérelmet az [útválasztási módszer](front-door-routing-methods.md) konfigurációja alapján a "legjobb" háttérbe.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
