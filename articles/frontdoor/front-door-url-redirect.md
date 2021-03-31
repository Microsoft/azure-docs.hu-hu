---
title: Azure bejárati ajtó – URL-átirányítás | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure-beli bejárati ajtó hogyan támogatja az útválasztási szabályok URL-átirányítását.
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
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91442049"
---
# <a name="url-redirect"></a>URL-átirányítás
Az Azure bevezető ajtaja átirányíthatja a forgalmat a következő szinteken: protokoll, állomásnév, elérési út, lekérdezési karakterlánc. Ezek a funkciók konfigurálhatók az egyes szolgáltatásokhoz, mivel az átirányítás elérésiút-alapú. Ez leegyszerűsítheti az alkalmazások konfigurációját az erőforrás-használat optimalizálásával, és támogatja az új átirányítási forgatókönyveket, beleértve a globális és az elérésiút-alapú átirányítást.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure-beli bejárati URL-cím átirányítása":::

## <a name="redirection-types"></a>Átirányítási típusok
Az átirányítási típus beállítja az ügyfelekre vonatkozó válasz állapotkódot, hogy megértse az átirányítás célját. A következő típusú átirányítási típusok támogatottak:

- **301 (véglegesen áthelyezve)**: azt jelzi, hogy a célként megadott erőforráshoz új állandó URI van rendelve. Az erőforrásra vonatkozó jövőbeli hivatkozások az egyik befoglalt URI-t fogják használni. A HTTP-n a HTTPS-átirányítás 301-as állapotkódot használja. 
- **302 (found)**: azt jelzi, hogy a célként megadott erőforrás átmenetileg egy másik URI-n belül van. Mivel az átirányítás alkalmanként változhat, az ügyfélnek továbbra is a hatályos kérelem URI-JÁT kell használnia a jövőbeli kérésekhez.
- **307 (ideiglenes átirányítás)**: azt jelzi, hogy a célként megadott erőforrás átmenetileg egy másik URI-n belül van. A felhasználói ügynök nem változtathatja meg a kérési módszert, ha az automatikusan átirányítja az adott URI-ra. Mivel az átirányítás idővel változhat, az ügyfélnek továbbra is az eredeti érvényes kérelem URI-JÁT kell használnia a jövőbeli kérésekhez.
- **308 (állandó átirányítás)**: azt jelzi, hogy a célként megadott erőforráshoz új állandó URI van rendelve. Az erőforrásra vonatkozó jövőbeli hivatkozásoknak az egyik befoglalt URI-t kell használniuk.

## <a name="redirection-protocol"></a>Átirányítási protokoll
Beállíthatja azt a protokollt, amelyet az átirányítás használni fog. Az átirányítási funkció leggyakoribb felhasználási esetei a HTTP és a HTTPS közötti átirányítás beállítása.

- **Csak HTTPS** esetén: a PROTOKOLLT csak HTTPS értékre állítsa be, ha http-ről HTTPS-re szeretné átirányítani a forgalmat. Az Azure bejárati ajtó azt javasolja, hogy mindig csak HTTPS-re állítsa be az átirányítást.
- **Csak http**: átirányítja a bejövő kérelmet a http-re. Ezt az értéket csak akkor használja, ha a forgalmat nem titkosított HTTP-n szeretné megőrizni.
- **Kérelem egyeztetése**: ezzel a beállítással megtarthatja a bejövő kérelem által használt protokollt. Így a HTTP-kérések HTTP-n maradnak, és a HTTPS-kérések HTTPS post átirányítás maradnak.

## <a name="destination-host"></a>Cél gazdagép
Az átirányítási útválasztás konfigurálásának részeként módosíthatja az átirányítási kérelem állomásnevét vagy tartományát is. Ezt a mezőt beállíthatja úgy, hogy módosítsa az állomásnév URL-címét az átirányítás számára, vagy egyéb módon őrizze meg a gazdagépet a bejövő kérelemből. Így a mező használatával átirányíthatja a szolgáltatásba küldött összes `https://www.contoso.com/*` kérelmet `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Cél elérési útja
Olyan esetekben, amikor az URL-cím elérésiút-szegmensét az átirányítás részeként szeretné lecserélni, ezt a mezőt az új elérésiút-értékkel állíthatja be. Ellenkező esetben dönthet úgy is, hogy az elérési út értékét az átirányítás részeként megőrzi. Így a mező használatával átirányíthatja a szolgáltatásba küldött összes kérelmet `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei
A lekérdezési karakterlánc paramétereit is lecserélheti az átirányított URL-címekre. A bejövő kérelem URL-címében szereplő összes meglévő lekérdezési karakterlánc lecseréléséhez állítsa a mezőt "Replace" értékre, majd állítsa be a megfelelő értéket. Ellenkező esetben megtarthatja a lekérdezési karakterláncok eredeti készletét úgy, hogy a mezőt "megőrzés" értékre állítja. Ennek a mezőnek a használatával például átirányíthatja a szolgáltatásba érkező összes `https://www.contoso.com/foo/bar` forgalmat `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Cél töredéke
A cél töredék az URL "#" utáni része, amelyet a böngésző használ a weblapok adott szakaszának leszállásához. Ezt a mezőt beállíthatja úgy, hogy felvegyen egy töredéket az átirányítási URL-címhez.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
