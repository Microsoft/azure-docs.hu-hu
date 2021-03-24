---
title: Ajánlott üzembe helyezési eljárások
description: Ez a cikk az Azure hatáskörébe való telepítés ajánlott eljárásait ismerteti. Az Azure hatáskörébe minden felhasználó regisztrálhatja, felderítheti, értelmezheti és felhasználhatja az adatforrásokat.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e02ad9187743603d46259d70965e49d6839ecd71
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949838"
---
# <a name="azure-purview-deployment-best-practices"></a>Az Azure hatáskörébe telepítése – ajánlott eljárások

Ez a cikk azokat a gyakori feladatokat ismerteti, amelyek segíthetnek a hatáskörébe éles környezetben történő üzembe helyezésében. Ezeket a feladatokat egy hónap vagy több fázisban is elvégezheti. Az útmutató segítségével még a már üzembe helyezett szervezetek is használhatják ezt az útmutatót annak biztosítására, hogy a lehető legtöbbet hozzanak ki a befektetésük során.

Az adatirányítási platform (például az Azure hatáskörébe) jól tervezett üzembe helyezése a következő előnyöket nyújtja:

- Jobb adatfelderítés
- Továbbfejlesztett analitikai együttműködés
- Maximalizálja a beruházások megtérülését.

## <a name="prerequisites"></a>Előfeltételek

* Hozzáférés Microsoft Azure fejlesztési vagy termelési előfizetéssel
* Lehetőség Azure-erőforrások létrehozására, beleértve a hatáskörébe
* Hozzáférés olyan adatforrásokhoz, mint a Azure Data Lake Storage vagy az Azure SQL tesztelési, fejlesztési vagy éles környezetekben
  * Data Lake Storage esetén a vizsgálathoz szükséges szerepkör az olvasó szerepköre
  * Az SQL esetében az identitásnak képesnek kell lennie a táblázatok lekérdezésére a besorolások mintavételezéséhez
* Hozzáférés a Azure Security Centerhoz, illetve az adatcímkéző Security Center-rendszergazdákkal való együttműködéshez

## <a name="identify-objectives-and-goals"></a>Célok és célok azonosítása

Számos szervezet elindította az adatirányítási utat olyan egyedi megoldások fejlesztésével, amelyek az elkülönített csoportok és adattartományok speciális követelményeinek kielégítésére szolgálnak a szervezeten belül. Bár a tapasztalatok az iparágtól, a terméktől és a kulturális környezettől függően eltérőek lehetnek, a legtöbb szervezet nehéznek tekinti az ilyen típusú megoldások konzisztens szabályozását és szabályzatait.

Néhány olyan közös adatirányítási célkitűzés, amelyet a korai fázisokban érdemes azonosítani, a következők:

* Az adatai üzleti értékének maximalizálása
* Adatkulturális környezet engedélyezése, amelyben az adatfogyasztók könnyedén megtalálják, értelmezik és megbíznak az adatkezelésben
* A különböző üzleti egységek közötti együttműködés fokozása egységes adatkezelési élmény biztosítása érdekében
* Az innováció elősegítése az adatelemzés felgyorsításával a felhő előnyeinek kihasználásához
* A különböző szaktudás-csoportok önkiszolgáló lehetőségeivel való felderített idő csökkentése
* A szolgáltatás ügyfeleit javító elemzési megoldások kézbesítésének időbeli csökkentése
* A tartományi specifikus eszközök és a nem támogatott technológiák használata miatti működési kockázatok csökkentése

Az általános megközelítés az átfogó célkitűzések különböző kategóriákba és célokba való bontása. Néhány példa:

|Kategória|Cél|
|---------|---------|
|Felderítés|A rendszergazda felhasználóknak képesnek kell lenniük az Azure-beli és nem Azure-beli adatforrások (beleértve a helyszíni forrásokat is) vizsgálatára az adategységek automatikus adatainak gyűjtéséhez.|
|Osztályozás|A platformnak automatikusan kell osztályoznia az adatelemzést az adatmintavételezés alapján, és az egyéni besorolások használatával manuális felülbírálást is engedélyezhet.|
|Használat|Az üzleti felhasználóknak az üzleti és technikai metaadatokra vonatkozó információkat is meg kell tudniuk találni az egyes eszközökről.|
|Lineage|Minden objektumnak az alapul szolgáló adatkészletek grafikus nézetét kell megjelenítenie, hogy a felhasználók megértsék az eredeti forrásokat, és hogy milyen módosításokat hajtottak végre.|
|Együttműködés|A platformnak lehetővé kell tennie a felhasználók számára, hogy további információkat biztosítson az egyes adategységekről.|
|Jelentéskészítés|A felhasználóknak képesnek kell lenniük a jelentések megtekintésére az adatbirtokon, beleértve a bizalmas adatokat és az olyan adatokat, amelyek további dúsítást igényelnek.|
|Adatszabályozás|A platformnak lehetővé kell tennie, hogy a rendszergazda szabályzatokat határozzon meg a hozzáférés-vezérléshez, és az egyes felhasználók alapján automatikusan érvényesítse az adatelérést.|
|Munkafolyamat|A platformnak képesnek kell lennie a munkafolyamatok létrehozására és módosítására, hogy könnyen kibővíthető és automatizálható legyen a különböző feladatok a platformon belül.|
|Integráció|Más, harmadik féltől származó technológiák, mint például a jegyek vagy a koordinálás, parancsfájl-vagy REST API-kon keresztül képesnek kell lenniük a platform integrálására.|

## <a name="top-questions-to-ask"></a>Kérdések a legfontosabb kérdésekre

Ha a szervezet egyetért a magas szintű célkitűzésekkel és célokkal, több csoportból is sok kérdéssel kell foglalkoznia. Fontos, hogy összegyűjtse ezeket a kérdéseket, hogy tervezzen egy tervet az összes probléma megoldása érdekében. Néhány példa a kezdeti fázisban futtatott kérdésekre:

1. Mik a fő szervezeti adatforrások és az adatrendszerek?
2. A hatáskörébe tartozó, még nem támogatott adatforrások esetén mi a lehetőségeim?
3. Hány a hatáskörébe tartozó példányra van szükségünk?
4. Kik a felhasználók?
5. Kik tudják beolvasni az új adatforrásokat?
6. Kik módosíthatják a tartalmat a hatáskörébe?
7. Milyen eljárást használhatok a hatáskörébe tartozó adatminőség javítására?
8. Hogyan lehet a platformot a meglévő kritikus eszközök, szószedeti feltételek és névjegyek használatával beindítani?
9. Hogyan integrálható a meglévő rendszerekkel?
10. Hogyan gyűjthet visszajelzést, és hogyan hozhat létre fenntartható folyamatot?

Habár előfordulhat, hogy a fenti kérdések többségére azonnal nem válaszol, a szervezet felhasználhatja a projekt keretét, és gondoskodhat arról, hogy az összes "kötelező" követelmény teljesül.

## <a name="include-the-right-stakeholders"></a>A megfelelő résztvevők belefoglalása

A teljes vállalat hatáskörébe tartozó megvalósítás sikerességének biztosítása érdekében fontos, hogy bevonja a megfelelő érintett feleket. Csak néhány személy vesz részt a kezdeti fázisban. A hatókör kibővítésekor azonban további personák szükségesek ahhoz, hogy hozzájáruljanak a projekthez, és visszajelzést nyújtsanak.

Néhány fontos érintett fél, akikkel a következőket teheti:

|Persona|Szerepkörök|
|---------|---------|
|**Adatkezelési igazgató**|A CDO számos olyan függvényt felügyel, amely magában foglalhatja az adatkezelést, az adatminőséget, a Master adatkezelést, az adatelemzést, az üzleti intelligenciát és az adatstratégia létrehozását. A hatáskörébe tartozó megvalósítási projekt szponzora lehet.|
|**Tartomány/vállalati tulajdonos**|Egy üzleti személy, aki befolyásolja az eszközök használatát, és rendelkezik költségvetés-vezérléssel|
|**Adatelemző**|Képes az üzleti problémák keretbe való bevezetésére és az adatelemzésre, hogy a vezetők üzleti döntéseket hozhassanak|
|**Adatfejlesztő**|Tervezze meg az üzleti szempontból kritikus fontosságú üzletági alkalmazások kialakítását és az adatbiztonság megtervezését és megvalósítását|
|**Adatmérnök**|Az adatverem üzemeltetése és karbantartása, különböző forrásokból származó adatok lekérése, adatok integrálása és előkészítése, adatfolyamatok beállítása|
|**Adatszakértő**|Elemzési modellek létrehozása és az API-k által elérhetővé tett adattermékek beállítása|
|**ADATBÁZIS-rendszergazda**|Az adatbázisokkal kapcsolatos incidensek és kérelmek saját, nyomon követése és feloldása a szolgáltatói szerződések (SLA) keretében; Az adatfolyamatokat is beállíthat|
|**DevOps**|Üzletági alkalmazások fejlesztése és megvalósítása; tartalmazhat parancsfájlokat és hangszerelési képességeket|
|**Adatbiztonsági specialista**|A hálózat és az adatbiztonság teljes körű felmérése, amely magában foglalja a hatáskörébe tartozó és kívüli adatforgalomot|

## <a name="identify-key-scenarios"></a>A főbb forgatókönyvek azonosítása

A hatáskörébe a Felhőbeli és a helyszíni környezeteket felölelő szervezeti adatközpontok közötti adatszabályozás központi kezelése használható. A sikeres megvalósításhoz meg kell határoznia a vállalat szempontjából kritikus fontosságú forgatókönyveket. Ezek a forgatókönyvek az üzleti egység határain kívüli vagy akár több felhasználói személy számára is kihathat a felsőbb rétegbeli vagy a alsóbb rétegekre

Ezek a forgatókönyvek többféleképpen is írhatók, de legalább az alábbi öt dimenziót kell tartalmaznia:

1. Persona – kik a felhasználók?
2. Forrásoldali rendszer – Mik az adatforrások, például Azure Data Lake Storage Gen2 vagy Azure SQL Database?
3. Impact terület – a forgatókönyv kategóriája
4. Részletes forgatókönyvek – hogyan használják a felhasználók a hatáskörébe a problémák megoldását?
5. Várt eredmény – mi a siker feltételei?

A forgatókönyveknek a mérhető eredményekkel kell rendelkezniük, amelyeknek konkrét, végrehajtható és végrehajthatónak kell lenniük. Néhány példa arra, hogy milyen forgatókönyveket használhat:

|Eset|Részletek|Persona|
|---------|---------|---------|
|Az üzleti szempontból kritikus fontosságú eszközök katalogizálása|Információra van szükségem az egyes adatkészletekről, hogy jól lássuk, mi is az. Ez a forgatókönyv a katalógusban lévő adatkészlettel kapcsolatos üzleti és technikai metaadatokat is tartalmazza. Az adatforrások közé tartoznak az Azure Data Lake Storage Gen2, az Azure szinapszis DW és/vagy Power BI. Ez a forgatókönyv olyan helyszíni erőforrást is tartalmaz, mint például a SQL Server.|Üzleti elemző, adattudós, adatmérnök|
|Üzleti szempontból kritikus fontosságú eszközök felderítése|Olyan keresőmotorra van szükségem, amely a katalógusban található összes metaadaton át tud keresni. Képesnek kell lennie arra, hogy a technikai kifejezéssel, az üzleti kifejezéssel vagy egyszerű vagy összetett kereséssel keressen helyettesítő karaktert.|Üzleti elemző, adattudós, adatmérnök, adatadminisztrátor|
|Nyomon követheti az adatforrásokat, és elháríthatja az adatkezelési problémákat|A jelentésekben, az előrejelzésekben és a modellekben lévő adatbányászati folyamatoknak az eredeti forráshoz való visszakövetéséhez, valamint a változások megértéséhez és az adatéletcikluson keresztüli adatkezeléshez szükségesek. Ennek a forgatókönyvnek támogatnia kell a rangsorolt adatfolyamatokat Azure Data Factory és a Databricks.|Adatmérnök, adattudós|
|A kritikus adategységek metaadatainak gyarapítása|A katalógusban lévő adatkészleteket a automatikusan generált technikai metaadatokkal kell bővíteni. Néhány példa a besorolásra és címkézésre.|Adatmérnök, tartomány/üzleti tulajdonos|
|Adategységek szabályozása felhasználóbarát felhasználói felülettel|Üzleti szószedetre van szükségem az üzleti célú metaadatokhoz. Az üzleti felhasználók a hatáskörébe tartozó önkiszolgáló forgatókönyvekkel jegyzeteket láthatnak az adatokhoz, és lehetővé teszik az adatok gyors felderítését a keresés használatával.|Tartomány/üzlet tulajdonosa, üzleti elemző, adattudós, adatmérnök|

## <a name="deployment-models"></a>Üzembe helyezési modellek

Ha csak egy kis csoporttal rendelkezik, amely alapszintű felhasználási használati esetekkel rendelkezik, akkor a megközelítés olyan egyszerű lehet, mintha egy hatáskörébe tartozik a teljes csoport kiszolgálására. Az is előfordulhat azonban, hogy a szervezetnek több, mint egy hatáskörébe tartozó példányra van szüksége. Ha pedig több, a hatáskörébe tartozó példányt használ, akkor az alkalmazottak hogyan támogathatják az eszközöket az egyik fázisból a másikba.

### <a name="determine-the-number-of-purview-instances"></a>A hatáskörébe tartozó példányok számának meghatározása

A legtöbb esetben a teljes szervezetnek csak egy hatáskörébe tartozó fiókkal kell rendelkeznie. Ez a megközelítés maximálisan kihasználja a "hálózati effektusok" előnyeit, ahol a platform értéke exponenciálisan nő a platformon belüli adatmennyiség függvényében.

A minta azonban kivételt képez:

1. **Új konfigurációk tesztelése** – a szervezetek több példányt is létrehozhatnak a vizsgálati konfigurációk vagy az elkülönített környezetekben lévő besorolások kipróbálásához. Bár a platform egyes területein (például a szószedetben) "verziószámozás" funkció található, egyszerűbb lenne az "eldobható" példány használata a szabad teszteléshez.
2. A **tesztelés, az üzem előtti és az éles üzem elválasztása** – a szervezetek különböző platformokat kívánnak létrehozni különböző környezetekben tárolt különféle adattípusokhoz. Nem ajánlott, mert ezek az adattípusok eltérő tartalomtípusok. A tartalom típusának elkülönítéséhez használhatja a szószedetet a felső hierarchia szintjén vagy kategóriájában.
3. **Konglomerátumok és összevont modell** – a konglomerátumok gyakran sok üzleti egységgel (busszal) működnek, és bizonyos esetekben nem is osztják meg egymással a számlázást. Ezekben az esetekben a szervezet végül létrehoz egy hatáskörébe tartozó példányt minden egyes BU-hoz. Ez a modell nem ideális, de szükség lehet rá, különösen azért, mert a busz gyakran nem hajlandó a számlázás megosztására.
4. **Megfelelőség** – vannak olyan szigorú megfelelőségi rendszerek is, amelyek a metaadatokat bizalmasként kezelik, és megkövetelik, hogy egy adott földrajzi helyen legyenek. Ha egy vállalat több földrajzi régióval rendelkezik, az egyetlen megoldás az, hogy több, az egyes földrajzi helyekhez tartozó hatáskörébe tartozik.

### <a name="create-a-process-to-move-to-production"></a>Folyamat létrehozása az éles környezetbe való áttéréshez

Egyes szervezetek dönthetnek úgy, hogy a hatáskörébe tartozó egyetlen éles verzióval együttműködve egyszerűen megtartják a dolgokat. Valószínűleg nem kell túllépniük a felderítési, keresési és tallózási helyzetekben. Ha egyes eszközökhöz nem megfelelő Szószedet van megadva, akkor elég elnézőnek kell lennie ahhoz, hogy a felhasználók saját maguk is javítsák. Azonban a legtöbb szervezet, amely különböző üzleti egységekben szeretné üzembe helyezni a hatáskörébe, a folyamat és a felügyelet valamilyen formáját kívánja használni.

Az éles folyamat egy másik fontos szempontja a besorolások és a címkék áttelepítésének módja. A hatáskörébe több mint 90 rendszerosztályozó tartozik. A fájl-, tábla-vagy oszlop-eszközökön rendszer-vagy egyéni besorolásokat alkalmazhat. A besorolások olyanok, mint a tulajdonos címkéi, és a vizsgálat során az adatbirtokban található adott típusú tartalmak megjelölésére és azonosítására szolgálnak. Az érzékenységi címkék segítségével azonosíthatja a szervezeti adatain belüli besorolási típusok kategóriáit, majd csoportosíthatja az egyes kategóriákra alkalmazni kívánt házirendeket. Ugyanazokat a bizalmas adattípusokat használja, mint a Microsoft 365, lehetővé téve a meglévő biztonsági szabályzatok és a teljes tartalom és adatkészletek védelmét. Képes a dokumentumok vizsgálatára és automatikus besorolására. Ha például van egy multiple.docx nevű fájlja, és a tartalma egy nemzeti azonosító számmal rendelkezik, a hatáskörébe tartozik a besorolás, például az EU nemzeti azonosító száma az eszköz részletei lapon.

A hatáskörébe tartoznak több olyan terület, ahol a katalógus-rendszergazdáknak biztosítaniuk kell a konzisztencia és a karbantartás ajánlott eljárásait a életciklusa során:

* **Adategységek** – az adatforrásokat újra kell vizsgálni a különböző környezetekben. A vizsgálat csak a fejlesztés során ajánlott, majd az API-k használatával újra létrehozza őket az éles környezetben. Ennek a legfőbb oka az, hogy a hatáskörébe tartozó képolvasók sokkal több "kábelezést" végeznek az adategységek színterei mögött, ami összetett lehet, ha egy másik hatáskörébe tartozó példányra helyezi át őket. Sokkal könnyebb felvenni ugyanazt az adatforrást az éles környezetben, és újra beolvasni a forrásokat. Az általános ajánlott eljárás az, hogy az összes használatban lévő vizsgálattal, kapcsolattal és hitelesítési mechanizmussal kapcsolatos dokumentációval rendelkezzen.
* **Ellenőrzési szabálykészlet** – ez az adott vizsgálathoz rendelt szabályok gyűjteménye, például a fájltípus és a besorolások észlelése. Ha még nem rendelkezik ennyi vizsgálati szabállyal, akkor lehetséges, hogy manuálisan újra létre kell hoznia őket az éles üzemen keresztül. Ehhez belső folyamatra és jó dokumentációra lesz szükség. Ha azonban a szabály a napi vagy heti rendszerességgel állítja be a változást, ezt a REST API útvonal megismerésével lehet megoldani.
* **Egyéni besorolások** – a besorolások nem változhatnak rendszeresen. Az üzembe helyezés kezdeti fázisában eltarthat egy ideig, hogy megértse a különböző követelményeket az egyéni besorolásokkal. A rendezést követően azonban ez kis módosítást igényel. Ezért javasoljuk, hogy az egyéni besorolásokat manuálisan telepítse át, vagy használja a REST API.
* **Szószedet** – a szószedetben szereplő kifejezések exportálására és importálására az UX használatával van lehetősége. Az automatizálási forgatókönyvekhez használhatja a REST API is.
* **Erőforrás-készletre vonatkozó szabályzatok** – ez a funkció nagyon fejlett minden jellemző szervezet esetében. Bizonyos esetekben a Azure Data Lake Storage rendelkezik a mappa elnevezési konvenciókkal és adott struktúrával, amely problémákat okozhat a hatáskörébe az erőforrás-készlet létrehozásához. Előfordulhat, hogy az üzleti egység az üzleti igényekhez igazodva további testreszabási lehetőségekkel szeretné módosítani az erőforrás-készlet kialakítását. Ebben az esetben érdemes nyomon követni az összes módosítást REST APIon keresztül, és dokumentálni a módosításokat a külső verziószámozási platformon keresztül.
* **Szerepkör-hozzárendelés** – itt szabályozhatja, hogy ki férhet hozzá a hatáskörébe, és hogy milyen engedélyekkel rendelkezik. A hatáskörébe a felhasználók és szerepkörök exportálásának és importálásának támogatása is REST API, de ez nem az Atlas API-kompatibilis. Javasoljuk, hogy egy Azure biztonsági csoportot rendeljen hozzá, és kezelje helyette a csoporttagság kezelését.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Különböző integrációs pontok tervezése és implementálása a hatáskörébe

Valószínű, hogy egy érett szervezet már rendelkezik egy meglévő adatkatalógussal. A legfontosabb kérdés az, hogy továbbra is használja-e a meglévő technológiát, és szinkronizáljon a hatáskörébe vagy sem. A szervezet meglévő termékeivel való szinkronizálás kezeléséhez a hatáskörébe tartozik az Atlas REST API-k. Az Atlas API-k hatékony és rugalmas mechanizmust biztosítanak a leküldéses és lekéréses forgatókönyvek kezelésére. Az információ közzétehető a hatáskörébe az Atlas API-k használatával, vagy leküldheti a legújabb frissítéseket egy másik rendszerről a hatáskörébe. A hatáskörébe tartozó információk olvashatók az Atlas API-k használatával, majd a meglévő termékekhez való szinkronizálása is lehetséges. 

Más integrációs forgatókönyvek, például a jegyek, az egyéni felhasználói felület és az összehangolás esetében az Atlas API-kat és a Kafka-végpontokat használhatja. Általában négy integrációs pont van a hatáskörébe:

* **Adategység** – ez lehetővé teszi a hatáskörébe, hogy beolvassa a tároló eszközeit, hogy felszámolja, hogy az adott eszköz milyen adatokat tartalmaz, és hogyan gyűjthet hozzájuk minden elérhető metaadatot. Így az SQL esetében ez lehet egy lista, amely a táblákat, a táblázatokat, a tárolt eljárásokat, a nézeteket és a konfigurációs információkat tartalmazza, például a következő helyeken `sys.tables` . A Azure Data Factory (ADF) esetében ez lehet az összes folyamat enumerálása, és a létrehozásuk, utolsó futtatása, jelenlegi állapota alapján történő beolvasása.
* **Lineage** – ez lehetővé teszi a hatáskörébe, hogy adatokat gyűjtsön egy elemzés/adatmutációs rendszerről az adatok áthelyezésének módjáról. A Spark-hoz hasonló dolgok összegyűjthetik a notebookok végrehajtásával kapcsolatos információkat, hogy megtudja, milyen adatokat töltenek be a jegyzetfüzetből, hogyan alakították át és hová hozták azt. Az SQL esetében például elemezheti a lekérdezési naplókat, hogy visszafejtse a mutációs műveleteket, és hogy mit tettek. Az igényektől függően leküldéses és lekéréses leválasztást is támogatunk.
* **Besorolás** – ez lehetővé teszi a hatáskörébe, hogy az adatforrásokból származó fizikai mintákat vegyen át, és a besorolási rendszeren keresztül fusson. A besorolási rendszer egy adat szemantikai adatait tekinti át. Előfordulhat például, hogy egy fájl egy parketta-fájl, és három oszlopból áll, a harmadik pedig egy sztring. A mintákon futtatott osztályozók azonban azt jelzik, hogy a karakterlánc neve, címe vagy telefonszáma. Az integrációs pont megvilágítása azt jelenti, hogy definiáljuk, hogyan nyithat meg a hatáskörébe olyan objektumokat, mint a jegyzetfüzetek, a folyamatok, a Parquet-fájlok, a táblák és a tárolók.
* **Beágyazott felhasználói élmény** – a "Studióval" (például ADF, SZINAPSZIS, SQL Studio, PBI és Dynamics) rendelkező termékek általában lehetővé teszik a felhasználók számára, hogy felderítsék a velük kommunikálni kívánt adatokat, valamint megtalálják a kimeneti adatokat is. A hatáskörébe katalógusa megkönnyíti az ilyen élmények felgyorsítását egy beágyazási élmény biztosításával. Ez a felhasználói élmény az API-ban vagy az UX-szinten fordulhat elő a partner lehetőségnél. Ha a hatáskörébe tartozó hívást ágyaz be, a szervezet kihasználhatja az adategységek felépítését a hatáskörébe, hogy megtalálja az adategységeket, lásd: Lineage, ellenőrzés sémák, minősítések, névjegyek stb.

## <a name="phase-1-pilot"></a>1. fázis: pilóta

Ebben a fázisban a hatáskörébe létre kell hozni és konfigurálni kell a felhasználók nagyon kis készletét. Általában csak egy 2-3-es csoport, amely teljes körűen használható a végpontok közötti forgatókönyvek futtatására. A szervezeten belüli hatáskörébe tartoznak. Ennek a fázisnak a fő célja annak biztosítása, hogy a kulcsfontosságú funkciók teljesíthetők legyenek, és hogy a megfelelő felek tisztában legyenek a projekttel.

### <a name="tasks-to-complete"></a>Elvégzendő feladatok

|Feladat|Részletek|Időtartam|
|---------|---------|---------|
|A követelmények összegyűjtése & beleegyezése|A követelmények teljes készletének összegyűjtése az összes érintett féllel. A különböző személyeknek részt kell venniük ahhoz, hogy a projekt egyes szakaszaiban a követelmények egy részhalmaza befejeződjön.|1 hét|
|Az alapszintű csomag beállítása|Menjen végig a [hatáskörébe gyorskonfigurálás](create-catalog-portal.md) és állítsa be a [hatáskörébe Starter Kit](tutorial-scan-data.md) -t a hatáskörébe tartozó összes érintett fél bemutatójában.|1 nap|
|Navigálás a hatáskörébe|Ismerje meg, hogyan használható a hatáskörébe a kezdőlapon.|1 nap|
|Az ADF konfigurálása a Lineage számára|A fő folyamatok és adategységek azonosítása. Gyűjtse össze a belső ADF-fiókhoz való kapcsolódáshoz szükséges összes információt.|1 nap|
|Adatforrás bevizsgálása, például Azure Data Lake Storage|Adja hozzá az adatforrást, és állítson be egy vizsgálatot. Győződjön meg arról, hogy a vizsgálat sikeresen észleli az összes eszközt.|2 nap|
|Keresés és Tallózás|Lehetővé teszi a végfelhasználók számára a hatáskörébe való hozzáférést és a végpontok közötti keresési és tallózási forgatókönyvek végrehajtását.|1 nap|

### <a name="acceptance-criteria"></a>Elfogadási feltételek

* A hatáskörébe tartozó fiók létrehozása sikeresen megtörtént a szervezet bérlője alatt a szervezeti előfizetésben.
* Több szerepkörrel rendelkező felhasználók egy kis csoportja fér hozzá a hatáskörébe.
* A hatáskörébe legalább egy adatforrás vizsgálatára van konfigurálva.
* A felhasználóknak képesnek kell lenniük a hatáskörébe tartozó kulcsok értékének kinyerésére, például:
  * Keresés és Tallózás
  * Lineage
* A felhasználóknak képesnek kell lenniük az eszköz tulajdonjogának hozzárendelésére az eszköz oldalon.
* Bemutató és bemutató a legfontosabb érintett felekkel kapcsolatos tudatosság növeléséhez.
* Vásárlás a felügyeletből az MVP-fázis további erőforrásainak jóváhagyásához.

## <a name="phase-2-minimum-viable-product"></a>2. fázis: a minimálisan életképes termék

Ha az egyeztetett követelmények és az üzleti egységek bevonása a hatáskörébe, a következő lépés a minimálisan életképes termék (MVP) kiadásának használata. Ebben a fázisban több olyan felhasználó számára terjeszti ki a hatáskörébe, akik horizontálisan és függőlegesen további igényeket is igénybe vesznek. Olyan kulcsfontosságú forgatókönyvek szükségesek, amelyeknek minden felhasználó számára vízszintesen kell teljesülniük, például a Szószedet feltételeit, a keresést és a böngészést. Az egyes üzleti egységekhez vagy csoportokhoz is részletes követelmények vonatkoznak, hogy olyan konkrét végpontok közötti forgatókönyveket fedjenek fel, mint például az Azure Data Lake Storage az Azure szinapszis DW és a Power BI.

### <a name="tasks-to-complete"></a>Elvégzendő feladatok

|Feladat|Részletek|Időtartam|
|---------|---------|---------|
|[Az Azure szinapszis Analytics vizsgálata](register-scan-azure-synapse-analytics.md)|Az adatbázis-források bevezetésének megkezdése és a kulcsfontosságú eszközök feltöltésének vizsgálata|2 nap|
|[Egyéni besorolások és szabályok létrehozása](create-a-custom-classification-and-classification-rule.md)|Az eszközök vizsgálatát követően a felhasználók felmerülhetnek, hogy a hatáskörébe tartozó alapértelmezett besorolások mellett további használati esetek is rendelkezésre állnak.|2-4 hét|
|[Power BI vizsgálata](register-scan-power-bi-tenant.md)|Ha a szervezete Power BI használ, a Power BI vizsgálatával összegyűjtheti az adatelemzők által használt adategységeket, illetve az adatelemzők által felhasznált adategységeket, amelyeknek követelménye, hogy tartalmazza a leszármazást a tárolási rétegből.|1-2 hét|
|[Szószedet-kifejezések importálása](how-to-create-import-export-glossary.md)|A legtöbb esetben a szervezet már kifejlesztheti a Szószedet használati feltételeinek és kifejezésének gyűjteményét az eszközökhöz. Ehhez az importálási folyamatnak a. csv-fájlon keresztül kell lennie a hatáskörébe.|1 hét|
|Névjegyek hozzáadása az eszközökhöz|A legfontosabb eszközök esetében előfordulhat, hogy létre kell hoznia egy folyamatot, amely lehetővé teszi más personák számára a névjegyek hozzárendelését vagy a REST API-kon keresztül történő importálást.|1 hét|
|Bizalmas címkék és vizsgálat hozzáadása|Előfordulhat, hogy ez nem kötelező egyes szervezetek számára, attól függően, hogy hogyan használják a címkézést a M365.|1-2 hét|
|Besorolás és bizalmas adatok beszerzése|A hatáskörébe tartozó jelentéskészítéshez és betekintéshez hozzáférhet a funkcióhoz, hogy különböző jelentéseket kapjon, és bemutatót nyújtson a felügyelethez.|1 nap|
|A felhasználók a hatáskörébe tartozó felügyelt felhasználók használatával|Ez a lépés megköveteli, hogy a hatáskörébe tartozó rendszergazda működjön együtt a Azure Active Directory rendszergazdájával, és hozzon létre új biztonsági csoportokat, hogy hozzáférést biztosítson a hatáskörébe.|1 hét|

### <a name="acceptance-criteria"></a>Elfogadási feltételek

* A felhasználók nagyobb csoportja sikeresen bevezetve a hatáskörébe (50 +)
* Üzleti szempontból kritikus adatforrások vizsgálata
* Az összes kritikus Szószedet-kifejezés importálása és társítása
* A kulcsfontosságú eszközök fontos címkézésének tesztelése sikeresen megtörtént
* A résztvevő üzleti egységek felhasználói számára vonatkozó minimális forgatókönyvek sikeresen teljesültek

## <a name="phase-3-pre-production"></a>3. fázis: üzem előtti

Az MVP-fázis bevezetését követően itt az ideje, hogy az üzem előtti mérföldkövet tervezze meg. A szervezet dönthet úgy, hogy az üzem előtti és az éles üzemre vonatkozó, külön példányban tárolja a hatáskörébe, vagy megtartja ugyanazt a példányt, de korlátozza a hozzáférést. Ebben a fázisban érdemes lehet bemutatni a helyszíni adatforrások, például a SQL Server vizsgálatát is. Ha az adatforrások nem támogatják a hatáskörébe tartozó hézagokat, ideje, hogy megismerje az Atlas API-t, hogy megismerjék a további lehetőségeket.

### <a name="tasks-to-complete"></a>Elvégzendő feladatok

|Feladat|Részletek|Időtartam|
|---------|---------|---------|
|A vizsgálat pontosítása a vizsgálati szabálykészlet használatával|A szervezet számos adatforrással rendelkezik az üzem előtti használathoz. Fontos, hogy előzetesen definiálja a vizsgálat legfontosabb feltételeit, hogy a besorolások és a fájlkiterjesztés következetesen alkalmazható legyen a táblán.|1-2 nap|
|A régió rendelkezésre állásának felmérése a vizsgálathoz|Az adatforrások régiójától és a megfelelőségre és biztonságra vonatkozó szervezeti követelményektől függően érdemes megfontolni, hogy mely régiókat kell elérhetővé tennie a vizsgálathoz.|1 nap|
|A tűzfal fogalmának megértése a vizsgálat során|Ehhez a lépéshez meg kell ismernie, hogy a szervezet hogyan konfigurálja a tűzfalat, és hogy a hatáskörébe hogyan hitelesítheti magát a vizsgálathoz az adatforrásokhoz való hozzáféréshez.|1 nap|
|A privát hivatkozás fogalmának megértése a vizsgálat során|Ha a szervezete privát hivatkozást használ, meg kell határoznia a hálózati biztonság alapjait, hogy tartalmazza a követelmények részét képező privát hivatkozást.|1 nap|
|[Helyszíni SQL Server ellenőrzése](register-scan-on-premises-sql-server.md)|Ez nem kötelező, ha helyszíni SQL Server van. A vizsgálathoz saját üzemeltetésű [Integration Runtime](manage-integration-runtimes.md) beállítása szükséges, és adatforrásként hozzá kell SQL Server.|1-2 hét|
|A hatáskörébe REST API használata integrációs forgatókönyvekhez|Ha a hatáskörébe más külső technológiákkal, például a bevezetéssel vagy a jegyrendszer-szolgáltatással való integrálására vonatkozó követelményekkel rendelkezik, érdemes felderíteni REST API területén.|1-4 hét|
|A hatáskörébe tartozó díjszabás ismertetése|Ez a lépés a szervezet számára fontos pénzügyi információkat biztosít a döntéshozatalhoz.|1-5 nap|

### <a name="acceptance-criteria"></a>Elfogadási feltételek

* Legalább egy üzleti egység beléptetése az összes felhasználóval
* Helyszíni adatforrások, például SQL Server ellenőrzése
* POC legalább egy integrációs forgatókönyv REST API használatával
* Végezzen el egy olyan tervet, amely az infrastruktúra és a biztonság kulcsfontosságú területeit magában foglalja az éles környezetben

## <a name="phase-4-production"></a>4. fázis: éles üzem

A fenti fázisokat egy hatékony információs irányítás létrehozásával kell követni, amely a jobb irányítási programok alapja. Az adatkezelés révén a szervezet felkészíti a növekvő trendeket, például a mesterséges intelligenciát, a Hadoop, a IoT és a blockchain. Ez csak a sok dologra vonatkozó adatmennyiség és az elemzés, és sok más is megbeszélhető. A megoldás eredménye a következő:

* **Üzleti** irányultságú – olyan megoldás, amely az üzleti igényekhez és forgatókönyvekhez igazodik a technikai követelményekhez képest.
* **Jövőre kész** – a megoldás maximalizálja a platform alapértelmezett funkcióit, és szabványos iparági gyakorlatokat használ a konfigurációs vagy parancsfájlos tevékenységekhez a platform fejlesztésének és fejlődésének támogatásához.

### <a name="tasks-to-complete"></a>Elvégzendő feladatok

|Feladat|Részletek|Időtartam|
|---------|---------|---------|
|Üzemi adatforrások vizsgálata engedélyezett tűzfallal|Ha a tűzfal nem kötelező, de fontos, hogy feltárja az infrastruktúra megerősítésének lehetőségeit.|1-5 nap|
|Privát hivatkozás engedélyezése|Ha ez nem kötelező, ha privát hivatkozást használ. Ellenkező esetben kihagyhatja ezt, mert a kötelező feltétel, ha a magánjellegű beállítás engedélyezve van.|1-5 nap|
|Automatizált munkafolyamat létrehozása|A munkafolyamat az olyan folyamatok automatizálásához fontos, mint például a jóváhagyás, az eszkaláció, a felülvizsgálat és a kibocsátások kezelése.|2-3 hét|
|Művelet dokumentációja|Az adatszabályozás nem egyszeri projekt. Ez egy folyamatos program az adatvezérelt döntéshozatalhoz és a vállalati lehetőségek létrehozásához. Fontos a kulcsfontosságú eljárás és az üzleti szabványok dokumentálása.|1 hét|

### <a name="acceptance-criteria"></a>Elfogadási feltételek

* Az összes üzleti egység és a felhasználók beléptetése sikeresen megtörtént
* Az infrastruktúra-és biztonsági követelmények teljesítése sikeres volt az üzemi környezetben
* A felhasználók által igényelt összes használati eset sikeres teljesítése

## <a name="platform-hardening"></a>Platform megerősítése

További megerősítő lépések hajthatók végre:

* A biztonsági helyzet növeléséhez engedélyezze a vizsgálatot a tűzfal erőforrásain, vagy használja a privát hivatkozást
* A hatókör-ellenőrzés finomhangolása a vizsgálati teljesítmény javítása érdekében
* A REST API-k használata a biztonsági mentéshez és helyreállításhoz szükséges kritikus metaadatok és tulajdonságok exportálásához
* Az emberi hibák elkerülése érdekében a munkafolyamattal automatizálhatja a jegyeket és az eseményeket

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: a Starter Kit futtatása és az adatvizsgálat](tutorial-scan-data.md)
- [Oktatóanyag: navigáljon a kezdőlapon, és keressen rá egy eszközre](tutorial-asset-search.md)