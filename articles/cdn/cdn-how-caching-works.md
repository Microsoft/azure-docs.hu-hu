---
title: A gyorsítótárazás működése | Microsoft Docs
description: A gyorsítótárazás az adattárolási folyamat helyi tárolása, hogy az adott adatkérések jövőbeli kérései gyorsabban elérhetők legyenek.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: a226682c2580a871e1b2fc4db71f369f3bcc3abb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010163"
---
# <a name="how-caching-works"></a>A gyorsítótárazás működése

Ez a cikk áttekintést nyújt az általános gyorsítótárazási fogalmakról, valamint arról, hogy az [Azure Content Delivery Network (CDN)](cdn-overview.md) hogyan használja a gyorsítótárazást a teljesítmény javítása érdekében. Ha meg szeretné tudni, hogyan szabhatja testre a gyorsítótárazási viselkedést a CDN-végponton, tekintse meg az [Azure CDN gyorsítótárazási viselkedés szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md) és [a Azure CDN gyorsítótárazási viselkedés lekérdezési karakterláncokkal való kezelését](cdn-query-string.md).

## <a name="introduction-to-caching"></a>A gyorsítótárazás bemutatása

A gyorsítótárazás az adattárolási folyamat helyi tárolása, hogy az adott adatkérések jövőbeli kérései gyorsabban elérhetők legyenek. A leggyakoribb gyorsítótárazási típus a webböngésző gyorsítótárazása, a webböngésző helyileg tárolja a statikus adatpéldányokat a helyi merevlemezen. A gyorsítótárazás használatával a webböngésző elkerülheti, hogy a kiszolgáló több oda-és visszautazást is biztosítson, így az idő és az erőforrások megtakarítása is megtörténik. A gyorsítótárazás kiválóan alkalmas a kisebb, statikus adatok, például a statikus képek, a CSS-fájlok és a JavaScript-fájlok kezelésére.

Hasonlóképpen, a Content Delivery Network a peremhálózati kiszolgálókon közel a felhasználóhoz, hogy elkerülje a kérések visszautazását a forrásra, és csökkenti a végfelhasználói késést. A webböngésző-gyorsítótártól eltérően, amelyet csak egyetlen felhasználó használ, a CDN megosztott gyorsítótárral rendelkezik. A CDN megosztott gyorsítótárában az egyik felhasználó által kért fájl később más felhasználók számára is elérhető, ami nagy mértékben csökkenti a forrás-kiszolgálónak küldött kérések számát.

Az egyes felhasználók számára gyakran vagy egyedivé vált dinamikus erőforrások nem gyorsítótárazható. Az ilyen típusú erőforrások azonban kihasználhatják az Azure-Content Delivery Network a dinamikus helyek gyorsításának (DSA) optimalizálásának előnyeit a teljesítmény javításához.

A gyorsítótárazás több szinten is történhet a forráskiszolgáló és a végfelhasználó között:

- Webkiszolgáló: megosztott gyorsítótárat használ (több felhasználó esetében).
- Content Delivery Network: megosztott gyorsítótárat használ (több felhasználó számára).
- Internetszolgáltató (ISP): megosztott gyorsítótárat használ (több felhasználó számára).
- Webböngésző: privát gyorsítótárat használ (egy felhasználóhoz).

Mindegyik gyorsítótár általában a saját erőforrás-frissességét kezeli, és érvényesítést végez, ha a fájl elavult. Ez a viselkedés a HTTP-gyorsítótárazási specifikációban, az [RFC 7234](https://tools.ietf.org/html/rfc7234)-ben van meghatározva.

### <a name="resource-freshness"></a>Erőforrás-frissesség

Mivel a gyorsítótárazott erőforrások valószínűleg elavultak vagy elavultak (a forráskiszolgáló megfelelő erőforrásához képest), fontos, hogy minden gyorsítótárazási mechanizmus vezérelje a tartalom frissítését. Az idő-és sávszélesség-használat megtakarításához a rendszer nem hasonlítja össze a gyorsítótárazott erőforrásokat a forrás-kiszolgálón lévő verzióval minden egyes hozzáféréskor. Ehelyett, ha egy gyorsítótárazott erőforrás frissnek tekintendő, a rendszer azt feltételezi, hogy a legfrissebb verzió, és közvetlenül az ügyfélnek lesz küldve. A gyorsítótárazott erőforrások akkor tekinthetők frissnek, ha az életkora kevesebb, mint a gyorsítótár-beállítás által meghatározott életkor vagy időszak. Ha például egy böngésző újratölt egy weboldalt, ellenőrzi, hogy a merevlemezen lévő minden gyorsítótárazott erőforrás friss-e, és betölti azt. Ha az erőforrás nem friss (elavult), akkor a rendszer naprakész másolatot tölt be a kiszolgálóról.

### <a name="validation"></a>Érvényesítés

Ha egy erőforrás elavultnak minősül, a rendszer megkéri a forráskiszolgáló érvényességét, vagyis megállapítja, hogy a gyorsítótárban lévő adatmennyiség megegyezik-e a forráskiszolgálón lévővel. Ha a fájl módosult a forráskiszolgálón, a gyorsítótár frissíti az erőforrás verzióját. Ellenkező esetben, ha az erőforrás friss, az adatok közvetlenül a gyorsítótárból érkeznek, anélkül, hogy először érvényesíteni kellene.

### <a name="cdn-caching"></a>CDN-gyorsítótárazás

A gyorsítótárazás olyan módon működik, mint a CDN, amely felgyorsítja a kézbesítést, és csökkenti a statikus eszközök, például a képek, a betűtípusok és a videók terhelését. A CDN-gyorsítótárazásban a statikus erőforrásokat szelektíven tárolja a rendszer, amely a felhasználók számára sokkal nagyobb helyi, és a következő előnyöket kínálja:

- Mivel a legtöbb webes forgalom statikus (például képek, betűtípusok és videók), a CDN gyorsítótárazása csökkenti a hálózati késést azáltal, hogy a tartalmat közelebb helyezi a felhasználóhoz, így csökkentve az adatátviteli távolságot.

- Egy CDN-re történő kiszervezéssel a gyorsítótárazás csökkentheti a hálózati forgalmat és a forráskiszolgáló terhelését. Ez csökkenti az alkalmazás költségeit és erőforrás-követelményeit, még akkor is, ha nagy számú felhasználó van.

A gyorsítótárazás egy webböngészőben való implementálása hasonlóan azt is szabályozhatja, hogyan történjen a gyorsítótárazás a CDN-ben a gyorsítótár-direktíva fejlécek elküldésével. A cache-direktíva fejlécek a HTTP-fejlécek, amelyeket általában a forráskiszolgáló ad hozzá. Bár a fejlécek többségét eredetileg az ügyféloldali böngészők gyorsítótárazására tervezték, mostantól az összes köztes gyorsítótár, például a CDNs is használja őket. 

A gyorsítótár frissességének meghatározásához két fejléc használható: `Cache-Control` és `Expires` . `Cache-Control` több aktuális, és elsőbbséget élvez `Expires` , ha mindkettő létezik. Az érvényesítéshez (ún. validatorok) két típusú fejléc is használatos: `ETag` és `Last-Modified` . `ETag` több aktuális, és elsőbbséget élvez `Last-Modified` , ha mindkettő definiálva van.  

## <a name="cache-directive-headers"></a>Cache-direktíva fejlécek

> [!IMPORTANT]
> Alapértelmezés szerint a DSA-ra optimalizált Azure CDN-végpont figyelmen kívül hagyja a gyorsítótár-direktíva fejléceit, és megkerüli a gyorsítótárazást. A **Verizon** és a Akamai-profiloktól **Azure CDN standard** szintű Azure CDN esetében beállíthatja, hogy egy Azure CDN végpont hogyan kezelje ezeket a fejléceket a [CDN gyorsítótárazási szabályainak](cdn-caching-rules.md) használatával a gyorsítótárazás engedélyezéséhez. A csak Verizon-profilokból **származó Azure CDN Premium** esetén a [szabályok motor](./cdn-verizon-premium-rules-engine.md) használatával engedélyezheti a gyorsítótárazást.

Azure CDN támogatja a következő HTTP-gyorsítótár-direktívák fejléceit, amelyek meghatározzák a gyorsítótár időtartamát és a gyorsítótár megosztását.

**Gyorsítótár-vezérlő:**
- A HTTP 1,1-ben jelent meg, hogy a webkiadók nagyobb mértékben szabályozzák a tartalmukat és a fejléc korlátozásait `Expires` .
- Felülbírálja a `Expires` fejlécet, ha mind a, mind `Cache-Control` a definiálva van.
- Ha az ügyféltől a CDN-POP-hoz tartozó HTTP-kérelemben használja, a `Cache-Control` rendszer alapértelmezés szerint figyelmen kívül hagyja az összes Azure CDN-profilt.
- Ha az ügyféltől a CDN-POP-hoz tartozó HTTP-válaszban használja:
     - A Verizon és **a Azure CDN standard** **/prémium Azure CDN** a Microsoft támogatási szolgálatának minden `Cache-Control` direktíva.
     - **Azure CDN a standard from Akamai** csak a következő `Cache-Control` irányelveket támogatja, a többi figyelmen kívül marad:
         - `max-age`: A gyorsítótár a megadott időtartamig tárolja a tartalmat. Például: `Cache-Control: max-age=5`. Ez az irányelv azt a maximális időtartamot határozza meg, ameddig a tartalom frissnek tekintendő.
         - `no-cache`: Gyorsítótárazza a tartalmat, de minden alkalommal érvényesíti a tartalmat, mielőtt a gyorsítótárból adná azt. Egyenértékű a következővel: `Cache-Control: max-age=0` .
         - `no-store`: Soha ne gyorsítótárazza a tartalmat. Távolítsa el a tartalmat, ha korábban már tárolva van.

**Lejár**
- A HTTP 1,0-ben bevezetett örökölt fejléc visszamenőleges kompatibilitás esetén támogatott.
- Dátum-alapú lejárati időt használ második pontossággal. 
- Hasonló a következőhöz: `Cache-Control: max-age` .
- Akkor használatos, ha `Cache-Control` nem létezik.

**Sorpragmákat**
   - A Azure CDN alapértelmezés szerint nem tartja tiszteletben.
   - A HTTP 1,0-ben bevezetett örökölt fejléc visszamenőleges kompatibilitás esetén támogatott.
   - Ügyfél-kérelem fejlécként használatos az alábbi direktívával: `no-cache` . Ez az irányelv arra utasítja a kiszolgálót, hogy az erőforrás új verzióját kézbesítse.
   - A `Pragma: no-cache` és a `Cache-Control: no-cache` kifejezés egyenértékű.

## <a name="validators"></a>Érvényesítőket

Ha a gyorsítótár elavult, a HTTP-gyorsítótár-érvényesítő használatával hasonlíthatja össze a fájl gyorsítótárazott verzióját a forrás-kiszolgálón lévő verzióval. **Azure CDN a Verizon standard/Premium** verziója alapértelmezés szerint mind a, mind a `ETag` `Last-Modified` validatorokat támogatja, míg a **Microsofttól Azure CDN standard** , a **Akamai pedig a standard szintű Azure CDN** csak `Last-Modified` alapértelmezés szerint támogatja.

**ETAG**
- Alapértelmezés szerint a Verizon által támogatott **standard/prémium szintű Azure CDN** `ETag` , míg a **Microsofttól Azure CDN standard** , a **Akamai pedig Azure CDN standard** .
- `ETag` egy olyan karakterláncot határoz meg, amely minden fájl és verzió esetében egyedi. Például: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- A HTTP 1,1-ben lett bevezetve, és a jelenleginél nagyobb `Last-Modified` . Akkor hasznos, ha az utolsó módosítás dátuma nehéz megállapítani.
- Az erős érvényesítést és a gyenge ellenőrzést is támogatja; Azure CDN azonban csak erős ellenőrzést támogat. Erős ellenőrzés esetén a két erőforrás-ábrázolásnak azonos bájt-bájtnak kell lennie. 
- A gyorsítótár egy olyan fájlt érvényesít, amely egy `ETag` vagy több érvényesítő utasítással elküld egy `If-None-Match` fejlécet `ETag` a kérelemben. Például: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Ha a kiszolgáló verziószáma megegyezik `ETag` a listán szereplő validatorokkal, a válaszában a 304 (nem módosított) állapotkódot küldi el. Ha a verzió eltérő, a kiszolgáló a 200-as (OK) állapotkódot és a frissített erőforrást válaszolja meg.

**Utolsó módosítás:**
- A csak a Verizon esetében a **standard/prémium szintű Azure CDN** `Last-Modified` akkor használatos, ha az `ETag` nem része a http-válasznak. 
- Meghatározza azt a dátumot és időpontot, ameddig a forráskiszolgáló meghatározta az erőforrás utolsó módosítását. Például: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- A gyorsítótár egy olyan fájlt érvényesít, amely a `Last-Modified` `If-Modified-Since` kérelemben megadott dátummal és idővel elküld egy fejlécet. A forráskiszolgáló összehasonlítja ezt a dátumot a `Last-Modified` legújabb erőforrás fejlécével. Ha az erőforrást a megadott idő óta nem módosították, a kiszolgáló a válaszban visszaadja a 304 (nem módosított) állapotkódot. Ha az erőforrás módosult, a kiszolgáló a 200 (OK) állapotkódot és a frissített erőforrást adja vissza.

## <a name="determining-which-files-can-be-cached"></a>A gyorsítótárazható fájlok meghatározása

Nem minden erőforrást lehet gyorsítótárazni. Az alábbi táblázat a HTTP-válasz típusán alapuló gyorsítótárazott erőforrásokat mutatja be. Azok a HTTP-válaszokkal szállított erőforrások, amelyek nem felelnek meg az összes feltételnek, nem gyorsítótárazható. A csak a **Verizon Azure CDN Premium** esetében a szabályok motor használatával testreszabhatja ezeket a feltételeket.

|                       | Azure CDN a Microsofttól          | Azure CDN a Verizontól | Azure CDN a Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **HTTP-állapotkódok** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **HTTP-metódusok**      | LETÖLTÉS, FEJ                         | GET                    | GET                          |
| **Fájlméret korlátai**  | 300 GB                            | 300 GB                 | – Általános webes kézbesítés optimalizálása: 1,8 GB<br />-Media Streaming-optimalizálások: 1,8 GB<br />-Nagyméretű fájlok optimalizálása: 150 GB |

Azure CDN ahhoz, hogy a **Microsoft** gyorsítótárazási szolgáltatás egy adott erőforráson működjön, a forrás-kiszolgálónak támogatnia kell a Head és a Get http-kérelmeket, és a Content-Length értékeknek meg kell egyezniük minden Head és HTTP-válaszhoz az adott objektumra vonatkozóan. HEAD kérelem esetén a forrás-kiszolgálónak támogatnia kell a HEAD kérést, és ugyanazokkal a fejlécekkel kell válaszolnia, mint ha a GET kérést kapta.

## <a name="default-caching-behavior"></a>Alapértelmezett gyorsítótárazási viselkedés

Az alábbi táblázat a Azure CDN termékek alapértelmezett gyorsítótárazási viselkedését, valamint azok optimalizálását ismerteti.

|    | Microsoft: általános webes kézbesítés | Verizon: általános webes kézbesítés | Verizon: DSA | Akamai: általános webes kézbesítés | Akamai: DSA | Akamai: nagyméretű fájl letöltése | Akamai: általános vagy VOD média streaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **A becsület forrása**       | Igen    | Igen   | Nem   | Igen    | Nem   | Igen   | Yes    |
| **CDN gyorsítótárának időtartama** | 2 nap |7 nap | Nincsenek | 7 nap | Nincsenek | 1 nap | 1 év |

**Becsület forrása**: Megadja, hogy a rendszer a támogatott gyorsítótár-direktíva fejléceket adja-e meg, ha azok szerepelnek a forráskiszolgáló http-válaszában.

**CDN-gyorsítótár időtartama**: azt határozza meg, hogy mennyi ideig legyen gyorsítótárazva az erőforrás a Azure CDN. Ha azonban a **becsület forrása** igen, és a forráskiszolgáló http-válasza tartalmazza a gyorsítótár-direktíva fejlécét `Expires` , vagy a `Cache-Control: max-age` Azure CDN a fejlécben megadott időtartam értéket használja. 

## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan szabhatja testre és felülbírálhatja a CDN alapértelmezett gyorsítótárazási viselkedését a gyorsítótárazási szabályokon keresztül, tekintse meg a következő témakört: a gyorsítótárazási [szabályok kezelése Azure CDN](cdn-caching-rules.md) 
- Ha meg szeretné tudni, hogyan használhatja a lekérdezési karakterláncokat a gyorsítótárazási viselkedés vezérlésére, tekintse meg a [vezérlési karakterláncokkal Azure CDN gyorsítótárazási viselkedés szabályozása](cdn-query-string.md)