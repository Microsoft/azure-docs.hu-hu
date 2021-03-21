---
title: Azure Data Lake Storage migrálása a Gen1-ből a Gen2-be
description: Azure Data Lake Storage migrálása a Gen1-ből a Gen2-be, amely az Azure Blob Storage-ra épül, és a big data Analytics számára dedikált képességeket biztosít.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 875c046406b7989a39f09dde82640ed5567b6b3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714867"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Azure Data Lake Storage migrálása a Gen1-ből a Gen2-be

Az adatok, munkaterhelések és alkalmazások áttelepíthetők Data Lake Storage Gen1ról Data Lake Storage Gen2re.

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](storage-blobs-introduction.md) -ra épül, és a Big Data Analytics szolgáltatáshoz dedikált képességeket biztosít. A [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) a [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), például a fájlrendszer szemantikai, a könyvtári és a fájl szintű biztonságot és a méretezést az [Azure Blob Storage](storage-blobs-introduction.md)alacsony díjas, többszintű tárolásával, magas rendelkezésre állású/vész-helyreállítási képességeivel ötvözi.

> [!NOTE]
> A könnyebb olvashatóság érdekében ebben a cikkben a *Gen1* kifejezésre kell hivatkozni Azure Data Lake Storage Gen1ra, és a *Gen2* kifejezésre kell hivatkozni Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Ajánlott megközelítés

A Gen2 való Migrálás a következő megközelítést ajánljuk.

: heavy_check_mark: 1. lépés: készültség felmérése

: heavy_check_mark: 2. lépés: Felkészülés az áttelepíteni

: heavy_check_mark: 3. lépés: az adatok és az alkalmazások számítási feladatainak áttelepítenie

: heavy_check_mark: 4. lépés: a Gen1 és a Gen2 közötti átváltás

> [!NOTE]
> A Gen1 és a Gen2 különböző szolgáltatások, ezért nincs helyben történő verziófrissítés, a szándékos migrációs erőkifejtés szükséges. 

### <a name="step-1-assess-readiness"></a>1. lépés: készültség felmérése

1. További információ a [Data Lake Storage Gen2 ajánlatról](https://azure.microsoft.com/services/storage/data-lake-storage/); a szolgáltatás előnyeit, költségeit és általános architektúráját. 

2. [Hasonlítsa össze a Gen1 képességeit](#gen1-gen2-feature-comparison) a Gen2-mel. 

3. Tekintse át az [ismert problémák](data-lake-storage-known-issues.md) listáját, hogy felmérje az esetleges hiányosságokat a funkcionalitásban.

4. A Gen2 támogatja a blob Storage szolgáltatásait, például a [diagnosztikai naplózást](../common/storage-analytics-logging.md), a [hozzáférési szinteket](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatait](storage-lifecycle-management-concepts.md). Ha ezen funkciók bármelyikét érdemes használni, tekintse át az [aktuális támogatási szintet](./data-lake-storage-supported-blob-storage-features.md).

5. Tekintse át az [Azure ökoszisztéma-támogatás](./data-lake-storage-multi-protocol-access.md) aktuális állapotát, és győződjön meg arról, hogy a Gen2 minden olyan szolgáltatást támogat, amelytől a megoldások függenek.

### <a name="step-2-prepare-to-migrate"></a>2. lépés: Felkészülés az áttelepíteni

1. Azonosítsa az áttelepíteni kívánt adatkészleteket.

   Ezzel a lehetőséggel törölheti azokat az adatkészleteket, amelyeket már nem használ. Ha nem tervezi egyszerre az összes adatátvitelt, akkor a fázisokban áttelepíthető adatlogikai csoportok azonosításához használja ezt az időt.
   
2. Állapítsa meg, hogy az áttelepítés milyen hatással lesz a vállalatra.

   Tegyük fel például, hogy az áttelepítés megkezdése előtt bármilyen állásidőt biztosít. Ezek a szempontok segítenek a megfelelő áttelepítési minta azonosításában és a legmegfelelőbb eszközök kiválasztásában.

3. Hozzon létre egy áttelepítési tervet. 

   Ezeket az [áttelepítési mintákat](#migration-patterns)ajánljuk. Választhatja a minták egyikét, kombinálhatja őket, vagy megtervezheti saját egyéni mintáját.

### <a name="step-3-migrate-data-workloads-and-applications"></a>3. lépés: adatok, munkaterhelések és alkalmazások migrálása

Adatok, munkaterhelések és alkalmazások migrálása a kívánt mintázat használatával. Javasoljuk, hogy fokozatosan érvényesítse a forgatókönyveket.

1. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md) , és engedélyezze a hierarchikus névtér funkciót. 

2. Migrálja adatait. 

3. Konfigurálja a [munkaterhelések szolgáltatásait](./data-lake-storage-supported-azure-services.md) úgy, hogy az a Gen2-végpontra mutasson. 
   
4. Alkalmazások frissítése Gen2 API-k használatára. Lásd: a [.net](data-lake-storage-directory-file-acl-dotnet.md), a [Java](data-lake-storage-directory-file-acl-java.md), a [Python](data-lake-storage-directory-file-acl-python.md), a [JavaScript](data-lake-storage-directory-file-acl-javascript.md) és a [Rest](/rest/api/storageservices/data-lake-storage-gen2)útmutatók. 
   
5. Parancsfájlok frissítése Data Lake Storage Gen2 PowerShell- [parancsmagok](data-lake-storage-directory-file-acl-powershell.md)és [Azure CLI-parancsok](data-lake-storage-directory-file-acl-cli.md)használatához.
   
6. Keressen olyan URI-referenciákat, amelyek tartalmazzák a karakterláncot a `adl://` Databricks, vagy a Apache HIVE HQL-fájlokat, illetve a számítási feladatok részeként használt bármely más fájlt. Cserélje le ezeket a hivatkozásokat az új [Gen2 formázott URI azonosítóra](data-lake-storage-introduction-abfs-uri.md) . Például: a Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` lehet `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. Konfigurálja a fiókja biztonságát úgy, hogy az tartalmazza az [Azure-szerepköröket](../common/storage-auth-aad-rbac-portal.md), a [fájl-és a mappa szintű biztonságot](data-lake-storage-access-control.md), valamint az [Azure Storage-tűzfalakat és a virtuális hálózatokat](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>4. lépés: a Gen1 és a Gen2 közötti átváltás

Miután meggyőződött arról, hogy alkalmazásai és munkaterhelései stabilak a Gen2, elkezdheti a Gen2 használatát az üzleti forgatókönyvek kielégítése érdekében. Kapcsolja ki a Gen1-on futó fennmaradó folyamatokat, és szerelje le a Gen1-fiókját. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2-képességek

Ez a táblázat összehasonlítja a Gen1 képességeit a Gen2.

|Terület |Gen1   |Gen2 |
|---|---|---|
|Adatszervezet|[Hierarchikus névtér](data-lake-storage-namespace.md)<br>Fájlok és mappák támogatása|[Hierarchikus névtér](data-lake-storage-namespace.md)<br>Tároló, fájl és mappa támogatása |
|Georedundancia| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [ra-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Hitelesítés|[HRE felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Szolgáltatásnevek](../../active-directory/develop/app-objects-and-service-principals.md)|[HRE felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Szolgáltatásnevek](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Megosztott elérési kulcs](/rest/api/storageservices/authorize-with-shared-key)|
|Engedélyezés|Felügyelet – [Azure RBAC](../../role-based-access-control/overview.md)<br>Adathozzáférés – [ACL](data-lake-storage-access-control.md) -EK|Felügyelet – [Azure RBAC](../../role-based-access-control/overview.md)<br>Adatacl [](data-lake-storage-access-control.md)-EK, [Azure-RBAC](../../role-based-access-control/overview.md) |
|Titkosítás – inaktív adatok|Kiszolgálóoldali – a [Microsoft által felügyelt](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy az [ügyfél által felügyelt](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kulcsokkal|Kiszolgálóoldali – a [Microsoft által felügyelt](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy az [ügyfél által felügyelt](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kulcsokkal|
|VNET-támogatás|[VNET-integráció](../../data-lake-store/data-lake-store-network-security.md)|[Szolgáltatási végpontok](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [magánhálózati végpontok](../common/storage-private-endpoints.md)|
|Fejlesztői élmény|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Általánosan elérhető – [Rest](/rest/api/storageservices/data-lake-storage-gen2), [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Nyilvános előzetes verzió – [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [POWERSHELL](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Erőforrásnaplók|Klasszikus naplók<br>[Integrált Azure Monitor](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klasszikus naplók](../common/storage-analytics-logging.md) – általánosan elérhető<br>[Integrált Azure monitor](monitor-blob-storage.md) – előzetes verzió|
|Ökoszisztéma|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 vagy újabb)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 és újabb)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure szinapszis Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 Gen2-mintákhoz

Válasszon egy áttelepítési mintát, majd szükség szerint módosítsa a mintát.

|||
|---|---|
|**Emelés és váltás**|A legegyszerűbb minta. Ideális, ha az adatfolyamatok leállást biztosítanak.|
|**Növekményes másolat**|Hasonló a *lifthez és a váltáshoz*, de kevesebb állásidővel. Ideális nagy mennyiségű, a másolást hosszabb ideig elfoglaló adatforgalomhoz.|
|**Kettős folyamat**|Olyan folyamatok esetében ideális, amelyek nem tudnak leállást biztosítani.|
|**Kétirányú szinkronizálás**|A *kettős folyamathoz* hasonlóan, de egy többfázisú megközelítéssel, amely bonyolultabb folyamatokhoz is alkalmas.|

Ismerkedjen meg közelebbről az egyes mintákhoz.
 
### <a name="lift-and-shift-pattern"></a>Emelési és eltolási minta

Ez a legegyszerűbb minta. 

1. Állítsa le az összes írást a Gen1.

2. Adatok áthelyezése a Gen1 a Gen2-be. Javasoljuk, hogy [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Az ACL-ek az adattal együtt másolhatók.

3. Mutasson a műveletek és a számítási feladatok Gen2.

4. A Gen1 leszerelése.

Tekintse meg a lift és a SHIFT minta minta kódját a lift és a [SHIFT áttelepítési mintában](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md).

> [!div class="mx-imgBorder"]
> ![emelési és eltolási minta](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>A lift és a SHIFT minta használatának szempontjai

: heavy_check_mark: a Gen1 és a Gen2 közötti átváltás az összes munkaterhelés esetében egyszerre.

: heavy_check_mark: várható állásidő az áttelepítés során és a átváltás időszakban.

: heavy_check_mark: ideális olyan folyamatok számára, amelyek leállást biztosítanak, és az összes alkalmazás egyszerre frissíthető.

### <a name="incremental-copy-pattern"></a>Növekményes másolási minta

1. Az adatok áthelyezésének megkezdése a Gen1 és a Gen2 között. Javasoljuk, hogy [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Az ACL-ek az adattal együtt másolhatók.

2. Az új adatok növekményes másolása a Gen1.

3. Az összes adat másolása után állítsa le az összes írást a Gen1, és mutasson a számítási feladatok Gen2.

4. A Gen1 leszerelése.

Tekintse meg a növekményes másolási minta növekményes másolási mintájának mintáját [.](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md)


> [!div class="mx-imgBorder"]
> ![Növekményes másolási minta](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>A növekményes másolási minta használatának szempontjai:

: heavy_check_mark: a Gen1 és a Gen2 közötti átváltás az összes munkaterhelés esetében egyszerre.

: heavy_check_mark: a átváltás időszakban csak a leállás várható.

: heavy_check_mark: ideális olyan folyamatokhoz, ahol az összes alkalmazás egyszerre frissül, de az adatmásoláshoz több idő szükséges.

### <a name="dual-pipeline-pattern"></a>Kettős folyamat mintája

1. Adatok áthelyezése a Gen1 a Gen2-be. Javasoljuk, hogy [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Az ACL-ek az adattal együtt másolhatók.

2. Az új adatmennyiséget Gen1 és Gen2 is betöltheti.

3. A számítási feladatok Gen2.

4. Állítsa le az összes írást a Gen1, majd szerelje le a Gen1.

Tekintse meg a kétfolyamatos [áttelepítési mintában](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md)a kettős feldolgozási minta mintáját.

> [!div class="mx-imgBorder"]
> ![Kettős folyamat mintája](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>A kettős feldolgozási minta használatának szempontjai:

: heavy_check_mark: a Gen1 és a Gen2 folyamatok párhuzamosan futnak.

: heavy_check_mark: a nulla állásidőt támogatja.

: heavy_check_mark: ideális olyan helyzetekben, amikor a munkaterhelések és az alkalmazások nem biztosítanak semmilyen állásidőt, és mindkét Storage-fiókban betölthető.

### <a name="bi-directional-sync-pattern"></a>Kétirányú szinkronizálási minta

1. A kétirányú replikáció beállítása a Gen1 és a Gen2 között. Javasoljuk, hogy [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Javítási funkciót kínál a meglévő adatszolgáltatásokhoz.

3. Ha minden lépés befejeződött, állítsa le az összes írást a Gen1, és kapcsolja ki a kétirányú replikációt.

4. A Gen1 leszerelése.

Tekintse meg a kétirányú szinkronizálási minta mintáját a [kétirányú szinkronizációs áttelepítési mintában](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md).

> [!div class="mx-imgBorder"]
> ![Kétirányú minta](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>A kétirányú szinkronizálási minta használatának szempontjai:

: heavy_check_mark: ideális olyan összetett forgatókönyvek esetében, amelyek nagy mennyiségű folyamatot és függőséget foglalnak magukban, ahol a szakaszos megközelítés több értelmet is igénybe vehet.  

: heavy_check_mark: az áttelepítési erőfeszítés magas, de párhuzamosan támogatja a Gen1 és a Gen2.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a Storage-fiók biztonsági beállításainak különböző részeit. Lásd: az [Azure Storage biztonsági útmutatója](./security-recommendations.md).
- Optimalizálja a Data Lake Store teljesítményét. Lásd: [Azure Data Lake Storage Gen2 optimalizálása a teljesítményhez](data-lake-storage-performance-tuning-guidance.md)
- Tekintse át a Data Lake Store kezelésével kapcsolatos ajánlott eljárásokat. Lásd: [ajánlott eljárások Azure Data Lake Storage Gen2 használatához](data-lake-storage-best-practices.md)