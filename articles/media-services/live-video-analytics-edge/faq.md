---
title: Élő videó-elemzés IoT Edge GYIK-ban – Azure
description: Ez a cikk a IoT Edge élő videó-elemzésekkel kapcsolatos gyakori kérdésekre ad választ.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 0cb378bf614582070dd1bdd0a11706b26437af53
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880050"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Élő video Analytics IoT Edge GYIK

Ez a cikk a Azure IoT Edge élő videó-elemzésekkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

**Milyen rendszerváltozókat használhatok a Graph topológia definíciójában?**

| Változó   |  Leírás  | 
| --- | --- | 
| [System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | UTC-idő szerint egy pillanatot jelöl, amely általában a nap dátumának és időpontjának a következő formátumban van kifejezve:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Egy egyezményes világidő (UTC) szerinti időpontot jelöl, amely egy ISO8601-fájlnak megfelelő formátumú, ezredmásodpercben megadva, a következő formátumban:<br>*yyyyMMddTHHmmss. fffZ* | 
| System. GraphTopologyName   | Egy Media Graph-topológiát jelöl, és egy gráf tervrajzát tárolja. | 
| System. GraphInstanceName |    Egy Media Graph-példányt jelöl, a paraméter értékeit tartalmazza, és hivatkozik a topológiára. | 

## <a name="configuration-and-deployment"></a>Konfigurálás és üzembe helyezés

**Telepíthetem a Media Edge-modult egy Windows 10-es eszközre?**

Igen. További információ: Linux- [tárolók a Windows 10 rendszerben](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Rögzítés az IP-kamera és az RTSP-beállítások alapján

**Speciális SDK-t kell használnom az eszközömön videóstream küldéséhez?**

Nem, a IoT Edge Live Video Analytics támogatja az adathordozók rögzítését az RTSP (valós idejű Streaming Protocol) használatával a video streaming számára, amely a legtöbb IP-kamera esetében támogatott.

**Leküldhetek médiatartalmakat a IoT Edge Real-Time üzenetküldési protokoll (RTMP) vagy Smooth Streaming protokoll (például egy Media Services élő esemény) használatával?**

Nem, a Live Video Analytics csak az RTSP-t támogatja a videók IP-kamerákból való rögzítéséhez. Minden olyan kamera, amely támogatja a TCP/HTTP protokollon keresztüli RTSP-átvitelt, működnie kell. 

**Alaphelyzetbe állíthatom vagy frissíthetem az RTSP-forrás URL-címét egy Graph-példányban?**

Igen, ha a Graph-példány *inaktív* állapotban van.  

**Használható-e egy RTSP-szimulátor a tesztelés és a fejlesztés során?**

Igen, a gyors útmutatókban és oktatóanyagokban a tanulási folyamat támogatásához használható egy [RTSP szimulátor](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-modul. Ez a modul a legjobb megoldás, és lehet, hogy nem mindig érhető el. Javasoljuk, hogy a szimulátort *ne* használja több órán át. Az éles üzembe helyezés tervezése előtt a tényleges RTSP-forrással kell befektetni a tesztelésbe.

**Támogatott az IP-kamerák ONVIF-felderítése a peremhálózaton?**

Nem, nem támogatjuk az Open Network Video Interface Forum (ONVIF)-felderítést a peremhálózati eszközökön.

## <a name="streaming-and-playback"></a>Folyamatos átvitel és lejátszás

**Lejátszható a szélétől Azure Media Services rögzített eszközök az olyan streaming-technológiák használatával, mint a HLS vagy a DASH?**

Igen. A rögzített eszközöket, például a Azure Media Services bármely más eszközét továbbíthatja. A tartalom továbbításához létre kell hoznia egy folyamatos átviteli végpontot és a futó állapotot. A standard szintű folyamatos átviteli lokátor létrehozási folyamata hozzáférést biztosít egy Apple HTTP Live Streaming (HLS) vagy dinamikus adaptív streaminghez HTTP-n keresztül (kötőjel, más néven MPEG-DASH). További információ a HLS és a DASH-jegyzékek létrehozásáról és közzétételéről: [dinamikus csomagolás](../latest/dynamic-packaging-overview.md).

**Használhatom a Media Services szabványos tartalomvédelem és DRM funkcióit archivált eszközön?**

Igen. A szabványos dinamikus titkosítású tartalomvédelem és a digitális jogkezelési (DRM) funkciók a Media Graph-ból rögzített eszközökön használhatók.

**Milyen játékosokat használhatok a rögzített eszközök tartalmának megtekintésére?**

A megfelelő HLS 3-as vagy 4-es verziójú szabványt támogató szabványos lejátszók támogatottak. Emellett minden olyan lejátszó is támogatott, amely kompatibilis MPEG-DASH-lejátszásra alkalmas.

A teszteléshez ajánlott játékosok a következők:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [A-lejátszó](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Edge, Chrome vagy Safari beépített HTML5-videolejátszó
* HLS vagy DASH lejátszást támogató kereskedelmi játékosok

**Milyen korlátozások vonatkoznak a Media Graph-eszközök továbbítására?**

A Media Graph élő vagy rögzített eszközének adatfolyam-továbbítása ugyanazt a nagy léptékű infrastruktúrát és folyamatos átviteli végpontot használja, amely Media Services támogatja az igény szerinti és élő adatfolyam-továbbítást a Media & Entertainment, a felső (OTT) és a közvetített ügyfelek számára. Ez azt jelenti, hogy gyorsan és egyszerűen engedélyezheti az Azure Content Delivery Network, a Verizon vagy a Akamai számára, hogy a tartalmat egy közönség számára kis méretekben, vagy akár millióknak is továbbítsa, a forgatókönyvtől függően.

Tartalmat az Apple HLS vagy az MPEG-DASH használatával is továbbíthat.

## <a name="design-your-ai-model"></a>AI-modell megtervezése 

**Több AI-modell van becsomagolva egy Docker-tárolóba. Hogyan használhatom őket az élő videó Analytics szolgáltatással?** 

A megoldások attól függően változnak, hogy milyen kommunikációs protokollt használ a következtetési kiszolgáló az élő videó-elemzésekkel való kommunikációhoz. A következő szakaszok ismertetik, hogyan működnek az egyes protokollok.

*Http protokoll használata*:

* Egyetlen tároló (egyetlen lvaExtension):  

   A következtetésben álló kiszolgálón egyetlen portot, de különböző AI-modellekhez tartozó végpontokat is használhat. Egy Python-minta esetében például használhat különböző `route` s/modelleket is, ahogy az itt látható: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Majd az élő videó elemzési üzembe helyezése során, amikor gráfokat hoz létre, állítsa be a következtetési kiszolgáló URL-címét az egyes példányok esetében, ahogy az itt látható: 

   1. példány: következtetés a kiszolgáló URL-címe =`http://lvaExtension:44000/score/face_detection`<br/>
   második példány: következtetés a kiszolgáló URL-címe =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Azt is megteheti, hogy az AI-modelleket különböző portokon teszi elérhetővé, és a gráfok létrehozásakor meghívja őket.  

* Több tároló: 

   Minden tároló egy másik névvel van telepítve. Korábban a Live Video Analytics dokumentációs készletében bemutatjuk, hogyan helyezhet üzembe egy *lvaExtension* nevű bővítményt. Most létrehozhat két különböző tárolót, amelyek mindegyike ugyanazzal a HTTP-felülettel rendelkezik, ami azt jelenti, hogy ugyanazt a `/score` végpontot használják. Telepítse a két tárolót különböző nevekkel, és győződjön meg arról, hogy mindkettő figyeli a *különböző portokat*. 

   Például egy nevű tároló `lvaExtension1` figyeli a portot `44000` , és egy nevű második tároló `lvaExtension2` figyeli a portot `44001` . 

   Az élő videó-elemzési topológiában két gráfot hoz létre különböző URL-címekkel, ahogy az itt látható: 

   Első példány: következtetés a kiszolgáló URL-címe = `http://lvaExtension1:44001/score`    
   Második példány: következtetési kiszolgáló URL-címe = `http://lvaExtension2:44001/score`
   
*Használja az gRPC protokollt*: 

* Az 1,0-as Live Video Analytics-modul használata esetén, ha általános célú távoli eljáráshívás (gRPC) protokollt használ, az egyetlen módszer erre, ha a gRPC-kiszolgáló különböző AI-modelleket tesz elérhetővé különböző portokon keresztül. [Ebben a kódban például](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)egy 44000-es port teszi elérhetővé az összes Yolo-modellt. Elméletileg a Yolo gRPC-kiszolgáló újraírható, hogy a 44000-es porton és másokon is elérhetők legyenek egyes modellek a 45000-es porton. 

* Az 2,0-es Live Video Analytics-modul használatával új tulajdonságot ad hozzá a gRPC-bővítmény csomóponthoz. Ez a tulajdonság, a **extensionConfiguration** egy opcionális karakterlánc, amelyet a gRPC-szerződés részeként használhat. Ha egyetlen következtetési kiszolgálón több AI-modell van csomagolva, az összes AI-modellhez nem szükséges csomópontot kitenni. Ehelyett a Graph-példányok esetében a **extensionConfiguration** tulajdonsággal határozhatja meg, hogyan válassza ki a különböző ai-modelleket. A végrehajtás során a Live Video Analytics átadja ezt a karakterláncot a következtetésben lévő kiszolgálónak, amely a kívánt AI-modell meghívására használható. 

**GRPC-kiszolgálót dolgozok fel egy AI-modell körül, és szeretném támogatni a használatát több kamera vagy gráf példány használatával. Hogyan kell létrehozni a kiszolgálót?** 

 Először is győződjön meg arról, hogy a kiszolgáló egyszerre egynél több kérést tud kezelni, vagy párhuzamos szálban dolgozhat. 

A párhuzamos csatornák alapértelmezett száma például a következő [élő videó Analytics gRPC-mintában](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)van beállítva: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

A gRPC-kiszolgáló előző példányában a kiszolgáló csak három csatornát nyithat meg kamerán vagy Graph-topológiai példányon. Ne próbáljon háromnál több példányt csatlakozni a kiszolgálóhoz. Ha több mint három csatornát próbál megnyitni, a kérelmek mindaddig függőben lesznek, amíg egy meglévő csatorna nem csökken.  

A korábbi gRPC-kiszolgáló implementációja a Python-mintákban van használatban. Fejlesztőként implementálhatja saját kiszolgálóját, vagy az előző alapértelmezett implementáció használatával növelheti a munkavégző számot, amelyet a videós hírcsatornák számára használt kamerák számára lehet beállítani. 

Több kamera beállításához és használatához több gráf-topológiát is létrehozhat, amelyek mindegyike ugyanahhoz vagy egy másik következtetési kiszolgálóhoz mutat (például az előző bekezdésben említett kiszolgáló). 

**Több képkockát szeretnék fogadni a felsőbb rétegből, mielőtt következtetéseket hozna. Hogyan engedélyezhető a szolgáltatás?** 

Az aktuális [alapértelmezett minták](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) *állapot nélküli* módban működnek. Nem őrzik meg az előző hívások állapotát, vagy még a meghívottak is. Ez azt jelenti, hogy több topológiai példány is meghívhatja ugyanazt a következtetési kiszolgálót, de a kiszolgáló nem tudja megkülönböztetni, hogy ki hívja meg a hívót vagy az állapotot. 

*Http protokoll használata*:

Ha meg szeretné őrizni az állapotot, minden hívó vagy gráf topológiai példány meghívja a hivatkozó kiszolgálót a hívó számára egyedi HTTP lekérdezési paraméterrel. Az egyes példányok esetében például a következtetési kiszolgáló URL-címei láthatók itt:  

1. topológiai példány = `http://lvaExtension:44000/score?id=1`<br/>
második topológiai példány = `http://lvaExtension:44000/score?id=2`

… 

A pontszám útvonala a kiszolgáló oldalon tudja, hogy ki hívja meg. Ha az ID = 1, akkor az a hívó vagy a Graph topológiai példány esetében külön-külön is megtarthatja az állapotot. Ezután megtarthatja a kapott képkockákat a pufferben. Például használhat egy tömböt vagy egy DateTime kulccsal rendelkező szótárt, és az érték a keret. Ezután megadhatja a kiszolgálót a feldolgozáshoz (következtetve), miután *x* számú keretet fogad. 

*Használja az gRPC protokollt*: 

A gRPC-bővítmények mindegyike egyetlen kamerás hírcsatornára vonatkozik, így nincs szükség az azonosító megadására. A extensionConfiguration tulajdonsággal a képkockákat egy pufferben tárolhatja, és meghatározhatja a feldolgozni kívánt kiszolgálót (a következtetést) a keretek *x* számának fogadása után. 

**Egy adott tároló összes ProcessMediaStreams ugyanazt a AI-modellt futtatja?** 

Nem. A végponton kezdeményezett hívások elindítása vagy leállítása egy gráf-példányban, vagy egy kamera leválasztása vagy újracsatlakozása lehet. A cél egy munkamenet fenntartása, ha a kamera streaming videó. 

* A videók feldolgozására két kamera hoz létre két munkamenetet. 
* Egy kamera egy olyan gráfra mutat, amely két gRPC-kiterjesztési csomóponttal két munkamenetet hoz létre. 

Minden munkamenet egy teljes kétirányú kapcsolat az élő videó-elemzés és a gRPC-kiszolgáló között, és mindegyik munkamenet rendelkezhet más modellel vagy folyamattal is. 

> [!NOTE]
> Egy kamera leválasztása vagy újracsatlakozása esetén, ha a kamera a tűréshatáron túli időtartamon keresztül offline állapotba kerül, a Live Video Analytics egy új munkamenetet nyit meg a gRPC-kiszolgálóval. A kiszolgáló nem követeli meg az állapot nyomon követését ezen munkamenetek között. 

Az élő videó Analytics több gRPC-bővítmény támogatását is támogatja egyetlen kamerához egy gráf-példányban. Ezekkel a gRPC-bővítményekkel szekvenciálisan, párhuzamosan, vagy mindkettő kombinációjában végezheti el az AI-feldolgozást. 

> [!NOTE]
> Ha több bővítmény fut párhuzamosan, az hatással lesz a hardveres erőforrásokra. Tartsa szem előtt, hogy a számítási igényeknek megfelelő hardvert választotta. 

**Mi az egyidejű ProcessMediaStreams maximális száma?** 

Az élő videó Analytics nem korlátozza ezt a számot.  

**Hogyan határozható meg, hogy a hivatkozó kiszolgáló a CPU-t vagy a GPU-t vagy bármely más hardveres gyorssegédet használ-e?** 

Döntése a fejlett AI-modell összetettségének és a processzor és a hardveres gyorssegédek használatának módjától függ. Ahogy fejleszti az AI-modellt, megadhatja, hogy a modell milyen erőforrásokat használjon, és milyen műveleteket kell végrehajtania. 

**Hogyan a képek tárolása a határolókeret feldolgozását követően?** 

Jelenleg a határolókeret koordinátáit biztosítjuk, csak a következtetési üzenetekben. Létrehozhat egy egyéni MJPEG-streamet, amely használhatja ezeket az üzeneteket, és átfedi a határoló mezőket a videó kereteken.  

## <a name="grpc-compatibility"></a>gRPC kompatibilitása 

**Honnan tudhatom meg, hogy a Media stream-leíró kötelező mezői milyenek?** 

Bármely olyan mező, amelyhez nem ad meg értéket [, a gRPC által megadott alapértelmezett értéket](https://developers.google.com/protocol-buffers/docs/proto3#default)kapja meg.  

Az élő videó Analytics a protokoll puffer nyelvének *proto3* verzióját használja. Az élő videó-elemzési szerződések által használt összes protokoll-puffer-érték elérhető a [protokoll-puffer fájljaiban](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Hogyan biztosíthatom, hogy a legfrissebb protokoll-puffer-fájlokat használom?** 

A legfrissebb protokoll-puffer fájlok a [szerződések fájljainak webhelyéről](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)szerezhetők be. Amikor frissítjük a szerződés fájljait, ezen a helyen lesznek elérhetők. Nincs azonnali terv a protokoll fájljainak frissítésére, ezért a fájlok tetején keresse meg a csomag nevét, és Ismerje meg a verziót. A következőket kell elolvasni: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

A fájlok frissítései a név végén megnövelik a "v-Value" értéket. 

> [!NOTE]
> Mivel az élő video Analytics a nyelv proto3 verzióját használja, a mezők nem kötelezőek, és a verzió visszafelé és továbbítva kompatibilis. 

**Milyen gRPC funkciók érhetők el az élő videó Analytics használatával? Mely funkciók kötelezőek, és melyek nem kötelezőek?** 

Bármely kiszolgálóoldali gRPC funkciót használhat, ha a protokoll-pufferek (Protopuf) egyezmény teljesül. 

## <a name="monitoring-and-metrics"></a>Monitorozás és mérőszámok

**Nyomon követhető a peremhálózati adathordozó-gráf a Azure Event Grid használatával?**

Igen. A Prometheus-mérőszámokat felhasználhatja, és közzéteheti őket az Event Gridben. 

**Használhatom Azure Monitor a saját adathordozó-diagramok állapotának, metrikáinak és teljesítményének megtekintéséhez a felhőben vagy a peremen?**

Igen, támogatjuk ezt a megközelítést. További információ: [Azure monitor mérőszámok áttekintése](../../azure-monitor/platform/data-platform-metrics.md).

**Vannak olyan eszközök, amelyek megkönnyítik a Media Services IoT Edge modul figyelését?**

A Visual Studio Code támogatja az Azure IoT Tools bővítményt, amellyel könnyedén figyelheti a LVAEdge modul végpontját. Ezzel az eszközzel gyorsan megkezdheti a IoT hub beépített végpontjának figyelését az "Events" kifejezésre, és megtekintheti a peremhálózati eszközről a felhőbe irányított következtetési üzeneteket. 

Ezen felül a bővítmény használatával szerkesztheti a LVAEdge modulhoz tartozó Twin modult a Media Graph beállításainak módosításához.

További információ: [figyelés és naplózás](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Számlázás és rendelkezésre állás

**Hogyan történik a Live Video Analytics IoT Edge számlázása?**

A számlázási részleteket lásd: [Media Services díjszabása](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>További lépések

[Gyors útmutató: az élő videó Analytics használatának első lépései IoT Edge](get-started-detect-motion-emit-events-quickstart.md)