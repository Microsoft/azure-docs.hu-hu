---
title: URL-átirányítás és URL-cím újraírása az Azure-beli előtérben standard/prémium (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan támogatja az Azure bejárati ajtó az URL-átirányítást és az URL-újraírást az Azure bejárati
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099170"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>URL-átirányítás és URL-cím újraírása az Azure-beli előtérben standard/prémium (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ebből a cikkből megtudhatja, hogy az Azure bejárati ajtó standard/Premium hogyan támogatja az URL-átirányítás és a szabálykészlet URL-címének újraírását.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>URL-átirányítás

Az Azure bejárati ajtaja átirányíthatja a forgalmat a következő szinteken: protokoll, állomásnév, elérési út, lekérdezési karakterlánc és töredék. Ezek a funkciók konfigurálhatók az egyes mikro-szolgáltatásokhoz, mivel az átirányítás útvonal-alapú. Az URL-átirányítás segítségével egyszerűsítheti az alkalmazások konfigurációját az erőforrás-használat optimalizálásával, és támogatja az új átirányítási forgatókönyveket, beleértve a globális és az elérésiút-alapú átirányítást.

Az URL-átirányítás beállítható szabálykészlet használatával.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Képernyőkép az URL-átirányítás létrehozásához a szabálykészlet beállításával." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Átirányítási típusok
Az átirányítási típus beállítja az ügyfelekre vonatkozó válasz állapotkódot, hogy megértse az átirányítás célját. A következő típusú átirányítási típusok támogatottak:

* **301 (véglegesen áthelyezve)**: azt jelzi, hogy a célként megadott erőforráshoz új állandó URI van rendelve. Az erőforrásra vonatkozó jövőbeli hivatkozások az egyik befoglalt URI-t fogják használni. A HTTP-n a HTTPS-átirányítás 301-as állapotkódot használja.
* **302 (found)**: azt jelzi, hogy a célként megadott erőforrás átmenetileg egy másik URI-n belül van. Mivel az átirányítás alkalmanként változhat, az ügyfélnek továbbra is a hatályos kérelem URI-JÁT kell használnia a jövőbeli kérésekhez.
* **307 (ideiglenes átirányítás)**: azt jelzi, hogy a célként megadott erőforrás átmenetileg egy másik URI-n belül van. A felhasználói ügynök nem változtathatja meg a kérési módszert, ha az automatikusan átirányítja az adott URI-ra. Mivel az átirányítás idővel változhat, az ügyfélnek továbbra is az eredeti érvényes kérelem URI-JÁT kell használnia a jövőbeli kérésekhez.
* **308 (állandó átirányítás)**: azt jelzi, hogy a célként megadott erőforráshoz új állandó URI van rendelve. Az erőforrásra vonatkozó jövőbeli hivatkozásoknak az egyik befoglalt URI-t kell használniuk.

### <a name="redirection-protocol"></a>Átirányítási protokoll
Beállíthatja azt a protokollt, amelyet az átirányítás használni fog. Az átirányítási funkció leggyakoribb felhasználási esetei a HTTP és a HTTPS közötti átirányítás beállítása.

* **Csak HTTPS** esetén: a PROTOKOLLT csak HTTPS értékre állítsa be, ha http-ről HTTPS-re szeretné átirányítani a forgalmat. Az Azure bejárati ajtó azt javasolja, hogy mindig csak HTTPS-re állítsa be az átirányítást.
* **Csak http**: átirányítja a bejövő kérelmet a http-re. Ezt az értéket csak akkor használja, ha a forgalmat nem titkosított HTTP-n szeretné megőrizni.
* **Kérelem egyeztetése**: ezzel a beállítással megtarthatja a bejövő kérelem által használt protokollt. Így a HTTP-kérések HTTP-n maradnak, és a HTTPS-kérések HTTPS post átirányítás maradnak.

### <a name="destination-host"></a>Cél gazdagép
Az átirányítási útválasztás konfigurálásának részeként módosíthatja az átirányítási kérelem állomásnevét vagy tartományát is. Ezt a mezőt beállíthatja úgy, hogy módosítsa az állomásnév URL-címét az átirányítás számára, vagy egyéb módon őrizze meg a gazdagépet a bejövő kérelemből. Így a mező használatával átirányíthatja a szolgáltatásba küldött összes `https://www.contoso.com/*` kérelmet `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Cél elérési útja
Olyan esetekben, amikor az URL-cím elérésiút-szegmensét az átirányítás részeként szeretné lecserélni, ezt a mezőt az új elérésiút-értékkel állíthatja be. Ellenkező esetben dönthet úgy is, hogy az elérési út értékét az átirányítás részeként megőrzi. Így a mező használatával átirányíthatja a szolgáltatásba küldött összes kérelmet `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei
A lekérdezési karakterlánc paramétereit is lecserélheti az átirányított URL-címekre. A bejövő kérelem URL-címében szereplő összes meglévő lekérdezési karakterlánc lecseréléséhez állítsa a mezőt "Replace" értékre, majd állítsa be a megfelelő értéket. Ellenkező esetben megtarthatja a lekérdezési karakterláncok eredeti készletét úgy, hogy a mezőt "megőrzés" értékre állítja. Ennek a mezőnek a használatával például átirányíthatja a szolgáltatásba érkező összes `https://www.contoso.com/foo/bar` forgalmat `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Cél töredéke
A cél töredék az URL "#" utáni része, amelyet a böngésző használ a weblapok adott szakaszának leszállásához. Ezt a mezőt beállíthatja úgy, hogy felvegyen egy töredéket az átirányítási URL-címhez.

## <a name="url-rewrite"></a>URL-átírás

Az Azure bevezető ajtaja támogatja az URL-cím újraírását, hogy átírja a saját forrására irányuló kérelem elérési útját. Az URL-cím újraírása lehetővé teszi feltételek hozzáadását annak biztosításához, hogy az URL-cím vagy a megadott fejlécek csak akkor legyenek újraírva, ha bizonyos feltételek teljesülnek. Ezek a feltételek a kérelem és a válasz információin alapulnak.

Ezzel a funkcióval különböző forrásokra irányíthatja át a felhasználókat a forgatókönyv, az eszköz típusa és a kért fájltípus alapján.

Az URL-átirányítás beállítható szabálykészlet használatával.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Képernyőkép a szabálykészlet URL-re való újraírásának létrehozásáról." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Forrás mintája

A forrás minta a lecserélni kívánt forrás URL-címének elérési útja. Jelenleg a forrás minta előtag-alapú egyezést használ. Az összes URL-cím eléréséhez használjon egy perjelet (/) a forrás minta értékének megfelelően.

### <a name="destination"></a>Cél

Megadhatja az újraíráshoz használandó célhely elérési útját. A cél elérési útja felülírja a forrás mintát.

### <a name="preserve-unmatched-path"></a>Páratlan elérési út megőrzése

A páratlan elérési út megőrzése lehetővé teszi a hátralévő elérési út hozzáfűzését a forrás mintázatának az új elérési útra való hozzáadását követően.

Ha például a nem **egyező elérési út megőrzése igen** értékre van állítva.
* Ha a bejövő kérelem, a `www.contoso.com/sub/1.jpg` forrásként megadott minta, a cél a értékre van állítva, a tartalom pedig. jpg-ből lesz `/` `/foo/` kiszolgálva a forrástól `/foo/sub/1` .

* Ha a bejövő kérelem a, a forrás minta lesz a értékre beállítva, a cél a következő lesz: `www.contoso.com/sub/image/1.jpg` . a tartalom a forrástól származik `/sub/` `/foo/` `/foo/image/1.jpg` .

Ha például a **nem egyező elérési út megőrzése nem** értékre van állítva.
* Ha a bejövő kérelem értéke, a forrás minta a értékre van állítva, a cél a következő lesz `www.contoso.com/sub/image/1.jpg` `/sub/` :, `/foo/2.jpg` a tartalmat a rendszer mindig az eredeti forrásból fogja kiszolgálni, függetlenül attól `/foo/2.jpg` , hogy milyen útvonalakat követett el `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure bejárati ajtó standard/prémium szabálykészlet beállításáról](concept-rule-set.md).
