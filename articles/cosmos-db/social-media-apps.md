---
title: 'Azure Cosmos DB tervezési minta: közösségi multimédia alkalmazások'
description: Ismerje meg a közösségi hálózatokra vonatkozó kialakítási mintát a Azure Cosmos DB és más Azure-szolgáltatások tárolási rugalmasságának kihasználásával.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 329c4b40f11b36de80581d4a1396813bc8de5c73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010316"
---
# <a name="going-social-with-azure-cosmos-db"></a>Közösségi Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A nagymértékben összekapcsolt társadalomban való élet azt jelenti, hogy egy adott időpontban egy **közösségi hálózat** részévé válik. A közösségi hálózatokkal a barátokkal, munkatársakkal, családtal vagy időnként megoszthatja a közös érdeklődéssel rendelkező személyekkel való szenvedélyét.

A mérnökök és a fejlesztők megértették, hogyan tárolják ezeket a hálózatokat, és összekapcsolják az adataikat. Vagy lehet, hogy az új közösségi hálózat létrehozásához vagy összeépítéséhez is feladatozott egy adott piaci szegmenshez. Ha a jelentős kérdés merül fel: Hogyan történik az összes tárolt adatmennyiség?

Tegyük fel, hogy új és fényes közösségi hálózatot hoz létre, ahol a felhasználók a kapcsolódó médiával, például képekkel, videókkal vagy akár zenével is közzétehetnek cikkeket. A felhasználók észrevételeket tehetnek a bejegyzésekhez, és pontokat adhatnak a minősítésekhez. A felhasználók a fő webhely kezdőlapján láthatják és kezelhetik a bejegyzéseket. Ez a módszer nem a bonyolult, hanem az egyszerűség kedvéért nem áll fenn. (A kapcsolatok által érintett egyéni felhasználói hírcsatornák is kiterjedhetnek, de a cikk célja meghaladja ezt a cikket.)

Tehát hogyan tárolja ezeket az adattárakat, és hol?

Lehetséges, hogy az SQL-adatbázisokkal kapcsolatos tapasztalatokkal rendelkezik, vagy az [adatmodell-modellezési](https://en.wikipedia.org/wiki/Relational_model)fogalmakkal rendelkezik. A következőképpen kezdheti meg a rajzolást:

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="A relatív rokonsági modellt bemutató diagram" border="false":::

Egy tökéletesen normalizált és csinos adatstruktúra... Ez nem méretezhető.

Nem értem, hogy az SQL-adatbázisok teljes életemmel dolgoztam. Nagyszerűek, de minden minta, gyakorlat és szoftver platformhoz hasonlóan nem tökéletesek minden forgatókönyvhöz.

Miért nem az SQL a legjobb választás ebben a forgatókönyvben? Tekintsük át egy adott bejegyzés szerkezetét. Ha szeretném megjeleníteni a bejegyzést egy webhelyen vagy alkalmazásban, a következővel kell elvégeznie a lekérdezést:... nyolc tábla (!) összekapcsolásával csak egyetlen bejegyzést jeleníthet meg. Most egy olyan, a képernyőn megjelenő streamet ábrázol, amely dinamikusan betöltődik és megjelenik a képernyőn, és megnézheti, hová megyek.

A tartalom kiszolgálása érdekében egy hatalmas SQL-példányt is használhat, amely elegendő teljesítménnyel rendelkezik több ezer lekérdezés megoldásához. De miért lenne, ha egy egyszerűbb megoldás létezik?

## <a name="the-nosql-road"></a>A NoSQL út

Ez a cikk végigvezeti Önt a közösségi platform adatainak az Azure NoSQL-adatbázissal való modellezésében [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) költséghatékonyan. Azt is megtudhatja, hogyan használhat más Azure Cosmos DB funkciókat, például a [GREMLIN API](../cosmos-db/graph-introduction.md)-t. Az [NoSQL](https://en.wikipedia.org/wiki/NoSQL) megközelítés használatával az adattárolás JSON formátumban és a [denormalizálás](https://en.wikipedia.org/wiki/Denormalization)alkalmazása során a korábban bonyolított post egyetlen [dokumentumba](https://en.wikipedia.org/wiki/Document-oriented_database)alakítható át:

```json
{
    "id":"ew12-res2-234e-544f",
    "title":"post title",
    "date":"2016-01-01",
    "body":"this is an awesome post stored on NoSQL",
    "createdBy":User,
    "images":["https://myfirstimage.png","https://mysecondimage.png"],
    "videos":[
        {"url":"https://myfirstvideo.mp4", "title":"The first video"},
        {"url":"https://mysecondvideo.mp4", "title":"The second video"}
    ],
    "audios":[
        {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
        {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
    ]
}
```

Emellett egyetlen lekérdezéssel is felhívható, és nem lehet illesztés. Ez a lekérdezés sokkal egyszerűbb és egyszerű, és a költségvetés-Wise, kevesebb erőforrást igényel a jobb eredmény eléréséhez.

Azure Cosmos DB ellenőrzi, hogy az összes tulajdonság indexelve van-e az automatikus indexeléssel. Az Automatikus indexelés akár testre is [szabható](index-policy.md). A séma nélküli megközelítés lehetővé teszi, hogy a dokumentumok különböző és dinamikus struktúrákkal legyenek tárolva. Lehet, hogy holnap szeretné, hogy a hozzászólások listáját a kategóriákat vagy a hashtageket társítja? A Cosmos DB a hozzáadott attribútumokkal együtt kezeli az új dokumentumokat, és nem igényel további munkát.

A bejegyzésekkel kapcsolatos megjegyzések a szülő tulajdonsággal rendelkező egyéb bejegyzésekként is kezelhetők. (Ez a gyakorlat leegyszerűsíti az objektumok leképezését.)

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":User2,
    "parent":"ew12-res2-234e-544f"
}

{
    "id":"asd2-fee4-23gc-jh67",
    "title":"Ditto!",
    "date":"2016-01-03",
    "createdBy":User3,
    "parent":"ew12-res2-234e-544f"
}
```

A közösségi interakciók pedig külön objektumként tárolhatók számlálóként:

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

A hírcsatornák létrehozása csak olyan dokumentumok létrehozására szolgál, amelyek egy adott fontossági sorrendbe tartozó post-azonosítók listáját tudják tárolni:

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

Lehet, hogy a "legújabb" streamet a létrehozási dátum szerint rendezve rendezi a rendszer. Vagy lehet, hogy az elmúlt 24 órában egy "legforróbb" streamtel rendelkezik azokkal az álláshelyekkel, amelyek több szeretnek. Akár egyéni streamet is megvalósíthat az egyes felhasználók számára logika, például követők és érdeklődési körök alapján. Továbbra is a hozzászólások listája. Ennek a listának a létrehozása, de az olvasási teljesítmény nem hátráltatható. Miután megszerezte az egyik listát, egyetlen lekérdezést ad ki, hogy Cosmos DB a (z) [kulcsszó](sql-query-keywords.md#in) használatával, hogy egyszerre kapjon bejegyzéseket.

A hírcsatorna-adatfolyamok az [Azure app Services "](https://azure.microsoft.com/services/app-service/) háttér-folyamatok: [webjobs](../app-service/webjobs-create.md)használatával hozhatók fel. A létrehozást követően az [Azure Storage](https://azure.microsoft.com/services/storage/) - [várólistákkal](../storage/queues/storage-dotnet-how-to-use-queues.md) és az [Azure webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)-val aktivált webjobs-feladatokkal indítható el a háttér-feldolgozás, amely a saját egyéni logikája alapján továbbítja a streamek utáni propagálást.

A pontok és a kedvelt bejegyzések feldolgozhatók egy késleltetett módon, ezzel a módszerrel egy végül konzisztens környezet létrehozásához.

A követői trükkösebbak. Cosmos DB a dokumentum mérete korlátozott, és a nagyméretű dokumentumok olvasása/írása hatással lehet az alkalmazás méretezhetőségére. Így úgy gondolja, hogy a követőket a következő struktúrával rendelkező dokumentumként tárolja:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "followersOf": "dse4-qwe2-ert4-aad2",
    "followers":[
        "ewr5-232d-tyrg-iuo2",
        "qejh-2345-sdf1-ytg5",
        //...
        "uie0-4tyg-3456-rwjh"
    ]
}
```

Ez a struktúra több ezer követővel rendelkező felhasználó számára is működhet. Ha azonban néhány híresség csatlakozik a ranglétrához, ez a módszer nagy méretű dokumentumokhoz vezethet, és végül a dokumentum méretére vonatkozó korlátot is elérheti.

A probléma megoldásához vegyes megközelítést használhat. A felhasználói statisztikai dokumentum részeként tárolhatja a követők számát:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

Azure Cosmos DB [GREMLIN API](../cosmos-db/graph-introduction.md) -val a követői tényleges gráfját tárolhatja az egyes felhasználók és [élek](http://mathworld.wolfram.com/GraphEdge.html) [számára,](http://mathworld.wolfram.com/GraphVertex.html) amelyek az "a-Follow-B" kapcsolatokat használják. A Gremlin API-val egy adott felhasználó követői olvashatók be, és összetettebb lekérdezések hozhatók létre, amelyek közösen javasolják az embereket. Ha felveszi a diagramba azokat a tartalmi kategóriákat, amelyeket az emberek szeretnek vagy szeretnek, megkezdheti az intelligens tartalom felderítését, az olyan tartalmak megkeresését, amelyeket az Ön által követett személyeknek ajánlanak, vagy megtalálhatja azokat a személyeket, akikkel a közös használatban lehet.

A felhasználói statisztikai dokumentum továbbra is felhasználható kártyák létrehozására a felhasználói felületen vagy a gyors profilok előzetes verziójában.

## <a name="the-ladder-pattern-and-data-duplication"></a>A "létra" minta és az adatismétlődés

Előfordulhat, hogy észrevette a hozzászólásra hivatkozó JSON-dokumentumot, a felhasználó számos előfordulása van. És kitalálta volna, hogy ezek a duplikált adatok azt jelentik, hogy a denormalizálás miatt a felhasználót leíró információk több helyen is szerepelhetnek.

A gyorsabb lekérdezések lehetővé teszik az adatismétlődést. A probléma ennek a mellékhatásnak a következménye, hogy ha valamilyen művelet, a felhasználó adatváltozása megváltozik, meg kell találnia az összes olyan tevékenységet, amelyet a felhasználó valaha tett, és az összeset frissíti. Nem megfelelő, igaz?

Ezt úgy oldja meg, hogy azonosítja az alkalmazásban az egyes tevékenységekhez megjelenített felhasználó fő attribútumait. Ha vizuálisan megjelenít egy bejegyzést az alkalmazásban, és csak a létrehozó nevét és képét jeleníti meg, miért tárolja az összes felhasználó adatait a "createdBy" attribútumban? Ha minden megjegyzéshez csak a felhasználó képét jeleníti meg, akkor nincs szükség a felhasználó további adataira. Ebben az esetben a "ladder Pattern" kifejezést kell meghívni.

Vegyük például a felhasználói adatokat:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "address":"742 Evergreen Terrace",
    "birthday":"1983-05-07",
    "email":"john@doe.com",
    "twitterHandle":"\@john",
    "username":"johndoe",
    "password":"some_encrypted_phrase",
    "totalPoints":100,
    "totalPosts":24
}
```

Ennek az információnak a megtekintésével gyorsan észlelhető, hogy melyik a kritikus információ, ami nem így van, így a "létra" létrehozása is megtörténik:

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="A létra mintájának ábrája" border="false":::

A legkisebb lépés neve UserChunk, a felhasználó azonosítására szolgáló minimális információ, amely az adatok ismétlődésére szolgál. Ha csökkenti a duplikált adatok méretét, hogy csak a "show" adatokat fogja megjeleníteni, csökkentse a tömeges frissítések lehetőségét.

A középső lépés neve felhasználó. Ez a teljes adat, amelyet a legtöbb teljesítménytől függő lekérdezésre használ a Cosmos DB, a leginkább hozzáférő és kritikus fontosságú. Ez tartalmazza a UserChunk által jelzett információkat.

A legnagyobb a kiterjesztett felhasználó. Magában foglalja a kritikus fontosságú felhasználói adatokat és más olyan adatokat, amelyeket nem kell gyorsan olvasni, vagy az esetleges használatot, például a bejelentkezési folyamatot. Ezeket az adattárakat a Cosmos DBon kívül, Azure SQL Database vagy Azure Storage-táblákban lehet tárolni.

Miért érdemes felosztani a felhasználót, és más helyeken is tárolni ezeket az információkat? Mivel a teljesítmény szempontjából, annál nagyobb a dokumentumok, a drágább a lekérdezések. Tartsa a dokumentumok Slim-et, és a megfelelő információkkal elvégezheti a közösségi hálózat teljesítmény-függő lekérdezéseit. A további információkat az esetleges forgatókönyvek, például a teljes profil szerkesztése, a bejelentkezések és az adatbányászat a használati elemzésekhez és a Big Information-kezdeményezésekhez című témakörben tárolja. Valójában nem érdekli, hogy az adatbányászati adatgyűjtés lassabb-e, mert Azure SQL Database fut. Aggálya van annak ellenére, hogy a felhasználók gyors és karcsú felhasználói élményt nyújtanak. Cosmos DB tárolt felhasználó a következő kódhoz hasonlóan fog kinézni:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

A post így néz ki:

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

Ha egy szerkesztés olyankor lép fel, ahol a rendszer a darab attribútumot érinti, könnyedén megtalálhatja az érintett dokumentumokat. Csak az indexelt attribútumokra mutató lekérdezéseket használja, például: `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"` , majd frissítse az adattömböket.

## <a name="the-search-box"></a>A keresőmező

A felhasználók Szerencsére nagy mennyiségű tartalmat hoznak. Továbbá képesnek kell lennie olyan tartalmak keresésére és keresésére, amelyek esetleg nem közvetlenül a tartalom-adatfolyamokban vannak, például azért, mert nem követi az alkotókat, vagy ha csak azt próbálta megkeresni, hogy a régi közzététel hat hónappal ezelőtt történt.

Mivel Azure Cosmos DB használ, a keresőmotort könnyedén megvalósíthatja az [Azure Cognitive Search](https://azure.microsoft.com/services/search/) használatával néhány percen belül anélkül, hogy bármilyen kódot kellene beírnia, a keresési folyamattól és a felhasználói felülettől függetlenül.

Miért ilyen egyszerű a folyamat?

Az Azure Cognitive Search implementálja az [Indexelő](/rest/api/searchservice/Indexer-operations), a háttérben futó folyamatokat, amelyek az adattárakat csatlakoztatják, és automatikusan felveszik, frissítik vagy eltávolítják az objektumokat az indexekben. Támogatják a [Azure SQL Database indexelő](/archive/blogs/kaevans/indexing-azure-sql-database-with-azure-search), az [Azure-Blobok indexelő](../search/search-howto-indexing-azure-blob-storage.md) és a szerencsére [Azure Cosmos db indexelő](../search/search-howto-index-cosmosdb.md)használatát. Az adatok Cosmos DBból az Cognitive Search Azure-ba való átállása egyszerű. Mindkét technológia JSON formátumban tárolja az adatokat, ezért csak [létre kell hoznia az indexet](../search/search-what-is-an-index.md) , és le kell képeznie az indexelni kívánt dokumentumok attribútumait. Készen is van. Az adatai méretétől függően az összes tartalom elérhető lesz a Felhőbeli infrastruktúrában a legjobb keresési megoldás, amelyet perceken belül megkeres.

Az Azure Cognitive Search-ról további információt talál a [Stoppoló útmutatójában](/archive/blogs/mvpawardprogram/a-hitchhikers-guide-to-search).

## <a name="the-underlying-knowledge"></a>A mögöttes tudás

Az összes, a napról napra növekedő és növekedő tartalom tárolását követően érdemes meggondolni, hogy mit tehetek az összes, a felhasználóktól származó információ streamtel?

A válasz egyszerű: tedd a munkát, és tanuljon belőle.

De mit tud tanulni? Néhány egyszerű példa az [érzelmek elemzésére](https://en.wikipedia.org/wiki/Sentiment_analysis), a tartalomra vonatkozó ajánlásokra a felhasználó preferenciái alapján, vagy akár egy automatizált tartalmi moderátor, amely biztosítja, hogy a közösségi hálózat által közzétett tartalom biztonságos legyen a család számára.

Most, hogy megkaptam a hurkot, valószínűleg úgy gondolja, hogy szüksége van néhány PhD-re a matematika-tudományban, hogy kinyerje ezeket a mintákat és információkat az egyszerű adatbázisokból és fájlokból, de helytelen lenne.

[Azure Machine learning](https://azure.microsoft.com/services/machine-learning/)a [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)része, egy teljes körűen felügyelt felhőalapú szolgáltatás, amely lehetővé teszi, hogy az algoritmusokat használó munkafolyamatokat hozzon létre egy egyszerű, fogd és vidd felületen, kód formájában saját algoritmusokat az [R](https://en.wikipedia.org/wiki/R_\(programming_language\))-ben, vagy használja a már létrehozott és használatra kész api-kat (például: [text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), Content moderator vagy [javaslatok](https://gallery.azure.ai/Solution/Recommendations-Solution)).

Ezen Machine Learning forgatókönyvek eléréséhez használhatja a [Azure Data Laket](https://azure.microsoft.com/services/data-lake-store/) az információk különböző forrásokból való betöltéséhez. A [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) használatával feldolgozhatja az adatokat, és létrehozhat egy Azure Machine learning által feldolgozható kimenetet.

Egy másik lehetőség az [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) használata a felhasználók tartalmának elemzéséhez; nem csupán jobban megértette őket (az [text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)-val írt adatok elemzésével), de a nemkívánatos vagy érett tartalmat is felderítheti, és ennek megfelelően járhat el a [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services számos olyan beépített megoldást tartalmaz, amelyek nem igényelnek semmilyen Machine Learning ismeretet.

## <a name="a-planet-scale-social-experience"></a>Egy bolygó-méretezési közösségi felület

Egy utolsó, de nem utolsósorban fontos cikknek kell foglalkoznia: **méretezhetőséggel**. Architektúra tervezésekor minden összetevőnek saját méretezéssel kell rendelkeznie. Végül további adatfeldolgozásra van szükség, vagy nagyobb földrajzi lefedettséget szeretne biztosítani. Szerencsére mindkét feladat megvalósítása **kulcsrakész élményt** nyújt a Cosmos db.

Cosmos DB támogatja a dinamikus particionálást. Egy adott **partíciós kulcs** alapján automatikusan létrehozza a partíciókat, amelyek a dokumentumok attribútumként vannak meghatározva. A megfelelő partíciós kulcs definiálását a tervezéskor kell elvégezni. További információ: [particionálás Azure Cosmos DBban](partitioning-overview.md).

A közösségi élmény érdekében a particionálási stratégiát úgy kell összehangolni, ahogy lekérdezést és írást végez. (Például az azonos partíción belüli olvasások kívánatosak, és a "gyors elérésű pontok" elkerülése érdekében az írásokat több partícióra terjeszti.) Néhány lehetőség: a partíciók egy időbeli kulcs (nap/hónap/hét) alapján, a tartalom kategóriája, földrajzi régió vagy felhasználó szerint. Valójában attól függ, hogyan kérdezi le az adatait, és hogyan jeleníti meg az adatait a közösségi felhasználói felületén.

Cosmos DB az összes partíció transzparens módon futtatja a lekérdezéseket (beleértve az [összesítéseket](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)is), így nem kell semmilyen logikát felvennie az adatmennyiség növekedésével.

Az idő múlásával végül növekedni fog a forgalom, és az erőforrás-fogyasztás (az [RUs](request-units.md)-ben vagy a kérelmek egységében mérve) nő. A felhasználói alapszintű növekedéshez gyakrabban kell olvasnia és írnia. A felhasználói alap több tartalom létrehozását és olvasását is megkezdi. Így létfontosságú az **átviteli sebesség skálázása** . Az RUs növelése egyszerű. Ezt elvégezheti néhány kattintással a Azure Portal vagy az API-n [keresztüli parancsok kiadásával](/rest/api/cosmos-db/replace-an-offer).

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="Partíciós kulcs skálázása és definiálása":::

Mi történik, ha a dolgok egyre jobbak maradnak? Tegyük fel, hogy egy másik régióból, országból vagy kontinensből származó felhasználók észlelik a platformot, és elkezdik használni azt. Mi a nagyszerű meglepetés!

De várjon! Hamarosan tisztában lesz azzal, hogy a platform nem optimális. Így távol vannak az operatív régiótól, hogy a késés borzasztó. Nyilvánvalóan nem szeretné, hogy kilépjenek. Ha csak egyszerűen **kiterjesztheti a globális elérhetőségét**? Van!

Cosmos DB lehetővé teszi [az adatok globális és átlátható replikálását](../cosmos-db/tutorial-global-distribution-sql-api.md) néhány kattintással, és automatikusan kiválaszthatja az [ügyfél kódjából](../cosmos-db/tutorial-global-distribution-sql-api.md)elérhető régiók közül. Ez a folyamat azt is jelenti, hogy [több feladatátvételi régióval](high-availability.md)is rendelkezhet.

Amikor globálisan replikálja az adatait, meg kell győződnie arról, hogy az ügyfelek kihasználhatják. Ha webes felületet használ vagy API-kat fér hozzá a mobil ügyfelektől, üzembe helyezheti az [Azure Traffic Managert](https://azure.microsoft.com/services/traffic-manager/) , és megnyithatja a Azure app Servicet az összes kívánt régióban, a teljesítmény-konfigurációval, hogy támogassa a kiterjesztett globális lefedettséget. Amikor az ügyfelek hozzáférnek a előtérben vagy az API-khoz, a rendszer a legközelebbi App Service irányítja, amely viszont a helyi Cosmos DB replikához fog csatlakozni.

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="Globális lefedettség hozzáadása a közösségi platformhoz" border="false":::

## <a name="conclusion"></a>Összegzés

Ez a cikk néhány fényt mutat be a közösségi hálózatok teljes körű létrehozására az Azure-ban alacsony díjszabású szolgáltatásokkal. Ennek eredményeképpen a többrétegű tárolási megoldások és a "ladder" elnevezésű adateloszlás használatát ösztönözheti.

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Az Azure-szolgáltatások közösségi hálózatkezeléssel való interakciójának ábrája" border="false":::

Az igazság az, hogy az ilyen forgatókönyvek esetében nincs ilyen típusú ezüst-felsorolásjel. Ez a nagyszerű szolgáltatások kombinációja által létrehozott szinergia, amely lehetővé teszi számunkra, hogy nagyszerű tapasztalatokat építsünk ki: a Azure Cosmos DB gyorsasága és szabadsága, hogy kiváló közösségi alkalmazást, az intelligenciát egy olyan első osztályú keresési megoldás mögött, mint az Azure Cognitive Search, a rugalmasság az Azure-App Services, hogy még a nem egyenletes nyelvezetű alkalmazásokat, de hatékony háttér-folyamatokat, valamint a bővíthető Azure Storage-t és Azure SQL Database nagy mennyiségű adat tárolásához és az Azure Machine analitikai teljesítményének biztosításához. Tanuljon olyan tudást és intelligenciát létrehozni, amely visszajelzést tud adni a folyamatokról, és segít a megfelelő tartalomnak a megfelelő felhasználók számára történő továbbításában.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Cosmos DB használatáról, tekintse meg az [általános Cosmos db használati esetek](use-cases.md)című témakört.