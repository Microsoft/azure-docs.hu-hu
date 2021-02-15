---
title: 'Oktatóanyag: az adatátviteli rendellenességek észlelése Azure Databricks használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a anomália-Kiderítő API-t és a Azure Databricks az adataiban észlelt rendellenességek figyelésére.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: mbullwin
ms.openlocfilehash: f42d294dec4dd2c92fe08498a7bce3c1eabae4b3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519133"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Oktatóanyag: az adatátviteli rendellenességek észlelése Azure Databricks használatával

A [Azure Databricks](https://azure.microsoft.com/services/databricks/) egy gyors, könnyű és együttműködő Apache Spark-alapú elemzési szolgáltatás. Az Azure Cognitive Services részét képező rendellenesség-Kiderítő API lehetővé teszi az idősoros adatai figyelését. Ez az oktatóanyag egy közel valós idejű adatstreamen futtatja a anomáliák észlelését Azure Databricks használatával. A Twitter-adatbevitelt az Azure Event Hubs használatával végezheti el, és importálhatja őket a Azure Databricks a Spark Event Hubs-összekötő használatával. Ezt követően az API használatával észlelheti az adatfolyamban lévő adatvesztést.

Az alábbi ábrán az alkalmazásfolyam látható:

![Azure Databricks Event Hubs és Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks Event Hubs és Cognitive Services")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Twitter-alkalmazás létrehozása a streamadatok eléréséhez
> * Jegyzetfüzetek létrehozása az Azure Databricksben
> * Kódtárak csatlakoztatása az Event Hubshoz és a Twitter API-hoz
> * Anomália-detektor erőforrás létrehozása és a hozzáférési kulcs lekérése
> * Tweetek küldése az Event Hubsnak
> * Tweetek beolvasása az Event Hubsról
> * Anomáliák észlelésének futtatása tweeteken

> [!Note]
> * Ez az oktatóanyag bemutatja, hogyan lehet megvalósítani a rendellenesség-Kiderítő API ajánlott [megoldási architektúráját](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) .
> * Ez az oktatóanyag nem hajtható végre ingyenes szintű ( `F0` ) előfizetéssel a rendellenesség-Kiderítő API-hoz vagy Azure Databrickshoz.

Ha még nem rendelkezik [Azure-előfizetéssel](https://azure.microsoft.com/free/cognitive-services) , hozzon létre egyet.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure Event Hubs névtér](../../../event-hubs/event-hubs-create.md) és Event hub.

- A Event Hubs névtér eléréséhez használt [kapcsolati karakterlánc](../../../event-hubs/event-hubs-get-connection-string.md) . A kapcsolódási karakterláncnak hasonló formátumúnak kell lennie a következőhöz:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.

- A Event Hubs megosztott hozzáférési szabályzatának neve és házirend-kulcsa.

A névtér és az Event hub létrehozásával kapcsolatos információkért tekintse [meg az](../../../event-hubs/event-hubs-create.md) Azure Event Hubs rövid útmutatót.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks munkaterületet hoz létre a [Azure Portal](https://portal.azure.com/)használatával.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Azure Databricks** lehetőséget.

    ![Azure Databricks a portálon](../media/tutorials/azure-databricks-on-portal.png "Databricks Azure Portal")

3. A **Azure Databricks szolgáltatás** területen adja meg a következő értékeket egy Databricks-munkaterület létrehozásához:


    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../../azure-resource-manager/management/overview.md). |
    |**Hely**     | Válassza ki az **USA 2. keleti** régióját vagy egy másik elérhető régiót. Tekintse meg a [régiók számára elérhető Azure-szolgáltatások](https://azure.microsoft.com/regions/services/) elérhetőségét.        |
    |**Tarifacsomag**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. Ne válassza a **próbaverzió** lehetőséget. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

    Kattintson a **Létrehozás** gombra.

4. A munkaterület létrehozása eltarthat néhány percig.

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja a Azure Databricks portálra. A portálon válassza az **új fürt** lehetőséget.

    ![Databricks az Azure-ban](../media/tutorials/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](../media/tutorials/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ehhez a cikkhez hozzon létre egy **5,2** futtatókörnyezettel rendelkező fürtöt. Ne válassza a **5,3** futtatókörnyezetet.
   * Győződjön meg arról, hogy a **megszakítás \_ \_ perc inaktivitás után** jelölőnégyzet be van jelölve. Adja meg a fürt megszakításához szükséges időtartamot (percben), ha a fürt nincs használatban.

     Válassza a **fürt létrehozása** lehetőséget.
4. A fürt létrehozása több percet vesz igénybe. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse a Twitter-alkalmazás létrehozására vonatkozó lépéseket, és jegyezze fel az értékeket, amelyek az oktatóanyag elvégzéséhez szükségesek.

1. Egy webböngészőben nyissa meg a [Twitter Application Management](https://apps.twitter.com/) oldalt, és válassza az **Új alkalmazás létrehozása** elemet.

    ![Twitter-alkalmazás létrehozása](../media/tutorials/databricks-create-twitter-app.png "Twitter-alkalmazás létrehozása")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

    ![Twitter-alkalmazás részletei](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter-alkalmazás részletei")

3. Az alkalmazás oldalán válassza a **Kulcsok és hozzáférési tokenek** fület, és másolja a **Fogyasztói kulcs** és a **Fogyasztói titkos kulcs** mezők értékeit. Ezenkívül válassza a **Saját hozzáférési token létrehozása** lehetőséget a hozzáférési tokenek létrehozásához. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

    ![Twitter-alkalmazás részletei 2](../media/tutorials/twitter-app-key-secret.png "Twitter-alkalmazás részletei")

Mentse a Twitter-alkalmazáshoz lekért értékeket. Az oktatóanyag későbbi részében szüksége lesz rájuk.

## <a name="attach-libraries-to-spark-cluster"></a>Kódtárak csatolása Spark-fürthöz

Ez az oktatóanyag bemutatja, hogyan küldhet tweeteket az Event Hubsnak a Twitter API-k segítségével. Ezenkívül az [Apache Spark Event Hubs-összekötő](https://github.com/Azure/azure-event-hubs-spark) segítségével adatokat olvashat be és írhat az Azure Event Hubsba. Ha az API-kat a fürt részeként kívánja használni, adja hozzá azokat kódtárként az Azure Databrickshez, majd társítsa a Spark-fürthöz. Az alábbi utasítások azt mutatják be, hogyan adhatók hozzá a kódtárak a **megosztott** mappához a munkaterületen.

1. Az Azure Databricks-munkaterületen válassza a **Munkaterület** lehetőséget, majd kattintson a jobb gombbal a **Megosztott** elemre. A helyi menüben válassza a könyvtár **létrehozása** lehetőséget  >  .

   ![Kódtár hozzáadása párbeszédpanel](../media/tutorials/databricks-add-library-option.png "Kódtár hozzáadása párbeszédpanel")

2. Az új könyvtár lapon a **forrás** kiválasztásához válassza a **Maven** lehetőséget. A **koordináták** mezőben adja meg a hozzáadni kívánt csomag koordinátáit. Az oktatóanyagban használt kódtárak Maven-koordinátái a következők:

   * Spark Event Hubs-összekötő – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven-koordináták megadása](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven-koordináták megadása")

3. Válassza a **Létrehozás** lehetőséget.

4. Válassza ki a mappát, amelyhez hozzáadta a kódtárat, majd válassza ki a kódtár nevét.

    ![Válassza ki a hozzáadni kívánt tárat](../media/tutorials/select-library.png "Válassza ki a hozzáadni kívánt tárat")

5. Ha nincs fürt a könyvtár lapon, válassza a **fürtök** lehetőséget, és futtassa a létrehozott fürtöt. Várjon, amíg az állapot megjelenik a "Running", majd térjen vissza a könyvtár lapra.
A könyvtár lapon válassza ki azt a fürtöt, ahol a könyvtárat használni szeretné, majd válassza a **telepítés** lehetőséget. Miután sikeresen hozzárendelte a kódtárat a fürthöz, az állapot azonnal **települ**.

    ![Függvénytár telepítése fürtre](../media/tutorials/databricks-library-attached.png "Függvénytár telepítése fürtre")

6. Ismételje meg ezeket a lépéseket a Twitter-csomag (`twitter4j-core:4.0.7`) esetében is.

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services hozzáférési kulcs beszerzése

Ebben az oktatóanyagban az [Azure Cognitive Services anomália-érzékelő API](../overview.md) -k használatával futtathatja a anomáliák észlelését egy közel valós idejű tweetek streamen. Az API-k használata előtt létre kell hoznia egy rendellenesség-Kiderítő erőforrást az Azure-ban, és be kell szereznie egy hozzáférési kulcsot az anomália detektor API-k használatához

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **+ Erőforrás létrehozása** lehetőséget.

3. Az Azure Marketplace területen válassza az **AI + Machine learning** az  >  **összes**  >  **Cognitive Services – több**  >  **anomália-detektor** elemet. Vagy használhatja ezt a [hivatkozást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) , ha közvetlenül a **Létrehozás** párbeszédpanelt nyitja meg.

    ![Rendellenesség-Kiderítő erőforrás létrehozása](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Rendellenesség-Kiderítő erőforrás létrehozása")

4. A **Létrehozás** párbeszédpanelen adja meg az alábbi értékeket:

    |Érték |Leírás  |
    |---------|---------|
    |Név     | Az anomália-detektor erőforrásának neve.        |
    |Előfizetés     | Az az Azure-előfizetés, amelyhez az erőforrás társítva lesz.        |
    |Hely     | Egy Azure-beli hely.        |
    |Tarifacsomag     | A szolgáltatás díjszabási szintje. További információ az anomália-detektor díjszabásáról: [díjszabási oldal](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Erőforráscsoport     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne kijelölni.        |


     Válassza a **Létrehozás** lehetőséget.

5. Az erőforrás létrehozása után az **Áttekintés** lapon másolja és mentse a **végpont** URL-címét a képernyőképen látható módon. Ezután válassza a **hozzáférési kulcsok megjelenítése** lehetőséget.

    ![Hozzáférési kulcsok megjelenítése](../media/tutorials/cognitive-services-get-access-keys.png "Hozzáférési kulcsok megjelenítése")

6. A **kulcsok** területen válassza a másolás ikont a használni kívánt kulcshoz. Mentse a hozzáférési kulcsot.

    ![Hozzáférési kulcsok másolása](../media/tutorials/cognitive-services-copy-access-keys.png "Hozzáférési kulcsok másolása")

## <a name="create-notebooks-in-databricks"></a>Jegyzetfüzetek létrehozása a Databricksben

Ebben a szakaszban két jegyzetfüzetet hoz létre a Databricks munkaterületen a következő nevekkel

- **SendTweetsToEventHub** – Előállítói jegyzetfüzet a tweetek beszerzésére a Twitterről, majd azok streamelésére az Event Hubsnak.
- **AnalyzeTweetsFromEventHub** – egy fogyasztói jegyzetfüzet, amellyel beolvashatja a tweeteket Event Hubs és futtathatja a anomáliák észlelését.

1. A Azure Databricks munkaterületen válassza a **munkaterület** lehetőséget a bal oldali ablaktáblán. A **Munkaterület** legördülő menüjében válassza a **Létrehozás**, majd a **Jegyzetfüzet** elemet.

    ![Jegyzetfüzet létrehozása a Databricks-ben](../media/tutorials/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen írja be a **SendTweetsToEventHub** nevet, válassza a **Scala** nyelvet, és válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet részletei](../media/tutorials/databricks-notebook-details.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Válassza a **Létrehozás** lehetőséget.

3. Az **AnalyzeTweetsFromEventHub** jegyzetfüzet létrehozásához ismételje meg ezeket a lépéseket.

## <a name="send-tweets-to-event-hubs"></a>Tweetek küldése az Event Hubsnak

Illessze be a következő kódot a **SendTweetsToEventHub** jegyzetfüzetbe, és cserélje le a helyőrzőt a korábban létrehozott Event Hubs névtér és Twitter-alkalmazás értékeire. Ez a jegyzetfüzet kinyeri az "Azure" kulcsszóval rendelkező tweetek létrehozási idejét és "Like" s számát, és valós időben továbbítja azokat az események Event Hubsba.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }

  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie. A kimenetben szereplő minden egyes esemény az időbélyegek és a Event Hubsba betöltött "Like" s szám kombinációja.

```output
    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...
```

## <a name="read-tweets-from-event-hubs"></a>Tweetek beolvasása az Event Hubsról

Illessze be a következő kódot a **AnalyzeTweetsFromEventHub** jegyzetfüzetbe, és cserélje le a helyőrzőt a korábban létrehozott anomália-detektor erőforrásához tartozó értékekre. Ez a jegyzetfüzet beolvassa a tweeteket, amelyeket korábban az Event Hubsba streamelt a **SendTweetsToEventHub** jegyzetfüzet segítségével.

Először írjon egy ügyfelet a anomália-detektor meghívásához.
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie.

```scala
import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection
defined class Point
defined class Series
defined class AnomalySingleResponse
defined class AnomalyBatchResponse
defined object AnomalyDetector
```

Ezután készítse elő az összesítési függvényt a jövőbeli használatra.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)

  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))

  override def dataType: DataType = BooleanType

  override def deterministic: Boolean = false

  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }

  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }

  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }

  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }


      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)

      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }

    return None
  }
}

```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie.

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

Ezután töltse be az Event hub adatait az anomáliák észleléséhez. Cserélje le a helyőrzőt a korábban létrehozott Azure-Event Hubs értékeire.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

A kimenet most az alábbi képhez hasonlít. Vegye figyelembe, hogy a táblázatban szereplő dátum ettől eltérő lehet az oktatóanyagban szereplő dátumtól, mert az adatok valós időben jelennek meg.
![Adatok betöltése az Event hub-ból](../media/tutorials/load-data-from-eventhub.png "Adatok betöltése az Event hub-ból")

Mostantól közel valós időben áramlott Azure Databricks be az Azure Event Hubsból származó adatok az Event Hubs-összekötővel Apache Sparkhoz. A Spark Event Hubs-összekötőinek használatáról az [összekötő dokumentációjában](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs) talál további információt.



## <a name="run-anomaly-detection-on-tweets"></a>Anomáliák észlelésének futtatása tweeteken

Ebben a szakaszban az anomáliák észlelése API használatával elküldött tweetek anomáliát futtatja. Ebben a szakaszban a kódrészleteket fogja hozzáadni ugyanazon **AnalyzeTweetsFromEventHub** jegyzetfüzethez.

A anomáliák észleléséhez először összesíteni kell a metrikák darabszámát óránként.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window.
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
A kimenet mostantól az alábbi kódrészletekhez hasonlít.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Ezután szerezze be az összesített kimeneti eredményt a Delta értékre. Mivel a anomáliák észlelése már egy korábbi előzményi időszakot igényel, a Delta használatával megtarthatja az észlelni kívánt pont előzményeit.
Cserélje le a "[helyőrző: táblanév]" nevet a létrehozandó, minősített Delta Table (például "tweetek") névre. Cserélje le a "[helyőrző: mappa neve az ellenőrzőpontokhoz]" karakterlánc értékét, amely minden alkalommal, amikor futtatja ezt a kódot (például "ETL-from-eventhub-20190605").
Ha többet szeretne megtudni a Azure Databricks Delta Lake-ről, tekintse meg a [Delta Lake útmutatót](/databricks/delta/)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Cserélje le a "[helyőrző: Table Name]" kifejezést a fent kiválasztott különbözeti táblázat nevére.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Az alábbi kimenet:
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Most az összesített idősoros adatokat folyamatosan betölti a rendszer a Különbözetbe. Ezután ütemezhet egy óránkénti feladatot a legutóbbi pont rendellenességének észlelésére.
Cserélje le a "[helyőrző: Table Name]" kifejezést a fent kiválasztott különbözeti táblázat nevére.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Eredmény az alábbiak szerint:

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Ennyi az egész! A Azure Databricks használatával sikeresen továbbította az adatátvitelt az Azure Event Hubsba, a stream-adatmennyiséget az Event Hubs-összekötő használatával használta fel, majd közel valós időben futtatta az adatátviteli rendellenességek észlelését.
Habár ebben az oktatóanyagban a részletesség óránként történik, bármikor módosíthatja a részletességet, hogy megfeleljen a szükséges követelményeknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után leállíthatja a fürtöt. Ehhez a Azure Databricks munkaterületen válassza a **fürtök** lehetőséget a bal oldali ablaktáblán. A leállítani kívánt fürtnél vigye az egérmutatót a **műveletek** **oszlopban található három** pont fölé, majd válassza a Leállítás ikont, majd válassza a **megerősítés** lehetőséget.

![Databricks-fürt leállítása](../media/tutorials/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem állítja be manuálisan a fürtöt, az automatikusan leáll, ha a fürt létrehozásakor bejelölte a **megszakítás \_ \_ perc inaktivitás után** jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan használhatja az Azure Databricks szolgáltatást az adatok Azure Event Hubsra való streamelésére, és hogyan olvashatja valós időben a streamelt adatokat az Event Hubsról. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hívhatja meg a rendellenesség-Kiderítő API-t, és hogyan jeleníthet meg rendellenességeket Power BI

> [!div class="nextstepaction"]
>[A Batch anomália észlelése Power BI asztallal](batch-anomaly-detection-powerbi.md)