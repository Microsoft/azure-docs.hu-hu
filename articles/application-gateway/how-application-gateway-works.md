---
title: Az Application Gateway működése
description: Ez a cikk arról nyújt tájékoztatást, hogy az Application Gateway hogyan fogadja a bejövő kérelmeket, és hogyan irányítja azokat a háttérbe.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d2055bf812c3dc986a907d4358fa0e74e8af20fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599663"
---
# <a name="how-an-application-gateway-works"></a>Az Application Gateway működése

Ez a cikk azt ismerteti, hogyan fogadja el az Application Gateway a bejövő kérelmeket, és hogyan irányítja azokat a háttérbe.

![Az Application Gateway által elfogadott kérelem](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Az Application Gateway által elfogadott kérelem

1. Mielőtt egy ügyfél kérelmet küld egy Application Gatewaynek, az az Application Gateway tartománynevét egy DNS-kiszolgáló használatával oldja fel. Az Azure a DNS-bejegyzést vezérli, mivel az összes Application Gateway a azure.com tartományban van.

2. A Azure DNS visszaadja az ügyfél IP-címét, amely az Application Gateway előtérbeli IP-címe.

3. Az Application Gateway egy vagy több figyelőn fogadja a bejövő forgalmat. A figyelő egy logikai entitás, amely a kapcsolatkérelmeket ellenőrzi. A szolgáltatás az ügyfelek és az Application Gateway közötti kapcsolatok elérésére szolgáló előtéri IP-címmel, protokollal és portszámmal van konfigurálva.

4. Ha egy webalkalmazási tűzfal (WAF) használatban van, az Application Gateway ellenőrzi a kérések fejléceit és a törzset, ha van ilyen, a WAF szabályokkal szemben. Ez a művelet határozza meg, hogy a kérelem érvényes kérelem vagy biztonsági fenyegetés. Ha a kérelem érvényes, a rendszer átirányítja a háttérbe. Ha a kérelem nem érvényes, és a WAF megelőzési módban van, akkor biztonsági fenyegetésként blokkolva van. Ha észlelési módban van, a rendszer kiértékeli és naplózza a kérést, de továbbra is továbbítja a háttér-kiszolgálónak.

Az Azure Application Gateway belső alkalmazás-terheléselosztóként vagy internetkapcsolattal rendelkező alkalmazás-terheléselosztóként is használható. Az internetre irányuló Application Gateway nyilvános IP-címeket használ. Az internetre irányuló Application Gateway DNS-neve nyilvánosan feloldható a nyilvános IP-címére. Ennek eredményeképpen az internetre irányuló Application Gateway átjárók az ügyfelektől érkező kérelmeket az internetről is átirányítják.

A belső alkalmazás-átjárók csak privát IP-címeket használnak. Ha egyéni vagy [saját DNS zónát](../dns/private-dns-overview.md)használ, a tartománynévnek belsőleg feloldhatónak kell lennie a Application Gateway magánhálózati IP-címére. Ezért a belső terheléselosztó csak olyan ügyfelektől érkező kéréseket tud irányítani, akiknek hozzáférése van egy virtuális hálózathoz az Application Gateway számára.

## <a name="how-an-application-gateway-routes-a-request"></a>Egy kérelem átirányítása az Application Gateway számára

Ha egy kérelem érvényes, és nem tiltja le a WAF, az Application Gateway kiértékeli a figyelőhöz társított kérelem-útválasztási szabályt. Ez a művelet határozza meg, hogy melyik háttér-készlethez kell irányítani a kérést.

A kérelem útválasztási szabálya alapján az Application Gateway meghatározza, hogy a figyelő összes kérelmét egy adott háttér-készletre irányítsa-e át, átirányítja a kérelmeket a különböző backend-készletekbe az URL-cím alapján, vagy átirányítja a kéréseket egy másik portra vagy külső webhelyre.
>[!NOTE]
>A szabályok feldolgozása abban a sorrendben történik, ahogy a v1 SKU-portálon vannak felsorolva. 

Amikor az Application Gateway kiválasztja a háttér-készletet, a rendszer elküldi a kérést a készletben található kifogástalan háttér-kiszolgálók egyikének (y. y. y. y). A kiszolgáló állapotát állapot-mintavétel határozza meg. Ha a háttér-készlet több kiszolgálót is tartalmaz, az Application Gateway egy ciklikusan megjelenő algoritmus használatával irányítja a kérelmeket az egészséges kiszolgálók között. Ez a terhelés kiegyenlíti a kérelmeket a kiszolgálókon.

Miután az Application Gateway meghatározza a háttér-kiszolgálót, megnyílik egy új TCP-munkamenet a háttér-kiszolgálóval a HTTP-beállítások alapján. A HTTP-beállítások határozzák meg a protokoll, a port és az útválasztással kapcsolatos egyéb beállításokat, amelyek szükségesek ahhoz, hogy új munkamenetet hozzon létre a háttér-kiszolgálóval.

A HTTP-beállításokban használt port és protokoll határozza meg, hogy az Application Gateway és a háttérrendszer-kiszolgálók közötti forgalom titkosítva van-e (így a végpontok közötti TLS), vagy nem titkosított.

Amikor egy Application Gateway elküldi az eredeti kérést a háttér-kiszolgálónak, az az állomásnév, az elérési út és a protokoll felülbírálásához kapcsolódó HTTP-beállításokban megadott egyéni konfigurációkat is tiszteletben tartja. Ez a művelet megtartja a cookie-alapú munkamenet-affinitást, a kapcsolat kiürítését, az állomásnév kijelölését a háttérből, és így tovább.

 >[!NOTE]
>Ha a háttér-készlet:
> - **Nyilvános végpont**, az Application Gateway a előtér nyilvános IP-címét használja a kiszolgáló eléréséhez. Ha nincs egy előtér nyilvános IP-címe, a rendszer az egyiket a kimenő külső kapcsolathoz rendeli hozzá.
> - **Belsőleg feloldható teljes tartománynevet vagy magánhálózati IP-címet tartalmaz**, az Application Gateway a kérést a háttér-kiszolgálóra irányítja a példány magánhálózati IP-címeinek használatával.
> - **Külső végpontot vagy külsőleg feloldható teljes tartománynevet tartalmaz**, az Application Gateway a háttérben lévő nyilvános IP-cím használatával irányítja a kérést a háttér-kiszolgálónak. A DNS-feloldás egy magánhálózati DNS-zónán vagy egyéni DNS-kiszolgálón alapul, ha konfigurálva van, vagy az alapértelmezett Azure által biztosított DNS-t használja. Ha nincs egy előtér nyilvános IP-címe, a rendszer az egyiket a kimenő külső kapcsolathoz rendeli hozzá.

### <a name="modifications-to-the-request"></a>A kérelem módosításai

Az Application Gateway négy további fejlécet szúr be az összes kérelembe, mielőtt továbbítja a kéréseket a háttérbe. Ezek a fejlécek x-továbbítva, x-Forwarded-proto, x által továbbított-port és x-Original-Host. Az x által továbbított fejléc formátuma az IP: Port vesszővel tagolt listája.

Az x által továbbított-proto érvényes értékei HTTP vagy HTTPS. X – továbbított – a port megadja azt a portot, ahol a kérés elérte az Application Gatewayt. Az X-Original-Host fejléc tartalmazza azt az eredeti állomásfejléc-fejlécet, amelyhez a kérés érkezett. Ez a fejléc az Azure-webhelyek integrációjában hasznos, ahol a bejövő állomásfejléc módosul, mielőtt a rendszer átirányítja a forgalmat a háttérbe. Ha a munkamenet-affinitás engedélyezve van, akkor egy átjáró által felügyelt affinitási cookie-t adhat hozzá.

Az Application Gateway beállítható úgy, hogy módosítsa a kérések és válaszok fejléceit és URL-címét a [HTTP-fejlécek és URL-cím újraírásával](rewrite-http-headers-url.md) , vagy az URI elérési útjának módosításához egy elérésiút-felülbírálási beállítás használatával. Ha azonban erre nincs konfigurálva, az összes bejövő kérelem a háttérbe kerül.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók az Application Gateway összetevőiről](application-gateway-components.md)
