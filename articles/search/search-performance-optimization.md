---
title: Teljesítményorientált skálázás
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Azure Cognitive Search teljesítményének finomhangolásához és az optimális méretezés konfigurálásához szükséges technikákat és ajánlott eljárásokat.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.custom: references_regions
ms.openlocfilehash: 60371888dbc4f0cbc33f1ad1b2a685dbb071c01a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670713"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Az Azure-Cognitive Search teljesítményének méretezése

Ez a cikk az ajánlott eljárásokat mutatja be a méretezhetőség és a rendelkezésre állás kifinomult követelményeit biztosító speciális forgatókönyvek esetében.

## <a name="start-with-baseline-numbers"></a>Kezdés alapszámokkal

A nagyobb üzembe helyezési erőfeszítések elvégzése előtt győződjön meg arról, hogy tudja, mi a lekérdezés átlagos terhelése. Az alábbi irányelvek segítséget nyújtanak az alapkonfiguráció-lekérdezési számok megérkezéséhez.

1. Válassza ki a cél késését (vagy a maximális időtartamot), amelyet egy tipikus keresési kérelem végrehajtásához el kell végezni.

1. Valós számítási feladatok létrehozása és tesztelése reális adatkészlettel a keresési szolgáltatáshoz a késési arányok méréséhez.

1. Kezdje a másodpercenkénti lekérdezések másodpercenkénti számát (QPS), majd fokozatosan növelje a teszt során végrehajtott számot, amíg a lekérdezés késése az előre definiált cél alá nem csökken. Ez egy fontos viszonyítási alap, amely segít a méretezés megtervezésében, mivel alkalmazása növekszik a használat során.

1. Ahol csak lehet, használja újra a HTTP-kapcsolatokat. Ha az Azure Cognitive Search .NET SDK-t használja, ez azt jelenti, hogy fel kell használni egy példány-vagy [SearchClient](/dotnet/api/azure.search.documents.searchclient) -példányt, és ha a REST API használja, akkor egyetlen HttpClient kell felhasználnia.

1. A lekérdezési kérések lényegének változása, hogy a Keresés az index különböző részein történjen. A változás azért fontos, mert ha folyamatosan hajtja végre ugyanazokat a keresési kéréseket, az adatgyorsítótárazás megkezdi, hogy a teljesítmény jobban nézzen ki, mint a több különböző lekérdezési készlet.

1. A lekérdezési kérelmek szerkezete változó, hogy különböző típusú lekérdezéseket kapjon. Nem minden keresési lekérdezés ugyanazon a szinten végezhető el. Például egy dokumentum keresési vagy keresési javaslata általában gyorsabb, mint egy jelentős számú dimenzióval és szűrőkkel rendelkező lekérdezés. A tesztelési összeállításnak különböző lekérdezéseket kell tartalmaznia, nagyjából azonos arányban, ahogy az éles környezetben várható.  

A tesztelési feladatok létrehozásakor az Azure Cognitive Search néhány jellemzője van:

+ A szolgáltatás túlterhelést okoz, ha egyszerre több keresési lekérdezést küld. Ebben az esetben a HTTP 503-válasz kódokat fogja látni. Ha a tesztelés során a 503-es számú keresési kérést szeretné elkerülni, a különböző keresési kérelmekkel kapcsolatos különbségeket a késési arányok között tekintheti meg.

+ Az Azure Cognitive Search nem futtat indexelési feladatokat a háttérben. Ha a szolgáltatás párhuzamosan kezeli a lekérdezési és indexelési feladatokat, ezt vegye figyelembe az indexelési feladatok bevezetésével a lekérdezési tesztekben, vagy az indexelési feladatok futtatásának időpontjában való futási lehetőségek feltárásával.

> [!Tip]
> A valós lekérdezési terhelést a Load Testing Tools használatával szimulálhatja. Próbálja meg [betölteni az Azure DevOps,](/azure/devops/test/load-test/get-started-simple-cloud-load-test) vagy használja az alábbi [alternatívák](/azure/devops/test/load-test/overview#alternatives)egyikét.

## <a name="scale-for-high-query-volume"></a>Méretezés nagy lekérdezési kötethez

A szolgáltatás túlterhelt, ha a lekérdezések túl sokáig tartanak, vagy amikor a szolgáltatás elkezdi a kérelmek eldobását. Ha ez történik, az alábbi két módszer egyikével kezelheti a problémát:

+ **Replikák hozzáadása**  

  Minden replika az adatai másolata, amely lehetővé teszi, hogy a szolgáltatás a kérelmeket több példányon is terheléselosztással ossza meg.  Az Azure Cognitive Search felügyeli az összes terheléselosztást és az adatreplikációt, és a szolgáltatáshoz lefoglalt replikák számát bármikor módosíthatja. Egy standard keresési szolgáltatásban legfeljebb 12 replikát, egy alapszintű keresési szolgáltatásban pedig 3 replikát foglalhat le. A replikák a [Azure Portal](search-create-service-portal.md) vagy a [PowerShell](search-manage-powershell.md)használatával módosíthatók.

+ **Új szolgáltatás létrehozása magasabb szintű szinten**  

  Az Azure Cognitive Search [számos szinttel](https://azure.microsoft.com/pricing/details/search/) rendelkezik, és mindegyik különböző szintű teljesítményt nyújt. Bizonyos esetekben előfordulhat, hogy annyi lekérdezése van, hogy az Ön által használt keret nem tud elegendő fordulatot biztosítani, még akkor is, ha a replikák maxed ki. Ebben az esetben érdemes áthelyezni egy magasabb teljesítményű szintet, például a standard S3 szintet, amelyet nagy mennyiségű dokumentummal és rendkívül nagy lekérdezési számítási feladatokkal rendelkező forgatókönyvekhez terveztek.

## <a name="scale-for-slow-individual-queries"></a>A lassú egyéni lekérdezések méretezése

A nagy késési arányok egy másik oka, hogy egy lekérdezés túl sok időt vesz igénybe. Ebben az esetben a replikák hozzáadása nem fog segíteni. Két lehetséges lehetőség, amely segíthet a következőkben:

+ **Partíciók bővítése**

  A partíciók feldarabolják az adategységeket A további számítástechnikai erőforrások között. A két partíció az adatmegosztást fél, a harmadik partíció pedig háromra osztja szét, és így tovább. Az egyik pozitív mellékhatás az, hogy a lassabb lekérdezések időnként gyorsabban futnak párhuzamos számítástechnika miatt. Megjegyezték, hogy az alacsony szelektivitású lekérdezések, például a sok dokumentumnak megfelelő lekérdezések, illetve a nagy számú dokumentumra vonatkozó párhuzamos. Mivel a dokumentumok relevanciájának kiszámításához, illetve a dokumentumok számának megszámlálásához jelentős számítási követelmény szükséges, a további partíciók hozzáadásával a lekérdezések gyorsabban elvégezhető.  
   
  A standard keresési szolgáltatásban legfeljebb 12 partíció lehet, az alapszintű keresési szolgáltatásban pedig 1 partíció található. A partíciók a [Azure Portal](search-create-service-portal.md) vagy a [PowerShell](search-manage-powershell.md)használatával módosíthatók.

+ **Magas fokú kardinális mezők korlátozása**

  A magas kardinális mező egy olyan, jól látható vagy szűrhető mezőből áll, amely jelentős számú egyedi értékkel rendelkezik, és ennek eredményeképpen jelentős erőforrásokat használ a számítási eredmények alapján. Például ha a termék-azonosító vagy a Description mező úgy van beállítva, hogy a felszűrhető/szűrhető, a dokumentum a dokumentumból származó értékek nagy részét egyedinek számítja. Ahol csak lehetséges, korlátozza a magas kardinális mezők számát.

+ **Keresési szintek emelése**  

  Akár egy magasabb szintű Azure Cognitive Search-szintet is használhat, így javíthatja a lassú lekérdezések teljesítményét. Minden magasabb szinten gyorsabb processzorok és több memória áll rendelkezésre, mindkettőnek pozitív hatása van a lekérdezési teljesítményre.

## <a name="scale-for-availability"></a>Méretezés a rendelkezésre álláshoz

A replikák nem csupán csökkentik a lekérdezés késését, de a magas rendelkezésre állást is lehetővé tehetik. Egyetlen replika esetén rendszeres állásidőt kell várnia, mert a kiszolgáló újraindul a szoftverfrissítések vagy egyéb karbantartási események után. Ennek eredményeképpen fontos figyelembe venni, hogy az alkalmazás magas rendelkezésre állást igényel-e a keresésekhez (lekérdezésekhez) és írásokhoz (az indexelési eseményekhez). Az Azure Cognitive Search SLA-beállításokat biztosít az összes fizetős keresési ajánlathoz a következő attribútumokkal:

+ Két replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)

+ Három vagy több replika az írási és olvasási feladatok (lekérdezések és indexelés) magas rendelkezésre állásához

További részletekért látogasson el az [Azure Cognitive Search szolgáltatói szerződésra](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Mivel a replikák az adatai másolatai, több replikát is biztosít az Azure Cognitive Search a számítógép újraindítását és karbantartását az egyik replikán, míg a lekérdezés végrehajtása más replikákban folytatódik. Ezzel szemben, ha elvégzi a replikákat, a lekérdezési teljesítmény romlása merül fel, feltéve, hogy ezek a replikák egy kihasználatlan erőforrás.

<a name="availability-zones"></a>

### <a name="availability-zones"></a>Rendelkezésre állási zónák

[Availability Zones](../availability-zones/az-overview.md) a régió adatközpontjait különálló fizikai hely csoportokba oszthatja, hogy magas rendelkezésre állást biztosítsanak ugyanazon a régión belül. Cognitive Search esetében az egyes replikák a zónák hozzárendelésének egységei. Egy keresési szolgáltatás egy régión belül fut; a replikái különböző zónákban futnak.

Availability Zones az Azure Cognitive Search használatával két vagy több replikát adhat hozzá a keresési szolgáltatáshoz. Minden replika a régión belül egy másik rendelkezésre állási zónába kerül. Ha Availability Zones több replikával rendelkezik, a replikák a lehető legegyenletesebb Availability Zones lesznek elosztva.

Az Azure Cognitive Search jelenleg a következő régiók egyikében létrehozott standard szintű vagy magasabb szintű keresési szolgáltatásokhoz Availability Zonest támogatja:

+ Kelet-Ausztrália (létrehozva: január 30., 2021 vagy újabb)
+ Közép-Kanada (2021. január 30-ig vagy újabb)
+ USA középső régiója (2020. december 4. és újabb verziók)
+ USA keleti régiója (2021. január 27. vagy újabb)
+ USA 2. keleti régiója (2021-es vagy újabb)
+ Közép-Franciaország (2020. október 23. vagy újabb)
+ Kelet-Japán (2021. január 30-ig vagy újabb)
+ Észak-Európa (létrehozva január 28., 2021 vagy újabb)
+ Dél-Kelet-Ázsia (létrehozva: január 31., 2021 vagy újabb)
+ Egyesült Királyság déli régiója (létrehozva: január 30., 2021 vagy újabb)
+ Nyugat-Európa (létrehozva január 29., 2021 vagy újabb)
+ USA 2. nyugati régiója (létrehozva január 30, 2021 vagy újabb)

Availability Zones nem érinti az [Azure Cognitive Search szolgáltatói szerződés](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Továbbra is 3 vagy több replikára van szükség a magas rendelkezésre állás lekérdezéséhez.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Méretezés földrajzilag elosztott számítási feladatokhoz és geo-redundancia

A földrajzilag elosztott számítási feladatokhoz a gazdagép-adatközponttól távol lévő felhasználóknak nagyobb késési aránya lesz. Az egyik megoldás az, hogy több keresési szolgáltatást kell kiépíteni a régiókban, és ezek a felhasználók közelebb vannak egymáshoz.

Az Azure Cognitive Search jelenleg nem biztosít automatizált módszert az Azure Cognitive Search indexek földrajzi replikálására a régiók között, de vannak olyan technikák is, amelyek a folyamat egyszerű megvalósításához és kezeléséhez használhatók. Ezeket a következő néhány szakaszban ismertetjük.

A földrajzi eloszlású keresési szolgáltatások célja, hogy legalább két, két vagy több régióban rendelkezésre álló index legyen elérhető, ahol a felhasználó az Azure Cognitive Search szolgáltatáshoz van irányítva, amely az ebben a példában látható legalacsonyabb késést biztosítja:

   ![Szolgáltatások közötti, régiónként][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Több szolgáltatás között szinkronizálva marad az adatszinkronizálás

Az elosztott keresési szolgáltatások szinkronizálása két lehetőség közül választhat, amelyek az [azure Cognitive Search indexelő](search-indexer-overview.md) vagy a leküldéses API (más néven [Azure Cognitive Search REST API](/rest/api/searchservice/)) használatával állnak.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexelő használata több szolgáltatás tartalmának frissítéséhez

Ha már használja az indexelő az egyik szolgáltatáson, a második indexelő is konfigurálható egy másik szolgáltatáson, hogy ugyanazt az adatforrás-objektumot használja, az adatok ugyanabból a helyről való kihúzásával. Az egyes régiókban található szolgáltatások saját indexelő és egy célként megadott indextel rendelkeznek (a keresési index nincs megosztva, ami azt jelenti, hogy az adatok duplikálva vannak), de minden indexelő ugyanarra az adatforrásra hivatkozik.

Itt látható egy magas szintű vizualizáció, hogy az architektúra milyen módon fog kinézni.

   ![Egyetlen adatforrás elosztott indexelő és szolgáltatási kombinációkkal][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>A REST API-k használata a tartalmi frissítések több szolgáltatásban való leküldéséhez

Ha az Azure Cognitive Search REST APIt használja az [azure Cognitive Search indexben lévő tartalmak leküldéséhez](/rest/api/searchservice/update-index), a különböző keresési szolgáltatásokat szinkronizálhatja, ha az összes keresési szolgáltatás módosítását kéri, amikor frissítésre van szükség. Ügyeljen arra, hogy a kódban olyan eseteket kezeljen, amelyekben az egyik keresési szolgáltatás frissítése meghiúsul, de a többi keresési szolgáltatás sikeres.

## <a name="leverage-azure-traffic-manager"></a>Az Azure Traffic Manager kihasználása

Az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy a kérelmeket több, több keresési szolgáltatás által támogatott földrajzi helyen lévő webhelyre irányítsa. Az Traffic Manager egyik előnye, hogy az Azure-Cognitive Search mintavétele lehetővé teszi, hogy elérhető legyen, és a felhasználók átirányítása alternatív keresési szolgáltatásokra leállás esetén. Emellett, ha az Azure-webhelyeken keresztül irányítja a keresési kérelmeket, az Azure Traffic Manager lehetővé teszi a terheléselosztási esetek terhelését, ha a webhely fel van töltve, de nem az Azure Cognitive Search. Íme egy példa arra, hogy milyen architektúrát használ a Traffic Manager.

   ![Szolgáltatások – régiók közötti, központi Traffic Manager][3]

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az egyes díjszabási csomagokról és szolgáltatásokra vonatkozó korlátozásokról, tekintse meg a [szolgáltatási korlátok](search-limits-quotas-capacity.md)című témakört. A partíció-és replika-kombinációkkal kapcsolatos további tudnivalókért tekintse meg [a kapacitás megtervezése](search-capacity-planning.md) című témakört.

Az ebben a cikkben tárgyalt módszerek teljesítményével és bemutatásával kapcsolatban tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png