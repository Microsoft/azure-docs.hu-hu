---
title: Fájlok feltöltése az eszközökről az Azure IoT Hub Javával | Microsoft Docs
description: Fájlok feltöltése egy eszközről a felhőbe a Javához készült Azure IoT Device SDK használatával. A feltöltött fájlok tárolása egy Azure Storage blob-tárolóban történik.
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
ms.openlocfilehash: 3529361cacf0890b7c4752bbd745a9240020b4f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217822"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Fájlok feltöltése az eszközről a felhőbe IoT Hub (Java) használatával

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag a [felhőből az eszközre küldött üzenetek küldésére](iot-hub-java-java-c2d.md) szolgáló kódot a IoT hub oktatóanyag segítségével mutatja be, amelyből megtudhatja, hogyan tölthet fel fájlokat az [Azure Blob Storage](../storage/index.yml)-ba a [IoT hub fájlfeltöltés funkciójának](iot-hub-devguide-file-upload.md) használatával. Ez az oktatóanyag a következőket mutatja be:

* A fájlok feltöltéséhez biztonságosan biztosítson egy Azure Blob URI-t tartalmazó eszközt.

* A fájl feltöltésével kapcsolatos értesítéseket használva aktiválja a fájlt az alkalmazás hátterében lévő IoT Hub.

A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-java.md) gyors üzembe helyezése és a [felhőből az eszközre irányuló üzenetek küldése az IoT hub](iot-hub-java-java-c2d.md) oktatóanyaggal mutatja be a IoT hub alapszintű eszközről a felhőbe és a felhőből az eszközre való üzenetkezelés funkcióját. Az [üzenet-útválasztás konfigurálása IoT hub](tutorial-routing.md) oktatóanyagmal az eszközről a felhőbe irányuló üzenetek megbízható tárolásának módját ismerteti az Azure Blob Storage-ban. Bizonyos helyzetekben azonban nem lehet könnyedén leképezni az eszközök által a IoT Hub által elfogadott viszonylag kis eszközről a felhőbe küldött üzeneteket. Például:

* Képeket tartalmazó nagyméretű fájlok
* Videók
* Nagy gyakorisággal vett vibrációs adatelemzés
* Az előfeldolgozott adatmennyiségek valamilyen formája.

Ezeket a fájlokat a rendszer általában a felhőben dolgozza fel a felhőben olyan eszközökkel, mint a [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről szeretne fájlokat felvenni, továbbra is használhatja IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két Java-konzol alkalmazást futtat:

* **szimulált-eszköz**, a [felhőből az eszközre küldött üzenetek küldése a IoT hub] oktatóanyagmal létrehozott alkalmazás módosított verziója. Ez az alkalmazás feltölt egy fájlt a Storage-ba az IoT hub által biztosított SAS URI használatával.

* **olvasási-fájl-feltöltési értesítés**, amely a IoT hub fájl feltöltési értesítéseit fogadja.

> [!NOTE]
> IoT Hub számos eszköz platformját és nyelvét támogatja (beleértve a C, .NET és JavaScript rendszereket) az Azure IoT Device SDK-k használatával. Az eszköz Azure IoT Hubhoz való csatlakoztatásának részletes ismertetését az [Azure IoT fejlesztői központban](https://azure.microsoft.com/develop/iot) találja.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Development Kit 8](/java/azure/jdk/). Győződjön meg arról, hogy a **Java 8** lehetőséget választja a **hosszú távú támogatás** alatt a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése egy eszköz alkalmazásból

Ebben a szakaszban a [felhőből az eszközre irányuló üzenetek küldése a IoT hub](iot-hub-java-java-c2d.md) használatával a IoT hub-ba való feltöltéshez létrehozott eszközt módosítja.

1. Másolja a mappába egy lemezképfájlt, `simulated-device` és nevezze át `myimage.png` .

2. Egy szövegszerkesztővel nyissa meg a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

3. Adja hozzá a változó deklarációt az **app** osztályhoz:

    ```java
    private static String fileName = "myimage.png";
    ```

4. A fájlfeltöltés állapotának visszahívási üzeneteinek feldolgozásához adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Ha képeket szeretne feltölteni a IoT Hubba, adja hozzá a következő metódust az **app** osztályhoz a lemezképek IoT hubba való feltöltéséhez:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Módosítsa a **Main** metódust a **uploadFile** metódus meghívásához az alábbi kódrészletben látható módon:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. A következő parancs használatával hozza létre a **szimulált-eszköz** alkalmazást, és keressen hibákat:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely a fájlfeltöltés-értesítési üzeneteket fogadja a IoT hub-ból, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-java.md)hozott létre. A fájlfeltöltés-értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltés-értesítés fogadása

Ebben a szakaszban egy Java-konzol alkalmazást hoz létre, amely a IoT Hub címről érkező fájlfeltöltés-értesítési üzeneteket fogad.

1. Hozzon létre egy **READ-file-upload-Notification** nevű Maven-projektet a következő parancs futtatásával a parancssorban. Vegye figyelembe, hogy ez a parancs egyetlen hosszú parancs:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban navigáljon az új `read-file-upload-notification` mappára.

3. Egy szövegszerkesztővel nyissa meg a `pom.xml` fájlt a `read-file-upload-notification` mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. A függőség hozzáadása lehetővé teszi, hogy a **iothub-Java-Service-Client** csomagot használja az alkalmazásban az IoT hub szolgáltatással való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Mentse és zárjuk be a `pom.xml` fájlt.

5. Egy szövegszerkesztővel nyissa meg a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. A `{Your IoT Hub connection string}` helyőrző értékét cserélje le a IoT hub-beli, a korábban a [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)során átmásolt karakterláncra:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Ha adatokat szeretne nyomtatni a konzolon a fájl feltöltéséről, adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. A fájlfeltöltés-értesítéseket figyelő szál elindításához adja hozzá a következő kódot a **Main** metódushoz:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Mentse és zárjuk be a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

11. A következő parancs használatával hozza létre az **olvasási-fájl-feltöltési-értesítési** alkalmazást, és keressen hibákat:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A mappában a parancssorban `read-file-upload-notification` futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A mappában a parancssorban `simulated-device` futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Az alábbi képernyőfelvételen a **szimulált eszköz** alkalmazás kimenete látható:

![Szimulált eszköz alkalmazás kimenete](media/iot-hub-java-java-upload/simulated-device.png)

A következő képernyőképen az **olvasási fájl – feltöltés – értesítési** alkalmazás kimenete látható:

![A Read-file-upload-Notification alkalmazás kimenete](media/iot-hub-java-java-upload/read-file-upload-notification.png)

A portál használatával megtekintheti a feltöltött fájlt a konfigurált tárolóban:

![Feltöltött fájl](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a IoT Hub fájlfeltöltés funkcióit az eszközökről történő fájlfeltöltés egyszerűsítése érdekében. A IoT hub funkcióit és forgatókönyveit továbbra is megismerheti a következő cikkekkel:

* [IoT hub programozott módon történő létrehozása](iot-hub-rm-template-powershell.md)

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [Eszköz szimulálása IoT Edge](../iot-edge/quickstart-linux.md)