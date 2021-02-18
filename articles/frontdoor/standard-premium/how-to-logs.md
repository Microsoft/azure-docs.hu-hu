---
title: Azure bejárati ajtó standard/prémium (előzetes verzió) naplózása
description: Ez a cikk azt ismerteti, hogyan működik a naplózás az Azure bejárati ajtó standard/Premium verziójában.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2020
ms.author: duau
ms.openlocfilehash: 6a1cf3112cd936ec842c755eb90b2c7b094aa781
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098858"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) naplózása

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtó különböző naplózási lehetőségeket biztosít a bejárati ajtó nyomon követéséhez, figyeléséhez és hibakereséséhez. 

* A hozzáférési naplók részletes információkkal szolgálnak a AFD által fogadott és a hozzáférési minták elemzéséhez és figyeléséhez, valamint a hibakeresési problémákhoz szükséges összes kérelemről. 
* A Tevékenységnaplók biztosítják az Azure-erőforrásokon végzett műveletek láthatóságát.  
* Az állapot-mintavételi naplók az összes sikertelen mintavétel naplóit biztosítják a forráshoz. 
* A webalkalmazási tűzfal (WAF) naplói részletes információkat biztosítanak az Azure bejárati végpontjának észlelési vagy megelőzési módjában naplózott kérelmekről. A WAF-sel konfigurált egyéni tartomány ezen naplókon keresztül is megtekinthető.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A hozzáférési naplók, az állapot-mintavételi naplók és a WAF-naplók alapértelmezés szerint nem engedélyezettek. A naplózás engedélyezéséhez kövesse az alábbi lépéseket. A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg. A naplók késése akár néhány percet is igénybe vehet. 

A naplók tárolásához három lehetőség közül választhat: 

* **Storage-fiók:** A Storage-fiókokat leginkább olyan helyzetekben érdemes használni, amikor a naplókat hosszabb ideig tárolják, és szükség esetén áttekintik. 
* **Event hubok:** Az Event hubok nagyszerű lehetőséget biztosít a más biztonsági információk és eseménykezelő (SIEM) eszközök vagy külső adattárak integrálására. Például: splunk/Datadoggal/Sumo. 
* **Azure log Analytics:** A Azure Monitor Azure Log Analytics az Azure bejárati ajtó teljesítményének általános valós idejű figyelésére és elemzésére szolgál.

## <a name="configure-logs"></a>Naplók konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keressen az Azure bejárati ajtó standard/Premium kifejezésre, és válassza ki az Azure bejárati profilját.

1. A profilban lépjen a **figyelés** elemre, és válassza a **diagnosztikai beállítás** elemet. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Képernyőkép a diagnosztikai beállítások kezdőlapján.":::

1. A **diagnosztikai beállítások** területen adja meg a  **diagnosztikai beállítások nevének** nevét.

1. Válassza ki a  **naplót** a **FrontDoorAccessLog**, a **FrontDoorHealthProbeLog** és a **FrontDoorWebApplicationFirewallLog**.

1. Válassza ki a **célhely részleteit**. A cél beállításai a következők: 

    * **Küldés a Log Analyticsnek**
        * Válassza ki az *előfizetést* és a *log Analytics munkaterületet*.
    * **Archiválás egy Storage-fiókba**
        * Válassza ki az *előfizetést* és a *Storage-fiókot*. és állítsa be a **megőrzési időt (nap)**.
    * **Streamelés eseményközpontba**
        * Válassza ki az *előfizetést, az Event hub-névteret, az Event hub nevét (nem kötelező)* és az *Event hub-szabályzat nevét*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="A diagnosztikai beállítások oldal képernyőképe.":::

1. Kattintson a **Save (Mentés**) gombra.

## <a name="access-log"></a>Hozzáférési napló

Az Azure bejárati ajtaja jelenleg külön API-kérelmeket biztosít minden olyan bejegyzéshez, amely a következő sémával rendelkezik, és JSON formátumban van naplózva az alábbiak szerint. 

| Tulajdonság | Leírás |
|----------|-------------| 
| TrackingReference | A AFD által kiszolgált kérelmet azonosító egyedi hivatkozási karakterlánc, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |
| Idő | Az a dátum és idő, amikor a AFD Edge a kért tartalmat az ügyfélnek (UTC) szállította. |
| HttpMethod | A kérelem által használt HTTP-metódus: DELETE, GET, HEAD, OPTIONs, PATCH, POST vagy PUT. |
| HttpVersion | A kérelemben megadott megjelenítőnek a HTTP-verziója. |
| RequestUri | A fogadott kérelem URI-ja. Ez a mező a teljes séma, a port, a tartomány, az elérési út és a lekérdezési karakterlánc. |
| HostName | Az állomásnév az ügyféltől érkező kérésben. Ha engedélyezi az egyéni tartományokat, és helyettesítő tartománnyal (*. contoso.com) rendelkezik, a hostname értéke a.contoso.com. Ha Azure-beli bejárati tartományt (contoso.azurefd.net) használ, az állomásnév a contoso.azurefd.net. |
| RequestBytes | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét. A kérelemben szereplő megjelenítőben tárolt adatbájtok száma, beleértve a fejléceket is. |
| ResponseBytes | A háttér-kiszolgáló által válaszként küldött bájtok. |
| UserAgent | Az ügyfél által használt böngésző típusa. |
| ClientIp (Ügyfél IP-címe) | Annak az ügyfélnek az IP-címe, amely az eredeti kérelmet elvégezte. Ha a kérelemben a fejléchez X-továbbítás történt, akkor az ügyfél IP-címe azonos. |
| SocketIp | Az AFD Edge-hez való közvetlen kapcsolódás IP-címe. Ha az ügyfél egy HTTP-proxy vagy egy terheléselosztó használatával küldi el a kérelmet, a SocketIp értéke a proxy vagy a terheléselosztó IP-címe. |
| Késés | Az idő AFD Edge-kiszolgálótól érkező kérés arra az időre érkezik, ameddig a AFD az ügyfélnek küldött válasz utolsó bájtját (ezredmásodpercben) fogadja. Ez a mező nem veszi figyelembe a hálózati késést és a TCP-pufferelést. |
| RequestProtocol | Az ügyfél által a kérelemben megadott protokoll: HTTP, HTTPS. |
| SecurityProtocol | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás. A lehetséges értékek a következők: SSLv3, TLSv1, TLS 1.1, TLS 1.2 |
| SecurityCipher | Ha a kérelem protokolljának értéke HTTPS, ez a mező jelzi az ügyfél által egyeztetett TLS/SSL-titkosítást, valamint a titkosítás AFD. |
| Végpont | A AFD végpont tartományneve, például contoso.z01.azurefd.net |
| HttpStatusCode | A AFD által visszaadott HTTP-állapotkód. |
| Pop | A felhasználói kérésre válaszoló Edge-pop.  |
| Gyorsítótár állapota | Azt az állapotkódot adja meg, hogy a rendszer hogyan kezeli a kérést a CDN szolgáltatás által, amikor gyorsítótárazásra kerül. A lehetséges értékek a következők: a HTTP-kérelem a AFD Edge POP-gyorsítótárból lett kézbesítve. <br> **Miss**: a HTTP-kérelem forrása a forrás. <br/> **PARTIAL_HIT**: a kérelemből származó bájtok némelyike az AFD Edge pop-gyorsítótárból szolgált, míg a forrásként kapott bájtok némelyike kiszolgált az objektum-darabolási forgatókönyvekhez. <br> **CACHE_NOCONFIG**: kérelmek továbbítása gyorsítótárazási beállítások nélkül, beleértve a megkerülési forgatókönyvet. <br/> **PRIVATE_NOSTORE**: nincs gyorsítótár konfigurálva az ügyfelek gyorsítótárazási beállításaiban. <br> **REMOTE_HIT**: a kérést a szülő csomópont gyorsítótára kézbesíti. <br/> **N/A**:* * kérelem, amely az aláírt URL-cím és a beállított szabályok által meg lett tagadva. |
| MatchedRulesSetName | A feldolgozott szabályok nevei. |
| Z | Annak az útvonalnak a neve, amelyhez a kérelem illeszkedik. |
| ClientPort | Az ügyfél IP-portja, amely a kérést elvégezte. |
| Referrer | A kérést kezdeményező hely URL-címe |
| TimetoFirstByte | A AFD ezredmásodpercben megadott időtartama fogadja a kérést arra az időre, amikor az első bájt megkapja az ügyfelet az Azure-beli bejárati ajtón mérve. Ez a tulajdonság nem méri az ügyféladatok mennyiségét. |
| ErrorInfo | Ez a mező az egyes válaszokhoz tartozó hiba-jogkivonat részletes információit tartalmazza. <br> Nincs **hiba**: azt jelzi, hogy nem található hiba. <br> **CertificateError**: általános SSL-tanúsítvány hiba. <br> **CertificateNameCheckFailed**: az SSL-tanúsítványban található állomásnév érvénytelen vagy nem egyezik. <br> **ClientDisconnected**: az ügyfél hálózati kapcsolatai miatt sikertelen volt a kérelem. <br> **ClientGeoBlocked**: az ügyfél le lett tiltva az IP-cím földrajzi elhelyezkedése miatt. <br> **UnspecifiedClientError**: általános ügyfél-hiba. <br> **InvalidRequest**: érvénytelen kérelem. Előfordulhat, hogy helytelen formátumú fejléc, törzs és URL-cím miatt fordul elő. <br> **DNSFailure**: DNS-hiba. <br> **DNSTimeout**: a háttérrendszer feloldására szolgáló DNS-lekérdezés időkorlátja lejárt. <br> **DNSNameNotResolved**: a kiszolgáló neve vagy címe nem oldható fel. <br> **OriginConnectionAborted**: a forrással létesített kapcsolat rendellenes módon le lett választva. <br> **OriginConnectionError**: általános forrással kapcsolatos hiba történt. <br> **OriginConnectionRefused**: a forrással létesített kapcsolatok nem lettek létrehozva. <br> **OriginError**: általános eredetű hiba. <br> **OriginInvalidRequest**: a rendszer érvénytelen kérelmet küld a forrásnak. <br> **ResponseHeaderTooBig**: a forrás túl nagy értéket adott vissza a válasz fejlécében. <br> A **OriginInvalidResponse**:* * forrás érvénytelen vagy ismeretlen választ adott vissza. <br> **OriginTimeout**: a forrás kérelemre vonatkozó időtúllépési időszak lejárt. <br> **ResponseHeaderTooBig**: a forrás túl nagy értéket adott vissza a válasz fejlécében. <br> **RestrictedIP**: a kérést a rendszer a korlátozott IP-cím miatt blokkolta. <br> **SSLHandshakeError**: nem sikerült kapcsolatot létesíteni a forrással az SSL-Shake-hiba miatt. <br> **SSLInvalidRootCA**: a RootCA érvénytelen volt. <br> **SSLInvalidCipher**: a titkosítás érvénytelen, mert a HTTPS-kapcsolat létrejött. <br> **OriginConnectionAborted**: a forrással létesített kapcsolat rendellenes módon le lett választva. <br> **OriginConnectionRefused**: a forrással létesített kapcsolatok nem lettek létrehozva. <br> **UnspecifiedError**: hiba történt, amely nem felelt meg a tábla egyik hibájának sem. |
| OriginURL | Azon forrás teljes URL-címe, ahol a kérelmeket küldik. A séma, a állomásfejléc, a port, az elérési út és a lekérdezési karakterlánc tagjai. <br> **URL-cím újraírása**: Ha a szabálykészlet URL-cím újraírására vonatkozó szabály van megadva, az elérési út az átírásos útvonalra hivatkozik. <br> **Gyorsítótár az Edge-pop-on** Ha az Edge-POP-on található gyorsítótár-találat, a forrás értéke N/A. <br> **Nagyméretű kérelem** Ha a kért tartalom nagyméretű, és a rendszer több, a forráshoz visszatérve visszahelyezi a feldarabolt kérést, akkor ez a mező a forráshoz tartozó első kérésnek felel meg. További információért lásd az objektumok darabolását ismertető témakört. |
| OriginIP | A kérelmet kézbesítő forrás IP-cím. <br> **Gyorsítótár-találat a PEREMHÁLÓZATI pop-on** Ha az Edge-POP-on található gyorsítótár-találat, a forrás értéke N/A. <br> **Nagyméretű kérelem** Ha a kért tartalom nagyméretű, és a rendszer több, a forráshoz visszatérve visszahelyezi a feldarabolt kérést, akkor ez a mező a forráshoz tartozó első kérésnek felel meg. További információért lásd az objektumok darabolását ismertető témakört. |
| OriginName| A teljes DNS-név (állomásnév a forrás URL-címben) a forráshoz. <br> **Gyorsítótár-találat a PEREMHÁLÓZATI pop-on** Ha az Edge-POP-on található gyorsítótár-találat, a forrás értéke N/A. <br> **Nagyméretű kérelem** Ha a kért tartalom nagyméretű, és a rendszer több, a forráshoz visszatérve visszahelyezi a feldarabolt kérést, akkor ez a mező a forráshoz tartozó első kérésnek felel meg. További információért lásd az objektumok darabolását ismertető témakört. |

## <a name="health-probe-log"></a>Állapot mintavételi naplója

Az állapot-mintavételi naplók naplózást biztosítanak minden sikertelen mintavételhez a forrás diagnosztizálásának elősegítése érdekében.A naplók információkkal szolgálnak, amelyek segítségével visszaállíthatja a forrást a szolgáltatásba. A napló hasznos lehet a következő esetekben:

* Észrevette, hogy az Azure-beli előtérben lévő forgalom egy része lett küldve. Például csak három négy, a forgalmat fogadó eredet. Tudni szeretné, hogy az eredetek megkapják-e a mintavételeket, és nem a hiba okát.  

* Észrevette, hogy a forrás állapota (%) a vártnál alacsonyabb, és szeretné tudni, hogy melyik forrást sikerült leállítani, és a hiba okát.

### <a name="health-probe-log-properties"></a>Állapot mintavételi naplójának tulajdonságai

Az egyes állapot-mintavételi naplók a következő sémával rendelkeznek.

| Tulajdonság | Leírás |
| --- | --- |
| HealthProbeId  | A kérelem azonosítására szolgáló egyedi azonosító. |
| Idő | Mintavétel teljes ideje |
| HttpMethod | Az állapot mintavételi kérelme által használt HTTP-metódus. Az értékek a GET és a HEAD értékkel rendelkeznek, az állapot-mintavételi konfigurációk alapján. |
| Eredmény | Az állapot-mintavétel eredete, az érték tartalmazza a sikeres és egyéb hibaüzeneteket. |
| HttpStatusCode  | A forrás által visszaadott HTTP-állapotkód. |
| ProbeURL (cél) | Azon forrás teljes URL-címe, ahol a kérelmeket küldik. A séma, a állomásfejléc, az elérési út és a lekérdezési karakterlánc tagjai. |
| OriginName  | Az a forrás, ahová a rendszer elküldi a kérelmeket. Ez a mező segít megtalálni a kamatok eredetét, ha a forrás a FQDN van konfigurálva. |
| POP | A mintavételi kérést küldő Edge pop. |
| Forrás IP-cím | Célként megadott forrás IP-címe. Ez a mező akkor hasznos, ha a FQDN használatával konfigurálja a kamatok eredetét. |
| TotolaLatency | Az AFDX Edge-től származó idő az utolsó választ a AFDX Edge-nek küldi el. |
| ConnectionLatency| A TCP-kapcsolatok beállítására fordított időtartam a HTTP-mintavételi kérelem forrásként való elküldéséhez. | 
| DNSResolution késés | A DNS-feloldásra fordított időtartam, ha a forrás IP-cím helyett FQDN van konfigurálva. N/A, ha a forrás IP-re van konfigurálva. |

### <a name="health-probe-log-sample-in-json"></a>Állapot mintavételi naplójának mintája JSON-ban

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Tevékenységnaplók

A Tevékenységnaplók az Azure bejárati ajtó standard/prémium szintű műveleteivel kapcsolatos információkat biztosítanak. A naplók részletesen ismertetik, hogy mi, ki és mikor történt írási művelet az Azure-beli bejárati ajtón. 

> [!NOTE]
> A tevékenységi naplók nem tartalmaznak lekérési műveleteket. Emellett nem tartalmazzák a Azure Portal vagy az eredeti felügyeleti API használatával végrehajtott műveleteket. 

A hozzáférési tevékenység naplóit a bejárati ajtón vagy az Azure-erőforrások összes naplóján Azure Monitor.

A tevékenységnaplók megtekintése:

1. Válassza ki az előtérben lévő profilt.

1. Válassza a **műveletnapló lehetőséget.**

1. Válasszon egy szűrési hatókört, majd kattintson az **alkalmaz** gombra.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [Azure bejárati ajtó standard/prémium (előzetes verzió) jelentéseit](how-to-reports.md).
- Ismerje meg az [Azure bejárati ajtó standard/Premium (előzetes verzió) valós idejű figyelési mérőszámait](how-to-monitor-metrics.md).
