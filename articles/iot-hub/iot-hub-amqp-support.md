---
title: Az Azure IoT Hub AMQP-támogatás ismertetése | Microsoft Docs
description: Fejlesztői útmutató – a AMQP protokoll használatával a IoT Hub eszközre irányuló és a szolgáltatáshoz kapcsolódó végpontokhoz csatlakozó eszközök támogatása. Az Azure IoT Device SDK-k beépített AMQP-támogatásával kapcsolatos információkat tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7b3dcfc51df7f0fe4291e9c5babccc1444ad32e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730755"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Kommunikáció az IoT hub használatával a AMQP protokoll segítségével

Az Azure IoT Hub támogatja az [OASIS Advanced Message Queueing Protocol (AMQP) 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) -es verzióját, hogy különféle funkciókat nyújtson az eszközre irányuló és a szolgáltatással szemben elérhető végpontokon keresztül. Ez a dokumentum azt ismerteti, hogyan használhatók a AMQP-ügyfelek az IoT hub-hoz való kapcsolódáshoz IoT Hub funkció használatához.

## <a name="service-client"></a>Szolgáltatási ügyfél

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Kapcsolódás és hitelesítés IoT hub-ban (szolgáltatási ügyfél)

Ha a AMQP használatával szeretne csatlakozni egy IoT hubhoz, az ügyfél használhatja a [jogcímbarát biztonsági (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy a [egyszerű hitelesítési és biztonsági réteg (SASL) hitelesítést](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

A szolgáltatás ügyfelének a következő információkat kell megadnia:

| Tájékoztatás | Érték |
|-------------|--------------|
| IoT hub-állomásnév | `<iot-hub-name>.azure-devices.net` |
| Kulcs neve | `service` |
| Hozzáférési kulcs | A szolgáltatáshoz társított elsődleges vagy másodlagos kulcs |
| Közös hozzáférésű jogosultságkód | Egy rövid életű közös hozzáférésű aláírás a következő formátumban: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Az aláírás létrehozásához szükséges kód beszerzéséhez tekintse meg a [IoT hub hozzáférésének szabályozása](./iot-hub-devguide-security.md#security-token-structure)című témakört.

A következő kódrészlet a [Python uAMQP könyvtárát](https://github.com/Azure/azure-uamqp-python) használja a IoT hubhoz való csatlakozáshoz a küldő hivatkozásán keresztül.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Felhőből az eszközre irányuló üzenetek meghívása (szolgáltatás-ügyfél)

A szolgáltatás és az IoT hub, valamint az eszköz és az IoT hub között a felhőből az eszközre irányuló üzenetváltásról további információt a [felhőből az eszközre irányuló üzenetek küldése az IoT hubhoz](iot-hub-devguide-messages-c2d.md)című témakörben talál. A szolgáltatás-ügyfél két hivatkozást használ az üzenetek küldésére és visszajelzések fogadására a korábban elküldött üzenetekről az eszközökről, az alábbi táblázatban leírtak szerint:

| Létrehozó | Hivatkozás típusa | Hivatkozás elérési útja | Description |
|------------|-----------|-----------|-------------|
| Szolgáltatás | Feladó hivatkozása | `/messages/devicebound` | Az eszközökre szánt, felhőből eszközre irányuló üzeneteket a szolgáltatás elküldi erre a hivatkozásra. A hivatkozáson keresztül küldött üzeneteknek a `To` tulajdonsága a cél eszköz fogadó hivatkozásának elérési útjára van beállítva `/devices/<deviceID>/messages/devicebound` . |
| Szolgáltatás | Fogadó hivatkozása | `/messages/serviceBound/feedback` | Az ezen a hivatkozáson keresztül fogadott eszközökről érkező, a küldéssel, elutasítással és törléssel kapcsolatos visszajelzési üzenetek. További információ a visszajelzési üzenetekről: a [felhőből az eszközre irányuló üzenetek küldése az IoT hub-ból](./iot-hub-devguide-messages-c2d.md#message-feedback). |

A következő kódrészlet bemutatja, hogyan hozhat létre egy felhőből az eszközre irányuló üzenetet, és hogyan küldheti el azt egy eszközre a [Python uAMQP könyvtárának](https://github.com/Azure/azure-uamqp-python)használatával.

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Ha visszajelzést szeretne kapni, a szolgáltatás ügyfél létrehoz egy fogadó hivatkozást. A következő kódrészlet azt mutatja be, hogyan hozható létre hivatkozás a [Python uAMQP könyvtárának](https://github.com/Azure/azure-uamqp-python)használatával:

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Ahogy az előző kódban is látható, a felhőből az eszközre irányuló visszajelzési üzenetben az *alkalmazás/vnd.microsoft.iothub.feedback.js* tartalomtípusa szerepel a következőben:. Az üzenet JSON-törzsének tulajdonságainak használatával következtetheti ki az eredeti üzenet kézbesítési állapotát:

* `statusCode`A visszajelzés törzsében található kulcs a következő értékek egyikével rendelkezik: *sikeres*, *lejárt*, *DeliveryCountExceeded*, *visszautasított* vagy *kiürített*.

* A `deviceId` visszajelzés törzsében található kulcs rendelkezik a CÉLESZKÖZ azonosítójával.

* A `originalMessageId` visszajelzés törzsében található kulcs a szolgáltatás által küldött, az eredeti felhőből az eszközre irányuló üzenet azonosítóját kapja. Ezt a kézbesítési állapotot használhatja a felhőből az eszközre irányuló üzenetek visszajelzésének összekapcsolásához.

### <a name="receive-telemetry-messages-service-client"></a>Telemetria üzenetek fogadása (szolgáltatási ügyfél)

Alapértelmezés szerint az IoT hub a betöltött eszköz telemetria-üzeneteit egy beépített Event hub tárolja. A szolgáltatás ügyfele a AMQP protokollt használva fogadhatja a tárolt eseményeket.

Erre a célra a szolgáltatás ügyfelének először csatlakoznia kell az IoT hub-végponthoz, és el kell fogadnia egy átirányítási címeket a beépített esemény-hubhoz. A szolgáltatási ügyfél ezután a megadott címen csatlakozik a beépített Event hubhoz.

Az ügyfélnek minden lépésben be kell mutatnia a következő információkat:

* Érvényes szolgáltatás hitelesítő adatai (a szolgáltatás közös hozzáférési aláírási jogkivonata).

* A fogyasztói csoport partíciójának jól formázott elérési útja, amelyből üzeneteket kíván lekérdezni. Az adott fogyasztói csoport és partíciós azonosító esetében az elérési út formátuma a következő: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (az alapértelmezett fogyasztói csoport `$Default` ).

* Egy nem kötelező szűrési predikátum, amely kijelöl egy kiindulási pontot a partícióban. Ez a predikátum sorszám, eltolás vagy várólistán lévő timestamp formátumú lehet.

A következő kódrészlet a [Python uAMQP könyvtárát](https://github.com/Azure/azure-uamqp-python) használja az előző lépések bemutatására:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Egy adott eszköz AZONOSÍTÓjának esetében az IoT hub az eszköz AZONOSÍTÓjának kivonatát használja annak meghatározására, hogy melyik partíció tárolja az üzeneteit a alkalmazásban. Az előző kódrészlet azt mutatja be, hogy az események hogyan érkeznek egyetlen ilyen partícióból. Vegye figyelembe azonban, hogy egy tipikus alkalmazásnak gyakran kell lekérnie az Event hub összes partíciójában tárolt eseményeket.

## <a name="device-client"></a>Eszköz ügyfele

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Kapcsolódás és hitelesítés IoT hub-ban (eszköz-ügyfél)

Ha a AMQP használatával szeretne csatlakozni egy IoT hubhoz, az eszköz használhat [jogcímek alapú biztonsági (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [egyszerű hitelesítési és biztonsági réteg (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) hitelesítést.

Az eszköz ügyfelének a következő információkat kell megadnia:

| Tájékoztatás | Érték |
|-------------|--------------|
| IoT hub-állomásnév | `<iot-hub-name>.azure-devices.net` |
| Hozzáférési kulcs | Az eszközhöz társított elsődleges vagy másodlagos kulcs |
| Közös hozzáférésű jogosultságkód | Egy rövid életű közös hozzáférésű aláírás a következő formátumban: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Az aláírás létrehozásához szükséges kód beszerzéséhez tekintse meg a [IoT hub hozzáférésének szabályozása](./iot-hub-devguide-security.md#security-token-structure)című témakört.

A következő kódrészlet a [Python uAMQP könyvtárát](https://github.com/Azure/azure-uamqp-python) használja a IoT hubhoz való csatlakozáshoz a küldő hivatkozásán keresztül.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

A következő hivatkozás elérési útjai támogatottak az eszköz műveleteiként:

| Létrehozó | Hivatkozás típusa | Hivatkozás elérési útja | Description |
|------------|-----------|-----------|-------------|
| Eszközök | Fogadó hivatkozása | `/devices/<deviceID>/messages/devicebound` | Az eszközökre szánt, felhőből az eszközre irányuló üzenetek fogadása ezen a hivatkozáson keresztül történik minden céleszköz esetében. |
| Eszközök | Feladó hivatkozása | `/devices/<deviceID>/messages/events` | Az eszközről a felhőbe küldött üzenetek küldése ezen a hivatkozáson keresztül történik. |
| Eszközök | Feladó hivatkozása | `/messages/serviceBound/feedback` | A felhőből az eszközre küldött üzenetek visszajelzése a szolgáltatásnak ezen a hivatkozáson keresztül, eszközökön keresztül. |

### <a name="receive-cloud-to-device-commands-device-client"></a>A felhőből az eszközre irányuló parancsok fogadása (eszköz-ügyfél)

Az eszközöknek egy hivatkozáson keresztül továbbított, a felhőből az eszközre irányuló parancsok `/devices/<deviceID>/messages/devicebound` . Az eszközök a kötegekben fogadhatják ezeket az üzeneteket, és igény szerint használhatják az üzenet adattartalmát, az üzenet tulajdonságait, a jegyzeteket vagy az alkalmazás tulajdonságait.

A következő kódrészlet a [Python uAMQP könyvtárát](https://github.com/Azure/azure-uamqp-python)használja a felhőből az eszközre irányuló üzenetek fogadására egy eszközön.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetria üzenetek küldése (eszköz ügyfélprogramja)

A AMQP használatával is küldhet telemetria-üzeneteket egy eszközről. Az eszköz opcionálisan megadhatja az alkalmazás tulajdonságait, illetve az üzenetek különböző tulajdonságait, például az üzenet AZONOSÍTÓját.

A következő kódrészlet a [Python uAMQP könyvtárát](https://github.com/Azure/azure-uamqp-python) használja az eszközről a felhőbe irányuló üzenetek küldéséhez az eszközről.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>További megjegyzések

* Előfordulhat, hogy a AMQP-kapcsolatok hálózati hiba miatt megszakadnak, vagy a hitelesítési jogkivonat lejárata (amely a kódban lett létrehozva). A szolgáltatás ügyfelének ezeket a körülményeket kell kezelnie, és szükség esetén újra létre kell hoznia a kapcsolatot és a hivatkozásokat. Ha egy hitelesítési jogkivonat lejár, az ügyfél elkerülheti a kapcsolat csökkenését a jogkivonat proaktív megújításával a lejárat előtt.

* Az ügyfélnek időnként képesnek kell lennie helyesen kezelni a hivatkozások átirányítását. Az ilyen műveletek megismeréséhez tekintse meg a AMQP-ügyfél dokumentációját.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az AMQP protokollról, tekintse meg a [AMQP v 1.0 specifikációját](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Az IoT Hub üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következőt:

* [Felhőből az eszközre irányuló üzenetek](./iot-hub-devguide-messages-c2d.md)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [A Message Queuing telemetria Transport (MQTT) protokoll támogatása](./iot-hub-mqtt-support.md)
