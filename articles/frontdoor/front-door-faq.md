---
title: Azure-beli bejárati ajtó – gyakori kérdések
description: Ez a lap az Azure bejárati ajtóval kapcsolatos gyakori kérdésekre ad választ.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: 77cc509a9fac2a24b3cd70675c1ee4160ecdb24d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741854"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Gyakori kérdések az Azure bejárati ajtóról

Ez a cikk az Azure-előtérben elérhető funkciókkal és funkciókkal kapcsolatos gyakori kérdésekre ad választ. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. A cikk megjegyzések szakasza.
2. [Azure bejárati ajtó UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft ügyfélszolgálata:** Új támogatási kérelem létrehozásához a Azure Portal **Súgó** lapján kattintson a **Súgó + támogatás** gombra, majd válassza az **új támogatási kérelem** lehetőséget.

## <a name="general"></a>Általános kérdések

### <a name="what-is-azure-front-door"></a>Mi az az Azure Front Door?

Az Azure bejárati ajtó egy alkalmazásszolgáltatási hálózat (ADN) szolgáltatás, amely különböző 7. rétegbeli terheléselosztási funkciókat kínál alkalmazásai számára. Dinamikuswebhely-gyorsítást (DSA), valamint globális terheléselosztást biztosít, közel valós idejű feladatátvétellel. Ez egy magas rendelkezésre állású és nagy mértékben skálázható szolgáltatás, amelyet teljes mértékben az Azure felügyel.

### <a name="what-features-does-azure-front-door-support"></a>Milyen funkciókat támogat az Azure bejárati ajtó?

Az Azure bevezető ajtaja támogatja a dinamikus hely gyorsítását (DSA), a TLS/SSL-kiszervezést és a végpontok közötti TLS-t, a webalkalmazási tűzfalat, a cookie-alapú munkamenet-affinitást, az URL-elérésiút-alapú útválasztást, az ingyenes tanúsítványokat és több tartományi A támogatott szolgáltatások teljes listájáért lásd: [Az Azure bejárati ajtó áttekintése](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Mi a különbség az Azure bejárati ajtó és az Azure Application Gateway között?

Míg a bejárati ajtó és a Application Gateway a 7. rétegbeli (HTTP/HTTPS) terheléselosztó, az elsődleges különbség az, hogy a bejárati ajtó globális szolgáltatás, míg a Application Gateway regionális szolgáltatás. Míg a bejárati ajtó terheléselosztást végez a különböző méretezési egységek/fürtök/Stamp egységek között a régiók között, Application Gateway lehetővé teszi a terheléselosztást a virtuális gépek/tárolók, illetve a skálázási egységen belül.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Mikor érdemes üzembe helyezni egy Application Gateway a bejárati ajtó mögött?

A legfontosabb forgatókönyvek, hogy miért érdemes Application Gateway a bejárati ajtó mögött:

- A bevezető ajtó csak globális szinten hajthat végre elérésiút-alapú terheléselosztást, de ha a virtuális hálózatán (VNET) belül még tovább szeretné terheléselosztási forgalmat végezni, akkor a Application Gatewayt kell használniuk.
- Mivel a bejárati ajtó nem működik a virtuális gépek/tárolók szintjén, így nem tudja elvégezni a kapcsolatok kiürítését. Application Gateway azonban lehetővé teszi a kapcsolatok kiürítését. 
- Egy Application Gateway a bejárati ajtó mögött az egyik elérheti a 100%-os TLS/SSL-kiszervezést, és csak HTTP-kérelmeket irányíthat a virtuális hálózaton (VNET) belül.
- A bejárati ajtó és a Application Gateway támogatja a munkamenet-affinitást is. Míg a bejárati ajtó egy adott régióban a felhasználói munkamenetből egy adott fürtre vagy háttérre irányíthatja a további forgalmat, Application Gateway a fürtön belüli ugyanazon kiszolgálóra irányíthatja a forgalmat a affinitize.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Üzembe helyezhető Azure Load Balancer a bejárati ajtó mögött?

Az Azure bevezető ajtajának nyilvános VIP-nek vagy nyilvánosan elérhető DNS-névnek kell lennie ahhoz, hogy átirányítsa a forgalmat. A bejárati ajtó mögötti Azure Load Balancer üzembe helyezése gyakori használati eset.

### <a name="what-protocols-does-azure-front-door-support"></a>Milyen protokollok támogatják az Azure bejárati ajtót?

Az Azure bejárati ajtó támogatja a HTTP, a HTTPS és a HTTP/2 használatát.

### <a name="how-does-azure-front-door-support-http2"></a>Hogyan támogatja az Azure bejárati ajtó a HTTP/2-t?

A HTTP/2 protokoll támogatása csak az Azure bejárati ajtóhoz csatlakozó ügyfelek számára érhető el. A háttérrendszer-készletben található háttérrendszer-kommunikáció HTTP/1.1-n keresztül történik. A HTTP/2 támogatás alapértelmezés szerint engedélyezve van.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Milyen erőforrások támogatottak ma a háttérrendszer-készlet részeként?

A háttér-készletek a következők lehetnek: Storage, Web App, Kubernetes instances vagy bármely más, nyilvános kapcsolattal rendelkező egyedi állomásnév. Az Azure bejárati ajtó megköveteli, hogy a háttérrendszer egy nyilvános IP-címen vagy egy nyilvánosan feloldható DNS-állomásnévn keresztül legyen definiálva. A háttér-készletek tagjai többek között zónák, régiók vagy akár az Azure-on kívül is lehetnek, amíg nyilvános kapcsolattal rendelkeznek.

### <a name="what-regions-is-the-service-available-in"></a>Milyen régiókban érhető el a szolgáltatás?

Az Azure bejárati ajtó globális szolgáltatás, és nem kötődik egyetlen adott Azure-régióhoz sem. A bejárati ajtó létrehozásakor csak az erőforráscsoport helyét kell megadnia, amely alapvetően meghatározza, hogy az erőforráscsoport metaadatainak hol lesznek tárolva. Az előtérben lévő erőforrás maga globális erőforrásként jön létre, és a konfiguráció globálisan települ az összes pop-ra (a jelenléti pontra). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Mik a POP-helyszínek az Azure-beli bejárati ajtón?

Az Azure bejárati ajtaja ugyanazokat a listát tartalmazza, mint a POP (jelenléti pont) helyei a Microsoft Azure CDN. A POPs teljes listájáért tekintse meg [Azure CDN pop-helyszíneket a Microsofttól](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Az Azure bejárati ajtó egy dedikált üzembe helyezés az alkalmazáshoz vagy megosztott az ügyfelek között?

Az Azure bejárati ajtó egy globálisan elosztott, több-bérlős szolgáltatás. Így a bejárati ajtó infrastruktúrája az összes ügyfele között meg van osztva. A bejárati ajtó profiljának létrehozásával azonban megadhatja az alkalmazáshoz szükséges konkrét konfigurációt, és a bejárati ajtón végrehajtott módosítások nem érintik a többi bejárati ajtó konfigurációját.

### <a name="is-http-https-redirection-supported"></a>Támogatott-e a HTTP->HTTPS-átirányítás?

Igen. Valójában az Azure bevezető ajtaja támogatja a gazdagép, az elérési út és a lekérdezési karakterlánc átirányítását, valamint az URL-átirányítás részét. További információ az [URL-átirányításról](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>Milyen sorrendben történik az útválasztási szabályok feldolgozása?

Az előtérben lévő útvonalak nincsenek megrendezve, és egy adott útvonal van kiválasztva a legjobb egyezés alapján. További információ arról [, hogyan illeszkedik a bejárati ajtó a kérelmekhez egy útválasztási szabályhoz](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hogyan lezárni a háttérbeli hozzáférést, hogy csak az Azure bejárati ajtót?

Ha úgy szeretné zárolni az alkalmazást, hogy csak a megadott bejárati ajtóról fogadja a forgalmat, be kell állítania az IP ACL-eket a háttér számára, majd a háttérbeli forgalmat a "X-Azure-FDID" fejléc adott értékére kell korlátoznia. Ezeket a lépéseket az alábbiak szerint részletezjük:

- Konfigurálja az IP-hozzáférés a háttérrendszer számára, hogy fogadja a forgalmat az Azure bejárati ajtó háttérbeli IP-címe és az Azure infrastruktúra-szolgáltatásai között. Tekintse át az alábbi IP-hozzáférés a háttérrendszer:
 
    - Tekintse át az [Azure IP-címtartományok és szolgáltatás-címkék](https://www.microsoft.com/download/details.aspx?id=56519) *AzureFrontDoor. backend* szakaszát az első ajtó IPv4-háttér IP-címéhez, vagy használhatja a *AzureFrontDoor. backend* szolgáltatási címkét is a [hálózati biztonsági csoportokban](../virtual-network/network-security-groups-overview.md#security-rules).
    - Az Azure [alapszintű infrastruktúra-szolgáltatásai](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) virtualizált gazdagép IP-címein keresztül: `168.63.129.16` és `169.254.169.254`

    > [!WARNING]
    > Előfordulhat, hogy a bejárati ajtó háttérbeli IP-címe később is változhat, ezért a megjelenő esetekben biztosítani fogjuk, hogy az [Azure IP-címtartományok és a szolgáltatási címkék](https://www.microsoft.com/download/details.aspx?id=56519)integrálva lennének. Javasoljuk, hogy az [Azure IP-címtartományok és a szolgáltatás-címkék](https://www.microsoft.com/download/details.aspx?id=56519) esetében minden módosítást és frissítést előfizessen.

- Keresse meg az `Front Door ID` értéket az első ajtós portál oldal Áttekintés szakaszában. Ezt követően szűrheti az "**X-Azure-FDID**" bejövő fejlécet, amelyet a rendszer bekapcsol a háttérbe az adott értékkel annak érdekében, hogy csak a saját konkrét ajtós példánya legyen engedélyezve (mivel a fenti IP-címtartományok más ügyfelek más bejárati példányaival is megoszthatók).

- Alkalmazzon szabály-szűrést a háttérbeli webkiszolgálón a forgalom korlátozásához az eredményül kapott X-Azure-FDID fejléc értéke alapján. Vegye figyelembe, hogy egyes szolgáltatások, például a Azure App Service biztosítják ezt a [fejléc-alapú szűrési](../app-service/app-service-ip-restrictions#restrict-access-to-a-specific-azure-front-door-instance-preview) funkciót anélkül, hogy módosítani kellene az alkalmazást vagy a gazdagépet.

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

További információ az [előtérben támogatott HTTP-fejlécekről](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Mennyi ideig tart egy Azure bejárati ajtó üzembe helyezése? A bejárati ajtóm továbbra is működik a frissítéskor?

Egy új bejárati ajtó létrehozása vagy egy meglévő bejárati ajtó frissítése körülbelül 3 – 5 percet vesz igénybe globális üzembe helyezés esetén. Ez azt jelenti, hogy körülbelül 3 – 5 percet vesz igénybe a bejárati ajtó konfigurálása globálisan.

Megjegyzés: az egyéni TLS/SSL-tanúsítvány frissítései körülbelül 30 percet vesznek igénybe globálisan.

Az útvonalak vagy háttér-készletek, stb. minden frissítése zökkenőmentes, és nulla állásidőt eredményez (ha az új konfiguráció helyes). A tanúsítványok frissítései szintén atomi jellegűek, és nem okoznak kimaradást, kivéve, ha a "AFD Managed" típusról "saját tanúsítvány használata" értékre vált, vagy fordítva.


## <a name="configuration"></a>Konfiguráció

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Képes az Azure-beli bejárati terheléselosztásra vagy a virtuális hálózaton belüli forgalom irányítására?

Az Azure bejárati ajtajának (AFD) egy nyilvános IP-címet vagy nyilvánosan feloldható DNS-nevet igényel a forgalom irányításához. Így a válasz nem AFD közvetlenül egy virtuális hálózaton belül, de a két Application Gateway vagy Azure Load Balancer használatával megoldja ezt a forgatókönyvet.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Milyen időtúllépéseket és korlátokat biztosít az Azure bejárati ajtó?

Ismerje meg az Azure-előtérben lévő összes dokumentált [időtúllépést és korlátot](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Mennyi időt vesz igénybe egy szabály, hogy az a bejárati ajtó szabályainak motorba való felvétele után is érvénybe lép?

A szabályok motorjának konfigurációja körülbelül 10 – 15 percet vesz igénybe a frissítés befejezéséhez. A szabály a frissítés befejeződése után is érvénybe lép. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>Konfigurálható Azure CDN az előtérben található profil mögött vagy fordítva?

Az Azure bejárati ajtó és a Azure CDN nem konfigurálható együtt, mert mindkét szolgáltatás ugyanazt az Azure Edge-helyet használja, amikor válaszol a kérelmekre. 

## <a name="performance"></a>Teljesítmény

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hogyan támogatja az Azure bejárati ajtó a magas rendelkezésre állást és a méretezhetőséget?

Az Azure bejárati ajtó egy globálisan elosztott, több-bérlős platform, amely nagy mennyiségű kapacitással gondoskodik az alkalmazás méretezhetőségi igényeiről. A Microsoft globális hálózatának szélétől kezdve a bejárati ajtó globális terheléselosztási képességet biztosít, amely lehetővé teszi, hogy a teljes alkalmazást, vagy akár az egyes régiókban vagy más felhőkben is egyedi szolgáltatást végezzen.

## <a name="tls-configuration"></a>TLS-konfiguráció

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Milyen TLS-verziókat támogat az Azure bejárati ajtó?

A 2019 szeptember után létrehozott összes bejárati profil a TLS 1,2-et használja alapértelmezett minimumként.

A bejárati ajtó támogatja a 1,0, 1,1 és 1,2 TLS-verziókat. A TLS 1,3 még nem támogatott.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Milyen tanúsítványokat támogat az Azure-beli bejárati ajtó?

Ha engedélyezni szeretné, hogy a HTTPS protokoll biztonságos módon kézbesítse a tartalmakat egy bejárati ajtó egyéni tartományán, dönthet úgy, hogy az Azure bejárati ajtó által felügyelt tanúsítványt használ, vagy a saját tanúsítványát használja.
A bejárati ajtó által felügyelt beállítás a Digicert-on keresztül szabványos TLS/SSL-tanúsítványt, valamint a bejárati ajtó Key Vault tárolja. Ha úgy dönt, hogy saját tanúsítványt használ, akkor a tanúsítványokat egy támogatott HITELESÍTÉSSZOLGÁLTATÓTÓL is elvégezheti, és lehet szabványos TLS, kiterjesztett ellenőrzési tanúsítvány vagy akár helyettesítő tanúsítvány is. Az önaláírt tanúsítványok nem támogatottak. Megtudhatja [, hogyan engedélyezheti a HTTPS-t egy egyéni tartományhoz](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Támogatja a bejárati ajtó a tanúsítványok autorotációját?

Az előtérben felügyelt tanúsítvány beállításnál a tanúsítványokat a rendszer automatikusan elforgatja a bejárati ajtón. Ha egy bejárati ajtót felügyelt tanúsítványt használ, és azt látja, hogy a tanúsítvány lejárati dátuma kevesebb, mint 60 nap múlva, a támogatási jegyet.
</br>Saját egyéni TLS/SSL-tanúsítvány esetén az autorotáció nem támogatott. Az adott egyéni tartományhoz való első beállításhoz hasonlóan a Key Vault a megfelelő tanúsítvány-verzióra kell mutatnia, és biztosítania kell, hogy az első ajtóhoz tartozó szolgáltatásnév továbbra is hozzáférhessen a Key Vaulthoz. Ez a tanúsítvány bevezetési művelete a bejárati ajtónál atomi, és nem okoz semmilyen éles hatást, ha a tulajdonos neve vagy a SAN nem változik a tanúsítványban.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Milyen titkosítási csomagokat támogat az Azure bejárati ajtó?

A TLS 1.2 esetén a következő titkosítási csomagok támogatottak: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Ha a TLS 1.0/1.1-es verzióval rendelkező egyéni tartományok használata engedélyezve van, a következő titkosítási csomagok támogatottak:

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

Az Azure-ban a minimális TLS-verziót az egyéni tartomány HTTPS-beállításai között Azure Portal vagy az [azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)használatával konfigurálhatja. Jelenleg a 1,0 és a 1,2 közötti választásra van lehetőség.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Beállítható a bejárati ajtó, hogy csak bizonyos titkosítási csomagokat támogasson?

Nem, az adott titkosítási csomagok bejáratának konfigurálása nem támogatott. Saját TLS/SSL-tanúsítványát azonban a hitelesítésszolgáltatótól szerezheti be (mondjuk a VeriSign, a Trust vagy a Digicert), és adott titkosítási lakosztállyal rendelkezik a tanúsítványban, amikor az létrejött. 

### <a name="does-front-door-support-ocsp-stapling"></a>Támogatja az előtérben az OCSP-tűzést?

Igen, a bejárati ajtó alapértelmezés szerint támogatja az OCSP-tűzést, és nincs szükség konfigurációra.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Támogatja az Azure bejárati ajtó a háttérbeli forgalom újratitkosítását is?

Igen, az Azure bejárati ajtó támogatja a TLS/SSL-kiszervezést és a végpontok közötti TLS-t, amely újratitkosítja a forgalmat a háttérbe. Valójában, mivel a háttérrel létesített kapcsolatok a nyilvános IP-címeken keresztül történnek, javasoljuk, hogy konfigurálja a bejárati ajtót a HTTPS használatára továbbítási protokollként.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Támogatja a bejárati ajtó a HTTPS-kapcsolathoz tartozó önaláírt tanúsítványokat?

Nem, az önaláírt tanúsítványok nem támogatottak a bejárati ajtón, és a korlátozás a következőkre is vonatkozik:

1. **Háttérrendszer**: az önaláírt tanúsítványok nem használhatók, ha a forgalmat HTTPS vagy https állapotú mintavételként továbbítja, vagy a gyorsítótárat a forrástól az útválasztási szabályokhoz a gyorsítótárazás engedélyezve beállítással tölti ki.
2. Előtér **: nem** használhat önaláírt tanúsítványokat saját egyéni TLS/SSL-tanúsítvány használata esetén a https engedélyezéséhez az egyéni tartományon.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Miért nem sikerül HTTPS-forgalmat felvenni a háttérben?

Ahhoz, hogy sikeresen HTTPS-kapcsolatot lehessen létesíteni a háttérrel, hogy az állapot-és a továbbítási kérelmeket, két oka lehet a HTTPS-forgalom meghibásodásának:

1. A **tanúsítvány tulajdonosának neve nem egyezik**: a HTTPS-kapcsolatok esetében a bejárati ajtó azt várja, hogy a háttérrendszer a háttérbeli állomásnévvel egyező tulajdonosi névvel rendelkező érvényes hitelesítésszolgáltatótól származó tanúsítványt jelenít meg. Ha például a háttérbeli állomásnév be van állítva, `myapp-centralus.contosonews.net` és az a tanúsítvány, amelyet a háttér a TLS-kézfogás során ad meg `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` , sem a tulajdonos, sem a tulajdonos nevében, akkor a bejárati ajtó elutasítja a kapcsolódást, és hibát eredményez. 
    1. **Megoldás**: noha a megfelelőség szempontjából nem ajánlott, megkerülheti ezt a hibát, ha letiltja a tanúsítvány tulajdonosának nevének ellenőrzését az előtérben. Ez a Azure Portal és az API BackendPoolsSettings területén található beállítások területen érhető el.
2. A **tanúsítvány érvénytelen hitelesítésszolgáltatótól való üzemeltetése**: csak az [érvényes hitelesítésszolgáltatóktól](./front-door-troubleshoot-allowed-ca.md) származó tanúsítványok használhatók a háttérbe a bejárati ajtónál. A belső hitelesítésszolgáltatóktól vagy önaláírt tanúsítványokból származó tanúsítványok nem engedélyezettek.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Használhatok ügyfél/kölcsönös hitelesítést az Azure bejárati ajtaján?

Nem. Bár az Azure bejárati ajtaja támogatja a TLS 1,2-et, amely az [RFC 5246](https://tools.ietf.org/html/rfc5246)-ben bevezette az ügyfél/kölcsönös hitelesítést, az Azure bejárati ajtó jelenleg nem támogatja az ügyfél/kölcsönös

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Milyen típusú metrikák és naplók érhetők el az Azure-beli bejárati ajtón?

A naplókról és az egyéb diagnosztikai képességekről további információt a [metrikák és naplók a bejárati ajtóhoz való figyelését](front-door-diagnostics.md)ismertető témakörben talál.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Mi az adatmegőrzési szabályzat a diagnosztikai naplókon?

A diagnosztikai naplók a Customers Storage-fiókba áramlanak, és az ügyfelek megadhatják az adatmegőrzési szabályzatot a preferencia alapján. A diagnosztikai naplókat egy Event hub-vagy Azure Monitor-naplóba is lehet elküldeni. További információ: [Azure bejárati ajtó diagnosztika](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hogyan naplók beszerzése az Azure bejárati ajtóhoz?

A naplók elérhetők az Azure-előtérben. A portálon kattintson a **tevékenység napló** elemre a bejárati ajtó menüjében a napló eléréséhez. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Beállíthat riasztásokat az Azure bejárati ajtón?

Igen, az Azure bejárati ajtó támogatja a riasztásokat. A riasztások a metrikák használatára vannak konfigurálva. 

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
