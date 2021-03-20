---
title: Metrikák és naplók figyelése az Azure bejárati ajtóban | Microsoft Docs
description: Ez a cikk az Azure-előtérben támogatott különböző mérőszámokat és hozzáférési naplókat ismerteti
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 58db217ca173acbe0356596de916216c4ab7f241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715547"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Metrikák és naplók figyelése az Azure-beli bejárati ajtón

Az Azure bejárati ajtót használva a következő módokon figyelheti az erőforrásokat:

- **Metrikák**. Az Azure bejárati ajtajának jelenleg nyolc mérőszáma van a teljesítményszámlálók megtekintéséhez.
- **Naplók**. A tevékenység-és diagnosztikai naplók lehetővé teszik a teljesítmény, hozzáférés és egyéb adatok mentését, illetve az erőforrásokból való felhasználását figyelés céljából.

##  <a name="metrics"></a><a name="metrics"></a>Mérőszámok

A metrikák bizonyos Azure-erőforrások, amelyek lehetővé teszik a teljesítményszámlálók megtekintését a portálon. A következő elérhetők a bejárati ajtó metrikái:

| Metric | Metrika megjelenítendő neve | Unit (Egység) | Dimenziók | Description |
| --- | --- | --- | --- | --- |
| RequestCount | Kérelmek száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | A bejárati ajtó által kiszolgált ügyfél-kérelmek száma.  |
| RequestSize | Kérelem mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelektől a befelé irányuló kérelmekként küldött bájtok száma. |
| ResponseSize | Válasz mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | A bejárati ajtótól az ügyfeleknek küldött válaszként küldött bájtok száma. |
| TotalLatency | Teljes késés | Ezredmásodpercben | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfél kérésének teljes ideje, amely a AFD-ből az ügyfélnek küldött utolsó válasz bájtig érkezett. |
| BackendRequestCount | Háttérbeli kérelmek száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>Háttérrendszer | A bejárati ajtóról a háttérbe küldött kérések száma. |
| BackendRequestLatency | Háttérbeli kérelmek késése | Ezredmásodpercben | Háttérrendszer | A kérelemnek a háttérbe való beérkezése után kiszámított idő, amíg be nem érkezett az utolsó válasz bájtja a háttérből. |
| BackendHealthPercentage | Háttér állapotának százalékos aránya | Százalék | Háttérrendszer</br>Háttérkészletek | A sikeres állapot-mintavételek százalékos aránya a bejárati ajtóról a háttérbe. |
| WebApplicationFirewallRequestCount | Webalkalmazási tűzfalra vonatkozó kérelmek száma | Darabszám | PolicyName</br>RuleName</br>Művelet | Az alkalmazási rétegben a bejárati ajtó biztonságával feldolgozott ügyfélalkalmazások száma |

## <a name="activity-logs"></a><a name="activity-log"></a>Tevékenységnaplók

A Tevékenységnaplók a bejárati ajtókon végzett műveletekről nyújtanak információt. Azt is meghatározzák, hogy mi, ki és mikor történik a bejáratkor végrehajtott írási műveletek (Put, post vagy DELETE).

>[!NOTE]
>A Tevékenységnaplók nem tartalmaznak olvasási (Get) műveleteket. Emellett nem tartalmazzák a Azure Portal vagy az eredeti felügyeleti API használatával végrehajtott műveleteket.

A hozzáférési tevékenység naplóit a bejárati ajtón vagy az Azure-erőforrások összes naplóján Azure Monitor. A tevékenységnaplók megtekintése:

1. Válassza ki az előtérben lévő példányt.
2. Válassza a **műveletnapló** lehetőséget.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Tevékenységnapló":::

3. Válasszon egy szűrési hatókört, majd kattintson az **alkalmaz** gombra.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnosztikai naplók
A diagnosztikai naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A diagnosztikai naplók eltérnek a tevékenység naplóitól.

A Tevékenységnaplók betekintést nyújtanak az Azure-erőforrásokon végzett műveletekre. A diagnosztikai naplók betekintést nyújtanak az erőforrás által végzett műveletekre. További információ: [Azure monitor diagnosztikai naplók](../azure-monitor/essentials/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Diagnosztikai naplók":::

Diagnosztikai naplók konfigurálása a bejárati ajtóhoz:

1. Válassza ki az Azure-bejárati ajtót.

2. Válassza a **diagnosztikai beállítások** lehetőséget.

3. Kattintson **a diagnosztika bekapcsolása** elemre. Archiválja a diagnosztikai naplókat, valamint mérőszámokat egy Storage-fiókba, továbbíthatja őket egy Event hubhoz, vagy elküldheti azokat Azure Monitor naplókba.

A bejárati ajtó jelenleg biztosít diagnosztikai naplókat (óránként kötegelt feldolgozással). A diagnosztikai naplók egyedi API-kérelmeket biztosítanak minden egyes bejegyzéshez a következő sémával:

| Tulajdonság  | Leírás |
| ------------- | ------------- |
| BackendHostname | Ha a kérést egy háttérbe továbbították, ez a mező a háttér állomásnevét jelöli. Ez a mező üresen marad, ha a kérés átirányítása vagy továbbítása regionális gyorsítótárba történik (ha a gyorsítótárazás engedélyezve van az útválasztási szabályhoz). |
| CacheStatus | Gyorsítótárazási forgatókönyvek esetén ez a mező a legördülő menüben definiált gyorsítótár-találatok és-kihagyás |
| ClientIp (Ügyfél IP-címe) | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. Ha a kérelemben a fejléchez X-továbbítás történt, akkor az ügyfél IP-címe azonos. |
| ClientPort | Az ügyfél IP-portja, amely a kérést elvégezte. |
| HttpMethod | A kérelem által használt HTTP-metódus. |
| HttpStatusCode | A proxy által visszaadott HTTP-állapotkód. |
| HttpStatusDetails | Az eredményül kapott állapot a kérelemben. A karakterlánc értékének jelentése az állapot-hivatkozási táblában található. |
| HttpVersion | A kérelem vagy a kapcsolatok típusa. |
| POP | A kérelem által kirakodott szegély rövid neve. |
| RequestBytes | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét. |
| RequestUri | A fogadott kérelem URI-ja. |
| ResponseBytes | A háttér-kiszolgáló által válaszként küldött bájtok.  |
| RoutingRuleName | Annak az útválasztási szabálynak a neve, amelyhez a kérelem illeszkedik. |
| RulesEngineMatchNames | A kérelemnek megfelelő szabályok nevei. |
| SecurityProtocol | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás. |
| SentToOriginShield </br> (elavult) * **tekintse meg a következő szakaszban szereplő, az elavult megjegyzések című szakaszt.**| Ha az értéke igaz, az azt jelenti, hogy a rendszer a lekérést a peremhálózati pop helyett a forrás pajzs gyorsítótárából választta. A Origin Shield egy szülő gyorsítótár, amely a gyorsítótár találati arányának növelésére szolgál. |
| isReceivedFromClient | Ha az értéke igaz, az azt jelenti, hogy a kérelem az ügyféltől érkezett. Ha false (hamis) értékű, a kérelem a szélén (gyermek POP) található, és a forrás pajzstól (szülő POP) válaszol. |
| Eltelt idő | A kérelem első bájtjában eltelt idő (másodpercben) a válasz utolsó bájtjában. |
| TrackingReference | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |
| UserAgent | Az ügyfél által használt böngésző típusa. |
| ErrorInfo | Ez a mező a további hibaelhárítási hibák adott típusát tartalmazza. </br> A lehetséges értékek a következők: </br> Nincs **hiba**: azt jelzi, hogy nem található hiba. </br> **CertificateError**: általános SSL-tanúsítvány hiba.</br> **CertificateNameCheckFailed**: az SSL-tanúsítványban található állomásnév érvénytelen vagy nem egyezik. </br> **ClientDisconnected**: az ügyfél hálózati kapcsolatai miatt sikertelen volt a kérelem. </br> **UnspecifiedClientError**: általános ügyfél-hiba. </br> **InvalidRequest**: érvénytelen kérelem. Előfordulhat, hogy helytelen formátumú fejléc, törzs és URL-cím miatt fordul elő. </br> **DNSFailure**: DNS-hiba. </br> **DNSNameNotResolved**: a kiszolgáló neve vagy címe nem oldható fel. </br> **OriginConnectionAborted**: a forrással létesített kapcsolatok váratlanul leálltak. </br> **OriginConnectionError**: általános forrással kapcsolatos hiba történt. </br> **OriginConnectionRefused**: a forrással létesített kapcsolódás nem sikerült. </br> **OriginError**: általános eredetű hiba. </br> **OriginInvalidResponse**: a forrás érvénytelen vagy ismeretlen választ adott vissza. </br> **OriginTimeout**: a forrás kérelemre vonatkozó időtúllépési időszak lejárt. </br> **ResponseHeaderTooBig**: a forrás túl nagy értéket adott vissza a válasz fejlécében. </br> **RestrictedIP**: a kérést a rendszer a korlátozott IP-cím miatt blokkolta. </br> **SSLHandshakeError**: nem sikerült kapcsolatot létesíteni a forrással az SSL-Shake-hiba miatt. </br> **UnspecifiedError**: hiba történt, amely nem felelt meg a tábla egyik hibájának sem. |

### <a name="sent-to-origin-shield-deprecation"></a>Elküldve a Origin Shield elavulttá
A RAW log tulajdonság **isSentToOriginShield** elavult, és egy új mező **isReceivedFromClient** vált. Ha már használja az elavult mezőt, használja az új mezőt. 

A nyers naplók közé tartoznak a CDN Edge (Child POP) és a Origin Shield által létrehozott naplók. A Origin Shield olyan szülő csomópontokra utal, amelyek stratégiailag a világ minden részén találhatók. Ezek a csomópontok a forrás-kiszolgálókkal kommunikálnak, és csökkentik a forgalom terhelését a forrásnál. 

A forrásként szolgáló védelemre kerülő minden kérelem esetében 2 naplós bejegyzés található:

* Egy az Edge-csomópontok számára
* Az egyik a Origin Shield. 

Az Edge-csomópontok és a forrás pajzs alapján a kilépések és válaszok megkülönböztetése érdekében a megfelelő adatok beolvasásához használja a **isReceivedFromClient** mezőt. 

Ha az érték hamis, akkor az azt jelenti, hogy a kérést a rendszer a forrás pajzsról az Edge-csomópontokra válaszol. Ez a megközelítés hatékonyan hasonlítható össze a számlázási adattal rendelkező nyers naplók összehasonlításával. Nem számítunk fel díjat a forrásként szolgáló pajzs és a peremhálózati csomópont közötti kimenő forgalomért. A peremhálózati csomópontok és az ügyfelek közötti kimenő forgalomért számítunk fel díjat. 

**Kusto lekérdezési minta a forrásként szolgáló Log Analyticsban generált naplók kizárásához.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> A különböző útválasztási konfigurációk és a forgalom viselkedése érdekében egyes mezők, például a backendHostname, a cacheStatus, a isReceivedFromClient és a POP mező különböző értékekkel is reagálhat. Az alábbi táblázat ismerteti azokat a különböző értékeket, amelyeket ezek a mezők különböző forgatókönyvekhez fognak tartalmazni:

| Forgatókönyvek | Naplóbejegyzések száma | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Útválasztási szabály engedélyezve gyorsítótárazás nélkül | 1 | Edge POP-kód | A kérés továbbítására szolgáló háttér | Igaz | CONFIG_NOCACHE |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár-találat a peremhálózati POP-on | 1 | Edge POP-kód | Üres | Igaz | HIT |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár-lemaradás az Edge POP-ban, de a gyorsítótár a szülő gyorsítótár POP-ban | 2 | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. szülő gyorsítótár POP-állomásnév</br>2. üres | 1. igaz</br>2. hamis | 1. MISS</br>2. TALÁLAT |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótárak kihagyása az Edge POP-ban, de részleges gyorsítótár-találat a szülő gyorsítótár POP-ban | 2 | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. szülő gyorsítótár POP-állomásnév</br>2. a gyorsítótár feltöltését segítő háttérrendszer | 1. igaz</br>2. hamis | 1. MISS</br>2. PARTIAL_HIT |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár PARTIAL_HIT az Edge-POP-ban, de a gyorsítótár a szülő gyorsítótár POP-ra ütközött | 2 | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. igaz</br>2. hamis | 1. PARTIAL_HIT</br>2. TALÁLAT |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár-lemaradás az Edge és a szülő gyorsítótárban | 2 | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. igaz</br>2. hamis | 1. MISS</br>2. MISS |

> [!NOTE]
> Gyorsítótárazási forgatókönyvek esetén a gyorsítótár állapotának értéke partial_hit, ha egy kérelemhez tartozó bájtok egy része a bejárati peremhálózati vagy a Origin Shield cache szolgáltatásból származik, míg a bájtok némelyike a nagy objektumok forrásaként szolgál.

A bejárati ajtó egy objektum-darabolás nevű technikát használ. Nagyméretű fájl kérése esetén a bejárati ajtó lekéri a fájl kisebb darabját a forrásból. Miután a bejelentkező POP-kiszolgáló megkapja a kért fájl teljes vagy bájtos tartományát, a bejárati ajtó peremhálózati kiszolgálója 8 MB méretű adattömbökben kéri le a fájlt a forrástól.

Miután az adathalmaz megérkezik a bejárati ajtó szélén, a gyorsítótárba kerül, és azonnal kézbesítve van a felhasználó számára. A bejárati ajtó ezután párhuzamosan lekéri a következő adathalmazt. Ez a kilépés biztosítja, hogy a tartalom a felhasználó előtt egy darabban maradjon, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájl le nem töltődik (ha szükséges), az összes bájtos tartomány elérhető (ha szükséges), vagy az ügyfél lezárja a csatlakozást. A byte-Range kérelemmel kapcsolatos további információkért lásd: RFC 7233. A bejárati ajtó gyorsítótárba helyezi a kapott adattömböket. A teljes fájlt nem kell gyorsítótárazni a bejárati ajtó gyorsítótárában. A fájl-vagy byte-tartományokra vonatkozó kérelmeket a rendszer a bejárati ajtó gyorsítótárából kézbesíti. Ha nem az összes adathalmaz gyorsítótárazva van a bejárati ajtón, a rendszer kikéri a kiindulási pontokra vonatkozó adattömböket. Ez az optimalizálás arra támaszkodik, hogy a forráskiszolgáló képes támogatni a bájtos tartományokra vonatkozó kérelmeket. Ha a forráskiszolgáló nem támogatja a bájtok közötti kérelmeket, ez az optimalizálás nem érvényes.

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó profiljának létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)
