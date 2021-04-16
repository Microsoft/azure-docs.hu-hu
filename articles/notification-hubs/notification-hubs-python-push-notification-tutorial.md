---
title: Az Notification Hubs használata a Pythonnal
description: Megtudhatja, hogyan használhatja az Azure Notification Hubs Python-alkalmazásból.
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
ms.openlocfilehash: f964957b916c6841da097f93173b0306bb65c8a4
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576023"
---
# <a name="how-to-use-notification-hubs-from-python"></a>A Pythonból Notification Hubs használata

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Az összes Notification Hubs java-/PHP-/Python-/Ruby-háttérrendszerről elérheti a Notification Hub REST-felületét az MSDN REST API Notification Hubs cikkben leírtak [szerint.](/previous-versions/azure/reference/dn223264(v=azure.100))

> [!NOTE]
> Ez egy minta referencia-implementáció az értesítések Pythonban való végrehajtásához, és nem a hivatalosan támogatott Notifications Hub Python SDK. A minta a Python 3.4 használatával lett létrehozva.

Ez a cikk a következőkhöz nyújt útmutatást:

- REST-ügyfél összeállítása a Python Notification Hubs funkcióihoz.
- Értesítések küldése a Python felület használatával a Notification Hub REST API-knak.
- Kérje le a HTTP REST-kérés/-válasz memóriaképét hibakeresési/oktatási célból.

Kövesse az [](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Első lépések oktatóanyagot a választott mobilplatformhoz, amely a háttér-rész Pythonban való megvalósításával kapcsolatos.

> [!NOTE]
> A minta hatóköre csak értesítések küldését jelenti, regisztrációkezelést nem.

## <a name="client-interface"></a>Ügyféloldali felület

A fő ügyféloldali felület a .NET sdk-ban elérhető [Notification Hubs biztosíthatja.](https://msdn.microsoft.com/library/jj933431.aspx) Ez a felület lehetővé teszi, hogy közvetlenül lefordítsa az ezen a webhelyen jelenleg elérhető összes oktatóanyagot és mintát, és a közösség által az interneten közzétett okat.

Az összes elérhető kódot megtalálja a [Python REST-burkoló mintában.]

Ügyfél létrehozásához például:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Windows bejelentési értesítés küldése:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementálás

Ha még nem tette [] meg, kövesse az Első lépések oktatóanyagot az utolsó szakaszig, ahol implementálja a háttéranyagot.

A teljes REST-burkoló implementált összes részlete megtalálható az [MSDN-on.](/previous-versions/azure/reference/dn530746(v=azure.100)) Ez a szakasz a REST-végpontok eléréséhez és az értesítések küld Notification Hubs való hozzáféréshez szükséges fő lépések Python-implementációját ismerteti

1. Kapcsolati sztring elemzése
2. Az engedélyezési jogkivonat létrehozása
3. Értesítés küldése HTTP-REST API

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése

Itt található az ügyfelet végrehajtó fő osztály, amelynek konstruktora a kapcsolati sztringet elemezi:

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

A biztonsági jogkivonat létrehozásának részletei itt [érhetők el.](/previous-versions/azure/reference/dn495627(v=azure.100))
Adja hozzá a következő metódusokat a osztályhoz, hogy az aktuális kérés URI-ja és a kapcsolati sztringből kinyert hitelesítő adatok alapján hozza `NotificationHub` létre a jogkivonatot.

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

### <a name="send-a-notification-using-http-rest-api"></a>Értesítés küldése HTTP-REST API

Először is használjuk az értesítésnek megfelelő osztály definiálását.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'gcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Ez az osztály egy natív értesítési törzs tárolója, vagy egy sablonértesítés tulajdonságainak készlete, egy fejléckészlet, amely formátumot (natív platform vagy sablon) és platformspecifikus tulajdonságokat (például Apple lejárati tulajdonság és WNS-fejlécek) tartalmaz.

Az elérhető [lehetőségekért tekintse Notification Hubs REST API-k](/previous-versions/azure/reference/dn495827(v=azure.100)) dokumentációját és az adott értesítési platformok formátumát.

Most ezzel az osztálysal írja meg a küldési értesítési metódusokat a `NotificationHub` osztályon belül.

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

    json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

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


def send_google_notification(self, payload, tags=""):
    nh = Notification("gcm", payload)
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

Ezek a metódusok egy HTTP POST kérést küldenek az értesítési központ /messages végpontjára, az értesítés elküldését megfelelő törzsvel és fejlécekkel.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Hibakeresési tulajdonság használata a részletes naplózás engedélyezéséhez

A hibakeresési tulajdonság engedélyezése az értesítési központ inicializálása során részletes naplózási adatokat ír ki a HTTP-kérésről és a válasz memóriaképéről, valamint részletes értesítési üzenetek küldési eredményéről.
A [Notification Hubs TestSend tulajdonság részletes](/previous-versions/azure/reference/dn495827(v=azure.100)) információkat ad vissza az értesítés-küldés eredményéről.
A használathoz inicializálja az inicializálást a következő kóddal:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Az Értesítési központ Kérés küldése HTTP URL-címe hozzá lesz fűzve egy "teszt" lekérdezési sztringhez eredményként.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Az oktatóanyag befejezése

Most már befejezheti az Első lépések oktatóanyagot úgy, hogy elküldi az értesítést egy Python-háttérből.

Inicializálja a Notification Hubs -ügyfelet (helyettesítse be a kapcsolati sztringet és a központ nevét az Első lépések [oktatóanyag utasításainak megfelelően):]

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Ezután adja hozzá a küldési kódot a cél mobilplatformtól függően. Ez a minta magasabb szintű metódusokat is hozzáad, amelyek lehetővé teszik az értesítések küldését a platform alapján, például send_windows_notification Windows esetén; send_apple_notification (apple) stb.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Áruház és Windows Phone-telefon 8.1 (nem Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone-telefon 8.0 és 8.1 Silverlight

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
gcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_google_notification(gcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

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

A Python-kód futtatásával egy értesítésnek kell megjelennie a céleszközön.

## <a name="examples"></a>Példák

### <a name="enabling-the-debug-property"></a>A tulajdonság `debug` engedélyezése

Ha engedélyezi a hibakeresési jelzőt a NotificationHub inicializálása során, a részletes HTTP-kérés és -válasz memóriakép, valamint a NotificationOutcome az alábbihoz hasonló megjelenik, ahol láthatja, hogy milyen HTTP-fejlécek vannak átkultálva a kérésben, és milyen HTTP-választ kapott az értesítési központtól:

![Képernyőkép egy konzolról, amely a H T T P kérés- és válaszmentés, valamint az értesítési eredmény piros színben felvázolt üzeneteit tartalmazza.][1]

Itt láthatja például az értesítési központ részletes eredményét.

- ha az üzenet sikeresen el lett küldve a leküldéses értesítési szolgáltatásnak.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Ha egyetlen leküldéses értesítéshez sem található cél, akkor valószínűleg a következő kimenetet fogja látni válaszként (ami azt jelzi, hogy nem találhatók az értesítés kézbesítésére alkalmas regisztrációk, valószínűleg azért, mert a regisztrációk nem egyező címkékkel rendelkezik)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Bejelentési értesítés szórása Windowsra

Figyelje meg a bejelentési értesítések Windows-ügyfélnek való küldésekor küldött fejléceket.

```python
hub.send_windows_notification(wns_payload)
```

![Képernyőkép egy konzolról, amely a H T T P kérelem részleteit, valamint a Service Bus Notification Format és X W N S Type (Értesítési formátum) és X W N S Type (X W N S típus) értékeket tartalmazza piros színben.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Címke (vagy címkekifejezés) megadásával kapcsolatos értesítés küldése

Figyelje meg a Címkék HTTP-fejlécet, amely hozzá lesz adva a HTTP-kéréshez (az alábbi példában az értesítés csak a "sport" hasznos adatokkal való regisztrációknak lesz elküldve)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Képernyőkép egy konzolról, amely a H T T P kérelem részleteit és a Service Bus Notification Format, egy Service Bus Notification Tag és X W N S type (Értesítési címke) értékét tartalmazza piros színben.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Értesítés küldése több címkével

Figyelje meg, hogyan változik a Címkék HTTP-fejléce több címke elküldése esetén.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Képernyőkép egy konzolról, amely a H T T P kérés és a Service Bus Notification Format, több Service Bus Notification Tags és X W N S Type értékeket tartalmaz piros színben.][4]

### <a name="templated-notification"></a>Sablonos értesítés

Figyelje meg, hogy a FORMAT HTTP-fejléc megváltozik, és a hasznos adatok törzse a HTTP-kérés törzsének részeként lesz elküldve:

**Ügyféloldal – regisztrált sablon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Kiszolgálóoldal – a hasznos tartalom küldése:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Képernyőkép egy konzolról, amely a H T T P kérelem részleteit, valamint a tartalomtípust és a Service Bus vörös színben felvázolt Értesítési formátum értékeket tartalmazza.][5]

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt mutatta be, hogyan hozhat létre Python REST-ügyfelet a Notification Hubs. Ebből a menüből:

- Töltse le a [teljes Python REST-burkoló mintát,]amely a cikkben található összes kódot tartalmazza.
- Folytassa a Notification Hubs funkció megismerése a Legfrissebb hírek [oktatóanyagban]
- Folytassa a Notification Hubs új sablonok funkcióval kapcsolatos további tanulást a [Localizing News (Hírek honosulása) oktatóanyagban]

<!-- URLs -->
[Python REST-burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Első lépéseket ismertető oktatóanyag]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Friss hírek oktatóanyag]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Hírek honosító oktatóanyaga]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
