---
title: A költségek megbecslése
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan kezelheti a számlázható eseményeket, a díjszabási modellt, valamint a Cognitive Search szolgáltatás futtatásának költségeit.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: e2ab7a007a92bd2306a7a97781af5653f932d0b7
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580606"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search-szolgáltatás költségeinek becslése és kezelése

Ebben a cikkben megismerheti a díjszabási modellt, a számlázható eseményeket, valamint az Azure Cognitive Search szolgáltatás futtatásának költségét kezelő tippeket.

## <a name="pricing-model"></a>Díjszabási modell

Az Azure Cognitive Search skálázhatósági architektúrája a replikák és partíciók rugalmas kombinációján alapul, így a kapacitás attól függően változhat, hogy több lekérdezésre vagy indexelésre van szüksége, és csak azért kell fizetnie, amire szüksége van.

A keresési szolgáltatás által használt erőforrások mennyisége, a szolgáltatási szinten meghatározott számlázási aránysal megszorozva meghatározza a szolgáltatás futtatásának költségeit. A költségek és a kapacitás szorosan kötődik. A költségek becslése során az indexelés és a lekérdezési feladatok futtatásához szükséges kapacitás megismerése a legjobb ötlet, hogy a tervezett költségek milyenek lesznek.

Számlázási célokra két egyszerű képletet kell figyelembe venni:

| Képlet | Description |
|---------|-------------|
| **R x P = SU** | A használt replikák száma, a felhasznált partíciók számával szorozva a szolgáltatás által használt *keresési egységek* (su) mennyiségével egyenlő. A SU erőforrás-egység, amely lehet partíció vagy replika is. |
| **SU * számlázási arány = havi ráfordítás** | A teljes havi számla elsődleges tényezője, hogy az SUs száma megszorozza a szolgáltatás kiépített szintjének számlázási arányával. Bizonyos funkciók vagy munkaterhelések más Azure-szolgáltatások függőségeivel is rendelkeznek, ami növelheti a megoldás költségeit az előfizetés szintjén. Az alábbi számlázható események szakasz azokat a funkciókat azonosítja, amelyek hozzáadhatók a számlához. |

Minden szolgáltatás egy SU-val kezdődik (az egyik replikát egy partícióval szorozva) a minimális értékkel. Bármely szolgáltatás esetében a maximális érték 36 SUs. A maximális érték több módon is elérhető: 6 partíció x 6 replika vagy 3 partíció x 12 replika, például. Gyakori, hogy a teljes kapacitásnál kevesebbet használ (például egy 3 replika, 3 partíciós szolgáltatás, amely 9 SUs-ként lett kiszámlázva). Tekintse meg a [partíciós és replika kombinációk](search-capacity-planning.md#chart) diagramot az érvényes kombinációk esetében.

A számlázási díj óradíja/SU. Az egyes szintek fokozatosan magasabb arányban jelennek meg. A magasabb szintek nagyobb és gyorsabb partíciókkal rendelkeznek, és ez az adott szinten a legmagasabb szintű óradíjat is segíti. Az egyes szintek díjszabását a [díjszabás részletei](https://azure.microsoft.com/pricing/details/search/) lapon tekintheti meg.

A legtöbb ügyfél csak egy részét a teljes kapacitás online állapotba helyezi, a többi pedig tartalékban tartja. A számlázáshoz a SU-képlet alapján kiszámított partíciók és replikák száma határozza meg, hogy mit fizet óránként. 

## <a name="billable-events"></a>Számlázható események

Az Azure Cognitive Search-ra épülő megoldások a következő módokon vehetik igénybe a költségeket:

+ Magának [a szolgáltatásnak a díja](#service-costs) , a nonstop futtatása minimális konfigurációval (egy partíció és egy replika) az alaparányban. Ezt a szolgáltatás futtatásának fix díjaként tekintheti át.

+ Kapacitás (replikák vagy partíciók) hozzáadása, ahol a költségek növekednek a számlázható díj növekményei között. Ha a magas rendelkezésre állás üzleti követelmény, 3 replikára lesz szüksége.

+ Sávszélességgel kapcsolatos díjak (kimenő adatforgalom)

+ Adott funkciókhoz vagy szolgáltatásokhoz szükséges kiegészítő szolgáltatások:

  + AI-gazdagodás ( [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)szükséges)
  + Knowledge Store ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)szükséges)
  + növekményes gazdagodás (az [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)-t igényli, az AI-bővítésre vonatkozik)
  + ügyfél által felügyelt kulcsok és kettős titkosítás ( [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)szükséges)
  + privát végpontok nem Internet-hozzáférési modellhez ( [Azure Private-hivatkozást](https://azure.microsoft.com/pricing/details/private-link/)igényel)

### <a name="service-costs"></a>Szolgáltatás költségei

A virtuális gépekkel vagy más, a díjak elkerülésére szolgáló egyéb erőforrásokkal ellentétben az Azure Cognitive Search szolgáltatás mindig a kizárólagos használatra dedikált hardveren érhető el. Ennek megfelelően a szolgáltatás létrehozása olyan számlázható esemény, amely a szolgáltatás létrehozásakor kezdődik, és a szolgáltatás törlésekor lejár. 

A minimális díj az első keresési egység (az egyik replika x egy partíció) a számlázható sebességgel. Ez a minimális érték a szolgáltatás élettartamára van javítva, mert a szolgáltatás nem futhat ennél a konfigurációnál kisebb mértékben. 

A minimumon túl egymástól függetlenül is hozzáadhat replikákat és partíciókat. A kapacitás replikák és partíciók révén történő növekményes növekedése a következő képlet alapján növeli a számla mennyiségét: **(replikák x Partitions x számlázási arány)**, ahol a felszámított díj a kiválasztott árképzési szinttől függ.

Ha megbecsüli egy keresési megoldás költségét, ne feledje, hogy a díjszabás és a kapacitás nem lineáris (a kapacitás megkettőzése több, mint az ugyanazon a szinten megjelenő költség). Emellett egy magasabb szintű rendszerre való áttérés nagyobb és gyorsabb teljesítményt biztosít, nagyjából ugyanazon az áron. További információkat és példákat a [standard S2-es szintre való frissítéssel](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier)foglalkozó témakörben talál.

### <a name="bandwidth-charges"></a>Sávszélességgel kapcsolatos díjak

Az [Indexelő](search-indexer-overview.md) használata hatással lehet a számlázásra, ha az Azure-adatforrás az Azure Cognitive Searchtól eltérő régióban található. Ebben az esetben a kimenő adatok Azure-adatforrásból az Azure-Cognitive Searchba való áthelyezésének díja lehet. Részletekért tekintse meg az Azure-beli adatplatform díjszabási oldalát.

Ha az Azure Cognitive Search szolgáltatást ugyanabban a régióban hozza létre, mint az adatait, teljes mértékben kiküszöbölheti az adatforgalom díját. Íme néhány információ a sávszélesség- [díjszabási lapról](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Bejövő adatforgalom: a Microsoft nem számít fel díjat semmilyen, az Azure-beli szolgáltatásba beérkező adatforgalomért. 

+ Kimenő adatforgalom: a kimenő adatforgalom a lekérdezési eredményekre hivatkozik. Cognitive Search a kimenő adatokért nem számítunk fel díjat, de az Azure-beli kimenő díjak akkor is lehetségesek, ha a szolgáltatások különböző régiókban találhatók.

  Ezek a díjak valójában nem részei az Azure Cognitive Search-számlájának. Itt említik, mert ha más régióknak vagy nem Azure-alkalmazásoknak küldi az eredményeket, láthatja, hogy ezek a költségek a teljes számlán szerepelnek.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-gazdagítás Cognitive Services

Az [AI](cognitive-search-concept-intro.md)-bővítés esetében érdemes megtervezni, hogy [egy számlázható Azure Cognitive Services-erőforrást](cognitive-search-attach-cognitive-services.md), ugyanabban a régióban, mint az Azure Cognitive Searcht, az utólagos elszámolású feldolgozás S0 díjszabási szintjére kell terveznie. Nincs rögzített díj a Cognitive Services csatolásával kapcsolatban. Csak a szükséges feldolgozásért kell fizetnie.

| Művelet | Számlázási hatás |
|-----------|----------------|
| Dokumentum repedése, szöveg kinyerése | Ingyenes |
| A dokumentumok repedése, a képek kinyerése | A számlázás a dokumentumokból kinyert képek száma alapján történik. Az [Indexelő konfigurációjában](/rest/api/searchservice/create-indexer#indexer-parameters)a **imageAction** az a paraméter, amely a képek kinyerését váltja ki. Ha a **imageAction** értéke "None" (alapértelmezett), nem számítunk fel díjat a képek kinyeréséhez. A képek kinyerésének díja az Azure Cognitive Search [díjszabási részleteit ismertető](https://azure.microsoft.com/pricing/details/search/) oldalon található.|
| [Beépített kognitív képességek](cognitive-search-predefined-skills.md) | A számlázás ugyanolyan sebességgel történik, mint ha a feladatot a Cognitive Services közvetlen használatával végezte el. |
| Egyéni készségek | Az egyéni képességek az Ön által megadott funkciók. Az egyéni képességek használatának díja teljes mértékben attól függ, hogy az egyéni kód más mért szolgáltatásokat hív-e meg. |

A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) funkció lehetővé teszi egy olyan gyorsítótár megadását, amely lehetővé teszi, hogy az indexelő hatékonyabban fusson, és csak azokat a kognitív képességeket futtassa, amelyekre szükség van, ha később módosítja a készségkészlet, így időt és pénzt takarít meg.

## <a name="tips-for-managing-costs"></a>Tippek a költségek kezeléséhez

Az alábbi útmutató segítségével csökkentheti a költségeket, vagy hatékonyabban kezelheti a költségeket:

+ Hozza létre az összes erőforrást ugyanabban a régióban, vagy a lehető legkevesebb régióban, hogy csökkentse vagy eltávolítsa a sávszélességgel kapcsolatos díjakat.

+ Összevonja az összes szolgáltatást egyetlen erőforráscsoport, például az Azure Cognitive Search, Cognitive Services és minden más, a megoldásban használt Azure-szolgáltatás között. A Azure Portal keresse meg az erőforráscsoportot, és használja a **Cost Management** -parancsokat, hogy betekintést kapjon a tényleges és a tervezett költségekbe.

+ Tekintse át az Azure-webalkalmazást az előtér-alkalmazáshoz, hogy a kérések és válaszok az adatközpont határain belül maradjanak.

+ Vertikális felskálázás az erőforrás-igényes műveletekhez, például az indexeléshez, majd a rendszeres lekérdezési feladatokhoz tartozó leállások újraigazítása. Az Azure Cognitive Search minimális konfigurációjának megkezdése (egy partícióból és egy replikából álló egy SU), majd a felhasználói tevékenység figyelése olyan használati minták azonosításához, amelyek nagyobb kapacitást jelezhetnek. Ha kiszámítható minta van, lehetséges, hogy szinkronizálni tudja a méretezést a tevékenységgel (ezt a kódot kell írnia az automatizáláshoz).

+ A Cost Management be van építve az Azure-infrastruktúrába. A költségek, az eszközök és az API-k nyomon követésével kapcsolatos további információkért tekintse át a [számlázási és a Cost Management](../cost-management-billing/cost-management-billing-overview.md) szolgáltatást.

A keresési szolgáltatás ideiglenes leállítása nem lehetséges. A dedikált erőforrások mindig működőképesek, és a szolgáltatás élettartama szempontjából kizárólagos használatra vannak kiosztva. A szolgáltatás törlése végleges, és a hozzá tartozó adatai is törlődnek.

A szolgáltatás szempontjából a számla csökkentése egyetlen módja annak, hogy csökkentse a replikákat és a partíciókat olyan szintre, amely továbbra is elfogadható teljesítményt és [SLA-megfelelőséget](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biztosít, vagy egy alacsonyabb szintű szolgáltatást hoz létre (az S1 óradíjak alacsonyabbak, mint az S2 vagy az S3). Feltételezve, hogy kiépíti a szolgáltatást a betöltési kivetítések alsó végére, ha kibővíti a szolgáltatást, létrehozhat egy második nagyobb rétegű szolgáltatást, újraépítheti az indexeket a második szolgáltatásban, majd törölheti az elsőt.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan figyelheti és kezelheti az Azure-előfizetések költségeit.

> [!div class="nextstepaction"]
> [Az Azure Cost Management and Billing dokumentációja](../cost-management-billing/cost-management-billing-overview.md)