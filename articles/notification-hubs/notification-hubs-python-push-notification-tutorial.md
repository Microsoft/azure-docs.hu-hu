---
title: A Notification Hubs használata a Python használatával
description: Ismerje meg, hogyan használható az Azure Notification Hubs egy Python-alkalmazásból.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: f81614005a1b0374dc249187c4ff3c920b7c97e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424847"
---
# <a name="how-to-use-notification-hubs-from-python"></a>A Notification Hubs használata a Pythonból

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Egy Java/PHP/Python/Ruby háttérrendszer összes Notification Hubs funkcióját az értesítési központ REST felületének használatával érheti el az MSDN-cikk [Notification HUBS REST API](/previous-versions/azure/reference/dn223264(v=azure.100))-k című cikkben leírtak szerint.

> [!NOTE]
> Ez az értesítés a Pythonban való végrehajtásának megvalósítására szolgál, és nem a hivatalosan támogatott Notifications hub Python SDK. A minta a Python 3,4 használatával lett létrehozva.

Ez a cikk a következőkhöz nyújt útmutatást:

- REST-ügyfél létrehozása a Python Notification Hubs szolgáltatásaihoz.
- Értesítések küldése a Python felületen keresztül az értesítési központ REST API-jai számára.
- A HTTP REST-kérelem/-válasz memóriaképének beolvasása hibakeresési/oktatási célra.

Az [első lépéseket ismertető oktatóanyagban](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) megtekintheti a mobil platformot, és a Pythonban a háttérrendszer részét képezheti.

> [!NOTE]
> A minta hatóköre csak az értesítések küldésére korlátozódik, és nem végez regisztrációs felügyeletet.

## <a name="client-interface"></a>Ügyfél felülete

A fő ügyfél felülete ugyanazokat a módszereket nyújtja, mint amelyek a [.net Notification HUBS SDK](https://msdn.microsoft.com/library/jj933431.aspx)-ban is elérhetők. Ez az interfész lehetővé teszi, hogy közvetlenül lefordítsa az ezen a webhelyen jelenleg elérhető oktatóanyagokat és mintákat, és a Közösség az interneten is részt vesz.

A [PYTHON Rest burkoló mintában]elérhető összes kód megtalálható.

Például egy ügyfél létrehozásához:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Windows Toast-értesítés küldése:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementálás

Ha még nem tette meg, kövesse az [első lépések oktatóanyagot] az utolsó szakaszhoz, ahol a háttér megvalósítására van szükség.

A teljes REST-burkoló megvalósításának minden részletét az [MSDN webhelyén](/previous-versions/azure/reference/dn530746(v=azure.100))találja. Ez a szakasz a Notification Hubs REST-végpontok eléréséhez és az értesítések küldéséhez szükséges fő lépések Python-megvalósítását ismerteti.

1. Kapcsolati sztring elemzése
2. Az engedélyezési jogkivonat előállítása
3. Értesítés küldése HTTP-REST API használatával

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése

Itt látható a-ügyfelet megvalósító fő osztály, amelynek konstruktora elemzi a kapcsolatot megadó karakterláncot:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Biztonsági jogkivonat létrehozása

A biztonsági jogkivonat létrehozásának részletei [itt](/previous-versions/azure/reference/dn495627(v=azure.100))érhetők el.
Adja hozzá a következő metódusokat a `NotificationHub` osztályhoz a jogkivonat létrehozásához az aktuális kérelem URI-ja és a kapcsolati karakterláncból kinyert hitelesítő adatok alapján.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Értesítés küldése HTTP-REST API használatával

Először is használjon egy olyan osztályt, amely egy értesítést jelöl.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Ez az osztály egy natív értesítési törzs vagy egy sablonra vonatkozó értesítés tulajdonságai, a fejlécek egy készlete, amely a formátumot (natív platform vagy sablon) és a platform-specifikus tulajdonságokat (például az Apple lejárat tulajdonságát és a WNS fejléceket) tartalmazza.

A rendelkezésre álló lehetőségekért tekintse meg a [Notification HUBS REST API-k dokumentációját](/previous-versions/azure/reference/dn495827(v=azure.100)) és az egyes értesítési platformok formátumait.

Ebben az osztályban a küldési értesítési módszereket az osztályban belül kell megírni `NotificationHub` .

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Ezek a módszerek HTTP POST-kérést küldenek az értesítési központ/messages-végpontjának, és a megfelelő törzstel és fejlécekkel küldik el az értesítést.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Hibakeresési tulajdonság használata a részletes naplózás engedélyezéséhez

Ha az értesítési központ inicializálásakor engedélyezi a hibakeresési tulajdonságot, a a HTTP-kérelem és a válasz-memóriakép részletes naplózási információit, valamint a részletes értesítési üzenetek küldésének eredményét írja le.
A [Notification Hubs TestSend tulajdonság](/previous-versions/azure/reference/dn495827(v=azure.100)) részletes információkat ad vissza az értesítés küldésének eredményéről.
Az inicializálásához használja az alábbi kódot:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Az értesítési központ küldési kérelmének HTTP URL-címe a "teszt" lekérdezési karakterlánccal lesz hozzáfűzve.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Az oktatóanyag befejezése

Most már elvégezheti az első lépéseket ismertető oktatóanyagot, ha az értesítést egy Python-háttérből küldi el.

Inicializálja a Notification Hubs ügyfelet (a [kezdeti lépések oktatóanyagban]leírtak szerint helyettesítse be a kapcsolatok karakterláncát és a hub nevét):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Ezután adja hozzá a küldési kódot a cél mobil platformtól függően. Ez a minta magasabb szintű módszereket is biztosít, amelyek lehetővé teszik a platformon alapuló értesítések küldését, például send_windows_notification a Windowshoz; send_apple_notification (Apple esetében) stb.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows áruház és Windows Phone-telefon 8,1 (nem Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone-telefon 8,0 és 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle-Tűz

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

A Python-kód futtatásakor egy értesítést kell létrehozni a céleszköz.

## <a name="examples"></a>Példák

### <a name="enabling-the-debug-property"></a>A `debug` tulajdonság engedélyezése

Ha engedélyezi a hibakeresési jelzőt a NotificationHub inicializálása közben, a részletes HTTP-kérés és-válasz memóriaképe, valamint a NotificationOutcome, például a következő, ahol megtudhatja, hogy milyen HTTP-fejlécek lesznek átadva a kérelemben, és milyen HTTP-választ kapott az értesítési központ:

![Képernyőkép a-konzolról, amelyen a H T T P kérelem és a válasz memóriaképe, valamint a riasztások eredményeinek piros színnel való megjelenítése látható.][1]

Például az értesítési központ részletes eredményét láthatja.

- Ha az üzenet sikeresen el lett küldve a leküldéses értesítési szolgáltatásba.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Ha nem találhatók célok a leküldéses értesítésekhez, akkor valószínűleg a következő kimenetet fogja látni a válaszként (amely azt jelzi, hogy az értesítés kézbesítése nem történt meg, mert a regisztrációk nem egyeznek meg a címkével)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Bejelentési értesítés küldése a Windowsnak

Figyelje meg azokat a fejléceket, amelyeket a rendszer akkor küld, ha a Windows-ügyfélnek küldi el a bejelentést.

```python
hub.send_windows_notification(wns_payload)
```

![Képernyőkép a-konzolról, amelyen a H T T P-kérelem és a Service Bus értesítési formátum és az X W N S Type értékek piros színnel jelennek meg.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Értesítés küldése címke (vagy címke kifejezés) megadásával

Figyelje meg a címkék HTTP-fejlécet, amelyet a rendszer a HTTP-kérelemhez ad hozzá (az alábbi példában az értesítés csak a "sport" adattartalommal rendelkező regisztrációk esetében lesz elküldve)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Képernyőkép a-konzolról, amelyen a H T T P-kérelem és a Service Bus értesítési formátum, a Service Bus értesítési címke és az X W N S Type értékek piros színnel jelennek meg.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Értesítés küldése több címke megadásával

Figyelje meg, hogy a címkék HTTP-fejléce hogyan változik meg több címke küldésekor.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Képernyőkép a-konzolról, amelyen a H T T P-kérelem és a Service Bus értesítési formátum, több Service Bus értesítési címke, valamint X W N S Type érték szerepel piros színnel.][4]

### <a name="templated-notification"></a>Sablonban szereplő értesítés

Figyelje meg, hogy a HTTP-fejléc formátuma és a hasznos adatok szövegtörzse a HTTP-kérelem törzsének részeként lesz elküldve:

**Ügyféloldali regisztrált sablon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Kiszolgálóoldali – a hasznos adatok küldése:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Képernyőkép a-konzolról a H T T P-kérelem részleteivel, valamint a tartalom típusával és Service Bus értesítési formátumával kapcsolatos, piros színnel megadott értékekkel.][5]

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan hozhat létre Notification Hubs Python REST-ügyfelet. Ebből a menüből:

- Töltse le a teljes [PYTHON Rest burkoló mintát], amely tartalmazza a cikkben szereplő összes kódot.
- Folytassa a Notification Hubs címkézési funkciójának megismerését a [legfrissebb hírek oktatóanyagban]
- Folytassa a Notification Hubs templates szolgáltatás megismerését a [honosító Hírek oktatóanyagában]

<!-- URLs -->
[Python REST burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Első lépéseket ismertető oktatóanyag]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[A legfrissebb hírek oktatóanyaga]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Hírek honosítása – oktatóanyag]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
