---
title: Rendelkezésre állás és folytonosság
titleSuffix: Azure Cognitive Search
description: megtudhatja, hogyan teheti elérhetővé a keresési szolgáltatást az időszakos megszakítások vagy akár katasztrofális hibák miatt.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581535"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Az Azure Cognitive Search rendelkezésre állása és üzletmenet-folytonossága

Cognitive Search a rendelkezésre állás több replikán keresztül érhető el, míg az üzletmenet folytonossága (és a vész-helyreállítás) több keresési szolgáltatáson keresztül érhető el. Ez a cikk útmutatást nyújt, amely kiindulási pontként használható olyan stratégia kidolgozásához, amely megfelel a rendelkezésre állási és a folyamatos műveletek üzleti követelményeinek.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás

Cognitive Search a replikák az index másolatai. A több replika lehetővé teszi, hogy az Azure Cognitive Search a számítógép újraindítását és karbantartását egy másodpéldányon, míg a lekérdezés végrehajtása más replikán is folytatódik. A replikák hozzáadásával kapcsolatos további információkért lásd: [replikák és partíciók hozzáadása vagy csökkentése](search-capacity-planning.md#adjust-capacity).

A Microsoft minden egyes keresési szolgáltatás esetében legalább 99,9%-os rendelkezésre állást garantál az alábbi feltételeknek megfelelő konfigurációk esetében: 

+ Két replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)

+ Három vagy több replika az írási és olvasási feladatok (lekérdezések és indexelés) magas rendelkezésre állásához 

Az ingyenes szinthez nem biztosítunk SLA-t. További információ: [SLA for Azure Cognitive Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Rendelkezésre állási zónák

[Availability Zones](../availability-zones/az-overview.md) olyan Azure platform-képesség, amely a régió adatközpontjait különálló fizikai csoportokba osztja, hogy magas rendelkezésre állást biztosítson ugyanazon a régión belül. Ha Cognitive Search Availability Zones használ, az egyes replikák a zónák hozzárendelésének egységei. Egy keresési szolgáltatás egy régión belül fut; a replikái különböző zónákban futnak.

Availability Zones az Azure Cognitive Search használatával két vagy több replikát adhat hozzá a keresési szolgáltatáshoz. Minden replika a régión belül egy másik rendelkezésre állási zónába kerül. Ha Availability Zones több replikával rendelkezik, a replikák a lehető legegyenletesebb Availability Zones lesznek elosztva. Nincs konkrét művelet a részben, kivéve, ha olyan [keresési szolgáltatást hoz létre](search-create-service-portal.md) egy régióban, amely Availability Zonest biztosít, majd úgy konfigurálja a szolgáltatást, hogy [több replikát használjon](search-capacity-planning.md#adjust-capacity).

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

## <a name="multiple-services-in-separate-geographic-regions"></a>Több szolgáltatás különböző földrajzi régiókban

Bár az ügyfelek többsége csak egy szolgáltatást használ, szükség lehet a szolgáltatás redundanciájára, ha az üzemeltetési körülmények között az alábbiak szerepelnek:

+ Az [üzletmenet folytonossága és a vész-helyreállítási (BCDR)](../best-practices-availability-paired-regions.md) (Cognitive Search leállás esetén nem biztosít azonnali feladatátvételt).
+ Globálisan telepített alkalmazások. Ha a lekérdezési és indexelési kérelmek a világ minden tájáról érkeznek, a gazdagép-adatközponthoz legközelebb eső felhasználók gyorsabb teljesítményt kapnak. Ha további szolgáltatásokat szeretne létrehozni a régiókban, az ezekhez a felhasználókhoz közeli közelségben az összes felhasználó teljesítményét kiegyenlítheti.
+ A [több-bérlős architektúrák](search-modeling-multitenant-saas-applications.md) időnként két vagy több szolgáltatást hívhatnak meg.

Ha két további keresési szolgáltatásra van szüksége, a különböző régiókban való létrehozásával kielégítheti a folytonosságot és a helyreállítást igénylő alkalmazások követelményeit, valamint a globális felhasználói bázisok gyorsabb válaszidőt is.

Az Azure Cognitive Search jelenleg nem biztosít automatizált módszert a keresési indexek földrajzi replikálására a régiók között, de vannak olyan technikák is, amelyek a folyamat egyszerű megvalósítását és kezelését teszik lehetővé. Ezeket a következő néhány szakaszban ismertetjük.

A földrajzi eloszlású keresési szolgáltatások célja, hogy legalább két, két vagy több régióban elérhető index legyen, ahol a felhasználó átirányítja az Azure Cognitive Search szolgáltatásba, amely a legalacsonyabb késést biztosítja:

   ![Szolgáltatások közötti, régiónként][1]

Ezt az architektúrát több szolgáltatás létrehozásával és egy adatszinkronizálási stratégia kialakításával is megvalósíthatja. Igény szerint olyan erőforrást is hozzáadhat, mint például az Azure Traffic Manager útválasztási kérelmek esetén. További információkért lásd: [keresési szolgáltatás létrehozása](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Több szolgáltatás között szinkronizálva marad az adatszinkronizálás

Két lehetőség közül választhat a két vagy több elosztott keresési szolgáltatás szinkronizálása, amelyek az [azure Cognitive Search indexelő](search-indexer-overview.md) vagy a leküldéses API (más néven az [Azure Cognitive Search REST API](/rest/api/searchservice/)) használatával állnak. 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>1. lehetőség: indexelő használata több szolgáltatás tartalmának frissítéséhez

Ha már használja az indexelő az egyik szolgáltatáson, a második indexelő is konfigurálható egy másik szolgáltatáson, hogy ugyanazt az adatforrás-objektumot használja, az adatok ugyanabból a helyről való kihúzásával. Az egyes régiókban található szolgáltatások saját indexelő és egy célként megadott indextel rendelkeznek (a keresési index nincs megosztva, ami azt jelenti, hogy az adatok duplikálva vannak), de minden indexelő ugyanarra az adatforrásra hivatkozik.

Itt látható egy magas szintű vizualizáció, hogy az architektúra milyen módon fog kinézni.

   ![Egyetlen adatforrás elosztott indexelő és szolgáltatási kombinációkkal][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>2. lehetőség: REST API-k használata a tartalom frissítéseinek több szolgáltatásban való leküldéséhez

Ha az Azure Cognitive Search REST API használatával [küld tartalmat a keresési indexbe](tutorial-optimize-indexing-push-api.md), a különböző keresési szolgáltatásokat szinkronizálhatja, ha a módosításokat az összes keresési szolgáltatásban megnyomja, amikor frissítésre van szükség. Ügyeljen arra, hogy a kódban olyan eseteket kezeljen, amelyekben az egyik keresési szolgáltatás frissítése meghiúsul, de a többi keresési szolgáltatás sikeres.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Az Azure Traffic Manager használata a kérelmek koordinálásához

Az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy a kérelmeket több, több keresési szolgáltatás által támogatott földrajzi helyen lévő webhelyre irányítsa. Az Traffic Manager egyik előnye, hogy az Azure-Cognitive Search mintavétele lehetővé teszi, hogy elérhető legyen, és a felhasználók átirányítása alternatív keresési szolgáltatásokra leállás esetén. Emellett, ha az Azure-webhelyeken keresztül irányítja a keresési kérelmeket, az Azure Traffic Manager lehetővé teszi a terheléselosztási esetek terhelését, ha a webhely fel van töltve, de nem az Azure Cognitive Search. Íme egy példa arra, hogy milyen architektúrát használ a Traffic Manager.

   ![Szolgáltatások – régiók közötti, központi Traffic Manager][3]

## <a name="disaster-recovery-and-service-outages"></a>Vész-helyreállítási és szolgáltatás-kimaradások

Bár az adatai megmentését is lehetővé teszi, az Azure Cognitive Search nem biztosítja a szolgáltatás azonnali feladatátvételét, ha a fürt vagy az adatközpont szintjén áramkimaradás történik. Ha egy fürt meghibásodik az adatközpontban, az operatív csapat felismeri és a szolgáltatás visszaállítását végzi. A szolgáltatás visszaállítása során állásidőt tapasztalhat, de a szolgáltatási kreditek igénylésével kompenzálhatja a szolgáltatás nem rendelkezésre állását a [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Ha a Microsoft általi ellenőrzésen kívüli katasztrofális hibák esetén folyamatos szolgáltatásra van szükség, egy másik régióban is [kiépítheti a további](search-create-service-portal.md) szolgáltatásokat, és a Geo-replikációs stratégia megvalósításával biztosíthatja, hogy az indexek teljes mértékben redundánsak legyenek az összes szolgáltatásban.

Azok az ügyfelek, akik [Indexelő](search-indexer-overview.md) adatokat használnak az indexek feltöltéséhez és frissítéséhez, az azonos adatforrást használó geo-specifikus indexelő segítségével kezelhetik a vész-helyreállítást. A különböző régiókban található két szolgáltatás, amelyek mindegyike indexelt, indexelheti ugyanazt az adatforrást a Geo-redundancia eléréséhez. Ha olyan adatforrásokból származó indexelést is használ, amelyek földrajzilag redundánsak, vegye figyelembe, hogy az Azure Cognitive Search indexelő csak növekményes indexelést végezhetnek (új, módosított vagy törölt dokumentumokból származó frissítések egyesítése) az elsődleges replikából. Feladatátvételi esemény esetén ügyeljen arra, hogy az indexelő újra az új elsődleges replikára irányítsa. 

Ha nem használ indexelő funkciót, az alkalmazás kódjával párhuzamosan küldheti el az objektumokat és az adatait a különböző keresési szolgáltatásoknak. További információ: a [több szolgáltatás között szinkronizált adatok megtartása](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Alternatívák biztonsági mentése és visszaállítása

Mivel az Azure Cognitive Search nem elsődleges adattárolási megoldás, a Microsoft nem biztosít formális mechanizmust az önkiszolgáló biztonsági mentéshez és visszaállításhoz. Az [Azure Cognitive Search .net minta](https://github.com/Azure-Samples/azure-search-dotnet-samples) -tárházban található **index-Backup-Restore** mintakód használatával azonban a tárgymutató-definíciót és a pillanatképet egy sor JSON-fájlra is elkészítheti, majd a fájlok használatával visszaállíthatja az indexet, ha szükséges. Ez az eszköz az indexeket is át tudja helyezni a szolgáltatási szintek között.

Ellenkező esetben az index létrehozásához és feltöltéséhez használt alkalmazás kódja a de facto Visszaállítási lehetőség, ha tévedésből töröl egy indexet. Az indexek újraépítéséhez törölnie kell azt (feltéve, hogy létezik), újra létre kell hoznia az indexet a szolgáltatásban, majd újra kell töltenie az adatok elsődleges adattárból való beolvasásával.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az egyes díjszabási csomagokról és szolgáltatásokra vonatkozó korlátozásokról, tekintse meg a [szolgáltatási korlátok](search-limits-quotas-capacity.md)című témakört. A partíció-és replika-kombinációkkal kapcsolatos további tudnivalókért tekintse meg [a kapacitás megtervezése](search-capacity-planning.md) című témakört.

Az ebben a cikkben tárgyalt módszerek teljesítményével és bemutatásával kapcsolatban tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png