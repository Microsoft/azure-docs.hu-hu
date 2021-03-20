---
title: Érzékelői partner integrációja
description: Ez a cikk az érzékelő partner integrációját ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179748"
---
# <a name="sensor-partner-integration"></a>Érzékelői partner integrációja

Ez a cikk az érzékelő partner-integrációt lehetővé tevő Azure FarmBeats **Translator** összetevővel kapcsolatos információkat tartalmaz.

Ennek az összetevőnek a használatával a partnerek a FarmBeats Datahub API-k segítségével integrálhatók a FarmBeats, és az ügyfél-eszköz adatai és a telemetria FarmBeats Datahub. Ha az adatok elérhetők a FarmBeats-ben, a rendszer a FarmBeats-gyorsító használatával, valamint a gépi tanulási/mesterséges intelligencia-modellek létrehozásához használható.

## <a name="before-you-start"></a>Előkészületek

A Translator összetevő fejlesztéséhez a következő hitelesítő adatokra lesz szüksége, amelyek engedélyezik a hozzáférést a FarmBeats API-khoz.

- API-végpont
- Bérlőazonosító
- Ügyfél-azonosító
- Titkos ügyfélkulcs
- EventHub-kapcsolatok karakterlánca

Tekintse meg ezt a szakaszt a fenti hitelesítő adatok beszerzéséhez: az [eszközök integrálásának engedélyezése](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Fordítói fejlesztés

**REST API-alapú integráció**

A FarmBeats érzékelő-adatintegrációs képességei elérhetők a REST APIon keresztül. A képességek közé tartozik a metaadatok definíciója, az eszközök és az érzékelők üzembe helyezése, valamint az eszközök és az érzékelők kezelése.

**Telemetria betöltése**

A telemetria-adatainak feldolgozásra az Azure Event Hubs közzétett kanonikus üzenetre van leképezve. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból.

**API-fejlesztés**

Az API-k a hencegés műszaki dokumentációját tartalmazzák. További információ az API-król és a hozzájuk kapcsolódó kérésekről vagy válaszokról: [hencegés](https://aka.ms/FarmBeatsSwagger).

**Hitelesítés**

A FarmBeats Microsoft Azure Active Directory hitelesítést használ.A Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít.

További információ: [Azure Active Directory](../../app-service/overview-authentication-authorization.md).

A FarmBeats Datahub tulajdonosi hitelesítést használ, amelynek a következő hitelesítő adatokra van szüksége:
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - Bérlőazonosító

Ezen hitelesítő adatok használatával a hívó hozzáférési jogkivonatot kérhet. A tokent a következő API-kérelmekben kell elküldeni a fejléc szakaszban a következő módon:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A következő Python-kód megadja a hozzáférési jogkivonatot, amely a következő API-hívásokhoz használható a FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**HTTP-kérelmek fejlécei**

Itt láthatja a leggyakoribb kérelmek fejléceit, amelyeket meg kell adni, amikor API-hívást végez a FarmBeats Datahub.


**Fejléc** | **Leírás és példa**
--- | ---
Content-Type | A kérelem formátuma (Content-Type: Application/ <format> ). A FarmBeats Datahub API-k formátuma a JSON. Content-Type: Application/JSON
Engedélyezés | Meghatározza az API-hívások létrehozásához szükséges hozzáférési jogkivonatot. Engedélyezés: tulajdonos <Access-Token>
Elfogadás | A válasz formátuma. A FarmBeats Datahub API-k formátuma a JSON. Elfogadás: alkalmazás/JSON

**API-kérelmek**

REST API kérelem elvégzéséhez kombinálja a HTTP-(GET, POST, vagy PUT) metódust, az API szolgáltatás URL-címét, a Uniform Resource Identifier (URI) egy erőforráshoz a lekérdezéshez, az adatküldés frissítéséhez vagy törléséhez, valamint egy vagy több HTTP-kérelem fejlécéhez. Az API-szolgáltatás URL-címe az Ön által megadott API-végpont. Példa: https:// \<yourdatahub-website-name> . azurewebsites.net

Igény szerint a lekérdezési paramétereket is megadhatja a szűréshez, korlátozhatja az adatok méretét, és rendezheti a válaszokat.

Az alábbi példa az eszközök listájának beszerzésére szolgál.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A legtöbb GET, POST és PUT híváshoz JSON-kérést tartalmazó törzs szükséges.

Az alábbi példa egy eszköz létrehozását kéri. (Ez a minta egy bemeneti JSON-t tartalmaz a kérés Törzsével.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Adatformátum

A JSON egy közös nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Metaadatok specifikációi

A FarmBeats Datahub a következő API-kkal rendelkezik, amelyek lehetővé teszik az eszközök partnerei számára az eszköz vagy az érzékelő metaadatainak létrehozását és felügyeletét.

- /**DeviceModel**: a DeviceModel megfelel az eszköz metaadatainak, például a gyártónak és az eszköz típusának, amely átjáró vagy csomópont.
- /**Eszköz**: az eszköz a farmon található fizikai eszköznek felel meg.
- /**SensorModel**: a SensorModel megfelel az érzékelő metaadatainak, például a gyártó, az érzékelő típusa, amely az analóg vagy a digitális, valamint az érzékelő mértékét, például a környezeti hőmérsékletet és a nyomást.
- /**Érzékelő**: az érzékelő olyan fizikai érzékelőnek felel meg, amely értékeket rögzít. Az érzékelő általában eszköz-AZONOSÍTÓval van csatlakoztatva egy eszközhöz.

  DeviceModel | Description |
  --- | ---
  Típus (csomópont, átjáró)  | Az eszköz – csomópont vagy átjáró típusa |
  Gyártó  | A gyártó neve |
  ProductCode  | Eszköz Termékkód vagy modell neve vagy száma. Például: EnviroMonitor # 6800. |
  Portok  | A port neve és típusa, amely digitális vagy analóg.  |
  Name  | Az erőforrást azonosító név. Például a modell neve vagy a terméknév. |
  Description  | Adjon meg egy értelmes leírást a modellről. |
  Tulajdonságok  | További tulajdonságok a gyártótól. |
  **Eszköz** | **Leírás** |
  DeviceModelId  |A társított eszköz-modell azonosítója. |
  HardwareId   |Az eszköz egyedi azonosítója, például egy MAC-címe.  |
  ReportingInterval |Jelentéskészítési időköz (másodperc). |
  Hely    |Az eszköz földrajzi szélessége (-90 és + 90), a hosszúság (-180 – 180) és a Jogosultságszint-emelés (méterben). |
  ParentDeviceId | Annak a fölérendelt eszköznek az azonosítója, amelyhez az eszköz csatlakoztatva van. Ha például egy csomópont átjáróhoz csatlakozik, a csomópont parentDeviceID van. |
  Name  | Az erőforrást azonosító név. Az eszköz partnereinek olyan nevet kell küldeniük, amely konzisztens az eszköz nevével az erőforráspartner oldalán. Ha az eszköz neve felhasználó által definiálva van az eszköz partner oldalán, a felhasználó által definiált nevet is propagálni kell a FarmBeats.  |
  Description  | Adjon meg egy értelmes leírást.  |
  Tulajdonságok  |További tulajdonságok a gyártótól.  |
  **SensorModel** | **Leírás** |
  Típus (analóg, digitális)  |Említi az analóg vagy a digitális érzékelőt.|
  Gyártó  | A gyártó neve. |
  ProductCode  | Termékkód vagy modell neve vagy száma. Például RS-CO2-N01.  |
  SensorMeasures > neve  | Az érzékelő mértékének neve. Csak kisbetűk támogatottak. A különböző mélységű mérések esetében határozza meg a mélységet. Például soil_moisture_15cm. Ennek a névnek konzisztensnek kell lennie a telemetria-adattal. |
  SensorMeasures > adattípus  | Az telemetria adattípusa. Jelenleg a Double támogatott. |
  SensorMeasures > típusa  | Az érzékelő telemetria-adattípusának mértékegysége A rendszer által meghatározott típusok a következők: AmbientTemperature, CO2, mélység, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrát, O2, PH, foszfát, PointInTime, kálium, nyomás, RainGauge, RelativeHumidity, sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, szélsebesség, párolgás, PAR. További részletekért tekintse meg a/ExtendedType API-t.
  SensorMeasures > egység | Az érzékelő telemetria-adategysége. A rendszer által definiált egységek a következők: nincs egység, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, milliméter, centiméter, méter, hüvelyk, láb, Mile, kilométer, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, másodperc, UnixTimestamp, MicroMolPerMeterSquaredPerSecond és InchesPerHour. További részletekért tekintse meg a/ExtendedType API-t.
  SensorMeasures > AggregationType  | Egyik sem, átlagos, maximális, minimum vagy StandardDeviation.
  SensorMeasures > mélység  | Az érzékelő mélysége centiméterben. Például a nedvesség 10 cm-es méretének mérése a terepen.
  SensorMeasures > leírása  | Adjon meg egy értelmes leírást a mérésről.
  Name  | Az erőforrást azonosító név. Például a modell neve vagy a termék neve.
  Description  | Adjon meg egy értelmes leírást a modellről.
  Tulajdonságok  | További tulajdonságok a gyártótól.
  **Érzékelő**  | **Leírás** |
  HardwareId  | A gyártó által beállított érzékelő egyedi azonosítója.
  SensorModelId  | A társított érzékelő modell azonosítója.
  Hely  | Érzékelő szélesség (-90 és + 90), hosszúság (-180 – 180) és Jogosultságszint-emelés (méterben).
  Port > neve  |Annak a portnak a neve és típusa, amelyhez az érzékelő csatlakozik az eszközhöz. Ennek a névnek meg kell egyeznie az eszköz modelljében megadott névvel.
  DeviceId  | Annak az eszköznek az azonosítója, amelyhez az érzékelő csatlakozik.
  Name  | Az erőforrást azonosító név. Például az érzékelő neve, a terméknév és a modell száma vagy a termékkód.
  Description  | Adjon meg egy értelmes leírást.
  Tulajdonságok  | További tulajdonságok a gyártótól.

 Az egyes objektumokra és azok tulajdonságaira vonatkozó információkért lásd: [hencegés](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Ezt az azonosítót a fordítónak meg kell őriznie az eszközkezelés és a metaadatok szinkronizálása érdekében.


**Metaadatok szinkronizálása**

A fordítónak frissítéseket kell küldenie a metaadatokon. A frissítési forgatókönyvek például az eszköz vagy az érzékelő nevének módosítása, valamint az eszköz vagy az érzékelő helyének módosítása.

A fordítónak képesnek kell lennie olyan új eszközök vagy érzékelők hozzáadására, amelyeket a FarmBeats felhasználó utáni csatolása telepített. Hasonlóképpen, ha egy eszközt vagy érzékelőt a felhasználó frissített, ugyanezt a megfelelő eszköz vagy érzékelő FarmBeats kell frissíteni. Az eszköz vagy az érzékelő frissítését igénylő jellemző forgatókönyvek az eszköz helyének változása vagy egy csomóponton lévő érzékelők hozzáadása.


> [!NOTE]
> Az eszköz vagy az érzékelő metaadatai nem támogatják a törlést.
>
> A metaadatok frissítéséhez meg kell hívnia az/Get/{ID} az eszközön vagy az érzékelőn, frissítenie kell a módosított tulajdonságokat, majd el kell végeznie egy/Put/{ID}, hogy a felhasználó által megadott tulajdonságok ne vesszenek el.

### <a name="add-new-types-and-units"></a>Új típusok és egységek hozzáadása

A FarmBeats támogatja az új szenzor mértékének típusát és egységeit. További információ a/ExtendedType API-ról: [hencegés](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Telemetria-specifikációk

A telemetria-adatainak feldolgozásra az Azure Event Hubs közzétett kanonikus üzenetre van leképezve. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetria-FarmBeats küldése

Ha telemetria-adatokat szeretne küldeni a FarmBeats, hozzon létre egy ügyfelet, amely üzeneteket küld egy FarmBeats lévő Event hubhoz. További információ a telemetria-adatokról: [telemetria küldése az Event hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)-ba.

Itt található egy Python-kód, amely a telemetria ügyfélként küldi el a megadott Event hub-nak.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

A Canonical üzenet formátuma a következő:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
A telemetria JSON-ban szereplő összes kulcsnévnek kisbetűsnek kell lennie. Ilyenek például az DeviceID és a sensordata.

Például itt van egy telemetria-üzenet:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> A következő fejezetek más változásokhoz kapcsolódnak (például Felhasználói felület, hibakezelés stb.) az érzékelő partnere a fordítói összetevő fejlesztésében is hivatkozhat.


## <a name="link-a-farmbeats-account"></a>FarmBeats-fiók csatolása

Miután az ügyfelek megvásárolták és telepítették az eszközöket vagy érzékelőket, hozzáférhetnek az eszköz-és telemetria az eszköz-partnerek szolgáltatott szoftver (SaaS) portálon. Az eszköz partnerei lehetővé tehetik, hogy az ügyfelek az Azure-beli FarmBeats-példánnyal összekapcsolják a fiókját, hogy megadják a következő hitelesítő adatok bevitelének módját:

   - Megjelenítendő név (egy választható mező, amellyel a felhasználók megadhatják az integráció nevét)
   - API-végpont
   - Bérlőazonosító
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - EventHub-kapcsolatok karakterlánca
   - Kezdő dátum

   > [!NOTE]
   > A kezdő dátum lehetővé teszi, hogy a korábbi adatcsatorna, azaz a felhasználó által megadott dátummal rendelkező adatok legyenek.

## <a name="unlink-farmbeats"></a>FarmBeats leválasztása

Az eszközök partnerei lehetővé teszik az ügyfelek számára egy meglévő FarmBeats-integráció leválasztását. A FarmBeats leválasztása nem törli a FarmBeats Datahub-ben létrehozott összes eszközt vagy érzékelő metaadatot. A Leválasztás a következő műveleteket végzi el:

   - Leállítja a telemetria folyamatot.
   - Törli és törli az integrációs hitelesítő adatokat az eszköz partnerén.

## <a name="edit-farmbeats-integration"></a>FarmBeats-integráció szerkesztése

Az eszközök partnerei lehetővé teszik az ügyfeleknek, hogy szerkesszék a FarmBeats integrációs beállításait, ha az ügyfél titka vagy a kapcsolódási karakterlánc megváltozik. Ebben az esetben csak a következő mezők szerkeszthetők:

   - Megjelenítendő név (ha van ilyen)
   - Az ügyfél titka (a "2x8 * * * * * * * * * * * * *" vagy a "
   - Kapcsolati karakterlánc (a "2x8 * * * * * * * * * * * * * *"

## <a name="view-the-last-telemetry-sent"></a>Az utoljára eljuttatott telemetria megtekintése

Az eszközök partnerei lehetővé teszik az ügyfeleknek, hogy megtekintsék az elindított utolsó telemetria időbélyegét, amely a **telemetria elküldése** alatt található. Ez az az idő, amikor a legújabb telemetria sikeresen elküldtük a FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Hibaelhárítás és hibák kezelése

**A beállítások vagy a támogatás hibáinak megoldása**

Ha az ügyfél nem tudja fogadni az eszköz vagy a telemetria a megadott FarmBeats-példányban, az eszköz partnerének támogatást és egy hibaelhárítási mechanizmust kell biztosítania.

**Telemetria adatok megőrzése**

A telemetria-információkat egy előre meghatározott időszakra vonatkozóan is meg kell őrizni, hogy a hiba vagy adatvesztés esetén hasznos legyen a telemetria hibakeresése vagy újraküldése.

**Hiba kezelése vagy hiba miatti értesítés**

Ha egy hiba hatással van az eszköz vagy az érzékelő metaadataira, illetve az adatintegrációs vagy telemetria-adatfolyamra az eszköz-partneri rendszeren, az ügyfélnek értesítést kell kapnia. A hibák megoldására szolgáló mechanizmust is meg kell tervezni és megvalósítani.

**A kapcsolatok ellenőrzőlistája**

Az eszközök gyártói vagy partnerei az alábbi ellenőrzőlista segítségével ellenőrizhetik, hogy az ügyfél által biztosított hitelesítő adatok pontosak-e:

   - Ellenőrizze, hogy kapott-e hozzáférési jogkivonatot a megadott hitelesítő adatokkal.
   - Ellenőrizze, hogy az API-hívás sikeres-e a kapott hozzáférési jogkivonattal.
   - Ellenőrizze, hogy létrejött-e a EventHub-ügyfélkapcsolat.

## <a name="next-steps"></a>Következő lépések

További információ a REST APIről: [REST API](rest-api-in-azure-farmbeats.md).