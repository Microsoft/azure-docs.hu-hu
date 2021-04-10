---
title: A Defender for IoT API-k használata
description: Külső REST API használatával férhet hozzá az érzékelők és a felügyeleti konzol által felderített adatokhoz, és műveleteket hajthat végre ezekkel az adatokkal.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: d509f2674a61af1d0ab03892186526b1cb109eee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778831"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender a IoT-érzékelő és a felügyeleti konzol API-jai számára

Külső REST API használatával férhet hozzá az érzékelők és a felügyeleti konzol által felderített adatokhoz, és műveleteket hajthat végre ezekkel az adatokkal.

A kapcsolatok SSL protokollon keresztül biztonságosak.

## <a name="getting-started"></a>Első lépések

Általánosságban elmondható, hogy ha külső API-t használ az Azure Defender for IoT-érzékelő vagy a helyszíni felügyeleti konzol számára, egy hozzáférési jogkivonatot kell létrehoznia. Az érzékelőn és a helyszíni felügyeleti konzolon használt hitelesítési API-kra nem szükségesek tokenek.

Jogkivonat létrehozása:

1. A **Rendszerbeállítások** ablakban válassza a **hozzáférési jogkivonatok** elemet.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="A rendszerbeállítások ablak képernyőképe a hozzáférési jogkivonatok gomb kiemelése.":::

2. Válassza az **új jogkivonat előállítása** lehetőséget.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Új jogkivonat létrehozásához kattintson a gombra.":::

3. Írja le az új jogkivonat célját, és kattintson a **tovább** gombra.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Új jogkivonat létrehozása és a hozzá társított integráció nevének megadása.":::

4. Megjelenik a hozzáférési jogkivonat. Másolja, mert nem jelenik meg újra.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Másolja az integrációhoz tartozó hozzáférési jogkivonatot.":::

5. Válassza a **Befejezés** gombot. A létrehozott tokenek megjelennek a **hozzáférési jogkivonatok** párbeszédpanelen.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="A kitöltött tokenekkel rendelkező eszköz-jogkivonatok párbeszédpanel képernyőképe":::

   A **használat** azt jelzi, hogy a rendszer mikor fogadta el a tokenhez tartozó külső hívást.

   Ha **N/A megjelenik** a jogkivonat **használt** mezőjében, az érzékelő és a csatlakoztatott kiszolgáló közötti kapcsolat nem működik.

6. Adjon hozzá egy **jogosultságot** a kérelemhez egy HTTP-fejlécben, és állítsa be annak értékét a generált jogkivonatra.

## <a name="sensor-api-specifications"></a>Sensor API-specifikációk

Ez a szakasz a következő érzékelő API-kat ismerteti:

- [Eszköz információinak beolvasása –/API/v1/Devices](#retrieve-device-information---apiv1devices)

- [Eszköz csatlakoztatási információinak beolvasása –/API/v1/Devices/Connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Információk beolvasása a CVEs-/API/v1/Devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Riasztási információk beolvasása –/API/v1/Alerts](#retrieve-alert-information---apiv1alerts)

- [Idősor eseményeinek beolvasása –/API/v1/Events](#retrieve-timeline-events---apiv1events)

- [Sebezhetőségi adatok beolvasása –/API/v1/Reports/Vulnerabilities/Devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Biztonsági rések beolvasása –/API/v1/Reports/Vulnerabilities/Security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Működési biztonsági rések beolvasása –/API/v1/Reports/Vulnerabilities/Operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Felhasználói hitelesítő adatok érvényesítése –/API/External/Authentication/Validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Jelszó módosítása –/External/Authentication/set_password](#change-password---externalauthenticationset_password)

- [Felhasználói jelszó frissítése Rendszerfelügyeleti webszolgáltatások által –/External/Authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Eszköz információinak beolvasása –/API/v1/Devices

Ezzel az API-val lekérheti az összes olyan eszköz listáját, amelyet a Defender for IoT érzékelő észlelt.

#### <a name="method"></a>Metódus

**GET**

Az összes olyan eszköz listáját kéri, amelyet a Defender IoT-érzékelő észlelt.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **engedélyezett**: csak az engedélyezett és a jogosulatlan eszközök szűrésére.

  **Példák**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközöket jelölő JSON-objektumok tömbje.

#### <a name="device-fields"></a>Eszköz mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **id** | Numerikus | No | - |
| **erőforrásrekordjaiba** | JSON-tömb | Yes | IP-címek (a két hálózati adapterrel rendelkező eszközök esetében több cím is lehet) |
| **name** | Sztring | No | - |
| **típusa** | Sztring | No | Ismeretlen, mérnöki állomás, PLC, HMI, történész, tartományvezérlő, adatbázis-kiszolgáló, vezeték nélküli hozzáférési pont, útválasztó, kapcsoló, kiszolgáló, munkaállomás, IP-kamera, nyomtató, tűzfal, terminál, VPN Gateway, Internet vagy csoportos küldés és szórás |
| **macAddresses** | JSON-tömb | Yes | MAC-címek (két hálózati adapterrel rendelkező eszköz esetén több cím is lehet) |
| **operatingSystem** | Sztring | Yes | - |
| **engineeringStation** | Logikai | No | Igaz vagy hamis |
| **szkenner** | Logikai | No | Igaz vagy hamis |
| **jogosult** | Logikai | No | Igaz vagy hamis |
| **gyártó** | Sztring | Yes | - |
| **protokollok** | JSON-tömb | Yes | Protokoll objektum |
| **belső vezérlőprogram** | JSON-tömb | Yes | Belső vezérlőprogram objektuma |

#### <a name="protocol-fields"></a>Protokoll mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Név** | Sztring | No |  |
| **Címek** | JSON-tömb | Yes | Fő vagy numerikus értékek |

#### <a name="firmware-fields"></a>Belső vezérlőprogram mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **sorozatszám** | Sztring | No | N/A vagy a tényleges érték |
| **modell** | Sztring | No | N/A vagy a tényleges érték |
| **firmwareVersion** | Dupla | No | N/A vagy a tényleges érték |
| **additionalData** | Sztring | No | N/A vagy a tényleges érték |
| **moduleAddress** | Sztring | No | N/A vagy a tényleges érték |
| **rack** | Sztring | No | N/A vagy a tényleges érték |
| **slot** | Sztring | No | N/A vagy a tényleges érték |
| **Cím** | Sztring | No | N/A vagy a tényleges érték |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https: <span> //127 <span> . 0.0.1/API/v1/Devices? engedélyezett = True |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Eszköz csatlakoztatási információinak beolvasása –/API/v1/Devices/Connections

Ezzel az API-val egy eszközön lévő összes kapcsolat listáját kérheti le.

#### <a name="method"></a>Metódus

**GET**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

Ha nem állítja be a lekérdezési paramétereket, a rendszer az összes eszköz kapcsolatát adja vissza.

**Példa**:

`/api/v1/devices/connections`

- **deviceId**: szűrés egy adott eszköz azonosítója alapján a kapcsolatainak megtekintéséhez.

  **Példa**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: az időkeret mostantól visszamenőlegesen, percenként, a kapcsolatok aktív volt.

  **Példa**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: csak a megadott időpont előtt észlelt kapcsolatok szűrése (ezredmásodpercben, UTC).

  **Példa**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: csak a megadott idő után észlelt kapcsolatok szűrése (ezredmásodpercben, UTC).

  **Példa**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközök kapcsolatait képviselő JSON-objektumok tömbje.

#### <a name="fields"></a>Mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **firstDeviceId** | Numerikus | No | - |
| **secondDeviceId** | Numerikus | No | - |
| **lastSeen** | Numerikus | No | EPOCH (UTC) |
| **felderített** | Numerikus | No | EPOCH (UTC) |
| **portok** | Szám tömb | No | - |
| **protokollok** | JSON-tömb | No | Protokoll mező |

#### <a name="protocol-field"></a>Protokoll mező

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **name** | Sztring | No | - |
| **parancsok** | Sztringtömb | No | - |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices/Connections | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Devices/Connections |
> | GET | Curl-k-H "Authorization: <AUTH_TOKEN>" "https://<IP_ADDRESS>/API/v1/Devices/ <deviceId> /Connections? lastActiveInMinutes =&discoveredBefore =&discoveredAfter =" | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/API/v1/Devices/2/Connections? lastActiveInMinutes = 20&discoveredBefore = 1594550986000&discoveredAfter = 1594550986000 ' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Információk beolvasása a CVEs-/API/v1/Devices/cves

Ezzel az API-val lekérheti a hálózatban lévő eszközökön észlelt összes ismert CVEs listáját.

#### <a name="method"></a>Metódus

**GET**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

Alapértelmezés szerint ez az API a CVEs-val rendelkező összes eszköz IP-címének listáját, az egyes IP-címekhez legfeljebb 100 CVEs biztosít.

**Példa**:

`/api/v1/devices/cves`

- **deviceId**: egy adott eszköz IP-címe alapján szűrheti az adott eszközön azonosított, legfeljebb 100 értékű CVEs.

  **Példa**:

  `/api/v1/devices/<ipAddress>/cves`

- **Top**: az egyes eszközök IP-címeinek lekéréséhez használt, legfelső pontszámot mutató CVEs száma.

  **Példa**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az IP-címeken azonosított CVEs jelölő JSON-objektumok tömbje.

#### <a name="fields"></a>Mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **cveId** | Sztring | No | - |
| **IP-cím** | Sztring | No | IP-cím |
| **pontszám** | Sztring | No | 0,0 – 10,0 |
| **attackVector** | Sztring | No | Hálózat, szomszédos hálózat, helyi vagy fizikai |
| **Leírás** | Sztring | No | - |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices/cves | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Devices/cves |
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices/ <deviceIpAddress> /cves? Top = | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Devices/10.10.10.15/cves? top = 50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Riasztási információk beolvasása –/API/v1/Alerts

Ezzel az API-val lekérheti az összes olyan riasztás listáját, amelyeket a Defender IoT-érzékelő észlelt.

#### <a name="method"></a>Metódus

**GET**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **állapot**: csak kezelt vagy nem kezelt riasztások szűrése.

  **Példa**:

  `/api/v1/alerts?state=handled`

- **fromTime**: adott időpontból létrehozott riasztások szűrése (UTC ezredmásodpercben).

  **Példa**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: csak adott idő (ezredmásodpercben, UTC) alapján létrehozott riasztások szűrésére.

  **Példa**:

  `/api/v1/alerts?toTime=<epoch>`

- **típus**: a riasztások adott típus szerinti szűrése. Meglévő típusok a szűréshez: nem várt új eszközök, leválasztások.

  **Példa**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A riasztásokat jelölő JSON-objektumok tömbje.

#### <a name="alert-fields"></a>Riasztási mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Numerikus | No | - |
| **idő** | Numerikus | No | EPOCH (UTC) |
| **cím** | Sztring | No | - |
| **message** | Sztring | No | - |
| **Súlyosság** | Sztring | No | Figyelmeztetés, másodlagos, fő vagy kritikus |
| **motor** | Sztring | No | Protokoll megsértése, szabályzat megsértése, kártevő, rendellenesség vagy működési |
| **sourceDevice** | Numerikus | Yes | Eszközazonosító |
| **destinationDevice** | Numerikus | Yes | Eszközazonosító |
| **additionalInformation** | További információ objektum | Yes | - |

#### <a name="additional-information-fields"></a>További információ mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Leírás** | Sztring | No | - |
| **információk** | JSON-tömb | Nem | Sztring |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | GET | Curl-k-H "hitelesítés: <AUTH_TOKEN>" "https://<IP_ADDRESS>/API/v1/Alerts? State =&fromTime =&toTime =&típus =" | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/API/v1/Alerts? állapot = kezeletlen&fromTime = 1594550986000&toTime = 1594550986001&típus = leválasztások" |

### <a name="retrieve-timeline-events---apiv1events"></a>Idősor eseményeinek beolvasása –/API/v1/Events

Ezzel az API-val az esemény idővonalára jelentett események listáját kérheti le.

#### <a name="method"></a>Metódus

**GET**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **minutesTimeFrame**: az időkeret mostantól visszamenőleg, percben, az események jelentésének időpontja.

  **Példa**:

  `/api/v1/events?minutesTimeFrame=20`

- **írja be a következőt**: az események listájának szűrése adott típus alapján.

  **Példák**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A riasztásokat jelölő JSON-objektumok tömbje.

#### <a name="event-fields"></a>Esemény mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|--|
| **időbélyeg** | Numerikus | No | EPOCH (UTC) |
| **cím** | Sztring | No | - |
| **Súlyosság** | Sztring | No | INFORMÁCIÓ, értesítés vagy riasztás |
| **tulajdonosa** | Sztring | Yes | Ha az esemény manuálisan lett létrehozva, akkor ez a mező tartalmazza az eseményt létrehozó felhasználónevet. |
| **tartalom** | Sztring | No | - |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" "https://<IP_ADDRESS>/API/v1/Events? minutesTimeFrame =&típus =" | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/API/v1/Events? minutesTimeFrame = 20&típus = DEVICE_CONNECTION_CREATED ' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Sebezhetőségi adatok beolvasása –/API/v1/Reports/Vulnerabilities/Devices

Ezzel az API-val megkérheti a sebezhetőségi felmérés eredményeit az egyes eszközökön.

#### <a name="method"></a>Metódus

**GET**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az értékelendő eszközöket jelölő JSON-objektumok tömbje.

Az eszköz objektum a következőket tartalmazza:

- Általános adatértékek

- Értékelés pontszáma

- Biztonsági rések

#### <a name="device-fields"></a>Eszköz mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **name** | Sztring | No | - |
| **erőforrásrekordjaiba** | JSON-tömb | No | - |
| **securityScore** | Numerikus | No | - |
| **gyártó** | Sztring | Yes |  |
| **firmwareVersion** | Sztring | Yes | - |
| **modell** | Sztring | Yes | - |
| **isWirelessAccessPoint** | Logikai | No | Igaz vagy hamis |
| **operatingSystem** | Operációs rendszer objektuma | Yes | - |
| **biztonsági rések** | Sebezhetőségi objektum | Yes | - |

#### <a name="operating-system-fields"></a>Operációs rendszer mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Név** | Sztring | Yes | - |
| **Típus** | Sztring | Yes | - |
| **Verzió** | Sztring | Yes | - |
| **latestVersion** | Sztring | Yes | - |

#### <a name="vulnerabilities-fields"></a>Biztonsági rések mezői
 
| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **antiViruses** | JSON-tömb | Yes | Vírusvédelmi nevek |
| **plainTextPasswords** | JSON-tömb | Yes | Jelszó-objektumok |
| **remoteAccess** | JSON-tömb | Yes | Távelérési objektumok |
| **isBackupServer** | Logikai | No | Igaz vagy hamis |
| **openedPorts** | JSON-tömb | Yes | Megnyitott portok objektumai |
| **isEngineeringStation** | Logikai | No | Igaz vagy hamis |
| **isKnownScanner** | Logikai | No | Igaz vagy hamis |
| **cves** | JSON-tömb | Yes | CVE-objektumok |
| **isUnauthorized** | Logikai | No | Igaz vagy hamis |
| **malwareIndicationsDetected** | Logikai | No | Igaz vagy hamis |
| **weakAuthentication** | JSON-tömb | Yes | Gyenge hitelesítést használó észlelt alkalmazások |

#### <a name="password-fields"></a>Jelszó mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **alaphelyzetbe állítása** | Sztring | No | - |
| **protokoll** | Sztring | No | - |
| **erősségét** | Sztring | No | Nagyon gyenge, gyenge, közepes vagy erős |

#### <a name="remote-access-fields"></a>Távelérési mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Port** | Numerikus | No | - |
| **közlekedési** | Sztring | No | TCP vagy UDP |
| **ügyfél** | Sztring | No | IP-cím |
| **clientSoftware** | Sztring | No | SSH, VNC, távoli asztal vagy csapat megjelenítője |

#### <a name="open-port-fields"></a>Port mezőinek megnyitása

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Port** | Numerikus | No | - |
| **közlekedési** | Sztring | No | TCP vagy UDP |
| **protokoll** | Sztring | Yes | - |
| **isConflictingWithFirewall** | Logikai | No | Igaz vagy hamis |

#### <a name="cve-fields"></a>CVE-mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Sztring | No | - |
| **pontszám** | Numerikus | No | Dupla |
| **Leírás** | Sztring | No | - |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Devices | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Biztonsági rések beolvasása –/API/v1/Reports/Vulnerabilities/Security

Ezzel az API-val egy általános sebezhetőségi felmérés eredményeit kérheti le. Ez az értékelés betekintést nyújt a rendszerek biztonsági szintjébe.

Ez az értékelés az általános hálózati és rendszerinformáción alapul, nem egy adott eszköz kiértékelésén.

#### <a name="method"></a>Metódus

**GET**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A vizsgált eredményeket jelölő JSON-objektum. Minden kulcs lehet üres. Ellenkező esetben egy nem null értékű kulccsal rendelkező JSON-objektumot fog tartalmazni.

### <a name="result-fields"></a>Eredmény mezők

**Kulcsok**

**unauthorizedDevices**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Cím** | Sztring | IP-cím |
| **name** | Sztring | - |
| **firstDetectionTime** | Numerikus | EPOCH (UTC) |
| lastSeen | Numerikus | EPOCH (UTC) |

**illegalTrafficByFirewallRules**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Server** | Sztring | IP-cím |
| **ügyfél** | Sztring | IP-cím |
| **Port** | Numerikus | - |
| **közlekedési** | Sztring | TCP, UDP vagy ICMP |

**weakFirewallRules**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **adatforrások** | A források JSON-tömbje. Minden forrás négy formátumban lehet. | "Any", "IP-cím (gazdagép)", "IP-ről IP-re (tartomány)", "IP-cím, alhálózati maszk (hálózat)" |
| **Destinations** | A célhelyek JSON-tömbje. Minden cél négy formátumban lehet. | "Any", "IP-cím (gazdagép)", "IP-ről IP-re (tartomány)", "IP-cím, alhálózati maszk (hálózat)" |
| **portok** | A portok JSON-tömbje három formátumban | "Any", "port (ha észlelhető)", "portról portra (az észlelt protokoll)" |

**accessPoints**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **macAddress** | Sztring | MAC-cím |
| **gyártó** | Sztring | Szállító neve |
| **IP-cím** | Sztring | IP-cím vagy N/A |
| **name** | Sztring | Eszköz neve vagy N/A |
| **Szikratávíró** | Sztring | Nem, feltételezett vagy igen |

**connectionsBetweenSubnets**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Server** | Sztring | IP-cím |
| **ügyfél** | Sztring | IP-cím |

**industrialMalwareIndicators**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **detectionTime** | Numerikus | EPOCH (UTC) |
| **alertMessage** | Sztring | - |
| **Leírás** | Sztring | - |
| **eszközök** | JSON-tömb | Eszközök nevei | 

**internetConnections**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **internalAddress** | Sztring | IP-cím |
| **jogosult** | Logikai | Igen vagy nem | 
| **externalAddresses** | JSON-tömb | IP-cím |

#### <a name="response-example"></a>Példa válaszra

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Security | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Működési biztonsági rések beolvasása –/API/v1/Reports/Vulnerabilities/Operational

Ezzel az API-val egy általános sebezhetőségi felmérés eredményeit kérheti le. Ez az értékelés betekintést nyújt a hálózat működési állapotára. A szolgáltatás általános hálózati és rendszerinformáción alapul, nem egy adott eszköz kiértékelésével.

#### <a name="method"></a>Metódus

**GET**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A vizsgált eredményeket jelölő JSON-objektum. Mindegyik kulcs az eredmények JSON-tömbjét tartalmazza.

#### <a name="result-fields"></a>Eredmény mezők

**Kulcsok**

**backupServer**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **forrás** | Sztring | IP-cím |
| **cél** | Sztring | IP-cím |
| **Port** | Numerikus | - |
| **közlekedési** | Sztring | TCP vagy UDP |
| **backupMaximalInterval** | Sztring | - |
| **lastSeenBackup** | Numerikus | EPOCH (UTC) |

**ipNetworks**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| s **címek** | Numerikus | - |
| **network** | Sztring | IP-cím |
| **mask** | Sztring | Alhálózati maszk |

**protocolProblems**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **protokoll** | Sztring | - |
| **címek** | JSON-tömb | IP-címek |
| **riasztás** | Sztring | - |
| **reportTime** | Numerikus | EPOCH (UTC) |

**protocolDataVolumes**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| protokoll | Sztring | - |
| kötet | Sztring | "kötet száma MB" |

**szétkapcsolások**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **assetAddress** | Sztring | IP-cím |
| **assetName** | Sztring | - |
| **lastDetectionTime** | Numerikus | EPOCH (UTC) |
| **backToNormalTime** | Numerikus | EPOCH (UTC) |     

#### <a name="response-example"></a>Példa válaszra

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Operational | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Felhasználói hitelesítő adatok érvényesítése –/API/External/Authentication/Validation

Ezzel az API-val ellenőrizheti a Defender IoT felhasználónevét és jelszavát. A IoT összes felhasználói szerepköre az API-val is működhet.

Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **username** | Sztring | No |
| **alaphelyzetbe állítása** | Sztring | No |

#### <a name="request-example"></a>Példa kérésre

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A műveleti állapot részleteit tartalmazó üzenet sztringje:

- **Sikeres – msg**: a hitelesítés sikerült

- Hiba **– hiba**: a hitelesítő adatok érvényesítése nem sikerült

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/External/Authentication/Validation | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/External/Authentication/Validation |

### <a name="change-password---externalauthenticationset_password"></a>Jelszó módosítása –/External/Authentication/set_password

Ezzel az API-val engedélyezheti, hogy a felhasználók megváltoztassák a saját jelszavukat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-example"></a>Példa kérésre

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A műveleti állapot részleteit tartalmazó üzenet sztringje:

- **Sikeres – msg**: a jelszó le lett cserélve

- Hiba **– hiba**: felhasználói hitelesítési hiba

- Hiba **– hiba**: a jelszó nem felel meg a biztonsági házirendnek

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Eszköz mezői

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **username** | Sztring | No |
| **alaphelyzetbe állítása** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | Curl-k-d ' {"username": "<USER_NAME>", "password": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} "-H" Content-Type: Application/JSON "https://<IP_ADDRESS>/API/External/Authentication/set_password | Curl-k-d ' {"username": "myUser", "password": " 1234@abcd ", "new_password": " abcd@1234 "} "-H" Content-Type: Application/JSON "https:/ <span> /127.0.0.1/API/External/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Felhasználói jelszó frissítése Rendszerfelügyeleti webszolgáltatások által –/External/Authentication/set_password_by_admin

Ezzel az API-val engedélyezheti a rendszergazdáknak a megadott felhasználók jelszavainak módosítását. A IoT rendszergazda felhasználói szerepkörei az API-val is működhetnek. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-example"></a>Példa kérésre

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A műveleti állapot részleteit tartalmazó üzenet sztringje:

- **Sikeres – msg**: a jelszó le lett cserélve

- Hiba **– hiba**: felhasználói hitelesítési hiba

- Hiba **– hiba**: a felhasználó nem létezik

- Hiba **– hiba**: a jelszó nem felel meg a biztonsági házirendnek

- Hiba **– hiba**: a felhasználó nem rendelkezik a jelszó módosítására vonatkozó engedélyekkel

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Eszköz mezői

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **admin_username** | Sztring | No |
| **admin_password** | Sztring | No |
| **username** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | POST | Curl-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "username": "<USER_NAME>", "new_password": "<NEW_PASSWORD>"} "-H" Content-Type: Application/JSON "https://<IP_ADDRESS>/API/External/Authentication/set_password_by_admin | Curl-k-d ' {"admin_user": "adminUser", "admin_password": " 1234@abcd ", "username": "myUser", "new_password": " abcd@1234 "} "-H" Content-Type: Application/JSON "https:/ <span> /127.0.0.1/API/External/Authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>A helyszíni felügyeleti konzol API-specifikációi

Ez a szakasz a következő helyszíni felügyeleti konzol API-kat ismerteti:

- **/external/v1/alerts/<UUID>**

- **Riasztás kizárása (karbantartási időszak)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A riasztás kizárása ablak, amely az aktív szabályokat mutatja.":::

Adja meg azokat a feltételeket, amelyekben a rendszer nem küld riasztásokat. Például megadhatja és frissítheti a leállítási és indítási időpontokat, az olyan eszközöket és alhálózatokat, amelyeket ki kell zárni a riasztások kiváltásakor, vagy a kizárni kívánt IoT-motorok védelmezőjét. Előfordulhat például, hogy a karbantartási időszak során le szeretné állítani az összes riasztás kézbesítését, kivéve a kritikus eszközökön található kártevő-riasztásokat.

Az itt definiált API-k csak olvasási kizárási szabályként jelennek meg a helyszíni felügyeleti konzol **riasztás kizárások** ablakában.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Példa válaszra**

- **Válasz**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Jelszó módosítása –/External/Authentication/set_password

Ezzel az API-val engedélyezheti, hogy a felhasználók megváltoztassák a saját jelszavukat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Felhasználói jelszó frissítése Rendszerfelügyeleti webszolgáltatások által –/External/Authentication/set_password_by_admin

Ezzel az API-val engedélyezheti, hogy a rendszergazdák módosítsák a jelszavakat bizonyos felhasználók számára. A IoT rendszergazdai szerepkörrel rendelkező Defender képes együttműködni az API-val. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

### <a name="retrieve-device-information---externalv1devices"></a>Eszköz információinak beolvasása –/External/v1/Devices

Ez az API a Defender által a helyszíni felügyeleti konzolhoz csatlakozó IoT érzékelők számára észlelt összes eszköz listáját kéri le.

#### <a name="method"></a>Metódus

**GET**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközöket jelölő JSON-objektumok tömbje.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **engedélyezett**: csak az engedélyezett és a jogosulatlan eszközök szűrésére.

- **siteId**: csak adott helyekhez kapcsolódó eszközök szűrése.

- **zónaazonosító**: csak adott zónákhoz kapcsolódó eszközök szűrése. [1](#1)

- **sensorId**: csak adott érzékelők által észlelt eszközök szűrésére. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Előfordulhat, hogy nem rendelkezik a hely és a zóna azonosítójával. Ha ez a helyzet, az összes eszköz lekérdezése a hely és a zóna AZONOSÍTÓjának lekéréséhez.*

#### <a name="query-parameters-example"></a>Példa lekérdezési paraméterekre

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Eszköz mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **sensorId** | Numerikus | No | - |
| **Zónaazonosító** | Numerikus | Yes | - |
| **siteId** | Numerikus | Yes | - |
| **erőforrásrekordjaiba** | JSON-tömb | Yes | IP-címek (a két hálózati adapterrel rendelkező eszközök esetében több cím is lehet) |
| **name** | Sztring | No | - |
| **típusa** | Sztring | No | Ismeretlen, mérnöki állomás, PLC, HMI, történész, tartományvezérlő, adatbázis-kiszolgáló, vezeték nélküli hozzáférési pont, útválasztó, kapcsoló, kiszolgáló, munkaállomás, IP-kamera, nyomtató, tűzfal, terminál, VPN Gateway, Internet vagy csoportos küldés és szórás |
| **macAddresses** | JSON-tömb | Yes | MAC-címek (két hálózati adapterrel rendelkező eszköz esetén több cím is lehet) |
| **operatingSystem** | Sztring | Yes | - |
| **engineeringStation** | Logikai | No | Igaz vagy hamis |
| **szkenner** | Logikai | No | Igaz vagy hamis |
| **jogosult** | Logikai | No | Igaz vagy hamis |
| **gyártó** | Sztring | Yes | - |
| **Protokollok** | JSON-tömb | Yes | Protokoll objektum |
| **belső vezérlőprogram** | JSON-tömb | Yes | Belső vezérlőprogram objektuma |

#### <a name="protocol-fields"></a>Protokoll mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| Name | Sztring | No | - |
| Címek | JSON-tömb | Yes | Fő vagy numerikus értékek |

#### <a name="firmware-fields"></a>Belső vezérlőprogram mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **sorozatszám** | Sztring | No | N/A vagy a tényleges érték |
| **modell** | Sztring | No | N/A vagy a tényleges érték |
| **firmwareVersion** | Dupla | No | N/A vagy a tényleges érték |
| **additionalData** | Sztring | No | N/A vagy a tényleges érték |
| **moduleAddress** | Sztring | No | N/A vagy a tényleges érték |
| **rack** | Sztring | No | N/A vagy a tényleges érték |
| **slot** | Sztring | No | N/A vagy a tényleges érték |
| **Cím** | Sztring | No | N/A vagy a tényleges érték |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" "https://<>IP_ADDRESS>/External/v1/Devices? siteId =&zónaazonosító =&sensorId =&engedélyezett =" | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/Devices? siteId = 1&zónaazonosító = 2&sensorId = 5&engedélyezett = igaz ' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Riasztási információk beolvasása –/External/v1/Alerts

Ezzel az API-val lekérheti az összes vagy szűrt riasztást egy helyszíni felügyeleti konzolról.

#### <a name="method"></a>Metódus

**GET**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **állapot**: csak a kezelt és a nem kezelt riasztások szűrése.

  **Példa**:

  `/api/v1/alerts?state=handled`

- **fromTime**: adott időpontból létrehozott riasztások szűrése (UTC ezredmásodpercben).

  **Példa**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: csak adott idő (ezredmásodpercben, UTC) alapján létrehozott riasztások szűrésére.

  **Példa**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**: a hely, amelyen a riasztást észlelték. [2](#2)

- **zónaazonosító**: az a zóna, amelyen a rendszer észlelte a riasztást. [2](#2)

- **érzékelő**: a riasztást felderítő érzékelő.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *Előfordulhat, hogy nem rendelkezik a hely és a zóna azonosítójával. Ha ez a helyzet, az összes eszköz lekérdezése a hely és a zóna AZONOSÍTÓjának lekéréséhez.*

#### <a name="alert-fields"></a>Riasztási mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Numerikus | No | - |
| **idő** | Numerikus | No | EPOCH (UTC) |
| **cím** | Sztring | No | - |
| **message** | Sztring | No | - |
| **Súlyosság** | Sztring | No | Figyelmeztetés, másodlagos, fő vagy kritikus |
| **motor** | Sztring | No | Protokoll megsértése, szabályzat megsértése, kártevő, rendellenesség vagy működési |
| **sourceDevice** | Numerikus | Yes | Eszközazonosító |
| **destinationDevice** | Numerikus | Yes | Eszközazonosító |
| **additionalInformation** | További információ objektum | Yes | - |

#### <a name="additional-information-fields"></a>További információ mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Leírás** | Sztring | No | - |
| **információk** | JSON-tömb | Nem | Sztring |

#### <a name="response-example"></a>Példa válaszra

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | GET | Curl-k-H "hitelesítés: <AUTH_TOKEN>" "https://<>IP_ADDRESS>/External/v1/Alerts? State =&zónaazonosító =&fromTime =&toTime =&siteId =&érzékelő =" | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/Alerts? állapot = kezeletlen&zónaazonosító = 1&fromTime = 0&toTime = 1594551777000&siteId = 1&érzékelő = 1 " |

### <a name="qradar-alerts"></a>QRadar-riasztások

A QRadar-integráció a Defender for IoT segítségével azonosíthatja a Defender által a IoT által generált riasztásokat, és műveleteket hajthat végre ezekkel a riasztásokkal. A QRadar fogadja a Defender for IoT adatait, majd felveszi a kapcsolatot a nyilvános API helyszíni felügyeleti konzol összetevőjével.

Ha a Defender által észlelt, a IoT-hez QRadar számára felderített adatküldést szeretné elküldeni, Definiáljon egy továbbítási szabályt a Defender for IoT rendszer számára, és válassza a **Távoli támogatási riasztás kezelése** lehetőséget.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Szerkessze a továbbítási szabályokat az igényeinek megfelelően.":::

Ha ezt a lehetőséget választja a továbbítási szabályok konfigurálása során, a következő további mezők jelennek meg a QRadar:

- **UUID**: egyedi riasztási azonosító, például 1-1555245116250.

- **Hely**: az a hely, ahol a riasztást észlelték.

- **Zóna**: a riasztást felderítő zóna.

Példa a QRadar eljuttatott adattartalomra:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/External/v1/Alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metódus

**PUT**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-content"></a>Tartalom kérése

JSON-objektum, amely az UUID-t tartalmazó riasztáson végrehajtandó műveletet jelöli.

#### <a name="action-fields"></a>Művelet mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **művelet** | Sztring | No | leíró vagy handleAndLearn |

#### <a name="request-example"></a>Példa kérésre

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközöket jelölő JSON-objektumok tömbje.

#### <a name="response-fields"></a>Válasz mezői


| Név | Típus | Nullázható | Leírás |
|--|--|--|--|
| **tartalom/hiba** | Sztring | No | Ha a kérelem sikeres, megjelenik a Content (tartalom) tulajdonság. Ellenkező esetben a hiba tulajdonság jelenik meg. |

#### <a name="possible-content-values"></a>Lehetséges tartalom értékei

| Állapotkód | Tartalom értéke | Leírás |
|--|--|--|
| 200 | A riasztások frissítési kérelme sikeresen befejeződött. | A frissítési kérelem sikeresen befejeződött. Nincsenek megjegyzések. |
| 200 | A riasztás már kezelt (**Handle**). | A riasztást a rendszer már kezelte, amikor a riasztáshoz tartozó leíró kérés érkezett.<br />A riasztást továbbra is **kezeli** a rendszer. |
| 200 | A riasztás már kezelt és megtanult (**handleAndLearn**). | A rendszer már kezelte a riasztást, és megismerte, hogy mikor érkezett kérelem a **handleAndLearn** .<br />A riasztás a **handledAndLearn** állapotban marad. |
| 200 | A riasztás már kezelt (**kezelt**).<br />A kezelő és a tanulás (**handleAndLearn**) végrehajtása a riasztáson történt. | A riasztás már a **handleAndLearn** kérelem kézhezvétele után lett kezelve.<br />A riasztás **handleAndLearn** válik. |
| 200 | A riasztás már kezelt és megtanult (**handleAndLearn**). A kezelői kérelem figyelmen kívül hagyva. | A riasztás már **handleAndLearn** , amikor a riasztás kezelésére irányuló kérés érkezett. A riasztás **handleAndLearn** marad. |
| 500 | Érvénytelen művelet. | Az elindított művelet nem érvényes művelet a riasztáson való végrehajtáshoz. |
| 500 | Váratlan hiba történt. | Váratlan hiba történt. A probléma megoldásához forduljon a technikai támogatási szolgálathoz. |
| 500 | A kérelem nem hajtható végre, mert ehhez az UUID-hoz nem található riasztás. | A megadott riasztási UUID nem található a rendszeren. |

#### <a name="response-example"></a>Példa válaszra

**Sikeres**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Sikertelen**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| PUT | Curl-k-X PUT-d {"Action": " <ACTION> "} "-H" hitelesítés: <AUTH_TOKEN> "https://<IP_ADDRESS>/External/v1/Alerts/<UUID> | Curl-k-X PUT-d {művelet ":" Handle "}"-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/Alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Riasztás kizárása (karbantartási időszak) –/external/v1/maintenanceWindow

Adja meg azokat a feltételeket, amelyekben a rendszer nem küld riasztásokat. Például megadhatja és frissítheti a leállítási és indítási időpontokat, az olyan eszközöket és alhálózatokat, amelyeket ki kell zárni a riasztások kiváltásakor, vagy a kizárni kívánt IoT-motorok védelmezőjét. Előfordulhat például, hogy a karbantartási időszak során le szeretné állítani az összes riasztás riasztási kézbesítését, kivéve a kritikus eszközökön észlelt kártevő-riasztásokat.

Az itt definiált API-k csak olvasási kizárási szabályként jelennek meg a helyszíni felügyeleti konzol **riasztás kizárások** ablakában.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A riasztás kizárása ablak, amely az összes kizárási szabályt megjeleníti. ":::

#### <a name="method---post"></a>Metódus – közzététel

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **ticketId**: meghatározza a karbantartási jegy azonosítóját a felhasználó rendszereiben.

- **TTL**: meghatározza a TTL (élettartam) értékét, amely a karbantartási időszak időtartama percben. A paraméter által definiált időszak után a rendszer automatikusan elindítja a riasztásokat.

- **motorok**: meghatározza, hogy a rendszer melyik biztonsági motorból távolítsa el a riasztásokat a karbantartási folyamat során:

   - ANOMÁLIADETEKTÁLÁSI

   - KÁRTEVŐ

   - OPERATÍV

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: azt határozza meg, hogy a IoT-érzékelő melyik Defender esetében hagyja figyelmen kívül a riasztásokat a karbantartási folyamat során. Ez ugyanaz az azonosító, amely a/API/v1/Appliances (GET) szolgáltatásból lett beolvasva.

- **alhálózatok**: azt határozza meg, hogy melyik alhálózatból kell letiltani a riasztásokat a karbantartási folyamat során. Az alhálózat a következő formátumban lesz elküldve: 192.168.0.0/16.

#### <a name="error-codes"></a>Hibakódok

- **201 (létrehozva)**: a művelet sikeresen befejeződött.

- **400 (hibás kérelem)**: a következő esetekben jelenik meg:

   - A **TTL** paraméter nem numerikus vagy nem pozitív.

   - Az **alhálózatok** paraméter formátuma helytelen.

   - A **ticketId** paraméter hiányzik.

   - A **motor** paraméter nem felel meg a meglévő biztonsági motoroknak.

- **404 (nem található)**: az érzékelők egyike nem létezik.

- **409 (ütközés)**: a jegy azonosítója egy másik nyitott karbantartási időszakhoz van csatolva.

- **500 (belső kiszolgálóhiba)**: bármilyen más váratlan hiba.

> [!NOTE]
> Győződjön meg arról, hogy a jegy azonosítója nem egy meglévő megnyitott ablakhoz van csatolva. A következő kizárási szabály jön létre: karbantartás-{token neve}-{Ticket ID}.

#### <a name="method---put"></a>Metódus – PUT

Lehetővé teszi a karbantartási időszak időtartamának frissítését a karbantartási folyamat elindítása után a **TTL** paraméter módosításával. Az új időtartam-definíció felülbírálja az előzőt.

Ez a módszer akkor hasznos, ha hosszabb időtartamot szeretne beállítani, mint a jelenleg konfigurált időtartam.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **ticketId**: meghatározza a karbantartási jegy azonosítóját a felhasználó rendszereiben.

- **TTL**: az ablak időtartamát adja meg percben.

#### <a name="error-code"></a>Hibakód

- **200 (ok)**: a művelet sikeresen befejeződött.

- **400 (hibás kérelem)**: a következő esetekben jelenik meg:

   - A **TTL** paraméter nem numerikus vagy nem pozitív.

   - A **ticketId** paraméter hiányzik.

   - A **TTL** paraméter hiányzik.

- **404 (nem található)**: a jegy azonosítója nincs nyitott karbantartási időszakhoz kapcsolva.

- **500 (belső kiszolgálóhiba)**: bármilyen más váratlan hiba.

> [!NOTE]
> Győződjön meg arról, hogy a jegy azonosítója egy meglévő megnyitott ablakhoz van csatolva.

#### <a name="method---delete"></a>Metódus – törlés

Egy meglévő karbantartási időszak bezárása.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **ticketId**: naplózza a karbantartási jegy azonosítóját a felhasználó rendszereiben.

#### <a name="error-code"></a>Hibakód

- **200 (ok)**: a művelet sikeresen befejeződött.

- **400 (hibás kérelem)**: hiányzik a **ticketId** paraméter.

- **404 (nem található)**: a jegy azonosítója nincs nyitott karbantartási időszakhoz kapcsolva.

- **500 (belső kiszolgálóhiba)**: bármilyen más váratlan hiba.

> [!NOTE]
> Győződjön meg arról, hogy a jegy azonosítója egy meglévő megnyitott ablakhoz van csatolva.

#### <a name="method---get"></a>Metódus – GET

A karbantartás során a rendszeren elvégzett összes nyitott, záró és frissítési művelet naplójának beolvasása. Csak olyan karbantartási időszakokra kérhet le naplókat, amelyek korábban aktívak voltak, és amelyeket lezártak.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **fromDate**: a naplókat az előre megadott dátummal és később szűri. A formátum 2019-12-30.

- **toDate**: a naplók szűrése az előre meghatározott dátumig. A formátum 2019-12-30.

- **ticketId**: egy adott jegy azonosítóhoz kapcsolódó naplók szűrése.

- **tokenName**: egy adott jogkivonat nevéhez kapcsolódó naplókat szűri.

#### <a name="error-code"></a>Hibakód

- **200 (ok)**: a művelet sikeresen befejeződött.

- **400 (hibás kérelem)**: a dátumformátum helytelen.

- **204 (nincs tartalom)**: a rendszer nem jelenít meg adatokat.

- **500 (belső kiszolgálóhiba)**: bármilyen más váratlan hiba.

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A karbantartási időszakra vonatkozó műveleteket jelölő JSON-objektumok tömbje.

#### <a name="response-structure"></a>Válasz szerkezete

| Név | Típus | Megjegyzés | Nullázható |
|--|--|--|--|
| **dateTime** | Sztring | Példa: "2012-04-23T18:25:43.511 Z" | nem |
| **ticketId** | Sztring | Példa: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nem |
| **tokenName** | Sztring | - | nem |
| **végrehajtók** | A sztring tömbje | - | igen |
| **sensorIds** | A sztring tömbje | - | igen |
| **alhálózatok** | A sztring tömbje | - | igen |
| **ttl** | Numerikus | - | igen |
| **operationType** | Sztring | Az értékek a következők: "OPEN", "UPDATE" és "Bezárás" | nem |

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | Curl-k-X POST-d {"ticketId": "<TICKET_ID>", TTL ": <TIME_TO_LIVE>," motorok ": [<ENGINE1, ENGINE2... ENGINEn>], "sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>], "alhálózatok": [<SUBNET1, SUBNET2 ALHÁLÓZATTAL... SUBNETn>]} "-H" hitelesítés: <AUTH_TOKEN> "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | Curl-k-X POST-d {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20", "motorok": ["ANOMÁLIa"], "sensorIds": ["5", "3"], "alhálózatok": ["10.0.0.3"]} "-H" Authorization: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| PUT | Curl-k-X PUT-d {"ticketId": "<TICKET_ID>", TTL ":" <TIME_TO_LIVE> "}"-H "hitelesítés: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | Curl-k-X PUT-d {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20"} "-H" hitelesítés: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| DELETE | Curl-k-X DELETE-d ' {"ticketId": "<TICKET_ID>"} "-H" engedélyezés: <AUTH_TOKEN> "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | Curl-k-X DELETE-d ' {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"} "-H" hitelesítés: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| GET | Curl-k-H "Authorization: <AUTH_TOKEN>" "https://<IP_ADDRESS>/external/v1/maintenanceWindow? fromDate =&toDate =&ticketId =&tokenName =" | Curl-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/maintenanceWindow? fromDate = 2020-01-01&toDate = 2020-07-14&ticketId = a5fe99c-d914-4bda-9332-307384fe40bf&tokenName = a " |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Felhasználói hitelesítő adatok hitelesítése –/External/Authentication/Validation

Ezzel az API-val érvényesítheti a felhasználói hitelesítő adatokat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-example"></a>Példa kérésre

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A műveleti állapot részleteit tartalmazó üzenet sztringje:

- **Sikeres – msg**: a hitelesítés sikerült

- Hiba **– hiba**: a hitelesítő adatok érvényesítése nem sikerült

#### <a name="device-fields"></a>Eszköz mezői

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **username** | Sztring | No |
| **alaphelyzetbe állítása** | Sztring | No |

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | Curl-k-d ' {"username": "<USER_NAME>", "password": "PASSWORD"} "https://<IP_ADDRESS>/External/Authentication/Validation" | Curl-k-d ' {"username": "myUser", "password": " 1234@abcd "} "" https:/ <span> /127.0.0.1/External/Authentication/Validation " |

### <a name="change-password---externalauthenticationset_password"></a>Jelszó módosítása –/External/Authentication/set_password

Ezzel az API-val engedélyezheti, hogy a felhasználók megváltoztassák a saját jelszavukat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-example"></a>Példa kérésre

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A műveleti állapot részleteit tartalmazó üzenet sztringje:

- **Sikeres – msg**: a jelszó le lett cserélve

- Hiba **– hiba**: felhasználói hitelesítési hiba

- Hiba **– hiba**: a jelszó nem felel meg a biztonsági házirendnek

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Eszköz mezői

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **username** | Sztring | No |
| **alaphelyzetbe állítása** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | Curl-k-d ' {"username": "<USER_NAME>", "password": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} "-H" Content-Type: Application/JSON "https://<IP_ADDRESS>/External/Authentication/set_password | Curl-k-d ' {"username": "myUser", "password": " 1234@abcd ", "new_password": " abcd@1234 "} "-H" Content-Type: Application/JSON "https:/ <span> /127.0.0.1/External/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Felhasználói jelszó frissítése Rendszerfelügyeleti webszolgáltatások által –/External/Authentication/set_password_by_admin

Ezzel az API-val engedélyezheti a rendszergazdáknak a megadott felhasználók jelszavainak módosítását. A IoT rendszergazdai szerepkörrel rendelkező Defender képes együttműködni az API-val. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-example"></a>Példa kérésre

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A műveleti állapot részleteit tartalmazó üzenet sztringje:

- **Sikeres – msg**: a jelszó le lett cserélve

- Hiba **– hiba**: felhasználói hitelesítési hiba

- Hiba **– hiba**: a felhasználó nem létezik

- Hiba **– hiba**: a jelszó nem felel meg a biztonsági házirendnek

- Hiba **– hiba**: a felhasználó nem rendelkezik a jelszó módosítására vonatkozó engedélyekkel

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Eszköz mezői

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **admin_username** | Sztring | No |
| **admin_password** | Sztring | No |
| **username** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | POST | Curl-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "username": "<USER_NAME>", "new_password": "<NEW_PASSWORD>"} "-H" Content-Type: Application/JSON "https://<IP_ADDRESS>/External/Authentication/set_password_by_admin | Curl-k-d ' {"admin_user": "adminUser", "admin_password": " 1234@abcd ", "username": "myUser", "new_password": " abcd@1234 "} "-H" Content-Type: Application/JSON "https:/ <span> /127.0.0.1/External/Authentication/set_password_by_admin |

## <a name="next-steps"></a>Következő lépések

- [Egy eszközkészlet érzékelői általi észlelések vizsgálata](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Egy eszközkészlet összes vállalati érzékelője általi észlelések vizsgálata](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
