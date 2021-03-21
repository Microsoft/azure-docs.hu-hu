---
title: Gyakran ismételt kérdések a Table APIról Azure Cosmos DB
description: Választ kaphat a Table APIával kapcsolatos gyakori kérdésekre Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341235"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Gyakran ismételt kérdések a Table APIról Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

A Azure Cosmos DB Table API a [Azure Portalban](https://portal.azure.com) érhető el, először regisztrálnia kell egy Azure-előfizetésre. A regisztráció után hozzáadhat egy Azure Cosmos DB Table API fiókot az Azure-előfizetéséhez, majd hozzáadhat táblákat a fiókjához. A támogatott nyelveket és a kapcsolódó gyors kezdéseket a [Azure Cosmos DB Table API bevezetésében](table-introduction.md)találhatja meg.

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Table API Azure Cosmos DB vs Azure Table Storage-ban

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Miben tér el a Table API és az Azure Table Storage működése?

Vannak olyan viselkedési eltérések, amelyeket a felhasználók az Azure Table Storage-ból érkeznek, és a Azure Cosmos DB Table API táblákat szeretnének létrehozni:

* Azure Cosmos DB a Table API fenntartott kapacitási modellt használ a garantált teljesítmény biztosításához, de ez azt jelenti, hogy az egyik a tábla létrehozásakor a kapacitást a lehető leghamarabb kifizeti, még akkor is, ha a kapacitás nincs használatban. Az Azure Table Storage szolgáltatással csak a felhasznált kapacitásért fizet. Ez segít megmagyarázni, hogy a Table API miért nyújthat 10 MS Read és 15 MS írási SLA-t a esetek 99% percentilis-ben, az Azure Table Storage pedig 10 másodperces SLA-t biztosít. Ennek következményeként azonban Table API táblákat, akár üres táblákat is anélkül, hogy kérések nélkül, pénzbe kerül, hogy a kapacitás elérhető legyen a Azure Cosmos DB által kínált SLA-ra irányuló kérések kezeléséhez.

* A Table API által visszaadott lekérdezési eredményeket nem rendezi a rendszer a partíciós kulcs/sor kulcs sorrendjében, mivel azok az Azure Table Storage-ban találhatók.

* A sorok kulcsa legfeljebb 255 bájt lehet.

* A kötegek legfeljebb 2 MB-ot tartalmazhatnak.

* A CORS jelenleg nem támogatott.

* Az Azure Table Storage-beli táblanév nem különbözteti meg a kis-és nagybetűket, Azure Cosmos DB Table API.

* A kódolási információk (például bináris mezők) Azure Cosmos DB belső formátuma jelenleg nem annyira hatékony, mint az egyik. Emiatt ez váratlan korlátozásokat okozhat az adatméretnél. Például jelenleg nem sikerült a Table entitás teljes készletének használata a bináris adat tárolására, mert a kódolás növeli az adat méretét.

* Az entitás "ID" tulajdonságának neve jelenleg nem támogatott.

* A TableQuery TakeCount nem korlátozódik a 1000-re.

* A REST API a Azure Cosmos DB Table API által nem támogatott végpontok/lekérdezési beállítások száma:

  | Rest metódus (ok) | Rest-végpont/lekérdezési beállítás | Doc URL-címek | Magyarázat |
  | ------------| ------------- | ---------- | ----------- |
  | LETÖLTÉS, PUT | `/?restype=service@comp=properties`| A [Table szolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/set-table-service-properties) és a [Table szolgáltatás tulajdonságainak beolvasása](/rest/api/storageservices/get-table-service-properties) | Ez a végpont a CORS-szabályok, a tárolási elemzési konfiguráció és a naplózási beállítások beállítására szolgál. A CORS jelenleg nem támogatott, és az elemzési és a naplózási módokat az Azure Storage-táblák Azure Cosmos DB eltérő módon kezelik. |
  | BEÁLLÍTÁSOK | `/<table-resource-name>` | [Repülés előtti CORS-tábla kérése](/rest/api/storageservices/preflight-table-request) | Ez a CORS része, amely Azure Cosmos DB jelenleg nem támogatott. |
  | GET | `/?restype=service@comp=stats` | [Table szolgáltatás statisztikáinak beolvasása](/rest/api/storageservices/get-table-service-stats) | Információt nyújt arról, hogy az adatok milyen gyorsan replikálódnak az elsődleges és a formátumú másodlagos zónák között. Ez nem szükséges Cosmos DBban, mert a replikálás része az írásoknak. |
  | LETÖLTÉS, PUT | `/mytable?comp=acl` | [Táblázat ACL lekérése](/rest/api/storageservices/get-table-acl) és a [tábla ACL beállítása](/rest/api/storageservices/set-table-acl) | Ez lekérdezi és beállítja a megosztott hozzáférési aláírások (SAS) kezelésére használt tárolt hozzáférési szabályzatokat. Bár a SAS támogatott, a rendszer másként állítja be és kezeli őket. |

* A Azure Cosmos DB Table API csak a JSON formátumot támogatja, az ATOMot nem.

* Míg a Azure Cosmos DB támogatja a közös hozzáférésű aláírásokat (SAS), bizonyos szabályzatok nem támogatottak, kifejezetten a felügyeleti műveletekhez kapcsolódóan, például az új táblák létrehozásához való jogosultsággal.

* A .NET SDK esetében bizonyos osztályok és metódusok vannak, amelyeket a Azure Cosmos DB jelenleg nem támogat.

  | Osztály | Nem támogatott metódus |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (ez az osztály elavult) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Egyéb gyakori kérdések

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Szükségem van egy új SDK-ra a Table API használatához?

Nem, a meglévő Storage SDK-k továbbra is működni tudnak. Azt azonban javasoljuk, hogy az egyik mindig a legújabb SDK-kat kapja a legjobb támogatáshoz, és sok esetben a kiváló teljesítményt. Tekintse meg az elérhető nyelvek listáját a [Azure Cosmos DB Table API bevezetésében](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Milyen kapcsolati sztringet kell használnia a Table APIhoz való kapcsolódáshoz?

A kapcsolatok karakterlánca:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

A Azure Portal a kapcsolatok karakterlánc oldaláról kérheti le a kapcsolatok karakterláncát.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hogyan felülbírálja a kérés beállításainak konfigurációs beállításait a .NET SDK-ban a Table APIhoz?

Egyes beállítások kezelése a CreateCloudTableClient metódussal történik, a másik pedig az ügyfélalkalmazás appSettings szakaszának app.configén keresztül. További információ a konfigurációs beállításokról: [Azure Cosmos db képességek](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Van-e változás a meglévő Azure Table Storage SDK-kat használó ügyfeleknél?

Nincsenek. A meglévő és az új ügyfelek esetében nem történt változás a meglévő Azure Table Storage SDK-k használatával.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hogyan a Azure Cosmos DB tárolt táblázatos adattábla-adatmegjelenítést a Table API használatával?

A Azure Portal használatával böngészheti az adatfájlokat. Használhatja a következő válaszban említett Table API kódot vagy eszközöket is.

### <a name="which-tools-work-with-the-table-api"></a>Mely eszközök működnek a Table API?

Használhatja a [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Azok az eszközök, amelyeknek a rugalmassága a korábban megadott formátumban a kapcsolódási karakterlánc elvégzésére szolgál, támogathatja az új Table API. A tábla eszközeinek listáját az [Azure Storage-ügyfél eszközei](../storage/common/storage-explorers.md) oldalon található.

### <a name="is-the-concurrency-on-operations-controlled"></a>Az a párhuzamosság az ellenőrzött műveleteken?

Igen, az optimista párhuzamosságot a ETag mechanizmus használatával biztosítjuk.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Támogatott-e az OData-lekérdezési modell az entitások esetében?

Igen, a Table API támogatja a OData-lekérdezési és a LINQ-lekérdezést.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Csatlakozhatok az Azure Table Storagehoz, és Azure Cosmos DB Table API egymás mellett ugyanazon alkalmazásban?

Igen, csatlakozhat a CloudTableClient két különálló példányának létrehozásával, amelyek mindegyike a saját URI-ra mutat a kapcsolati karakterláncon keresztül.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hogyan migrálni egy meglévő Azure Table Storage-alkalmazást erre az ajánlatra?

A [AzCopy](../storage/common/storage-use-azcopy-v10.md) és a [Azure Cosmos db adatáttelepítési eszköz](import-data.md) egyaránt támogatott.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan történik a szolgáltatás tárolási méretének bővítése, ha például egy *n* GB adattal kezdek, és az adataim az idő múlásával 1 TB-ra növekednek?

Azure Cosmos DB úgy lett kialakítva, hogy a horizontális skálázás használatával korlátlan tárhelyet biztosítson. A szolgáltatás nyomon követheti és hatékonyan növelheti a tárterületet.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hogyan figyeli a Table API ajánlatot?

A kérelmek és a tárhely használatának figyeléséhez használhatja a Table API **metrikák** ablaktáblát.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan kiszámítja a szükséges átviteli sebességet?

A kapacitás-kalkulátor használatával kiszámíthatja a műveletekhez szükséges TableThroughput. További információ: [becsült kérelmek egységei és adattárolása](https://www.documentdb.com/capacityplanner). Általánosságban megjelenítheti az entitást JSON-ként, és megadhatja a műveleteinek számát.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Helyileg is használhatom az Table API SDK-t az emulátorral?

Jelenleg nem.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Használhatom a meglévő alkalmazást a Table API?

Igen, ugyanazt az API-t támogatja.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Át kell-e telepíteni a meglévő Azure Table Storage-alkalmazásokat az SDK-ba, ha nem szeretném használni az Table API-szolgáltatásokat?

Nem, a meglévő Azure Table Storage-eszközöket bármilyen típusú megszakítás nélkül hozhatja létre és használhatja. Ha azonban nem használja a Table API, az Automatikus indexelés, a további konzisztencia-beállítás vagy a globális terjesztés nem részesülhet előnyben.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hogyan az Azure-ban több régióban is megadhatja az adatreplikációt a Table APIban?

Az Azure Cosmos DB portál [globális replikációs beállításaival](tutorial-global-distribution-sql-api.md#portal) hozzáadhat olyan régiókat, amelyek megfelelőek az alkalmazáshoz. Globálisan elosztott alkalmazások fejlesztéséhez az alkalmazásnak a helyi régió számára beállított PreferredLocation-információval kell rendelkeznie, hogy alacsony olvasási késést biztosítson.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hogyan módosítja a fiók elsődleges írási régióját a Table API?

A Azure Cosmos DB globális replikációs portál panel használatával hozzáadhat egy régiót, majd átadhatja a feladatokat a szükséges régióhoz. Útmutatásért lásd: [fejlesztés többrégiós Azure Cosmos db-fiókokkal](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hogyan a saját előnyben részesített olvasási régiókat az adatelosztáskor alacsony késés érdekében?

A helyi helyről való olvasáshoz használja a PreferredLocation kulcsot a app.config fájlban. Meglévő alkalmazások esetén a Table API hibát jelez, ha a Blobrequestoptions locationmode be van állítva. Távolítsa el ezt a kódot, mert a Table API az app.config fájlból veszi fel ezeket az információkat. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hogyan érdemes meggondolni az Table API konzisztencia-szintjét?

Azure Cosmos DB jól indokolt kompromisszumot biztosít a konzisztencia, a rendelkezésre állás és a késés között. A Azure Cosmos DB öt konzisztencia-Table API szintet biztosít a fejlesztők számára, így kiválaszthatja a megfelelő konzisztencia-modellt a tábla szintjén, és egyéni kéréseket végezhet az adatlekérdezés során. Amikor egy ügyfél csatlakozik, megadhatja a konzisztencia szintjét. A szintet a CreateCloudTableClient consistencyLevel argumentumának használatával módosíthatja.

A Table API alacsony késleltetésű olvasást biztosít a "saját írások olvasása" kifejezéssel, amely az alapértelmezett értékkel határos késéssel rendelkezik. További információ: konzisztencia- [szintek](consistency-levels.md).

Alapértelmezés szerint az Azure Table Storage erős konzisztenciát biztosít a régión belül és a másodlagos helyen lévő végleges konzisztenciát.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Table API az Azure Table Storage-nál nagyobb konzisztencia-szintet kínál?

Igen, a Azure Cosmos DB elosztott jellegéből származó előnyökkel kapcsolatos információkért lásd: [konzisztencia-szintek](consistency-levels.md). Mivel a rendszer garanciákat biztosít a konzisztencia-szintekhez, nyugodtan használhatja őket.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha engedélyezve van a globális terjesztés, mennyi ideig tart az adatreplikálás?

Azure Cosmos DB véglegesíti az tartósan a helyi régióban, és azonnal leküldi az adatmennyiséget a többi régióba ezredmásodpercben. Ez a replikálás csak az adatközpont időpontra (RTT) függ. Ha többet szeretne megtudni a Azure Cosmos DB globális elosztási képességeiről, tekintse meg a [Azure Cosmos db: globálisan elosztott adatbázis-szolgáltatás az Azure](distribute-data-globally.md)-ban című témakört.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosítható az olvasási kérelmek konzisztenciáji szintje?

A Azure Cosmos DB használatával beállíthatja a konzisztencia szintjét a tároló szintjén (a táblán). A .NET SDK használatával megváltoztathatja a szintet úgy, hogy megadja a TableConsistencyLevel kulcs értékét a app.config fájlban. A lehetséges értékek: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. További információkért lásd: [Azure Cosmos db hangolt adatkonzisztencia-szintjei](consistency-levels.md). A legfontosabb elképzelés az, hogy a kérelem konzisztencia-szintje nem állítható be a tábla beállításától eltérő értékre. Nem állíthatja be például a tábla konzisztencia-szintjét, és a kérések konzisztencia-szintje erős.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Table API a feladatátvételt, ha egy régió leáll?

A Table API a Azure Cosmos DB globálisan elosztott platformját használja. Annak érdekében, hogy az alkalmazás képes legyen az adatközpontok leállásának elvégzésére, legalább egy régiót be kell állítani a Azure Cosmos DB-portálon a [többrégiós Azure Cosmos db-fiókokkal fejlesztett](high-availability.md)fiókhoz. A régió prioritását beállíthatja a [többrégiós Azure Cosmos db-fiókokkal fejlesztett](high-availability.md)portál használatával.

A fiókhoz tetszőleges számú régiót adhat hozzá, és szabályozhatja, hogy a feladatátvételi prioritás biztosításával miként lehet átadni a feladatokat. Az adatbázis használatához meg kell adnia egy alkalmazást is. Ha így tesz, az ügyfelek nem fognak tapasztalni állásidőt. A [legújabb .net Client SDK](table-sdk-dotnet.md) automatikus vezérlés, de a többi SDK nem. Ez azt is megteheti, hogy felismeri a régiót, és automatikusan átadja a feladatátvételt az új régiónak.

### <a name="is-the-table-api-enabled-for-backups"></a>Engedélyezve van-e a biztonsági mentések Table API?

Igen, a Table API a biztonsági mentések Azure Cosmos DB platformját használja. A biztonsági mentések automatikusan történnek. További információkért tekintse meg [az online biztonsági mentést és a visszaállítást a Azure Cosmos DBával](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Az Table API indexeli az entitás összes attribútumát alapértelmezés szerint?

Igen, az entitások összes attribútuma alapértelmezés szerint indexelve van. További információ [: Azure Cosmos db: indexelési házirendek](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Ez azt jelenti, hogy nem kell egynél több indexet létrehozni a lekérdezések kielégítéséhez?

Igen, Azure Cosmos DB Table API az összes attribútum automatikus indexelését biztosítja a séma definíciója nélkül. Ez az Automation felszabadítja a fejlesztőket, hogy az index létrehozása és kezelése helyett az alkalmazásra összpontosítsanak. További információ [: Azure Cosmos db: indexelési házirendek](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Módosíthatom az indexelési szabályzatot?

Igen, az indexelési házirendet megadhatja az index definíciójának megadásával. A beállításokat megfelelően kell kódolni és elmenekülnie.

A non-.NET SDK-k esetében az indexelési házirend csak a portálon állítható be **Adatkezelőon**, navigáljon a módosítani kívánt táblához, majd nyissa meg a **méretezési & beállításait**– >indexelési házirend, végezze el a kívánt módosításokat, majd **mentse**.

A .NET SDK-ból a app.config fájlban is elküldhető:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Úgy tűnik, hogy a platform számos képességgel rendelkezik, mint például a rendezés, az összesítések, a hierarchia és az egyéb funkciók. Azure Cosmos DB Felveszi ezeket a képességeket a Table API?

A Table API ugyanazokat a lekérdezési funkciókat biztosítja, mint az Azure Table Storage. Az Azure Cosmos DB a rendezést, az összesítéseket, a földrajzi lekérdezéseket, a hierarchiát és számos különféle beépített funkciót is támogat. További információ: SQL- [lekérdezések](./sql-query-getting-started.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Mikor kell módosítani a Table API TableThroughput?

A TableThroughput akkor kell módosítania, ha a következő feltételek valamelyike teljesül:

* Az adatok kinyerését, átalakítását és betöltését (ETL) végzi, vagy rövid idő alatt nagy mennyiségű adatmennyiséget szeretne feltölteni.
* Több átviteli sebességre van szüksége a tárolóból vagy a háttérbeli tárolók készletében. Láthatja például, hogy a felhasznált átviteli sebesség meghaladja a kiépített átviteli sebességet, és a szabályozása folyamatban van. További információ: [az átviteli sebesség beállítása az Azure Cosmos-tárolók számára](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Felskálázás vagy leskálázás a Table API-táblázat átviteli sebességét?

Igen, az adatátviteli sebesség méretezéséhez használhatja a Azure Cosmos DB portál méretezési paneljét. További információ: [set átviteli sebesség](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Alapértelmezett TableThroughput van beállítva az újonnan kiosztott táblákhoz?

Igen, ha nem bírálja felül a TableThroughput app.configon keresztül, és nem használ előre létrehozott tárolót a Azure Cosmos DBban, a szolgáltatás egy 400-os átviteli sebességgel rendelkező táblázatot hoz létre.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Módosult az Azure Table Storage szolgáltatás meglévő ügyfeleinek díjszabása?

Nincsenek. A meglévő Azure Table Storage-ügyfelek árát nem lehet megváltoztatni.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hogyan számítja ki a Table API árát?

Az ár a lefoglalt TableThroughput függ.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hogyan kezelni a Table API ajánlat tábláira vonatkozó korlátozásokat?

Ha a kérelmek aránya nagyobb, mint az alapul szolgáló tároló vagy a tárolók készletének kiépített átviteli kapacitása, hibaüzenetet kap, és az SDK újrapróbálkozik a hívással az újrapróbálkozási szabályzat alkalmazásával.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Miért van szükség a PartitionKey és a RowKey melletti átviteli sebesség kiválasztására, hogy kihasználhassa a Azure Cosmos DB Table API ajánlatát?

Azure Cosmos DB beállítja a tároló alapértelmezett átviteli sebességét, ha nem ad meg egyet a app.config fájlban vagy a portálon keresztül.

A Azure Cosmos DB a teljesítményre és a késésre vonatkozó garanciákat biztosít, és a felső korláttal működik. Ez a garancia akkor lehetséges, ha a motor érvényesítheti a bérlő műveleteinek irányítását. A TableThroughput beállítása biztosítja, hogy megkapja a garantált átviteli sebességet és a késést, mivel a platform fenntartja ezt a kapacitást, és garantálja a működés sikerességét.

Az átviteli sebesség meghatározásával rugalmasan változtathatja meg az alkalmazás szezonális használatát, teljesítheti az adatátviteli igényeket, és megtakaríthatja a költségeket.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Az Azure Table Storage szolgáltatás olcsó volt számomra, mert csak az Adattárolásért kell fizetnie, és ritkán kérdezem le. Úgy tűnik, hogy a Azure Cosmos DB Table API ajánlat akkor is megtöltődik, ha egyetlen tranzakciót sem hajtottak végre, vagy semmit nem tároltam. El tudja magyarázni?

A Azure Cosmos DB globálisan elosztott, SLA-alapú rendszernek van kialakítva, amely garantálja a rendelkezésre állást, a késést és az átviteli sebességet. Ha Azure Cosmos DB-ban foglalt adatátviteli kapacitást foglal le, akkor az más rendszerek átviteli sebességével ellentétben garantált. Azure Cosmos DB az ügyfelek által kért további képességeket biztosít, mint például a másodlagos indexek és a globális terjesztés.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem kaptam meg a kvóta teljes "értesítését (amely azt jelzi, hogy a partíció megtelt), amikor az Azure Table Storage-ba tölt be adatot. A Table API a következő üzenet jelenik meg:. Ez az ajánlat korlátozza a szolgáltatást, és kényszeríti a meglévő alkalmazás módosítását?

Azure Cosmos DB egy SLA-alapú rendszer, amely korlátlan léptéket biztosít, és garantálja a késést, az átviteli sebességet, a rendelkezésre állást és a konzisztenciát. A prémium szintű garantált teljesítmény biztosításához ellenőrizze, hogy az adatméret és az index kezelhető és méretezhető-e. Az entitások vagy elemek számának 20 GB-os korlátozása a partíciós kulcson alapul, így biztosítva, hogy nagy keresési és lekérdezési teljesítményt biztosítunk. Annak biztosítása érdekében, hogy az alkalmazás megfelelően méretezhető legyen, még az Azure Storage esetében is, javasoljuk, hogy *ne* hozzon létre egy gyors partíciót úgy, hogy az összes információt egy partícióban tárolja, és lekérdezi azt.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Így PartitionKey és RowKey is szükség van a Table API?

Igen. Mivel a Table API felülete hasonló az Azure Table Storage SDK-hoz, a partíciós kulcs hatékony módszert biztosít az adatterjesztéshez. A sor kulcsa egyedi a partíción belül. A sor kulcsának jelen kell lennie, és nem lehet null értékű a standard SDK-ban. A RowKey hossza 255 bájt, és a PartitionKey hossza 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Mik a Table API hibaüzenetei?

Az Azure Table Storage és a Azure Cosmos DB Table API ugyanazokat az SDK-kat használja, így a legtöbb hiba ugyanaz lesz.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Miért kell szabályozni a szabályozást, amikor megpróbálok sok táblázatot létrehozni egy másik után a Table API?

Azure Cosmos DB egy SLA-alapú rendszer, amely késést, teljesítményt, rendelkezésre állást és konzisztencia-garanciákat biztosít. Mivel ez egy kiépített rendszer, a megtartja az erőforrásokat a követelmények garantálása érdekében. A táblázatok gyors létrehozásának gyakorisága észlelhető és szabályozható. Javasoljuk, hogy tekintse meg a táblák létrehozásának sebességét, és csökkentse kevesebb, mint 5 percenként. Ne feledje, hogy a Table API egy kiépített rendszer. A kiépítés pillanatában meg kell fizetnie.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan visszajelzést ad az SDK-ról vagy a hibákról?

A következő módokon megoszthatja a visszajelzéseit:

* [Felhasználói vélemény](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft Q&egy kérdés oldal](/answers/topics/azure-cosmos-db.html)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). A Stack Overflow a programozási kérdésekre legmegfelelőbb. Győződjön meg arról, hogy a kérdés [a témában](https://stackoverflow.com/help/on-topic) van, és a [lehető legtöbb részletet biztosít, így a kérdés egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Következő lépések

* [Table API-alkalmazás létrehozása .NET SDK-val és Azure Cosmos DB](create-table-dotnet.md)
* [Java-alkalmazás létrehozása Azure Cosmos DB Table API-alapú adatkezeléshez](create-table-java.md)