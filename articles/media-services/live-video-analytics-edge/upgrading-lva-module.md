---
title: Élő videó-elemzések frissítése IoT Edge 1,0 – 2,0
description: Ez a cikk ismerteti a különbségeket és azokat a különböző szempontokat, amelyeket figyelembe kell venni a Azure IoT Edge modul élő videós elemzésének (LVA) frissítésekor.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955649"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Élő videó-elemzések frissítése IoT Edge 1,0 – 2,0

Ez a cikk ismerteti a különbségeket és azokat a különböző szempontokat, amelyeket figyelembe kell venni a Azure IoT Edge modul élő videós elemzésének (LVA) frissítésekor.

## <a name="change-list"></a>Lista módosítása

> [!div class="mx-tdCol4BreakAll"]
> |Cím|Élő videó Analytics 1,0|Élő videó Analytics 2,0|Leírás|
> |-------------|----------|---------|---------|
> |Tároló képe|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Microsoft közzétett Docker-rendszerképek az élő videók elemzéséhez Azure IoT Edge|
> |**MediaGraph-csomópontok** |    |   |   |
> |Források|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-forrás </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub üzenet forrása |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-forrás </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub üzenet forrása | MediaGraph-csomópontok, amelyek a média betöltésének és üzeneteinek forrásaként működnek.|
> |Processzorok|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Mozgásészlelési processzor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Frame rate Filter processzor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http-bővítmény processzora </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc-bővítmény processzora </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signal Gate processzor |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Mozgásészlelési processzor </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Frame rate Filter processzor**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http-bővítmény processzora </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc-bővítmény processzora </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signal Gate processzor | MediaGraph-csomópontok, amelyek lehetővé teszik az adathordozó formázását az AI-következtetési kiszolgálókra való küldés előtt.|
> |Fogadóként|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Eszköz fogadója </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: File mosogató </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Üzenet-fogadó IoT Hub|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Eszköz fogadója </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: File mosogató </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Üzenet-fogadó IoT Hub| MediaGraph-csomópontok, amelyek lehetővé teszik a feldolgozott adathordozó tárolását.|
> |**Támogatott MediaGraphs** |    |   |   |
> |Topológiák|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Folyamatos videofelvétel </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Motion Analysis és folyamatos videofelvétel </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Külső elemzés és folyamatos videofelvétel </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Esemény-alapú rögzítés a mozgásban </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Eseményvezérelt rögzítés az AI-on</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Esemény-alapú rögzítés külső eseményen </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Mozgás elemzése </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Motion Analysis és AI-következtetések |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Folyamatos videofelvétel </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Motion Analysis és folyamatos videofelvétel </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Külső elemzés és folyamatos videofelvétel </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Esemény-alapú rögzítés a mozgásban </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Eseményvezérelt rögzítés az AI-on</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Esemény-alapú rögzítés külső eseményen </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Mozgás elemzése </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Motion Analysis és AI-következtetések </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI-összeállítás** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Hang-és videofelvétel** </br>  | MediaGraph-topológiák, amelyek lehetővé teszik egy gráf tervének meghatározását, és a paramétereket helyőrzőként adja meg az értékekhez.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>A IoT Edge modul élő videós elemzésének frissítése 1,0 – 2,0
IoT Edge modul élő videós elemzésének frissítésekor ellenőrizze, hogy az alábbi adatokat frissíti-e.
### <a name="container-image"></a>Tároló képe
Az üzembe helyezési sablonban keresse meg az élő videó Analytics IoT Edge modult a `modules` csomópont alatt, és frissítse a mezőt a következő `image` módon:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Ha nem módosította az élő videó-elemzés nevét IoT Edge modulban, keresse meg a `lvaEdge` modul csomópontját.

### <a name="topology-file-changes"></a>Topológiai fájl módosításai
A topológia fájljaiban győződjön meg róla, **`apiVersion`** hogy a 2,0 értékre van állítva

### <a name="mediagraph-node-changes"></a>MediaGraph csomópont változásai


* A kamerás forrásból származó hang mostantól a videóval együtt is áthaladhat. **Csak hang-** vagy **video-** , illetve **hang-és video** -átadható is lehet, ha bármilyen gráf-csomóponthoz segítségére van szüksége **`outputSelectors`** . Ha például csak az RTSP-forrásból kívánja kiválasztani a videót, és a kimenetét szeretné átadni, adja hozzá a következőt az RTSP forrás csomóponthoz:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>Az egy **`outputSelectors`** opcionális tulajdonság. Ha ez nincs használatban, akkor a Media Graph továbbítja a hanganyagot (ha engedélyezve van), és a videót az RTSP-kameráról a folyamat során. 

* A `MediaGraphHttpExtension` és a `MediaGraphGrpcExtension` processzorok esetében jegyezze fel a következő módosításokat:  
    #### <a name="image-properties"></a>Rendszerkép tulajdonságai
    * `MediaGraphImageFormatEncoded` már nem támogatott. 
      * Ehelyett használja a vagy a vagy a parancsot **`MediaGraphImageFormatBmp`** **`MediaGraphImageFormatJpeg`** **`MediaGraphImageFormatPng`** . Példa:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Ha nyers képeket szeretne használni, használja a következőt: **`MediaGraphImageFormatRaw`** . Ennek használatához frissítse a rendszerkép csomópontját a következőképpen:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > A pixelFormat lehetséges értékei a következők:,,, `yuv420p` `rgb565be` `rgb565le` `rgb555be` , `rgb555le` , `rgb24` , `bgr24` , `argb` ,, `rgba` `abgr` , `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>extensionConfiguration a Grpc bővítmény processzorához  
    * A `MediaGraphGrpcExtension` processzorban egy új nevű tulajdonság **`extensionConfiguration`** érhető el, amely egy opcionális karakterlánc, amely a gRPC-szerződés részeként használható. Ezzel a mezővel bármilyen adattovábbítható a következtetési kiszolgálónak, és meghatározhatja, hogy a következtetési kiszolgáló hogyan használja ezeket az adattípusokat.  
    Ennek a tulajdonságnak az egyik használati esete, ha több AI-modellt is csomagolt egyetlen következtetési kiszolgálóként. Ezzel a tulajdonsággal nem kell minden AI-modellhez ki kell mutatnia egy csomópontot. Ehelyett a Graph-példányokhoz, mint a bővítmények szolgáltatója, megadhatja, hogyan válassza ki a különböző AI-modelleket a **`extensionConfiguration`** tulajdonsággal és a végrehajtás során, a LVA továbbítja ezt a karakterláncot a következtetést felhasználó kiszolgálónak, amely ezt a parancsot használhatja a kívánt AI-modell meghívásához.  

    #### <a name="ai-composition"></a>AI-összeállítás
    * A Live Video Analytics 2,0 mostantól támogatja a több, mint egy Media Graph-bővítményt használó processzor használatát a topológián belül. Az RTSP kamerából különböző AI-modellekre is átadhatja az adathordozó-képkockákat egymás után párhuzamosan, vagy a kettő kombinációjával. Tekintse meg a minta topológiát, amely két, egymás után használt AI-modellt mutat be.

### <a name="disk-space-management-with-sink-nodes"></a>Lemezterület-kezelés fogadó csomópontokkal
* A **file mosogató** csomópontban megadhatja, hogy mekkora lemezterületet használhat az élő videó-elemzés IoT Edge modulban a feldolgozott lemezképek tárolásához. Ehhez adja hozzá a mezőt a **`maximumSizeMiB`** FileSink csomóponthoz. A minta file mosogató csomópont a következő:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* Az **eszköz** fogadó csomópontján megadhatja, hogy mekkora lemezterületet használhat az élő videó-elemzés IoT Edge modulban a feldolgozott lemezképek tárolásához. Ehhez adja hozzá a mezőt az objektum-fogadó **`localMediaCacheMaximumSizeMiB`** csomóponthoz. A példaként szolgáló eszköz fogadó csomópontja a következő:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  A **file mosogató** elérési útja az Alapkönyvtár elérési útjára és a fájlnév mintára van felosztva, **míg az objektum elérési** útja magában foglalja az Alapkönyvtár elérési útját.  

### <a name="frame-rate-management"></a>A frame rate kezelése
* **`MediaGraphFrameRateFilterProcessor`** a **IoT Edge 2,0 modul Live Video Analytics szolgáltatásában** elavult.
    * A bejövő videó feldolgozásra való mintavételezéséhez adja hozzá a **`samplingOptions`** tulajdonságot a MediaGraph bővítmény processzorokhoz ( `MediaGraphHttpExtension` vagy `MediaGraphGrpcExtension` )  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Modul metrikái a Prometheus formátumban
Ezzel a kiadással a a (Azure Monitor) mérőszámok küldésére használható. A metrikák innen Log Analytics vagy egy Event hubhoz irányíthatók.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Az események osztályozása":::

A Docker használatával könnyedén létrehozhat egy, a saját konfigurációval rendelkező Grafi rendszerképet. További információ a [figyelés és naplózás](monitoring-logging.md#azure-monitor-collection-via-telegraf) oldalon található.

## <a name="next-steps"></a>Következő lépések

[Ismerkedés az élő videók Analytics szolgáltatásával IoT Edge](get-started-detect-motion-emit-events-quickstart.md)