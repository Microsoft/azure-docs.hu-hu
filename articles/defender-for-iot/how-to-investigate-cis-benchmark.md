---
title: A CIS benchmark javaslatának vizsgálata
description: Az operációs rendszer alapkonfigurációjának ajánlásai alapján alapszintű és speciális vizsgálatokat hajthat végre.
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 01ca6e1fecddff9800872a3e5495a5cac578a74f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782027"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Az operációs rendszer alapkonfigurációjának vizsgálata (a CIS-teljesítményteszt alapján) javaslat 

Az operációs rendszer alapkonfigurációjának ajánlásai alapján alapszintű és speciális vizsgálatokat hajthat végre.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Alapszintű operációs rendszer alapkonfigurációjának biztonsági javaslatának vizsgálata  

Az operációs rendszer alapelveinek megvizsgálása érdekében navigáljon az Azure Defender for IoT portálra a **IoT hub** alatt. További információ: a [biztonsági javaslatok kivizsgálása](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>A speciális operációsrendszer-alapkonfiguráció biztonsági javaslatának vizsgálata  

Ez a szakasz azt ismerteti, hogyan lehet jobban megismerni az operációs rendszer alapkonfigurációjának teszt eredményeit, és hogyan kérdezheti le az eseményeket az Azure Log Analyticsban.  

A speciális operációs rendszer alapkonfigurációjának biztonsági javaslatait vizsgáló vizsgálat csak a log Analytics használatával támogatott. A folytatás előtt kapcsolja össze a Defendert a IoT Log Analytics munkaterülettel. További információk a speciális operációs rendszer alapkonfigurációjának biztonsági javaslatairól: [Azure Defender konfigurálása IoT-ügynökön alapuló megoldáshoz](how-to-configure-agent-based-solution.md).

A riasztások Log Analytics IoT biztonsági eseményeinek lekérdezése:

1. Navigáljon a **riasztások** oldalra.

1. Válassza **a javaslatok vizsgálata log Analytics munkaterületen** lehetőséget.

A IoT biztonsági eseményeinek lekérdezése Log Analyticsban ajánlások:

1. Navigáljon a **javaslatok** oldalra.

1. Válassza **a javaslatok vizsgálata log Analytics munkaterületen** lehetőséget.

1. Az adott eszköz részleteinek megtekintéséhez válassza az **operációs rendszer (os) alapkonfiguráció szabályai Részletek megjelenítése** a **Javaslat részletei** gyors nézetből oldalon.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Egy adott eszköz részleteinek megtekintése."::: 

A IoT biztonsági eseményeinek lekérdezése közvetlenül Log Analytics munkaterületen:

1. Navigáljon a **naplók** lapra.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Válassza ki a naplók elemet a bal oldali ablaktáblán.":::

1. Válassza a **riasztások vizsgálata** lehetőséget, vagy jelölje be a **riasztások vizsgálata log Analytics** lehetőségnél a biztonsági javaslatokból vagy a riasztásokból jelölőnégyzetet.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Hasznos lekérdezések az operációs rendszer alapverziójának erőforrásainak vizsgálatához: 

> [!Note]
> Ügyeljen arra, hogy `<device-id>` a következő lekérdezésekben megadott névvel (s) legyen lecserélve az eszközre. 


### <a name="retrieve-the-latest-information"></a>A legfrissebb információk beolvasása

- **Eszköz-flotta meghibásodása**: a következő lekérdezés futtatásával lekérheti az eszköz-flottában meghiúsult ellenőrzésekkel kapcsolatos legújabb információkat: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Adott eszköz meghibásodása** – a következő lekérdezés futtatásával lekérheti az adott eszközön sikertelen ellenőrzésekkel kapcsolatos legújabb információkat:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Adott eszközhöz tartozó hiba** – a lekérdezés futtatásával lekérheti az adott eszközön hibás ellenőrzésekkel kapcsolatos legújabb információkat: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- Az eszköz- **flotta azon listájának frissítése, amely nem** adott meg egy adott ellenőrzését, a lekérdezés futtatásával lekérheti az eszközök (az eszköz flottáján) frissített listáját:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Következő lépések

A [biztonsági javaslatok vizsgálata](quickstart-investigate-security-recommendations.md).
 