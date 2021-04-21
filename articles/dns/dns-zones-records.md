---
title: DNS-zónák és -rekordok áttekintése – Azure DNS
description: A DNS-zónák és -rekordok DNS-ben való Microsoft Azure áttekintése.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816962"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS-zónák és -rekordok áttekintése

Ez a cikk a tartományok, DNS-zónák, DNS-rekordok és -rekordhalmazok alapvető fogalmait ismerteti. Megtudhatja, hogyan támogatott a Azure DNS.

## <a name="domain-names"></a>Tartománynevek

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  A legfelső szintű tartományok alatt második szintű tartományok, például "org.uk" vagy "co.jp". A DNS-hierarchia tartománya globálisan elosztott, és a világ különböző táján található DNS-névkiszolgálók üzemeltetik.

A tartománynév-regisztráló egy olyan szervezet, amely lehetővé teszi tartománynév( például ) `contoso.com` megvásárlását.  Tartománynév vásárlásával ön szabályozhatja az adott név alatt található DNS-hierarchiát, például lehetővé teszi, hogy a nevet a vállalati `www.contoso.com` webhelyre irányja. Előfordulhat, hogy a regisztráló a saját névkiszolgálóiban tárolja a tartományt az Ön nevében, vagy lehetővé teheti alternatív névkiszolgálók megadását.

Azure DNS globálisan elosztott és magas rendelkezésre állású névkiszolgálói infrastruktúrát biztosít, amely a tartománya gazdagépeként használható. Ha tartományait Azure DNS, a DNS-rekordokat ugyanazokkal a hitelesítő adatokkal, API-okkal, eszközökkel, számlázással és támogatással kezelheti, mint a többi Azure-szolgáltatást.

Azure DNS jelenleg nem támogatja a tartománynevek vásárlását. Ha tartománynevet szeretne vásárolni, külső tartománynév-regisztrálót kell használnia. A regisztráló általában kis éves díjat számít fel. A tartományok ezután a DNS-rekordok Azure DNS üzemeltetve. További információkért tekintse meg a [tartományok az Azure DNS-be való delegálását](dns-domain-delegation.md) ismertető cikket.

## <a name="dns-zones"></a>DNS-zónák

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Az élő adás ideje

Az itt megadott idő (TTL) azt határozza meg, hogy az ügyfelek mennyi ideig gyorsítótárazták az egyes rekordokat az ismételt lekérdezés előtt. A fenti példában az TTL 3600 másodperc vagy 1 óra.

Ebben Azure DNS rekordhalmazhoz, nem pedig minden rekordhoz meg van adva az TTL, így a rekordhalmaz összes rekordja ugyanazt az értéket használja.  Bármilyen TTL-értéket megadhat 1 és 2 147 483 647 másodperc között.

### <a name="wildcard-records"></a>Helyettesítő rekordok

Az Azure DNS [helyettesítő rekordok](https://en.wikipedia.org/wiki/Wildcard_DNS_record) használatát is támogatja. A helyettesítő rekordokat a lekérdezések válaszként kapják vissza egyező névvel, kivéve, ha nincs közelebb egyezés a nem helyettesítő karakteres rekordhalmazból. Azure DNS NS és SOA kivételével minden rekordtípus esetében támogatja a helyettesítő rekordhalmazokat.

Helyettesítő karakteres rekordhalmaz létrehozásához használja a " " \* rekordhalmaz-nevet. A bal oldali címkeként a ' nevet \* is használhatja, például: ' \* .foo'.

### <a name="caa-records"></a>CAA-rekordok

A CAA-rekordok lehetővé teszik a tartománytulajdonosok számára annak megadását, hogy mely hitelesítésszolgáltató (CA) jogosult tanúsítványok kiadására a tartományuk számára. Ez a rekord lehetővé teszi, hogy a HITELESÍTÉS-felelősök bizonyos körülmények között ne hibásan adnak ki tanúsítványokat. A CAA-rekordok három tulajdonsággal rendelkeznek:
* **Jelzők:** Ez a mező egy 0 és 255 közötti egész szám, amely az RFC szerint különleges jelentéssel bíró kritikus [jelzőt jelöl](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: ASCII-sztring, amely a következők egyike lehet:
    * **probléma:** ha olyan HITELESÍTÉS-eket szeretne megadni, amelyek számára engedélyezett a tanúsítványok kiállítása (minden típus)
    * **issuewild:** ha olyan hitelesítés-tanúsítványokat szeretne megadni, amelyek számára engedélyezett a tanúsítványok kiállítása (csak helyettesítő tanúsítványok esetén)
    * **iodef:** adjon meg egy e-mail-címet vagy állomásnevet, amelyre a hitelesítés címek értesítést kérhetnek jogosulatlan tanúsítványokkal kapcsolatban
* **Érték:** a kiválasztott címke értéke

### <a name="cname-records"></a>CNAME-rekordok

A CNAME-rekordhalmazok nem használhatjak egyidejűleg az azonos nevű rekordhalmazokat. Nem hozhat létre például egy CNAME-rekordhalmazt "www" relatív névvel és egy A rekordot "www" relatív névvel.

Mivel a zóna csúcsa (név = ' ') mindig tartalmazza az NS- és SOA-rekordhalmazokat a zóna létrehozása során, nem hozhat létre CNAME-rekordhalmazt a zóna \@ csúcsán.

Ezek a korlátozások a DNS-szabványokból erednek, és nem az Azure DNS korlátozásai.

### <a name="ns-records"></a>NS-rekordok

A zóna csúcsán (név: ' ) beállított NS-rekord automatikusan létrejön minden DNS-zónával, és a zóna törlésekor \@ automatikusan törlődik. Külön nem törölhető.

Ez a rekordhalmaz a zónához Azure DNS névkiszolgálók nevét tartalmazza. Több névkiszolgálót is hozzáadhat ehhez az NS-rekordhalmazhoz, hogy támogassa a tartományok több DNS-szolgáltatóval való közös áthelyezését. A rekordhalmaz TTL-ját és metaadatait is módosíthatja. A névkiszolgálók előre Azure DNS eltávolítása vagy módosítása azonban nem engedélyezett. 

Ez a korlátozás csak a zóna csúcspontjában beállított NS-rekordra vonatkozik. A zónában található egyéb NS-rekordhalmazok (a gyermekzónák delegálhatóak) korlátozás nélkül is létre lehet hozhatóak, módosíthatók és törölhetők.

### <a name="soa-records"></a>SOA-rekordok

Az SOA-rekordhalmazok automatikusan létrejönnek az egyes zónák csúcsán (név = ' ' ), és a zóna törlésekor \@ automatikusan törlődnek.  SOA-rekordok nem hozhatók létre és nem törölhetők külön.

Az SOA-rekord összes tulajdonságát módosíthatja a "host" tulajdonság kivételével. A rendszer előre konfigurálja ezt a tulajdonságot, hogy a kiszolgáló által megadott elsődleges névkiszolgáló Azure DNS.

Az SOA-rekordban található zóna sorozatszáma nem frissül automatikusan, amikor módosítják a zónában található rekordokat. Szükség esetén manuálisan is frissíthető az SOA-rekord szerkesztésével.

### <a name="spf-records"></a>SPF-rekordok

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-rekordok

[Az SRV rekordokat](https://en.wikipedia.org/wiki/SRV_record) különböző szolgáltatások használják a kiszolgálók helyének megadására. SRV rekord megadásakor a következő Azure DNS:

* A *szolgáltatást* és *a protokollt* a rekordhalmaz nevének részeként kell megadni, aláhúzásjelekkel előtaggal.  Például: ' \_ sip. \_ tcp.name".  A zóna csúcsponton található rekordhoz nem kell megadni a ' értéket a rekord nevében, egyszerűen használja a szolgáltatást és a \@ protokollt, például: ' \_ sip. \_ tcp'.
* A *prioritás,* *a súly,* *a port* és a cél a rekordhalmaz minden rekordjának paramétereiként van megadva. 

### <a name="txt-records"></a>TXT-rekordok

A TXT-rekordokkal a tartománynevek tetszőleges szöveges sztringekra vannak leképezve. Több alkalmazásban használják őket, különösen az e-mail-konfigurációval kapcsolatban, például a Küldő [házirend-keretrendszer (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) és a [DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)esetében.

A DNS-szabványok lehetővé teszik, hogy egyetlen TXT-rekord több sztringet tartalmaz, amelyek akár 254 karakter hosszúságúak is lehetnek. Ha több sztringet használ, az ügyfelek össze vannak őket össze concatálva, és egyetlen sztringként kezelik őket.

A Azure DNS REST API minden TXT-sztringet külön kell megadnia.  A Azure Portal, a PowerShell- vagy a parancssori felület használata esetén rekordonként egyetlen sztringet kell megadnia, amely szükség esetén automatikusan 254 karakterből álló szegmensekre van felosztva.

A DNS-rekordban található több sztringet nem szabad összekeverni a TXT-rekordhalmazok több TXT-rekordjaival.  A TXT típusú rekordhalmazok több rekordot is *tartalmazhatnak,* amelyek több sztringet is tartalmazhatnak.  Azure DNS txt-rekordhalmazok legfeljebb 1024 karakter hosszúságú sztringhosszt támogatnak (az összes rekordon együtt).

## <a name="tags-and-metadata"></a>Címkék és metaadatok

### <a name="tags"></a>Címkék

A címkék név-érték párok listája, amelyet a Azure Resource Manager használnak az erőforrások címkézése érdekében. Azure Resource Manager címkék használatával engedélyezi az Azure-számla szűrt nézeteit, és lehetővé teszi szabályzatok beállítását bizonyos címkékhez. Címkékkel kapcsolatos további információ: [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).

Azure DNS támogatja a Azure Resource Manager címkék használatát a DNS-zóna erőforrásain.  A DNS-rekordhalmazok címkéi nem támogatottak, bár a DNS-rekordhalmazok támogatják a metaadatokat is, az alábbiakban leírtak szerint.

### <a name="metadata"></a>Metaadatok

A rekordhalmazok címkéinek alternatívájaként a Azure DNS a "metadata" használatával jegyzetekkel.  A címkékhez hasonlóan a metaadatok lehetővé teszik név-érték párok társítását az egyes rekordhalmazokkal.  Ez a funkció hasznos lehet például az egyes rekordhalmazok céljának rögzítéséhez.  A címkéktől eltérően a metaadatok nem használhatók az Azure-számla szűrt nézetének megtekintésére, és nem Azure Resource Manager szabályzatban.

## <a name="etags"></a>Etagek

Tegyük fel, hogy két vagy két folyamat egyszerre próbál módosítani egy DNS-rekordot. Melyik nyer? És tudja a győztes, hogy felülírta valaki más által létrehozott módosításokat?

Azure DNS Etagek segítségével kezeli biztonságosan ugyanazon erőforrás egyidejű módosításait. Az Etagek elkülönülnek [a Azure Resource Manager címkéktől.](#tags) Minden DNS-erőforráshoz (zónához vagy rekordhalmazhoz) tartozik egy Etag. Az erőforrás lekérésekor az Etag is le lesz kérve. Erőforrás frissítésekekor dönthet úgy, hogy visszaküldi az Etaget, Azure DNS ellenőrizni tudja, hogy a kiszolgálón egyezik-e az Etag. Mivel az erőforrás minden egyes frissítése az Etag újragenerálását jelenti, az Etag eltérése azt jelzi, hogy egyidejű módosítás történt. Az etagek új erőforrás létrehozásakor is használhatók annak biztosítására, hogy az erőforrás még ne létezik.

Alapértelmezés szerint a Azure DNS PowerShell Etagek használatával blokkolja a zónák és rekordhalmazok egyidejű módosításait. Az opcionális *-Overwrite* kapcsolóval letilthatók az Etag-ellenőrzések, ebben az esetben a rendszer felülírja az egyidejű módosításokat.

A címkék szintjén az Etagek Azure DNS REST API HTTP-fejlécek használatával vannak megadva.  A viselkedésük az alábbi táblázatban látható:

| Fejléc | Működés |
| --- | --- |
| Nincsenek |PUT always succeeds (nincs Etag-ellenőrzés) |
| If-match (Ha egyezés) \<etag> |A PUT csak akkor sikeres, ha az erőforrás létezik, és az Etag megegyezik |
| If-match * |A PUT csak akkor sikeres, ha az erőforrás létezik |
| If-none-match * |A PUT csak akkor sikeres, ha az erőforrás nem létezik |


## <a name="limits"></a>Korlátok

A következő alapértelmezett korlátok érvényesek a Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Következő lépések

* A szolgáltatás használatának Azure DNS dns-zónák és [DNS-rekordok](./dns-getstarted-portal.md) létrehozása című [cikkből tudható meg.](./dns-getstarted-portal.md)
* Meglévő DNS-zóna áttelepítése: DNS-zónafájl importálása és [exportálása.](dns-import-export.md)