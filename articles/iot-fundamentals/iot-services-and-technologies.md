---
title: Azure eszközök internetes hálózata (IoT) technológiák és megoldások
description: Ismerteti az Azure IoT-megoldás létrehozásához használható technológiák és szolgáltatások gyűjteményét.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0cd98705d2d6947ff766f528e4e92f1e51bcc42d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852812"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>Milyen Azure-technológiákat és-szolgáltatásokat használhat a IoT-megoldások létrehozásához?

Az Azure IoT-technológiák és-szolgáltatások számos olyan IoT-megoldás létrehozásához nyújtanak lehetőséget, amelyek lehetővé teszik a szervezet digitális átalakítását. Megteheti például a következőt:

* A biztonságos, nagyvállalati szintű IoT-megoldás létrehozásához és üzembe helyezéséhez használja az [Azure IoT Central](https://apps.azureiotcentral.com), egy felügyelt IoT-alkalmazás platformját. A IoT Central a megoldás fejlesztési folyamatának felgyorsításához az iparági specifikus alkalmazás-sablonok, például a kiskereskedelmi és az egészségügyi szolgáltatások gyűjteményét.
* Az Azure IoT platform szolgáltatásai (például az [azure IoT hub](../iot-hub/about-iot-hub.md) és az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) -k) segítségével teljesen új, egyéni IoT-megoldások hozhatók létre.

![Azure IoT-technológiák,-szolgáltatások és-megoldások](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

A [IoT Central alkalmazás platform](https://apps.azureiotcentral.com) csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésével, kezelésével és karbantartásával járó terheket és költségeket. A IoT Central testreszabható webes felhasználói felületének segítségével figyelheti az eszközök feltételeit, szabályokat hozhat létre, és több millió eszközt és adatmennyiséget kezelhet életciklusa során. Az IoT Central API felülete programozási hozzáférést biztosít a IoT-megoldás konfigurálásához és használatához.

Az Azure IoT Central egy teljes körűen felügyelt alkalmazás-platform, amely egyéni IoT-megoldások létrehozására használható. A IoT Central alkalmazás-sablonok használatával hozza létre a megoldásokat. Vannak sablonok általános megoldásokhoz és adott iparágakhoz, mint például az energia, az egészségügy, a kormányzat és a kereskedelmi. IoT Central alkalmazás-sablonok segítségével percek alatt üzembe helyezhet egy IoT Central alkalmazást, amelyet aztán testreszabhat a témákkal, az irányítópultokkal és a nézetekkel.

A megoldáshoz való gyors kapcsolódáshoz válassza az [Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com) eszközeit. Az IoT Central webes felhasználói felülettel figyelheti és kezelheti az eszközöket, hogy azok egészségesek maradjanak és csatlakoztatva legyenek. Összekötők és API-k használatával integrálhatja IoT Central alkalmazását más üzleti alkalmazásokkal.

Teljes körűen felügyelt alkalmazási platformként IoT Central egy egyszerű, kiszámítható díjszabási modellel rendelkezik.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-megoldásgyorsítók

Az [Azure IoT megoldás-gyorsítók](https://www.azureiotsolutions.com) testreszabható nagyvállalati szintű megoldások gyűjteményei. Ezeket a megoldásokat üzembe helyezheti, vagy létrehozhat egy egyéni IoT-megoldást a nyílt forráskódú Java-vagy .NET-forráskód használatával.

Az Azure IoT megoldás-gyorsítók magas szintű irányítást biztosítanak a IoT-megoldáshoz. A megoldás-gyorssegédek előre összeállított megoldásokat tartalmaznak olyan általános IoT-forgatókönyvekhez, amelyeket percek alatt üzembe helyezhet az Azure-előfizetésében. A forgatókönyvek a következők:

* Csatlakoztatott gyár
* Eszközszimuláció

Az összes megoldás-gyorssegédhez elérhető nyílt forráskódú kód a GitHubon érhető el. Töltse le a kódot egy megoldás-gyorsító testreszabásához, hogy megfeleljen az adott IoT követelményeinek.

A megoldás-gyorsítók olyan Azure-szolgáltatásokat használnak, mint például az Azure IoT Hub és az Azure Storage, amelyet az Azure-előfizetésében kell kezelnie.

## <a name="custom-solutions"></a>Egyéni megoldások

Ha teljesen új IoT-megoldást szeretne létrehozni, vagy IoT Central vagy megoldás-gyorsító használatával létrehozott megoldást kíván kibővíteni, használja a következő Azure IoT-technológiák és-szolgáltatások közül egyet vagy többet:

### <a name="devices"></a>Eszközök

Fejlessze IoT-eszközeit az [Azure IoT Starter-készletek](https://catalog.azureiotsolutions.com/kits) egyikével, vagy válasszon ki egy eszközt, [amelyet az Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com)használatával használhat. A beágyazott kód implementálása a nyílt forráskódú [eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md)-k használatával. Az eszköz SDK-k több operációs rendszert támogatnak, mint például a Linux, a Windows és a valós idejű operációs rendszerek. Több programozási nyelvhez is vannak SDK-k, például a [C](https://github.com/Azure/azure-iot-sdk-c), a [Node.js](https://github.com/Azure/azure-iot-sdk-node), a [Java](https://github.com/Azure/azure-iot-sdk-java), a [.net](https://github.com/Azure/azure-iot-sdk-csharp)és a [Python](https://github.com/Azure/azure-iot-sdk-python).

A [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) szolgáltatás használatával tovább egyszerűsítheti az eszközök beágyazott kódjának létrehozását. A IoT Plug and Play lehetővé teszi, hogy a megoldások fejlesztői bármilyen beágyazott kód írása nélkül integrálják az eszközöket a megoldásaikkal. A IoT-Plug and Play középpontjában az eszköz képességeinek leírására szolgáló _eszköz-képességi modell_ séma található. Az eszköz képességeinek modellje segítségével létrehozza a beágyazott eszköz kódját, és konfigurálhat egy felhőalapú megoldást, például egy IoT Central alkalmazást.

[Azure IoT Edge](../iot-edge/about-iot-edge.md) lehetővé teszi, hogy a IoT számítási feladatainak részeit az Azure Cloud servicesből az eszközeire kiossza. A IoT Edge csökkentheti a megoldás késését, csökkentheti a felhőben tárolt adatok mennyiségét, és lehetővé teheti az off-line forgatókönyvek kikapcsolását. IoT Edge-eszközöket kezelheti IoT Central és néhány megoldási gyorssegéd használatával.

A [Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) egy biztonságos, magas szintű alkalmazás-platform, amely beépített kommunikációs és biztonsági funkciókat biztosít az internethez csatlakoztatott eszközökhöz. Tartalmaz egy biztonságos mikrovezérlő-egységet, egy egyéni Linux-alapú operációs rendszert és egy felhőalapú biztonsági szolgáltatást, amely folyamatos, megújítható biztonságot nyújt.

### <a name="cloud-connectivity"></a>Felhőalapú kapcsolat

Az [Azure IoT hub](../iot-hub/about-iot-hub.md) szolgáltatás megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz és egy felhőalapú megoldás között. Az [Azure IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md) a IoT hub segítő szolgáltatása. A szolgáltatás emberi beavatkozás nélkül lehetővé teszi, hogy az eszközök a megfelelő IoT hub-ra való, igény szerinti üzembe helyezését biztosítsák. Ezek a képességek lehetővé teszik, hogy az ügyfelek több millió eszközt telepítsenek biztonságos és skálázható módon.

IoT Hub a megoldás-gyorssegédek alapvető összetevője, és az IoT-megvalósítási kihívásoknak is eleget tesz, például:

* Nagy mennyiségű eszköz csatlakoztatása és felügyelete.
* Nagy mennyiségű telemetriaadat feldolgozása.
* Eszközök irányítása és vezérlése.
* Eszközbiztonság kikényszerítése.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>A fizikai és a digitális világok közötti szakadék áthidalása

Az [Azure Digital Twins](../digital-twins/overview.md) egy IoT szolgáltatás, amely lehetővé teszi a fizikai környezet modellezését. A térbeli intelligencia gráf használatával modellezi a személyek, a szóközök és az eszközök közötti kapcsolatokat. A digitális és a fizikai világok egymással kapcsolatos adatkezelési megoldásaival kontextussal is kompatibilis megoldásokat hozhat létre.

A IOT Central digitális ikrek használatával szinkronizálja az eszközöket és az adatokat a valós világban a digitális modellekkel, amelyek lehetővé teszik a felhasználók számára a csatlakoztatott eszközök figyelését és felügyeletét.

### <a name="data-and-analytics"></a>Adatok és analitika

A IoT-eszközök általában nagy mennyiségű idősoros adatok előállítására szolgálnak, mint például az érzékelőkből származó hőmérséklet-beolvasások. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) tud csatlakozni egy IoT hubhoz, olvassa el a telemetria streamet az eszközökről, tárolja azokat, és lehetővé teszi a lekérdezését és megjelenítését.

A [Azure Maps](../azure-maps/index.yml) térinformatikai szolgáltatások gyűjteménye, amely friss leképezési adatait használja a webes és mobil alkalmazások pontos földrajzi kontextusának biztosításához. Az alkalmazások létrehozásához REST API, webalapú JavaScript-vezérlőt vagy Android SDK-t használhat.

## <a name="next-steps"></a>Következő lépések

Gyakorlati tapasztalatért próbálja ki az egyik rövid útmutatót:

- [Azure IoT Central-alkalmazás létrehozása](../iot-central/core/quick-deploy-iot-central.md)
- [Telemetria küldése egy eszközről egy IoT-hubhoz](../iot-hub/quickstart-send-telemetry-cli.md)
