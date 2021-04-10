---
title: Azure AI-útmutató a prediktív karbantartási megoldásokhoz – csoportos adatelemzési folyamat
description: Az adatelemzés átfogó leírása, amely a prediktív karbantartási megoldásokat több vertikális iparágban is kikapcsolja.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 1e939b86eeadfee276378488cfcb40c07f28684d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98880658"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-útmutató a prediktív karbantartási megoldásokhoz

## <a name="summary"></a>Összefoglalás

A prediktív karbantartás (**PDM**) a prediktív elemzések népszerű alkalmazása, amely segítheti a különböző iparágakban lévő vállalkozásoknak a működési költségek magas szintű kihasználtságának és megtakarításának megvalósítását. Ez az útmutató a PdM-megoldások [Microsoft Azure AI platform](https://azure.microsoft.com/overview/ai-platform) technológiával való sikeres fejlesztéséhez és üzembe helyezéséhez szükséges üzleti és elemzési irányelvek és ajánlott eljárások együttes használatát ismerteti.

A kezdők számára ez az útmutató az iparági specifikus üzleti forgatókönyveket, valamint a PdM-forgatókönyvek kiváltásának folyamatát mutatja be. A PdM-megoldások létrehozásához szükséges adatkövetelményeket és modellezési technikákat is megadták. Az útmutató fő tartalma az adatelemzési folyamaton alapul – beleértve az adatelőkészítési, a funkciók mérnöki, a modell-létrehozási és a modell-operacionalizálási lépéseit. A legfontosabb fogalmak kiegészítése érdekében ez az útmutató olyan megoldási sablonokat sorol fel, amelyek segítenek felgyorsítani a PdM-alkalmazások fejlesztését. Az útmutató Emellett hasznos képzési erőforrásokat is tartalmaz, amelyekkel többet tudhat meg az adatelemzés mögötti AI-ról. 

### <a name="data-science-guide-overview-and-target-audience"></a>Az adatelemzési útmutató áttekintése és célközönsége
Az útmutató első fele a tipikus üzleti problémákat ismerteti, a PdM megvalósításának előnyeit a problémák megoldásához, és felsorolja a gyakori használati eseteket. Az üzleti döntéshozók (BDMs-EK) ezt a tartalmat fogják kihasználni. A második fél ismerteti az PdM mögötti adatelemzést, és felsorolja az útmutatóban ismertetett alapelvek alapján létrehozott PdM-megoldásokat. Emellett tanulási útvonalakat és mutatókat is biztosít az anyagok betanításához. A technikai döntéshozók (TDMs) hasznosnak találja ezt a tartalmat.

| Kezdés... | Ha... |
|:---------------|:---------------|
| [Üzleti eset a prediktív karbantartáshoz](#business-case-for-predictive-maintenance) |egy üzleti döntéshozó (BDM), amely csökkenti az állásidőt és a működési költségeket, és javítja a berendezések kihasználtságát |
| [Adatelemzés a prediktív karbantartáshoz](#data-science-for-predictive-maintenance) |technikai döntéshozó (TDM) a PdM technológiák kiértékelése a prediktív karbantartás egyedi adatfeldolgozási és AI-követelményeinek megismeréséhez |
| [Megoldási sablonok a prediktív karbantartáshoz](#solution-templates-for-predictive-maintenance)|egy szoftverfejlesztő vagy AI-fejlesztő, aki gyorsan fel szeretne készíteni egy bemutatót vagy egy próba-koncepciót |
| [Erőforrások betanítása a prediktív karbantartáshoz](#training-resources-for-predictive-maintenance) | a fentiek bármelyike vagy mindegyike, és szeretné megismerni az adatelemzés, az eszközök és a technikák mögötti alapvető fogalmakat.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó előfeltételek
A BDM tartalma nem várja az olvasót, hogy bármilyen korábbi adatelemzési ismerettel rendelkezzen. A TDM-tartalomhoz a statisztikák és adatelemzések alapszintű ismerete hasznos. Az Azure-beli adat-és AI-szolgáltatások, a Python, az R, az XML és a JSON ismerete ajánlott. Az AI-technikák Python-és R-csomagokban vannak implementálva. A megoldási sablonok az Azure-szolgáltatások, a fejlesztői eszközök és az SDK-k használatával valósíthatók meg.

## <a name="business-case-for-predictive-maintenance"></a>Üzleti eset a prediktív karbantartáshoz

A vállalatok kritikus fontosságú berendezéseket igényelnek a maximális hatékonyság és kihasználtság érdekében, hogy felismerjék a tőkebefektetések megtérülését. Ezek az eszközök a repülőgép-hajtóművektől, a turbináktól, a felvonóktól és az ipari hűtőberendezésektól függően akár több milliót is tartalmazhatnak, például a fénymásolókat, a kávégépeket vagy a vízhűtőket.
- Alapértelmezés szerint a legtöbb vállalat a _javító karbantartásra_ támaszkodik, ahol a részek a és a meghibásodása esetén is lecserélhetők. A javító karbantartás lehetővé teszi, hogy a részek teljes mértékben használatban legyenek (ezért nem pazarolja az összetevők élettartamát), de a leállási, a munkaerő-és a nem ütemezett karbantartási követelmények (munkaidőn kívüli vagy nem megfelelő helyszínek) költségeit is felhasználhatja.
- A következő szinten a vállalatok  _megelőző karbantartást_ végeznek, ahol egy rész hasznos élettartamát határozzák meg, és a hiba előtt megtarthatják vagy lecserélhetik azokat. A megelőző karbantartás elkerüli a nem ütemezett és katasztrofális hibákat. Azonban az ütemezett leállás magas költségei, az összetevő kihasználtsága a hasznos élettartama alatt is fennáll, és továbbra is fennáll a munka.
- A _prediktív karbantartás_ célja, hogy optimalizálja az egyensúlyt a javítási és a megelőző karbantartás között az összetevők _időben_ történő cseréje révén. Ez a módszer csak akkor cseréli le ezeket az összetevőket, amikor hibákhoz közelednek. Az összetevők élettartamának (a megelőző karbantartáshoz képest) kibővítésével, valamint a nem ütemezett karbantartási és munkaerő-költségek csökkentésével (a javító karbantartással szemben) a vállalatok költségmegtakarítást és versenyelőnyt biztosítanak.

## <a name="business-problems-in-pdm"></a>Üzleti problémák a PdM-ben
A vállalatok váratlan meghibásodások miatt magas működési kockázatokkal szembesülnek, és korlátozott mértékben betekintést nyerhetnek az összetett rendszerek problémáinak kiváltó okának. A legfontosabb üzleti kérdések a következők:

- A berendezések vagy a rendszerteljesítmény vagy a funkciók rendellenességének észlelése.
- Megjósolhatja, hogy az eszköz a közeljövőben meghiúsulhat-e.
- Egy adott eszköz hátralévő hasznos élettartamának becslése.
- Azonosíthatja egy adott eszköz meghibásodásának fő okait.
- Azonosítsa, hogy milyen karbantartási műveleteket kell elvégezni, ha egy adott eszközön.

A PdM jellemző Goal-utasítások a következők:

- Csökkentse a kritikus fontosságú berendezések működési kockázatát.
- Az eszközök megtérülési arányának növeléséhez a hibák előrejelzésével.
- Az igény szerinti karbantartási műveletek engedélyezésével vezérelheti a karbantartási költségeket.
- Csökkentheti az ügyfelek adatvesztését, javíthatja a márka képét és elveszítette az értékesítést.
- Az átrendezési pont előrejelzésével csökkentheti a leltározási költségeket.
- Különböző karbantartási problémákhoz kapcsolódó mintázatok felderítése.
- Adja meg a KPI-ket (fő teljesítménymutatók), például az eszközre vonatkozó feltételek állapotát.
- Az eszközök hátralévő élettartamának becslése.
- Javasolja az időben végzett karbantartási tevékenységeket.
- Az időkorlátot az alkatrészek cseréjére vonatkozó rendelési dátumok becslésével engedélyezheti.

Ezek a cél-utasítások a következők kiindulási pontjai:

- _adatszakértők_ az adott prediktív problémák elemzéséhez és megoldásához.
- a _Felhőbeli tervezők és fejlesztők_ közösen helyezik üzembe a teljes körű megoldást.

## <a name="qualifying-problems-for-predictive-maintenance"></a>A prediktív karbantartásra vonatkozó problémák
Fontos kiemelni, hogy a PdM nem minden használati esetet vagy üzleti problémát képes hatékonyan megoldani. Három fontos, a probléma kiválasztásakor megfontolandó szempontot kell figyelembe venni:

- A problémának prediktív jellegűnek kell lennie; Ez azt jelzi, hogy meg kell adni egy célt vagy egy végeredményt. A probléma egyértelmű elérési úttal is rendelkeznie kell, hogy megakadályozza a hibák észlelését.
- A problémának szerepelnie kell a _jó és a rossz eredményeket_ tartalmazó berendezés működési előzményeivel. A hibás eredmények enyhítésére tett műveletek készletét ezen rekordok részeként is elérhetővé kell tenni. A hibajelentések, a teljesítmény-romlás, a javítási és a csere-naplók karbantartási naplói szintén fontosak. Emellett a javítások is javíthatók, és a helyettesítő rekordok is hasznosak.
- A rögzített előzményeket a használati eset támogatásához _elégséges_ minőségű _megfelelő_ adatokat kell figyelembe venni. Az adatok relevanciájának és megfelelőségének részletes ismertetését lásd: [az adatkövetelmények a prediktív karbantartáshoz](#data-requirements-for-predictive-maintenance).
- Végezetül a vállalatnak olyan tartományi szakértőkkel kell rendelkeznie, akik egyértelmű ismeretekkel rendelkeznek a problémáról. Ismerniük kell a belső folyamatokat és gyakorlatokat, amelyek segítségével az elemző megismerheti és értelmezheti az adatelemzést. A meglévő üzleti folyamatoknak is el kell tudniuk végezni a szükséges módosításokat, hogy szükség esetén a problémákhoz megfelelő adatokat gyűjtsenek.

## <a name="sample-pdm-use-cases"></a>Példa PdM-használati esetekre
Ez a szakasz a különböző iparágak, például a Repülőgépipar, a segédprogramok és a szállítás PdM-használati eseteinek egy gyűjteményére koncentrál. Az egyes szakaszok üzleti problémával kezdődnek, és tárgyalják a PdM előnyeit, az üzleti problémával kapcsolatos releváns adatokat, és végül egy PdM-megoldás előnyeit.

| Üzleti probléma | A PdM előnyei |
|:-----------------|-------------------|
|**Légi közlekedés**      |                   |
|A _repülési késés és a lemondás_ mechanikai problémák miatt. Az időben nem javítható hibák miatt a járatok megszakadnak, és megzavarhatja az ütemezést és a műveleteket. |A PdM-megoldások megjósolják, hogy a repülőgépek milyen valószínűséggel vannak késleltetve vagy megszakítva a mechanikai hibák miatt.|
|_Repülőgép-motor részeinek meghibásodása_: a repülőgép-hajtóművek részei a légitársaságok legáltalánosabb karbantartási feladatai közé tartoznak. A karbantartási megoldások gondos felügyeletet igényelnek az összetevők rendelkezésre állása, továbbítása és tervezése terén|Az összetevők megbízhatóságával kapcsolatos intelligenciával jelentős mértékben csökkenthető a beruházási költségek.|
|**Pénzügy** |                         |
|Az _ATM-hiba_ a bankszektor gyakori problémája. A probléma az, ha azt a valószínűséget jelenti, hogy egy ATM-alapú készpénz-visszavonási tranzakció megszakad, mert a készpénz-adagolóban Papírelakadás vagy részleges hiba történt. A tranzakciós hibák előrejelzése alapján az ATM-ket proaktív módon lehet kiszolgálni, hogy elkerülje a hibák előfordulását.| Ahelyett, hogy a gép a tranzakció felé irányuló feladatátvételt hajtja végre, a kívánt alternatíva a számítógép beprogramozása a szolgáltatás megtagadására az előrejelzés alapján.|
|**Energiafogyasztás** |                          |
|_Szélturbinák meghibásodásai_: a szélerőművek a legfontosabb energiaforrások a környezetvédelmi szempontból felelős országokban/régiókban, és nagy beruházási költségekkel járnak. A szélerőművek egyik fő összetevője a Generator motor, amelynek meghibásodása miatt a turbina hatástalan. Emellett nagyon költséges a javítás.|A KPI-k (például a TTF) előrejelzése segíthet az energetikai vállalatoknak megakadályozni a turbina meghibásodását, és minimális állásidőt biztosítani. A meghibásodási valószínűségek tájékoztatják a technikusokat a közeljövőben várhatóan sikertelen turbinák monitorozásáról és az időalapú karbantartási rendszerek bevezetéséről. A prediktív modellek olyan különböző tényezőket biztosítanak, amelyek hozzájárulnak a hibákhoz, ami segít a technikusoknak jobban megismerni a problémák kiváltó okait.|
|_Áramkör-megszakítói hibák_: a villamos energia és a vállalkozások közötti elosztáshoz a Power Lines szolgáltatásnak mindig működőképesnek kell lennie az energiaellátás biztosításához. Az áramkör-megszakítók segítenek korlátozni vagy elkerülni a energiaellátási vonalak károsodását a túlterhelés vagy a kedvezőtlen időjárási viszonyok során. Az üzleti probléma az áramkör-megszakító meghibásodásának előrejelzése.| A PdM-megoldások segítenek a javítási költségek csökkentésében és a berendezések, például az áramkör-megszakítók élettartamának növelésében. A váratlan hibák és a szolgáltatás-megszakítások csökkentésével javítják az energiagazdálkodás minőségét.|
|**Szállítás és logisztika** |    |
|_Lift Door-hibák_: a nagyméretű lifttel rendelkező vállalatok teljes stack szolgáltatást biztosítanak a világ számos millió működési felvonója számára. A lift biztonsága, megbízhatósága és a rendelkezésre állás a legfontosabb szempont az ügyfelek számára. Ezek a vállalatok az érzékelők segítségével követik nyomon ezeket és más attribútumokat, hogy azok megfelelő és megelőző karbantartást tudjanak nyújtani. Egy liftben a legjelentősebb ügyfél-probléma a lift ajtajának meghibásodása. Az üzleti probléma ebben az esetben egy olyan prediktív alkalmazás, amely a meghibásodások lehetséges okait Jósolja meg.| A felvonók olyan tőkebefektetések, amelyek potenciálisan 20-30 éves élettartamot biztosítanak. Így minden lehetséges értékesítés nagyon versenyképes lehet; Ezért a szolgáltatás és a támogatás iránti elvárások magasak. A prediktív karbantartás lehetővé teszi a vállalatok számára, hogy a termék-és szolgáltatás-kínálatuk versenytársain keresztül kihasználhassák a versenytársakat.|
|_Kerekek meghibásodása_: az összes vonat kisiklásának felét és a globális vasúti iparágra vonatkozó díjakért felelős kormány meghibásodása. A kerekek meghibásodása azt is okozhatja, hogy a sínek romlanak, időnként a vasút idő előtti megszakítását okozza. A vasúti szünetek katasztrofális események, például kisiklások esetén vezetnek. Az ilyen példányok elkerülése érdekében a vasutak figyelik a kerekek teljesítményét, és lecserélik azokat megelőző módon. Az üzleti probléma itt a kerék meghibásodásának előrejelzése.| A kerekek prediktív karbantartása segítséget nyújt a kerekek igény szerinti cseréjéhez. |
|_Földalatti betanítási ajtó hibái_: a földalatti műveletekben a késések jelentős oka az autók betanítása. Az üzleti probléma a betanítási ajtó meghibásodásának előrejelzése.|Az ajtó meghibásodásának korai ismerete, illetve az ajtó meghibásodása előtt eltelt napok száma segít az üzletnek a vonat-ajtó karbantartási ütemezésének optimalizálásában.|

A következő szakasz részletesen ismerteti, hogyan lehet megvalósítani a fentiekben ismertetett PdM-előnyöket.

## <a name="data-science-for-predictive-maintenance"></a>Adatelemzés a prediktív karbantartáshoz

Ez a szakasz az adatelemzési elvekkel és a PdM kapcsolatos gyakorlat általános irányelveit tartalmazza. Célja, hogy segítséget nyújtson egy TDM, megoldás-építész vagy egy fejlesztő számára, hogy megismerje az előfeltételeket és a folyamatokat a teljes körű AI-alkalmazások PdM való létrehozásához. Ez a szakasz a [prediktív karbantartáshoz használható megoldási sablonokban](#solution-templates-for-predictive-maintenance)található demók és próba-koncepciós sablonok áttekintésével együtt olvasható. A PdM-megoldás Azure-beli megvalósításához ezeket az elveket és ajánlott eljárásokat használhatja.

> [!NOTE]
> Ez az útmutató nem alkalmas az olvasói adatelemzés tanítására. Számos hasznos forrást talál a [prediktív karbantartáshoz szükséges erőforrások betanítása](#training-resources-for-predictive-maintenance)című szakaszban. Az útmutatóban felsorolt [megoldási sablonok](#solution-templates-for-predictive-maintenance) néhány ilyen AI-technikát mutatnak be bizonyos PDM problémák esetén.

## <a name="data-requirements-for-predictive-maintenance"></a>Az adatkövetelmények a prediktív karbantartáshoz

A tanulás sikeressége függ attól, hogy milyen minőségben tanulják meg, és (b) a tanuló képességei. A prediktív modellek megtudhatják a múltbeli adatok mintáit, és bizonyos valószínűséggel megjósolják a jövőbeli eredményeket a megfigyelt minták alapján. A modell prediktív pontossága a képzés és a tesztelési adatok relevanciájának, megfelelőségének és minőségének a függvénye. Az ebben a modellben szereplő "felhorzsolt" új adattípusnak ugyanazokkal a szolgáltatásokkal és sémával kell rendelkeznie, mint a betanítási/tesztelési adatként. Az új adatok funkció-jellemzőinek (típus, sűrűség, eloszlás stb.) meg kell egyezniük a betanítási és tesztelési adatkészletekkel. Ennek a szakasznak a középpontjában az ilyen adatkövetelmények szerepelnek.

### <a name="relevant-data"></a>Kapcsolódó adatokat

Először is fontos, hogy az adatokat meg kell adni _a problémához_. Vegye figyelembe, hogy a fentiekben tárgyalt _kerék-meghibásodási_ eset – a betanítási adatoknak a kerék műveletekkel kapcsolatos funkciókat kell tartalmazniuk. Ha a probléma a  _vontatási rendszer_ meghibásodásának előrejelzése volt, a betanítási adatmennyiségnek a vontatási rendszer minden különböző összetevőjét ki kell terjednie. Az első eset egy adott összetevőt céloz meg, míg a második eset egy nagyobb alrendszer meghibásodását célozza meg. Az általános javaslat az előrejelzési rendszerek megtervezése bizonyos összetevőkkel, a nagyobb alrendszerek helyett, mivel az utóbbi több szétszórt adattal fog rendelkezni. A tartományi szakértő (lásd: a [prediktív karbantartással kapcsolatos problémák](#qualifying-problems-for-predictive-maintenance)) segítséget nyújt az elemzéshez tartozó legfontosabb alkészletek kiválasztásában. A megfelelő adatforrásokat részletesebben ismertetjük a [prediktív karbantartás adatelőkészítési feladataival](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Elegendő adatmennyiség
A hiba-előzményekre vonatkozó adatmennyiséggel kapcsolatban általában két kérdés fordul elő: (1) "hány sikertelen esemény szükséges a modell betanításához?" (2) "hány rekord tekintendő" elegendőnek? " Nincsenek végleges válaszok, de csak a hüvelykujj szabályok. A (1), a hiba eseményeinek száma és a modell jobb számának növelése érdekében. A (2) és a sikertelen események pontos száma az adatoktól és a megoldandó probléma kontextustól függ. De a flip oldalon, ha egy gép túl gyakran meghibásodik, akkor a vállalat lecseréli, ami csökkenti a meghibásodási példányokat. Itt is fontos a tartományi szakértő útmutatása. Vannak azonban olyan metódusok, amelyek megbirkóznak a _ritka események_ problémájára. Ezeket a rendszer a [kiegyensúlyozatlan adatkezelési](#handling-imbalanced-data)szakaszban tárgyalja.

### <a name="quality-data"></a>Minőségi adatgyűjtés
Az adatminőség kritikus fontosságú – minden előrejelző attribútum értékének _pontosnak_ kell lennie a cél változó értékével együtt. Az adatminőség a statisztikában és az adatkezelésben jól tanulható terület, ezért az útmutató hatókörén kívül esik.

> [!NOTE]
> Számos erőforrás és nagyvállalati termék biztosítja a minőségi adatgyűjtést. A következő hivatkozásokból álló mintát láthat:
> - Sas József, T, Johnson, T. felderítő adatbányászat és Adattisztítás, Wiley, 2003.
> - [Tájékozódási célú adatelemzés, wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, mennyiségi Adattisztítás nagyméretű adatbázisokhoz](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der Loo, M, bevezetés az adattisztításba az R használatával](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Az adatelőkészítés a prediktív karbantartáshoz

### <a name="data-sources"></a>Adatforrások

A prediktív karbantartáshoz szükséges adatforrások közé tartoznak a következők:
- Sikertelenség előzményei
- Karbantartás/javítási előzmények
- Gépi üzemeltetési feltételek
- Berendezések metaadatai

#### <a name="failure-history"></a>Sikertelenség előzményei
A sikertelen események PdM-alkalmazásokban ritkák. Az előrejelzési modellek létrehozásakor azonban az algoritmusnak meg kell ismernie az összetevők normál működési mintáját, valamint a hibák mintázatát. Így a betanítási adatoknak elegendő számú példát kell tartalmazniuk mindkét kategóriából. A karbantartási rekordok és a részek helyettesítési előzményei jó források a sikertelen események kereséséhez. Bizonyos tartományi ismeretek segítségével a betanítási adatban mutatkozó rendellenességek is meghatározhatók hibákként.

#### <a name="maintenancerepair-history"></a>Karbantartás/javítási előzmények
Egy adott eszköz karbantartási előzményei a lecserélt összetevőkre, a végrehajtott javítási tevékenységekre stb. vonatkozó adatokat tartalmaznak. Ezek az események a romlási mintákat rögzítik. A betanítási adatokban lévő fontos információk hiánya félrevezető modell eredményeihez vezethet. A hibák előzményei a karbantartási előzményeken belül is megtalálhatók, mint a speciális hibakódok, vagy a részek rendelési dátumai. A meghibásodási mintákat befolyásoló további adatforrásokat tartományi szakértőknek kell megvizsgálniuk és biztosítaniuk.

#### <a name="machine-operating-conditions"></a>Gépi üzemeltetési feltételek
A működésben lévő berendezések érzékelőn alapuló (vagy más) adatfolyam-adatátviteli adatforrása fontos adatforrás. A PdM egyik legfontosabb feltételezése, hogy a gép állapotának romlása az idő múlásával történik a rutin művelet során. Az adatmennyiségnek szerepelnie kell idő-változó funkciókkal, amelyek rögzítik ezt az öregedési mintát, valamint a romlást okozó rendellenességeket. Az adat időbeli aspektusa szükséges ahhoz, hogy az algoritmus elsajátítsa a hiba és a nem hibák mintázatát az idő múlásával. Ezen adatpontok alapján az algoritmus megtudhatja, hogy egy adott gép hány egységnyi ideig dolgozhat tovább a meghibásodása előtt.

#### <a name="static-feature-data"></a>Statikus funkciók
A statikus funkciók a berendezésekkel kapcsolatos metaadatok. Ilyenek például a berendezések gyártmánya, a modell, a gyártás dátuma, a szolgáltatás kezdő dátuma, a rendszer helye és egyéb technikai specifikáció.

A [minta PDM használati eseteire](#sample-pdm-use-cases) vonatkozó releváns adatokat az alábbi táblázat ismerteti:

| Használati eset | Példák a kapcsolódó adatokat |
|:---------|---------------------------|
|_Repülési késés és lemondás_ | Repülési útvonal információi a repülési lábak és a lapok naplói formájában. A repülési láb adatai olyan útválasztási részleteket foglalnak magukban, mint a távozás/érkezés dátuma, az idő, a repülőtér, a layovers stb. Az oldal naplójában számos hiba és karbantartási kód található, amelyeket a rendszer a helyszíni karbantartó személyzete rögzített.|
|_Repülőgép-motor részeinek meghibásodása_ | A repülőgép érzékelőkből gyűjtött adatok, amelyek a különböző részek feltételére vonatkozó információkat biztosítanak. A karbantartási rekordok segítenek azonosítani, hogy mikor történtek az összetevők hibái, és mikor lettek lecserélve.|
|_ATM-hiba_ | Az érzékelők beolvasása minden egyes tranzakcióhoz (készpénz/ellenőrzési letétbe helyezés) és a készpénz elvégzéséhez szükséges. A jegyzetek közötti Térközi méréssel kapcsolatos információk, a vastagság, a Megjegyzés érkezési távolsága, az attribútumok megtekintése stb. Hibakódokat biztosító karbantartási rekordok, javítási információk, a készpénz-adagoló utolsó újratöltésének időpontja.|
|_Szélturbina meghibásodása_ | Az érzékelők monitorozási feltételeit, például a hőmérsékletet, a szél irányát, az áramellátást, a generátor sebességét stb. Az adatok több, különböző régiókban található szélerőműből származnak. A turbina jellemzően több érzékelővel rendelkező, rögzített időközönkénti adatolvasási továbbítási mérést végez.|
|_Áramkör-megszakító hibái_ | A javítási, megelőző és szisztematikus műveleteket tartalmazó karbantartási naplók. Olyan operatív adatműveletek, amelyek automatikus és manuális parancsokat tartalmaznak az áramkör-megszakítók számára, például a megnyitási és a bezárási műveletek elvégzéséhez. Az eszköz metaadatai, például a gyártás dátuma, a hely, a modell stb. Áramkör-megszakító specifikációi, például feszültségi szintek, térinformatikai, környezeti feltételek.|
|_Elevátori ajtó meghibásodása_| A lift metaadatait, például a lift típusát, a gyártás dátumát, a karbantartási gyakoriságot, az építési típust stb. Operatív információk, például az ajtós ciklusok száma, az átlagos ajtó zárási ideje. Hibák előzményei az okokkal.|
|_Kerekek meghibásodása_ | A görgős gyorsítást, a fékezési példányokat, a távolságot, a sebességet stb. mérő adat. Statikus információ a kerekek, például a gyártó, a gyári dátum alapján. A megrendelési dátumokat és mennyiségeket nyomon követő, részben megrendelési adatbázisból következtetett adatok.|
|_Földalatti vonatok meghibásodása_ | Az ajtót nyitó és záró időpontok, egyéb működési adatmennyiségek, például a betanítási ajtók aktuális feltétele. A statikus adat magában foglalja az eszköz azonosítóját, időpontját és a feltétel értékének oszlopait.|

### <a name="data-types"></a>Adattípusok
A fenti adatforrások miatt a PdM tartományban megfigyelt két fő adattípus a következők:

- _Időbeli adatmennyiség_: működési telemetria, gépi feltételek, munkasorrend-típusok, prioritási kódok, amelyeknek időbélyegei lesznek a rögzítés időpontjában. A hiba, a karbantartás/javítás és a használati előzmények az egyes eseményekhez tartozó időbélyegzőket is tartalmazni fognak.
- _Statikus adatok_: a gépi funkciók és az operátori funkciók általában statikusak, mivel leírják a gépek vagy a kezelő attribútumainak műszaki specifikációit. Ha ezek a funkciók idővel változhatnak, a hozzájuk tartozó időbélyegekkel is rendelkezhetnek.

A prediktív és a TARGET változókat előre fel kell dolgozni/át kell alakítani [számszerű, kategorikus és más adattípusokra](https://www.statsdirect.com/help/basics/measurement_scales.htm) a használt algoritmustól függően.

### <a name="data-preprocessing"></a>Adatfeldolgozás
A funkciók _mérnöki_ előfeltétele, hogy előkészítse a különböző streamekről származó adatok előkészítését egy olyan séma összeállításához, amelyből egyszerűen létrehozhatók szolgáltatások. Először jelenítse meg az adatokat a rekordok táblájában. A táblázat minden sora egy betanítási példányt jelöl, és az oszlopok a _prediktív_ funkciókat (más néven független attribútumokat vagy változókat) jelölik. Rendezze az adathalmazt úgy, hogy az utolsó oszlop (ok) a _cél_ (függő változó) legyen. Minden egyes betanítási példányhoz rendeljen egy _címkét_ az oszlop értékeként.

Az időszakos adatmennyiségek esetében az érzékelői adategységek időtartamát kell elosztani. Minden rekordnak egy adott eszközhöz tartozó időegységhez kell tartoznia, _és külön adatokat kell_ biztosítania. Az időegységek meghatározása az üzleti igények alapján több másodperc, perc, óra, nap, hónap stb. szerint történik. Az időegységnek _nem kell megegyeznie az adatgyűjtés gyakoriságával_. Ha a gyakoriság magas, előfordulhat, hogy az adatok nem mutatnak jelentős különbséget az egyik egységből a másikba. Tegyük fel például, hogy 10 másodpercenként összegyűjtötte a környezeti hőmérsékletet. Ha ugyanezt az intervallumot használja a betanítási adatokhoz, a további információk megadása nélkül csak a példák számát adja meg. Ebben az esetben a jobb stratégia az, hogy az adatátlagot 10 percen belül használja, vagy egy órát az üzleti indoklás alapján.

Statikus adatként
- _Karbantartási rekordok_: a nyers karbantartási adatok egy adott időpontban végrehajtott karbantartási tevékenységekre vonatkozó adatokat tartalmazó azonosítóval és időbélyeggel rendelkeznek. A karbantartási tevékenységeket _kategorikus_ oszlopokra alakíthatja át, ahol minden kategória leírója egyedi módon képezi le egy adott karbantartási műveletet. A karbantartási rekordok sémája magában foglalja az eszköz azonosítóját, az időt és a karbantartási műveletet.

- _Sikertelen rekordok_: a hibák vagy a meghibásodási okok az adott üzleti feltételek által meghatározott hibakódként vagy meghibásodási eseményként rögzíthetik. Abban az esetben, ha a berendezés több hibakódtal rendelkezik, a tartományi szakértőnek segítenie kell a célként megadott változóhoz tartozók azonosításában. A fennmaradó hibakódokkal vagy feltétellel a hibákkal kapcsolatos _előrejelző_ funkciókat hozhat létre. A sikertelen rekordok sémája magában foglalja az eszköz azonosítóját, az időpontot, a meghibásodást vagy a hiba okát – ha elérhető.

- _Számítógép-és operátori metaadatok_: egyesítse a gépet és az operátori adatokat egy sémába, hogy az eszközt társítsa a kezelőhöz, valamint a megfelelő attribútumokat. A gépi feltételek sémája magában foglalja az eszköz azonosítóját, az eszköz funkcióit, az operátor azonosítóját és a kezelő funkcióit.

Az egyéb adatok előfeldolgozási lépései közé tartozik a _hiányzó értékek_ és az attribútumérték _normalizálása_ . A részletes megbeszélések az útmutató hatókörén kívül találhatók – további hasznos hivatkozások a következő szakaszban olvashatók.

A fenti, feldolgozatlan adatforrások esetében a szolgáltatás mérnöki folyamata előtt a végső átalakítás a fenti táblázatokhoz csatlakozik, az eszköz azonosítója és az időbélyeg alapján. Az eredményül kapott tábla null értékekkel rendelkezhet a hiba oszlopnál, ha a gép normál működésben van. Ezeket a null értékeket a normál működés mutatója lehet. A hiba oszlop segítségével címkéket hozhat létre _a prediktív modellhez_. További információ: a [prediktív karbantartás modellezési módszereinek](#modeling-techniques-for-predictive-maintenance)szakasza.

## <a name="feature-engineering"></a>Jellemzőkiemelés
A szolgáltatás mérnöki funkciója az első lépés az adatmodellezés előtt. Az adatelemzési folyamat szerepkörét [itt ismertetjük](./create-features.md). A _funkció_ a modell prediktív attribútuma – például a hőmérséklet, a nyomás, a vibráció stb. A PdM esetében a funkciók Mérnöki tevékenysége a gép állapotának a jelentős időtartamon keresztül gyűjtött korábbi adatokkal való kivonását foglalja magában. Ebben az értelemben ez különbözik a társaitól, például a távoli figyeléstől, a rendellenesség észleléstől és a hibák észleléséről. 

### <a name="time-windows"></a>Idő Windows
A távoli figyelés magában foglalja az adott _időpontban_ megjelenő események jelentését. A anomáliák észlelési modelljei kiértékelik (a pontszám) a bejövő adatstreameket, amelyek a rendellenességeket jelzik az időpontokban. A hibák észlelése olyan típusú hibákat osztályoz, amelyek az adott időpontokban történnek. Ezzel szemben a PdM egy _jövőbeli_ időszakon belül a hibák előrejelzését is magában foglalja a _korábbi_ időszakokban a gépi viselkedést képviselő funkciók alapján. A PdM esetében az egyes időpontokból származó funkciók túl zajosak a prediktív megjelenítéshez. Így az egyes szolgáltatásokhoz tartozó adatpontokat az adatpontok időbeli összevonásával _kell kialakítani._

### <a name="lag-features"></a>Lag-funkciók
Az üzleti követelmények határozzák meg, hogy a modell milyen messzire becsülhető a jövőbe. Ennek az időtartamnak a segítségével meghatározhatja, hogy a modellnek milyen messzire kell kitérnie ahhoz, hogy megkeresse ezeket az előrejelzéseket. Ez a "visszakeresés" időszak a _késésnek_, és az ezen a késési időszakban megjelenő funkcióknak _lag-funkcióknak_ nevezik. Ez a szakasz azokat a késési funkciókat ismerteti, amelyek az adatforrásokból és a statikus adatforrásokból történő szolgáltatásokból hozhatók létre. A lag-funkciók jellemzően _számszerű_ jellegűek.

> [!IMPORTANT]
> Az ablakméret kísérletezéssel lett meghatározva, és egy tartományi szakértő súgójában kell véglegesíteni. Ugyanez a kikötés a késési funkciók, az összesítések és a Windows típusának kiválasztásával és meghatározásával is rendelkezik.

#### <a name="rolling-aggregates"></a>Gördülő összesítések
Egy adott eszköz minden rekordja esetében a "W" méretű gördülő ablak van kiválasztva az összesítések kiszámításához szükséges időegységek számaként. A lag funkcióit ezután a rendszer a rekord _dátumát megelőző W-pontokkal_ számítja ki. Az 1. ábrán a kék vonalak az egyes adategységek esetében az adott eszközön rögzített érzékelő értékeket mutatják. A funkciók értékének mozgóátlagát jelölik a W = 3 méretű ablakban. A gördülő átlagot az összes olyan rekordra számítjuk, amelyben a t<sub>1</sub> (narancssárga) és a t<sub>2</sub> közötti időbélyeg szerepel (zöld színnel). A W érték általában percben vagy órában, az adattermészettől függően. Bizonyos problémák esetén azonban a nagy mennyiségű (azaz 12 hónapos) adatmennyiséget a rekord teljes előzményeit is megadhatják.

![1. ábra Folyamatos összesítési funkciók](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

1. ábra Folyamatos összesítési funkciók

Egy adott időszakon belül a gördülő összesítések például a következők: darabszám, átlag, CUMESUM (összesített összeg) mértékek, minimális/maximális értékek. Ezen felül a szórás, a szórás és a kiugró értékek száma az N standard szóráson túl gyakran használatos. Alább láthatók az útmutató [használati eseteire](#sample-pdm-use-cases) alkalmazható összesítések. 
- _Repülési késleltetés_: a hibakódok száma az elmúlt nap/hét során.
- _Repülőgép-hajtóművek meghibásodása_: a gördülő, a szórás és az összeg az elmúlt nap, hét stb. esetében Ezt a metrikát az üzleti tartomány szakértővel együtt kell meghatározni.
- _ATM-hibák_: a működés közbeni középértékek, a medián, a tartomány, a szórások, a kiugró értékek száma a három szóráson felül, a felső és az alsó CUMESUM.
- _Földalatti betanítási hibák_: az elmúlt nap, hét, két hét stb. eseményeinek száma.
- _Áramkör-megszakítói hibák_: az elmúlt hét, év, három év stb. során fellépő hibák száma.

A PdM-ben egy másik hasznos módszer a trendek változásainak, a tüskéknek és a szintek változásainak az adatrendellenességet észlelő algoritmusok használatával történő rögzítése.

#### <a name="tumbling-aggregates"></a>Kieséses összesítések
Egy adott objektum minden címkézett rekordjánál meg van határozva egy " _w-<sub>k</sub>_ " méretű ablak, ahol a _k_ a " _w_" méretű ablakok száma. Az összesítéseket a rendszer a rekordok időbélyegének megkezdése előtti időszakokban hozza létre több mint _k_ -re felváltó _Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_ . a _k_ rövid távú hatások rögzítésére, illetve a hosszú távú romlási minták rögzítésére szolgáló nagy szám lehet. (lásd a 2. ábrát).

![2. ábra Az összesített funkciók kiesése](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

2. ábra Az összesített funkciók kiesése

Például a szélturbinák használati esetének késési funkcióit a W = 1 és a k = 3 használatával lehet létrehozni. Ezek az elmúlt három hónap késését jelentik az első és az alsó kiugró értékek használatával.

### <a name="static-features"></a>Statikus funkciók

A berendezések műszaki előírásai, például a gyártás dátuma, a modell száma, a hely, néhány példa a statikus funkciókra. Ezeket _kategorikus_ változókként kezelik modellezéshez. Az áramkör-megszakító használati esetének néhány példája a feszültség, a jelenlegi, az energiaellátási kapacitás, az átalakító típusa és az áramforrás. A kerekek meghibásodása esetén a gumiabroncs-kerekek típusa (ötvözött vs Steel) egy példa.

Az eddig tárgyalt adatelőkészítési erőfeszítéseknek az alább látható módon kell megszervezniük az adatgyűjtést. A betanítási, tesztelési és érvényesítési adatmennyiségnek tartalmaznia kell ezt a logikai sémát (ez a példa nap egységben jeleníti meg az időt).

| Eszköz azonosítója | Idő | \<Feature Columns> | Címke |
| ---- | ---- | --- | --- |
| A123 |1. nap | . . . | . |
| A123 |2. nap | . . . | . |
| ...  |...   | . . . | . |
| B234 |1. nap | . . . | . |
| B234 |2. nap | . . . | . |
| ...  |...   | . . . | . |

A Feature Engineering utolsó lépése a cél változó **címkézése** . Ez a folyamat a modellezési technikától függ. A modellezési technika pedig az elérhető adatok üzleti problémájára és természetétől függ. A címkézést a következő szakaszban tárgyaljuk.

> [!IMPORTANT]
> Az adatelőkészítés és a szolgáltatás-mérnöki funkciók olyan fontosak, mint a sikeres PdM-megoldások megérkezésére szolgáló modellezési technikák. A tartományi szakértőnek és a gyakorlónak jelentős időt kell fordítania a modell megfelelő funkcióinak és adatmennyiségének beérkezésére. A következő lista egy kis mintát tartalmaz a funkciók mérnöki szolgáltatásairól:
> - Pyle, D. adatgyűjtés adatbányászathoz (a Morgan Kaufmann sorozat adatkezelés rendszerekben), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: alapelvek és technikák adatszakértőknek, O'Reilly, 2018.
> - Dong, G. Liu, H. (szerkesztők), a Machine Learning és az adatelemzési funkciók (Chapman & Hall/CRC adatbányászat és a Knowledge Discovery sorozat), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modellezési technikák a prediktív karbantartáshoz

Ez a szakasz a PdM kapcsolatos fő modellezési technikákat ismerteti, valamint az adott címke-kialakítási módszerekkel. Figyelje meg, hogy egyetlen modellezési technikát használhat a különböző iparágakban. A modellezési technikát az adatelemzési problémához párosítjuk, nem pedig az aktuális adatkörnyezetek kontextusában.

> [!IMPORTANT]
> A meghibásodási esetek és a címkézési stratégia választható címkéi  
> a tartományi szakértővel való konzultáció során kell meghatározni.

### <a name="binary-classification"></a>Bináris besorolás
A bináris besorolással _megjósolható, hogy egy adott berendezés egy jövőbeli időszakon belül meghibásodik-e, azaz_ az _X jövőbeli horizontját_. Az X-t az üzleti probléma és az ott tárolt adatszolgáltatás határozza meg, a tartományi szakértővel való konzultáció során. Példák:
- az összetevők lecseréléséhez, karbantartási erőforrások üzembe helyezéséhez _minimálisan szükséges idő_ , karbantartási műveletek elvégzése az adott időszakban valószínűleg előforduló probléma elkerüléséhez.
- _az események minimális_ száma, amely a probléma megkezdése előtt fordulhat elő.

Ebben a technikában két különböző típusú tanítási példát azonosítottak. Egy pozitív példa, _amely egy hibát jelez_ a Label = 1 címkével. Negatív példa, amely normál műveleteket jelez a Label = 0 címkével. A cél változó, így a címke értékei _kategorikusak_. A modellnek minden olyan új példát azonosítania kell, amely valószínűleg sikertelen lesz, vagy a következő X időegységek esetében megfelelően működik.

#### <a name="label-construction-for-binary-classification"></a>Címkézési konstrukció a bináris besoroláshoz
A kérdés itt látható: "mi a valószínűsége annak, hogy az eszköz a következő X egységben sikertelen lesz?" A kérdés megválaszolásához X rekordokat kell megadnia az eszköz meghibásodása előtt "a sikertelen működéshez" (Label = 1), és az összes többi rekordot "normál" (Label = 0) címkével kell megjelölni. (lásd a 3. ábrát).

![3. ábra Címkézés a bináris besoroláshoz](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

3. ábra Címkézés a bináris besoroláshoz

Az alábbiakban felsorolunk néhány példát a címkézési stratégiára néhány használati esethez.
- _Repülési késések_: X kiválasztható egy nap, a késések előrejelzésére a következő 24 órában. Ezután az összes olyan járat, amely 24 órán belül van, a hibák előtt 1 lesz címkézve.
- _ATM készpénz_-kiosztási hibák: a következő egy órában egy tranzakció meghibásodási valószínűségét lehet meghatározni. Ebben az esetben a hiba elmúlt órájában történt összes tranzakció 1. A hiba valószínűségének előrejelzése a következő N pénznemre vonatkozó megjegyzések esetében: az utolsó N megjegyzésekben megadott összes Megjegyzés 1.
- _Áramkör-megszakítói hibák_: a cél lehet előre jelezni a következő áramkör-megszakító parancs hibáját. Ebben az esetben az X egy jövőbeli parancsként van kiválasztva.
- _Betanítási ajtó meghibásodása_: az X két napra kiválasztható.
- _Szélturbinák-hibák_: az X két hónapra kiválasztható.

### <a name="regression-for-predictive-maintenance"></a>Regresszió a prediktív karbantartáshoz
A regressziós modellek _egy adott eszköz hátralévő hasznos élettartamának (RUL) kiszámítására_ szolgálnak. A RUL azt az időtartamot határozza meg, ameddig egy eszköz működőképes lesz a következő hiba bekövetkezése előtt. Az egyes betanítási példa egy olyan rekord, amely egy, az adott objektumhoz tartozó, a _nY_ időegységéhez tartozik, ahol _n_ a többszörös. A modellnek minden új példa RUL kell kiszámítani _folytonos számként_. Ez a szám azt jelzi, hogy a hiba előtt hátralévő idő van-e.

#### <a name="label-construction-for-regression"></a>A regressziós címke felépítése
A kérdés a következő: "mi az a berendezés hátralévő hasznos élettartama (RUL)?" A hibát megelőzően minden rekordnál számítsa ki a címkét a következő hiba előtt hátralévő időegységek számának kiszámításához. Ebben a metódusban a feliratok folytonos változók. (Lásd a 4. ábrát)

![4. ábra A regresszió címkézése](./media/predictive-maintenance-playbook/labelling-for-regression.png)

4. ábra A regresszió címkézése

A regresszió esetében a címkézés egy meghibásodási pontra mutató hivatkozással történik. A számítás nem lehetséges anélkül, hogy megtudja, hogy az eszköz mennyi ideig élte túl a hibát. A bináris besorolással ellentétben tehát az adategységek nem használhatók modellezéshez. Ezt a problémát a [túlélés elemzése](https://en.wikipedia.org/wiki/Survival_analysis)nevű másik statisztikai módszer tárgyalja. Azonban lehetséges szövődmények merülhetnek fel a technika alkalmazásakor, hogy olyan használati eseteket PdM, amelyekben gyakran előfordulnak az idő-és időváltozók. A Survival Analysis szolgáltatással kapcsolatos további információkért tekintse meg [ezt az egy pagert](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Többosztályos besorolás a prediktív karbantartáshoz
A többosztályos besorolási technikák két forgatókönyv esetén használhatók PdM-megoldásokban:
- _Kétféle jövőbeli_ végeredmény: az első eredmény az adott eszköz _meghibásodásának egy tartománya_ . Az eszköz több lehetséges időszak egyikéhez van rendelve. A második eredmény az a valószínűsége, hogy egy jövőbeli időszakban hiba történt a _többszörös kiváltó okok egyike_ miatt. Ez az előrejelzés lehetővé teszi, hogy a karbantartási személyzet figyelje a tüneteket, és tervezze meg a karbantartási ütemterveket.
- Egy adott hiba _legvalószínűbb kiváltó okának_ előrejelzése. Ez az eredmény azt ajánlja, hogy a megfelelő karbantartási műveletekkel javítsa a hibát. A kiváltó okok rangsorolt listája és az ajánlott javítások segítségével a technikusok a meghibásodás után rangsorolják a javítási műveleteiket.

#### <a name="label-construction-for-multi-class-classification"></a>Címkézési konstrukció többosztályos besoroláshoz
A kérdés a következő: "mi a valószínűsége annak, hogy egy eszköz nem fog sikerülni a következő _nZ_ -egységekben, ahol _n_ az időszakok száma?" A kérdés megválaszolásához az nZ rekordjait az eszköz meghibásodása előtti idő (3Z, 2Z, Z) alapján címkézheti. Az összes többi rekordot "normál" (Label = 0) címkével címkézheti. Ebben a metódusban a TARGET változó a _kategorikus_ értékeket tartalmazza. (Lásd az 5. ábrát).

![5. ábra A többosztályos besorolásnál fellépő hibák időpontjának előrejelzése](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

5. ábra A többosztályos besorolás címkézése a sikertelen idő előrejelzéséhez

A kérdés itt látható: "mi a valószínűsége annak, hogy az eszköz a következő X egységben sikertelen lesz a kiváltó ok/probléma miatt. _P <sub>i</sub>_?" hol _vagyok_ a lehetséges kiváltó okok száma. A kérdés megválaszolásához X rekordok felirata az eszköz meghibásodása előtt "a hiba oka, hogy a root ok miatt meghiúsul a _p <sub>i</sub>_" (Label = _P <sub>i</sub>_). Címkézse az összes többi rekordot "normál" (Label = 0) értékként. Ebben a módszerben a címkék kategorikusak (lásd a 6. ábrát).

![6. ábra Kiváltó okok előrejelzési címkéi többosztályos besoroláshoz](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

6. ábra Címkézés a többosztályos besoroláshoz a kiváltó okok előrejelzéséhez

A modell egy hiba valószínűségét rendeli hozzá a _P <sub>i</sub>_ . és a hiba valószínűsége miatt. Ezek a valószínűségek úgy rendelhetők meg, hogy a jövőben várhatóan előforduló problémák előrejelzését lehetővé teszik.

A kérdés a következő: "milyen karbantartási műveleteket javasol a hiba után?" A kérdés megválaszolásához a címkézés nem _igényel jövőbeli horizontot_, mivel a modell a jövőben nem jósol előre hibát. Csak a legvalószínűbb kiváltó okot Jósolja meg, _Ha a hiba már megtörtént_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Képzési, ellenőrzési és tesztelési módszerek a prediktív karbantartáshoz
A [csoportos adatelemzési folyamat](./overview.md) teljes körű lefedettséget biztosít a modell Train-test-validate ciklusával. Ez a szakasz a PdM egyedi aspektusait ismerteti.

### <a name="cross-validation"></a>Több érvényesítés
A [több érvényesítés](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) célja, hogy Definiáljon egy olyan adatkészletet, amely a modellnek a betanítási fázisban történő "tesztelés" értékre van állítva. Ezt az adatkészletet az _ellenőrzési csoportnak_ nevezzük. Ez a módszer segít korlátozni a problémákat, például a túlterhelést, és betekintést nyújt _arról, hogyan_ általánosítja a modell egy független adatkészletet. Ez egy ismeretlen adathalmaz, amely valódi problémából származhat. A PdM betanítási és tesztelési rutinjának figyelembe kell vennie a különböző szempontokat a láthatatlan jövőbeli adatelemzések jobb általánosítása érdekében.

A gépi tanulási algoritmusok számos hiperparaméterek beállítása függenek, amelyek jelentősen megváltoztathatják a modell teljesítményét. A modell betanításakor a rendszer nem számítja fel automatikusan a hiperparaméterek beállítása optimális értékét. Ezeket az adattudósnak kell megadnia. Több módon is megtalálhatja a hiperparaméterek beállítása helyes értékeit.

A leggyakoribb érték a _k-szor_ , amely a példákat véletlenszerűen felosztja _k_ -re. Az egyes hiperparaméterek beállítása-értékek esetében futtassa a " _k_ " tanulási algoritmust. Minden egyes iteráció során használja az aktuálisan megadott példát ellenőrzési készletként, a többi példát pedig betanítási csoportként. Betanítja az algoritmust a betanítási példák fölé, és kiszámítja a teljesítmény mérőszámait az érvényesítési példákban. A hurok végén számítjuk ki a _k_ teljesítmény-mérőszámok átlagát. Az egyes hiperparaméter értékek esetében válassza ki azokat, amelyek a legjobb átlagos teljesítménnyel rendelkeznek. A hiperparaméterek beállítása kiválasztásának feladata gyakran kísérleti jellegű.

PdM problémák esetén a rendszer az adatok időbeli sorozatát rögzíti, amely több adatforrásból származik. Ezeket a rekordokat a címkézés időpontja szerint lehet megrendelni. Ezért ha az adatkészlet _véletlenszerűen_ van felosztva a betanítási és az ellenőrzési csoportba, _néhány betanítási példa később is előfordulhat, mint néhány érvényesítési példa_. A hiperparaméter-értékek jövőbeli teljesítményét a rendszer a modell betanítása _előtt_ beérkező adatok alapján becsüli. Ezek a becslések túlságosan optimisták lehetnek, különösen akkor, ha az idősorozat nem álló, és az idő múlásával fejlődik. Ennek eredményeképpen a kiválasztott hiperparaméter-értékek inoptimálisak lehetnek.

Az ajánlott módszer a példák betanításra és érvényesítésre való felosztása _időfüggő_ módon, ahol az összes ellenőrzési példa később, mint az összes betanítási példa. A hiperparaméter minden egyes készlete esetében az algoritmus betanítása a betanítási adatkészletbe. A modell teljesítményének mérése ugyanazzal az ellenőrzési csoporttal. Válassza ki azokat a hiperparaméter értékeket, amelyek a legjobb teljesítményt mutatják. A vonat/érvényesítési felosztás által választott hiperparaméter-értékek jobb jövőbeli modell-teljesítményt eredményeznek, mint a többes hitelesítéssel véletlenszerűen kiválasztott értékek.

A végső modell a legjobb hiperparaméter értékek használatával a tanulási algoritmusok teljes betanítási adatokon keresztül történő képzésével hozható létre.

### <a name="testing-for-model-performance"></a>Modell teljesítményének tesztelése
A modell felépítése után az új adatmennyiségre vonatkozó jövőbeli teljesítményre vonatkozó becslés szükséges. A jó becslés az ellenőrzési készleten kiszámított hiperparaméter értékek teljesítményének mérőszáma, vagy az átellenőrzésből kiszámított átlagos teljesítmény-metrika. Ezek a becslések gyakran túlzottan optimistaak. Előfordulhat, hogy a vállalat gyakran tartalmaz néhány további útmutatást arról, hogy miként szeretnék tesztelni a modellt.

A PdM ajánlott módja, ha a példákat a tanítás, az érvényesítés és a tesztelési adatkészletek _időfüggő_ módon kell felosztani. Az összes tesztelési példát később kell megadnia, mint az összes tanítási és érvényesítési példát. A felosztás után létrehozza a modellt, és mérje fel a teljesítményét a korábban leírtak szerint.

Az idősorozatok és a megjósolható idő alapján a véletlenszerű és az időfüggő megközelítés is hasonló becsléseket eredményez a jövőbeli teljesítményről. Ha azonban az idősorozat nem állomásos, és/vagy nehezen megjósolható, az időfüggő megközelítés reálisabb becsléseket fog eredményezni a jövőbeli teljesítményről.

### <a name="time-dependent-split"></a>Időfüggő felosztás
Ez a szakasz az időfüggő felosztás megvalósításának ajánlott eljárásait ismerteti. Az alábbiakban ismertetjük a képzés és a tesztelési készletek közötti időfüggő kétirányú osztást.

Az időbélyeggel ellátott események, például a különböző érzékelőktől származó mérések továbbítása. Adja meg a betanítási és tesztelési példákat a több eseményt tartalmazó időbeli keretekben. Például a bináris besoroláshoz, a múltbeli események alapján hozzon létre szolgáltatásokat, és hozzon létre címkéket a jövőben az "X" időegységeken belüli jövőbeli események alapján (lásd a [funkciók mérnöki](#feature-engineering) és modellezési technikák című szakaszt). Így egy példa címkézési időkerete későbbi, mint a funkcióinak időbeli kerete.

Az időfüggő felosztáshoz válasszon ki egy, a modell betanításához szükséges _kiképzési <sub></sub> időt_ , amelynek során a hiperparaméterek beállítása-t a korábbi és a t <sub>c</sub>értékekkel hangolják össze. Ha meg szeretné akadályozni, hogy a jövőbeli címkék ne legyenek a betanítási adatmennyiségen felül, válassza ki a legkésőbbi betanítási példákat X egységként<sub>a t</sub> <sub>c</sub>előtt. A 7. ábrán látható példában mindegyik tér egy olyan rekordot jelöl, amelyben a funkciók és címkék a fent leírtak szerint vannak kiszámítva. Az ábra azokat a rekordokat mutatja be, amelyeknek az X = 2 és a W = 3 esetében be kell lépniük a képzési és tesztelési készletekbe:

![7. ábra Időfüggő felosztás a bináris besoroláshoz](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

7. ábra Időfüggő felosztás a bináris besoroláshoz

A zöld négyzetek a betanításhoz használható időegységekhez tartozó rekordokat jelölik. Az egyes betanítási példákat a szolgáltatás generációjának utolsó három időszakára, valamint a T<sub>c</sub>előtti címkézésre két jövőbeli időszakra vonatkozóan kell létrehozni. Ha a két jövőbeli időszak bármelyik része meghaladja a T<sub>c</sub>-t, akkor kizárhatja ezt a példát a betanítási adatkészletből, mert nem feltételezhető, hogy a t<sub>c</sub>-t túlmutat.

A fekete négyzet a végső címkézett adathalmaz azon rekordjait jelöli, amelyeket nem szabad a betanítási adatkészletben használni, a fenti korlátozás miatt. Ezeket a rekordokat a rendszer nem használja a tesztelési adatokban, mivel azok a T<sub>c</sub>előtt vannak. Emellett a címkézési időkeretek részben a betanítási időszaktól függenek, ami nem ideális. A betanítási és tesztelési adatoknak külön címkéző időkeretekkel kell rendelkezniük a címkézési adatok szivárgásának megakadályozása érdekében.

Az eddig tárgyalt eljárás lehetővé teszi az átfedést a betanítási és tesztelési példák között, amelyek időbélyege a T<sub>c</sub>közelében van. A nagyobb elkülönítés elérésére irányuló megoldás a tesztelési készletben lévő T<sub>c</sub> -n belül található W-Time egységekben található példák kizárása. Az agresszív felosztás azonban az adatok bőséges rendelkezésre állásával függ.

A RUL előrejelzéséhez használt regressziós modellek Súlyosabb hatással vannak a szivárgási problémákra. A véletlenszerű felosztási módszer használata extrém túlillesztést eredményez. A regressziós problémák esetén a felosztásnak olyannak kell lennie, hogy az eszközökhöz tartozó rekordok a betanítási készletbe kerüljenek, mielőtt a T<sub>c</sub> bejelentkeznek. Azon eszközök rekordjai, amelyek meghibásodása után a kivágás után a teszt be lett állítva.

Az adatgyűjtési és-tesztelési célú adatmegosztáshoz egy másik ajánlott eljárás az objektum-azonosító felosztásának használata. A felosztásnak olyannak kell lennie, hogy a betanítási készletben használt egyik eszköz se legyen használatban a modell teljesítményének tesztelésében. Ennek a módszernek a használatával a modell nagyobb eséllyel nyújt reálisabb eredményeket az új eszközökkel.

### <a name="handling-imbalanced-data"></a>Kiegyensúlyozatlan adathalmazok kezelése
Besorolási problémák esetén, ha több példa van egy osztályra, mint a többitől, az adathalmazt a rendszer _kiegyensúlyozza_. Ideális esetben a betanítási adatkészletek minden osztályának megfelelő képviselői előnyben részesítettek a különböző osztályok közötti különbségtétel lehetővé tételéhez. Ha az egyik osztály kevesebb, mint 10%-a az adatmennyiségnek, akkor az adatmennyiség kiegyensúlyozatlan tekintendő. A kijelölt osztály neve _kisebbségi osztály_.

Sok PdM probléma merül fel ilyen kiegyensúlyozatlan adatkészletekben, ahol az egyik osztályt súlyosan kimutatják a másik osztályhoz vagy osztályokhoz képest. Bizonyos helyzetekben a kisebbségi osztály a teljes adatpontok 0,001%-át is kiszámíthatja. Az osztály egyensúlyhiánya nem egyedi a PdM. Más tartományok, ahol a hibák és anomáliák ritka előfordulások hasonló problémákkal szembesülnek, például a csalások észlelésével és a hálózati behatolással kapcsolatban. Ezek a hibák a kisebbségi osztály példáit alkotják.

Az adatkezelési osztályok esetében a legtöbb szabványos tanulási algoritmus teljesítménye sérült, mivel ezek a hibák a teljes hiba mértékének minimalizálását célozzák meg. Az 99%-os negatív és 1%-os pozitív példákkal rendelkező adatkészletek esetében a modell az összes példány negatívként való címkézésével megjeleníthető 99%-os pontossággal. A modell azonban helytelenül osztályozza az összes pozitív példát; Tehát még ha a pontossága is magas, az algoritmus nem hasznos. Ennek következtében a hagyományos értékelési mérőszámok, például _a hibák összesített pontossága_ nem elegendő a kiegyensúlyozatlan tanuláshoz. Ha kiegyensúlyozatlan adatkészletekkel szembesül, a modell kiértékeléséhez más mérőszámok is használhatók:
- Pontosság
- Recall
- F1 pontszámok
- Cost kiigazított ROC (fogadó működési jellemzői)

További információ ezekről a metrikákkal kapcsolatban: [modell kiértékelése](#model-evaluation).

Vannak azonban olyan módszerek, amelyek segítenek az osztály egyensúlyhiányi problémájának orvoslásában. A két fő közül a _mintavételi technikák_ és a _költséges tanulás_.

#### <a name="sampling-methods"></a>Mintavételi módszerek
A kiegyensúlyozatlan tanulás magában foglalja a mintavételi módszerek használatát, amelyekkel módosíthatja a betanítási adatkészletet egy kiegyensúlyozott adathalmazra. A mintavételi módszerek nem alkalmazhatók a tesztelési készletre. Bár több mintavételi módszer is létezik, a legtöbb közvetlen továbbítás _véletlenszerű túlmintavételezést_ és _mintavételezést_ is végez.

_Véletlenszerű túlmintavételezés_ esetén a rendszer kiválasztja a kisebbségi osztályból származó véletlenszerű mintát, replikálja ezeket a példákat, és hozzáadja őket a betanítási adatkészlethez. Ennek következtében a kisebbségi osztályban található példák száma megnő, és végül egyensúlyt teremt a különböző osztályokba tartozó példák száma. A túlmintavételezés hátránya, hogy bizonyos példák több példánya is kiválthatja, hogy az osztályozó túlságosan egyedivé váljon, ami túlillesztést eredményez. A modell magas betanítási pontosságot mutathat be, de a láthatatlan tesztelési adatok teljesítménye is optimális lehet.

Ezzel szemben a _mintavétel során véletlenszerűen_ választ egy véletlenszerű mintát a többségi osztályból, és eltávolítja ezeket a példákat a betanítási adatkészletből. A többségi osztályból származó példák eltávolítása azonban azt eredményezheti, hogy az osztályozó nem hagyja ki a többségi osztályra vonatkozó fontos fogalmakat. A _hibrid mintavétel_ , amelyben a kisebbségi osztály a mintavételen kívüli és a többségi osztály alatt van, egy másik életképes megközelítés.

Számos kifinomult mintavételi módszer létezik. A választott módszer az adattudós által az adatok tulajdonságaitól és az ismétlődő kísérletek eredményeitől függ.

#### <a name="cost-sensitive-learning"></a>Cost szenzitív learning
A PdM a kisebbségi osztályt alkotó hibák a szokásos példáknál nagyobb érdeklődést mutatnak. Így a hangsúly főleg az algoritmus teljesítményének hibáján alapul. Ha a pozitív osztályt helytelenül jósolják meg, akkor a negatív osztály nem lehet nagyobb, mint fordítva. Ezt a helyzetet általában a különböző osztályokra való helytelen besorolású elemek nem egyenlő mértékű elvesztése vagy aszimmetrikus díja okozta. Az ideális osztályozó magas előrejelzési pontosságot biztosít a kisebbségi osztályon, a többségi osztály pontosságának veszélyeztetése nélkül.

Ezt az egyensúlyt többféle módon is elérheti. A nem egyenlő veszteségek problémájának enyhítéséhez rendeljen magas költségeket a kisebbségi osztály hibás besorolásához, és próbálja meg minimálisra csökkenteni a teljes költségeket. Az olyan algoritmusok, mint a _SVMs (support Vector machines)_ , ez a metódus önmagában is bevezethető, mivel lehetővé teszi a pozitív és negatív példák bevezetését a képzés során. Hasonlóképpen, a többek között a _megnövelt döntési fákat_ is kihasználó módszerek általában a jó teljesítményt mutatják be a kiegyensúlyozatlan adatokkal.

## <a name="model-evaluation"></a>A modell kiértékelése
A helytelen besorolás jelentős problémát jelent a PdM-forgatókönyvek esetében, ahol a hamis riasztások üzleti szempontból magas a díja. Ha például egy repülőgépnek a motor meghibásodása miatt nem megfelelő előrejelzése alapján dönt, akkor megszakadhat az ütemtervek és az utazási csomagok. Ha egy gépet offline állapotba helyez egy szerelvényből, a bevétel elvesztését vonhatja maga után. Így a modell kiértékelése a megfelelő teljesítmény-mérőszámokkal az új tesztelési adatok esetében kritikus fontosságú.

A PdM modellek kiértékeléséhez használt jellemző teljesítménymutatókat az alábbiakban tárgyaljuk:

- A [pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision) a legelterjedtebb metrika, amely az osztályozó teljesítményének leírására szolgál. A pontosság azonban érzékeny az adateloszlásokra, és nem hatékony mérőszámot jelent a kiegyensúlyozatlan adatkészletekkel rendelkező forgatókönyvek esetében. A rendszer a többi mérőszámot használja helyette. Az olyan eszközök, mint a [zavartsági mátrix](https://en.wikipedia.org/wiki/Confusion_matrix) , a modell pontosságának kiszámítására és indoklására szolgálnak.
- A PdM modellek [pontossága](https://en.wikipedia.org/wiki/Precision_and_recall) a téves riasztások arányára vonatkozik. A modell alacsonyabb pontossága általában nagyobb, mint a hamis riasztások aránya.
- A [visszahívási](https://en.wikipedia.org/wiki/Precision_and_recall) arány azt jelzi, hogy a modell által jelzett hibák közül hányat helyesen azonosítottak. A magasabb visszahívási arány azt jelenti, hogy a modell sikeres volt a valódi hibák azonosítása során.
- Az [F1 pontszám](https://en.wikipedia.org/wiki/F1_score) a pontosság és a visszahívás harmonikus átlaga, amelynek értéke 0 (legrosszabb) és 1 (a legjobb) között van.

Bináris besorolás esetén
- A [fogadó működési görbék (Roc)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) szintén népszerű metrika. A ROC-görbékben a modell teljesítményének értelmezése a ROC egyik rögzített működési pontján alapul.
- A PdM problémák miatt azonban a _decile táblák_ és a _lift-diagramok_ is részletesebbek. Csak a pozitív osztályra (hibákra) koncentrálnak, és összetettebb képet adnak az algoritmus teljesítményéről, mint a ROC-görbék.
  - A _Decile táblák_ a hibák valószínűségének csökkenő sorrendjében, tesztelési példák használatával jönnek létre. A rendezett mintákat ezután a deciles (a minták 10%-ában, a legnagyobb valószínűséggel, 20%-kal, 30%-kal stb.) csoportosítjuk. Az egyes decile arányának (valódi pozitív arány)/(véletlenszerű alapkonfiguráció) segítségével megbecsülhető az algoritmus teljesítménye az egyes decile. A véletlenszerű alapterv a 0,1, 0,2 és hasonló értékeket veszi figyelembe.
  - A [lift-diagramok](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) a decile valódi pozitív arányát és az összes deciles véletlenszerű valódi pozitív arányát ábrázolják. Az első deciles általában az eredmények középpontjában állnak, mivel a legnagyobb nyereséget mutatják. Az első deciles a "veszélyben" képviselőként is láthatja, ha a PdM használja.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modell operacionalizálási a prediktív karbantartáshoz

Az adatelemzési gyakorlat kihasználása csak akkor valósul meg, ha a betanított modell működőképes. Ezt a modellt az üzleti rendszerbe kell telepíteni, hogy az előrejelzések az új, korábban láthatatlan, adatértékek alapján legyenek elérhetők.  Az új adatmennyiségnek pontosan meg kell felelnie a betanított modell _aláírásának_ két módon:
- minden funkciónak jelen kell lennie minden logikai példányban (mondjuk egy tábla egy sorában) az új adatmennyiséget.
- az új adatmennyiséget előre fel kell dolgozni, és a betanított funkciók mindegyikét pontosan ugyanúgy kell megadnia, mint a betanítási adatként.

A fenti folyamat számos módon van meghatározva az egyetemi és az iparági irodalomban. Az alábbi utasítások azonban ugyanazt a dolgot jelentik:
- _Új adatértékek kiértékelése_ a modell használatával
- _A modell alkalmazása_ új adatértékekre
- A modell _működővé tenni_
- A modell _üzembe helyezése_
- _A modell futtatása_ új adatértékekkel

Ahogy korábban már említettük, a PdM operacionalizálási modellje eltér a társaitól. A rendellenességek észlelését és a hibák észlelését érintő forgatókönyvek jellemzően az _online pontozást_ (más néven _valós idejű pontozást_) implementálják. Itt _a modell minden_ bejövő rekordot leolvas, és egy előrejelzést ad vissza. A anomáliák észlelése esetén az előrejelzés azt jelzi, hogy anomália történt (példa: egyosztályos SVM). A hiba észlelése esetén a hiba típusa vagy osztálya lenne.

Ezzel szemben a PdM a _Batch pontozást_ is magában foglalja. Ahhoz, hogy meg lehessen felelni a modell aláírásának, az új adatképzések funkcióit ugyanúgy kell megtervezni, mint a betanítási adatgyűjtést. Az új adatokat jellemző nagyméretű adatkészletek esetében a funkciók az idő múlásával összesítve jelennek meg a Windowsban, és a kötegben jelennek meg. A kötegelt pontozás általában olyan elosztott rendszerekben történik, mint a [Spark](https://spark.apache.org/) vagy a [Azure batch](../../batch/batch-service-workflow-features.md). Több alternatíva is létezik – az optimálisnál is:
- A streaming adatkezelők támogatják a memóriában lévő Windows-összesítést. Azt is mondhatjuk, hogy az online pontozást támogatják. Ezek a rendszerek azonban alkalmasak arra, hogy a sűrű, szűk idő alatt lévő adatokat vagy a szélesebb körben megjelenő elemeket használják. Előfordulhat, hogy a PdM-forgatókönyvekben látható módon nem méretezhetők jól a sűrű adatoknak a szélesebb időkeretek között.
- Ha a Batch-pontozási szolgáltatás nem érhető el, a megoldás az, hogy az online pontozást a kis kötegekben lévő új adatmennyiségek kezeléséhez igazítsa.

## <a name="solution-templates-for-predictive-maintenance"></a>Megoldási sablonok a prediktív karbantartáshoz

Az útmutató utolsó szakasza az Azure-ban megvalósított PdM-megoldási sablonok, oktatóanyagok és kísérletek listáját tartalmazza. Ezek a PdM alkalmazások bizonyos esetekben néhány percen belül üzembe helyezhetők Azure-előfizetésekben. Felhasználhatók a megvalósíthatósági bemutatók, a munkaterületek és az alternatívák kipróbálására, illetve az éles környezetek tényleges megvalósítására szolgáló gyorssegédek. Ezek a sablonok a [Azure AI Gallery](https://gallery.azure.ai) vagy az [Azure githubon](https://github.com/Azure)találhatók. Ezek a különböző minták a megoldás sablonjában jelennek meg az idő múlásával.

| # | Cím | Leírás |
|--:|:------|-------------|
| 2 | [Azure prediktív karbantartási megoldás sablonja](https://github.com/Azure/AI-PredictiveMaintenance) | Egy nyílt forráskódú megoldási sablon, amely bemutatja az Azure ML-modellezést és egy teljes körű Azure-infrastruktúrát, amely képes a prediktív karbantartási forgatókönyvek támogatására a IoT távoli monitorozása kontextusában. |
| 3 | [Mélytanulás a prediktív karbantartáshoz](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Az Azure notebook olyan bemutató megoldással rendelkezik, amely a prediktív karbantartáshoz LSTM (hosszú Short-Term memória) hálózatokat (az ismétlődő neurális hálózatok osztályát) használja, és [Ez a minta blogbejegyzés](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Azure prediktív karbantartás a repülőgépipar számára](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Az egyik első PdM megoldás-sablon, amely az Azure ML v 1.0-s verzióján alapul a repülőgépek karbantartásához. Ez az útmutató ebből a projektből származik. |
| 5 | [Azure AI-eszközkészlet a IoT Edgehoz](https://github.com/Azure/ai-toolkit-iot-edge) | AI a IoT Edge a TensorFlow használatával; Az eszközkészlet részletes tanulási modelleket biztosít Azure IoT Edge-kompatibilis Docker-tárolókban, és a modelleket REST API-ként teszi elérhetővé.
| 6 | [Azure IoT prediktív karbantartás](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite számítógépek – előre konfigurált megoldás. A repülőgép-karbantartási PdM sablon IoT Suite. [Egy másik](/previous-versions/azure/iot-accelerators/about-iot-accelerators) , ugyanahhoz a projekthez kapcsolódó dokumentum és [útmutató](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) . |
| 7 | [Prediktív karbantartási sablon SQL Server R Services használatával](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Az R-szolgáltatásokon alapuló, hátralévő hasznos élettartam forgatókönyvének bemutatója. |
| 8 | [Prediktív karbantartási modellezési útmutató](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | A repülőgép-karbantartási adatkészlet az R [használatával kísérletezik](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) és [adatkészletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) , valamint Azure notebookok és [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) a AzureML 1.0-s verziójában|

## <a name="training-resources-for-predictive-maintenance"></a>Erőforrások betanítása a prediktív karbantartáshoz

A Microsoft Azure a PdM-technikák mögötti alapfogalmakhoz nyújt képzési terveket, az általános AI-fogalmakkal és gyakorlattal kapcsolatos tartalmak és képzések mellett.

| Oktatási erőforrás  | Rendelkezésre állás |
|:-------------------|--------------|
| [Képzési terv a PdM a fák és a véletlenszerű erdő használatával](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Nyilvános | 
| [Képzési terv a PdM a Deep learning használatával](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Nyilvános |
| [AI-fejlesztő az Azure-ban](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Nyilvános |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Nyilvános |
| [Azure AI learning a GitHubról](https://github.com/Azure/connectthedots/blob/master/readme.md) | Nyilvános |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Nyilvános |
| [Microsoft AI YouTube-előadások](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Nyilvános |
| [Microsoft AI-show](https://channel9.msdn.com/Shows/AI-Show) | Nyilvános |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerek |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partnerek |

Továbbá az AI-on elérhető ingyenes MOOC (masszív nyílt online tanfolyamok) olyan oktatási intézmények által is elérhetők, mint a Stanford és az MIT, valamint más oktatási vállalatok.