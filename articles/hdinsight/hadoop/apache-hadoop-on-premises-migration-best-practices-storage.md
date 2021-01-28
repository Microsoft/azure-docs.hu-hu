---
title: 'Tárterület: helyszíni Apache Hadoop migrálása az Azure HDInsight'
description: Ismerje meg a helyszíni Hadoop-fürtök Azure HDInsight való áttelepítésére vonatkozó ajánlott eljárásokat.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 8d87d2164a5131b71a2000243c37553610497750
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944859"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Helyszíni Apache Hadoop-fürtök migrálása az Azure HDInsight

Ez a cikk az Azure HDInsight Systems adattárolási szolgáltatásával kapcsolatos ajánlásokat ismerteti. Egy sorozat része, amely ajánlott eljárásokat biztosít a helyszíni Apache Hadoop rendszerek Azure HDInsight való áttelepítésének segítésére.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>A HDInsight-fürtök megfelelő tárolási rendszerének kiválasztása

A helyszíni Apache Hadoop fájlrendszer (HDFS) címtár-struktúrája újra létrehozható az Azure Blob Storage-ban vagy Azure Data Lake Storageban. Ezután biztonságosan törölheti a számításhoz használt HDInsight-fürtöket a felhasználói adatvesztés nélkül. Mindkét szolgáltatás a HDInsight-fürt alapértelmezett fájlrendszerének és egy további fájlrendszerének is használható. A HDInsight-fürtnek és a Storage-fióknak ugyanabban a régióban kell lennie.

### <a name="azure-storage"></a>Azure Storage

A HDInsight-fürtök az Azure Storage-ban lévő BLOB-tárolót az alapértelmezett fájlrendszerként vagy egy további fájlrendszerként is használhatják. A standard szintű Storage-fiók támogatott a HDInsight-fürtökkel való használathoz. A Premier szintű csomag nem támogatott. Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Egy blob-tároló megosztása, mivel a több fürt alapértelmezett fájlrendszere nem támogatott.

A létrehozási folyamatban és a hozzájuk tartozó kulcsokban definiált tárolási fiókok a `%HADOOP_HOME%/conf/core-site.xml` fürtcsomópontokon találhatók. A HDFS konfigurációjában a Ambari felhasználói felületén a "Custom Core site" szakaszban is elérhetők. Alapértelmezés szerint a Storage-fiók kulcsa titkosítva van, és a rendszer egy egyéni visszafejtési parancsfájlt használ a kulcsok visszafejtéséhez, mielőtt a rendszer átadja a Hadoop démonoknak. A feladatok, például a kaptár, a MapReduce, a Hadoop streaming és a Pig, elvégezhetik a Storage-fiókok és-metaadatok leírását.

Az Azure Storage lehet földrajzilag replikálható. Bár a Geo-replikáció földrajzi helyreállítást és adatredundanciát biztosít, a földrajzilag replikált helyre történő feladatátvétel jelentős hatással van a teljesítményre, és további költségekkel járhat. A javaslat célja, hogy a Geo-replikációt okosan válassza, és csak akkor, ha az adatértékek további költségeket érnek el.

Az alábbi formátumok egyike használható az Azure Storage-ban tárolt adateléréshez:

|Adatelérési formátum |Description |
|---|---|
|`wasb:///`|Az alapértelmezett tárterületet titkosítatlan kommunikációval érheti el.|
|`wasbs:///`|Az alapértelmezett tároló elérése titkosított kommunikáció használatával.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Nem alapértelmezett Storage-fiókkal való kommunikációhoz használatos. |

A [standard szintű Storage-fiókok méretezhetőségi céljai](../../storage/common/scalability-targets-standard-account.md) az Azure Storage-fiókok aktuális korlátozásait sorolja fel. Ha az alkalmazás igényei meghaladják az egyetlen Storage-fiók skálázhatósági céljait, az alkalmazás több Storage-fiók használatára is felépíthető, majd az adatobjektumok particionálása a Storage-fiókok között.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) az összes tárolási szolgáltatáshoz biztosít metrikákat, és a Azure Portal konfigurálható a diagramok használatával megjeleníthető mérőszámok gyűjtése. A riasztások akkor hozhatók létre, ha elérik a tárolási erőforrás metrikáinak küszöbértékeit.

Az Azure Storage helyreállítható [törlést biztosít a blob-objektumok](../../storage/blobs/soft-delete-blob-overview.md) számára az adatok helyreállításához, amikor egy alkalmazás vagy más Storage-fiók felhasználója véletlenül módosította vagy törölte az adatokat.

Létrehozhat blob- [pillanatképeket](/rest/api/storageservices/creating-a-snapshot-of-a-blob). A pillanatkép egy adott időpontban végrehajtott blob írásvédett verziója, amely lehetővé teszi a Blobok biztonsági mentését. A pillanatkép létrehozása után a fájl olvasható, másolható vagy törölhető, de nem módosítható.

> [!Note]
> A helyszíni Hadoop-disztribúciók régebbi verziói esetében, amelyeknek nincs "wasbs" tanúsítványa, importálni kell őket a Java Trust Store-ba.

A következő módszerek használhatók tanúsítványok importálására a Java Trust Store-ba:

Az Azure Blob TLS/SSL-tanúsítvány letöltése fájlba

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

A fenti fájl importálása a Java Trust Store-ba az összes csomóponton

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Ellenőrizze, hogy a hozzáadott tanúsítvány szerepel-e a megbízhatósági tárolóban.

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

További információért tekintse át a következő cikkeket:

- [Az Azure Storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-blob-storage.md)
- [A standard szintű Storage-fiókok méretezhetőségi céljai](../../storage/common/scalability-targets-standard-account.md)
- [A blob Storage méretezhetőségi és teljesítménybeli céljai](../../storage/blobs/scalability-targets.md)
- [A Microsoft Azure Storage teljesítmény- és skálázhatósági ellenőrzőlistája](../../storage/blobs/storage-performance-checklist.md)
- [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Tárfiók monitorozása az Azure Portalon](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

Azure Data Lake Storage Gen1 megvalósítja a HDFS és a POSIX stílusú hozzáférés-vezérlési modellt. Első osztályú integrációt biztosít az Azure AD-vel a részletes hozzáférés-vezérléshez. A tárolt adatmennyiségnek nincs korlátja, vagy a nagymértékben párhuzamos elemzések futtatására is lehetőség van.

További információért tekintse át a következő cikkeket:

- [HDInsight-fürtök létrehozása Data Lake Storage Gen1 használatával a Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

Azure Data Lake Storage Gen2 a legújabb tárolási ajánlat. A Azure Data Lake Storage Gen1 első generációjának alapképességeit egyesíti az Azure Blob Storageba közvetlenül integrált Hadoop-kompatibilis fájlrendszer-végponttal. Ez a fejlesztés ötvözi az objektumok tárterületének méretezési és költséghatékonysági előnyeit, és a megbízhatóság és a teljesítmény jellemzően csak a helyszíni fájlrendszerek esetében van társítva.

A 2. generációs Azure Data Lake Storage az [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) -ra épül, és lehetővé teszi az adatkapcsolatot a fájlrendszer és az objektum tárolási paradigma használatával. A [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), például a fájlrendszer szemantikai, a fájl szintű biztonság és a skálázás funkciói az alacsony költségeket, a többszintű tárolást, a magas rendelkezésre állást és a vész-helyreállítási képességeket, valamint az [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md)-ból származó nagy SDK/eszköz-ökoszisztémát kombinálják. Data Lake Storage Gen2 az objektumok tárterületének összes tulajdonsága továbbra is az elemzési számítási feladatokhoz optimalizált fájlrendszer-felület előnyeit egészíti ki.

A Data Lake Storage Gen2 alapvető funkciója a blob Storage szolgáltatás [hierarchikus névtérének](../../storage/blobs/data-lake-storage-namespace.md) hozzáadása, amely objektumokat és fájlokat szervez a könyvtárak hierarchiájában az elvégezhető adathozzáféréshez. A hierarchikus struktúra lehetővé teszi olyan műveletek használatát, mint például a címtár átnevezése vagy törlése, hogy az egyetlen atomi metaadat-művelet legyen a címtárban, nem pedig az összes olyan objektum enumerálása és feldolgozása, amelyik a címtár nevének előtagját használja.

A múltban a felhőalapú elemzések a teljesítmény, a felügyelet és a biztonság területén is veszélyeztethetik a biztonságot. A Azure Data Lake Storage-(ADLS-) Gen2 legfontosabb funkciói a következők:

- **Hadoop-kompatibilis hozzáférés**: a Azure Data Lake Storage Gen2 lehetővé teszi az adatkezelést és az adathozzáférést ugyanúgy, mint a [HADOOP elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS-illesztőprogram](../../storage/blobs/data-lake-storage-abfs-driver.md) az [Azure HDInsight](../index.yml)-ban található összes Apache Hadoop környezetben elérhető. Ez az illesztőprogram lehetővé teszi a Data Lake Storage Gen2ban tárolt adatelérést.

- **A POSIX-engedélyek felülbírálása**: a Data Lake Gen2 biztonsági modellje teljes mértékben támogatja az ACL-és a POSIX-engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. A beállítások rendszergazdai eszközökön vagy a kaptáron vagy a Sparkon keresztül konfigurálhatók.

- **Költséghatékony**: Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat tartalmaz. Ahogy a teljes életciklusán keresztül változik, a számlázási díjak a beépített funkciókkal, például az [Azure Blob Storage életciklusával](../../storage/blobs/storage-lifecycle-management-concepts.md)változnak a költségek csökkentése érdekében.

- **A blob Storage-eszközökkel,-keretrendszerekkel és-** alkalmazásokkal használható: Data Lake Storage Gen2 továbbra is együttműködik a blob Storage-hoz jelenleg létező eszközök, keretrendszerek és alkalmazások széles körével.

- **Optimalizált illesztőprogram**: az Azure Blob fájlrendszer-illesztőprogram (ABFS) kifejezetten Big Data elemzéshez van [optimalizálva](../../storage/blobs/data-lake-storage-abfs-driver.md) . A megfelelő REST API-k a dfs.core.windows.net elosztott fájlrendszerbeli végponton keresztül vannak felszínen.

A következő formátumok egyike használható a ADLS Gen2ban tárolt adateléréshez:
- `abfs:///`: A fürthöz tartozó alapértelmezett Data Lake Storage elérése.
- `abfs://file_system@account_name.dfs.core.windows.net`: Nem alapértelmezett Data Lake Storagesal való kommunikációhoz használatos.

További információért tekintse át a következő cikkeket:

- [Az Azure Data Lake Storage Gen2 bemutatása](../../storage/blobs/data-lake-storage-introduction.md)
- [Az Azure Blob fájlrendszer-illesztőprogramja (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Azure Storage-kulcsok biztonságossá tétele a helyszíni Hadoop-fürt konfigurációján belül

A Hadoop konfigurációs fájljaihoz hozzáadott Azure Storage-kulcsok, a helyszíni HDFS és az Azure Blob Storage közötti kapcsolat létesítése. Ezek a kulcsok a Hadoop hitelesítőadat-szolgáltatói keretrendszerrel titkosítva is védhetők. A titkosítást követően biztonságosan tárolhatók és érhetők el.

**A hitelesítő adatok kiépítése:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**A fenti szolgáltató elérési útjának hozzáadása a core-site.xmlhoz vagy a Ambari-konfigurációhoz az egyéni mag – hely területen:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> A szolgáltatói elérési út tulajdonság a distcp parancssorhoz is hozzáadható ahelyett, hogy a kulcsot a fürt szintjén tárolja a core-site.xml a következő módon:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Az Azure Storage-adathozzáférés korlátozása SAS használatával

A HDInsight alapértelmezés szerint teljes hozzáféréssel rendelkezik a fürthöz társított Azure Storage-fiókokban lévő összes adathoz. A blob tárolón a közös hozzáférésű aláírások (SAS) használhatók az adathozzáférés korlátozására, például csak olvasási hozzáféréssel rendelkező felhasználók számára.

### <a name="using-the-sas-token-created-with-python"></a>A Python használatával létrehozott SAS-jogkivonat használata

1. Nyissa meg a  [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) fájlt, és módosítsa a következő értékeket:

    |Jogkivonat tulajdonsága|Description|
    |---|---|
    |policy_name|A létrehozandó házirend létrehozásához használandó név.|
    |storage_account_name|A Storage-fiók neve.|
    |storage_account_key|A Storage-fiók kulcsa.|
    |storage_container_name|A Storage-fiók azon tárolója, amelyhez korlátozni kívánja a hozzáférést.|
    |example_file_path|A tárolóba feltöltött fájl elérési útja.|

2. A SASToken.py-fájl tartalmazza az `ContainerPermissions.READ + ContainerPermissions.LIST` engedélyeket, és a használati eset alapján módosítható.

3. Futtassa a szkriptet a következőképpen: `python SASToken.py`

4. Az alábbi szöveghez hasonló SAS-tokent jeleníti meg a parancsfájl befejeződése után: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. A megosztott hozzáférési aláírással rendelkező tárolóhoz való hozzáférés korlátozásához vegyen fel egy egyéni bejegyzést a fürt alapszintű konfigurációjában a Ambari HDFS-konfigurációk speciális egyéni mag-hely hozzáadása tulajdonság alatt.

6. Használja a következő értékeket a **kulcs** és **érték** mezőkhöz:

    **Kulcs**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **érték**: a Python-alkalmazás által visszaadott sas-kulcs a fenti 4. lépéssel.

7. Kattintson a **Hozzáadás** gombra a kulcs és az érték mentéséhez, majd kattintson a **Save (Mentés** ) gombra a konfigurációs módosítások mentéséhez. Ha a rendszer kéri, adja meg a módosítás leírását (például "SAS-tároló-hozzáférés hozzáadása"), majd kattintson a **Mentés** gombra.

8. A Ambari webes FELÜLETén válassza a bal oldali listából a HDFS elemet, majd kattintson a jobb oldalon található szolgáltatási műveletek legördülő listából az **összes érintett újraindítása** elemre. Ha a rendszer kéri, válassza **az összes újraindításának megerősítése** lehetőséget.

9. Ismételje meg ezt a folyamatot a MapReduce2 és a fonal esetében.

A SAS-jogkivonatok Azure-beli használatával kapcsolatban három fontos dolgot kell figyelembe venni:

1. Ha a SAS-jogkivonatok "READ + LIST" engedélyekkel jönnek létre, a blob-tárolóhoz hozzáféréssel rendelkező felhasználók nem tudják "írni és törölni" az adatbevitelt. Azok a felhasználók, akik az adott SAS-tokenhez hozzáférnek a blob-tárolóhoz, és megpróbálnak írási vagy törlési műveletet végrehajtani, például a következő üzenet jelenik meg: `"This request is not authorized to perform this operation"`

2. Ha a SAS-jogkivonatok `READ + LIST + WRITE` engedélyekkel jönnek létre ( `DELETE` csak korlátozás esetén), akkor az olyan parancsokat, mint az `hadoop fs -put` első írás `\_COPYING\_` fájlba, majd próbálja meg átnevezni a fájlt. Ez a HDFS művelet leképezi a `copy+delete` WASB. Mivel az `DELETE` engedély nem lett megadva, a "put" művelet sikertelen lesz. A `\_COPYING\_` művelet egy Hadoop-szolgáltatás, amely némi Egyidejűség-vezérlést biztosít. Jelenleg nincs lehetőség arra, hogy csak a "Törlés" műveletet kelljen korlátozni anélkül, hogy az "írás" műveletet is befolyásolná.

3. Sajnos a Hadoop hitelesítő adatok szolgáltatója és a visszafejtési kulcs szolgáltatója (ShellDecryptionKeyProvider) jelenleg nem működik az SAS-jogkivonatokkal, így jelenleg nem lehet védeni a láthatóságot.

További információ: az [Azure Storage közös hozzáférésű aláírások használata a HDInsight lévő adatokhoz való hozzáférés korlátozásához](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Az adattitkosítás és a replikáció használata

Az Azure Storage-ba írt összes adatforgalom automatikusan [Storage Service encryption (SSE)](../../storage/common/storage-service-encryption.md)használatával lesz titkosítva. Az Azure Storage-fiókban tárolt adattárolási szolgáltatás mindig magas rendelkezésre állású. A Storage-fiók létrehozásakor a következő replikációs lehetőségek közül választhat:

- [Helyileg redundáns tárolás (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Zónaredundáns tárolás (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Georedundáns tárolás (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Írásvédett georedundáns tárolás (RA-GRS)](../../storage/common/storage-redundancy.md)

Az Azure Storage helyileg redundáns tárolást (LRS) biztosít, de a kritikus fontosságú adatmennyiséget egy másik régióban található másik Azure Storage-fiókba is másolhatja, a vész-helyreállítási terv igényeihez igazított gyakorisággal. Különböző módszerekkel másolhatók az adatmásolások, például a [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), a [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), a [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)vagy a [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Javasoljuk továbbá, hogy az Azure Storage-fiókhoz tartozó hozzáférési szabályzatokat a véletlen törlés megakadályozása érdekében érvényesítse.

További információért tekintse át a következő cikkeket:

- [Azure Storage-replikáció](../../storage/common/storage-redundancy.md)
- [Vészhelyzeti útmutató a Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>További Azure Storage-fiókok csatlakoztatása a fürthöz

A HDInsight-létrehozási folyamat során egy Azure Storage-fiók, Azure Data Lake Storage Gen1 vagy Azure Data Lake Storage Gen2 lesz kiválasztva alapértelmezett fájlrendszerként. Ezen alapértelmezett Storage-fiókon kívül további Storage-fiókok is hozzáadhatók ugyanahhoz az Azure-előfizetéshez vagy különböző Azure-előfizetésekhez a fürt létrehozási folyamata során vagy a fürt létrehozása után.

A következő módokon adhat hozzá további Storage-fiókot:
- Ambari HDFS config Advanced Custom Core – hely hozzáadása a Storage-fiók neve és kulcsa a szolgáltatások újraindítása
- [Parancsfájl-művelet](../hdinsight-hadoop-add-storage.md) használata a Storage-fiók nevének és kulcsának átadásával

> [!Note]
> Érvényes használati esetekben az Azure-tárterületre vonatkozó korlátok az [Azure-támogatásra](https://azure.microsoft.com/support/faq/)vonatkozó kéréssel növelhetők.

További információkért lásd: [Add additional storage accounts to HDInsight](../hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikket ebben a sorozatban: az [adatáttelepítés ajánlott eljárásai a helyszíni Azure HDInsight Hadoop áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
