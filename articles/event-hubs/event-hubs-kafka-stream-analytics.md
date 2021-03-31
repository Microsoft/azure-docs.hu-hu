---
title: Azure Event Hubs – Apache Kafka események feldolgozása
description: 'Oktatóanyag: Ez a cikk bemutatja, hogyan dolgozhatja fel az Event hub-n keresztül betöltött Kafka-eseményeket Azure Stream Analytics használatával'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 328537eebd05391fc4c8138395a9c10f0a5d072f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319380"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Oktatóanyag: Event Hubs-események Apache Kafkaának feldolgozása a stream Analytics használatával 
Ez a cikk bemutatja, hogyan továbbíthatja az adatstreameket a Event Hubsba, és hogyan dolgozhatja fel azokat Azure Stream Analytics használatával. Végigvezeti a következő lépéseken: 

1. Event Hubs-névtér létrehozása.
2. Hozzon létre egy Kafka-ügyfelet, amely üzeneteket küld az Event hub-nak.
3. Hozzon létre egy Stream Analytics feladatot, amely az Event hub adatait másolja egy Azure Blob Storage-ba. 

Az Event hub által közzétett Kafka-végpont használatakor nincs szükség a protokoll ügyfeleinek módosítására vagy a saját fürtök futtatására. Az Azure Event Hubs az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja. és újabb verziók. 


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Töltse le](https://maven.apache.org/download.cgi) és [telepítse](https://maven.apache.org/install.html) a Maven bináris archívumát.
* [Git](https://www.git-scm.com/)
* Egy **Azure Storage-fiók**. Ha még nem rendelkezik ilyennel, a folytatás előtt [hozzon létre egyet](../storage/common/storage-account-create.md) . Az ebben a bemutatóban található Stream Analytics feladata egy Azure Blob Storage-ban tárolja a kimeneti adatokat. 


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
A **standard** szintű Event Hubs névtér létrehozásakor a rendszer automatikusan engedélyezi a névtérhez tartozó Kafka-végpontot. A Kafka protokollt használó alkalmazásokból olyan eseményeket lehet továbbítani, amelyek standard szintű Event Hubs. A **standard** szintű Event Hubs névtér létrehozásához kövesse az [Event hub létrehozása a Azure Portal használatával](event-hubs-create.md) című témakör részletes utasításait. 

> [!NOTE]
> A Kafka-Event Hubs csak a **standard** és a **dedikált** szinteken érhető el. Az alapszintű **csomag** nem támogatja a Kafka használatát Event Hubson.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése a Kafka-vel Event Hubs

1. A [Kafka-tárházhoz tartozó Azure-Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka) klónozása a gépre.
2. Navigáljon a következő mappához: `azure-event-hubs-for-kafka/quickstart/java/producer` . 
4. Frissítse a gyártó konfigurációs adatait a alkalmazásban `src/main/resources/producer.config` . Adja meg az **Event hub-névtér** **nevét** és **kapcsolati karakterláncát** . 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigáljon a címre `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` , és nyissa meg a **TestDataReporter. Java** fájlt egy tetszőleges szerkesztőben. 
6. Tegye megjegyzésbe a következő kódrészletet:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adja hozzá a következő kódrészletet a Megjegyzés kódjának helyére: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ez a kód **JSON** formátumban küldi el az eseményre vonatkozó adatokat. Amikor egy stream Analytics-feladathoz konfigurálja a bemenetet, a bemeneti adatok formátuma adja meg a JSON-t. 
7. **Futtassa a gyártót és a** streamet Event Hubsba. Ha Windows-gépen **Node.js parancssort** használ, váltson át a `azure-event-hubs-for-kafka/quickstart/java/producer` mappára a parancsok futtatása előtt. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Annak ellenőrzése, hogy az Event hub fogadja-e az

1. Az **entitások** területen válassza a **Event Hubs** lehetőséget. Ellenőrizze, hogy megjelenik-e egy **teszt** nevű Event hub. 

    ![Event hub – teszt](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Győződjön meg arról, hogy az Event hub-ba érkező üzenetek láthatók. 

    ![Event hub – üzenetek](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Event-adatok feldolgozása Stream Analytics feladatokkal
Ebben a szakaszban egy Azure Stream Analytics feladatot hoz létre. A Kafka-ügyfél eseményeket küld az Event hub-nak. Olyan Stream Analytics-feladatot hoz létre, amely adatokat fogad el bemenetként, és egy Azure Blob Storage-ba küldi azt. Ha nem rendelkezik **Azure Storage-fiókkal**, [hozzon létre egyet](../storage/common/storage-account-create.md).

A Stream Analytics-feladatokban szereplő lekérdezés az adatelemzések végrehajtása nélkül halad át az adatokon. Létrehozhat egy olyan lekérdezést, amely átalakítja a bemeneti adatokat, hogy más formátumú kimeneti adatokat hozzon létre, vagy az elemzések során.  

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása 

1. Válassza az **+ erőforrás létrehozása** lehetőséget a [Azure Portalban](https://portal.azure.com).
2. Válassza az **elemzés** lehetőséget az **Azure Marketplace** menüjében, majd válassza ki **stream Analytics feladatot**. 
3. Az **új stream Analytics** oldalon hajtsa végre a következő műveleteket: 
    1. Adja meg a feladattípus **nevét** . 
    2. Válassza ki **előfizetését**.
    3. Válassza az **új létrehozása** az **erőforráscsoport** számára lehetőséget, és adja meg a nevet. **Használhat meglévő** erőforráscsoportot is. 
    4. Válassza ki a feladatokhoz tartozó **helyet** .
    5. Válassza a **Létrehozás** lehetőséget a feladatok létrehozásához. 

        ![Új Stream Analytics-feladatok](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Az értesítési üzenetben válassza az **Ugrás az erőforráshoz** lehetőséget a **stream Analytics feladatok** lap megjelenítéséhez. 
2. A bal oldali menüben a **feladatok topológiája** szakaszban válassza a **bemenetek** lehetőséget.
3. Válassza az **adatfolyam hozzáadása bemenet** lehetőséget, majd válassza az **Event hub** elemet. 

    ![Event hub hozzáadása bemenetként](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Az **Event hub bemeneti** konfigurációja lapon hajtsa végre a következő műveleteket: 

    1. Adja meg a bemenet **aliasát** . 
    2. Válassza ki az **Azure-előfizetését**.
    3. Válassza ki a korábban létrehozott **Event hub-névteret** . 
    4. Válassza ki az **Event hub** **tesztjét** . 
    5. Kattintson a **Mentés** gombra. 

        ![Event hub-beli bemeneti konfiguráció](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Feladatkimenet konfigurálása 

1. Válassza a **kimenetek** lehetőséget a menü **feladatok topológiája** szakaszában. 
2. Válassza a **+ Hozzáadás** lehetőséget az eszköztáron, és válassza a **blob Storage** elemet.
3. A blob Storage kimeneti beállításai oldalon hajtsa végre a következő műveleteket: 
    1. Adja meg a kimenet **aliasát** . 
    2. Válassza ki az Azure- **előfizetését**. 
    3. Válassza ki az **Azure Storage-fiókját**. 
    4. Adja meg annak a **tárolónak a nevét** , amely a stream Analytics lekérdezésből származó kimeneti adatokat tárolja.
    5. Kattintson a **Mentés** gombra.

        ![Blob Storage kimeneti konfiguráció](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Lekérdezés meghatározása
Miután sikeresen beállította a Stream Analytics-feladatot a beérkező adatfolyam olvasására, a következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](/stream-analytics-query/stream-analytics-query-language-reference). Ebben az útmutatóban egy olyan lekérdezést határoz meg, amely áthalad az adatokon anélkül, hogy átalakítást végezne.

1. Válassza a **lekérdezés** lehetőséget.
2. A lekérdezési ablakban cserélje le `[YourOutputAlias]` a nevet a korábban létrehozott kimeneti aliasra.
3. Cserélje le `[YourInputAlias]` a nevet a korábban létrehozott bemeneti aliasra. 
4. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![A képernyőfelvételen a bemeneti és a kimeneti változók értékeit tartalmazó lekérdezési ablak látható.](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. Válassza az **Áttekintés** lehetőséget a bal oldali menüben. 
2. Válassza az **Indítás** elemet. 

    ![Start menü](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. A **kezdési feladatok** lapon válassza az **Indítás** lehetőséget. 

    ![Kezdési feladatok lap](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Várjon, amíg a feladatok állapota nem változik a **futtatástól** **kezdve** . 

    ![Feladatok állapota – fut](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése
1. Futtassa újra a **Kafka gyártóját** , hogy eseményeket küldjön az Event hub-nak. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Győződjön meg arról, hogy a **kimeneti adatokat** az **Azure Blob Storage**-ban hozza létre a rendszer. A tárolóban egy olyan JSON-fájl jelenik meg, amely a következő mintához hasonló 100 sorral rendelkezik: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    A Azure Stream Analytics feladatot kapott a bemeneti adatok az Event hub-ból, és az Azure Blob Storage-ban tárolta ebben a forgatókönyvben. 



## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan végezheti el a Event Hubs továbbítását a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. Ha többet szeretne megtudni a Apache Kafka Event Hubsről, tekintse meg [Az Azure Event Hubs Apache Kafka fejlesztői útmutató](apache-kafka-developer-guide.md)című témakört.