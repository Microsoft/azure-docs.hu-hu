---
title: A SIP-interfész infrastrukturális követelményei – Azure kommunikációs szolgáltatások
description: Ismerje meg az Azure kommunikációs szolgáltatások SIP-interfész konfigurációjának infrastruktúra-követelményeit
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 432a9dab851bda65ecf8736d725b08c5e726ac16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659256"
---
# <a name="sip-interface-infrastructure-requirements"></a>A SIP-interfész infrastruktúrájának követelményei 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Ez a cikk az infrastruktúra, a licencelés és a munkamenet-határ vezérlő (SBC) kapcsolati adatait ismerteti, amelyeket érdemes figyelembe vennie a SIP-interfész üzembe helyezésének megtervezése során.


## <a name="infrastructure-requirements"></a>Infrastruktúrával kapcsolatos követelmények
A SIP-interfész üzembe helyezésének támogatott egybájtos, tartományokra és egyéb hálózati kapcsolatokra vonatkozó követelményei a következő táblázatban láthatók:  

|Infrastruktúra-követelmény|A következőkre lesz szüksége|
|:--- |:--- |
|Munkamenet-szegély vezérlője (SBC)|Támogatott SBC. További információ: [támogatott egybájtos](#supported-session-border-controllers-sbcs).|
|Az SBC-hez csatlakozó telefonos rendszertörzsek|Egy vagy több, az SBC-hez csatlakozó telefonos törzs. Az SBC a SIP felületen keresztül csatlakozik az Azure kommunikációs szolgáltatáshoz. Az SBC harmadik féltől származó telefonos entitásokhoz (például PBX, analóg telefonos adapterekhez stb.) is csatlakozhat. Az SBC-hez csatlakozó PSTN-kapcsolati lehetőségek is működni fognak. (A PSTN-törzseknek az SBC-hez való konfigurálásához tekintse meg az SBC-szállítókat vagy a törzs szolgáltatóit.)|
|Azure-előfizetés|Azure-előfizetés, amely az ACS-erőforrások létrehozásához, valamint az SBC-hez való konfiguráláshoz és kapcsolódáshoz használható.|
|Kommunikációs szolgáltatások hozzáférési jogkivonata|A hívások végrehajtásához érvényes hozzáférési jogkivonatra van szükség `voip` hatókörrel. Lásd: [hozzáférési tokenek](../identity-model.md#access-tokens)|
|Az SBC nyilvános IP-címe|Egy nyilvános IP-cím, amelyet az SBC-hez való kapcsolódáshoz használhat. Az SBC típusa alapján az SBC a NAT-ot is használhatja.|
|Az SBC teljes tartományneve (FQDN)|Az SBC teljes tartományneve, ahol a teljes tartománynév tartomány része nem egyezik a Microsoft 365 vagy az Office 365 szervezet regisztrált tartományával. További információ: SBC- [tartománynevek](#sbc-domain-names).|
|Nyilvános DNS-bejegyzés az SBC számára |Egy nyilvános DNS-bejegyzés az SBC teljes tartománynevét a nyilvános IP-címhez rendeli. |
|Az SBC nyilvános megbízható tanúsítványa |A SIP-felülettel folytatott kommunikációhoz használt SBC tanúsítványa. További információ: [nyilvános megbízható tanúsítvány az SBC-hez](#public-trusted-certificate-for-the-sbc).|
|Tűzfal IP-címei és portjai a SIP-jelek és a média számára |Az SBC a következő szolgáltatásokkal kommunikál a felhőben:<br/><br/>A jelzést kezelő SIP-proxy<br/>Adathordozó-feldolgozó, amely kezeli a médiát<br/><br/>Ez a két szolgáltatás külön IP-címmel rendelkezik a jelen dokumentum későbbi részében ismertetett Microsoft Cloudban.


## <a name="sbc-domain-names"></a>SBC-tartománynevek

Az Office 365 nélküli ügyfelek bármely olyan tartománynevet használhatnak, amelyhez nyilvános tanúsítványt kérhetnek.

A következő táblázat példákat mutat be a bérlőhöz regisztrált DNS-nevekre, függetlenül attól, hogy a név használható-e az SBC teljes tartománynevéként (FQDN), és példák érvényes FQDN-nevekre:

|DNS-név|SBC teljes tartománynevet használhat.|Példa FQDN-nevekre|
|:--- |:--- |:--- |
contoso.com|Igen|**Érvényes nevek:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|Nem|Az *. onmicrosoft.com tartományok használata nem támogatott az SBC-neveknél

Ha Ön Office 365-ügyfél, akkor az SBC-tartomány neve nem egyezhet meg az Office 365-bérlőben regisztrált tartományokban. Az alábbi példa az Office 365 és az Azure kommunikációs szolgáltatás együttes létezését mutatja be:

|Az Office 365-ben regisztrált tartomány|Példák az SBC FQDN-re a csapatokban|Példa SBC FQDN-nevekre az ACS-ben|
|:--- |:--- |:--- |
**contoso.com** (második szintű tartomány)|**SBC.contoso.com** (név a második szintű tartományban)|**SBC.ACS.contoso.com** (név a harmadik szintű tartományban)<br/>**SBC.fabrikam.com** (bármely név különböző tartományon belül)|
|**o365.contoso.com** (harmadik szintű tartomány)|**SBC.o365.contoso.com** (név a harmadik szintű tartományban)|**SBC.contoso.com** (név a második szintű tartományban)<br/>**SBC.ACS.o365.contoso.com** (név a negyedik szintű tartományban)<br/>**SBC.fabrikam.com** (bármely név különböző tartományon belül)

Az SBC-párosítás a kommunikációs szolgáltatások erőforrás-szintjén működik, ami azt jelenti, hogy számos egybájtos egyetlen kommunikációs szolgáltatási erőforráshoz párosítható, de egyetlen SBC több kommunikációs szolgáltatási erőforráshoz is párosítható. A különböző erőforrásokhoz való párosításhoz egyedi SBC teljes tartománynevek szükségesek.

## <a name="public-trusted-certificate-for-the-sbc"></a>Az SBC nyilvános megbízható tanúsítványa

A Microsoft javasolja, hogy az SBC tanúsítványát egy tanúsítvány-aláírási kérelem (CSR) létrehozásával igényelje. A CSR-nek az SBC-hez való létrehozásával kapcsolatos konkrét utasításokért tekintse meg az SBC-szállítók által biztosított összekapcsolási utasításokat vagy dokumentációt. 

  > [!NOTE]
  > A legtöbb hitelesítésszolgáltató (CAs) megköveteli, hogy a titkos kulcs mérete legalább 2048 legyen. Ezt ne feledje el a CSR létrehozásakor.

A tanúsítványnak tartalmaznia kell az SBC FQDN-t a köznapi név (CN) vagy a tulajdonos alternatív neve (SAN) mezőben. A tanúsítványt közvetlenül a hitelesítésszolgáltatótól kell kiadni, nem egy köztes szolgáltatótól.

Másik lehetőségként a kommunikációs szolgáltatások SIP-felülete támogatja a CN-ben és/vagy a SAN-ban található helyettesítő karaktert, és a helyettesítő karakternek meg kell felelnie a szabványos [RFC http](https://tools.ietf.org/html/rfc2818#section-3.1)protokollon keresztül 

Ilyen lehet például, ha `\*.contoso.com` az SBC FQDN-nek felel meg `sbc.contoso.com` , de nem egyezik a következővel: `sbc.test.contoso.com` .

A tanúsítványt a következő legfelső szintű hitelesítésszolgáltatók egyikével kell létrehozni:

- AffirmTrust
- AddTrust külső HITELESÍTÉSSZOLGÁLTATÓI gyökér
- Baltimore CyberTrust-gyökér *
- Buypass
- Cybertrust
- 3. osztály – nyilvános elsődleges hitelesítésszolgáltató
- Comodo – biztonságos legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ
- Deutsche Telekom 
- DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ
- DigiCert magas megbízhatóságú, EV legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja
- Bízza
- GlobalSign
- Go Daddy
- GeoTrust
- VeriSign, Inc. 
- SSL.com
- Csillagmezõ
- Symantec Enterprise Mobile root a Microsoft számára 
- SwissSign
- Thawte időbélyegzővel rendelkező HITELESÍTÉSSZOLGÁLTATÓ
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

A Microsoft az ügyfelek kérései alapján további hitelesítésszolgáltatók hozzáadásán dolgozik. 

## <a name="sip-signaling-fqdns"></a>SIP-jelzés: teljes tartománynevek 

A kommunikációs szolgáltatások SIP-felületének kapcsolódási pontjai a következő három teljes tartománynevek:

- **SIP.pstnhub.microsoft.com** – globális FQDN – először meg kell próbálkozni. Amikor az SBC a név feloldására vonatkozó kérelmet küld, a Microsoft Azure DNS-kiszolgálók egy IP-címet adnak vissza, amely az SBC-hez rendelt elsődleges Azure-adatközpontra mutat. A hozzárendelés az adatközpontok teljesítmény-mérőszámai és az SBC földrajzi közelsége alapján történik. A visszaadott IP-cím megfelel az elsődleges FQDN-nek.
- **sip2.pstnhub.microsoft.com** – másodlagos FQDN – földrajzilag leképezi a második prioritási régiót.
- **sip3.pstnhub.microsoft.com** – tercier FQDN – földrajzilag leképezi a harmadik prioritási régiót.

Ennek a három teljes tartománynevet a következő sorrendben kell elhelyeznie:

- Adja meg az optimális élményt (az első FQDN lekérdezésével kiosztott SBC-adatközponthoz képest kevésbé betöltve és legközelebb).
- Adja meg a feladatátvételt, ha az SBC-vel létesített csatlakozás egy ideiglenes hibát észlelő adatközponthoz van létrehozva. További információ: az alábbi [feladatátvételi mechanizmus](#failover-mechanism-for-sip-signaling) .  

A teljes tartománynevek – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com és sip3.pstnhub.microsoft.com – a következő IP-címek egyikére lesznek feloldva:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Nyissa meg a tűzfal portjait ezekhez az IP-címekhez, hogy engedélyezze a bejövő és kimenő forgalmat a címekre, és onnan a jeleket. Ha a tűzfal támogatja a DNS-neveket, a teljes tartománynév a `sip-all.pstnhub.microsoft.com` következő IP-címekre van feloldva. 

## <a name="sip-signaling-ports"></a>SIP-jelzés: portok

A következő portok használata a kommunikációs szolgáltatások SIP-felületéhez:

|Adatforgalom|Forrás|Művelet|Forrásport|Célport|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP-proxy|SBC|1024 – 65535|Az SBC-ben definiálva (az Office 365 GCC High/DoD csak 5061-es port használata esetén)|
SIP/TLS|SBC|SIP-proxy|Az SBC-ben definiálva|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Feladatátvételi mechanizmus a SIP-jelzéshez

Az SBC DNS-lekérdezést végez a sip.pstnhub.microsoft.com feloldásához. Az SBC helye és az adatközpont teljesítményének mérőszámai alapján az elsődleges adatközpont van kiválasztva. Ha az elsődleges adatközpont problémát észlel, az SBC megpróbálják végrehajtani a sip2.pstnhub.microsoft.com, amely a második hozzárendelt adatközpontra lesz feloldva, és abban az esetben, ha a két régióban található adatközpontok nem érhetők el, az SBC újrapróbálkozik az utolsó FQDN (sip3.pstnhub.microsoft.com), amely biztosítja a harmadlagos adatközpont IP-címét.

## <a name="media-traffic-ip-and-port-ranges"></a>Média forgalma: IP-és porttartomány

Az adathordozó-forgalom a Media Processor nevű különálló szolgáltatás felé irányul. Az ACS-hez készült média-processzor közzétételének pillanatában bármely Azure IP-cím használható. Töltse le [a címek teljes listáját](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Porttartomány
Az adathordozó-processzorok porttartomány a következő táblázatban látható: 

|Adatforgalom|Forrás|Művelet|Forrásport|Célport|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Adathordozó-feldolgozó|SBC|3478-3481 és 49152 – 53247|Az SBC-ben definiálva|
|UDP/SRTP|SBC|Adathordozó-feldolgozó|Az SBC-ben definiálva|3478-3481 és 49152 – 53247|

  > [!NOTE]
  > A Microsoft legalább két portot javasol egy egyidejű híváshoz az SBC-ben.


## <a name="media-traffic-media-processors-geography"></a>Média forgalma: a Media processors földrajza

Az adathordozó-forgalom a Media processors nevű összetevőkön keresztül folyik. Az adathordozó-processzorok a SIP-proxykkal megegyező adatközpontokban vannak elhelyezve. Emellett a Media Flow optimalizálásához további adathordozó-processzorok is használhatók. Például nem rendelkezünk SIP proxy-összetevővel Ausztráliában (SIP-folyamatok Szingapúron vagy Hongkongon keresztül), de a média-feldolgozó helyileg van Ausztráliában. A helyi média-processzorok szükségességét a késéssel kell megszabni, amelyet a forgalom hosszú távú továbbítása okoz, például Ausztráliában, Szingapúrba vagy Hongkongba. Habár az Ausztráliából a Hongkongba vagy Szingapúrba áramló forgalom késése elfogadható a jó hívási minőség megőrzése érdekében a SIP-forgalom számára, a valós idejű adathordozó-forgalom esetében nem.

Hely, ahol a SIP-proxy és a Media Processor-összetevők is telepítve vannak:
- USA (kettő az USA nyugati régiójában és az USA keleti adatközpontjában)
- Európa (Amszterdam és dublini adatközpontok)
- Ázsia (Szingapúr és Hong Kong-adatközpontok)
- Ausztrália (Ausztrália keleti régiója és Délkelet-adatközpontok)

Azok a helyszínek, ahol csak adathordozó-processzorok vannak telepítve (a SIP-folyamatok a fent felsorolt legközelebbi adatközponton keresztül):
- Japán (JP Kelet-és Nyugat-adatközpontok)


## <a name="media-traffic-codecs"></a>Média forgalma: kodekek

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Az SBC és a Cloud Media Processor vagy a Microsoft Teams Client közötti láb.
Mindkét adathordozó-megkerülési esetre és nem megkerülési esetekre vonatkozik.

A munkamenet-határellenőrzési vezérlő és a felhőalapú adathordozó-feldolgozó közötti közvetlen útválasztási interfész a következő kodekeket használhatja:

- SELYEM, G. 711, G. 722, G. 729

Az adott kodeket kényszerítheti a munkamenet-szegély vezérlőn úgy, hogy kizárja a nem kívánatos kodekeket az ajánlatból.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Az ACS SDK-alkalmazás és a Felhőbeli média processzora közötti láb

A Felhőbeli adathordozó processzora és az ACS SDK-alkalmazás közötti, a SILK vagy a G. 722 közötti láb használatos. A láb által választott codec a Microsoft-algoritmusokon alapul, amelyek több paramétert is figyelembe kell venni. 

## <a name="supported-session-border-controllers-sbcs"></a>Támogatott munkamenet-határellenőrzési vezérlők (egybájtos)

A minősítés folyamatban van. Eközben az ügyfelek használhatnak [csapatoknak minősített munkamenet-vezérlőket](/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Következő lépések

### <a name="conceptual-documentation"></a>Fogalmi dokumentáció

- [Telefonos koncepció](./telephony-concept.md)
- [Telefonszám-típusok az Azure kommunikációs szolgáltatásokban](./plan-solution.md)
- [Díjszabás](../pricing.md)

### <a name="quickstarts"></a>Rövid útmutatók

- [Telefonos hívás](../../quickstarts/voice-video-calling/pstn-call.md)