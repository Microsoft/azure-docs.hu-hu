---
title: Az Azure IoT Hub MQTT-támogatás ismertetése | Microsoft Docs
description: IoT Hub eszközre irányuló végponthoz csatlakozó eszközök támogatása a MQTT protokoll használatával. Az Azure IoT Device SDK-k beépített MQTT-támogatásával kapcsolatos információkat tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: df706a83c4892c15140e5d5c827a248156b66069
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095689"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunikáció az IoT hub használatával a MQTT protokollal

IoT Hub lehetővé teszi, hogy az eszközök a következő használatával kommunikáljanak a IoT Hub-eszköz végpontjait:

* [MQTT v 3.1.1](https://mqtt.org/) a 8883-es porton
* MQTT v 3.1.1 a 443-es porton keresztüli WebSocket-en keresztül.

Az IoT Hub nem teljes szolgáltatásokat nyújtó MQTT-közvetítő, és nem támogatja az MQTT 3.1.1-es standard verziójában megadott összes viselkedést. Ez a cikk azt ismerteti, hogy az eszközök hogyan használhatják a támogatott MQTT-viselkedéseket IoT Hubekkel való kommunikációhoz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A IoT Hubokkal való kommunikációt a TLS/SSL protokollal kell biztosítani. Ezért a IoT Hub nem támogatja a nem biztonságos kapcsolatokat a 1883-as porton keresztül.

## <a name="connecting-to-iot-hub"></a>Csatlakozás a IoT Hubhoz

Az eszközök a MQTT protokoll használatával csatlakozhatnak egy IoT hubhoz az alábbi lehetőségek bármelyikével.

* Az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k könyvtárai.
* A MQTT protokoll közvetlenül.

A MQTT port (8883) számos vállalati és oktatási hálózati környezetben blokkolva van. Ha nem tudja megnyitni a 8883-as portot a tűzfalon, javasoljuk, hogy használjon MQTT-t a webes szoftvercsatornák használatával. A webes szoftvercsatornák MQTT a 443-as porton keresztül kommunikál, ami szinte mindig hálózati környezetben van nyitva. Az Azure IoT SDK-k használatával megtudhatja, hogyan határozhatja meg a MQTT és a MQTT a web Sockets protokollokon keresztül, az [eszköz SDK](#using-the-device-sdks)-k használata című témakörben.

## <a name="using-the-device-sdks"></a>Az eszköz SDK-k használata

A MQTT protokollt támogató [eszköz SDK](https://github.com/Azure/azure-iot-sdks) -k Java, Node.js, C, C# és Python nyelveken érhetők el. Az eszköz SDK-k a standard IoT Hub kapcsolati karakterláncot használják az IoT hub-kapcsolat létesítéséhez. Az MQTT protokoll használatához a **MQTT** értékre kell állítani az ügyfél protokoll paraméterét. A MQTT megadhatja a webes szoftvercsatornák között is az ügyféloldali protokoll paraméterében. Alapértelmezés szerint az eszköz SDK-k egy, a **CleanSession** jelzővel rendelkező IoT hubhoz csatlakoznak **, és az** IoT hub használatával történő üzenetváltáshoz a **QoS 1** eszközt használják. Habár lehetséges a **QoS 0** konfigurálása a gyorsabb üzenetváltáshoz, vegye figyelembe, hogy a kézbesítés nem garantált és nem elismert. Emiatt a **QoS 0** kifejezést gyakran "Fire and forget" néven emlegetik.

Ha egy eszköz egy IoT-hubhoz csatlakozik, az eszköz SDK-k olyan metódusokat biztosítanak, amelyek lehetővé teszik, hogy az eszköz üzeneteket cseréljen az IoT hub-ra.

Az alábbi táblázat az egyes támogatott nyelvekre mutató hivatkozásokat tartalmaz, és meghatározza a paramétert, amellyel kapcsolatot létesíthet IoT Hub a MQTT vagy a MQTT keresztül a web Sockets protokoll használatával.

| Nyelv | MQTT protokoll paramétere | MQTT a webes szoftvercsatornák protokoll paramétereként
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | Azure-IOT-Device-mqtt. Mqtt | Azure-IOT-Device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable&preserve-view=true). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet&preserve-view=true). Mqtt | A TransportType. Mqtt visszaesik a MQTT-ra, ha a MQTT meghibásodik. Ha csak a webes szoftvercsatornák MQTT szeretné megadni, használja a TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Alapértelmezés szerint a MQTT támogatja | Hozzáadás a `websockets=True` -ügyfél létrehozásához a hívásban |

A következő kódrészlet azt mutatja be, hogyan adható meg a MQTT over web Sockets protokoll az Azure IoT Node.js SDK használatakor:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Az alábbi kódrészlet bemutatja, hogyan adhatja meg a MQTT a web Sockets protokollon keresztül az Azure IoT Python SDK használatával:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Alapértelmezett életben tartási időkorlát

Annak biztosítása érdekében, hogy az ügyfél/IoT Hub-kapcsolatok életben maradjanak, a szolgáltatás és az ügyfél is rendszeresen küld egy *életben tartási* pingelést. A IoT SDK-t használó ügyfél a következő táblázatban meghatározott időközönként elküld egy életben tartást:

|Nyelv  |Alapértelmezett életben tartási időköz  |Konfigurálható  |
|---------|---------|---------|
|Node.js     |   180 másodperc      |     No    |
|Java     |    230 másodperc     |     No    |
|C     | 240 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 másodperc |  No   |

Az [MQTT specifikáció](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)után a IoT hub életben tartási pingelési intervalluma 1,5 alkalommal az ügyfél életben tartási értéke. A IoT Hub azonban korlátozza a kiszolgálóoldali időtúllépési korlátot 29,45 percre (1767 másodpercre), mert az összes Azure-szolgáltatás az Azure Load Balancer TCP üresjárati időkorlátjának (29,45 perc) van kötve. 

Egy Java SDK-t használó eszköz például a Keep-Alive ping üzenetet küldi, majd elveszíti a hálózati kapcsolatot. 230 másodperccel később az eszköz elmulasztja a Keep-Alive pingelést, mert offline állapotban van. IoT Hub azonban nem kapcsolja be azonnal a kapcsolatot – megvárja, `(230 * 1.5) - 230 = 115` amíg az eszköz leválasztása megszakadt az 404104-es [DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)hiba miatt. 

A maximálisan beállítható ügyfél életben tartási értéke `1767 / 1.5 = 1177` másodperc. Minden forgalom alaphelyzetbe állítja a Keep-Alive-t. Egy sikeres SAS-jogkivonat frissítése például alaphelyzetbe állítja a Keep-Alive állapotot.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Eszköz alkalmazás migrálása a AMQP-ből a MQTT-be

Ha az [eszköz SDK](https://github.com/Azure/azure-iot-sdks)-kat használja, a AMQP-ről MQTT-re való váltáshoz a protokoll paraméterét kell módosítania az ügyfél inicializálása során, ahogy azt korábban már említettük.

Ha ezt teszi, ellenőrizze a következő elemeket:

* A AMQP számos feltételnél hibákat ad vissza, míg a MQTT megszakítja a kapcsolódást. Ennek eredményeképpen előfordulhat, hogy a kivételek kezelésére szolgáló logikának valamilyen módosításra van szüksége.

* A MQTT nem támogatja az *elutasítási* műveleteket a [felhőből az eszközre irányuló üzenetek](iot-hub-devguide-messaging.md)fogadásakor. Ha a háttér-alkalmazásnak választ kell kapnia az eszköz alkalmazástól, érdemes lehet [közvetlen módszereket](iot-hub-devguide-direct-methods.md)használni.

* A AMQP nem támogatott a Python SDK-ban.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Példa C-ben az Azure IoT SDK-val nem rendelkező MQTT használatával

A [IOT MQTT-minta adattárában](https://github.com/Azure-Samples/IoTMQTTSample)talál néhány C/C++ bemutató projektet, amely bemutatja, hogyan küldhet telemetria-üzeneteket, és hogyan fogadhat eseményeket egy IoT hub használatával az Azure IOT C SDK használata nélkül. 

Ezek a minták az Eclipse Mosquitto függvénytár használatával küldenek üzeneteket az IoT hub-ban megvalósított MQTT-közvetítőnek.

Ha szeretné megtudni, hogyan alakíthatja át a mintákat az [Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) konvenciók használatára, tekintse meg a következőt: [oktatóanyag – a IoT Plug and Play MQTT fejlesztése](../iot-pnp/tutorial-use-mqtt.md).

A tárház tartalma:

**Windows esetén:**

* TelemetryMQTTWin32: olyan kódot tartalmaz, amely telemetria üzenetet küld egy Azure IoT hub-ra, amely Windows rendszerű gépen lett létrehozva és futtatva.

* SubscribeMQTTWin32: olyan kódot tartalmaz, amely egy Windows rendszerű gépen lévő adott IoT hub eseményeire fizet elő.

* DeviceTwinMQTTWin32: olyan kódot tartalmaz, amely lekérdezi és előfizet egy eszköznek az Azure IoT hub-ban egy Windows rendszerű gépen lévő eszközének kettős eseményeire.

* PnPMQTTWin32: olyan kódot tartalmaz, amely a IoT Plug and Play telemetria üzenetet küld egy Azure IoT hub-ra, amely Windows rendszerű gépen lett létrehozva és futtatva. További információk a [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)

**Linux esetén:**

* MQTTLinux: kód-és Build-szkriptet tartalmaz a Linuxon való futtatáshoz (WSL, Ubuntu és Raspbian).

* LinuxConsoleVS2019: ugyanazt a kódot tartalmazza, de egy VS2019-projekt WSL (Windows Linux alrendszer). Ez a projekt lehetővé teszi a Linux lépésről lépésre futó kód hibakeresését a Visual studióból.

**Mosquitto_pub esetén:**

Ez a mappa két, a Mosquitto.org által biztosított mosquitto_pub segédprogram eszközzel használható mintát tartalmaz.

* Mosquitto_sendmessage: egyszerű szöveges üzenet küldése egy eszközként működő Azure IoT hub-nak.

* Mosquitto_subscribe: az Azure IoT hub-ban előforduló események megtekintéséhez.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>A MQTT protokoll használata közvetlenül (eszközként)

Ha egy eszköz nem tudja használni az eszköz SDK-kat, akkor továbbra is csatlakozhat a nyilvános eszközök végpontjaihoz a 8883-es porton az MQTT protokoll használatával. A **csatlakoztatási** csomagban az eszköznek a következő értékeket kell használnia:

* A **ClientId** mezőben használja a **deviceId**-t.

* A **username (Felhasználónév** ) mezőben használja a következőt: `{iothubhostname}/{device_id}/?api-version=2018-06-30` , ahol a `{iothubhostname}` az IoT hub teljes CNAME értéke.

    Ha például az IoT hub neve **contoso.Azure-Devices.net** , és ha az eszköz neve **MyDevice01**, a teljes **Felhasználónév** mezőben a következőket kell tartalmaznia:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* A **Password (jelszó** ) mezőben használjon sas-tokent. Az SAS-token formátuma megegyezik a HTTPS és a AMQP protokollok esetében is:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Ha X. 509 tanúsítványalapú hitelesítést használ, az SAS-jogkivonat jelszavai nem szükségesek. További információ: az [X. 509 Biztonság beállítása az Azure-ban IoT hub](iot-hub-security-x509-get-started.md) és kövesse a kód utasításait a [TLS/SSL-konfiguráció szakaszban](#tlsssl-configuration).

  Az SAS-tokenek létrehozásával kapcsolatos további információkért tekintse meg a [IoT hub biztonsági jogkivonatok használata](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)című témakör eszközét.

  A tesztelés során a Visual Studio Code-hoz készült platformfüggetlen [Azure IoT-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy a CLI-bővítmény parancsát is használhatja az [IoT hub generált-sas-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token&preserve-view=true) használatával, amellyel gyorsan létrehozhat egy sas-tokent, amelyet másolhat és beilleszthet a saját kódjába.

### <a name="for-azure-iot-tools"></a>Azure IoT-eszközökhöz

1. Bontsa ki az **Azure IOT hub-eszközök** fület a Visual Studio Code bal alsó sarkában.
  
2. Kattintson a jobb gombbal az eszközre, és válassza a **sas-jogkivonat előállítása az eszközhöz** lehetőséget.
  
3. Adja meg a **lejárati időt** , és nyomja le az ENTER billentyűt.
  
4. A rendszer létrehoz egy SAS-tokent, és átmásolja a vágólapra.

   A generált SAS-jogkivonat a következő szerkezettel rendelkezik:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Ennek a tokennek a része a MQTT használatával való kapcsolódáshoz használt **jelszó** mező:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

A MQTT csatlakoztatásához és a csomagok leválasztásához IoT Hub a **műveletek figyelése** csatornán eseményt ad ki. Ez az esemény további információkat tartalmaz, amelyek segíthetnek a kapcsolódási problémák elhárításában.

Az alkalmazás megadhat **egy üzenetet** a **csatlakoztatási** csomagban. Az alkalmazásnak `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}` a téma nevének meg kell egyeznie, hogy  a **rendszer** telemetria üzenetként továbbítsa az üzeneteket. Ebben az esetben, ha a hálózati kapcsolat be van zárva, de a **leválasztási** csomag korábban nem érkezett meg az eszközről, a IoT hub elküldi a telemetria csatornához a **kapcsolódási** csomagban **megadott üzenetet.** Az telemetria csatorna az alapértelmezett **események** végpontja vagy IoT hub útválasztás által definiált egyéni végpont lehet. Az üzenet a **iothub-MessageType** tulajdonsággal rendelkezik, amelynek a értéke hozzá **lesz** rendelve.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>A MQTT protokoll használata közvetlenül (modulként)

Ha egy modul-identitás használatával csatlakozik a MQTT-hez IoT Hubhoz, az eszközhöz hasonlóan (lásd: a [MQTT protokoll használata közvetlenül eszközként](#using-the-mqtt-protocol-directly-as-a-device)), de a következőket kell használnia:

* Állítsa be az ügyfél-azonosítót a következőre: `{device_id}/{module_id}` .

* Ha felhasználónévvel és jelszóval végzi a hitelesítést, állítsa be a felhasználónevet, `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` és használja a modul identitásához társított sas-tokent jelszóként.

* A `devices/{device_id}/modules/{module_id}/messages/events/` telemetria közzétételéhez használható témakörként.

* Használja a következőt: `devices/{device_id}/modules/{module_id}/messages/events/` .

* A Twin GET és a PATCH témakörök azonosak a modulok és az eszközök esetében.

* A Twin status témakör azonos a modulok és az eszközök esetében.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguráció

Ahhoz, hogy az ügyfél közvetlenül használhassa a MQTT protokollt, a TLS/SSL protokollon keresztül *kell* csatlakoznia. Csatlakozási hibák esetén a lépés kihagyása sikertelen.

A TLS-kapcsolat létesítéséhez előfordulhat, hogy le kell töltenie és hivatkoznia kell a DigiCert Baltimore főtanúsítványára. Ez a tanúsítvány az Azure által a kapcsolatok biztonságossá tételéhez használt tanúsítvány. Ezt a tanúsítványt az [Azure-IOT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) tárházban találja. A tanúsítványokról további információt a [Digicert webhelyén](https://www.digicert.com/digicert-root-certificates.htm)találhat.

Egy példa arra, hogyan valósítható meg ez a [PAHO MQTT Library](https://pypi.python.org/pypi/paho-mqtt) Python-verziójának használatával az Eclipse Foundation által az alábbihoz hasonló módon.

Először telepítse a PAHO könyvtárat a parancssori környezetből:

```cmd/sh
pip install paho-mqtt
```

Ezután implementálja az ügyfelet egy Python-parancsfájlban. Cserélje le a helyőrzőket a következőképpen:

* `<local path to digicert.cer>` egy helyi fájl elérési útja, amely a DigiCert Baltimore főtanúsítványát tartalmazza. Ezt a fájlt úgy is létrehozhatja, hogy átmásolja a tanúsítvány [adatait a (](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) z) c Azure IoT SDK-ban. a sor tartalmazza a sorokat, `-----BEGIN CERTIFICATE-----` és eltávolítja a `-----END CERTIFICATE-----` `"` jeleket az egyes sorok elején és végén, és eltávolítja az `\r\n` összes sor végén lévő karaktereket.

* `<device id from device registry>` az IoT hub-hoz hozzáadott eszköz azonosítója.

* `<generated SAS token>` a a cikkben korábban ismertetett módon létrehozott SAS-jogkivonat.

* `<iot hub name>` az IoT hub neve.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Az eszköz tanúsítványának használatával történő hitelesítéshez frissítse a fenti kódrészletet a következő módosításokkal (lásd: [X. 509 hitelesítésszolgáltatói tanúsítvány beszerzése](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) a tanúsítványalapú hitelesítés előkészítéséhez):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Eszközről a felhőbe irányuló üzenetek küldése

A sikeres kapcsolódást követően az eszközök üzeneteket küldhetnek IoT Hub a `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}` a **témakör neveként**. Az `{property_bag}` elem lehetővé teszi, hogy az eszköz további tulajdonságokkal rendelkező üzeneteket küldjön URL-kódolású formátumban. Például:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez `{property_bag}` az elem ugyanazt a kódolást használja, mint a lekérdezési karakterláncok a https protokollban.

Az alábbi lista a IoT Hub implementáció-specifikus viselkedéseit sorolja fel:

* A IoT Hub nem támogatja a QoS 2 üzeneteit. Ha egy eszköz egy, a **QoS 2** eszközzel közzétett üzenetet tesz közzé, IoT hub zárja be a hálózati kapcsolatokat.

* A IoT Hub nem tartja fenn az üzenetek megőrzését. Ha egy eszköz a **megőrzési** jelzővel ellátott üzenetet küld az 1 értékre, IoT hub hozzáadja a **mqtt-megőrzött** alkalmazás tulajdonságot az üzenethez. Ebben az esetben a megőrzött üzenet megőrzése helyett a IoT Hub átadja a háttérbeli alkalmazásnak.

* A IoT Hub eszközön csak egy aktív MQTT-kapcsolatok támogatottak. Az azonos eszköz AZONOSÍTÓjának nevében az új MQTT-kapcsolatok IoT Hub eldobni a meglévő kapcsolatokat, és az 400027-es **ConnectionForcefullyClosedOnNewConnection** bekerülnek IoT hub naplókba


További információ: [üzenetküldési fejlesztői útmutató](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Felhőből az eszközre irányuló üzenetek fogadása

Az üzenetek IoT Hubból való fogadásához az eszköznek a használatával kell előfizetnie `devices/{device_id}/messages/devicebound/#` egy **témakör-szűrőként**. A témakör szűrője többszintű helyettesítő karaktert használ, amely `#` lehetővé teszi, hogy az eszköz további tulajdonságokat kapjon a témakör nevében. IoT Hub nem teszi lehetővé a `#` vagy `?` helyettesítő karakterek használatát az altémakörök szűréséhez. Mivel IoT Hub nem egy általános célú pub-sub Messaging Broker, csak a dokumentált témakörök nevét és a témakör szűrőit támogatja.

Az eszköz nem kap üzeneteket a IoT Hub addig, amíg sikeresen előfizette az eszközre vonatkozó végpontot, amelyet a `devices/{device_id}/messages/devicebound/#` témakör szűrője képvisel. Az előfizetés létrejötte után az eszköz megkapja a felhőből az eszközre irányuló üzeneteket, amelyeket az előfizetés időpontja után küldtek el. Ha az eszköz a **CleanSession** jelzővel csatlakozik **0-ra**, az előfizetés a különböző munkamenetek között marad meg. Ebben az esetben a következő alkalommal, amikor az eszköz a **CleanSession** -hez csatlakozik, a kapcsolat megszakadása közben minden, a számára elküldött függő üzenetet fogad. Ha az eszköz a **CleanSession** jelzőt **1-re** állítja, akkor a IoT hub addig nem kap üzenetet, amíg előfizet az eszköz-végpontra.

IoT Hub kézbesíti az üzeneteket a **témakör nevével** `devices/{device_id}/messages/devicebound/` , vagy `devices/{device_id}/messages/devicebound/{property_bag}` Ha üzenet tulajdonságai vannak. `{property_bag}` URL-kódolású kulcs/érték párokat tartalmaz az üzenet tulajdonságaiban. Csak az alkalmazás tulajdonságai és a felhasználó által beállítható Rendszertulajdonságok (például **messageId** vagy **correlationId**) szerepelnek a tulajdonság táskájában. A Rendszertulajdonságok nevei előtaggal rendelkeznek **$** , az alkalmazás tulajdonságai az eredeti tulajdonságnév előtag nélküli nevet használnak. A tulajdonság táska formátumával kapcsolatos további részletekért tekintse meg az [eszközről a felhőbe irányuló üzenetek küldését](#sending-device-to-cloud-messages)ismertető témakört.

A felhőből az eszközre irányuló üzenetekben a tulajdonságok táskájában lévő értékek a következő táblázatban látható módon jelennek meg:

| Tulajdonság értéke | Képviselet | Description |
|----|----|----|
| `null` | `key` | Csak a kulcs jelenik meg a tulajdonság táskájában |
| üres karakterlánc | `key=` | A kulcsot, és nem értékkel rendelkező egyenlőségjelet kell írni |
| nem null értékű, nem üres érték | `key=value` | A kulcsot, majd egy egyenlőségjelet és egy értéket. |

Az alábbi példa egy olyan tulajdonságot mutat be, amely három alkalmazás-tulajdonságot tartalmaz: a **prop1** értékkel `null` . **prop2**, üres karakterlánc (""); és **prop3** "a string" értékkel.

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Ha egy eszköz egy, a **QoS 2-es** verzióra való előfizetést tartalmaz, a IoT hub maximális QoS-szintet biztosít a **SUBACK** -csomagban. Ezután a IoT Hub a QoS 1 használatával továbbítja az üzeneteket az eszköznek.

## <a name="retrieving-a-device-twins-properties"></a>Az eszközök Twin tulajdonságainak beolvasása

Először egy eszköz előfizet a szolgáltatásra `$iothub/twin/res/#` , hogy megkapja a művelet válaszait. Ezután egy üres üzenetet küld a témakörnek, amely a `$iothub/twin/GET/?$rid={request id}` **kérelem azonosítójának** feltöltésére szolgál. A szolgáltatás ezután elküld egy válaszüzenetet, amely tartalmazza az eszköz Twin-adatokat a témakörben `$iothub/twin/res/{status}/?$rid={request id}` , a kérelem **azonosítójának** használatával.

A kérelem azonosítója bármely érvényes érték lehet az üzenet tulajdonságának értékeként, a [IoT hub üzenetkezelés fejlesztői útmutatója](iot-hub-devguide-messaging.md)szerint, és az állapot egész számként van érvényesítve.

A válasz törzse a Twin eszköz tulajdonságok szakaszát tartalmazza, ahogy az a következő példában látható:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

A lehetséges állapotkódok a következők:

|Állapot | Leírás |
| ----- | ----------- |
| 200 | Siker |
| 429 | Túl sok kérés (szabályozott) a [IoT hub szabályozása](iot-hub-devguide-quotas-throttling.md) szerint |
| 5 * * | Kiszolgálóhibák |

További információ: az [eszközök ikrek fejlesztői útmutatója](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>A Device Twin szolgáltatás jelentett tulajdonságainak frissítése

A jelentett tulajdonságok frissítéséhez az eszköz egy, a kijelölt MQTT-témakörben közzétett kiadványon keresztül bocsát ki IoT Hub kérelmet. A kérelem feldolgozása után IoT Hub válaszol a frissítési művelet sikerességi vagy meghibásodási állapotára egy kiadványon keresztül egy másik témakörbe. Ez a témakör előfizethet az eszközön, hogy értesítést kapjon a kettős frissítési kérelem eredményéről. Az ilyen típusú kérés/válasz interakció MQTT-ben való megvalósításához az `$rid` eszköz által a frissítési kérelemben eredetileg megadott kérelem-azonosító () fogalmát használjuk ki. Ez a kérelem-azonosító a IoT Hub válaszában is szerepel, hogy az eszköz korrelálja az adott korábbi kérelemre adott választ.

A következő szakasz azt ismerteti, hogyan frissíti az eszköz a jelentett tulajdonságokat az eszköz két IoT Hubjában:

1. Az eszköznek először elő kell fizetnie a `$iothub/twin/res/#` témakörre, hogy megkapja a művelet válaszait IoT hub.

2. Az eszköz egy üzenetet küld, amely tartalmazza az eszköz kettős frissítését a `$iothub/twin/PATCH/properties/reported/?$rid={request id}` témakörhöz. Ez az üzenet tartalmazza a **kérelem azonosítójának** értékét.

3. A szolgáltatás ezután egy válaszüzenetet küld, amely tartalmazza a jelentett tulajdonságok gyűjtemény új ETag értékét a következő témakörben: `$iothub/twin/res/{status}/?$rid={request id}` . Ez a válaszüzenet ugyanazt a **kérés-azonosítót** használja, mint a kérelem.

A kérelem üzenet törzse tartalmaz egy JSON-dokumentumot, amely a jelentett tulajdonságok új értékeit tartalmazza. A JSON-dokumentum minden tagja frissíti vagy hozzáadja a megfelelő tagot az eszköz Twin dokumentumához. Egy tag, amely `null` törli a tagot a befoglaló objektumból. Például:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

A lehetséges állapotkódok a következők:

|Állapot | Leírás |
| ----- | ----------- |
| 204 | Sikeres (a rendszer nem ad vissza tartalmat) |
| 400 | Hibás kérelem. Helytelen formátumú JSON |
| 429 | Túl sok kérés (szabályozott) a [IoT hub szabályozása](iot-hub-devguide-quotas-throttling.md) szerint |
| 5 * * | Kiszolgálóhibák |

Az alábbi Python-kódrészlet a következőt mutatja be: MQTT (PAHO MQTT-ügyfél használata)

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

A két jelentett tulajdonság frissítési műveletének sikeressége után a IoT Hub közzétételére vonatkozó üzenet a következő témakörben fog megjelenni: `$iothub/twin/res/204/?$rid=1&$version=6` , ahol `204` a a sikert jelző állapotkód, az `$rid=1` eszköz által a kódban megadott kérelem-azonosítónak felel meg, és `$version` megfelel az eszköz ikrek által a frissítés után a jelentett tulajdonságok szakaszának.

További információ: az [eszközök ikrek fejlesztői útmutatója](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>A kívánt tulajdonságok frissítési értesítéseinek fogadása

Ha egy eszköz csatlakoztatva van, IoT Hub értesítést küld a témakörnek `$iothub/twin/PATCH/properties/desired/?$version={new version}` , amely tartalmazza a megoldás hátterében végrehajtott frissítés tartalmát. Például:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

A tulajdonságok frissítéseihez hasonlóan `null` az értékek azt is jelentik, hogy a JSON-objektum tagja törölve van. Azt is vegye figyelembe, hogy a `$version` Twin tulajdonság kívánt tulajdonságok szakaszának új verzióját jelzi.

> [!IMPORTANT]
> IoT Hub csak akkor hoz létre módosítási értesítéseket, ha az eszközök csatlakoztatva vannak. Győződjön meg arról, hogy az [eszköz újrakapcsolódási folyamata](iot-hub-devguide-device-twins.md#device-reconnection-flow) megtartja a kívánt tulajdonságokat a IoT hub és az eszköz alkalmazás között szinkronizálva.

További információ: az [eszközök ikrek fejlesztői útmutatója](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Válasz közvetlen metódusra

Először is elő kell fizetnünk az eszközre `$iothub/methods/POST/#` . IoT Hub metódus-kérelmeket küld a témakörnek `$iothub/methods/POST/{method name}/?$rid={request id}` , érvényes JSON-val vagy üres törzstel.

A válaszadáshoz az eszköz egy érvényes JSON-vagy üres törzsű üzenetet küld a témakörnek `$iothub/methods/res/{status}/?$rid={request id}` . Ebben az üzenetben a **kérelem azonosítójának** egyeznie kell a kérelem üzenetében szereplővel, és az **állapotnak** egész számnak kell lennie.

További információ: a [közvetlen módszer fejlesztői útmutatója](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Végső megfontolásként, ha testre kell szabnia a MQTT protokoll viselkedését a felhős oldalon, tekintse át az [Azure IoT Protocol Gatewayt](iot-hub-protocol-gateway.md). Ez a szoftver lehetővé teszi egy olyan nagy teljesítményű egyéni protokoll-átjáró üzembe helyezését, amely közvetlenül a IoT Hub. Az Azure IoT Protocol Gateway lehetővé teszi, hogy testreszabja az eszköz protokollját, hogy az rozsdaövezetek rehabilitálása MQTT-telepítések vagy más egyéni protokollok is megfeleljenek. Ez a megközelítés azonban megköveteli, hogy egy egyéni protokoll-átjárót futtasson és működtessen.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MQTT protokollról, tekintse meg a [MQTT dokumentációját](https://mqtt.org/).

Ha többet szeretne megtudni a IoT Hub központi telepítésének megtervezéséről, olvassa el a következőt:

* [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsolutions.com/)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [Összehasonlítás Event Hubs](iot-hub-compare-event-hubs.md)
* [Skálázás, HA és DR](iot-hub-scaling.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)
