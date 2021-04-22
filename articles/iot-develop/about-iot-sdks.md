---
title: Az Azure IoT eszközoldali SDK-lehetőségeinek áttekintése
description: Megtudhatja, hogy melyik Azure IoT eszközoldali SDK-t használja a fejlesztési szerepköre és feladatai alapján.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876456"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Az Azure IoT eszköz-SDK-k áttekintése

Az Azure IoT eszközoldali SZOFTVERFEJLESZTŐI eszközoldali kódtárak, fejlesztői útmutatók, minták és dokumentációk készlete. Az eszköz-SDK-k segítségével programozott módon csatlakoztathatja az eszközöket az Azure IoT-szolgáltatásokhoz.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Különböző Azure IoT-SDK-kat bemutató ábra":::

Ahogy az ábrán látható, számos eszköz-SDK áll rendelkezésre az eszköz és a programozási nyelv igényeinek megfelelően. Útmutató a megfelelő eszközOLDALI SDK kiválasztásához: [Melyik SDK-t használjam?](#which-sdk-should-i-use) A felhőalapú alkalmazás és a háttérbeli Azure IoT-szolgáltatások csatlakoztatásához Azure IoT-szolgáltatási SDK-k is elérhetők. Ez a cikk az eszköz-SDK-okkal foglalkozik, az Azure-szolgáltatásokHOZ való ADK-król itt talál [további információt.](#service-sdks)

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Miért érdemes az Azure IoT eszköz-ADATTK-okat használni?

Az eszközök Azure IoT-hez való csatlakoztatásához létrehozhat egy egyéni kapcsolati réteget, vagy használhatja az Azure IoT eszköz-ADATTK-okat. Az Azure IoT eszköz-ADATTK-k használatának számos előnye van:

| Fejlesztési költségek &nbsp; &nbsp; &nbsp;&nbsp; | Egyéni kapcsolati réteg | Azure IoT eszköz-ADATTK-k |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Támogatás | Támogatnia és dokumentálni kell minden buildet | Hozzáférése van a Microsoft ügyfélszolgálatához (GitHub, Microsoft Q&A, Microsoft Docs ügyfélszolgálati csapatok) |
| Új funkciók | Új Azure-funkciókat kell hozzáadnia az egyéni middleware-hez | Azonnal kihasználhatja a Microsoft által az IoT-SDK-khoz folyamatosan elérhető új funkciók előnyeit |
| Befektetés | Több száz órányi beágyazott fejlesztést fektethet egy egyéni verzió tervezésébe, létrehozásába, tesztelésébe és karbantartásába | Kihasználhatja az ingyenes, nyílt forráskódú eszközök előnyeit. Az SDK-khoz kapcsolódó egyetlen költség a tanulási görbe. |

## <a name="which-sdk-should-i-use"></a>Melyik SDK-t használjam?

Az Azure IoT eszköz-SDK-k olyan népszerű programozási nyelveken érhetők el, mint a C, a C#, a Java, a Node.js és a Python. Az SDK kiválasztásakor két fő szempontot kell figyelembe venni: az eszközképességeket, valamint a csapat programozási nyelvét.

### <a name="device-capabilities"></a>Eszközképességek

Amikor SDK-t választ, figyelembe kell vennie a használt eszközök korlátait. A korlátozott eszköz egyetlen mikrovezérlővel (MCU) és korlátozott memóriával rendelkezik. Ha korlátozott eszközt használ, javasoljuk, hogy használja az [Embedded C SDK-t.](#embedded-c-sdk) Ez az SDK az Azure IoT-hez való csatlakozáshoz szükséges képességek minimális készletét biztosítja. Kiválaszthatja a beágyazott eszközhöz leginkább optimalizált összetevőket (MQTT-ügyfél, TLS és szoftvercsatorna-kódtárak). Ha a korlátozott eszköz Azure RTOS is fut, a Azure RTOS segítségével csatlakozhat az Azure IoT-hez. A Azure RTOS szoftver további funkciókkal burkossa az Embedded C SDK-t, hogy leegyszerűsítse Azure RTOS eszköz csatlakoztatását a felhőhöz.

A nem korlátozott eszköz robusztusabb processzorral rendelkezik, amely képes operációs rendszert futtatni egy olyan nyelvi futtatókörnyezet támogatásához, mint a .NET vagy a Python. Ha nem korlátozott eszközt használ, a legfontosabb szempont a nyelv ismerete.

### <a name="your-teams-familiarity-with-the-programming-language"></a>A csapat jártas a programozási nyelvben

Az Azure IoT eszköz-SDK-k több nyelven vannak megvalósítva, így kiválaszthatja az Önnek megfelelő nyelvet. Az eszköz-SDK-k más ismert, nyelvspecifikus eszközökkel is integrálhatók. A jól ismert fejlesztési nyelvvel és eszközökkel való munka lehetővé teszi, hogy a csapat optimalizálja a kutatási ciklust, a prototípus-olást, a termékfejlesztést és a folyamatos karbantartást.

Amikor csak lehetséges, olyan SDK-t válasszon, amely ismerősnek tűnik a fejlesztői csapat számára. Minden Azure IoT SDK nyílt forráskódú, és számos mintával rendelkezik, amelyek kiértékelése és tesztelése egy adott SDK-ra való véglegesítés előtt lehetséges.

## <a name="how-can-i-get-started"></a>Hogyan kezdhetem meg a szolgáltatások használatát?

Kezdésként ismerkedhet meg az Azure Device SDK-k GitHub-adattáraival. Kipróbálhat egy rövid [](quickstart-send-telemetry-python.md) útmutatót is, amely bemutatja, hogyan küldhet gyorsan telemetriai adatokat az Azure IoT-nek egy SDK használatával.

Az első lépésekhez rendelkezésre álló lehetőségek attól függnek, hogy milyen eszközzel is van:
- Korlátozott eszközök esetén használja az [Embedded C SDK-t.](#embedded-c-sdk) 
- A -on futó Azure RTOS a fejlesztést a [Azure RTOS szoftver segítségével.](#azure-rtos-middleware) 
- A nem korlátozott eszközökhöz kiválaszthatja az [SDK-t](#unconstrained-device-sdks) egy ön által választott nyelven. 

### <a name="constrained-device-sdks"></a>Korlátozott eszköz-SDK-k
Ezek az SDK-k korlátozott számítási vagy memória-erőforrásokkal futó eszközökön való futtatásra vannak specializáltak. A gyakori eszköztípusokkal kapcsolatos további információkért lásd: [Az Azure IoT-eszköztípusok áttekintése.](concepts-iot-device-types.md)

#### <a name="embedded-c-sdk"></a>Beágyazott C SDK
* [GitHub-adattár](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Példák](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referenciadokumentáció](https://azure.github.io/azure-sdk-for-c/)
* [Az Embedded C SDK buildjének összeállítása](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Méretdiagram korlátozott eszközökhöz](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS Middleware

* [GitHub-adattár](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Első lépések útmutatók](https://github.com/azure-rtos/getting-started) és [további minták](https://github.com/azure-rtos/samples)
* [Referenciadokumentáció](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Nem korlátozott eszköz-SDK-k
Ezek az SDK-k bármely olyan eszközön futtathatók, amely támogatja a magasabb rendű nyelvi futtatásokat. Ide tartoznak például a számítógépek, a Raspberry Piszk és az okostelefonok. Elsősorban nyelv alapján vannak megkülönböztetve, így kiválaszthatja azt a kódtárat, amely a legjobban megfelel a csapatának és a forgatókönyvének.

#### <a name="c-device-sdk"></a>C eszközoldali SDK
* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-c)
* [Példák](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Csomagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referenciadokumentáció](/azure/iot-hub/iot-c-sdk-ref/)
* [Edge-modulok referenciadokumentációja](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [A C eszközoldali SDK fordítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [A C SDK portolása más platformokra](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Fejlesztői dokumentáció](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) a különböző platformokon való fordításról és az első lépésekről
* [Azure IoT Hub C SDK erőforrás-felhasználási információi](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C# eszközoldali SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-csharp)
* [Példák](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Csomag](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referenciadokumentáció](/dotnet/api/microsoft.azure.devices)
* [Edge-modulok referenciadokumentációja](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java eszközoldali SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-java)
* [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Csomag](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referenciadokumentáció](/java/api/com.microsoft.azure.sdk.iot.device)
* [Edge-modulok referenciadokumentációja](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js EszközOLDALI SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-node)
* [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Csomag](https://www.npmjs.com/package/azure-iot-device)
* [Referenciadokumentáció](/javascript/api/azure-iot-device/)
* [Edge-modulok referenciadokumentációja](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python eszközoldali SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-python)
* [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Csomag](https://pypi.org/project/azure-iot-device/)
* [Referenciadokumentáció](/python/api/azure-iot-device)
* [Az Edge-modulok referenciadokumentációja](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Szolgáltatási SDK-k
Az Azure IoT olyan szolgáltatásoldali ADATTK-okat is kínál, amelyek lehetővé teszik megoldásoldali alkalmazások felépítését az eszközök kezeléséhez, az elemzésekhez, az adatok megjelenítéséhez stb. Ezek az EGYES Azure IoT-szolgáltatásokhoz használhatóK, és C#, Java, JavaScript és Python nyelven érhetők el a fejlesztési élmény egyszerűsítése érdekében. 

#### <a name="iot-hub"></a>IoT Hub

A IoT Hub szolgáltatás-SDK-k segítségével olyan alkalmazásokat építhet, amelyek könnyedén kommunikálnak a IoT Hub az eszközök és a biztonság kezeléséhez. Ezekkel az SDK-okkal felhőből az eszközre üzeneteket küldhet, közvetlen metódusokat hívhat meg az eszközökön, frissítheti az eszköztulajdonságokat stb.

[**További információ a IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Eszköz vezérlésének próbálkozása**](../iot-hub/quickstart-control-device-python.md)

C# IoT Hub Service SDK: [GitHub Repository](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)Package Samples Reference Documentation Documentation **(A GitHub-adattár**  |  [csomagminták referenciadokumentációja)](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub Service SDK:** [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [csomagminták](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)referenciadokumentációja  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub Service SDK:** [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [csomagminták](https://www.npmjs.com/package/azure-iothub)referenciadokumentációja  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [](/javascript/api/azure-iothub/)

**Python IoT Hub Service SDK:** [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [csomagminták](https://pypi.python.org/pypi/azure-iot-hub/)referenciadokumentációja  |  [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins egy szolgáltatásként szolgáló platform (PaaS) ajánlat, amely a teljes környezetek digitális modelljei alapján teszi lehetővé tudásgráfok létrehozását. Ezek a környezetek például épületek, gyárak, farmok, energiahálózatok, házak, házak stb., sőt akár teljes városok is. Ezekkel a digitális modellekkel jobb termékeket, optimalizált műveleteket, alacsonyabb költségeket és áttörést jelentő ügyfélélményeket hozhatnak. Az Azure IoT szolgáltatás-ADATTK-okat kínál, amelyek megkönnyítik az olyan alkalmazások felépítését, amelyek a Azure Digital Twins.

[**További információ a Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/)  |  [ **ADT-alkalmazás kódja**](../digital-twins/tutorial-code.md)

**C# ADT Service SDK:** [GitHub Repository](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)Package Samples Reference Documentation (A GitHub-adattár  |  [csomagminták](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [referenciadokumentációja)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT Service SDK:** [GitHub-adattárcsomagminták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)referenciadokumentációja  |  [](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT Service SDK:** [GitHub-adattárcsomag-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [referenciadokumentációja](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/)

**Python ADT Service SDK:** [GitHub-adattárcsomagminták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [referenciadokumentációja](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

A IoT Hub Device Provisioning Service (DPS) a IoT Hub segítő szolgáltatása, amely emberi beavatkozás nélkül teszi lehetővé a megfelelő IoT Hubon történő érintés nélküli, igény szerinti üzembe építést. A DPS eszközök millióinak biztonságos és skálázható módon való üzembe beállítását teszi lehetővé. A DPS szolgáltatási SDK-k segítségével olyan alkalmazásokat építhet, amelyek biztonságosan felügyelik az eszközöket regisztrációs csoportok létrehozásával és tömeges műveletekkel.

[**További információ a Device Provisioning Service-ről**](../iot-dps/index.yml)  |  [ **Próbáljon meg csoportos regisztrációt létrehozni X.509-eszközökhöz**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Device Provisioning Service SDK:** GitHub Repository Package Samples Reference Documentation [(A GitHub-adattár](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [csomagminták](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [referenciadokumentációja)](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device Provisioning Service SDK:** [GitHub-adattárcsomag-minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [referenciadokumentációja](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device Provisioning Service SDK:** [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [csomagminták](https://www.npmjs.com/package/azure-iot-provisioning-service)referenciadokumentációja  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Eszköz csatlakoztatása IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Rövid útmutató: Eszköz csatlakoztatása IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [A beágyazott fejlesztés első lépések](quickstart-device-development.md)
* További információ az [Azure IoT SDK-k](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) használatával való fejlesztés előnyeiről