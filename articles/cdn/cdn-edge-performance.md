---
title: Az Edge Node teljesítményének elemzése a Azure CDNban | Microsoft Docs
description: Microsoft Azure CDN-ben az Edge Node teljesítményének elemzése. Az Edge Performance Analytics részletes adatforgalmat és sávszélesség-használatot biztosít a CDN számára.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778657"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Az élcsomópontok teljesítményének elemzése a Microsoft Azure CDN szolgáltatásban
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Az Edge Performance Analytics részletes adatforgalmat és sávszélesség-használatot biztosít a CDN számára. Ezek az adatok felhasználhatók a trend Statistics létrehozásához, amely lehetővé teszi, hogy betekintést kapjon az adategységek gyorsítótárazásának és az ügyfeleknek való kézbesítésének módjára. Ez lehetővé teszi a tartalmak továbbításának optimalizálását, valamint annak meghatározását, hogy milyen problémákkal kell foglalkozni a CDN jobb kihasználásához. Ennek eredményeképpen nem csupán az adattovábbítási teljesítmény javítására lesz lehetősége, de csökkentheti a CDN-költségeket is.

> [!NOTE]
> Minden jelentés UTC/GMT-jelölést használ a dátum és idő megadásakor.
> 
> 

## <a name="reports-and-log-collection"></a>Jelentések és naplók gyűjteménye
A CDN-tevékenységre vonatkozó adatokat a Edge Performance Analytics modulnak kell összegyűjtenie, mielőtt jelentéseket tud készíteni rajta. Ez a begyűjtési folyamat naponta egyszer történik meg, és az előző nap folyamán lezajlott tevékenységre vonatkozik. Ez azt jelenti, hogy a jelentés statisztikái a nap statisztikáit jelölik a feldolgozásuk időpontjában, és nem feltétlenül tartalmazzák az aktuális nap teljes készletét. A jelentések elsődleges funkciója a teljesítmény értékelése. Nem használhatók számlázási célokra vagy pontos numerikus statisztikákra.

> [!NOTE]
> Azok a nyers adatok, amelyekről az Edge Performance analitikai jelentései jönnek létre, legalább 90 napig elérhetők.
> 
> 

## <a name="dashboard"></a>Irányítópult
Az Edge Performance Analytics-irányítópult diagramokon és statisztikákon keresztül nyomon követi az aktuális és a korábbi CDN-forgalmat. Ezen az irányítópulton a fiókja CDN-forgalmának teljesítményére vonatkozó legutóbbi és hosszú távú trendek észlelhetők.

Ez az irányítópult a következőkből áll:

* Interaktív diagram, amely lehetővé teszi a fő mérőszámok és trendek megjelenítését.
* Egy ütemterv, amely a fő mérőszámok és trendek hosszú távú mintáit biztosítja.
* Fő mérőszámok és statisztikai információk arról, hogy a CDN-hálózat Hogyan javítja a helyek forgalmát a teljes teljesítmény, a használat és a hatékonyság alapján mérve.

### <a name="accessing-the-edge-performance-dashboard"></a>Az Edge Performance irányítópult elérése
1. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil panel kezelése gomb](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Vigye az egérmutatót az **elemzés** lapra, majd vigye az egérmutatót az **Edge Performance Analytics** menü fölé.  Kattintson az **irányítópult** elemre.
   
    Megjelenik az Edge Node Analytics irányítópultja.

### <a name="chart"></a>Diagram
Az irányítópult olyan diagramot tartalmaz, amely nyomon követi a metrikát az ütemtervben kiválasztott időszakon belül, amely közvetlenül alatta jelenik meg.  Egy olyan ütemterv, amely a CDN-tevékenység utolsó két évében gráfra mutat, közvetlenül a diagram alatt jelenik meg.

#### <a name="using-the-chart"></a>A diagram használata
* Alapértelmezés szerint az elmúlt 30 napban a gyorsítótár-hatékonysági arány lesz ábrázolva.
* Ez a diagram napi rendszerességgel gyűjtött adatokból jön létre.
* Ha a grafikonon egy nap fölé viszi az egérmutatót, a dátum és a metrika értéke is megjelenik.
* Kattintson a hétvégek kiemelése lehetőségre a diagramon hétvégeket jelképező világos szürke függőleges sávok átfedésének váltásához. Ez a típusú átfedés hasznos lehet a forgalmi minták hétvégeken való azonosításához.
* Kattintson a megtekintés egy évvel ezelőtt lehetőségre az előző évi tevékenység átfedésének a diagramra való váltásához. Ez a típusú összehasonlítás betekintést nyújt a hosszú távú CDN használati mintákba. A diagram jobb felső sarka egy jelmagyarázatot tartalmaz, amely az egyes sorok színkódjait jelzi.

#### <a name="updating-the-chart"></a>A diagram frissítése
* Időtartomány: hajtsa végre az alábbi műveletek egyikét:
  * Válassza ki a kívánt régiót az idősoron. A rendszer frissíti a diagramot a kijelölt időszaknak megfelelő adatmennyiséggel.
  * A diagramra duplán kattintva megjelenítheti az összes rendelkezésre álló korábbi adatmennyiséget legfeljebb két évig.
* Metrika: kattintson a kívánt metrika mellett megjelenő diagram ikonra. A diagram és az idősor a megfelelő metrika adataival lesz frissítve.

### <a name="key-metrics-and-statistics"></a>Fő mérőszámok és statisztikák
#### <a name="efficiency-metrics"></a>Hatékonysági mérőszámok
A metrikák célja annak megállapítása, hogy javul-e a gyorsítótár hatékonysága. A gyorsítótár hatékonyságának fő előnyei a következők:

* Csökkentett terhelés a forráskiszolgáló esetében, amely a következőhöz vezethet:
  * A webkiszolgáló jobb teljesítménye.
  * Csökkentett működési költségek.
* Továbbfejlesztett adattovábbítási gyorsítás, mivel a további kérések közvetlenül a CDN-ből lesznek kézbesítve.

| Mező | Leírás |
| --- | --- |
| Gyorsítótár-hatékonyság |A gyorsítótárból kiszolgált adatok százalékos arányát jelzi. Ez a mérőszám azt méri, hogy a kért tartalom gyorsítótárazott verziójának kiszolgálása közvetlenül a CDN-ből (Edge-kiszolgálók) a kérelmezőknek (pl. webböngésző) |
| Találatok aránya |A gyorsítótárból kiszolgált kérelmek százalékos arányát jelzi. Ez a mérőszám azt méri, hogy a kért tartalom gyorsítótárazott verziójának kiszolgálása közvetlenül a CDN-ből (Edge-kiszolgálók) a kérelmezőknek (például a webböngészőbe). |
| Távoli bájtok százaléka – nincs gyorsítótár-konfiguráció |Megadja a forrásként szolgáló kiszolgálóról a CDN-re (Edge-kiszolgálókra) kiszolgált forgalom százalékos arányát, amelyet a rendszer nem gyorsítótáraz a gyorsítótár megkerülése funkció (HTTP-szabályok motor) eredményeképpen. |
| Távoli bájtok%-a – lejárt gyorsítótár |Meghatározza a forrás-kiszolgálókról a CDN-re (Edge-kiszolgálókra) kiszolgált forgalom százalékos arányát az elavult tartalom-újraérvényesítés eredményeként. |

#### <a name="usage-metrics"></a>Használati metrikák
Ezen mérőszámok célja, hogy betekintést nyújtson a következő költségkímélő intézkedésekbe:

* A működési költségek minimalizálása a CDN-en keresztül.
* A CDN-kiadások csökkentése a gyorsítótár hatékonysága és tömörítése révén.

> [!NOTE]
> A forgalmi kötetek száma az arányok és százalékok számításakor használt forgalmat jelöli, és csak a nagy mennyiségű ügyfelek teljes forgalmának egy részét jeleníti meg.
> 
> 

| Mező | Leírás |
| --- | --- |
| Ave bájt kimenő |Megadja a CDN (Edge-kiszolgálók) által a kérelmezőnek (például webböngészőnek) kiszolgált kérelmekhez továbbított bájtok átlagos számát. |
| Nincs gyorsítótár-konfigurációs bájtok aránya |Megadja a CDN (Edge-kiszolgálók) által a kérelmezőnek (például a webböngészőnek) kiszolgált forgalom százalékos arányát, amelyet a rendszer a gyorsítótár megkerülése funkció miatt nem gyorsítótáraz. |
| Tömörített bájtok aránya |Megadja a CDN (Edge-kiszolgálók) által a kérelmezőknek (például a webböngészőnek) a tömörített formátumban elküldett forgalom százalékos arányát. |
| Kimenő bájtok |Meghatározza a CDN (Edge-kiszolgálók) által a kérelmezőnek (például webböngészőnek) továbbított adatok mennyiségét bájtban kifejezve. |
| Bájtok itt |A kérelmezőtől (például webböngészőből) a CDN-be (Edge-kiszolgálókra) továbbított adatok mennyiségét jelzi bájtban. |
| Távoli bájtok |Meghatározza a CDN-ből és az ügyfél-forrásokból a CDN-be (Edge-kiszolgálókra) továbbított adatok mennyiségét bájtban kifejezve. |

#### <a name="performance-metrics"></a>Teljesítmény-metrikák
Ezen mérőszámok célja, hogy nyomon kövessék a forgalom általános CDN-teljesítményét.

| Mező | Leírás |
| --- | --- |
| Adatátviteli sebesség |Azt az átlagos sebességet jelzi, amikor a tartalmat a CDN-ből egy kérelmezőnek adták át. |
| Időtartam |Azt jelzi, hogy az átlagos idő ezredmásodpercben egy eszköz kézbesítése egy kérelmező számára (pl. webböngésző). |
| Tömörített kérelmek gyakorisága |Meghatározza a CDN-ből (Edge-kiszolgálókról) a kérelmezőnek (például a webböngészőnek) küldött találatok százalékos arányát tömörített formátumban. |
| 4xx hibák aránya |A 4xx-állapotkódot generáló találatok százalékos arányát jelzi. |
| 5xx hibák aránya |A 5xx-állapotkódot generáló találatok százalékos arányát jelzi. |
| Találatok |A CDN-tartalomra vonatkozó kérelmek számát jelzi. |

#### <a name="secure-traffic-metrics"></a>Biztonságos forgalmi metrikák
Ezen mérőszámok célja, hogy nyomon kövessék a CDN teljesítményét a HTTPS-forgalomhoz.

| Mező | Leírás |
| --- | --- |
| Biztonságos gyorsítótár hatékonysága |A gyorsítótárból kiszolgált HTTPS-kérelmekhez továbbított adatok százalékos arányát jelzi. Ez a mérőszám azt méri, hogy a kért tartalom gyorsítótárazott verziójának kiszolgálása közvetlenül a CDN-ből (Edge-kiszolgálók) a kérelmezőknek (például a webböngészőnek) a HTTPS protokollon keresztül. |
| Biztonságos átvitel sebessége |Azt az átlagos sebességet jelzi, amely alapján a rendszer átvitte a tartalmat a CDN-ből (az Edge-kiszolgálókról) a kérelmezőknek (például webkiszolgálóknak) a HTTPS protokollon keresztül. |
| Átlagos biztonságos időtartam |Azt jelzi, hogy az átlagos idő ezredmásodpercben egy eszköznek a kérelmezőhöz (például a webböngészőhöz) való kézbesítése HTTPS-kapcsolaton keresztül történt. |
| Biztonságos találatok |A CDN-tartalomhoz tartozó HTTPS-kérelmek számát jelzi. |
| Biztonságos kimenő bájtok |Azt a HTTPS-forgalmat adja meg bájtban kifejezve, amelyet a CDN-ből (Edge-kiszolgálókról) a kérelmezőnek (pl. webböngésző) küldött. |

## <a name="reports"></a>Jelentések
A modul minden jelentése tartalmaz egy diagramot és statisztikát a különböző típusú metrikák (pl. HTTP-állapotkódok, gyorsítótár-állapotkódok, kérelem URL-címe stb.) tekintetében a sávszélességgel és a forgalommal kapcsolatban. Ezeket az információkat felhasználhatja a tartalomnak az ügyfelek számára való kiszolgálása, illetve a CDN viselkedésének finomhangolása érdekében az adatok kézbesítési teljesítményének javítására.

### <a name="accessing-the-edge-performance-reports"></a>Az Edge Performance-jelentések elérése
1. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil panel kezelése gomb](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Vigye az egérmutatót az **elemzés** lapra, majd vigye az egérmutatót az **Edge Performance Analytics** menü fölé.  Kattintson a **nagyméretű http-objektumra**.
   
    Megjelenik az Edge Node Analytics-jelentések képernyője.

| Jelentés | Leírás |
| --- | --- |
| Napi összefoglalás |Lehetővé teszi a napi forgalmi trendek megtekintését egy adott időszakra vonatkozóan. A gráf egyes sávja egy adott dátumot jelöl. A sáv mérete az adott napon bekövetkezett találatok teljes számát jelzi. |
| Óránkénti összefoglalás |Lehetővé teszi az óránkénti forgalom tendenciáinak megtekintését egy adott időszakra vonatkozóan. A gráf minden sávja egy adott napon belül egy órát jelöl. A sáv mérete az adott órában történt találatok teljes számát jelzi. |
| Protokollok |A HTTP és a HTTPS protokollok közötti adatforgalom részletezését jeleníti meg. A fánk diagram a protokollok egyes típusaihoz tartozó találatok százalékos arányát jelzi. |
| HTTP-metódusok |Lehetővé teszi, hogy gyorsan megszerezze, mely HTTP-metódusokat használja az adatkéréshez. A leggyakoribb HTTP-kérési módszerek általában a GET, a HEAD és a POST. A fánk diagram a HTTP-kérelmek egyes típusaihoz tartozó találatok százalékos arányát jelzi. |
| URL-címek |Egy olyan gráfot tartalmaz, amely a legelső 10 kért URL-címet jeleníti meg. Megjelenik egy sáv az egyes URL-címekhez. A sáv magassága azt jelzi, hogy az adott URL-cím hány találatot generált a jelentés által jelzett időtartományon belül. A felső 100 kért URL-címek statisztikái közvetlenül a gráf alatt jelennek meg. |
| CNAME |Egy olyan gráfot tartalmaz, amely a jelentések időkeretén keresztül az eszközök igényléséhez használt 10 CNAME-t jeleníti meg. A felső 100 kért CNAME rekord statisztikái közvetlenül a gráf alatt jelennek meg. |
| Eredete |Egy olyan gráfot tartalmaz, amely megjeleníti az első 10 CDN-vagy ügyfél-kiindulási kiszolgálót, amelyből egy adott időszakban kértek eszközöket. Az első 100 kért CDN-vagy ügyfél-forráskiszolgáló statisztikái közvetlenül ezen a gráfon jelennek meg. Az ügyfél-kiszolgálókat a címtár neve beállításban megadott név azonosítja. |
| Földrajzi durranás |Azt mutatja, hogy a forgalom mekkora részét irányítja egy adott jelenléti pontra (POP). A hárombetűs rövidítés a CDN-hálózatban lévő POP-t jelöli. |
| Ügyfelek |Egy olyan gráfot tartalmaz, amely az adott időszakon belül az eszközöket kérő első 10 ügyfelet jeleníti meg. A jelentés esetében az azonos IP-címről származó kérelmeket ugyanabból az ügyfélből kell tekinteni. A legfontosabb 100-ügyfelek statisztikái közvetlenül ezen a gráfon jelennek meg. Ez a jelentés hasznos lehet a legfelső szintű ügyfelek letöltési tevékenységi mintáinak meghatározásához. |
| Gyorsítótár állapota |Részletesen részletezi a gyorsítótár viselkedését, ami felfedi a megközelítést a teljes végfelhasználói élmény javításához. Mivel a leggyorsabb teljesítmény a gyorsítótárbeli találatokból származik, optimalizálhatja az adattovábbítási sebességet a gyorsítótár-lemaradás és a gyorsítótár-találatok lejáratának minimalizálása mellett. |
| NINCS adat |Egy olyan gráfot tartalmaz, amely megjeleníti azokat az eszközöket, amelyek a gyorsítótár tartalmának frissességét egy adott időszakban nem ellenőrizték. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| CONFIG_NOCACHE részletei |Egy olyan gráfot tartalmaz, amely az ügyfél CDN-konfigurációja miatt nem gyorsítótárazott eszközök első 10 URL-címét jeleníti meg. Az ilyen típusú eszközök közvetlenül a forrás-kiszolgálóról lettek kézbesítve. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| Nem GYORSÍTÓTÁRAZható részletek |Egy olyan gráfot tartalmaz, amely a kérelmek fejléce miatt nem gyorsítótárazott eszközök első 10 URL-címét jeleníti meg. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| TCP_HIT részletei |Egy olyan gráfot tartalmaz, amely a gyorsítótárból azonnal kiszolgált eszközök első 10 URL-címét jeleníti meg. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| TCP_MISS részletei |Egy olyan gráfot tartalmaz, amely a TCP_MISS gyorsítótár-állapotú eszközeinek első 10 URL-címét jeleníti meg. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| TCP_EXPIRED_HIT részletei |Egy olyan gráfot tartalmaz, amely a közvetlenül a POP-ból kiszolgált elavult eszközök első 10 URL-címét jeleníti meg. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| TCP_EXPIRED_MISS részletei |Egy olyan gráfot tartalmaz, amely az elavult eszközök első 10 URL-címét jeleníti meg, amelyekhez új verziót kellett beolvasni a forrás-kiszolgálóról. Az ilyen típusú eszközök leggyakoribb 100 URL-címeinek statisztikája közvetlenül ebben a diagramban látható. |
| TCP_CLIENT_REFRESH_MISS részletei |Egy sávdiagram, amely megjeleníti az eszközök első 10 URL-címét a forrás-kiszolgálóról egy, az ügyféltől való nem gyorsítótár-kérelem miatt. Az ilyen típusú kérelmek legfontosabb 100 URL-címeinek statisztikái közvetlenül ezen a diagramon jelennek meg. |
| Ügyfél-kérelmek típusai |Megadja a HTTP-ügyfelek (például böngészők) által kezdeményezett kérelmek típusát. Ez a jelentés egy fánk-diagramot tartalmaz, amely a kérelmek kezelésének módját mutatja be. Az egyes kérelmekhez tartozó sávszélesség és forgalmi információk a diagram alatt jelennek meg. |
| Felhasználói ügynök |Egy oszlopdiagram, amely az első 10 felhasználói ügynököt jeleníti meg, hogy a CDN-en keresztül kérje a tartalmat. A felhasználói ügynök általában webböngésző, médialejátszó vagy mobiltelefonos böngésző. A legnépszerűbb 100 felhasználói ügynökök statisztikái közvetlenül ezen a diagramon jelennek meg. |
| Utalt |Egy oszlopdiagram, amely az első 10 hivatkozót jeleníti meg a CDN-en keresztül elért tartalomhoz. A hivatkozó általában a weblap vagy az erőforrás URL-címe, amely a tartalomra hivatkozik. Részletes információkat a gráf alatt talál a legfontosabb 100-hivatkozók számára. |
| Tömörítési típusok |Egy fánk-diagramot tartalmaz, amely lebontja a kért eszközöket, attól függetlenül, hogy azok a peremhálózati kiszolgálókon vannak-e tömörítve. A tömörített eszközök százalékos arányát a rendszer a használt tömörítési típus szerint bontja. Részletes információkat az egyes tömörítési típusokhoz és állapotokhoz tartozó gráf alatt talál. |
| Fájltípusok |Egy oszlopdiagram, amely megjeleníti az Ön fiókja számára a CDN-en keresztül kért első 10 fájltípust. A jelentés esetében a fájl típusát az eszköz fájlnévkiterjesztés és az internetes adathordozó típusa (például. html \[ text/HTML \] ,. htm \[ text/HTML \] ,. aspx \[ szöveg/HTML \] stb.) határozza meg. A részletes információkat a Graph a legfontosabb 100-fájltípusok alatt találja. |
| Egyedi fájlok |Egy olyan gráfot tartalmaz, amely egy adott napon a megadott időszakra vonatkozóan kért egyedi eszközök teljes számát ábrázolja. |
| Jogkivonat-hitelesítés összegzése |Egy tortadiagramot tartalmaz, amely gyors áttekintést nyújt arról, hogy Token-Based hitelesítéssel védettek-e a kért eszközök. A védett eszközök a diagramon a megkísérelt hitelesítés eredményei alapján jelennek meg. |
| Jogkivonat-hitelesítés – megtagadás részletei |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse a Token-Based hitelesítés miatt megtagadott első 10 kérelmet. |
| HTTP-válaszok kódjai |Lebontja a HTTP-állapotkódok (például 200 OK, 403 Tiltott, 404 nem található stb.) lebontását, amelyet az Edge-kiszolgálók a HTTP-ügyfeleknek továbbítottak. A tortadiagram segítségével gyorsan kivizsgálhatja az eszközei kiszolgálásának módját. A gráf alatt minden egyes hibakódhoz részletes statisztikai információt kell megadnia. |
| 404 hiba |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse a 404 nem található hibakódot eredményező első 10 kérelmet. |
| 403 hiba |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse a 403-es tiltott hibakódot eredményező első 10 kérelmet. A 403-es tiltott hibakód akkor fordul elő, ha egy ügyfél-forráskiszolgáló vagy egy, a POP-on lévő peremhálózati kiszolgáló megtagadja a kérést. |
| 4xx hibák |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse az első 10 olyan kérést, amely az 400-es tartományba tartozó válasz kódját eredményezte. Ez a jelentés nem található a 403-es és 404-es tiltott reagálási kódokkal. Egy 4xx-hibakód általában akkor fordul elő, ha egy kérelem elutasítása egy ügyfél hibája miatt megtagadva. |
| 504 hiba |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse az 504-átjáró időtúllépési kódját eredményező első 10 kérelmet. A 504-átjáró időtúllépési kódja akkor fordul elő, ha időtúllépés történik, amikor egy HTTP-proxy megpróbál kommunikálni egy másik kiszolgálóval. CDN esetén a 504-átjáró időtúllépési kódja általában akkor fordul elő, ha egy peremhálózati kiszolgáló nem tud kapcsolatot létesíteni az ügyfél-kiszolgálóval. |
| 502 hiba |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse a 502-es hibás átjárót eredményező tíz kérést. A 502 hibás átjáró-válasz kódja akkor fordul elő, ha egy kiszolgáló és egy HTTP-proxy között HTTP protokoll meghibásodása történik. CDN esetén a 502-es hibás átjáróra adott válasz kódja általában akkor fordul elő, ha egy ügyfél-forráskiszolgáló érvénytelen választ ad vissza egy peremhálózati kiszolgálónak. A válasz érvénytelen, ha nem elemezhető, vagy hiányos. |
| 5xx hibák |Egy sávdiagram, amely lehetővé teszi, hogy megtekintse az első 10 olyan kérést, amely az 500-es tartományba tartozó válasz kódját eredményezte.  A jelentésből kizárt a 502 hibás átjáró és a 504-átjáró időtúllépési kódja. |

## <a name="see-also"></a>Lásd még
* [Azure tartalomkézbesítési hálózat (CDN) – áttekintés](cdn-overview.md)
* [Microsoft Azure CDN valós idejű statisztikái](cdn-real-time-stats.md)
* [Az alapértelmezett HTTP-viselkedés felülbírálása a szabályok motor használatával](./cdn-verizon-premium-rules-engine.md)
* [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)