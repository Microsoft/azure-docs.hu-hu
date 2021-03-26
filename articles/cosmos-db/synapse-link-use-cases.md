---
title: Valós idejű elemzési használati esetek az Azure szinapszis hivatkozásával Azure Cosmos DB
description: Ismerje meg, hogyan használható az Azure szinapszis-hivatkozás a Azure Cosmos DB az ellátási lánc elemzésében, az előrejelzésben, a jelentéskészítésben, a valós idejű személyre szabásban és a IOT prediktív karbantartásban.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 253257564ffc96108f282d7de3dbd9f5e8ce68fd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869854"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Cosmos DB-hez készült Azure Synapse Link: Közel valós idejű elemzési használati esetek
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Az [Azure szinapszis hivatkozása](synapse-link.md) Azure Cosmos db egy Felhőbeli natív hibrid tranzakciós és analitikai feldolgozási (HTAP) képesség, amely lehetővé teszi a közel valós idejű elemzések futtatását az operatív adaton keresztül. A szinapszis-kapcsolat szoros zökkenőmentes integrációt hoz létre Azure Cosmos DB és az Azure szinapszis Analytics között.

Érdemes lehet megismerni, hogy milyen iparági használati esetek használhatják a Felhőbeli natív HTAP képességet a közel valós idejű elemzésekhez az operatív adaton keresztül. Íme három gyakori felhasználási eset a Azure Cosmos DB Azure szinapszis-hivatkozásához:

* Ellátási lánc elemzése, előrejelzés & jelentéskészítés
* Valós idejű személyre szabás
* Prediktív karbantartás, anomáliák észlelése IOT-forgatókönyvekben

> [!NOTE]
> Az Azure szinapszis hivatkozása Azure Cosmos DB célozza meg a forgatókönyvet, ahol a nagyvállalati csapatok közel valós idejű elemzést szeretnének futtatni. Ezek az elemzések ETL nélkül futnak a Azure Cosmos DB-ra épülő tranzakciós alkalmazások által generált működési adaton keresztül. Ez nem helyettesíti a különálló adattárházak igényét, ha vannak olyan hagyományos adattárház-követelmények, mint például a munkaterhelés-kezelés, a magas Egyidejűség, az adatmegőrzési összesítések több adatforrásban.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Ellátási lánc elemzése, előrejelzés & jelentéskészítés

A kutatási tanulmányok azt mutatják, hogy a big data-elemzések az ellátási lánc műveleteiben való beágyazásával javulnak a lejárati idő és a rendelkezésre állási lánc hatékonysága.

A gyártók a Felhőbeli natív technológiákba való bevezetéssel kitörhetik az örökölt vállalati erőforrás-tervezési (ERP) és az ellátásilánc-kezelési (SCM) rendszerek korlátozásait. Ha az ellátási láncok egyre több operatív adatmennyiséget generálnak percenként (megrendelés, szállítás, tranzakciós adatforgalom), a gyártóknak operatív adatbázisra van szükségük. Az operatív adatbázisnak méretezhetőnek kell lennie az adatmennyiségek és egy analitikai platform kezeléséhez, hogy a valós idejű környezetfüggő intelligenciát a görbe előtt tartsa.

A következő architektúra a Azure Cosmos DB kihasználása a Felhőbeli natív operatív adatbázis és a szinapszis hivatkozását mutatja be az ellátási lánc Analyticsben:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Azure szinapszis-hivatkozás a Azure Cosmos DB az ellátási lánc elemzésében " border="false":::

A korábbi architektúra alapján a következő használati esetek érhetők el a Azure Cosmos DB a szinapszis-hivatkozással:

* **& Train prediktív folyamat előkészítése:** A gépi tanulás lefordításával elemzéseket készíthet a szolgáltatási láncon keresztüli operatív adatokat illetően. Ily módon csökkentheti a leltárt, az üzemeltetési költségeket, és csökkentheti az ügyfelekhez rendelés – kézbesítési időt.

  A szinapszis hivatkozás lehetővé teszi, hogy a Azure Cosmos DB a manuális ETL-folyamatok nélkül elemezze az operatív adatváltozásokat. A szolgáltatás további költségeket, késést és működési komplexitást takarít meg. A szinapszis-hivatkozás lehetővé teszi az adatmérnökök és az adatszakértők számára, hogy robusztus prediktív folyamatokat hozzanak létre:

  * Azure Cosmos DB analitikus áruházból származó operatív adatok lekérdezése az Azure szinapszis Analytics Apache Spark-készletekkel való natív integrációjának kihasználásával. Az adatok lekérdezése interaktív jegyzetfüzetben vagy ütemezett távoli feladatokban összetett adattervezés nélkül végezhető el.

  * Hozzon létre Machine Learning (ML) modelleket Spark ML-algoritmusokkal és Azure ML-integrációval az Azure szinapszis Analyticsben.

  * Írja vissza az eredményeket a modell következtetésének Azure Cosmos DB a működés közel valós idejű értékeléséhez.

* **Működési jelentéskészítés:** Az ellátási lánc tagjainak rugalmas és egyéni jelentések szükségesek a valós idejű, pontos üzemeltetési adatokhoz. Ezek a jelentések az ellátási lánc hatékonyságának, jövedelmezőségének és termelékenységének pillanatkép-nézetének beszerzéséhez szükségesek. Lehetővé teszi az adatelemzők és az egyéb kulcsfontosságú felek számára, hogy folyamatosan értékeljék az üzleti tevékenységet, és azonosítsák a felmerülő területeket a működési költségek csökkentése érdekében. 

  A Azure Cosmos DB szinapszis hivatkozása lehetővé teszi a gazdag üzleti intelligencia (BI)/Reporting forgatókönyvek használatát:

  * A Azure Cosmos DB analitikus áruházból származó operatív adatok lekérdezése a kiszolgáló nélküli SQL-készlettel való natív integráció és a T-SQL nyelv teljes kifejező használatával.

  * A BI-irányítópultok automatikus frissítésének modellezése és közzététele Azure Cosmos DB keresztül a kiszolgáló nélküli SQL-készlet támogatásával ismerős BI-eszközökön. Például Azure Analysis Services, Power BI Premium stb.

Az alábbiakban néhány útmutatást talál a Batch adatintegrációs & a Azure Cosmos DB:

* **Batch-adatintegrációs &** -előkészítés: Az ellátási láncok egyre összetettebbek, az ellátási lánc adatplatformok számos különböző adatforrással és formátummal integrálhatók. Az Azure szinapszis a Azure Data Factoryával azonos adatintegrációs motorral és tapasztalatokkal rendelkezik. Ez az integráció lehetővé teszi, hogy az adatmérnökök gazdag adatfolyamatokat hozzanak létre, külön hangkezelő motor nélkül:

  * A 85 + támogatott adatforrásokból származó adatok áthelyezése [Azure Data Factory Azure Cosmos db](../data-factory/connector-azure-cosmos-db.md).

  * Kód nélküli ETL-Azure Cosmos DB folyamatokat írhat, amelyekkel a [leképezési adatfolyamatokkal](../data-factory/how-to-sqldb-to-cosmosdb.md)összehasonlítható a viszonyítási és hierarchikus, valamint a hierarchikus és hierarchikus leképezések.

* **Adatfolyam-integráció & feldolgozása:** Az ipari IoT növekedésével (érzékelők nyomon követik a "padlóról az áruházból", a csatlakoztatott logisztikai flottákat stb.) a valós idejű, folyamatos adatátviteli folyamatokat, amelyeket integrálni kell a hagyományos lassú adatáthelyezési adatokkal az elemzések létrehozásához. Azure Stream Analytics az ETL és az Azure-beli feldolgozás ajánlott szolgáltatása [széles körű forgatókönyvekkel](../stream-analytics/streaming-technologies.md). A Azure Stream Analytics [natív](../stream-analytics/stream-analytics-documentdb-output.md)adatfogadóként támogatja a Azure Cosmos db.

## <a name="real-time-personalization"></a>Valós idejű személyre szabás

A kereskedőknek olyan biztonságos és skálázható e-kereskedelmi megoldásokat kell létrehozniuk, amelyek megfelelnek mind az ügyfelek, mind a vállalati igényeknek. Ezek az e-kereskedelmi megoldások testreszabott termékekkel és ajánlatokkal, gyorsan és biztonságosan dolgozzák fel az ügyfeleket, és a teljesítésre és az ügyfélszolgálatra összpontosítanak. Azure Cosmos DB a legújabb szinapszis-hivatkozással együtt, Azure Cosmos DB lehetővé teszi, hogy a kereskedők valós időben személyre szabott javaslatokat készítsenek az ügyfeleknek. Alacsony késleltetésű és hangolt konzisztencia-beállításokat használnak az azonnali felismerésekhez, ahogyan az a következő architektúrán látható:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Azure szinapszis-hivatkozás a Azure Cosmos DB valós idejű személyre szabásához" border="false":::

Szinapszis-hivatkozás Azure Cosmos DB használati esethez:

* **& Train prediktív folyamat előkészítése:** A szinapszis Spark és a Machine learning modellek segítségével elemzéseket készíthet az üzleti egységek vagy az ügyfelek szegmensei között. Ez a személyre szabott kézbesítésre fordítja a vásárlói szegmenseket, a prediktív végfelhasználói élményt és a célzott marketinget, hogy illeszkedjen a végfelhasználói igényekhez.

## <a name="iot-predictive-maintenance"></a>IOT prediktív karbantartás

Az ipari IOT innovációk jelentősen csökkentik a gépek leállását, és az iparág összes területén megnövelték az általános hatékonyságot. Az ilyen fejlesztések egyike a felhő szélén lévő gépek prediktív karbantartási elemzését.

A következő egy architektúra, amely kihasználja az Azure szinapszis IoT-Azure Cosmos DB kapcsolatának Felhőbeli natív HTAP képességeit a prediktív karbantartásban:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Azure-beli szinapszis-hivatkozás a IOT prediktív karbantartásban Azure Cosmos DB" border="false" :::

Szinapszis-hivatkozás Azure Cosmos DB használati esetekhez:

* **& Train prediktív folyamat előkészítése:** A IoT-eszköz érzékelőkből származó korábbi operatív adatok olyan prediktív modellek betanítására használhatók, mint például a anomáliák észlelése. Az anomáliadetektorok ezután ismét üzembe helyezhetők a szélen valós idejű monitorozás céljából. Egy ilyen hasznos hurok lehetővé teszi a prediktív modellek folyamatos újraképzését.

* **Működési jelentéskészítés:** A digitális kettős kezdeményezések növekedésével a vállalatok nagy mennyiségű, a különböző érzékelőkből származó operatív adatok gyűjtésével készítenek digitális másolatot az egyes gépekről. Ez az adatvédelemi BI-nak ismernie kell a múltbeli adatokkal kapcsolatos trendeket, valamint a valós idejű alkalmazásokat a legutóbbi gyors adatokon.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Minta forgatókönyv: HTAP for Azure Cosmos DB

Közel egy évtizednél Azure Cosmos DB több ezer ügyfelünk használta olyan kritikus fontosságú alkalmazásokhoz, amelyek rugalmas skálázást, kulcsrakész globális elosztást, többrégiós írási replikálást igényelnek az alacsony késés és a magas rendelkezésre állás érdekében mindkét beolvasás & a tranzakciós munkaterhelésben.
 
Az alábbi lista áttekintést nyújt az operatív adatok által támogatott különböző munkaterhelési mintákról Azure Cosmos DB használatával:

* Valós idejű alkalmazások & szolgáltatások
* Eseménystream-feldolgozás
* BI-irányítópultok
* Big data elemzése
* Gépi tanulás

Az Azure szinapszis-hivatkozás lehetővé teszi, hogy a Azure Cosmos DB ne csak az áramellátási tranzakciós feladatokat használja, hanem a közel valós idejű analitikai számítási feladatokat is végrehajtsa a korábbi működési adatokon. A tranzakciós munkaterhelések esetében az ETL-követelmények és a garantált teljesítmény-elkülönítés nem történik meg.

Az alábbi képen a számítási feladatok mintái a Azure Cosmos DB használatával: az :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Azure szinapszis hivatkozása Azure Cosmos db munkaterhelés-mintákhoz" border="false":::

Tegyük fel például, hogy egy e-kereskedelmi cég CompanyXYZ a 20 ország/régió globális műveleteivel, hogy bemutassa a Azure Cosmos DB kiválasztásának előnyeit, mint egyetlen valós idejű adatbázis, amely a készletkezelési platform tranzakciós és analitikai követelményeit is kikapcsolja.

* A CompanyXYZ alapszintű üzleti adatai a készletkezelési rendszertől függenek – így a rendelkezésre állási & megbízhatósága a fő pillérre vonatkozó követelmények. A Azure Cosmos DB használatának előnyei:

  * Az Azure-infrastruktúrával való szoros integráció, valamint az átlátszó, többrégiós írások révén a globális replikáció Azure Cosmos DB piacvezető [99,999%-os magas rendelkezésre állást](high-availability.md) biztosít a regionális kimaradások ellen.

* A CompanyXYZ ellátási láncbeli partnerei külön földrajzi helyen lehetnek, de előfordulhat, hogy a termékek leltározásának egyetlen nézetét kell megnézniük a világ minden részén, hogy támogassák a helyi műveleteiket. Ez magában foglalja a más ellátási láncú partnerek frissítéseinek valós idejű olvasását. Emellett az is előfordulhat, hogy a frissítéseket nem kell aggódnia, ha más partnerekkel ütközik a nagy átviteli sebességgel. A Azure Cosmos DB használatának előnyei:

  * Az egyedi többrégiós replikációs protokoll és a zárolás nélküli, írásra optimalizált tranzakciós tárolók révén a Azure Cosmos DB kevesebb, mint 10 MS késést biztosít mind az indexelt olvasások, mind az írások esetében a esetek 99% percentilis globálisan.

  * A kötegelt adatcsatornák nagy teljesítményű betöltése & adatfolyamok [valós idejű indexelésével](index-policy.md) a tranzakciós tárolóban.

  * A Azure Cosmos DB tranzakciós áruház három további lehetőséget kínál, mint az erős és a végleges konzisztencia-szintek közül, hogy a [rendelkezésre állási és teljesítménybeli kompromisszumok](./consistency-levels.md) az üzleti igényekhez legközelebb álljanak.

* A CompanyXYZ ellátási láncával foglalkozó partnerei nagy mértékben ingadoznak a forgalmi minták több száz és akár több millió kérelem/s között, így a leltár-kezelési platformnak a forgalom váratlan burstiness kell foglalkoznia.  A Azure Cosmos DB használatának előnyei:

  * A Azure Cosmos DB tranzakciós tárolója támogatja a tárolás és az átviteli sebesség rugalmas méretezhetőségét horizontális particionálással. Az Autopilot módban konfigurált tárolók és adatbázisok automatikusan és azonnal méretezhetik az alkalmazás által igényelt kiépített átviteli sebességet anélkül, hogy ez befolyásolná a számítási feladat rendelkezésre állását, késését, sebességét vagy teljesítményét globálisan.

* A CompanyXYZ-nek egy biztonságos elemzési platformot kell létrehoznia a teljes rendszerszintű leltári adatokhoz, hogy lehetővé váljon az elemzés és a Bepillantás az ellátási láncbeli partner, a szervezeti egységek és a függvények terén. Az elemzési platformnak lehetővé kell tennie a rendszeren belüli együttműködés használatát, a hagyományos BI/jelentéskészítési használati eseteket, a fejlett analitikai használati eseteket és a prediktív intelligens megoldásokat az operatív leltári adaton keresztül. A Azure Cosmos DB-re vonatkozó szinapszis-hivatkozás használatának előnyei:

  * A [Azure Cosmos db Analytical Store](analytical-store-introduction.md)egy teljesen elszigetelt oszlopdiagram használatával a szinapszis-hivatkozás nem teszi lehetővé, hogy az [Azure szinapszis Analytics](../synapse-analytics/overview-what-is.md) szolgáltatásban ne legyenek kinyerhető átalakítási-betöltési (etl) elemzések a globálisan elosztott operatív adatokon.  Az üzleti elemzők, adatmérnökök és adatszakértők mostantól a szinapszis Spark vagy a szinapszis SQL használatát interoperábilis módon futtathatják a közel valós idejű üzleti intelligenciát, elemzést és gépi tanulási folyamatokat anélkül, hogy ez hatással lenne a tranzakciós munkaterhelések teljesítményére a Azure Cosmos DB. További részletekért tekintse meg a [szinapszis-hivatkozás előnyeit Azure Cosmos DBban](synapse-link.md) .

## <a name="next-steps"></a>Következő lépések

További információért lásd a következő dokumentumokat:

* [Azure Cosmos DB-hez készült Azure Synapse Link](synapse-link.md) 

* [Azure Cosmos DB elemzési tár](analytical-store-introduction.md)

* [Az Azure szinapszis-hivatkozás használata Azure Cosmos DB](configure-synapse-link.md)

* [Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban](synapse-link-frequently-asked-questions.md)

* [Apache Spark az Azure Synapse Analyticsben](../synapse-analytics/spark/apache-spark-concepts.md)

* [Kiszolgáló nélküli SQL Pool Runtime-támogatás az Azure szinapszis Analyticsben](../synapse-analytics/sql/on-demand-workspace-overview.md)