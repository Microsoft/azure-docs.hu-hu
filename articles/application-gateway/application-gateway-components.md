---
title: Az Application Gateway összetevői
description: Ez a cikk az Application Gateway különböző összetevőivel kapcsolatos információkat tartalmaz
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88723316"
---
# <a name="application-gateway-components"></a>Az Application Gateway összetevői

 Az Application Gateway az ügyfelek számára egyetlen kapcsolódási pontként szolgál. A bejövő alkalmazások forgalmát több háttér-készlet között osztja el, többek között az Azure-beli virtuális gépeket, a virtuálisgép-méretezési csoportokat, a Azure App Servicet és a helyszíni/külső kiszolgálókat. A forgalom terjesztéséhez az Application Gateway számos, a jelen cikkben ismertetett összetevőt használ.

![Az Application gatewayben használt összetevők](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Előtérbeli IP-címek

Az előtérbeli IP-cím az Application gatewayhöz tartozó IP-cím. Az Application Gateway egy nyilvános IP-címmel, egy magánhálózati IP-címmel vagy mindkettővel is konfigurálható. Az Application Gateway egy nyilvános vagy egy magánhálózati IP-címet támogat. A virtuális hálózatnak és a nyilvános IP-címnek ugyanabban a helyen kell lennie, mint az Application Gateway-nek. A létrehozást követően a rendszer egy előtérbeli IP-címet társít egy figyelőhöz.

### <a name="static-versus-dynamic-public-ip-address"></a>Statikus vagy dinamikus nyilvános IP-cím

Az Azure Application Gateway v2 SKU konfigurálható úgy, hogy mindkét statikus belső IP-címet és statikus nyilvános IP-címet, vagy csak a statikus nyilvános IP-címet támogassa. Nem konfigurálható úgy, hogy csak a statikus belső IP-címet támogassa.

A v1 SKU beállítható úgy, hogy támogassa a statikus vagy dinamikus belső IP-címet és a dinamikus nyilvános IP-címet. A Application Gateway dinamikus IP-címe nem változik egy futó átjárón. Csak az átjáró leállításakor vagy indításakor lehet megváltoztatni. Nem változtatja meg a rendszerhibákat, a frissítéseket, az Azure-gazdagépek frissítéseit stb. 

Az Application gatewayhez társított DNS-név nem változik az átjáró életciklusa során. Ennek eredményeképpen CNAME aliast kell használnia, és az Application Gateway DNS-címeként kell mutatnia.

## <a name="listeners"></a>Figyelők

A figyelő egy logikai entitás, amely ellenőrzi a bejövő kapcsolatok kéréseit. A figyelő fogad egy kérelmet, ha a kérelemhez társított protokoll, port, állomásnév és IP-cím megegyezik a figyelő konfigurációjával társított elemekkel.

Az Application Gateway használata előtt legalább egy figyelőt hozzá kell adnia. Az Application gatewayhez több figyelő is tartozhat, és ezekhez a protokollhoz is használható.

Miután egy figyelő észleli az ügyfelektől érkező beérkező kéréseket, az Application Gateway ezeket a kéréseket a szabályban konfigurált háttér-készlet tagjaira irányítja.

A figyelők a következő portokat és protokollokat támogatják.

### <a name="ports"></a>Portok

Egy port, ahol a figyelő figyeli az ügyfél kérelmét. Az 1 és 65502 közötti portok a v2 SKU-hoz készült v1 SKU és 1 – 65199 között állíthatók be.

### <a name="protocols"></a>Protokollok

A Application Gateway négy protokollt támogat: HTTP, HTTPS, HTTP/2 és WebSocket:
>[!NOTE]
>A HTTP/2 protokoll támogatása csak az Application Gateway-figyelőkhöz csatlakozó ügyfelek számára érhető el. A háttér-kiszolgálói készletekkel folytatott kommunikáció mindig HTTP/1.1-en keresztül történik. Alapértelmezés szerint a HTTP/2 támogatás le van tiltva. Dönthet úgy is, hogy engedélyezi.

- A figyelő konfigurációjában adjon meg a HTTP és a HTTPS protokoll közötti értéket.
- A [WebSockets és a http/2 protokollok](features.md#websocket-and-http2-traffic) támogatása natív módon történik, és a [WebSocket-támogatás](application-gateway-websocket.md) alapértelmezés szerint engedélyezve van. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. Websocketek használata HTTP-és HTTPS-figyelővel.

HTTPS-figyelő használata a TLS-lezáráshoz. Egy HTTPS-figyelő kiszervezi a titkosítási és a visszafejtési munkát az Application Gateway felé, így a webkiszolgálók nem terhelik a terhelést.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Application Gateway lehetővé teszi, hogy egyéni hibaüzeneteket hozzon létre az alapértelmezett hibaüzenetek megjelenítése helyett. Az egyéni hibalapokon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Application Gateway egy egyéni hibaüzenetet jelenít meg, ha egy kérelem nem tudja elérni a hátteret.

További információ: [az Application Gateway egyéni hibáinak lapja](custom-error.md).

### <a name="types-of-listeners"></a>Figyelők típusai

Kétféle figyelő létezik:

- **Alapszintű**. Ez a típusú figyelő egyetlen tartományi helyet figyel, ahol egyetlen DNS-hozzárendelés van az Application Gateway IP-címéhez. Ez a figyelő-konfiguráció akkor szükséges, ha egyetlen helyet üzemeltet az Application Gateway mögött.

- **Több hely**. Ezt a figyelő konfigurációt akkor kell megadni, ha az adott alkalmazás-átjárón egynél több webalkalmazáshoz tartozó állomásnév vagy tartománynév alapján szeretné konfigurálni az útválasztást. Így hatékonyabb topológiát konfigurálhat telepítéseihez, mivel akár 100-nál is több webhelyet adhat hozzá egyetlen alkalmazásátjáróhoz. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Például három tartomány (contoso.com, fabrikam.com és adatum.com) mutat az alkalmazásátjáró IP címére. Hozzon létre három [többhelyes figyelőt](multiple-site-overview.md) , és konfigurálja az egyes figyelőket a megfelelő port és protokoll beállításhoz. 

    A helyettesítő karakterrel ellátott gazdaneveket többhelyes figyelőben és figyelőként legfeljebb 5 gazdanévben is meghatározhatja. További információ: [helyettesítő karakterek nevei a figyelőben (előzetes verzió)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

    A többhelyes figyelő konfigurálásával kapcsolatos további információkért lásd: [többhelyes üzemeltetés Application Gateway a Azure Portal használatával](create-multiple-sites-portal.md).

A figyelő létrehozása után társítsa azt egy kérelem-útválasztási szabállyal. Ez a szabály határozza meg, hogy a figyelőre érkező kérés hogyan legyen átirányítva a háttérbe. A kérelem útválasztási szabálya tartalmazza az átirányítani kívánt háttér-készletet is, valamint azt a HTTP-beállítást, amelyben a háttér-port, a protokoll stb. szerepel.

## <a name="request-routing-rules"></a>Kérelemirányítási szabályok

A kérelem-útválasztási szabály az Application Gateway egyik fő összetevője, mert meghatározza, hogyan irányíthatja át a forgalmat a figyelőn. A szabály köti a figyelőt, a háttér-kiszolgáló készletet és a háttérbeli HTTP-beállításokat.

Amikor egy figyelő fogad egy kérelmet, a kérések útválasztási szabálya továbbítja a kérést a háttérnek, vagy máshová irányítja át. Ha a kérést továbbítják a háttérbe, a kérelem útválasztási szabálya határozza meg, hogy melyik háttér-kiszolgáló készletet szeretné továbbítani. A kérelem útválasztási szabálya azt is meghatározza, hogy a kérésben szereplő fejléceket át kell-e írni. Egy figyelő egyetlen szabályhoz is csatolható.

A kérések útválasztási szabályainak két típusa létezik:

- **Alapszintű**. A társított figyelőn (például blog.contoso.com/*) lévő összes kérelem a társított backend-készletbe van továbbítva a kapcsolódó HTTP-beállítás használatával.

- **Elérésiút-alapú**. Ez az útválasztási szabály lehetővé teszi, hogy a kérelemben szereplő URL-cím alapján a társított figyelőn keresztül átirányítsa a kéréseket egy adott háttér-készletre. Ha egy kérelem URL-címének elérési útja megegyezik egy elérésiút-alapú szabályban szereplő elérésiút-mintával, a szabály a kérést irányítja át. Az elérésiút-mintát csak az URL-címre alkalmazza, nem pedig a lekérdezési paraméterekre. Ha a figyelőre vonatkozó kérelem URL-címének elérési útja nem egyezik az elérésiút-alapú szabályokkal, a kérést az alapértelmezett háttér-készletre és a HTTP-beállításokra irányítja át.

További információ: [URL-alapú útválasztás](url-route-overview.md).

### <a name="redirection-support"></a>Átirányítási támogatás

A kérések útválasztási szabálya lehetővé teszi az Application Gateway forgalmának átirányítását is. Ez egy általános átirányítási mechanizmus, amellyel bármely, a szabályok segítségével meghatározott portról átirányíthatja a t.

Kiválaszthatja, hogy az átirányítási cél egy másik figyelő legyen (amely lehetővé teszi az automatikus HTTP-t a HTTPS-átirányításhoz) vagy egy külső helyet. Dönthet úgy is, hogy az átirányítás átmenetileg vagy állandó, vagy az átirányított URL-címhez hozzáfűzi az URI elérési utat és a lekérdezési karakterláncot.

További információ: [forgalom átirányítása az Application Gateway](redirect-overview.md)-ben.

### <a name="rewrite-http-headers-and-url"></a>HTTP-fejlécek és URL átírása

Az Újraírási szabályok használatával a HTTP (S) kérések és válaszok fejléceit, valamint az URL-cím és a lekérdezési karakterlánc paramétereit is hozzáadhatja, eltávolíthatja vagy frissítheti, mivel a kérelmek és válaszok csomagjai az Application Gateway segítségével az ügyfél és a háttérbeli készletek között mozognak.

A fejlécek és URL-paraméterek statikus értékekre vagy más fejlécekre és kiszolgálói változókra állíthatók be. Ez segíti a fontos használati eseteket, például az ügyfél IP-címeinek kinyerését, a háttér bizalmas adatainak eltávolítását, a nagyobb biztonság hozzáadását stb.

További információ: HTTP- [fejlécek és URL-cím átírása az Application Gateway-](rewrite-http-headers-url.md)ben.

## <a name="http-settings"></a>HTTP-beállítások

Az Application Gateway átirányítja a forgalmat a háttér-kiszolgálókra (a HTTP-beállításokat tartalmazó kérelem-útválasztási szabályban meghatározottak szerint) a portszám, a protokoll és az ebben az összetevőben részletezett egyéb beállítások használatával.

A HTTP-beállításokban használt port és protokoll határozza meg, hogy az Application Gateway és a háttérrendszer-kiszolgálók közötti forgalom titkosítva van-e (végpontok közötti TLS-t biztosítva) vagy titkosítatlan.

Ez az összetevő a következőket is használja:

- Állapítsa meg, hogy a felhasználói munkamenetet ugyanazon a kiszolgálón kell-e tárolni a [cookie-alapú munkamenet-affinitás](features.md#session-affinity)használatával.

- A háttérbeli készlet tagjainak biztonságos eltávolítása a [kapcsolatok kiürítésével](features.md#connection-draining).

- Rendeljen egyéni mintavételt a háttér állapotának figyeléséhez, állítsa be a kérés időkorlátját, felülbírálja az állomásnév és elérési út értékét a kérelemben, és egy kattintással könnyedén megadhatja a App Service háttér beállításait.

## <a name="backend-pools"></a>Háttér-készletek

A háttér-készlet átirányítja a kérést a háttér-kiszolgálókra. A háttér-készletek a következőket tartalmazhatják:

- Hálózati adapterek (NIC-k)
- Virtuálisgép-méretezési csoportok
- Nyilvános IP-címek
- Belső IP-címek
- FQDN
- Több-bérlős háttérrendszer (például App Service)

Application Gateway háttérbeli készlet tagjai nem kapcsolódnak rendelkezésre állási csoportokhoz. Az Application Gateway képes kommunikálni a virtuális hálózatán kívüli példányokkal. Ennek eredményeképpen a háttér-készletek tagjai többek között fürtök, adatközpontok vagy az Azure-on kívül is lehetnek, feltéve, hogy IP-kapcsolat van.

Ha belső IP-címeket használ a háttérbeli készlet tagjaiként, [virtuális hálózati](../virtual-network/virtual-network-peering-overview.md) társítást vagy [VPN-átjárót](../vpn-gateway/vpn-gateway-about-vpngateways.md)kell használnia. A virtuális hálózat társítása támogatott és előnyös a más virtuális hálózatok terheléselosztási forgalmához.

Az Application Gateway képes kommunikálni a helyszíni kiszolgálókkal is, amikor az Azure ExpressRoute vagy VPN-alagutak csatlakoznak, ha a forgalom engedélyezett.

Különböző háttér-készleteket hozhat létre különböző típusú kérelmekhez. Hozzon létre például egy háttér-készletet az általános kérelmekhez, majd egy másik háttér-készletet az alkalmazáshoz tartozó Service-szolgáltatásokhoz intézett kérésekhez.

## <a name="health-probes"></a>Állapotminták

Alapértelmezés szerint az Application Gateway a háttér-készlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja a nem megfelelő állapotú fájlokat. Ezután figyeli a nem megfelelő állapotú példányokat, és visszaadja azokat az egészséges háttérrendszer-készlethez, amikor elérhetővé válnak, és reagálnak az állapotra.

Amellett, hogy az alapértelmezett állapot mintavételi figyelését is használja, testre is szabhatja az állapot-mintavételt az alkalmazás követelményeinek megfelelően. Az egyéni mintavételek részletesebb szabályozást tesznek lehetővé az állapot figyelése során. Egyéni mintavétel használatakor beállíthatja az egyéni állomásnév, az URL-cím és a mintavételi időköz értékét, valamint azt, hogy hány sikertelen választ fogadjon el a rendszer, mielőtt a háttérrendszer-példányt nem Kifogástalan állapotba állítja, egyéni állapotkódot és a válasz törzsének megfelelőt, stb. Javasoljuk, hogy az egyes háttérrendszer-készletek állapotának figyeléséhez egyéni mintavételt állítson be.

További információ: [az Application Gateway állapotának figyelése](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Következő lépések

Application Gateway létrehozása:

* [A Azure Portal](quick-create-portal.md)
* [Azure PowerShell használatával](quick-create-powershell.md)
* [Az Azure CLI használatával](quick-create-cli.md)
