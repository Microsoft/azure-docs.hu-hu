---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (Java) használatával | Microsoft Docs
description: A felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről az Azure IoT SDK-k használatával a Javához. Módosít egy szimulált eszközt a felhőből az eszközre irányuló üzenetek fogadására és a felhőből az eszközre irányuló üzenetek küldésére szolgáló háttérbeli alkalmazás módosítására.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 5ae1850add94d83278b0fe1905dfa6e53c71fc8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217890"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Üzenetküldés a felhőből az eszközre IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-java.md) -gyors üzembe helyezési útmutató bemutatja, hogyan hozhat létre egy IoT hubot, kiépítheti a benne lévő eszköz identitását, valamint kódot készíthet egy szimulált eszközről, amely az eszközről a felhőbe irányuló üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-java.md)épít. Bemutatja, hogyan végezheti el a következőket:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.

* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

[A felhőből az eszközre irányuló üzenetekről a IoT hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Java-konzol alkalmazást futtat:

* **szimulált-eszköz**, az alkalmazás egy módosított verziója, amelyet a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-java.md)hoztak létre, amely csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* **Send-C2D-** messages, amely egy felhőből az eszközre irányuló üzenetet küld a szimulált eszköz alkalmazásnak IoT hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

> [!NOTE]
> A IoT Hub számos eszköz platformjának és nyelvének (például C, Java, Python és JavaScript) támogatásával rendelkezik az Azure IoT Device SDK-k használatával. Az eszköznek az oktatóanyag kódjához való csatlakoztatásának részletes ismertetését, és általában az Azure IoT Hub az Azure [IoT fejlesztői központját](https://azure.microsoft.com/develop/iot)tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

* Az [eszközről a IoT hub](quickstart-send-telemetry-java.md) gyors üzembe helyezése, illetve az telemetria- [Útválasztás konfigurálása IoT hub](tutorial-routing.md) oktatóanyag használatával.

* [Java SE Development Kit 8](/java/azure/jdk/). Győződjön meg arról, hogy a **Java 8** lehetőséget választja a **hosszú távú támogatás** alatt a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban

Ebben a szakaszban módosítania kell a szimulált eszközt, amelyet a [telemetria küldése eszközről egy IoT-hubhoz](quickstart-send-telemetry-java.md) hozott létre, hogy fogadja a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

2. Adja hozzá a következő **MessageCallback** osztályt beágyazott osztályként az **app** osztályban belül. Az **Execute** metódus akkor kerül meghívásra, amikor az eszköz üzenetet kap IoT hubról. Ebben a példában az eszköz mindig értesíti az IoT hubot arról, hogy befejezte az üzenetet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Módosítsa a **Main** metódust egy **AppMessageCallback** -példány létrehozásához, és hívja meg a **setMessageCallback** metódust, mielőtt a következőképpen megnyitja az ügyfelet:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

A `execute` osztály metódusa `AppMessageCallback` visszaadja a értéket `IotHubMessageResult.COMPLETE` . Ez értesíti IoT Hub az üzenet sikeres feldolgozásáról, valamint arról, hogy az üzenet biztonságosan eltávolítható az eszköz-várólistából. Az eszköznek ezt az értéket kell visszaadnia, ha a feldolgozás sikeresen befejeződik, függetlenül attól, hogy milyen protokollt használ.

A AMQP és a HTTPS használatával, de nem MQTT, az eszköz a következőket is elvégezheti:

* Hagyjon ki egy üzenetet, amely IoT Hub az üzenet megtartását az eszköz várólistájában a későbbi felhasználás érdekében.
* Egy üzenet elutasítása, amely véglegesen eltávolítja az üzenetet az eszköz várólistáról.

Ha valami történik, amely megakadályozza, hogy az eszköz elvégezze, lemondsa vagy elutasítja az üzenetet, IoT Hub a rögzített időtúllépési időszak után az üzenetet újra kézbesíti. Emiatt az *idempotens* logikának kell lennie az eszköz alkalmazásában, hogy ugyanazt az üzenetet kapja többször is ugyanez az eredmény.

További információ arról, hogy a IoT Hub hogyan dolgozza fel a felhőből az eszközre irányuló üzeneteket, beleértve a felhőből az eszközre irányuló üzenetek életciklusának részleteit is: a [felhőből az eszközre irányuló üzenetek küldése az IoT hub-ból](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Ha MQTT vagy AMQP helyett HTTPS-t használ, akkor a **DeviceClient** -példányok ritkán keresnek IoT hub üzeneteket (legalább 25 percenként). További információ a MQTT, a AMQP és a HTTPS-támogatás közötti különbségekről: a [felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md) és [a kommunikációs protokoll kiválasztása](iot-hub-devguide-protocols.md).

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebből a cikkből megtudhatja, hogyan küldhet el egy háttér-szolgáltatást a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése az eszközről az IoT hubhoz](quickstart-send-telemetry-java.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Ebben a szakaszban egy Java-konzol alkalmazást hoz létre, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszköz alkalmazásnak. Szüksége lesz annak az eszköznek az eszköz-AZONOSÍTÓJÁRA, amelyet a [telemetria küldése az eszközről az IoT hub](quickstart-send-telemetry-java.md) -gyors üzembe helyezéshez adott meg. Szüksége lesz a korábban átmásolt IoT hub-kapcsolatok karakterláncára is a [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)című részében.

1. Hozzon létre egy **Send-C2D-messages** nevű Maven-projektet a következő parancs futtatásával a parancssorban. Vegye figyelembe, hogy ez a parancs egyetlen hosszú parancs:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban navigáljon az új Send-C2D-messages mappára.

3. Egy szövegszerkesztővel nyissa meg az pom.xml fájlt a Send-C2D-messages mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. A függőség hozzáadása lehetővé teszi, hogy a **iothub-Java-Service-Client** csomagot használja az alkalmazásban az IoT hub szolgáltatással való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Mentse és zárja be a pom.xml fájlt.

5. Egy szövegszerkesztővel nyissa meg a send-c2d-messages\src\main\java\com\mycompany\app\App.java fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adja hozzá a következő osztály-szintű változókat az **app** osztályhoz, lecserélve a **{yourhubconnectionstring}** és **{yourdeviceid}** kifejezést a korábban feljegyzett értékekre:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Cserélje le a **Main** metódust a következő kódra. Ez a kód csatlakozik az IoT hub-hoz, üzenetet küld az eszköznek, majd megvárja, amíg az eszköz befogadta és feldolgozta az üzenetet:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például az exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).

9. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A szimulált-Device mappában egy parancssorban futtassa a következő parancsot, hogy megkezdje a telemetria küldését az IoT hub-ra, és figyelje a központból küldött felhőből az eszközre irányuló üzeneteket:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![A szimulált eszköz alkalmazásának futtatása](./media/iot-hub-java-java-c2d/receivec2d.png)

2. A Send-C2D-messages mappában egy parancssorban futtassa a következő parancsot egy felhőből az eszközre irányuló üzenet küldéséhez, és várjon a visszajelzések visszaigazolására:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Futtassa a parancsot a felhőből az eszközre irányuló üzenet elküldéséhez](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT megoldás-gyorssegédek](https://azure.microsoft.com/documentation/suites/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).