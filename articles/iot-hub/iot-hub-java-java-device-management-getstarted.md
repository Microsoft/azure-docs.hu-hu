---
title: Az eszközkezelés (Java Azure IoT Hub – első lépések | Microsoft Docs
description: Az eszközfelügyelet Azure IoT Hub távoli eszköz-újraindítás kezdeményezéséhez. A Javához készült Azure IoT eszközoldali SDK-val egy közvetlen metódust tartalmazó szimulált eszközalkalmazást, a Javához készült Azure IoT szolgáltatási SDK-t pedig a közvetlen metódust meghívó szolgáltatásalkalmazást valósíthat meg.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 797c795b6c936e2f117a96fed4b4a483876f1c47
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478080"
---
# <a name="get-started-with-device-management-java"></a>Az eszközkezelés első lépések (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal hozzon létre egy IoT Hub és hozzon létre egy eszközidentitást az IoT Hubban.

* Hozzon létre egy szimulált eszközalkalmazást, amely közvetlen metódust valósít meg az eszköz újraindítása érdekében. A közvetlen metódusok a felhőből vannak meghívva.

* Hozzon létre egy alkalmazást, amely meghívja a közvetlen újraindítási metódust a szimulált eszközalkalmazásban az IoT Hubon keresztül. Ez az alkalmazás ezután figyeli az eszköz jelentett tulajdonságait, hogy lássa, mikor fejeződött be az újraindítási művelet.

Az oktatóanyag végén két Java-konzolalkalmazása lesz:

**simulated-device**. Ez az alkalmazás:

* A korábban létrehozott eszközidentitással csatlakozik az IoT Hubhoz.

* Egy újraindítási közvetlen metódushívást fogad.

* Fizikai újraindítást szimulál.

* Egy jelentett tulajdonságon keresztül jelenti az utolsó újraindítás idejét.

**trigger-reboot**. Ez az alkalmazás:

* Közvetlen metódust hív meg a szimulált eszközalkalmazásban.

* Megjeleníti a szimulált eszköz által küldött közvetlen metódushívásra adott választ.

* Megjeleníti a frissített jelentett tulajdonságokat.

> [!NOTE]
> Az eszközökön és a megoldás háttéralkalmazásán futó alkalmazások buildjéhez használható ADATTK-okkal kapcsolatos információkért lásd: [Azure IoT SDK-k.](iot-hub-devguide-sdks.md)

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Development Kit 8](/java/azure/jdk/). A JDK 8 **letöltéséhez** válassza a **Java 8** lehetőséget a Hosszú távú támogatás alatt.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetek blokkolják ezt a portot. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT Hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT Hub kapcsolati sztring lekérte

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban egy Java-konzolalkalmazást hoz létre, amely:

1. Meghívja a közvetlen újraindítási metódust a szimulált eszközalkalmazásban.

2. Megjeleníti a választ.

3. Lekérdezi az eszközről küldött jelentett tulajdonságokat annak megállapításához, hogy mikor fejeződött be az újraindítás.

Ez a konzolalkalmazás csatlakozik a IoT Hub, hogy meghívja a közvetlen metódust, és beolvassa a jelentett tulajdonságokat.

1. Hozzon létre egy **dm-get-started nevű üres mappát.**

2. A **dm-get-started mappában** hozzon létre egy **trigger-reboot** nevű Maven-projektet a következő paranccsal a parancssorban:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. A parancssorban keresse meg a **trigger-reboot mappát.**

4. Egy szövegszerkesztővel nyissa meg a **pom.xml** **trigger-reboot** mappában, és adja hozzá a következő függőséget a **függőségek csomóponthoz.** Ez a függőség lehetővé teszi az iot-service-client csomag használatát az alkalmazásban az IoT Hubbal való kommunikációhoz:

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

5. Adja hozzá a **következő buildcsomópontot** a **függőségek csomópont** után. Ez a konfiguráció arra utasítja a Mavent, hogy a Java 1.8 használatával készítse el az alkalmazást:

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

7. Egy szövegszerkesztővel nyissa meg a **trigger-reboot\src\main\java\com\mycompany\app\App.java** forrásfájlt.

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

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le a helyére IoT Hub korábban az IoT Hub kapcsolati sztring `{youriothubconnectionstring}` [lekért kapcsolati sztringjében kimásott kapcsolati sztringet:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Ha olyan szálat szeretne implementálni, amely 10 másodpercenként olvassa be a jelentett tulajdonságokat az ikereszközről, adja hozzá a következő beágyazott **osztályt** az App osztályhoz:

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

11. Módosítsa a fő metódus **aláírását** úgy, hogy az a következő kivételt dobjon:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. A közvetlen újraindítási metódus a szimulált eszközön való meghíváshoz cserélje le a **fő** metódusban található kódot a következő kódra:

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

13. Ahhoz, hogy elindítsa a szálat a jelentett tulajdonságok szimulált eszközről való lekérdezéséhez, adja hozzá a következő kódot a **fő metódushoz:**

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Az alkalmazás leállításának engedélyezéséhez adja hozzá a következő kódot a **main metódushoz:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Mentse és zárja be a **trigger-reboot\src\main\java\com\mycompany\app\App.java** fájlt.

16. Készítse el **a trigger-reboot** háttéralkalmazást, és javítsa ki a hibákat. A parancssorban keresse meg a **trigger-reboot** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Java-konzolalkalmazást hoz létre, amely egy eszközt szimulál. Az alkalmazás az IoT Hubról származó közvetlen újraindítási metódus hívását figyeli, és azonnal válaszol a hívásra. Az alkalmazás ezután egy ideig alvó üzemmódban szimulálja az újraindítási folyamatot, mielőtt jelentett tulajdonság használatával értesíti a **trigger-reboot** háttéralkalmazást az újraindítás befejezéséről.

1. A **dm-get-started** mappában hozzon létre egy **simulated-device** nevű Maven-projektet a következő paranccsal a parancssorban:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg a **simulated-device** mappát.

3. Egy szövegszerkesztővel nyissa meg a **pom.xml** fájlt a **simulated-device** mappában, és adja hozzá a következő függőséget a **függőségek csomóponthoz.** Ez a függőség lehetővé teszi az iot-service-client csomag használatát az alkalmazásban az IoT Hubbal való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Adja hozzá a következő függőséget **a függőségek csomóponthoz.** Ez a függőség konfigurál egy NOP-t az Apache [SLF4J](https://www.slf4j.org/) naplózási rendszeréhez, amelyet az eszköz ügyféloldali SDK használ a naplózás megvalósításához. Ez a konfiguráció nem kötelező, de ha kihagyja, az alkalmazás futtatásakor figyelmeztetést láthat a konzolon. További információ az eszközoldali SDK-ban való naplózásról: [Naplózás](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) a Javához készült *Azure IoT eszközoldali SDK-hoz* készült információs fájl mintáiban.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Adja hozzá a következő **buildcsomópontot** a **függőségek csomópont** után. Ez a konfiguráció arra utasítja a Mavent, hogy a Java 1.8-at használja az alkalmazás felépítéséhez:

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

7. Egy szövegszerkesztővel nyissa meg a **simulated-device\src\main\java\com\mycompany\app\App.java** forrásfájlt.

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

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le a helyére az Új eszköz regisztrálása az `{yourdeviceconnectionstring}` [IoT Hubban szakaszban feljegyzett eszközkapcsolati sztringet:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. A közvetlen metódus állapotesemények visszahívási kezelőinek megvalósításához adja hozzá a következő beágyazott osztályt az **App osztályhoz:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Az ikereszköz állapotesemények visszahívási kezelőinek megvalósításához adja hozzá a következő beágyazott osztályt az **App osztályhoz:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. A tulajdonságesemények visszahívási kezelőinek megvalósításához adja hozzá a következő beágyazott osztályt az **App osztályhoz:**

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

13. Az eszköz újraindítását szimuláló szál implementálására adja hozzá a következő beágyazott osztályt az **App osztályhoz.** A szál öt másodpercig alvó üzemmódban van, majd beállítja a **lastReboot** jelentett tulajdonságot:

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

14. A közvetlen metódus az eszközön való megvalósításához adja hozzá a következő beágyazott osztályt az **App osztályhoz.** Amikor a szimulált alkalmazás hívást kap a **közvetlen újraindítási** metódushoz, nyugtát ad vissza a hívónak, majd elindít egy szálat az újraindítás feldolgozásához:

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

15. Módosítsa a fő metódus **aláírását** úgy, hogy az a következő kivételeket tartalmazza:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. A **DeviceClient példányosítania kell** a kódot a **main** metódusban a következő kódra:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. A közvetlen metódushívások figyelésének elkezdéshez adja hozzá a következő kódot a **fő metódushoz:**

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

18. Az eszközszimulátor kikapcsoléhez adja hozzá a következő kódot a **fő metódushoz:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

20. Készítse **el a simulated-device** alkalmazást, és javítsa ki a hibákat. A parancssorban keresse meg a **simulated-device** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **simulated-device** mappában egy parancssorban futtassa a következő parancsot az IoT Hub újraindítási metódushívásának figyelése megkezdéséhez:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub eszközalkalmazás a közvetlen újraindítási metódusok hívásának figyelására](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. A **trigger-reboot** mappában egy parancssorban futtassa a következő parancsot a szimulált eszköz újraindítási metódusának az IoT Hubról való hívásához:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatásalkalmazás a közvetlen újraindítási metódus hívására](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. A szimulált eszköz válaszol a közvetlen újraindítási metódus hívásra:

    ![A Java IoT Hub eszközalkalmazás válaszol a közvetlen metódus hívására](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
