---
title: Használati eset – ügyfél-profilkészítés
description: Megtudhatja, hogyan hozhat létre Azure Data Factory adatvezérelt munkafolyamatot (folyamat) a profil játékok ügyfelei számára.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b8193039edd21db5a52eabca37c1e61880e49cfe
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779749"
---
# <a name="use-case---customer-profiling"></a>Használati eset – ügyfél-profilkészítés
Azure Data Factory a megoldási gyorssegédek Cortana Intelligence Suite megvalósításához használt számos szolgáltatás egyike.  További információ a Cortana Intelligenceről: [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). Ebben a dokumentumban egy egyszerű használati esetet ismertetünk, amely segít megismerni, hogy Azure Data Factory Hogyan oldhatók meg a gyakori elemzési problémák.

## <a name="scenario"></a>Eset
A contoso egy szerencsejáték-cég, amely több platformhoz hoz létre játékokat: játékkonzolok, kézi eszközök és személyi számítógépek (PC-k). Ahogy a játékosok játszanak ilyen játékokat, nagy mennyiségű naplózási adatok készülnek, amelyek nyomon követik a felhasználó használati mintáit, a játék stílusát és a felhasználói preferenciákat.  Demográfiai, regionális és Termékadatok együttes használata esetén a contoso az elemzések elvégzésével megtudhatja, hogyan javíthatja a játékosok élményét, és hogyan célozhatja meg azokat a frissítésekhez és a játékon belüli vásárlásokhoz. 

A contoso célja, hogy a játékosok szerencsejáték-előzményei alapján azonosítsa a fel-eladás/értékesítés utáni lehetőségeket, és látványos funkciókat adjon az üzleti növekedéshez, és jobb felhasználói élményt nyújtson az ügyfeleknek. Ebben a használati esetben egy szerencsejáték-vállalatot használunk egy üzleti példaként. A vállalat a játékosok viselkedése alapján szeretné optimalizálni játékait. Ezek az alapelvek minden olyan vállalkozásra érvényesek, amely az ügyfeleit a termékeivel és szolgáltatásaival kapcsolatban kívánja elérni, és fokozza ügyfelei tapasztalatait.

Ebben a megoldásban a contoso szeretné kiértékelni a közelmúltban elindított marketing-kampány hatékonyságát. Kezdjük a nyers játékok naplóival, feldolgozzuk és gazdagítjuk azokat a térinformatikai adataival, összekapcsoljuk azokat a hirdetési referenciákkal, és végül egy Azure SQL Databaseba másoljuk a kampány hatásának elemzéséhez.

## <a name="deploy-solution"></a>Megoldás üzembe helyezése
Ehhez az egyszerű használati esethez mindössze egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/), egy [Azure Blob Storage-fiók](../../storage/common/storage-account-create.md)és egy [Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md)szükséges. Az ügyfél profilkészítési folyamatát az adatok előállítójának kezdőlapján a **minta folyamatok** csempén helyezheti üzembe.

1. Hozzon létre egy adatelőállítót, vagy nyisson meg egy meglévő adatgyárat. Az adatok-előállító létrehozásához szükséges lépésekért lásd: [adatok másolása blob Storageról SQL Databasera Data Factory használatával](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. Az adatelőállító **adatelőállító** paneljén kattintson a **mintavételezési folyamatok** csempére.

    ![Mintavételezési folyamatok csempe](./media/data-factory-samples/SamplePipelinesTile.png)
3. A **mintavételezési folyamatok** panelen kattintson a telepíteni kívánt **ügyfél-profilkészítésre** .

    ![Mintavételezési folyamatok panel](./media/data-factory-samples/SampleTile.png)
4. A minta konfigurációs beállításainak megadása. Például az Azure Storage-fiók nevét és kulcsát, a logikai SQL-kiszolgáló nevét, az adatbázist, a felhasználói azonosítót és a jelszót.

    ![Minta panel](./media/data-factory-samples/SampleBlade.png)
5. Miután végzett a konfigurációs beállítások megadásával, a **Létrehozás** gombra kattintva hozhatja létre és helyezheti üzembe a folyamatok által használt mintavételi folyamatokat és társított szolgáltatásokat/táblákat.
6. A telepítési állapotot a minta csempén láthatja, amelyet korábban a **mintavételezési folyamatok** panelen kattintott.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik az **üzembe helyezés sikeres** üzenete a minta csempén, akkor zárjuk be a **mintavételezési folyamatok** panelt.  
8. A **adat-előállító** panelen láthatja, hogy a társított szolgáltatások, adatkészletek és folyamatok hozzáadódnak az adat-előállítóhoz.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Megoldási áttekintés
Ez az egyszerű használati eset példaként használható arra, hogy miként használhatók a Azure Data Factory az adatgyűjtésre,-előkészítésre,-átalakításra,-elemzésre és-közzétételre.

![Teljes körű munkafolyamat](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Ez az ábra azt ábrázolja, hogyan jelennek meg az adatfolyamatok a Azure Portal az üzembe helyezésük után.

1. A **PartitionGameLogsPipeline** beolvassa a nyers játék eseményeit a blob Storage-ból, és a partíciókat az év, hónap és nap alapján hozza létre.
2. A **EnrichGameLogsPipeline** összekapcsolja a particionált játékhoz tartozó eseményeket a Geo-kóddal kapcsolatos hivatkozásokkal, és az IP-címek a megfelelő földrajzi helyszínekre való leképezésével bővíti az adatmennyiséget.
3. A **AnalyzeMarketingCampaignPipeline** folyamat a kibővített adatokat használja, és a hirdetési adatokat feldolgozza a marketing kampány hatékonyságát tartalmazó végső kimenet létrehozásához.

Ebben a példában a Data Factory a bemeneti adatokat másoló tevékenységek, az adatok átalakítására és feldolgozására, valamint a végső adatok egy Azure SQL Databaseba való exportálására szolgál.  Emellett megjelenítheti az adatfolyamatok hálózatát, kezelheti őket, és figyelheti az állapotukat a felhasználói felületen.

## <a name="benefits"></a>Előnyök
A felhasználói profil elemzésének optimalizálása és az üzleti célokkal való összehangolása révén a Gaming Company gyorsan gyűjthet használati mintákat, és elemezheti a marketing kampányok hatékonyságát.