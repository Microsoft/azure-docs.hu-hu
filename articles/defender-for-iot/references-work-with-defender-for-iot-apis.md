---
title: A Defender for IoT API-k használata
description: Külső REST API használatával férhet hozzá az érzékelők és a felügyeleti konzol által felderített adatokhoz, és műveleteket hajthat végre ezekkel az adatokkal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 44ea6e8343203a9cb18947f31f45aa0b023178b0
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624574"
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

- /api/v1/devices

- /api/v1/devices/connections

- /api/v1/devices/cves

- /api/v1/alerts

- /api/v1/events

- /api/v1/reports/vulnerabilities/devices

- /api/v1/reports/vulnerabilities/security

- /api/v1/reports/vulnerabilities/operational

- /api/external/authentication/validation

- /External/Authentication/set_password

- /External/Authentication/set_password_by_admin

### <a name="retrieve-device-information"></a>Eszköz adatainak beolvasása

Ezzel az API-val lekérheti az összes olyan eszköz listáját, amelyet a Defender for IoT érzékelő észlelt.

#### <a name="apiv1devices"></a>/api/v1/devices

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
| **id** | Numerikus | Nem | - |
| **erőforrásrekordjaiba** | JSON-tömb | Igen | IP-címek (a két hálózati adapterrel rendelkező eszközök esetében több cím is lehet) |
| **name** | Sztring | Nem | - |
| **típusa** | Sztring | Nem | Ismeretlen, mérnöki állomás, PLC, HMI, történész, tartományvezérlő, adatbázis-kiszolgáló, vezeték nélküli hozzáférési pont, útválasztó, kapcsoló, kiszolgáló, munkaállomás, IP-kamera, nyomtató, tűzfal, terminál, VPN Gateway, Internet vagy csoportos küldés és szórás |
| **macAddresses** | JSON-tömb | Igen | MAC-címek (két hálózati adapterrel rendelkező eszköz esetén több cím is lehet) |
| **operatingSystem** | Sztring | Igen | - |
| **engineeringStation** | Logikai | Nem | Igaz vagy hamis |
| **szkenner** | Logikai | Nem | Igaz vagy hamis |
| **jogosult** | Logikai | Nem | Igaz vagy hamis |
| **gyártó** | Sztring | Igen | - |
| **protokollok** | JSON-tömb | Igen | Protokoll objektum |
| **belső vezérlőprogram** | JSON-tömb | Igen | Belső vezérlőprogram objektuma |

#### <a name="protocol-fields"></a>Protokoll mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Név** | Sztring | Nem |  |
| **Címek** | JSON-tömb | Igen | Fő vagy numerikus értékek |

#### <a name="firmware-fields"></a>Belső vezérlőprogram mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **sorozatszám** | Sztring | Nem | N/A vagy a tényleges érték |
| **modell** | Sztring | Nem | N/A vagy a tényleges érték |
| **firmwareVersion** | Dupla | Nem | N/A vagy a tényleges érték |
| **additionalData** | Sztring | Nem | N/A vagy a tényleges érték |
| **moduleAddress** | Sztring | Nem | N/A vagy a tényleges érték |
| **rack** | Sztring | Nem | N/A vagy a tényleges érték |
| **slot** | Sztring | Nem | N/A vagy a tényleges érték |
| **Cím** | Sztring | Nem | N/A vagy a tényleges érték |

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

### <a name="retrieve-device-connection-information"></a>Eszköz csatlakoztatási adatainak beolvasása

Ezzel az API-val egy eszközön lévő összes kapcsolat listáját kérheti le.

#### <a name="apiv1devicesconnections"></a>/api/v1/devices/connections

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
| **firstDeviceId** | Numerikus | Nem | - |
| **secondDeviceId** | Numerikus | Nem | - |
| **lastSeen** | Numerikus | Nem | EPOCH (UTC) |
| **felderített** | Numerikus | Nem | EPOCH (UTC) |
| **portok** | Szám tömb | Nem | - |
| **protokollok** | JSON-tömb | Nem | Protokoll mező |

#### <a name="protocol-field"></a>Protokoll mező

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **name** | Sztring | Nem | - |
| **parancsok** | Sztringtömb | Nem | - |

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

### <a name="retrieve-information-on-cves"></a>Információk beolvasása a CVEs

Ezzel az API-val lekérheti a hálózatban lévő eszközökön észlelt összes ismert CVEs listáját.

#### <a name="apiv1devicescves"></a>/api/v1/devices/cves

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
| **cveId** | Sztring | Nem | - |
| **IP-cím** | Sztring | Nem | IP-cím |
| **pontszám** | Sztring | Nem | 0,0 – 10,0 |
| **attackVector** | Sztring | Nem | Hálózat, szomszédos hálózat, helyi vagy fizikai |
| **Leírás** | Sztring | Nem | - |

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

### <a name="retrieve-alert-information"></a>Riasztási adatok beolvasása

Ezzel az API-val lekérheti az összes olyan riasztás listáját, amelyeket a Defender IoT-érzékelő észlelt.

#### <a name="apiv1alerts"></a>/api/v1/alerts

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
| **ID (Azonosító)** | Numerikus | Nem | - |
| **idő** | Numerikus | Nem | EPOCH (UTC) |
| **cím** | Sztring | Nem | - |
| **üzenetet** | Sztring | Nem | - |
| **Súlyosság** | Sztring | Nem | Figyelmeztetés, másodlagos, fő vagy kritikus |
| **motor** | Sztring | Nem | Protokoll megsértése, szabályzat megsértése, kártevő, rendellenesség vagy működési |
| **sourceDevice** | Numerikus | Igen | Eszközazonosító |
| **destinationDevice** | Numerikus | Igen | Eszközazonosító |
| **additionalInformation** | További információ objektum | Igen | - |

#### <a name="additional-information-fields"></a>További információ mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Leírás** | Sztring | Nem | - |
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

### <a name="retrieve-timeline-events"></a>Idővonal eseményeinek beolvasása

Ezzel az API-val az esemény idővonalára jelentett események listáját kérheti le.

#### <a name="apiv1events"></a>/api/v1/events

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
| **időbélyeg** | Numerikus | Nem | EPOCH (UTC) |
| **cím** | Sztring | Nem | - |
| **Súlyosság** | Sztring | Nem | INFORMÁCIÓ, értesítés vagy riasztás |
| **tulajdonosa** | Sztring | Igen | Ha az esemény manuálisan lett létrehozva, akkor ez a mező tartalmazza az eseményt létrehozó felhasználónevet. |
| **tartalom** | Sztring | Nem | - |

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

### <a name="retrieve-vulnerability-information"></a>Sebezhetőségi adatok beolvasása

Ezzel az API-val megkérheti a sebezhetőségi felmérés eredményeit az egyes eszközökön.

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilities/devices

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
| **name** | Sztring | Nem | - |
| **erőforrásrekordjaiba** | JSON-tömb | Nem | - |
| **securityScore** | Numerikus | Nem | - |
| **gyártó** | Sztring | Igen |  |
| **firmwareVersion** | Sztring | Igen | - |
| **modell** | Sztring | Igen | - |
| **isWirelessAccessPoint** | Logikai | Nem | Igaz vagy hamis |
| **operatingSystem** | Operációs rendszer objektuma | Igen | - |
| **biztonsági rések** | Sebezhetőségi objektum | Igen | - |

#### <a name="operating-system-fields"></a>Operációs rendszer mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Név** | Sztring | Igen | - |
| **Típus** | Sztring | Igen | - |
| **Verzió** | Sztring | Igen | - |
| **latestVersion** | Sztring | Igen | - |

#### <a name="vulnerabilities-fields"></a>Biztonsági rések mezői
 
| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **antiViruses** | JSON-tömb | Igen | Vírusvédelmi nevek |
| **plainTextPasswords** | JSON-tömb | Igen | Jelszó-objektumok |
| **remoteAccess** | JSON-tömb | Igen | Távelérési objektumok |
| **isBackupServer** | Logikai | Nem | Igaz vagy hamis |
| **openedPorts** | JSON-tömb | Igen | Megnyitott portok objektumai |
| **isEngineeringStation** | Logikai | Nem | Igaz vagy hamis |
| **isKnownScanner** | Logikai | Nem | Igaz vagy hamis |
| **cves** | JSON-tömb | Igen | CVE-objektumok |
| **isUnauthorized** | Logikai | Nem | Igaz vagy hamis |
| **malwareIndicationsDetected** | Logikai | Nem | Igaz vagy hamis |
| **weakAuthentication** | JSON-tömb | Igen | Gyenge hitelesítést használó észlelt alkalmazások |

#### <a name="password-fields"></a>Jelszó mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **alaphelyzetbe állítása** | Sztring | Nem | - |
| **protokoll** | Sztring | Nem | - |
| **erősségét** | Sztring | Nem | Nagyon gyenge, gyenge, közepes vagy erős |

#### <a name="remote-access-fields"></a>Távelérési mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Port** | Numerikus | Nem | - |
| **közlekedési** | Sztring | Nem | TCP vagy UDP |
| **ügyfél** | Sztring | Nem | IP-cím |
| **clientSoftware** | Sztring | Nem | SSH, VNC, távoli asztal vagy csapat megjelenítője |

#### <a name="open-port-fields"></a>Port mezőinek megnyitása

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Port** | Numerikus | Nem | - |
| **közlekedési** | Sztring | Nem | TCP vagy UDP |
| **protokoll** | Sztring | Igen | - |
| **isConflictingWithFirewall** | Logikai | Nem | Igaz vagy hamis |

#### <a name="cve-fields"></a>CVE-mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Sztring | Nem | - |
| **pontszám** | Numerikus | Nem | Dupla |
| **Leírás** | Sztring | Nem | - |

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

### <a name="retrieve-security-vulnerabilities"></a>Biztonsági rések beolvasása

Ezzel az API-val egy általános sebezhetőségi felmérés eredményeit kérheti le. Ez az értékelés betekintést nyújt a rendszerek biztonsági szintjébe.

Ez az értékelés az általános hálózati és rendszerinformáción alapul, nem egy adott eszköz kiértékelésén.

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilities/security

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

### <a name="retrieve-operational-vulnerabilities"></a>Működési biztonsági rések beolvasása

Ezzel az API-val egy általános sebezhetőségi felmérés eredményeit kérheti le. Ez az értékelés betekintést nyújt a hálózat működési állapotára. A szolgáltatás általános hálózati és rendszerinformáción alapul, nem egy adott eszköz kiértékelésével.

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilities/operational

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
| **hálózati** | Sztring | IP-cím |
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

### <a name="validate-user-credentials"></a>Felhasználói hitelesítő adatok érvényesítése

Ezzel az API-val ellenőrizheti a Defender IoT felhasználónevét és jelszavát. A IoT összes felhasználói szerepköre az API-val is működhet.

Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="apiexternalauthenticationvalidation"></a>/api/external/authentication/validation

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **username** | Sztring | Nem |
| **alaphelyzetbe állítása** | Sztring | Nem |

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

### <a name="change-password"></a>Change password

Ezzel az API-val engedélyezheti, hogy a felhasználók megváltoztassák a saját jelszavukat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="externalauthenticationset_password"></a>/External/Authentication/set_password

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
| **username** | Sztring | Nem |
| **alaphelyzetbe állítása** | Sztring | Nem |
| **new_password** | Sztring | Nem |

### <a name="user-password-update-by-system-admin"></a>Felhasználói jelszó frissítése rendszergazda szerint

Ezzel az API-val engedélyezheti a rendszergazdáknak a megadott felhasználók jelszavainak módosítását. A IoT rendszergazda felhasználói szerepkörei az API-val is működhetnek. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="externalauthenticationset_password_by_admin"></a>/External/Authentication/set_password_by_admin

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
| **admin_username** | Sztring | Nem |
| **admin_password** | Sztring | Nem |
| **username** | Sztring | Nem |
| **new_password** | Sztring | Nem |

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

#### <a name="change-password"></a>Change password

Ezzel az API-val engedélyezheti, hogy a felhasználók megváltoztassák a saját jelszavukat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

- **/External/Authentication/set_password**

#### <a name="user-password-update-by-system-admin"></a>Felhasználói jelszó frissítése rendszergazda szerint

Ezzel az API-val engedélyezheti, hogy a rendszergazdák módosítsák a jelszavakat bizonyos felhasználók számára. A IoT rendszergazdai szerepkörrel rendelkező Defender képes együttműködni az API-val. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

- **/External/Authentication/set_password_by_admin**

### <a name="retrieve-device-information"></a>Eszköz adatainak beolvasása

Ez az API a Defender által a helyszíni felügyeleti konzolhoz csatlakozó IoT érzékelők számára észlelt összes eszköz listáját kéri le.

- **/external/v1/devices**

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
| **sensorId** | Numerikus | Nem | - |
| **Zónaazonosító** | Numerikus | Igen | - |
| **siteId** | Numerikus | Igen | - |
| **erőforrásrekordjaiba** | JSON-tömb | Igen | IP-címek (a két hálózati adapterrel rendelkező eszközök esetében több cím is lehet) |
| **name** | Sztring | Nem | - |
| **típusa** | Sztring | Nem | Ismeretlen, mérnöki állomás, PLC, HMI, történész, tartományvezérlő, adatbázis-kiszolgáló, vezeték nélküli hozzáférési pont, útválasztó, kapcsoló, kiszolgáló, munkaállomás, IP-kamera, nyomtató, tűzfal, terminál, VPN Gateway, Internet vagy csoportos küldés és szórás |
| **macAddresses** | JSON-tömb | Igen | MAC-címek (két hálózati adapterrel rendelkező eszköz esetén több cím is lehet) |
| **operatingSystem** | Sztring | Igen | - |
| **engineeringStation** | Logikai | Nem | Igaz vagy hamis |
| **szkenner** | Logikai | Nem | Igaz vagy hamis |
| **jogosult** | Logikai | Nem | Igaz vagy hamis |
| **gyártó** | Sztring | Igen | - |
| **Protokollok** | JSON-tömb | Igen | Protokoll objektum |
| **belső vezérlőprogram** | JSON-tömb | Igen | Belső vezérlőprogram objektuma |

#### <a name="protocol-fields"></a>Protokoll mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| Name | Sztring | Nem | - |
| Címek | JSON-tömb | Igen | Fő vagy numerikus értékek |

#### <a name="firmware-fields"></a>Belső vezérlőprogram mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **sorozatszám** | Sztring | Nem | N/A vagy a tényleges érték |
| **modell** | Sztring | Nem | N/A vagy a tényleges érték |
| **firmwareVersion** | Dupla | Nem | N/A vagy a tényleges érték |
| **additionalData** | Sztring | Nem | N/A vagy a tényleges érték |
| **moduleAddress** | Sztring | Nem | N/A vagy a tényleges érték |
| **rack** | Sztring | Nem | N/A vagy a tényleges érték |
| **slot** | Sztring | Nem | N/A vagy a tényleges érték |
| **Cím** | Sztring | Nem | N/A vagy a tényleges érték |

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

### <a name="retrieve-alert-information"></a>Riasztási adatok beolvasása

Ezzel az API-val lekérheti az összes vagy szűrt riasztást egy helyszíni felügyeleti konzolról.

#### <a name="externalv1alerts"></a>/external/v1/alerts

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
| **ID (Azonosító)** | Numerikus | Nem | - |
| **idő** | Numerikus | Nem | EPOCH (UTC) |
| **cím** | Sztring | Nem | - |
| **üzenetet** | Sztring | Nem | - |
| **Súlyosság** | Sztring | Nem | Figyelmeztetés, másodlagos, fő vagy kritikus |
| **motor** | Sztring | Nem | Protokoll megsértése, szabályzat megsértése, kártevő, rendellenesség vagy működési |
| **sourceDevice** | Numerikus | Igen | Eszközazonosító |
| **destinationDevice** | Numerikus | Igen | Eszközazonosító |
| **additionalInformation** | További információ objektum | Igen | - |

#### <a name="additional-information-fields"></a>További információ mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Leírás** | Sztring | Nem | - |
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
| **művelet** | Sztring | Nem | leíró vagy handleAndLearn |

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
| **tartalom/hiba** | Sztring | Nem | Ha a kérelem sikeres, megjelenik a Content (tartalom) tulajdonság. Ellenkező esetben a hiba tulajdonság jelenik meg. |

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

### <a name="alert-exclusions-maintenance-window"></a>Riasztás kizárása (karbantartási időszak)

Adja meg azokat a feltételeket, amelyekben a rendszer nem küld riasztásokat. Például megadhatja és frissítheti a leállítási és indítási időpontokat, az olyan eszközöket és alhálózatokat, amelyeket ki kell zárni a riasztások kiváltásakor, vagy a kizárni kívánt IoT-motorok védelmezőjét. Előfordulhat például, hogy a karbantartási időszak során le szeretné állítani az összes riasztás riasztási kézbesítését, kivéve a kritikus eszközökön észlelt kártevő-riasztásokat.

Az itt definiált API-k csak olvasási kizárási szabályként jelennek meg a helyszíni felügyeleti konzol **riasztás kizárások** ablakában.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A riasztás kizárása ablak, amely az összes kizárási szabályt megjeleníti. ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

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

### <a name="authenticate-user-credentials"></a>Felhasználói hitelesítő adatok hitelesítése

Ezzel az API-val érvényesítheti a felhasználói hitelesítő adatokat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="externalauthenticationvalidation"></a>/external/authentication/validation

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
| **username** | Sztring | Nem |
| **alaphelyzetbe állítása** | Sztring | Nem |

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>Change password

Ezzel az API-val engedélyezheti, hogy a felhasználók megváltoztassák a saját jelszavukat. A IoT összes felhasználói szerepköre az API-val is működhet. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="externalauthenticationset_password"></a>/External/Authentication/set_password

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
| **username** | Sztring | Nem |
| **alaphelyzetbe állítása** | Sztring | Nem |
| **new_password** | Sztring | Nem |

### <a name="user-password-update-by-system-admin"></a>Felhasználói jelszó frissítése rendszergazda szerint

Ezzel az API-val engedélyezheti a rendszergazdáknak a megadott felhasználók jelszavainak módosítását. A IoT rendszergazdai szerepkörrel rendelkező Defender képes együttműködni az API-val. Ehhez az API-hoz nem szükséges a IoT hozzáférési tokenhez tartozó Defender használata.

#### <a name="externalauthenticationset_password_by_admin"></a>/External/Authentication/set_password_by_admin

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
| **admin_username** | Sztring | Nem |
| **admin_password** | Sztring | Nem |
| **username** | Sztring | Nem |
| **new_password** | Sztring | Nem |

## <a name="see-also"></a>Lásd még
[Érzékelő észlelésének vizsgálata egy eszköz leltározásakor](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 Az [eszközök leltárában szereplő összes vállalati érzékelő észlelésének vizsgálata](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
