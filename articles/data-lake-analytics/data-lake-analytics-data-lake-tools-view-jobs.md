---
title: A böngésző & Job View-Azure Data Lake Analytics használata
description: Ez a cikk azt ismerteti, hogyan használható a Azure Data Lake Analytics feladatokhoz tartozó feladat-tallózó és feladat nézet.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/02/2017
ms.openlocfilehash: a1e9a9df4c2ec57dfeec8cf5ddd5348228b9cc3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018562"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>A feladatböngésző és a Feladat nézet használata az Azure Data Lake Analyticsben
A Azure Data Lake Analytics szolgáltatás archivált feladatokat küld egy lekérdezési tárolóban. Ebből a cikkből megtudhatja, hogyan használhatja a feladatok böngészőjét és a feladatok nézetét a Visual studióhoz készült Azure Data Lake-eszközökben a korábbi feladatok adatainak megkereséséhez. 

Alapértelmezés szerint a Data Lake Analytics szolgáltatás 30 napig archiválja a feladatokat. A lejárati időszak a Azure Portal konfigurálható a testreszabott elévülési szabályzat konfigurálásával. A lejárati idő lejárta után nem fog tudni hozzáférni a feladathoz. 

## <a name="prerequisites"></a>Előfeltételek
Lásd: [Data Lake Tools for Visual Studio előfeltételek](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>A feladatokhoz tartozó böngésző megnyitása
A **>Azure>Data Lake Analytics>feladatok** a Visual Studióban való eléréséhez nyissa meg a feladatot a böngészőn keresztül.  A böngésző használatával elérheti egy Data Lake Analytics fiók lekérdezési tárolóját. A feladat böngészője a bal oldalon jeleníti meg a lekérdezési tárolót, amely az alapszintű feladatok adatait és a feladat nézetét mutatja be a részletes feladatra vonatkozó információk

## <a name="job-view"></a>Feladatok nézet
A feladatok nézet a feladatok részletes információit jeleníti meg. A feladatok megnyitásához kattintson duplán a feladatokra a böngészőben, vagy nyissa meg a Data Lake menüből a feladatok nézet elemre kattintva. Ekkor megjelenik egy párbeszédpanel, amely a feladatok URL-címével van feltöltve.

![Data Lake Tools Visual Studio-feladatok böngészője](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A feladatok nézete a következőket tartalmazza:

* Feladat összegzése
  
    Frissítse a feladat nézetet a futó feladatok újabb információinak megtekintéséhez.
  
  * Feladatok állapota (gráf):
    
      A feladatok állapota a feladatok fázisait ismerteti:
    
      ![Képernyőkép, amely a Azure Data Lake Analytics feladatokra vonatkozó fázisokat mutatja.](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Felkészülés: töltse fel a szkriptet a felhőbe, a szkript fordítását és optimalizálását a fordítási szolgáltatás használatával.
    * Várakozási sorban: a feladatok várólistára kerülnek, ha elegendő erőforrásra várnak, vagy ha a feladatok túllépik az egyidejű feladatok maximális számát. A prioritási beállítás határozza meg a várólistán lévő feladatok sorrendjét – minél kisebb számot, annál magasabb prioritást.
    * Fut: a művelet ténylegesen fut a Data Lake Analytics-fiókjában.
    * Véglegesítés: a rendszer befejezi a feladatot (például véglegesíti a fájlt).
      
      A feladat minden fázisban sikertelen lehet. Például fordítási hibák az előkészítési fázisban, időtúllépési hibák a várólistán lévő fázisban, illetve végrehajtási hibák a futó fázisban stb.
  * Alapvető adatok
    
      Az alapszintű feladat adatai a feladat összegzése panel alsó részén láthatók.
    
      ![Képernyőkép, amely a feladatok összegzését jeleníti meg a szövegmezők leírásával.](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * A feladatok eredménye: sikeres vagy sikertelen. Előfordulhat, hogy a feladat minden fázisban meghiúsul.
    * Teljes időtartam: az idő beküldése és a Befejezés időpontja közötti falióra idő (időtartam).
    * Teljes számítási idő: a csúcspontok végrehajtási idejének összege, amelyet úgy tekinthet meg, ahogy a feladatot csak egy csúcsponton hajtja végre. A csúcspontokkal kapcsolatos további információkért tekintse meg a teljes csúcspontokat.
    * Elküldés/Kezdés/Befejezés időpontja: az az idő, amikor a Data Lake Analytics szolgáltatás megkapja a feladatot, illetve elindítja a feladatot, vagy a feladatot sikeresen befejezi vagy nem.
    * Fordítás/aszinkron/futó: az előkészítési/várólistán lévő/futó fázisban töltött falióra-idő.
    * Fiók: a feladatok futtatásához használt Data Lake Analytics fiók.
    * Szerző: az a felhasználó, aki elküldte a feladatot, lehet valódi személy fiókja vagy rendszerfiókja.
    * Prioritás: a feladat prioritása. Minél kisebb a szám, annál magasabb a prioritás. Ez csak a várólistán lévő feladatok sorrendjére van hatással. A magasabb prioritású beállítás nem megelőzik futó feladatokat.
    * Párhuzamosság: az egyidejű Azure Data Lake Analytics egységek (ADLAUs-EK), más néven a csúcspontok maximális száma. Jelenleg az egyik csúcspont a két virtuális mag és a hat GB RAM-mal rendelkező virtuális gépekkel egyenlő, bár ez a későbbi Data Lake Analytics frissítésekben is frissíthető.
    * Bal oldali bájtok: a feladatok befejezéséig feldolgozandó bájtok.
    * Olvasott/megírt bájtok: bájtok, amelyeket a rendszer a feladatok elindítása óta olvasott/írt.
    * Összes csúcspont: a feladatnak sok munkadarabja van, és minden egyes munkafolyamatot csúcspontnak nevezzük. Ez az érték azt írja le, hogy a feladatból hány darab működik. A csúcspontokat alapszintű feldolgozási egységként, más néven Azure Data Lake Analytics egységként (ADLAU) is megtekintheti, a csúcspontok pedig párhuzamosan futtathatók. 
    * Befejezett/futó/sikertelen: a befejezett/futó/sikertelen csúcspontok száma. A csúcspontok a felhasználói kód és a rendszerhibák miatt sikertelenek lehetnek, de a rendszer néhányszor újrapróbálkozik a sikertelen csúcspontok újrapróbálkozásával. Ha az Újrapróbálkozás után is sikertelen volt a csúcspont, a teljes feladat sikertelen lesz.
* Feladatok gráfja
  
    A U-SQL-szkript a bemeneti adatok kimeneti adatokra alakításának logikáját jelöli. A szkript fordítása és optimalizálása az előkészítési fázisban található fizikai végrehajtási tervre történik. A feladatok gráfja a fizikai végrehajtási terv megjelenítése.  Az alábbi ábra a folyamatot szemlélteti:
  
    ![Azure Data Lake Analytics a feladatok fázisainak állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    A feladatok sok munkára vannak bontva. Minden egyes munkadarabot Csúcspontnak nevezünk. A csúcspontok a Super Vertex (más néven szakasz), és a feladatokkal való vizualizációk. A feladatütemezés zöld szakasza megjeleníti a szakaszokat.
  
    Egy adott szakasz minden csúcspontja ugyanolyan jellegű munkát végez, mint az azonos adatok különböző darabjai. Ha például van egy TB-adatokkal rendelkező fájlja, és több száz csúcspontja van, akkor mindegyik egy adatrészletet olvas. Ezek a csúcspontok ugyanabba a szakaszba vannak csoportosítva, és ugyanazon a bemeneti fájl különböző részein működnek.
  
  * <a name="state-information"></a>Szakasz adatai
    
      Egy adott szakaszban néhány szám jelenik meg a plakáton.
    
      ![Azure Data Lake Analytics a feladatok gráfjának fázisa](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1-kivonat: egy szám és egy műveleti metódus által elnevezett szakasz neve.
    * 84 csúcsok: ebben a szakaszban a csúcspontok teljes száma. Az ábra azt jelzi, hogy a rendszer hány munkadarabot oszt szét ebben a fázisban.
    * 12,90 s/Vertex: a szakasz átlagos csúcspont-végrehajtási ideje. Ezt az értéket a SUM (minden csúcspont végrehajtási idő)/(teljes csúcspontok száma) alapján számítjuk ki. Ez azt jelenti, hogy ha a párhuzamosan végrehajtott összes csúcspontot hozzárendelheti, a teljes szakasz 12,90 s-ban fejeződik be. Ez azt is jelenti, hogy az ebben a szakaszban található összes munka sorosan van-e, a díj #vertices * átlagos idő.
    * 850 895 sor írva: ebben a szakaszban írt teljes sorok száma.
    * R/W: az ebben a fázisban olvasott/írt adatmennyiség bájtban megadva.
    * Színek: a fázisban a színek a különböző csúcspontok állapotának jelzésére használatosak.
      
      * A zöld érték azt jelzi, hogy a csúcspont sikeres.
      * A narancssárga érték azt jelzi, hogy a csúcspont újból próbálkozik. Az újrapróbált csúcspont meghiúsult, de a rendszer automatikusan újrapróbálkozik, és a teljes szakasz sikeresen befejeződött. Ha a csúcspont újrapróbálkozik, de a hiba továbbra is sikertelen, a szín pirosra vált, és az egész feladatot nem sikerült végrehajtani.
      * A piros jelzi a hibát, ami azt jelenti, hogy egy bizonyos csúcspont többször is próbálkozott a rendszeren, de még sikertelen volt. Ez a forgatókönyv azt eredményezi, hogy a teljes feladat meghiúsul.
      * A kék érték azt jelenti, hogy egy adott csúcspont fut.
      * A fehér érték azt jelzi, hogy a csúcspont várakozik. Előfordulhat, hogy a csúcspont a ADLAU elérhetővé válását követően ütemezhető, vagy a bemenetre vár, mert a bemeneti adatok esetleg nem állnak készen.
      
      A fázissal kapcsolatban további részleteket talál, ha az egérmutatót az egyik állam fölé helyezi:
      
      ![Azure Data Lake Analytics a feladatok gráf szakaszának részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Csúcspontok: leírja a csúcspontok részleteit, például azt, hogy hány csúcspontot, hány csúcspontot töltöttek le, sikertelenek vagy még futnak/várnak, stb.
  * Adatolvasások Cross/intra pod: a fájlok és az információk tárolása több hüvelyben történik az elosztott fájlrendszerben. Az itt megadott érték azt írja le, hogy az adott Pod-vagy Cross Pod-ban mennyi információ van beolvasva.
  * Teljes számítási idő: a szakasz minden csúcspont-végrehajtási idejének összege a fázisban azt veszi figyelembe, hogy az adott fázisban lévő összes művelet csak egy csúcsponton legyen végrehajtva.
  * Írás/olvasás: az olvasott/olvasható sorok mennyisége azt jelzi, hogy az adatmennyiség vagy a sorok olvasása/írása megtörtént-e.
  * Vertex olvasási hibák: azt mutatja, hogy az adatolvasás során hány csúcspontra van hiba.
  * Töréspontok ismétlődő elvetése: Ha egy csúcspont túl lassú, a rendszer több csúcspontot is ütemezhet, hogy ugyanazt a munkát futtassa. Ha a csúcspontok egyike sikeresen befejeződött, a rendszer elveti a visszavonási csúcspontokat. A töréspontok ismétlődő elvetései rögzítik a fázisban ismétlődésként elvetett csúcspontok számát.
  * Csúcspont-visszavonások: a csúcspont sikeresen megtörtént, de néhány ok miatt később újra kell futtatni. Ha például az alsóbb szintű csúcspont elveszti a köztes bemeneti adatokat, a rendszer a felsőbb rétegbeli csúcspontot kéri újra.
  * Csúcspont-ütemezési végrehajtások: a csúcspontok ütemezett teljes ideje.
  * Minimális/átlagos/maximális Vertex-adatolvasás: a csúcspontok összes olvasási értékének minimum/átlaga/maximális száma.
  * Időtartam: a lépés elvégzése után a fal órája, az érték megtekintéséhez be kell töltenie a profilt.
  * Feladat visszajátszása
    
      Data Lake Analytics futtatja a feladatokat, és archiválja a feladatok információit futtató csúcspontokat, például a csúcspontok elindítását, leállítását, leállását és azok újrapróbálkozását stb. Az összes információ automatikusan bekerül a lekérdezési tárolóba, és a profiljában tárolódik. A feladattípust letöltheti a "betöltési profil" használatával a feladatok nézetben, és a feladattípus letöltése után megtekintheti a feladatok lejátszását.
    
      A feladatok lejátszási listája a fürtben történtek megtestesülését ábrázolja. Segít megtekinteni a feladatok végrehajtási folyamatát, és a teljesítménybeli rendellenességek és szűk keresztmetszetek vizuális észlelése nagyon rövid idő alatt (kevesebb, mint 30-as általában).
  * Feladatokhoz tartozó hő Térkép megjelenítése 
    
      A feladatokhoz tartozó Heat Map a feladatütemezés megjelenítési legördülő menüjéből választható ki. 
    
      ![Azure Data Lake Analytics a feladatok diagramjának heap térképének megjelenítése](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Megjeleníti egy adott feladathoz tartozó I/O-, idő-és átviteli hőmérsékleti térképet, amelyen megtalálhatja, hogy a feladatnak mennyi idő telik el, vagy hogy a feladatnak van-e egy I/O-határa, és így tovább.
    
      ![Példa a Azure Data Lake Analytics Job Graph heap térképre](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Előrehaladás: a feladatok végrehajtásának folyamata, lásd: információk a szakasz információi között.
    * Olvasott/írt adatmennyiség: az egyes fázisokban olvasott/írt teljes adatmennyiség.
    * Számítási idő: a (z) SUM (minden csúcspont-végrehajtási idő) hő-térképe úgy tekintheti meg, hogy mennyi ideig tart, ha a fázisban lévő összes munkát csak 1 csúcsponttal hajtja végre.
    * Átlagos végrehajtási idő/csomópont: a SUM (minden csúcspont végrehajtási idő)/(csúcspont-szám). Ez azt jelenti, hogy ha az összes csomópontot párhuzamosan hajtja végre, a teljes szakasz ebben az időkeretben lesz végrehajtva.
    * Bemeneti/kimeneti átviteli sebesség: az egyes fázisok bemeneti/kimeneti teljesítményének leképezése esetén megerősítheti, hogy a feladatok egy I/O-kötésű feladatot végeznek-e.
* Metaadat-műveletek
  
    Az U-SQL-parancsfájlban bizonyos metaadatokat is végrehajthat, például adatbázis létrehozását, tábla eldobását stb. Ezek a műveletek a metaadatokkal kapcsolatos műveletekben jelennek meg a fordítás után. Itt megtalálhatja az állításokat, entitásokat hozhat létre, és eldobni az entitásokat.
  
    ![Azure Data Lake Analytics feladatok nézet metaadat-műveletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Korábbi állapotok
  
    A korábbi állapotok is megjeleníthetők a feladatok összegzésében, de itt további részleteket is megtudhat. Megtalálhatja azokat a részletes információkat, mint a feladatok előkészítése, várólistára helyezése, elindítva, befejezve. Azt is megtalálhatja, hogy a feladatnak hányszor lett lefordítva (a CcsAttempts: 1), ha a feladatot ténylegesen a fürtnek küldi el (a részletek: a feladatok elküldése a fürtbe) stb.
  
    ![Azure Data Lake Analytics Job View állapot előzményei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnosztika
  
    Az eszköz automatikusan diagnosztizálja a feladatok végrehajtását. A rendszer riasztást küld, amikor valamilyen hiba vagy teljesítménnyel kapcsolatos probléma merül fel a feladatokban. Vegye figyelembe, hogy a profil letöltéséhez le kell töltenie a teljes körű információkat. 
  
    ![Azure Data Lake Analytics Job View diagnosztika](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Figyelmeztetések: itt látható a fordító figyelmeztetése. Ha a riasztás megjelenik, kattintson a "x probléma (ok)" hivatkozásra.
  * A csúcspont futtatása túl hosszú: Ha a csúcspontok nem állnak le időben (mondjuk 5 óra), a problémák itt jelennek meg.
  * Erőforrás-használat: Ha a szükségesnél több vagy nem elég párhuzamosságot foglalt le, a probléma itt található. Emellett az erőforrás-használat lehetőségre kattintva további részleteket tekinthet meg, és elvégezheti, hogy milyen helyzetekben érdemes jobb erőforrás-kiosztást találni (további részletekért lásd az útmutatót).
  * Memória-ellenőrzési: Ha bármely csúcspont 5 GB-nál több memóriát használ, itt jelennek meg a problémák. A feladat végrehajtása a rendszeren is megszakadhat, ha a rendszer több memóriát használ, mint a rendszerkorlátozás.

## <a name="job-detail"></a>Feladatok részletei
A feladatok részletei a feladatok részletes adatait jelenítik meg, beleértve a parancsfájlt, az erőforrásokat és a csúcspontok végrehajtási nézetét.

![Azure Data Lake Analytics feladatok részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    A feladatok U-SQL-szkriptjét a rendszer a lekérdezési tárolóban tárolja. Megtekintheti az eredeti U-SQL-parancsfájlt, és szükség esetén újra elküldheti.
* Erőforrások
  
    A lekérdezési tárolóban tárolt feladatok fordítási kimeneteit erőforrásokon keresztül érheti el. Megtalálhatja például a "algebra.xml" kifejezést, amely a feladatok Gráfjának, a regisztrált szerelvényeknek stb. megjelenítésére szolgál.
* Csúcspont-végrehajtási nézet
  
    A csúcspontok végrehajtásának részleteit jeleníti meg. A feladatok profilja archivál minden csúcspont-végrehajtási naplót, például a teljes adatolvasási/írási, futtatókörnyezeti, állami stb. Ebben a nézetben további részleteket tudhat meg arról, hogyan futottak a feladatok. További információ: [Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Következő lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A csúcspont-végrehajtási nézet használatához tekintse meg [a Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) című témakört.

