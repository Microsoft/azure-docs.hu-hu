---
title: Media Graph-koncepció – Azure
description: A Media Graph lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. Ez a cikk részletes leírást nyújt a Media Graph-koncepcióról.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: ad23acbbbdd0c15e92e471ee22a229470a8a3a75
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557666"
---
# <a name="media-graph"></a>Médiagrafikon

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)

## <a name="overview"></a>Áttekintés

A Media Graph lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. Ezt úgy hajthatja végre, hogy a kívánt módon csatlakoztatja az összetevőket vagy a csomópontokat. Az alábbi ábra egy Media Graph grafikus ábrázolását mutatja be.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Médiagrafikon":::

A IoT Edge élő videó-elemzések támogatják a különböző típusú forrásokat, processzorokat és mosogatókat.

* A **forrásoldali csomópontok** lehetővé teszik az adathordozók rögzítését a Media Graphba. Ebben a kontextusban az adathordozók, például a hangadatfolyamok, a video streamek, az adatfolyamok, valamint a hang-, videó-és/vagy adatstreamek együttese egyetlen streamben.
* A **processzor csomópontjai** lehetővé teszik az adathordozók feldolgozását az adathordozó-gráfon belül.
* A fogadó **csomópontok** lehetővé teszik a feldolgozási eredmények kézbesítését a Media graphon kívüli szolgáltatásokhoz és alkalmazásokhoz.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topológiák és-példányok 

A IoT Edge élő videó-elemzések lehetővé teszik az adathordozó-diagramok kezelését két fogalommal – "gráf-topológia" és "Graph-példány". A Graph-topológia lehetővé teszi egy gráf tervének meghatározását, amely a paramétereket helyőrzőként adja meg az értékekhez. A topológia határozza meg, hogy milyen csomópontok használatosak a Media Graphban, és hogy azok hogyan kapcsolódnak a Media Graph-on belül. Ha például egy kamerából szeretné rögzíteni a hírcsatornát, szüksége lesz egy gráfra egy olyan forrás-csomóponttal, amely videót kap, valamint egy fogadó csomópontot, amely beírja a videót.

A topológia paramétereinek értékei a topológiára hivatkozó gráf-példányok létrehozásakor vannak megadva. Ez lehetővé teszi, hogy több példányt is hozzon létre ugyanarra a topológiára, de a topológiában megadott paraméterek eltérő értékeivel. A fenti példában használhat paramétereket a kamera IP-címének és a rögzített videó nevének a megjelenítéséhez. Több gráf-példányt is létrehozhat, ezzel a topológiával – egy példányban az egyes kamerákhoz egy adott épületben, lehet, hogy mindegyik az adott IP-címmel és adott névvel rendelkezik.

## <a name="media-graph-states"></a>Media Graph-állapotok  

A Graph-topológiák és a Graph-példányok életciklusa a következő állapot ábrán látható.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Gráf-topológia és gráf-példány életciklusa":::

Első lépésként [hozzon létre egy gráf-topológiát](direct-methods.md#graphtopologyset). Ezután minden olyan élő videó-hírcsatorna esetében, amelyet fel szeretne dolgozni ezzel a topológiával, [létrehoz egy gráf-példányt](direct-methods.md#graphinstanceset). 

A Graph-példány `Inactive` (inaktív) állapotban lesz.

Ha készen áll az élő videó-hírcsatornának a Graph-példányba való küldésére, [aktiválja](direct-methods.md#graphinstanceactivate) azt. A Graph-példány rövid időre átvált egy átmeneti `Activating` állapotba, és ha ez sikeres, akkor lépjen be egy `Active` állapotba. Az `Active` állapotban a rendszer feldolgozza az adathordozót (ha a Graph-példány bemeneti adatokat kap).

> [!NOTE]
>  Egy gráf-példány aktív lehet anélkül, hogy átáramlik rajta (például a kamera offline állapotba kerül).
> Az Azure-előfizetés számlázása akkor történik meg, amikor a Graph-példány aktív állapotban van.

Megismételheti a más gráf-példányok létrehozásának és aktiválásának folyamatát ugyanarra a topológiára vonatkozóan, ha más élő videó-hírcsatornákkal dolgoz fel.

Ha elkészült az élő videó hírcsatornájának feldolgozásával, [inaktiválja](direct-methods.md#graphinstancedeactivate) a Graph-példányt. A Graph-példány rövid időre átvált egy átmeneti `Deactivating` állapotba, kiüríti a benne lévő összes adatsort, majd visszatér az `Inactive` állapotba.

Csak akkor [törölhet](direct-methods.md#graphinstancedelete) egy gráf-példányt, ha az állapotban van `Inactive` .

Miután az adott gráf-topológiára hivatkozó összes gráf-példány törölve lett, [törölheti a Graph-topológiát](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Források, processzorok és mosogatók  

A IoT Edge Live Video Analytics a következő típusú csomópontokat támogatja a Media graphon belül:

### <a name="sources"></a>Források 

#### <a name="rtsp-source"></a>RTSP-forrás 

Az RTSP-forrás csomópont lehetővé teszi az adathordozók betöltését egy [RTSP](https://tools.ietf.org/html/rfc2326) -kiszolgálóról. A megfigyelési és IP-alapú kamerák az adatokat egy RTSP (valós idejű Streaming-Protocol) protokollon keresztül továbbítják, amely eltér a más típusú eszközöktől, például a telefonoktól és a videokameráktól. Ez a protokoll a-kiszolgáló (a kamera) és az ügyfél közötti adathordozó-munkamenetek létrehozásához és szabályozásához használatos. A Media Graph RTSP-forrás csomópontja ügyfélként működik, és egy RTSP-kiszolgálóval létesített munkamenetet tud létesíteni. Számos eszköz, például a legtöbb [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) beépített RTSP-kiszolgálóval rendelkezik. A [ONVIF](https://www.onvif.org/) megbízza az RTSP-t, hogy az a [G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) szabványnak megfelelő eszközök definíciójában legyen támogatva. Az RTSP-forrás csomóponthoz meg kell adnia egy RTSP URL-címet, valamint a hitelesített kapcsolat engedélyezéséhez szükséges hitelesítő adatokat.

#### <a name="iot-hub-message-source"></a>IoT Hub üzenet forrása 

A többi [IoT Edge-modulhoz](../../iot-fundamentals/iot-glossary.md#iot-edge)hasonlóan az élő videó Analytics IoT Edge modulban az [IoT Edge hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)használatával fogadhat üzeneteket. Ezek az üzenetek elküldhetők más modulokból, vagy a peremhálózati eszközön futó alkalmazásokból, illetve a felhőből is. Az ilyen üzenetek továbbítása (átirányítva) egy [elnevezett bemenetre](../../iot-edge/module-composition.md#sink) történik a modulban. Egy IoT Hub Message Source csomópont lehetővé teszi, hogy az ilyen üzenetek elérjék a Media Graphot. Ezek az üzenetek vagy jelek ezután a Media Graph-ban belső használatra használhatók, jellemzően a Signal Gates aktiválásához (lásd a [Signal Gates](#signal-gate-processor) szakaszt alább). 

Rendelkezhet például egy olyan IoT Edge modullal, amely egy ajtó megnyitásakor üzenetet hoz létre. A modulból érkező üzenet átirányítható IoT Edge központba, ahonnan továbbítható a Media Graph IoT hub-üzenet forrása. A Media Graphban az IoT hub-üzenet forrása továbbíthatja az eseményt egy Signal Gate-feldolgozónak, amely Ezután bekapcsolhatja a videó rögzítését egy RTSP-forrásból egy fájlba. 

### <a name="processors"></a>Processzorok  

#### <a name="motion-detection-processor"></a>Mozgásészlelési processzor 

A mozgásészlelési processzor csomópont lehetővé teszi az élő videókon való mozgás észlelését. Megvizsgálja a beérkező képkockákat, és meghatározza, hogy van-e mozgás a videóban. Ha a rendszer mozgást észlel, a videó kereteit átadja az alsóbb rétegbeli összetevőnek, és egy eseményt bocsát ki. A mozgásérzékelő processzor csomópontja (más csomópontokkal együtt) felhasználható a bejövő videó rögzítésének indítására, amikor a rendszer mozgást észlelt.

#### <a name="frame-rate-filter-processor"></a>Frame rate Filter processzor  

A frame rate szűrő processzor csomópontja lehetővé teszi, hogy a beérkező videó streamből egy megadott arányban mintákat lehessen mintát venni. Ez lehetővé teszi, hogy csökkentse a lefelé stream-összetevőkre (például a HTTP-bővítmény processzor-csomópontjára) küldött keretek számát a további feldolgozás érdekében.
>[!WARNING]
> Ez a processzor **elavult** az élő videó Analytics legújabb kiadásában IoT Edge modulban. A frame rate Management mostantól a Graph Extension processors szolgáltatásban is támogatott.

#### <a name="http-extension-processor"></a>HTTP-bővítmény processzora

A HTTP-bővítmény processzor-csomópontja lehetővé teszi saját IoT Edge moduljának összekapcsolását egy adathordozó-gráfhoz. Ez a csomópont dekódolja a képkockákat bemenetként, és az ilyen képkockákat a modul által elérhető HTTP REST-végpontra továbbítja. Ez a csomópont szükség esetén képes a REST-végpont hitelesítésére. Emellett a csomópont beépített rendszerképekkel rendelkezik a képkockák méretezéséhez és kódolásához, mielőtt azok továbbítva lesznek a REST-végpontnak. A méretezési lehetőségekkel a képméretarány megtartható, párnázott vagy kifeszíthető. A képkódoló támogatja a JPEG, a PNG vagy a BMP formátumot. További információ [a processzorról](media-graph-extension-concept.md#http-extension-processor).

#### <a name="grpc-extension-processor"></a>gRPC-bővítmény processzora

A gRPC-bővítmény processzor-csomópontja dekódolja a képkockákat bemenetként, és továbbítja ezeket a képkockákat a modul által elérhető [gRPC](terminology.md#grpc) -végpontnak. A csomópont támogatja az adatátvitelt a [megosztott memóriával](https://en.wikipedia.org/wiki/Shared_memory) , vagy közvetlenül ágyazza be a tartalmat a gRPC-üzenetek törzsébe. Emellett a csomópont beépített rendszerképekkel rendelkezik a képkockák méretezéséhez és kódolásához, mielőtt azok továbbítva lesznek a gRPC-végpontnak. A méretezési lehetőségekkel a képméretarány megtartható, párnázott vagy kifeszíthető. A képkódoló támogatja a JPEG, a PNG vagy a BMP formátumot. További információ [a processzorról](media-graph-extension-concept.md#grpc-extension-processor).

#### <a name="signal-gate-processor"></a>Signal Gate processzor  

A Signal Gate processzor csomópontja lehetővé teszi az adathordozók feltételes továbbítását az egyik csomópontról a másikra. Pufferként is működik, amely lehetővé teszi a média és az események szinkronizálását. A tipikus használati eset az, ha egy Signal Gate processzor-csomópontot szúr be az RTSP-forrás csomópontja és az objektum fogadó csomópontja között, és egy mozgásérzékelő processzor-csomópont kimenetét használja a kapu elindításához. A Media Graph segítségével csak akkor rögzíthet videót, ha a rendszer mozgást észlel.

### <a name="sinks"></a>Fogadóként  

#### <a name="asset-sink"></a>Eszköz fogadója  

Az eszköz fogadó csomópontja lehetővé teszi adathordozó (videó és/vagy hang) adatok írását egy Azure Media Services eszközre. A Media Graph-ban csak egyetlen objektum-fogadó csomópont lehet. Tekintse meg az [eszköz](terminology.md#asset) szakaszt az eszközökről, valamint az adathordozók rögzítésével és lejátszásával kapcsolatos szerepéről. A [folyamatos videofelvételek](continuous-video-recording-concept.md) című cikkből megtudhatja, hogyan használja a rendszer a csomópontok tulajdonságait.

#### <a name="file-sink"></a>File mosogató  

A file fogadó csomópont lehetővé teszi adathordozó (videó és/vagy hang) típusú adatok írását a IoT Edge eszköz helyi fájlrendszerének egyik helyére. Egy Media Graph-ban csak egy file mosogató csomópont lehet, és a jel a Signal Gate processzor csomópontjának kell, hogy legyen. Ez korlátozza a kimeneti fájlok időtartamát a Signal Gate-feldolgozó csomópontjának tulajdonságaiban megadott értékekre. Annak biztosítása érdekében, hogy az Edge-eszköz ne fusson el a szabad lemezterületen, beállíthatja azt is, hogy az élő videó-elemzés IoT Edge moduljában hogyan tárolhatók az adatai.  
> [!NOTE]
Ha a fájl fogadója megtelik, az élő videó Analytics IoT Edge modulban elkezdi törölni a legrégebbi adat törlését, és lecseréli az újat.
#### <a name="iot-hub-message-sink"></a>Üzenet-fogadó IoT Hub  

Egy IoT Hub Message fogadó csomópont lehetővé teszi az események közzétételét IoT Edge hubhoz. Az IoT Edge hub ezután átirányíthatja az adatforrásokat a peremhálózati eszközön lévő más modulokba vagy alkalmazásokba, vagy a felhőben IoT Hub (az üzembe helyezési jegyzékben megadott útvonalakon). A IoT Hub Message fogadó csomópont képes fogadni a felsőbb rétegbeli processzorokból származó eseményeket, például a mozgásészlelési processzor csomópontját vagy egy külső következtetési szolgáltatást egy HTTP-bővítmény processzor-csomópontján keresztül.

## <a name="rules-on-the-use-of-nodes"></a>A csomópontok használatára vonatkozó szabályok

A különböző csomópontok adathordozó-diagramon belüli használatának módjával kapcsolatos további szabályok a [Graph-topológiák korlátozásai](quotas-limitations.md#limitations-on-graph-topologies-at-preview) című témakörben találhatók.

## <a name="scenarios"></a>Forgatókönyvek

A fent definiált források, processzorok és mosogatók kombinációjának használatával különböző forgatókönyveket hozhat létre, amelyek az élő videók elemzését foglalják magukban. Példák a következőkre:

* [Folyamatos videófelvétel](continuous-video-recording-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Live Video Analytics videófelvétel nélkül](analyze-live-video-concept.md)

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan futtathatja a mozgásérzékelőt élő videós hírcsatornán, tekintse meg a rövid útmutató [: élő videó-elemzés futtatása a saját modelljével](use-your-model-quickstart.md)című témakört.
