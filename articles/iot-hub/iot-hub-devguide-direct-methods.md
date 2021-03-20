---
title: Az Azure IoT Hub Direct metódusok ismertetése | Microsoft Docs
description: Fejlesztői útmutató – közvetlen metódusok használatával meghívhatja a programkódot az eszközökön egy szolgáltatási alkalmazásból.
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: b75e859fc1237bc88bee464cef423b7289810fa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147793"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Közvetlen metódusok megismerése és meghívása az IoT Hubból

IoT Hub lehetővé teszi közvetlen metódusok meghívását a felhőből származó eszközökön. A közvetlen metódusok a kérés-válasz interakciót egy olyan eszközhöz hasonlítják, amely a HTTP-híváshoz hasonlóan sikeres vagy sikertelen (a felhasználó által megadott időtúllépés után). Ez a módszer olyan helyzetekben hasznos, amikor az azonnali művelet végrehajtása eltérő attól függően, hogy az eszköz válaszképes volt-e.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Minden eszköz metódusa egyetlen eszközt céloz meg. [A feladatok több eszközön való ütemezésével](iot-hub-devguide-jobs.md) megtudhatja, hogyan lehet a közvetlen metódusokat több eszközön meghívni, és ütemezni a leválasztott eszközök meghívását.

A IoT Hub **Service-csatlakozási** engedélyeivel bárki meghívhatja az eszközön a metódust.

A közvetlen metódusok a kérelem-válasz mintát követik, és olyan kommunikációra szolgálnak, amelyek azonnali megerősítést igényelnek az eredményük alapján. Például az eszköz interaktív vezérlése, például ventilátor bekapcsolása.

Ha kétségei vannak a kívánt tulajdonságok, közvetlen metódusok vagy a felhőből az eszközre irányuló üzenetek használatával kapcsolatban, tekintse meg a [felhőből az eszközre irányuló kommunikációs útmutatót](iot-hub-devguide-c2d-guidance.md) .

## <a name="method-lifecycle"></a>Módszer életciklusa

A közvetlen metódusok implementálva vannak az eszközön, és az adattartalom megfelelő létrehozásához nulla vagy több bemenet szükséges. Közvetlen metódust hív meg egy szolgáltatással szemben álló URI-n ( `{iot hub}/twins/{device id}/methods/` ) keresztül. Egy eszköz közvetlen metódusokat kap egy adott eszközre vonatkozó MQTT-témakörben ( `$iothub/methods/POST/{method name}/` ), vagy AMQP-hivatkozásokon keresztül (a `IoThub-methodname` és az `IoThub-status` alkalmazás tulajdonságain) keresztül.

> [!NOTE]
> Ha egy eszközön közvetlen metódust hív meg, a tulajdonságok nevei és értékei csak az US-ASCII nyomtatható alfanumerikus karaktereket tartalmazhatják, kivéve a következő készletben lévőket: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

A közvetlen metódusok szinkronban vannak, és sikeresek vagy sikertelenek az időkorlát után (alapértelmezett: 30 másodperc, 5 és 300 másodperc között állítható be). A közvetlen metódusok olyan interaktív helyzetekben hasznosak, amikor azt szeretné, hogy az eszköz csak akkor járjon el, ha az eszköz online állapotban van, és parancsokat fogad. Tegyük fel például, hogy a telefonról bekapcsol egy fényt. Ezekben az esetekben azonnali sikerességet vagy hibát szeretne látni, hogy a felhőalapú szolgáltatás a lehető leghamarabb képes legyen az eredményre. Előfordulhat, hogy az eszköz valamilyen üzenetet ad vissza a metódus eredményeként, de nem szükséges ehhez a metódushoz. A metódusok hívásakor nincs garancia a rendelésre vagy a párhuzamossági szemantikara.

A közvetlen metódusok csak HTTPS-alapúak, a felhőalapú oldalról, a MQTT, a AMQP, a MQTT-en keresztül a websocketek, vagy a AMQP WebSocket-kapcsolaton keresztül az eszköz oldaláról.

A metódus-kérelmek és válaszok adattartalma egy JSON-dokumentum, amely akár 128 KB-ig is használható.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Közvetlen metódus meghívása egy háttérbeli alkalmazásból

Most hívja meg a közvetlen metódust egy háttérbeli alkalmazásból.

### <a name="method-invocation"></a>Metódus meghívása

Az eszközök közvetlen metódusának meghívása a következő elemekből álló HTTPS-hívások:

* Az eszközhöz tartozó *kérelem URI-ja* az [API-verzióval](/rest/api/iothub/service/devices/invokemethod)együtt:

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* A POST *metódus*

* Az engedélyezést, a kérelem AZONOSÍTÓját, a tartalom típusát és a tartalom kódolását tartalmazó *fejlécek* .

* Egy transzparens JSON- *törzs* a következő formátumban:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

A kérelemben megadott érték azt `responseTimeoutInSeconds` az időtartamot, ameddig IoT hub szolgáltatásnak meg kell várnia az eszközön végzett közvetlen metódus végrehajtásának befejezéséhez. Állítsa be ezt az időkorlátot úgy, hogy az egy adott eszköz által várt végrehajtási időnek megfelelően legyen legalább egy ideig. Ha nincs megadva időtúllépés, a rendszer az alapértelmezett 30 másodperc értéket használja. A minimális és a maximális érték `responseTimeoutInSeconds` 5 és 300 másodperc.

A `connectTimeoutInSeconds` kérelemben megadott érték azt az időtartamot határozza meg, ameddig egy közvetlen metódus meghívásakor a IoT hub szolgáltatásnak meg kell várnia, hogy egy leválasztott eszköz online állapotba kerüljön. Az alapértelmezett érték 0, ami azt jelenti, hogy az eszközöknek már online állapotba kell esniük a közvetlen metódus meghívása után. A maximális érték `connectTimeoutInSeconds` 300 másodperc.

#### <a name="example"></a>Példa

Ez a példa lehetővé teszi, hogy biztonságosan kezdeményezzen egy közvetlen metódus meghívására irányuló kérelmet egy Azure-IoT Hub regisztrált IoT-eszközön.

A kezdéshez használja a [Microsoft Azure IoT bővítményét az Azure CLI-hez](https://github.com/Azure/azure-iot-cli-extension) egy SharedAccessSignature létrehozásához.

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

Ezután cserélje le az engedélyezési fejlécet az újonnan létrehozott SharedAccessSignature, majd módosítsa a `iothubName` , `deviceId` a `methodName` és a `payload` paramétereket úgy, hogy az megfeleljen az alábbi példában szereplő parancs megvalósításának `curl` .  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

Futtassa a módosított parancsot a megadott közvetlen metódus meghívásához. A sikeres kérések egy HTTP 200 állapotkódot adnak vissza.

> [!NOTE]
> A fenti példa egy közvetlen metódus meghívását mutatja be egy eszközön.  Ha közvetlen metódust szeretne meghívni egy IoT Edge modulban, módosítania kell az URL-kérést az alábbi ábrán látható módon:

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Reagálás

A háttérbeli alkalmazás a következő elemekből álló választ kap:

* *Http-állapotkód*:
  * 200 a közvetlen metódus sikeres végrehajtását jelzi;
  * 404 azt jelzi, hogy az eszköz azonosítója érvénytelen, vagy ha az eszköz nem volt online állapotú, és `connectTimeoutInSeconds` ezt követően (a mellékelt hibaüzenetet használja a kiváltó ok megértéséhez);
  * a 504 azt jelzi, hogy az eszköz nem válaszol a közvetlen metódus hívására az átjáró által okozott időtúllépés miatt `responseTimeoutInSeconds` .

* A ETag, a kérelem AZONOSÍTÓját, a tartalom típusát és a tartalom kódolását tartalmazó *fejlécek* .

* Egy JSON- *törzs* a következő formátumban:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Mindkettő `status` és `body` az eszköz által biztosított, és az eszköz saját állapotkódot és/vagy leírását használva válaszol.

### <a name="method-invocation-for-iot-edge-modules"></a>Metódus meghívása IoT Edge modulokhoz

A közvetlen metódusok modul-AZONOSÍTÓval történő meghívása támogatott a [IoT szolgáltatás ügyfél C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/)-ban.

Erre a célra használja a `ServiceClient.InvokeDeviceMethodAsync()` metódust, és adja meg a `deviceId` és a `moduleId` paramétert.

## <a name="handle-a-direct-method-on-a-device"></a>Közvetlen metódus kezelése egy eszközön

Nézzük meg, hogyan kezelheti a Direct metódust egy IoT-eszközön.

### <a name="mqtt"></a>MQTT

A következő szakasz a MQTT protokollra mutat.

#### <a name="method-invocation"></a>Metódus meghívása

Az eszközök közvetlen metódus-kérelmeket fogadnak a MQTT témakörben: `$iothub/methods/POST/{method name}/?$rid={request id}` . Az eszközönkénti előfizetések száma legfeljebb 5 lehet. Ezért javasoljuk, hogy ne fizessen elő külön az egyes közvetlen metódusokra. Ehelyett érdemes lehet előfizetni, `$iothub/methods/POST/#` majd a kívánt metódus neve alapján szűrni a továbbított üzeneteket.

Az eszköz által fogadott törzs formátuma a következő:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

A metódusokra vonatkozó kérelmek QoS 0.

#### <a name="response"></a>Reagálás

Az eszköz választ küld a következő `$iothub/methods/res/{status}/?$rid={request id}` helyekre:

* A `status` tulajdonság a metódus-végrehajtás eszköz által megadott állapota.

* A `$rid` tulajdonság a IoT Hubtól kapott hívási kérelem azonosítója.

A törzset az eszköz állítja be, és bármely állapot lehet.

### <a name="amqp"></a>AMQP

A következő szakasz a AMQP protokollra mutat.

#### <a name="method-invocation"></a>Metódus meghívása

Az eszköz fogadja a közvetlen metódus kéréseit egy fogadási hivatkozás létrehozásával a címen `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

A AMQP üzenet a metódus kérését képviselő fogadási hivatkozáson érkezik. A következő szakaszt tartalmazza:

* A korrelációs azonosító tulajdonsága, amely tartalmazza a kérelem AZONOSÍTÓját, amelyet vissza kell adni a megfelelő metódus válaszával.

* Egy nevű alkalmazás `IoThub-methodname` -tulajdonság, amely a meghívott metódus nevét tartalmazza.

* Az AMQP JSON-ként tartalmazó üzenet törzse.

#### <a name="response"></a>Reagálás

Az eszköz létrehoz egy küldési hivatkozást a metódus válaszának visszaadásához a címen `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

A metódus válaszát a rendszer a küldési hivatkozáson adja vissza, a következő módon strukturálva:

* A korrelációs azonosító tulajdonsága, amely a metódus kérelmi üzenetében átadott kérés AZONOSÍTÓját tartalmazza.

* Egy nevű alkalmazás `IoThub-status` -tulajdonság, amely a felhasználó által megadott metódus állapotát tartalmazza.

* A AMQP JSON-ként tartalmazó üzenet törzse.

## <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* [IoT hub végpontok](iot-hub-devguide-endpoints.md) ismertetik a különböző végpontokat, amelyeket az egyes IoT hub a futásidejű és a felügyeleti műveletek számára tesz elérhetővé.

* A [szabályozás és a kvóták](iot-hub-devguide-quotas-throttling.md) ismertetik az alkalmazandó kvótákat és a IoT hub használatakor várható szabályozási viselkedést.

* Az [Azure IoT-eszközök és-szolgáltatások SDK](iot-hub-devguide-sdks.md) -k felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* Az [ikrek, a feladatok és az üzenet-útválasztás IoT hub lekérdezési nyelve](iot-hub-devguide-query-language.md) leírja a IoT hub lekérdezési nyelvet, amellyel információkat kérhet le az eszközről az ikrekről és a feladatokról IoT hub.

* [IOT hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a közvetlen módszerek használatát, érdemes lehet a következő IoT Hub fejlesztői útmutató cikke érdekli:

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Ha szeretné kipróbálni a cikkben ismertetett fogalmak némelyikét, érdemes lehet az alábbi IoT Hub oktatóanyagot felvenni:

* [Közvetlen metódusok használata](quickstart-control-device-node.md)
* [Eszközfelügyelet VS Code-hoz készült Azure IoT-eszközökkel](iot-hub-device-management-iot-toolkit.md)