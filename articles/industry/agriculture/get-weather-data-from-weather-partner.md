---
title: Időjárási adatok beolvasása a partnerektől
description: Ez a cikk azt ismerteti, hogyan kérhet le időjárási információkat a partnerektől.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460870"
---
# <a name="get-weather-data-from-weather-partners"></a>Időjárási adatok beolvasása a partnerektől

Az Azure FarmBeats egy Docker-alapú összekötő-keretrendszer segítségével teszi lehetővé az időjárási adatok beküldését az időjárási adatszolgáltatókból. Ezen keretrendszer használatával az időjárási adatszolgáltatók olyan Docker-t valósítanak meg, amely integrálható a FarmBeats. Jelenleg a következő időjárási adatszolgáltató támogatott:

  ![FarmBeats-partnerek](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Az időjárási információk használhatók a gyakorlatban hasznosítható elemzések létrehozásához és AI-vagy ML-modellek FarmBeats való létrehozásához.

## <a name="before-you-start"></a>Előkészületek

Az időjárási idő beszerzéséhez győződjön meg arról, hogy [telepítette a FarmBeats](./install-azure-farmbeats.md). Az időjárási integrációt a 1.2.11 és újabb verziók támogatják. 

## <a name="enable-weather-integration-with-farmbeats"></a>Időjárás-integráció engedélyezése a FarmBeats

A FarmBeats-Datahub időjárási adatai beszerzésének megkezdéséhez:

1. Nyissa meg a FarmBeats Datahub hencegő programot `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Nyissa meg a/partner API-t, és tegye meg a POST kérelmet. Használja a következő bemeneti adattartalmat:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Ha például a DTN időjárási adatait szeretné lekérni, használja a következő adattartalmat. A név és a leírás a kívánt beállításoknak megfelelően módosítható.

   > [!NOTE]
   > A következő lépéshez API-kulcs szükséges. A DTN-előfizetés kulcsának beszerzéséhez forduljon a DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > A partner objektummal kapcsolatos további információkért tekintse meg a jelen cikk [függelékét](get-weather-data-from-weather-partner.md#appendix) .

   Az előző lépésben kiépítheti azokat az erőforrásokat, amelyek lehetővé teszik, hogy a Docker fusson az ügyfél FarmBeats-környezetében.  

   Az erőforrások kiépítése körülbelül 10 – 15 percet vesz igénybe.

3. Tekintse meg az előző lépésben létrehozott/partner objektum állapotát. Az állapot ellenõrzéséhez hozzon igénybe egy GET kérelmet a/partner API-ban, és ellenőrizze a partner objektum állapotát. Miután a FarmBeats kiépíti a partnert, az állapota **aktív** értékre van állítva.

4. A/JobType API-ban hozzon igénybe egy GET-kérést. Keresse meg a korábban létrehozott időjárási feladatokat a partner hozzáadási folyamatában. Az időjárási feladatok esetében a **pipelineName** mező formátuma a következő: **partner-name_partner-type_job-Name**.

      Most a FarmBeats-példány aktív időjárási adatpartnert tartalmaz. A feladatok futtatásával egy adott hely (szélesség és hosszúság) és Dátumtartomány alapján is kérhet időjárási adatokat. A feladattípusok részletes információkkal szolgálnak az időjárási feladatok futtatásához szükséges paraméterekről.

      A DTN esetében például a következő feladattípusok lesznek létrehozva:
   
      - **get_dtn_daily_observations**: napi megfigyelések beolvasása egy adott helyen és időszakban.
      - **get_dtn_daily_forecasts**: napi előrejelzéseket kaphat egy adott helyhez és időszakhoz.
      - **get_dtn_hourly_observations**: óránkénti megfigyelést kap egy adott helyhez és időszakhoz.
      - **get_dtn_hourly_forecasts**: a hely és az időszak óránkénti előrejelzését kapja meg.

6. Jegyezze fel a feladattípusok AZONOSÍTÓját és paramétereit.

7. Nyissa meg a/Jobs API-t, és tegye meg a POST-kérést a/Jobs. Használja a következő bemeneti adattartalmat:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   **Get_dtn_daily_observations** futtatásához például használja a következő adattartalmat:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Az előző lépés a partner Docker által meghatározott időjárási feladatokat futtatja, és az időjárási adatok betöltését a FarmBeats. A feladatok állapotát a GET kérelem/Jobs. való beszerzésével tekintheti meg A válaszban keresse meg a **currentState**. Ha végzett, a **currentState** értéke **sikeres**.

## <a name="query-ingested-weather-data"></a>Betöltött időjárási adatmennyiség lekérdezése

Az időjárási feladatok befejezését követően lekérdezheti a betöltött időjárási adatokból a modelleket vagy a gyakorlatban használható elemzéseket a FarmBeats Datahub REST API-k használatával.

Időjárási adataik lekérdezése FarmBeats REST API használatával:

1. A FarmBeats-Datahub [hencegés](https://yourdatahub.azurewebsites.net/swagger)előtt lépjen a/WeatherDataLocation API-ra, és hozzon igénybe egy Get-kérést. A válasz a megadott helyhez (szélesség és hosszúság) létrehozott/WeatherDataLocation objektumokat tartalmazza. Jegyezze fel az objektumok **azonosítóját** és **weatherDataModelId** .

2. Hozzon végre egy GET/{ID} kérést a **weatherDataModelId** /WeatherDataModel API-on, ahogy korábban tette. Az időjárási adatok modellje megjeleníti a betöltött időjárási adatokkal kapcsolatos összes metaadatot és részletet. Az időjárási adatmodell objektumban például az időjárási mérték részletezi, hogy milyen időjárási információk támogatottak, és milyen típusokban és egységekben. Például:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Jegyezze fel a GET/{ID} hívás válaszát az időjárási adatok modelljéhez.

3. Nyissa meg a telemetria API-t, és tegye meg a POST-kérelmet. Használja a következő bemeneti adattartalmat:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    A válasz megjeleníti a megadott időtartományhoz elérhető időjárási adatértékeket:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

Az előző példában a válasz két időbélyegző esetében jeleníti meg az adatokat. Azt is megjeleníti a mérték neve (hőmérséklet) és a jelentett időjárási adatok értékei a két időbélyegben. A jelentett értékek típusának és egységének értelmezéséhez tekintse meg a kapcsolódó időjárási adatok modelljét.

## <a name="troubleshoot-job-failures"></a>Feladatok hibáinak elhárítása

A feladatokkal kapcsolatos hibák elhárításához [Keresse meg a feladatok naplóit](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Függelék

|        Partner   |  Részletek   |
| ------- | -------             |
|     DockerDetails – imageName         |          A Docker-rendszerkép neve. Például: docker.io/mydockerimage (rendszerkép hub.docker.com) vagy myazureacr.azurecr.io/mydockerimage (rendszerkép Azure Container Registry) és így tovább. Ha nincs megadva beállításjegyzék, az alapértelmezett érték hub.docker.com.      |
|          DockerDetails - imageTag             |         A Docker-rendszerkép címkéjének neve. Az alapértelmezett érték a "Latest".     |
|  DockerDetails – hitelesítő adatok      |  A privát Docker eléréséhez szükséges hitelesítő adatok. A partner biztosítja a hitelesítő adatokat.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU-t. További információ: az [összes elérhető linuxos virtuális gép](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Az érvényes értékek: "Small", "ExtraLarge", "Large", "A8", "A9", "medium", "a5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC24", "STANDARD_NC24r" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s" és "STANDARD_D2_V3". *Az alapértelmezett érték a "STANDARD_D2_V2".*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  A dedikált számítógép-csomópontok száma batch-készletben. Az alapértelmezett érték az 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch csomópont-ügynök SKU-azonosítója. Jelenleg csak a "batch. Node. Ubuntu 18,04" kötegelt csomópont-ügynök támogatott.    |
| DockerDetails - partnerCredentials | Hitelesítő adatok a partner API meghívásához a Docker-ben. A partner ezt az információt a támogatott engedélyezési mechanizmus alapján biztosítja; például: Felhasználónév és jelszó, vagy API-kulcsok. |
| partnerType | "Időjárás". A FarmBeats egyéb partneri típusai az "érzékelő" és a "képek".  |
|  name   |   A partner kívánt neve a FarmBeats-rendszeren.   |
|  leírás |  Leírás.   |

## <a name="next-steps"></a>Következő lépések

Most, hogy lekérdezte az érzékelő adatait az Azure FarmBeats-példányról, Ismerje meg, hogyan [hozhatja](generate-maps-in-azure-farmbeats.md#generate-maps) ki a térképeket a farmokhoz.