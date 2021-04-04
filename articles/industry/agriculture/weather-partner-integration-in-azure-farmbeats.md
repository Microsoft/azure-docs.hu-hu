---
title: Időjárási partner integrációja
description: Ismerje meg, hogyan integrálható az időjárási adatszolgáltató a FarmBeats-szel.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147079"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Időjárási partner-integráció a FarmBeats

Ez a cikk az Azure FarmBeats-összekötő Docker-összetevőjével kapcsolatos információkat tartalmaz. Időjárási adatszolgáltatóként használhatja az összekötő Docker-t a FarmBeats-nal való integráláshoz. Az API-k használatával időjárási FarmBeats küldhet. A FarmBeats-ben az adatok adatfúzióhoz, valamint gépi tanulási modellek vagy mesterséges intelligencia modellek létrehozásához használhatók.

 > [!NOTE]
 > Ebben a cikkben egy olyan [hivatkozási implementációt](https://github.com/azurefarmbeats/noaa_docker) használunk, amely az Azure Open-adatkészletek és az országos óceáni és légköri adminisztráció (NOAA) időjárási adatai segítségével készült. A megfelelő [Docker-rendszerképet](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)is használjuk.

Meg kell adnia egy [megfelelő Docker-rendszerképet vagy programot](#docker-specifications) , és a Docker-rendszerképet egy olyan tároló-beállításjegyzékben kell megadnia, amelyet az ügyfelek elérnek. Adja meg a következő információkat az ügyfeleknek:

- Docker-rendszerkép URL-címe
- Docker-rendszerkép címkéje
- A Docker-rendszerkép eléréséhez szükséges kulcsok vagy hitelesítő adatok
- Az ügyfél-specifikus API-kulcsok vagy a rendszerből származó adatok eléréséhez szükséges hitelesítő adatok
- Virtuális gép SKU-adatai (adja meg ezeket az adatokat, ha a Docker-rendszerkép meghatározott virtuálisgép-követelményekkel rendelkezik. Ellenkező esetben az ügyfelek választhatnak az Azure-ban támogatott virtuálisgép-SKU-ket is.)

Az ügyfelek ezt a Docker-információt használják egy időjárási partner FarmBeats-példányban való regisztrálásához. További információ arról, hogy az ügyfelek hogyan használhatják a Docker-t az időjárási adatok FarmBeats történő betöltéséhez: [adatok beolvasása az időjárási partnerektől](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Összekötő Docker-fejlesztés

**REST API-alapú integráció**

A FarmBeats API-k hencegő technikai dokumentációt tartalmaznak. További információ az API-król és a hozzájuk tartozó kérésekről vagy válaszokról: [FarmBeats hencegés](https://aka.ms/farmbeatsswagger). 

Ha már telepítette a FarmBeats-t, a FarmBeats a következő helyen érheti el: `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Figyelje meg, hogy az *-API* a FarmBeats-webhely nevéhez van hozzáfűzve. Az API-végpont `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

A FarmBeats olyan lib-t biztosít, amelyet használhat. A lib jelenleg a [hivatkozás implementációjának részeként](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)érhető el. Később több nyelvhez is elérhető SDK lesz.

### <a name="authentication"></a>Hitelesítés

**Hitelesítés FarmBeats API-kkal**

A FarmBeats tulajdonosi hitelesítést használ. Az API-kat a kérelem fejléc szakaszában található hozzáférési jogkivonat megadásával érheti el. Bemutatunk egy példát:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

A hozzáférési tokent az ügyfél FarmBeats-példányán futó Azure Functions alkalmazásból kérheti le. A Azure Functions URL-címet argumentumként a Docker program kapja meg. A hozzáférési tokent az `GET` URL-címre vonatkozó kérelem elküldésével érheti el. Az URL-cím válasza tartalmazza a hozzáférési jogkivonatot. 

A hozzáférési token beszerzéséhez használja a Datahub lib segítő függvényeit. További információ: a [NOAA Docker-rendszerkép GitHub-lapja](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

A hozzáférési jogkivonat csak néhány órára érvényes. Ha lejár, újra meg kell kérnie.

**Hitelesítés a partner oldali API-kkal**

Ha a Docker-feladatot futtató partneri API-kkal szeretne hitelesítést végezni, az ügyfeleknek meg kell adniuk a hitelesítő adatokat a partner regisztrálása során. Bemutatunk egy példát:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Az API szolgáltatás szerializálja ezt a dict, és egy [kulcstartóban](../../key-vault/general/basic-concepts.md)tárolja.

A [Azure Data Factory](../../data-factory/introduction.md) az időjárási feladatok előkészítésére szolgál. Felgyorsítja az erőforrásokat a Docker-kód futtatásához. A Data Factory az adatok biztonságos leküldését is lehetővé teszi a virtuális gépre, amelyen a Docker-feladatok futnak. Az API hitelesítő adatait a Key Vault biztonságosan tárolja. 

A hitelesítő adatok biztonságos sztringként lesznek beolvasva a Key vaultból. A Docker-tároló munkakönyvtárában kiterjesztett tulajdonságokként vannak megadva. A fájl elérési útja */mnt/working_dir/activity.js*. 

A Docker-kód a Futtatás ideje alatt beolvashatja a *activity.jsról a* hitelesítő adatokat az ügyfélhez tartozó partneri API-k eléréséhez. A JSON-fájlban a hitelesítő adatok a következőhöz hasonlóan jelennek meg:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
A `partnerCredentials` hitelesítő adat abban az értelemben érhető el, ahogyan az ügyfél a partner regisztrálása során biztosította.

A FarmBeats lib segítő függvényeket biztosít. Ezekkel a függvényekkel a tevékenység tulajdonságaiból olvashatja a hitelesítő adatokat. További információ: a [NOAA Docker-rendszerkép GitHub-lapja](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

A fájl csak akkor használható, ha a Docker-kód fut. A kód befejeződése után a rendszer törli a fájlt.

További információ a Data Factory folyamatok és tevékenységek működéséről: [séma-és adattípus-leképezés](../../data-factory/copy-activity-schema-and-type-mapping.md).

**HTTP-kérelmek fejlécei**

A következő táblázat a leggyakoribb kérelmek fejléceit mutatja be, amelyeket akkor kell megadnia, amikor API-hívást hajt végre a FarmBeats.

Fejléc | Leírás és példa
--- | ---
Content-Type | A kérelem formátuma. Például: `Content-Type: application/<format>` <br/>A FarmBeats Datahub API-k formátuma a JSON. Például: ` Content-Type: application/json`
Engedélyezés | Az API-hívások létrehozásához szükséges hozzáférési jogkivonat. Például: `Authorization: Bearer <Access-Token>`
Elfogadás | A válasz formátuma. A FarmBeats Datahub API-k formátuma a JSON. Például: `Accept: application/json`

## <a name="data-format"></a>Adatformátum

A JSON egy közös nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Docker-specifikációk

A Docker programnak két összetevőt kell tartalmaznia: a rendszerindítást és a feladatot. A programnak több feladata is lehet.

### <a name="bootstrap"></a>Bootstrap

A rendszerindítási összetevőnek akkor kell futnia, amikor az ügyfél elindítja a Docker-regisztrációt a FarmBeats. A program a következő argumentumokat ( `arg1` és `arg2` ) fogadja el:

- **FARMBEATS API-végpont**: az API-kérelmek FarmBeats API-végpontja. Ez a végpont API-hívásokat kezdeményez a FarmBeats üzemelő példányához.
- **Azure functions URL-cím**: saját végpont. Ez az URL-cím biztosítja a FarmBeats API-k hozzáférési jogkivonatát. Az URL-cím meghívásával lekérheti `GET` a hozzáférési tokent.

A bootstrap létrehozza azokat a metaadatokat, amelyeket a felhasználóknak az időjárási adatok beolvasásához el kell végezniük a feladatok futtatásához. További információ: a [hivatkozás implementációja](https://github.com/azurefarmbeats/noaa_docker). 

Ha testreszabja a *bootstrap_manifest.jsa* fájlban, akkor a hivatkozás rendszerindítási program létrehozza a szükséges metaadatokat. A bootstrap program a következő metaadatokat hozza létre: 

 > [!NOTE]
 > Ha a *bootstrap_manifest.jsa* fájlon frissíti a [hivatkozás implementációja](https://github.com/azurefarmbeats/noaa_docker) című témakörben leírtak szerint, nem kell létrehoznia a következő metaadatokat. A rendszerindítási program a jegyzékfájlt fogja használni a szükséges metaadatok létrehozásához.

- /**WeatherDataModel**: a WeatherDataModel-metaadatok időjárási adatokat jelölnek. Megfelel a forrás által biztosított adatkészleteknek. Előfordulhat például, hogy egy DailyForecastSimpleModel naponta egyszer átlagos hőmérséklet-, páratartalom-és csapadék-információkat biztosít. Ezzel szemben a DailyForecastAdvancedModel sokkal több információt biztosítanak óránkénti részletességgel. Tetszőleges számú időjárási adatmodellt hozhat létre.
- /**JobType**: a FarmBeats bővíthető felügyeleti rendszerrel rendelkezik. Időjárási adatszolgáltatóként különböző adatkészleteket és API-kat fog tartalmazni (például GetDailyForecasts). A JobType használatával engedélyezheti ezeket az adatkészleteket és API-kat a FarmBeats-ben. A feladattípus létrehozása után az ügyfél elindíthatja az adott típusú feladatokat, hogy a helyükhöz vagy a hozzájuk tartozó farmhoz tartozó időjárási adatokhoz lehessen jutni. További információ: JobType és Job API-k a [FarmBeats hencegő](https://aka.ms/farmbeatsswagger)szolgáltatásban.

### <a name="jobs"></a>Feladatok

A rendszer minden alkalommal meghívja a feladatok összetevőt, amikor egy FarmBeats-felhasználó futtatja a rendszerindítási folyamat részeként létrehozott/JobType feladatát. A feladatokhoz tartozó Docker-futtatási parancs a létrehozott/JobType részeként van definiálva.

A művelet beolvassa az adatait a forrásból, és leküldi a FarmBeats. A rendszerindítási folyamat során az adatok beolvasásához szükséges paramétereket a/JobType. részeként kell meghatározni.

A művelet részeként a programnak létre kell hoznia egy/WeatherDataLocation a rendszerindítási folyamat során létrehozott/WeatherDataModel alapján. A/WeatherDataLocation felel meg egy olyan helynek (szélességi és hosszúsági koordinátáknak), amelyet a felhasználó a feladatokhoz tartozó paraméterként adott meg.

### <a name="object-details"></a>Objektum részletei

WeatherDataModel | Leírás |
--- | ---
Név  | Az időjárási adatmodell neve. |
Leírás  | A modell értelmes leírása. |
Tulajdonságok  | Az adatszolgáltató által definiált további tulajdonságok. |
weatherMeasures > neve  | Az időjárási mérték neve. Például humidity_max. |
weatherMeasures > adattípus  | Dupla vagy enumerálás. Ha Enum, a measureEnumDefinition megadása kötelező. |
weatherMeasures > measureEnumDefinition  | Csak akkor szükséges, ha az adattípus enumerálás. Például: `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > típusa  | Az időjárási telemetria-adattípusok típusa. Például: RelativeHumidity. A rendszer által meghatározott típusok: AmbientTemperature, nem egység, CO2, mélység, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrát, O2, PH, foszfát, PointInTime, kálium, nyomás, RainGauge, RelativeHumidity, sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, szélsebesség, párolgás és PAR. További típusok hozzáadásához tekintse meg a jelen cikk [ExtendedType hozzáadása](#add-extendedtype) című szakaszát.
weatherMeasures > egység | Az időjárási telemetria vonatkozó adategység. A rendszer által meghatározott egységek: nincs egység, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, milliméter, centiméter, Meter, hüvelyk, láb, Mile, km, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, százalék, PartsPerMillion, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, MilliLiter, liter, UnixTimestamp, másodperc, MicroMolePerMeterSquaredPerSecond, InchesPerHour és. További egységek hozzáadásához tekintse meg a jelen cikk [ExtendedType hozzáadása](#add-extendedtype) című szakaszát.
weatherMeasures > AggregationType  | Az összesítés típusa. A lehetséges értékek: none, átlag, maximum, minimum, StandardDeviation, Sum és Total.
weatherMeasures > mélység  | Az érzékelő mélysége centiméterben. Például a nedvesség 10 cm-es méretének mérése a terepen.
weatherMeasures > leírása  | A mérték értelmes leírása. 

JobType | Leírás |
--- | ---
Név  | A feladattípus neve. Például Get_Daily_Forecast. Az ügyfél futtatja ezt a feladatot az időjárási adatok beszerzéséhez.|
pipelineDetails > paraméterek > neve  | A paraméter neve. |
pipelineDetails > paraméterek > típusa | A paraméter típusa. A lehetséges értékek a következők: string, int, float, bool és Array. |
pipelineDetails > paraméterek > isRequired | A paraméter logikai értéke. Az érték igaz, ha a paraméter megadása kötelező. Ellenkező esetben az érték hamis. Az alapértelmezett érték a True (igaz). |
pipelineDetails > paraméterek > defaultValue | A paraméter alapértelmezett értéke. |
pipelineDetails > paraméterek > leírása | A paraméter leírása. |
Tulajdonságok  | További tulajdonságok a gyártótól.
Tulajdonságok > programRunCommand | Docker-futtatási parancs. Ez a parancs akkor fut le, amikor az ügyfél futtatja az időjárási feladatot. |

WeatherDataLocation | Leírás |
--- | ---
weatherDataModelId  | A rendszerindítási folyamat során létrehozott megfelelő WeatherDataModel azonosítója.|
location  | Szélesség, hosszúság és Jogosultságszint-emelés. |
Name | Az objektum neve. |
Leírás | Az időjárási adattárolási hely leírása. |
farmId | Választható A farm azonosítója. Az ügyfél ezt az azonosítót a Job paraméter részeként adja meg. |
Tulajdonságok  | További tulajdonságok a gyártótól.

További információ az objektumokról és azok tulajdonságairól: [FarmBeats hencegés](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Az eszközök kezeléséhez és a metaadatok szinkronizálásához szükséges fordítónak meg kell őriznie ezt az azonosítót.

**Metaadatok szinkronizálása**

Az összekötő Docker-összetevőjének képesnek kell lennie frissítések küldésére a metaadatokon. Például ha az időjárási szolgáltató új paramétereket ad hozzá egy adatkészlethez, vagy amikor új funkciót, például új 30 napos előrejelzést ad hozzá, akkor a rendszer frissítéseket küld.

> [!NOTE]
> A törlés nem támogatott a metaadatokhoz az időjárási adatmodellben.
>
> A metaadatok frissítéséhez meg kell hívnia `/Get/{ID}` az időjárási adatok modelljét. Frissítse a módosított tulajdonságokat, majd hajtson végre `/Put/{ID}` minden olyan tulajdonságot, amelyet a felhasználó állít be.

## <a name="weather-data-telemetry-specifications"></a>Időjárási adatok (telemetria) – specifikációk

Az időjárási adatkezelési szolgáltatás egy Azure Event hub-ba feldolgozásra leküldett kanonikus üzenetre van leképezve. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból. 

Az időjárási adatokat FarmBeats küldéséhez hozzon létre egy ügyfelet, amely üzeneteket küld egy FarmBeats lévő Event hub-nak. További információ: [telemetria küldése az Event hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)-ba.

A következő Python-kód a telemetria ügyfélként küldi el a megadott Event hub-nak.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Itt látható a kanonikus üzenet formátuma:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Példa egy telemetria-üzenetre:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Hibaelhárítás és hibák kezelése

### <a name="error-logging"></a>Hiba naplózása

A partneri feladatot a meglévő feladatokhoz tartozó keretrendszerben futtathatja. Így a hibák ugyanúgy vannak naplózva, mint a többi meglévő FarmBeats-feladathoz (például GetFarmData és SensorPlacement) tartozó hibák. A Data Factory folyamaton belül futó Data Factory tevékenység mind a, mind a `STDERR` `STDOUT` . Mindkét fájl elérhető a `datahublogs-xxx` FarmBeats erőforráscsoporthoz tartozó Storage-fiókban.

A Datahub lib segítő függvények lehetővé teszik a naplózást a teljes Datahub-naplók részeként. További információ: a [NOAA Docker-rendszerkép GitHub-lapja](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

Ha az ügyfél nem tud időjárási időt kapni a FarmBeats-példányban, a probléma elhárításához nyújtson támogatást és mechanizmust.

## <a name="add-extendedtype"></a>ExtendedType hozzáadása

A FarmBeats támogatja az új szenzor mértékének típusát és egységeit. Új egységeket vagy típusokat adhat hozzá, ha frissíti a *bootstrap_manifest.js* fájlt a [hivatkozás implementációjában](https://github.com/azurefarmbeats/noaa_docker).

Az alábbi lépéseket követve hozzáadhat egy új WeatherMeasure-típust (például PrecipitationDepth).

1. Kérelem készítése a `GET` /ExtendedType a lekérdezés használatával `filter - key = WeatherMeasureType` .
2. Jegyezze fel a visszaadott objektum AZONOSÍTÓját.
3. Adja hozzá az új típust a listához a visszaadott objektumban. Hozzon végre egy `PUT` kérelmet a/ExtendedType{ID} az alábbi új listával. A bemeneti adattartalomnak meg kell egyeznie a korábban kapott választal. Az új egységet az értékek listájának végén kell hozzáfűzni.

A/ExtendedType API-val kapcsolatos további információkért tekintse meg a [FarmBeats hencegő](https://aka.ms/FarmBeatsSwagger)témakört.

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik egy összekötő Docker-összetevővel, amely integrálható a FarmBeats. Következő lépésként ismerje meg, hogyan [szerezhet be időjárási adatait](get-weather-data-from-weather-partner.md) a Docker-rendszerkép használatával a FarmBeats-ben. 
