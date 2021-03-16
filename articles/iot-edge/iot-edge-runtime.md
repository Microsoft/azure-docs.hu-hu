---
title: Ismerje meg, hogyan kezeli a futtatókörnyezet az eszközöket – Azure IoT Edge | Microsoft Docs
description: Ismerje meg, hogyan kezeli a IoT Edge Runtime a modulokat, a biztonságot, a kommunikációt és a jelentéskészítést az eszközökön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496252"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>A Azure IoT Edge futtatókörnyezet és az architektúrájának ismertetése

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Az IoT Edge-futtatókörnyezet olyan programok gyűjteménye, amelyek az eszközt IoT Edge-eszközzé alakítják. Együttesen a IoT Edge futtatókörnyezet-összetevők lehetővé teszik, hogy IoT Edge eszközök megkapják a kód futtatását a peremhálózat szélén, és közlik az eredményeket.

A IoT Edge futtatókörnyezet felelős a következő függvényekért IoT Edge eszközökön:

* A számítási feladatok telepítése és frissítése az eszközön.

* Azure IoT Edge biztonsági szabványok fenntartása az eszközön.

* Győződjön meg arról, hogy [IoT Edge modulok](iot-edge-modules.md) mindig futnak.

* Jelentési modul állapota a felhőben távoli figyeléshez.

* Az alsóbb rétegbeli eszközök és IoT Edge eszközök közötti kommunikáció kezelése.

* IoT Edge eszközön található modulok közötti kommunikáció kezelése.

* IoT Edge-eszköz és a felhő közötti kommunikáció kezelése.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* IoT Edge eszközök közötti kommunikáció kezelése.
::: moniker-end

![A Runtime a bepillantást és a modul állapotát közli IoT Hub](./media/iot-edge-runtime/Pipeline.png)

A IoT Edge futtatókörnyezet feladatai két kategóriába sorolhatók: a kommunikáció és a modul kezelése. Ezt a két szerepkört két, a IoT Edge futtatókörnyezet részét képező összetevő hajtja végre. A *IoT Edge ügynök* üzembe helyezi és figyeli a modulokat, míg a *IoT Edge hub* felelős a kommunikációért.

A IoT Edge ügynök és az IoT Edge hub is a modulok, ugyanúgy, mint bármely más, IoT Edge eszközön futó modulhoz. Más néven *futtatókörnyezeti modulok*.

## <a name="iot-edge-agent"></a>IoT Edge ügynök

A IoT Edge ügynök az Azure IoT Edge futtatókörnyezetet alkotó két modul egyike. A modulok létrehozásához, valamint a modulok állapotának a IoT Hub való visszajelentéséhez felelős. Ezek a konfigurációs adatkészletek a IoT Edge Agent modul Twin.

A [IoT Edge biztonsági démon](iot-edge-security-manager.md) elindítja a IoT Edge ügynököt az eszköz indításakor. Az ügynök lekéri a modult a IoT Hub, és megvizsgálja az üzembe helyezési jegyzéket. Az üzembe helyezési jegyzékfájl egy olyan JSON-fájl, amely deklarálja az elindításához szükséges modulokat.

Az üzembe helyezési jegyzék minden eleme tartalmaz konkrét információkat egy modulról, és a IoT Edge ügynök használja a modul életciklusának szabályozására. A IoT Edge-ügynök által a modulok vezérléséhez használt összes tulajdonsággal kapcsolatos további információkért olvassa el az [IoT Edge Agent és az IoT Edge hub-modul ikrek tulajdonságait](module-edgeagent-edgehub.md)ismertető témakört.

A IoT Edge ügynök futásidejű választ küld a IoT Hubnak. Itt látható a lehetséges válaszok listája:
  
* 200 – OK
* 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
* 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
* 412 – a séma verziója érvénytelen a telepítési konfigurációban.
* 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
* 500 – hiba történt a IoT Edge futtatókörnyezetben.

Az üzembe helyezési jegyzékek létrehozásával kapcsolatos további információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

### <a name="security"></a>Biztonság

A IoT Edge ügynök kritikus szerepet játszik egy IoT Edge-eszköz biztonságában. Például végrehajtja az olyan műveleteket, mint a modul rendszerképének ellenőrzése a megkezdése előtt.

A Azure IoT Edge biztonsági keretrendszerével kapcsolatos további információkért olvassa el a [IoT Edge Security Manager](iot-edge-security-manager.md)című témakört.

## <a name="iot-edge-hub"></a>IoT Edge hub

Az IoT Edge hub a másik modul, amely a Azure IoT Edge futtatókörnyezetet hozza létre. A IoT Hub helyi proxyként működik, ha a protokoll-végpontokat IoT Hubként teszi elérhetővé. Ez a konzisztencia azt jelenti, hogy az ügyfelek ugyanúgy csatlakozhatnak a IoT Edge futtatókörnyezethez, mint IoT Hub.

Az IoT Edge hub nem a helyileg futó IoT Hub teljes verziója. IoT Edge hub csendesen delegál bizonyos feladatokat IoT Hub. Az IoT Edge hub például automatikusan letölti a hitelesítési adatokat a IoT Hubról az első kapcsolaton, hogy lehetővé tegye az eszköz kapcsolódását. Az első összekapcsolást követően az engedélyezési adatokat IoT Edge hub helyileg gyorsítótárazza. Az eszközről érkező jövőbeli kapcsolatok engedélyezése anélkül engedélyezett, hogy újra le kellene töltenie az engedélyezési adatokat a felhőből.

### <a name="cloud-communication"></a>Felhőbeli kommunikáció

A IoT Edge-megoldás által használt sávszélesség csökkentése érdekében a IoT Edge hub azt optimalizálja, hogy hány tényleges kapcsolat legyen elérhető a felhőben. IoT Edge hub logikai kapcsolatot létesít a modulok vagy az alárendelt eszközök között, és egyetlen fizikai kapcsolattal kombinálja őket a felhővel. A folyamat részletei transzparensek a megoldás többi részén. Az ügyfelek úgy gondolják, hogy saját kapcsolattal rendelkeznek a felhővel, annak ellenére, hogy mindegyiket ugyanazon a kapcsolaton keresztül küldik el. Az IoT Edge hub a AMQP vagy a MQTT protokoll használatával kommunikálhat a felhővel, az alsóbb szintű eszközök által használt protokolloktól függetlenül. Az IoT Edge hub azonban jelenleg csak a logikai kapcsolatok egyetlen fizikai kapcsolatba való egyesítését támogatja, ha a AMQP használja a felsőbb rétegbeli protokollként és a Multiplexing képességekkel. A AMQP az alapértelmezett felsőbb rétegbeli protokoll.

![IoT Edge hub a fizikai eszközök és a IoT Hub közötti átjáró.](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge hub képes megállapítani, hogy csatlakozik-e IoT Hubhoz. Ha a kapcsolat megszakad, IoT Edge hub üzeneteket ment helyileg, vagy két különálló frissítést. A kapcsolatok újbóli létrehozása után szinkronizálja az összes adatforrást. Az ehhez az ideiglenes gyorsítótárhoz használt helyet az IoT Edge hub-modul Twin-moduljának egyik tulajdonsága határozza meg. A gyorsítótár mérete nem megengedett, és addig nő, amíg az eszköz tárolókapacitással rendelkezik. További információ: [Offline képességek](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Modul kommunikációja

IoT Edge hub megkönnyíti a modul kommunikációját. IoT Edge hub használata az üzenetsor-átvitelszervezőként a modulok egymástól függetlenek maradnak. A moduloknak csak azokat a bemeneteket kell megadniuk, amelyeken üzeneteket fogadnak, valamint azokat a kimeneteket, amelyekhez üzeneteket írnak. A megoldás fejlesztője összevonhatja ezeket a bemeneteket és kimeneteket, így a modulok az adott megoldáshoz tartozó sorrendben dolgozzák fel az adatokat.

![IoT Edge hub elősegíti a modul – modul kommunikációt](./media/iot-edge-runtime/module-endpoints.png)

Az IoT Edge hubhoz való adatküldéshez egy modul hívja meg a SendEventAsync metódust. Az első argumentum azt adja meg, hogy melyik kimenetre kell elküldeni az üzenetet. A következő pseudocode üzenetet küld a **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Ha üzenetet szeretne kapni, regisztráljon egy olyan visszahívást, amely egy adott bemeneten lévő üzeneteket dolgoz fel. A következő pseudocode regisztrálja a **input1** fogadott összes üzenet feldolgozásához használni kívánt messageProcessor-függvényt:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

A ModuleClient osztályról és a hozzá tartozó kommunikációs módszerekről további információt az előnyben részesített SDK-nyelv API-referenciája tartalmaz: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)vagy [Node.js](/javascript/api/azure-iot-device/moduleclient).

A megoldás fejlesztői feladata azoknak a szabályoknak a meghatározása, amelyek meghatározzák, hogy IoT Edge hub hogyan továbbít üzeneteket a modulok között. Az útválasztási szabályok a felhőben vannak meghatározva, és leküldve IoT Edge hubhoz a moduljában. A IoT Hub útvonalakhoz tartozó szintaxis a Azure IoT Edge moduljai közötti útvonalak definiálására szolgál. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

![A modulok közötti útvonalak IoT Edge hub-on keresztül haladnak](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Helyi kommunikáció

IoT Edge hub elősegíti a helyi kommunikációt. Lehetővé teszi az eszközök közötti, modul-modul, eszközök közötti kommunikációt az üzenetek közvetítésével, hogy az eszközök és modulok egymástól függetlenek maradjanak.

>[!NOTE]
> A MQTT Broker szolgáltatás nyilvános előzetes verziója IoT Edge 1,2-es verzió. Explicit módon engedélyezve kell lennie.

Az IoT Edge hub két közvetítői mechanizmust támogat:

1. A [IoT hub és a által támogatott üzenet-útválasztási funkciók](../iot-hub/iot-hub-devguide-messages-d2c.md)
2. Általános célú MQTT-közvetítő, amely megfelel a [MQTT standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) -nek

#### <a name="using-routing"></a>Útválasztás használata

Az első közvetítő mechanizmus ugyanazt az útválasztási funkciót használja, mint a IoT Hub annak meghatározásához, hogy az üzenetek hogyan legyenek átadva az eszközök vagy modulok között. Az első eszközök vagy modulok határozzák meg, hogy mely bemeneteken fogadják el az üzeneteket, valamint azokat a kimeneteket, amelyekhez üzeneteket írnak. Ezt követően a megoldás fejlesztője üzeneteket továbbíthat a forrás, például a kimenetek és a célhely között, például a bemeneteket, és lehetséges szűrőket is használhat.

![A modulok közötti útvonalak IoT Edge hub-on keresztül haladnak](./media/iot-edge-runtime/module-endpoints-routing.png)

Az útválasztást az Azure IoT Device SDK-val készített eszközök vagy modulok használhatják a AMQP vagy a MQTT protokoll használatával. Az üzenetküldési IoT Hub primitívek, például a telemetria, a közvetlen metódusok, a C2D, az ikrek, de a felhasználó által definiált témakörökkel való kommunikáció nem támogatott.

Az útvonalakkal kapcsolatos további információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>A MQTT Broker használata

A második közvetítő mechanizmus egy standard MQTT-közvetítőn alapul. A MQTT egy egyszerűsített üzenetküldési protokoll, amely garantálja az optimális teljesítményt az erőforrás által korlátozott eszközökön, és népszerű közzétételi és előfizetési szabvány. Az eszközök vagy modulok előfizetnek a témakörökre más eszközök vagy modulok által közzétett üzenetek fogadásához. IoT Edge hub implementálja saját MQTT-közvetítőjét, amely [a 3.1.1-es MQTT-verzió specifikációit](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)követi.

A MQTT Broker két további kommunikációs mintát tesz lehetővé az útválasztáshoz képest: helyi műsorszolgáltatás és pont – pont kommunikáció. A helyi szórás akkor hasznos, ha egy eszköznek vagy modulnak helyileg kell riasztania több más eszközt vagy modult. A pont-pont típusú kommunikáció lehetővé teszi, hogy két IoT Edge eszköz vagy két IoT-eszköz helyileg kommunikáljon anélkül, hogy a felhőbe oda kellene lépni.

![Helyi közzététel és előfizetés IoT Edge hubhoz](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

A MQTT-közvetítőt olyan eszközök vagy modulok is használhatják, amelyek a MQTT protokollon vagy az általános célú MQTT-ügyfeleken keresztül kommunikáló Azure IoT Device SDK-k használatával készültek. A C2D kivételével az összes üzenetküldési IoT Hub primitív, például a telemetria, a közvetlen metódusok, az ikrek támogatottak. IoT Hub IoT Hub primitívek által használt speciális témakörök támogatottak, ezért a felhasználó által definiált témakörök.
Ez a témakör egy IoT Hub speciális témakör vagy egy felhasználó által definiált témakör.

Az útválasztási mechanizmustól eltérően az üzenetek rendezése csak a legjobb megoldás, és nem garantált, és az üzenetek szűrését a közvetítő nem támogatja. Ezeknek a funkcióknak a hiánya azonban lehetővé teszi, hogy a MQTT-közvetítő gyorsabban legyen, mint az Útválasztás.

További információ a MQTT-közvetítőről: [Közzététel és előfizetés IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>A közvetítő mechanizmusok összehasonlítása

Az alábbi funkciók érhetők el az egyes közvetítői mechanizmusokhoz:

|Funkciók  | Útválasztás  | MQTT-közvetítő  |
|---------|---------|---------|
|D2C telemetria    |     &#10004;    |         |
|Helyi telemetria     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Ikereszköz     |    &#10004;     |    &#10004;     |
|C2D az eszközökhöz     |   &#10004;      |         |
|Rendezés     |    &#10004;     |         |
|Szűrés     |     &#10004;    |         |
|Felhasználó által definiált témakörök     |         |    &#10004;     |
|Eszközről eszközre     |         |    &#10004;     |
|Helyi szórás     |         |    &#10004;     |
|Teljesítmény     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Csatlakozás az IoT Edge hubhoz

Az IoT Edge hub a MQTT protokollon vagy a AMQP protokollon keresztül fogadja az eszköz-vagy modul-ügyfelek kapcsolatait.

>[!NOTE]
> IoT Edge hub a MQTT vagy a AMQP használatával csatlakozó ügyfeleket támogatja. Nem támogatja a HTTP protokollt használó ügyfeleket.

Amikor az ügyfél csatlakozik az IoT Edge hubhoz, a következők történnek:

1. Ha Transport Layer Security (TLS) használatban van (ajánlott), a rendszer egy TLS-csatornát hoz létre az ügyfél és a IoT Edge hub közötti titkosított kommunikáció létrehozásához.
2. A hitelesítő adatokat a rendszer az ügyféltől IoT Edge hub-ra továbbítja a saját maga azonosítására.
3. IoT Edge hub engedélyezési házirendje alapján engedélyezi vagy elutasítja a kapcsolat elutasítását.

#### <a name="secure-connections-tls"></a>Biztonságos kapcsolatok (TLS)

Alapértelmezés szerint az IoT Edge hub csak Transport Layer Security (TLS) védelemmel ellátott kapcsolatokat fogad el, például titkosított kapcsolatokat, amelyeket a harmadik fél nem tud visszafejteni.

Ha az ügyfél az 8883-as (MQTTS) vagy a 5671 (AMQPS) porton csatlakozik az IoT Edge hubhoz, a TLS-csatornát létre kell venni. A TLS-kézfogás során az IoT Edge hub elküldi a tanúsítványláncot, amelyet az ügyfélnek ellenőriznie kell. A tanúsítványlánc érvényesítéséhez az IoT Edge hub főtanúsítványát megbízható tanúsítványként kell telepíteni az ügyfélen. Ha a főtanúsítvány nem megbízható, az IoT Edge hub a tanúsítvány-ellenőrzési hiba miatt elutasítja az ügyfél függvénytárát.

Az eszközökön található közvetítő főtanúsítványának telepítéséhez szükséges lépéseket az [átlátszó átjáró](how-to-create-transparent-gateway.md) és az [alsóbb rétegbeli eszköz előkészítése](how-to-connect-downstream-device.md#prepare-a-downstream-device) című dokumentáció ismerteti. A modulok ugyanazt a főtanúsítványt használhatják, mint az IoT Edge hub a IoT Edge Daemon API használatával.

#### <a name="authentication"></a>Hitelesítés

Az IoT Edge hub csak olyan eszközök vagy modulok kapcsolatait fogadja el, amelyeknek IoT Hub identitása van, például a IoT Hub regisztrálva van, és az IoT hub által támogatott három ügyfél-hitelesítési módszer közül az egyiket kell bizonyítania: [szimmetrikus kulcsok hitelesítése](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [x. 509 önaláírt hitelesítés](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), [x. 509 hitelesítésszolgáltató által aláírt hitelesítés](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Ezeket a IoT Hub identitásokat a IoT Edge hub helyileg ellenőrizheti, így a kapcsolatok kapcsolat nélküli üzemmódban is elvégezhető.

Megjegyzések:

* IoT Edge modulok jelenleg csak a szimmetrikus kulcsos hitelesítést támogatják.
* Az IoT Edge hub MQTT Broker nem fogadja el a csak helyi felhasználónévvel és jelszóval rendelkező MQTT-ügyfeleket, IoT Hub identitásokat kell használniuk.

#### <a name="authorization"></a>Engedélyezés

A hitelesítés után az IoT Edge hub két módon engedélyezheti az ügyfélkapcsolatokat:

* Annak ellenőrzésével, hogy az ügyfél a IoT Hubban definiált megbízható ügyfelek készletéhez tartozik-e. A megbízható ügyfelek készletét szülő/gyermek vagy eszköz/modul kapcsolatok beállításával adja meg IoT Hubban. Ha egy modult IoT Edge hoz létre, automatikusan létrejön egy megbízhatósági kapcsolat a modul és a IoT Edge eszköz között. Ez az egyetlen, az útválasztási közvetítő mechanizmus által támogatott engedélyezési modell.

* Egy engedélyezési házirend beállításával. Ez az engedélyezési házirend egy dokumentum, amely felsorolja az összes olyan engedélyezett ügyfél-identitást, amely hozzáférhet az IoT Edge hub erőforrásaihoz. Ez az IoT Edge hub MQTT Broker által használt elsődleges engedélyezési modell, bár a szülő/gyermek és az eszközök/modulok kapcsolatait a MQTT Broker is értelmezheti IoT Hub témakörökhöz.

### <a name="remote-configuration"></a>Távoli konfigurálás

Az IoT Edge hub-t teljes mértékben a felhő vezérli. A konfigurációját IoT Hub a [modul Twin](iot-edge-modules.md#module-twins)-n keresztül érheti el. A következőket tartalmazza:

* Útvonalak konfigurálása
* Engedélyezési házirendek
* MQTT-híd konfigurációja

Emellett több konfigurációt is elvégezhet [az IoT Edge hub környezeti változóinak](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)beállításával.
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Futtatókörnyezet minőségi telemetria

A IoT Edge a gazdagép-futtatókörnyezetből és a rendszermodulokból származó névtelen telemetria gyűjti a termék minőségének javítása érdekében. Ezt az információt futtatókörnyezet minőségi telemetria nevezzük. Az összegyűjtött telemetria az eszközről a felhőbe irányuló üzenetekként küldi el a rendszer a IoT Edge-ügynöktől IoT Hub. Ezek az üzenetek nem jelennek meg az ügyfél normál telemetria, és nem használják fel az üzenetek kvótáját.

A IoT Edge-ügynök és a hub olyan mérőszámokat állít elő, amelyeket az eszköz teljesítményének megismeréséhez gyűjthet. A metrikák egy részhalmazát a IoT Edge ügynök gyűjti a futtatókörnyezet minőségi telemetria részeként. A futásidejű minőségi telemetria gyűjtött metrikák a címkével vannak ellátva `ms_telemetry` . További információ az összes elérhető metrikáról: hozzáférés a [beépített mérőszámokhoz](how-to-access-built-in-metrics.md).

A feltöltés előtt a rendszer eltávolítja az összes személyes vagy szervezetian azonosítható információt, például az eszközök és a modulok nevét, így biztosítva a futásidejű minőségi telemetria névtelen jellegét.

A IoT Edge ügynök óránként gyűjti a telemetria, és minden 24 órában egy üzenetet küld IoT Hub.

Ha szeretné letiltani a futásidejű telemetria az eszközökről, két módon teheti meg a következőket:

* Állítsa a `SendRuntimeQualityTelemetry` környezeti változót `false` a **edgeAgent**, vagy
* Az üzembe helyezés során törölje a Azure Portal kapcsolót.

## <a name="next-steps"></a>Következő lépések

* [Az Azure IoT Edge-modulok ismertetése](iot-edge-modules.md)
* [Ismerje meg, hogyan telepíthet modulokat és hozhat létre útvonalakat az IoT Edge-ben](module-composition.md)
* [Megtudhatja, hogyan tehet közzé és fizethet elő IoT Edge](how-to-publish-subscribe.md)
* [Tudnivalók a IoT Edge futtatókörnyezeti mérőszámokról](how-to-access-built-in-metrics.md)
