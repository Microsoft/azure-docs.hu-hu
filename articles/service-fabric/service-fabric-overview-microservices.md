---
title: Az Azure-beli mikroszolgáltatások bemutatása
description: Annak áttekintése, hogy miért fontos a Felhőbeli alkalmazások fejlesztése a szolgáltatásokkal a modern alkalmazásfejlesztés szempontjából, és hogy az Azure Service Fabric milyen platformot biztosít ennek eléréséhez.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 46d4be7321057bd7a5ee19e0aca136c8c60d43bc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044430"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Miért érdemes alkalmazások létrehozására használni a Service-t

A szoftverfejlesztők számára az alkalmazások összetevő-részekre való kiszámításának nem újdonsága. A rendszer általában egy többrétegű megközelítést használ, egy háttérbeli tárolóval, egy közepes szintű üzleti logikával és egy előtér-felhasználói felülettel (UI). Az elmúlt néhány *évben módosult* , hogy a fejlesztők elosztott alkalmazásokat készítenek a felhőhöz.

Íme néhány változó üzleti igény:

* Egy nagy léptékű szolgáltatás, amellyel az ügyfelek az új földrajzi régiókban érhetők el.
* A funkciók és képességek gyorsabb megvalósítása, hogy az ügyfelek igényeire reagáljon agilis módon.
* Jobb erőforrás-használat a költségek csökkentése érdekében.

Ezek az üzleti igények hatással vannak az alkalmazások kiépítésének *módjára* .

További információ az Azure-beli szolgáltatásokkal kapcsolatos módszerekről: a Service-t [a felhő által működtetett alkalmazás-forradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)ismerteti.

## <a name="monolithic-vs-microservices-design-approach"></a>A monolit és a szolgáltatások kialakításának megközelítése

Az alkalmazások az idő múlásával fejlődnek. A sikeres alkalmazások fejlődése a felhasználók számára hasznos. A sikertelen alkalmazások nem fejlődnek, és végül elavultak. Itt a kérdés: mennyit ismer a követelményekkel kapcsolatban ma, és mi is a jövőben? Tegyük fel például, hogy létrehoz egy jelentéskészítési alkalmazást a vállalat egyik részlege számára. Biztos, hogy az alkalmazás csak a vállalat hatókörén belül érvényes, és a jelentések nem maradnak meg sokáig. Az Ön megközelítése különbözik a következőtől:, azaz olyan szolgáltatás létrehozása, amely több tízezer ügyfelünk számára biztosít videotartalom-tartalmat.

Időnként előfordulhat, hogy az ajtót úgy kezdi meg, hogy a koncepció bizonyítása a vezetési tényező. Tudja, hogy az alkalmazás később újratervezhető. Kevés a mérnöki tevékenység, ami soha nem lesz használatban. Másfelől, amikor a vállalatok a felhőre építenek, a várt érték a növekedés és a használat. A növekedés és a skálázás előre nem látható. Azt szeretnénk, hogy a prototípus gyors legyen, miközben azt is tudjuk, hogy olyan elérési úton vagyunk, amely képes a jövőbeli siker kezelésére. Ez a sovány indítási módszer: build, mérték, Learn és iteráció.

Az ügyfél/kiszolgáló korszakban úgy döntöttünk, hogy az egyes rétegekben meghatározott technológiák használatával a többrétegű alkalmazások kiépítésére koncentrálunk. A *monolitikus* alkalmazás kifejezése a következő megközelítések leírására lett kiváltva. A felületek általában a rétegek közé vannak felhasználva, és egy szorosabban összekapcsolt kialakítás lett használva az egyes rétegek összetevői között. A fejlesztők a könyvtárakba lefordított és a több végrehajtható fájlban és DLL-ben összekapcsolt osztályokba tartozó osztályokat hoztak létre.

A monolitikus tervezési megközelítés előnyei vannak. A monolit alkalmazások gyakran egyszerűbbek a tervezéshez, és az összetevők közötti hívások gyorsabbak, mert ezek a hívások gyakran többek között az adatfeldolgozási kommunikáción (IPC) alapulnak. Emellett mindenki tesztel egy terméket, ami általában hatékonyabban használja az emberi erőforrásokat. A hátránya az, hogy a réteges rétegek között szoros kapcsolat áll fenn, és az egyes összetevők nem méretezhetők. Ha javításokra vagy frissítésekre van szüksége, meg kell várnia, amíg mások befejezzük a tesztelést. Nehezebben lehet agilis.

A szolgáltatások a korábbi üzleti igényeknek megfelelően kezelik ezeket a hátrányait. Ugyanakkor előnyökkel és kötelezettségekkel is rendelkeznek. A Service-szolgáltatások előnye, hogy mindegyik általában egyszerűbb üzleti funkciókat ágyaz be, amelyek egymástól függetlenül méretezhetők, üzembe helyezhetők és kezelhetők. A szolgáltatások egyik fontos előnye, hogy a csapatok az üzleti forgatókönyvek szerint, mint a technológián alapulnak. A kisebb csapatok egy ügyfél-forgatókönyv alapján fejlesztenek egy szolgáltatást, és a használni kívánt technológiákat használják.

Más szóval a szervezetnek nem kell szabványosítani a technológiát a Service-alkalmazások karbantartásához. Az egyes csapatoknak, amelyeket a saját szolgáltatásai elvégezhetnek, a csapat szakértelmére vagy a probléma megoldására legmegfelelőbbek lehetnek. A gyakorlatban a javasolt technológiák, például egy adott NoSQL-tár vagy webalkalmazás-keretrendszer használata előnyösebb.

A szolgáltatások hátránya, hogy több különálló entitást kell kezelnie, és összetettebb központi telepítéseket és verziószámozást kell kezelni. A szolgáltatások közötti hálózati forgalom nő, akárcsak a hálózati késések. A nagy mennyiségű beszélgetés, a részletes szolgáltatások a teljesítményhez vezethetnek. A függőségek megtekintését segítő eszközök nélkül nehéz megtekinteni a teljes rendszerképet.

A szabványok a szolgáltatások megközelítését teszik elérhetővé azáltal, hogy a merev szerződések helyett csak azokat a dolgokat kell megadnia, amelyekre szüksége van a szolgáltatástól. Fontos, hogy ezeket a szerződéseket a kialakítás előtt adja meg, mivel a szolgáltatások egymástól függetlenül frissülnek. Egy másik, a Service-szolgáltatásokkal való tervezéshez megalkotott Leírás a "részletes szolgáltatásorientált architektúra (SOA)".

***Legegyszerűbben a szolgáltatásokra vonatkozó tervezési módszer a szolgáltatások leválasztott összevonására vonatkozik, amely az egyes és a kommunikációra vonatkozó elfogadott szabványok egymástól független módosításait tartalmazza.***

Ahogy egyre több felhőalapú alkalmazás jön létre, az emberek felfedezték, hogy a teljes alkalmazás független, forgatókönyvekre összpontosító szolgáltatásokra való kibontása jobb hosszú távú megközelítés.

## <a name="comparison-between-application-development-approaches"></a>Az alkalmazások fejlesztési módszereinek összehasonlítása

![Platform-alkalmazásfejlesztés Service Fabric][Image1]

1) A monolit alkalmazások tartományszintű funkciókat tartalmaznak, és általában funkcionális rétegekbe vannak osztva, például web, Business és adat.

2) A monolit alkalmazások méretezése több kiszolgálón/virtuális gépen/tárolón keresztüli klónozással végezhető el.

3) A Service-alkalmazások külön kisebb szolgáltatásokban különítik el a funkciókat.

4) A Service-szolgáltatások megközelítése az egyes szolgáltatások egymástól független üzembe helyezésével történik, így a szolgáltatások példányai a kiszolgálók/virtuális gépek/tárolók között hozhatók létre.

A szolgáltatással történő tervezés nem megfelelő az összes projekthez, de a korábban ismertetett üzleti célokkal szorosabban igazodik. A monolit megközelítéstől kezdve érdemes lehet, ha tudja, hogy a kódot később újra feldolgozza a Services-tervbe. Gyakrabban érdemes egy monolitikus alkalmazást kezdeni, és lassan kitörni a fázisokban, kezdve azokkal a funkcionális területekkel, amelyeknek méretezhetőnek vagy agilisnak kell lenniük.

Ha a szolgáltatással kapcsolatos módszert használ, számos kis szolgáltatás alkalmazását kell megalkotnia. Ezek a szolgáltatások olyan tárolókban futnak, amelyek számítógépeken üzemelő fürtökön vannak telepítve. A kisebb csapatok olyan szolgáltatást fejlesztenek, amely egy forgatókönyvre összpontosít, és az egyes szolgáltatások egymástól független tesztelését, verzióját, üzembe helyezését és méretezését végzik, hogy a teljes alkalmazás fejlődjön.

## <a name="what-is-a-microservice"></a>Mik azok a mikroszolgáltatások?

A szolgáltatások különböző definíciói vannak. A legtöbb ilyen jellemzőt azonban széles körben elfogadják:

* Egy ügyfél vagy üzleti forgatókönyv beágyazása. Milyen problémák megoldására?
* Egy kisméretű mérnöki csapat fejlesztette ki.
* Bármilyen programozási nyelven írt, bármilyen keretrendszer használatával.
* Kód és opcionális állapot, mindkettő egymástól függetlenül, külön verziója, üzembe helyezése és méretezése.
* Más szolgáltatásokkal való interakció jól meghatározott felületek és protokollok alapján.
* A helyük feloldásához használt egyedi nevek (URL-címek).
* Konzisztensek maradnak, és a hibák jelenlétében is elérhetők.

Összegzés:

***A Service-alkalmazások kis, egymástól független verzióval és skálázható, ügyfél-központú szolgáltatásokból állnak, amelyek egymással kommunikálnak a szabványos protokollokkal, jól definiált felületekkel.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Bármilyen programozási nyelven írt, bármilyen keretrendszer használatával

A fejlesztőknek szabadon választhatnak egy nyelvet vagy keretrendszert, a képességeink és a létrehozandó szolgáltatás igényeitől függően. Egyes szolgáltatások esetében előfordulhat, hogy a C++ teljesítménybeli előnyeit a többinél többre is kiértékeli. Mások számára a C#-ból vagy a Java-ból származó egyszerű felügyelt fejlesztés fontosabb lehet. Bizonyos esetekben előfordulhat, hogy egy adott partner-függvénytárat, adattárolási technológiát vagy módszert kell használnia a szolgáltatás ügyfeleknek való kilépéséhez.

A technológia kiválasztása után figyelembe kell vennie a szolgáltatás működésének vagy életciklusának kezelését és skálázását.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>A kód és az állapot egymástól függetlenül telepíthető, telepíthető és méretezhető

Függetlenül attól, hogy hogyan írja le a szolgáltatásait, a kódot és opcionálisan az állapotot, függetlenül kell telepítenie, frissítenie és méreteznie. Ez a probléma nehezen oldható meg, mert az Ön által választott technológiákhoz tartozik. A skálázáshoz a kód és az állapot felosztása (vagy szilánkja) is kihívást jelent. Ha a kód és az állapot különböző technológiákat használ, ami manapság gyakran előfordul, akkor a Service üzembe helyezési parancsfájljainak mindkettőt meg kell tudniuk méretezni. Ez az elkülönítés a rugalmassággal és a rugalmassággal is kapcsolatban áll, így az összes szolgáltatás frissítését anélkül is frissítheti, hogy mindegyiket egyszerre kellene frissítenie.

Térjen vissza a monolitikus és a Services-szolgáltatások összehasonlítására egy pillanatra. Ez az ábra az állapot tárolásának módszereinek különbségeit mutatja be:

#### <a name="state-storage-for-the-two-approaches"></a>A két megközelítés állapotának tárolása

![Service Fabric platform állapotának tárolása][Image2]

***A bal oldalon a monolitikus megközelítés egyetlen adatbázissal és meghatározott technológiákkal rendelkezik.***

***A jobb oldalon a szolgáltatásokhoz kapcsolódó szolgáltatásokra vonatkozó grafikon látható, ahol az állapot jellemzően a szolgáltatásra terjed ki, és a különböző technológiákat használják.***

A monolitikus megközelítésben az alkalmazás általában egyetlen adatbázist használ. Az egyetlen adatbázis használatának előnye, hogy egyetlen helyen van, így könnyen üzembe helyezhető. Minden összetevő rendelkezhet egyetlen táblával az állapotának tárolásához. A csapatoknak szigorúan külön kell elkülöníteniük az állapotot, ami kihívást jelent. Elkerülhetetlen, hogy valaki felvesz egy oszlopot egy meglévő ügyfél-táblába, csatlakozhat a táblák között, és függőségeket hoz létre a tárolási rétegben. Ezt követően nem méretezheti az egyes összetevőket.

A szolgáltatásokban az egyes szolgáltatások a saját állapotát kezelik és tárolják. Minden szolgáltatás felelős a kód és az állapot skálázásával, hogy megfeleljen a szolgáltatás követelményeinek. A hátránya, hogy ha nézeteket vagy lekérdezéseket kell létrehoznia az alkalmazás adataihoz, több állapotú tárolóban kell lekérdezni. Ezt a problémát általában egy különálló szolgáltatás oldja fel, amely egy, a-gyűjteményen belül létrehozott nézetet hoz létre. Ha több rögtönzött-lekérdezést kell futtatnia az adatkapcsolaton, érdemes megfontolnia, hogy az egyes Service-szolgáltatások adattárház-szolgáltatásait az offline Analitika számára is megírja.

A szervizcsomagok verziója. Lehetséges, hogy a Service különböző verziói egymás mellett futnak. A webszolgáltatás újabb verziója meghiúsulhat a frissítés során, és vissza kell térnie egy korábbi verzióra. A verziószámozás az A/B teszteléshez is hasznos, ahol a különböző felhasználók a szolgáltatás különböző verzióit tapasztalják. Például gyakori, hogy egy adott ügyfél-készletre frissít egy szolgáltatást az új funkciók kipróbálása előtt, még szélesebb körben.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Más szolgáltatásokkal való interakció jól definiált illesztőfelületeken és protokollokon keresztül

Az elmúlt 10 évben részletes információk jelentek meg a szolgáltatás-orientált architektúrák kommunikációs mintáinak leírásában. A szolgáltatás-kommunikáció általánosságban a HTTP-és TCP-protokollok, valamint az XML vagy a JSON használatával történő REST-megközelítést használ a szerializálási formátumként. Egy kezelőfelületi perspektívából a webes kialakítás megközelítése zajlik. A bináris protokollok vagy a saját adatformátumok használata azonban nem áll le. Ha ezek a protokollok és formátumok nem érhetők el, ne feledje, hogy az emberek nehezebb időt fognak használni a szolgáltatásokkal.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>A hely feloldásához használt egyedi név (URL)

A szolgáltatásnak bárhol kell lennie, ahol fut. Ha olyan gépeket használ, amelyek egy adott szolgáltatást futtatnak, akkor a dolgok gyorsan eltérhetnek.

Ugyanúgy, ahogy a DNS feloldja egy adott gép egy adott URL-címét, a webszolgáltatás egyedi nevet igényel, hogy a jelenlegi helye felderíthető legyen. A-szolgáltatásokhoz olyan címezhető nevekre van szükség, amelyek függetlenek a-on futó infrastruktúrától. Ez azt jelenti, hogy a szolgáltatás üzembe helyezése és felderítése között interakció van, mivel a szolgáltatásnak beállításjegyzékre van szüksége. Ha egy gép meghibásodik, a beállításjegyzék szolgáltatásnak meg kell állapítania, hogy a szolgáltatás hová lett helyezve.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Konzisztens és elérhető marad a hibák jelenlétében

A váratlan hibák kezelése az egyik legnehezebb megoldás, különösen az elosztott rendszerekben. A fejlesztők által írt kód nagy része a kivételek kezelésére szolgál. A tesztelés során a legtöbb időt a kivételek kezelésére is költjük. A folyamat nagyobb szerepet játszik a hibák kezelésére szolgáló kód írásakor. Mi történik, ha a gép, amelyen a szolgáltatás fut, nem sikerül? Meg kell állapítania a hibát, ami a saját problémája. Azonban újra kell indítania a szolgáltatást.

A rendelkezésre állás érdekében a Service-nek rugalmasnak kell lennie a hibákhoz, és képesnek kell lennie egy másik gépen való újraindításra. Ezen rugalmassági követelmények mellett az adatoknak nem szabad elveszni, és az adatoknak konzisztensnek kell maradniuk.

A rugalmasság nehezen érhető el, ha a hibák egy adott alkalmazás frissítésekor történnek. Az üzembe helyezési rendszerrel nem kell helyreállítani a szolgáltatást. Meg kell határoznia, hogy továbbra is folytathatja-e az újabb verzióra való áttérést, vagy visszaállíthatja egy korábbi verzióra a konzisztens állapot fenntartása érdekében. Néhány kérdést is figyelembe kell vennie, például, hogy rendelkezésre áll-e elegendő gép a folyamatos átvitelhez, és hogyan állíthatja helyre a szervizcsomag korábbi verzióit. A döntések elvégzéséhez szüksége lesz a Service-nek az állapotadatok kibocsátására.

### <a name="reports-health-and-diagnostics"></a>Jelentések állapota és diagnosztika

Úgy tűnhet, hogy nyilvánvaló, és gyakran megtekinthető, de a szolgáltatásnak jelentenie kell az állapotát és a diagnosztikát. Ellenkező esetben a működési perspektívából kevés az állapota. A diagnosztikai események összekapcsolhatók egymástól független szolgáltatásokban, és az események sorrendjének meghatározásakor a gépi óra eltérései kihívást jelentenek. Ugyanúgy, ahogyan a szolgáltatást a megállapodott protokollokon és az adatformátumokon keresztül kezeli, egységesíteni kell, hogyan kell naplózni az állapot-és diagnosztikai eseményeket, amelyek végül egy Event Store-ban fognak működni a lekérdezések és a megtekintés céljából. A szolgáltatási módszerekkel a különböző csapatoknak egyetlen naplózási formátumban kell megegyezniük. A diagnosztikai események teljes egészében történő megtekintésének egységesnek kell lennie az alkalmazásban.

Az állapot különbözik a diagnosztikatól. Az állapot arról szól, hogy a webszolgáltatás jelentést készít a jelenlegi állapotáról a megfelelő műveletek elvégzéséhez. Jó példa arra, hogy a rendelkezésre állás fenntartása érdekében a frissítési és üzembe helyezési mechanizmusok működnek. Bár előfordulhat, hogy egy szolgáltatás állapota összeomlás vagy a gép újraindítása miatt nem kifogástalan állapotú, a szolgáltatás továbbra is működőképes lehet. Az utolsó szükséges, hogy a helyzet még rosszabb legyen a frissítés elindításával. A legjobb megoldás az, ha először vizsgálja meg a szolgáltatás helyreállításának idejét. A szolgáltatással kapcsolatos egészségügyi események segítenek a tájékozott döntések meghozatalában, és ennek hatására segítünk önjavító szolgáltatásokat létrehozni.

## <a name="guidance-for-designing-microservices-on-azure"></a>Útmutató az Azure-beli szolgáltatások tervezéséhez

Tekintse meg az Azure Architecture centert, amely útmutatást nyújt az [Azure-beli szolgáltatások tervezéséhez és létrehozásához](/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric Service-platformként

Az Azure Service Fabric akkor alakult ki, amikor a Microsoft áttért a bekeretezett termékek, amelyek jellemzően monolitikus voltak a szolgáltatások nyújtására. Nagy méretű szolgáltatások (például Azure SQL Database és Azure Cosmos DB, formázott Service Fabric kialakításának és üzemeltetésének élménye. A platform az idő múlásával fejlődött, ahogy egyre több szolgáltatás fogadta el. Service Fabric nem csak az Azure-ban, hanem az önálló Windows Server-példányokban is futtatni kellett.

***A Service Fabric célja, hogy megoldja a szolgáltatások kiépítésével és futtatásával, valamint az infrastruktúra-erőforrások hatékony kihasználásával kapcsolatos problémákat, így a csapatok a szolgáltatásokkal kapcsolatos üzleti problémák megoldására használhatók.***

Ez a rövid videó bemutatja a Service Fabric platformot és a mikroszolgáltatásokat: 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

A Service Fabric segítségével a következőkkel hozhat létre olyan alkalmazásokat, amelyek a szolgáltatással kapcsolatos megközelítést használnak:

* Platform, amely rendszerszolgáltatásokat biztosít a sikertelen szolgáltatások üzembe helyezésére, frissítésére, észlelésére és újraindítására, a szolgáltatások felderítésére, az üzenetek továbbítására, az állapot kezelésére és az állapot figyelésére.
* A tárolókban vagy folyamatként futó alkalmazások központi telepítését is lehetővé teszi. Service Fabric tároló-és folyamat-Orchestrator.
* Produktív programozási API-k, amelyek segítséget nyújtanak az alkalmazások [ASP.net Core, Reliable Actors és Reliable Services](service-fabric-choose-framework.md)létrehozásához. Például beszerezhet állapot-és diagnosztikai adatokat, vagy kihasználhatja a beépített magas rendelkezésre állást is.

***Service Fabric a szolgáltatás felépítésének módjáról van szó, és bármilyen technológia használható. Azonban olyan beépített programozási API-kat biztosít, amelyek megkönnyítik a szolgáltatások kiépítését.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Meglévő alkalmazások áttelepítése Service Fabricre

Service Fabric lehetővé teszi a meglévő kódok újbóli felhasználását és korszerűsítését új Service-szolgáltatásokkal. Az alkalmazások korszerűsítésének öt szakasza van, és bármelyik szakaszban elindítható és leállítható. A szakaszok a következők:

1) Kezdje egy hagyományos monolit alkalmazással.  
2) Áttelepítése. Tárolók vagy vendég végrehajtható fájlok használata meglévő kód Service Fabric-ben való üzemeltetéséhez.  
3) Korszerűsítésére. Új szolgáltatások hozzáadása a meglévő tároló kód mellett.  
4) Innováció. A monolit alkalmazást igény szerint a Service-be kell bontani.  
5) Alkalmazások átalakítása a szolgáltatásba. Alakítsa át a meglévő monolitikus alkalmazásokat, vagy hozzon létre új zöldmezős alkalmazásokat.

![Migrálás a Services szolgáltatásba][Image3]

Ne feledje, hogy *bármelyik szakaszban elindíthatja és leállíthatja*. A következő szakasznak nem kell haladnia. 

Nézzük meg az egyes szakaszokra vonatkozó példákat.

**Migrate**  
Két okból kifolyólag számos vállalat telepít át meglévő monolitikus alkalmazásokat tárolóba:

* Költségcsökkentés a meglévő hardverek összevonása és eltávolítása, illetve a nagyobb sűrűségű alkalmazások futtatása miatt.
* Konzisztens központi telepítési szerződés A fejlesztés és a műveletek között.

A költségcsökkentés egyszerű. A Microsoftnál számos meglévő alkalmazás van tárolóban, ami több millió dolláros megtakarítást eredményez. Az egységes üzembe helyezés nehezebben kiértékelhető, de ugyanilyen fontos. Ez azt jelenti, hogy a fejlesztők választhatják ki az igényeinek megfelelő technológiákat, de a műveletek csak egyetlen módszert fogadnak el az alkalmazások üzembe helyezéséhez és kezeléséhez. Ez csökkenti a különböző technológiák támogatásának összetettségét, és nem kell kiválasztania a fejlesztőket, hogy csak bizonyos beállításokat válasszon ki. Lényegében minden alkalmazás a saját környezetbe foglalt telepítési lemezképbe van beosztva.

Számos szervezet leáll. Már rendelkeznek a tárolók előnyeivel, és a Service Fabric biztosítja a teljes körű felügyeleti élményt, beleértve az üzembe helyezést, a verziófrissítést, a verziószámozást, a visszaállítást és az állapot figyelését.

**Korszerűsítésére**  
A modernizáció az új szolgáltatások hozzáadása a meglévő tároló kód mellett. Ha új kódot fog írni, a legjobb megoldás, ha a szolgáltatás elérési útját kis méretekben hajtja végre. Ez egy új REST API-végpont vagy új üzleti logika hozzáadását jelentheti. Így elindíthatja az új mikroprocesszorok létrehozásának folyamatát, és megkezdheti az alkalmazások fejlesztését és üzembe helyezését.

**Újítás**  
A Service-szolgáltatások megközelítése alkalmazkodik az üzleti igényekhez. Ebben a szakaszban el kell döntenie, hogy megkezdi-e a monolit alkalmazás felosztását a szolgáltatásokban vagy az innovációban. Egy klasszikus példa itt az, amikor egy munkafolyamat-várólistaként használt adatbázis feldolgozási szűk keresztmetszetet vált ki. Ahogy a munkafolyamat-kérelmek száma nő, a munkavégzést a méretezéshez kell osztani. Vegye figyelembe, hogy az alkalmazás nem méretezhető, vagy amelyet gyakrabban kell frissíteni, és fel kell osztania a szolgáltatást és az innovációt.

**Alkalmazások átalakítása a Service-be**  
Ebben a szakaszban az alkalmazás teljes mértékben a (vagy a felosztott) szolgáltatásokból áll. Ezen pont eléréséhez a Services-útvonalakat hozta létre. Itt megkezdheti a használatát, de erre a szolgáltatásra nincs szükség a nagy mennyiségű befektetés biztosításához.

### <a name="are-microservices-right-for-my-application"></a>Megfelelőek-e a szolgáltatások az alkalmazáshoz?

Talán. A Microsoftnál, ahogy egyre több csapat kezdte meg a felhőbe való kiépítést, számos felismerte az előnyeit, hogy milyen előnyökkel jár a szolgáltatáshoz hasonló módszer. A Bing például évek óta használ a Services szolgáltatást. Más csapatok esetében a szolgáltatások megközelítése új volt. A csapatok úgy találták, hogy nehéz problémák léptek fel az erősségük alapvető területein kívül. Ezért Service Fabric a szolgáltatások kiépítési technológiájának köszönhetően a vontatást.

Service Fabric célja, hogy csökkentse a Service-alkalmazások kiépítésének bonyolultságát, így nem kell a lehető legtöbb költséges újratervezéssel eljárnia. Kis méretű, méretezhető, ha szükséges, elavult szolgáltatásokat, újakat adhat hozzá, és fejlődik az ügyfelek használatában. Azt is tudjuk, hogy még sok más problémát kell megoldani, hogy a legtöbb fejlesztő számára jobban elérhetővé tegye a szolgáltatást. A tárolók és a színészek programozási modellje példákat mutat be ennek az iránynak a kis lépéseire. Biztosak vagyunk abban, hogy az innovációk könnyebben megkönnyítik a szolgáltatások kialakítását.

## <a name="next-steps"></a>Következő lépések

* [Szolgáltatások: a felhő által működtetett alkalmazás-forradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: szolgáltatások készítése az Azure-ban](/azure/architecture/microservices/)
* [Azure Service Fabric-alkalmazás és -fürt – ajánlott eljárások](./service-fabric-best-practices-security.md)
* [Service Fabric terminológia áttekintése](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png