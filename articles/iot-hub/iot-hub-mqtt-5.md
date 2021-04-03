---
title: Azure IoT Hub MQTT 5 támogatás (előzetes verzió)
description: Tudnivalók a IoT Hub MQTT 5 támogatásáról
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603328"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub MQTT 5 – támogatás áttekintése (előzetes verzió)

**Version:** 2,0 **API-verzió:** 2020-10-01-előzetes verzió

Ez a dokumentum a MQTT 5,0-es protokollal IoT Hub adatközpont API-t határozza meg. Lásd: [API-referenciák](iot-hub-mqtt-5-reference.md) a teljes definíciók létrehozásához ebben az API-ban.

## <a name="prerequisites"></a>Előfeltételek

- Az [előnézeti mód engedélyezése](iot-hub-preview-mode.md) egy vadonatúj IoT hub-on a MQTT 5 kipróbálásához.
- A [MQTT 5 specifikáció](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) előzetes ismerete szükséges.

## <a name="level-of-support-and-limitations"></a>Támogatás és korlátozások szintje

A (z) MQTT 5 IoT Hub támogatása előzetes verzióban érhető el, és a következő módon van korlátozva (az ügyfél által a tulajdonságok segítségével közölt `CONNACK` ), kivéve, ha explicit módon nem jelezzük

- Még nincs hivatalos [Azure IoT hub-eszköz SDK](iot-hub-devguide-sdks.md) -támogatás.
- Az előfizetés-azonosítók nem támogatottak.
- A megosztott előfizetések nem támogatottak.
- `RETAIN` nem támogatott.
- A `Maximum QoS` értéke `1`.
- `Maximum Packet Size` a `256 KiB` (műveletre vonatkozó további korlátozásokra vonatkozik).
- A hozzárendelt ügyfél-azonosítók nem támogatottak.
- `Keep Alive` korlátozódik a `19 min` következőre: (az élettartam-ellenőrzési maximális késleltetése – `28.5 min` ).
- A `Topic Alias Maximum` értéke `10`.
- `Response Information` nem támogatott; `CONNACK` nem ad vissza `Response Information` tulajdonságot még akkor sem, ha `CONNECT` `Request Response Information` tulajdonságot tartalmaz.
- `Receive Maximum` (a függőben lévő nem nyugtázott csomagok maximális száma `PUBLISH` (az ügyfél és a kiszolgáló irányában) a ( `QoS: 1` ) értéke `16` .
- Egyetlen ügyfél nem rendelkezhet több `50` előfizetéssel.
  Ha elérte a korlátot, a `SUBACK` rendszer visszaküldi a `0x97` (kvóta túllépése) okkódot az előfizetésekhez.

## <a name="connection-lifecycle"></a>A kapcsolatok életciklusa

### <a name="connection"></a>Kapcsolat

Ha ezzel az API-val szeretne csatlakoztatni egy ügyfelet az IoT Hubhoz, hozzon létre kapcsolatot a MQTT 5 specifikáció alapján.
Az ügyfélnek `CONNECT` a sikeres TLS-kézfogás után 30 másodpercen belül el kell küldenie a csomagot, vagy a kiszolgáló lezárja a csatlakozást.
Példa a `CONNECT` csomagra:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` a tulajdonság megadása kötelező, és meghatározza, hogy melyik hitelesítési módszert használja a rendszer. További információ a hitelesítési módszerről: [hitelesítés](#authentication).
- `Authentication Data` a tulajdonság a következőtől függ: `Authentication Method` . Ha a `Authentication Method` értéke `SAS` , akkor `Authentication Data` kötelező, és érvényes aláírást kell tartalmaznia. További információ a hitelesítési adatokról: [hitelesítés](#authentication).
- `api-version` a tulajdonság megadása kötelező, és az adott specifikáció fejlécében szereplő API-verzió értékének kell megadnia ehhez a specifikációhoz.
- `host` a tulajdonság a bérlő állomásnevét határozza meg. Csak akkor szükséges, ha a SNI bővítményt a TLS-kézfogás során az ügyfél Hello-rekordjában mutatták be
- `sas-at` a kapcsolat időpontját határozza meg.
- `sas-expiry` meghatározza a megadott SAS lejárati idejét.
- `client-agent` opcionálisan tájékoztatja a kapcsolatot létrehozó ügyféllel kapcsolatos információkat.

> [!NOTE]
> `Authentication Method` továbbá a specifikációban szereplő, tőkésített nevekkel rendelkező egyéb tulajdonságok az első osztályú tulajdonságok a MQTT 5-ben – ezeket a részleteket a MQTT 5 specifikáció ismerteti. `api-version` és az egyéb tulajdonságok a kötőjel esetben a IoT Hub API-ra jellemző felhasználói tulajdonságok.

A IoT Hub a `CONNACK` csomaggal válaszol, miután befejezte a hitelesítést, és beolvassa az adatgyűjtést a kapcsolat támogatásához. Ha a kapcsolatok sikeresen létrejöttek, a `CONNACK` következőképpen néz ki:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

A `CONNACK` csomagok tulajdonságai a [MQTT 5 specifikációt](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)követik. Ezek a IoT Hub képességeit tükrözik.

### <a name="authentication"></a>Hitelesítés

Az `Authentication Method` ügyfél tulajdonsága határozza meg, `CONNECT` hogy milyen típusú hitelesítést használ ehhez a kapcsolathoz:

- `SAS` -A közös hozzáférésű aláírás a `CONNECT` `Authentication Data` tulajdonságában van megadva,
- `X509` -az ügyfél az ügyféltanúsítvány-alapú hitelesítésre támaszkodik.

 A hitelesítés sikertelen, ha a hitelesítési módszer nem egyezik az ügyfél konfigurált metódusával IoT Hubban.

> [!NOTE]
> Ehhez az API `Authentication Method` -hoz a csomagban be kell állítani a tulajdonságot `CONNECT` . Ha `Authentication Method` a tulajdonság nincs megadva, a kapcsolat meghiúsul a `Bad Request` válaszban.

A korábbi API-verziókban használt felhasználónév/jelszó hitelesítés nem támogatott.

#### <a name="sas"></a>SAS

SAS-alapú hitelesítés esetén az ügyfélnek meg kell adnia a kapcsolatok környezetének aláírását. Ez igazolja a MQTT-kapcsolatok hitelességét. Az aláírásnak az ügyfél konfigurációjában található két hitelesítési kulcs egyikén kell alapulnia IoT Hub vagy egy [megosztott hozzáférési házirend](iot-hub-devguide-security.md)két megosztott elérési kulcsa egyike.

Az aláírandó karakterláncot a következőképpen kell létrehozni:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` a SNI-bővítményből származik (amelyet a rendszer a TLS-kézfogás során az ügyfél által a Hello rekordban nyújt) vagy `host` a csomag felhasználói tulajdonsága `CONNECT` .
- `Client Id` az ügyfél-azonosító a `CONNECT` csomagban.
- `sas-policy` – Ha van ilyen, meghatározza a hitelesítéshez használt IoT Hub hozzáférési szabályzatot. A csomag felhasználói tulajdonságként van kódolva `CONNECT` . Nem kötelező: a beállítás kihagyása azt jelenti, hogy a rendszer a hitelesítési beállításokat használja az eszköz beállításjegyzékében.
- `sas-at` -Ha van, adja meg a kapcsolatok idejét – aktuális idő. Ez a csomag típusú felhasználói tulajdonságként van kódolva `time` `CONNECT` .
- `sas-expiry` a hitelesítés lejárati idejét határozza meg. A (z `time` ) a csomagban megadott felhasználói tulajdonság `CONNECT` . Ezt a tulajdonságot kötelező megadni.

Ha nem adja meg a választható paramétereket, akkor az aláíráshoz az üres karakterláncot kell használni.

A HMAC-SHA256 a karakterláncnak az eszköz szimmetrikus kulcsai alapján történő kivonatolására szolgál. Ezt követően a kivonatoló érték a tulajdonság értékeként van beállítva `Authentication Data` .

#### <a name="x509"></a>X509

Ha a `Authentication Method` tulajdonság értéke `X509` , IoT hub hitelesíti a hozzáférést a megadott ügyféltanúsítvány alapján.

#### <a name="reauthentication"></a>Újrahitelesítés

Ha SAS-alapú hitelesítést használ, javasoljuk a rövid élettartamú hitelesítési tokenek használatát. A kapcsolat hitelesítésének megtartása és a lejárati idő lejárta miatti leválasztásának megakadályozása érdekében az ügyfélnek újra kell hitelesítenie magát a `AUTH` csomagok `Reason Code: 0x19` (újrahitelesítés) küldésével

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Szabályok

- `Authentication Method` meg kell egyeznie a kezdeti hitelesítéshez használt értékkel
- Ha a kapcsolat eredetileg a SAS használatával lett hitelesítve a megosztott hozzáférési házirend alapján, az újrahitelesítés során használt aláírásnak ugyanarra a szabályzatra kell alapulnia.

Ha az újrahitelesítés sikeres, IoT Hub `AUTH` a (sikeres) csomagot küldi el `Reason Code: 0x00` . Ellenkező esetben IoT Hub `DISCONNECT` `Reason Code: 0x87` a csomagot (nem hitelesítve) küldi, és bezárja a kapcsolatát.

### <a name="disconnection"></a>Lekapcsolás

A kiszolgáló több okból is leválaszthatja az ügyfelet:

- az ügyfél olyan módon működik, amely nem tud válaszolni a negatív visszaigazolásra (vagy válaszra) közvetlenül,
- a kiszolgáló nem tartja naprakészen a kapcsolatok állapotát.
- ugyanazzal az identitással rendelkező ügyfél csatlakoztatva van.

A kiszolgáló leválaszthatja a MQTT 5,0 specifikációban definiált bármilyen okkódot. Jelentős említések:

- `135` (Nincs engedélyezve) Ha az újrahitelesítés meghiúsul, a jelenlegi SAS-token lejár vagy az eszköz hitelesítő adatai módosulnak.
- `142` (Munkamenet átvétele) az azonos ügyfél-identitással rendelkező új kapcsolat megnyitásakor.
- `159` (Túllépte a kapcsolatok sebességét), ha a IoT hub kapcsolódási sebessége meghaladja a  
- `131` (Implementáció-specifikus hiba) az API-ban definiált bármely egyéni hiba esetén használatos. `status` és `reason` a tulajdonságok a leválasztási ok további részleteinek közlésére lesznek felhasználva (lásd a [Válasz](#response) részleteit).

## <a name="operations"></a>Üzemeltetés

Az API összes funkciója műveletként van megadva. Az alábbi példa a telemetria művelet küldését szemlélteti:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Az API működésének részletes leírását lásd: [API-referenciák](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Az ebben a specifikációban szereplő összes minta megjelenik az ügyfél szemszögéből. `->`A jel azt jelenti, hogy ügyfél-küldő csomag, `<-` fogadás.

### <a name="message-topics-and-subscriptions"></a>Üzenetekkel kapcsolatos témakörök és előfizetések

Az ebben az API-ban a műveleti üzenetekben használt témakörök a következővel kezdődnek: `$iothub/` .
A MQTT-átvitelszervező szemantikai adatai nem érvényesek ezekre a műveletekre (a részletekért lásd a "[témakörök \$ ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)" című témakört).
Az `$iothub/` ebben az API-ban nem definiált témakörök nem támogatottak:

- A nem definiált témakörre küldött üzenetek `Not Found` válaszként [való küldése](#response) (lásd az alábbi részleteket),
- A nem definiált témakörre való feliratkozás a következővel eredményezi a- `SUBACK` t: `Reason Code: 0x8F` (a témakör szűrője érvénytelen).

A témakörök nevei és a tulajdonságok nevei megkülönböztetik a kis-és nagybetűket, és pontosan egyezniük kell egymással. A nem támogatja például a következőt: `$iothub/telemetry/` `$iothub/telemetry` .

> [!NOTE]
> Az előfizetésekben található helyettesítő karakterek `$iothub/..` nem támogatottak. Az ügyfél nem tud előfizetni a vagy a rendszerre `$iothub/+` `$iothub/#` . Ennek a következménye a `SUBACK` `Reason Code: 0xA2` (helyettesítő karakteres előfizetések nem támogatott). Csak egyszegmenses helyettesítő karakterek ( `+` ) támogatottak az elérési út paraméterei helyett a témakör neve alatt az azokat futtató műveletekhez.

### <a name="interaction-types"></a>Interakció típusa

Az API összes művelete a következő két interakciós típus egyikén alapul:

- Üzenet opcionális nyugtával (MessageAck)
- Request-Response (ReqRep)

A műveletek az irány alapján is változhatnak (az Exchange-ben a kezdeti üzenet iránya határozza meg):

- Ügyfél – kiszolgáló (c2s)
- Kiszolgáló – ügyfél (s2c)

Például a Send telemetria az "üzenet nyugtával" típusú ügyfél-kiszolgáló művelete, a Direct metódus kezelése pedig Request-Response típusú kiszolgáló – ügyfél művelet.

#### <a name="message-acknowledgement-interactions"></a>Üzenet – nyugtázási interakciók

A választható nyugtázási (MessageAck) interakcióval rendelkező üzenet a `PUBLISH` MQTT-ben és a-ban található csomagok cseréjével van kifejezve `PUBACK` . A visszaigazolás nem kötelező, és a küldő dönthet úgy, hogy nem kéri a csomag küldésével `PUBLISH` `QoS: 0` .

> [!NOTE]
> Ha `PUBACK` a csomagban lévő tulajdonságokat a-ügyfél deklarálása miatt le kell vágni `Maximum Packet Size` , a IoT hub annyi felhasználói tulajdonságot fog megőrizni, amennyi a megadott korláton belül elfér. Az elsőként felsorolt felhasználói tulajdonságokat nagyobb eséllyel kell elküldeni, mint a későbbiekben. `Reason String` a tulajdonságnak a legkevésbé prioritása van.

##### <a name="example-of-simple-messageack-interaction"></a>Egyszerű MessageAck-interakció – példa

Üzenet:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Visszaigazolás (sikeres):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response interakciók

Request-Response (ReqRep) interakciók esetén a kérelmek és válaszok is a `PUBLISH` csomaggal együtt fordíthatók le `QoS: 0` .

`Correlation Data` a tulajdonságot mindkettőben be kell állítani, és a válasz-csomagnak a csomagok igényléséhez való egyeztetésére szolgál.

Ez az API egyetlen válasz témakört használ `$iothub/responses` az összes ReqRep művelethez. A jelen témakörben az ügyfél és a kiszolgáló közötti műveletekre való Feliratkozás/Leiratkozás nem kötelező – a kiszolgáló feltételezi, hogy minden ügyfél előfizetésre kerül.

##### <a name="example-of-simple-reqrep-interaction"></a>Egyszerű ReqRep-interakció – példa

Kérés:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Válasz (sikeres):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

A ReqRep-interakciók nem támogatják `PUBLISH` a `QoS: 1` kérelmeket és válaszüzeneteket tartalmazó csomagokat. A kérelem küldésének `PUBLISH` eredménye `Bad Request` válasz.

A tulajdonságban támogatott maximális hossz `Correlation Data` 16 bájt. Ha `Correlation Data` a csomag tulajdonsága `PUBLISH` 16 bájtnál hosszabb értékre van állítva, a IoT hub `DISCONNECT` a `Bad Request` kimenetet küldi, és bezárja a kapcsolódást. Ez a viselkedés csak az ezen az API-n belül kicserélt csomagokra vonatkozik.

> [!NOTE]
> A korrelációs adatmennyiség egy tetszőleges bájtos sorozat, például nem garantált UTF-8 sztring.
>
> A ReqRep előre definiált témaköröket használ a válaszhoz; A Request `PUBLISH` csomag (ha a feladó által beállított) válasz témakör tulajdonsága figyelmen kívül lesz hagyva.

IoT Hub automatikusan előfizet az ügyfélnek az összes ügyfél és kiszolgáló közötti ReqRep műveletre vonatkozó válaszra. Még ha az ügyfél explicit módon lemond a válaszról, IoT Hub visszaállítja az előfizetést automatikusan. A kiszolgálók közötti ReqRep-interakciók esetében továbbra is szükség van az eszköz előfizetésére.

### <a name="message-properties"></a>Üzenet tulajdonságai

Művelet tulajdonságai – a rendszer vagy a felhasználó által definiált – a MQTT 5 csomag tulajdonságaiként van megadva.

A felhasználói tulajdonságok nevei megkülönböztetik a kis-és nagybetűket, és pontosan a megadott módon kell megadni őket. A nem támogatja például a következőt: `Trace-ID` `trace-id` .

A specifikáción kívüli felhasználói tulajdonságokkal rendelkező kérések és az előtag hiánya `@` hibát eredményez.

A rendszertulajdonságokat a rendszer az első osztály tulajdonságai (például `Content Type` ) vagy a felhasználói tulajdonságok szerint kódolja. A specifikáció a támogatott Rendszertulajdonságok részletes listáját tartalmazza.
Az első osztály összes tulajdonságát figyelmen kívül hagyja a rendszer, kivéve, ha a támogatás a specifikációban explicit módon szerepel.

Ha a felhasználó által definiált tulajdonságok engedélyezettek, a nevüknek meg kell felelnie a formátumnak `@{property name}` . A felhasználó által definiált tulajdonságok csak az érvényes UTF-8 karakterlánc-értékeket támogatják. például az `MyProperty1` értékkel rendelkező tulajdonságot a `15` name `@MyProperty` és Value értékű felhasználói tulajdonságként kell kódolni `15` .

Ha IoT Hub nem ismeri fel a felhasználói tulajdonságot, a rendszer hibát jelez, és IoT Hub válaszol a `PUBACK` `Reason Code: 0x83` következővel: (implementáció-specifikus hiba) és `status: 0100` (hibás kérés). Ha a rendszer nem kért nyugtát (QoS: 0), akkor `DISCONNECT` a rendszer visszaküldi a csomagot ugyanazzal a hibával, és a kapcsolat megszakad.

Ez az API a következő adattípusokat határozza meg a szolgáltatáson kívül `string` :

- `time`: ezredmásodpercek száma óta `1970-01-01T00:00:00.000Z` . például `1600987195320` : `2020-09-24T22:39:55.320Z` ,
- `u32`: előjel nélküli 32 bites egész szám,
- `u64`: előjel nélküli 64 bites egész szám,
- `i32`: aláírt 32 bites egész szám.

### <a name="response"></a>Reagálás

Az interakciók különböző eredményekhez vezethetnek:,, `Success` `Bad Request` `Not Found` és mások.
A kimenetek a felhasználói tulajdonságok alapján különböztethetők meg egymástól `status` . `Reason Code` a `PUBACK` csomagok (MessageAck-interakciók esetében) a `status` jelentésekben szerepelnek, ahol lehetséges.

> [!NOTE]
> Ha az ügyfél `Request Problem Information: 0` a kapcsolódási csomagot adja meg, a rendszer nem küld felhasználói tulajdonságokat a `PUBACK` MQTT 5 specifikációnak, beleértve a `status` tulajdonságot. Ebben az esetben az ügyfél továbbra is támaszkodhat `Reason Code` annak megállapítására, hogy pozitív vagy negatív-e az elismerés. 

Minden interakció alapértelmezett (vagy sikeres). A (z) és a (z `Reason Code` `0` `status` ) "nincs beállítva" tulajdonsággal rendelkezik. Egyéb esetben:

- A MessageAck-interakciók esetén `PUBACK` a `Reason Code` 0x0 (sikeres) értéktől eltérő lesz. `status` Előfordulhat, hogy a tulajdonság tovább pontosítja az eredményt.
- A ReqRep-interakciók esetében a `PUBLISH` Válasz `status` tulajdonságot kap.
- Mivel nem lehet közvetlenül reagálni a MessageAck interakcióra `QoS: 0` , a `DISCONNECT` csomagokat a rendszer a válasz adataival együtt, majd a kapcsolat bontása után is elküldi.

Angol nyelvű Példák:

Hibás kérelem (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Nincs engedélyezve (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Nincs engedélyezve (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Ha szükséges, IoT Hub a következő felhasználói tulajdonságokat állítja be:

- `status` -IoT Hub kiterjesztett kódja a művelet állapotához. Ez a kód az eredmények megkülönböztetésére használható.
- `trace-id` – a művelet nyomkövetési azonosítója; IoT Hub a belső vizsgálathoz használható művelettel kapcsolatos további diagnosztikát is tarthat.
- `reason` – ember által olvasható üzenet, amely további információkat nyújt arról, hogy a művelet miért fejeződött be a tulajdonság által jelzett állapotban `status` .

> [!NOTE]
> Ha az ügyfél `Maximum Packet Size` egy nagyon kis értékre állítja be a kapcsolódási csomag tulajdonságát, akkor nem minden felhasználói tulajdonság fér hozzá, és nem jelenik meg a csomagban.
> 
> `reason` kizárólag a felhasználók számára készült, és nem használható az ügyfél logikájában. Ez az API lehetővé teszi, hogy az üzenetek bármikor megváltoznak a figyelmeztetés vagy a verzió módosítása nélkül.
>
> Ha az ügyfél `RequestProblemInformation: 0` a csatlakozási csomagot küldi, a felhasználói tulajdonságok nem lesznek belefoglalva a [MQTT 5 specifikációban](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Állapotkód

`status` a tulajdonság a művelethez tartozó állapotkódot hordozza. A gépi olvasás hatékonyságára van optimalizálva.
Két bájtos előjel nélküli egész számból áll, mint hexadecimális karakterlánc `0501` .
Kód szerkezete (bit map):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

Az első bájt a jelzők esetében használható:

- a BITS 0 és 1 érték a kimenetek típusát jelzi:
  - `00` – sikeres
  - `01` – ügyfél-hiba
  - `10` – kiszolgálóhiba
- 2. bit: `1` azt jelzi, hogy a hiba újrapróbálható
- a 3 – 7. BITS foglalt, és a következőre kell beállítani `0`

A második bájt tényleges külön hibakódot tartalmaz. A különböző jelzőket tartalmazó hibakódok ugyanazzal a második bájt értékkel rendelkezhetnek. Például a, a `0001` `0101` `0201` , a `0301` hibakódok eltérő jelentéssel rendelkezhetnek.

Például `Too Many Requests` egy ügyfél, újrapróbálkozást lehetővé tevő hiba a saját kódjával `1` . Értéke `0000 0101 0000 0001` vagy `0x0501` .

Előfordulhat, hogy az ügyfelek a BITS típus használatával határozzák meg, hogy a művelet sikeresen létrejött-e. Az ügyfelek újrapróbálkozást lehetővé tevő bitet is használhatnak annak eldöntéséhez, hogy ésszerű-e az újrapróbálkozási művelet.

## <a name="recommendations"></a>Javaslatok

### <a name="session-management"></a>Munkamenet-kezelés

`CONNACK` a csomagok `Session Present` tulajdonsága jelzi, hogy a kiszolgáló visszaállította-e a korábban létrehozott munkamenetet. Ezzel a tulajdonsággal kiderítheti, hogy előfizethet-e a témakörökre, vagy kihagyhatja az előfizetést, mert korábban megtörtént az előfizetés.

A kihasználása érdekében `Session Present` az ügyfélnek nyomon kell követnie az általa készített előfizetéseket (azaz az elküldött `SUBSCRIBE` csomagokat, és `SUBACK` sikeres okkódot kapott), vagy mindenképpen elő kell fizetnie az egyetlen Exchange-ben található összes témakörre `SUBSCRIBE` / `SUBACK` . Ellenkező esetben, ha az ügyfél két `SUBSCRIBE` csomagot küld, és csak az egyiket dolgozza fel sikeresen a kiszolgáló, akkor a kiszolgáló kommunikálni fog, `Session Present: 1` `CONNACK` miközben csak az ügyfél előfizetésének egy részét fogadja el.

Ha meg szeretné akadályozni, hogy az ügyfél egy régebbi verziója ne fizessen elő az összes témakörre, érdemes feltétlenül előfizetni az ügyfél viselkedésének változásakor (például a belső vezérlőprogram frissítésének részeként). Továbbá annak érdekében, hogy az elavult előfizetések ne maradnak le (a maximálisan megengedett számú előfizetés miatt), explicit módon leiratkozhat a már nem használt előfizetésekről.

### <a name="batching"></a>Kötegelés

Az üzenetek kötegét nem lehet külön formátumban elküldeni. A TLS-és hálózatkezelési erőforrás-igényes műveletek terhelésének csökkentése érdekében a csomagok ( `PUBLISH` , `PUBACK` , `SUBSCRIBE` és így tovább) együttesen, a mögöttes TLS/TCP-verembe való átadásuk előtt. Emellett az ügyfél a "batch"-ben könnyebben elvégezheti a témakör aliasát:

- A `PUBLISH` kapcsolat első csomagjában adja meg a témakör teljes nevét, és társítsa a témakör aliasát,
- Helyezze a következő csomagokat ugyanahhoz a témakörhöz, és adjon meg egy üres témakört, valamint a témakör alias tulajdonságát.

## <a name="migration"></a>Migrálás

Ez a szakasz az API változásait sorolja fel a [korábbi MQTT API](iot-hub-mqtt-support.md)-hoz képest.

- Az átviteli protokoll MQTT 5. Korábban – MQTT 3.1.1.
- Az SAS-hitelesítés környezeti információi közvetlenül a csomagban találhatók az `CONNECT` aláírással együtt.
- A hitelesítési módszer a használt hitelesítési módszer jelzésére szolgál.
- A közös hozzáférésű aláírás a hitelesítési adattulajdonságba kerül. A rendszer korábban a jelszó mezőt használta.
- A műveletekkel kapcsolatos témakörök különbözőek:
  - Telemetria: `$iothub/telemetry` helyett `devices/{Client Id}/messages/events` ,
  - Parancsok: `$iothub/commands` a helyett `devices/{Client Id}/messages/devicebound`
  - Patch Twin jelentett: `$iothub/twin/patch/reported` a helyett `$iothub/twin/PATCH/properties/reported`
  - Értesítés a Twin kívánt állapotról: `$iothub/twin/patch/desired` helyett `$iothub/twin/PATCH/properties/desired` .
- Nincs szükség az ügyfél-kiszolgáló kérés – válasz műveletekre vonatkozó válaszra.
- A felhasználói tulajdonságok a témakör Name szegmensében a kódolási tulajdonságok helyett használatosak.
- a tulajdonságok neve "Dash-Case" elnevezési konvencióban van megadva, a speciális előtaggal rendelkező rövidítések helyett. A felhasználó által definiált tulajdonságok mostantól előtagot igényelnek. Ekkor például a következő `$.mid` `message-id` lesz: `myProperty1` `@myProperty1` .
- A korrelációs adatok tulajdonság a kérelem és a válaszüzenetek összekapcsolására szolgál a kérelem-válasz műveletekhez a `$rid` témakörben kódolt tulajdonság helyett.
- `iothub-connection-auth-method` a tulajdonság már nem a telemetria eseményekre van lepecsételve.
- Az C2D parancsok nem törlődnek az eszközről való előfizetés hiányában. A várólistán maradnak, amíg az eszköz fel nem iratkozik vagy lejár.

## <a name="examples"></a>Példák

### <a name="send-telemetry"></a>Telemetria küldése

Üzenet:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Tudomásul vétele

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternatív visszaigazolás (szabályozott):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>A Get Twin-állapot küldése

Kérés:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Válasz (sikeres):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Válasz (nem engedélyezett):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Közvetlen metódus hívásának kezelése

Kérés:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Válasz (sikeres):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` nincs beállítva – sikeres válasz.

Az eszköz nem érhető el Válasz:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Hiba történt a QoS 0 használatakor, 1. rész

Kérés:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Válasz:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Hiba történt a QoS 0 használata közben, 2. rész

Kérés:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Válasz:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Következő lépések

- A MQTT 5 előzetes verzió API-referenciájának áttekintéséhez tekintse meg a [IoT hub adatsík MQTT 5 API-referenciát](iot-hub-mqtt-5-reference.md).
- A C# minta követéséhez lásd: [GitHub-minta tárház](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).