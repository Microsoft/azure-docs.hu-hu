---
title: Azure-Service Fabric fordított proxy diagnosztika
description: Megtudhatja, hogyan figyelheti és diagnosztizálhatja a kérelmek feldolgozását az Azure Service Fabric-alkalmazások fordított proxyján.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75645463"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>A kérelmek feldolgozásának figyelése és diagnosztizálása fordított proxyn

A Service Fabric 5,7-es kiadásával kezdődően a fordított proxy eseményei gyűjteményhez érhetők el. Az események két csatornán érhetők el, amelyek közül az egyik csak a kérelem feldolgozási hibával kapcsolatos, a fordított proxyn és a második csatornán, amely a sikeres és a sikertelen kérelmek bejegyzéseihez tartalmaz részletes eseményeket.

A [fordított proxyval kapcsolatos események](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) összegyűjtéséhez tekintse át a helyi és az Azure Service Fabric-fürtökben lévő események összegyűjtésének engedélyezését.

## <a name="troubleshoot-using-diagnostics-logs"></a>Hibakeresés diagnosztikai naplók használatával
Íme néhány példa arra, hogyan lehet értelmezni a gyakori meghibásodási naplókat, amelyeket az egyik találkozhat:

1. A fordított proxy visszaadja a válasz állapotkódot (504) (időtúllépés).

    Ennek oka az lehet, hogy a szolgáltatás nem válaszol a kérés időkorlátján belül.
   Az alábbi első esemény naplózza a fordított proxyn fogadott kérelem részleteit. 
   A második esemény azt jelzi, hogy a kérés meghiúsult a szolgáltatásra való továbbítás során, a "belső hiba = ERROR_WINHTTP_TIMEOUT" miatt 

    A hasznos adatok a következők:

   * **traceId**: Ez a GUID az egyetlen kérelemnek megfelelő összes esemény összekapcsolására használható. Az alábbi két eseményben a traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, ami azt jelenti, hogy ugyanahhoz a kéréshez tartoznak.
   * **requestUrl**: a kérés elküldésének URL-címe (fordított proxy URL-címe).
   * **művelet**: http-művelet.
   * **remoteAddress**: a kérést küldő ügyfél címe.
   * **resolvedServiceUrl**: a szolgáltatás végpontjának URL-címe, amelyhez a bejövő kérelem meg lett oldva. 
   * **errorDetails**: További információ a hibáról.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. A fordított proxy visszaadja a 404-es válasz-állapotkódot (nem található). 
    
    Itt látható egy példa arra az esetre, amikor a fordított proxy a 404 értéket adja vissza, mert nem találta meg a megfelelő szolgáltatási végpontot.
    A hasznos adattartalom-bejegyzések a következők:
   * **processRequestPhase**: azt jelzi, hogy a kérelem feldolgozása során milyen fázisban történt a hiba, a **_TryGetEndpoint_** , azaz a szolgáltatás végpontjának beolvasására irányuló kísérlet során. 
   * **errorDetails**: a végpontok keresési feltételeit sorolja fel. Itt láthatja, hogy a megadott listenerName = **FrontEndListener** , míg a replika-végpontok listája csak a **OldListener** nevű figyelőt tartalmazza.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Egy másik példa, ahol a fordított proxy a 404 nem található értéket adja vissza: a ApplicationGateway\Http konfigurációs paraméter értéke TRUE (igaz), a fordított proxy pedig a **https** **-t figyeli** , azonban az összes replika-végpont nem biztonságos (a http-t figyeli).
     A fordított proxy a 404 értéket adja vissza, mert nem talál a HTTPS-t figyelő végpontot a kérelem továbbítására. Az esemény-adattartalomban található paraméterek elemzése segít a probléma szűkítéséhez:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. A fordított proxyra irányuló kérelem időtúllépési hiba miatt meghiúsul. 
    Az eseménynaplók olyan eseményt tartalmaznak, amely tartalmazza a kapott kérelem részleteit (itt nem látható).
    A következő esemény azt mutatja, hogy a szolgáltatás egy 404-es állapotkód és fordított proxy miatt válaszol egy ismételt feloldásra. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Az összes esemény összegyűjtésekor megjelenik egy esemény, amely minden feloldási és továbbítási kísérletet megjelenít.
    Az adatsorozat utolsó eseménye azt mutatja, hogy a kérelmek feldolgozása időtúllépéssel meghiúsult, valamint a sikeres feloldási kísérletek száma.
    
    > [!NOTE]
    > Javasoljuk, hogy a részletes csatorna-események gyűjtését alapértelmezés szerint letiltsa, és szükség esetén engedélyezze a hibaelhárítást.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Ha a gyűjtemény csak a kritikus/hibák eseményeire van engedélyezve, akkor egy esemény jelenik meg, amely részletesen ismerteti az időtúllépést és a feloldási kísérletek számát. 
    
    Azok a szolgáltatások, amelyek 404 állapotkódot küldenek vissza a felhasználónak, adjon hozzá egy "X-ServiceFabric" fejlécet a válaszban. Miután hozzáadta a fejlécet a válaszhoz, a fordított proxy visszaküldi az állapotkódot az ügyfélnek.  

4. Olyan esetek, amikor az ügyfél leválasztta a kérést.

    A következő esemény akkor kerül rögzítésre, ha a fordított proxy továbbítja a választ az ügyfélnek, de az ügyfél bontja a kapcsolatot:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. A fordított proxy visszaadja a 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST hiba történik, ha a szolgáltatási jegyzékfájlban nincs megadva URI-séma a szolgáltatási végponthoz.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    A probléma megoldásához az URI-sémát a jegyzékfájlban kell megadni.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> A WebSocket-kérelmek feldolgozásával kapcsolatos események jelenleg nincsenek naplózva. Ez a következő kiadásban lesz hozzáadva.

## <a name="next-steps"></a>Következő lépések
* [Események összesítése és gyűjtése a Windows Azure Diagnostics használatával](service-fabric-diagnostics-event-aggregation-wad.md) a naplók Azure-fürtökön való engedélyezéséhez.
* A Visual Studióban Service Fabric események megtekintéséhez lásd: [helyi figyelés és Diagnosztizálás](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Tekintse meg a [fordított proxy konfigurálása a biztonságos szolgáltatások](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) Azure Resource Manager-sablonokhoz való csatlakozáshoz című témakört a biztonságos fordított proxy konfigurálásához a különböző szolgáltatás-tanúsítvány ellenőrzési lehetőségeivel.
* További információért olvassa el [Service Fabric fordított proxyt](service-fabric-reverseproxy.md) .
