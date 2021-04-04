---
title: Bérlős és tartalom elkülönítése
titleSuffix: Azure Cognitive Search
description: Ismerje meg a több-bérlős SaaS-alkalmazások általános kialakítási mintáit az Azure Cognitive Search használata során.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: cd21197d6d1559b681ae622b974f6eb7ba95ad3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91397368"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Tervezési minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Cognitive Search

Egy több-bérlős alkalmazás az egyik, amely ugyanazokat a szolgáltatásokat és képességeket biztosítja a bérlők számára, akik nem láthatják és nem oszthatják meg más bérlők információit. Ez a dokumentum az Azure Cognitive Search-mel létrehozott több-bérlős alkalmazások bérlői elkülönítési stratégiáit tárgyalja.

## <a name="azure-cognitive-search-concepts"></a>Az Azure Cognitive Search fogalmak
A szolgáltatásként nyújtott keresési megoldásként az [Azure Cognitive Search](search-what-is-azure-search.md) lehetővé teszi, hogy a fejlesztők gazdag keresési funkciókat adjanak az alkalmazásokhoz anélkül, hogy bármilyen infrastruktúrát kellene kezelniük, vagy az adatok lekérése nélkül kellene foglalkoznia. A szolgáltatás feltölti az adattárat, majd a felhőben tárolja azokat. Az Azure Cognitive Search API-hoz való egyszerű kérések használatával az adatai módosíthatók és kereshetők. 

### <a name="search-services-indexes-fields-and-documents"></a>Szolgáltatások, indexek, mezők és dokumentumok keresése

A tervezési minták megtárgyalása előtt fontos megérteni néhány alapvető fogalmat.

Az Azure Cognitive Search használatakor az egyik a *keresési szolgáltatásra* van előfizetve. Az Azure Cognitive Searchba való feltöltéskor a rendszer a keresési szolgáltatásban található *indexben* tárolja azt. Egy szolgáltatáson belül több index is lehet. Az adatbázisok ismerős fogalmait használva a keresési szolgáltatás egy adatbázishoz hasonlítható, míg a szolgáltatásban lévő indexek az adatbázisban lévő táblákhoz is hasonlóvá lehetnek.

A keresési szolgáltatásban található minden egyes index saját sémával rendelkezik, amelyet számos testreszabható *mező* határoz meg. Az Azure Cognitive Search indexbe kerülnek az egyes *dokumentumok* formájában. Minden dokumentumot fel kell tölteni egy adott indexbe, és hozzá kell férnie az index sémájának. Amikor az Azure Cognitive Search használatával keres adatokat, a teljes szöveges keresési lekérdezések egy adott indexre lesznek kiadva.  Ha össze szeretné hasonlítani ezeket a fogalmakat egy adatbázishoz, a mezőket a táblázat oszlopaihoz lehet hasonlítani, és a dokumentumok a sorokhoz is összehasonlíthatók.

### <a name="scalability"></a>Méretezhetőség
A standard szintű [díjszabásban](https://azure.microsoft.com/pricing/details/search/) szereplő Azure Cognitive Search-szolgáltatások két dimenzióban méretezhetők: tárterület és rendelkezésre állás.

* A *partíciók* hozzáadhatók a keresési szolgáltatás tárterületének növeléséhez.
* A *replikák* hozzáadhatók a szolgáltatásokhoz, így növelhetik a keresési szolgáltatás által kezelhető kérelmek átviteli sebességét.

A partíciók és replikák hozzáadásával és eltávolításával lehetővé válik a keresési szolgáltatás kapacitása, hogy az adatok mennyisége és az alkalmazás által igényelt forgalom növekedni fog. Ahhoz, hogy egy keresési szolgáltatás elér egy olvasási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-t, két replikára van szükség. Ahhoz, hogy egy szolgáltatás olvasási és írási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-t érjen el, három replikára van szükség.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Szolgáltatások és indexek korlátai az Azure Cognitive Search
Az Azure Cognitive Search különböző [díjszabási szintjei](https://azure.microsoft.com/pricing/details/search/) vannak, és a rétegek mindegyike különböző [korlátozásokkal és kvótákkal](search-limits-quotas-capacity.md)rendelkezik. A korlátozások némelyike a szolgáltatás szintjén van, néhány pedig az index szintjén, néhány pedig a partíció szintjén.

|  | Alapszintű | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Replikák maximális száma szolgáltatás szerint** |3 |12 |12 |12 |12 |
| **Partíciók maximális száma szolgáltatás szerint** |1 |12 |12 |12 |3 |
| **Keresési egységek (replikák * partíciók) maximális száma szolgáltatásban** |3 |36 |36 |36 |36 (legfeljebb 3 partíció) |
| **Tárterület maximális száma szolgáltatás alapján** |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| **Tárolók maximális száma partíción** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Indexek maximális száma (szolgáltatás)** |5 |50 |200 |200 |3000 (max. 1000 indexek/partíció) |

#### <a name="s3-high-density"></a>Nagy sűrűségű S3
Az Azure Cognitive Search S3-as díjszabási csomagjában lehetőség van a nagy sűrűségű (HD) üzemmódra, amely kifejezetten több-bérlős forgatókönyvekhez készült. Sok esetben az egyszerűség és a költséghatékonyság előnyeinek elérése érdekében számos kisebb bérlőt kell támogatni egyetlen szolgáltatásban.

Az S3 HD lehetővé teszi, hogy a sok kisméretű indexet egyetlen keresési szolgáltatás felügyelete alá lehessen csomagolni azáltal, hogy a partíciók használatával kibővíti az indexeket, így több indexet is tárolhat egyetlen szolgáltatásban.

Az S3 szolgáltatás úgy lett kialakítva, hogy rögzített számú indexet működtessen (legfeljebb 200), és lehetővé teszi, hogy az egyes indexek horizontálisan méretezhetők legyenek, mivel új partíciókat adnak hozzá a szolgáltatáshoz. A partíciók S3 HD-szolgáltatásokhoz való hozzáadása növeli a szolgáltatás által üzemeltethető indexek maximális számát. Az egyes S3HD-indexek ideális maximális mérete 50 – 80 GB, de a rendszer által kiszabott egyes indexek esetében nem áll rendelkezésre rögzített méretkorlát.

## <a name="considerations-for-multitenant-applications"></a>Több-bérlős alkalmazások szempontjai
A több-bérlős alkalmazásoknak hatékonyan kell kiosztaniuk az erőforrásokat a bérlők között, miközben a különböző bérlők között bizonyos fokú adatvédelemre van szükség. Az ilyen alkalmazások architektúrájának tervezésekor néhány szempontot figyelembe kell venni:

* *Bérlő elkülönítése:* Az alkalmazás fejlesztőknek megfelelő intézkedéseket kell tenniük annak biztosításához, hogy a bérlők ne legyenek jogosulatlan vagy nemkívánatos hozzáférésük más bérlők adatához. Az adatvédelem szempontjából a bérlők elkülönítési stratégiái a megosztott erőforrások hatékony kezelését és a zajos szomszédok elleni védelmet igénylik.
* *Felhőalapú erőforrás díja:* A többi alkalmazáshoz hasonlóan a szoftveres megoldásoknak is versenyképesnek kell lenniük a több-bérlős alkalmazások összetevőjeként.
* *Egyszerű műveletek:* Több-bérlős architektúra fejlesztésekor fontos szempont az alkalmazás működésére és összetettségére gyakorolt hatás. Az Azure Cognitive Search rendelkezik [99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-os SLA-val.
* *Globális helyigény:* Előfordulhat, hogy a több-bérlős alkalmazásoknak hatékonyan kell kiszolgálni a világ bármely részén elosztott bérlőket.
* *Méretezhetőség:* Az alkalmazás-fejlesztőknek meg kell fontolniuk, hogyan egyeztethetők össze az alkalmazások kellően alacsony szintjének fenntartása és az alkalmazás megtervezése között a bérlők számával, valamint a bérlők adatai és a számítási feladatok méretétől függően.

Az Azure Cognitive Search néhány olyan határt kínál, amely a bérlők adatai és a számítási feladatok elkülönítésére használható.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Bérlős modellezése az Azure Cognitive Search
Több-bérlős forgatókönyv esetén az alkalmazás fejlesztői egy vagy több keresési szolgáltatást használnak, és a bérlőket a szolgáltatások, az indexek vagy mindkettő között osztják el. Az Azure Cognitive Search néhány gyakori mintát tartalmaz a több-bérlős forgatókönyv modellezéséhez:

1. *Index/bérlő:* Minden bérlő saját indexet tartalmaz a más Bérlővel megosztott keresési szolgáltatáson belül.
2. *Szolgáltatás/bérlő:* Mindegyik bérlő rendelkezik saját dedikált Azure Cognitive Search szolgáltatással, amely a legmagasabb szintű adatmennyiséget és a munkaterhelések elkülönítését kínálja.
3. *Kettő* kombinációja: Nagyobb, aktívabb bérlők dedikált szolgáltatásokat kapnak, míg a kisebb bérlők egyedi indexeket kapnak a megosztott szolgáltatásokon belül.

## <a name="1-index-per-tenant"></a>1. index/bérlő

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="Az index/bérlői modell ábrázolása" border="false":::

Egy index/bérlői modellben több bérlő is foglal el egyetlen Azure Cognitive Search szolgáltatást, ahol mindegyik bérlő saját indextel rendelkezik.

A bérlők az adatok elkülönítését érik el, mivel minden keresési kérelem és dokumentum-művelet az Azure Cognitive Search index szintjén lett kiadva. Az alkalmazási rétegben arra van szükség, hogy a különböző bérlők forgalmát a megfelelő indexekre irányítsa, miközben a szolgáltatási szinten lévő erőforrásokat is az összes bérlőn keresztül kezeli.

Az index/bérlői modell egyik fő attribútuma az, hogy az alkalmazás fejlesztője előfizesse a keresési szolgáltatás kapacitását az alkalmazás bérlői között. Ha a bérlők a számítási feladatok egyenlőtlen eloszlásával rendelkeznek, a bérlők optimális kombinációja a keresési szolgáltatás indexei között terjeszthető ki, így számos igen aktív, erőforrás-igényes bérlőt használhat, miközben hosszabb idő alatt kevesebb aktív bérlőt szolgál ki. A kikapcsolás a modell nem képes olyan helyzetek kezelésére, amikor az egyes bérlők egyidejűleg igen aktívak.

Az index/bérlői modell egy változó cost-modell alapját képezi, ahol a teljes Azure Cognitive Search szolgáltatás megvásárolta az előfizetést, majd később kitölti a bérlőket. Ez lehetővé teszi, hogy a fel nem használt kapacitás kijelölhető legyen a próbaverziók és az ingyenes fiókok számára.

Globális helyigényű alkalmazások esetében előfordulhat, hogy az index/bérlői modell nem a leghatékonyabb. Ha egy alkalmazás bérlői szét vannak osztva a világ bármely régiójában, külön szolgáltatásra lehet szükség az egyes régiók esetében, amelyek mindegyike megismétli a költségeket.

Az Azure Cognitive Search lehetővé teszi az egyéni indexek és a növekedő indexek teljes számának méretezését. Ha a megfelelő árképzési szintet választja, akkor a partíciók és a replikák a teljes keresési szolgáltatásba felvehetők, ha a szolgáltatáson belüli egyes indexek túl nagy mennyiségű tárterületet vagy forgalmat foglalnak magukban.

Ha az indexek teljes száma túl nagyra nő egyetlen szolgáltatás esetében, egy másik szolgáltatást kell kiépíteni az új bérlők befogadásához. Ha az indexeket új szolgáltatások hozzáadásakor kell áthelyezni a keresési szolgáltatások között, az indexből származó adatoknak kézzel kell átmásolnia az egyik indexből a másikba, mivel az Azure Cognitive Search nem engedélyezi az index áthelyezését.

## <a name="2-service-per-tenant"></a>2. szolgáltatás/bérlő

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="A szolgáltatás/bérlői modell ábrázolása" border="false":::

A szolgáltatás-bérlői architektúrában minden bérlő saját keresési szolgáltatással rendelkezik.

Ebben a modellben az alkalmazás a bérlők számára a maximális elkülönítési szintet éri el. Minden szolgáltatás dedikált tárterületet és átviteli sebességet biztosít a keresési kérések kezelésére, valamint különálló API-kulcsokra.

Azokban az alkalmazásokban, amelyekben az egyes bérlők nagy helyigénysel rendelkeznek, vagy a munkaterhelésnek a bérlőtől a bérlőig kevés a változékonysága, a szolgáltatás-bérlői modell olyan hatékony megoldás, mint a különböző bérlők munkaterhelései között.

A bérlői modelleken alapuló szolgáltatás a kiszámítható és rögzített költségtakarékos modell előnyeit is biztosítja. A teljes keresési szolgáltatásban nincs kezdeti beruházás, amíg a bérlő kitölti azt, azonban a bérlői díj magasabb, mint az index-bérlői modell.

A szolgáltatás/bérlő modell hatékony választás a globális helyigényű alkalmazások számára. Földrajzilag elosztott bérlők esetében a bérlők szolgáltatásait egyszerűen megoszthatja a megfelelő régióban.

A minta skálázásának kihívásai akkor jelentkeznek, amikor az egyes bérlők kibővítik a szolgáltatást. Az Azure Cognitive Search jelenleg nem támogatja a keresési szolgáltatás díjszabási szintjét, így az összes adatmennyiséget manuálisan át kell másolni egy új szolgáltatásba.

## <a name="3-mixing-both-models"></a>3. mindkét modell keverése
A modellezési bérlős egy másik mintázata a bérlői és a szolgáltatás-bérlői stratégiák keveredését is felkeveri.

A két minta összekeverésével az alkalmazás legnagyobb bérlői dedikált szolgáltatásokat foglalhatnak el, míg a kevésbé aktív, kisebb bérlők egy megosztott szolgáltatásban foglalhatnak le indexeket. Ez a modell biztosítja, hogy a legnagyobb bérlők következetesen nagy teljesítményt biztosítanak a szolgáltatástól, miközben a kisebb bérlők számára a zajos szomszédoktól való védekezést segítik.

Ennek a stratégiának a megvalósítása azonban előrelátó módon feltételezi, hogy mely bérlők igényelnek dedikált szolgáltatást, illetve egy megosztott szolgáltatásbeli indexet. Az alkalmazások összetettsége a két bérlős-modell kezelésének szükségességével nő.

## <a name="achieving-even-finer-granularity"></a>Még finomabb részletesség elérése
A fenti tervezési minták a több-bérlős forgatókönyvek Azure-beli modellezésére Cognitive Search feltételezik, hogy az egyes bérlők egy adott alkalmazás teljes példányai. Az alkalmazások azonban néha több kisebb hatókört is kezelhetnek.

Ha a szolgáltatás-/bérlői és az index/bérlői modellek nem eléggé kis hatókörű, az index modellezése még finomabb részletességi fokú részletességgel valósítható meg.

Ahhoz, hogy egyetlen index eltérő módon viselkedjen a különböző ügyféloldali végpontok esetében, egy mező hozzáadható egy olyan indexhez, amely minden lehetséges ügyfél esetében egy bizonyos értéket jelöl ki. Minden alkalommal, amikor egy ügyfél meghívja az Azure Cognitive Search egy index lekérdezésére vagy módosítására, az ügyfélalkalmazás kódja megadja az adott mező megfelelő értékét az Azure Cognitive Search [szűrő](./query-odata-filter-orderby-syntax.md) funkciójának lekérdezési időpontra való használatakor.

Ezzel a módszerrel különböző felhasználói fiókok, különálló jogosultsági szintek és akár teljesen különálló alkalmazások funkciói is elérhetők.

> [!NOTE]
> A fent ismertetett módszer használatával egyetlen indexet állíthat be több bérlő kiszolgálására, ami befolyásolja a keresési eredmények relevanciáját. A keresés relevanciás pontszámait index-szintű hatókörre számítjuk ki, nem pedig a bérlői szintű hatókörre, így az összes bérlő adatai bekerülnek a releváns pontszámok alapjául szolgáló statisztikai adatokba, például a kifejezés gyakoriságával.
> 
> 

## <a name="next-steps"></a>Következő lépések
Az Azure Cognitive Search számos alkalmazás számára meggyőző megoldás. A több-bérlős alkalmazások különböző tervezési mintáinak kiértékelése során vegye figyelembe a [különböző díjszabási](https://azure.microsoft.com/pricing/details/search/) csomagokat és a megfelelő [szolgáltatási korlátokat](search-limits-quotas-capacity.md) a legmegfelelőbb Azure-Cognitive Search az alkalmazások számítási feladatainak és architektúráinak méretének megfelelően.

Az Azure Cognitive Search és a több-bérlős forgatókönyvekkel kapcsolatos bármilyen kérdés a következő lehet: azuresearch_contact@microsoft.com .