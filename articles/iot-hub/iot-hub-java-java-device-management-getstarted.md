---
title: Ismerkedés az Azure IoT Hub-eszközkezelés (Java) szolgáltatással | Microsoft Docs
description: Az Azure IoT Hub-eszközök felügyeletének használata távoli eszköz újraindításának elindításához. A Javához készült Azure IoT eszközoldali SDK használatával olyan szimulált eszköz alkalmazást hozhat létre, amely közvetlen metódust és a Javához készült Azure IoT Service SDK-t is tartalmazza a közvetlen metódust meghívó szolgáltatásalkalmazás megvalósításához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: f05e1a458bc83fe4042c4b6cf35d9aa2095868ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217958"
---
# <a name="get-started-with-device-management-java"></a>Ismerkedés az eszközkezelés (Java) szolgáltatással

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal használatával hozzon létre egy IoT Hub, és hozzon létre egy eszköz-identitást az IoT hub-ban.

* Hozzon létre egy szimulált eszköz alkalmazást, amely egy közvetlen módszert valósít meg az eszköz újraindításához. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy alkalmazást, amely elindítja a Direct reboot metódust a szimulált eszköz alkalmazásban az IoT hub használatával. Ez az alkalmazás ezután figyeli a jelentett tulajdonságokat az eszközről, hogy megtekintse az újraindítási művelet befejeződését.

Az oktatóanyag végén két Java-konzolos alkalmazás található:

**szimulált eszköz**. Ez az alkalmazás:

* Csatlakozik az IoT hubhoz a korábban létrehozott eszköz-identitással.

* A közvetlen újraindítási metódus hívásának fogadása.

* Fizikai újraindítás szimulálása.

* A jelentett tulajdonság utolsó újraindításának időpontját jelenti.

**trigger – újraindítás**. Ez az alkalmazás:

* Közvetlen metódust hív meg a szimulált eszköz alkalmazásban.

* Megjeleníti a szimulált eszköz által küldött közvetlen metódus hívására adott választ.

* Megjeleníti a frissített jelentett tulajdonságokat.

> [!NOTE]
> Az eszközökön és a megoldás hátterében futó alkalmazások létrehozásához használható SDK-k ismertetése: [Azure IoT SDK](iot-hub-devguide-sdks.md)-k.

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Development Kit 8](/java/azure/jdk/). Győződjön meg arról, hogy a **Java 8** lehetőséget választja a **hosszú távú támogatás** alatt a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás indítása az eszközön közvetlen módszer használatával

Ebben a szakaszban egy Java-konzol alkalmazást hoz létre, amely a következőket tartalmazza:

1. Elindítja a Direct reboot metódust a szimulált eszköz alkalmazásban.

2. Megjeleníti a választ.

3. Az eszközről küldött jelentett tulajdonságok lekérdezése az újraindítás befejezésének megállapításához.

Ez a konzol-alkalmazás csatlakozik a IoT Hubhoz, hogy meghívja a közvetlen metódust, és beolvassa a jelentett tulajdonságokat.

1. Hozzon létre egy, a **DM-Get-Started** nevű üres mappát.

2. A **DM-Get-Started** mappában hozzon létre egy **trigger-újraindítás** nevű Maven-projektet a következő parancs használatával a parancssorban:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. A parancssorban navigáljon a **trigger-reboot** mappára.

4. Egy szövegszerkesztővel nyissa meg a **pom.xml** fájlt a **trigger-reboot** mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ezzel a függőséggel használhatja az IOT-Service-Client csomagot az alkalmazásban az IoT hub használatával folytatott kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

5. Adja hozzá a következő **Build** csomópontot a **függőségek** csomópont után. Ez a konfiguráció arra utasítja a Mavent, hogy a Java 1,8-et használja az alkalmazás létrehozásához:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Mentse és zárja be a **pom.xml** fájlt.

7. Egy szövegszerkesztővel nyissa meg a **trigger-reboot\src\main\java\com\mycompany\app\App.Java** forrásfájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le a (z) helyére `{youriothubconnectionstring}` a korábban átmásolt IoT hub-kapcsolatok karakterláncát a [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Ha olyan szálat szeretne megvalósítani, amely a jelentett tulajdonságokat 10 másodpercenként beolvassa az eszközről, adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Módosítsa a **Main** metódus aláírását úgy, hogy kidobja a következő kivételt:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. A szimulált eszközön a közvetlen újraindítási módszer meghívásához cserélje le a kódot a **Main** metódusba a következő kóddal:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Ha szeretné elindítani a szálat a jelentett tulajdonságok lekérdezéséhez a szimulált eszközről, adja hozzá a következő kódot a **Main** metódushoz:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Az alkalmazás leállításának engedélyezéséhez adja hozzá a következő kódot a **Main** metódushoz:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Mentse és zárjuk be a **trigger-reboot\src\main\java\com\mycompany\app\App.Java** fájlt.

16. Hozza létre a **triggert – indítsa újra** a háttér-alkalmazást, és javítsa ki az esetleges hibákat. A parancssorban navigáljon a **trigger-reboot** mappára, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Java-konzol alkalmazást hoz létre, amely egy eszközt szimulál. Az alkalmazás figyeli a közvetlen újraindítási metódus hívását az IoT hub-ból, és azonnal válaszol erre a hívásra. Az alkalmazás ezután alvó állapotba lép, hogy szimulálja az újraindítási folyamatot, mielőtt a jelentett tulajdonságot felhasználva értesíti az újraindítást **indító háttér-újraindítási** alkalmazást.

1. A **DM-Get-Started** mappában hozzon létre egy **szimulált eszköz** nevű Maven-projektet a következő parancs parancssorból történő futtatásával:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban navigáljon a **szimulált eszköz** mappájához.

3. Egy szövegszerkesztővel nyissa meg a **szimulált eszköz** mappában található **pom.xml** fájlt, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ezzel a függőséggel használhatja az IOT-Service-Client csomagot az alkalmazásban az IoT hub használatával folytatott kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ez a függőség konfigurálja az Apache [SLF4J](https://www.slf4j.org/) naplózási homlokzatának NOP, amelyet az eszköz ügyféloldali SDK használ a naplózás megvalósításához. Ez a konfiguráció nem kötelező, de ha kihagyja, az alkalmazás futtatásakor figyelmeztetés jelenik meg a konzolon. Az eszköz ügyféloldali SDK-beli naplózásával kapcsolatos további információkért lásd: az *Azure IoT ESZKÖZOLDALI SDK for Java* információs fájlhoz tartozó minták [naplózása](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Adja hozzá a következő **Build** csomópontot a **függőségek** csomópont után. Ez a konfiguráció arra utasítja a Mavent, hogy a Java 1,8-et használja az alkalmazás létrehozásához:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Mentse és zárja be a **pom.xml** fájlt.

7. Egy szövegszerkesztővel nyissa meg a **simulated-device\src\main\java\com\mycompany\app\App.Java** forrásfájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le az `{yourdeviceconnectionstring}` eszközt az [új eszköz regisztrálása az IoT hub-ben](#register-a-new-device-in-the-iot-hub) című szakaszban leírt eszköz-csatlakoztatási karakterláncra:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Ha egy visszahívási kezelőt szeretne megvalósítani a közvetlen metódusok állapotának eseményeihez, adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Ha egy visszahívási kezelőt szeretne megvalósítani az eszközök Twin állapotával kapcsolatos eseményekhez, adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. A tulajdonság eseményeinek visszahívási kezelőjének megvalósításához adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Ha egy szálat szeretne megvalósítani az eszköz újraindításának szimulálása érdekében, adja hozzá a következő beágyazott osztályt az **app** osztályhoz. A szál öt másodpercig alvó állapotba kerül, majd beállítja a **lastReboot** jelentett tulajdonságot:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Az eszközön a közvetlen metódus megvalósításához adja hozzá a következő beágyazott osztályt az **app** osztályhoz. Amikor a szimulált alkalmazás fogadja a közvetlen **Újraindítási** módszert, visszaigazolja a hívónak, majd elindít egy szálat az újraindítás feldolgozásához:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Módosítsa a **Main** metódus aláírását úgy, hogy az a következő kivételeket dobja:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. **DeviceClient** létrehozásához cserélje le a **Main** metódus kódját a következő kódra:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. A közvetlen metódusok hívásainak figyelésének megkezdéséhez adja hozzá a következő kódot a **Main** metódushoz:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Az eszköz-szimulátor leállításához adja hozzá a következő kódot a **Main** metódushoz:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Mentse és zárjuk be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

20. Hozza létre a **szimulált eszköz** alkalmazást, és javítsa ki az esetleges hibákat. A parancssorban navigáljon a **szimulált-Device** mappára, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **szimulált-Device** mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a IoT hub újraindítási metódusának figyelését:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szimulált eszköz alkalmazás a közvetlen újraindítási metódusok hívásának figyeléséhez](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. A **trigger-reboot** mappában egy parancssorban futtassa a következő parancsot a szimulált eszközön a IoT hub-ról indított újraindítási módszer meghívásához:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatásalkalmazás a reboot Direct metódus meghívásához](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. A szimulált eszköz válaszol a közvetlen újraindítási metódus hívására:

    ![A Java IoT Hub szimulált eszköz alkalmazás válaszol a közvetlen metódus hívására](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]