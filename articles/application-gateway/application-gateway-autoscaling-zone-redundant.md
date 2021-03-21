---
title: Automatikusan skálázó és zónaredundáns Application Gateway v2
description: Ez a cikk bemutatja az Azure Application Standard_v2 és WAF_v2 SKU-t, amely magában foglalja az automatikus skálázást és a zóna-redundáns funkciókat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fad6e27c4ee7e8c10237cb3face5cfab9329b2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059721"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatikusan skálázó és zónaredundáns Application Gateway v2 

A Application Gateway Standard_v2 SKU-ban érhető el. A webalkalmazási tűzfal (WAF) WAF_v2 SKU-ban érhető el. A v2 SKU teljesítmény-fejlesztéseket biztosít, és támogatja a kritikus fontosságú új funkciókat, például az automatikus skálázást, a zónák redundanciát és a statikus VIP-címek támogatását. A standard és a WAF SKU meglévő szolgáltatásai továbbra is támogatottak lesznek az új v2 SKU-ban, és néhány kivétel szerepel az [összehasonlítás](#differences-from-v1-sku) szakaszban.

Az új v2 SKU a következő fejlesztéseket tartalmazza:

- Automatikus **skálázás**: az automatikus SKÁLÁZÁSi SKU-ban lévő Application Gateway vagy WAF üzembe helyezése horizontálisan vagy a forgalom terhelési mintáinak változása alapján végezhető el. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ez az SKU valódi rugalmasságot biztosít. A Standard_v2 és WAF_v2 SKU-ban a Application Gateway a rögzített kapacitásban (automatikus skálázás letiltva) és az automatikus skálázást engedélyező módban is működhet. A rögzített kapacitás mód olyan helyzetekben hasznos, amelyek konzisztens és kiszámítható számítási feladatokkal rendelkeznek. Az automatikus skálázási mód hasznos olyan alkalmazásokban, amelyek az alkalmazás forgalmában tapasztalnak eltérést.
- **Zóna redundancia**: egy Application Gateway vagy WAF üzemelő példány több Availability Zonesra is terjedhet, így nem kell külön Application Gateway példányt kiépíteni az egyes zónákban Traffic Manager. Kiválaszthat egyetlen zónát vagy több olyan zónát, ahol Application Gateway példányok vannak telepítve, ami rugalmasabb lehet a zóna meghibásodása miatt. Az alkalmazások háttér-készlete hasonló módon terjeszthető a rendelkezésre állási zónák között.

  A zóna redundancia csak akkor érhető el, ha az Azure-zónák elérhetők. Más régiókban a többi funkció is támogatott. További információ: [régiók és Availability Zones az Azure-ban](../availability-zones/az-overview.md)
- **Statikus VIP**: a Application Gateway v2 SKU kizárólag a statikus VIP-típust támogatja. Ez biztosítja, hogy az Application gatewayhez társított virtuális IP-cím ne változzon az üzemelő példány életciklusa alatt, még újraindítás után is.  Nem létezik statikus virtuális IP-cím a v1-ben, ezért az Application Gateway URL-címe helyett a tartománynév-útválasztáshoz használt IP-címet kell használnia az Application Gateway használatával App Services.
- **Fejléc újraírása**: a Application Gateway lehetővé teszi a HTTP-kérések és a válasz-fejlécek hozzáadását, eltávolítását vagy frissítését v2 SKU-val. További információt a HTTP- [fejlécek újraírása a Application Gatewaysal](rewrite-http-headers.md) című témakörben talál.
- **Key Vault integráció**: a Application Gateway v2 támogatja az Key Vault-integrációt a https-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványokhoz. További információért lásd [a Key Vault tanúsítványokkal való TLS-megszakításról szóló részt](key-vault-certs.md).
- **Azure Kubernetes-szolgáltatás bejövő adatkezelője**: a Application Gateway v2 beáramló vezérlő lehetővé teszi, hogy az azure-Application Gateway használható legyen a bejövő forgalomként egy Azure Kubernetes szolgáltatás (ak) számára, az úgynevezett AK-fürt. További információ: [Mi az a Application Gateway beáramló vezérlő?](ingress-controller-overview.md).
- **Teljesítménybeli fejlesztések**: a v2 SKU a standard/WAF SKU-hoz képest akár 5x-ös nagyobb TLS-kiszervezési teljesítményt nyújt.
- **Gyorsabb üzembe helyezés és frissítés ideje** A v2 SKU gyorsabb üzembe helyezést és frissítési időt biztosít a standard/WAF SKU-hoz képest. Ez magában foglalja a WAF konfigurációs változásait is.

![Diagram az automatikus skálázási zónához.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Támogatott régiók

A Standard_v2 és WAF_v2 SKU a következő régiókban érhető el: az USA északi középső régiója, az USA déli középső régiója, az USA nyugati régiója, az USA nyugati régiója, USA keleti régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Kelet-Ázsia Kelet-Ausztrália Egyesült Királyság nyugati régiója Közép-Németország , Egyesült Királyság déli régiója, Közép-India, Nyugat-India, Dél-India.

## <a name="pricing"></a>Díjszabás

A v2 SKU-val a díjszabási modellt a használat vezérli, és a rendszer már nem csatolja a példányszámot vagy a méretet. A v2 SKU díjszabása két összetevőből áll:

- **Rögzített ár** – ez egy Standard_v2 vagy WAF_v2 átjáró kiépítésének óránkénti (vagy részleges órányi) díja. Vegye figyelembe, hogy a 0 további minimális példány továbbra is biztosítja a szolgáltatás magas rendelkezésre állását, amely mindig rögzített díjszabással van ellátva.
- **Kapacitási egység díja** – ez egy fogyasztási alapú költség, amelyet a rögzített költség mellett számítunk fel. A kapacitási egység díját óránként vagy részben is számításba kell venni. A kapacitás egysége – számítási egység, állandó kapcsolatok és átviteli sebesség – három dimenzióval rendelkezik. A számítási egység a felhasznált processzorkapacitás mérőszáma. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-újraírási számítások és a WAF-szabályok feldolgozása. Az állandó kapcsolat az Application Gateway számára a megadott számlázási időszakban létesített TCP-kapcsolatok mértéke. Az átviteli sebesség egy adott számlázási időszakban a rendszer által feldolgozott átlagos megabit/mp.  A számlázás a fenntartott példányok számának megfelelő kapacitás egység szintjén történik.

Minden kapacitási egység legfeljebb a következőkből áll: 1 számítási egység, 2500 állandó kapcsolat és 2,22 – Mbps átviteli sebesség.

További információ: a [díjszabás ismertetése](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>A Application Gateway és a WAF v2 méretezése

A Application Gateway és a WAF két módban is konfigurálható:

- Automatikus **skálázás** – ha engedélyezve van az automatikus skálázás, a Application Gateway és a WAF v2 SKU-ra vertikális fel-és leskálázás az alkalmazás-forgalmi követelmények alapján. Ez a mód jobb rugalmasságot biztosít az alkalmazás számára, és nem kell kitalálnia az Application Gateway-méretet vagy a példányszámot. Ez a mód lehetővé teszi a költségmegtakarítást úgy, hogy a várt maximális forgalmi terheléshez nem szükséges, hogy az átjáró kiosztott kapacitással fusson. Meg kell adnia a példányok minimális és opcionális maximális példányszámát. A minimális kapacitás biztosítja, hogy a Application Gateway és a WAF v2 ne érje el a megadott minimális példányszámot, még a forgalom hiányában is. Az egyes példányok nagyjából 10 további fenntartott kapacitási egységnek felelnek meg. A nulla nem jelent fenntartott kapacitást, és kizárólag az automatikus skálázást jelenti. Megadhatja a példányok maximális számát is, ami garantálja, hogy a Application Gateway a megadott számú példányon túl nem méretezhető. Az átjáró által kiszolgált forgalom mennyisége csak a számlázás után történik meg. A példányok száma 0 és 125 között lehet. A példányok maximális számának alapértelmezett értéke 20, ha nincs megadva.
- **Manuális** – választhatja azt a manuális módot is, amelyben az átjáró nem rendelkezik Automatikus méretezéssel. Ebben a módban, ha több forgalom van, mint amit a Application Gateway-vagy WAF tud kezelni, a forgalom elvesztését eredményezheti. A manuális mód megadása esetén kötelező megadni a példányszámot. A példányok száma 1 és 125 példány között lehet.

## <a name="autoscaling-and-high-availability"></a>Automatikus skálázás és magas rendelkezésre állás

Az Azure Application Gateway-átjárók mindig egy kiválóan elérhető módon vannak üzembe helyezve. A szolgáltatás több, konfiguráltként létrehozott példányból áll (ha az automatikus skálázás le van tiltva), vagy az alkalmazás terhelése miatt szükséges (ha az automatikus skálázás engedélyezve van). Vegye figyelembe, hogy a felhasználó szemszögéből nem feltétlenül látja el az egyes példányokat, hanem csak az Application Gateway szolgáltatás egészére. Ha egy adott példány problémába ütközik, és a működése leáll, az Azure Application Gateway transzparens módon létrehoz egy új példányt.

Vegye figyelembe, hogy még akkor is, ha az automatikus skálázást nulla minimális példányokkal konfigurálja, a szolgáltatás továbbra is elérhető lesz, ami mindig a rögzített ár részét képezi.

Egy új példány létrehozása azonban hosszabb időt is igénybe vehet (körülbelül hat vagy hét perc). Ezért ha nem szeretné megbirkózni ezzel az állásidővel, beállíthatja a minimális példányszámot (2), ideális esetben a rendelkezésre állási zónák támogatásával. Így a szokásos körülmények között legalább két példányban kell lennie az Azure-Application Gateway belül, így ha az egyik probléma a másikkal próbálkozik, akkor az új példány létrehozásakor megpróbál megbirkózni a forgalommal. Vegye figyelembe, hogy az Azure Application Gateway-példányok körülbelül 10 kapacitást tudnak támogatni, így attól függően, hogy mekkora forgalomra van szüksége, a minimális példány automatikus skálázási beállítását a 2 értéknél nagyobb értékre kell beállítania.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Szolgáltatások összehasonlítása v1 SKU és v2 SKU között

A következő táblázat összehasonlítja az egyes SKU-kal elérhető szolgáltatásokat.

| Szolgáltatás                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatikus skálázás                                       |          | &#x2713; |
| Zónaredundancia                                   |          | &#x2713; |
| Statikus VIP                                        |          | &#x2713; |
| Azure Kubernetes szolgáltatás (ak) bejövő adatkezelője |          | &#x2713; |
| Azure Key Vault-integráció                       |          | &#x2713; |
| HTTP (S) fejlécek újraírása                           |          | &#x2713; |
| URL-alapú útválasztás                                 | &#x2713; | &#x2713; |
| Több hely üzemeltetése                             | &#x2713; | &#x2713; |
| Forgalom átirányítása                               | &#x2713; | &#x2713; |
| Webalkalmazási tűzfal (WAF)                    | &#x2713; | &#x2713; |
| WAF egyéni szabályok                                  |          | &#x2713; |
| Transport Layer Security (TLS)/Secure-szoftvercsatorna (SSL) leállítása            | &#x2713; | &#x2713; |
| Végpontok közötti TLS-titkosítás                         | &#x2713; | &#x2713; |
| Munkamenet-affinitás                                  | &#x2713; | &#x2713; |
| Egyéni hibalapok                                | &#x2713; | &#x2713; |
| WebSocket támogatás                                 | &#x2713; | &#x2713; |
| HTTP/2-támogatás                                    | &#x2713; | &#x2713; |
| Kapcsolatkiürítés                               | &#x2713; | &#x2713; |

> [!NOTE]
> Az automatikus skálázási v2 SKU mostantól támogatja az [alapértelmezett állapot](application-gateway-probe-overview.md#default-health-probe) -ellenőrzéseket, hogy automatikusan figyelje a háttér-készlet összes erőforrásának állapotát, és kiemelje azokat a háttérbeli tagokat, amelyek nem megfelelőnek minősülnek. Az alapértelmezett állapot-mintavétel automatikusan konfigurálva van olyan backendekhez, amelyek nem rendelkeznek egyéni mintavételi konfigurációval. További információért lásd: [az Application Gateway Health-szondái](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>A v1 SKU-tól származó különbségek

Ez a szakasz a v1 SKU-ból eltérő v2 SKU szolgáltatásait és korlátozásait ismerteti.

|Különbség|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információ: [a végpontok és a végpontok közötti TLS áttekintése Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Standard_v2 és standard Application Gateway összekeverése ugyanazon az alhálózaton|Nem támogatott|
|User-Defined útvonal (UDR) Application Gateway alhálózaton|Támogatott (konkrét forgatókönyvek). Előzetes verzióban.<br> További információ a támogatott forgatókönyvekről: [Application Gateway konfiguráció áttekintése](configuration-infrastructure.md#supported-user-defined-routes).|
|NSG a bejövő porttartomány| -65200 – 65535 Standard_v2 SKU-hoz<br>-65503 – 65534 a standard SKU-hoz.<br>További információt a [Gyakori kérdések](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet)című témakörben talál.|
|Teljesítménynaplók az Azure diagnosticsban|Nem támogatott.<br>Az Azure-metrikákat kell használni.|
|Számlázás|Számlázás ütemezve, hogy 2019. július 1-jén induljon el.|
|FIPS mód|Ezek jelenleg nem támogatottak.|
|Csak ILB mód|Ez jelenleg nem támogatott. A nyilvános és a ILB mód együttes használata támogatott.|
|NET Watcher-integráció|Nem támogatott.|
|Azure Security Center – integráció|Még nem érhető el.

## <a name="migrate-from-v1-to-v2"></a>Migrálás az 1-esről a 2-es verzióra

Azure PowerShell parancsfájl a PowerShell-galériában érhető el, hogy segítséget nyújtson a v1 Application Gateway/WAF a v2 automatikus skálázási SKU-ba való átálláshoz. Ez a szkript segítséget nyújt a konfigurációnak a v1-átjáróról történő másolásához. A forgalom áttelepítése továbbra is az Ön felelőssége. További információ: [Azure Application Gateway migrálása v1-ről v2-re](migrate-v1-v2.md).

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal](quick-create-portal.md)
- [Hozzon létre egy automatikus skálázást, a Zone redundáns Application Gateway-t egy fenntartott virtuális IP-címmel a Azure PowerShell használatával](tutorial-autoscale-ps.md)
- További információ a [Application Gatewayról](overview.md).
- További információ a [Azure Firewallról](../firewall/overview.md).
