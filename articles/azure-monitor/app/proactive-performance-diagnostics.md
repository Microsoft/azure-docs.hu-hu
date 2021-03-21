---
title: Intelligens észlelés – teljesítménybeli rendellenességek | Microsoft Docs
description: Application Insights az alkalmazás telemetria intelligens elemzését végzi, és figyelmezteti a lehetséges problémákra. Ehhez a szolgáltatáshoz nincs szükség beállításra.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: a055a323001e0291d3cb5c1716e640b3c8b21dbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573728"
---
# <a name="smart-detection---performance-anomalies"></a>Intelligens észlelés – teljesítménybeli rendellenességek

[Application Insights](./app-insights-overview.md) automatikusan elemzi a webalkalmazás teljesítményét, és figyelmezteti a lehetséges problémákra. Lehet, hogy ezt olvashatja, mert az intelligens észlelési értesítések egyikét kapta.

Ehhez a szolgáltatáshoz nincs szükség speciális beállításra, kivéve, ha az alkalmazást a Application Insightsra konfigurálja a [támogatott nyelvre](./platforms.md). Akkor aktív, ha az alkalmazás elég telemetria hoz létre.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Mikor kapok egy intelligens észlelési értesítést?

Application Insights azt észlelte, hogy az alkalmazás teljesítménye a következő módszerek egyikével csökken:

* **Válaszidő-romlás** – az alkalmazása lassabban reagált a kérelmekre, mint a használt. Előfordulhat, hogy a változás gyors volt, például azért, mert a legújabb üzemelő példányában regresszió történt. Vagy lehet, hogy fokozatosan történt, ami a memóriavesztés okozta. 
* **Függőségi időtartam csökkenése** – az alkalmazás kezdeményezi a REST API, az adatbázis vagy más függőség meghívását. A függőség a által használtnál lassabban reagál.
* **Lassú teljesítményű minta** – az alkalmazás úgy tűnik, hogy a teljesítménnyel kapcsolatos probléma csak néhány kérelmet érint. Például a lapok lassabban töltődnek be az egyik böngészőben, mint a többinél. vagy a kérelmek lassabban szolgálnak egy adott kiszolgálóról. Az algoritmusok jelenleg az oldal betöltési idejének, a válaszadási időpontok és a függőségek válaszideje alapján jelennek meg.  

Az intelligens észlelésnek legalább 8 napos telemetria kell lennie egy működőképes köteten a normál teljesítmény alapkonfigurációjának létrehozásához. Tehát ha az alkalmazás már fut az adott időszakra, akkor az összes jelentős probléma egy értesítést fog eredményezni.


## <a name="does-my-app-definitely-have-a-problem"></a>Az alkalmazásom biztosan probléma?

Nem, egy értesítés nem azt jelenti, hogy az alkalmazásnak feltétlenül van problémája. Ezek csupán javaslatok, hogy az adott anomáliát érdemes részletesebben megvizsgálni.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?

Az értesítések közé tartoznak a diagnosztikai információk. Bemutatunk egy példát:


![Íme egy példa a kiszolgálói válaszidő-romlás észlelésére](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Osztályozás**. Az értesítésben láthatja, hogy hány felhasználó vagy hány műveletet érint. Ez segíthet a probléma prioritásának hozzárendelésében.
2. **Hatókör**. A probléma hatással van az összes forgalomra, vagy csak néhány oldalra? Bizonyos böngészőkre vagy helyszínekre korlátozódik? Ezeket az információkat az értesítésből lehet beszerezni.
3. **Diagnosztika**. Gyakran előfordul, hogy az értesítésben szereplő diagnosztikai információk a probléma természetét fogják javasolni. Ha például a válaszidő lelassul, ha a kérések sebessége magas, akkor a kiszolgáló vagy a függőségek túlterhelését sugallják. 

    Ellenkező esetben nyissa meg Application Insights teljesítmény paneljét. Itt megtalálhatja a [Profiler](profiler.md) -adatgyűjtést. Kivételek eldobása esetén a [Snapshot debuggert](./snapshot-debugger.md)is kipróbálhatja.



## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Az intelligens észlelési értesítések alapértelmezés szerint engedélyezve vannak, és a rendszer elküldte azokat a felhasználókat, akik [figyelik az olvasót](../../role-based-access-control/built-in-roles.md#monitoring-reader) , és [figyelik a közreműködői](../../role-based-access-control/built-in-roles.md#monitoring-contributor) hozzáférést ahhoz az előfizetéshez, amelyben a Application Insights erőforrás található. Ennek módosításához kattintson a configure ( **Konfigurálás** ) elemre az e-mail-értesítésben, vagy nyissa meg a Application Insights intelligens észlelési beállításait. 
  
  ![Intelligens észlelési beállítások](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Az e-mail-értesítések fogadásának leállításához használhatja az intelligens észlelési e-mailben található **leiratkozási** hivatkozást.

Az intelligens Észlelésekkel kapcsolatos e-mailek száma Application Insights erőforráson naponta egy e-mailre korlátozódik. Az e-mail csak akkor lesz elküldve, ha van legalább egy új, az adott napon észlelt probléma. Egyetlen üzenet sem jelenik meg. 

## <a name="faq"></a>GYIK

* *Tehát a Microsoft munkatársai megvizsgálják az adataikat?*
  * Nem. A szolgáltatás teljesen automatikus. Csak Ön kapja meg az értesítéseket. Az adatai [magánjellegűek](./data-retention-privacy.md).
* *Elemzi az Application Insights által összegyűjtött összes adatokat?*
  * Jelenleg nem. Jelenleg elemezzük a kérelem válaszideje, a függőségi válaszidő és az oldal betöltési ideje. A további mérőszámok elemzése a várakozó várakozási sorban áll.

* Milyen típusú alkalmazásokhoz működik ez?
  * Ezek a romlások minden olyan alkalmazásban észlelhetők, amely a megfelelő telemetria hozza létre. Ha a webalkalmazásban Application Insights telepített, akkor a rendszer automatikusan nyomon követi a kérelmeket és a függőségeket. Ha azonban a háttér-szolgáltatásokban vagy más alkalmazásokban a [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) vagy a [TrackDependency](./api-custom-events-metrics.md#trackdependency)hívásait szúrta be, akkor az intelligens észlelés ugyanúgy fog működni.

* *Létrehozhatom a saját anomáliák észlelési szabályait, vagy testre is szabhatom a meglévő szabályokat?*

  * Még nem, de a következőket teheti:
    * [Riasztásokat állíthat be](../alerts/alerts-log.md) , amelyek azt jelzik, ha egy metrika átlép egy küszöbértéket.
    * [Exportálja a telemetria](./export-telemetry.md) egy [adatbázisba](./code-sample-export-sql-stream-analytics.md) vagy [Power BIba](./export-power-bi.md), ahol elemezheti saját maga is.
* *Milyen gyakran történik az elemzés?*

  * Az elemzést naponta futtatjuk a telemetria az előző naptól számítva (az UTC időzónában teljes nap).
* *Tehát ez a [metrikai riasztásokat](../alerts/alerts-log.md)váltja fel?*
  * Nem.  Nem vállalunk kötelezettséget arra, hogy észlelje az esetleges rendellenes viselkedést.


* *Ha nem teszek semmit egy értesítésre adott válaszban, értesítést kapok?*
  * Nem, az egyes problémákról csak egyszer kap üzenetet. Ha a probléma továbbra is fennáll, a rendszer frissíti az intelligens észlelési csatorna panelen.
* *Elfelejtettem az e-mailt. Hol találhatók az értesítések a portálon?*
  * Az alkalmazás Application Insights áttekintésében kattintson az **intelligens észlelés** csempére. Az összes értesítést akár 90 napig is megtalálhatja.

## <a name="how-can-i-improve-performance"></a>Hogyan javíthatom a teljesítményt?
A lassú és sikertelen válaszok a webhelyek felhasználóinak egyik legnagyobb frusztrációja, ahogy azt a saját felhasználói felületén ismeri. Ezért fontos, hogy foglalkozzon a problémákkal.

### <a name="triage"></a>Osztályozás
Először is számít? Ha egy oldal mindig lassan töltődik be, de a hely felhasználóinak csak 1%-a tekinti meg, talán még fontosabb dolgokra gondol. Másfelől, ha a felhasználók csak 1%-át nyitják meg, de a kivételeket minden alkalommal eldönti, érdemes lehet kivizsgálni.

Általános útmutatóként használja a Impact utasítást (érintett felhasználók vagy forgalom), de vegye figyelembe, hogy nem ez a teljes történet. Gyűjtsön más bizonyítékot a megerősítéshez.

Vegye figyelembe a probléma paramétereit. Ha földrajzilag függ, állítsa be a [rendelkezésre állási teszteket](./monitor-web-app-availability.md) , beleértve az adott régiót: az adott területen csak hálózati problémák merülhetnek fel.

### <a name="diagnose-slow-page-loads"></a>Lassú oldal terhelésének diagnosztizálása
Hol található a probléma? Lassú a kiszolgáló válasza, a lap nagyon hosszú, vagy a böngészőnek sok munkát kell tennie a megjelenítéshez?

Nyissa meg a böngészők metrika paneljét. A böngésző oldal betöltési idejének szegmentált megjelenítése megmutatja, hogy az idő hol zajlik. 

* Ha a **küldési kérelem ideje** magas, vagy a kiszolgáló lassan válaszol, vagy ha a kérelem sok adattal rendelkező bejegyzés. A válaszidő vizsgálatához tekintse meg a [teljesítmény mérőszámait](./performance-counters.md) .
* Állítsa be a [függőségek nyomon követését](./asp-net-dependencies.md) , hogy megtekintse, hogy a lassúság oka a külső szolgáltatások vagy az adatbázis.
* Ha a **Válasz fogadása** túlsúlyban van, a lap és a hozzá tartozó részek – JavaScript, CSS, képek és így tovább (de aszinkron módon nem tölthetők be). Állítson be egy [rendelkezésre állási tesztet](./monitor-web-app-availability.md), és győződjön meg arról, hogy a függő részek betöltésére vonatkozó beállítás be van állítva. Ha némi eredményt kap, nyissa meg az eredmény részleteit, és bontsa ki a különböző fájlok betöltési idejének megjelenítéséhez.
* A nagy **ügyfél-feldolgozási idő** azt sugallja, hogy a parancsfájlok lassan futnak. Ha az ok nem nyilvánvaló, vegyen fel némi időzítési kódot, és küldje el az időpontokat a trackMetric-hívásokban.

### <a name="improve-slow-pages"></a>Lassú lapok javítása
A kiszolgáló válaszait és az oldalak betöltési idejét a teljes körűen megtalálhatja, így nem fogjuk megismételni. Íme néhány tipp, amely valószínűleg már ismeri a-t, csak azért, hogy megismerje a következőket:

* Lassú betöltés a Big files miatt: a parancsfájlok és más részek aszinkron módon tölthetők be. Parancsfájl-árukapcsolás használata. Bontsa ki a Főoldalt a widgetek számára, amelyek külön töltik be az adatfájlokat. Ne küldjön egyszerű, régi HTML-t a hosszú táblákhoz: használjon egy parancsfájlt, amely JSON-ként vagy más kompakt formátumba kéri az adatkérést, majd töltse ki a táblázatot a helyén. Ebben az útmutatóban nagyszerű keretrendszerek érhetők el. (Természetesen a Big Scripts is magában foglalja.)
* Lassú kiszolgálói függőségek: vegye figyelembe az összetevők földrajzi elhelyezkedését. Ha például az Azure-t használja, győződjön meg arról, hogy a webkiszolgáló és az adatbázis ugyanabban a régióban található. Kérik a lekérdezések a szükségesnél több információt? Gyorsítótárazást vagy kötegelt segítséget szeretne?
* Kapacitással kapcsolatos problémák: Tekintse meg a kiszolgálói metrikákat és a kérelmek számát. Ha a válaszidő csúcsa nem arányos a kérelmek számának csúcsával, akkor valószínű, hogy a kiszolgálók ki vannak feszítve.


## <a name="server-response-time-degradation"></a>Kiszolgáló válaszidő-romlása

A válaszidő-romlási értesítés a következőket mutatja be:

* A művelethez képest a normál válaszidő-értékhez viszonyított válaszidő.
* Hány felhasználót érint a rendszer.
* Az észlelés napján a művelet átlagos válaszideje és 90%-os válaszidő, a megelőző 7 napban. 
* A műveletre vonatkozó kérelmek száma az észlelés napján és 7 nappal korábban.
* A művelet romlása és a kapcsolódó függőségek romlása közötti korreláció. 
* Hivatkozások a probléma diagnosztizálásához.
  * A Profiler nyomkövetései segítenek megtekinteni a működési idő elköltésének helyét (a hivatkozás akkor érhető el, ha az észlelési időszakban Profiler-nyomkövetési példákat gyűjtöttek be ehhez a művelethez.) 
  * Teljesítménymutatók a metrika Explorerben, ahol a művelethez a szeletek és a kockák időtartománya/szűrői érhetők el.
  * A hívás keresésével megtekintheti az adott hívási tulajdonságokat.
  * Hibajelentés – ha a Count > 1 Ez azt jelenti, hogy hiba történt a műveletben, ami a teljesítmény romlásához vezethetett volna.

## <a name="dependency-duration-degradation"></a>Függőségi időtartam csökkenése

A modern alkalmazások egyre nagyobb mértékben alkalmazzák a Micro Services kialakítási megközelítését, ami sok esetben nagy megbízhatóságot eredményez a külső szolgáltatásokon. Ha például az alkalmazás valamilyen adatplatformra támaszkodik, vagy ha saját robot-szolgáltatást készít, akkor valószínűleg egy kognitív szolgáltatót továbbít, amely lehetővé teszi, hogy a robotok több emberi módszert használják, és néhány adattárolási szolgáltatást a bot számára a válaszok lekéréséhez.  

Példa függőségi romlási értesítésre:

![Íme egy példa a függőségi időtartam romlásának észlelésére](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Figyelje meg, hogy a következőket mutatja be:

* A művelethez képest normál válaszidő-értékhez viszonyított időtartam
* Hány felhasználó érintett
* A függőség átlagos időtartama és 90%-os időtartama az észlelés napján és a megelőző 7 napban
* A függőségi hívások száma az észlelés napján és 7 nappal korábban
* A probléma diagnosztizálását segítő hivatkozások
  * Teljesítmény-jelentések a metrika-kezelőben ehhez a függőséghez
  * A függőségi hívások keresése a hívások tulajdonságainak megtekintéséhez
  * Hibajelentések – ha a Count > 1 Ez azt jelenti, hogy az észlelési időszak során sikertelen függőségi hívások történtek, amelyek az időtartam csökkenéséhez is hozzájárultak. 
  * Az elemzés megnyitása a függőségi időtartamot és a darabszámot kiszámító lekérdezésekkel  

## <a name="smart-detection-of-slow-performing-patterns"></a>Lassú teljesítményt nyújtó minták intelligens észlelése 

Application Insights megkeresi azokat a teljesítménnyel kapcsolatos problémákat, amelyek csak a felhasználók bizonyos részeit érintik, vagy bizonyos esetekben csak a felhasználókat érintik. Például a lapok betöltésével kapcsolatos értesítések lassabbak az egyik böngészőben, mint más típusú böngészőknél, vagy ha a kérelmek lassabban vannak kiszolgálva egy adott kiszolgálóról. Emellett az adott operációs rendszert használó ügyfelek számára is felderítheti a tulajdonságok kombinációjára vonatkozó problémákat, például a lassú lapok betöltését az egyik földrajzi területen.  

Az ilyen anomáliák nagyon nehezen észlelhetők az adatellenőrzéssel, de gyakrabban fordulnak elő, mint gondolná. Gyakran csak a felületet használják, ha az ügyfelek panaszkodnak. Ez idő alatt túl késő: az érintett felhasználók már áttérnek a versenytársakra.

Az algoritmusok jelenleg az oldal betöltési idejének, a kérések válaszideje a kiszolgálón és a függőségi válaszidő alapján néznek ki.  

Nem kell megadnia a küszöbértékeket, és nem kell konfigurálnia a szabályokat. A gépi tanulás és az adatbányászati algoritmusok a rendellenes minták észlelésére szolgálnak.

![Az e-mail riasztásban kattintson a hivatkozásra a diagnosztikai jelentés megnyitásához az Azure-ban](./media/proactive-performance-diagnostics/03.png)

* **Mikor** jelenik meg a probléma észlelésének időpontja.
* A **leírása:**

  * A észlelt probléma;
  * Az események azon készletének jellemzői, amelyek a probléma viselkedését jelenítik meg.
* A tábla összehasonlítja a gyengén teljesítő készletet az összes többi esemény átlagos viselkedésével.

Kattintson a hivatkozásokra a metrika-kezelő megnyitásához és a megfelelő jelentésekben való kereséshez, a lassú teljesítményű készlet időpontjában és tulajdonságaiban szűrve.

Módosítsa az időtartományt és a szűrőket a telemetria megismeréséhez.

## <a name="next-steps"></a>Következő lépések
Ezek a diagnosztikai eszközök segítenek megvizsgálni a telemetria az alkalmazásból:

* [Profilkészítő](profiler.md) 
* [Pillanatkép-hibakereső](./snapshot-debugger.md)
* [Elemzés](../logs/log-analytics-tutorial.md)
* [Analitika – intelligens diagnosztika](../logs/log-query-overview.md)

Az intelligens észlelések teljesen automatikusak. De lehet, hogy néhány riasztást szeretne beállítani?

* [Manuálisan konfigurált metrikai riasztások](../alerts/alerts-log.md)
* [Rendelkezésre állási webes tesztek](./monitor-web-app-availability.md)