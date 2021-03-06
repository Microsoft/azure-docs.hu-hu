---
title: Feladatok ütemezhetnek az Azure IoT Hub (Java) szolgáltatással | Microsoft Docs
description: Azure IoT Hub-feladatok ütemezésének beállítása közvetlen metódus meghívásához és a kívánt tulajdonság több eszközön való beállításához. A Javához készült Azure IoT eszközoldali SDK segítségével megvalósíthatja a szimulált eszközökön futó alkalmazásokat és a Javához készült Azure IoT Service SDK-t, hogy implementálja a feladatot.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 3e98cfc2d8c7fb8d40c8565a1c620f123ce171ff
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217839"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Feladatok ütemezett és szórása (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub használatával több millió eszközt frissítő feladatokat ütemezhet és követhet nyomon. Feladatok használata a következőhöz:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

A feladatok becsomagolják az egyik műveletet, és nyomon követik a végrehajtást egy adott eszközön. Az eszköz kettős lekérdezése meghatározza azon eszközök készletét, amelyeken a feladatot végrehajtja. Például egy háttérbeli alkalmazás felhasználhat egy közvetlen metódust az 10 000-es eszközökön, amelyek újraindítják az eszközöket. Megadhatja az eszköz dupla lekérdezéssel rendelkező eszközök készletét, és ütemezheti a feladatot, hogy a későbbiekben fusson. A feladatok nyomon követik az előrehaladást, mivel minden eszköz megkapja és végrehajtja a közvetlen újraindítási módszert.

Ha többet szeretne megtudni ezekről a képességekről, tekintse meg a következőt:

* Eszközök Twin és Properties: Ismerkedés [az eszközök ikrekkel](iot-hub-java-java-twin-getstarted.md)

* Közvetlen metódusok: [IoT hub fejlesztői útmutató – közvetlen](iot-hub-devguide-direct-methods.md) metódusok és [oktatóanyag: közvetlen metódusok használata](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy **lockDoor** nevű közvetlen metódust megvalósító eszközt. Az eszközbeállítások a háttérbeli alkalmazásból is fogadja a kívánt tulajdonságokat.

* Hozzon létre egy háttérbeli alkalmazást, amely létrehoz egy feladatot a **lockDoor** Direct metódus több eszközön való meghívásához. Egy másik feladatot a kívánt tulajdonságok frissítései több eszközre küldenek.

Az oktatóanyag végén egy Java-konzolos eszköz-alkalmazás és egy Java-konzol háttér-alkalmazás található:

**szimulált –** a IoT hubhoz csatlakozó eszköz megvalósítja a **lockDoor** Direct metódust, és kezeli a kívánt tulajdonságok változásait.

**Schedule – olyan feladatok** , amelyek a **lockDoor** Direct metódus meghívására, valamint az eszköz két kívánt tulajdonságának több eszközön történő meghívására használják a feladatokat.

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k cikke olyan Azure IoT SDK-kat tartalmaz, amelyek segítségével mind az eszközök, mind a háttérbeli alkalmazások készíthetők.

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Development Kit 8](/java/azure/jdk/). Győződjön meg arról, hogy a **Java 8** lehetőséget választja a **hosszú távú támogatás** alatt a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Az [Azure CLI-hez készült IoT bővítmény](https://github.com/Azure/azure-iot-cli-extension) használatával hozzáadhat egy eszközt az IoT hub-hoz.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy olyan Java-konzol alkalmazást hoz létre, amely a következő feladatokat használja:

* Hívja meg a **lockDoor** Direct metódust több eszközön.

* A kívánt tulajdonságok küldése több eszközre.

Az alkalmazás létrehozása:

1. A fejlesztői gépen hozzon létre egy **IOT-Java-Schedule-Jobs** nevű üres mappát.

2. A **IOT-Java-Schedule-Jobs** mappában hozzon létre egy **Schedule-Jobs** nevű Maven-projektet a következő parancs futtatásával a parancssorba. Látható, hogy ez egyetlen hosszú parancs:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. A parancssorban navigáljon az **ütemezett feladatok** mappára.

4. Egy szövegszerkesztővel nyissa meg a **pom.xml** fájlt az **Schedule-Jobs** mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ezzel a függőséggel használhatja az **IOT-Service-Client** csomagot az alkalmazásban az IOT hub használatával folytatott kommunikációhoz:

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

7. Egy szövegszerkesztővel nyissa meg a **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** fájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le a `{youriothubconnectionstring}` szót a IoT hub-beli, a korábban a [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)során bemásolt elérési karakterlánccá:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Adja hozzá a következő metódust az **app** osztályhoz, hogy ütemezzen egy feladatot, amely frissíti az eszköz dupla **kiépítési** és a **padló** kívánt tulajdonságait:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Ha a **lockDoor** metódus meghívására feladatot szeretne ütemezni, adja hozzá a következő metódust az **app** osztályhoz:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. A feladatok figyeléséhez adja hozzá a következő metódust az **app** osztályhoz:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. A futtatott feladatok részleteinek lekérdezéséhez adja hozzá a következő metódust:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Frissítse a **fő** metódus aláírását, hogy tartalmazza a következő `throws` záradékot:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Két feladat egymás utáni futtatásához és figyeléséhez cserélje le a **Main** metódus kódját a következő kódra:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. A **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** fájl mentése és lezárása

17. Hozza létre az **ütemterv – feladatok** alkalmazást, és javítsa ki az esetleges hibákat. A parancssorban navigáljon az **Schedule-Jobs** mappára, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban egy Java-konzol alkalmazást hoz létre, amely kezeli a IoT Hubból eljuttatott kívánt tulajdonságokat, és megvalósítja a közvetlen metódus hívását.

1. A **IOT-Java-Schedule-Jobs** mappában hozzon létre egy **szimulált eszköz** nevű Maven-projektet a következő parancs futtatásával a parancssorban. Látható, hogy ez egyetlen hosszú parancs:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. A parancssorban navigáljon a **szimulált eszköz** mappájához.

3. Egy szövegszerkesztővel nyissa meg a **szimulált eszköz** mappában található **pom.xml** fájlt, és adja hozzá a következő függőségeket a **függőségek** csomóponthoz. Ez a függőség lehetővé teszi, hogy a **IOT-Device-Client** csomagot használja az alkalmazásban az IOT hub-vel való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ez a függőség konfigurálja az Apache [SLF4J](https://www.slf4j.org/) naplózási homlokzatának NOP, amelyet az eszköz ügyféloldali SDK használ a naplózás megvalósításához. Ez a konfiguráció nem kötelező, de ha kihagyja, előfordulhat, hogy a konzolon megjelenik egy figyelmeztetés, amely az alkalmazás futtatásakor jelenik meg. Az eszköz ügyféloldali SDK-beli naplózásával kapcsolatos további információkért lásd: az *Azure IoT ESZKÖZOLDALI SDK for Java* információs fájlhoz tartozó minták [naplózása](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging).

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

7. Egy szövegszerkesztővel nyissa meg a **simulated-device\src\main\java\com\mycompany\app\App.Java** fájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le a szöveget a `{yourdeviceconnectionstring}` korábban az [új eszköz regisztrálása az IoT hub-ben](#register-a-new-device-in-the-iot-hub) részben átmásolt eszköz-csatlakoztatási sztringre:

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor.

10. Az eszköz kettős értesítéseinek a konzolra való nyomtatásához adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. A közvetlen metódus értesítéseinek a konzolra való nyomtatásához adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. A közvetlen metódusok IoT Hubból való kezeléséhez adja hozzá a következő beágyazott osztályt az **app** osztályhoz:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Frissítse a **fő** metódus aláírását, hogy tartalmazza a következő `throws` záradékot:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Cserélje le a **Main** metódus kódját a következő kódra:
    * Hozzon létre egy eszköz-ügyfelet a IoT Hubsal való kommunikációhoz.
    * Hozzon létre egy **eszköz** -objektumot az eszköz Twin tulajdonságainak tárolásához.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Az eszköz ügyféloldali szolgáltatásainak elindításához adja hozzá a következő kódot a **Main** metódushoz:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Ha meg szeretné várni, amíg a felhasználó lenyomja az **ENTER** billentyűt a leállítás előtt, adja hozzá a következő kódot a **Main** metódus végéhez:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Mentse és zárjuk be a **simulated-device\src\main\java\com\mycompany\app\App.Java** fájlt.

18. Hozza létre a **szimulált eszköz** alkalmazást, és javítsa ki az esetleges hibákat. A parancssorban navigáljon a **szimulált-Device** mappára, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzolon futó alkalmazások futtatására.

1. Futtassa a következő parancsot a **szimulált-Device** mappában egy parancssorban, hogy elindítsa a kívánt tulajdonságok változásait és a közvetlen metódusok hívásait figyelő eszköz alkalmazást:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Az eszköz ügyfele elindul](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. A mappában a parancssorban `schedule-jobs` futtassa a következő parancsot a **Schedule-Jobs** szolgáltatás alkalmazás futtatásához két feladat futtatásához. Az első beállítja a kívánt tulajdonságértékek értékét, a második pedig a közvetlen metódust hívja:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![A Java IoT Hub Service app két feladatot hoz létre](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Az alkalmazás kezeli a kívánt tulajdonság-változást és a közvetlen metódus hívását:

   ![Az eszköz ügyfele válaszol a változásokra](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan feladatot használt, amely egy közvetlen metódust ütemez egy eszközre, és az eszköz Twin tulajdonságainak frissítését.

Az alábbi források segítségével megismerheti a következőket:

* Telemetria küldése az eszközökről az első [lépések IoT hub](quickstart-send-telemetry-java.md) oktatóanyaggal.

* Az eszközök interaktív vezérlése (például egy felhasználó által vezérelt alkalmazás ventilátorának bekapcsolása) a [Direct Methods](quickstart-control-device-java.md) tutorial. s használatával