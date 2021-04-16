---
title: Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban
description: Választ kaphat az olyan területeken Synapse Link kérdésekre Azure Cosmos DB kérdésekre, mint a számlázás, az elemzésitár, a biztonság és az elemzésitár-használathoz való idő.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483826"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link for Azure Cosmos DB szoros integrációt hoz létre a Azure Cosmos DB és a Azure Synapse Analytics. Lehetővé teszi az ügyfelek számára, hogy közel valós idejű elemzéseket futtassanak a működési adataikon, teljes teljesítmény-elkülönítéssel a tranzakciós számítási feladatoktól és ETL-folyamat nélkül. Ez a cikk az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="general-faq"></a>Gyakori kérdések – általános

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>A Azure Synapse Link minden api-Azure Cosmos DB támogatott?

Azure Synapse Link a Azure Cosmos DB SQL (Core) API és a MongoDB-hez Azure Cosmos DB API esetén támogatott. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>A Azure Synapse Link támogatott a többrépontos és Azure Cosmos DB fiókok esetében?

Igen, a többrépontos Azure Cosmos-fiókok esetében az elemzési tárolóban tárolt adatok is globálisan el vannak osztva. Az Azure Synapse Analyticsről végzett elemzési lekérdezések a legközelebbi helyi régióról szolgálhatók ki, függetlenül attól, hogy egy vagy több írási régióról van szó.

Ha elemzési táras támogatással tervezi konfigurálni a Azure Cosmos DB-fiókot, javasoljuk, hogy a fiók létrehozásakor minden szükséges régiót hozzáadjon.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Engedélyezhetem a Azure Synapse Linket csak egy adott régióhoz, nem pedig a többrégió-fiókhoz beállított összes régióhoz?

Ha Azure Synapse Link engedélyezve van egy többrégió-fiókhoz, az elemzési tároló minden régióban létrejön. A mögöttes adatok a tranzakciós tároló átviteli sebességére és tranzakciós konzisztenciára vannak optimalizálva.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Minden régióban támogatott az elemzési Azure Cosmos DB?

Igen.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Támogatott a biztonsági mentés és a visszaállítás Azure Synapse Link-kompatibilis fiókok esetében?

Az elemzési tárat bekapcsolt tárolók esetén az adatok automatikus biztonsági mentése és visszaállítása az elemzési tárolóban jelenleg nem támogatott. 

Ha Synapse Link engedélyezve van egy adatbázisfiókban, a Azure Cosmos DB [](./online-backup-and-restore.md) továbbra is automatikusan biztonsági másolatot készít a tárolók tranzakciós tárolóiban tárolt adatairól az ütemezett biztonsági mentési időközzel, ahogy mindig. Fontos megjegyezni, hogy ha egy elemzési tárolják bekapcsolt tárolót visszaállít egy új fiókba, a tároló csak tranzakciós tárolóval lesz visszaállva, és nincs engedélyezve az elemzési tároló. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Letiltható a Azure Synapse-fiókom hivatkozási Azure Cosmos DB funkció?

Jelenleg nem tilthatja le a Synapse Link képességet, miután fiókszinten engedélyezte azt. Fontos tudni, hogy nem jár költséggel, ha a Synapse Link képesség engedélyezve van a fiók szintjén, és nem találhatók elemzésitár-kompatibilis tárolók.

Ha ki kell kapcsolnia a képességet, kétféle lehetősége van. Az első lehetőség az, hogy törli, majd újból létrehozza az új Azure Cosmos DB-fiókot, ha szükséges, az adatok migrálásával. A második lehetőség a támogatási jegy nyitása, hogy segítséget kaphasson az adatok másik fiókba való migrálásához.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Az elemzési tároló hatással van a Cosmos DB-kra?

Nem, nincs hatása.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB elemzési tároló

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Engedélyezem az elemzési tárolót a meglévő tárolókon?

Az elemzési tároló jelenleg csak új tárolókhoz engedélyezhető (az új és a meglévő fiókokban egyaránt).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Letiltható az elemzésitár a Azure Cosmos DB tárolókon, miután engedélyeztem azt a tároló létrehozása során?

Az elemzési tár jelenleg nem tiltható le az Azure Cosmos DB-tárolókon, ha a tároló létrehozásakor engedélyezte azt.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Támogatott az elemzési tároló Azure Cosmos DB automatikus skálázású átviteli sebességet tartalmazó tárolókhoz?

Igen, az elemzési tároló engedélyezhető az automatikus skálázás által kiépített átviteli sebességet tartalmazó tárolókon.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Van bármilyen hatása a Azure Cosmos DB kiépített ru-kra?

Azure Cosmos DB biztosítja a teljesítmény elkülönítését a tranzakciós és az elemzési számítási feladatok között. Az elemzési tároló tárolón való engedélyezése nem befolyásolja a tranzakciós tárolón Azure Cosmos DB RU/s adatokat. Az elemzési tár tranzakciói (olvasási &) és tárolási költségei külön lesznek felszámolva. További [részletekért](analytical-store-introduction.md#analytical-store-pricing) tekintse Azure Cosmos DB elemzési tároló díjszabását.

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>Korlátozható a hálózati hozzáférés Azure Cosmos DB elemzési tárolóhoz?

Igen, konfigurálhat egy felügyelt privát [végpontot,](analytical-store-private-endpoints.md) és korlátozhatja az elemzési tároló hálózati hozzáférését a Azure Synapse virtuális hálózatra. A felügyelt privát végpontok privát kapcsolatot hoznak létre az elemzési tárolóval. 

A tranzakciós és az elemzésitár privát végpontokat is hozzáadhat ugyanakhoz a Azure Cosmos DB fiókhoz egy Azure Synapse Analytics munkaterületen. Ha csak elemzési lekérdezéseket szeretne futtatni, akkor csak az elemzési privát végpontot szeretné engedélyezni a Synapse Analytics munkaterületen.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Használhatok ügyfél által kezelt kulcsokat a Azure Cosmos DB elemzési tárolóval?

Zökkenőmentesen titkosíthatja az adatokat a tranzakciós és elemzési tárolókban ugyanazokkal az ügyfél által kezelt kulcsokkal, automatikus és átlátható módon. Ha ügyfél által felügyelt kulcsokat használ az elemzési tárolóval, a Azure Cosmos DB-fiókjának rendszer által hozzárendelt felügyelt identitását kell használnia a Azure Key Vault szabályzatban. Ezt itt [ismertetjük.](how-to-setup-cmk.md#using-managed-identity) Ezután engedélyeznie kell az elemzési tárolót a fiókjában.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Megjelennek a tranzakciós tároló törlési és frissítési műveletei az elemzési tárolóban?

Igen, a tranzakciós tárolóban található adatok törlése és frissítései megjelennek az elemzési tárolóban. Beállíthatja, hogy a tárolóban rendelkezésre áll idő (TTL) előzményadatokat is tartalmaz, így az elemzési tároló megőrzi az elemzési TTL-feltételeknek megfelelő elemek összes verzióját. További [részletekért tekintse meg az elemzési TTL](analytical-store-introduction.md#analytical-ttl) áttekintését.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Kapcsolódok az elemzési áruházhoz az elemzési motortól Azure Synapse Analytics?

Csak az Azure Synapse Analytics által biztosított különböző futtatókörnyezetekkel érhet el és futtathat lekérdezéseket az elemzési táron. Az elemzési tár a következők használatával kérdezhető le és elemezhető:

* Synapse Spark a Scala, Python, SparkSQL és C# teljes támogatásával. A Synapse Spark az adatfeldolgozási és -elemzési forgatókönyvek központi eleme
* Kiszolgáló nélküli SQL-készlet T-SQL nyelvvel és a jól ismert BI-eszközök támogatásával (például Power BI Premium stb.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Kapcsolódok az elemzésitárhoz a Synapse SQL kiépítve?

Az elemzési tároló jelenleg nem érhető el a Synapse SQL számára.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Visszaírhatja a Synapse lekérdezési összesítési eredményeit az elemzési tárolóba?

Az elemzési tároló egy csak olvasható tároló egy Azure Cosmos DB tárolóban. Így nem írhatja vissza közvetlenül az aggregációs eredményeket az elemzési tárolóba, de kiírhatja őket egy másik tároló Azure Cosmos DB tranzakciós tárolójában, amely később kiszolgáló rétegként használható.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>A tranzakciós tárolóból az elemzési tárba történő automatikus replikáció aszinkron vagy szinkron, és milyen késések vannak?

Az automatikus szinkronizálás késése általában 2 percen belül van. Nagy számú tárolóval megosztott átviteli sebességet biztosító adatbázisok esetén az egyes tárolók automatikus szinkronizálásának késése magasabb lehet, és akár 5 percet is igénybe vehet. Szeretnénk többet megtudni arról, hogyan illeszkedik ez a késés az Ön forgatókönyveibe. Ennek érdekében forduljon a Azure Cosmos DB [csapathoz.](mailto:cosmosdbsynapselink@microsoft.com)

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Vannak olyan forgatókönyvek, amelyekben a tranzakciós tároló elemeit nem propagálják automatikusan az elemzési tárolóba?

Ha a tároló adott [](analytical-store-introduction.md#analytical-schema)elemei megsértik a jól meghatározott elemzési sémát, azok nem fognak szerepelni az elemzési tárolóban. Ha egy jól meghatározott elemzési séma blokkol forgatókönyveket, küldjön e-mailt [Azure Cosmos DB csapatnak.](mailto:cosmosdbsynapselink@microsoft.com)

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Az elemzési adattárban található adatokat particionálhatja másképp, mint a tranzakciós tárolót?

Az elemzési tárban lévő adatok particionálása a tranzakciós tárolóban lévő szegmensek horizontális particionálásán alapul. Jelenleg nem választhat más particionálási stratégiát az elemzési tárhoz.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Testre szabhatom vagy felülbírálható a tranzakciós adatok oszlopos formátumra való átalakítása az elemzési tárolóban?

Az adatelemek jelenleg nem alakíthatóak át, amikor automatikusan propagálva vannak a tranzakciós tárolóból az elemzési tárolóba. Ha a korlátozás által blokkolt forgatókönyveket, küldjön e-mailt [a Azure Cosmos DB csapatának.](mailto:cosmosdbsynapselink@microsoft.com)

### <a name="is-analytical-store-supported-by-terraform"></a>Támogatja a Terraform az elemzési tárat?

A Terraform jelenleg nem támogatja az elemzési tár tárolóit. További információért tekintse meg a [Terraform GitHub-problémáit](https://github.com/hashicorp/terraform/issues) ismertető témakört.

## <a name="analytical-time-to-live-ttl"></a>Elemzési idő az élhez (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Az elemzési adatok TTL-e tároló- és elemszinten is támogatott?

Az elemzési adatok élettartama jelenleg csak a tároló szintjén konfigurálható, az elemek szintjén nem támogatott az elemzési élettartam beállítása.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Miután beállítottam a tárolószintű elemzési TTL-t egy Azure Cosmos DB tárolón, módosíthatok egy másik értékre később?

Igen, az elemzési TTL bármilyen érvényes értékre frissíthető. Az elemzési [TTL-ről](analytical-store-introduction.md#analytical-ttl) további információt az elemzési TTL-t tartalmazó cikkben talál.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Frissíthetek vagy törölhetek egy elemet az elemzési tárolóból, miután az TTL-t kivesz a tranzakciós tárolóból?

A rendszer minden tranzakciós frissítést és törlést átmásol az elemzési tárolóba, de ha az elemet kiürítik a tranzakciós tárolóból, akkor az nem frissíthető az elemzési tárolóban. További tudnivalókért tekintse meg az [elemzési TTL-cikket.](analytical-store-introduction.md#analytical-ttl)

## <a name="billing"></a>Számlázás

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Mi a hivatkozáshoz Azure Synapse számlázási Azure Cosmos DB?

A Azure Synapse Link számlázási modellje tartalmazza a Azure Cosmos DB elemzési tároló és a Synapse-futtatás használatával felmerült költségeket. További tudnivalókért tekintse meg az [elemzési Azure Cosmos DB és](analytical-store-introduction.md#analytical-store-pricing) díjszabását Azure Synapse Analytics [cikkeket.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Milyen hatással van a számlázás, ha engedélyezem a Synapse Link az Azure Cosmos DB-fiókban?

Nincsenek. Csak akkor számítunk fel díjat, ha létrehoz egy elemzésitár-kompatibilis tárolót, és elkezdi betölteni az adatokat.


## <a name="security"></a>Biztonság

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Milyen módokon lehet hitelesíteni az elemzési tárolóval?

Az elemzési tárolóval való hitelesítés megegyezik a tranzakciós tárolóval. Egy adott adatbázis esetén az elsődleges vagy csak olvasható kulccsal hitelesíthet. A Azure Synapse Studióban a csatolt szolgáltatás használatával megakadályozhatja a Azure Cosmos DB-kulcsok beillesztését a Spark-jegyzetfüzetekbe. A linked service-hez való hozzáférés mindenki számára elérhető, aki hozzáféréssel rendelkezik a munkaterülethez.

Kiszolgáló nélküli Synapse SQL-készletek használata esetén lekérdezheti az Azure Cosmos DB elemzési tárolót úgy, hogy előre létrehozza a fiókkulcsokat tároló SQL-hitelesítő adatokat, és ezekre hivatkozik az OPENROWSET függvényben. További információ: Lekérdezés kiszolgáló nélküli SQL-készlet használatával a [Azure Synapse Link című cikkben.](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

## <a name="synapse-run-times"></a>Synapse-futtatás

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Melyek a Synapse jelenleg támogatott futási idői az elemzési Azure Cosmos DB eléréséhez?

|Azure Synapse futásidejű |Aktuális támogatás |
|---------|---------|
|Azure Synapse Spark-készletek | Olvasás, Írás (tranzakciós tárolón keresztül), Tábla, Ideiglenes nézet |
|Azure Synapse SQL-készlet használata    | Olvasás, Megtekintés |
|Azure Synapse SQL kiépítve   |  Nem elérhető |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>A spark Azure Synapse tábláim ugyanúgy szinkronizálnak Azure Synapse kiszolgáló nélküli SQL-készlet tábláival, mint az Azure Data Lake-ben?

Ez a funkció jelenleg nem érhető el.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Tudok strukturált Spark-streamelést az elemzésitárból?

Jelenleg a Spark strukturált streamelési támogatása Azure Cosmos DB a tranzakciós tároló változáscsatorna funkcióját használja, és az elemzési tároló még nem támogatja.

### <a name="is-streaming-supported"></a>Támogatott a streamelés?

Az elemzési tárolóból származó adatok streamelése nem támogatott.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>A Azure Synapse Studióban hogyan tudom felismerni, ha olyan Azure Cosmos DB tárolóhoz vagyok csatlakoztatva, ahol engedélyezve van az elemzési áruház?

Az Azure Cosmos DB tároló elemzési tárolóval való engedélyezése a következő ikonnal rendelkezik:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB elemzési tárolóval engedélyezett tároló – ikon":::

A tranzakciós tárolót a következő ikon jelöli:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB tároló tranzakciós tárolóval való engedélyezése – ikon":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Hogyan lehet átadni Azure Cosmos DB hitelesítő adatokat a Azure Synapse Studióból?

Jelenleg Azure Cosmos DB hitelesítő adatokat a rendszer a csatolt szolgáltatás létrehozásakor a felhasználónak, aki hozzáféréssel rendelkezik a Azure Cosmos DB adatbázisokhoz. Az áruházhoz való hozzáférés más felhasználók számára is elérhető, akik hozzáféréssel rendelkezik a munkaterülethez.

## <a name="next-steps"></a>Következő lépések

* A Azure Synapse [Link előnyeinek megismerása](synapse-link.md#synapse-link-benefits)

* Ismerje meg az [Azure Synapse Link és a Azure Cosmos DB.](synapse-link.md#synapse-link-integration)
