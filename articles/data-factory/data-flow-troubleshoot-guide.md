---
title: Az adatfolyamatok leképezésének hibakeresése
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory adatáramlási problémáit.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.openlocfilehash: 72ab685b58f7d940fe4d682cacba6212fe80ced8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933083"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory adatforgalmának hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az adatfolyamatok Azure Data Factoryban történő leképezésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-error-codes-and-messages"></a>Gyakori hibakódok és üzenetek 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-végrehajtó-SourceInvalidPayload
- **Üzenet**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása nem sikerült, mert a tároló nem létezik
- **OK**: az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban.
- **Javaslat**: Ellenőrizze, hogy az adatkészletben hivatkozott tároló létezik-e, és hogy elérhető-e.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson

- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: a nem támogatott kódolás, a sérült bájtok vagy a JSON-forrás egyetlen dokumentumként való használata sok beágyazott sorban.
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a **JSON-beállítások** elemet, és kapcsolja be az **egy dokumentumot**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-végrehajtó-BroadcastTimeout

- **Üzenet**: szórásos csatlakozás időtúllépési hibája, győződjön meg arról, hogy a szórásos adatfolyam 60 másodpercen belül, hibakeresési futtatások és 300 a feladat futtatásakor
- **OK**: a szórás a hibakeresési és a 300 másodperces 60 másodperces időkorlátot futtatja a feladatok futtatásakor. A szórásra kiválasztott adatfolyam túl nagy ahhoz, hogy az adott korláton belül adatmennyiséget hozzon létre.
- **Javaslat**: a csatlakozáshoz, a létezéshez és a kereséshez tekintse meg az **optimalizálás** lapot az adatfolyam-transzformációk lapon. A szórás alapértelmezett beállítása **automatikus**. Ha az **automatikus** beállítás be van állítva, vagy ha manuálisan állítja be a bal vagy a jobb oldali szórást a **rögzített** értékre, akkor beállíthat egy nagyobb Azure Integration Runtime (IR) konfigurációt vagy kikapcsolhatja a szórást. A legjobb teljesítmény az adatforgalomban azt javasoljuk, hogy engedélyezze a Spark számára az **automatikus** küldést, és használjon memóriát optimalizált Azure IR. 
 
  Ha hibakeresési folyamat futása során futtatja az adatfolyamatot hibakeresési teszt végrehajtás közben, akkor gyakrabban is futtathatja ezt az állapotot. Ennek az az oka, hogy a Azure Data Factory a szórási időtúllépést 60 másodpercre szabályozza a gyorsabb hibakeresési élmény fenntartása érdekében. Az időtúllépést kiterjesztheti egy aktivált Futtatás 300 másodperces időtúllépésére. Ehhez használhatja a **hibakeresési**  >  **tevékenység futtatókörnyezete** lehetőséget a végrehajtás adatáramlási folyamat tevékenységében definiált Azure IR használatára.

- **Üzenet**: szórásos csatlakozás időtúllépési hibája esetén a probléma elkerülése érdekében a csatlakozás/létező/keresési átalakítás lehetőségnél kiválaszthatja a szórásos küldés lehetőséget. Ha a teljesítmény javítása érdekében szeretne csatlakozni a csatlakozáshoz, akkor győződjön meg arról, hogy a szórásos stream 60 másodpercen belül képes adatok létrehozására a hibakeresési és a 300-es mp-ben a feladatok futtatásakor.
- **OK**: a szórás a hibakeresési futtatásokban és a 300 másodpercben, a feladatok futtatásakor 60 másodperces időkorlátot tartalmaz. A szórásos csatlakozás esetében a szórásra kiválasztott stream túl nagy ahhoz, hogy az adott korláton belül adatmennyiséget hozzon létre. Ha nem használ szórásos csatlakozást, a adatfolyam alapértelmezett szórása ugyanezt a korlátot érheti el.
- **Javaslat**: kapcsolja ki a szórási beállítást, vagy Kerülje a nagyméretű adatfolyamok szórását, amelyek esetében a feldolgozás több mint 60 másodpercet vehet igénybe. Válasszon ki egy kisebb streamet a szóráshoz. A nagyméretű Azure SQL Data Warehouse táblák és forrásfájlok általában nem jó választás. Szórásos csatlakozás hiányában használjon nagyobb fürtöt, ha ez a hiba történik.

### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió

- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **OK**: az adatértékek nem a várt formátumban vannak.
- **Javaslat**: használja a megfelelő adattípust.

### <a name="error-code-df-executor-invalidcolumn"></a>Hibakód: DF-végrehajtó-InvalidColumn
- **Üzenet**: az oszlop nevét meg kell adni a lekérdezésben, egy aliast kell beállítania, ha SQL-függvényt használ
- **OK**: nincs megadva oszlopnév.
- **Javaslat**: állítsa be az aliast, ha olyan SQL-függvényt használ, mint a min () vagy a Max ().

### <a name="error-code-df-executor-drivererror"></a>Hibakód: DF-végrehajtó-DriverError
- **Üzenet**: a INT96 olyan örökölt időbélyeg-típus, amelyet az ADF adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusokra.
- **OK**: illesztőprogram-hiba.
- **Javaslat**: a INT96 olyan örökölt időbélyeg-típus, amelyet Azure Data Factory adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusra.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hibakód: DF-végrehajtó-BlockCountExceedsLimitError
- **Üzenet**: a nem véglegesített blokkok száma nem haladhatja meg a 100 000 blokk maximális korlátját. Keresse meg a blob konfigurációját.
- **OK**: a blobban a nem véglegesített blokkok maximális száma 100 000.
- **Javaslat**: a problémával kapcsolatos további információkért forduljon a Microsoft termék csapatához.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hibakód: DF-végrehajtó-PartitionDirectoryError
- **Üzenet**: a megadott forrás elérési út több particionált címtárral rendelkezik (például <Source Path> /<partíció gyökérkönyvtára 1>/a = 10/b = 20, <Source Path> /<partíció gyökérkönyvtára 2>/c = 10/d = 30) vagy particionált könyvtár más fájlokkal vagy nem particionált címtárral (például <Source Path> /<Partition Root Directory 1>/a = 10/b = 20, <Source Path> /mappa 2/file1), távolítsa el a partíció gyökérkönyvtárát a forrás elérési útról, és olvassa el külön forrás-átalakítással.
- **OK**: a forrás elérési útjának több particionált könyvtára vagy egy másik fájlt vagy nem particionált könyvtárat tartalmazó particionált könyvtár is van.
- **Javaslat**: távolítsa el a particionált gyökérkönyvtárat a forrás elérési útról, és olvassa el külön forrás-átalakítással.

### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **OK**: a deklarált típus adattípusa nem kompatibilis a tényleges paraméter értékével.
- **Javaslat**: Győződjön meg arról, hogy az adatfolyamnak átadott paraméterérték megfelel a deklarált típusnak.

### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető
- **OK**: a rendszer helytelen formázás miatt generált egy kifejezést a hibák elemzéséhez.
- **Javaslat**: vizsgálja meg a kifejezés formázását.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian
- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztések implicit Descartes-as termékei nem támogatottak. Ha az illesztésben oszlopok vannak használatban, hozzon létre egy egyedi kulcsot.
- **Javaslat**: a nem egyenlőségen alapuló illesztésekhez használjon kereszt-összekapcsolást.

### <a name="error-code-getcommand-outputasync-failed"></a>Hibakód: a GetCommand OutputAsync sikertelen
- **Üzenet**: az adatfolyam hibakeresése és az adatelőnézet: a GetCommand OutputAsync sikertelen volt...
- **OK**: Ez a hiba egy háttér-szolgáltatási hiba. 
- **Javaslat**: Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet. Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Hibakód: DF-végrehajtó-működése OutOfMemoryError
 
- **Üzenet**: a fürt kifogyott a memóriából a végrehajtás során, próbálkozzon újra egy integrációs modul használatával, amely nagyobb alapszámmal és/vagy memória-optimalizált számítási típussal rendelkezik.
- **OK**: a fürt elfogyott a memóriából.
- **Javaslat**: a hibakeresési fürtök fejlesztést jelentenek. Használja az adatok mintavételezését és a megfelelő számítási típust és méretet az adattartalom futtatásához. A teljesítménnyel kapcsolatos tippekért lásd: [az adatfolyamok teljesítményére vonatkozó útmutató leképezése](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Hibakód: DF-végrehajtó-illegalArgument

- **Üzenet**: Ellenőrizze, hogy helyes-e a társított szolgáltatás elérési kulcsa
- **OK**: a fiók neve vagy a hozzáférési kulcs helytelen.
- **Javaslat**: Ellenőrizze, hogy helyes-e a társított szolgáltatásban megadott fiók neve vagy elérési kulcsa. 

### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen.
- **OK**: érvénytelen vagy nem elérhető oszlopnév van használatban egy kifejezésben.
- **Javaslat**: vizsgálja meg a kifejezésekben használt oszlopnevek nevét.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Hibakód: DF-végrehajtó-OutOfDiskSpaceError
- **Üzenet**: belső kiszolgálóhiba
- **OK**: a fürtön elfogyott a szabad lemezterület.
- **Javaslat**: próbálkozzon újra a folyamattal. Ha ez nem oldja meg a problémát, forduljon az ügyfélszolgálathoz.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Hibakód: DF-végrehajtó-StoreIsNotDefined
- **Üzenet**: az áruház konfigurációja nincs definiálva. Ezt a hibát a folyamat Érvénytelen paraméter-hozzárendelése okozta.
- **OK**: nem meghatározott.
- **Javaslat**: a paraméter érték-hozzárendelésének ellenőrzését a folyamatban. Egy paraméter kifejezése érvénytelen karaktereket tartalmazhat.


### <a name="error-code-4502"></a>Hibakód: 4502
- **Üzenet**: jelentős egyidejű MappingDataflow-végrehajtás történt, amely a Integration Runtime alatti szabályozás miatt hibákat okoz.
- **OK**: nagy mennyiségű adatfolyam-tevékenység fut egyidejűleg az integrációs modulban. További információ: [Azure Data Factory korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Javaslat**: Ha több adatfolyam-tevékenységet szeretne párhuzamosan futtatni, Ossza szét őket több integrációs modul között.


### <a name="error-code-invalidtemplate"></a>Hibakód: InvalidTemplate
- **Üzenet**: a folyamat kifejezése nem értékelhető ki.
- **OK**: az adatfolyam-tevékenységben átadott egyik folyamat kifejezése nem megfelelően van feldolgozva egy szintaktikai hiba miatt.
- **Javaslat**: Ellenőrizze a tevékenység figyelését a kifejezés ellenőrzéséhez.

### <a name="error-code-2011"></a>Hibakód: 2011
- **Üzenet**: a tevékenység Azure Integration Runtime fut, és nem tudta visszafejteni a saját üzemeltetésű Integration Runtime keresztül csatlakoztatott adattár vagy számítás hitelesítő adatait. Ellenőrizze az ehhez a tevékenységhez társított társított szolgáltatások konfigurációját, és győződjön meg arról, hogy az integrációs modul megfelelő típusát használja.
- **OK**: az adatfolyam nem támogatja a társított szolgáltatásokat a saját üzemeltetésű integrációs modulokon.
- **Javaslat**: állítsa be az adatfolyamot felügyelt Virtual Network integrációs futtatókörnyezeten való futtatásra.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Hibakód: DF-XML-InvalidValidationMode
- **Üzenet**: érvénytelen XML-érvényesítési mód van megadva.
- **Javaslat**: a paraméter értékének ellenőrzése és a megfelelő ellenőrzési mód megadása.

### <a name="error-code-df-xml-invaliddatafield"></a>Hibakód: DF-XML-InvalidDataField
- **Üzenet**: a sérült rekordok mezőjének karakterlánc típusúnak és üresnek kell lennie.
- **Javaslat**: Győződjön meg arról, hogy a `\"_corrupt_record\"` forrás projektben szereplő oszlop sztring adattípussal rendelkezik.

### <a name="error-code-df-xml-malformedfile"></a>Hibakód: DF-XML-MalformedFile
- **Üzenet**: helytelen formátumú XML a következőben: "FailFastMode".
- **Javaslat**: frissítse az XML-fájl tartalmát a megfelelő formátumra.

### <a name="error-code-df-xml-invaliddatatype"></a>Hibakód: DF-XML-InvalidDataType
- **Üzenet**: az XML-elem alelemekkel vagy attribútumokkal rendelkezik, és nem konvertálható.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Hibakód: DF-XML-InvalidReferenceResource
- **Üzenet**: az XML-adatfájlban szereplő hivatkozási erőforrás nem oldható fel.
- **Javaslat**: az XML-adatfájlban tekintse meg a hivatkozási erőforrást.

### <a name="error-code-df-xml-invalidschema"></a>Hibakód: DF-XML-InvalidSchema
- **Üzenet**: a séma ellenőrzése nem sikerült.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Hibakód: DF-XML-UnsupportedExternalReferenceResource
- **Üzenet**: az XML-adatfájlban található külső hivatkozási erőforrás nem támogatott.
- **Javaslat**: az XML-fájl tartalmának frissítése, ha a külső hivatkozási erőforrás jelenleg nem támogatott.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Hibakód: DF-GEN2-InvalidAccountConfiguration
- **Üzenet**: meg kell adni a fiók vagy a bérlő/SpnId/SpnCredential/spnCredentialType vagy a MiServiceUri/miServiceToken egyikét.
- **Javaslat**: a megfelelő fiók konfigurálása a kapcsolódó GEN2 társított szolgáltatásban.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Hibakód: DF-GEN2-InvalidAuthConfiguration
- **Üzenet**: a három hitelesítési módszer (Key, SERVICEPRINCIPAL és mi) közül csak az egyik adható meg. 
- **Javaslat**: válassza ki a megfelelő hitelesítési típust a kapcsolódó GEN2 társított szolgáltatásban.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Hibakód: DF-GEN2-InvalidServicePrincipalCredentialType
- **Üzenet**: a ServicePrincipalCredentialType érvénytelen.

### <a name="error-code-df-gen2-invaliddatatype"></a>Hibakód: DF-GEN2-InvalidDataType
- **Üzenet**: a felhő típusa érvénytelen.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Hibakód: DF-blob-InvalidAccountConfiguration
- **Üzenet**: a fiók kulcsa vagy sas_token megadása kötelező.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Hibakód: DF-blob-InvalidAuthConfiguration
- **Üzenet**: a két hitelesítési módszer (kulcs, SAS) közül csak az egyik adható meg.

### <a name="error-code-df-blob-invaliddatatype"></a>Hibakód: DF-blob-InvalidDataType
- **Üzenet**: a felhő típusa érvénytelen.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Hibakód: DF-Cosmos-PartitionKeyMissed
- **Üzenet**: a partíciós kulcs elérési útját meg kell adni a frissítési és törlési műveletekhez.
- **Javaslat**: a partíció megadása a Cosmos mosogató beállításaiban.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Hibakód: DF-Cosmos-InvalidPartitionKey
- **Üzenet**: a partíciós kulcs elérési útja nem lehet üres a frissítési és törlési műveletekhez.
- **Javaslat**: a partíció megadása a Cosmos mosogató beállításaiban.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Hibakód: DF-Cosmos-IdPropertyMissed
- **Üzenet**: az "id" tulajdonságot le kell képezni a törlési és frissítési műveletekhez.
- **Javaslat**: Ellenőrizze, hogy a bemeneti adatok rendelkeznek `id` -e oszlopokkal a Cosmos-fogadó beállításaiban. Ha nem, használja az **átalakítás kiválasztása vagy származtatása lehetőséget** az oszlop létrehozásához a fogadó előtt.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Hibakód: DF-Cosmos-InvalidPartitionKeyContent
- **Üzenet**: a partíciós kulcsnak/értékkel kell kezdődnie.
- **Javaslat**: a partíciós kulcs kezdetének `/` beállítása a Cosmos fogadó beállításaiban, például: `/movieId` .

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Hibakód: DF-Cosmos-InvalidPartitionKey
- **Üzenet**: a partitionKey nincs leképezve a fogadóban a törlési és frissítési műveletekhez.
- **Javaslat**: a Cosmos-fogadó beállításaiban használja a tároló partíciós kulcsával megegyező partíciós kulcsot.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Hibakód: DF-Cosmos-InvalidConnectionMode
- **Üzenet**: érvénytelen connectionMode.
- **Javaslat**: Győződjön meg arról, hogy a támogatott mód az **átjáró** -és **DirectHttps** a Cosmos-beállításokban.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Hibakód: DF-Cosmos-InvalidAccountConfiguration
- **Üzenet**: meg kell adni a accountName vagy a accountEndpoint értéket.

### <a name="error-code-df-github-writenotsupported"></a>Hibakód: DF-GitHub-WriteNotSupported
- **Üzenet**: a GitHub-tároló nem engedélyezi az írást.

### <a name="error-code-df-pgsql-invalidcredential"></a>Hibakód: DF-PGSQL-InvalidCredential
- **Üzenet**: meg kell adni a felhasználót és a jelszót.
- **Javaslat**: Győződjön meg arról, hogy megfelelő hitelesítőadat-beállítások vannak a kapcsolódó PostgreSQL társított szolgáltatásban.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Hibakód: DF-hópehely-InvalidStageConfiguration
- **Üzenet**: csak a blob Storage-típus használható a hópehely írási/olvasási műveletben.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Hibakód: DF-hópehely-InvalidStageConfiguration
- **Üzenet**: a hópehely szakasz tulajdonságait meg kell adni az Azure Blob + sas hitelesítéssel.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Hibakód: DF-hópehely-InvalidDataType
- **Üzenet**: a Spark típusa nem támogatott a hópehely-ban.
- **Javaslat**: a **származtatás átalakításával** módosítsa a bemeneti adatok kapcsolódó oszlopát a string típusba a hópehely fogadó előtt. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Hibakód: DF-kaptár-InvalidBlobStagingConfiguration
- **Üzenet**: a blob Storage átmeneti tulajdonságait meg kell adni.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Hibakód: DF-kaptár-InvalidGen2StagingConfiguration
- **Üzenet**: ADLS Gen2 a tárterület átmeneti beállítása csak a szolgáltatás egyszerű kulcsának hitelesítő adatait támogatja.
- **Javaslat**: Ellenőrizze, hogy az egyszerű szolgáltatásnév hitelesítő adatait alkalmazza-e az átmenetiként használt ADLS Gen2 társított szolgáltatásban.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Hibakód: DF-kaptár-InvalidGen2StagingConfiguration
- **Üzenet**: ADLS Gen2 tárterület-átmeneti tulajdonságokat meg kell adni. A kulcs vagy a bérlő/spnId/spnKey, illetve a miServiceUri/miServiceToken egyike szükséges.
- **Javaslat**: alkalmazza a megfelelő hitelesítő adatokat, amelyet átmenetiként használ a rendszer a kapcsolódó ADLS Gen2 társított szolgáltatásban a struktúrában. 

### <a name="error-code-df-hive-invaliddatatype"></a>Hibakód: DF-kaptár-InvalidDataType
- **Üzenet**: nem támogatott oszlop (ok).
- **Javaslat**: frissítse a bemeneti adatok oszlopát a struktúra által támogatott adattípusnak megfelelő értékre.

### <a name="error-code-df-hive-invalidstoragetype"></a>Hibakód: DF-kaptár-InvalidStorageType
- **Üzenet**: a tárolási típus lehet blob vagy Gen2.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Hibakód: DF-tagolt-InvalidConfiguration
- **Üzenet**: az egyik üres sort vagy egyéni fejlécet kell megadni.
- **Javaslat**: üres sorok vagy egyéni fejlécek megadása a CSV-beállításokban.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Hibakód: DF-tagolt-ColumnDelimiterMissed
- **Üzenet**: az elemzéshez az oszlop elválasztója szükséges.
- **Javaslat**: Ellenőrizze, hogy rendelkezik-e az oszlop elválasztóval a CSV-beállításokban.

### <a name="error-code-df-mssql-invalidcredential"></a>Hibakód: DF-MSSQL-InvalidCredential
- **Üzenet**: a User/pwd, a bérlő/SpnId/spnKey vagy a MiServiceUri/miServiceToken egyikét meg kell adni.
- **Javaslat**: a megfelelő hitelesítő adatok alkalmazása a kapcsolódó MSSQL társított szolgáltatásban.

### <a name="error-code-df-mssql-invaliddatatype"></a>Hibakód: DF-MSSQL-InvalidDataType
- **Üzenet**: nem támogatott mező (k).
- **Javaslat**: módosítsa a bemeneti adatok OSZLOPOT az MSSQL által támogatott adattípusnak megfelelően.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Hibakód: DF-MSSQL-InvalidAuthConfiguration
- **Üzenet**: a három hitelesítési módszer (Key, SERVICEPRINCIPAL és mi) közül csak az egyik adható meg.
- **Javaslat**: csak a három hitelesítési módszer (Key, SERVICEPRINCIPAL és mi) egyikét adhatja meg a kapcsolódó MSSQL társított szolgáltatásban.

### <a name="error-code-df-mssql-invalidcloudtype"></a>Hibakód: DF-MSSQL-InvalidCloudType
- **Üzenet**: a felhő típusa érvénytelen.
- **Javaslat**: vizsgálja meg a felhő típusát a kapcsolódó MSSQL társított szolgáltatásban.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Hibakód: DF-SQLDW-InvalidBlobStagingConfiguration
- **Üzenet**: a blob Storage átmeneti tulajdonságait meg kell adni.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Hibakód: DF-SQLDW-InvalidStorageType
- **Üzenet**: a tárolási típus lehet blob vagy Gen2.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Hibakód: DF-SQLDW-InvalidGen2StagingConfiguration
- **Üzenet**: ADLS Gen2 a tárterület átmeneti beállítása csak a szolgáltatás egyszerű kulcsának hitelesítő adatait támogatja.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Hibakód: DF-SQLDW-InvalidConfiguration
- **Üzenet**: ADLS Gen2 tárterület-átmeneti tulajdonságokat meg kell adni. A kulcs vagy a bérlő/spnId/spnCredential/spnCredentialType vagy a miServiceUri/miServiceToken egyike szükséges.

### <a name="error-code-df-delta-invalidconfiguration"></a>Hibakód: DF-DELTA-InvalidConfiguration
- **Üzenet**: az időbélyeg és a verzió nem állítható be egyszerre.

### <a name="error-code-df-delta-keycolumnmissed"></a>Hibakód: DF-DELTA-KeyColumnMissed
- **Üzenet**: a nem beilleszthető műveletekhez meg kell adni a kulcs oszlop (oka) t.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Hibakód: DF-DELTA-InvalidTableOperationSettings
- **Üzenet**: az újbóli létrehozás és a csonkolt beállítások nem adhatók meg egyszerre.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Hibakód: DF-Excel-WorksheetConfigMissed
- **Üzenet**: az Excel-lapok neve vagy indexe kötelező.
- **Javaslat**: Tekintse meg a paraméter értékét, és adja meg a lapok nevét vagy indexét az Excel-adatok olvasásához.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Hibakód: DF-Excel-InvalidWorksheetConfiguration
- **Üzenet**: az Excel-lapok neve és indexe nem létezhet egyszerre.
- **Javaslat**: Tekintse meg a paraméter értékét, és adja meg a lapok nevét vagy indexét az Excel-adatok olvasásához.

### <a name="error-code-df-excel-invalidrange"></a>Hibakód: DF-Excel-InvalidRange
- **Üzenet**: érvénytelen tartomány van megadva.
- **Javaslat**: a paraméter értékének megadásához és az érvényes tartományt a következő hivatkozással kell megadnia: [Excel-formátum az Azure-beli adatFactory-Dataset tulajdonságaiban](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Hibakód: DF-Excel-WorksheetNotExist
- **Üzenet**: az Excel-munkalap nem létezik.
- **Javaslat**: Tekintse meg a paraméter értékét, és az Excel-adatok olvasásához adja meg az érvényes Sheet-nevet vagy-indexet.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Hibakód: DF-Excel-DifferentSchemaNotSupport
- **Üzenet**: az Excel-fájlok más sémával való olvasása most nem támogatott.

### <a name="error-code-df-excel-invaliddatatype"></a>Hibakód: DF-Excel-InvalidDataType
- **Üzenet**: az adattípus nem támogatott.

### <a name="error-code-df-excel-invalidfile"></a>Hibakód: DF-Excel-InvalidFile
- **Üzenet**: érvénytelen Excel-fájl van megadva, amíg csak az. xlsx és az. xls támogatott.


## <a name="miscellaneous-troubleshooting-tips"></a>Egyéb hibaelhárítási tippek
- **Probléma**: váratlan kivétel történt, és a végrehajtás nem sikerült.
    - **Üzenet**: az adatfolyam tevékenységének végrehajtása során: váratlan kivétel történt, és a végrehajtás nem sikerült.
    - **OK**: Ez a hiba egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
    - **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

-  **Probléma**: a hibakeresési adatelőnézet során nem érhető el kimeneti érték a csatlakoztatáshoz.
    - **Üzenet**: nagy számú Null érték vagy hiányzó érték áll rendelkezésre, amelyek oka lehet a túl kevés sor mintavételének. Próbálja meg frissíteni a hibakeresési sor korlátját, és frissítse az adatforrást.
    - **OK**: az illesztési feltétel nem felelt meg egyetlen sornak sem, vagy nagy mennyiségű null értéket eredményezett az adatelőnézet során.
    - **Javaslat**: a **hibakeresési beállítások** területen növelje a sorok számát a Sorforrás korlátján belül. Ügyeljen arra, hogy olyan Azure IR válasszon ki, amely egy olyan adatáramlási fürttel rendelkezik, amely elég nagy a további adatkezeléshez.
    
- **Probléma**: érvényesítési hiba a (z) forrásnál TÖBBsoros CSV-fájlokkal. 
    - **Üzenet**: a következő hibaüzenetek egyike jelenhet meg:
        - Az utolsó oszlop null értékű vagy hiányzik.
        - A séma érvényesítése a forrásnál meghiúsul.
        - A séma importálása nem sikerül helyesen megjeleníteni a UX-ben, és az utolsó oszlopban egy új sor karakter szerepel a névben.
    - **OK**: a leképezési adatfolyamban a többsoros CSV-forrásfájlok jelenleg nem működnek, ha \r\n használ. Előfordulhat, hogy a kocsivissza extra vonalak hibát okozhatnak. 
    - **Javaslat**: a fájl a forráshoz való előállítása a \n értékkel a \r\n. helyett. Vagy a másolási tevékenység használatával alakítsa át a CSV-fájlt, hogy a \n-as sort elválasztóként használja.

## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató
1. Keresse meg az adatkészlet kapcsolatainak állapotát. Minden forrás-és fogadó-átalakításban nyissa meg a társított szolgáltatást minden Ön által használt adatkészlethez, majd tesztelje a kapcsolatokat.
2. Győződjön meg róla, hogy a fájl és a tábla kapcsolatainak állapota az adatfolyam-tervezőben található. Hibakeresési módban válassza az **adatelőnézet** lehetőséget a forrás átalakításokban, hogy hozzáférjen az adataihoz.
3. Ha úgy tűnik, hogy minden megfelelően megjelenik az adatelőnézetben, ugorjon a folyamat-tervezőbe, és helyezze át az adatfolyamatot egy folyamat tevékenységbe. Egy végpontok közötti teszt folyamatának hibakeresése.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>A CSV/CDM formátumának fejlesztése az adatfolyamban 

Ha a **tagolt szöveges vagy CDM-formátumot használja a Azure Data Factory v2-ben lévő adatforgalom leképezéséhez**, akkor előfordulhat, hogy az adatforgalomban a tagolt szöveg/CDM változása a **2021**. 

A fejlesztés előtt a következő problémák merülhetnek fel, de a fejlesztés után a problémák kijavítva lettek. A következő tartalom elolvasásával megállapíthatja, hogy ez a javulás befolyásolja-e Önt. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>1. forgatókönyv: a váratlan sor elválasztó hibájának beolvasása

 A következő esetekben kell érintenie:
 - A tagolt szöveg használata az igaz vagy a CDM értékre állítva forrásként.
 - Az első sor több mint 128 karakterből áll. 
 - Az adatfájlokban lévő sorok elválasztója nem `\n` .

 A fejlesztés előtt az alapértelmezett sor elválasztója `\n` váratlanul felhasználható a tagolt szövegfájlok elemzéséhez, mert ha a többsoros beállítás értéke TRUE (igaz), akkor érvényteleníti a sor elválasztó beállítását, és az első 128 karakter alapján automatikusan észleli a sor elválasztóját. Ha nem ismeri fel a tényleges sor elválasztó karakterét, akkor az vissza fog térni a következőre: `\n` .  

 A fejlesztést követően a három sorból álló határolójel közül a következőt kell `\r` `\n` `\r\n` kidolgozni:,.
 
 A következő példa egy folyamat működésének változását mutatja be a fejlesztés után:

 **Példa**:<br/>
   A következő oszlophoz:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   A fejlesztés előtt az `\r` oszlop értéke megmarad. Az elemzett oszlop eredménye a következőket eredményezi:<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   A fejlesztést követően az elemzett oszlop eredményének a következőnek kell lennie:<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>2. forgatókönyv: a "\r\n" értéket tartalmazó oszlop értékeinek helytelen olvasásával kapcsolatos probléma

 A következő esetekben kell érintenie:
 - A tagolt szöveg és a többsoros beállítás értékeként a True vagy a CDM értéket használja forrásként. 
 - A sor elválasztója: `\r\n` .

 A fejlesztés előtt az oszlop értékének beolvasása során a `\r\n` nem helyettesíthető `\n` . 

 A fejlesztést követően az `\r\n` oszlop értéke nem lesz lecserélve `\n` .

 A következő példa egy folyamat működésének változását mutatja be a fejlesztés után:
 
 **Példa**:<br/>
  
 A következő oszlophoz:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 A fejlesztés előtt az elemzett oszlop eredménye a következő:<br/>
  **`A\n`**` B C`<br/>

 A fejlesztést követően az elemzett oszlop eredményének a következőnek kell lennie:<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>3. forgatókönyv: a "\n" értéket tartalmazó oszlop értékeinek helytelen írásakor felmerülő hiba

 A következő esetekben kell érintenie:
 - A tagolt szöveg fogadóként való használata.
 - Az oszlop értéke tartalmaz `\n` .
 - A sor elválasztója a következőre van beállítva: `\r\n` .
 
 A fejlesztés előtt az oszlop értékének írásakor a `\n` nem helyettesíthető `\r\n` . 

 A fejlesztést követően az `\n` oszlop értéke nem lesz lecserélve `\r\n` .
 
 A következő példa egy folyamat működésének változását mutatja be a fejlesztés után:

 **Példa**:<br/>

 A következő oszlophoz:<br/>
 **`A\n`**` B C`<br/>

 A kijavítás előtt a CSV-fogadó a következő:<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 A fejlesztés után a CSV-gyűjtőnek a következőnek kell lennie:<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>4. forgatókönyv: üres karakterlánc helytelen olvasásának hibája (NULL)
 
 A következő esetekben kell érintenie:
 - Tagolt szöveg használata forrásként. 
 - A NULL érték nem üres értékre van állítva. 
 - Az oszlop értéke üres karakterlánc, és nem jegyzett. 
 
 A fejlesztés előtt a nem jegyzett üres karakterlánc oszlopának értékét a rendszer NULL értékre olvassa. 

 A fejlesztést követően az üres sztringek nem lesznek NULL értékként értelmezve. 
 
 A következő példa egy folyamat működésének változását mutatja be a fejlesztés után:

 **Példa**:<br/>

 A következő oszlophoz:<br/>
  `A, ,B, `<br/>

 A fejlesztés előtt az elemzett oszlop eredménye a következő:<br/>
  `A null B null`<br/>

 A fejlesztést követően az elemzett oszlop eredményének a következőnek kell lennie:<br/>
  `A "" (empty string) B "" (empty string)`<br/>


## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért tekintse meg a következő forrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
