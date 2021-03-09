---
title: Az Azure IoT Device SDK lehetőségeinek áttekintése
description: Megtudhatja, hogy melyik Azure IoT-eszköz SDK-t használja a fejlesztési szerepköre és feladatai alapján.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: fa10ddad8d2fbbc414f0ccaf16b9b99fae0b0786
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505035"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Az Azure IoT Device SDK-k áttekintése

Az Azure IoT Device SDK-k az eszközök ügyféloldali kódtárai, fejlesztői útmutatók, minták és dokumentációk. Az eszköz SDK-k segítenek az eszközök Azure IoT-szolgáltatásokhoz való programozott csatlakoztatásában.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Különböző Azure IoT SDK-kat bemutató ábra":::

Ahogy az ábrán is látható, az eszköz és a programozási nyelvi igényeknek megfelelően számos eszköz-SDK érhető el. Útmutató a megfelelő SDK kiválasztásához, [melyben az SDK-t használni szeretném](#which-sdk-should-i-use). Emellett az Azure IoT Service SDK-k is elérhetők a felhőalapú alkalmazás Azure IoT-szolgáltatásokkal való összekapcsolásához a háttérben. Ez a cikk az eszköz SDK-kat tárgyalja, de [itt](#service-sdks)többet is megtudhat az Azure Service SDK-ról.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Miért érdemes az Azure IoT Device SDK-kat használni?

Az eszközök Azure IoT való csatlakoztatásához létrehozhat egy egyéni kapcsolati réteget, vagy használhatja az Azure IoT Device SDK-kat. Az Azure IoT Device SDK-k használatának számos előnye van:

| Fejlesztési díj &nbsp; &nbsp; &nbsp;&nbsp; | Egyéni kapcsolatok rétege | Azure IoT-eszközök SDK-k |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Támogatás | Az Ön által felépített támogatás és dokumentálás szükséges | Hozzáférés a Microsoft támogatási szolgálatához (GitHub, Microsoft Q&A, Microsoft Docs, ügyfélszolgálati csapat) |
| Új funkciók | Új Azure-funkciók hozzáadására van szükség az egyéni middleware-hez | Azonnal kihasználhatja a Microsoft által a IoT SDK-k által folyamatosan hozzáadott új szolgáltatásokat |
| Befektetés | Hozzon létre több száz órányi beágyazott fejlesztést egyéni verzió tervezéséhez, létrehozásához, teszteléséhez és karbantartásához | Igénybe veheti az ingyenes, nyílt forráskódú eszközöket. Az SDK-k egyetlen díja a tanulási görbe. |

## <a name="which-sdk-should-i-use"></a>Melyik SDK-t használjam?

Az Azure IoT-eszközök SDK-k népszerű programozási nyelveken, például C, C#, Java, Node.js és Python nyelven érhetők el. Az SDK kiválasztásakor két elsődleges szempontot kell figyelembe venni: az eszköz képességeit, valamint a csapat ismereteit a programozási nyelvvel.

### <a name="device-capabilities"></a>Eszköz képességei

Az SDK kiválasztásakor figyelembe kell vennie az Ön által használt eszközök korlátait. Egy korlátozott eszköz az egyik, amely egyetlen Micro-Controller (MCU) és korlátozott memóriával rendelkezik. Ha korlátozott eszközt használ, javasoljuk, hogy használja a [beágyazott C SDK](#embedded-c-sdk)-t. Ez az SDK úgy lett kialakítva, hogy az Azure IoT való kapcsolódáshoz a lehető legkevesebb képességet biztosítsa. A beágyazott eszközhöz leginkább optimalizált összetevőket (MQTT-ügyfél, TLS és szoftvercsatorna-kódtárak) is kiválaszthatja. Ha a korlátozott eszköz az Azure RTOS is futtatja, akkor az Azure RTOS middleware használatával kapcsolódhat az Azure IoT-hez. Az Azure RTOS middleware a beágyazott C SDK-t további funkciókkal rendezi, így egyszerűbbé válik az Azure RTOS-eszköz felhőhöz csatlakoztatása.

A nem korlátozott eszköz olyan robusztus CPU-val rendelkezik, amely képes operációs rendszer futtatására olyan nyelvi futtatókörnyezet támogatásához, mint például a .NET vagy a Python. Ha nem korlátozott eszközt használ, a fő szempont a nyelv ismerete.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Csapatának ismerete a programozási nyelvvel

Az Azure IoT-eszközök SDK-k több nyelven is implementálva vannak, így kiválaszthatja a kívánt nyelvet. Az eszköz SDK-k más, jól ismert, nyelvspecifikus eszközökkel is integrálva vannak. Az ismerős fejlesztői nyelvekkel és eszközökkel való együttműködés lehetővé teszi a csapat számára a kutatás, a prototípusok, a termékfejlesztés és a folyamatos karbantartás fejlesztési ciklusának optimalizálását.

Ha lehetséges, válasszon ki egy, a fejlesztői csapat számára ismerős SDK-t. Minden Azure IoT SDK nyílt forráskódú, és több, a csapat számára elérhető mintát használ a kiértékeléséhez és teszteléséhez, mielőtt véglegesíti az adott SDK-t.

## <a name="how-can-i-get-started"></a>Hogyan kezdhetem meg a szolgáltatások használatát?

Az indulási hely az Azure-eszközök SDK-k GitHub-tárházának megismerése. Kipróbálhatja a gyors üzembe helyezési útmutatót is [, amely bemutatja, hogyan](quickstart-send-telemetry-python.md) lehet gyorsan használni az SDK-t telemetria küldéséhez az Azure IoT.

Az első lépésekhez szükséges lehetőségek attól függnek, hogy milyen típusú eszközre van szüksége:
- Korlátozott eszközök esetén használja a [beágyazott C SDK](#embedded-c-sdk)-t. 
- Az Azure RTOS-on futó eszközök esetében fejlesztheti az [Azure RTOS middleware](#azure-rtos-middleware)-t. 
- A nem korlátozott eszközök esetében [választhat egy SDK](#unconstrained-device-sdks) -t az Ön által választott nyelven. 

### <a name="constrained-device-sdks"></a>Korlátozott eszköz SDK-k
Ezek az SDK-k korlátozott számítási vagy memória-erőforrásokkal rendelkező eszközökön futnak. További információ a gyakori eszközök típusairól: [Az Azure IoT-eszközök típusának áttekintése](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Beágyazott C SDK
* [GitHub-adattár](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Példák](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referenciadokumentáció](https://azure.github.io/azure-sdk-for-c/)
* [A beágyazott C SDK létrehozása](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Korlátozott eszközök méretezési diagramja](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS middleware

* [GitHub-adattár](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Első lépések útmutatók](https://github.com/azure-rtos/getting-started) és [további minták](https://github.com/azure-rtos/samples)
* [Referenciadokumentáció](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Nem korlátozott eszköz SDK-k
Ezek az SDK-k bármely olyan eszközön futtathatók, amely támogatja a magasabb rendű nyelvi futtatókörnyezetet. Ide tartoznak az eszközök, például a számítógépek, a málna-és az okostelefonok. Ezek elsősorban a nyelv alapján különböztethetők meg, így bármelyik könyvtárat kiválaszthatja, amely legjobban megfelel a csapatának és forgatókönyvének.

#### <a name="c-device-sdk"></a>C eszköz SDK
* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-c)
* [Példák](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Csomagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referenciadokumentáció](/azure/iot-hub/iot-c-sdk-ref/)
* [Az Edge-modul dokumentációja](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [A C Device SDK fordítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [A C SDK portolása más platformokra](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Fejlesztői dokumentáció](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) a különböző platformokon való fordítással és a használat megkezdésével kapcsolatos információkért
* [Azure IoT Hub C SDK erőforrás-felhasználási információk](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C#-eszköz SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-csharp)
* [Példák](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Csomag](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referenciadokumentáció](/dotnet/api/microsoft.azure.devices)
* [Az Edge-modul dokumentációja](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java-eszköz SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-java)
* [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Csomag](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referenciadokumentáció](/java/api/com.microsoft.azure.sdk.iot.device)
* [Az Edge-modul dokumentációja](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js eszköz SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-node)
* [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Csomag](https://www.npmjs.com/package/azure-iot-device)
* [Referenciadokumentáció](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest&preserve-view=true)
* [Az Edge-modul dokumentációja](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python-eszköz SDK

* [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-python)
* [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Csomag](https://pypi.org/project/azure-iot-device/)
* [Referenciadokumentáció](/python/api/azure-iot-device)
* [Az Edge-modul dokumentációja](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Szolgáltatási SDK-k
Az Azure IoT olyan szolgáltatás-SDK-kat is kínál, amelyek lehetővé teszik a megoldási alkalmazások felügyeletét az eszközök kezeléséhez, az adatelemzéshez és az adatmegjelenítéshez. Ezek az SDK-k az egyes Azure IoT-szolgáltatásokra jellemzőek, és C#, Java, JavaScript és Python nyelven érhetők el, és egyszerűbbé teszik a fejlesztési élményt. 

#### <a name="iot-hub"></a>IoT Hub

Az IoT Hub Service SDK-k lehetővé teszik olyan alkalmazások összeállítását, amelyek könnyen kommunikálhatnak a IoT Hubekkel az eszközök és a biztonság kezeléséhez. Ezeket az SDK-kat használhatja a felhőből az eszközre irányuló üzenetek küldéséhez, közvetlen metódusok meghívásához az eszközökön, az eszköz tulajdonságainak frissítését és egyebeket.

[**További információ a IoT hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Egy eszköz vezérlésének kipróbálása**](../iot-hub/quickstart-control-device-python.md)

**C# IoT hub Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [csomag](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [minták](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [dokumentációja](/dotnet/api/microsoft.azure.devices)

**Java IoT hub Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [csomag](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [dokumentációja](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT hub Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [csomag](https://www.npmjs.com/package/azure-iothub)  |  [minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [dokumentációja](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest&preserve-view=true)

**Python IoT hub Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [csomag](https://pypi.python.org/pypi/azure-iot-hub/)  |  [minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [dokumentációja](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Az Azure Digital Twins egy szolgáltatásként nyújtott platform, amely lehetővé teszi, hogy a teljes környezetek digitális modelljein alapuló ismereti diagramokat hozzanak létre. Ezek a környezetek lehetnek olyan épületek, gyárak, gazdaságok, energiahálózatok, vasutak, stadionok és sok más – akár a teljes város is. Ezek a digitális modellek olyan elemzések megszerzésére használhatók, amelyek jobb termékeket, optimalizált műveleteket, csökkentett költségeket és áttörést jelentő felhasználói élményt nyújtanak. Az Azure IoT szolgáltatás SDK-kat kínál, amelyek megkönnyítik az Azure digitális ikrek hatékonyságát használó alkalmazások készítését.

[**További információ az Azure digitális Twins**](https://azure.microsoft.com/services/digital-twins/)  |  szolgáltatásról [ **ADT-alkalmazás kódja**](../digital-twins/tutorial-code.md)

**C# ADT Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [csomag](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [dokumentációja](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [csomag](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [dokumentációja](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [csomag](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [dokumentációja](/javascript/api/@azure/digital-twins-core/)

**Python ADT Service SDK**: [GitHub-adattár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [csomag](https://pypi.org/project/azure-digitaltwins-core/)  |  [minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [dokumentációja](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

A IoT Hub Device Provisioning Service (DPS) egy olyan IoT Hub segítő szolgáltatása, amely lehetővé teszi, hogy az emberi beavatkozás nélkül, a megfelelő IoT hub-ra való leválasztást, valamint a jobb oldali üzembe helyezést. A DPS lehetővé teszi több millió eszköz üzembe helyezését biztonságos és skálázható módon. A DPS szolgáltatás SDK-k lehetővé teszik olyan alkalmazások létrehozását, amelyek a regisztrációs csoportok létrehozásával és tömeges műveletek végrehajtásával biztonságosan kezelhetik az eszközeiket.

[**További információ az eszköz kiépítési szolgáltatásáról**](../iot-dps/index.yml)  |  [ **Próbáljon meg létrehozni egy csoportos regisztrációt X. 509-eszközökhöz**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Device kiépítési szolgáltatás SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [csomag](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [minták](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [dokumentációja](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device kiépítési szolgáltatás SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [csomag](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [dokumentációja](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device kiépítési szolgáltatás SDK**: [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [csomag](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [dokumentációja](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: eszköz csatlakoztatása IoT Centralhoz (Python)](quickstart-send-telemetry-python.md)
* [Gyors útmutató: eszköz csatlakoztatása IoT Hubhoz (Python)](quickstart-send-telemetry-cli-python.md)
* [Ismerkedés a beágyazott fejlesztéssel](quickstart-device-development.md)
* További információ az [Azure IoT SDK-k használatával történő fejlesztés előnyeiről](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)