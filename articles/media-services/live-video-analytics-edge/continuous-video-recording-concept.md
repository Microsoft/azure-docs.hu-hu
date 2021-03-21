---
title: Folyamatos videofelvétel – Azure
description: A folyamatos videofelvétel (CVR) arra a folyamatra utal, amely folyamatosan rögzíti a videót a videó forrásaként. Ez a témakör a CVR ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453750"
---
# <a name="continuous-video-recording"></a>Folyamatos videófelvétel  

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

* [A Media Graph koncepciója](media-graph-concept.md)
* [Videofelvételi koncepció](video-recording-concept.md)

## <a name="overview"></a>Áttekintés

A folyamatos videofelvétel (CVR) arra a folyamatra utal, amely folyamatosan rögzíti a videót a videó forrásaként. A IoT Edge élő videó-elemzési szolgáltatás támogatja a videó folyamatos, nonstop-alapú, a CCTV kamerából való rögzítését egy olyan [adathordozó-gráfon](media-graph-concept.md) keresztül, amely egy RTSP-forrás csomópontból és egy eszköz fogadó csomópontból áll. Az alábbi ábrán egy ilyen adathordozó-gráf grafikus ábrázolása látható. Az ilyen adathordozó-gráf [gráf-topológiájának](media-graph-concept.md#media-graph-topologies-and-instances) JSON-ábrázolása [itt](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)található.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Folyamatos videófelvétel":::

A fent ábrázolt adathordozó-gráf egy peremhálózati eszközön futtatható, az eszköz fogadója pedig egy Azure Media Services [eszközre](terminology.md#asset)rögzíti a videót. A videó addig lesz rögzítve, amíg az adathordozó-gráf aktivált állapotban marad. Mivel a videó egy eszközként van rögzítve, a Media Services meglévő folyamatos átviteli képességeivel is lejátszhatók. További részletekért lásd [a rögzített tartalom lejátszását](video-playback-concept.md) ismertető témakört.

## <a name="resilient-recording"></a>Rugalmas rögzítés

Az élő videó-elemzések IoT Edge támogatja a kevésbé tökéletes hálózati feltételek melletti működést, ahol a peremhálózati eszköz időnként elveszítheti a kapcsolatot a felhővel, vagy csökkentheti a rendelkezésre álló sávszélesség csökkenését. Ennek érdekében a forrásról származó videót helyileg rögzíti a rendszer a gyorsítótárba, és rendszeres időközönként automatikusan szinkronizálja az eszközzel. Ha megvizsgálja a [Graph TOPOLÓGIA JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)-t, látni fogja, hogy a következő tulajdonságok vannak meghatározva:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

Az utóbbi két tulajdonság a rugalmas rögzítéshez is fontos (mindkettő kötelező tulajdonságok egy eszköz fogadó csomóponthoz). A localMediaCachePath tulajdonság azt jelzi, hogy az eszköz fogadója a mappa elérési útját használja a médiaadatok gyorsítótárazásához az eszközre való feltöltés előtt. [Ebből](../../iot-edge/how-to-access-host-storage-from-module.md) a cikkből megtudhatja, hogyan használhatja az Edge-modul az eszköz helyi tárolóját. A localMediaCacheMaximumSizeMiB tulajdonság határozza meg, hogy mennyi lemezterületet használhat az objektum a gyorsítótárban (1 MiB = 1024 * 1024 bájt). 

Ha a peremhálózati modul nagyon hosszú ideig elveszti a kapcsolódást, és a gyorsítótár mappában tárolt tartalom eléri a localMediaCacheMaximumSizeMiB értéket, az objektum fogadója elkezdi az adatok elvetését a gyorsítótárból a legrégebbi adatoktól kezdve. Ha például az eszköz 10 órakor megszakadt a kapcsolat, és a gyorsítótár eléri a maximális korlátot (6), akkor az objektum fogadója a 10:00-on rögzített adattörlést kezdi meg. 

A hálózati kapcsolat visszaállításakor az eszköz fogadója megkezdi a feltöltést a gyorsítótárból, a legrégebbi adatoktól kezdve. A fenti példában tegyük fel, hogy az időkapcsolat visszaállítása után 5 perces videót kellett volna eldobni a gyorsítótárból (mondjuk 6:02PM), majd az eszköz fogadója elkezdi feltölteni a 10:05AM jelet.

Ha később megvizsgálja az eszközt [ezen](playback-recordings-how-to.md) API-k használatával, látni fogja, hogy az eszközön található hézag körülbelül 10:00 – 10:05AM.

## <a name="segmented-recording"></a>Szegmentált rögzítés  

A fentiekben leírtak szerint az eszköz fogadó csomópontja a videót egy helyi gyorsítótárba rögzíti, és rendszeresen feltölti a videót a felhőbe. A segmentLength tulajdonság (a fenti szakaszban látható) segítségével szabályozhatja az írási tranzakciók költségeit a Storage-fiókba az adategység rögzítésekor. Ha például 30 másodperc és 5 perc között növeli a feltöltési időtartamot, akkor a tárolási tranzakciók száma 10 (5 * 60/30) lesz.

A segmentLength tulajdonság biztosítja, hogy az Edge-modul segmentLength másodpercenként legfeljebb egyszer feltölti a videót. Ennek a tulajdonságnak a minimális értéke 30 másodperc (az alapértelmezett érték is), és legfeljebb 30 másodperces növekménysel növelhető.

> [!NOTE]
> Tekintse meg a lejátszási [felvételek](playback-recordings-how-to.md) című cikket arról, hogy a segmentLength milyen hatással van a lejátszásra.

## <a name="see-also"></a>Lásd még

* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Rögzített tartalom lejátszása](video-playback-concept.md)

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: folyamatos videofelvétel](continuous-video-recording-tutorial.md)
