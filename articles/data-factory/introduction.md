---
title: Az Azure Data Factory bemutatása
description: 'A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása.'
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 8fef4bbc0c0e77c1e152b8c2c44254e091b0e5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392544"
---
# <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A big data világában a nyers, rendezetlen adatok tárolása leggyakrabban relációs, nem relációs vagy egyéb tárolórendszerekben történik. A nyers adatok önmagukban azonban nem biztosítanak megfelelő környezetet, sem értelmezhető elemzési lehetőségeket az elemzők, adatelemzők vagy üzleti döntéshozók számára. 

A Big az adatmennyiséghez olyan szolgáltatásra van szükség, amely képes a nagy mennyiségű nyers adatmennyiséget hasznosítható üzleti információkra kialakítani és működővé tenni a folyamatokat. Az Azure Data Factory egy ilyen, az összetett hibrid ETL-, ELT- és adatintegrációs projektek kezelésre szolgáló felügyelt felhőszolgáltatás.

Képzeljünk el például egy játékfejlesztő vállalatot, amely több petabájtnyi, a játékok által készített naplót gyűjt össze a felhőben. A vállalat e naplókat szeretné elemezni, hogy betekintést nyerhessen az ügyfelek preferenciáiba, demográfiai adataiba és felhasználói viselkedésébe, hogy ezek alapján azonosítsa az értékesítési és keresztértékesítési lehetőségeket, új funkciókat fejlesszen az üzleti növekedés elősegítése érdekében, és jobb felhasználói élményt nyújtson az ügyfeleinek.

A naplók elemzéséhez a vállalatnak a helyszíni adattárban tárolt referenciaadatokat kell felhasználnia, mint például az ügyféladatokat, a játékadatokat és a reklámkampány-adatokat. A vállalat úgy kívánja hasznosítani ezeket a helyszíni adattárakból származó adatokat, hogy azokat további, a felhőalapú adattárban lévő naplóadatokkal kombinálja. 

Az elemzések kinyeréséhez a Felhőbeli Spark-fürt (Azure HDInsight) használatával szeretné feldolgozni az összevont adatok feldolgozását, és egy Felhőbeli adattárházban (például az Azure szinapszis Analyticsben) közzéteheti az átalakított információkat, így egyszerűen létrehozhat egy jelentést. A vállalat automatizálni, illetve napi rendszerességgel monitorozni és kezelni szeretné ezt a munkafolyamatot. Ezenkívül végre is szeretné hajtani, ha fájlok kerülnek egy blobtárolóba.

Az Azure Data Factory az a platform, amely az ilyen adatforgatókönyvek esetében sikeresen használható. Ez a *FELHŐALAPÚ ETL-és adatintegrációs szolgáltatás, amely lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatáthelyezés előkészítéséhez és a nagy léptékű adatátalakításhoz*. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok. Összetett ETL-folyamatokat hozhat létre, amelyek az adatok vizuálisan alakíthatók át adatfolyamatokkal vagy számítási szolgáltatások, például Azure HDInsight Hadoop, Azure Databricks és Azure SQL Database használatával. 

Emellett közzéteheti az átalakított adatait olyan adattárakban is, mint például az Azure szinapszis Analytics for Business Intelligence-(BI-) alkalmazások. Végső soron az Azure Data Factory segítségével a nyers adatokat használható adattárakba rendezhetjük, így jobb üzleti döntéseket hozhatunk.

![A Data Factory felső szintű nézete](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Hogyan működik?

A Data Factory egymással összekapcsolt rendszerek sorozatát tartalmazza, amelyek teljes körű platformot biztosítanak az adatszakértők számára.

### <a name="connect-and-collect"></a>Csatlakozás és összegyűjtés

A vállalatok a legkülönfélébb adatokkal rendelkeznek a legkülönfélébb (helyszíni, felhőbeli, strukturált, strukturálatlan és részben strukturált) forrásokból, amelyek ráadásul eltérő időközzel és sebességgel érkeznek be. 

Az információ-előállítási rendszerek kiépítésének első lépése az összes szükséges adatforrás és feldolgozó, például az SaaS-szolgáltatások, az adatbázisok, a fájlmegosztások és az FTP-alapú webszolgáltatások összekapcsolása. A következő lépés az adatok igényalapú átmozgatása egy központi helyre a további feldolgozás előtt.

A Data Factory nélkül a vállalatoknak egyéni adattovábbítási összetevőket kell készíteniük vagy egyéni szolgáltatásokat kell írniuk az adatforrások és feldolgozók integrálására. Az ilyen rendszerek költségesek, nehezen integrálhatóak és tarthatók karban. Továbbá gyakran nem áll rendelkezésre az a vállalati szintű monitorozási, riasztási és vezérlési funkcionalitás, amelyet egy teljes mértékben felügyelt szolgáltatás biztosítani képes.

A Data Factory segítségével a [Másolási tevékenység](copy-activity-overview.md) keretében az adatok egyazon adatfolyamatban helyszíni és felhőalapú forrásadattárakból egyaránt továbbíthatóak egy, a felhőben lévő adattárba további elemzésre. Adatokat gyűjthet például Azure Data Lake Storageban, és később átalakíthatja az adatokat egy Azure Data Lake Analytics számítási szolgáltatás használatával. Esetleg begyűjtheti az adatokat egy Azure Blob Storage tárolóból, és később átalakíthatja azokat egy Azure HDInsight Hadoop-fürt használatával.

### <a name="transform-and-enrich"></a>Átalakítás és bővítés
Miután az adatok szerepelnek egy központi adattárban a felhőben, a gyűjtött adatokat az ADF-leképezési adatforgalom használatával dolgozza fel vagy alakíthatja át. Az adatforgalom lehetővé teszi, hogy az adatmérnökök a Spark-fürtöket és a Spark-programozást nem igénylő Adatátalakítási gráfokat hozzanak létre és tartsanak karban.

Ha inkább a kód átalakítását részesíti előnyben, az ADF támogatja a külső tevékenységeket az átalakítások végrehajtásához olyan számítási szolgáltatásokon, mint a HDInsight Hadoop, a Spark, a Data Lake Analytics és az Machine Learning.

### <a name="cicd-and-publish"></a>CI/CD és közzététel
A [Data Factory teljes körű támogatást nyújt](continuous-integration-deployment.md) az adatfolyamatok CI/CD-hez az Azure DevOps és a GitHub használatával. Ez lehetővé teszi az ETL-folyamatok fokozatos fejlesztését és továbbítását a végtermék közzététele előtt. Miután a nyers adatok át lettek alakítva üzleti használatra kész formába, feltöltheti azokat az Azure Data Warehouse-ba, az Azure SQL Database-be, az Azure CosmosDB-be vagy bármely olyan elemzési motorra, amelyre üzleti felhasználói intelligens üzleti eszközeikkel rámutathatnak.
### <a name="monitor"></a>Monitor
Miután sikeresen kiépítette és üzembe helyezte az adatintegrációs folyamatot, amely üzleti értéket állít elő a feldolgozott adatokból, kövesse figyelemmel az ütemezett tevékenységek és folyamatok sikerességi arányát. A Azure Data Factory beépített támogatást nyújt a folyamat-figyeléshez Azure Monitor, API, PowerShell, Azure Monitor naplók és az Azure Portal található állapotadatok használatával.

## <a name="top-level-concepts"></a>Legfelső szintű fogalmak
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Azure Data Factory a legfontosabb összetevőkből áll.
- Pipelines
- Tevékenységek
- Adathalmazok
- Társított szolgáltatások
- Adatfolyamok
- Integrációs modulok

Ezek együtt alkotják azt a platformot, amelyen létrehozhatók olyan adatvezérelt munkafolyamatok, amelyeknek a lépései áthelyezik és átalakítják az adatokat.

### <a name="pipeline"></a>Folyamat
Az adat-előállító egy vagy több folyamattal rendelkezhet. A folyamatok a tevékenységek logikus csoportosításai egy adott munkaegység végrehajtásához. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például a folyamat tartalmazhat egy csoportnyi műveletet, amelyek adatokat fogadnak egy Azure-blobból, majd egy Hive-lekérdezést futtatnak egy HDInsight-fürtön az adatok particionálásához. 

A folyamatok használatának az az előnye, hogy így a tevékenységek egy készletben kezelhetők, nem pedig külön-külön. Egy adott folyamat tevékenységei összefűzhetők, így azok egymás után vagy egymástól függetlenül, párhuzamosan üzemeltethetők.

### <a name="mapping-data-flows"></a>Adatfolyamok leképezése
Létrehozhatja és kezelheti az Adatátalakítási logika azon gráfait, amelyekkel átalakíthatja a bármilyen méretű adatmennyiséget. Felépítheti az Adatátalakítási rutinok újrafelhasználható könyvtárát, és kibővített módon hajthatja végre ezeket a folyamatokat az ADF-folyamatokból. A Data Factory egy Spark-fürtön hajtja végre a logikát, amely a felpörgetést és a pörgetést is igénybe veszi. Nem kell többé a fürtöket kezelni vagy karbantartani.

### <a name="activity"></a>Tevékenység
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. A másolási tevékenység használatával például az egyik adattárból a másikba másolhatja az adatokat. Hasonlóképpen, egy Hive-tevékenység használatával Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Tulajdonképpen a társított szolgáltatás határozza meg az adatforrással való kapcsolatot, míg az adatkészlet jelöli az adatok struktúráját. Az Azure Storage társított szolgáltatása például kapcsolati sztringet szolgáltat az Azure Storage-fiókhoz való csatlakozáshoz. Az Azure Blob-adatkészlet pedig meghatározza a blobtárolót és az adatokat tartalmazó mappát.

A társított szolgáltatásokat két célból használjuk a Data Factoryban:

- Olyan **adattár** jelölésére, amely tartalmaz, de nem korlátozódik a SQL Server adatbázisra, az Oracle-adatbázisra, a fájlmegosztás vagy az Azure Blob Storage-fiókra. A támogatott adattárak listáját lásd a [másolási tevékenység](copy-activity-overview.md) cikkben.

- Olyan **számítási erőforrás** jelölésére, amely egy tevékenység végrehajtását képes tárolni. A HDInsightHive-tevékenység végrehajtása például egy HDInsight Hadoop-fürtön történik. Az átalakítási tevékenységek és a támogatott számítási környezetek listáját lásd az [adatok átalakítása](transform-data.md) cikkben.

### <a name="triggers"></a>Triggerek
Az eseményindítók olyan feldolgozási egységek, amelyek meghatározzák, hogy mikor kezdődjön egy folyamat végrehajtása. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak.

### <a name="pipeline-runs"></a>Folyamatfuttatások
A folyamatfuttatások a folyamat-végrehajtás egy-egy példányát jelentik. A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatokban meghatározott paraméterekhez történő továbbításával történik. Az argumentumok továbbítása történhet manuálisan vagy az eseményindító meghatározásán keresztül is.

### <a name="parameters"></a>Paraméterek
A paraméterek írásvédett konfigurációk kulcs-érték párjainak tekintendők.A paraméterek meghatározása a folyamatban történik. Az e paraméterekhez tartozó argumentumok továbbítása a végrehajtás során történik a futtatási környezetből, amelyet egy eseményindító vagy egy manuálisan végrehajtott folyamat hoz létre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Az adatkészletek szigorúan típusos paraméterek és újrahasznosítható/hivatkozható entitások együttesei. A tevékenységek hivatkozhatnak adatkészletekre, és képesek az adatkészlet-meghatározásban megadott tulajdonságok feldolgozására.

A társított szolgáltatások szintén adattárak vagy számítási környezetek kapcsolatadatait tartalmazó, szigorúan típusos paraméterek. Ezenkívül újrahasznosítható/hivatkozható entitások.

### <a name="control-flow"></a>Átvitelvezérlés
Átvitelvezérlés: folyamattevékenységek vezénylése, például egy sorozat láncolási tevékenységei, elágaztatás és olyan paraméterek, amelyek folyamatszinten határozhatók meg, valamint olyan argumentumok, amelyek a folyamat igény szerinti meghívása során vagy egy eseményindítóból továbbítódnak. Ilyen tevékenységek az egyéniállapot-átadás és a tárolók hurkolása, vagyis a For-each iterátorok is.

### <a name="variables"></a>Változók
A változókat a folyamatokon belül lehet használni az ideiglenes értékek tárolására, és a paraméterekkel együtt használhatók a folyamatok, az adatfolyamatok és az egyéb tevékenységek közötti értékek átadásának engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések
A következő, fontos dokumentumok a következők:

- [Adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md)
- [Folyamatok és tevékenységek](concepts-pipelines-activities.md)
- [Integrációs modul](concepts-integration-runtime.md)
- [Adatfolyamok leképezése](concepts-data-flow-overview.md)
- [Data Factory felhasználói felülete az Azure Portalon](quickstart-create-data-factory-portal.md)
- [Adatok másolása eszköz az Azure Portalon](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-sablon](quickstart-create-data-factory-resource-manager-template.md)
 
