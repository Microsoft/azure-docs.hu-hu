---
title: Az Azure bejárati ajtó standard/prémium szintű használata az erőforrás-megosztási erőforrások megosztása révén
description: Megtudhatja, hogyan használhatja az Azure bejárati ajtót (AFD) a több eredetű erőforrás-megosztással (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098839"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Az Azure bejárati ajtó standard/prémium szintű használata a több eredetű erőforrás-megosztással (CORS)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

## <a name="what-is-cors"></a>Mi az a CORS?

A CORS (eltérő eredetű erőforrások megosztása) egy olyan HTTP-szolgáltatás, amely egy adott tartományban futó webalkalmazás számára teszi lehetővé, hogy hozzáférjen egy másik tartomány erőforrásaihoz. A helyek közötti parancsfájlok elleni támadások lehetőségének csökkentése érdekében minden modern webböngészővel [azonos eredetű](https://www.w3.org/Security/wiki/Same_Origin_Policy)biztonsági korlátozást valósíthat meg. Ez megakadályozza, hogy egy weblap más tartományba tartozó API-kat hívjon fel.  A CORS lehetővé teszi, hogy egy forrás (a forrás tartomány) egy másik forrásban lévő API-kat hívjon fel.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Működés

Kétféle CORS-kérés, *egyszerű kérelem* és *összetett kérelem van.*

### <a name="for-simple-requests"></a>Egyszerű kérelmek esetén:

1. A böngésző egy másik **forrás** HTTP-kérelem fejlécével küldi el a CORS kérelmet. Ennek a fejlécnek az értéke a szülő lapot kiszolgáló forrás, amely a *protokoll,* a *tartomány* és a port kombinációjával van meghatározva *.*  Ha egy https \: //www.contoso.com-oldal megpróbál hozzáférni egy felhasználói adatokhoz a fabrikam.com-forrásban, a rendszer a következő fabrikam.com küldi el:

   `Origin: https://www.contoso.com`

2. A kiszolgáló a következők bármelyikével válaszolhat:

   * Egy **hozzáférés-vezérlési engedélyezési-származási** fejléc a válaszban, amely azt jelzi, hogy melyik hely engedélyezett. Például:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Egy HTTP-hibakód, például 403, ha a kiszolgáló nem engedélyezi az eredeti forrásból származó kérelmet a forrás fejlécének ellenőrzése után

   * **Hozzáférés-vezérlési-engedélyezési-származási** fejléc olyan helyettesítő karakterrel, amely lehetővé teszi az összes eredetét:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Összetett kérelmek esetén:

Egy összetett kérelem egy CORS-kérelem, amelyben a böngészőnek el kell küldenie egy *elővizsgálati kérelmet* (azaz egy előzetes mintavételt) a tényleges CORS-kérelem elküldése előtt. Az elővizsgálati kérelem kéri a kiszolgáló engedélyét, ha az eredeti CORS-kérelem folytatódni fog, és `OPTIONS` ugyanarra az URL-címre irányuló kérést küld.

> [!TIP]
> A CORS és a gyakori buktatókkal kapcsolatos további részletekért tekintse meg a [CORS for REST API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)-k útmutatóját.
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Helyettesítő karakteres vagy önálló eredetű forgatókönyvek
Az Azure-beli CORS automatikusan, külön konfiguráció nélkül fog működni, ha a **hozzáférés-vezérlés engedélyezése-Origin** fejléce helyettesítő karakter (*) vagy egyetlen forrás.  Az Azure bejárati ajtaja gyorsítótárazza az első választ, és az azt követő kérelmek ugyanazt a fejlécet fogják használni.

Ha a rendszer már megadta a kérelmeket az Azure-előtérben, mielőtt CORS a forrásra, ki kell ürítenie a tartalmat a végpont tartalmán, hogy újra lehessen tölteni a tartalmat a **hozzáférés-vezérlés-engedélyezési-Origin** fejléctel.

## <a name="multiple-origin-scenarios"></a>Több eredetű forgatókönyv
Ha engedélyezni szeretné, hogy egy adott lista a CORS számára engedélyezett legyen, a dolgok egy kicsit bonyolultabbak lesznek. A probléma akkor fordul elő, amikor a CDN gyorsítótárazza a **hozzáférés-vezérlés-engedélyezés-Origin** fejlécet az első CORS-forráshoz.  Ha egy másik CORS-forrás más kérelmet tesz elérhetővé, a CDN a gyorsítótárazott **hozzáférés-vezérlés-engedélyezési-Origin** fejlécet fogja kiszolgálni, amely nem egyezik. A probléma számos módon orvosolható.

### <a name="azure-front-door-rule-set"></a>Azure-beli előtérben beállított szabálykészlet

Az Azure-beli bejárati ajtón létrehozhat egy szabályt az Azure bejárati ajtó [szabályaiban](concept-rule-set.md) úgy, hogy a kérelemben található **forrás** fejlécet ellenőrizzék. Ha érvényes a forrás, a szabály a megfelelő értékkel állítja be a **hozzáférés-vezérlés-engedélyezés-Origin** fejlécet. Ebben az esetben a rendszer figyelmen kívül hagyja a fájl forrás-kiszolgálójának **hozzáférés-vezérlés-engedélyezés-eredet** fejlécét, és a AFD szabályainak motorja teljes mértékben felügyeli az engedélyezett CORS-eredetű elemeket.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Képernyőkép a szabályokról – példa a szabálykészlet beállítására.":::

> [!TIP]
> További műveleteket is hozzáadhat a szabályhoz a további válaszok fejlécek, például a **hozzáférés-vezérlés-engedélyezés-metódusok** módosításához.
> 

## <a name="next-steps"></a>Következő lépések

* Útmutató a [Front Door létrehozásához](create-front-door-portal.md).
