---
title: Több-bérlős SaaS-minták
description: Ismerje meg az Azure Cloud Environment szolgáltatásban futó több-bérlős szoftverek (SaaS) adatbázis-alkalmazásai követelményeit és közös adatarchitektúra-mintáit.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793228"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Több-bérlős SaaS-adatbázisok bérletének mintái
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk a több-bérlős SaaS-alkalmazásokhoz elérhető különböző kivezetési modelleket ismerteti.

Több-bérlős SaaS-alkalmazások tervezésekor gondosan ki kell választania az alkalmazás igényeinek leginkább megfelelő bérleti modellt.  A bérleti modell határozza meg, hogy az egyes bérlők adattára hogyan legyen leképezve a tárterületre.  Az Ön által választott bérlői modell hatással van az alkalmazások tervezésére és felügyeletére.  A másik modellre való váltás esetenként költséges.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS-fogalmak és-terminológia

A szoftveres szolgáltatásként (SaaS) modellben a vállalat nem értékesít *licenceket* a szoftverhez. Ehelyett minden ügyfél kifizet a vállalatnak, így minden ügyfél *bérlője* lesz a vállalatának.

A bérleti díj fejében az egyes bérlők hozzáférést kapnak a SaaS-alkalmazás összetevőihez, és az adataikat a SaaS-rendszer tárolja.

A *bérleti modell* kifejezés arra utal, hogy a bérlők tárolt információi hogyan vannak rendszerezve:

- *Önálló bérlet:* &nbsp; Minden adatbázis csak egy bérlő adatait tárolja.
- *Több-bérlő:* &nbsp; Minden adatbázis több különálló bérlő adatait tárolja (az adatvédelmet biztosító mechanizmusokkal).
- A hibrid bérlői modellek is elérhetők.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. A megfelelő bérleti modell kiválasztása

Általánosságban elmondható, hogy a bérleti modell nem befolyásolja az alkalmazások működését, de ez valószínűleg a teljes megoldás egyéb szempontjait is befolyásolja.  A következő feltételek használhatók az egyes modellek értékelésére:

- **Méretezhetőség**
    - Bérlők száma.
    - Storage/bérlő.
    - A tárterület összesítésben.
    - Munkaterhelés.

- **Bérlő elkülönítése:** &nbsp; Adatok elkülönítése és teljesítménye (akár egy bérlő munkaterhelése is kihat másokra).

- **/Bérlői díj:** &nbsp; Az adatbázis költségei.

- **Fejlesztési komplexitás:**
    - A séma módosításai.
    - A lekérdezések módosításai (a mintához szükséges).

- **Működési komplexitás:**
    - A teljesítmény figyelése és kezelése.
    - Séma-kezelés.
    - Bérlő visszaállítása.
    - Vészhelyreállítás.

- **Testreszabhatóság:** &nbsp; Egyszerűvé teszi a séma-specifikus vagy bérlői osztályra jellemző testreszabások támogatását.

A bérleti vitát az *adatrétegre* összpontosítjuk.  Azonban Vegyünk egy pillanatra az *alkalmazás* rétegét.  Az alkalmazás rétegét monolit entitásként kezeli a rendszer.  Ha az alkalmazást sok kis összetevőre osztja, a bérlői modell választása változhat.  Néhány összetevőt eltérő módon kezelhet, mint a bérletet, valamint a felhasznált tárolási technológiát vagy platformot.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Önálló egybérlős alkalmazás egyetlen bérlős adatbázissal

#### <a name="application-level-isolation"></a>Alkalmazás szintjének elkülönítése

Ebben a modellben a teljes alkalmazást minden egyes bérlő esetében ismételten telepítik.  Az alkalmazás minden példánya önálló példány, így soha nem működik együtt a többi önálló példánnyal.  Az alkalmazás minden példánya csak egy Bérlővel rendelkezik, ezért csak egy adatbázisra van szükség.  A bérlő magához az adatbázishoz tartozik.

![Önálló alkalmazás kialakítása pontosan egyetlen bérlős adatbázissal.][image-standalone-app-st-db-111a]

Minden egyes alkalmazás-példány külön Azure-erőforráscsoporthoz van telepítve.  Az erőforráscsoport olyan előfizetéshez tartozhat, amelynek tulajdonosa a szoftvergyártó vagy a bérlő.  Mindkét esetben a szállító felügyelheti a bérlő szoftverét.  Minden egyes alkalmazás-példány úgy van konfigurálva, hogy a hozzá tartozó adatbázishoz kapcsolódjon.

Minden bérlői adatbázis egyetlen adatbázisként van telepítve.  Ez a modell biztosítja a legnagyobb adatbázis-elkülönítést.  Az elkülönítés azonban megköveteli, hogy elegendő erőforrás legyen lefoglalva az egyes adatbázisokhoz a legmagasabb terhelések kezeléséhez.  Itt azt számítja, hogy a rugalmas készletek nem használhatók különböző erőforráscsoportok vagy különböző előfizetések számára telepített adatbázisokhoz.  Ezzel a korlátozással ez a különálló egybérlős alkalmazás-modell a legdrágább megoldás a teljes adatbázis-költség szempontjából.

#### <a name="vendor-management"></a>Szállítói felügyelet

A szállító az összes önálló alkalmazás-példányban elérheti az összes adatbázist, még akkor is, ha az alkalmazás példányai különböző bérlői előfizetésekben vannak telepítve.  A hozzáférés SQL-kapcsolatokon keresztül érhető el.  Ez a több példányra kiterjedő hozzáférés lehetővé teszi, hogy a gyártó központosítsa a séma-kezelést és az adatbázison belüli lekérdezéseket jelentéskészítési vagy elemzési célokra.  Ha ilyen központosított felügyeletre van szükség, olyan katalógust kell központilag telepíteni, amely a bérlő azonosítóit a Database URI-k számára képezi le.  Azure SQL Database egy olyan horizontálisan használt könyvtárat biztosít, amely együtt használható a katalógus megadásához.  A horizontális Felskálázási függvénytár [Elastic Database ügyféloldali kódtár][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Több-bérlős alkalmazás adatbázis-Bérlővel

Ez a következő minta több-bérlős alkalmazást használ számos adatbázissal, amelyek mindegyike egybérlős adatbázis.  Minden új bérlőhöz új adatbázis van kiépítve.  Az alkalmazási *szint vertikális* felskálázása a csomópontok további erőforrásainak hozzáadásával.  Az *alkalmazás horizontális* felskálázása több csomópont hozzáadásával.  A skálázás a munkaterhelés alapján történik, és független az egyes adatbázisok számától és méretétől.

![Több-bérlős alkalmazás tervezése adatbázis-Bérlővel.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Testreszabás a bérlők számára

Az önálló alkalmazási mintához hasonlóan az egybérlős adatbázisok használata is erős bérlői elkülönítést biztosít.  Bármely olyan alkalmazásban, amelynek a modellje csak egybérlős adatbázisokat határoz meg, egy adott adatbázis sémája testreszabható és optimalizálható a bérlője számára.  Ez a Testreszabás nem érinti a többi bérlőt az alkalmazásban. Előfordulhat, hogy a bérlőnek az összes bérlő által igényelt alapszintű adatmezőn kívül kell lennie az adatoknak.  Emellett előfordulhat, hogy az extra adatmezőhöz index szükséges.

Az adatbázis-bérlők esetében a séma egy vagy több egyéni bérlőhöz való testreszabása egyszerűen elérhető.  Az alkalmazás gyártójának eljárásokat kell megterveznie a séma testreszabásának körültekintő kezeléséhez a skálán.

#### <a name="elastic-pools"></a>Rugalmas készletek

Ha az adatbázisok ugyanabban az erőforráscsoporthoz vannak telepítve, akkor rugalmas készletekre csoportosíthatók.  A készletek költséghatékony megoldást biztosítanak az erőforrások több adatbázis közötti megosztására.  Ez a készlet-beállítás olcsóbb, mint ami megköveteli, hogy az egyes adatbázisok elég nagyok legyenek ahhoz, hogy megfeleljenek az általa megszerzett használati csúcsoknak.  Annak ellenére, hogy a készletezett adatbázisok megosztják az erőforrásokhoz való hozzáférést, továbbra is nagy mértékben képesek a teljesítmény elszigetelésére.

![Több-bérlős alkalmazás tervezése a bérlői adatbázissal, rugalmas készlet használatával.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database biztosítja a megosztás konfigurálásához, figyeléséhez és kezeléséhez szükséges eszközöket.  A készlet-és adatbázis-szintű teljesítménymutatók egyaránt elérhetők a Azure Portalban és a Azure Monitor naplókon keresztül.  A mérőszámok nagy mértékben betekintést nyerhetnek az aggregált és a bérlői szintű teljesítménybe is.  Az egyes adatbázisok áthelyezhetők a készletek között egy adott bérlő számára fenntartott erőforrások biztosításához.  Ezek az eszközök lehetővé teszik a megfelelő teljesítmény biztosítását költséghatékony módon.

#### <a name="operations-scale-for-database-per-tenant"></a>Az adatbázis-bérlői műveletek méretezése

Azure SQL Database számos olyan felügyeleti funkcióval rendelkezik, amelyek nagy mennyiségű adatbázis, például több mint 100 000 adatbázis kezelésére lettek kialakítva.  Ezek a funkciók a bérlői adatbázis-alapú mintát teszik elérhetővé.

Tegyük fel például, hogy a rendszer rendelkezik egy 1000-bérlői adatbázissal, amely csak egy adatbázis.  Előfordulhat, hogy az adatbázis 20 indextel rendelkezik.  Ha a rendszeren 1000 egybérlős adatbázis van átalakítva, az indexek mennyisége 20 000-ra emelkedik.  Az [automatikus hangolás][docu-sql-db-automatic-tuning-771a]részeként Azure SQL Database az automatikus indexelési funkciók alapértelmezés szerint engedélyezve vannak.  Az Automatikus indexelés az összes 20 000-indexet és a folyamatban lévő létrehozási és eldobási optimalizálásokat kezeli.  Ezek az automatizált műveletek egy adott adatbázison belül történnek, és nem vannak összehangolva és nem korlátozódnak más adatbázisokhoz hasonló műveletekkel.  Az Automatikus indexelés a forgalmas adatbázisban eltérően kezeli az indexeket, mint a kevésbé forgalmas adatbázisban.  Az ilyen típusú indexelési feladatok nem praktikusak az adatbázis-bérlői skálán, ha ezt a hatalmas felügyeleti feladatot manuálisan kellett volna elvégezni.

A jól méretezhető egyéb felügyeleti funkciók közé tartoznak a következők:

- Beépített biztonsági másolatok.
- Magas rendelkezésre állás.
- Lemezes titkosítás.
- Teljesítmény telemetria.

#### <a name="automation"></a>Automation

A kezelési műveletek [devops][http-visual-studio-devops-485m] -modellen keresztül is megadhatók és felhasználhatók.  A műveletek akár automatizáltak is lehetnek, és elérhetők az alkalmazásban.

Automatizálhatja például egy bérlő egy korábbi időpontra történő helyreállítását.  A helyreállítás csak a bérlőt tároló egybérlős adatbázist kell visszaállítani.  Ez a visszaállítás nem érinti a többi bérlőt, ami megerősíti, hogy a felügyeleti műveletek az egyes bérlők részletes részletességi szintjén találhatók.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Több-bérlős alkalmazás több-bérlős adatbázisokkal

Egy másik elérhető minta egy több-bérlős adatbázisban található bérlők tárolása.  Az alkalmazás példánya tetszőleges számú több-bérlős adatbázist tartalmazhat.  Egy több-bérlős adatbázis sémájának egy vagy több bérlői azonosító oszlopával kell rendelkeznie ahhoz, hogy az adott bérlőről származó adatok szelektív módon legyenek lekérdezve.  Emellett előfordulhat, hogy a séma néhány olyan táblát vagy oszlopot igényel, amelyet a bérlők csak egy részhalmaza használ.  A statikus kód és a hivatkozási adathalmazok azonban csak egyszer tárolódnak, és az összes bérlő megosztja őket.

#### <a name="tenant-isolation-is-sacrificed"></a>A bérlő elkülönítése feláldozva

*Adatértékek:* &nbsp; A több-bérlős adatbázisok szükségszerűen feláldozzák A bérlők elkülönítését.  A több bérlő által tárolt adatmennyiséget egy adatbázisban tárolja a rendszer.  A fejlesztés során győződjön meg arról, hogy a lekérdezések soha nem teszik elérhetővé egynél több bérlő adatait.  A SQL Database támogatja a [sor szintű biztonságot][docu-sql-svr-db-row-level-security-947w], amely kikényszerítheti, hogy egy lekérdezés által visszaadott adatok hatóköre egyetlen bérlőre legyen érvényes.

*Feldolgozás:* &nbsp; A több-bérlős adatbázisok megosztják a számítási és tárolási erőforrásokat az összes bérlője között.  Az adatbázis teljes egészében megfigyelhető annak biztosítása érdekében, hogy a szolgáltatás elfogadható módon legyen végrehajtva.  Az Azure-rendszer azonban nem rendelkezik beépített módszerrel ezen erőforrások egyetlen bérlő általi használatának monitorozásához és kezeléséhez.  Ezért a több-bérlős adatbázis nagyobb kockázatot jelent a zajos szomszédokkal való találkozás során, amikor egy túlaktív bérlő munkaterhelése hatással van az ugyanazon adatbázisban lévő többi bérlő teljesítményére.  A további alkalmazás-szintű figyelés a bérlői szintű teljesítmény figyelésére is képes.

#### <a name="lower-cost"></a>Alacsonyabb díj

Általánosságban elmondható, hogy a több-bérlős adatbázisok esetében a legalacsonyabb a bérlői díj.  Egy adott adatbázishoz tartozó erőforrás-költségek alacsonyabbak, mint a megfelelő méretű rugalmas készletnél.  Emellett azokban az esetekben, amikor a bérlők csak korlátozott tárhelyet igényelnek, akár több millió bérlőt is tárolhat egyetlen adatbázisban.  Egyetlen rugalmas készlet sem tartalmazhat több millió adatbázist.  Azonban a 1000-készletekből álló, 1000-es adatbázisokat tartalmazó megoldás akár több millióan is elérheti a kapacitást, hogy a felügyelet ne legyen nehézkes.

A több-bérlős adatbázis-modell két változatát tárgyaljuk a következőkben, a több-bérlős, több-bérlős modell pedig a legrugalmasabb és skálázható.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Több-bérlős alkalmazás egyetlen több-bérlős adatbázissal

A legegyszerűbb több-bérlős adatbázis-minta egyetlen adatbázist használ az összes bérlőre vonatkozó adattároláshoz.  Ahogy egyre több bérlő lett hozzáadva, az adatbázis nagyobb tárolási és számítási erőforrásokkal bővült.  Lehetséges, hogy ez a vertikális felskálázás szükséges, bár mindig van egy végső méretezési korlát.  Azonban a korlát megkezdése előtt az adatbázis nem lesz kezelhető.

Az egyes bérlők felé irányuló felügyeleti műveletek összetettebbek a több-bérlős adatbázisok megvalósításához.  Ezek a műveletek nagy mértékben elfogadhatatlanul lassúak lehetnek.  Az egyik példa az, hogy csak egy bérlőhöz tartozó adatpontra vonatkozóan kell visszaállítani az adott időt.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Több-bérlős alkalmazás több-bérlős adatbázisokkal

A legtöbb SaaS-alkalmazás egyszerre csak egy bérlő információit éri el.  Ez a hozzáférési minta lehetővé teszi, hogy a bérlői adatforgalom több adatbázisban vagy szegmensben legyen elosztva, ahol az egyik bérlő összes információja egy szegmensben található.  Több-bérlős adatbázis-mintával kombinálva a többrétegű modell szinte korlátlan méretet tesz lehetővé.

![Több-bérlős alkalmazás tervezése több-bérlős adatbázisokkal.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Szegmensek kezelése

A horizontális skálázás a tervezéshez és az operatív felügyelethez is komplexitást kölcsönöz.  A bérlők és adatbázisok közötti leképezés fenntartásához katalógusra van szükség.  Emellett a szegmensek és a bérlői populáció kezeléséhez is szükség van a felügyeleti eljárásokra.  Az eljárásokat például a szegmensek hozzáadásához és eltávolításához, valamint a bérlők adatszegmensek közötti áthelyezéséhez kell tervezni.  A méretezés egyik módja, ha új szegmenst ad hozzá, és feltölti azt új Bérlővel.  Máskor a sűrűn kitöltött szegmenseket két, kevésbé sűrű feltöltésű szegmensre lehet bontani.  Több bérlő áthelyezését vagy megszüntetését követően egyesítheti a ritkán feltöltött szegmenseket.  Az egyesítés költséghatékonyabb erőforrás-kihasználtságot eredményezne.  A bérlők a munkaterhelések kiegyensúlyozására is áthelyezhetők a szegmensek között.

A SQL Database olyan felosztó/egyesítési eszközt biztosít, amely a horizontális Felskálázási függvénytárral és a katalógus-adatbázissal együtt működik.  A megadott alkalmazás feloszthatja és egyesítheti a szegmenseket, és áthelyezheti a bérlői adatszegmensek között.  Az alkalmazás ezen műveletek során is karbantartja a katalógust, és az érintett bérlőket offline állapotba helyezi az áthelyezésük előtt.  Az áthelyezést követően az alkalmazás ismét frissíti a katalógust az új leképezéssel, és a bérlőt ismét online állapotba helyezi.

#### <a name="smaller-databases-more-easily-managed"></a>A kisebb adatbázisok könnyebben kezelhetők

A bérlők több adatbázisba való elosztásával a több-bérlős több-bérlős megoldás kisebb adatbázisokat eredményez, amelyek könnyebben kezelhetők.  Például egy adott bérlő egy korábbi időpontra való visszaállítása mostantól egy kisebb adatbázis biztonsági másolatból való visszaállítását jelenti, nem pedig az összes bérlőt tartalmazó nagyobb adatbázist. Az adatbázis mérete és a bérlők száma adatbázis alapján lehetőség van a számítási feladatok és a felügyeleti erőfeszítések egyensúlyának kiválasztására.

#### <a name="tenant-identifier-in-the-schema"></a>Bérlő azonosítója a sémában

A használaton kívüli megközelítéstől függően további megkötések is megoldhatók az adatbázis-sémában.  A SQL Database felosztási/egyesítési alkalmazáshoz szükséges, hogy a séma tartalmazza a horizontális Felskálázási kulcsot, amely általában a bérlő azonosítója.  A bérlői azonosító az összes szilánkos tábla elsődleges kulcsának vezető eleme.  A bérlő azonosítója lehetővé teszi, hogy a felosztott/egyesítési alkalmazás gyorsan megkeresse és áthelyezze az adott bérlőhöz társított adatmennyiséget.

#### <a name="elastic-pool-for-shards"></a>Rugalmas készlet a szegmensekhez

A több-bérlős többbérlős adatbázisok rugalmas készletekben helyezhetők el.  Általánosságban elmondható, hogy a készletek számos egybérlős adatbázisa költséghatékonyan működik, és több bérlőt is kínál néhány több-bérlős adatbázisban.  A több-bérlős adatbázisok előnyösek, ha nagy számú viszonylag inaktív bérlő van.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hibrid többhelyes, több-bérlős adatbázis-modell

A hibrid modellben minden adatbázis rendelkezik a bérlői azonosítóval a sémájában.  Az adatbázisok egyszerre több bérlőt is tárolhatnak, az adatbázisok pedig elhelyezhetők.  Tehát a séma szempontjából mind a több-bérlős adatbázisok.  A gyakorlatban még néhány ilyen adatbázis csak egy bérlőt tartalmaz.  Függetlenül attól, hogy egy adott adatbázisban tárolt bérlők mennyisége nincs hatással az adatbázis-sémára.

#### <a name="move-tenants-around"></a>Bérlők áthelyezése

Egy adott bérlőt bármikor áthelyezhet a saját több-bérlős adatbázisba.  Bármikor megváltoztathatja az elméjét, és visszahelyezheti a bérlőt egy olyan adatbázisba, amely több bérlőt is tartalmaz.  Az új adatbázis kiépítésekor bérlőt is hozzárendelhet új egybérlős adatbázishoz.

A hibrid modell akkor világít, ha nagy különbségek vannak a bérlők azonosítható csoportjaihoz tartozó erőforrás-szükségletek között.  Tegyük fel például, hogy az ingyenes próbaverzióban részt vevő bérlők nem garantálják a bérlők előfizetésével járó magas szintű teljesítményt.  Előfordulhat, hogy a szabályzat az ingyenes próbaidőszakban lévő bérlők számára az ingyenes próbaverziós bérlők között megosztott több-bérlős adatbázisban lesz tárolva.  Ha egy ingyenes próbaverzió bérlője előfizet az alapszintű szolgáltatási csomagra, a bérlő áthelyezhető egy másik több-bérlős adatbázisba, amely kevesebb Bérlővel rendelkezhet.  A prémium szintű szolgáltatási rétegért fizető előfizető áthelyezhető a saját új, egybérlős adatbázisba.

#### <a name="pools"></a>Készletek

Ebben a hibrid modellben az előfizetői bérlők számára az egybérlős adatbázisok helyezhetők el az adatbázisok költségeinek csökkentése érdekében a bérlők számára.  Ez az adatbázis-bérlői modellben is megtörténik.

## <a name="i-tenancy-models-compared"></a>I. A bérlői modellek összehasonlítása

A következő táblázat összefoglalja a fő bérleti modellek közötti különbségeket.

| Mérés | Önálló alkalmazás | Adatbázis/bérlő | Több-bérlős szegmens |
| :---------- | :------------- | :------------------ | :------------------- |
| Méretezés | Közepes<br />1 – 100s | Nagyon magas<br />1 – 100, 10E | Korlátlan<br />1 – 1, 000, 10E |
| Bérlők elkülönítése | Nagyon magas | Magas | Alacsony egyetlen bérlő kivételével (azaz egy MT dB-ben). |
| Adatbázis díja bérlőn | Magas méret a csúcsok számára. | Alacsony felhasznált készletek. | Legalacsonyabb, kis bérlők esetében az MT-adatbázisok esetében. |
| Teljesítményfigyelés és-felügyelet | Csak bérlők esetében | Összesítés/bérlő | Összesített Bár a bérlők száma csak az egyediek esetében érhető el. |
| Fejlesztési komplexitás | Alacsony | Alacsony | Közepes horizontális skálázás miatt. |
| Működési komplexitás | Alacsony – magas. Egyénileg egyszerű, összetett, nagy léptékű. | Alacsony közepes. A minták az összetettséget a skálán kezelik. | Alacsony – magas. Az egyes bérlők kezelése összetett. |
| &nbsp; ||||

## <a name="next-steps"></a>Következő lépések

- [Olyan több-bérlős Wingtip-alkalmazás üzembe helyezése és feltárása, amely az adatbázis-bérlői SaaS-modellt használja – Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Üdvözöljük a Wingtip tickets minta SaaS Azure SQL Database bérleti alkalmazásban][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Önálló alkalmazás kialakítása pontosan egyetlen bérlős adatbázissal."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Több-bérlős alkalmazás tervezése adatbázis-Bérlővel."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Több-bérlős alkalmazás tervezése a bérlői adatbázissal, rugalmas készlet használatával."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Több-bérlős alkalmazás tervezése több-bérlős adatbázisokkal."