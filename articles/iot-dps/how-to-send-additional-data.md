---
title: Hasznos adatok átvitele az eszköz és az Azure Device kiépítési szolgáltatás között
description: Ez a dokumentum azt ismerteti, hogyan lehet átvinni a hasznos adatokat az eszköz és az eszköz kiépítési szolgáltatása (DPS) között.
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259519"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>A hasznos adatok átvitele az eszközök és a DPS között
Időnként a DPS-nek több adatokra van szüksége az eszközökről, hogy megfelelően kiépítse őket a megfelelő IoT Hubba, és hogy az adatoknak az eszköznek kell megadniuk. A DPS azonban visszatérhet az eszközre, hogy az ügyféloldali logikát is megkönnyítse. 

## <a name="when-to-use-it"></a>Mikor lehet használni
Ez a funkció az [Egyéni foglalások](./how-to-use-custom-allocation-policies.md)fejlesztéséhez használható. Például az eszköz modellje alapján szeretné kiosztani az eszközöket az emberi beavatkozás nélkül. Ebben az esetben [Egyéni foglalást](./how-to-use-custom-allocation-policies.md)fog használni. Beállíthatja, hogy az eszköz bejelentse a modell adatait az [eszköz regisztrálása hívásának](/rest/api/iot-dps/runtimeregistration/registerdevice)részeként. A DPS továbbítja az eszköz hasznos adatait az egyéni kiosztási webhookba. A függvény eldöntheti, hogy mely IoT Hub az eszköz fog megjelenni, amikor megkapja az eszköz modelljének adatait. Hasonlóképpen, ha a webhook bizonyos adatértékeket szeretne visszaadni az eszköznek, akkor a webhook válaszában a rendszer visszaadja az adatbevitelt karakterláncként.  

## <a name="device-sends-data-payload-to-dps"></a>Az eszköz adattartalmat küld a DPS-nek
Ha az eszköz [regisztrálja](/rest/api/iot-dps/runtimeregistration/registerdevice) a DPS-t, a regisztrálási hívás javítható a törzs más mezőinek elvégzéséhez. A törzs a következőhöz hasonlóan néz ki: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>A DPS visszaadja az eszköznek az adatok értékét
Ha az egyéni kiosztási szabályzat webhooka bizonyos adatmennyiséget szeretne visszaadni az eszköznek, akkor a webhook-válaszban karakterláncként adja vissza az adatmennyiséget. A módosítás az alábbi hasznos adatok szakaszban található. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-támogatás
Ez a funkció C, C#, JAVA és Node.js [ügyféloldali SDK](./index.yml)-k esetében érhető el.  

## <a name="next-steps"></a>Következő lépések
* Fejlesztés az Azure IoT Hub és az Azure-hoz készült [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) -val IoT hub Device Provisioning Service