---
title: Teljesítménnyel kapcsolatos tippek Azure Cosmos DB Sync Java SDK v2-hez
description: Az ügyfél-konfigurációs beállítások megismerése az Azure Cosmos Database teljesítményének növeléséhez a Java SDK v2 szinkronizálásához
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6e7b01ae88645d8b16c3a43e21e40b53d242fdde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549242"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Teljesítménnyel kapcsolatos tippek Azure Cosmos DB Sync Java SDK v2-hez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Ez *nem* a legújabb Java SDK a Azure Cosmos db! Frissítse a projektet [Azure Cosmos db Java SDK v4](sql-api-sdk-java-v4.md) -re, majd olvassa el a Azure Cosmos db Java SDK v4 [Performance tippek útmutatót](performance-tips-java-sdk-v4-sql.md). A frissítéshez kövesse az [áttelepítés Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -útmutató és a [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) útmutató című témakör utasításait. 
> 
> Ezek a teljesítménnyel kapcsolatos tippek csak Azure Cosmos DB Sync Java SDK v2-re vonatkoznak. További információkért tekintse meg a Azure Cosmos DB Sync Java SDK v2 [kibocsátási megjegyzéseit](sql-api-sdk-java.md) és a [Maven-tárházat](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) .
>

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás. További információkért lásd: [a tárolók teljesítményének kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis átviteli sebességének kiépítése](how-to-provision-database-throughput.md). Mivel azonban a Azure Cosmos DB hálózati hívásokon keresztül érhetők el, az ügyféloldali optimalizálással elérheti a maximális teljesítményt, ha [Azure Cosmos db Sync Java SDK v2](./sql-api-sdk-java.md)-t használ.

Tehát ha a "Hogyan javíthatom az adatbázis teljesítményét?" című témakört kérdezi le? vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózatkezelés
<a id="direct-connection"></a>

1. **Csatlakoztatási mód: DirectHttps használata**

    Az ügyfél Azure Cosmos DB-hez való csatlakozásának módja jelentősen befolyásolja a teljesítményt, főként a megfigyelt ügyféloldali késés szempontjából. Az ügyfél- [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) ( [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode)) konfigurálásához egy kulcsfontosságú konfigurációs beállítás áll rendelkezésre.  A két elérhető ConnectionModes a következők:

   1. [Átjáró (alapértelmezett)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      Az átjáró üzemmód minden SDK-platformon támogatott, és a beállított alapértelmezett érték.  Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ. A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az összes adat beolvasása vagy írása Azure Cosmos DB. Emiatt a DirectHttps mód jobb teljesítményt nyújt kevesebb hálózati ugrás miatt. 

      A Azure Cosmos DB Sync Java SDK v2 HTTPS protokollt használ átviteli protokollként. A HTTPS a TLS-t használja a kezdeti hitelesítéshez és a forgalom titkosításához. A Azure Cosmos DB Sync Java SDK v2 használatakor csak a 443-es HTTPS-portot kell megnyitni. 

      A ConnectionMode a DocumentClient-példány építése során, a ConnectionPolicy paraméterrel konfigurálható. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Sync Java SDK v2 (Maven com. microsoft. Azure:: Azure-documentdb)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="A diagram az Azure Cosmos D B-kapcsolatok házirendjét mutatja." border="false":::

   <a id="same-region"></a>
2. **Az ügyfeleket ugyanabban az Azure-régióban helyezze el a jobb teljesítmény érdekében**

    Ha lehetséges, helyezzen minden olyan alkalmazást, amely a Azure Cosmos DBt hívja meg ugyanabban a régióban, mint az Azure Cosmos Database. A hozzávetőleges összehasonlításhoz az azonos régióban lévő Azure Cosmos DB a 1-2 MS-on belül fejeződik be, de az USA nyugati és keleti partja közötti késés >50 MS. Ez a késés valószínűleg a kérelemtől függ attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure Datacenter-határig. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="A diagram két régióban jeleníti meg a kérelmeket és a válaszokat, ahol a számítógépek a középső rétegbeli szolgáltatásokon keresztül csatlakoznak a Cosmos D B-fiókhoz." border="false":::
   
## <a name="sdk-usage"></a>SDK-használat
1. **A legújabb SDK telepítése**

    A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](./sql-api-sdk-java.md) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.
2. **Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

    Az egyes [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient) -példányok a szálon biztonságosak, és hatékony hálózatkezelést és gyorsítótárazást hajtanak végre közvetlen módban való működés közben. A hatékony DocumentClient és a jobb teljesítmény érdekében ajánlott a DocumentClient/alkalmazástartomány egyetlen példányát használni az alkalmazás élettartamára.

   <a id="max-connection"></a>
3. **MaxPoolSize növelésének engedélyezése az átjáró mód használatakor**

    Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek az átjáró mód használatakor, és a rendszer az alapértelmezett kapcsolati korlátot adja meg egy állomásnév vagy IP-cím alapján. Előfordulhat, hogy a MaxPoolSize magasabb értékre (200-1000) kell állítania, hogy az ügyféloldali kódtár több egyidejű kapcsolatot is felhasználjon Azure Cosmos DBhoz. A Azure Cosmos DB Sync Java SDK v2-ben a [ConnectionPolicy. getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) alapértelmezett értéke 100. Módosítsa az értéket a [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) használatával.

4. **A particionált gyűjtemények párhuzamos lekérdezésének finomhangolása**

    Azure Cosmos DB Sync Java SDK-verzió 1.9.0 és újabb verziói támogatják a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy a lekérdezési késést és az adatátvitelt a soros munkatársaik

    (a) a ***setMaxDegreeOfParallelism \: párhuzamos lekérdezések finomhangolása*** több partíció párhuzamos lekérdezésével működik. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezéssel kapcsolatos sorosan kerülnek beolvasásra. Ezért a [setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) használatával állítsa be a legtöbb teljesítményű lekérdezés elérésének maximális esélyét biztosító partíciók számát, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism használatával magas számot állíthat be, a rendszer pedig a minimális párhuzamosságot (a partíciók számát, a felhasználó által megadott bemenetet) adja meg. 

    Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyöket nyújtják, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény úgy van particionálva, hogy a lekérdezés által visszaadott összes adat többsége néhány partíción (egy partíció a legrosszabb esetben) van, akkor a lekérdezés teljesítményét a partíciók szűk keresztmetszete okozhatja.

    (b) az ***setMaxBufferedItemCount \: párhuzamos lekérdezés finomhangolása*** az eredmények előzetes beolvasására szolgál, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. a setMaxBufferedItemCount korlátozza az előre beolvasott eredmények számát. Ha a [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) értékre állítja a visszaadott eredmények várt számát (vagy egy magasabb számot), ez lehetővé teszi a lekérdezés számára, hogy a lehető legtöbbet fogadja az előzetes lekéréstől.

    Az előzetes lekérés ugyanúgy működik, mint a Maxanalyticsunits, és egyetlen puffer van az összes partícióból származó adatokhoz.  

5. **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

    A teljesítmény tesztelése során növelje a terhelést, amíg a kérelmek kis száma le nem kerül a szabályozásba. Ha a szabályozás meg van adva, az ügyfélalkalmazás leállítási kell lennie a kiszolgáló által megadott újrapróbálkozási időköz szabályozása érdekében. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia. Az újrapróbálkozási szabályzat támogatása a [Azure Cosmos db Sync Java SDK](./sql-api-sdk-java.md)-hoz tartozó 1.8.0 és újabb verziókban érhető el. További információ: [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Az ügyfél felskálázása – munkaterhelés**

    Ha magas átviteli sebességű (>50 000 RU/s) tesztelést végez, előfordulhat, hogy az ügyfélalkalmazás szűk keresztmetszetet okoz a processzor vagy a hálózat kihasználtsága miatt. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

7. **Név alapú címzés használata**

    Használjon név-alapú címzést, ahol a hivatkozások formátuma a `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` SelfLinks helyett ( \_ saját), amelynek formátuma a `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` hivatkozás létrehozásához használt összes erőforrás ResourceId: beolvasásának elkerülése. Továbbá, mivel ezek az erőforrások újra létrejönnek (valószínűleg ugyanazzal a névvel), a gyorsítótárazás nem segít.

   <a id="tune-page-size"></a>
8. **A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

    A dokumentumok tömeges beolvasása a hírcsatornák olvasási funkciójának használatával (például [readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) vagy SQL-lekérdezés kiállításakor az eredményeket szegmentált módon adja vissza, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

    Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör alakú utak számát, az [x-MS-Max-Item-](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) Request fejléc használatával növelheti az oldal méretét a legfeljebb 1000 értékre. Olyan esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API csak 10 eredményt ad vissza, az olvasási és lekérdezési teljesítmény csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

    Az oldalméret a [setPageSize metódussal](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)is megadható.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
1. **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Azure Cosmos DB indexelési házirendje lehetővé teszi annak meghatározását, hogy mely dokumentum-elérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési útvonalak ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) és [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)) használatával. Az indexelési útvonalak használata javíthatja az írási teljesítményt és az alacsonyabb indexű tárolást olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték, mivel az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával vannak összekapcsolva.  Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok teljes szakasza (részfa) az indexelésből a "*" helyettesítő karakterrel.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Sync Java SDK v2 (Maven com. microsoft. Azure:: Azure-documentdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információ: [Azure Cosmos db indexelési házirendek](./index-policy.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

    A Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, beleértve a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, és az adatbázis-gyűjteményen belüli összes műveletet. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

    Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt. 

    A lekérdezés bonyolultsága befolyásolja, hogy hány kérelem-egységet használ a művelet. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok készletének mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

    Bármilyen művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécét (vagy a [ResourceResponse \<T> ](/java/api/com.microsoft.azure.documentdb.resourceresponse) vagy a [FeedResponse \<T> ](/java/api/com.microsoft.azure.documentdb.feedresponse) egyenértékű RequestCharge tulajdonságát a műveletek által felhasznált kérelmek mennyiségének méréséhez.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Sync Java SDK v2 (Maven com. microsoft. Azure:: Azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség töredéke. Ha például 2000 RU/s van kiépítve, és ha az előző lekérdezés a 1000 1KB-dokumentumokat adja vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

    Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel a kérést a RequestRateTooLarge (429-es HTTP-állapotkód), és visszaküldi az [x-MS-újrapróbálkozás-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet, jelezve azt az időtartamot (ezredmásodpercben), ameddig a felhasználónak meg kell várnia a kérés ismételt megkísérlése előtt.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

    Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) hoz az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható a [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) -példány [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) használatával. Alapértelmezés szerint a 429-as kódú DocumentClientException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték.

    Míg az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását, akkor előfordulhat, hogy a teljesítményre vonatkozó teljesítménymutatók végrehajtásakor a rendszer hasznosnak bizonyul, különösen a késés mérése során.  Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.
3. **Tervezzen kisebb dokumentumokat a nagyobb átviteli sebesség érdekében**

    Egy adott műveletre vonatkozó kérelem díja (a kérelmek feldolgozási díja) közvetlenül összefügg a dokumentum méretével. A nagyméretű dokumentumokon végzett műveletek többek között a kis méretű dokumentumok műveleteinél nagyobb mértékben járnak.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partitioning-overview.md)című témakört.