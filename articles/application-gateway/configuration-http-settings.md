---
title: Azure Application Gateway HTTP-beállítások konfigurálása
description: Ez a cikk az Azure Application Gateway HTTP-beállításainak konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652881"
---
# <a name="application-gateway-http-settings-configuration"></a>Application Gateway HTTP-beállítások konfigurálása

Az Application Gateway az itt megadott konfiguráció használatával irányítja át a forgalmat a háttér-kiszolgálókra. A HTTP-beállítás létrehozása után hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz.

## <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Az Azure Application Gateway átjáró által felügyelt cookie-kat használ a felhasználói munkamenetek karbantartásához. Amikor egy felhasználó az első kérést Application Gateway küldi el, az egy affinitási cookie-t állít be a válaszban egy olyan kivonatoló értékkel, amely tartalmazza a munkamenet részleteit, így az affinitási cookie-t tároló további kérelmek ugyanahhoz a háttér-kiszolgálóhoz lesznek irányítva a stickiing fenntartása érdekében. 

Ez a funkció akkor hasznos, ha egy felhasználói munkamenetet szeretne megőrizni ugyanazon a kiszolgálón, és amikor a munkamenet-állapotot helyileg menti a kiszolgálón egy felhasználói munkamenethez. Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitást, nem használhatja ezt a funkciót. A használatához győződjön meg arról, hogy az ügyfelek támogatják a cookie-kat.

A [Chromium böngésző](https://www.chromium.org/Home) [V80 frissítése](https://chromiumdash.appspot.com/schedule) olyan mandátumot hozott, amelyben a [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) attribútum nélküli http-cookie-kat SameSite = LAX-ként kell kezelni. CORS (eltérő eredetű erőforrás-megosztási) kérelmek esetén, ha a cookie-t harmadik féltől származó környezetben kell elküldeni, akkor a SameSite = none értéket kell használnia *. Biztonságos* attribútumok, és csak HTTPS protokollal küldhetők el. Ellenkező esetben a böngészőben a böngésző nem küldi el a cookie-kat a harmadik féltől származó környezetben. Ennek a frissítésnek a célja, hogy fokozza a biztonságot, és elkerülje a helyek közötti kérelmek hamisításának (CSRF) elleni támadásait. 

Ha támogatni szeretné ezt a változást, a 17 2020. február Application Gateway (az összes SKU-típus) egy másik, *ApplicationGatewayAffinityCORS* nevű cookie-t fog beadni a meglévő *ApplicationGatewayAffinity* -cookie mellett. A *ApplicationGatewayAffinityCORS* -cookie két további attribútummal bővült (*"SameSite = none; Biztonságos "*), így a Sticky-munkamenetek még a forráson kívüli kérelmek esetében is megmaradnak.

Vegye figyelembe, hogy az alapértelmezett affinitási cookie neve *ApplicationGatewayAffinity* , és módosítható. Ha egyéni affinitási cookie-nevet használ, további cookie-t adunk hozzá a CORS utótagként. Például: *CustomCookieNameCORS*.

> [!NOTE]
> Ha a *SameSite = none* attribútum be van állítva, akkor kötelező, hogy a cookie a *biztonságos* jelzőt is tartalmazza, és HTTPS-kapcsolaton keresztül kell elküldeni.  Ha a CORS-kapcsolaton keresztül szükséges a munkamenet-affinitás, a számítási feladatokat HTTPS-re kell áttelepítenie. Tekintse meg a TLS-kiszervezést és a teljes körű TLS-dokumentációt Application Gateway itt – [Áttekintés](ssl-overview.md), az [Application Gateway konfigurálása TLS-megszakítással a Azure Portal használatával, a](create-ssl-portal.md) [végpontok közötti TLS konfigurálásához Application Gateway használatával a portálon](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatok kiürítése megkönnyíti a háttérbeli készlet tagjainak biztonságos eltávolítását a tervezett szolgáltatási frissítések során. Ezt a beállítást egy háttér-készlet összes tagjára alkalmazhatja, ha engedélyezi a kapcsolatok kiürítését a HTTP-beállításban. Gondoskodik róla, hogy a háttér-készletek összes regisztrációja továbbra is megőrizze a meglévő kapcsolatokat, és a rendelkezésre állási kérelmeket a konfigurálható időtúllépés mellett kézbesítse, és ne kapjon új kéréseket és kapcsolatokat. Ez alól kivételt képeznek a példányok deregisztrációja az átjáró által felügyelt munkamenet-affinitás miatt, és a rendszer továbbra is továbbítja őket a deregistering példányokra. A kapcsolatok kiürítése olyan háttérbeli példányokra vonatkozik, amelyek kifejezetten eltávolíthatók a háttér-készletből.

## <a name="protocol"></a>Protokoll

A Application Gateway a HTTP-t és a HTTPS-t is támogatja a háttér-kiszolgálókra irányuló útválasztási kérelmek esetében. Ha a HTTP lehetőséget választja, a háttér-kiszolgálókra irányuló forgalom titkosítatlan. Ha a titkosítatlan kommunikáció nem elfogadható, válassza a HTTPS lehetőséget.

A figyelőben lévő HTTPS-vel kombinált beállítás támogatja a [végpontok közötti TLS-t](ssl-overview.md). Ez lehetővé teszi, hogy biztonságosan továbbítsa a háttérben titkosított bizalmas adatokat. A végpontok közötti TLS-kapcsolattal rendelkező háttérrendszer minden olyan háttér-kiszolgálóját be kell állítani egy tanúsítvánnyal, amely lehetővé teszi a biztonságos kommunikációt.

## <a name="port"></a>Port

Ez a beállítás határozza meg azt a portot, ahol a háttér-kiszolgálók az Application gatewaytől érkező forgalmat figyelik. A portok 1-től 65535-ig állíthatók be.

## <a name="request-timeout"></a>Kérelem időtúllépése

Ez a beállítás azon másodpercek száma, ameddig az Application Gateway várakozik a háttér-kiszolgálótól érkező válasz fogadására.

## <a name="override-back-end-path"></a>Háttérbeli elérési út felülbírálása

Ezzel a beállítással beállíthatja, hogy a rendszer mikor továbbítsa a kérést a háttérbe, ha nem kötelező egyéni továbbítási útvonalat szeretne használni. A rendszer átmásolja a **felülbírálási háttér elérési útjának** egyéni elérési útjával megegyező bejövő elérési út bármely részét a továbbított elérési útra. A következő táblázat bemutatja, hogyan működik ez a szolgáltatás:

- Ha a HTTP-beállítás egy alapszintű kérelemhez van csatolva – útválasztási szabály:

  | Eredeti kérelem  | Háttérbeli elérési út felülbírálása | Kérelem továbbítva a háttérbe |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home            | felülbírálás            | /override/home/              |
  | /home/secondhome/ | felülbírálás            | /override/home/secondhome/   |

- Ha a HTTP-beállítás egy elérésiút-alapú kérelem – útválasztási szabályhoz van csatolva:

  | Eredeti kérelem           | Elérésiút-szabály       | Háttérbeli elérési út felülbírálása | Kérelem továbbítva a háttérbe |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | elérésiút      | felülbírálás            | /override/home/              |
  | /pathrule/home/secondhome/ | elérésiút      | felülbírálás            | /override/home/secondhome/   |
  | /Home                     | elérésiút      | felülbírálás            | /override/home/              |
  | /home/secondhome/          | elérésiút      | felülbírálás            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | felülbírálás            | felülbírálás                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | felülbírálás            | /override/secondhome/        |
  | elérésiút                 | elérésiút      | felülbírálás            | felülbírálás                   |

## <a name="use-for-app-service"></a>Az App Service használata

Ez egy felhasználói felületi parancsikon, amely kiválasztja a Azure App Service háttér két kötelező beállítását. Lehetővé teszi **az állomásnév kiválasztását a háttérbeli címről**, és létrehoz egy új egyéni mintavételt, ha még nem rendelkezik ilyennel. (További tudnivalókért tekintse meg a jelen cikk az [állomásnév kiválasztása a háttérbeli címekről](#pick-host-name-from-back-end-address)beállítását ismertető szakaszt.) Létrejön egy új mintavétel, és a mintavételi fejléc a háttérbeli tag címe alapján van kiválasztva.

## <a name="use-custom-probe"></a>Egyéni mintavétel használata

Ez a beállítás egy [Egyéni](application-gateway-probe-overview.md#custom-health-probe) mintavételt TÁRSÍT egy http-beállítással. Egy HTTP-beállítással csak egy egyéni mintavételt rendelhet hozzá. Ha nem kifejezetten egyéni mintavételt rendel hozzá, az [alapértelmezett](application-gateway-probe-overview.md#default-health-probe-settings) mintavétel a háttér állapotának figyelésére szolgál. Javasoljuk, hogy hozzon létre egy egyéni mintavételt a háttér állapotának monitorozásához.

> [!NOTE]
> Az egyéni mintavétel nem figyeli a háttér-készlet állapotát, kivéve, ha a megfelelő HTTP-beállítás explicit módon hozzá van rendelve egy figyelőhöz.

## <a name="pick-host-name-from-back-end-address"></a>Állomásnév kiválasztása a háttérbeli címről

Ez a funkció dinamikusan beállítja a kérésben szereplő *állomásfejléc* -fejlécet a háttérbeli készlet állomásneve számára. IP-címet vagy teljes tartománynevet használ.

Ez a funkció segít, ha a háttér tartományneve eltér az Application Gateway DNS-nevétől, a háttér pedig egy adott állomásfejléc-re támaszkodik a megfelelő végpontra való feloldáshoz.

Ilyen eset például a több-bérlős szolgáltatás, mint a háttérrendszer. Az App Service egy több-bérlős szolgáltatás, amely egyetlen IP-címmel rendelkező megosztott területet használ. Így az App Service csak az egyéni tartomány beállításaiban konfigurált gazdagépeken keresztül érhető el.

Alapértelmezés szerint az Egyéni tartománynév a *example.azurewebsites.net*. Ha az App Service-t olyan állomásnév használatával szeretné elérni az alkalmazás-átjárón keresztül, amely nincs explicit módon regisztrálva az App Service-ben vagy az Application Gateway FQDN-n keresztül, akkor a rendszer felülbírálja az eredeti kérelemben szereplő állomásnév nevét az App Service állomásneve számára. Ehhez engedélyezze az **állomásnév kiválasztása a háttérbeli címekről** beállítást.

Olyan egyéni tartomány esetében, amelynek meglévő egyéni DNS-neve az App Service-re van leképezve, nem kell engedélyeznie ezt a beállítást.

> [!NOTE]
> Ez a beállítás nem szükséges a App Service Environmenthoz, amely egy dedikált központi telepítés.

## <a name="host-name-override"></a>Állomásnév felülbírálása

Ez a funkció a megadott állomásnévvel helyettesíti az Application Gateway bejövő kérelmében szereplő *állomásfejléc* -fejlécet.

Ha például a *www.contoso.com* meg van adva az **állomásnév** beállításban, az eredeti kérelem * a (z) * értékre `https://appgw.eastus.cloudapp.azure.com/path1` módosul, `https://www.contoso.com/path1` Amikor a kérést a rendszer továbbítja a háttér-kiszolgálónak.

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a háttérrendszer-készletről](configuration-overview.md#back-end-pool)