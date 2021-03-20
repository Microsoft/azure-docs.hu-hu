---
title: Event-alapú videofelvétel – Azure
description: Az Event-based video Recording (EVR) az esemény által aktivált videó rögzítési folyamatára utal. A szóban forgó esemény a videós jel (például a mozgás észlelése) vagy egy független forrásból (például egy ajtó megnyitása) származó feldolgozásból származhat.  Ebben a cikkben az Event-alapú videofelvételekkel kapcsolatos néhány felhasználási esetet ismertetjük.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97401601"
---
# <a name="event-based-video-recording"></a>Eseményalapú videófelvétel  
 
## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

* [Folyamatos videófelvétel](continuous-video-recording-concept.md)
* [Rögzített tartalom lejátszása](video-playback-concept.md)
* [A Media Graph koncepciója](media-graph-concept.md)

## <a name="overview"></a>Áttekintés 

Az Event-based video Recording (EVR) az esemény által aktivált videó rögzítési folyamatára utal. A szóban forgó esemény a videó-jel (például a mozgás észlelése) vagy egy független forrásból (például egy ajtó megnyitása) származó feldolgozás miatt lehet. 

A videó (esemény által aktivált) egy CCTV kameráról egy Media Services eszközre is rögzíthető egy olyan adathordozó-gráf használatával, amely egy [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontból, egy [eszköz](media-graph-concept.md#asset-sink) fogadó csomópontjához és más csomópontokból áll, amelyek az alábbi használati esetekben szerepelnek. Beállíthatja, hogy az [eszköz](media-graph-concept.md#asset-sink) fogadó csomópontja minden egyes esemény bekövetkeztekor új eszközöket állítson elő, így az egyes eseményeknek megfelelő videó a saját eszközén lesz. Azt is megteheti, hogy egyetlen eszközt használ az összes esemény Videójának tárolásához. 

Az eszköz fogadó csomópontjának alternatívájaként egy [file](media-graph-concept.md#file-sink) fogadó csomópont használatával rögzítheti a videó rövid részleteit (egy adott eseményhez kapcsolódóan) egy adott helyre a peremhálózati eszköz helyi fájlrendszerén. 

Ebben a cikkben az Event-alapú videofelvételekkel kapcsolatos néhány felhasználási esetet ismertetjük.

### <a name="video-recording-based-on-motion-detection"></a>Videó rögzítése a mozgásészlelés alapján  

Ebben a használati esetben csak akkor rögzíthet videoklipeket, ha a videóban mozgás észlelhető, és a rendszer riasztást küld, amikor egy videoklipet generál. Ez fontos lehet a kritikus infrastruktúrák védelmét érintő kereskedelmi biztonsági forgatókönyvekben. Ha riasztásokat állít elő, és videót rögzít a váratlan mozgás észlelésekor, javíthatja az emberi operátor hatékonyságát, mivel a művelet csak a riasztás létrehozásakor szükséges.

Az alábbi ábrán egy olyan adathordozó-gráf grafikus ábrázolása látható, amely ezt a használati esetet kezeli. Az ilyen adathordozó-gráf gráf-topológiájának JSON-ábrázolása [itt](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)található.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Videó rögzítése a mozgásészlelés alapján":::

A diagramon az RTSP forrás csomópontja rögzíti az élő video-hírcsatornát a kamerából, és továbbítja azt egy [mozgásérzékelő processzor](media-graph-concept.md#motion-detection-processor) -csomópontnak. Ha a mozgás észlelését észleli az élő videóban, a mozgásérzékelő processzor csomópontja olyan eseményt hoz létre, amely a [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) -csomópontra, valamint a IoT hub Message fogadó csomópontra mutat. Az utóbbi csomópont az eseményeket az IoT Edge hubhoz küldi, ahonnan a riasztások elindításához más célhelyekre is átirányítható. 

A mozgásészlelési csomópont egy eseménye aktiválja a Signal Gate processzor csomópontját, és lehetővé teszi az élő videó-hírcsatornát az RTSP-forrás csomópontról az eszköz fogadó csomópontjára. A következő ilyen mozgásészlelési események hiányában a kapu egy beállított idő után lezárul. Ez határozza meg a rögzített videó időtartamát.

### <a name="video-recording-based-on-events-from-other-sources"></a>Videók rögzítése más forrásokból származó események alapján  

Ebben a használati esetben egy másik IoT-érzékelőből származó jeleket lehet használni a videó rögzítésének elindításához. Az alábbi ábrán egy olyan adathordozó-gráf grafikus ábrázolása látható, amely ezt a használati esetet kezeli. Az ilyen adathordozó-gráf gráf-topológiájának JSON-ábrázolása [itt](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)található.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Videók rögzítése más forrásokból származó események alapján":::

A diagramon a külső érzékelő eseményeket küld az IoT Edge hubhoz. Ezután a rendszer átirányítja az eseményeket a Signal Gate processzor-csomópontra a [IoT hub Message Source](media-graph-concept.md#iot-hub-message-source) csomóponton keresztül. A Signal Gate processzor csomópontjának viselkedése ugyanaz, mint az előző használati eset esetében – megnyílik, és hagyja, hogy az élő videó adatcsatornája az RTSP-forrás csomópontról a file fogadó csomópontra (vagy az eszköz fogadó csomópontjára) kerüljön, amikor a külső esemény aktiválja. 

Ha file mosogató-csomópontot használ, a videó a peremhálózati eszköz helyi fájlrendszerében lesz rögzítve. Ha egy eszköz-fogadó csomópontot használ, a videó rögzítve lesz egy eszközön.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Videó rögzítése egy külső viszonyítási modul alapján 

Ebben a használati esetben a külső logikai rendszertől származó jel alapján rögzíthet videoklipeket. Ilyen használati eset például csak akkor rögzíthet egy videoklipet, ha az adott országúton lévő forgalom videós csatornáján egy teherautó észlelhető. Az alábbi ábrán egy olyan adathordozó-gráf grafikus ábrázolása látható, amely ezt a használati esetet kezeli. Az ilyen adathordozó-gráf gráf-topológiájának JSON-ábrázolása [itt](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)található.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Videó rögzítése egy külső viszonyítási modul alapján":::

A diagramon az RTSP forrás csomópontja rögzíti az élő video-hírcsatornát a kamerából, és két ág számára teszi elérhetővé: az egyiknek van egy [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) -csomópontja, a másik pedig egy [http-bővítmény](media-graph-concept.md) csomópontot használ az adatok külső logikai modulba való küldéséhez. A HTTP-bővítmény csomópont lehetővé teszi, hogy a Media Graph képkockákat (JPEG, BMP vagy PNG formátumban) küldjön a REST-alapú külső következtetési szolgáltatásnak. A jel elérési útja általában csak az alacsony képkockák (<5fps) támogatására használható. A HTTP-bővítmény processzor-csomópontjának használatával csökkentheti a videó képkocka-sebességét, amely a külső következtetési modulra mutat.

A külső következtetési szolgáltatásból származó eredményeket a HTTP-bővítmény csomópont kérdezi le, és a IoT Edge hubhoz IoT Hub Message fogadó csomóponton keresztül továbbítja, ahol a külső logikai modul további feldolgozhatja őket. Ha a következtetési szolgáltatás képes a járművek észlelésére, például a logikai modul megkeresheti egy adott járművet, például egy buszt vagy egy teherautót. Ha a logikai modul észleli a fontos objektumot, akkor a Signal Gate processzor-csomópontot úgy aktiválhatja, hogy az IoT Edge hub-on keresztül küld egy eseményt a gráf IoT Hub üzenet forrása csomópontjának. A Signal Gate kimenete egy file fogadó csomóponthoz vagy egy eszköz fogadó csomópontjára mutat. Az előző esetben a videó a peremhálózati eszköz helyi fájlrendszerén lesz rögzítve. Az utóbbi esetben a rendszer rögzíti a videót egy eszközre.

Ennek a példának a továbbfejlesztése a HTTP-bővítmény processzor-csomópontja előtt a mozgásérzékelő processzorának használata. Ez csökkenti a következtetést, például az éjszakai időt, ha az országúton nem található járműveket nem lehet megtakarítani. 

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: eseményvezérelt videó rögzítése](event-based-video-recording-tutorial.md)
