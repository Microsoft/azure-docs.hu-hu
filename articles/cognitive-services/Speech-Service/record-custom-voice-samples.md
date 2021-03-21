---
title: Egyéni hangminták rögzítése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Egy robusztus szkript előkészítésével, a jó hangtehetségek bérbeadásával és a professzionális felvételsel kiváló minőségű egyéni hang készíthető.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 6d2ad87f6a8c79f61d62140a1549968cd88d83aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722313"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Hangminták rögzítése egyéni hang létrehozásához

A kiváló minőségű éles környezetből származó egyéni hang létrehozása nem alkalmi vállalkozás. Az egyéni hang központi összetevője az emberi beszéd hangmintáinak nagy gyűjteménye. Fontos, hogy ezek a hangfelvételek kiváló minőségűek legyenek. Válasszon ki egy olyan hangtehetséget, aki tapasztalattal rendelkezik az ilyen típusú felvételekhez, és egy hozzáértő hangvételi szakember rögzíti őket.

A felvételek elvégzéséhez azonban szükség van egy parancsfájlra: a hangfelvételek által a hangmintáknak kimondott szavak. A legjobb eredmények elérése érdekében a szkriptnek jó fonetikusan kell rendelkeznie, és elegendő különbözőnek kell lennie az egyéni hangmodell betanításához.

Sok kicsi, de fontos részlet a professzionális hangfelvételek létrehozásában. Ez az útmutató egy olyan folyamat ütemterve, amely segítséget nyújt a jó és konzisztens eredmények megszerzésében.

> [!NOTE]
> Ha szeretné betanítani a neurális hangvételt, meg kell adnia egy hangtehetségi profilt a hangfelvételt tartalmazó hangfelvételi fájllal, amely a beszédfelismerési adatok használatával betanítja az egyéni hangmodellt. A rögzítési parancsfájl előkészítésekor ügyeljen rá, hogy tartalmazza az alábbi mondatot. 

> "I [állapot az első és az utolsó név] tisztában vagyok azzal, hogy a hangfelvételeket [a vállalat neve] fogja használni a hang szintetikus verziójának létrehozásához és használatához."
Ezt a mondatot fogjuk használni annak ellenőrzéséhez, hogy ugyanaz a személy végzi-e a betanítási adatgyűjtést, aki a beleegyezik. További információ a [hangalapú tehetségek ellenőrzéséről](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) itt olvashat.

> Az egyéni neurális hang korlátozott hozzáféréssel érhető el. Győződjön meg róla, hogy tisztában van a [felelős AI-követelményekkel](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) , és [alkalmazza a hozzáférést itt](https://aka.ms/customneural). 

> [!TIP]
> A legjobb minőség érdekében érdemes megfontolni a Microsoftot abban, hogy fejlessze saját hangját. A Microsoft széles körű tapasztalattal rendelkezik a saját termékeire, például a Cortana és az Office-ra való kiváló minőségű hangok készítésére.

## <a name="voice-recording-roles"></a>Hangrögzítési szerepkörök

Az egyéni hangrögzítési projektben négy alapvető szerepkör található:

Szerepkör|Cél
-|-
Hangalapú tehetség        |Ez a személy hangja az egyéni hang alapját képezi majd.
Felvételi mérnök  |Felügyeli a rögzítés technikai szempontjait, és működteti a menetíró készüléket.
Igazgató            |Előkészíti a szkriptet, és elkészíti a hang tehetségének teljesítményét.
Szerkesztő              |Véglegesíti a hangfájlokat, és előkészíti azokat az egyéni hangportálra való feltöltéshez.

Egy személy több szerepkört is kitölthet. Ez az útmutató azt feltételezi, hogy elsődlegesen az igazgatói szerepkört fogja kitölteni, és a hangvételi és a felvételi mérnök is felveszi őket. Ha saját maga szeretné elvégezni a felvételeket, ez a cikk néhány információt tartalmaz a felvételi mérnök szerepkörről. A szerkesztői szerepkörre nincs szükség a munkamenet befejezése után, így az igazgató vagy a rögzítési mérnök is elvégezheti.

## <a name="choose-your-voice-talent"></a>Válassza ki a hangtehetségét

A kommentáros vagy hangkarakteres tapasztalatokkal rendelkező szereplők jó egyéni hangtehetséget tesznek elérhetővé. A kiállítók és a hírolvasók között gyakran is talál megfelelő tehetséget.

Válassza ki azokat a hangtehetségeket, amelyek természetes hangja tetszik. Lehetőség van egyedi "karakter" hangokat létrehozni, de sokkal nehezebb a legtöbb tehetséget következetesen végrehajtani, és az erőfeszítés hangsúlyt eredményezhet.

> [!TIP]
> Általában ne használjon felismerhető hangokat egyéni hang létrehozásához – kivéve, ha a cél az, hogy egy híresség beszédet hozzon létre. A kevésbé ismert hangok általában kevésbé zavaróak a felhasználók számára.

A hangtehetségek kiválasztásának egyetlen legfontosabb tényezője a konzisztencia. A felvételek mindegyike úgy hangzik, mintha ugyanazon a napon, ugyanabban a nap lett volna. Ez ideális megoldás a megfelelő rögzítési eljárások és mérnöki megoldások révén.

A hangtehetség az egyenlet másik fele. Képesnek kell lenniük az állandó, a hangerő, a szurok és a tónus megszólalása érdekében. A tiltás törlése kötelező. A tehetségnek emellett képesnek kell lennie szigorúan szabályozni a szurok változatát, az érzelmi befolyásolást és a beszéd stílust.

Az egyéni hangminták rögzítése több fárasztó is lehet, mint más típusú hangminták. A legtöbb hangtehetség naponta kettő vagy három órát is képes rögzíteni. A munkamenetek legfeljebb három vagy négy hétig állíthatók be, és ha lehetséges, az egyik napról a másikra.

A hangtehetségek használatával olyan "Personát" fejleszthet, amely az egyéni hang teljes hangját és érzelmi tónusát határozza meg. A folyamat során meg kell határoznia, hogy milyen "semleges" hangzik a Persona számára. Az egyéni neurális hangképességgel olyan modellt is betaníthat, amely az érzelmekkel beszél. Adja meg a "beszélő stílusokat", és kérje meg a hangvételt, hogy olvassa el a szkriptet úgy, hogy rezonál a kívánt stílusokat.  

Előfordulhat például, hogy egy Persona egy természetesen optimista személyiséggel rendelkezik. Tehát a "saját" hangon is szerepelhetnek az optimizmussal kapcsolatos megjegyzések, még akkor is, ha semleges módon beszélnek. Egy ilyen személyiségi tulajdonság azonban finom és konzisztens lehet. Hallgassa meg a meglévő hangok olvasásait, hogy megtudja, mi a célja.

> [!TIP]
> A hangfelvételeket általában saját maga szeretné elkészíteni. A hangtehetségnek a projekthez tartozó, munkahelyi használatra vonatkozó szerződéssel kell rendelkeznie.

## <a name="create-a-script"></a>Parancsfájl létrehozása

Az egyéni hangrögzítési munkamenet kiindulási pontja a szkript, amely tartalmazza a hangtehetségek által hosszúságú kimondott szöveg kívánt szöveget. (A "hosszúságú kimondott szöveg" kifejezés magában foglalja a teljes mondatokat és a rövidebb kifejezéseket is.)

A szkriptben található hosszúságú kimondott szöveg bárhonnan elérhető: fikció, nem fikció, beszédek átirata, híradás és bármi más nyomtatott formában. Ha biztos szeretne lenni abban, hogy a hangja jól működik-e az adott típusú szavak (például az orvosi terminológia vagy a programozási zsargon) esetében, érdemes lehet olyan mondatokat is bevonni a tudományos dokumentumokból vagy a technikai dokumentumokból. A lehetséges jogi problémák rövid megvitatására tekintse meg a ["törvényességek"](#legalities) című szakaszt. Írhat saját szöveget is.

A hosszúságú kimondott szöveg nem kell ugyanabból a forrásból vagy azonos forrásból származnia. Még semmit sem kell tenniük egymással. Ha azonban beállított kifejezéseket fog használni (például "sikeresen bejelentkezett") a beszédfelismerési alkalmazásban, ügyeljen rá, hogy a szkriptben szerepeljenek bennük. Így az egyéni hang nagyobb eséllyel kimondhatja ezeket a kifejezéseket. Ha azonban úgy dönt, hogy a szintetizált beszéd helyett egy felvételt használ, akkor már ugyanazt a hangot fogja látni.

Habár a hangtehetségek kiválasztásának kulcsa a konzisztencia, a Variety egy jó parancsfájl fémjelzi. A szkriptnek számos különböző szót és mondatot kell tartalmaznia, különböző mondatokkal, struktúrákkal és hangulatokkal. A nyelv minden hangjának többször és számos kontextusban kell szerepelnie ( *fonetikus lefedettségnek* nevezik).

Emellett a szövegnek tartalmaznia kell egy adott hang írásos megjelenítésének összes módját, és az egyes hangokat a mondatokban különböző helyeken helyezheti el. A deklaratív mondatokat és kérdéseket is bele kell foglalni, és a megfelelő hanglejtéssel kell olvasni.

Nehéz megírni egy olyan parancsfájlt, amely *elegendő* adatmennyiséget biztosít ahhoz, hogy a Custom Speech-portál jó hangvételt hozzon létre. A gyakorlatban a megbízható fonetikus lefedettséget biztosító szkriptek legegyszerűbb módja, ha nagy mennyiségű mintát tartalmaz. A Microsoft által biztosított standard hangok több tízezer hosszúságú kimondott szöveg készültek. Fel kell készülnie arra, hogy legalább néhány ezer hosszúságú kimondott szöveg feljegyezzen, hogy minőségi egyéni hangvételt hozzon létre.

A parancsfájlt alaposan tekintse meg a hibákért. Ha lehetséges, azt is ellenőrizze, hogy valaki más-e. Ha a szkripten keresztül futtatja a tehetségét, valószínűleg néhány hibát fog elsajátítani.

### <a name="script-format"></a>Parancsfájl formátuma

A parancsfájlt a Microsoft Wordben is megírhatja. A szkript a rögzítési munkamenet során használható, így bármilyen módon megadhatja, hogy könnyen működjön. Hozza létre az egyéni hangportál számára szükséges szövegfájlt külön.

Az alapszintű parancsfájlok formátuma három oszlopot tartalmaz:

* A Kimondás száma, 1-től kezdődően. A számozás megkönnyíti a Studióban mindenki számára, hogy egy adott részletre hivatkozzon ("próbálkozzon újra a 356 számmal"). A Word bekezdés számozási funkciójával a tábla sorait automatikusan számba állíthatja.
* Egy üres oszlop, amelyben minden egyes kiíráshoz szükséges számot vagy időpontot kell írni, hogy segítsen a kész rögzítésben.
* A Kimondás szövege.

![Példaszkript](media/custom-voice/script.png)

> [!NOTE]
> A legtöbb stúdió-rekord az úgynevezett rövid szegmensekben *szerepel.* A Take általában 10 – 24 hosszúságú kimondott szöveg tartalmaz. A Take number (belevételi szám) értékének megkeresése elegendő ahhoz, hogy később is megkereshető legyen. Ha olyan Studióban végez felvételt, amely inkább több felvételt szeretne készíteni, érdemes megjegyezni az időkódot. A Studióban látható idő jelenik meg.

Az egyes sorok után hagyjon elég helyet a megjegyzések írásához. Ügyeljen arra, hogy ne Ossza szét a lapok között a teljes kiosztást. Adja meg a lapok számát, és nyomtassa ki a szkriptet a papír egyik oldalára.

Nyomtassa ki a szkript három példányát: egyet a tehetséghez, egyet a mérnökhöz, egyet pedig az igazgatóhoz (Ön). A tűzés helyett a gemkapocs használata: egy tapasztalt hangművész elkülöníti a lapokat, hogy elkerülje az oldalak bekapcsolását.

### <a name="legalities"></a>Legalities

A szerzői jogi törvények szerint a szerzői jog által védett szöveg elolvasása olyan teljesítmény lehet, amelynek a feladatának elvégzését kompenzálni kell. Ez a teljesítmény nem ismerhető fel a végtermék, az egyéni hang. Ennek ellenére az erre a célra szolgáló szerzői jog használatának jogszerűsége nem jól bevált. A Microsoft nem tud jogi tanácsot adni a probléma megoldásához; forduljon a saját Tanácsához.

Szerencsére lehetséges elkerülni ezeket a problémákat. A szöveg számos forrást használhat engedély vagy licenc nélkül.

|Szöveges forrás|Description|
|-|-|
|[CMU Arctic Corpus](http://festvox.org/cmu_arctic/)|Az out-of-Copyright által kiválasztott 1100 mondatok kifejezetten a beszédfelismerési projektekben használhatók. Kiváló kiindulási pont.|
|Már nem működik<br>szerzői jog|A általában a 1923-nél korábbi verziókban van közzétéve. Angol nyelven a [Project Gutenberg](https://www.gutenberg.org/) több tízezer ilyen munkát kínál. Érdemes lehet az újabb verzióra összpontosítani, mivel a nyelv közelebb lesz a modern angolhoz.|
|A kormányzat &nbsp; működése|A Egyesült Államok kormányzat által létrehozott alkotások nem tartoznak a Egyesült Államokra, bár a kormány más országokban/régiókban is igényelhet szerzői jogokat.|
|Nyilvános tartomány|Azok a munkálatok, amelyekhez kifejezetten elutasításra került a szerzői jog, vagy amelyeket a nyilvános tartományhoz rendeltek. Előfordulhat, hogy a szerzői jogok teljes körű lemondása bizonyos jogszabályokban nem lehetséges.|
|Permissively – licencelt működés|Az olyan licencek keretében terjesztett munkák, mint a Creative Commons vagy a GNU Free dokumentációs licence (GFDL). A wikipedia a GFDL használja. Bizonyos licencek azonban korlátozásokat szabhatnak a licencelt tartalom teljesítményére, ami hatással lehet az egyéni hangmodell létrehozására, ezért alaposan olvassa el a licencet.|

## <a name="recording-your-script"></a>A szkript rögzítése

Jegyezze fel a szkriptet egy olyan Professional Recording Studióban, amely a hangvételre specializálódott. Egy regisztráló-standtal, a megfelelő berendezéssel és a megfelelő személyekkel fognak működni. A rögzítés nem teszi ki a szűkös terhelést.

Beszéljen a projektről a Studio rögzítési mérnökével, és hallgassa meg a véleményét. A rögzítésnek kis vagy egyetlen dinamikus tartományból álló tömörítéssel kell rendelkeznie (legfeljebb 4:1). Kritikus fontosságú, hogy a hang konzisztens kötettel rendelkezzen, és magas a jelek és a zaj aránya, miközben a nem kívánt hangokat nem szabad használni.

### <a name="do-it-yourself"></a>Saját magának

Ha saját kezűleg szeretné elvégezni a rögzítést, ahelyett, hogy a rögzítési studióba kerül, itt egy rövid ismertetőt talál. A Home Recording és a podcasting növekedésének köszönhetően minden eddiginél könnyebben találhatja meg a megfelelő rögzítési tanácsokat és erőforrásokat online.

A "Recording Booth" olyan kis helyiségnek kell lennie, amely nem érzékelhető visszhangot vagy "Room Tone". A lehető legcsendesnek és a hangszigeteltnek kell lennie. A falakon lévő drapériák segítségével csökkentheti az ECHO és a semlegesítés vagy a "deaden" a helyiség hangját.

A hangrögzítéshez a kiváló minőségű Studio kondenzátor mikrofon ("MIC") használható. A Sennheiser, az AKG és az újabb nagyítású mikrofonok is jó eredményeket tudnak eredményezni. Vásárolhat egy mikrofont, vagy béreljen egy helyi hang-vizuális kölcsönzési vállalatot. Keresse meg az egyiket egy USB-adapterrel. Ez a típusú mikrofon kényelmesen kombinálja a mikrofon elemét, az előerősítő és az analóg – digitális átalakítót egyetlen csomagba, egyszerűbbé téve a kapcsolódást.

Analóg mikrofont is használhat. Számos bérelt ház "vintage" mikrofont kínál a hangkarakterek számára. Vegye figyelembe, hogy a Professional Analog Gear kiegyensúlyozott XLR-összekötőket használ, nem pedig a felhasználói berendezésekben használt 1/4 hüvelykes beépülő modult. Ha az analógt választja, szüksége lesz egy előerősítő és egy számítógép-hangillesztőre is ezekkel az összekötővel.

Telepítse a mikrofont egy állványra vagy fellendülésre, és telepítsen egy pop-szűrőt a mikrofon elé a "zárhang" mássalhangzók (például "p" és "b") által okozott zaj elkerülése érdekében. Egyes mikrofonok olyan felfüggesztési csatlakoztatással rendelkeznek, amely elkülöníti őket a standon található rezgéstől, ami hasznos lehet.

A hangtehetségnek konzisztens távolságban kell maradnia a mikrofontól. A padlón lévő szalag használatával megjelölheti, hol kell állnia. Ha a tehetség inkább ülni szeretne, ügyeljen arra, hogy figyelje a MIC-távolságot, és elkerülje az elnöki zajt.

A szkript tárolására használjon állványt. Kerülje az állvány Horgászatát, hogy az tükrözze a hangerőt a mikrofon irányába.

A regisztráló berendezést üzemeltető személy – a mérnök – a tehetségtől különálló helyiségben kell lennie, és valamilyen módon a rögzítési standon ( *Talkback-áramkör)* található tehetséggel kommunikálhat.

A rögzítésnek a lehető legkevesebb zajt kell tartalmaznia, amelynek célja egy 80-db jel-zaj arány vagy jobb.

Hallgassa meg szorosan a "standján" lévő csend rögzítését, és állapítsa meg, hogy hol található a zaj, és ne zárja ki az okot. A zaj gyakori forrásai a légszellőzők, a fluoreszkáló fényanyagok, a közeli utakon lévő forgalom és a berendezések rajongói (még a notebookos számítógépek is rendelkezhetnek). A mikrofonok és a kábelek a közeli AC-vezetékektől, általában a zümmögéstől vagy a Buzz-tól származó elektromos zajt is választhatnak. A zümmögés oka egy *körkörös hurok* is lehet, amelyet a berendezések több elektromos áramkörhöz való csatlakoztatása okoz.

> [!TIP]
> Bizonyos esetekben lehet, hogy egy Hangszínszabályzót vagy egy zajcsökkentő szoftveres beépülő modult használ a felvételekből származó zaj eltávolításához, de mindig a legjobb, ha leállítja a forrását.

Állítsa be a szinteket úgy, hogy a rendszer a rendelkezésre álló dinamikus tartomány nagy részét túlvezetés nélkül használja. Ez azt jelenti, hogy a hang hangosan van beállítva, de nem annyira hangos, hogy torzul. A megfelelő rögzítés hullámformájának példája az alábbi képen látható:

![Egy jó rögzítési hullámforma](media/custom-voice/good-recording.png)

Itt a legtöbb tartomány (magasság) használatos, de a jel legmagasabb csúcsa nem éri el az ablak tetején vagy alján. Azt is láthatja, hogy a rögzítés csendje körülbelül egy vékony vízszintes vonalat mutat, amely egy alacsony zajszintet jelez. A rögzítés elfogadható dinamikus tartománnyal és a jel-zaj aránysal rendelkezik.

Az Ön által használt MIC-től függően közvetlenül a számítógépre kell rögzíteni a kiváló minőségű hang-vagy USB-porton keresztül. Az Analog esetében az egyszerű hangláncot a következőket kell megtartani: MIC, előerősítő, hang interfész, számítógép. A [lelkes Pro-eszközöket](https://www.avid.com/en/pro-tools) és az [Adobe Audition](https://www.adobe.com/products/audition.html) -t havonta, ésszerű költséggel is elvégezheti. Ha a költségvetés rendkívül szűk, próbálja ki az ingyenes [Audacityt](https://www.audacityteam.org/).

Rekord: 44,1 kHz 16 bites egyszólamú (CD-minőség) vagy jobb. A jelenlegi legkorszerűbb a 48 kHz 24 bites, ha a berendezés támogatja azt. Az egyéni hangportálra való beküldése előtt a rendszer leállítja a hangját 16 kHz-re. Mégis fontos, hogy az események szerkesztéséhez kiváló minőségű eredeti felvételre van szükség.

Ideális esetben a különböző személyek a Director, a Engineering és a Talent szerepköreiben szolgálnak. Ne próbálja meg önállóan. Egy csipetnyi egy személy lehet az igazgató és a mérnök is.

### <a name="before-the-session"></a>A munkamenet előtt

A Studio-idő pazarlásának elkerülése érdekében a rögzítési munkamenet előtt futtassa a parancsfájlt a hangtehetséggel. Míg a hangtehetségek megismerik a szöveget, megtekinthetik az ismeretlen szavak kiejtését.

> [!NOTE]
> A legtöbb regisztráló stúdió a szkriptek elektronikus megjelenítését kínálja a rögzítési fülkében. Ebben az esetben közvetlenül a szkript dokumentumában adja meg a futtatási megjegyzéseit. Továbbra is azt szeretné, hogy a dokumentum másolata vegyen fel megjegyzéseket a munkamenet során. A mérnökök többsége is szeretne egy merevlemezt másolni. És továbbra is szeretne egy harmadik nyomtatott másolatot készíteni a tehetségről, ha a számítógép nem áll le.

A hangalapú tehetség megkérdezheti, hogy melyik szót kívánja kiemelni ("operatív szó"). Mondja el nekik, hogy természetes olvasásra van szüksége, amely nem különösebb hangsúlyt mutat. Hangsúlyt adhat hozzá a beszéd szintetizálása során. nem lehet az eredeti rögzítés része.

A tehetséget elkülönítve kimondhatja a szavakat. A szkript minden szavát írásban kell kiejteni. A hangokat nem szabad kihagyni vagy összekeverni, ahogyan az az alkalmi beszédben gyakori, *kivéve, ha a parancsfájlban nem írták*.

|Írott szöveg|Nemkívánatos véletlen kiejtés|
|-|-|
|Soha ne adja meg|Soha ne adja meg|
|négy jelzőfény van|négy lámpa van|
|a mai Időjárás|Mi a mai Időjárás|
|kis barátomnak köszönhető|mondja el a Lil barátomnak|

A tehetség *nem* adhat hozzá különböző szüneteltetéseket a szavak között. A mondatnak természetesen természetesnek kell lennie, még akkor is, ha egy kis formálisan hangzik. Ez a részletes különbségtétel a helyesség gyakorlását is elvégezheti.

### <a name="the-recording-session"></a>A rögzítési munkamenet

Hozzon létre egy hivatkozási rögzítést vagy egy *egyező fájlt* a munkamenet elején. Kérje meg a tehetséget, hogy ismételje meg ezt a sort minden oldalon vagy így. Minden alkalommal, amikor összehasonlítja az új felvételt a hivatkozással. Ez a gyakorlat segít a tehetségnek a mennyiség, a tempó, a szurok és a hanglejtés egységességének megőrzése terén. Eközben a mérnök is használhatja a fájl egyeztetését a hangszintek és a teljes konzisztencia tekintetében.

Az egyeztetési fájl különösen akkor fontos, ha egy megszakítás után vagy egy másik nap múlva folytatja a rögzítést. Néhány alkalommal szeretné lejátszani a tehetséget, és minden alkalommal ismételje meg őket, amíg azok nem megfelelőek.

Vegye igénybe a tehetségét, és szakítson meg egy kis időt az egyes részletek előtt. Rögzítsen néhány másodperces csendet a hosszúságú kimondott szöveg között. A szavakat minden egyes alkalommal meg kell adni, a kontextust figyelembe véve. Például a "Record" utasítás a "Record" kifejezéstől eltérőként van kimondva.

Rögzítsen egy jó, öt másodperces csendet az első felvétel előtt, hogy rögzítse a "helyiség tónusát". Ezzel a gyakorlattal az egyéni hangportál kompenzálja a felvételek fennmaradó zajait.

> [!TIP]
> A hangvételhez csak a hangtehetségek szükségesek, így egy egyszólamú (egycsatornás) rögzítést is készíthet. Ha azonban a sztereóban rögzíti a hangfelvételt, a második csatornát használva rögzítheti a vezérlési helyiségben lévő fecsegést, hogy rögzítse az adott vonalak vagy a beérkező beszélgetéseket. Távolítsa el ezt a számot az egyéni hangportálra feltöltött verzióról.

Hallgassa meg szorosan a fejhallgatók használatával a hangtehetségek teljesítményét. Jó, de természetes letiltást, helyes kiejtést és nem kívánatos hangokat keres. Ne habozzon megkérdezni a tehetségét, hogy ismételten jegyezze fel, hogy nem felelnek meg a követelményeknek.

> [!TIP]
> Ha nagy mennyiségű hosszúságú kimondott szöveg használ, akkor előfordulhat, hogy egyetlen Kimondás nem észlelhető hatással az eredményül kapott egyéni hangra. Elképzelhető, hogy a problémákkal kapcsolatos hosszúságú kimondott szöveg egyszerűen jegyezze fel, kizárhatja azokat az adatkészletből, és megtudhatja, hogyan derül ki az egyéni hang. Bármikor visszatérhet a studióhoz, és később rögzítheti a kihagyott mintákat.

Vegye figyelembe, hogy minden egyes kiíráshoz a szkriptben szereplő szám vagy idő kód szerepel. Kérje meg a mérnöket, hogy minden egyes kiírást megjelölje a rögzítés metaadatait vagy a végszót is.

Készítsen rendszeres szüneteket, és adjon meg egy olyan italt, amely segít a hangtehetségnek jó formában megtartani a hangját.

### <a name="after-the-session"></a>A munkamenet után

A modern rögzítési stúdiók számítógépeken futnak. A munkamenet végén egy vagy több hangfájl jelenik meg, nem pedig szalag. Ezek a fájlok valószínűleg WAV vagy AIFF formátumúak CD-minőségben (44,1 kHz 16 bites) vagy jobb. 48 kHz 24 bites gyakori és kívánatos. A nagyobb mintavételi sebességek, például az 96 kHz, általában nem szükségesek.

Az egyéni hangportál használatához minden megadott kifejezésnek saját fájlban kell lennie. A Studio által szállított összes hangfájl több hosszúságú kimondott szöveg tartalmaz. Ezért az elsődleges gyártás utáni feladat az, hogy feldarabolja a felvételeket, és előkészítse őket a beküldéshez. Előfordulhat, hogy a rögzítési mérnök a fájlban (vagy egy külön Dákón) elhelyezett jelölőket tartalmaz, amely jelzi, hogy az egyes kiírások hol kezdődnek.

A megjegyzésekkel pontosan megtalálhatja a kívánt igényt, majd egy hangszerkesztő segédprogramot, például a [lelkes Pro-eszközöket](https://www.avid.com/en/pro-tools), az [Adobe auditiont](https://www.adobe.com/products/audition.html)vagy az ingyenes [Audacityt](https://www.audacityteam.org/)egy új fájlba másolhatja.

Az egyes klipek elején és végén csak az 0,2 másodperces csendet hagyja meg, kivéve az elsőt. A fájlnak a csend teljes öt másodpercével kell kezdődnie. Ne használjon hangszerkesztőt a fájl csendes részeinek "nulla" értékre való felvételéhez. A "Room Tone" is segít az egyéni hangalgoritmusok kompenzálására a fennmaradó háttérzajok esetében.

Hallgassa meg figyelmesen az egyes fájlokat. Ebben a szakaszban szerkesztheti azokat a kisméretű nemkívánatos hangokat, amelyeket a rögzítés során kihagyott, például egy enyhe ajak-csettint a sor előtt, de ügyeljen arra, hogy ne távolítsa el a tényleges beszédet. Ha nem tudja kijavítani a fájlt, távolítsa el az adatkészletből, és vegye figyelembe, hogy ezt megtette.

Konvertálja az egyes fájlokat 16 bitesre, a Mentés előtt pedig 16 kHz-es mintavételezési sebességet, és ha rögzítette a Studio csevegést, távolítsa el a második csatornát. Mentse az összes fájlt WAV formátumban, nevezze el a fájlokat a szkriptből a teljes számmal.

Végül hozza létre azt az *átiratot* , amely az egyes WAV-fájlokat a megfelelő kifejezés szöveges verziójával társítja. Az [egyéni hangok létrehozása](./how-to-custom-voice-create-voice.md) a szükséges formátum részleteit tartalmazza. A szöveget közvetlenül a szkriptből is másolhatja. Ezután hozzon létre egy zip-fájlt a WAV-fájlokból és a szöveges átiratból.

Ha később szüksége van rájuk, archiválja az eredeti felvételeket biztonságos helyen. Őrizze meg a parancsfájlt és a jegyzeteket is.

## <a name="next-steps"></a>Következő lépések

Készen áll a felvételek feltöltésére és az egyéni hang létrehozására.

> [!div class="nextstepaction"]
> [Egyéni hangbetűkészletek létrehozása](./how-to-custom-voice-create-voice.md)