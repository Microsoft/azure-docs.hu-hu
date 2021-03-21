---
title: Webalkalmazások teljesítményének monitorozása – Azure Application Insights
description: Hogyan illeszkedik a Application Insights a devOps ciklusba
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: bc897cc0259894964e0b6164219b1e5459e824b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579559"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Webalkalmazások és szolgáltatások részletes diagnosztikája az Application Insights szolgáltatással
## <a name="why-do-i-need-application-insights"></a>Miért van szükség a Application Insightsre?
Application Insights figyeli a futó webalkalmazást. Ismerteti a hibákat és a teljesítménnyel kapcsolatos problémákat, és segít elemezni, hogyan használják az ügyfelek az alkalmazást. A sok platformon (ASP.NET, Java EE, Node.js,...) futó alkalmazások esetében működik, és a felhőben vagy a helyszínen üzemel. 

![A webalkalmazások kézbesítésének bonyolultsági szempontjai](./media/devops/010.png)

Alapvető fontosságú a modern alkalmazások figyelése a futás közben. A legfontosabb, hogy az ügyfelek többsége előtt észlelni fogja a hibákat. Emellett érdemes felderíteni és elhárítani a teljesítménnyel kapcsolatos problémákat is, amelyek nem katasztrofálisak, talán lassítják a dolgokat, vagy valamilyen kényelmetlenséget okoznak a felhasználóknak. Ha a rendszer megfelel az Ön elégedettségének, tudni szeretné, hogy mit csinálnak a felhasználók? a legújabb szolgáltatást használják? Sikerül?

A modern webalkalmazások folyamatos kézbesítési ciklusban lettek kifejlesztve: új funkció vagy fejlesztés kiadása; Figyelje meg, hogy milyen jól működik a felhasználók számára; az ismeret alapján tervezze meg a fejlesztés következő lépését. A ciklus egyik kulcsfontosságú része a megfigyelési fázis. A Application Insights a webalkalmazások teljesítményének és használatának figyelésére szolgáló eszközöket biztosít.

Ennek a folyamatnak a legfontosabb aspektusa a diagnosztika és a diagnosztika. Ha az alkalmazás meghibásodik, akkor az üzlet elvész. A figyelési keretrendszer elsődleges szerepe ezért a hibák megbízható észlelése, azonnali értesítés, valamint a probléma diagnosztizálásához szükséges információk benyújtása. Pontosan ez a Application Insights.

### <a name="where-do-bugs-come-from"></a>Honnan származnak a hibák?
A webrendszerekben előforduló hibák jellemzően a konfigurációs problémákból vagy a sok összetevője közötti rossz interakcióból származnak. Az élő hely bekövetkezésekor felmerülő első feladat ezért a probléma irányának megállapítása: melyik összetevő vagy kapcsolat okozza a problémát?

Néhány minket, akik szürke hajjal rendelkeznek, megjegyezhető egy egyszerűbb korszak, amelyben egy számítógép programja egy számítógépen futott le. A fejlesztők alaposan tesztelik a szállítás előtt; és elküldte azt, ritkán tekinti vagy gondolja újra. A felhasználóknak sok évig kellene megtenniük a fennmaradó hibákat. 

A dolgok mostantól nagyon különbözőek. Az alkalmazás számos különböző eszközön fut, és nehéz lehet garantálni, hogy mindegyiken ugyanaz a viselkedés. Az alkalmazások felhőben történő üzemeltetése azt jelenti, hogy a hibák gyorsan megoldhatók, de a folyamatos verseny és az új funkciók várhatóan gyakori időközönként is megmaradnak. 

Ezekben a feltételekben a hibák számának megtartása egyetlen módja az automatizált egység tesztelése. Nem lehet manuálisan újra tesztelni minden kézbesítést. Az egység tesztelése mostantól a felépítési folyamat egy általános része. Az eszközök, például a Xamarin Test Cloud segítséget nyújtanak, ha automatizált felhasználói felületi tesztelést biztosít több böngészős verzióban. Ezek a tesztelési rendszerek lehetővé teszik, hogy reméljük, hogy az alkalmazáson belül talált hibák aránya minimálisan megtartható.

A tipikus webalkalmazások sok élő összetevővel rendelkeznek. Az ügyfélen (egy böngészőben vagy egy eszköz alkalmazásban) és a webkiszolgálón kívül valószínűleg jelentős a háttér-feldolgozás. Lehet, hogy a háttér az összetevők egyik folyamata, vagy egy olyan lazább gyűjtemény, amely összegyűjti a közös elemeket. És ezek közül sokan nem lesznek a vezérlőben – ezek olyan külső szolgáltatások, amelyektől függ.

A hasonló konfigurációk esetében nehéz és gazdaságtalan lehet a teszteléshez, vagy az összes lehetséges meghibásodási mód tesztelése, mint a maga az élő rendszer. 

### <a name="questions-"></a>Kérdések...
Néhány kérdés, amelyet a webes rendszer fejlesztésekor kérünk:

* Összeomlik az alkalmazás? 
* Pontosan mi történt? – Ha sikertelen volt egy kérelem, szeretném tudni, hogy van-e itt. Az események nyomkövetésére van szükség...
* Elég gyors az alkalmazásom? Mennyi ideig tart a tipikus kérelmekre való reagálás?
* Képes a kiszolgáló kezelni a terhelést? Ha a kérések aránya növekszik, a válaszidő stabilan tart?
* A függőségi viszonyok – a REST API-k, adatbázisok és az alkalmazás által meghívást más összetevők. Különösen, ha a rendszer lassú, a saját összetevő, vagy kapok lassú válaszokat valakitől?
* Fel vagy le van-e az alkalmazásom? A világ minden tájáról látható? Tudassa velem, ha leáll...
* Mi a kiváltó ok? Hiba történt az összetevőben vagy a függőségben? Kommunikációs probléma?
* Hány felhasználót érint a rendszer? Ha egynél több problémát kell megoldani, ami a legfontosabb?

## <a name="what-is-application-insights"></a>Mi az Application Insights?
![Application Insights alapszintű munkafolyamata](./media/devops/020.png)

1. Application Insightsi az alkalmazást, és az alkalmazás futása közben telemetria küld róla. Vagy felépítheti az Application Insights SDK-t az alkalmazásba, vagy a rendszerállapot-készítést is alkalmazhatja futásidőben. A korábbi módszer rugalmasabb, hiszen saját telemetria is hozzáadhat a normál modulokhoz.
2. A rendszer elküldi a telemetria a Application Insights-portálra, ahol a tároló és a feldolgozás történik. (Bár a Application Insights a Microsoft Azureban található, a webalkalmazások nem csak Azure-alkalmazások figyelésére használhatók.)
3. A telemetria diagramokat és eseményeket tartalmazó táblázatok formájában jelenik meg.

A telemetria két fő típusa létezik: összesített és nyers példányok. 

* A példányok adatai tartalmazzák például a webalkalmazás által fogadott kérelem jelentését. A Application Insights portál keresési eszközével megkeresheti és megvizsgálhatja a kérelmek részleteit. A példány olyan adatmennyiséget foglal magában, mint például az, hogy mennyi ideig tartott az alkalmazás válasza a kérésre, valamint a kért URL-cím, az ügyfél hozzávetőleges helye és más adat.
* Az összesített adatmennyiségek az egyes időegységek számát tartalmazzák, így összehasonlíthatja a kérelmek arányát a válaszadási idővel. Emellett olyan mérőszámok átlagait is tartalmazza, mint a kérelmek válaszideje.

A fő adatkategóriák a következők:

* Kérelmek az alkalmazáshoz (általában HTTP-kérelmek), az URL-cím, a válaszidő, valamint a sikeres vagy sikertelen műveletekhez.
* Függőségek – az alkalmazás által végrehajtott REST-és SQL-hívások, valamint URI-, válaszidő-és sikerességi műveletek
* Kivételek, beleértve a verem nyomkövetését.
* Az oldal nézetének adatait, amelyek a felhasználók böngészőből származnak.
* Metrikák, például teljesítményszámlálók, valamint a saját maga által írt mérőszámok. 
* Az üzleti események nyomon követésére használható egyéni események
* Hibakereséshez használt nyomkövetési naplók.

## <a name="case-study-real-madrid-fc"></a>Esettanulmány: Real Madrid FC
A [Real Madrid Football Club](https://www.realmadrid.com/) webszolgáltatása körülbelül 450 000 000 rajongót szolgál ki világszerte. A szurkolók a webböngészőkben és a Club Mobile apps szolgáltatásban is hozzáférnek. A rajongók nem csak jegyeket vásárolhatnak, de az eredmények, a játékosok és a közelgő játékok adataihoz és videoklipekhez is hozzáférhetnek. Kereshetnek szűrőkkel, például a gólok számával. A közösségi médiára is hivatkozik. A felhasználói élmény nagyon személyre szabott, és úgy van kialakítva, hogy kétirányú kommunikációt folytasson a rajongókkal.

A megoldás a [Microsoft Azure szolgáltatások és alkalmazások rendszere](https://www.microsoft.com/inculture/sports/real-madrid/). A skálázhatóság kulcsfontosságú követelmény: a forgalom változó, és a találatok között nagyon nagy mennyiségeket érhet el.

A Real Madrid esetében elengedhetetlen a rendszer teljesítményének figyelése. Az Azure Application Insights átfogó áttekintést nyújt a rendszerről, biztosítva a megbízható és magas szintű szolgáltatást. 

A klub emellett részletesen ismerteti a rajongóit is: ahol (csak 3% van Spanyolországban), milyen érdeklődéssel rendelkeznek a játékosok, a korábbi eredmények és a közelgő játékok esetében, és hogyan reagálnak a találatokra.

A telemetria-adatok nagy részét automatikusan gyűjti a rendszer a hozzáadott kód nélkül, ami leegyszerűsíti a megoldást, és csökkenti a működési bonyolultságot.  A Real Madrid esetében Application Insights havonta 3 800 000 000 telemetria ponttal foglalkozik.

A Real Madrid a Power BI modult használja a telemetria megtekintéséhez.

![Application Insights telemetria Power BI nézete](./media/devops/080.png)

## <a name="smart-detection"></a>Intelligens észlelés
A [proaktív diagnosztika](./proactive-diagnostics.md) egy közelmúltbeli szolgáltatás. A speciális beállítások nélkül a Application Insights automatikusan észleli és riasztást küld arról, hogy az alkalmazásban előforduló meghibásodási arányok szokatlanul növekednek. Elég okos ahhoz, hogy figyelmen kívül hagyják az alkalmi hibák hátterét, és a kérések növekedésével arányos mértékben is emelkedni lehessen. Így például, ha az egyik olyan szolgáltatás meghibásodik, amelytől függ, vagy ha az imént üzembe helyezett új Build nem működik megfelelően, akkor az e-mail-cím megkeresése után azonnal tudni fogja. (És vannak webhookok, amelyekkel más alkalmazásokat indíthat el.)

A szolgáltatás egy másik aspektusa a telemetria naponta részletes elemzését végzi, amely a nehezen felderíthető, szokatlan teljesítményű mintákat keresi. Például megtalálhatja az adott földrajzi területhez tartozó lassú teljesítményt vagy egy adott böngésző verziószámát.

Mindkét esetben a riasztás nem csupán a felderített tüneteket jelzi, hanem a probléma diagnosztizálásához szükséges adatokat is megadja, például a vonatkozó kivételekről szóló jelentéseket.

![Az proaktív diagnosztika e-mail-címe](./media/devops/030.png)

A Customer Samtec azt mondta, hogy "a közelmúltban a szolgáltatás átváltás egy olyan, az erőforrás-korlátokat és az időtúllépéseket kiváltó, alulra méretezett adatbázist találtunk. A proaktív észlelési riasztások a szó szoros értelmében osztályozásakor a problémát, nagyon közel valós időben, ahogy meghirdették. Ez a riasztás az Azure platform-riasztásokkal párosulva gyakorlatilag azonnal kijavította a problémát. A teljes állásidő < 10 percet vesz igénybe. "

## <a name="live-metrics-stream"></a>Élő metrikastream
A legújabb buildek üzembe helyezése ideges élmény lehet. Ha bármilyen probléma merül fel, azonnal érdemes tudni róluk, hogy szükség esetén biztonsági mentést végezzen. A Élő metrikastream egy másodperces késéssel rendelkező fő mérőszámokat biztosít.

![Élő metrikák](./media/devops/0040.png)

És lehetővé teszi, hogy azonnal megvizsgálja a hibák vagy kivételek mintáját.

![Élő hibák eseményei](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Alkalmazástérkép
Az alkalmazás-hozzárendelés automatikusan felderíti az alkalmazás-topológiát, és rámutat a teljesítményre, és lehetővé teszi, hogy könnyedén azonosítsa a teljesítmény szűk keresztmetszeteit és a problémás folyamatokat az elosztott környezetben. Lehetővé teszi az alkalmazások függőségeinek felderítését az Azure-szolgáltatásokban. A probléma osztályozása a kód és a függőség összekapcsolása, valamint a kapcsolódó diagnosztika felhasználói felületének egyhelyes részletezése alapján. Előfordulhat például, hogy az alkalmazás meghibásodik az SQL-szinten a teljesítmény romlása miatt. Az Application Map használatával azonnal megtekintheti az SQL Index Advisor vagy a lekérdezési elemzések élményét.

![Alkalmazástérkép](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analitika
Az [Analytics](../logs/log-query-overview.md)segítségével tetszőleges lekérdezéseket írhat egy hatékony SQL-szerű nyelven.  A teljes alkalmazás-verembe való Diagnosztizálás számos különböző perspektívát biztosít, és a szolgáltatás teljesítményének az üzleti metrikákkal és az ügyfelekkel való összekapcsolásával kapcsolatos kérdéseket is megteheti. 

A portálon tárolt összes telemetria-példány és metrikus nyers adat lekérdezhető. A nyelv magában foglalja a szűrést, a csatlakozást, az összesítést és az egyéb műveleteket. Kiszámíthatja a mezőket és statisztikai elemzéseket végezhet. Táblázatos és grafikus megjelenítés is használható.

![Elemzési lekérdezés és eredmények diagramja](./media/devops/0025.png)

Például egyszerűen:

* Az alkalmazásra vonatkozó kérelmek teljesítményadatokat az ügyfél szintjei szerint szegmentálhatja, hogy megértse a felhasználói élményt.
* Adott hibakódok vagy egyéni események nevének keresése az élő hely vizsgálata során.
* A szolgáltatások beszerzésének és elfogadásának megismeréséhez részletezheti az egyes ügyfelek alkalmazás-használati feladatait.
* A munkamenetek és a válaszadási idő nyomon követésével adott felhasználók számára engedélyezheti a támogatási és az operatív csapatokat, hogy azonnali támogatást nyújtsanak.
* Határozza meg a gyakran használt alkalmazások funkcióit a funkciók rangsorolásával kapcsolatos kérdések megválaszolásához.

A Customer DNN azt mondta, hogy "Application Insights megadta nekünk az egyenlet hiányzó részét, hogy szükség szerint lehessen egyesíteni, rendezni, lekérdezni és szűrni az adatmennyiséget. A csapatunk lehetővé teszi, hogy a saját találékonyságát és tapasztalatait egy hatékony lekérdezési nyelven megtalálják, és lehetővé tettük az elemzések megkeresését és a problémák megoldását. Sok érdekes választ kaphat a *"kíváncsi vagyok, ha..*." kezdetű kérdésekről.

## <a name="development-tools-integration"></a>Fejlesztői eszközök integrációja
### <a name="configuring-application-insights"></a>Application Insights konfigurálása
A Visual Studio és az Eclipse eszközeivel konfigurálhatja a fejlesztés alatt álló projekthez megfelelő SDK-csomagokat. Application Insights hozzáadására szolgáló menüparancs van.

Ha olyan nyomkövetési naplózási keretrendszert használ, mint például a Log4N, a NLog vagy a System. Diagnostics. Trace, akkor a naplókat elküldheti Application Insights a többi telemetria együtt, így könnyedén korrelálhatja a kérelmekkel, a függőségi hívásokkal és a kivételekkel kapcsolatos nyomkövetéseket.

### <a name="search-telemetry-in-visual-studio"></a>Telemetria keresése a Visual Studióban
Egy szolgáltatás fejlesztése és hibakeresése során közvetlenül a Visual Studióban tekintheti meg és keresheti meg a telemetria, ugyanúgy, mint a webes portálon.

Ha Application Insights naplóz egy kivételt, megtekintheti az adatpontot a Visual Studióban, és közvetlenül a megfelelő kódra ugorhat.

![Visual Studio-keresés](./media/devops/060.png)

A hibakeresés során megtarthatja a telemetria a fejlesztői gépen, megtekintheti azt a Visual Studióban, de anélkül, hogy elküldené a portálra. Ezzel a helyi lehetőséggel elkerülhető a hibakeresés a termelési telemetria.

### <a name="work-items"></a>Munkaelemek
Ha riasztást vált ki, Application Insights automatikusan létrehozhat egy munkaelemet a munkahelyi követési rendszeren.

## <a name="but-what-about"></a>De mi a helyzet...?
* [Adatvédelem és tárolás](./data-retention-privacy.md) – a telemetria az Azure biztonságos kiszolgálókon tároljuk.
* Teljesítmény – a hatás nagyon alacsony. A telemetria kötegelt.
* [Díjszabás](./pricing.md) – megkezdheti az ingyenes használatot, ami továbbra is kis mennyiségű.


## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
A Application Insights első lépései egyszerűek. A fő lehetőségek a következők:

* [IIS-kiszolgálók](./monitor-performance-live-website-now.md)és [Azure app Service](./app-insights-overview.md)esetén is.
* A projekt fejlesztése a fejlesztés során. Ezt megteheti a [ASP.net](./asp-net.md) vagy [Java](./java-get-started.md) -alkalmazásokhoz, valamint a [Node.js](./nodejs.md) és [más típusú](./platforms.md)gépekhez is. 
* [Bármely weblap](./javascript.md) egy rövid kódrészlet hozzáadásával.

