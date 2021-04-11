---
title: Azure IoT Device SDK-platform támogatása | Microsoft Docs
description: A nyílt forráskódú eszközök SDK-k a GitHubon, a C, a .NET (C#), a Java, a Node.js és a Python alkalmazásban érhetők el az eszközök Azure-beli IoT Hub és az eszközök kiépítési szolgáltatásához (DPS) való csatlakoztatásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: ab450c6ec6ae32cafcf07e88940736f209973d8d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168042"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT Device SDK-platform támogatása

A Microsoft igyekszik folyamatosan bővíteni az Azure IoT Hub-kompatibilis eszközök univerzumát. A Microsoft a GitHubon teszi közzé a nyílt forráskódú eszközök SDK-kat, hogy segítsen az eszközök csatlakoztatásában az Azure IoT Hub és a Device kiépítési szolgáltatáshoz. Az eszköz SDK-k a következőkhöz érhetők el: C, .NET (C#), Java, Node.js és Python. A Microsoft teszteli az egyes SDK-kat, hogy azok a támogatott konfigurációkon futnak a [Microsoft SDK-k és az eszközök platformjának támogatása](#microsoft-sdks-and-device-platform-support) szakaszban.

Az eszköz SDK-k mellett a Microsoft számos más utat is biztosít az ügyfelek és a fejlesztők számára az eszközök Azure IoT való csatlakoztatásához:

* A Microsoft számos partneri vállalattal együttműködve segíti a fejlesztési készletek közzétételét az Azure IoT C SDK alapján a hardveres platformokon.

* A Microsoft a Microsoft megbízható partnereivel együttműködve folyamatosan bővülő eszközöket biztosít az Azure-IoT tesztelt és tanúsított eszközökhöz. Az eszközök aktuális listájának megtekintéséhez tekintse meg az [Azure Certified for IoT-eszközök katalógusát](https://devicecatalog.azure.com/).

* A Microsoft egy platform absztrakciós réteget (PAL) biztosít az Azure IoT Hub Device C SDK-ban, amely segítséget nyújt a fejlesztőknek az SDK-nak a platformhoz való egyszerű hordozhatóságában. További információ: [C SDK-Porting útmutató](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Ez a témakör a Microsoft SDK-k és az általuk támogatott platform-konfigurációkról, valamint a fent felsorolt egyéb beállításokról nyújt információkat.

## <a name="microsoft-sdks-and-device-platform-support"></a>A Microsoft SDK-k és az eszközök platformjának támogatása

A Microsoft nyílt forráskódú SDK-kat tesz közzé a GitHubon a következő nyelveken: C, .NET (C#), Node.js, Java és Python. Ebben a szakaszban az SDK-k és azok függőségei szerepelnek. Az SDK-k bármely, a függőségeket kielégítő eszköz platformon támogatottak.

Az egyes felsorolt SDK-k esetében a Microsoft:

* Folyamatosan épít és futtat teljes körű teszteket a GitHubban található releváns SDK fő ágának számos népszerű platformon.  A különböző fordítóprogram-verziókon keresztüli tesztelés biztosításához általában a legújabb LTS-verziót és a legnépszerűbb verziót teszteljük.

* Telepítési útmutatót vagy telepítési csomagokat biztosít, ha vannak ilyenek.

* Teljes mértékben támogatja az SDK-kat a GitHubon, nyílt forráskódot, az ügyfelek hozzájárulásainak elérési útját és a termékek csapatának a GitHubtal kapcsolatos problémáit.

### <a name="c-sdk"></a>C SDK

Az [Azure IoT hub C ESZKÖZOLDALI SDK](https://github.com/Azure/azure-iot-sdk-c) -t teszteli, és a következő konfigurációkat támogatja.

| Operációs rendszer                  | TLS-könyvtár                  | További követelmények                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL vagy BearSSL | Berkeley-szoftvercsatornák</br></br>Hordozható operációs rendszer felülete (POSIX)                       |
| iOS 12,2            | OpenSSL                      | Az OSX-10.13.4 emulált XCode                                                               |
| Windows 10 termékcsalád   | SChannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [MXChip IoT fejlesztői csomag](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere operációs rendszer     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 vagy ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

Az [Azure IoT hub Python-eszköz SDK](https://github.com/Azure/azure-iot-sdk-python) -val tesztelve van, és a következő konfigurációkat támogatja.

| Operációs rendszer                  | Fordítóprogram                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,6 vagy újabb |
| macOS High Sierra   | Python 2,7. *, 3,6 vagy újabb |
| Windows 10 termékcsalád   | Python 2,7. *, 3,6 vagy újabb |

Csak a Python 3.5.3 vagy újabb verziója támogatja az aszinkron API-kat, javasoljuk, hogy a 3,7-es vagy újabb verziót használja.

### <a name="net-sdk"></a>.NET SDK

Az [Azure IoT hub .net (C#) eszköz SDK](https://github.com/Azure/azure-iot-sdk-csharp) -t tesztelték, és a következő konfigurációkat támogatja.

| Operációs rendszer                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 asztali és kiszolgálói SKU   | .NET Core 2,1, .NET-keretrendszer 4.5.1 vagy .NET-keretrendszer 4,7 |

A .NET SDK a Windows IoT Core-ban is használható az [Azure-eszköz ügynökével](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) vagy [olyan egyéni NTService, amely az RPC használatával képes kommunikálni a UWP-alkalmazásokkal](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

Az [Azure IoT Hub Node.js az eszköz SDK](https://github.com/Azure/azure-iot-sdk-node) -t teszteli, és a következő konfigurációkat támogatja.

| Operációs rendszer                  | Csomópont verziója    |
|---------------------|-----------------|
| Linux               | LTS és jelenlegi |
| Windows 10 termékcsalád   | LTS és jelenlegi |

### <a name="java-sdk"></a>Java SDK

Az [Azure IoT hub Java-eszköz SDK](https://github.com/Azure/azure-iot-sdk-java) -val tesztelve van, és a következő konfigurációkat támogatja.

| Operációs rendszer                     | Java-verzió |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 család x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Partner által támogatott fejlesztői készletek

A Microsoft számos különböző partnerrel dolgozik, hogy több mikroprocesszoros architektúrához biztosítson fejlesztői készleteket. Ezek a partnerek az Azure IoT C SDK-t a platformra portolták. A partnerek létrehozzák és karbantartják az SDK platform absztrakciós rétegét (PAL). A Microsoft együttműködik ezekkel a partnerekkel a kiterjesztett támogatás biztosításához.

| Partner             | Eszközök                            | Hivatkozás                     | Támogatás |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP – Azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT modem     | [Qualcomm LTE a IoT SDK-hoz](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST MICROELECTRONICS | STM32L4 sorozat <br/> STM32F4 sorozat <br/>  STM32F7 sorozat <br/>  A IoT-csomópont STM32L4-felderítési készlete    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO – AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Támogatás](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas-eszközök   | CC3220SF Kezdőpanel </br> CC3220S Kezdőpanel </br> CC3235SF Kezdőpanel </br> CC3235S Kezdőpanel </br> MSP432E4 Kezdőpanel | [A SimpleLink készült Azure IoT beépülő modul](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E fórum](https://e2e.ti.com) <br/> [TI E2E fórum CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E fórum MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>A Microsoft Azure IoT C SDK portolása

Ha az eszköz platformját az előző részek egyike sem fedi le, érdemes lehet az Azure IoT C SDK-t is átvenni. A C SDK portolása elsősorban az SDK platform absztrakciós rétegének (PAL) megvalósítását foglalja magában. A PAL olyan primitíveket határoz meg, amelyek az eszköz és a magasabb szintű függvények közötti ragasztót biztosítják az SDK-ban. További információ: [Porting útmutató](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft-partnerek és Certified Azure IoT-eszközök

A Microsoft számos partnerrel működik együtt az Azure IoT-univerzum folyamatos kibővítéséhez az Azure IoT tesztelt és minősített eszközökkel.

* Az Azure IoT Certified eszközök böngészéséhez tekintse meg a [Microsoft Azure Certified for IoT-eszköz katalógusát](https://devicecatalog.azure.com/).

* Ha többet szeretne megtudni a IoT-ökoszisztéma Azure Certified minősítéséről, tekintse meg [a Certified for IoT ökoszisztéma csatlakoztatása](../certification/overview.md)című témakört.

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Csatlakozás a IoT Hub SDK nélkül

Ha nem tudja használni az IoT Hub eszköz SDK-kat, csatlakozhat közvetlenül a IoT Hubhoz egy támogatott kommunikációs protokoll használatával. További információ: [fejlesztés Azure IOT SDK nélkül](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Támogatás és egyéb forrásanyagok

Ha az Azure IoT Device SDK-k használata során problémákba ütközik, többféleképpen is megkeresheti az alább összefoglalt támogatást. Az összes támogatási lehetőségről az [Azure IoT támogatási és súgójában](../iot-fundamentals/iot-support-help.md)talál további információt. 

**Hibajelentések** – az eszköz SDK-k hibáit a megfelelő GitHub-projekt problémák lapján lehet jelenteni. Gyorsan megtörténik a javítások a projektből a termékek frissítéseibe való beüzemelése.

* [Azure IoT Hub C SDK-problémák](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK-problémák](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT Hub Java SDK-problémák](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js SDK-problémák](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT Hub Python SDK-problémák](https://github.com/Azure/azure-iot-sdk-python/issues)

**Technikai kérdések** – technikai kérdéseket tehet fel mind a [Microsoft Q&a](/answers/topics/azure-iot-sdk.html) , mind a [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) az *Azure-IOT-SDK* címke használatával.

A **Microsoft ügyfélszolgálati csapata** – a [támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkező felhasználók a Microsoft ügyfélszolgálati csapatával új támogatási kérést hozhatnak létre közvetlenül a [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Szolgáltatásra vonatkozó kérelmek** – az Azure IoT szolgáltatásra vonatkozó kérelmeket a termék [felhasználói hangja oldalon](https://feedback.azure.com/forums/321918-azure-iot)követheti nyomon.

## <a name="next-steps"></a>Következő lépések

* [Eszköz és szolgáltatás SDK-k](iot-hub-devguide-sdks.md)
* [A Porting útmutatója](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
