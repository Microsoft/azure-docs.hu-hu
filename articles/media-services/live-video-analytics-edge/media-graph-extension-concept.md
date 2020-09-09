---
title: Mi az a Media Graph Extension – Azure
description: A IoT Edge élő videó-elemzések lehetővé teszik a Media Graph feldolgozási képességeinek kiterjesztését egy gráf kiterjesztésű csomóponton keresztül.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 74929cc51a868d20952f1e25432f5343e4821d08
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569329"
---
# <a name="media-graph-extension"></a>Médiagrafikon bővítmény

A IoT Edge élő videó-elemzések lehetővé teszik a Media Graph feldolgozási képességeinek kiterjesztését egy gráf kiterjesztésű csomóponton keresztül. Az Analytics bővítmény beépülő modulja a hagyományos képfeldolgozási technikák vagy a Computer látási AI-modellek használatát teszi elérhetővé. A Graph-bővítmények engedélyezve vannak, ha a bővítmény processzor-csomópontját a Media Graph-ban engedélyezték. A bővítmény processzor-csomópontja továbbítja a képkockákat a konfigurált végponthoz, és illesztőfelületként viselkedik a bővítmény számára. A kapcsolódás helyi vagy távoli végponton végezhető el, és szükség esetén hitelesítéssel és TLS-titkosítással is biztonságossá tehető. Emellett a Graph-bővítmény processzor-csomópontja lehetővé teszi a képkockák opcionális skálázását és kódolását, mielőtt elküldik azokat az egyéni bővítménynek.

Az élő videó-elemzés a Media Graph bővítmények két típusát támogatja:

* [HTTP-bővítmény processzora](media-graph-concept.md#http-extension-processor)
* [gRPC-bővítmény processzora](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>HTTP-bővítmény processzora

A HTTP-bővítmény processzora a HTTP protokollt használó bővíthetőségi forgatókönyveket tesz lehetővé, ahol a teljesítmény és/vagy az optimális erőforrás-használat nem az elsődleges szempont. A saját AI-t egy HTTP REST-végponton keresztül teheti elérhetővé a Media Graph-ban. 

HTTP-bővítmény processzor-csomópontjának használata:

* Nagyobb együttműködésre van szükség a meglévő HTTP-következtetési rendszerekkel.
* Az alacsony teljesítményű adatátvitel elfogadható.
* Egy egyszerű kérelem-válasz felületet szeretne használni az élő videó elemzéséhez.

## <a name="grpc-extension-processor"></a>gRPC-bővítmény processzora

a gRPC-bővítmény processzora gRPC-alapú, nagy teljesítményű strukturált protokollt használó bővíthetőségi forgatókönyveket tesz lehetővé. Ideális olyan helyzetekben, ahol a teljesítmény és/vagy az optimális erőforrás-használat prioritás. A gRPC bővítmény-feldolgozó lehetővé teszi a strukturált adatdefiníciók teljes előnyeit. a gRPC a következőket kínálja a tartalom átviteléhez:

* [beépített megosztott memória](https://en.wikipedia.org/wiki/Shared_memory) vagy 
* a tartalom közvetlen beágyazása a gRPC-üzenetek törzsébe. 

A gRPC-bővítmény processzora adathordozó-tulajdonságok küldésére, valamint a következtetési üzenetek cseréjére használható.
Tehát a gRPC-bővítmény processzor-csomópontjának használatakor:

* Strukturált szerződést kíván használni (például a kérelmek és válaszok strukturált üzenetei)
* A protokoll puffereit ([protopuf](https://developers.google.com/protocol-buffers)) szeretné használni a kommunikáció alapjául szolgáló Message Interchange formátumként.
* Szeretne kommunikálni egy gRPC-kiszolgálóval egyetlen adatfolyam-munkamenetben ahelyett, hogy a hagyományos kérelem-válasz modellnek egy egyéni kérés-kezelőt kell használnia a bejövő kérések elemzéséhez és a megfelelő implementációs függvények meghívásához. 
* Kis késést és nagy átviteli sebességű kommunikációt szeretne az élő videó-elemzés és a modul között.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>A következtetési modell használata az élő videó elemzésével

A Media Graph-bővítmények lehetővé teszik, hogy tetszőleges következtetési modelleket futtasson bármely elérhető következtetési futtatókörnyezetben (például ONNX, TensorFlow, PyTorch vagy mások saját Docker-tárolóban). A legjobb teljesítmény érdekében az élő videó Analytics Edge-modul mellett kell központilag telepíteni az egyéni bővítményt, amelyet a rendszer a HTTP-bővítmény processzorán vagy a Graph-topológiában található gRPC-bővítményi processzoron keresztül hív meg. Emellett az egyéni bővítményre irányuló hívások gyakorisága szabályozható úgy, hogy opcionálisan hozzáad egy [mozgásérzékelő processzort](media-graph-concept.md#motion-detection-processor) és egy [frame rate szűrő processzort](media-graph-concept.md#frame-rate-filter-processor) a Media Extension processzorhoz.

Az alábbi ábra a magas szintű adatfolyamot ábrázolja:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="AI-következtetési szolgáltatás":::

## <a name="samples"></a>Példák

Az élő videó elemzéséhez [Jupyter-jegyzetfüzet](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) -mintákat is megadunk. Ezek a jegyzetfüzetek részletes útmutatást nyújtanak **a Media Graph bővítményeivel** kapcsolatban:

* A Docker-tároló rendszerképének létrehozása bővítmény-szolgáltatásból
* A bővítmény szolgáltatás tárolóként való üzembe helyezése az élő video Analytics-tárolóval együtt
* Élő video Analytics Media Graph használata kiterjesztési ügyféllel és a bővítmény végpontjának (HTTP/gRPC) használatával