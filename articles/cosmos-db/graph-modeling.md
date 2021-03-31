---
title: Graph adatmodellezés Azure Cosmos DB Gremlin API-hoz
description: Útmutató a Graph-adatbázisok modellezéséhez Azure Cosmos DB Gremlin API használatával. Ez a cikk azt ismerteti, hogy mikor érdemes gráf-adatbázist és ajánlott eljárásokat használni az entitások és a kapcsolatok modellezéséhez.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 12/02/2019
ms.author: chrande
ms.openlocfilehash: d99e2e2ffd63b050e7373c98084fed3fb14727bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93357045"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Graph adatmodellezés Azure Cosmos DB Gremlin API-hoz
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

A következő dokumentum a Graph adatmodellezési javaslatok biztosítására szolgál. Ez a lépés elengedhetetlen ahhoz, hogy biztosítható legyen egy gráf-adatbázis rendszerének méretezhetősége és teljesítménye, ahogy az adat fejlődik. A hatékony adatmodell különösen fontos a nagyméretű diagramok esetében.

## <a name="requirements"></a>Követelmények

Az útmutatóban ismertetett folyamat az alábbi feltételezéseken alapul:
 * A probléma **entitásai** azonosítva vannak. Ezeket az entitásokat az egyes kérések esetében _atomian_ kell használni. Más szóval az adatbázisrendszer nem úgy van kialakítva, hogy egyetlen entitást több lekérdezési kérelemben is lekérjen.
 * Az adatbázisrendszer **olvasási és írási követelményeinek** ismerete. Ezek a követelmények a Graph adatmodellhez szükséges optimalizálásokat ismertetik.
 * Az [Apache Tinkerpop Property Graph standard](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) alapelvei jól megértettek.

## <a name="when-do-i-need-a-graph-database"></a>Mikor van szükség gráf-adatbázisra?

A Graph adatbázis-megoldások optimálisan alkalmazhatók, ha az adattartományban lévő entitások és kapcsolatok a következő jellemzők valamelyikével rendelkeznek: 

* Az entitások **nagyon összekapcsolhatók** a leíró kapcsolatokon keresztül. Ebben a forgatókönyvben az a tény, hogy a kapcsolatok tárolása megmarad.
* **Ciklikus kapcsolatok** vagy **saját maga által hivatkozott entitások** vannak. Ez a minta gyakran kihívást jelent a viszonyítási vagy dokumentum-adatbázisok használatakor.
* **Dinamikusan változó kapcsolatok** vannak az entitások között. Ez a minta különösen az olyan hierarchikus vagy faszerkezetű adatmennyiségek esetében alkalmazható, amelyeknek számos szintje van.
* Az entitások között **több-a-többhöz kapcsolat** van.
* Az **entitások és a kapcsolatok esetében írási és olvasási követelmények** is vannak. 

Ha a fenti feltételek teljesülnek, akkor valószínű, hogy a Graph-adatbázis megközelítése előnyeit biztosítja a **lekérdezések összetettsége**, az **adatmodell méretezhetősége** és a **lekérdezési teljesítmény** szempontjából.

A következő lépés annak megállapítása, hogy a gráfot analitikai vagy tranzakciós célokra kívánja-e használni. Ha a gráf nagy számítási és adatfeldolgozási feladatokhoz készült, érdemes megvizsgálni a [Cosmos db Spark-összekötőt](./spark-connector.md) és a [GraphX-könyvtár](https://spark.apache.org/graphx/)használatát. 

## <a name="how-to-use-graph-objects"></a>Graph-objektumok használata

Az [Apache Tinkerpop Property Graph standard](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) két típusú objektumot határoz meg a **csúcspontok** és **élek** közül. 

A Graph-objektumok tulajdonságaira vonatkozó ajánlott eljárások a következők:

| Objektum | Tulajdonság | Típus | Jegyzetek |
| --- | --- | --- |  --- |
| Vertex | ID (Azonosító) | Sztring | A partíciók egyedi kikényszerítve. Ha nincs megadva érték a beszúráskor, az automatikusan generált GUID-t fogja tárolni. |
| Vertex | címke | Sztring | Ez a tulajdonság határozza meg a csúcspont által reprezentált entitás típusát. Ha nincs megadva érték, a rendszer az alapértelmezett "Vertex" értéket fogja használni. |
| Vertex | properties | Karakterlánc, logikai, numerikus | Az egyes csúcspontokban kulcs-érték párokként tárolt külön tulajdonságok listája. |
| Vertex | partíciós kulcs | Karakterlánc, logikai, numerikus | Ez a tulajdonság határozza meg, hogy a csúcspont és a kimenő élek hol lesznek tárolva. További információ a [Graph particionálásról](graph-partitioning.md). |
| Edge | ID (Azonosító) | Sztring | A partíciók egyedi kikényszerítve. Alapértelmezés szerint automatikusan létrejön. Az éleket általában nem kell egyedi módon beolvasni egy AZONOSÍTÓval. |
| Edge | címke | Sztring | Ez a tulajdonság határozza meg, hogy milyen típusú kapcsolatra van két csúcspont. |
| Edge | properties | Karakterlánc, logikai, numerikus | Az egyes szegélyekben kulcs-érték párokként tárolt külön tulajdonságok listája. |

> [!NOTE]
> Az élek nem igénylik a partíciós kulcs értékét, mivel az értékét a rendszer automatikusan hozzárendeli a forrás csúcspontja alapján. További információt a [Graph particionálási](graph-partitioning.md) cikkében talál.

## <a name="entity-and-relationship-modeling-guidelines"></a>Az entitás és a kapcsolat modellezési irányelvei

Az alábbi irányelvek egy Azure Cosmos DB Gremlin API Graph-adatbázis adatmodellezésének megközelítésére vonatkoznak. Ezek az irányelvek feltételezik, hogy az adattartományhoz és a lekérdezésekhez egy meglévő definíció van.

> [!NOTE]
> Az alábbi lépéseket javaslatokként mutatjuk be. A végleges modellt az éles környezetbe való felkészülés előtt ki kell értékelni és tesztelni kell. Emellett az alábbi javaslatok a Azure Cosmos DB Gremlin API-implementációra vonatkoznak. 

### <a name="modeling-vertices-and-properties"></a>Modellezési csúcspontok és tulajdonságok 

A Graph adatmodell első lépése az összes azonosított entitás leképezése egy csúcspont- **objektumra**. Az összes entitásnak a csúcspontokra való hozzárendelésének egy kezdeti lépésnek kell lennie, és a változás változhat.

Az egyik gyakori buktató az egyetlen entitás tulajdonságainak leképezése különálló csúcspontként. Vegye figyelembe az alábbi példát, ahol ugyanaz az entitás két különböző módon van ábrázolva:

* **Vertex-alapú tulajdonságok**: ebben a megközelítésben az entitás három különálló csúcspontot és két szegélyt használ a tulajdonságainak leírására. Ez a megközelítés csökkentheti a redundanciát, és növeli a modell bonyolultságát. A modell bonyolultságának növekedése a késés, a lekérdezés bonyolultsága és a számítási költségeket eredményezheti. Ez a modell a particionálással kapcsolatos kihívásokat is jelenthet.

:::image type="content" source="./media/graph-modeling/graph-modeling-1.png" alt-text="Entitás-modell a tulajdonságok csúcspontokkal." border="false":::

* **Tulajdonsággal beágyazott csúcspontok**: Ez a megközelítés kihasználja a kulcs-érték párok listáját, hogy az entitás összes tulajdonságát reprezentálja a csúcsponton belül. Ez a megközelítés csökkenti a modell bonyolultságát, ami egyszerűbb lekérdezéseket és költséghatékonyabb bejárásokat eredményez.

:::image type="content" source="./media/graph-modeling/graph-modeling-2.png" alt-text="Az ábrán az előző diagram Luis csúcspontja látható az i d, a Label és a Properties tulajdonsággal." border="false":::

> [!NOTE]
> A fenti példák egy egyszerűsített gráfot mutatnak be, amely csak az entitás tulajdonságainak felosztására szolgáló két módszer összehasonlítását mutatja be.

A **tulajdonsággal beágyazott csúcspontok** mintája általában nagyobb teljesítményű és skálázható megközelítést biztosít. Az új Graph adatmodellek alapértelmezett megközelítése ennek a mintának az elérésére irányul.

Vannak azonban olyan helyzetek, amikor egy tulajdonságra hivatkozva előnyt jelenthetnek. Például: Ha a hivatkozott tulajdonságot gyakran frissítik. Ha egy külön csúcspontot használ, amely egy folyamatosan módosított tulajdonságot jelöl, minimálisra csökkentheti a frissítés által igényelt írási műveletek mennyiségét.

### <a name="relationship-modeling-with-edge-directions"></a>Kapcsolat modellezése Edge-utasításokkal

A csúcspontok modellezése után a szegélyek hozzáadhatók a közöttük lévő kapcsolatok jelöléséhez. Az első szempont, amelyet ki kell értékelni a **kapcsolat irányát**. 

Az Edge-objektumok alapértelmezett iránya a vagy a függvény használatakor bejárási `out()` `outE()` művelet. Ennek a természetes irányú iránynak a használata hatékony műveletet eredményez, mivel minden csúcspontot a kimenő élek tárolnak. 

Egy Edge ellenkező irányú átjárása azonban a `in()` függvény használatával mindig egy több partíciós lekérdezést fog eredményezni. További információ a [Graph particionálásról](graph-partitioning.md). Ha állandóan át kell haladnia a `in()` függvény használatával, azt javasoljuk, hogy mindkét irányban vegyen fel éleket.

A peremhálózat irányát a vagy a predikátumok használatával határozhatja meg `.to()` `.from()` az `.addE()` Gremlin lépéshez. Vagy a Gremlin API-hoz készült [tömeges végrehajtó kódtár](bulk-executor-graph-dotnet.md)használatával.

> [!NOTE]
> Az Edge-objektumok alapértelmezés szerint irányt mutatnak.

### <a name="relationship-labeling"></a>Kapcsolat címkézése

A leíró kapcsolati címkék használatával javítható a peremhálózat-feloldási műveletek hatékonysága. Ezt a mintát a következő módokon lehet alkalmazni:
* Használjon nem általános kifejezéseket a kapcsolatok címkézéséhez.
* Társítsa a forrás csúcspontjának címkéjét a cél csúcspontjának címkéjéhez a kapcsolat nevével.

:::image type="content" source="./media/graph-modeling/graph-modeling-3.png" alt-text="Relációs címkézési példák." border="false":::

Minél pontosabb a felirat, amelyet a bejárás használ az élek szűrésére, annál jobb. Ez a döntés jelentős hatással lehet a lekérdezési díjakra is. A lekérdezési költségeket bármikor kiértékelheti [a executionProfile lépés használatával](graph-execution-profile.md).


## <a name="next-steps"></a>Következő lépések: 
* Tekintse meg a támogatott [Gremlin lépések](gremlin-support.md)listáját.
* Ismerkedjen meg a [Graph Database particionálásával](graph-partitioning.md) a nagyméretű gráfok kezeléséhez.
* Értékelje ki a Gremlin-lekérdezéseket a [végrehajtási profil lépés](graph-execution-profile.md)használatával.