---
title: 'Azure-beli bejárati ajtó: gyakori kérdések'
description: Ezen a lapon választ kaphat az Azure bejárati standard/Premium kiadásával kapcsolatos gyakori kérdésekre.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a42601b696f292e9d2a9da90070fea3662acae87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099110"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Gyakori kérdések az Azure-beli előtérben standard/prémium (előzetes verzió)

Ez a cikk az Azure-előtérben elérhető funkciókkal és funkciókkal kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

### <a name="what-is-azure-front-door"></a>Mi az az Azure Front Door?

Az Azure bejárati ajtó egy gyors, megbízható és biztonságos, modern felhőalapú CDN intelligens veszélyforrások elleni védelemmel. Statikus és dinamikus tartalom-gyorsítást, globális terheléselosztást és fokozott biztonságot nyújt a globális Hyper-Scale alkalmazások, API-k, webhelyek és felhőalapú szolgáltatások számára az intelligens veszélyforrások elleni védelemmel.

### <a name="what-features-does-azure-front-door-support"></a>Milyen funkciókat támogat az Azure bejárati ajtó?

Az Azure bejárati ajtaja a következőket támogatja:

* Statikus tartalom és dinamikus alkalmazás-gyorsítás is
* TLS/SSL-kiürítés és végpontok közötti TLS
* Webalkalmazási tűzfal
* Cookie-alapú munkamenet-affinitás
* URL-elérésiút-alapú útválasztás
* Ingyenes tanúsítványok és több tartományi felügyelet

A támogatott szolgáltatások teljes listájáért lásd: [Az Azure bejárati ajtó áttekintése](overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Mi a különbség az Azure bejárati ajtó és az Azure Application Gateway között?

Míg a bejárati ajtó és a Application Gateway a 7. rétegbeli (HTTP/HTTPS) terheléselosztó, az elsődleges különbség az, hogy a bejárati ajtó globális szolgáltatás. Application Gateway regionális szolgáltatás. Míg a bejárati ajtó terheléselosztást végez a különböző méretezési egységek/fürtök/Stamp egységek között a régiók között, Application Gateway lehetővé teszi a skálázási egységen belüli virtuális gépek/tárolók közötti terheléselosztást.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Mikor érdemes üzembe helyezni egy Application Gateway a bejárati ajtó mögött?

A legfontosabb forgatókönyvek, hogy miért érdemes Application Gateway a bejárati ajtó mögött:

* A bevezető ajtó csak a globális szinten hajthatja végre a Path-alapú terheléselosztást, de ha a virtuális hálózaton (VNET) belül még a forgalom terheléselosztását szeretné elvégezni, akkor a Application Gatewayt kell használniuk.
* Mivel a bejárati ajtó nem működik a virtuális gépek/tárolók szintjén, így nem tudja elvégezni a kapcsolatok kiürítését. Application Gateway azonban lehetővé teszi a kapcsolatok kiürítését. 
* Egy Application Gateway a bejárati ajtó mögött az egyik elérheti a 100%-os TLS/SSL-kiszervezést, és csak HTTP-kérelmeket irányíthat a virtuális hálózaton (VNET) belül.
* A bejárati ajtó és a Application Gateway támogatja a munkamenet-affinitást is. A bejárati ajtó közvetlenül a felhasználói munkamenetből egy adott régióban lévő fürtre vagy háttérre irányíthatja át a forgalmat. Application Gateway a fürtön belüli ugyanazon kiszolgálóra irányíthatja a forgalmat a affinitize.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Üzembe helyezhető Azure Load Balancer a bejárati ajtó mögött?

Az Azure bevezető ajtajának nyilvános VIP-nek vagy nyilvánosan elérhető DNS-névnek kell lennie ahhoz, hogy átirányítsa a forgalmat. A bejárati ajtó mögötti Azure Load Balancer üzembe helyezése gyakori használati eset.

### <a name="what-protocols-does-azure-front-door-support"></a>Milyen protokollok támogatják az Azure bejárati ajtót?

Az Azure bejárati ajtó támogatja a HTTP, a HTTPS és a HTTP/2 használatát.

### <a name="how-does-azure-front-door-support-http2"></a>Hogyan támogatja az Azure bejárati ajtó a HTTP/2-t?

A HTTP/2 protokoll támogatása csak az Azure bejárati ajtóhoz csatlakozó ügyfelek számára érhető el. A háttérrendszer-készletben található háttérrendszer-kommunikáció HTTP/1.1-n keresztül történik. A HTTP/2 támogatás alapértelmezés szerint engedélyezve van.

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>Milyen erőforrások támogatottak ma a Origin Group részeként?

A Origin csoport lehet tárolási, webalkalmazási, Kubernetes-példány vagy bármely más, nyilvános kapcsolattal rendelkező egyedi állomásnév. Az Azure-bejárati ajtó megköveteli, hogy az eredetek a nyilvános IP-címen vagy nyilvánosan feloldható DNS-állomásnéven legyenek definiálva. A származási csoport tagjai többek között zónák, régiók vagy akár az Azure-on kívül is lehetnek, amíg nyilvános kapcsolattal rendelkeznek.

### <a name="what-regions-is-the-service-available-in"></a>Milyen régiókban érhető el a szolgáltatás?

Az Azure bejárati ajtó egy globális szolgáltatás, amely nem kötődik egyetlen adott Azure-régióhoz sem. A bejárati ajtó létrehozásakor csak az erőforráscsoport számára szükséges helyet kell megadnia. Ez a hely alapvetően meghatározza, hogy az erőforráscsoport metaadatai hol lesznek tárolva. Az előtérben lévő erőforrás maga globális erőforrásként jön létre, és a konfiguráció globálisan települ az összes pop-ra (a jelenléti pontra). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Mik a POP-helyszínek az Azure-beli bejárati ajtón?

Az Azure bejárati ajtaja ugyanazokat a listát tartalmazza, mint a POP (jelenléti pont) helyei a Microsoft Azure CDN. A POPs teljes listájáért tekintse meg [Azure CDN pop-helyszíneket a Microsofttól](../../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Az Azure bejárati ajtó egy dedikált üzembe helyezés az alkalmazáshoz vagy megosztott az ügyfelek között?

Az Azure bejárati ajtó egy globálisan elosztott, több-bérlős szolgáltatás. A bejárati ajtó infrastruktúrája az összes ügyfele között meg van osztva. Egy bejárati ajtó profiljának létrehozásával meghatározhatja az alkalmazáshoz szükséges konkrét konfigurációt. Az előtérben végzett módosítások nem érintik a többi bejárati ajtó konfigurációját.

### <a name="is-http-https-redirection-supported"></a>Támogatott-e a HTTP->HTTPS-átirányítás?

Igen. Valójában az Azure bejárati ajtó támogatja a gazdagépet, az elérési utat, a lekérdezési karakterlánc-átirányítást és az URL-átirányítás részét. További információ az [URL-átirányításról](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hogyan lezárni a háttérbeli hozzáférést, hogy csak az Azure bejárati ajtót?

Ha le szeretné zárni az alkalmazást, hogy csak a megadott bejárati ajtóról fogadjon forgalmat, be kell állítania a háttér IP-ACL-jeit. Ezután korlátozza a háttér forgalmát a bejárati ajtó által eljuttatott X-Azure-FDID fejléc adott értékére. Ezeket a lépéseket az alábbiak szerint részletezjük:

* Konfigurálja az IP-hozzáférés a háttérrendszer számára, hogy fogadja a forgalmat az Azure bejárati ajtó háttérbeli IP-címe és az Azure infrastruktúra-szolgáltatásai között. Az alábbi IP-címen tájékozódhat a háttérrendszer hozzáférés:
 
    * Tekintse át az [Azure IP-címtartományok és szolgáltatás-címkék](https://www.microsoft.com/download/details.aspx?id=56519) *AzureFrontDoor. backend* szakaszát az első ajtó IPv4-háttér IP-címének tartományához. Használhatja a *AzureFrontDoor. backend* szolgáltatást is a [hálózati biztonsági csoportokban](../../virtual-network/network-security-groups-overview.md#security-rules).
    * Az Azure [alapszintű infrastruktúra-szolgáltatásai](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) virtualizált gazdagépi IP-címeken keresztül: `168.63.129.16` és `169.254.169.254` .

    > [!WARNING]
    > Előfordulhat, hogy a bejárati ajtó háttérbeli IP-címe később is változhat, ezért a megjelenő esetekben biztosítani fogjuk, hogy az [Azure IP-címtartományok és a szolgáltatási címkék](https://www.microsoft.com/download/details.aspx?id=56519)integrálva lennének. Javasoljuk, hogy az [Azure IP-címtartományok és a szolgáltatás-címkék](https://www.microsoft.com/download/details.aspx?id=56519) esetében minden módosítást és frissítést előfizessen.

* Hajtson végre egy GET műveletet az előtérben az API `2020-01-01` -vagy újabb verzióval. Az API-hívásban keresse meg a `frontdoorID` mezőt. Szűrje a "**X-Azure-FDID**" bejövő fejlécet a háttérbe a mező értékével `frontdoorID` . `Front Door ID`Az értéket az áttekintő szakaszban is megtalálhatja az első ajtós portál oldalon. 

* Alkalmazzon szabály-szűrést a háttérbeli webkiszolgálón a forgalom korlátozásához az eredményül kapott X-Azure-FDID fejléc értéke alapján.

  Íme egy példa a [Microsoft Internet Information Services (IIS)](https://www.iis.net/)szolgáltatásra:

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>A kiválasztó IP-cím változhat a bejárati ajtó élettartama során?

A bejárati ajtóhoz tartozó előtér-alhálózatok IP-címe általában nem változik, és a bejárati ajtó élettartama esetén statikus maradhat. Azonban nincsenek ilyen **garanciák** . Kérjük, ne vegyen fel közvetlen függőségeket az IP-címekre.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Támogatja az Azure bejárati ajtó a statikus vagy dedikált IP-címeket?

Nem, az Azure bejárati ajtaja jelenleg nem támogatja a statikus vagy dedikált előtér-IP-címek használatát. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Támogatja az Azure bejárati ajtót az x-Forwarded – fejlécek esetében?

Igen, az Azure bejárati ajtaja támogatja az X-továbbított-for, X-Forwarded-Host és X-Forwarded-proto fejléceket. X-továbbítás esetén – ha a fejléc már szerepel, akkor a bejárati ajtó hozzáfűzi az ügyfél szoftvercsatorna IP-címét. Máskülönben hozzáadja a fejlécet az ügyfél szoftvercsatorna IP-címéhez az értékként. Az X által továbbított-gazdagép és az X-továbbított-proto esetében az érték felül van bírálva.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Mennyi ideig tart egy Azure bejárati ajtó üzembe helyezése? A bejárati ajtóm továbbra is működik a frissítéskor?

Egy új bejárati ajtó létrehozása vagy egy meglévő bejárati ajtó frissítése körülbelül 3 – 5 percet vesz igénybe globális üzembe helyezés esetén. Ez azt jelenti, hogy körülbelül 3 – 5 percet vesz igénybe a bejárati ajtó konfigurálása globálisan.

Megjegyzés: az egyéni TLS/SSL-tanúsítvány frissítései körülbelül 30 percet vesznek igénybe globálisan.

Az útvonalak vagy a háttér-készletek frissítései zökkenőmentesek, és nulla állásidőt okoznak (ha az új konfiguráció helyes). A tanúsítvány frissítései nem okoznak kimaradást, kivéve, ha az "Azure bevezető ajtaja felügyelve" értékről "a saját tanúsítvány használata" vagy a más módon vált.


## <a name="configuration"></a>Konfiguráció

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Képes az Azure-beli bejárati terheléselosztásra vagy a virtuális hálózaton belüli forgalom irányítására?

Az Azure bejárati ajtaján (AFD) egy nyilvános IP-cím vagy egy nyilvánosan feloldható DNS-név szükséges a forgalom irányításához. Az Azure bevezető ajtaja nem tud közvetlenül átirányítani egy virtuális hálózat erőforrásaira. A probléma megoldásához használhat egy Application Gateway vagy egy nyilvános IP-címmel rendelkező Azure Load Balancer is.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Milyen időtúllépéseket és korlátokat biztosít az Azure bejárati ajtó?

Ismerje meg az Azure-előtérben lévő összes dokumentált [időtúllépést és korlátot](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Mennyi időt vesz igénybe egy szabály, hogy az a bejárati ajtó szabályainak motorba való felvétele után is érvénybe lép?

A szabályok motorjának konfigurációja körülbelül 10 – 15 percet vesz igénybe a frissítés befejezéséhez. A szabály a frissítés befejeződése után is érvénybe lép. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Konfigurálható Azure CDN az előtérben lévő profil vagy a Azure CDN mögötti ajtó mögött?

Az Azure bejárati ajtó és a Azure CDN nem konfigurálható együtt, mert mindkét szolgáltatás ugyanazt az Azure Edge-helyet használja, amikor válaszol a kérelmekre. 

## <a name="performance"></a>Teljesítmény

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hogyan támogatja az Azure bejárati ajtó a magas rendelkezésre állást és a méretezhetőséget?

Az Azure bejárati ajtó egy globálisan elosztott, több-bérlős platform, amely nagy mennyiségű kapacitással gondoskodik az alkalmazás méretezhetőségi igényeiről. A Microsoft globális hálózatának szélétől kezdve a bejárati ajtó globális terheléselosztási képességet biztosít, amely lehetővé teszi, hogy a teljes alkalmazást, vagy akár az egyes régiókban vagy más felhőkben is egyedi szolgáltatást végezzen.

## <a name="tls-configuration"></a>TLS-konfiguráció

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Milyen TLS-verziókat támogat az Azure bejárati ajtó?

A 2019 szeptember után létrehozott összes bejárati profil a TLS 1,2-et használja alapértelmezett minimumként.

A bejárati ajtó támogatja a 1,0, 1,1 és 1,2 TLS-verziókat. A TLS 1,3 még nem támogatott.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Milyen tanúsítványokat támogat az Azure-beli bejárati ajtó?

Ha engedélyezni szeretné a HTTPS protokollt a bejárati ajtó egyéni tartományán, kiválaszthat egy olyan tanúsítványt, amely az Azure bejárati ajtó által felügyelt vagy a saját tanúsítványát használja.
A bejárati ajtó által felügyelt beállítás a Digicert-on keresztül szabványos TLS/SSL-tanúsítványt, valamint a bejárati ajtó Key Vault tárolja. Ha úgy dönt, hogy saját tanúsítványt használ, akkor a tanúsítványokat egy támogatott HITELESÍTÉSSZOLGÁLTATÓTÓL is elvégezheti, és lehet szabványos TLS, kiterjesztett ellenőrzési tanúsítvány vagy akár helyettesítő tanúsítvány is. Az önaláírt tanúsítványok nem támogatottak.

### <a name="does-front-door-support-autorotation-of-certificates"></a>Támogatja a bejárati ajtó a tanúsítványok autorotációját?

Az előtérben felügyelt tanúsítvány beállításnál a tanúsítványokat a rendszer automatikusan elforgatja a bejárati ajtón. Ha egy bejárati ajtót felügyelt tanúsítványt használ, és azt látja, hogy a tanúsítvány lejárati dátuma kevesebb, mint 60 nap múlva, a támogatási jegyet.

Saját egyéni TLS/SSL-tanúsítvány esetén az autorotáció nem támogatott. Az adott egyéni tartományhoz való első beállításhoz hasonlóan a Key Vault a megfelelő tanúsítvány-verzióra kell mutatnia. Győződjön meg arról, hogy az első ajtóhoz tartozó szolgáltatásnév továbbra is rendelkezik hozzáféréssel a Key Vaulthoz. Ez a tanúsítvány-bevezetési művelet nem okoz semmilyen termelési időt, ha a tulajdonos neve vagy a TÁROLÓHÁLÓZATI tanúsítvány nem változik.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Milyen titkosítási csomagokat támogat az Azure bejárati ajtó?

A TLS 1.2 esetén a következő titkosítási csomagok támogatottak: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Az egyéni tartományok használata a TLS 1.0/1.1-es verzióval engedélyezve a következő titkosítási csomagok támogatottak:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Beállítható a TLS-házirend a TLS protokoll verzióinak szabályozására?

A minimális TLS-verziót az Azure-beli bejárati ajtóban konfigurálhatja az egyéni tartomány HTTPS-beállításaiban az Azure Portal vagy az [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)használatával. Jelenleg a 1,0 és a 1,2 közötti választásra van lehetőség.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Beállítható a bejárati ajtó, hogy csak bizonyos titkosítási csomagokat támogasson?

Nem, az adott titkosítási csomagok bejáratának konfigurálása nem támogatott. Saját TLS-/SSL-tanúsítványt kaphat a hitelesítésszolgáltatótól (például VeriSign, Trust vagy Digicert). Ezután a tanúsítvány létrehozásakor adott titkosítási lakosztállyal rendelkezik. 

### <a name="does-front-door-support-ocsp-stapling"></a>Támogatja az előtérben az OCSP-tűzést?

Igen, a bejárati ajtó alapértelmezés szerint támogatja az OCSP-tűzést, és nincs szükség konfigurációra.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Támogatja az Azure bejárati ajtó a háttérbeli forgalom újratitkosítását is?

Igen, az Azure bejárati ajtó támogatja a TLS/SSL-kiszervezést és a végpontok közötti TLS-t, amely újratitkosítja a forgalmat a háttérbe. Mivel a háttérbeli kapcsolatok a nyilvános IP-címen történnek, javasoljuk, hogy konfigurálja a bejárati ajtót a HTTPS használatára továbbítási protokollként.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Támogatja a bejárati ajtó a HTTPS-kapcsolathoz tartozó önaláírt tanúsítványokat?

Nem, az önaláírt tanúsítványok nem támogatottak a bejárati ajtón, és a korlátozás a következőkre is vonatkozik:

* **Háttérrendszer**: nem használhat önaláírt tanúsítványokat, amikor a forgalmat HTTPS vagy https állapotú mintavételként továbbítja, vagy ha a gyorsítótárat a forrástól az útválasztási szabályoknak megfelelő gyorsítótárazással tölti ki.
* Előtér **: nem** használhat önaláírt tanúsítványokat saját egyéni TLS/SSL-tanúsítvány használata esetén a https engedélyezéséhez az egyéni tartományon.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Miért nem sikerül HTTPS-forgalmat felvenni a háttérben?

Ahhoz, hogy sikeresen HTTPS-kapcsolatot lehessen létesíteni a háttérrel, hogy az állapot-és a továbbítási kérelmeket, két oka lehet a HTTPS-forgalom meghibásodásának:

* A **tanúsítvány tulajdonosának neve nem egyezik**: a HTTPS-kapcsolatok esetében a bejárati ajtó azt várja, hogy a háttérrendszer a háttérbeli állomásnévvel egyező tulajdonosi névvel rendelkező érvényes hitelesítésszolgáltatótól származó tanúsítványt jelenít meg. Ha például a háttérbeli állomásnév be van állítva, `myapp-centralus.contosonews.net` és az a tanúsítvány, amelyet a háttér a TLS-kézfogás során mutat be, nem rendelkezik `myapp-centralus.contosonews.net` vagy `*myapp-centralus*.contosonews.net` a tulajdonos nevében. Ezután a bejárati ajtó elutasítja a kapcsolatokat, és hibát eredményez. 
    * **Megoldás**: nem ajánlott a megfelelőség szempontjából, de a hiba megkerüléséhez tiltsa le a tanúsítvány tulajdonosának neve ellenőrzését az előtérben. Ezt a beállítást a Azure Portal beállítások területén, valamint az API BackendPoolsSettings alatt találja.
* A **tanúsítvány érvénytelen hitelesítésszolgáltatótól való üzemeltetése**: csak az [érvényes](troubleshoot-allowed-certificate-authority.md) hitelesítésszolgáltatóktól származó tanúsítványok használhatók a háttérbe a bejárati ajtón. A belső hitelesítésszolgáltatóktól vagy önaláírt tanúsítványokból származó tanúsítványok nem engedélyezettek.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Használhatok ügyfél/kölcsönös hitelesítést az Azure bejárati ajtaján?

Nem. Bár az Azure bejárati ajtaja támogatja a TLS 1,2-et, amely az [RFC 5246](https://tools.ietf.org/html/rfc5246)-ben bevezette az ügyfél/kölcsönös hitelesítést, az Azure bejárati ajtó jelenleg nem támogatja az ügyfél/kölcsönös

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Milyen típusú metrikák és naplók érhetők el az Azure-beli bejárati ajtón?

A naplókról és az egyéb diagnosztikai képességekről további információt a metrikák és naplók a bejárati ajtóhoz való figyelését ismertető témakörben talál.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Mi az adatmegőrzési szabályzat a diagnosztikai naplókon?

A diagnosztikai naplók a Customers Storage-fiókba áramlanak, és az ügyfelek megadhatják az adatmegőrzési szabályzatot a preferencia alapján. A diagnosztikai naplókat egy Event hub-vagy Azure Monitor-naplóba is lehet elküldeni. További információ: [Azure bejárati ajtó naplózása](how-to-logs.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hogyan naplók beszerzése az Azure bejárati ajtóhoz?

A naplók elérhetők az Azure-előtérben. A portálon válassza a **tevékenység napló** lehetőséget a bejárati ajtó menüjében a napló eléréséhez. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Beállíthat riasztásokat az Azure bejárati ajtón?

Igen, az Azure bejárati ajtó támogatja a riasztásokat. A riasztások a metrikák használatára vannak konfigurálva. 

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
