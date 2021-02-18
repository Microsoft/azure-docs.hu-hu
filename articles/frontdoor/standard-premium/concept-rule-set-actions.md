---
title: Az Azure bejárati ajtó standard/prémium szabálykészlet műveleteinek beállítása
description: Ez a cikk felsorolja az Azure bejárati szabályainak készletével végrehajtható különböző műveleteket.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099194"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Azure bejárati ajtó standard/prémium szabálykészlet műveletei

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure-beli bejárati szabályok [készlete](concept-rule-set.md) olyan szabályokat tartalmaz, amelyek egyezési feltételekkel és műveletekkel rendelkeznek. Ez a cikk a szabálykészlet által használható műveletek részletes leírását tartalmazza. A művelet meghatározza azt a viselkedést, amelyet a rendszer a megfelelési feltétel (ek) azonosítására szolgáló kérelem típusára alkalmaz. Egy Azure-beli bejárati szabály készletében egy szabály legfeljebb öt műveletet tartalmazhat. A kiszolgálói változó minden művelet esetén támogatott.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A következő műveletek használhatók az Azure-beli előtérben beállított szabálykészlet számára.  

## <a name="cache-expiration"></a>Gyorsítótár lejárata

Ezzel a művelettel írhatja felül a végpont élettartam (TTL) értékét azon kérelmek esetében, amelyekre a szabályok megfelelnek a feltételeknek.

### <a name="required-fields"></a>Kötelező mezők

A gyorsítótár viselkedésének kiválasztásakor a következő leírás érvényes, és a szabály egyezése:

Gyorsítótár viselkedése |  Description              
---------------|----------------
Gyorsítótár megkerülése | A tartalom nincs gyorsítótárazva.
Felülbírálás | A rendszer felülírja a forrásból visszaadott TTL-értéket a műveletben megadott értékkel. Ez a viselkedés csak akkor lesz alkalmazva, ha a válasz gyorsítótárazható. A "no-cache", "Private", "No-Store" értékkel rendelkező gyorsítótár-vezérlési válasz fejlécében a művelet nem alkalmazható.
Ha hiányzik, állítsa be | Ha a forrástól nem érkezik TTL-érték, a szabály az ÉLETTARTAMot a műveletben megadott értékre állítja be. Ez a viselkedés csak akkor lesz alkalmazva, ha a válasz gyorsítótárazható. A "no-cache", "Private", "No-Store" értékkel rendelkező gyorsítótár-vezérlési válasz fejlécében a művelet nem alkalmazható.

### <a name="additional-fields"></a>További mezők

Nap | Óra | Percek | Másodperc
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Gyorsítótár-kulcs lekérdezési karakterlánca

Ezzel a művelettel módosíthatja a gyorsítótár-kulcsot a lekérdezési karakterláncok alapján.

### <a name="required-fields"></a>Kötelező mezők

A következő leírás a viselkedések kiválasztásakor és a szabály egyezése esetén érvényes:

Működés | Description
---------|------------
Belefoglalás | A paraméterekben megadott lekérdezési karakterláncok a gyorsítótár kulcsának generálásakor szerepelnek. 
Minden egyedi URL gyorsítótárazása | Mindegyik egyedi URL-címnek saját gyorsítótár-kulcsa van. 
Exclude | A paraméterekben megadott lekérdezési karakterláncok kimaradnak a gyorsítótár-kulcs generálása után.
Lekérdezési sztringek figyelmen kívül hagyása | A lekérdezési karakterláncok nem tekintendők a gyorsítótárbeli kulcs generálásakor. 

## <a name="modify-request-header"></a>Kérelem fejlécének módosítása

Ezzel a művelettel módosíthatja azokat a fejléceket, amelyek a forrásnak küldött kérésekben szerepelnek.

### <a name="required-fields"></a>Kötelező mezők

A következő leírás a műveletek kiválasztásakor és a szabály egyezése esetén érvényes:

Művelet | HTTP-fejléc neve | Érték
-------|------------------|------
Append (Hozzáfűzés) | A **fejlécben** megadott fejléc a megadott értékkel lesz hozzáadva a kéréshez. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő értékhez. | Sztring
Felülírás | A **fejlécben** megadott fejléc a megadott értékkel lesz hozzáadva a kéréshez. Ha a fejléc már létezik, a megadott érték felülírja a meglévő értéket. | Sztring
Törlés | Ha a szabályban megadott fejléc szerepel, a rendszer törli a fejlécet a kérelemből. | Sztring

## <a name="modify-response-header"></a>Válasz fejlécének módosítása

Ezzel a művelettel módosíthatja azokat a fejléceket, amelyek az ügyfeleknek küldött válaszokban szerepelnek.

### <a name="required-fields"></a>Kötelező mezők

A következő leírás a műveletek kiválasztásakor és a szabály egyezése esetén érvényes:

Művelet | HTTP-fejléc neve | Érték
-------|------------------|------
Append (Hozzáfűzés) | A **fejlécben** megadott fejléc a megadott **érték** használatával lesz hozzáadva a válaszhoz. Ha a fejléc már létezik, a rendszer hozzáfűzi az **értéket** a meglévő értékhez. | Sztring
Felülírás | A **fejlécben** megadott fejléc a megadott **érték** használatával lesz hozzáadva a válaszhoz. Ha a fejléc már létezik, az **érték** felülírja a meglévő értéket. | Sztring
Törlés | Ha a szabályban megadott fejléc jelen van, a rendszer törli a fejlécet a válaszból. | Sztring

## <a name="url-redirect"></a>URL-átirányítás

Ezzel a művelettel átirányíthatja az ügyfeleket egy új URL-címre. 

### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Átirányítás típusa | Válassza ki a kérelmezőnek visszatérni kívánt választ: talált (302), áthelyezett (301), ideiglenes átirányítás (307) és végleges átirányítás (308).
Átirányítási protokoll | Egyeztetési kérelem, HTTP, HTTPS.
Cél gazdagép | Válassza ki annak az állomásnak a nevét, amelyre át szeretné irányítani a kérést. Hagyja üresen a bejövő gazdagép megőrzését.
Cél elérési útja | Adja meg az átirányításhoz használandó elérési utat. Hagyja üresen a bejövő elérési út megőrzését.  
Lekérdezési sztring | Adja meg az átirányítás során használt lekérdezési karakterláncot. Hagyja üresen a bejövő lekérdezési karakterlánc megőrzését. 
Cél töredéke | Adja meg az átirányítás során használandó kódrészletet. Hagyja üresen a bejövő töredék megőrzése érdekében. 

## <a name="url-rewrite"></a>URL-átírás

Ezzel a művelettel átírhatja egy olyan kérelem elérési útját, amely útban van a forrásához.

### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Forrás mintája | Adja meg a forrás mintát a lecserélni kívánt URL-útvonalon. Jelenleg a forrás minta előtag-alapú egyezést használ. Az összes URL-cím eléréséhez használjon egy perjelet ( **/** ) a forrás minta értékének megfelelően.
Cél | Adja meg az újraíráshoz használandó célhely elérési útját. A cél elérési útja felülírja a forrás mintát.
Páratlan elérési út megőrzése | Ha az **Igen** értékre van állítva, a rendszer a forrás minta után hátralévő elérési utat hozzáfűzi az új cél elérési úthoz. 

## <a name="server-variable"></a>Kiszolgálói változó

### <a name="supported-variables"></a>Támogatott változók

| Változó neve | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | Az Azure-beli első ajtó széléhez való közvetlen kapcsolódás IP-címe. Ha az ügyfél egy HTTP-proxy vagy egy terheléselosztó használatával küldi el a kérelmet, a SocketIp értéke a proxy vagy a terheléselosztó IP-címe. |
| client_ip                  | Annak az ügyfélnek az IP-címe, amely az eredeti kérelmet elvégezte. Ha a kérelemben a fejléchez X-továbbítás történt, akkor az ügyfél IP-címe azonos. |
| client_port                | Az ügyfél IP-portja, amely a kérést elvégezte. |
| hostname                      | Az állomásnév az ügyféltől érkező kérésben. |
| geo_country                     | Azt jelzi, hogy a kérelmező országa/régiója az ország/régió kódja alapján van-e. |
| http_method                | Az URL-kérelem elvégzéséhez használt metódus. Például: GET vagy POST. |
| http_version               | A kérelem protokollja. Általában HTTP/1.0, HTTP/1.1 vagy HTTP/2.0. |
| query_string               | A kért URL-cím "?" értékét követő változó/érték párok listája. Példa: a kérelemben *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING érték *azonosító = 123&title = Fabrikam* |
| request_scheme             | A kérési séma: http vagy HTTPS. |
| request_uri                | A teljes eredeti kérelem URI-ja (argumentumokkal). Példa: a kérelemben *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI érték lesz a */Article.aspx? id = 123&title = Fabrikam* |
| server_port                | A kérelmet fogadó kiszolgáló portja. |
| ssl_protocol    | Egy létesített TLS-kapcsolat protokollja. |
| url_path                   | Annak a gazdagépnek az adott erőforrását azonosítja, amelyet a webes ügyfél szeretne elérni. Ez az argumentumok nélküli kérelem URI-ja. Példa: a kérelemben *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path érték lesz a */Article.aspx* |

### <a name="server-variable-format"></a>Kiszolgálói változó formátuma    

**Formátum:** {változó: eltolás}, {változó: eltolás: Length}, {változó}

### <a name="supported-server-variable-actions"></a>Támogatott kiszolgálói változó műveletek

* Kérelem fejléce
* Válasz fejléce
* Gyorsítótár-kulcs lekérdezési karakterlánca
* URL-átírás
* URL-átirányítás

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure bejárati stanard/prémium szabálykészlet beállításáról](concept-rule-set.md).
* További információ a [szabálykészlet egyeztetési feltételeiről](concept-rule-set-match-conditions.md).
