---
title: Az Azure-beli bejárati architektúra architektúrájának és terminológiájának szabályozása
description: Ez a cikk áttekintést nyújt az Azure bejárati szabályainak motor szolgáltatásáról.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 110d0a347020e2bf2c1e957ed6dab65ffe13e2f0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056511"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Mi a szabályok motorja az Azure bejárati kapujában? 

A Rules Engine lehetővé teszi a HTTP-kérések kezelésének testreszabását a peremhálózat szélén, és a webalkalmazások által vezérelt viselkedést biztosít. Az Azure bejárati ajtóhoz tartozó szabályok motorja számos főbb funkcióval rendelkezik, többek között:

* A HTTPS kikényszeríti annak biztosítására, hogy minden végfelhasználó kommunikáljon a tartalommal biztonságos kapcsolaton keresztül.
* A biztonsági fejléceket implementálva megakadályozhatja a böngészőalapú biztonsági réseket, például a HTTP Strict-Transport-Security (HSTS), az X-XSS-Protection, a Content-Security-Policy, az X-frame-Options és a hozzáférés-vezérlés-engedélyezési-Origin fejléceket a különböző eredetű erőforrás-megosztási (CORS) forgatókönyvekhez. A biztonság-alapú attribútumok cookie-kkal is meghatározhatók.
* Az alkalmazás mobil-vagy asztali verziójára irányuló kérelmek átirányítása a kérelem fejlécének tartalma, cookie-k vagy lekérdezési karakterláncok mintái alapján.
* Az átirányítási képességekkel 301, 302, 307 és 308 értéket adhat vissza az ügyfélnek, hogy közvetlenül az új gazdagépekre, elérési utakra vagy protokollokra irányítsa.
- Dinamikusan módosítja az útvonal gyorsítótárazási konfigurációját a bejövő kérelmek alapján.
- Írja át újra a kérelem URL-címét, és továbbítsa a kérést a megfelelő háttérbe a konfigurált háttérrendszer-készletben.

## <a name="architecture"></a>Architektúra 

A szabályok motorja kezeli a kérelmeket az Edge-ben. Amikor egy kérelem eléri a bejárati ajtó végpontját, a rendszer először a WAF hajtja végre, majd ezt követi a előtér/tartományhoz társított szabályok motor konfigurációja. Ha a szabályok motorjának konfigurációja fut, az azt jelenti, hogy a szülő-útválasztási szabály már egyezik. Az egyes szabályokban végrehajtott összes művelet végrehajtásához az adott szabályban található összes egyezési feltételnek teljesülnie kell. Ha egy kérelem nem felel meg a szabály-végrehajtó konfigurációjának egyik feltételének sem, akkor az alapértelmezett útválasztási szabály lesz végrehajtva. 

Az alábbi ábrán például a szabályok motorja a válasz fejlécének hozzáfűzésére van konfigurálva. A fejléc megváltoztatja a gyorsítótár-vezérlő Max-Age értékeit, ha az egyeztetési feltétel teljesül. 

![Válasz fejlécének művelete](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Egy másik példában látható, hogy a szabályok motorja úgy van konfigurálva, hogy egy felhasználót a hely mobil verziójára küldjön, ha az egyeztetési feltétel, az eszköz típusa igaz. 

![útvonal konfigurációjának felülbírálása](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Mindkét példa esetében, ha egyetlen egyezési feltétel sem teljesül, a megadott útválasztási szabály az, amit végrehajt. 

## <a name="terminology"></a>Terminológia 

A AFD Rules Engine használatával létrehozhatók a szabályokhoz tartozó konfigurációk kombinációja, amelyek mindegyike szabályokból áll. Az alábbiakban néhány hasznos terminológiát ismertetünk, amely a szabályok motorjának konfigurálásakor fog megjelenni. 

- *Rules Engine-konfiguráció*: egyetlen útvonalra vonatkozó szabályra alkalmazott szabályok halmaza. Mindegyik konfiguráció 25 szabályra van korlátozva. Akár 10 konfigurációt is létrehozhat. 
- *Rules Engine szabály*: egy legfeljebb 10 egyeztetési feltételből és 5 műveletből álló szabály.
- *Egyeztetési feltétel*: a beérkező kérések elemzéséhez számos egyezési feltételt lehet használni. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltételek a **és** a operátorral vannak kiértékelve. Az egyeztetési feltételek teljes listája [itt](front-door-rules-engine-match-conditions.md)található. 
- *Művelet*: a műveletek azt írják elő, hogy mi történik a bejövő kérelmekkel – a kérés/válasz fejléc műveletei, a továbbítás, az átirányítások és az újraírások még ma elérhetők. Egy szabály legfeljebb öt műveletet tartalmazhat. egy szabály azonban csak egy útvonal-konfiguráció felülbírálását tartalmazhatja.  A műveletek teljes listája [itt](front-door-rules-engine-actions.md)található.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan konfigurálhatja az első [szabályok motorjának konfigurációját](front-door-tutorial-rules-engine.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
