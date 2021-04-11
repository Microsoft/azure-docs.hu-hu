---
title: Közzététel és előfizetés Azure IoT Edge | Microsoft Docs
description: Üzenetek közzététele és előfizetése IoT Edge MQTT Broker használatával
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1a251f5718ee91a90ba9ba0e65c5cc7cb39096bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937192"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Közzététel és előfizetés Azure IoT Edge

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Az üzenetek közzétételéhez és előfizetéséhez Azure IoT Edge MQTT Broker használható. Ez a cikk bemutatja, hogyan csatlakozhat ehhez a közvetítőhöz, hogyan tehet közzé és fizethet elő üzeneteket a felhasználó által definiált témakörökön keresztül, valamint IoT Hub üzenetkezelési primitíveket használhat. A IoT Edge MQTT Broker a IoT Edge hub-ban található. További információ: az [IoT Edge hub közvetítési képességei](iot-edge-runtime.md).

> [!NOTE]
> A IoT Edge MQTT Broker jelenleg nyilvános előzetes verzióban érhető el.

## <a name="pre-requisites"></a>Előfeltételek

- Érvényes előfizetéssel rendelkező Azure-fiók
- Az [Azure CLI](/cli/azure/) `azure-iot` telepítve van a CLI bővítménnyel. További információ: az Azure [IoT bővítmény telepítési lépései az Azure Azure CLI-hez](/cli/azure/azure-cli-reference-for-iot).
- Egy, az F1, S1, S2 vagy S3 értékű SKU- **IoT hub** .
- Az **1,2-es vagy újabb verzióval rendelkező IoT Edge-eszköz**. Mivel IoT Edge MQTT Broker jelenleg nyilvános előzetes verzióban érhető el, a MQTT-átvitelszervező engedélyezéséhez állítsa az alábbi környezeti változókat True értékre a edgeHub-tárolón:

   | Name | Érték |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- A IoT Edge eszközre telepített **Mosquitto-ügyfelek** . Ez a cikk a népszerű Mosquitto-ügyfeleket használja [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) és [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Ehelyett más MQTT-ügyfelek is használhatók. Ha Ubuntu-eszközön szeretné telepíteni a Mosquitto-ügyfeleket, futtassa a következő parancsot:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Ne telepítse a Mosquitto-kiszolgálót, mert az MQTT-portok (8883 és 1883) blokkolását okozhatja, és ütközik az IoT Edge hub-val.

## <a name="connect-to-iot-edge-hub"></a>Kapcsolódás IoT Edge hubhoz

Az IoT Edge hub-hoz való csatlakozás ugyanazokat a lépéseket követi, mint a [Kapcsolódás a IoT hubhoz általános MQTT-ügyféllel című cikk](../iot-hub/iot-hub-mqtt-support.md) , vagy az [IoT Edge hub fogalmi leírása](iot-edge-runtime.md). Ezek a lépések a következők:

1. Igény szerint a MQTT-ügyfél *biztonságos kapcsolatot* létesít az IoT Edge hub-val TRANSPORT Layer Security (TLS) használatával
2. A MQTT-ügyfél *hitelesíti* magát IoT Edge hubhoz
3. Az IoT Edge hub engedélyezési házirendjében *engedélyezi* a MQTT-ügyfelet.

### <a name="secure-connection-tls"></a>Biztonságos kapcsolatok (TLS)

Transport Layer Security (TLS) használatával titkosított kommunikáció hozható létre az ügyfél és a IoT Edge hub között.

A TLS letiltásához használja az 1883-as portot (MQTT), és kösse a edgeHub-tárolót a 1883-es porthoz.

Ha az ügyfél az 8883-as porton (MQTTS) keresztül csatlakozik a MQTT-közvetítőhöz, a TLS-csatornát a rendszer kezdeményezi. A közvetítő azt a tanúsítványláncot küldi, amelyet az ügyfélnek ellenőriznie kell. A tanúsítványlánc érvényesítéséhez a MQTT-átvitelszervező főtanúsítványát megbízható tanúsítványként kell telepíteni az ügyfélen. Ha a főtanúsítvány nem megbízható, akkor az MQTT-közvetítő tanúsítvány-ellenőrzési hibával elutasítja az ügyfél függvénytárát. Az alábbi lépéseket követve telepítheti a közvetítő főtanúsítványát az ügyfélen, és megegyeznek az [átlátszó átjáró](how-to-create-transparent-gateway.md) esetében, és az [előkészítő eszköz](how-to-connect-downstream-device.md#prepare-a-downstream-device) dokumentációjában olvashat.

### <a name="authentication"></a>Hitelesítés

Ahhoz, hogy egy MQTT-ügyfél hitelesítse magát, először egy csatlakozási csomagot kell küldenie a MQTT-közvetítőnek, hogy kapcsolatot kezdeményezzen a nevében. Ez a csomag három hitelesítési információt biztosít: a `client identifier` , a `username` és a `password` :

- A `client identifier` mező a IoT hub eszköz vagy modul neve. A következő szintaxist használja:

  - Eszköz esetén: `<device_name>`

  - Modul esetén: `<device_name>/<module_name>`

   A MQTT-átvitelszervezőhöz való kapcsolódáshoz az eszközt vagy a modult regisztrálni kell IoT Hubban.

   A közvetítő nem teszi lehetővé, hogy több ügyfél kapcsolatait ugyanazzal a hitelesítő adatokkal használja. A közvetítő leválasztja a már csatlakoztatott ügyfelet, ha egy második ügyfél ugyanazzal a hitelesítő adatokkal csatlakozik.

- A `username` mező az eszköz vagy a modul nevéből származik, és a IoTHub neve az eszköz a következő szintaxissal tartozik:

  - Eszköz esetén: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Modul esetén: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- A `password` csatlakozási csomag mező a hitelesítési módból függ:

  - A [szimmetrikus kulcsok hitelesítésének](how-to-authenticate-downstream-device.md#symmetric-key-authentication)használatakor a `password` mező sas-token.
  - Ha [X. 509 önaláírt hitelesítést](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)használ, akkor a `password` mező nem jelenik meg. Ebben a hitelesítési módban a TLS-csatornát kötelező megadni. Az ügyfélnek csatlakoznia kell az 8883-as porthoz a TLS-kapcsolat létrehozásához. A TLS-kézfogás során a MQTT-közvetítő ügyféltanúsítványt kér. Ez a tanúsítvány az ügyfél identitásának ellenőrzésére szolgál, így a `password` mezőre a kapcsolódási csomag elküldésekor még nincs szükség. Az ügyféltanúsítvány és a jelszó mező elküldése hibát jelez, és a rendszer bezárja a kapcsolódást. A MQTT-kódtárak és a TLS-ügyféloldali kódtárak általában úgy is rendelkezhetnek, hogy a kapcsolatok kezdeményezése során ügyféltanúsítványt küldjenek. Az [X509-tanúsítvány használata az ügyfél-hitelesítéshez](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)szakasz lépésről lépésre látható példát mutat.

A IoT Edge által központilag telepített modulok a [szimmetrikus kulcsok hitelesítését](how-to-authenticate-downstream-device.md#symmetric-key-authentication) használják, és a helyi [IoT Edge munkaterhelés API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) -t hívhatják programozott módon, ha kapcsolat nélküli módban is megkapják az SAS-tokent.

### <a name="authorization"></a>Engedélyezés

Ha egy MQTT-ügyfél hitelesítése IoT Edge hub-ra, engedélyezni kell a kapcsolódást. A csatlakozást követően engedélyezni kell a konkrét témakörök közzétételét vagy előfizetését. Ezeket az engedélyeket az IoT Edge hub engedélyezte az engedélyezési házirend alapján. Az engedélyezési házirend egy JSON-struktúraként kifejezett utasítások halmaza, amelyet a IoT Edge hub a Twin használatával továbbít. Egy IoT Edge hub-iker szerkesztése az engedélyezési házirend konfigurálásához.

> [!NOTE]
> A nyilvános előzetes verzióban a MQTT Broker engedélyezési házirendjeinek szerkesztése csak a Visual Studio, a Visual Studio Code vagy az Azure CLI használatával érhető el. A Azure Portal jelenleg nem támogatja a IoT Edge hub Twin és annak engedélyezési szabályzatának szerkesztését.

Minden engedélyezési házirend-utasítás a (z) és a (z) és a (z), `identities` `allow` `deny` `operations` és `resources` :

- `identities` írja le a szabályzat tárgyát. Az `client identifier` ügyfélnek a kapcsolódási csomagban lévő ügyfeleknek kell leképeznie.
- `allow` vagy `deny` a hatások határozzák meg, hogy engedélyezik vagy megtagadják a műveleteket.
- `operations` adja meg az engedélyezni kívánt műveleteket. `mqtt:connect`, `mqtt:publish` és `mqtt:subscribe` a három támogatott művelet jelenleg.
- `resources` a szabályzat objektumának megadása. Ez lehet egy témakör vagy egy [MQTT-helyettesítő karakterekkel](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)definiált témakör-minta.

Az alábbi példa egy olyan engedélyezési házirendre mutat példát, amely explicit módon nem engedélyezi a "rogue_client" ügyfelet a kapcsolódáshoz, lehetővé teszi bármely Azure IoT-ügyfél számára a kapcsolódást, és lehetővé teszi a "sensor_1" közzétételét a témakörben `events/alerts` .

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

Az engedélyezési szabályzat írásakor néhány dolgot figyelembe kell vennie:

- Ehhez a `$edgeHub` Twin schema 1,2-es verziója szükséges.
- Alapértelmezés szerint az összes művelet megtagadva.
- Az engedélyezési utasítások a JSON-definícióban megjelenő sorrendben lesznek kiértékelve. Ekkor megtekinti a következőt: `identities` , majd kiválasztja a kérésnek megfelelő első engedélyezési vagy megtagadási utasítást. Az engedélyezési és megtagadási utasítások közötti ütközés esetén a megtagadási utasítás nyer.
- Az engedélyezési házirendben több változó (például a helyettesítések) is használható:
    - `{{iot:identity}}` a jelenleg csatlakoztatott ügyfél identitását jelöli. Például az eszköz identitása, `myDevice` vagy egy olyan azonosító, mint a modul `myEdgeDevice/SampleModule` .
    - `{{iot:device_id}}` a jelenleg csatlakoztatott eszköz identitását jelöli. Például egy eszköz identitása, `myDevice` vagy az eszköz identitása, ahol egy modul fut `myEdgeDevice` .
    - `{{iot:module_id}}` a jelenleg csatlakoztatott modul identitását jelöli. Ez a változó nem üres a csatlakoztatott eszközökhöz, vagy például a modul identitásához `SampleModule` .
    - `{{iot:this_device_id}}` az engedélyezési házirendet futtató IoT Edge eszköz identitását jelöli. Például: `myIoTEdgeDevice`.

Az IoT hub-témakörökre vonatkozó engedélyeket a felhasználó által definiált témakörök némileg eltérően kezelik. A következő fontos szempontokat érdemes megjegyezni:

- Az Azure IoT-eszközöknek vagy-moduloknak explicit engedélyezési szabályra van szükségük IoT Edge hub MQTT-átvitelszervezőhöz való kapcsolódáshoz. Az alábbi alapértelmezett csatlakozási engedélyezési házirendet kell megadnia.
- Az Azure IoT-eszközök vagy-modulok alapértelmezés szerint az explicit engedélyezési szabályok nélkül férhetnek hozzá a saját IoT hub-témakörökhöz. Ebben az esetben azonban az engedélyek szülő/gyermek kapcsolatból származnak, és ezeket a kapcsolatokat be kell állítani. IoT Edge modulokat a rendszer automatikusan a IoT Edge eszköz gyermekeiként állítja be, de az eszközöket explicit módon be kell állítani a IoT Edge átjáró gyermekeiként.

Itt látható egy alapértelmezett engedélyezési szabályzat, amely lehetővé teszi, hogy az összes Azure IoT-eszköz vagy-modul **csatlakozhasson** a közvetítőhöz:

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

Most, hogy megértette, hogyan csatlakozhat a IoT Edge MQTT-közvetítőhöz, lássuk, hogyan használható az üzenetek közzétételére és előfizetésére a felhasználó által definiált témakörökben, majd az IoT hub témaköreiben és végül egy másik MQTT-Közvetítőben.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Közzététel és előfizetés felhasználó által definiált témakörökre

Ebben a cikkben egy **sub_client** nevű ügyfelet fog használni, amely előfizet egy témakörre, és egy **pub_client** nevű másik ügyfelet, amely egy témakört tesz közzé. A [szimmetrikus kulcsos hitelesítést](how-to-authenticate-downstream-device.md#symmetric-key-authentication) fogjuk használni, de ugyanezt [x. 509 önaláírt hitelesítéssel](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) vagy [x. 509 hitelesítésszolgáltató által aláírt hitelesítéssel](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)is elvégezheti.

### <a name="create-publisher-and-subscriber-clients"></a>Közzétevő és előfizetői ügyfelek létrehozása

Hozzon létre két IoT-eszközt a IoT Hubban, és kérje meg a jelszavukat. Az Azure CLI használata a terminálról a következőre:

1. Hozzon létre két IoT-eszközt a IoT Hubban, majd a IoT Edge eszközön:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Jelszó kérése SAS-token létrehozásával:

    - Eszköz esetén:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       ahol a 3600 az SAS-token időtartama másodpercben (például 3600 = 1 óra).
    
    - Modul esetén:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       ahol a 3600 az SAS-token időtartama másodpercben (például 3600 = 1 óra).

3. Másolja az SAS-tokent, amely a kimenetből származó "sas" kulcsnak megfelelő érték. Íme egy példa a fenti Azure CLI-parancs kimenetére:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Közzétevő és előfizetői ügyfelek engedélyezése

A közzétevő és az előfizető engedélyezéséhez szerkessze a IoT Edge hub Twin-et egy IoT Edge központi telepítés létrehozásával az Azure CLI-vel, a Visual Studióval vagy a Visual Studio Code-nal, hogy tartalmazza a következő engedélyezési házirendet:

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

Kapcsolódjon **sub_client** MQTT-ügyfeléhez a MQTT-közvetítőhöz, és fizessen elő a `test_topic` következő parancs futtatásával a IoT Edge eszközön:

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

`<edge_device_address>`  =  `localhost` ebben a példában az ügyfél ugyanazon az eszközön fut, mint a IoT Edge.

Vegye figyelembe, hogy a TLS nélküli 1883 (MQTT) port ebben az első példában van használatban. A következő szakaszban látható egy másik példa a 8883 (MQTTS) portra, amelyen engedélyezve van a TLS.

A **sub_client** MQTT-ügyfél most elindult, és a bejövő üzenetekre vár `test_topic` .

#### <a name="publish"></a>Közzététel

Kapcsolódjon a **pub_client** MQTT-ügyfeléhez a MQTT-közvetítőhöz, és tegye közzé a fentiekkel megegyező üzenetet `test_topic` úgy, hogy futtatja a következő parancsot a IoT Edge eszközön egy másik terminálról:

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

`<edge_device_address>`  =  `localhost` ebben a példában az ügyfél ugyanazon az eszközön fut, mint a IoT Edge.

A parancs végrehajtásakor a **sub_client** MQTT-ügyfél megkapja a "Hello" üzenetet.

### <a name="symmetric-keys-authentication-with-tls"></a>Szimmetrikus kulcsok hitelesítése TLS-vel

A TLS engedélyezéséhez a portot 1883-tól (MQTT) a 8883 (MQTTS) értékre kell módosítani, és az ügyfeleknek rendelkezniük kell a MQTT-közvetítő főtanúsítványával, hogy ellenőrizni tudják a MQTT-közvetítő által eljuttatott tanúsítványlánc érvényességét. Ezt a [biztonságos kapcsolatok (TLS)](#secure-connection-tls)szakaszban leírt lépéseket követve teheti meg.

Mivel az ügyfelek ugyanazon az eszközön futnak, mint a fenti példában a MQTT-közvetítő, ugyanezek a lépések érvényesek a TLS engedélyezésére a 1883 (MQTT) és a 8883 (MQTTS) közötti portszám megváltoztatásával.

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Közzétételi és előfizetési IoT Hub témakörök

Az [Azure IoT eszközoldali SDK](https://github.com/Azure/azure-iot-sdks) -k már lehetővé teszik, hogy az ügyfelek elvégezzenek IoT hub műveleteket, de nem engedélyezik a felhasználók által definiált témakörök közzétételét és előfizetését. A IoT Hub műveletek bármilyen MQTT-ügyféllel elvégezhetők a közzétételi és előfizetési szemantika használatával, feltéve, hogy a IoT hub primitív protokollait figyelembe veszi. Ezen protokollok működésének megismeréséhez a sajátosságokat mutatjuk be.

### <a name="send-telemetry-data-to-iot-hub"></a>Telemetria-IoT Hub küldése

A telemetria-adatok IoT Hubba való küldése hasonló a felhasználó által definiált témakörök közzétételéhez, de egy adott IoT Hub témakört használva:

- Egy eszköz esetében a telemetria a következő témakörben lesz elküldve: `devices/<device_name>/messages/events/`
- Modul esetén a telemetria a következő témakörben lesz elküldve: `devices/<device_name>/<module_name>/messages/events/`

Emellett hozzon létre egy útvonalat, például `FROM /messages/* INTO $upstream` hogy telemetria küldjön a IOT Edge MQTT Broker és az IoT hub között. Az útválasztással kapcsolatos további tudnivalókért tekintse meg az [útvonalak deklarálása](module-composition.md#declare-routes)című témakört.

### <a name="get-twin"></a>Get Twin

Az eszköz/modul Twin típusának beolvasása nem egy tipikus MQTT-minta. Az ügyfélnek olyan kérelmet kell kiadnia a Twin-nek, amelyet a IoT Hub fog kiszolgálni.

Az ikrek fogadásához az ügyfélnek egy IoT Hub-specifikus témakörre kell előfizetnie `$iothub/twin/res/#` . Ennek a témakörnek a neve örökölt IoT Hubtól, és minden ügyfélnek ugyanarra a témakörre kell előfizetnie. Ez nem jelenti azt, hogy az eszközök vagy modulok egymástól kapják meg a kétat. A IoT Hub és az IoT Edge hub tudja, hogy melyik Twin-t kell megadnia, még akkor is, ha az összes eszköz ugyanazt a témakör-nevet figyeli. 

Az előfizetés elvégzése után az ügyfélnek meg kell kérnie a Twin-t, ha közzétesz egy üzenetet egy IoT Hub adott témakörben, `$iothub/twin/GET/?rid=<request_id>/#` ahol  `<request_id>` az egy tetszőleges azonosító. Az IoT hub ezt követően elküldi a válaszát a kért, a témakörben szereplő adattal `$iothub/twin/res/200/?rid=<request_id>` , amelyre az ügyfél előfizet. Az ügyfél így is párosíthatja kérelmeit a válaszokkal.

### <a name="receive-twin-patches"></a>Twin-javítások fogadása

A Twin-javítások fogadásához az ügyfeleknek elő kell fizetniük a speciális IoTHub témakörre `$iothub/twin/PATCH/properties/desired/#` . Az előfizetés után az ügyfél megkapja a jelen témakör IoT Hub által küldött két javítást. 

### <a name="receive-direct-methods"></a>Közvetlen metódusok fogadása

A közvetlen metódus fogadása hasonló a teljes ikrek fogadásához, azzal a kiegészítéssel, hogy az ügyfélnek vissza kell igazolnia, hogy megkapta a hívást. Az ügyfél először az IoT hub speciális témakörére iratkozott fel `$iothub/methods/POST/#` . Ezt követően a jelen témakörben a közvetlen metódus megérkezése után az ügyfélnek ki kell bontania a kérés azonosítóját `rid` a közvetlen metódus fogadására szolgáló altémakörből, és végül közzé kell tennie egy megerősítő üzenetet az IoT hub speciális témakörében `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Közvetlen metódusok küldése

A közvetlen metódus küldése HTTP-hívás, így nem halad át a MQTT-közvetítőn. Ha közvetlen metódust szeretne küldeni az IoT hub számára, olvassa el a [közvetlen módszerek megismerése és meghívása](../iot-hub/iot-hub-devguide-direct-methods.md)című témakört. Ha egy közvetlen metódust helyileg szeretne elküldeni egy másik modulba, tekintse meg ezt az [Azure IoT C# SDK Direct metódus Meghívási példáját](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Közzététel és előfizetés a MQTT-közvetítők között

Két MQTT-közvetítő összekapcsolásához a IoT Edge hub tartalmaz egy MQTT-hidat. Egy MQTT-híd általában egy másik MQTT-közvetítőhöz kapcsolódó MQTT-közvetítő összekapcsolására szolgál. A helyi forgalomnak csak egy részhalmazát küldi el a rendszer általában egy másik közvetítőnek.

> [!NOTE]
> Az IoT Edge hub-híd jelenleg csak a beágyazott IoT Edge-eszközök között használható. Nem használható adat küldésére az IoT hub számára, mivel a IoT hub nem teljes körűen Kiemelt MQTT-közvetítő. Ha többet szeretne megtudni a IoT hub MQTT Broker funkcióinak támogatásáról, tekintse meg [a kommunikáció az IoT hubhoz a MQTT protokoll használatával](../iot-hub/iot-hub-mqtt-support.md)című témakört. Az IoT Edge eszközök beágyazásával kapcsolatos további tudnivalókért lásd: [alsóbb rétegbeli IoT Edge-eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

Beágyazott konfigurációban az IoT Edge hub MQTT-híd a szülő MQTT-közvetítő ügyfeleként működik, ezért az engedélyezési szabályokat a szülő EdgeHub kell beállítani, hogy a gyermek EdgeHub közzé lehessen tenni, és előfizessen az adott felhasználó által definiált témakörökre, amelyekhez a híd konfigurálva van.

A IoT Edge MQTT-híd egy olyan JSON-struktúrán keresztül lett konfigurálva, amelyet a IoT Edge hub számára a Twin használatával továbbítanak. IoT Edge hub-kapcsolat szerkesztése a MQTT-híd konfigurálásához.

> [!NOTE]
> A nyilvános előzetes verzió esetében a MQTT-híd konfigurációja csak a Visual Studióban, a Visual Studio Code-ban vagy az Azure CLI-n keresztül érhető el. A Azure Portal jelenleg nem támogatja az IoT Edge hub Twin és a MQTT-híd konfigurációjának szerkesztését.

A MQTT-híd úgy konfigurálható, hogy egy IoT Edge hub MQTT-közvetítőt több külső közvetítőhöz csatlakoztasson. Minden külső közvetítő esetében a következő beállítások szükségesek:

- `endpoint` a távoli MQTT-közvetítő címe, amelyhez csatlakozni kíván. Jelenleg csak a szülő IoT Edge-eszközök támogatottak, és a változó határozza meg őket `$upstream` .
- `settings` meghatározza, hogy mely témaköröket kell áthidalni egy végpont számára. A végpontok több beállítást is használhatnak, és a következő értékek használhatók a konfigurálásához:
    - `direction`: `in` előfizethet a távoli közvetítő témaköreire, vagy `out` közzéteheti a távoli közvetítő témáit
    - `topic`: az alapvető téma mintája. A minta definiálásához [MQTT helyettesítő karakterek](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) használhatók. A témakör különböző előtagokat alkalmazhat a helyi közvetítőn és a távoli közvetítőn.
    - `outPrefix`: Előtag, amely a `topic` távoli közvetítőn alkalmazott mintára vonatkozik.
    - `inPrefix`: Előtag, amely a `topic` helyi közvetítő mintázatára vonatkozik.

Az alábbi példa egy olyan IoT Edge MQTT híd-konfigurációra mutat példát, amely egy szülő IoT Edge eszköznek a témakörökben fogadott összes üzenetet `alerts/#` egy alárendelt IoT Edge eszközre teszi közzé ugyanazon a témakörben, és újból közzéteszi az összes olyan üzenetet, amely a `/local/telemetry/#` gyermek IoT Edge eszköznek az alárendelt IoT Edge eszközre való küldésével kapcsolatos témakörökre vonatkozik `/remote/messages/#` .

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
Egyéb megjegyzések a IoT Edge hub MQTT-hídhoz:
- Az MQTT protokollt a rendszer automatikusan az upstream protokollként használja a MQTT-közvetítő használatakor, valamint azt, hogy a IoT Edge egy beágyazott konfigurációban, például egy megadott módon legyen használatban `parent_hostname` . A felsőbb rétegbeli protokollokkal kapcsolatos további tudnivalókért tekintse meg a [felhőalapú kommunikációt](iot-edge-runtime.md#cloud-communication)ismertető témakört. További információ a beágyazott konfigurációkról: [alsóbb rétegbeli IoT Edge eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Következő lépések

[Az IoT Edge hub ismertetése](iot-edge-runtime.md#iot-edge-hub)
