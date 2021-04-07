---
title: Gyakori kérdések – Azure DNS
description: Ebben a cikkben megismerheti a Azure DNSekkel kapcsolatos gyakori kérdéseket
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246382"
---
# <a name="azure-dns-faq"></a>Azure DNS GYIK

## <a name="about-azure-dns"></a>Tudnivalók Azure DNS

### <a name="what-is-azure-dns"></a>Mi az Azure DNS?

A tartománynévrendszer (DNS) lefordítja vagy feloldja a webhely vagy szolgáltatás nevét az IP-címére. Az Azure DNS a DNS-tartományokhoz használható üzemeltetési szolgáltatás. Microsoft Azure infrastruktúra használatával biztosítja a névfeloldást. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

A Azure DNS DNS-tartományait a DNS-névkiszolgálók Azure globális hálózata üzemelteti. Ez a rendszer a legközelebb elérhető DNS-kiszolgáló által megválaszolt összes DNS-lekérdezést kiválasztó hálózatokat használ. Azure DNS gyors teljesítményt és magas rendelkezésre állást biztosít a tartomány számára.

A Azure DNS Azure Resource Manageron alapul. Azure DNS a Resource Manager-funkciók előnyeit, például az Azure szerepköralapú hozzáférés-vezérlését, a naplókat és az erőforrás-zárolást. A tartományokat és rekordokat a Azure Portal, Azure PowerShell parancsmagok és a platformfüggetlen Azure CLI segítségével kezelheti. Az automatikus DNS-kezelést igénylő alkalmazások a REST API és az SDK-k segítségével integrálhatók a szolgáltatással.

### <a name="how-much-does-azure-dns-cost"></a>Mennyibe kerül a Azure DNS?

A Azure DNS számlázási modell a Azure DNSban üzemeltetett DNS-zónák számán alapul. Emellett a kapott DNS-lekérdezések számától is függ. A kedvezmények a használat alapján érhetők el.

További információkért tekintse meg a [Azure DNS díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Mi az Azure DNS SLA-ja?

Az Azure garantálja, hogy az érvényes DNS-kérelmek választ kapnak az idő legalább egy Azure DNS 100%-ában.

További információ: [Azure DNS SLA oldal](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Mi az a DNS-zóna? Ugyanaz, mint a DNS-tartomány? 

A tartomány a tartománynévrendszer egyedi neve. Például: contoso.com.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartomány contoso.com például több DNS-rekordot is tartalmazhat. A rekordok tartalmazhatnak mail.contoso.com a levelezési kiszolgálóhoz és a \. webhelyekhez tartozó www-contoso.com is. Ezek a rekordok a DNS-zóna contoso.com futnak.

A tartománynév *csak egy név*. A DNS-zónák olyan adatforrások, amelyek egy tartománynév DNS-rekordjait tartalmazzák. Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. Emellett DNS-névkiszolgálók számára is lehetővé teszi az internetről érkező DNS-lekérdezések megválaszolását.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>A Azure DNS használatához DNS-tartománynevet kell vásárolni? 

Nem feltétlenül.

Nem kell tartományt megvásárolnia egy DNS-zóna üzemeltetéséhez Azure DNSban. Bármikor létrehozhat egy DNS-zónát a tartománynév tulajdonjoga nélkül. A zóna DNS-lekérdezései csak akkor oldhatók fel, ha a zónához rendelt Azure DNS névkiszolgálók felé irányulnak.

Ha a DNS-zónát a globális DNS-hierarchiához szeretné kapcsolni, meg kell vásárolnia a tartománynevet. Ezután a világ bármely pontjáról érkező DNS-lekérdezések megkeresik a DNS-zónát, és választ adnak a DNS-rekordokra.

## <a name="azure-dns-features"></a>Azure DNS funkciók

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Van-e korlátozás, amikor alias-rekordokat használ egy tartománynévhez Traffic Manager?

Igen. Statikus nyilvános IP-címeket kell használnia az Azure Traffic Manager használatával. Konfigurálja a **külső végpont** célját statikus IP-cím használatával. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Támogatja Azure DNS a DNS-alapú forgalom útválasztását vagy végpontjának feladatátvételét?

A DNS-alapú forgalom útválasztását és végpontjának feladatátvételét Traffic Manager biztosítjuk. A Traffic Manager egy különálló Azure-szolgáltatás, amely a Azure DNS használatával használható. További információ: [Traffic Manager áttekintése](../traffic-manager/traffic-manager-overview.md).

Azure DNS csak a statikus DNS-tartományok üzemeltetését támogatja, ahol egy adott DNS-rekord minden DNS-lekérdezése mindig ugyanazt a DNS-választ kapja.

### <a name="does-azure-dns-support-domain-name-registration"></a>Támogatja a Azure DNS a tartománynév-regisztrációt?

Nem. Azure DNS jelenleg nem támogatja a tartománynevek megvásárlásának lehetőségét. A tartományok megvásárlásához harmadik féltől származó tartománynév-regisztrálót kell használnia. A regisztrátor általában egy kis éves díjat számol fel. Ezt követően a tartományok a DNS-rekordok kezeléséhez Azure DNSban tárolhatók. További információ: [tartomány delegálása Azure DNSra](dns-domain-delegation.md).

A tartománynevek vásárlására szolgáló funkció nyomon követhető az Azure-beli várakozó fájlok között. A visszajelzési webhely használatával [regisztrálja a szolgáltatás támogatását](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Támogatja a Azure DNS a DNSSEC-et?

Nem. Azure DNS jelenleg nem támogatja a tartománynévrendszer biztonsági bővítményeit (DNSSEC).

A DNSSEC funkció nyomon követhető a Azure DNS várakozó fájlok között. A visszajelzési webhely használatával [regisztrálja a szolgáltatás támogatását](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Támogatja a Azure DNS a zónaletöltések (AXFR/IXFR)?

Nem. Azure DNS jelenleg nem támogatja a zónaletöltések használatát. A DNS-zónák [Az Azure CLI használatával importálhatók Azure DNSba](dns-import-export.md). A DNS-rekordokat a [Azure DNS felügyeleti portál](dns-operations-recordsets-portal.md), a [REST API](/powershell/module/az.dns), az [SDK](dns-sdk.md), a [PowerShell-parancsmagok](dns-operations-recordsets.md)vagy a [CLI-eszköz](dns-operations-recordsets-cli.md)kezeli.

A zónaletöltés funkció nyomon követhető a Azure DNS várakozó fájlok között. A visszajelzési webhely használatával [regisztrálja a szolgáltatás támogatását](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS támogatja az URL-címek átirányítását?

Nem. Az URL-átirányítási szolgáltatások nem DNS-szolgáltatás. A DNS-szint helyett HTTP-szinten működnek. Egyes DNS-szolgáltatók az URL-átirányítási szolgáltatást a teljes ajánlat részeként csomagolják. A Azure DNS jelenleg nem támogatja ezt a szolgáltatást.

Az URL-átirányítás funkció nyomon követhető a Azure DNS várakozó fájlok között. A visszajelzési webhely használatával [regisztrálja a szolgáltatás támogatását](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Támogatja a Azure DNS a TXT-rekordhalmazok kiterjesztett ASCII-kódolását (8 bites)?

Igen. A Azure DNS támogatja a TXT-rekordhalmazok kiterjesztett ASCII-kódolási készletét. Azonban az Azure REST API-k, SDK-k, PowerShell és a parancssori felület legújabb verzióját kell használnia. Az 1., 2017-es vagy SDK 2,1-nál régebbi verziók nem támogatják a kiterjesztett ASCII-készletet. 

Például megadhat egy karakterláncot egy olyan TXT-rekord értékeként, amelynek a kiterjesztett ASCII-karaktere \ 128. Példa: "abcd\128efgh." A Azure DNS a karakter bájt értékét használja, amely 128, belső ábrázolásban. A DNS-feloldás időpontjában ez a bájt érték lesz visszaadva a válaszban. Azt is vegye figyelembe, hogy az "ABC" és a "\ 097 \ 098 \ 099" a megoldáshoz hasonlóan felcserélhető. 

A TXT-rekordokhoz tartozó [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) Zone file Master Format Escape-szabályokat követjük. Például `\` mostantól az RFC-ben mindent megmenekül. Ha `A\B` a TXT-rekord értékeként a értéket adta meg, akkor az csak a következőképpen jelenik meg és oldható fel `AB` . Ha szeretné, hogy a TXT-rekord `A\B` felbontásban legyen, újra kell menekülnie `\` . Példaként válassza a lehetőséget `A\\B` .

Ez a támogatás jelenleg nem érhető el a Azure Portalból létrehozott TXT-rekordokhoz.

## <a name="alias-records"></a>Aliasrekordok

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Milyen esetekben hasznos az alias-rekordok használata?

Tekintse meg a forgatókönyvek című szakaszt a [Azure DNS alias rekordok áttekintésében](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Milyen bejegyzéstípusok támogatottak az alias-rekordhalmazok esetében?

Az alias-rekordhalmazok a következő bejegyzéstípusok esetén támogatottak egy Azure DNS zónában:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Milyen erőforrások támogatottak az alias-rekordhalmazok céljaiként?

- **Mutasson egy nyilvános IP-erőforrásra egy DNS-A/AAAA-rekorddal.** Létrehozhat egy egy/AAAA-rekordot, és egy alias-rekordot állíthat be úgy, hogy egy nyilvános IP-erőforrásra mutasson.
- **Mutasson egy Traffic Manager-profilra egy DNS A/AAAA/CNAME-rekorddal.** Egy Traffic Manager-profil CNAME-értékét egy DNS CNAME-rekordból lehet irányítani. Ilyen például a contoso.trafficmanager.net. Egy olyan Traffic Manager-profilra is rámutathat, amely külső végpontokkal rendelkezik a DNS-zónában lévő egy vagy AAAA-rekorddal.
- **Mutasson egy Azure Content Delivery Network (CDN) végpontra**. Ez akkor hasznos, ha statikus webhelyeket hoz létre az Azure Storage és a Azure CDN használatával.
- **Mutasson egy másik DNS-rekordhalmazra ugyanazon a zónán belül.** Az alias-rekordok hivatkozhatnak ugyanarra a típusra más rekordhalmazokra is. Például rendelkezhet egy DNS CNAME-rekordhalmazzal, amely egy ugyanolyan típusú másik CNAME-rekordhalmaz aliasa. Ez a megoldás akkor hasznos, ha azt szeretné, hogy bizonyos rekordhalmazok aliasok és néhány nem alias legyen.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Létrehozhatok és frissíthetek alias-rekordokat a Azure Portal?

Igen. Alias-rekordokat hozhat létre vagy kezelhet a Azure Portal az Azure REST API-k, a PowerShell, a parancssori felület és az SDK-k használatával.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Az alias-rekordok segítenek meggyőződni arról, hogy a DNS-rekordhalmaz törölve lett a mögöttes nyilvános IP-cím törlésekor?

Igen. Ez a funkció az alias rekordok egyik alapvető funkciója. Segít elkerülni a lehetséges kimaradásokat az alkalmazás felhasználói számára.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Az alias-rekordok segítenek meggyőződni arról, hogy a DNS-rekordhalmaz a megfelelő IP-címre frissült, ha a mögöttes nyilvános IP-cím megváltozik?

Igen. Ez a funkció az alias rekordok egyik alapvető funkciója. Segít elkerülni az alkalmazás potenciális kimaradásait vagy biztonsági kockázatait.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Van olyan korlátozás, amikor alias-rekordhalmazt használ egy vagy AAAA rekordhoz, hogy Traffic Manager mutasson?

Igen. Ha egy Traffic Manager-profilra szeretne hivatkozni egy-vagy AAAA-rekorddal való aliasként, a Traffic Manager profilnak csak külső végpontokat kell használnia. Ha a Traffic Manager külső végpontokat hoz létre, adja meg a végpontok tényleges IP-címeit.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Van további díj az alias-rekordok használatához?

Az alias rekordok egy érvényes DNS-rekordhalmaz minősítése. Az alias-rekordok további számlázása nem történik meg.

## <a name="use-azure-dns"></a>Azure DNS használata

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Használhatok egy tartományt Azure DNS és egy másik DNS-szolgáltató használatával?

Igen. Azure DNS támogatja a közös üzemeltetésű tartományokat más DNS-szolgáltatásokkal.

A közös üzemeltetés beállításához módosítsa a tartományhoz tartozó NS-rekordokat úgy, hogy mindkét szolgáltató névkiszolgálók felé mutassanak. A névkiszolgáló (NS) rekordjai határozzák meg, hogy mely szolgáltatók kapják meg a tartomány DNS-lekérdezéseit. Ezeket az NS-rekordokat Azure DNS, a másik szolgáltatóban és a szülő zónában is módosíthatja. A fölérendelt zóna általában a tartománynév-regisztrálón keresztül van konfigurálva. További információ a DNS-delegálásról: [DNS-tartomány delegálása](dns-domain-delegation.md).

Győződjön meg arról is, hogy a tartomány DNS-rekordjai szinkronban vannak a DNS-szolgáltatók között. A Azure DNS jelenleg nem támogatja a DNS-zónák átvitelét. A DNS-rekordokat a [Azure DNS felügyeleti portál](dns-operations-recordsets-portal.md), a [REST API](/rest/api/dns/), az [SDK](dns-sdk.md), a [PowerShell-parancsmagok](dns-operations-recordsets.md)vagy a [CLI-eszköz](dns-operations-recordsets-cli.md)használatával kell szinkronizálni.

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Meg kell-e adni a tartományom mind a négy Azure DNS a névkiszolgálók delegálását?

Igen. Azure DNS négy névszervert rendel minden DNS-zónához. Ez a megoldás a hibák elkülönítésére és a rugalmasság növelésére szolgál. A Azure DNS SLA-ra való jogosultsághoz delegálja a tartományt mind a négy névkiszolgáló számára.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Mik a Azure DNS használati korlátai?

A Azure DNS használatakor a következő alapértelmezett korlátozások érvényesek.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Áthelyezhetek egy Azure DNS zónát az erőforráscsoportok vagy az előfizetések között?

Igen. A DNS-zónák az erőforráscsoportok vagy az előfizetések között helyezhetők át.

DNS-zónák áthelyezésekor nincs hatása a DNS-lekérdezésekre. A zónához hozzárendelt névkiszolgálók megmaradnak. A DNS-lekérdezések feldolgozása a szokásos módon történik.

A DNS-zónák áthelyezésével kapcsolatos további információkért és útmutatásért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Mennyi ideig tart a DNS-módosítások érvénybe léptetése?

Az új DNS-zónák és DNS-rekordok általában megjelennek a Azure DNS névkiszolgálók gyorsan. Az időzítés néhány másodperc.

A meglévő DNS-rekordok módosításai valamivel hosszabb időt vehetnek igénybe. Általában a Azure DNS névkiszolgálók között jelennek meg a 60 másodpercen belül. A DNS-ügyfelek és a DNS-rekurzív resolverek DNS-gyorsítótárazása a Azure DNSon kívül is befolyásolhatja az időzítést. A gyorsítótár időtartamának szabályozásához használja az egyes bejegyzéstípusok élettartam (TTL) tulajdonságát.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hogyan tudom védeni a DNS-zónákat a véletlen törléssel?

A Azure DNS Azure Resource Manager használatával felügyeli. A Azure Resource Manager által biztosított hozzáférés-vezérlési funkciók előnyei Azure DNS. Az Azure szerepköralapú hozzáférés-vezérléssel szabályozhatja, hogy mely felhasználók rendelkezzenek olvasási vagy írási hozzáféréssel a DNS-zónákhoz és a rekordhalmazokhoz. Az erőforrás-zárolások megakadályozzák a DNS-zónák és-rekordhalmazok véletlen módosítását vagy törlését.

További információ: [DNS-zónák és-rekordok elleni védelem](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hogyan az SPF-rekordok beállítása a Azure DNSban?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Az IPv6 protokollon keresztül Azure DNS névkiszolgálók feloldása? 

Igen. Azure DNS a névkiszolgálók kettős verem. A kettős verem azt jelenti, hogy IPv4-és IPv6-címekkel rendelkeznek. A DNS-zónához rendelt Azure DNS névkiszolgálók IPv6-címeinek megkereséséhez használjon olyan eszközt, mint az nslookup. Például: `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hogyan beállítani egy IDN-t a Azure DNSban?

A nemzetközi tartománynevek (IDNs) az egyes DNS-neveket az [punycode](https://en.wikipedia.org/wiki/Punycode)használatával kódolják. A DNS-lekérdezések ezekkel a punycode-kódolt nevekkel jönnek létre.

A IDNs Azure DNS-ben való konfigurálásához alakítsa át a zóna nevét vagy a punycode nevet. Azure DNS jelenleg nem támogatja a punycode-re vagy-re történő beépített átalakítást.

## <a name="next-steps"></a>Következő lépések

- [További információ a Azure DNSról](dns-overview.md).

- [További információ a Azure DNS privát tartományokhoz való használatáról](private-dns-overview.md).

- [További információ a DNS-zónákról és-rekordokról](dns-zones-records.md).

- [A Azure DNS első lépései](dns-getstarted-portal.md).
