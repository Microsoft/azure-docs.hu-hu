---
title: Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban
description: Válaszok a szinapszis-hivatkozással kapcsolatos gyakori kérdésekre a Azure Cosmos DB olyan területeken, mint például a számlázás, az analitikus tárolás, a biztonság, az elemzési tár élettartama.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 120bec65c92e2a13022682265b83bfe0f69d8ed0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592082"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

A Azure Cosmos DB Azure szinapszis-hivatkozása szoros integrációt hoz létre a Azure Cosmos DB és az Azure szinapszis Analytics között. Lehetővé teszi az ügyfelek számára a közel valós idejű elemzések futtatását a működési adataik teljes teljesítményének elkülönítésével a tranzakciós munkaterhelések és az ETL-folyamat nélkül. Ez a cikk az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="general-faq"></a>Gyakori kérdések – általános

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Támogatott-e az Azure szinapszis-hivatkozás az összes Azure Cosmos DB API-hoz?

Az Azure szinapszis hivatkozása támogatott a Azure Cosmos DB SQL (Core) API-hoz és a MongoDB Azure Cosmos DB API-hoz. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Támogatott-e az Azure szinapszis-hivatkozás a többrégiós Azure Cosmos DB fiókok esetében?

Igen, a többrégiós Azure Cosmos-fiókok esetében az analitikus tárolóban tárolt adatforgalom is globálisan terjeszthető. Az Azure Synapse Analyticsről végzett elemzési lekérdezések a legközelebbi helyi régióról szolgálhatók ki, függetlenül attól, hogy egy vagy több írási régióról van szó.

Ha többrégiós Azure Cosmos DB-fiók az analitikai tár támogatásával való konfigurálását tervezi, ajánlott az összes szükséges régiót a fiók létrehozásakor hozzáadni.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Engedélyezhető az Azure szinapszis-hivatkozás engedélyezése csak bizonyos régiókban, és nem minden régióban a többrégiós fiók beállítása?

Ha az Azure szinapszis-kapcsolat engedélyezve van egy többrégiós fiók esetében, az analitikai tároló minden régióban létrejön. Az alapul szolgáló adatok a tranzakciós tárolóban a teljesítményre és a tranzakciós konzisztenciare vannak optimalizálva.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Támogatott-e az analitikai tároló az összes Azure Cosmos DB régióban?

Igen.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Támogatott-e a biztonsági mentés és a visszaállítás az Azure szinapszis-kapcsolattal rendelkező fiókok esetében?

Az analitikai tárolóval rendelkező tárolók esetében a rendszer jelenleg nem támogatja az automatikus biztonsági mentést és visszaállítást az analitikus tárolóban. 

Ha a szinapszis-hivatkozás engedélyezve van egy adatbázis-fiókon, Azure Cosmos DB továbbra is automatikusan [biztonsági másolatot](./online-backup-and-restore.md) készít az adatairól a tranzakciós tárolóban (csak) a tárolók ütemezett biztonsági mentési időintervallumában, ahogy mindig. Fontos megjegyezni, hogy ha az analitikai tárolót bekapcsoló tároló egy új fiókra van visszaállítva, a tároló csak tranzakciós tárolóval lesz visszaállítva, és nincs engedélyezve az analitikus tároló. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Letiltható az Azure szinapszis hivatkozás funkciója a Azure Cosmos DB fiókomhoz?

Jelenleg nem tilthatja le a Synapse Link képességet, miután fiókszinten engedélyezte azt. Fontos tudni, hogy nem jár költséggel, ha a Synapse Link képesség engedélyezve van a fiók szintjén, és nem találhatók elemzésitár-kompatibilis tárolók.

Ha ki kell kapcsolnia a képességet, kétféle lehetősége van. Az első lehetőség az, hogy törli, majd újból létrehozza az új Azure Cosmos DB-fiókot, ha szükséges, az adatok migrálásával. A második lehetőség a támogatási jegy nyitása, hogy segítséget kaphasson az adatok másik fiókba való migrálásához.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Az analitikai tároló hatással van Cosmos DB tranzakciós SLA-ra?

Nem, nincs hatása.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitikus tároló

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Engedélyezhető az analitikai tár a meglévő tárolókban?

Az analitikai tároló jelenleg csak új tárolók esetében engedélyezhető (az új és a meglévő fiókokban is).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Letiltható az analitikus tároló a Azure Cosmos DB-tárolókban, miután engedélyezte azt a tároló létrehozásakor?

Az elemzési tár jelenleg nem tiltható le az Azure Cosmos DB-tárolókon, ha a tároló létrehozásakor engedélyezte azt.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Támogatott-e az analitikus tároló a Azure Cosmos DB tárolók számára az autoscale kiosztott átviteli sebességgel?

Igen, az analitikai tárolót engedélyezheti olyan tárolókban, amelyek az autoscale kiosztott átviteli sebességgel rendelkeznek.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Van-e hatása Azure Cosmos DB tranzakciós áruház kiépített RUs-re?

Azure Cosmos DB garantálja a tranzakciós és az analitikai számítási feladatok elkülönítését. Az analitikai tároló tárolón való engedélyezése nem befolyásolja az Azure Cosmos DB tranzakciós tárolóban kiosztott RU/s-t. Az analitikus tárolóra vonatkozó tranzakciók (olvasási & írás) és a tárolási költségek külön lesznek felszámítva. További részletekért tekintse [meg Azure Cosmos db analitikus áruház díjszabását](analytical-store-introduction.md#analytical-store-pricing) .

### <a name="can-i-restrict-access-to-azure-cosmos-db-analytical-store"></a>Korlátozható Azure Cosmos DB analitikus tárolóhoz való hozzáférés?

Igen, konfigurálhat egy [felügyelt privát végpontot](analytical-store-private-endpoints.md) , és korlátozhatja az analitikai tároló hálózati hozzáférését az Azure szinapszis által felügyelt virtuális hálózathoz. A felügyelt privát végpontok létrehozhatnak egy privát hivatkozást az analitikus tárolóhoz. Ez a privát végpont az Azure-adatszolgáltatások többek között a tranzakciós tárolóhoz való írási hozzáférést is korlátozza.

Az Azure szinapszis Analytics-munkaterületen a tranzakciós tároló és az analitikus tároló magánhálózati végpontokat is hozzáadhatja ugyanahhoz a Azure Cosmos DB-fiókhoz. Ha csak analitikai lekérdezéseket szeretne futtatni, akkor előfordulhat, hogy csak az analitikai magánhálózati végpontot szeretné leképezni.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Használhatom-e az ügyfél által felügyelt kulcsokat a Azure Cosmos DB analitikus tárolóval?

Az adattranzakciós és analitikai tárolók közötti zökkenőmentesen titkosíthatja az ügyfelek által felügyelt kulcsokat automatikus és átlátható módon. Az ügyfél által felügyelt kulcsok Azure Cosmos DB analitikus tárolóval való használata jelenleg további konfigurálást igényel a fiókjában. Részletekért forduljon a [Azure Cosmos db csapatához](mailto:azurecosmosdbcmk@service.microsoft.com)  .

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>A tranzakciós tárolóban a DELETE és a Update művelet szerepel az analitikus tárolóban?

Igen, a tranzakciós tárolóban lévő adattörlési és-frissítési műveletek megjelennek az analitikus tárolóban. Beállíthatja, hogy az élettartam (TTL) a tárolón, hogy tartalmazza a korábbi adatokat, hogy az analitikai tár megőrizze az összes olyan verziót, amely megfelel az analitikai élettartam feltételeinek. További részletekért tekintse meg az [analitikai TTL áttekintését](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Csatlakozhatok az analitikus tárolóhoz az Azure szinapszis Analytics szolgáltatástól eltérő elemzési motoroktól?

Csak az Azure Synapse Analytics által biztosított különböző futtatókörnyezetekkel érhet el és futtathat lekérdezéseket az elemzési táron. Az elemzési tár a következők használatával kérdezhető le és elemezhető:

* A szinapszis Spark teljes körű támogatást nyújt a Scala, a Python, a SparkSQL és a C# nyelvhez. A Synapse Spark az adatfeldolgozási és -elemzési forgatókönyvek központi eleme
* Kiszolgáló nélküli SQL-készlet a T-SQL nyelvvel és az ismerős BI-eszközök támogatása (például Power BI Premium stb.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Csatlakozhatok az analitikus tárolóhoz a szinapszis SQL kiépített használatával?

Jelenleg az analitikai tároló nem érhető el a szinapszis SQL kiépített állapotáról.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Lehet-e visszaírni a lekérdezési összesítés eredményeit a Szinapszisból az analitikai tárolóba?

Az analitikai tároló egy Azure Cosmos DB tárolóban található írásvédett tároló. Így nem tudja közvetlenül visszaírni az összesítési eredményeket az analitikai tárolóba, de megírhatja őket egy másik tároló Azure Cosmos DB tranzakciós tárolójába, amely később kihasználhatja a kiszolgálóként szolgáló réteget.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>A tranzakciós tárolóból az elemzési tárolóba aszinkron vagy szinkron módon történik az AutoSync replikáció, és mi a késés?

Az automatikus szinkronizálás késése általában 2 percen belül megtörténik. Ha a megosztott átviteli sebességű adatbázis nagy mennyiségű tárolóval rendelkezik, az egyes tárolók automatikus szinkronizálási késése magasabb lehet, és akár 5 percet is igénybe vehet. Szeretnénk többet megtudni, hogy ez a késés hogyan illeszkedik a forgatókönyvekhez. Ehhez kérjük, lépjen kapcsolatba a [Azure Cosmos db csapatával](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Vannak olyan helyzetek, amikor a tranzakciós tárolóból származó elemek nem lesznek automatikusan propagálva az analitikai tárolóba?

Ha a tárolóban meghatározott elemek megsértik a [jól meghatározott sémát az elemzéshez](analytical-store-introduction.md#analytical-schema), azok nem kerülnek bele az analitikai tárolóba. Ha az elemzéshez jól definiált séma által blokkolt forgatókönyvek vannak, küldje el a [Azure Cosmos db csapatának](mailto:cosmosdbsynapselink@microsoft.com) segítségét.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Az analitikus tárolóban lévő adatok a tranzakciós tárolótól eltérően is particionálva vannak?

Az elemzési tárban lévő adatok particionálása a tranzakciós tárolóban lévő szegmensek horizontális particionálásán alapul. Jelenleg nem választhat más particionálási stratégiát az elemzési tárhoz.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Testreszabható vagy felülbírálható a tranzakciós adatértékek oszlopos formátumba való átalakítása az analitikai tárolóban?

Az adatelemek jelenleg nem alakíthatók át, ha automatikusan propagálják őket a tranzakciós tárolóból az analitikai tárolóba. Ha ezt a korlátozást a forgatókönyvek blokkolják, küldje el a [Azure Cosmos db csapatnak](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-analytical-store-supported-by-terraform"></a>Támogatja a Terraform az elemzési tárat?

A Terraform jelenleg nem támogatja az elemzési tár tárolóit. További információért tekintse meg a [Terraform GitHub-problémáit](https://github.com/hashicorp/terraform/issues) ismertető témakört.

## <a name="analytical-time-to-live-ttl"></a>Analitikai élettartam (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>A a tárolók és az elemek szintjén támogatott analitikai adatértékek ÉLETTARTAMa?

Az elemzési adatok élettartama jelenleg csak a tároló szintjén konfigurálható, az elemek szintjén nem támogatott az elemzési élettartam beállítása.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Miután beállította a tároló szintjének analitikai ÉLETTARTAMát egy Azure Cosmos DB tárolón, másik értékre válthatok később?

Igen, az analitikai élettartam bármely érvényes értékre frissíthető. Az analitikai ÉLETTARTAMmal kapcsolatos további információkért tekintse meg az [analitikai TTL](analytical-store-introduction.md#analytical-ttl) című cikket.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Frissíthetek vagy törölhetek egy elemet az analitikus tárolóból, miután a tranzakciós tárolóból már ÉLETTARTAMa van?

Az összes tranzakciós frissítést és törlést a rendszer átmásolja az analitikai tárolóba, de ha az elemet törölte a tranzakciós tárolóból, akkor az nem frissíthető az analitikai tárolóban. További információért lásd az [analitikai élettartamot](analytical-store-introduction.md#analytical-ttl) ismertető cikket.

## <a name="billing"></a>Számlázás

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Mi a Azure Cosmos DB Azure szinapszis-hivatkozás számlázási modellje?

Az Azure szinapszis hivatkozás számlázási modellje tartalmazza a Azure Cosmos DB analitikus tároló és a szinapszis-futtatókörnyezet használatával felmerülő költségeket. További információért lásd a [Azure Cosmos db analitikus áruház díjszabását](analytical-store-introduction.md#analytical-store-pricing) és az [Azure szinapszis Analytics árképzési](https://azure.microsoft.com/pricing/details/synapse-analytics/) cikkeit.

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Mi a számlázási hatás, ha a szinapszis hivatkozást Engedélyezem a Azure Cosmos DB adatbázis-fiókban?

Nincsenek. A rendszer csak akkor számítja fel a díjat, amikor létrehoz egy analitikai tárolót engedélyező tárolót, és megkezdi az adattöltést.


## <a name="security"></a>Biztonság

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Milyen módon lehet hitelesíteni az analitikai tárolót?

Az analitikai tárolóval való hitelesítés megegyezik a tranzakciós tárolóval. Egy adott adatbázis esetében az elsődleges vagy csak olvasható kulccsal végezheti el a hitelesítést. Az Azure szinapszis Studióban használhatja a társított szolgáltatást, hogy megakadályozza a Azure Cosmos DB kulcsok beillesztését a Spark-jegyzetfüzetekben. A társított szolgáltatáshoz való hozzáférés mindenki számára elérhető, aki hozzáfér a munkaterülethez.

## <a name="synapse-run-times"></a>Szinapszis-futtatási idő

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Mi a jelenleg támogatott szinapszis-futtatási idő a Azure Cosmos DB Analytical Store-hoz való hozzáféréshez?

|Azure szinapszis futtatókörnyezet |Jelenlegi támogatás |
|---------|---------|
|Azure szinapszis Spark-készletek | Olvasás, írás (tranzakciós tárolón keresztül), tábla, ideiglenes nézet |
|Azure szinapszis kiszolgáló nélküli SQL-készlet    | Olvasás, megtekintés |
|Azure szinapszis SQL kiépítve   |  Nem elérhető |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Az Azure szinapszis Spark-táblái az Azure szinapszis kiszolgáló nélküli SQL Pool-tábláival szinkronizálhatók, ugyanúgy, mint a Azure Data Lake?

Ez a funkció jelenleg nem érhető el.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Használhatom a Spark strukturált streamet az analitikus áruházból?

Az Azure Cosmos DB jelenleg Spark strukturált adatfolyam-támogatást nyújt a tranzakciós tároló változási funkciójának módosításával, és ez a módszer még nem támogatott az analitikai tárolóban.

### <a name="is-streaming-supported"></a>Támogatott a streaming?

Nem támogatjuk az adatok streamjét az analitikus tárolóból.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Az Azure szinapszis Studióban hogyan ismerhető fel, hogy van-e csatlakoztatva egy Azure Cosmos DB-tárolóhoz az Analytics-tárolóval?

Az analitikai tárolóval engedélyezett Azure Cosmos DB tárolóban a következő ikon látható:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB tároló engedélyezve az analitikai tárolóval – ikon":::

A rendszer a következő ikonnal fogja megjeleníteni a tranzakciós tároló tárolóját:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB tároló engedélyezve a tranzakciós tárolóval – ikon":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Hogyan továbbíthatja Azure Cosmos DB hitelesítő adatait az Azure szinapszis studióból?

Jelenleg Azure Cosmos DB hitelesítő adatokat a rendszer a társított szolgáltatásnak a Azure Cosmos DB adatbázisokhoz hozzáféréssel rendelkező felhasználó általi létrehozásakor adja át. A tárolóhoz való hozzáférés a munkaterülethez hozzáférő más felhasználók számára is elérhető.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [Azure szinapszis-hivatkozás előnyeit](synapse-link.md#synapse-link-benefits)

* Ismerje meg az [Azure szinapszis-kapcsolat és a Azure Cosmos db közötti integrációt](synapse-link.md#synapse-link-integration).
