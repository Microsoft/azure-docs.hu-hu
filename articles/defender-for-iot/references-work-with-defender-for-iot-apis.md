---
title: A Defender for IoT API-k használata
description: Használjon külső REST API az érzékelők és a felügyeleti konzolok által felderített adatokhoz való hozzáféréshez, és hajtson végre műveleteket az adatokkal.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: e7833a20d4f708ecb5b80394fae2c56fc07c9489
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752731"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender az IoT-érzékelőhöz és a felügyeleti konzol API-khoz

Használjon külső REST API az érzékelők és a felügyeleti konzolok által felderített adatokhoz való hozzáféréshez, és hajtson végre műveleteket az adatokkal.

A kapcsolatok SSL-lel vannak biztosítva.

## <a name="getting-started"></a>Első lépések

Általánosságban elmondható, hogy ha külső API-t használ a Azure Defender for IoT-érzékelőn vagy a helyszíni felügyeleti konzolon, létre kell hoznia egy hozzáférési jogkivonatot. Az érzékelőn és a helyszíni felügyeleti konzolon használt hitelesítési API-khoz nincs szükség jogkivonatokra.

Jogkivonat létrehozása:

1. A **Rendszerbeállítások ablakban** válassza a **Hozzáférési jogkivonatok lehetőséget.**
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Képernyőkép a Hozzáférési jogkivonatok gombot kiemelő Rendszerbeállítások ablakról.":::

2. Válassza **az Új jogkivonat létrehozása lehetőséget.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Új jogkivonat létrehozásához kattintson a gombra.":::

3. Írja le az új jogkivonat célját, és válassza a **Tovább lehetőséget.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Hozzon létre egy új jogkivonatot, és adja meg a hozzá társított integráció nevét.":::

4. Megjelenik a hozzáférési jogkivonat. Másolja ki, mert nem jelenik meg újra.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Másolja ki az integrációhoz szükséges hozzáférési jogkivonatot.":::

5. Válassza a **Befejezés** gombot. A létrehozott jogkivonatok a Hozzáférési jogkivonatok párbeszédpanelen **jelennek** meg.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Az Eszközkivonatok párbeszédpanel képernyőképe kitöltött jogkivonatokkal":::

   **A** használat azt jelzi, hogy mikor érkezett utoljára külső hívás ezzel a jogkivonattal.

   Ha **n/A** jelenik meg a **jogkivonatHoz** használt mezőben, az érzékelő és a csatlakoztatott kiszolgáló közötti kapcsolat nem működik.

6. Adjon hozzá egy Authorization (Engedélyezés) **című** HTTP-fejlécet a kérelemhez, és állítsa annak értékét a létrehozott jogkivonatra.

## <a name="sensor-api-specifications"></a>Sensor API-specifikációk

Ez a szakasz a következő érzékelő API-kat ismerteti:

- [Eszközinformációk lekérése – /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Eszközkapcsolati adatok lekérése – /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [A CVES-ekkel kapcsolatos információk lekérése – /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Riasztási adatok lekérése – /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Idővonal eseményeinek lekérése – /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Biztonsági rések információinak lekérése – /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Biztonsági rések lekérése – /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Működési biztonsági rések lekérése – /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Felhasználói hitelesítő adatok érvényesítése – /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Jelszó módosítása – /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Felhasználói jelszó frissítése a rendszergazda által – /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Eszközinformációk lekérése – /api/v1/devices

Ezzel az API-val lekért lista az összes olyan eszközről, amit a Defender for IoT-érzékelő észlelt.

#### <a name="method"></a>Metódus

**Kap**

Lekért egy listát az összes eszközről, amit a Defender for IoT-érzékelő észlelt.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **authorized (engedélyezett):** Csak az engedélyezett és jogosulatlan eszközök szűréséhez.

  **Példák:**

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközöket képviselő JSON-objektumok tömbje.

#### <a name="device-fields"></a>Eszközmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **id** | Numerikus | No | - |
| **ipAddresses (Ip-címek)** | JSON-tömb | Yes | IP-címek (internetcímek vagy kettős hálózati kártyás eszközök esetén egynél több cím is lehet) |
| **name** | Sztring | No | - |
| **Típus** | Sztring | No | Ismeretlen, mérnöki állomás, PLC, HMI, tartományvezérlő, DB-kiszolgáló, vezeték nélküli hozzáférési pont, útválasztó, kapcsoló, kiszolgáló, munkaállomás, IP-kamera, nyomtató, tűzfal, terminálállomás, VPN Gateway, internet vagy csoportos küldés és szórás |
| **macAddresses** | JSON-tömb | Yes | MAC-címek (egynél több cím is lehet a kettős NICs-et használó eszközök esetén) |
| **operatingSystem** | Sztring | Yes | - |
| **engineeringStation** | Logikai | No | Igaz vagy hamis |
| **Szkenner** | Logikai | No | Igaz vagy hamis |
| **Engedélyezett** | Logikai | No | Igaz vagy hamis |
| **Szállító** | Sztring | Yes | - |
| **Protokollok** | JSON-tömb | Yes | Protokollobjektum |
| **Firmware** | JSON-tömb | Yes | Belsővezérlőprogram-objektum |

#### <a name="protocol-fields"></a>Protokollmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Név** | Sztring | No |  |
| **Címek** | JSON-tömb | Yes | Fő vagy numerikus értékek |

#### <a name="firmware-fields"></a>Belső vezérlőprogram mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Sorozat** | Sztring | No | N/A vagy a tényleges érték |
| **Modell** | Sztring | No | N/A vagy a tényleges érték |
| **firmwareVersion** | Dupla | No | N/A vagy a tényleges érték |
| **additionalData (további adatok)** | Sztring | No | N/A vagy a tényleges érték |
| **moduleAddress (modul-cím)** | Sztring | No | N/A vagy a tényleges érték |
| **Rack** | Sztring | No | N/A vagy a tényleges érték |
| **Slot** | Sztring | No | N/A vagy a tényleges érték |
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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https: <span> //127 <span> .0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Eszközkapcsolati adatok lekérése – /api/v1/devices/connections

Ezzel az API-val lekért lista az eszközönkénti kapcsolatokról.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

Ha nem adja meg a lekérdezési paramétereket, a rendszer az összes eszközkapcsolatot visszaadja.

**Például:**

`/api/v1/devices/connections`

- **deviceId:** Szűrés adott eszközazonosító alapján a kapcsolatai megtekintéséhez.

  **Például:**

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes:** A kapcsolatok aktívvá kapcsolódásának ideje ettől visszamenőleg, percek szerint.

  **Például:**

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore:** Csak a megadott időpont előtt észlelt kapcsolatokat szűrje (ezredmásodpercben, UTC).

  **Például:**

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter:** Csak egy adott idő után észlelt kapcsolatokat szűrje (ezredmásodpercben, UTC).

  **Például:**

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Eszközkapcsolatokat képviselő JSON-objektumok tömbje.

#### <a name="fields"></a>Mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **firstDeviceId (firstDeviceId)** | Numerikus | No | - |
| **secondDeviceId (secondDeviceId)** | Numerikus | No | - |
| **lastSeen (utolsó év)** | Numerikus | No | Epoch (UTC) |
| **Felfedezett** | Numerikus | No | Epoch (UTC) |
| **Portok** | Számtömb | No | - |
| **Protokollok** | JSON-tömb | No | Protokoll mező |

#### <a name="protocol-field"></a>Protokoll mező

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **name** | Sztring | No | - |
| **Parancsok** | Sztringtömb | No | - |

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
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/ <deviceId> /connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" <span> 'https:/ /127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>A CVES-ek információinak lekérése – /api/v1/devices/cves

Ezzel az API-val lekért lista a hálózatban található eszközökön felderített összes ismert CVE-ről.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

Alapértelmezés szerint ez az API az összes olyan CVE-t használó eszköz IP-cím listáját tartalmazza, amely minden IP-címhez legfeljebb 100 legjobb pontszámú CVE-t tartalmaz.

**Például:**

`/api/v1/devices/cves`

- **deviceId:** Szűrhet egy adott eszköz IP-címe alapján, és akár 100 legmagasabb pontszámú, az adott eszközön azonosított CVE-t is be tud szerezni.

  **Például:**

  `/api/v1/devices/<ipAddress>/cves`

- **top:** Az egyes eszközök IP-címeinek lekérhető legmagasabb pontszámú CVE-k száma.

  **Például:**

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az IP-címeken azonosított CVE-eket képviselő JSON-objektumok tömbje.

#### <a name="fields"></a>Mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **cveId** | Sztring | No | - |
| **ipAddress (Ip-cím)** | Sztring | No | IP-cím |
| **Pontszám** | Sztring | No | 0.0 - 10.0 |
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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/ <deviceIpAddress> /cves?top= | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcdcabcd" https:/ <span> /127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Riasztási adatok lekérése – /api/v1/alerts

Ezzel az API-val lekért lista az összes olyan riasztásról, amit a Defender for IoT-érzékelő észlelt.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **state**: Csak a kezelt vagy nem kezelt riasztások szűréséhez.

  **Például:**

  `/api/v1/alerts?state=handled`

- **fromTime:** Adott időpontból létrehozott riasztások szűrése (ezredmásodpercben, UTC).

  **Például:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** Csak egy adott időpont előtt létrehozott riasztások szűrése (ezredmásodpercben, UTC).

  **Például:**

  `/api/v1/alerts?toTime=<epoch>`

- **type**: Riasztások szűrése adott típus szerint. Szűrni használható meglévő típusok: váratlan új eszközök, kapcsolat bontása.

  **Például:**

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Riasztásokat képviselő JSON-objektumok tömbje.

#### <a name="alert-fields"></a>Riasztásmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Numerikus | No | - |
| **Idő** | Numerikus | No | Epoch (UTC) |
| **cím** | Sztring | No | - |
| **Üzenetet** | Sztring | No | - |
| **Súlyossága** | Sztring | No | Figyelmeztetés, kisebb, nagyobb vagy kritikus |
| **Motor** | Sztring | No | Protokollsértés, szabályzatsértés, kártevő, anomália vagy működési |
| **sourceDevice** | Numerikus | Yes | Eszközazonosító |
| **destinationDevice (célszámítógép)** | Numerikus | Yes | Eszközazonosító |
| **sourceDeviceAddress (sourceDeviceAddress)** | Numerikus | Yes | IP, MAC, Null |
| **destinationDeviceAddress** | Numerikus | Yes | IP, MAC, Null |
| **szervizelésSteps** | Sztring | Yes | A riasztásban leírt javítási lépések |
| **additionalInformation (további információk)** | További információs objektum | Yes | - |

Vegye figyelembe, hogy az /api/v2/ a következő információkhoz szükséges:

- sourceDeviceAddress 
- destinationDeviceAddress
- szervizelésSteps

#### <a name="additional-information-fields"></a>További információs mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Leírás** | Sztring | No | - |
| **Információ** | JSON-tömb | Nem | Sztring |

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
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/api/v1 /alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>Idővonal eseményeinek lekérése – /api/v1/events

Ezzel az API-val kérheti le az esemény idővonalára jelentett események listáját.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **minutesTimeFrame:** Az időkeret ettől visszamenőleg, percek szerint, amikor a rendszer jelentést ad az eseményekről.

  **Például:**

  `/api/v1/events?minutesTimeFrame=20`

- **type**: Az események listájának adott típus alapján való szűréséhez.

  **Példák:**

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Riasztásokat képviselő JSON-objektumok tömbje.

#### <a name="event-fields"></a>Eseménymezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|--|
| **Időbélyeg** | Numerikus | No | Epoch (UTC) |
| **cím** | Sztring | No | - |
| **Súlyossága** | Sztring | No | INFORMÁCIÓ, ÉRTESÍTÉS VAGY RIASZTÁS |
| **Tulajdonos** | Sztring | Yes | Ha az esemény manuálisan lett létrehozva, ez a mező tartalmazza az eseményt létrehozó felhasználónevet |
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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Biztonsági rések információinak lekérése – /api/v1/reports/vulnerabilities/devices

Ezzel az API-val minden eszközre lekért biztonsági rések felmérési eredményeit.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A értékelt eszközöket képviselő JSON-objektumok tömbje.

Az eszközobjektum a következő elemeket tartalmazza:

- Általános adatok

- Értékelési pontszám

- Biztonsági rések

#### <a name="device-fields"></a>Eszközmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **name** | Sztring | No | - |
| **ipAddresses** | JSON-tömb | No | - |
| **securityScore (biztonsági pontszám)** | Numerikus | No | - |
| **Szállító** | Sztring | Yes |  |
| **firmwareVersion** | Sztring | Yes | - |
| **Modell** | Sztring | Yes | - |
| **isWirelessAccessPoint** | Logikai | No | Igaz vagy hamis |
| **operatingSystem** | Operációsrendszer-objektum | Yes | - |
| **Sebezhetőség** | Biztonsági rések objektum | Yes | - |

#### <a name="operating-system-fields"></a>Operációs rendszer mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Név** | Sztring | Yes | - |
| **Típus** | Sztring | Yes | - |
| **Verzió** | Sztring | Yes | - |
| **latestVersion (legújabb verzió)** | Sztring | Yes | - |

#### <a name="vulnerabilities-fields"></a>Biztonsági rések mezői
 
| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Antiviruses** | JSON-tömb | Yes | Víruskeresők nevei |
| **plainTextPasswords** | JSON-tömb | Yes | Jelszóobjektumok |
| **Remoteaccess** | JSON-tömb | Yes | Távelérési objektumok |
| **isBackupServer** | Logikai | No | Igaz vagy hamis |
| **openedPorts** | JSON-tömb | Yes | Megnyitott portobjektumok |
| **isEngineeringStation** | Logikai | No | Igaz vagy hamis |
| **isKnownScanner** | Logikai | No | Igaz vagy hamis |
| **cves** | JSON-tömb | Yes | CVE-objektumok |
| **isUnauthorized** | Logikai | No | Igaz vagy hamis |
| **malwareIndicationsDetected** | Logikai | No | Igaz vagy hamis |
| **weakAuthentication (gyenge hitelesítés)** | JSON-tömb | Yes | Gyenge hitelesítést használó észlelt alkalmazások |

#### <a name="password-fields"></a>Jelszómezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **alaphelyzetbe állítása** | Sztring | No | - |
| **Protokoll** | Sztring | No | - |
| **Erejét** | Sztring | No | Nagyon gyenge, Gyenge, Közepes vagy Erős |

#### <a name="remote-access-fields"></a>Távelérési mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Port** | Numerikus | No | - |
| **Közlekedési** | Sztring | No | TCP vagy UDP |
| **Ügyfél** | Sztring | No | IP-cím |
| **clientSoftware** | Sztring | No | SSH, VNC, távoli asztal vagy csapatmegjelenítő |

#### <a name="open-port-fields"></a>Portmezők megnyitása

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Port** | Numerikus | No | - |
| **Közlekedési** | Sztring | No | TCP vagy UDP |
| **Protokoll** | Sztring | Yes | - |
| **isConflictingWithFirewall** | Logikai | No | Igaz vagy hamis |

#### <a name="cve-fields"></a>CVE-mezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Sztring | No | - |
| **Pontszám** | Numerikus | No | Dupla |
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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Biztonsági rések lekérése – /api/v1/reports/vulnerabilities/security

Ezzel az API-val egy általános sebezhetőségi felmérés eredményeit kérheti le. Ez az értékelés betekintést nyújt a rendszer biztonsági szintjébe.

Ez az értékelés általános hálózati és rendszerinformációkon alapul, nem pedig egy adott eszközértékelésen.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A értékelt eredményeket képviselő JSON-objektum. Minden kulcs nullázható lehet. Ellenkező esetben egy nem nullázható kulcsokat tartalmazó JSON-objektumot fog tartalmazni.

### <a name="result-fields"></a>Eredménymezők

**Kulcsok**

**unauthorizedDevices (nem engedélyezett devices)**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Cím** | Sztring | IP-cím |
| **name** | Sztring | - |
| **firstDetectionTime** | Numerikus | Epoch (UTC) |
| lastSeen (utolsó év) | Numerikus | Epoch (UTC) |

**illegalTrafficByFirewallRules**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Szerver** | Sztring | IP-cím |
| **Ügyfél** | Sztring | IP-cím |
| **Port** | Numerikus | - |
| **Közlekedési** | Sztring | TCP, UDP vagy ICMP |

**weakFirewallRules**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Források** | Források JSON-tömbje. Minden forrás négy formátum bármelyikében lehet. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Célpontok** | Cél JSON-tömbje. Minden cél négy formátum bármelyikében lehet. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Portok** | Portok JSON-tömbje három formátum bármelyikében | "Any", "port (protocol, if detected)", "from port-to port (protocol, if detected)" |

**accessPoints**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **macAddress** | Sztring | MAC-cím |
| **Szállító** | Sztring | Szállító neve |
| **ipAddress (Ip-cím)** | Sztring | IP-cím vagy N/A |
| **name** | Sztring | Eszköz neve vagy N/A |
| **Vezeték nélküli** | Sztring | Nem, Gyanús vagy Igen |

**connectionsBetweenSubnets**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **Szerver** | Sztring | IP-cím |
| **Ügyfél** | Sztring | IP-cím |

**industrialMalwareIndicators**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **detectionTime (észlelés ideje)** | Numerikus | Epoch (UTC) |
| **alertMessage** | Sztring | - |
| **Leírás** | Sztring | - |
| **Eszközök** | JSON-tömb | Eszköznevek | 

**internetConnections**

| Mező neve | Típus | Értékek listája |
| ---------- | ---- | -------------- |
| **internalAddress (belső cím)** | Sztring | IP-cím |
| **Engedélyezett** | Logikai | Igen vagy nem | 
| **externalAddresses (külső cím)** | JSON-tömb | IP-cím |

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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Működési biztonsági rések lekérése – /api/v1/reports/vulnerabilities/operational

Ezzel az API-val egy általános sebezhetőségi felmérés eredményeit kérheti le. Ez az értékelés betekintést nyújt a hálózat működési állapotába. Általános hálózati és rendszerinformáción alapul, nem pedig egy adott eszközértékelésen.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A értékelt eredményeket képviselő JSON-objektum. Minden kulcs egy JSON-tömböt tartalmaz az eredményekből.

#### <a name="result-fields"></a>Eredménymezők

**Kulcsok**

**backupServer**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **Forrás** | Sztring | IP-cím |
| **Cél** | Sztring | IP-cím |
| **Port** | Numerikus | - |
| **Közlekedési** | Sztring | TCP vagy UDP |
| **backupMaximalInterval** | Sztring | - |
| **lastSeenBackup** | Numerikus | Epoch (UTC) |

**ipNetworks**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **addresse** s | Numerikus | - |
| **Hálózati** | Sztring | IP-cím |
| **mask** | Sztring | Alhálózati maszk |

**protocolProblems (protokollproblems)**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **Protokoll** | Sztring | - |
| **Címek** | JSON-tömb | IP-címek |
| **Éber** | Sztring | - |
| **reportTime (jelentésidő)** | Numerikus | Epoch (UTC) |

**protocolDataVolumes (protokolladatok)**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| Protokoll | Sztring | - |
| Kötet | Sztring | "volume number MB" |

**kapcsolat bontása**

| Mező neve | Típus | Értékek listája |
|--|--|--|
| **assetAddress (assetAddress)** | Sztring | IP-cím |
| **assetName (eszköznév)** | Sztring | - |
| **lastDetectionTime** | Numerikus | Epoch (UTC) |
| **backToNormalTime** | Numerikus | Epoch (UTC) |     

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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Felhasználói hitelesítő adatok ellenőrzése – /api/external/authentication/validation

Ezzel az API-val ellenőrizheti a Defender for IoT felhasználónevét és jelszavát. Az API-val minden Defender for IoT felhasználói szerepkör használható.

Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

#### <a name="method"></a>Metódus

**POST**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **Felhasználónév** | Sztring | No |
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

Üzenetsring a művelet állapotának részleteivel:

- **Sikeres – msg:** A hitelesítés sikeres volt

- **Hiba – hiba:** A hitelesítő adatok érvényesítése sikertelen volt

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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>Jelszó módosítása – /external/authentication/set_password

Ezzel az API-val a felhasználók megváltoztathatják saját jelszavukat. Az API-val minden Defender for IoT felhasználói szerepkör használható. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

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

Üzenetsring a művelet állapotának részleteivel:

- **Sikeres – msg:** A jelszó le lett cserélve

- **Hiba – hiba:** Felhasználóhitelesítési hiba

- **Hiba – hiba:** A jelszó nem felel meg a biztonsági szabályzatnak

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Eszközmezők

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **Felhasználónév** | Sztring | No |
| **alaphelyzetbe állítása** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": " 1234@abcd ","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Felhasználói jelszó frissítése a rendszergazda által – /external/authentication/set_password_by_admin

Ezzel az API-val a rendszergazdák módosíthatják a megadott felhasználók jelszavát. A Defender for IoT rendszergazdai felhasználói szerepkörei működnek az API-val. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

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

Üzenetsring a művelet állapotának részleteivel:

- **Sikeres – msg:** A jelszó le lett cserélve

- **Hiba – hiba:** Felhasználóhitelesítési hiba

- **Hiba – hiba:** A felhasználó nem létezik

- **Hiba – hiba:** A jelszó nem felel meg a biztonsági szabályzatnak

- **Hiba – hiba:** A felhasználó nem rendelkezik a jelszóváltozáshoz szükséges engedélyekkel

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

#### <a name="device-fields"></a>Eszközmezők

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **admin_username** | Sztring | No |
| **admin_password** | Sztring | No |
| **Felhasználónév** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>A helyszíni felügyeleti konzol API-specifikációi ##

Ez a szakasz a helyszíni felügyeleti konzol API-ját ismerteti a következő hez:
- Riasztáskizárások
- Eszközinformációk
- Riasztási információk

### <a name="alert-exclusions"></a>Riasztáskizárások ###

Határozza meg azokat a feltételeket, amelyek esetén a rendszer nem küld riasztásokat. Meghatározhatja és frissítheti például a leállítási és indítási időket, azokat az eszközöket vagy alhálózatokat, amelyek ki lesznek zárva a riasztások kiváltása során, vagy a Defender for IoT-motorokat, amelyekből ki kell zárni őket. Előfordulhat például, hogy karbantartási időszakban le szeretné állítani az összes riasztás kézbesítését, kivéve a kritikus eszközökön a kártevőkre vonatkozó riasztásokat. Az itt definiált elemek csak olvasható kizárási  szabályokként jelennek meg a helyszíni felügyeleti konzol Riasztáskizárások ablakában.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Példa válaszra**

- **Válasz:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Jelszó módosítása – /external/authentication/set_password 

Ezzel az API-val a felhasználók megváltoztathatják saját jelszavukat. Az API-val minden Defender for IoT felhasználói szerepkör használható. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Felhasználói jelszó frissítése a rendszergazda által – /external/authentication/set_password_by_admin 

Ezzel az API-val a rendszergazdák módosíthatják adott felhasználók jelszavát. A Defender for IoT rendszergazdai felhasználói szerepkörei működnek az API-val. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

### <a name="retrieve-device-information---externalv1devices"></a>Eszközinformációk lekérése – /external/v1/devices ###

Ez az API lekért egy listát a Defender for IoT-érzékelők által észlelt összes eszközről, amely egy helyszíni felügyeleti konzolhoz csatlakozik.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközöket képviselő JSON-objektumok tömbje.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **authorized**: Csak az engedélyezett és a jogosulatlan eszközök szűrésére használható.

- **siteId**: Csak az adott helyekhez kapcsolódó eszközök szűréséhez.

- **zoneId:** Csak az adott zónákhoz kapcsolódó eszközök szűréséhez. [1](#1)

- **sensorId:** Csak az adott érzékelők által észlelt eszközök szűréséhez. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Előfordulhat, hogy nem tudja a hely- és zónaazonosítót. Ebben az esetben minden eszközt le kellkérdezni a hely és a zónaazonosító lekéréséhez.*

#### <a name="query-parameters-example"></a>Példa lekérdezési paraméterekre

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Eszközmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **sensorId (érzékelőazonosító)** | Numerikus | No | - |
| **zoneId (zónaazonosító)** | Numerikus | Yes | - |
| **siteId (helyazonosító)** | Numerikus | Yes | - |
| **ipAddresses** | JSON-tömb | Yes | IP-címek (internetcímek vagy kettős hálózati adatokat használhatja egynél több cím esetén) |
| **name** | Sztring | No | - |
| **Típus** | Sztring | No | Ismeretlen, mérnöki állomás, PLC, HKI, tartományvezérlő, DB-kiszolgáló, vezeték nélküli hozzáférési pont, útválasztó, kapcsoló, kiszolgáló, munkaállomás, IP-kamera, nyomtató, tűzfal, terminálállomás, VPN Gateway, internet vagy csoportos küldés és szórás |
| **macAddresses** | JSON-tömb | Yes | MAC-címek (egynél több cím is lehet, ha egy eszköz kettős NPC-ket használ) |
| **operatingSystem** | Sztring | Yes | - |
| **engineeringStation** | Logikai | No | Igaz vagy hamis |
| **Szkenner** | Logikai | No | Igaz vagy hamis |
| **Engedélyezett** | Logikai | No | Igaz vagy hamis |
| **Szállító** | Sztring | Yes | - |
| **Protokollok** | JSON-tömb | Yes | Protokollobjektum |
| **Firmware** | JSON-tömb | Yes | Belső vezérlőprogram objektuma |

#### <a name="protocol-fields"></a>Protokollmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| Name | Sztring | No | - |
| Címek | JSON-tömb | Yes | Fő vagy numerikus értékek |

#### <a name="firmware-fields"></a>Belső vezérlőprogram mezői

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Sorozat** | Sztring | No | N/A vagy a tényleges érték |
| **Modell** | Sztring | No | N/A vagy a tényleges érték |
| **firmwareVersion** | Dupla | No | N/A vagy a tényleges érték |
| **additionalData (további adatok)** | Sztring | No | N/A vagy a tényleges érték |
| **moduleAddress (modul-cím)** | Sztring | No | N/A vagy a tényleges érték |
| **Rack** | Sztring | No | N/A vagy a tényleges érték |
| **Slot** | Sztring | No | N/A vagy a tényleges érték |
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
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Riasztási adatok lekérése – /external/v1/alerts

Ezzel az API-val lekérheti az összes vagy szűrt riasztást egy helyszíni felügyeleti konzolról.

#### <a name="method"></a>Metódus

**Kap**

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **state**: Csak a kezelt és a nem kezelt riasztások szűréséhez.

  **Például:**

  `/api/v1/alerts?state=handled`

- **fromTime**: Adott időpontból létrehozott riasztások szűrése (ezredmásodpercben, UTC).

  **Például:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** Csak egy adott időpont előtt létrehozott riasztások szűrése (ezredmásodpercben, UTC).

  **Például:**

  `/api/v1/alerts?toTime=<epoch>`

- **siteId:** Az a hely, amelyen a riasztást felfedezték.
- **zoneId:** Az a zóna, amelyen a riasztást felfedezték.
- **sensor:** Az érzékelő, amelyen a riasztást felfedezték.

*Előfordulhat, hogy nem tudja a hely és a zónaazonosítót. Ebben az esetben minden eszközt le kellkérdezni a hely és a zónaazonosító lekéréséhez.*

#### <a name="alert-fields"></a>Riasztásmezők 

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **ID (Azonosító)** | Numerikus | No | - |
| **Idő** | Numerikus | No | Epoch (UTC) |
| **cím** | Sztring | No | - |
| **Üzenetet** | Sztring | No | - |
| **Súlyossága** | Sztring | No | Figyelmeztetés, kisebb, nagyobb vagy kritikus |
| **Motor** | Sztring | No | Protokollsértés, szabályzatsértés, kártevő, anomália vagy működési |
| **sourceDevice** | Numerikus | Yes | Eszközazonosító |
| **destinationDevice (célszámítógép)** | Numerikus | Yes | Eszközazonosító |
| **sourceDeviceAddress (sourceDeviceAddress)** | Numerikus | Yes | IP, MAC, Null |
| **destinationDeviceAddress** | Numerikus | Yes | IP, MAC, Null |
| **szervizelésSteps** | Sztring | Yes | A riasztásban látható javítási lépések|
| **sensorName (érzékelő neve)** | Sztring | Yes | A felhasználó által a konzolon definiált érzékelő neve|
|**zoneName (zónanév)** | Sztring | Yes | Az érzékelőhöz társított zóna neve a konzolon|
| **siteName (helynév)** | Sztring | Yes | Az érzékelőhöz társított hely neve a konzolon |
| **additionalInformation (további információk)** | További információs objektum | Yes | - |

Vegye figyelembe, hogy az /api/v2/ a következő információkhoz szükséges:

- sourceDeviceAddress 
- destinationDeviceAddress
- szervizelésSteps
- sensorName (érzékelő neve)
- zoneName (zónanév)
- siteName (helynév)

#### <a name="additional-information-fields"></a>További információmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Leírás** | Sztring | No | - |
| **Információ** | JSON-tömb | Nem | Sztring |

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
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" <span> 'https:/ /127.0.0.1/external/v1/alerts ?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>QRadar-riasztások

A QRadar és a Defender for IoT integrációjával azonosíthatja a Defender for IoT által generált riasztásokat, és műveleteket hajthatja végre ezekkel a riasztásokkal. A QRadar fogadja az adatokat a Defender for IoT-től, majd kapcsolatba lép a helyszíni felügyeleti konzol nyilvános API-jának összetevőjével.

Ahhoz, hogy a Defender for IoT által felderített adatokat elküldje a QRadarnak, határozzon meg egy továbbítási szabályt a Defender for IoT rendszerben, és válassza a Távoli támogatás **riasztáskezelése** lehetőséget.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Igény szerint szerkessze a továbbítási szabályokat.":::

Ha ezt a beállítást választja a továbbítási szabályok konfigurálása során, a következő további mezők jelennek meg a QRadarban:

- **UUID:** Egyedi riasztásazonosító, például 1-1555245116250.

- **Hely:** Az a hely, ahol a riasztást felfedezték.

- **Zóna:** Az a zóna, ahol a riasztást felfedezték.

Példa a QRadarnak küldött hasznos tartalomra:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metódus

**PUT**

#### <a name="request-type"></a>Kérelemtípus

**JSON**

#### <a name="request-content"></a>Tartalom kérése

JSON-objektum, amely az UUID-t tartalmazó riasztáson végrehajtani kívánt műveletet jelöli.

#### <a name="action-fields"></a>Műveletmezők

| Név | Típus | Nullázható | Értékek listája |
|--|--|--|--|
| **Akció** | Sztring | No | handle or handleAndLearn |

#### <a name="request-example"></a>Példa kérésre

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

Az eszközöket képviselő JSON-objektumok tömbje.

#### <a name="response-fields"></a>Válaszmezők


| Név | Típus | Nullázható | Description |
|--|--|--|--|
| **content/error (tartalom/hiba)** | Sztring | No | Ha a kérés sikeres, megjelenik a content (tartalom) tulajdonság. Ellenkező esetben a hiba tulajdonság jelenik meg. |

#### <a name="possible-content-values"></a>Lehetséges tartalomértékek

| Állapotkód | Tartalom értéke | Description |
|--|--|--|
| 200 | A riasztásfrissítési kérés sikeresen befejeződött. | A frissítési kérés sikeresen befejeződött. Nincsenek megjegyzések. |
| 200 | A riasztást már kezelték (**leíró).** | A riasztást már akkor kezelték, amikor a riasztáshoz leíró kérés érkezett.<br />A riasztás kezelése **megmarad.** |
| 200 | A riasztást már kezelték és megtanulták (**handleAndLearn**). | A riasztást már kezelték, és megtudta, hogy mikor érkezett kérés **azAndLearn kezeléshez.**<br />A riasztás a **handledAndLearn állapotú** marad. |
| 200 | A riasztást már kezelték (**kezelve).**<br />A handle és a learn (**handleAndLearn**) a riasztáson lett hajtva végre. | A riasztást már kezelték, amikor kérés érkezett **azAndLearn** kezeléshez.<br />A riasztás a **következőt kezeli:AndLearn**. |
| 200 | A riasztást már kezelték és megtanulták (**handleAndLearn**). Figyelmen kívül hagyja a leíró kérést. | A riasztást már **korábban is kezeltékAndLearn,** amikor a riasztást kezelő kérés érkezett. A riasztás a **következőt kezeli:AndLearn**. |
| 500 | Érvénytelen művelet. | Az elküldött művelet nem érvényes művelet a riasztáson. |
| 500 | Váratlan hiba történt. | Váratlan hiba történt. A probléma megoldásához lépjen kapcsolatba a műszaki támogatási szolgálattal. |
| 500 | Nem sikerült végrehajtani a kérelmet, mert nem található riasztás ehhez az UUID-hez. | A megadott riasztás UUID-je nem található a rendszerben. |

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
| PUT | curl -k -X PUT -d '{"action": " <ACTION> "}" -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}" -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Riasztáskizárások (karbantartási időszak) – /external/v1/maintenanceWindow

Határozza meg azokat a feltételeket, amelyek esetén a rendszer nem küld riasztásokat. Meghatározhatja és frissítheti például a leállítási és indítási időket, azokat az eszközöket vagy alhálózatokat, amelyek ki lesznek zárva a riasztások kiváltása során, vagy a Defender for IoT-motorokat, amelyekből ki kell zárni őket. Előfordulhat például, hogy karbantartási időszakban le szeretné állítani az összes riasztás kézbesítését, kivéve a kritikus eszközökön a kártevőkre vonatkozó riasztásokat.

Az itt definiált API-k csak olvasható kizárási  szabályként jelennek meg a helyszíni felügyeleti konzol Riasztási kizárások ablakában.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A Riasztási kizárások ablak, amely az összes kizárási szabályt tartalmazza. ":::

#### <a name="method---post"></a>Metódus – POST

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **ticketId:** Meghatározza a karbantartási jegy azonosítóját a felhasználó rendszerében.

- **ttl**: Meghatározza az TTL-t (az időtartam idejét), amely a karbantartási időszak percekben meghatározott időtartama. A paraméter által megadott időtartam után a rendszer automatikusan elkezd riasztásokat küldeni.

- **engines**: Meghatározza, hogy melyik biztonsági motor tiltsa le a riasztásokat a karbantartási folyamat során:

   - Anomália

   - Malware

   - Működési

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds:** Meghatározza, hogy melyik Defender for IoT-érzékelő tiltsa le a riasztásokat a karbantartási folyamat során. Ez megegyezik az /api/v1/appliances (GET) parancsból lekért azonosítóval.

- **subnets**: Meghatározza, hogy melyik alhálózatról tiltsa le a riasztásokat a karbantartási folyamat során. Az alhálózat a következő formátumban lesz elküldve: 192.168.0.0/16.

#### <a name="error-codes"></a>Hibakódok

- **201 (Létrehozva)**: A művelet sikeresen befejeződött.

- **400 (Hibás kérés):** A következő esetekben jelenik meg:

   - A **ttl** paraméter nem numerikus vagy nem pozitív.

   - Az **alhálózatok paraméter** nem megfelelő formátumban lett definiálva.

   - A **ticketId paraméter** hiányzik.

   - A  motorparaméter nem egyezik a meglévő biztonsági motorokkal.

- **404 (Nem található):** Az egyik érzékelő nem létezik.

- **409 (Ütközés):** A jegyazonosító egy másik nyitott karbantartási időszakhoz van csatolva.

- **500 (Belső kiszolgálóhiba):** Bármilyen egyéb váratlan hiba.

> [!NOTE]
> Győződjön meg arról, hogy a jegyazonosító nincs meglévő nyitott ablakhoz kapcsolva. A következő kizárási szabály jön létre: Maintenance-{jogkivonat neve}-{jegyazonosító}.

#### <a name="method---put"></a>Metódus – PUT

Lehetővé teszi a karbantartási időszak frissítését a karbantartási folyamat elkezdését követően az **ttl paraméter módosításával.** Az új időtartam definíciója felülírja az előzőt.

Ez a módszer akkor hasznos, ha a jelenleg konfigurált időtartamnál hosszabb időtartamot szeretne beállítani.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **ticketId:** Meghatározza a karbantartási jegy azonosítóját a felhasználó rendszerében.

- **ttl:** Az ablak időtartamát határozza meg percben.

#### <a name="error-code"></a>Hibakód

- **200 (OK)**: A művelet sikeresen befejeződött.

- **400 (Hibás kérés):** A következő esetekben jelenik meg:

   - A **ttl** paraméter nem numerikus vagy nem pozitív.

   - A **ticketId** paraméter hiányzik.

   - A **ttl paraméter** hiányzik.

- **404 (Nem található):** A jegyazonosító nincs nyitott karbantartási időszakhoz csatolva.

- **500 (Belső kiszolgálóhiba):** Bármilyen egyéb váratlan hiba.

> [!NOTE]
> Győződjön meg arról, hogy a jegyazonosító egy meglévő nyitott ablakhoz van csatolva.

#### <a name="method---delete"></a>Metódus – DELETE

Bezár egy meglévő karbantartási ablakot.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **ticketId:** A karbantartási jegy azonosítóját naplózza a felhasználó rendszerében.

#### <a name="error-code"></a>Hibakód

- **200 (OK)**: A művelet sikeresen befejeződött.

- **400 (Hibás kérés):** A **ticketId** paraméter hiányzik.

- **404 (Nem található):** A jegyazonosító nincs nyitott karbantartási időszakhoz csatolva.

- **500 (Belső kiszolgálóhiba):** Bármilyen egyéb váratlan hiba.

> [!NOTE]
> Győződjön meg arról, hogy a jegyazonosító egy meglévő nyitott ablakhoz van csatolva.

#### <a name="method---get"></a>Metódus – GET

Lekéri a rendszerben a karbantartás során végrehajtott összes nyitott, bezárási és frissítési művelet naplóját. Csak a múltban aktív és lezárt karbantartási időszakokkal kapcsolatos naplót lehet lekérni.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

- **fromDate:** Szűri a naplókat az előre meghatározott dátumból és később. A formátum 2019-12-30.

- **toDate:** Szűri a naplókat az előre meghatározott dátumig. A formátum 2019-12-30.

- **ticketId:** Szűri az adott jegyazonosítóhoz kapcsolódó naplókat.

- **tokenName:** Szűri az adott jogkivonat nevéhez kapcsolódó naplókat.

#### <a name="error-code"></a>Hibakód 

- **200 (OK)**: A művelet sikeresen befejeződött.

- **400 (Hibás kérés):** A dátumformátum helytelen.

- **204 (Nincs tartalom):** Nincsenek megmutatja adatok.

- **500 (Belső kiszolgálóhiba):** Bármilyen egyéb váratlan hiba.

#### <a name="response-type"></a>Válasz típusa

**JSON**

#### <a name="response-content"></a>Válasz tartalma

A karbantartási időszak műveleteit képviselő JSON-objektumok tömbje.

#### <a name="response-structure"></a>Válaszstruktúra

| Név | Típus | Megjegyzés | Nullázható |
|--|--|--|--|
| **Datetime** | Sztring | Például: "2012-04-23T18:25:43.511Z" | nem |
| **jegyazonosító** | Sztring | Például: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nem |
| **tokenName** | Sztring | - | nem |
| **Motorok** | A sztring tömbje | - | igen |
| **sensorIds (érzékelőazonosítók)** | A sztring tömbje | - | igen |
| **Alhálózatok** | A sztring tömbje | - | igen |
| **ttl** | Numerikus | - | igen |
| **operationType (művelet típusa)** | Sztring | Az értékek az "OPEN", az "UPDATE" és a "CLOSE" | nem |

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2... HUNINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2... SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","3 "],"subnets": ["10.0.0.3"]}" -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}" -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}" -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}" -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcdcabcd" 'https:/ <span> /127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-0 1-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Felhasználói hitelesítő adatok hitelesítése – /external/authentication/validation

Ezzel az API-val érvényesítheti a felhasználói hitelesítő adatokat. Az API-val minden Defender for IoT felhasználói szerepkör használható. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

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

Üzenetsring a művelet állapotának részleteivel:

- **Sikeres – msg:** A hitelesítés sikeres volt

- **Hiba – hiba:** A hitelesítő adatok érvényesítése sikertelen volt

#### <a name="device-fields"></a>Eszközmezők

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **Felhasználónév** | Sztring | No |
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
| POST | curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":" 1234@abcd "}" 'https:/ <span> /127.0.0.1/external/authentication/validation' |

### <a name="change-password---externalauthenticationset_password"></a>Jelszó módosítása – /external/authentication/set_password

Ezzel az API-val a felhasználók megváltoztathatják saját jelszavukat. Az API-val minden Defender for IoT felhasználói szerepkör használható. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

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

Üzenetsring a művelet állapotának részleteivel:

- **Sikeres – msg:** A jelszó le lett cserélve

- **Hiba – hiba:** Felhasználóhitelesítési hiba

- **Hiba – hiba:** A jelszó nem felel meg a biztonsági szabályzatnak

#### <a name="response-example"></a>Példa válaszra

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Eszközmezők

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **Felhasználónév** | Sztring | No |
| **alaphelyzetbe állítása** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

| Típus | API-k | Példa |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password | curl -k -d '{"username": "myUser","password": " 1234@abcd ","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Felhasználói jelszó frissítése a rendszergazda által – /external/authentication/set_password_by_admin

Ezzel az API-val a rendszergazdák módosíthatják a megadott felhasználók jelszavát. A Defender for IoT rendszergazdai felhasználói szerepkörei működnek az API-val. Az API használatához nincs szükség Defender for IoT hozzáférési jogkivonatra.

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

Üzenetsring a művelet állapotának részleteivel:

- **Sikeres – msg:** A jelszó le lett cserélve

- **Hiba – hiba:** Felhasználóhitelesítési hiba

- **Hiba – hiba:** A felhasználó nem létezik

- **Hiba – hiba:** A jelszó nem felel meg a biztonsági szabályzatnak

- **Hiba – hiba:** A felhasználó nem rendelkezik a jelszóváltozáshoz szükséges engedélyekkel

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

#### <a name="device-fields"></a>Eszközmezők

| **Név** | **Típus** | **Nullázható** |
|--|--|--|
| **admin_username** | Sztring | No |
| **admin_password** | Sztring | No |
| **Felhasználónév** | Sztring | No |
| **new_password** | Sztring | No |

#### <a name="curl-command"></a>Curl parancs

> [!div class="mx-tdBreakAll"]
> | Típus | API-k | Példa |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>Következő lépések

- [Egy eszközkészlet érzékelői általi észlelések vizsgálata](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Egy eszközkészlet összes vállalati érzékelője általi észlelések vizsgálata](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
