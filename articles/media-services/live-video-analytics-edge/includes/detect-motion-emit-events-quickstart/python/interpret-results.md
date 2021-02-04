---
ms.openlocfilehash: 8bcef40dad9c67e9e2c6d6c4a051045999487027
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531008"
---
A Media Graph futtatásakor a mozgásérzékelő processzor csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A Visual Studio Code **kimenet** ablakában látható üzenetek egy `body` szakaszt és egy `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-04-09T16:42:18.1280000Z"  
    }  
}
```

Az előző kimenetben: 

* Az üzenet egy diagnosztikai esemény `MediaSessionEstablished` . Azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A alkalmazásban a a `applicationProperties` `subject` Graph-topológiában található csomópontra hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* Az `eventTime` érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* A `body` szakasz a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="motiondetection-event"></a>MotionDetection esemény

Ha a rendszer mozgást észlel, az élő videó Analytics IoT Edge modulban egy következtetési eseményt küld. A értéke úgy `type` van beállítva, hogy `motion` jelezze, hogy a mozgásészlelési processzor eredménye. Az `eventTime` érték azt jelzi, hogy mikor (UTC) történt a mozgás. 

Íme egy példa erre az üzenetre:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  } 
}  
```

Ebben a példában: 

* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* Az `body` érték az elemzési eseményre vonatkozó információ. Ebben az esetben az esemény egy következtetési esemény, így a törzs tartalmaz és az adattartalmakat `timestamp` `inferences` .
* Az `inferences` érték azt jelzi, hogy a a `type` `motion` . További információkat tartalmaz az `motion` eseményről.
* A `box` szakasz a mozgó objektum körüli határoló mező koordinátáit tartalmazza. Az értékeket a videó szélessége és magassága (képpontban) normalizálja. Például a szélesség 1920, a magasság pedig 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
