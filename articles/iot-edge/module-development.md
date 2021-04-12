---
title: Modulok fejlesztése a Azure IoT Edgehoz | Microsoft Docs
description: Egyéni modulok fejlesztése olyan Azure IoT Edgeekhez, amelyek kommunikálhatnak a futtatókörnyezettel és IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6274d0f0265220877938775ab6efd72ff22b84e4
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031125"
---
# <a name="develop-your-own-iot-edge-modules"></a>Saját IoT Edge-modulok fejlesztése

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge modulok kapcsolódhatnak más Azure-szolgáltatásokhoz, és hozzájárulnak a nagyobb Felhőbeli adatfolyamathoz. Ez a cikk azt ismerteti, hogyan fejleszthet modulokat a IoT Edge futtatókörnyezettel és IoT Hubával, így az Azure-felhő többi részével folytatott kommunikációhoz.

## <a name="iot-edge-runtime-environment"></a>IoT Edge futásidejű környezet

A IoT Edge Runtime biztosítja az infrastruktúrát, amely több IoT Edge modul funkcióinak integrálására és a IoT Edge eszközökre történő telepítésére szolgál. Bármely program IoT Edge modulként is becsomagolható. Az IoT Edge kommunikációs és kezelési funkciók teljes kihasználása érdekében egy modulban futó program az Azure IoT-eszköz SDK-val csatlakozhat a helyi IoT Edge hubhoz.
::: moniker range=">=iotedge-2020-11"
A modulok bármilyen MQTT-ügyfelet is használhatnak a helyi IoT Edge hub MQTT-átvitelszervezőhöz való kapcsolódáshoz.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>A program IoT Edge-modulként való csomagolása

A program IoT Edge eszközön való üzembe helyezéséhez először tárolónak kell lennie, és egy Docker-kompatibilis motorral kell futnia. A IoT Edge a Docker mögött lévő nyílt forráskódú [projektet használja a](https://github.com/moby/moby)Docker-kompatibilis motorként. Ugyanazokat a paramétereket kell átadni a IoT Edge moduljainak, amelyeket a Docker használatával használ. További információ: a [tároló létrehozási beállításainak konfigurálása IoT Edge modulokhoz](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Az IoT Edge hub használata

Az IoT Edge hub két fő funkciót biztosít: proxy IoT Hub és helyi kommunikációhoz.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Csatlakozás IoT Edge hubhoz egy modulból

Ha egy modul helyi IoT Edge hubhoz csatlakozik, ugyanazokat a csatlakozási lépéseket kell megtennie, mint az ügyfelek esetében. További információ: [Csatlakozás az IoT Edge hubhoz](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Ha IoT Edge útválasztást AMQP vagy MQTT keresztül szeretné használni, a ModuleClient az Azure IoT SDK-ból is használhatja. Hozzon létre egy ModuleClient-példányt, amely az eszközön futó IoT Edge hubhoz csatlakoztatja a modult, hasonlóan ahhoz, ahogy a DeviceClient-példányok IoT-eszközök csatlakoztatását IoT Hubhoz. A ModuleClient osztályról és a hozzá tartozó kommunikációs módszerekről további információt az előnyben részesített SDK-nyelv API-referenciája tartalmaz: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)vagy [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

A IoT Edge MQTT Broker használatához saját MQTT-ügyfelet kell használnia, és a IoT Edge Daemon számítási API-ból lekért információkkal együtt kell kezdeményezni a kapcsolódást. <!--Need to add details here-->

További információ az Útválasztás és a közzététel/előfizetés MQTT-közvetítővel való kiválasztásáról: [helyi kommunikáció](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>MQTT-közvetítő primitívek

#### <a name="send-a-message-on-a-user-defined-topic"></a>Üzenet küldése felhasználó által definiált témakörben

A IoT Edge MQTT-közvetítővel bármely felhasználó által definiált témában közzétehet üzeneteket. Ehhez engedélyezze a modulnak, hogy tegye közzé az adott témaköröket, majd kap egy tokent a munkaterhelés API-ból, hogy jelszóként használja a MQTT-átvitelszervezőhöz való csatlakozáshoz, és végül tegye közzé a jogosult témakörök üzeneteit az Ön által választott MQTT-ügyféllel.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Üzenetek fogadása egy felhasználó által definiált témakörben

A IoT Edge MQTT-közvetítővel hasonlóak a fogadó üzenetek. Először győződjön meg arról, hogy a modulnak van-e engedélye adott témakörökre való előfizetésre, majd szerezzen be egy jogkivonatot a munkaterhelés API-ból, hogy jelszóként használja a MQTT-közvetítőhöz való csatlakozáshoz, és végül fizessen elő a meghatalmazott témakörökben lévő üzenetekre az Ön által választott MQTT-ügyféllel.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub primitívek

A IoT Hub egy analogously egy eszközre, abban az értelemben, hogy:

* képes az [eszközről a felhőbe irányuló üzenetek](../iot-hub/iot-hub-devguide-messaging.md)küldésére;
* közvetlenül a saját identitására irányuló [közvetlen metódusokat](../iot-hub/iot-hub-devguide-direct-methods.md) is fogadhat.
* Ez egy külön modul, amely külön és elkülönített az [eszköz Twin](../iot-hub/iot-hub-devguide-device-twins.md) és a többi modul ikrek közül.

A modulok jelenleg nem fogadhatnak a felhőből az eszközre irányuló üzeneteket, vagy használhatják a fájlfeltöltés funkciót.

Modul írásakor csatlakozhat az IoT Edge hubhoz, és IoT Hub primitíveket használhat, mint amikor a IoT Hub eszközt használja az eszköz alkalmazásával. Az egyetlen különbség IoT Edge modulok és a IoT között, hogy az eszköz identitása helyett a modul identitására kell hivatkoznia.

#### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek

Egy IoT Edge modul képes üzeneteket küldeni a felhőbe az IoT Edge hub segítségével, amely helyi közvetítőként működik, és üzeneteket továbbít a felhőbe. Az eszközről a felhőbe irányuló üzenetek összetett feldolgozásának engedélyezéséhez egy IoT Edge modul képes a más modulok vagy eszközök által a helyi IoT Edge hubhoz küldött üzenetek elfogadására és feldolgozására, valamint a feldolgozott adatmennyiséggel rendelkező új üzenetek küldésére is. A IoT Edge-modulok láncai így létrehozhatók helyi feldolgozási folyamatok létrehozásához.

Az eszközről a felhőbe irányuló telemetria-üzenetek útválasztás használatával történő elküldéséhez használja az Azure IoT SDK ModuleClient. Az Azure IoT SDK-val minden modulhoz tartozik a modul *bemeneti* és *kimeneti* végpontjának koncepciója, amely a speciális MQTT kapcsolatos témakörökre mutat. Használja a `ModuleClient.sendMessageAsync` metódust, és üzeneteket fog küldeni a modul kimeneti végpontján. Ezután konfiguráljon egy útvonalat a edgeHub-ben, hogy a kimeneti végpontot a IoT Hub küldje el.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Az eszközről a felhőbe irányuló telemetria üzenetek a MQTT-közvetítővel való küldése hasonló a felhasználó által definiált témakörökben található üzenetek közzétételéhez, de a következő IoT Hub speciális témakört használja a modulhoz: `devices/<device_name>/<module_name>/messages/events` . Az engedélyeket megfelelően kell beállítani. A MQTT-hidat úgy is be kell állítani, hogy a témakörben lévő üzeneteket a felhőbe továbbítsa.

::: moniker-end

Az útválasztást használó üzenetek feldolgozásához először hozzon létre egy útvonalat egy másik végpontról (modulból vagy eszközről) érkező üzenetek küldéséhez a modul bemeneti végpontján, majd figyelje az üzeneteket a modul bemeneti végpontján. Minden alkalommal, amikor új üzenet érkezik vissza, az Azure IoT SDK elindít egy visszahívási függvényt. Az üzenetet feldolgozhatja ezzel a visszahívási függvénnyel, és opcionálisan elküldheti az új üzeneteket a modul végpontjának várólistáján.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Az üzenetek a MQTT Broker használatával történő feldolgozása hasonló a felhasználó által definiált témakörökben lévő üzenetekre való feliratkozáshoz, de a modul kimeneti üzenetsor IoT Edge speciális témaköreinek használatával: `devices/<device_name>/<module_name>/messages/events` . Az engedélyeket megfelelően kell beállítani. Lehetőség van arra is, hogy új üzeneteket küldjön az Ön által választott témakörökre.

::: moniker-end

#### <a name="twins"></a>Ikrek

Az ikrek a IoT Hub által biztosított primitívek egyike. Az állapotadatok tárolására szolgáló JSON-dokumentumok, beleértve a metaadatokat, a konfigurációkat és a feltételeket is. Minden modulnak vagy eszköznek saját twinja van.

Ha az Azure IoT SDK-val szeretne egy modult beolvasni, hívja meg a `ModuleClient.getTwin` metódust.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Egy különálló modul MQTT-ügyféllel való lekéréséhez egy kicsit több munka van, mivel a Twin lekérdezés nem egy tipikus MQTT-minta. A modulnak először elő kell fizetnie IoT Hub speciális témakörre `$iothub/twin/res/#` . Ennek a témakörnek a neve örökölt IoT Hubtól, és minden eszköznek/modulnak ugyanarra a témakörre kell előfizetnie. Ez nem jelenti azt, hogy az eszközök egymástól függetlenül kapják meg a kétat. A IoT Hub és a edgeHub tudja, hogy melyik Twin-t kell megadnia, még akkor is, ha az összes eszköz ugyanazt a témakör-nevet figyeli. Az előfizetés elvégzése után a modulnak meg kell kérnie a Twin-t egy üzenet közzétételével a következő IoT Hub speciális témakörben egy kérelem-AZONOSÍTÓval `$iothub/twin/GET/?$rid=1234` . Ez a kérelem-azonosító egy tetszőleges azonosító (azaz egy GUID), amelyet a rendszer IoT Hub a kért adattal együtt küld vissza. Az ügyfél így is párosíthatja kérelmeit a válaszokkal. Az eredmény kódja egy HTTP-szerű állapotkód, ahol a sikeres kódolás 200-ként történik.

::: moniker-end

Ha az Azure IoT SDK-val szeretne külön modult kapni, hozzon létre egy visszahívási függvényt, és regisztrálja azt az `ModuleClient.moduleTwinCallback` Azure IOT SDK metódusával, hogy a visszahívási függvény minden alkalommal aktiválódik, amikor a két javítás bekerül.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Ha a modult a MQTT-ügyféllel szeretné megkapni, a folyamat nagyon hasonlít a teljes ikrek fogadására: az ügyfeleknek elő kell fizetniük a speciális IoT Hub témakörre `$iothub/twin/PATCH/properties/desired/#` . Miután az előfizetés létrejött, amikor a IoT Hub elküldi a Twin kívánt szakaszának változását, az ügyfél megkapja azt.

::: moniker-end

#### <a name="receive-direct-methods"></a>Közvetlen metódusok fogadása

Ha közvetlen metódust szeretne kapni az Azure IoT SDK-val, hozzon létre egy visszahívási függvényt, és regisztrálja az `ModuleClient.methodCallback` Azure IOT SDK metódusával, hogy a visszahívási függvény minden olyan alkalommal aktiválódik, amikor egy közvetlen metódus érkezik.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Ha közvetlen metódust szeretne kapni bármely MQTT-ügyféllel, a folyamat nagyon hasonlít a kettős javítások fogadására. Az ügyfélnek meg kell erősítenie, hogy megkapta a hívást, és egy időben küldhet vissza néhány információt. Speciális IoT Hub a következő témakörre: `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Nyelvi és architektúra-támogatás

IoT Edge támogatja több operációs rendszer, eszköz architektúrája és fejlesztői nyelv használatát, így az igényeinek megfelelő forgatókönyvet hozhatja létre. Ebben a szakaszban megismerheti az egyéni IoT Edge-modulok fejlesztésének lehetőségeit. A [fejlesztési és tesztelési környezet előkészítéséhez az IoT Edge](development-environment.md)az egyes nyelvekhez kapcsolódó támogatás és követelmények megismerését ismertető témakörben olvashat bővebben.

### <a name="linux"></a>Linux

Az alábbi táblázatban szereplő összes nyelv esetében IoT Edge támogatja az AMD64 és a ARM32 Linux-tárolók fejlesztését.

| Fejlesztési nyelv | Fejlesztési eszközök |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>A ARM64 Linux-tárolók fejlesztésének és hibakeresésének támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el. További információ: [ARM64 IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio Code-ban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Az alábbi táblázatban szereplő összes nyelv esetében IoT Edge támogatja az AMD64 Windows-tárolók fejlesztését.

| Fejlesztési nyelv | Fejlesztési eszközök |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (nincs hibakeresési képesség)<br>Visual Studio 2017/2019 |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 1,1 LTS az utolsó kiadási csatorna, amely támogatja a Windows-tárolókat. A 1,2-es verziótól kezdődően a Windows-tárolók nem támogatottak.

A Windows-tárolók fejlesztésével kapcsolatos információkért tekintse meg a jelen cikk [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) -es verzióját.

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>Következő lépések

[A fejlesztési és tesztelési környezet előkészítése IoT Edge](development-environment.md)

[A Visual Studio használata C#-modulok fejlesztéséhez IoT Edge](how-to-visual-studio-develop-module.md)

[Modulok fejlesztése a Visual Studio Code használatával IoT Edge](how-to-vs-code-develop-module.md)

[Az Azure IoT Hub SDK-k ismertetése és használata](../iot-hub/iot-hub-devguide-sdks.md)
