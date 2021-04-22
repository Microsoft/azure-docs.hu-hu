---
title: Az Azure IoT Hub MQTT támogatási | Microsoft Docs
description: Az MQTT protokollal IoT Hub eszköz felé néző végponthoz csatlakozó eszközök támogatása. Az Azure IoT eszköz-SDK-k beépített MQTT-támogatásával kapcsolatos információkat tartalmaz.
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
ms.openlocfilehash: 077b4ea6c7d4506e29673cbb4d6d89a92657b9c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873684"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunikáció az IoT Hubbal az MQTT protokoll használatával

IoT Hub lehetővé teszi, hogy az eszközök kommunikáljanak IoT Hub-végpontokkal a következő használatával:

* [MQTT v3.1.1 a](https://mqtt.org/) 8883-as porton
* MQTT v3.1.1 WebSocketen keresztül a 443-as porton.

Az IoT Hub nem teljes szolgáltatásokat nyújtó MQTT-közvetítő, és nem támogatja az MQTT 3.1.1-es standard verziójában megadott összes viselkedést. Ez a cikk bemutatja, hogyan kommunikálhatnak az eszközök a támogatott MQTT-viselkedésekkel a IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az eszköz és a IoT Hub kommunikációját TLS/SSL használatával kell biztosítani. Ezért a IoT Hub nem támogatja a nem biztonságos kapcsolatokat az 1883-as porton keresztül.

## <a name="connecting-to-iot-hub"></a>Csatlakozás IoT Hub

Az eszközök az MQTT protokollal csatlakozhatnak egy IoT Hubhoz az alábbi lehetőségek bármelyikével.

* Kódtárak az [Azure IoT SDK-kban.](https://github.com/Azure/azure-iot-sdks)
* Az MQTT protokoll közvetlenül.

Az MQTT-port (8883) blokkolva van számos vállalati és oktatási hálózati környezetben. Ha nem tudja megnyitni a 8883-as portot a tűzfalon, javasoljuk, hogy az MQTT-t használja a webs foglalatokkal. A webes szoftvercsatornákon keresztüli MQTT a 443-as porton keresztül kommunikál, amely szinte mindig nyitva van hálózati környezetekben. Ha meg szeretne ismerkedni az MQTT és az MQTT Web Sockets protokollon keresztüli megadásával az Azure IoT SDK-k használata esetén, olvassa el az [Eszköz-SDK-k használatacímeket.](#using-the-device-sdks)

## <a name="using-the-device-sdks"></a>Az eszköz-SDK-k használata

[Az](https://github.com/Azure/azure-iot-sdks) MQTT protokollt támogató eszköz-SDK-k Java, Node.js, C, C# és Python nyelven érhetők el. Az eszköz-SDK-k IoT Hub kapcsolati sztringet használják az IoT Hubbal való kapcsolat létesítéhez. Az MQTT protokoll használatának érdekében az ügyfél-protokoll paraméterét **MQTT -re kell állítani.** Az MQTT-t webs foglalaton keresztül is megadhatja az ügyfél-protokoll paraméterben. Alapértelmezés szerint az eszköz-SDK-k egy IoT Hub-hez csatlakoznak, és a **CleanSession** **jelző 0-ra** van állítva, és a **QoS 1-et** használják az IoT Hubbal való üzenetcseréhez. Bár a **QoS 0** konfigurálható a gyorsabb üzenetváltáshoz, vegye figyelembe, hogy a kézbesítés nincs garantálva és nem nyugtázva. Emiatt a **QoS 0-t** gyakran "tűz és felejtő" néven is nevezik.

Amikor egy eszköz csatlakozik egy IoT Hubhoz, az eszköz-ADATTK-k olyan metódusokat biztosítanak, amelyek lehetővé teszik az eszköz számára az üzenetek IoT Hubbal való cseréjét.

Az alábbi táblázat hivatkozásokat tartalmaz az egyes támogatott nyelvek kódmináira, és megadja az IoT Hub-hoz való csatlakozáshoz használt paramétert az MQTT vagy az MQTT over Web Sockets protokoll használatával.

| Nyelv | MQTT protokollparaméter | MQTT Web Sockets protokollon keresztüli paraméter
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype). Mqtt | Ha az MQTT meghibásodik, a TransportType.Mqtt visszaesik az MQTT-be a webes szoftvercsatornákon keresztül. Ha csak webes szoftvercsatornán keresztüli MQTT-t ad meg, használja a TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Alapértelmezés szerint támogatja az MQTT-t | Az `websockets=True` ügyfél létrehozásához adja hozzá a következőt a híváshoz: |

A következő töredék bemutatja, hogyan adhatja meg az MQTT-t a Web Sockets protokollon keresztül az Azure IoT Node.js SDK használatakor:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

A következő töredék bemutatja, hogyan adhatja meg az MQTT-t a Web Sockets protokollon keresztül az Azure IoT Python SDK használatakor:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Az életben tartás alapértelmezett időkorlátja

Annak érdekében, hogy az ügyfél/IoT Hub kapcsolat megmarad, *a* szolgáltatás és az ügyfél is rendszeresen küld egymásnak életben tartás pinget. Az IoT SDK-t használó ügyfél életben tartás adatokat küld az alábbi táblázatban meghatározott időközönként:

|Nyelv  |Alapértelmezett életben tartás időköze  |Konfigurálható  |
|---------|---------|---------|
|Node.js     |   180 másodperc      |     No    |
|Java     |    230 másodperc     |     No    |
|C     | 240 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 másodperc |  No   |

Az [MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)specifikációja alapján a IoT Hub életben tartás pingelési időköze az ügyfél életben tartás értékének 1,5-szer nagyobb. A IoT Hub azonban 29,45 percre (1767 másodpercre) korlátozza a maximális kiszolgálóoldali időtúllépést, mivel minden Azure-szolgáltatás az Azure Load Balancer TCP üresjárati időtúllépéséhez van kötve, ami 29,45 perc. 

A Java SDK-t használó eszközök például az életben tartás pinget küldik, majd elveszítik a hálózati kapcsolatot. 230 másodperccel később az eszköz kihagyja az életben tartás pingelését, mert offline állapotban van. A IoT Hub azonban nem zárja be azonnal a kapcsolatot – még néhány másodpercet vár, mielőtt leválasztja az eszközt a `(230 * 1.5) - 230 = 115` [404104 DeviceConnectionClosedRemotely hibával.](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) 

Az ügyfél életben tartás számára beállítható maximális értéke `1767 / 1.5 = 1177` másodperc. Minden forgalom visszaállítja az életben tartás hatóát. A sikeres SAS-jogkivonat-frissítés például visszaállítja az életben tartás alaphelyzetbe állítását.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Eszközalkalmazás áttelepítése AMQP-ről MQTT-re

Ha az eszközOLDALI-okat [használja,](https://github.com/Azure/azure-iot-sdks)az AMQP-ről MQTT-be való váltáshoz a protokollparamétert az ügyfél inicializálásában kell megváltoztatni a korábban már említettek szerint.

Ha így tesz, ellenőrizze a következőket:

* Az AMQP számos feltétel esetén hibát ad vissza, az MQTT pedig megszakítja a kapcsolatot. Emiatt előfordulhat, hogy a kivételkezelési logikához módosításokra van szükség.

* Az MQTT nem  támogatja a felhőből az eszközre küldött üzenetek fogadatáskor a műveletek [elutasítását.](iot-hub-devguide-messaging.md) Ha a háttéralkalmazásnak választ kell kapnia az eszközalkalmazástól, fontolja meg a közvetlen [metódusok alkalmazását.](iot-hub-devguide-direct-methods.md)

* Az AMQP nem támogatott a Python SDK-ban.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Példa A C-ben az MQTT Azure IoT SDK nélküli használatával

Az [IoT MQTT-mintaadattárban](https://github.com/Azure-Samples/IoTMQTTSample)talál néhány C/C++ bemutatóprojektet, amelyek bemutatják, hogyan küldhet telemetriai üzeneteket, és fogadhat eseményeket egy IoT Hubbal az Azure IoT C SDK használata nélkül. 

Ezek a minták az Eclipse Mosquitto kódtár használatával küldenek üzeneteket az IoT Hubban megvalósított MQTT Brokernek.

A minták [Azure IoT Plug and Play-konvenciókhoz](../iot-pnp/overview-iot-plug-and-play.md) való alkalmazkodásával az Oktatóanyag – [Az MQTT használata IoT Plug and Play eszköz ügyfelének fejlesztéséhez.](../iot-pnp/tutorial-use-mqtt.md)

Ez az adattár a következő adatokat tartalmazza:

**Windows esetén:**

* TelemetryMQTTWin32: olyan kódot tartalmaz, amely telemetriai üzenetet küld egy Windows rendszerű gépen készült És futtatott Azure IoT Hubra.

* SubscribeMQTTWin32: egy kódot tartalmaz, amely előfizet egy Adott IoT Hub eseményeire egy Windows rendszerű gépen.

* DeviceTwinMQTTWin32: olyan kódot tartalmaz, amely lekérdezi és előfizet egy Eszköz ikereszköz-eseményeit az Azure IoT Hubban egy Windows rendszerű gépen.

* PnPMQTTWin32: olyan kódot tartalmaz, amely IoT Plug and Play-eszközképességekkel telemetriai üzenetet küld egy Azure IoT Hubra, amely egy Windows rendszerű gépen készült és futtatható. További információ az [IoT-Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)

**Linux rendszeren:**

* MQTTLinux: linuxos futtatáshoz használható kódot és buildszkent tartalmaz (az eddig tesztelt WSL, Ubuntu és Raspbian).

* LinuxConsoleVS2019: ugyanazt a kódot tartalmazza, de egy VS2019-projektben, amely a WSL-t célozza (Windows Linux alrendszer). Ez a projekt lehetővé teszi a Linuxon futó kód részletes hibakeresését a Visual Studio.

**További mosquitto_pub:**

Ez a mappa két mintaparancsot tartalmaz, amelyek a mosquitto_pub által biztosított segédprogrammal Mosquitto.org.

* Mosquitto_sendmessage: egyszerű szöveges üzenet küldése egy eszközként működő Azure IoT Hubra.

* Mosquitto_subscribe: az Azure IoT Hubban előforduló eseményeket.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Az MQTT protokoll közvetlen használata (eszközként)

Ha egy eszköz nem tudja használni az eszköz-SDK-t, akkor is tud csatlakozni a nyilvános eszközvégponthoz az MQTT protokoll használatával a 8883-as porton. A **CONNECT csomagban** az eszköznek a következő értékeket kell használnia:

* A **ClientId mezőben** használja a **deviceId (eszközazonosító) adatokat.**

* A **Felhasználónév mezőben** használja a következőt: `{iothubhostname}/{device_id}/?api-version=2018-06-30` , ahol az az `{iothubhostname}` IoT Hub teljes CName-neve.

    Ha például az IoT Hub neve **contoso.azure-devices.net,** és az eszköz neve **MyDevice01,** a teljes **Felhasználónév** mezőnek a következőket kell tartalmaznia:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Erősen ajánlott az API-version (API-verzió) használata a mezőben. Ellenkező esetben nem várt működést okozhat. 
    
* A Jelszó **mezőben** használjon SAS-jogkivonatot. Az SAS-jogkivonat formátuma megegyezik a HTTPS és az AMQP protokoll formátuma is:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X.509-tanúsítványhitelesítés használata esetén nincs szükség SAS-jogkivonat-jelszavakra. További információ: [Az X.509](iot-hub-security-x509-get-started.md) biztonságának beállítása a Azure IoT Hub és kövesse a [TLS/SSL konfigurációs szakaszának kód utasításait.](#tlsssl-configuration)

  Az SAS-jogkivonatok létrehozásáról további információt a Using IoT Hub security tokens (Biztonsági jogkivonatok [használata) című IoT Hub tartalmaz.](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

  A tesztelés során a platformfüggetlen [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy az [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) CLI-bővítményparancs használatával gyorsan létrehozhat egy SAS-jogkivonatot, amely a saját kódba másolhatja és beillesztheti azt.

### <a name="for-azure-iot-tools"></a>A Azure IoT Tools

1. Bontsa ki az **AZURE IOT HUB DEVICES (AZURE IOT HUB-ESZKÖZÖK)** lapot a Code Visual Studio bal alsó sarkában.
  
2. Kattintson a jobb gombbal az eszközre, és válassza **a SAS-jogkivonat létrehozása az eszközhöz lehetőséget.**
  
3. Állítsa be **a lejárati időt,** és nyomja le az Enter billentyűt.
  
4. A rendszer létrehoz egy SAS-jogkivonatot, és átmásolja a vágólapra.

   A létrehozott SAS-jogkivonat szerkezete a következő:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Ennek a jogkivonatnak a  Jelszó mezőben az MQTT használatával való csatlakozáshoz a következő része használható:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Az MQTT-csatlakozás és a csomagok leválasztása esetén a IoT Hub az **Operations Monitoring csatornán** egy eseményt ad ki. Ez az esemény további információkat tartalmaz, amelyek segíthetnek a csatlakozási problémák elhárításában.

Az eszközalkalmazás megadhat egy **Will üzenetet** a **CONNECT csomagban.** Az eszközalkalmazásnak a vagy a nevet kell használnia Will-témakör neveként a telemetriai üzenetként továbbítható `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}`  **Will-üzenetek** meghatározásához. Ebben az esetben, ha a hálózati kapcsolat be van zárva, de korábban nem érkezett **DISCONNECT** csomag az eszközről, akkor a IoT Hub elküldi a **CONNECT** csomagban megadott **Will** üzenetet a telemetriai csatornára. A telemetriai csatorna lehet az alapértelmezett **Események** végpont, vagy egy egyéni végpont, amelyet az útválasztás IoT Hub definiál. Az üzenethez az **iothub-MessageType** tulajdonság van hozzárendelve **Will** értékkel.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Az MQTT protokoll közvetlen használata (modulként)

Ha az IoT Hub MQTT-kapcsolaton keresztül csatlakozik egy modulidentitás használatával, az eszközhöz hasonló (az [MQTT](#using-the-mqtt-protocol-directly-as-a-device)protokoll közvetlen eszközként való használatával kapcsolatban szóló szakaszban leírtak szerint), de a következőket kell használnia:

* Állítsa az ügyfél-azonosítót a `{device_id}/{module_id}` következőre: .

* Ha felhasználónévvel és jelszóval hitelesít, állítsa a felhasználónevet a következőre: , és használja a modulidentitáshoz társított SAS-jogkivonatot `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` jelszóként.

* A `devices/{device_id}/modules/{module_id}/messages/events/` telemetria közzétételéhez használja témakörként.

* A `devices/{device_id}/modules/{module_id}/messages/events/` használata WILL-témakörként.

* Az iker GET és PATCH témakörök azonosak a modulok és az eszközök esetében.

* Az ikerállapot témakör a modulok és az eszközök esetében azonos.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguráció

Az MQTT protokoll közvetlen használatának érdekében az *ügyfélnek* TLS/SSL-kapcsolaton keresztül kell csatlakoznia. Csatlakozási hibákkal megkísérli kihagyni ezt a lépést.

A TLS-kapcsolat létesítéhez szükség lehet a DigiCert Baltimore főtanúsítvány letöltésére és hivatkozásra. Ezt a tanúsítványt használja az Azure a kapcsolat biztonságossá tereként. Ezt a tanúsítványt az [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) adattárban találja. További információt ezekről a tanúsítványokról a [Digicert webhelyén talál.](https://www.digicert.com/digicert-root-certificates.htm)

Az Eclipse Foundation [paho MQTT-kódtárÁnak](https://pypi.python.org/pypi/paho-mqtt) Python-verziójával való implementálja ezt a műveletet például az alábbihoz hasonló lehet.

Először telepítse a Paho-kódtárat a parancssori környezetből:

```cmd/sh
pip install paho-mqtt
```

Ezután implementálja az ügyfelet egy Python-szkriptben. Cserélje le a helyőrzőket a következőképpen:

* `<local path to digicert.cer>` A a DigiCert Baltimore főtanúsítványt tartalmazó helyi fájl elérési útja. Ezt a fájlt úgy hozhatja létre, hogy kimásolja a tanúsítványadatokat a [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) fájlból a C-hez készült Azure IoT SDK-ban. Foglalja bele a és a sorokat, távolítsa el az összes sor elején és végén található jelöléseket, és távolítsa el a karaktereket minden sor `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` `"` `\r\n` végén.

* `<device id from device registry>` az IoT Hubhoz hozzáadott eszköz azonosítója.

* `<generated SAS token>` A egy SAS-jogkivonat a cikkben korábban leírtak szerint létrehozott eszközhöz.

* `<iot hub name>` az IoT Hub neve.

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

Az eszköz tanúsítványával való hitelesítéshez frissítse a fenti kódrészletet a következő módosításokkal (lásd: [X.509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) hitelesítésszolgáltatói tanúsítvány lekérhető a tanúsítványalapú hitelesítésre való felkészülésről):

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

## <a name="sending-device-to-cloud-messages"></a>Eszközről a felhőbe küldött üzenetek küldése

A sikeres kapcsolat létrehozása után az eszközök üzeneteket küldhetnek a IoT Hub vagy `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` **témakörnévként.** A elem lehetővé teszi az eszköz számára, hogy URL-kódolású formátumban küldjön további tulajdonságokkal `{property_bag}` együtt üzeneteket. Például:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez az elem ugyanazt a `{property_bag}` kódolást használja, mint a HTTPS protokoll lekérdezési sztringek.

Az alábbi lista a IoT Hub viselkedését sorolja fel:

* IoT Hub nem támogatja a QoS 2-üzeneteket. Ha egy eszközalkalmazás **QoS 2-es hibaüzenetet** tesz közzé, IoT Hub bezárja a hálózati kapcsolatot.

* IoT Hub nem őrzi meg az Üzenetek megőrzése adatokat. Ha egy eszköz 1-re beállított **RETAIN** jelzővel küld üzenetet, IoT Hub **mqtt-retain** application tulajdonságot adja hozzá az üzenethez. Ebben az esetben a megőrzött üzenet megőrzése helyett IoT Hub a háttéralkalmazásnak.

* IoT Hub eszközönként csak egy aktív MQTT-kapcsolatot támogat. Minden új MQTT-kapcsolat ugyanazon eszközazonosító nevében a IoT Hub eldobja a meglévő kapcsolatot, és a **400027 ConnectionForcefullyClosedOnNewConnection** lesz bejelentkezve a IoT Hub Logsba


További információ: [Messaging developer's guide (Üzenetkezelés fejlesztői útmutató).](iot-hub-devguide-messaging.md)

## <a name="receiving-cloud-to-device-messages"></a>Felhőből az eszközre küldött üzenetek fogadása

Ha üzeneteket szeretne fogadni a IoT Hub eszköznek elő kell fizetnie a használatával `devices/{device_id}/messages/devicebound/#` **témakörszűrőként.** A Témakörszűrő többszintű helyettesítő karaktere csak arra használható, hogy az eszköz további tulajdonságokat fogad a `#` témakör nevében. IoT Hub nem engedélyezi a vagy helyettesítő karakterek használatát az altopika `#` `?` szűréséhez. Mivel IoT Hub nem általános célú pub-sub üzenetközvetítő, csak a dokumentált témakörneveket és témakörszűrőket támogatja.

Az eszköz addig nem kap üzeneteket a IoT Hub, amíg sikeresen elő nem iratkált az eszközspecifikus végpontra, amelyet a `devices/{device_id}/messages/devicebound/#` témakörszűrő képvisel. Az előfizetés létrejötte után az eszköz az előfizetés ideje után a felhőből az eszközre küldött üzeneteket kap. Ha az eszköz **0-ra beállított CleanSession** **jelzővel** csatlakozik, az előfizetés több munkamenetben is megmarad. Ebben az esetben, amikor az eszköz legközelebb csatlakozik a **CleanSession 0-val,** leválasztott üzenetben fogadja az eszköznek küldött függőben lévő üzeneteket. Ha azonban az eszköz **a CleanSession** **jelzőt 1-re** beállította, nem kap semmilyen üzenetet a IoT Hub- és eszközvégpontra való feliratkozásig.

IoT Hub a témakör nevével  vagy üzenettulajdonságokkal `devices/{device_id}/messages/devicebound/` `devices/{device_id}/messages/devicebound/{property_bag}` kézbesíti az üzeneteket. `{property_bag}` az üzenettulajdonságok URL-kódolású kulcs/érték párokat tartalmaz. A tulajdonságkészlet csak az alkalmazástulajdonságokat és a felhasználó által beállítható rendszertulajdonságokat **(például messageId** vagy **correlationId)** tartalmazza. A rendszertulajdonságok nevei előtaggal **$** rendelkeznek, az alkalmazástulajdonságok az eredeti tulajdonságnevet használják előtag nélkül. A tulajdonságos csomag formátumára vonatkozó további részletekért lásd: [Eszközről a felhőbe küldött üzenetek küldése.](#sending-device-to-cloud-messages)

A felhőből az eszközre küldött üzenetekben a tulajdonságos csomag értékeit az alábbi táblázatban láthatóak szerint kell ábrázolni:

| Tulajdonság értéke | Képviselet | Description |
|----|----|----|
| `null` | `key` | Csak a kulcs jelenik meg a tulajdonságos csomagban |
| üres sztring | `key=` | A kulcs, amelyet egy egyenlőségjel követ érték nélkül |
| nem null, nem üres érték | `key=value` | A kulcs, amelyet egy egyenlőségjel és az érték követ |

Az alábbi példában egy tulajdonságos csomag látható, amely három alkalmazástulajdonságból ad vissza: **prop1** `null` értékkel; **prop2**, egy üres sztring (""); A és a **prop3** értéke "a string".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Amikor egy eszközalkalmazás előfizet egy **QoS 2-es** témakörre, a IoT Hub 1. QoS-szintet biztosítja a **SUBACK-csomagban.** Ezt követően a IoT Hub üzeneteket küld az eszközre a QoS 1 használatával.

## <a name="retrieving-a-device-twins-properties"></a>Ikereszköz tulajdonságainak leolvasása

Először egy eszköz feliratkozik a `$iothub/twin/res/#` szolgáltatásra, hogy megkapja a művelet válaszait. Ezután egy üres üzenetet küld a témakörnek, a kérésazonosítóhoz megadott `$iothub/twin/GET/?$rid={request id}` **értékkel.** A szolgáltatás ezután egy válaszüzenetet küld, amely a témakörben található ikereszköz-adatokat tartalmazza a kérés azonosítójával `$iothub/twin/res/{status}/?$rid={request id}` azonos kérelemazonosítóval. 

A kérelemazonosító az üzenettulajdonság érték bármely érvényes [](iot-hub-devguide-messaging.md)értéke lehet az üzenetkezelési IoT Hub fejlesztői útmutatójának alapján, és az állapot egész számként van ellenőrizve.

A válasz törzse tartalmazza az ikereszköz tulajdonságok szakaszát az alábbi példaválaszban látható módon:

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

A lehetséges állapotkódok a következőek:

|Állapot | Leírás |
| ----- | ----------- |
| 200 | Siker |
| 429 | Túl sok kérelem (szabályozás alatt) a IoT Hub [szerint](iot-hub-devguide-quotas-throttling.md) |
| 5** | Kiszolgálóhibák |

További információkért lásd az [ikereszközök fejlesztői útmutatóját.](iot-hub-devguide-device-twins.md)

## <a name="update-device-twins-reported-properties"></a>Az ikereszköz jelentett tulajdonságainak frissítése

A jelentett tulajdonságok frissítéséhez az eszköz egy kijelölt MQTT-témakörben IoT Hub egy közzétételen keresztül egy kérést ad ki az alkalmazásnak. A kérés feldolgozása után a IoT Hub egy másik témakörnek adott közzétételen keresztül válaszolja meg a frissítési művelet sikeres vagy sikertelen állapotát. Az eszköz előfizethet erre a témakörre, hogy értesítse az ikerfrissítési kérés eredményéről. Az ilyen típusú kérés-válasz interakció MQTT-ben való megvalósításához az eszköz által a frissítési kérésben eredetileg megadott kérésazonosítót `$rid` () használjuk. Ez a kérelemazonosító a kérésre adott válaszban is IoT Hub hogy az eszköz korrelál tudja a választ az adott korábbi kérésével.

Az alábbi sorozat azt ismerteti, hogyan frissíti egy eszköz az ikereszköz jelentett tulajdonságait a IoT Hub:

1. Az eszközöknek elő kell fizetniük a témakörre, hogy megkapják a művelet `$iothub/twin/res/#` válaszait a IoT Hub.

2. Az eszköz egy üzenetet küld, amely tartalmazza az ikereszköz frissítését a `$iothub/twin/PATCH/properties/reported/?$rid={request id}` témakörbe. Ez az üzenet tartalmazza a **kérelemazonosító** értékét.

3. A szolgáltatás ezután elküld egy válaszüzenetet, amely tartalmazza a témakörben jelentett tulajdonságok gyűjteményének új ETag `$iothub/twin/res/{status}/?$rid={request id}` értékét. Ez a válaszüzenet ugyanazt a **kérelemazonosítót használja,** mint a kérés.

A kérésüzenet törzse tartalmaz egy JSON-dokumentumot, amely a jelentett tulajdonságok új értékeit tartalmazza. A JSON-dokumentum minden tagja frissül, vagy hozzáadja a megfelelő tagokat az ikereszköz dokumentumában. Egy tag, amely `null` törli a tagokat a tartalmazott objektumból. Például:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

A lehetséges állapotkódok a következőek:

|Állapot | Leírás |
| ----- | ----------- |
| 204 | Sikeres (a rendszer nem ad vissza tartalmat) |
| 400 | Hibás kérés. Helytelenül formázott JSON |
| 429 | Túl sok kérelem (szabályozás alatt) a IoT Hub [szerint](iot-hub-devguide-quotas-throttling.md) |
| 5** | Kiszolgálóhibák |

Az alábbi Python-kódrészlet az ikertulajdonságok MQTT-n keresztüli frissítési folyamatát mutatja be (Paho MQTT-ügyfél használatával):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Az ikertulajdonságok frissítésének fenti sikeres művelete esetén a IoT Hub közzétételi üzenete a következő témakört tartalmazza: , ahol a sikeres állapotot jelző állapotkód megfelel az eszköz által a kódban megadott kérelemazonosítónak, és megfelel az ikereszköz frissítés utáni jelentett tulajdonságok `$iothub/twin/res/204/?$rid=1&$version=6` `204` `$rid=1` `$version` szakaszának.

További információkért lásd az [ikereszközök fejlesztői útmutatóját.](iot-hub-devguide-device-twins.md)

## <a name="receiving-desired-properties-update-notifications"></a>Kívánt tulajdonságok frissítési értesítései

Amikor egy eszköz csatlakozik, a IoT Hub értesítéseket küld a témakörbe, amely tartalmazza a megoldás háttér-háttéreszköze által végrehajtott `$iothub/twin/PATCH/properties/desired/?$version={new version}` frissítés tartalmát. Például:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

A tulajdonságfrissítések esetén az értékek azt jelentik, hogy a `null` JSON-objektum tagja törölve van. Azt is figyelje meg, hogy az ikereszköz kívánt tulajdonságok szakaszának új `$version` verzióját jelzi.

> [!IMPORTANT]
> IoT Hub csak akkor hoz létre változásértesítéseket, ha az eszközök csatlakoztatva vannak. Ügyeljen arra, hogy implementálja az eszköz [újracsatlakozási](iot-hub-devguide-device-twins.md#device-reconnection-flow) folyamatát, hogy a kívánt tulajdonságok szinkronizálva IoT Hub eszközalkalmazás és az eszközalkalmazás között.

További információkért lásd az [ikereszközök fejlesztői útmutatóját.](iot-hub-devguide-device-twins.md)

## <a name="respond-to-a-direct-method"></a>Válasz egy közvetlen metódusra

Először elő kell fizetni az `$iothub/methods/POST/#` eszközre. IoT Hub metóduskéréseket küld a témakörbe egy érvényes `$iothub/methods/POST/{method name}/?$rid={request id}` JSON-nal vagy egy üres törzsvel.

A válaszhoz az eszköz egy érvényes JSON-t vagy üres törzset is küld a `$iothub/methods/res/{status}/?$rid={request id}` témakörnek. Ebben az üzenetben a **kérésazonosítónak** meg kell  egyeznie a kérésüzenetben és az állapotnak egész számnak kell lennie.

További információkért tekintse meg a [Közvetlen módszer fejlesztői útmutatóját.](iot-hub-devguide-direct-methods.md)

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Utolsó szempontként, ha testre kell szabni az MQTT protokoll viselkedését a felhő oldalán, tekintse át az [Azure IoT protokollátjárót.](iot-hub-protocol-gateway.md) Ez a szoftver lehetővé teszi egy nagy teljesítményű egyéni protokollátjáró üzembe helyezését, amely közvetlenül kapcsolódik a IoT Hub. Az Azure IoT protokollátjáróval testre szabhatja az eszköz protokollját a barnamezős MQTT-környezetek vagy más egyéni protokollok befogadásához. Ehhez a megközelítéshez azonban szükség van egy egyéni protokollátjáró futtatására és működtetésére.

## <a name="next-steps"></a>Következő lépések

Az MQTT protokollról további információt az [MQTT dokumentációjában talál.](https://mqtt.org/)

Az üzembe helyezés megtervezésével kapcsolatos további IoT Hub lásd:

* [Azure Certified for IoT eszközkatalógus](https://devicecatalog.azure.com/)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [Összehasonlítás a Event Hubs](iot-hub-compare-event-hubs.md)
* [Méretezés, HA és DR](iot-hub-scaling.md)

A szolgáltatás képességeinek további IoT Hub:

* [IoT Hub útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)
