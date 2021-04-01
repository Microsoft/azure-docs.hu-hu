---
ms.openlocfilehash: fdc7360911e37babdb830b7d67ad7224ea84c774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92038525"
---
A Media Graph futtatásakor a HTTP-bővítmény processzor-csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A **kimeneti** ablakban látható üzenetek törzs szakaszt és applicationProperties szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva. Az esemény típusa: `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
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

Ebben az üzenetben a következő információkat láthatja:
* Az üzenet egy diagnosztikai esemény. `MediaSessionEstablished` azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A alkalmazásban a `applicationProperties` Tárgy azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* A `eventTime` jelzi az esemény időpontját.
* A törzs a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="inference-event"></a>Következtetési esemény

A gRPC-bővítmény processzor-csomópontja a lvaExtension modulból származó következtetéseket fogad. Ezután az eredményeket a IoT Hub fogadó csomóponton keresztül származtatott eseményként bocsátja ki.
Ezekben az eseményekben a típus entitásra van beállítva, amely azt jelzi, hogy egy entitás, például egy autó vagy egy teherautó. Az `eventTime` érték az a UTC-idő, amikor az objektum észlelve lett.
A következő példában három autót észlelt ugyanabban a videóban, különböző megbízhatósági szinttel.

```
[IoTHubMonitor] [7:52:57 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143802500954716,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.86707145
          },
          "attributes": [],
          "box": {
            "l": 0.7294476,
            "t": 0.567829,
            "w": 0.031738576,
            "h": 0.027762715
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.8634398
          },
          "attributes": [],
          "box": {
            "l": 0.4765757,
            "t": 0.59559196,
            "w": 0.05597749,
            "h": 0.048316106
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.67120105
          },
          "attributes": [],
          "box": {
            "l": 0.7247387,
            "t": 0.49816906,
            "w": 0.032748587,
            "h": 0.030686663
          }
        },
        "extensions": {},
        "valueCase": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/grpcExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-19T02:52:57.174Z",
    "dataVersion": "1.0"
  }
}
```

Az üzenetekben figyelje meg a következő adatokat:

* A alkalmazásban a `applicationProperties` tulajdonos a Graph-topológia azon csomópontjára hivatkozik, amelyről az üzenet létrejött.
* A-ben a `applicationProperties` EventType azt jelzi, hogy ez az esemény egy elemzési esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* A `body` szakasz az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, így a törzs következtetéseket tartalmaz.
* A `inferences` szakasz azt jelzi, hogy a típus entitás. Ez a szakasz az entitással kapcsolatos további információkat tartalmaz.