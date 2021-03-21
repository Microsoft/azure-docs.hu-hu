---
title: A DNS-zónák és a rekordok áttekintése – Azure DNS
description: A DNS-zónák és-rekordok Microsoft Azure DNS-ben való üzemeltetésének támogatásának áttekintése.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 41eb15a38c97532951723f12d1ac74c90c838eb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968196"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS-zónák és -rekordok áttekintése

Ez az oldal ismerteti a tartományok, a DNS-zónák és a DNS-rekordok és-rekordhalmazok legfontosabb fogalmait, valamint azt, hogy ezek hogyan támogatottak Azure DNSban.

## <a name="domain-names"></a>Tartománynevek

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  Ezek alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”. A DNS-hierarchia tartományait globálisan osztják el, és a DNS-névkiszolgálók világszerte futnak.

A tartománynév-regisztráló egy olyan szervezet, amely lehetővé teszi egy tartománynév megvásárlását, például: `contoso.com` .  A tartománynév megvásárlásával jogosult a név alatti DNS-hierarchia szabályozására, például lehetővé teszi, hogy a nevet a `www.contoso.com` vállalati webhelyre irányítsa. A regisztrátor a saját nevében üzemeltetheti a tartományt, vagy alternatív névkiszolgálók megadását is lehetővé teszi.

A Azure DNS globálisan elosztott, magas rendelkezésre állású névkiszolgáló-infrastruktúrát biztosít, amely a tartomány üzemeltetésére használható. A tartományok Azure DNS-ban való üzemeltetésével a DNS-rekordokat a többi Azure-szolgáltatáshoz hasonló hitelesítő adatokkal, API-kkal, eszközökkel, számlázással és támogatással kezelheti.

A Azure DNS jelenleg nem támogatja a tartománynevek megvásárlását. Ha tartománynevet szeretne vásárolni, egy külső gyártótól származó tartománynév-regisztrálót kell használnia. A regisztrátor általában egy kis éves díjat számol fel. A tartományokat ezután a Azure DNS a DNS-rekordok kezeléséhez is üzemeltetheti. További információkért tekintse meg a [tartományok az Azure DNS-be való delegálását](dns-domain-delegation.md) ismertető cikket.

## <a name="dns-zones"></a>DNS-zónák

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Élettartam

Az élettartam vagy a TTL érték azt határozza meg, hogy az ügyfelek mennyi ideig gyorsítótárazzák az egyes rekordokat az Újralekérdezés előtt. A fenti példában az élettartam 3600 másodperc vagy 1 óra.

A Azure DNSban az élettartam meg van adva a rekordhalmazhoz, nem minden rekord esetében, így ugyanazt az értéket használja a rendszer az adott rekordhalmazban lévő összes rekordhoz.  1 és 2 147 483 647 másodperc közötti TTL-értéket is megadhat.

### <a name="wildcard-records"></a>Helyettesítő rekordok

Az Azure DNS [helyettesítő rekordok](https://en.wikipedia.org/wiki/Wildcard_DNS_record) használatát is támogatja. A helyettesítő rekordok a megfelelő névvel rendelkező lekérdezésekre válaszként lesznek visszaadva (kivéve, ha a nem helyettesítő rekordhalmaz nem tartalmaz szorosabb egyezést). A Azure DNS az NS és a SOA kivételével az összes bejegyzéstípushoz támogatja a helyettesítő rekordhalmazokat.

Helyettesítő bejegyzéstípus létrehozásához használja a (z) "" rekordazonosító-nevet \* . Azt is megteheti, hogy a bal szélső címkével "" nevű nevet is használhat, \* például: " \* . foo".

### <a name="caa-records"></a>CAA-rekordok

A CAA-rekordok lehetővé teszik, hogy a tartományi tulajdonosok megadják, hogy mely hitelesítésszolgáltatók (CA-k) jogosultak tanúsítványok kiadására a tartományuk számára. Ez lehetővé teszi, hogy a hitelesítésszolgáltatók bizonyos körülmények között elkerüljék a helytelenül kibocsátott tanúsítványokat. A CAA-rekordok három tulajdonsággal rendelkeznek:
* **Jelzők**: 0 és 255 közötti egész szám, amely az [RFC](https://tools.ietf.org/html/rfc6844#section-3) -ben speciális jelentéssel bíró kritikus jelző jelölésére szolgál
* **Címke**: egy ASCII-karakterlánc, amely a következők egyike lehet:
    * **probléma**: akkor használja ezt a lehetőséget, ha meg szeretné adni a tanúsítványokat kiállító hitelesítésszolgáltatókat (az összes típust).
    * **issuewild**: akkor használja ezt a lehetőséget, ha meg szeretné adni a tanúsítványokat kiállító hitelesítésszolgáltatót (csak helyettesítő tanúsítványok)
    * **iodef**: adjon meg egy e-mail-címet vagy állomásnevet, amely számára a hitelesítésszolgáltatók értesítéseket kaphatnak a jogosulatlan tanúsítványokkal kapcsolatos kérelmekről
* **Value (érték**): a kijelölt címke értéke

### <a name="cname-records"></a>CNAME rekordok

CNAME-rekordhalmazok nem létezhetnek egyidejűleg más, velük egyező nevű rekordhalmazokkal. Nem hozhat létre például egy "www" relatív névvel rendelkező CNAME-rekordot, valamint egy olyan rekordot, amelynek relatív neve "www".

Mivel a zóna csúcsa (név = " \@ ") mindig tartalmazza a zóna létrehozásakor létrehozott NS és SOA rekordhalmazokat, nem hozhat létre CNAME-rekordot a zóna csúcsán.

Ezek a korlátozások a DNS-szabványokból erednek, és nem korlátozzák Azure DNS.

### <a name="ns-records"></a>NS-rekordok

A zóna csúcsán (név: " \@ ") lévő NS-rekordhalmaz automatikusan létrejön minden DNS-zónával, és automatikusan törlődik a zóna törlésekor (nem lehet külön törölni).

Ez a rekordtípus a zónához rendelt Azure DNS névkiszolgálók nevét tartalmazza. Ehhez az NS-rekordhoz további névkiszolgálók hozzáadásával több DNS-szolgáltatóval rendelkező közös üzemeltetésű tartományokat is támogathat. Módosíthatja a rekord ÉLETTARTAMát és metaadatait is. Az előre megadott Azure DNS névkiszolgálók azonban nem távolíthatók el és nem módosíthatók. 

Ez csak a zóna csúcsán beállított NS-rekordra vonatkozik. A zónában lévő egyéb NS-rekordhalmazok (a gyermekobjektumok delegálásához) a korlátozás nélkül hozhatók létre, módosíthatók és törölhetők.

### <a name="soa-records"></a>SOA-rekordok

A rendszer automatikusan létrehoz egy SOA-rekordhalmazt minden zóna csúcsán (név = " \@ "), és automatikusan törlődik a zóna törlésekor.  A SOA-rekordok nem hozhatók létre és nem törölhetők külön.

A SOA rekord összes tulajdonságát módosíthatja, kivéve a "host" tulajdonságot, amely előre konfigurálva van, hogy a Azure DNS által biztosított elsődleges névkiszolgálói névre hivatkozzon.

A SOA rekordban lévő zóna sorozatszáma nem frissül automatikusan, ha a zóna rekordjaira módosul. Ha szükséges, a SOA rekord szerkesztésével manuálisan is frissíthető.

### <a name="spf-records"></a>SPF-rekordok

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-rekordok

Az [SRV-rekordokat](https://en.wikipedia.org/wiki/SRV_record) a különböző szolgáltatások használják a kiszolgálók helyeinek megadására. SRV-rekord megadásakor a Azure DNSban:

* A *szolgáltatást* és a *protokollt* a rekordtípus nevének részeként kell megadni, az aláhúzások előtaggal.  Például: ' \_ SIP. \_ tcp.name '.  A zóna csúcsán lévő rekord esetében nem kell megadnia a (z) " \@ " nevet a rekord nevében, egyszerűen használja a szolgáltatást és a protokollt, például " \_ SIP". \_ TCP ".
* A *prioritás*, a *súlyozás*, a *port* és a *cél* paraméterként van megadva a rekordban lévő rekordok mindegyikében.

### <a name="txt-records"></a>TXT-rekordok

A TXT-rekordok a tartománynevek tetszőleges szöveges karakterlánccá való leképezésére szolgálnak. Ezeket több alkalmazásban használják, különösen az e-mail-konfigurációval, például a [küldő házirend-keretrendszerével (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) és a [DomainKeys azonosított levelekkel (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

A DNS-szabványok lehetővé teszik, hogy egyetlen TXT-rekord több karakterláncot tartalmazzon, amelyek mindegyike legfeljebb 254 karakter hosszú lehet. Ha több karakterláncot használ, azokat az ügyfelek összefűzik, és egyetlen sztringként kezelik őket.

A Azure DNS REST API meghívásakor külön kell megadnia minden TXT-karakterláncot.  A Azure Portal, a PowerShell vagy a CLI felületek használatakor egyetlen karakterláncot kell megadnia rekordként, amely automatikusan 254 karakterre oszlik, ha szükséges.

A DNS-rekordok több karakterlánca nem tévesztendő össze a TXT-rekordhalmazban található több TXT-rekorddal.  A TXT rekordhalmaz több rekordot is tartalmazhat, *amelyek mindegyike* több karakterláncot is tartalmazhat.  Azure DNS támogatja az összes TXT-rekordhalmazban legfeljebb 1024 karakter hosszúságú teljes karakterláncot (az összes rekord összevont értékével együtt).

## <a name="tags-and-metadata"></a>Címkék és metaadatok

### <a name="tags"></a>Címkék

A címkék a név-érték párok listája, amelyeket a Azure Resource Manager használ az erőforrások címkézéséhez.  A Azure Resource Manager címkéket használ az Azure-számla szűrt nézeteinek engedélyezéséhez, és lehetővé teszi, hogy beállítson egy olyan szabályzatot, amelynél szükség van a címkékre. Címkékkel kapcsolatos további információ: [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).

Azure DNS támogatja Azure Resource Manager címkék használatát a DNS-zónák erőforrásain.  A nem támogatja a címkéket a DNS-rekordhalmazokon, bár a DNS-rekordhalmazok egy másik "metaadatok" is támogatottak.

### <a name="metadata"></a>Metaadatok

A címkék rekordhalmaz alternatívájaként a Azure DNS a metaadatok használatával támogatja a rekordok megjegyzéseit.  A címkékhez hasonlóan a metaadatok lehetővé teszik a név-érték párok hozzárendelését az egyes bejegyzéstípusokhoz.  Ez akkor lehet hasznos, ha például rögzíti az egyes bejegyzéstípusok célját.  A címkéktől eltérően a metaadatok nem használhatók az Azure-számla szűrt nézetének biztosításához, és nem adhatók meg Azure Resource Manager házirendben.

## <a name="etags"></a>Etagek

Tegyük fel, hogy két személy vagy két folyamat egyszerre próbálkozik a DNS-rekordok módosításával. Melyik nyer? És a nyertes is tudja, hogy felülírták-e valaki más által létrehozott módosításokat?

Azure DNS a Etagek használatával kezeli az egyazon erőforrás egyidejű módosításait. A etagek nem különböznek [Azure Resource Manager "címkéktől"](#tags). Minden DNS-erőforráshoz (zónához vagy rekordhoz) társítva van egy ETAG. Amikor egy erőforrást lekérik, a rendszer lekéri a ETAG is. Egy erőforrás frissítésekor dönthet úgy, hogy visszaadja a ETAG, így Azure DNS ellenőrizheti, hogy a ETAG a kiszolgálón egyezik-e. Mivel az erőforrás minden frissítése újra létrehozza a ETAG, egy ETAG eltérés azt jelzi, hogy egyidejű változás történt. A etagek új erőforrás létrehozásakor is használható, így biztosítva, hogy az erőforrás még nem létezik.

Alapértelmezés szerint Azure DNS PowerShell a Etagek használatával blokkolja a zónák és a rekordhalmazok egyidejű módosításait. A nem kötelező *– felülírás* kapcsolóval letilthatja a ETAG-ellenőrzéseket, ebben az esetben a rendszer felülírja az egyidejű módosításokat.

A Azure DNS REST API szintjén a Etagek HTTP-fejlécek használatával vannak megadva.  Viselkedésük a következő táblázatban szerepel:

| Fejléc | Működés |
| --- | --- |
| Nincsenek |Mindig sikeres (nincs ETAG-ellenőrzés) |
| If-Match \<etag> |A PUT csak akkor sikeres, ha az erőforrás létezik, és a ETAG megfelel |
| If-Match * |A PUT csak akkor sikeres, ha az erőforrás létezik |
| If-None-Match * |A csak akkor sikeres, ha az erőforrás nem létezik |


## <a name="limits"></a>Korlátok

Azure DNS használatakor a következő alapértelmezett korlátok érvényesek:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Következő lépések

* A Azure DNS használatának megkezdéséhez Ismerje meg, hogyan [hozhat létre DNS-zónát](./dns-getstarted-portal.md) , és hogyan [hozhat létre DNS-rekordokat](./dns-getstarted-portal.md).
* Meglévő DNS-zóna áttelepítéséhez tekintse meg [a DNS-zónafájl importálását és exportálását](dns-import-export.md)ismertető témakört.