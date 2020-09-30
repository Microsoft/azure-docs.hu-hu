---
title: Az Azure IoT-megoldáshoz (Java) csatlakoztatott IoT Plug and Play-eszköz használata | Microsoft Docs
description: A Java használatával csatlakozhat az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play-eszközhöz, és kommunikálhat velük.
author: ericmitt
ms.author: ericmitt
ms.date: 9/17/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: cd618cf5f2f82b9c87981e961ed401f3409ec9d4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583561"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-java"></a>Gyors útmutató: a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz (Java) használata

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

A IoT Plug and Play leegyszerűsíti a IoT azáltal, hogy az eszköz képességeinek ismerete nélkül teszi lehetővé az eszközök képességeit. Ez a rövid útmutató bemutatja, hogyan használható a Java a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A rövid útmutató Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* Java SE Development Kit 8. A [Java hosszú távú Azure-és Azure stack-támogatásában](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)a **hosszú távú támogatás**alatt válassza a **Java 8**lehetőséget.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Az SDK-tárház klónozása a mintakód használatával

Ha befejezte a gyors üzembe helyezést [: csatlakoztasson egy IoT Plug and Play Windows rendszerű eszközt IoT hub (Java)](quickstart-connect-device-java.md), már klónozotta a tárházat.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa a következő parancsot a Java GitHub-tárház [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) -k ezen a helyen történő klónozásához:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>A minta eszköz kiépítése és futtatása

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md)információit.

Ebben a rövid útmutatóban egy, a Java-ban IoT Plug and Play eszközként írt minta termosztát-eszközt használ. A minta eszköz futtatása:

1. Nyisson meg egy terminált, és navigáljon ahhoz a helyi mappához, amely a GitHubról klónozott Java-tárházhoz tartozó Microsoft Azure IoT SDK-t tartalmazza.

1. A rendszer ezt a terminált használja az **eszköz** -terminálként. Nyissa meg a klónozott tárház gyökérkönyvtárát. Telepítse az összes függőséget a következő parancs futtatásával:

1. Futtassa a következő parancsot a minta eszköz alkalmazás létrehozásához:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. A minta eszköz alkalmazás futtatásához navigáljon a *device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* mappára, és futtassa a következő parancsot:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a minta eszközt.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

A [környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok](set-up-environment.md) létrehozott két környezeti változót a minta konfigurálásához a IoT hub és az eszközhöz való kapcsolódáshoz:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **DEVICE_ID**: `"my-pnp-device"` .

Ebben a rövid útmutatóban egy Java-ban írt minta IoT-megoldást használ, amely az imént beállított minta eszközzel kommunikál.

> [!NOTE]
> Ez a példa a **com. microsoft. Azure. SDK. IOT. Service. *;** névteret használja a **IoT hub szolgáltatás ügyfelétől**. A modell AZONOSÍTÓjának beolvasásával kapcsolatos további tudnivalókért tekintse meg a [fejlesztői útmutató](concepts-developer-guide-device-csharp.md)című témakört.

1. Nyisson meg egy másik Terminálablak **szolgáltatást a szolgáltatás** -terminálként való használatra.

1. A klónozott Java SDK-tárházban navigáljon a *service\iot-Service-samples\pnp-Service-sample\thermostat-Service-Sample* mappára.

1. A minta szolgáltatásalkalmazás futtatásához futtassa a következő parancsot:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-digital-twin"></a>Digitális dupla Letöltés

A következő kódrészlet bemutatja, hogyan kérhető le a szolgáltatásban a Twin eszköz:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-digital-twin"></a>Digitális iker frissítése

A következő kódrészletből megtudhatja, hogyan használhatja a *javítást* a tulajdonságok frissítéséhez a digitális Twin használatával:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

Az eszköz kimenete azt mutatja, hogy az eszköz hogyan válaszol erre a tulajdonság-frissítésre.

### <a name="invoke-a-command"></a>Parancs meghívása

A következő kódrészletben egy parancs hívása látható az eszközön:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

Az eszköz kimenete azt mutatja, hogy az eszköz hogyan válaszol erre a parancsra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](concepts-developer-guide-device-csharp.md)
