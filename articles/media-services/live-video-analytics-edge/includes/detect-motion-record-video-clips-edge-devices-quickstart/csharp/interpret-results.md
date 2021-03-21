---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88682120"
---
A Media Graph futtatásakor a mozgásérzékelő processzor csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A Visual Studio Code **kimenet** ablakában látható üzenetek egy `body` szakaszt és egy `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Az előző kimenetben: 

* Az üzenet egy diagnosztikai esemény `MediaSessionEstablished` . Azt jelzi, hogy az RTSP-forrás csomópontja (a tárgy) létrehozta a kapcsolódást az RTSP-szimulátorral, és megkezdte a (szimulált) élő adatcsatorna fogadását.
* A alkalmazásban a `applicationProperties` `subject` diagram topológiájában lévő csomópontra hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* A `body` szakasz a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Ha a rendszer mozgást észlel, a rendszer aktiválta a Signal Gate processzor csomópontját, és a Media Graph fájl fogadó csomópontja az MP4-fájl írására indul. A file mosogató csomópont egy operatív eseményt küld. A értéke úgy `type` van beállítva, hogy `motion` jelezze, hogy a mozgásészlelési processzor eredménye. Az `eventTime` érték az az UTC-idő, amikor a mozgás bekövetkezett. A folyamattal kapcsolatos további információkért tekintse meg a rövid útmutató [Áttekintés](#overview) című szakaszát.

Íme egy példa erre az üzenetre:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

Az előző üzenetben: 

* A alkalmazásban a a `applicationProperties` `subject` Media Graph azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet a file mosogató csomópontból származik.
* A ben a `applicationProperties` `eventType` azt jelzi, hogy ez az esemény működőképes.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett. Ez az idő 5 – 6 másodperc, amely után a `MediaSessionEstablished` videó elkezd áramlani. Ez az idő az 5 – 6 másodperces jelzésnek felel meg, amikor az [autó elindult](#review-the-sample-video) a parkolóba.
* A `body` szakasz az operatív eseményre vonatkozó információkat tartalmaz. Ebben az esetben az adathalmaz és a `outputType` `outputLocation` .
* A `outputType` változó azt jelzi, hogy ez az információ a fájl elérési útjáról szól.
* Az `outputLocation` érték az MP4-fájl helye a peremhálózati modulban.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped és RecordingAvailable események

Ha megvizsgálja a Signal Gate processzor csomópontjának tulajdonságait a [Graph-topológiában](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), láthatja, hogy az aktiválási idő 5 másodpercre van állítva. Az esemény fogadását követően 5 másodperccel a `RecordingStarted` következőket kapja:

* Egy `RecordingStopped` esemény, amely azt jelzi, hogy a rögzítés leállt.
* Egy `RecordingAvailable` esemény, amely azt jelzi, hogy az MP4-fájl már használható megtekintésre.

A két eseményt általában másodpercek alatt bocsátják ki.
