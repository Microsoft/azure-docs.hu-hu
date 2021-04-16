---
title: Közzététel és feliratkozás Azure IoT Edge | Microsoft Docs
description: Üzenetek IoT Edge és előfizetése az MQTT-közvetítő használatával
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1c4760362e7c2b3965638b3213910b5b8cd6f079
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516178"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Közzététel és feliratkozás Azure IoT Edge (előzetes verzió)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Az MQTT Azure IoT Edge használatával közzétehet és feliratkozhat üzeneteket. Ez a cikk bemutatja, hogyan csatlakozhat ehhez a közvetítőhez, tehet közzé és iratkhat fel üzenetekre felhasználó által meghatározott témakörökben, és hogyan használhatja IoT Hub üzenetkezelési primitíveket. A IoT Edge MQTT-közvetítő beépített IoT Edge központba. További információkért lásd a központ közvetítői képességeit [IoT Edge.](iot-edge-runtime.md)

> [!NOTE]
> IoT Edge MQTT-közvetítő jelenleg nyilvános előzetes verzióban érhető el.

## <a name="pre-requisites"></a>Előfeltételek

- Azure-fiók érvényes előfizetéssel
- [Azure CLI](/cli/azure/) telepített `azure-iot` CLI-bővítvekkel. További információ: [Az Azure IoT-bővítmény telepítési lépései az Azure CLI-hez.](/cli/azure/azure-cli-reference-for-iot)
- Az **IoT Hub** F1, S1, S2 vagy S3 termékváltozatok valamelyike.
- **1.2-es IoT Edge** vagy újabb verziójú IoT Edge kell, hogy legyen. Mivel IoT Edge MQTT-közvetítő jelenleg nyilvános előzetes verzióban érhető el, az alábbi környezeti változókat állítsa true (igaz) értékre az edgeHub-tárolón az MQTT-közvetítő engedélyezéséhez:

   | Name | Érték |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- A IoT Edge eszközön telepített **Mosquitto-ügyfelek.** Ez a cikk a népszerű Mosquitto-ügyfeleket használja [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) és [MOSQUITTO_SUB.](https://mosquitto.org/man/mosquitto_sub-1.html) Ehelyett más MQTT-ügyfelek is használhatók. A Mosquitto-ügyfelek Ubuntu-eszközön való telepítéséhez futtassa a következő parancsot:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Ne telepítse a Mosquitto-kiszolgálót, mert ez az MQTT-portok blokkolását okozhatja (8883 és 1883), és ütközhet az IoT Edge központtal.

## <a name="connect-to-iot-edge-hub"></a>Csatlakozás IoT Edge hubhoz

Az IoT Edge hubhoz való csatlakozás ugyanezeket a lépéseket követi, mint a csatlakozás az IoT Hub-hoz általános [MQTT-ügyféllel](../iot-hub/iot-hub-mqtt-support.md) cikkben vagy az IoT Edge hubról cikk fogalmi [leírásában.](iot-edge-runtime.md) A lépések a következőek:

1. Szükség esetén az MQTT-ügyfél biztonságos kapcsolatot létesít *a* IoT Edge központtal a Transport Layer Security (TLS) használatával
2. Az MQTT-ügyfél *hitelesíti* magát az IoT Edge központban
3. A IoT Edge központ *az* engedélyezési házirendnek megfelelő MQTT-ügyfelet engedélyezi

### <a name="secure-connection-tls"></a>Biztonságos kapcsolat (TLS)

Transport Layer Security (TLS) segítségével titkosított kommunikációt létesít az ügyfél és a IoT Edge között.

A TLS letiltásához használja az 1883-as (MQTT) portot, és kösse az edgeHub-tárolót az 1883-as porthoz.

A TLS engedélyezéséhez, ha egy ügyfél a 8883-as porton (MQTTS) csatlakozik az MQTT-közvetítőhöz, egy TLS-csatorna lesz kezdeményezve. A közvetítő elküldi a tanúsítványláncot, amit az ügyfélnek ellenőriznie kell. A tanúsítványlánc érvényesítéséhez az MQTT-közvetítő főtanúsítványát megbízható tanúsítványként kell telepíteni az ügyfélen. Ha a főtanúsítvány nem megbízható, az MQTT-közvetítő elutasítja az ügyfélkódtárat egy tanúsítvány-ellenőrzési hibával. A közvetítő főtanúsítványának az ügyfélen való telepítéséhez [](how-to-create-transparent-gateway.md) szükséges lépések ugyanazok, mint a transzparens átjárók esetében, és a leírását az lefelé irányuló eszköz előkészítése [dokumentációban ismertetjük.](how-to-connect-downstream-device.md#prepare-a-downstream-device)

### <a name="authentication"></a>Hitelesítés

Ahhoz, hogy egy MQTT-ügyfél hitelesítse magát, először CONNECT csomagot kell küldenie az MQTT-közvetítőnek, hogy kezdeményezzen egy kapcsolatot a nevében. Ez a csomag három hitelesítési információt biztosít: egy `client identifier` , egy és egy `username` `password` :

- A mező az eszköz vagy a modul neve `client identifier` a IoT Hub. A következő szintaxist használja:

  - Eszközhöz: `<device_name>`

  - Modul esetén: `<device_name>/<module_name>`

   Az MQTT-közvetítőhöz való csatlakozáshoz regisztrálni kell egy eszközt vagy modult a IoT Hub.

   A közvetítő nem engedélyezi az azonos hitelesítő adatokat használó több ügyféltől származó kapcsolatokat. A közvetítő leválasztja a már csatlakoztatott ügyfelet, ha egy második ügyfél ugyanazokkal a hitelesítő adatokkal csatlakozik.

- A mező az eszköz vagy a modul nevéből származik, és annak az IoTHub-névnek a használatával, amelyhez az eszköz tartozik, a `username` következő szintaxis használatával:

  - Eszközhöz: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Modul esetén: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- A `password` CONNECT csomag mezője a hitelesítési módtól függ:

  - Szimmetrikus [kulcsok hitelesítése esetén](how-to-authenticate-downstream-device.md#symmetric-key-authentication)a mező egy `password` SAS-jogkivonat.
  - [X.509 önaírt](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)hitelesítés használata esetén a mező `password` nincs jelen. Ebben a hitelesítési módban TLS-csatornára van szükség. Az ügyfélnek a 8883-as porthoz kell csatlakoznia a TLS-kapcsolat létesítése érdekében. A TLS-kézfogás során az MQTT-közvetítő egy ügyfél-tanúsítványt kér. Ezzel a tanúsítvánnyal ellenőrizhető az ügyfél identitása, így a mezőre később nincs szükség, amikor a CONNECT csomag `password` el lesz küldve. Ha az ügyfél tanúsítványát és a jelszó mezőt is elküldi, az hibát jelez, és a kapcsolat lezárul. Az MQTT-kódtárak és a TLS-ügyfélkódtárak általában okkal küldhetnek ügyfél-tanúsítványt a kapcsolat kezdeményezésekor. Részletes példát az [X509-tanúsítvány](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)használata ügyfél-hitelesítéshez című szakaszban láthat.

Az IoT Edge által üzembe helyezett [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) modulok szimmetrikus kulcsok hitelesítését használják, és a helyi IoT Edge számítási feladat [API-jának](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) hívásával programozott módon sas-jogkivonatot kaphatnak offline állapotban is.

### <a name="authorization"></a>Engedélyezés

Miután az MQTT-ügyfelet hitelesítette IoT Edge hubon, engedélyeznie kell a csatlakozást. A csatlakozás után jogosultnak kell lennie arra, hogy közzétessen vagy előfizetsen adott témakörökre. Ezeket az engedélyeket a IoT Edge az engedélyezési házirend alapján. Az engedélyezési szabályzat egy JSON-struktúraként kifejezett utasítások halmaza, amely az ikereszközével IoT Edge központnak. Szerkesszen egy IoT Edge ikereszközt az engedélyezési házirend konfigurálásához.

> [!NOTE]
> A nyilvános előzetes verzióban csak az Azure CLI támogatja az MQTT-közvetítő engedélyezési szabályzatokat tartalmazó üzemelő példányokat. A Azure Portal jelenleg nem támogatja a IoT Edge ikereszköz és az engedélyezési házirend szerkesztését.

Minden engedélyezési házirend-utasítás a , vagy hatás , és `identities` `allow` `deny` `operations` kombinációjából `resources` áll:

- `identities` írja le a szabályzat tárgyát. Az ügyfeleknek a `client identifier` CONNECT-csomagjukban küldött adatokat kell leképeznie.
- `allow` A `deny` vagy a hatás határozza meg, hogy engedélyezni vagy megtagadni kell-e a műveleteket.
- `operations` határozza meg az engedélyezni szükséges műveleteket. `mqtt:connect`A `mqtt:publish` és a a jelenleg támogatott három `mqtt:subscribe` művelet.
- `resources` határozza meg a szabályzat objektumát. Ez lehet egy [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)helyettesítő karakterekkel definiált témakör vagy témakörminta.

Az alábbi példában egy olyan engedélyezési szabályzat látható, amely kifejezetten nem engedélyezi az "rogue_client" ügyfél csatlakozását, lehetővé teszi az Azure IoT-ügyfelek csatlakozását, és engedélyezi a "sensor_1" számára a témakörben való `events/alerts` közzétételét.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Néhány dolog, amit szem előtt kell tartani az engedélyezési szabályzat megírásakor:

- Ehhez az `$edgeHub` ikerséma 1.2-es verziója szükséges
- Alapértelmezés szerint minden művelet le van tiltva.
- Az engedélyezési utasításokat a rendszer a JSON-definícióban megjelenő sorrendben értékeli ki. Először meg kell keresnie, majd ki kell választania az első engedélyező vagy megtagadási utasításokat, amelyek megfelelnek `identities` a kérésnek. Az engedélyező és a megtagadási utasítások közötti ütközések esetén a megtagadási utasítás nyer.
- Az engedélyezési házirendben számos változó (például helyettesítés) használható:

  - `{{iot:identity}}` A az aktuálisan csatlakoztatott ügyfél identitását jelöli. Ilyen lehet például egy eszközidentitás, vagy `myDevice` egy modulidentitás, például `myEdgeDevice/SampleModule` .
  - `{{iot:device_id}}` A az aktuálisan csatlakoztatott eszköz identitását jelöli. Ilyen lehet például egy eszközidentitás, vagy egy olyan eszközidentitás, ahol `myDevice` a modul fut, `myEdgeDevice` például: .
  - `{{iot:module_id}}` A a jelenleg csatlakoztatott modul identitását jelöli. Ez a változó a csatlakoztatott eszközökhöz vagy egy modulidentitáshoz (például ) `SampleModule` üres.
  - `{{iot:this_device_id}}` A az engedélyezési házirendet futtató IoT Edge identitását jelöli. Például: `myIoTEdgeDevice`.

Az IoT Hub-témakörökre vonatkozó engedélyek kezelése némileg eltér a felhasználó által meghatározott témaköröktől. A következő fontos pontokat kell megjegyeznie:

- Az Azure IoT-eszközöknek vagy -moduloknak kifejezett engedélyezési szabályra van szükségük az IoT Edge MQTT-közvetítőhez való csatlakozáshoz. Alább egy alapértelmezett kapcsolódási engedélyezési szabályzatot biztosítunk.
- Az Azure IoT-eszközök vagy -modulok alapértelmezés szerint hozzáférhetnek a saját IoT Hub-témaköreikhez explicit engedélyezési szabályok nélkül. Ebben az esetben azonban az engedélyezés a szülő-gyermek kapcsolatokból ered, és ezeket a kapcsolatokat be kell állítani. IoT Edge modulok automatikusan gyermekként lesznek beállítva az IoT Edge-eszközükhöz, de az eszközöket explicit módon az átjáró gyermekelemeként IoT Edge beállítani.

Használhatja az alapértelmezett engedélyezési szabályzatot, amely lehetővé teszi, hogy az összes Azure IoT-eszköz vagy -modul csatlakozzon **a** közvetítőhez:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Most, hogy már tudja, hogyan csatlakozhat az IoT Edge MQTT-közvetítőhez, nézzük meg, hogyan használható az üzenetek felhasználó által definiált témakörökben, majd IoT Hub-témakörökben, végül egy másik MQTT-közvetítőn való közzétételére és feliratkozatára.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Felhasználó által definiált témakörök közzététele és feliratkozás

Ebben a cikkben egy sub_client nevű  ügyfelet fog használni, amely feliratkozik  egy témakörre, és egy pub_client nevű ügyfelet, amely közzétesz egy témakörben. A szimmetrikus [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) kulcsos hitelesítést fogjuk használni, de ez [X.509 önaírt](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) hitelesítéssel vagy [X.509 hitelesítésszolgáltató](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)által aláírt hitelesítéssel is meg lehet tenni.

### <a name="create-publisher-and-subscriber-clients"></a>Közzétevő és előfizető ügyfelek létrehozása

Hozzon létre két IoT-eszközt a IoT Hub és szerezze be a jelszavukat. Az Azure CLI használata a terminálból a következőre:

1. Hozzon létre két IoT-eszközt a IoT Hub, és a szülőeszközre IoT Edge őket:

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```

2. Sas-jogkivonat generálása a jelszavuk le szolgáltatáshoz:

   - Eszközhöz:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     ahol a 3600 az SAS-jogkivonat másodpercben megadott időtartama (például 3600 = 1 óra).

   - Modul esetén:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     ahol a 3600 az SAS-jogkivonat másodpercben megadott időtartama (például 3600 = 1 óra).

3. Másolja ki az SAS-jogkivonatot, amely a kimenetből származó "sas" kulcsnak megfelelő érték. Példa a fenti Azure CLI-parancs kimenetére:

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Közzétevői és előfizetői ügyfelek jogosultsága

A közzétevő és az előfizető hitelesítéséhez szerkessze a IoT Edge-központot egy olyan központi IoT Edge, amely a következő engedélyezési szabályzatot tartalmazza.

>[!NOTE]
>Jelenleg az MQTT-hitelesítési tulajdonságokat tartalmazó üzemelő példányok csak az Azure CLI IoT Edge t használó IoT Edge alkalmazhatók.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Szimmetrikus kulcsok hitelesítése TLS nélkül

#### <a name="subscribe"></a>Feliratkozás

Csatlakoztassa a **sub_client** MQTT-ügyfelet az MQTT-közvetítőhöz, és iratkozzon fel rá a következő parancs futtatásával a `test_topic` IoT Edge eszközön:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

ahol ebben a példában, mivel az ügyfél ugyanazon az eszközön `<edge_device_address>`  =  `localhost` fut, mint IoT Edge.

Vegye figyelembe, hogy ebben az első példában a TLS nélküli 1883-as (MQTT) portot használjuk. A következő szakaszban egy másik példa látható a 8883-as (MQTTS) porttal, engedélyezett TLS-sel.

A **sub_client** MQTT-ügyfél most elindult, és a bejövő üzenetekre vár a következőn: `test_topic` .

#### <a name="publish"></a>Közzététel

Csatlakoztassa **pub_client** MQTT-ügyfelet az MQTT-közvetítőhöz, és tegyen közzé egy üzenetet a fentiekhez hasonlón a következő parancs futtatásával IoT Edge eszközön egy másik `test_topic` terminálból:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

ahol ebben a példában, mivel az ügyfél ugyanazon az eszközön `<edge_device_address>`  =  `localhost` fut, mint IoT Edge.

A parancs végrehajtása után a **sub_client** MQTT-ügyfél megkapja a "hello" üzenetet.

### <a name="symmetric-keys-authentication-with-tls"></a>Szimmetrikus kulcsok hitelesítése TLS-sel

A TLS engedélyezéséhez a portot a 1883-asról (MQTT) 8883-ra (MQTTS) kell módosítani, és az ügyfeleknek az MQTT-közvetítő főtanúsítványát kell használniuk az MQTT-közvetítő által küldött tanúsítványlánc érvényesítéséhez. Ehhez kövesse a Biztonságos kapcsolat [(TLS)](#secure-connection-tls)című szakaszban megadott lépéseket.

Mivel az ügyfelek ugyanazon az eszközön futnak, mint a fenti példában látható MQTT-közvetítő, ugyanezek a lépések érvényesek a TLS engedélyezésére is úgy, hogy a portszámot 1883-ról 8883-ra (MQTTS) módosítjuk.

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Közzététel és feliratkozás IoT Hub témakörökre

Az [Azure IoT eszközoldali SDK-k](https://github.com/Azure/azure-iot-sdks) már lehetővé teszik IoT Hub-műveletek elvégzését az ügyfelek számára, de nem teszik lehetővé a felhasználó által meghatározott témakörök közzétételét/előfizetését. IoT Hub műveletek bármely MQTT-ügyfél használatával elvégezhetők közzétételi és előfizetési szemantikával, felhasználhatja az IoT Hub primitív protokolljait. Végigveszünk a részleteken, hogy megértsük, hogyan működnek ezek a protokollok.

### <a name="send-telemetry-data-to-iot-hub"></a>Telemetriai adatok küldése a IoT Hub

A telemetriai adatok IoT Hub hasonló a felhasználó által meghatározott témakörökben való közzétételhez, de egy adott témakört IoT Hub használni:

- Az eszközök telemetriai adatainak elküldését a következő témakörben kell elküldeni: `devices/<device_name>/messages/events/`
- Modul esetén a telemetria a következő témakörben lesz elküldve: `devices/<device_name>/<module_name>/messages/events/`

Emellett hozzon létre egy útvonalat, például telemetriát küld a IoT Edge `FROM /messages/* INTO $upstream` MQTT-közvetítőről az IoT Hubra. További információ az útválasztásról: [Útvonalak deklarálása.](module-composition.md#declare-routes)

### <a name="get-twin"></a>Iker lekért

Az ikereszköz/modul lekérése nem tipikus MQTT-minta. Az ügyfélnek ki kell kérnie az ikereszközt, IoT Hub az ikereszközt fogja kiszolgálni.

Az ikereszköz fogadása érdekében az ügyfélnek elő kell fizetnie egy IoT Hub `$iothub/twin/res/#` témakörre. A témakör neve a IoT Hub öröklődik, és minden ügyfélnek elő kell fizetnie ugyanannak a témakörnek. Ez nem jelenti azt, hogy az eszközök vagy modulok egymás ikereszközét kapják meg. IoT Hub és IoT Edge hub tudja, hogy melyik ikereszközt kell kézbesíteni, még akkor is, ha az összes eszköz ugyanazt a témakörnevet figyeli.

Az előfizetés után az ügyfélnek egy üzenet egy adott témakörben való közzétételével kell kérnie az ikereszközt, IoT Hub egy `$iothub/twin/GET/?rid=<request_id>/#`  `<request_id>` tetszőleges azonosítóval. Az IoT Hub ezután elküldi a választ a kért adatokkal a témakörben, amelyre az `$iothub/twin/res/200/?rid=<request_id>` ügyfél előfizet. Az ügyfél így párosíthatja a kéréseket a válaszokkal.

### <a name="receive-twin-patches"></a>Ikerjavítások fogadása

Az ikerjavítások fogadása érdekében az ügyfélnek elő kell fizetnie a speciális IoTHub-témakörre. `$iothub/twin/PATCH/properties/desired/#` Az előfizetés után az ügyfél megkapja az ebben a témakörben IoT Hub ikerjavításokat.

### <a name="receive-direct-methods"></a>Közvetlen metódusok fogadása

A közvetlen metódusok fogadása hasonló a teljes ikereszköz fogadáshoz azzal a kiegészítéssel, hogy az ügyfélnek vissza kell erősítenie, hogy megkapta a hívást. Az ügyfél először feliratkozik az IoT Hub speciális `$iothub/methods/POST/#` témakörre. Ha a témakörben közvetlen metódus érkezik, az ügyfélnek ki kellnyerni a kérésazonosítót abból az altémakörből, amelyen a közvetlen metódus érkezik, és végül közzé kell tennie egy megerősítő üzenetet `rid` az IoT Hub speciális témakörében. `$iothub/methods/res/200/<request_id>`

### <a name="send-direct-methods"></a>Közvetlen metódusok küldése

A közvetlen metódusok küldése HTTP-hívás, így nem megy keresztül az MQTT-közvetítőn. Közvetlen metódus Az IoT Hubra való küldését lásd: Közvetlen metódusok használata és [meghívása.](../iot-hub/iot-hub-devguide-direct-methods.md) Közvetlen metódus helyi elküldését egy másik modulnak ebben az [Azure IoT C# SDK közvetlen metódushívási példában láthatja.](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>MQTT-közvetítők közötti közzététel és előfizetés

Két MQTT-közvetítő csatlakoztatásához a IoT Edge egy MQTT-hidat is tartalmaz. Az MQTT-hidat általában arra használják, hogy egy futó MQTT-közvetítőt egy másik MQTT-közvetítőhez csatlakoztassanak. Általában csak a helyi forgalom egy része lesz lekért egy másik közvetítőnek.

> [!NOTE]
> A IoT Edge hubhíd jelenleg csak beágyazott hálózati eszközök között IoT Edge használható. Nem használható az Adatok IoT Hubra való elküldését, mivel az IoT Hub nem teljes körű MQTT-közvetítő. További információ az IoT Hub MQTT közvetítői funkcióinak támogatásával: Kommunikáció az [IoT Hubbal az MQTT protokoll használatával.](../iot-hub/iot-hub-mqtt-support.md) További információ a IoT Edge beágyazásról: Lefelé irányuló IoT Edge [csatlakoztatása Azure IoT Edge átjáróhoz.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

Beágyazott konfigurációban az IoT Edge Hub MQTT-híd a szülő MQTT-közvetítő ügyfeleként működik, ezért az engedélyezési szabályokat a szülő EdgeHubon kell beállítani, hogy a gyermek EdgeHub közzétehet és feliratkozhat olyan, felhasználó által meghatározott témakörökre, amelyekhez a híd konfigurálva van.

A IoT Edge MQTT-híd egy JSON-struktúrával van konfigurálva, amely az ikereszköze révén IoT Edge a központnak. Szerkesszen egy IoT Edge ikereszközt az MQTT-híd konfigurálásához.

> [!NOTE]
> A nyilvános előzetes verzióban csak az Azure CLI támogatja az MQTT-hídkonfigurációkat tartalmazó üzemelő példányokat. A Azure Portal jelenleg nem támogatja a IoT Edge ikereszköz és az MQTT-híd konfigurációjának szerkesztését.

Az MQTT-híd konfigurálható úgy, hogy egy központi MQTT IoT Edge több külső közvetítőhez csatlakoztassa. Minden külső közvetítőnél a következő beállítások szükségesek:

- `endpoint` A annak a távoli MQTT-közvetítőnek a címe, amelyhez csatlakozni szeretne. Jelenleg csak IoT Edge szülőeszközök támogatottak, és a változó határozza `$upstream` meg.
- `settings` meghatározza, hogy mely témaköröket kell áthidalni egy végponthoz. Végpontonként több beállítás is lehet, és a konfigurálás a következő értékekkel lehetséges:
  - `direction`: a távoli közvetítő témaköreire való feliratkozáshoz vagy a távoli közvetítő témaköreibe `in` `out` való közzétételhez
  - `topic`: a fő témakörminta, amely megfeleltetve lesz. [Az MQTT helyettesítő](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) karakterekkel definiálható ez a minta. A témakör mintázatára különböző előtagok alkalmazhatók a helyi közvetítőn és a távoli közvetítőn.
  - `outPrefix`: A távoli közvetítő mintára `topic` alkalmazott előtag.
  - `inPrefix`: A helyi közvetítőn a mintára `topic` alkalmazott előtag.

Az alábbi példa egy IoT Edge MQTT-hídkonfigurációt mutat be, amely újból közzétesz minden üzenetet, amely egy szülő IoT Edge-eszköz témakörében érkezett egy gyermek IoT Edge-eszközre ugyanazon a témakörön, és újból közzétesz minden olyan üzenetet, amely egy gyermek `alerts/#` IoT Edge IoT Edge-eszköz témakörében a következő témakörökben található: `/local/telemetry/#` `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
További megjegyzések a IoT Edge MQTT-hídról:
- Az MQTT protokollt a rendszer automatikusan felfelé irányuló protokollként használja, amikor az MQTT-közvetítőt használja, és az IoT Edge-t beágyazott konfigurációban használja, például egy megadott `parent_hostname` konfigurációban. A felfelé irányuló protokollokkal kapcsolatos további információkért lásd: [Felhőbeli kommunikáció.](iot-edge-runtime.md#cloud-communication) További információ a beágyazott konfigurációkról: Lefelé irányuló IoT Edge csatlakoztatása egy Azure IoT Edge [átjáróhoz.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

## <a name="next-steps"></a>Következő lépések

[A IoT Edge központ](iot-edge-runtime.md#iot-edge-hub)
