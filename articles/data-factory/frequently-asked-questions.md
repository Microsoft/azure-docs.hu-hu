---
title: 'Azure Data Factory: gyakori kérdések | Microsoft Docs'
description: Választ kaphat a Azure Data Factoryával kapcsolatos gyakori kérdésekre.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 7ebcf865ad23e75b2aa9070fe14fc3ee8f1397c7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481145"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory GYIK
Ez a cikk a Azure Data Factoryával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.  

## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory? 
A Data Factory egy teljes körűen felügyelt, felhőalapú, adatintegrációs szolgáltatás, amely automatizálja az adatátvitelt és-átalakítást. Mint egy olyan gyár, amely a nyersanyagok késztermékbe alakítására szolgáló berendezéseket futtat, Azure Data Factory összehangolja a meglévő olyan szolgáltatásokat, amelyek nyers adatokat gyűjtenek, és a használatra kész információkra alakítják át azokat. 

A Azure Data Factory használatával adatvezérelt munkafolyamatokat hozhat létre, amelyekkel áthelyezheti az adatátvitelt a helyszíni és a Felhőbeli adattárak között. Emellett számítási szolgáltatások, például az Azure HDInsight, a Azure Data Lake Analytics és az SQL Server Integration Services (SSIS) integrációs modul használatával is feldolgozhatja és átalakíthatja az adatgyűjtést. 

Az Data Factory segítségével az adatfeldolgozást Azure-alapú felhőalapú szolgáltatáson vagy saját üzemeltetésű számítási környezetben, például SSIS, SQL Server vagy Oracle-ben is végrehajthatja. Ha olyan folyamatot hoz létre, amely végrehajtja a szükséges műveletet, ütemezheti rendszeres időközönként (például óránként, naponta vagy hetente), az időablakok ütemezését, vagy az esemény bekövetkezésekor a folyamat aktiválását. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

### <a name="control-flows-and-scale"></a>Folyamatok és méretezés szabályozása 
A modern adattárház különböző integrációs folyamatainak és mintáinak támogatásához Data Factory lehetővé teszi a rugalmas adatfolyamatok modellezését. Ez magában foglalja a teljes körű vezérlési folyamatok programozási paradigmát, amelyek magukban foglalják a feltételes végrehajtást, az adatfolyamatok elágazását, valamint a paraméterek explicit módon történő átadásának lehetőségét ezen folyamatok között. A vezérlési folyamat emellett magában foglalja az adatátalakítást a külső végrehajtó motoroknak és az adatfolyam-képességeknek, beleértve az adatáthelyezést is, a másolási tevékenységen keresztül.

A Data Factory biztosítja az adatintegrációhoz szükséges adatáramlási stílus modellezését, és az igény szerinti vagy ismétlődő ütemezés szerint elküldhető. Néhány gyakori folyamat, amelyet ez a modell engedélyez, a következők:   

- Vezérlési folyamatok:
    - A tevékenységek összekapcsolhatók egy folyamaton belül.
    - A tevékenységek egy adott folyamaton belül lehetnek.
    - Paraméterek:
        - A paraméterek meghatározhatók a folyamat szintjén, és az argumentumok átadhatók a folyamat igény szerinti vagy eseményindítóból való meghívásakor.
        - A tevékenységek képesek a folyamat számára továbbított argumentumok feldolgozására.
    - Egyéni állapot átadása:
        - A tevékenység kimeneteit, beleértve az állapotot, a folyamat egy későbbi tevékenysége is felhasználhatja.
    - Körkörös tárolók:
        - A foreach tevékenység egy adott tevékenység egy adott gyűjteményében fog megjelenni egy hurokban. 
- Trigger-alapú folyamatok:
    - A folyamatok igény szerint vagy falióra esetén is elindíthatók.
- Különbözeti folyamatok:
    - A paraméterekkel határozható meg a Delta másolás magas vízjele, miközben a dimenzió-vagy hivatkozásokat a helyszíni vagy a felhőben lévő, a helyi vagy a felhőben lévő adattárakba helyezi át az adatoknak a tóba való betöltéséhez. 

További információ: [oktatóanyag: folyamatok vezérlése](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Nagy léptékű, kód nélküli folyamatokkal átalakított adatmennyiség
Az új böngészőalapú szerszámozási élmény kód nélküli folyamat-létrehozási és üzembe helyezési lehetőséget kínál modern, interaktív webes felülettel.

A vizualizációs adatfejlesztők és adatmérnökök esetében a Data Factory webes felhasználói felület az a kód nélküli kialakítási környezet, amelyet a folyamatok létrehozásához fog használni. Teljes mértékben integrálva van a Visual Studio online git szolgáltatással, és a hibakeresési lehetőségekkel integrálható a CI/CD-vel és az iterációs fejlesztéssel.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Sokoldalú többplatformos SDK-k haladó felhasználóknak
A Data Factory v2 olyan SDK-k széles választékát kínálja, amelyekkel a saját kedvenc IDE-fiókjával hozhat létre, kezelhet és figyelheti a folyamatokat, többek között:
* Python SDK
* PowerShell parancssori felület
* C# SDK

A felhasználók a dokumentált REST API-kat is használhatják a Data Factory v2 felülettel való kapcsolathoz.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Ismétlődő fejlesztés és hibakeresés a Visual Tools használatával
Azure Data Factory vizuális eszközök lehetővé teszik az ismétlődő fejlesztést és hibakeresést. Létrehozhatja a folyamatokat, és elvégezheti a tesztek futtatását a folyamat vásznon található **hibakeresési** funkció használatával anélkül, hogy egyetlen sor kódot írna. A teszt eredményét a folyamat vászonjának **output (kimenet** ) ablakában tekintheti meg. A tesztelés sikeres futtatása után további tevékenységeket adhat hozzá a folyamathoz, és ismétlődő módon folytathatja a hibakeresést. A teszt futtatása is megszakítható, miután folyamatban van. 

A **hibakeresés**megkezdése előtt nem kell közzétennie a Changes-et a Refactory szolgáltatásban. Ez olyan esetekben hasznos, amikor azt szeretné, hogy az új hozzáadások vagy módosítások a várt módon működjenek, mielőtt frissíti az adatfeldolgozó munkafolyamatokat a fejlesztési, tesztelési vagy éles környezetekben. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>SSIS-csomagok üzembe helyezésének lehetősége az Azure-ban 
Ha át szeretné helyezni a SSIS számítási feladatait, létrehozhat egy Data Factory, és üzembe helyezhet egy Azure-SSIS integrációs modult. Az Azure-SSIS integrációs modul az Azure-beli virtuális gépek (csomópontok) teljes körűen felügyelt fürtje, amely a SSIS-csomagok felhőben történő futtatására van kijelölve. Részletes útmutatásért lásd a [SSIS-csomagok üzembe helyezése az Azure-](tutorial-create-azure-ssis-runtime-portal.md) ban című oktatóanyagot. 
 
### <a name="sdks"></a>SDK-k
Ha Ön fejlett felhasználó, és egy programozott felületet keres, a Data Factory az SDK-k széles választékát kínálja, amelyeket a folyamatok létrehozásához, kezeléséhez vagy monitorozásához használhat a kedvenc IDE használatával. A nyelvi támogatás magában foglalja a .NET, a PowerShell, a Python és a REST használatát.

### <a name="monitoring"></a>Figyelés
A böngésző felhasználói felületén a PowerShell, az SDK vagy a vizuális monitorozási eszközök segítségével figyelheti az adatgyárait. Hatékony és hatékony módon figyelheti és kezelheti az igény szerinti, trigger-és órajel-alapú egyéni folyamatokat. Szakítsa meg a meglévő feladatokat, tekintse meg a hibák áttekintését, a részletes hibaüzenetek lekérését és a problémák hibakeresését, mindezt egyetlen panelről, háttérbeli váltás nélkül, vagy navigáljon a képernyők között. 

### <a name="new-features-for-ssis-in-data-factory"></a>A SSIS új szolgáltatásai Data Factory
A 2017-es kezdeti nyilvános előzetes kiadás óta a Data Factory a következő funkciókat adta hozzá a SSIS-hez:

-   A projektek/csomagok SSIS-adatbázisának (SSISDB) üzemeltetéséhez a Azure SQL Database három további konfigurációjának/változatának támogatása:
-   SQL Database virtuális hálózati szolgáltatásbeli végpontokkal
-   Felügyelt példány
-   Rugalmas készlet
-   A klasszikus virtuális hálózatok Azure Resource Manager virtuális hálózatának támogatása a jövőben elavulttá válik, ami lehetővé teszi az Azure-SSIS integrációs modul beléptetését vagy csatlakoztatását a virtuális hálózati szolgáltatással SQL Database konfigurált virtuális hálózathoz. végpontok/MI/helyszíni adathozzáférés. További információkért lásd még: [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md).
-   Azure Active Directory-(Azure AD-) hitelesítés és SQL-hitelesítés támogatása a SSISDB való kapcsolódáshoz, az Azure AD-hitelesítés engedélyezése a Data Factory felügyelt identitásával az Azure-erőforrásokhoz
-   A saját helyszíni SQL Server licencének támogatásával jelentős költségmegtakarítást érhet el az Azure Hybrid Benefit lehetőséggel
-   Az Azure-SSIS integrációs modul Enterprise kiadásának támogatása, amely lehetővé teszi a speciális/prémium funkciók használatát, egy egyéni telepítési felületet a további összetevők/bővítmények és a partnerek ökoszisztémájának telepítéséhez. További információ: [Enterprise Edition, Custom Setup és harmadik féltől származó bővíthetőség a SSIS-hez az ADF-ben](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   A SSIS mélyebb integrációja Data Factoryban, amely lehetővé teszi az első osztályú SSIS-csomagok elindítását/aktiválását Data Factory folyamatokban, és a SSMS keresztül ütemezhetik azokat. További információ: az [ETL/elt-munkafolyamatok modernizálása és kiterjesztése SSIS-tevékenységekkel az ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)-folyamatokban.


## <a name="what-is-the-integration-runtime"></a>Mi az Integration Runtime?
Az Integration Runtime az a számítási infrastruktúra, amelyet a Azure Data Factory használ a következő adatintegrációs képességek biztosítására különböző hálózati környezetekben:

- **Adatáthelyezés**: az adatáthelyezéshez az integrációs modul a forrás és a cél adattárolók között helyezi át az adatátvitelt, miközben támogatja a beépített összekötőket, a formátum átalakítását, az oszlopok hozzárendelését, valamint a teljesítmény és a skálázható adatátvitelt.
- **Küldési tevékenységek**: átalakításhoz az integrációs modul képes natív módon végrehajtani a SSIS-csomagokat.
- **SSIS-csomagok végrehajtása**: az Integration Runtime natív módon végrehajtja a SSIS-csomagokat egy felügyelt Azure számítási környezetben. Az Integration Runtime számos számítási szolgáltatáson (például Azure HDInsight, Azure Machine Learning, SQL Database és SQL Server) futó átalakítási tevékenységek elküldését és figyelését is támogatja.

Az integrációs modul egy vagy több példányát is telepítheti az adatáthelyezéshez és-átalakításhoz szükséges módon. Az Integration Runtime egy Azure-beli nyilvános hálózaton vagy egy privát hálózaton (helyszíni, Azure Virtual Network vagy Amazon Web Services virtuális magánhálózati felhőben [VPC]) is futtatható. 

További információkért lásd: [Integrációs modul az Azure Data Factoryban](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Mi a korlátja az integrációs modulok számának?
Az Integration Runtime-példányok száma nincs korlátozva az adatelőállítóban. Az integrációs modul által az SSIS-csomag végrehajtásához felhasználható virtuálisgép-magok száma azonban korlátozott. További információ: [Data Factory korlátok](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Mik a Azure Data Factory legfelső szintű fogalmai?
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. A Azure Data Factory négy fő összetevőt tartalmaz, amelyek együtt működnek olyan platformként, amelyen adatvezérelt munkafolyamatokat hozhat létre az adatáthelyezési és-átalakítási lépésekkel.

### <a name="pipelines"></a>Folyamatok
A data factory egy vagy több folyamattal rendelkezhet. A folyamat a tevékenységek logikai csoportosítása a Munkaegységek elvégzéséhez. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Egy folyamat például tartalmazhat olyan tevékenységeket, amelyek egy Azure-blobból töltenek le adatot, majd egy HDInsight-fürtön futtatnak egy kaptár-lekérdezést az adatok particionálásához. Ennek az az előnye, hogy egy folyamattal kezelheti a tevékenységeket készletként ahelyett, hogy az egyes tevékenységeket külön kell kezelnie. Egy folyamaton belül összekapcsolhatja a tevékenységeket, hogy azok egymás után is működjenek, vagy egymástól függetlenül, párhuzamosan is működhetnek.

### <a name="activities"></a>Tevékenységek
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. A másolási tevékenység használatával például az adatok egy adattárból egy másik adattárba másolhatók. Ehhez hasonlóan használhat egy kaptár-tevékenységet is, amely egy Azure HDInsight-fürtön futó kaptár-lekérdezést futtat az adatai átalakításához vagy elemzéséhez. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Így Képzelje el: A társított szolgáltatás határozza meg az adatforráshoz való kapcsolódást, és egy adatkészlet az adat szerkezetét jelöli. Egy Azure Storage-beli társított szolgáltatás például meghatározza az Azure Storage-fiókhoz való kapcsolódáshoz használt kapcsolati karakterláncot. Az Azure Blob-adatkészlet pedig meghatározza a BLOB-tárolót és az adatokat tartalmazó mappát.

A társított szolgáltatásoknak két célja van Data Factoryban:

- Egy olyan *adattár* képviseletére, amely tartalmazza, de nem korlátozódik a helyszíni SQL Server példányra, egy Oracle Database-példányra, egy fájlmegosztás vagy egy Azure Blob Storage-fiókra. A támogatott adattárak listáját lásd: [másolási tevékenység Azure Data Factoryban](copy-activity-overview.md).
- Olyan *számítási erőforrás* jelölésére, amelyen végrehajtható a tevékenység. A HDInsight struktúra tevékenység például egy HDInsight Hadoop-fürtön fut. Az átalakítási tevékenységek és a támogatott számítási környezetek listáját lásd: az [adatok átalakítása Azure Data Factoryban](transform-data.md).

### <a name="triggers"></a>Eseményindítók
Az eseményindítók olyan feldolgozási egységeket jelölnek, amelyek meghatározzák, hogy mikor indul el a folyamat végrehajtása. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak. 

### <a name="pipeline-runs"></a>Folyamatfuttatások
A folyamat futása egy folyamat-végrehajtás egy példánya. Általában egy folyamat futtatását kell létrehoznia az argumentumok átadásával a folyamatban definiált paraméterekbe. Az argumentumokat manuálisan vagy az trigger definíciójában adhatja át.

### <a name="parameters"></a>Paraméterek
A paraméterek kulcs-érték párok egy írásvédett konfigurációban. A folyamatokban paramétereket határozhat meg, és a Futtatás során átadja a definiált paraméterek argumentumait a végrehajtás során. A futtatási környezetet egy trigger vagy egy manuálisan futtatott folyamat hozza létre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Az adatkészlet egy határozottan begépelt paraméter és egy olyan entitás, amelyet felhasználhat vagy hivatkozhat. A tevékenységek hivatkozhatnak adatkészletekre, és az adatkészlet-definícióban definiált tulajdonságokat használhatják.

A társított szolgáltatás egy olyan, szigorúan beírt paraméter is, amely kapcsolati adatokat tartalmaz egy adattárhoz vagy egy számítási környezethez. Ez egy olyan entitás is, amelyet újból felhasználhat vagy hivatkozhat.

### <a name="control-flows"></a>Vezérlőfolyamatok
A vezérlési folyamatok összehangolják azokat a folyamatokat, amelyek a folyamat szintjén definiált láncolási tevékenységeket foglalják magukban, és amelyek a folyamatot igény szerint vagy eseményindítóból hívhatják meg. A vezérlési folyamatokban az is lehet, hogy a rendszer egyéni állapotba helyezi és hurkos tárolókat (azaz foreach-iterációkat) tartalmaz.


A Data Factory alapelveivel kapcsolatban további információkat a következő cikkekben talál:

- [Adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md)
- [Folyamatok és tevékenységek](concepts-pipelines-activities.md)
- [Integrációs modul](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Mi a Data Factory díjszabási modellje?
A Azure Data Factory díjszabásáról a [Data Factory díjszabását](https://azure.microsoft.com/pricing/details/data-factory/)ismertető témakörben talál további információt.

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hogyan maradhatok naprakészen a Data Factory kapcsolatos információkkal?
A Azure Data Factory legfrissebb információit a következő webhelyeken teheti meg:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Dokumentáció kezdőlapja](/azure/data-factory)
- [Termék kezdőlapja](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technikai Deep Dive 

### <a name="how-can-i-schedule-a-pipeline"></a>Hogyan ütemezhetek folyamatokat? 
A folyamat ütemezéséhez használhatja az ütemező trigger vagy az idő ablak triggert. Az trigger egy falióra-naptárbeli ütemtervet használ, amely rendszeres időközönként vagy naptári alapú ismétlődő mintákban ütemezi a folyamatokat (például Hétfőként, 6:00 PM-kor és csütörtökön, 9:00 ÓRAKOR). További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Átadhatok paramétereket egy folyamat futásának?
Igen, a paraméterek a Data Factory első osztályú, legfelső szintű koncepciója. Megadhat paramétereket a folyamat szintjén, és átadhat argumentumokat a folyamat igény szerinti futtatásakor vagy trigger használatával.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Megadhatom a folyamat paramétereinek alapértelmezett értékeit? 
Igen. Megadhatja a folyamatok paramétereinek alapértelmezett értékeit. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Lehet egy folyamat egy tevékenysége egy folyamat futtatására átadott argumentumokat használni? 
Igen. A folyamaton belül minden tevékenység felhasználhatja a folyamatnak átadott paraméterérték értékét, és futtathatja a `@parameter` szerkezettel. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Felhasználható egy tevékenység kimeneti tulajdonsága egy másik tevékenységben? 
Igen. Egy tevékenység kimenete egy későbbi tevékenységben is felhasználható a `@activity` szerkezettel.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hogyan szabályosan kezeli a tevékenység kimenetében lévő null értékeket? 
A kifejezésekben a `@coalesce`-összeállítás használatával kecsesen kezelheti a null értékeket. 

## <a name="mapping-data-flows"></a>Adatfolyamok leképezése

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Segítségre van szükségem az adatfolyam logikájának hibaelhárításához. Milyen információkat kell megadnia a segítséghez?

Ha a Microsoft segítséget vagy hibaelhárítást biztosít az adatfolyamatokkal kapcsolatban, adja meg az adatfolyam-parancsfájlt. Ez az adatfolyam-diagramon található kód mögötti parancsfájl. Az ADF felhasználói felületén nyissa meg az adatfolyamatot, majd kattintson a jobb felső sarokban található "parancsfájl" gombra. Másolja és illessze be ezt a parancsfájlt, vagy mentse szövegfájlba.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>A Data Factory más 90 adatkészlet-típusaival Hogyan a hozzáférési adatokat?

A leképezési adatfolyam funkció jelenleg lehetővé teszi Azure SQL Database, Azure SQL Data Warehouse, tagolt szövegfájlok használatát az Azure Blob Storage-ból vagy a Azure Data Lake Storage Gen2-ból, valamint a blob Storage-ból származó, illetve a forrás-és a fogadó Data Lake Storage Gen2 natív módon. 

A másolási tevékenység használatával a többi összekötőtől származó adatok is megadhatók, majd az adatok átalakítását követően végrehajthat egy adatfolyam-tevékenységet. Például a folyamat először a blob Storage-ba másol, majd egy adatfolyam-tevékenység egy adatkészletet fog használni a forrásban az adatátalakításhoz.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Elérhető a saját üzemeltetésű integrációs modul az adatforgalomhoz?

A saját üzemeltetésű integrációs modul egy ADF-alapú folyamat-összeállítás, amellyel a másolási tevékenységgel adatok szerezhetők be és helyezhetők át a helyszíni vagy a VM-alapú adatforrásokból, illetve elsüllyednek. Először készítsen másolatot az adatforgalomról, majd az átalakításhoz szükséges adatfolyamot, majd egy későbbi másolatot, ha át kell helyeznie az átalakított adattárolót a helyszíni áruházba.

## <a name="wrangling-data-flows"></a>Huzavona-adatfolyamok

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Melyek a huzavona-adatfolyam támogatott régiói?

A huzavona-adatfolyam jelenleg támogatott a következő régiókban létrehozott adatüzemekben:

* Kelet-Ausztrália
* Közép-Kanada
* Közép-India
* USA középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* Kelet-Japán
* Észak-Európa
* Délkelet-Ázsia
* USA déli középső régiója
* Egyesült Királyság déli régiója
* USA nyugati középső régiója
* Nyugat-Európa
* USA nyugati régiója
* USA 2. nyugati régiója

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Milyen korlátozások és korlátozások vonatkoznak a huzavona-adatfolyamra?

Az adatkészlet neve csak alfanumerikus karaktereket tartalmazhat. A következő adattárak támogatottak:

* DelimitedText-adatkészlet az Azure Blob Storage fiók kulcsos hitelesítésének használatával
* DelimitedText adatkészlet Azure Data Lake Storage Gen2-ben a fiók kulcsa vagy a szolgáltatás egyszerű hitelesítése használatával
* DelimitedText adatkészlet Azure Data Lake Storage gen1 az egyszerű szolgáltatás hitelesítésének használatával
* A Azure SQL Database és az adatraktár SQL-hitelesítéssel. Lásd az alábbi támogatott SQL-típusokat. Az adatraktár nem rendelkezik alalaptal vagy átmeneti támogatással.

Jelenleg a társított szolgáltatás Key Vault integrációja nem támogatott a huzavona-adatfolyamatokban.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Mi a különbség a leképezési és a huzavona adatfolyamatok között?

Az Adatáramlások leképezése lehetővé teszi, hogy az adatforgalom méretezés nélkül, kódolás nélkül legyen átalakítva. Az Adatátalakítási feladatokat az adatáramlási vásznon is megtervezheti átalakítások sorozatának létrehozásával. Az Adatátalakítási lépések után tetszőleges számú forrás-átalakítást indíthat. Fejezze be az adatfolyamatot egy fogadóval, hogy az eredményeket egy célhelyen lehessen kirakodni. Az adatáramlás megfeleltetése kiváló megoldás a fogadók és a források ismert és ismeretlen sémákkal történő leképezésére és átalakítására.

A huzavona-adatfolyamatok lehetővé teszik az adatfeldolgozást és-feltárást az Power Query online mashup-szerkesztő használatával, a Spark-végrehajtással. Az adattavak növekedésével időnként csak egy adatkészletet kell felderíteni, vagy létre kell hoznia egy adatkészletet a tóban. Nem egy ismert célhoz van hozzárendelve. A huzavona-adatfolyamatok kevésbé formális és modell-alapú elemzési helyzetekben használatosak.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Mi a különbség a Power platform adatfolyamok és a huzavona adatforgalma között?

A Power platform adatfolyamok lehetővé teszi a felhasználók számára, hogy az adatforrások széles köréből importálják és alakítsuk át az Common Data Service és Azure Data Lake, hogy PowerApps alkalmazásokat, Power BI jelentéseket vagy folyamat-automatizálásokat hozzanak létre. A Power platform adatfolyamok a Power BI és az Excel programhoz hasonló, a Power Query adat-előkészítési élményt használja. A Power platform adatfolyamok a szervezeten belüli egyszerű újrafelhasználást is lehetővé teszi, és automatikusan kezeli a munkafolyamatot (például a adatfolyamok automatikus frissítését, amely egy másik adatfolyam függ, amikor az előbbi frissítve lett).

A Azure Data Factory (ADF) egy felügyelt adatintegrációs szolgáltatás, amely lehetővé teszi, hogy az adatmérnökök és az állampolgári adatintegrátor komplex hibrid kinyerési, átalakítási és kinyerési (ELT) munkafolyamatokat hozzon létre. Az ADF-ben a huzavona adatáramlás lehetővé teszi a felhasználók számára a Felhőbeli adatelőkészítést, valamint az infrastruktúra-felügyelet nélküli adatméretek méretezését. A Power Query adatelőkészítési technológiát használja (amelyet a Power platform adatfolyamok, Excelben, Power BI) is használ az adatgyűjtés előkészítéséhez és alakításához. A big data integráció összes összetettségét és méretezési nehézségeit felépítve a huzavona Adatáramlások lehetővé teszik a felhasználók számára, hogy a Spark-végrehajtással gyorsan készítsék el az adatmennyiséget. A felhasználók rugalmas adatfolyamatokat hozhatnak létre egy elérhető vizualizációs környezetben a böngészőalapú kezelőfelülettel, és lehetővé teszik, hogy az ADF kezelje a Spark-végrehajtás bonyolultságát. Készítsen ütemterveket a folyamatokhoz, és figyelje az adatfolyamatok végrehajtását az ADF-figyelési portálról. Egyszerűen kezelheti az adatrendelkezésre állási SLA-kat az ADF bőséges rendelkezésre állásának figyelésével és riasztásokkal, valamint a beépített folyamatos integrációs és üzembe helyezési képességekkel, hogy felügyelt környezetben mentse és kezelhesse a folyamatokat. Riasztásokat hozhat létre, és megtekintheti a végrehajtási terveket annak ellenőrzéséhez, hogy a logikája az adatfolyamatok hangolása szerint van-e végrehajtva.

### <a name="supported-sql-types"></a>Támogatott SQL-típusok

A huzavona-adatfolyam a következő adattípusokat támogatja az SQL-ben. Érvényesítési hiba jelenik meg, ha olyan adattípust használ, amely nem támogatott.

* rövid
* double
* valós
* lebegőpontos
* char
* NCHAR
* varchar
* nvarchar
* egész szám
* int
* bites
* logikai
* smallint
* tinyint
* bigint
* hosszú
* szöveg
* dátum
* dátum/idő
* datetime2
* idő adattípusúra
* időbélyeg
* uniqueidentifier
* xml

A jövőben más adattípusok is támogatottak lesznek.

## <a name="next-steps"></a>További lépések
Az adat-előállító létrehozásával kapcsolatos részletes utasításokért tekintse meg a következő oktatóanyagokat:

- [Gyors útmutató: adatelőállító létrehozása](quickstart-create-data-factory-dot-net.md)
- [Oktatóanyag: Adatmásolás a felhőben](tutorial-copy-data-dot-net.md)
