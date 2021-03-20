---
title: Etika és felelősségteljes használat – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ezek az irányelvek célja, hogy segítséget nyújtson a személyre szabás kialakításában a vállalat és a szolgáltatás megbízhatóságának kiépítésében. Ügyeljen arra, hogy az emberek életében való személyre szabás hatására a kutatásra, a tanulásra és a tudatos működésre figyeljen. Ha kétségei vannak, kérjen útmutatást.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005979"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Útmutatás a személyre szabáshoz

Ahhoz, hogy az emberek és a társadalom teljes mértékben kiaknázzák a mesterséges intelligenciát, a megvalósításokat úgy kell megtervezni, hogy a mesterséges intelligenciát az alkalmazásokhoz és a mesterséges intelligenciával létrehozott alkalmazások felhasználóihoz jussanak. Ezek az irányelvek arra szolgálnak, hogy segítsenek a személyre szabást olyan módon megvalósítani, amely segít a vállalat és a szolgáltatás megbízhatóságának kiépítésében. Ügyeljen arra, hogy az emberek életében való személyre szabás hatására a kutatásra, a tanulásra és a tudatos működésre figyeljen. Ha kétségei vannak, kérjen útmutatást.

Ezek az irányelvek nem jogi tanácsadásnak minősülnek, és külön kell gondoskodni arról, hogy az alkalmazás megfeleljen az ezen a területen és az ágazatban érvényes, gyors ütemben zajló fejleményeknek.

Emellett az alkalmazás személyre szabásának megtervezése során érdemes figyelembe vennie az adatközpontos AI-rendszerek (például az etika, az adatvédelem, a biztonság, a biztonság, a beilleszkedés, az átláthatóság és az elszámoltathatóság) fejlesztése során felmerülő feladatok széles körét. Ezekről a [javasolt olvasási](#recommended-reading) szakaszban olvashat bővebben.

A következő tartalmakat kezdő ellenőrzőlistaként használhatja, és testreszabhatja és pontosíthatja a forgatókönyvet. Ez a dokumentum két fő részből áll: az első kiemelve a felelős használati szempontokat a személyre szabott forgatókönyvek, funkciók és előnyök kiválasztásakor. A másodikban a Microsoft megítélése szerint figyelembe kell venni az AI-rendszerek kiépítését, és gyakorlati javaslatokat és kockázatokat biztosít a személyre szabott felhasználói beavatkozások befolyásolásához.


## <a name="your-responsibility"></a>Az Ön felelőssége

A felelős implementációra vonatkozó összes iránymutatás arra épül, hogy a fejlesztők és a személyre szabott vállalkozások felelősek az ilyen algoritmusok társadalomban való használatának következményeiért. Ha a szervezete által üzembe helyezett alkalmazást fejleszt, fel kell ismernie a szerepkörét és a felelősségét, valamint azt, hogy miként befolyásolja az embereket. Ha egy harmadik fél által központilag üzembe helyezett alkalmazást tervez megtervezni, akkor közösen megértette, hogy kik felelősek az alkalmazás működésével kapcsolatban, és dokumentálják azt.

A bizalmi kapcsolat a teljesített kötelezettségvállalásokra épül – vegye fontolóra a felhasználókat, a társadalmat és az alkalmazások által működtetett jogi keretrendszert a alkalmazásban, hogy azonosítsa az esetlegesen fennálló explicit és implicit kötelezettségvállalásokat.

A Microsoft folyamatosan fejleszti az eszközeit és dokumentumait, hogy segítséget nyújtson ezeknek a feladatoknak a meghozatalában. [Küldjön visszajelzést a Microsoftnak](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) , ha úgy véli, hogy további eszközök, a termékek funkciói és dokumentumai segítenek a személyre szabott irányelvek megvalósításában.


## <a name="factors-for-responsibly-implementing-personalizer"></a>A személyre szabott személyre szabáshoz szükséges tényezők

A személyre szabott személyre szabás a felhasználók és a vállalat számára is nagy értékű lehet. Ha felelősségteljesen szeretné megvalósítani a személyre szabást, kezdje az alábbi irányelvek megadásával:

* A használati esetek kiválasztásával alkalmazhatja a személyre szabást.
* A [jutalmazási funkciók](concept-rewards.md)kiépítése.
* Kiválaszthatja, hogy mely [funkciókra](concepts-features.md) van szüksége a kontextushoz, és milyen műveleteket fog használni a személyre szabáshoz.


## <a name="choosing-use-cases-for-personalizer"></a>Személyre szabott használati esetek kiválasztása

Hasznos, ha olyan szolgáltatást használ, amely a tartalom és a felhasználói felületek személyre szabását tanulja meg. Akkor is előfordulhat, ha a személyre szabási módszer negatív hatással van a valós világra, például ha a felhasználók nem ismerik a tartalom személyre szabását.

Többek között a negatív mellékhatásokkal vagy az átláthatóság hiányával kapcsolatos, a személyre szabott funkciókkal kapcsolatos példák olyan forgatókönyveket mutatnak be, amelyekben a "jutalom" olyan hosszú távú összetett tényezőktől függ, amelyeknek az azonnali jutalomban való egyszerűsítése az egyéni felhasználók számára nem kedvező eredménnyel járhat. Ezek általában "KÖVETKEZMÉNYES" döntéseknek minősülnek, vagy olyan döntések, amelyek ártalmas kockázatot jelenthetnek. Például:


* **Pénzügy**: a hitelek, pénzügyi és biztosítási termékek személyre szabására szolgáló ajánlatok, amelyekben a kockázati tényezők azon adatokon alapulnak, amelyeket a magánszemélyek nem ismerik, nem szerezhetnek be vagy nem vitatnak meg.
* **Oktatás**: az iskolai kurzusok és oktatási intézmények személyre szabása, ahol a javaslatok az eltéréseket propagálják, és csökkenthetik a felhasználók figyelmét a többi lehetőségre is.
* **Demokrácia és állampolgári részvétel**: a tartalom személyre szabása a vélemények befolyásolásának céljával a felhasználók számára.
* **Harmadik féltől származó jutalom kiértékelése**: olyan elemek személyre szabása, amelyekben a jutalom a felhasználó egy későbbi, harmadik féltől származó kiértékelésén alapul, és nem a felhasználó saját viselkedése alapján generált jutalmat.
* A **feltárás intolerancia**: olyan helyzetek, amikor a személyre szabott feltárási viselkedés ártalmas lehet.

A személyre szabott használati esetek kiválasztásakor:

* Indítsa el a tervezési folyamatot, és mérlegelje, hogy a személyre szabás hogyan segíti a felhasználókat.
* Ha egyes elemek személyre szabási mintázatok vagy feltárás miatt nem vannak rangsorolva a felhasználók számára, vegye figyelembe a negatív következményeket a valós világban.
* Gondolja át, hogy a használati eset olyan automatizált feldolgozást jelent-e, amely jelentősen befolyásolja az olyan érintett személyeket, akiket a 22. és más törvények értelmében [GDPR](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) .
* Érdemes lehet önmagát betartani a próféciai hurkokat. Ez akkor fordulhat elő, ha a személyre szabási jutalom egy modellt vezet be, így később további kizárhatók a demográfiai csoportok a kapcsolódó tartalmakhoz való hozzáféréstől. Az alacsony jövedelmű környék legtöbb embere például nem kap prémium szintű biztosítási ajánlatot, és lassan senki sem jelenik meg a környéken, ha nincs elég felderítés.
* Mentse a modelleket és a tanulási szabályzatokat, ha a jövőben újra létre kell majd állítani a személyre szabást. Ezt időszakonként vagy minden modell-frissítési időszakban megteheti.
* Vegye figyelembe a terület számára megfelelő feltárási szintet, valamint azt, hogy miként használható az "Echo Chamber" effektusok enyhítésére szolgáló eszközként.


## <a name="selecting-features-for-personalizer"></a>A személyre szabott szolgáltatások kiválasztása

A tartalom személyre szabása a tartalommal és a felhasználóval kapcsolatos hasznos információktól függ. Ne feledje, hogy egyes alkalmazások és iparágak esetében egyes felhasználói funkciók közvetlenül vagy közvetve diszkriminatívnak és potenciálisan törvénytelennek tekintendők.

Vegye figyelembe a következő funkciók hatásait:

* **Felhasználói demográfiai** viszonyok: a szex, a nem, az Age, a Race, a vallás: a nem engedélyezett funkciók bizonyos alkalmazásokban szabályozási okokból nem használhatók, és a személyre szabása nem etikus, mert a személyre szabás az általánosításokat és torzulásokat propagálja. Ez a torzítási propagálás egy példa arra, hogy az idősek és a nemi alapú célközönségek nem jelennek meg a mérnöki munkákban.
* **Területi információ**: a világ számos helyén, a helyadatok (például irányítószámok, irányítószámok vagy környéki nevek) nagy összefüggésben lehetnek a jövedelemmel, a faji és a vallással.
* A **felhasználó megítélése a méltányosság szempontjából**: még abban az esetben is, ha az alkalmazás a helyes döntéseket veszi, vegye figyelembe, hogy a felhasználók az alkalmazásban megjelenített tartalmat úgy tekintik, hogy összefügg a megkülönböztető képességgel rendelkező funkciókkal.
* Nem **szándékolt torzítás a funkciókban**: léteznek olyan torzítások, amelyek csak a populáció egy részhalmazát érintik. Ez további figyelmet igényel, ha a szolgáltatások algorithmically jönnek létre, például ha a képelemzés használatával kinyeri az elemeket egy kép-vagy szöveges elemzésben az entitások szövegbeli felderítéséhez. Ismerje meg a funkciók létrehozásához használt szolgáltatások jellemzőit.

Alkalmazza az alábbi eljárásokat, ha olyan szolgáltatásokat választ, amelyeket a személyre szabáshoz szeretne küldeni a következő helyzetekben:

* Vegye figyelembe az egyes alkalmazások bizonyos funkcióinak használatának jogszerűségét és etikáját, valamint azt, hogy az ártatlan megjelenésű funkciók lehetnek-e proxyk mások számára, akiket vagy amelyeket el szeretne kerülni,
* Legyen transzparens a felhasználók számára, hogy az algoritmusokat és az adatelemzést használják a megjelenő beállítások személyre szabásához.
* Kérdezze meg magától: szeretném, hogy a felhasználók és boldogok legyenek, ha ezeket az információkat a tartalom személyre szabásához is felhasználtam? Szeretném jól megmutatni, hogy a döntés hogyan lett kiemelve vagy elrejteni bizonyos elemeket?
* A besorolási vagy szegmentálási adatok helyett használja a viselkedést más jellemzők alapján. A demográfiai adatokat hagyományosan a kiskereskedők használták a korábbi okok miatt: a demográfiai attribútumok egyszerűen összegyűjthetők, és a digitális korszak előtt lépnek érvénybe, de kérdés, hogy milyen demográfiai információkra van szükség, amikor a tényleges interakció, a kontextus és a múltbeli adatok a felhasználók preferenciáinak és identitásának alaposabban kapcsolódnak.
* Gondolja át, hogyan akadályozza meg, hogy a rosszindulatú felhasználók ne tudják meghamisítani a szolgáltatásait, amelyek nagy számú kihasználása esetén a személyre szabott módon megzavarhatja a felhasználókat, hogy a szándékos fennakadást, zavart és a felhasználók bizonyos csoportjait zaklatják.
* Ha szükséges és megvalósítható, tervezze meg az alkalmazását, hogy a felhasználók bizonyos személyes szolgáltatások használatát engedélyezzék vagy letiltsák. Ezek csoportosítva lehetnek, például a "helyadatok", az "eszköz adatai", a "korábbi vásárlási előzmények" stb.


## <a name="computing-rewards-for-personalizer"></a>A személyre szabott számítástechnikai előnyök

A személyre szabás célja, hogy az alkalmazás üzleti logikája által biztosított jutalmazási pontszám alapján javítsa ki a kiválasztását.

Egy jól összeépített jutalmazási pontszám rövid távú proxyként fog működni egy üzleti célra, amely egy szervezet feladatához van kötve.

A kattintásokra való jutalom például arra törekszik, hogy a személyre szabott szolgáltatás a minden más költségén rákeresve legyen, még akkor is, ha a rákattintott elem megzavarja vagy nem kapcsolódik az üzleti eredményekhez.

Kontrasztos példaként előfordulhat, hogy a hírek webhelye a kattintások jobban megfogalmazott jutalmakat kíván beállítani, például: "a felhasználó elég időt töltött a tartalom olvasásához?" "Rákattintanak a kapcsolódó cikkekre vagy hivatkozásokra?" A személyre szabással könnyen összekapcsolhatja a mérőszámokat a nyereményekhez. Azonban ügyeljen arra, hogy a rövid távú felhasználói szerepvállalás jó eredményekkel járjon.

### <a name="unintended-consequences-from-reward-scores"></a>A jutalmazási pontszámok nem szándékolt következményei
A jutalmazási pontszámok a legjobb szándékokkal hozhatók létre, de váratlan következményekkel járhatnak, illetve nem szándékolt eredményekkel is rendelkezhetnek a tartalom személyre szabásával kapcsolatban.

Tekintse meg a következő példákat:

* A videó tartalmának személyre szabása a megfigyelt videó hosszának százalékában valószínűleg rövidebb videókra fog támaszkodni.
* A közösségi média részvényeinek kihasználása, a saját megosztásának és a tartalomnak a kifogásának elemzése nélkül, a támadók, a nem moderált vagy a gyulladásos tartalmak rangsorolására vezethetnek, ami általában sok "szerepvállalást" eredményez, de kevés értéket tesz lehetővé.
* A felhasználói felület azon elemeinek megjutalmazása, amelyeket a felhasználók nem várnak a változásra, zavarhatja a felhasználói felület használhatóságát és kiszámíthatóságát, ahol a gombok meglepően változó helyre vagy célra, figyelmeztetés nélkül, így a felhasználók bizonyos csoportjai számára is megnehezítik a hatékony munkavégzést.

Az ajánlott eljárások megvalósítása:

* Különböző jutalmazási módszerekkel futtathatja az offline kísérleteket a rendszer használatával a hatás és a mellékhatások megismerése érdekében.
* Értékelje ki a jutalmazási funkcióit, és kérdezzen rá, hogy egy nagyon naiv személy meghajlítsa az értelmezését, és nemkívánatos eredményekhez jusson.


## <a name="responsible-design-considerations"></a>Felelős tervezési szempontok

A következőkben az AI-k felelős megvalósításának kialakítási területei láthatók. További információ [a keretrendszer jövőbeli](https://news.microsoft.com/futurecomputed/)vmivel.

![A jövőben kiszámított AI-értékek](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Elszámoltathatóság
*Az AI-rendszereket megtervező és telepítő személyeknek a rendszereik működéséhez kell tartoznia*.

* Hozzon létre belső útmutatást a személyre szabás, a dokumentum és a csoport, a vezetők és a szállítók kommunikációjának megvalósításához.
* Rendszeres felülvizsgálatok elvégzése a jutalmazási pontszámok kiszámításához, az offline értékelések elvégzése, amelyekkel megtekintheti, hogy milyen funkciók érintik a személyre szabott funkciókat, és az eredmények használatával kizárja a szükségtelen és szükségtelen funkciókat
* Egyértelműen kommunikálhat a felhasználókkal, hogy milyen célra használják a személyeket, milyen célokra és milyen adatforrásokra.
* Archiválja az adatokat és az eszközöket – például a modelleket, a tanulási szabályzatokat és más adatokat –, amelyeket a személyre szabott funkció használ, hogy képes legyen reprodukálni az eredményeket.

### <a name="transparency"></a>Átláthatóság
*Az AI-rendszereknek érthetőnek kell lenniük*. Személyre szabott:

* *Adja meg a felhasználóknak a tartalom személyre szabásával kapcsolatos információkat.* Megjelenítheti például a felhasználók számára egy olyan gombot, `Why These Suggestions?` amely megmutatja, hogy a felhasználó és a műveletek mely fő funkciói játszottak szerepkört a személyre szabás eredményében.
* Győződjön meg arról, hogy a használati feltételek megemlítik, hogy a felhasználói élmény személyre szabásával és viselkedésével kapcsolatos információkat fog használni.

### <a name="fairness"></a>Igazságosság
*A AI-rendszereknek minden személyt tisztességesen kell kezelnie*.

* Ne használja a személyre szabott használati eseteket, ha az eredmények hosszú távúak, a következmény vagy a valós károsodás.
* Ne használjon olyan szolgáltatásokat, amelyek nem megfelelőek a tartalom személyre szabásához, vagy amelyek segíthetnek a nemkívánatos torzulások propagálásában. Például a hasonló pénzügyi körülményekkel rendelkezők számára a pénzügyi termékekre vonatkozó személyre szabott ajánlásokat kell látnia.
* A szerkesztőktől, az algoritmikus eszközöktől vagy a felhasználóktól származó funkciókban esetlegesen előforduló torzulások ismertetése.

### <a name="reliability-and-safety"></a>Megbízhatóság és biztonság
*A AI-rendszereknek megbízhatóan és biztonságosan kell* működniük. Személyre szabás:

* *Ne adja meg azokat a személyre szabott műveleteket, amelyek nem választhatók ki*. A nem megfelelő filmeket például ki kell szűrni a személyre szabott műveletekből, ha névtelen vagy fiatalkorú felhasználó számára tesz javaslatot.
* *Személyre szabott modell kezelése üzleti eszközként*.  Gondolja át, hogy milyen gyakran szeretné menteni és biztonsági mentést készíteni a modellre és a tanulási szabályzatokat a személyre szabott hurok mögött, és más módon kezelje fontos üzleti eszközként. A múltbeli eredmények reprodukálása fontos az önellenőrzés és a fejlesztés szempontjából.
* *Csatornákat biztosít a felhasználók közvetlen visszajelzéséhez*. A biztonsági ellenőrzéseken kívül győződjön meg arról, hogy csak a megfelelő célközönségek jelennek meg a megfelelő tartalomban, olyan visszajelzési mechanizmust biztosítanak a felhasználóknak, amely meglepő vagy zavaró tartalmat jelenthet. Különösen, ha a tartalma felhasználóktól vagy harmadik féltől származik, érdemes lehet a Microsoft Content Moderator vagy további eszközöket használni a tartalmak áttekintéséhez és ellenőrzéséhez.
* *Gyakori offline értékelések végrehajtása*. Ez segít a trendek monitorozásában és a hatékonyság ismeretében.
* *Hozzon létre egy folyamatot a kártékony manipuláció észleléséhez és kezeléséhez*. A gépi tanulás és a mesterséges intelligenciát használó rendszerek képesek a környezetből tanulni, hogy az eredményeket a céljaik irányába váltsanak. Ha a személyre szabott személyt a fontos döntések befolyásolására szeretné használni, ügyeljen arra, hogy megfelelő módon azonosítsa és csökkentse a támadási osztályokat, beleértve a megfelelő körülmények között az emberi felülvizsgálatot is.

### <a name="security-and-privacy"></a>Biztonság és adatvédelem
*Az AI-rendszereknek biztonságosnak és tiszteletben kell tartaniuk az adatvédelmet*. Személyre szabás használata esetén:

* *Tájékoztassa a felhasználókat a gyűjtött adatokról, valamint arról, hogy hogyan használják fel őket, és előzetesen*, a helyi és iparági előírásokat követve kapják meg a hozzájárulásukat.
* *Adatvédelem biztosítása felhasználói vezérlők védelméhez.* A személyes adatokat tároló alkalmazások esetében érdemes lehet egy könnyen megkereshető gombot biztosítani a következő függvényekhez, mint például:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

Bizonyos esetekben ezek jogilag kötelezőek lehetnek. Tekintse át a modellek rendszeres átképzését, hogy ne tartalmazzák a törölt adat nyomait.

### <a name="inclusiveness"></a>Befogadás
*Az emberi igények és tapasztalatok széles körét kezeli*.
* *Személyre szabott élményt biztosíthat a kisegítő lehetőségekkel rendelkező interfészekhez.* A megfelelő megszemélyesítésből származó hatékonyság – az interakciók, a mozgás és a szükségtelen ismétlődések mértékének csökkentése érdekében – különösen előnyös lehet a fogyatékkal élők számára.
* *Az alkalmazás viselkedésének beállítása a kontextushoz*. A személyre szabhatja a csevegést a egyértelműsítse között, például ha a megfelelő értelmezés lehet környezetfüggő, és az egyik méret nem fér hozzá.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktív készültség a megnövekedett adatvédelem és irányítás érdekében

Nehéz előre megjósolni a szabályozási kontextusok bizonyos változásait, de általában bölcs dolog lenne a személyes adat tiszteletben tartásának biztosításához szükséges minimális jogi kereten felül, valamint az algoritmusos döntéshozatalhoz kapcsolódó átláthatóság és választási lehetőség biztosítása.


* Érdemes megfontolni egy olyan helyzet megtervezését, ahol a magánszemélyek által gyűjtött adatokra vonatkozó új korlátozások lehetnek, és meg kell mutatni a döntések elvégzésének módját.
* Vegye fontolóra a további készültséget, ahol a felhasználók a marginalizált sebezhető populációkat, a gyermekeket, a gazdasági sebezhetőséget okozó felhasználókat, illetve az algoritmusos manipulációk befolyásolására nem alkalmas felhasználókat is tartalmazhatnak.
* Vegye figyelembe, hogy a célközönség és a közönség által befolyásoló adatgyűjtési programok és algoritmusok milyen mértékben lettek kiváltva, és hogyan kerülheti el a bevált stratégiai hibákat.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktív értékelések a projekt életciklusa során

Érdemes lehet módszereket létrehozni a csapattagok, a felhasználók és az üzleti tulajdonosok számára a felelősségteljes használattal kapcsolatos aggályok jelentésére, valamint olyan folyamat létrehozására, amely rangsorolja a megoldást, és megakadályozza a megtorlást.

Bármely olyan személy, aki az egyes technológiák használatának mellékhatásait felhasználja, a perspektívájuk és a magánéletük is korlátozott. Bővítse ki a rendelkezésre álló vélemények körét azáltal, hogy több különböző hangokat helyez el a csapatok, a felhasználók vagy a tanácsadói testületek számára; Ilyenkor lehetséges, és javasoljuk, hogy beszéljenek. Tekintse át a képzési és oktatási segédanyagokat, hogy tovább bővítse a csapat ismereteit ebben a tartományban, valamint hogy képességet adjon az összetett és bizalmas témakörök megismeréséhez.

Vegye fontolóra a felelősségteljes használattal kapcsolatos feladatok kezelését az alkalmazás-életciklus más crosscutting, például a felhasználói élményhez, a biztonsághoz vagy a DevOps kapcsolódó feladatokhoz hasonlóan. Ezek a feladatok és a rájuk vonatkozó követelmények nem lehetnek utólag. A felelősségteljes használatot az alkalmazás életciklusa során kell megvitatni és ellenőrizni.

## <a name="questions-and-feedback"></a>Kérdések és visszajelzés

A Microsoft folyamatosan olyan eszközöket és dokumentumokat tesz elérhetővé, amelyek segítséget nyújtanak az ilyen feladatokhoz. A csapat meghívja Önt, hogy [visszajelzést küldjön a Microsoftnak](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)  , ha úgy gondolja, hogy további eszközök, szolgáltatások és dokumentumok segítenek a személyre szabáshoz szükséges irányelvek megvalósításában.

## <a name="recommended-reading"></a>Ajánlott olvasás

* Tekintse meg a Microsoft hat alapelvét a januári 2018-as könyvben közzétett AI-k felelős fejlesztéséhez, [a jövő kiszámításához](https://news.microsoft.com/futurecomputed/)
* [Ki a jövő tulajdonosa?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) Jaron Lanier.
* [A Math Destruction](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) by-Cathy O'Neil
* [Etika és adatelemzés](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) DJ Pati, Hilary Mason, Mike Loukides.
* [Az etikai kódexe](https://www.acm.org/code-of-ethics)
* [Genetikai információ – nem diszkriminációs szabály – GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [A FATML alapelvei a fiókhoz tartozó algoritmusokhoz](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Következő lépések

[Funkciók: művelet és környezet](concepts-features.md).
