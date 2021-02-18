---
title: Azure bejárati ajtó standard/prémium (előzetes verzió) – jelentések
description: Ez a cikk azt ismerteti, hogyan működik a jelentéskészítés az Azure-beli bejárati ajtón.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100265"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) – jelentések

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure bejárati ajtó standard/prémium elemzési jelentései beépített és teljes körű áttekintést nyújtanak arról, hogy az Azure bejárati ajtó hogyan viselkedik a társított webalkalmazási tűzfal metrikákkal együtt. A hozzáférési naplók előnyeit kihasználva további hibaelhárítást és hibakeresést is végezhet. Az Azure bejárati ajtó elemzési jelentései tartalmazzák a forgalmi jelentéseket és a biztonsági jelentéseket.

| Jelentések | Részletek |
|---------|---------|
| A fő mérőszámok áttekintése | Az Azure-beli első ajtó szélétől az ügyfeleknek elküldett összesített adatok megjelenítése<br/>– Maximális sávszélesség<br/>– Kérelmek <br/>– Gyorsítótár találati aránya<br/> – Teljes késés<br/>– 5XX hibák aránya |
| Forgalom tartomány szerint | – Áttekintést nyújt a profilban lévő összes tartományról<br/>– A AFD szélétől az ügyfél felé továbbított adatok részletezése<br/>– Összes kérelem<br/>-3XX/4XX/5XX-válasz kódja tartományok szerint |
| Forgalom hely szerint | -Megjeleníti a legfelső országokból származó kérelem és használat Térkép nézetét<br/>– A leggyakoribb országok trendjeinek nézete |
| Használat | – Az Azure-beli bejárati ajtó szélétől az ügyfelek felé irányuló adatátvitelt jeleníti meg<br/>– Adatátvitel a forrástól a AFD széléig<br/>– Sávszélesség az AFD Edge-ből az ügyfeleknek<br/>-Sávszélesség a forrástól a AFD széléig<br/>– Kérelmek<br/>– Teljes késés<br/>-Kérelmek számának trendje HTTP-állapotkód alapján |
| Gyorsítótárazás | – A gyorsítótár találati arányának megjelenítése a kérelmek száma alapján<br/>-A találatok és a kihagyott kérelmek trendjeinek nézete |
| Felső URL-cím | – A kérelmek számának megjelenítése <br/>– Továbbított adatátvitel <br/>– Gyorsítótár találati aránya <br/>-Válasz állapotkód eloszlása a leginkább igényelt 50-eszközökhöz. |
| Leggyakoribb hivatkozó | – A kérelmek számának megjelenítése <br/>– Továbbított adatátvitel <br/>– Gyorsítótár találati aránya <br/>-Válasz állapotkód-eloszlás a forgalmat létrehozó legfontosabb 50-hivatkozók esetében. |
| Leggyakoribb felhasználói ügynök | – A kérelmek számának megjelenítése <br/>– Továbbított adatátvitel <br/>– Gyorsítótár találati aránya <br/>-Válasz állapotkód-eloszlás a tartalom kéréséhez használt legfontosabb 50 felhasználói ügynökök számára. |

| Biztonsági jelentések | Részletek |
|---------|---------|
| A fő mérőszámok áttekintése | -Az egyező WAF-szabályok megjelenítése<br/>-Egyező OWASP szabályok<br/>-Egyezéses BOT-szabályok<br/>-Egyező egyéni szabályok |
| Metrikák dimenzió szerint | – A megegyező WAF-szabályok eloszlása művelet szerint<br/>-Az események perec-diagramja szabálykészlet típusa és esemény szerint szabály szerint csoportosítva<br/>– A legfontosabb események listájának lebontása szabály azonosítója, ország, IP-cím, URL-cím és felhasználói ügynök szerint  |

> [!NOTE]
> A biztonsági jelentések csak az Azure bejárati ajtó Premium SKU-val érhetők el.

A jelentések többsége hozzáférési naplókon alapul, és díjmentesen vehető igénybe az Azure-beli előtérben található ügyfelek számára. Az ügyfélnek nem kell engedélyeznie a hozzáférési naplókat, vagy nem kell konfigurálnia ezeket a jelentéseket. A jelentések a Portálon és az API-n keresztül érhetők el. A CSV-Letöltés is támogatott. 

A jelentések az előző 90 nap bármely kiválasztott dátumtartományt támogatják. Az adatpontok 5 percenként, óránként, vagy minden nap a kiválasztott Dátumtartomány alapján. Normál esetben a késéssel rendelkező adat egy órán belül, alkalmanként pedig akár néhány óráig is megtekinthető. 

## <a name="access-reports-using-the-azure-portal"></a>Jelentések elérése a Azure Portal használatával

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza ki az Azure bejárati ajtó standard/prémium profilját.

1. A navigációs ablaktáblán válassza a **jelentések vagy biztonság** lehetőséget az *elemzés* alatt.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Képernyőkép a jelentések kezdőlapról":::

1. A különböző dimenziók esetében hét fül van, válassza ki a fontos dimenziót.

   * Forgalom tartomány szerint
   * Használat 
   * Forgalom hely szerint
   * Gyorsítótár
   * Felső URL-cím
   * Leggyakoribb hivatkozó
   * Leggyakoribb felhasználói ügynök

1. A dimenzió kiválasztása után különböző szűrőket választhat.
  
    1. **Adatok megjelenítése** – válassza ki azt a dátumtartományt, amelynek a tartomány szerint szeretné megtekinteni a forgalmát. A rendelkezésre álló tartományok a következők:
        
        * Az elmúlt 24 óra
        * Elmúlt 7 nap
        * Az elmúlt 30 nap
        * Utolsó 90 nap
        * Ez a hónap
        * Előző hónap
        * Egyéni dátum

         Alapértelmezés szerint az elmúlt hét napban megjelennek az adathalmazok. A vonalas diagramokkal rendelkező lapok esetében az adatok részletessége az alapértelmezett viselkedésként kiválasztott dátumtartomány szerint megy. 
    
        * 5 perc – egy adatpont 5 percenként, a dátumtartomány értéke pedig 24 óra.
        * Óránként – egy adat óránként – 24 óra és 30 nap közötti tartományba esik
        * Naponként – naponta egy adat a dátumtartomány 30 napnál hosszabb időtartamára.

        Az összesítést bármikor használhatja az Összesítés alapértelmezett részletességének módosításához. Megjegyzés: 5 percen belül nem működik a 14 napnál hosszabb adattartomány. 

    1. **Hely** – válasszon ki egy vagy több ügyfél-helyet országonként. Az országok hat régióba vannak csoportosítva: Észak-Amerika, Ázsia, Európa, Afrika, Óceánia és Dél-Amerika. Tekintse át a [régió/ország leképezését](https://en.wikipedia.org/wiki/Subregion). Alapértelmezés szerint az összes ország ki van választva.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Képernyőkép a Location Dimension-jelentésekről.":::
   
    1. **Protokoll** – válassza a http vagy a https lehetőséget a forgalmi adatok megtekintéséhez.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Képernyőkép a protokollok dimenziójának jelentéseiről.":::
    
    1. **Tartományok** – válasszon ki egy vagy több végpontot vagy egyéni tartományt. Alapértelmezés szerint az összes végpont és egyéni tartomány van kiválasztva. 
    
        * Ha töröl egy végpontot vagy egy egyéni tartományt egy profilban, majd újra létrehozza ugyanazt a végpontot vagy tartományt egy másik profilban. A végpont második végpontnak tekintendő.  
        * Ha egyéni tartomány szerint tekinti meg a jelentéseket, akkor ha töröl egy egyéni tartományt, és azt egy másik végponthoz köti. Egyetlen egyéni tartományként lesznek kezelve. Ha a megtekintés végpont alapján – a rendszer különálló elemekként kezeli őket. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Képernyőkép a tartományi dimenzióhoz tartozó jelentésekről.":::

1. Ha egy CSV-fájlba szeretné exportálni az adatfájlokat, jelölje be a CSV-fájl *letöltése* hivatkozást a kijelölt lapon.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Képernyőkép a CSV-fájl letöltéséről a jelentésekhez.":::

### <a name="key-metrics-for-all-reports"></a>Az összes jelentés fő mérőszámai

| Metric | Leírás |
|---------|---------|
| Továbbított adatátvitel | Az AFD Edge-ből átvitt adatok megjelenítése a kijelölt időkeretre, az ügyfél helyeire, tartományokra és protokollokra. |
| Maximális sávszélesség | Az Azure bejárati oldaláról származó bitek maximális kihasználtsága (bit/mp) a kiválasztott időkeretre, az ügyfél helyeire, tartományokra és protokollokra. | 
| Összes kérelem | Azoknak a kéréseknek a száma, amelyeket a AFD Edge a kiválasztott időkeretre, az ügyfél helyeire, tartományokra és protokollokra válaszol az ügyfélnek. |
| Gyorsítótár-találati arány | Azon gyorsítótárazható kérelmek százalékos aránya, amelyekhez a AFD a kijelölt időkeretre, az ügyfél helyeire, tartományokra és protokollokra vonatkozóan kézbesítették a tartalmat. |
| 5XX hibák aránya | Azon kérelmek százalékos aránya, amelyekhez a HTTP-állapotkód ügyfél-5XX volt a kiválasztott időkeretre, az ügyfél helyeire, tartományokra és protokollokra vonatkozóan. |
| Teljes késés | A kiválasztott időkeretre, az ügyfél helyeire, tartományokra és protokollokra vonatkozó összes kérelem átlagos késése. Az egyes kérések késését az Azure bejárati ajtótól az ügyfél felé irányuló utolsó válasz bájtig tartó teljes időpontig méri. |

## <a name="traffic-by-domain"></a>Forgalom tartomány szerint

A tartományon belüli forgalom az Azure-beli bejárati profilban lévő összes tartomány rácsos nézetét biztosítja. A jelentésben a következőket tekintheti meg: 
* Kérelmek
* Az Azure bejárati ajtóról az ügyfél felé továbbított adatok
* Az egyes tartományok állapotkódot (3XX, 4Xx és 5XX) tartalmazó kérelmek

A tartományok közé tartoznak a végpontok és az egyéni tartományok, ahogy azt a jelentés-munkamenet elérése című témakör ismerteti.  

Ha további információkra van szüksége, további információért tekintse meg a további lapokat, vagy tekintse meg a hozzáférési naplót, ha megtalálta a várt mérőszámokat. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Képernyőfelvétel a kezdőlapról a jelentésekhez":::


## <a name="usage"></a>Használat

Ez a jelentés a forgalom és válasz állapot kódjának különböző dimenziók szerinti tendenciáit jeleníti meg, többek között:

* A szélétől az ügyfélig és a forrástól az Edge-ig továbbított adatok a diagramon. 

* A peremhálózat és az ügyfél közötti adatátvitel a diagramon a protokoll szerint. 

* A szélétől az ügyfelek felé irányuló kérelmek száma a diagramon.  

* Az Edge-ből az ügyfelek felé irányuló kérelmek száma protokoll, HTTP és HTTPS szerint, a diagramon. 

* Sávszélesség a peremhálózat és az ügyfél között a diagramon. 

* Teljes késés, amely az ügyféltől érkező kérések teljes idejét méri a bejárati ajtótól az ügyfél felé küldött utolsó válasz bájtig.

* Az Edge és az ügyfelek közötti kérelmek száma HTTP-állapotkód szerint, a diagramon. Minden kérelem létrehoz egy HTTP-állapotkódot. A HTTP-állapotkód megjelenik a HTTPStatusCode a nyers naplóban. Az állapotkód leírja, hogyan kezeli a CDN Edge a kérelmet. Egy 2xx például azt jelzi, hogy a kérés sikeresen kézbesítve lett egy ügyfél számára. Míg a 4xx-állapotkód azt jelzi, hogy hiba történt. A HTTP-állapotkódok részletes ismertetését lásd: HTTP-állapotkódok listája. 

* A szélétől az ügyfelek felé irányuló kérelmek száma HTTP-állapotkód szerint. A kérelmek százalékos aránya a HTTP-állapotkód alapján a rácson lévő összes kérelem között. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Képernyőkép a jelentések használat alapján" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Forgalom hely szerint

Ez a jelentés a legfelső 50 helyet jeleníti meg azon látogatók országa számára, akik a leginkább hozzáférnek az eszközhöz. A jelentés a mérőszámok országonkénti részletezését is biztosítja, és átfogó képet nyújt azokról az országokról, ahol a legtöbb forgalom létrejön. Végül láthatja, hogy melyik országban van magasabb gyorsítótár-találati arány vagy 4XX/5XX-hibakód.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Képernyőkép a jelentések helyeiről" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

A jelentések a következőket tartalmazzák:

* A legfontosabb 50-országokat az Ön által választott adatforgalomból vagy az Ön által választott kérelmekből álló globális térképi nézet.
* Két vonalas diagram az első öt országra vonatkozóan az átvitt adatokat és az Ön által választott kérelmeket. 
* A legfelső országok rácsa, a megfelelő adatokkal a AFD-ből az ügyfeleknek, az összes ország%-ában továbbított adatok, a kérelmek, a kérések százaléka az összes ország között, a gyorsítótár találati aránya, a 4XX és a 5XX-válasz kódja.

## <a name="caching"></a>Gyorsítótárazás

A gyorsítótárazási jelentések diagramos nézetet biztosítanak a gyorsítótár-találatok/-hiányok és a gyorsítótár-találatok arányáról a kérelmek alapján. Ezek a fő mérőszámok elmagyarázzák, hogy a CDN hogyan gyorsítótárazza a tartalmakat, mivel a gyorsítótárbeli találatok leggyorsabb teljesítményének eredménye. Az adatátviteli sebességet a gyorsítótár-lemaradás minimalizálása mellett optimalizálhatja. Ez a jelentés a következőket tartalmazza:

* Gyorsítótár-találatok és a kihagyott darabszámok trendje a diagramon.

* A gyorsítótár találati aránya a diagramon.

Gyorsítótár-találatok/Hiányzikek: a kérelmek számának gyorsítótárbeli látogatottsága és a gyorsítótár-lekérések lemaradása.

* Találatok: a Azure CDN Edge-kiszolgálókról közvetlenül kiszolgált ügyfél-kérelmek. Azokat a kérelmeket jelöli, amelyek a nyers naplók CacheStatus értékei a TALÁLATok, PARTIAL_HIT vagy távoli TALÁLATok. 

* Miss: a Azure CDN Edge-kiszolgálók által kiszolgált ügyfelektől érkező kérelmeket a rendszer a forrástól származó tartalom beolvasására kéri le. Azokat a kérelmeket jelöli, amelyek értékei a nyers naplók CacheStatus mezőjében hiányoznak. 

A **gyorsítótárbeli találatok aránya** a közvetlenül a szélétől kiszolgált gyorsítótárazott kérelmek százalékos arányát ismerteti. A gyorsítótár találati arányának képlete a következő: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Ez a jelentés a gyorsítótárazási forgatókönyveket veszi figyelembe, és az alábbi követelményeknek megfelelő kérelmek számításba vesznek. 

* A kért tartalom gyorsítótárazva lett a kérelmező vagy a forrás pajzshoz legközelebb eső POP-on. 

* Az objektumok adatdarabolásának részleges gyorsítótárazott tartalma.

A következő esetek mindegyikét kizárják: 

* A beállított szabályok miatt megtagadott kérelmek.

* A letiltott gyorsítótárra beállított egyező szabályokat tartalmazó kérelmek. 

* A WAF által blokkolt kérelmek. 

* A forrás válasz fejlécei azt jelzik, hogy nem szabad gyorsítótárazni őket. Például a Cache-Control: Private, Cache-Control: no-cache vagy Sorpragmákat: no-cache fejléc megakadályozza egy eszköz gyorsítótárazását. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="A gyorsítótárazási jelentések képernyőképe.":::

## <a name="top-urls"></a>Legnépszerűbb URL-címek

A legnépszerűbb URL-címek segítségével megtekintheti egy adott végponton vagy egyéni tartományban felmerülő forgalom mennyiségét. Az elmúlt 90 nap során a leggyakrabban kért 50-eszközökre vonatkozó adatok jelennek meg. A népszerű URL-címek a következő értékekkel lesznek megjelenítve. A felhasználó rendezheti az URL-címeket a kérelmek száma, a kérelem%, a továbbított adatátvitel és a (z)%. Az összes mérőszámot óránként összesítjük, és a kiválasztott időkerettől függően változhatnak. Az URL-cím a RequestUri értékére hivatkozik a hozzáférési naplóban. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Képernyőkép a legnépszerűbb URL-címhez tartozó jelentésekről.":::

* URL-cím, a kért objektum teljes elérési útját jelenti a következő formátumban: `http(s)://contoso.com/index.html/images/example.jpg` . 
* A kérelmek száma. 
* Az Azure bejárati ajtó által kiszolgált összes kérelem%-a. 
* Továbbított adatátvitel. 
* Továbbított adatátvitel:%. 
* Gyorsítótár találati aránya (%)
* Válasz kóddal rendelkező kérelmek 4XX
* Válasz kóddal rendelkező kérelmek 5XX

> [!NOTE]
> A leggyakoribb URL-címek idővel megváltozhatnak, és az első 50 URL-címek pontos listáját kapják meg, az Azure bevezető ajtaja óránként megszámolja az összes URL-kérést, és megtartja a futó összeget egy nap folyamán. Az 500 URL-címek alján lévő URL-címek a nap folyamán felmerülhetnek a lista végére, így az URL-címek teljes száma közelítő.  
>
> A top 50 URL-címek felmerülhetnek és eshetnek a listában, de ritkán tűnnek el a listáról, így a leggyakoribb URL-címek általában megbízhatóak. Ha egy URL-cím leesik a listáról, és újból felveszi a napot, az adott időszakban a kérések száma az adott időszakban megjelenő URL-cím alapján várható.  
>
> Ugyanez a logika vonatkozik a felső felhasználói ügynökre. 

## <a name="top-referrers"></a>Leggyakoribb hivatkozók

A legfontosabb hivatkozók lehetővé teszik az ügyfelek számára, hogy megtekintsék a legfelső szintű 50 szponzort, amely a legtöbb kérést egy adott végpont vagy egyéni tartomány tartalmára vonatkozóan kezdeményezte. Az elmúlt 90 napban található bármely időszakra vonatkozóan megtekintheti az összes adathalmazt. A hivatkozó megadja azt az URL-címet, amelyből a rendszer létrehozta a kérést. A hivatkozó keresőmotorból vagy más webhelyről is származhat. Ha a felhasználó egy URL-címet (például http (s)://contoso.com/index.html) közvetlenül begépel egy böngésző címsorába, a kért hivatkozó "Empty". A legfontosabb hivatkozói jelentés a következő értékeket tartalmazza. Rendezheti a kérelmek száma, a kérelem%, a továbbított adatátvitel és a (z)% közötti adatátvitelt. Az összes mérőszámot óránként összesítjük, és a kiválasztott időkerettől függően változhatnak. 

* Hivatkozó, a hivatkozó érték a nyers naplókban 
* Kérelmek száma 
* A Azure CDN által kiszolgált kérelmek teljes száma a kiválasztott időszakban. 
* Továbbított adatátvitel 
* Továbbított adatátvitel% 
* Gyorsítótár találati aránya (%)
* Válasz kóddal rendelkező kérelmek 4XX
* Válasz kóddal rendelkező kérelmek 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Képernyőkép a leggyakoribb hivatkozói jelentésekről.":::

## <a name="top-user-agent"></a>Leggyakoribb felhasználói ügynök

Ez a jelentés lehetővé teszi, hogy a tartalom kéréséhez használt legfontosabb 50 felhasználói ügynökök grafikus és statisztikai nézetét használja. Példa:
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (KHTML, például Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

A rács megjeleníti a kérések számát, a kérést, az átvitt adatátvitelt és az átvitt adatátviteli arányt, a gyorsítótár találati arányát (%), a 4XX és a 5XX-kérelmeket. A felhasználói ügynök a hozzáférési naplók UserAgent értékére hivatkozik.

## <a name="security-report"></a>Biztonsági jelentés

Ez a jelentés lehetővé teszi, hogy különböző dimenziók alapján grafikus és statisztikai nézetet WAF a mintákról.

| Dimenziók | Description |
|---------|---------|
| Áttekintő mérőszámok – egyező WAF-szabályok | Az egyéni WAF-szabályokkal, a felügyelt WAF-szabályokkal és a robot-kezelővel egyező kérelmek. |
| Áttekintő metrikák – letiltott kérelmek | A WAF-szabályok által letiltott kérelmek százalékos aránya az összes WAF-szabálynak megfelelő kérések között. |
| Áttekintő mérőszámok – megfeleltetett felügyelt szabályok | Négy vonalas diagramra vonatkozó trend a blokkolt, naplót, engedélyezést és átirányítást kérő kérelmek esetében. |
| Áttekintő metrikák – egyeztetett egyéni szabály | Az egyéni WAF-szabályoknak megfelelő kérelmek. |
| Áttekintő mérőszámok – megfeleltetett bot-szabály | A bot Managerrel egyező kérelmek. |
| WAF-kérelmek trendje művelet szerint | Négy vonalas diagramra vonatkozó trend a blokkolt, naplót, engedélyezést és átirányítást kérő kérelmek esetében. |
| Események szabály típusa szerint | A WAF perecdiagram a szabály típusa szerint, például a robot, az egyéni szabályok és a felügyelt szabályok alapján kéri a terjesztést. |
| Események szabálytípus szerint | A WAF által kérelmek eloszlása szabály szerint csoportosítva. |
| Kérelmek műveletek szerint | A kérések által a csökkenő sorrendben végrehajtott kérelmek táblázata. |
| Kérelmek a legfelső szintű szabályok azonosítói alapján | A legfelső 50-szabály azonosítóinak táblázata csökkenő sorrendben. |
| Kérelmek a legfontosabb országok szerint |  A top 50 országok által küldött kérések táblázata csökkenő sorrendben. |
| Kérelmek az ügyfelek legfontosabb IP-címei alapján |  A legnépszerűbb 50 IP-címekkel rendelkező kérelmek táblázata csökkenő sorrendben. |
| Kérelmek a legfelső szintű kérelem URL-címe alapján |  A legnépszerűbb 50 URL-címekkel rendelkező kérelmek táblázata csökkenő sorrendben. |
| Kérés felső állomásnevek alapján | A top 50 hostname által küldött kérések táblázata csökkenő sorrendben. |
| Kérelmek leggyakoribb felhasználói ügynökök szerint | A top 50 felhasználói ügynökök által küldött kérések táblázata csökkenő sorrendben. |

## <a name="cvs-format"></a>CVS formátum

A jelentésekben a különböző lapokra vonatkozó CSV-fájlokat is letölthet. Ez a szakasz az egyes CSV-fájlok értékeit írja le.

### <a name="general-information-about-the-cvs-report"></a>Általános információk a CVS-jelentésről

Minden CSV-jelentés tartalmaz néhány általános információt, és az információ minden CSV-fájlban elérhető. a változókat a letöltött jelentés alapján. 


| Érték | Leírás |
|---------|---------|
| Jelentés | A jelentés neve. | 
| Tartományok | A jelentéshez tartozó végpontok vagy egyéni tartományok listája. |
| StartDateUTC | Azon dátumtartomány kezdete, amelyben a jelentést az egyezményes világidő (UTC) szerint generálta |
| EndDateUTC | Azon dátumtartomány vége, amelyhez a jelentést az egyezményes világidő (UTC) szerint generálta |
| GeneratedTimeUTC | A jelentés előállításának dátuma és időpontja az egyezményes világidő (UTC) szerint |
| Hely | Azon országok listája, amelyekben az ügyfél által kért kérelmek származnak. Alapértelmezés szerint az érték az összes. Nem alkalmazható a biztonsági jelentésre. |
| Protokoll | A kérelem, HTTP vagy HTTPs protokollja. Nem alkalmazható a jelentésekben és biztonsági jelentésekben szereplő felhasználói ügynök által használt legnépszerűbb URL-címre és forgalomra. |
| Összesítés | Az adatösszesítés részletessége minden sorban, 5 percenként, óránként és minden nap. Nem alkalmazható a tartomány, a felső URL-cím és a felhasználói ügynök által a jelentésekben és biztonsági jelentésekben forgalmazott forgalomra. |

### <a name="data-in-traffic-by-domain"></a>Forgalomban lévő adatok tartomány szerint

* Tartomány 
* Kérelem összesen 
* Gyorsítótár-találati arány 
* 3XX kérelmek 
* 4XX kérelmek 
* 5XX kérelmek 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Adatok a forgalomban hely szerint

* Hely
* TotalRequests
* Kérelem
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Használatban lévő adatok

Ebben a CSV-fájlban három jelentés található. Egyet a HTTP protokollhoz, egyet a HTTPS protokollhoz, egy pedig a HTTP-állapotkódot. 

A HTTP-és HTTPs-jelentések ugyanazt az adathalmazt használják. 

* Idő 
* Protokoll 
* DataTransferred (bájt) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

A HTTP-állapotkód jelentése. 

* Idő 
* DataTransferred (bájt) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Gyorsítótárban tárolt adatértékek 

* Idő
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>A legfelső URL-címben szereplő adatértékek 

* URL-cím 
* TotalRequests 
* Kérelem 
* DataTransferred (bájt) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>A felhasználói ügynökben található adatkészletek 

* UserAgent 
* TotalRequests 
* Kérelem 
* DataTransferred (bájt) 
* DataTransferred% 

### <a name="security-report"></a>Biztonsági jelentés 

A lenti mezőkben hét tábla található.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

A hét táblázat a következő: idő, szabály azonosítója, ország, IP-cím, URL, állomásnév, felhasználói ügynök. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Azure bejárati ajtó standard/Premium valós idejű monitorozási mérőszámait](how-to-monitor-metrics.md).
