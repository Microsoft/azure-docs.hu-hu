---
title: Térbeli elemzési műveletek
titleSuffix: Azure Cognitive Services
description: A Térbeli elemzés műveletei.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 37ac7573a1794c97c81fe5364204f85ff14d9fa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538088"
---
# <a name="spatial-analysis-operations"></a>Térbeli elemzési műveletek

A térbeli elemzés lehetővé teszi a kameraeszközökről származó valós idejű streamelési videók elemzését. A Térbeli elemzés műveletei minden egyes konfigurált kameraeszközhöz létrehoznak egy JSON-üzenetek kimeneti streamet, amely a saját eszközpéldányának Azure IoT Hub. 

A Spatial Analysis tároló a következő műveleteket valósítja meg:

| Művelet azonosítója| Leírás|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Megszámolja a kijelölt zónában található személyeket a kameranézetben. Ahhoz, hogy a PersonCount pontos összeget rögzítsen, a zónát teljes mértékben lefednie kell egyetlen kamerának. <br> Kibocsát egy kezdeti _personCountEvent eseményt,_ majd _a personCountEvent eseményt,_ amikor a szám megváltozik.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Nyomon követi, hogy egy személy mikor lép át egy kijelölt vonalon a kamera nézetében. <br>Egy _personLineEvent eseményt_ bocsát ki, amikor a személy átlépi a vonalat, és irányt szolgáltat. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Egy _personZoneEnterExitEvent_ eseményt bocsát ki, amikor egy személy belép a zónába vagy kilép a zónából, és irányt biztosít a zóna áthúzott oldalával. Egy _personZoneDwellTimeEvent_ adatokat bocsát ki, amikor a személy kilép a zónából, és irányinformációkat, valamint a zónán belül töltött ezredmásodpercek számát is biztosítja. |
| cognitiveservices.vision.spatialanalysis-persondistance | Nyomon követi, ha valaki megsért egy távolsági szabályt. <br> Rendszeres időközönként _egy personDistanceEvent_ adatokat bocsát ki az egyes távolságok megsértésének helyével együtt. |
| cognitiveservices.vision.spatialanalysis | Általános művelet, amely a fent említett összes forgatókönyv futtatására használható. Ez a lehetőség akkor hasznosabb, ha több forgatókönyvet szeretne futtatni ugyanazon a kamerán, vagy hatékonyabban szeretne rendszererőforrásokat (például GPU-t) használni. |

A fenti műveletek a verzióban is elérhetők, amelyek képesek a feldolgozás alatt álló videó képkockáit `.debug` vizualizálni. A képkockák és események megjelenítésének engedélyezéséhez futtassa a következőt a `xhost +` gazdagépen: .

| Művelet azonosítója| Leírás|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Megszámol egy kijelölt zónában található személyeket a kamera nézetében. <br> Kibocsát egy kezdeti _personCountEvent eseményt,_ majd _a personCountEvent eseményt,_ amikor a szám megváltozik.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Nyomon követi, hogy egy személy mikor lép át egy kijelölt vonalon a kamera nézetében. <br>Egy _personLineEvent eseményt_ bocsát ki, amikor a személy átlépi a vonalat, és irányt szolgáltat. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | A _personZoneEnterExitEvent_ eseményt bocsátja ki, amikor egy személy belép a zónába vagy kilép a zónából, és irányt biztosít a keresztezett zóna számolt oldalával. Egy _personZoneDwellTimeEvent_ adatokat bocsát ki, amikor a személy kilép a zónából, és irányt és a zónán belül töltött ezredmásodpercek számát is biztosítja. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Nyomon követi, ha valaki megsért egy távolsági szabályt. <br> Rendszeres időközönként _kibocsát egy personDistanceEvent_ adatokat az egyes távolságok megsértésének helyével. |
| cognitiveservices.vision.spatialanalysis.debug | Általános művelet, amely a fent említett összes forgatókönyv futtatására használható. Ez a lehetőség akkor hasznosabb, ha több forgatókönyvet szeretne futtatni ugyanazon a kamerán, vagy hatékonyabban szeretne rendszererőforrásokat (például GPU-t) használni. |

A térbeli elemzés [](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) a videó AI Live Video Analytics modulként is futtatható. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Művelet azonosítója| Leírás|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Megszámol egy kijelölt zónában található személyeket a kamera nézetében. <br> Kibocsát egy kezdeti _personCountEvent eseményt,_ majd _a personCountEvent eseményt,_ amikor a szám megváltozik.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Nyomon követi, hogy egy személy mikor lép át egy kijelölt vonalon a kamera nézetében. <br>Egy _personLineEvent eseményt_ bocsát ki, amikor a személy átlépi a vonalat, és irányt szolgáltat. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | A _personZoneEnterExitEvent_ eseményt bocsátja ki, amikor egy személy belép a zónába vagy kilép a zónából, és irányt biztosít a keresztezett zóna számolt oldalával. Kibocsát egy _personZoneDwellTimeEvent_ adatokat, amikor a személy kilép a zónából, és irányt és a zónában töltött ezredmásodpercek számát is biztosítja.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Nyomon követi, ha valaki megsért egy távolsági szabályt. <br> Rendszeres időközönként _kibocsát egy personDistanceEvent_ adatokat az egyes távolságok megsértésének helyével. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | Általános művelet, amely a fent említett összes forgatókönyv futtatására használható. Ez a lehetőség akkor hasznosabb, ha több forgatókönyvet szeretne futtatni ugyanazon a kamerán, vagy hatékonyabban szeretne rendszererőforrásokat (például GPU-t) használni. |

Live Video Analytics műveletek a verzióban is elérhetők `.debug` (például cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug), amely képes a videó képkockáit feldolgozás alatt álló képkockák megjelenítésére. A parancs futtatásával engedélyezze a képkockák és események megjelenítését a `xhost +` gazdagépen

> [!IMPORTANT]
> A számítógépes látási AI-modellek egy határolókeret használatával észlelik és megkeresik az emberi jelenlétet a videófelvételek és -kimenetek között. Az AI-modellek nem kísérelik meg az egyének identitásának vagy demográfiai személyazonosságának felderítését.

Ezek a térbeli elemzési műveletekhez szükséges paraméterek.

| Üzemeltetési paraméterek| Leírás|
|---------|---------|
| Művelet azonosítója | A fenti táblázatban található műveletazonosító.|
| engedélyezve | Logikai: true (igaz) vagy false (hamis)|
| VIDEO_URL| A kameraeszköz RTSP URL-címe (például: `rtsp://username:password@url` ). A térbeli elemzés támogatja a H.264 kódolású streamet RTSP, HTTP vagy mp4 protokollon keresztül. Video_URL base64 sztringértékként AES-titkosítással, és ha a videó URL-címe titkosítva van, akkor környezeti változóként kell meg `KEY_ENV` `IV_ENV` adni. A kulcsok létrehozásához és a titkosításhoz használható minta segédprogramot itt [talál:](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | A kameraeszköz vagy a videóstream rövid neve. Ez az esemény JSON-kimenetében lesz visszaadva.|
| VIDEO_IS_LIVE| A kameraeszközökre igaz; false (hamis) értéket a rögzített videókhoz.|
| VIDEO_DECODE_GPU_INDEX| Melyik GPU-t kell dekódolni a videókerethez? Alapértelmezés szerint ez 0. Meg kell egyedni a másik `gpu_index` csomópont-konfigurációban találhatóval, például `VICA_NODE_CONFIG` a következővel: `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Bemeneti videó/stream képkockaszélessége (például 1920). Ez egy nem kötelező mező, és ha meg van téve, a keret erre a dimenzióra lesz méretezve az oldalarány megőrzése mellett.|
| DETECTOR_NODE_CONFIG | JSON, amely azt jelzi, hogy melyik GPU-on kell futtatni az érzékelő csomópontot. A következő formátumban kell lennie: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | A zóna és a sor JSON-konfigurációja az alábbiakban ismertetett módon.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` a videóstreamben arcmaszkot viselő személyek észlelésének engedélyezéséhez, `False` letiltásához. Ez alapértelmezés szerint le van tiltva. Az arcmaszk észleléséhez a bemeneti videó szélességi paraméterének 1920-nak kell `"INPUT_VIDEO_WIDTH": 1920` lennie. Az arcmaszk attribútum nem lesz visszaadva, ha az észlelt személyek nem néznek a kamerával, vagy túl távol vannak tőle. További információért tekintse meg a kamera [elhelyezési](spatial-analysis-camera-placement.md) útmutatóját |

### <a name="detector-node-parameter-settings"></a>A Detector Node paraméterbeállítása
Ez egy példa az DETECTOR_NODE_CONFIG összes térbeli elemzési műveletre.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Név | Típus| Description|
|---------|---------|---------|
| `gpu_index` | sztring| A GPU-index, amelyen ez a művelet futni fog.|
| `do_calibration` | sztring | Azt jelzi, hogy a ellenszenve be van kapcsolva. `do_calibration` A megfelelő működéshez igaznak kell lennie a **cognitiveservices.vision.spatialanalysis-persondistance** függvényre. do_calibration alapértelmezett beállítása True (Igaz). |
| `enable_recalibration` | logikai | Azt jelzi, hogy az automatikus újraszámítás be van-e kapcsolva. Az alapértelmezett szint a `true`.|
| `calibration_quality_check_frequency_seconds` | int | Az egyes minőségellenőrzések közötti másodpercek minimális száma annak meghatározásához, hogy szükség van-e újraszámításra. Az alapértelmezett érték `86400` (24 óra). Csak akkor használatos, ha `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | Az új adatminták újraszámításra és minőség-ellenőrzésre való gyűjtése közötti másodpercek minimális száma. Az alapértelmezett érték `300` (5 perc). Csak akkor használatos, ha `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | int | A mintagyűjteményenként összegyűjthető új adatminták minimális száma. Az alapértelmezett szint a `10`. Csak akkor használatos, ha `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | int | A kameramodellek mintavételekor tárolható adatminták maximális száma. Az alapértelmezett szint a `1000`. Csak akkor használatos, ha `enable_recalibration=True` .|
| `enable_breakpad`| logikai | Jelzi, hogy engedélyezni szeretné-e a hibakeresési használathoz összeomlási memóriakép létrehozásához használt megszakítótömböt. Alapértelmezés szerint `false` ez a következő: . Ha a beállítása , akkor a tároló részében is hozzá kell `true` `"CapAdd": ["SYS_PTRACE"]` adni a `HostConfig` következőt: `createOptions` . Alapértelmezés szerint az összeomlási memóriakép fel lesz töltve a [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter-alkalmazásba. Ha azt szeretné, hogy az összeomlási memóriaképeket feltöltse a saját AppCenter-alkalmazásba, felülbírálhatja a környezeti változót az alkalmazás `RTPT_APPCENTER_APP_SECRET` titkos alkalmazásküldetésével.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Térbeli elemzés műveleteinek konfigurálása és kimenete
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>A cognitiveservices.vision.spatialanalysis-personcount zónakonfigurációja

 Ez egy példa egy zóna konfiguráló JSON-bemenetre SPACEANALYTICS_CONFIG paraméterhez. Ehhez a művelethez több zónát is konfigurálhat.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `zones` | list| Zónák listája. |
| `name` | sztring| A zóna rövid neve.|
| `polygon` | list| Minden értékpár egy sokszög csúcsának x,y értékét jelöli. A sokszög azokat a területeket jelöli, ahol a rendszer nyomon követi vagy számolja az embereket, a sokszögpontok pedig normalizált koordinátákon (0–1), ahol a bal felső sarok (0,0, 0,0), a jobb alsó sarok pedig (1,0, 1,0).   
| `threshold` | float| Az események akkor vannak kihozva, ha a személy nagyobb, mint a zónán belüli képpontok száma. |
| `type` | sztring| A **cognitiveservices.vision.spatialanalysis-personcount** számára ennek anak kell `count` lennie.|
| `trigger` | sztring| Az eseményküldési eseményindító típusa. A támogatott értékek olyan események küldésére használhatók, amikor a szám megváltozik, vagy rendszeres időközönként, függetlenül attól, hogy a szám `event` `interval` megváltozott-e.
| `output_frequency` | int | Az események kiesése. Ha `output_frequency` = X, minden X esemény ki lesz-e edzve, például: `output_frequency` A = 2 azt jelenti, hogy minden más esemény kimenet. A `output_frequency` és a egyaránt `event` `interval` vonatkozik. |
| `focus` | sztring| Az események kiszámításához használt ponthely a személy határolókeretében. A fókusz értéke lehet (a személylábnyoma), (a határolókeret alsó középső része), (a személy határolókeretének `footprint` `bottom_center` `center` közepén).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Vonalkonfiguráció a cognitiveservices.vision.spatialanalysis-personcrossingline-hez

Ez egy példa egy sor konfiguráló JSON-bemenetre SPACEANALYTICS_CONFIG paraméterhez. Ehhez a művelethez több határvonalat is konfigurálhat.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `lines` | list| Sorok listája.|
| `name` | sztring| A sor rövid neve.|
| `line` | list| A sor definíciója. Ez egy irányban található vonal, amely lehetővé teszi a "bejegyzés" és az "kilépés" összehasonlítását.|
| `start` | értékpár| x, y koordináták a vonal kiindulási pontjához. A lebegőpontos értékek a csúcspont bal felső sarkához viszonyított pozícióját jelentik. Az abszolút x, y értékek kiszámításához szorozza meg ezeket az értékeket a képkocka méretével. |
| `end` | értékpár| x, y koordináták a vonalvégponthoz. A lebegőpontos értékek a csúcspont bal felső sarkához viszonyított pozícióját jelentik. Az abszolút x, y értékek kiszámításához szorozza meg ezeket az értékeket a képkocka méretével. |
| `threshold` | float| Az események akkor vannak kihozva, ha a személy nagyobb, mint a zónában lévő képpontok száma. Az alapértelmezett érték a 16. Ez az ajánlott érték a maximális pontosság eléréséhez. |
| `type` | sztring| A **cognitiveservices.vision.spatialanalysis-personcrossingline számára** ez a kell, `linecrossing` hogy legyen.|
|`trigger`|sztring|Az esemény küldésére vonatkozó eseményindító típusa.<br>Támogatott értékek: "event": akkor történik, ha valaki átlépi a vonalat.|
| `focus` | sztring| Az események kiszámításához használt pont helye a személy határolókeretében. A fókusz értéke lehet (a személylábnyom), (a személy határolókeretének alsó középső része) (a személy határolókeretének `footprint` `bottom_center` `center` közepén). Az alapértelmezett érték a helyigény.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zónakonfiguráció a cognitiveservices.vision.spatialanalysis-personcrossingpolygonhoz

Ez egy példa egy zónát konfiguráló JSON-bemenetre SPACEANALYTICS_CONFIG paraméterhez. Ehhez a művelethez több zónát is konfigurálhat.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `zones` | list| Zónák listája. |
| `name` | sztring| A zóna rövid neve.|
| `polygon` | list| Minden értékpár a sokszög csúcsának x,y értékét jelöli. A sokszög azokat a területeket jelöli, ahol a személyeket nyomon követik vagy megszámolják. A lebegőpontos értékek a csúcspont bal felső sarkához viszonyított pozícióját jelentik. Az abszolút x, y értékek kiszámításához szorozza meg ezeket az értékeket a képkocka méretével. 
| `threshold` | float| Az események akkor vannak kihozva, ha a személy nagyobb, mint a zónában lévő képpontok száma. Az alapértelmezett érték 48, ha a típus zonecrossing, és 16, ha az idő DwellTime. Ezek az ajánlott értékek a maximális pontosság eléréséhez.  |
| `type` | sztring| A **cognitiveservices.vision.spatialanalysis-personcrossingpolygon számára** ez vagy `zonecrossing` `zonedwelltime` lehet.|
| `trigger`|sztring|Esemény küldésére vonatkozó eseményindító típusa<br>Támogatott értékek: "event": akkor jelentkezik be, ha valaki belép a zónába, vagy kilép a zónából.|
| `focus` | sztring| Az események kiszámításához használt pont helye a személy határolókeretében. A fókusz értéke lehet (a személylábnyom), (a személy határolókeretének alsó középső része) (a személy határolókeretének `footprint` `bottom_center` `center` közepén). Az alapértelmezett érték a helyigény.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zónakonfiguráció a cognitiveservices.vision.spatialanalysis-persondistance-hez

Ez egy példa egy JSON-bemenetre a SPACEANALYTICS_CONFIG paraméterhez, amely egy zónát konfigurál a **cognitiveservices.vision.spatialanalysis-persondistance számára.** Ehhez a művelethez több zónát is konfigurálhat.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `zones` | list| Zónák listája. |
| `name` | sztring| A zóna rövid neve.|
| `polygon` | list| Minden értékpár a sokszög csúcsának x,y értékét jelöli. A sokszög azokat a területeket jelöli, ahol az embereket megszámoljuk, és az emberek távolságát mérjük. A lebegőpontos értékek a csúcspont bal felső sarkához viszonyított pozícióját jelentik. Az abszolút x, y értékek kiszámításához szorozza meg ezeket az értékeket a képkocka méretével. 
| `threshold` | float| Az események akkor vannak kihozva, ha a személy nagyobb, mint a zónán belüli képpontok száma. |
| `type` | sztring| A **cognitiveservices.vision.spatialanalysis-persondistance objektumhoz** ennek anak kell `people_distance` lennie.|
| `trigger` | sztring| Az eseményküldési eseményindító típusa. A támogatott értékek akkor küldenek eseményeket, amikor a szám megváltozik, vagy rendszeres időközönként, függetlenül attól, hogy a szám `event` `interval` megváltozott-e.
| `output_frequency` | int | Az események kiesése. Ha `output_frequency` = X, akkor minden X esemény ki lesz-e ürülve, például: `output_frequency` = 2 azt jelenti, hogy minden más esemény kimenet. A `output_frequency` és a egyaránt `event` `interval` vonatkozik.|
| `minimum_distance_threshold` | float| Egy lábban található távolság, amely "TooClose" eseményt vált ki, ha az emberek kisebb távolságban vannak egymástól.|
| `maximum_distance_threshold` | float| Egy lábban található távolság, amely "TooFar" eseményt vált ki, ha az emberek nagyobbak egymástól.|
| `aggregation_method` | sztring| Az összesített persondistance eredményének metódusa. A aggregation_method és a esetén is `mode` `average` alkalmazható.|
| `focus` | sztring| Az események kiszámításához használt ponthely a személy határolókeretében. A fókusz értéke lehet (a személylábnyoma), (a határolókeret alsó középső része), (a személy határolókeretének `footprint` `bottom_center` `center` közepén).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>A cognitiveservices.vision.spatialanalysis konfigurációja
Ez egy példa egy JSON-bemenetre a SPACEANALYTICS_CONFIG paraméterhez, amely egy vonalat és zónát konfigurál a **cognitiveservices.vision.spatialanalysis paraméterhez.** Ehhez a művelethez több sort/zónát is konfigurálhat, és minden sor/zóna különböző eseményekkel is lehet.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Kamera konfigurációja

A zónák [és vonalak konfigurálásával](spatial-analysis-camera-placement.md) kapcsolatos további információkért tekintse meg a kamera elhelyezésével kapcsolatos irányelveket.

## <a name="spatial-analysis-operation-output"></a>Térbeli elemzési művelet kimenete

A rendszer az egyes műveletek eseményeit JSON formátumban Azure IoT Hub vissza.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-formátum a cognitiveservices.vision.spatialanalysis-personcount AI-elemzések

Példa JSON-fájlra a művelet által adott esemény kimeneteként.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Eseménymező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személyészlelés egyedi azonosítójának 1-es méretű tömbje|
| `properties` | Gyűjtemény| Értékek gyűjteménye|
| `trackinId` | sztring| Az észlelt személy egyedi azonosítója|
| `zone` | sztring | A sokszög "név" mezője, amely a keresztezett zónát jelöli|
| `trigger` | sztring| Az eseményindító típusa "esemény" vagy "interval", attól függően, hogy a értéke a SPACEANALYTICS_CONFIG `trigger`|

| Észlelések mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | Gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | Gyűjtemény| Bal felső és jobb alsó pont, ha a régió típusa TÉGLALAP |
| `confidence` | float| Algoritmus megbízhatósága|
| `face_mask` | float | A tartományba (0–1) megadott megbízhatósági érték azt jelzi, hogy az észlelt személy arcmaszkot viseli |
| `face_nomask` | float | A tartományba (0–1) megadott megbízhatósági érték  azt jelzi, hogy az észlelt személy nem arcmaszkot viseli |

| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kameraazonosító|
| `timestamp` | dátum| A hasznos JSON-adatok kibocsátásának UTC-dátuma|
| `width` | int | Videókeret szélessége|
| `height` | int | Videókeret magassága|
| `frameId` | int | Keret azonosítója|
| `cameraCallibrationInfo` | Gyűjtemény | Értékek gyűjteménye|
| `status` | sztring | A tudatállapot a következő formátumban: `state[;progress description]` . Az állapot lehet `Calibrating` , `Recalibrating` (ha az újraszámítás engedélyezve van) vagy `Calibrated` . A folyamat leírása rész csak akkor érvényes, ha és állapotban van, amely a jelenlegi állapotjelző folyamat `Calibrating` `Recalibrating` előrehaladását mutatja.|
| `cameraHeight` | float | A kamera magassága a talaj fölött, lábban. Ez az automatikus beszendelésből következik. |
| `focalLength` | float | A kamera fókuszhossza képpontban. Ez az automatikus beszendelésből következik. |
| `tiltUpAngle` | float | A kamera függőleges dőlésszöge. Ez az automatikus beszendelésből következik.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-formátum a cognitiveservices.vision.spatialanalysis-personcrossingline AI-elemzések

JSON-minta a művelet által az észlelések kimenetéhez.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Eseménymező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személyészlelés egyedi azonosítójának 1-es méretű tömbje|
| `properties` | Gyűjtemény| Értékek gyűjteménye|
| `trackinId` | sztring| Az észlelt személy egyedi azonosítója|
| `status` | sztring| Vonalátlépések iránya, "CrossLeft" vagy "CrossRight". Az irány a vonal "vége" felé álló "kezdő" képen alapul. A CrossRight balról jobbra halad. A CrossLeft jobbról balra halad.|
| `zone` | sztring | A keresztezett vonal "név" mezője|

| Észlelések mezőneve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | Gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | Gyűjtemény| Bal felső és jobb alsó pont, ha a régió típusa TÉGLALAP |
| `confidence` | float| Algoritmusok megbízhatósága|
| `face_mask` | float | A tartomány (0–1) attribútum megbízhatósági értéke azt jelzi, hogy az észlelt személy arcmaszkot viseli |
| `face_nomask` | float | A tartomány megbízhatósági értéke (0–1) azt  jelzi, hogy az észlelt személy nem arcmaszkot viseli |

| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kameraazonosító|
| `timestamp` | dátum| A JSON hasznos adatok kibocsátásának UTC-dátuma|
| `width` | int | Videókeret szélessége|
| `height` | int | Videókeret magassága|
| `frameId` | int | Keret azonosítója|


> [!IMPORTANT]
> Az AI-modell észleli a személyt, függetlenül attól, hogy a kamera felé vagy távolról néz-e. Az AI-modell nem futtat arcfelismerést, és nem bocsát ki biometrikus adatokat. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-formátum a cognitiveservices.vision.spatialanalysis-personcrossingpolygon AI-elemzések

Példa JSON-fájlra a művelet által az észlelések kimenetének észleléséhez `zonecrossing` SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

JSON-minta a művelet által az észlelések kimenetéhez a `zonedwelltime` következő típussal: SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Eseménymező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus. Az érték lehet _personZoneDwellTimeEvent vagy_ _personZoneEnterExitEvent_|
| `detectionsId` | array| Az eseményt kiváltó személyészlelés egyedi azonosítójának 1-es méretű tömbje|
| `properties` | Gyűjtemény| Értékek gyűjteménye|
| `trackinId` | sztring| Az észlelt személy egyedi azonosítója|
| `status` | sztring| Sokszögátlépések iránya, "Enter" vagy "Kilépés"|
| `side` | int| A sokszög oldalának száma, amit a személy keresztezt. Mindkét oldal a zónát képviselő sokszög két csúcsa közötti számmal számolt él. A sokszög első két csúcsa közötti él az első oldalt jelenti. Az "Oldal" üres, ha az esemény nincs társítva egy adott oldalhoz az eltakarítás miatt. Például kilépés történt, amikor egy személy eltűnik, de nem a zóna egy oldalán lépkednek át, vagy belépés történt, amikor egy személy megjelent a zónában, de nem látott átkelni egy oldalon.|
| `durationMs` | float | Az ezredmásodpercek száma, amely azt az időt jelenti, amit a személy a zónában töltött. Ez a mező akkor van megszava, ha az esemény típusa _personZoneDwellTimeEvent_|
| `zone` | sztring | A sokszög "név" mezője, amely a keresztezett zónát jelöli|

| Észlelések mezőneve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | Gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | Gyűjtemény| Bal felső és jobb alsó pont, ha a régió típusa TÉGLALAP |
| `confidence` | float| Algoritmus megbízhatósága|
| `face_mask` | float | A tartomány (0–1) attribútum megbízhatósági értéke azt jelzi, hogy az észlelt személy arcmaszkot viseli |
| `face_nomask` | float | A tartományba (0–1) megadott megbízhatósági érték  azt jelzi, hogy az észlelt személy nem arcmaszkot viseli |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-formátum a cognitiveservices.vision.spatialanalysis-persondistance AI-elemzések

JSON-minta a művelet által az észlelések kimenetéhez.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Eseménymező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személyészlelés egyedi azonosítójának 1-es méretű tömbje|
| `properties` | Gyűjtemény| Értékek gyűjteménye|
| `personCount` | int| Az esemény kiesésekor észlelt személyek száma|
| `averageDistance` | float| Az átlagos távolság a lábban észlelt személyek között|
| `minimumDistanceThreshold` | float| A lábban található távolság, amely "TooClose" eseményt vált ki, ha az emberek kisebb távolságban vannak egymástól.|
| `maximumDistanceThreshold` | float| A lábban található távolság, amely "TooFar" eseményt vált ki, ha az emberek nagyobbak egymástól, mint a távolság.|
| `eventName` | sztring| Az esemény `TooClose` neve a következő: `minimumDistanceThreshold` megsértve, `TooFar` `maximumDistanceThreshold` megsértve, `unknown` vagy ha nem fejeződött be az automatikus hitelesítés|
| `distanceViolationPersonCount` | int| A vagy szabálysértés során észlelt személyek `minimumDistanceThreshold` száma `maximumDistanceThreshold`|
| `zone` | sztring | A sokszög "név" mezője, amely a személyek közötti távolságtartó figyelt zónát jelöli|
| `trigger` | sztring| Az eseményindító típusa "event" vagy "interval" (időköz) az `trigger` eseményindítóban megadott SPACEANALYTICS_CONFIG|

| Észlelések mezőneve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | Gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | Gyűjtemény| Bal felső és jobb alsó pont, ha a régió típusa TÉGLALAP |
| `confidence` | float| Algoritmusok megbízhatósága|
| `centerGroundPoint` | 2 lebegőérték| `x`, olyan értékek, amelyek a személy kikövetkeztetett helyének koordinátái `y` a földön, lábban vannak megvakodva. `x` A `y` és a koordináták a padlósíkon, feltéve, hogy a padló szint. A kamera helye a forrás. |

A kiszámításakor a a kamera és a személy közötti távolság a kamera képsíkjával egy vonal mentén. `centerGroundPoint` `x` `y` A a kamera és a személy közötti távolság a kamera képsíkjával párhuzamos vonal mentén. 

![Példa a középponti alappontra](./media/spatial-analysis/x-y-chart.png) 

Ebben a példában `centerGroundPoint` a . `{x: 4, y: 5}` Ez azt jelenti, hogy egy személy 4 láb messze van a kamerától, és 5 lábbal jobbra, felülről lefelé nézve a helyiséget.


| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kameraazonosító|
| `timestamp` | dátum| A JSON hasznos adatok kibocsátásának UTC-dátuma|
| `width` | int | Videókeret szélessége|
| `height` | int | Videókeret magassága|
| `frameId` | int | Keret azonosítója|
| `cameraCallibrationInfo` | Gyűjtemény | Értékek gyűjteménye|
| `status` | sztring | A tudatállapot a következő formátumban: `state[;progress description]` . Az állapot lehet `Calibrating` , `Recalibrating` (ha az újraszámítás engedélyezve van) vagy `Calibrated` . A folyamat leírása rész csak akkor érvényes, ha és állapotban van, amely a jelenlegi állapotjelző folyamat `Calibrating` `Recalibrating` előrehaladását mutatja.|
| `cameraHeight` | float | A kamera magassága a talaj fölött, lábban. Ez az automatikus beszendelésből következik. |
| `focalLength` | float | A kamera fókuszhossza képpontban. Ez az automatikus beszendelésből következik. |
| `tiltUpAngle` | float | A kamera függőleges dőlésszöge. Ez az automatikus beszendelésből következik.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>JSON-formátum a cognitiveservices.vision.spatialanalysis AI-elemzések

A művelet kimenete a konfigurálttól függ, például ha a művelethez van konfigurálva esemény, akkor a kimenet ugyanaz lesz, mint `events` `zonecrossing` a `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>A tároló által létrehozott kimenet használata

Előfordulhat, hogy integrálni szeretné a térbeli elemzés észlelését vagy az eseményeket az alkalmazásba. Íme néhány megközelítés, amit érdemes megfontolni: 

* A választott programozási nyelvhez tartozó Azure Event Hub SDK-val csatlakozhat a Azure IoT Hub végponthoz, és fogadhatja az eseményeket. További [információ: Eszközről a](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) felhőbe küldött üzenetek olvasása a beépített végpontról. 
* Állítsa be **az üzenet-útválasztást** a Azure IoT Hub, hogy az eseményeket más végpontokra küldje, vagy az eseményeket az adattárolóba mentse. További [IoT Hub az üzenet-útválasztást.](../../iot-hub/iot-hub-devguide-messages-d2c.md) 
* Hozzon létre Azure Stream Analytics feladatot az események valós idejű feldolgozásához, amikor megérkeznek, és vizualizációkat hoz létre. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Térbeli elemzési műveletek nagy léptékű üzembe helyezése (több kamera)

A GPU-k legjobb teljesítményének és kihasználtságának érdekében bármilyen térbeli elemzési műveletet üzembe helyezhet több kamerán gráfpéldányok használatával. Az alábbiakban egy minta látható a művelet tizenöt kamerán `cognitiveservices.vision.spatialanalysis-personcrossingline` való futtatásához.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Név | Típus| Leírás|
|---------|---------|---------|
| `batch_size` | int | Ha az összes kamera felbontása azonos, állítsa be az adott műveletben használt kamerák számát, egyébként állítsa 1-re, vagy hagyja meg az alapértelmezett értéket `batch_size` (1), ami azt jelzi, hogy nincs támogatott `batch_size` köteg. |

## <a name="next-steps"></a>Következő lépések

* [Személyek számolásos webalkalmazás üzembe helyezése](spatial-analysis-web-app.md)
* [Naplózás és hibaelhárítás](spatial-analysis-logging.md)
* [Kamera elhelyezési útmutatója](spatial-analysis-camera-placement.md)
* [Útmutató a zónák és sorok elhelyezéséhez](spatial-analysis-zone-line-placement.md)
