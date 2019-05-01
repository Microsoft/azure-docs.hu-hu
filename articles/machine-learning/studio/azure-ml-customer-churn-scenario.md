---
title: Vásárlók lemorzsolódásának elemzése
titleSuffix: Azure Machine Learning Studio
description: Esettanulmány fejlődő egy integrált modell és pontozása Azure Machine Learning Studio használatával, a vásárlók lemorzsolódásának elemzésére.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: e6a7eaa94e7196c830a66b2d77023bd562119c92
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699443"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio"></a>Az ügyfél az Azure Machine Learning Studio használatával változásainak elemzése
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja egy referenciaimplementációt, egy ügyfél lemorzsolódásának elemzésére szolgáló projekt, amely az Azure Machine Learning Studio használatával. Ebben a cikkben bemutatjuk, azok alkalmazásfüggőségeit az iparági vásárlók lemorzsolódási problémájának megoldására szolgáló kapcsolódó általános modellekről. Azt is mérheti a Machine Learning segítségével létrehozott modellek pontossága, és felmérheti a további fejlesztésére vonatkozó utasításokat.  

### <a name="acknowledgements"></a>Nyugták
Ez a kísérlet fejlesztette ki és tesztelte Serge Berger egyszerű Adattudós, a Microsoft és Roger Barga, a Microsoft Azure Machine Learning Studio korábban Termékmenedzsere. Az Azure dokumentációs csapata köszönetet mondani a segítségükért összpontosíthassák nyugtázza, és Köszönjük Ez a tanulmány megosztása.

> [!NOTE]
> Ehhez a kísérlethez használt adatokat nem nyilvánosan érhető. Hogyan hozhat létre egy gépi tanulási modellt lemorzsolódásának elemzése a példát talál: [Kiskereskedelmi vásárlói lemorzsolódás folyamatmodell-sablont](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) a [Azure AI-katalógusban](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>A probléma a vásárlók lemorzsolódásának
A fogyasztó piacon elérhető, és az összes vállalati ágazatban vállalkozások kell forgalom kezelésére. Néha túlzott és befolyásolja a házirendet érintő döntések. A hagyományos megoldás, ha nagy-upsell churners előre jelezni, és oldja meg saját igényeinek megfelelő marketingkampányokba, recepciószolgálata szolgáltatáson keresztül vagy a speciális felmentésekről alkalmazásával. Ezek a módszerek eltérőek lehetnek industry iparági. Még vésve egy adott fogyasztói fürtről a másikra egy iparági (például távközlési) belül.

A gyakori tényezővel az, hogy vállalkozások minimalizálása érdekében ezeket külön ügyfél felhasználómegtartása kell-e. Így természetes módszer lehet a pontszám minden ügyfél a lemorzsolódás valószínűségét, és oldja meg a felső N azokat. Előfordulhat, hogy a legjelentősebb ügyfeleket legjövedelmezőbb azokat. Például kifinomultabb forgatókönyvekben nyereség függvény alkalmazzák a speciális felmentéssel a deduplikációra kijelölt során. Ezeket a szempontokat azonban csak a teljes forgalom kapcsolatos stratégia részét képezik. Vállalkozások is figyelembe kell fiók kockázati (és a kapcsolódó kockázattűrése), a szint és a beavatkozás és egyértelmű ügyfélcsoportok költségét.  

## <a name="industry-outlook-and-approaches"></a>Iparági az outlook és módszerek
Kifinomult kezelését a lemorzsolódás érett iparág bejelentkezési. A klasszikus például, a távközlési iparágban, ahol előfizetők ismert, hogy gyakran váltson egy szolgáltatói egy másikba. Önkéntes kihagyásával az elsődleges szempont. Ezenkívül szolgáltatók rendelkezik, amikor jelentős Tudásbázis kapcsolatos *illesztőprogramok churn*, melyek a tényezőket, amelyek váltson ügyfelei meghajtó.

Például kézibeszélő vagy az eszköz el egy jól ismert illesztőprogram a mobiltelefon üzleti adatforgalom. Ennek eredményeképpen egy népszerű szabályzatot, hogy kézibeszélő árát subsidize új előfizetők számára, és a frissítéshez a meglévő ügyfelek számára a teljes díj díja szerint számítjuk fel. Ez a szabályzat hagyományosan ügyfelek egy szolgáltató a másikra segítségével tehetjük meg új kedvezményes díjat vezetett. Ennek érdekében, a rendszer felkéri szolgáltatók számára, hogy stratégiákkal pontosításához.

Magas változékonyságán kézibeszélő ajánlatok a fontos, hogy gyorsan érvényteleníti modellek adatforgalom aktuális kézibeszélő modellek alapuló tényező. Ezenkívül mobiltelefonokról nem csak a távközlési eszközöket, azok is el utasítások (vegye figyelembe az iPhone-t). A közösségi előrejelzőket rendszeres távközlési adatkészletek hatókörén kívül vannak.

Az eredmény a modellezési, hogy egy hang házirend nem lényegesen egyszerűen elkerülve a lemorzsolódás ismert okait. A folyamatos modellezési stratégiát, beleértve a klasszikus modellt számszerűsítik a kategorikus változók (például a döntési fák algoritmus), valójában **kötelező**.

Big data jellegű adatkészletek használatával az ügyfelek, a szervezetek big data-elemzés (különösen, a big data-alapú forgalom észlelési), a probléma hatékony megközelítés végzik. További információk a big Data típusú adatok megközelítés ETL szakasz ajánlások lemorzsolódási problémájának találja.  

## <a name="methodology-to-model-customer-churn"></a>A modell vásárlók lemorzsolódásának módszertan
Gyakori problémák megoldásához folyamat megoldani a vásárlók lemorzsolódásának megfelelőként jelenik a számok 1-3:  

1. Kockázati modell lehetővé teszi, hogy hogyan befolyásolja az műveletek valószínűségét, és a kockázati.
2. Egy beavatkozás modellje lehetővé teszi, fontolja meg, hogyan beavatkozás szintjét a valószínűsége annak, és a vásárlói mennyisége hatással lehetnek a csoportélettartam értékének (CLV).
3. Ez az elemzés adatmodelljeinek egy minőségi elemző, amely a célozza meg, hogy az optimális ajánlat ügyfélszegmensek proaktív marketingkampány eszkalálása.  

![Hogyan kockázati tűréshatár és döntési modellek eredményez a gyakorlatban hasznosítható elemzéseket bemutató ábra.](./media/azure-ml-customer-churn-scenario/churn-1.png)

A továbbítás akik megközelítés kezelni az adatforgalom a legjobb módszer, de együttműködik a összetettséget: fejlesszen többmodelles archetype és nyomkövetési függőségek modellek között kell, hogy. A modellek közötti interakció is technológiába ágyazott, az alábbi ábrán látható módon:  

![Vásárlói lemorzsolódás modell kapcsolati diagram](./media/azure-ml-customer-churn-scenario/churn-2.png)

*4. ábra: Egyesített többmodelles archetype*  

A modellek közötti interakció az kulcs, ha egy átfogó megközelítés továbbítására az ügyfélmegtartást. Minden modell feltétlenül replikákban idővel; az architektúra ezért implicit ciklusba (az adatok adatbányászati SZÍNELOSZLÁS-DM szabvány által beállított archetype hasonló [***3***]).  

A teljes ciklus a kockázat-döntési-marketing Szegmentálás/idősorfelbontási még egy általánosított szerkezete, és számos üzleti problémára vonatkozik. Lemorzsolódásának elemzése egyszerűen a csoport problémák erős képviselője azért azonban egy összetett üzleti probléma, amely nem engedélyezi a prediktív megoldás egyszerűsített képességekre vonatkozó. A vásárlói lemorzsolódás modern megközelítését közösségi aspektusait nem különösen kiemelt a megközelítéssel, de a közösségi szempontok a a modellezési archetype vannak ágyazva, azok bármely modellben lenne.  

Egy érdekes emellett a big data-elemzés. A mai távközlési és kereskedelmi vállalkozások az ügyfeleknek az alábbi adatok gyűjtése, és azt is könnyen bizonyulhat, hogy többmodelles kapcsolat szükség lesz egy gyakori trend, adott jelentkező trendeket, például az eszközök internetes hálózata és a széles körben használt az eszközök, amelyek lehetővé teszik az üzleti több rétegén intelligens megoldások bevezetését.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>A modellezés archetype megvalósítása a Machine Learning Studióban
Adja meg a probléma, mi az a legjobb módszer egy integrált modellezési és pontozás megközelítés implementálása? Ebben a szakaszban bemutatjuk, hogyan tudjuk valósítható meg ez az Azure Machine Learning Studio használatával.  

A több modell megközelítés kell, a forgalom egy globális archetype tervezésekor. Még a relevanciaprofil (prediktív) részét megközelítés többmodelles kell lennie.  

Az alábbi ábrán látható a prototípus hoztunk létre, a Machine Learning Studióban lemorzsolódási előrejelzésére négy pontozási algoritmusok alkalmazó. Többmodelles módszerével oka nem csak egy nagyobb pontosság, de is túlzott méretezés ellen védelmet biztosító és javítása előírásoknak megfelelő szolgáltatás kiválasztása ensemble osztályozó létrehozása céljából.  

![Egy összetett Studio-munkaterület számos összekapcsolt modulok ábrázoló képernyőfelvétel](./media/azure-ml-customer-churn-scenario/churn-3.png)

*5. ábra: A modellezés megközelítés adatforgalom prototípus*  

A következő szakaszok további információt a prototípus-pontozási modelljei, hogy mi a Machine Learning Studio használatával.  

### <a name="data-selection-and-preparation"></a>Kijelölt adatok és -előkészítés
A modellek létrehozásához használt adatokat, és pontszám ügyfelek CRM-függőleges megoldásról, lett lekérve az adatokkal a rejtjelezett felhasználói adatok védelme. Az adatok az Egyesült Államokban 8000 előfizetésekkel kapcsolatos információkat tartalmaz, és amely ötvözi az három forrásának: provisioning (előfizetés metaadatai) adatok, tevékenység adatokat (használat a rendszer) és ügyfél-támogatási adatok. Az adatok nem tartalmazza az ügyfelek üzleti kapcsolatos információ Ez például nem tartalmaz hűségprogramok használatán keresztül metaadatok vagy -kreditemet pontszámokat.  

Az egyszerűség kedvéért ETL és folyamatok adattisztító hatókörén kívül mivel feltételezzük, hogy adat-előkészítési már rendelkezik-e már végzett máshol.

Szolgáltatás kiválasztása a modellezési előzetes többszörösére pontozási előrejelzőket, szerepel a folyamat a véletlenszerű erdő modult használó készlete alapul. A megvalósítás a Machine Learning Studióban hogy kiszámítása a középérték középérték és tartományok reprezentatív funkciók. Ha például hozzáadtunk összesítések a mennyiségi adatok, például a felhasználói tevékenység minimális és maximális értékeket.

A legutóbbi hat hónapig historikus információkat is rögzített. Adatok egy évig elemeztük, és azt létre, hogy akkor is, ha voltak statisztikailag trendeket, lemorzsolódási hatása jelentősen csökken hat hónapig érvényesek.  

A legfontosabb, hogy, hogy a teljes folyamatot, beleértve az ETL-szolgáltatás kiválasztása lapon, és modellezés a Machine Learning Studióban, a Microsoft Azure-ban adatforrások felhasználásával lett megvalósítva.   

Az alábbi ábrák bemutatják a használt adatok.  

![Nyers értékekkel használt adatok mintáját ábrázoló képernyőfelvétel](./media/azure-ml-customer-churn-scenario/churn-4.png)

*6. ábra: Adatforrás (rejtjelezett) cikkből szerint*  

![Képernyőfelvétel: az adatforrásból kinyert statisztikai funkciók](./media/azure-ml-customer-churn-scenario/churn-5.png)

*7. ábra: Adatforrásból kinyert funkciók*
 

> Vegye figyelembe, hogy ezek az adatok személyes, és ezért a modell és az adatok nem oszthatók meg.
> Azonban egy hasonló modell, nyilvánosan elérhető adatok használatával, lásd: Ez a minta Kísérletezgessen egy kicsit a [Azure AI-katalógusban](https://gallery.azure.ai/): [Ügyfeleinek Lemorzsolódása](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Többet szeretne megtudni, hogyan implementálható a Cortana Intelligence Suite segítségével lemorzsolódási elemzési modell, azt is javasoljuk [ebben a videóban](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) vezető Programmenedzsere hétköznapon Hyong Tok szerint. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>A prototípust használt algoritmusok
A következő négy gépi tanulási algoritmusok segítségével hozhat létre, a prototípus (nincs testreszabása):  

1. Logisztikai regressziós (LR)
2. Gyorsított döntési fa (BT)
3. Átlagolt perceptron (AP)
4. Támogató vektorgép (SVM)  

A következő ábra szemlélteti a kísérlet tervezőfelületére, amely azt jelzi, hogy a feladatütemezés, amelyben a modellek létrehozott egy része:  

![A studio-kísérletet kis szakaszának képernyőképe vászonra](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*8. ábra: Modellek létrehozása a Machine Learning Studióban*  

### <a name="scoring-methods"></a>Pontozó módszerek
A négy modell címkézett betanítási adatkészletet használatával pontozását azt.  

Azt is be a pontozási adatkészletet, egy SAS vállalati Miner 12 asztali kiadásának használatával létrehozott összehasonlítható modell. A SAS-modell és az összes négy Machine Learning Studio-modell pontosságának mérni azt.  

## <a name="results"></a>Results (Eredmények)
Ebben a szakaszban azt jelenleg a kapcsolatos a pontozási adatkészleten alapuló modell pontossága megállapításokat.  

### <a name="accuracy-and-precision-of-scoring"></a>És pontozás pontossága
Az Azure Machine Learning Studióban végrehajtása általában SAS mögött van, körülbelül 10 – 15 % (terület alatt görbe vagy AUC) pontossággal.  

Azonban a legfontosabb mérőszám adatforgalom-e a téves besorolás arány:, mint az osztályozó által előrejelzett felső N churners, melyikük ténylegesen fejeződött **nem** a forgalom, és a kapott különleges kezelést? A következő diagram az összes modellt a téves besorolás aránya hasonlítja össze:  

![A görbe diagram 4 algoritmusok teljesítményének összehasonlítása alatti terület](./media/azure-ml-customer-churn-scenario/churn-7.png)

*9. ábra: Görbe Passau prototípus terület*

### <a name="using-auc-to-compare-results"></a>Az eredmények összehasonlítása AUC használatával
Terület alatt görbe (AUC) egy globális érték, képviselő metrika *separability* a pontszámok pozitív és negatív számítógépcsoportokon a felosztások között. A hagyományos fogadó operátor jellemző (ROC) gráfhoz hasonló, de egy fontos különbség az, hogy a AUC metrika nem kell választani egy küszöbértéket. Ehelyett azt az eredményről keresztül **összes** választási lehetőségek. Ezzel szemben a hagyományos ROC-diagramon látható a riasztási aránnyal a függőleges tengely és a vízszintes tengelyről téves riasztási aránnyal, és a besorolási küszöbérték változó.   

AUC használata érték, érdemes különböző algoritmusok (vagy a különböző rendszerek esetén) lehetővé teszi modellek révén AUC értékekre kell összehasonlítani. Ez az iparágban például meteorológia és biosciences egy népszerű megközelítés. Ebből kifolyólag AUC jelöli egy népszerű eszköz osztályozó teljesítményének értékeléséhez.  

### <a name="comparing-misclassification-rates"></a>Téves besorolás díjak összehasonlítása
Körülbelül 8000 előfizetések CRM-adatok használatával az adott adatkészlet téves besorolás fel azt képest.  

* A SAS téves besorolás sebesség 10 – 15 % volt.
* A Machine Learning Studio téves besorolás az első 200 – 300 churners 15 – 20 %-os volt.  

A távközlési iparágban fontos csak a vásárlói lemorzsolódás recepciószolgálata szolgáltatásként vagy egyéb speciális kezelés felajánlásával őket a legmagasabb kockázattal rendelkező ügyfelek cím. Ebben a tekintetben a Machine Learning Studio megvalósítás hatékonysága eléri a SAS modell eredményei éri el.  

Hasonlóképpen pontossága fontosabb, mint a pontossága, mivel ez többnyire megfelelő Írisz lehetséges churners iránt.  

Az alábbi ábrán a Wikipedia a kapcsolatot a színes, könnyen érthető kép ábrázolja:  

![Két célok. Egy cél mutat be találati jelek lazán vannak csoportosítva, de a megjelölt bikák szemmel közelében "kevés a pontosság: jó egzaktság, gyenge pontosság. Egy másik cél szorosan vannak csoportosítva, de messze a bikák szem jelölt "kevés a pontosság: gyenge egzaktság, jó pontosság"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*10. ábra: Pontosság és a pontosság magával*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Gyorsított döntési fa modell pontosságát és a pontosság eredményei
Az alábbi ábra a pontozás a Machine Learning prototípus használata a gyorsított döntési fa modell, amely a négy modellek között a legpontosabb történetesen nyers eredményét jeleníti meg:  

![Tábla részlet megjelenítése a pontosságot, a pontosság, a már ismert, F-pontszám, AUC, Log átlagos veszteség és képzési Log adatveszteség négy algoritmusok](./media/azure-ml-customer-churn-scenario/churn-9.png)

*11. ábra: Gyorsított döntési fa modell jellemzői*

## <a name="performance-comparison"></a>Teljesítmény összehasonlítása
Hogy képest, a sebesség, amellyel adatokat pontozását volt az a Machine Learning Studio-modellek és a egy SAS vállalati Miner 12.1 asztali kiadásának használatával létrehozott összehasonlítható modell használatával.  

A következő táblázat összefoglalja az algoritmusok teljesítményének:  

*1. táblázat. Általános teljesítménye (pontosság) algoritmusok*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Átlagos modell |A legjobb modellt |Az alulteljesítő |Átlagos modell |

A modellek a végrehajtási, de a pontosság sebességének 15-25 %-kal outperformed SAS volt nagymértékben par a Machine Learning Studióban futtatott.  

## <a name="discussion-and-recommendations"></a>Hozzászólás és javaslatok
A távközlési iparágban több eljárások kiderült rendelkezik, lemorzsolódásának elemzése a többek között:  

* Származtasson négy alapvető kategória metrikái:
  * **Entitás (például egy előfizetés)**. Alapvető információkat szeretne az előfizetés és/vagy a vásárlói lemorzsolódás tárgyát képező kiépítése.
  * **Tevékenység** – Szerezze be a kapcsolódó entitás, például a bejelentkezések száma az összes lehetséges használati információkat.
  * **Ügyfél-támogatási**. Gyűjtsön információkat az ügyfél-támogatási naplók jelzi, hogy az előfizetés volt-e a problémákat és az ügyfél-támogatási interakciók.
  * **Versenyképes és üzleti adatokat**. Az ügyfél lehetséges minden olyan információt (például lehet érhető el vagy visszakövetését, hogy).
* Meghajtó szolgáltatás kiválasztása fontosak használja. Ez azt jelenti, hogy a gyorsított döntési fa modell mindig a ígéret megközelítést.  

Ezekben a kategóriákban használatát hoz létre a finomabb, amely egy egyszerű *determinisztikus* megközelítéseket, formázott ésszerű tényező befolyásolja, kategóriánként indexek ügyfelek lemorzsolódásának kitett azonosításához elegendőnek kell lennie. Sajnos bár ebben a formátumban egyértelmű úgy tűnik, egy hamis ismertetése. Az oka, hogy adatváltozás historikus érvénybe, és a vásárlói lemorzsolódás hozzájáruló tényezők rendszerint átmeneti állapotok. Mit kell figyelembe venni, így még ma ügyfél érdeklődők holnap lehet különböző, és természetesen lesz különböző hat hónap múlva. Ezért egy *valószínűségi* modellje áttelepítésére.  

Ez fontos megfigyelési gyakran üzleti, amely általában egy üzleti intelligencia megközelítéssel szeretnék analytics van kihagyott, leginkább, mert egy egyszerűbb értékesítésére, és könnyen érthető megjegyzésblokkok írására automation elismeri.  

Azonban beváltja az önkiszolgáló elemzés, Machine Learning Studio használatával is, ha az adatokat, részleg vagy osztály által osztályozott négy karakterkategóriából gépi tanulási lemorzsolódási kapcsolatos értékes forrást válik.  

Egy másik izgalmas funkció hamarosan elérhető az Azure Machine Learning Studióban, lehetővé teszi, hogy egy egyéni modult, előre meghatározott, amelyek már elérhetők a tárházba. Ez a funkció alapvetően hoz lehetőséget válassza ki a kódtárakat, és vertikális piacok sablonok létrehozásához. Egy fontos különbséget jelent az Azure Machine Learning Studio piacon.  

Reméljük, hogy továbbra is a jövőben ez a témakör különösen kapcsolatos big data-elemzés.
  

## <a name="conclusion"></a>Összegzés
Ez a tanulmány azt ismerteti, hogy észszerű megközelítése problémájának közös vásárlók lemorzsolódásának általános keretrendszer használatával. Mi a modellek pontozása prototípusát minősülnek, és implementálja az Azure Machine Learning Studio használatával. Végül azt adatokon, a pontosság és a teljesítmény, a prototípus-megoldás összehasonlítható algoritmusok a biztonsági Társítások kapcsolatban.  

 

## <a name="references"></a>Referencia
[1] prediktív elemzési: Túl az előrejelzéseket, Nyugat McKnight, információk kezelése, a p.18 – 20 2011. júliusi/augusztus.  

[2] Wikipedia-cikk: [Pontosság és a pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Részletes adatok adatbányászati útmutatója](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [big Data típusú adatok Marketing: Az ügyfelek hatékonyabb megszólítása és a meghajtó érték](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [telekommunikációs churn folyamatmodell-sablont](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) a [Azure AI-katalógusban](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Függelék
![Pillanatkép a lemorzsolódás prototípuson-bemutató](./media/azure-ml-customer-churn-scenario/churn-10.png)

*12. ábra: Pillanatkép a lemorzsolódás prototípuson-bemutató*
