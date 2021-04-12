---
title: Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben – Azure
description: Ez a témakör az élő videó-elemzések IoT Edge kiadásokra, javításokra, hibajavításokra és ismert problémákra vonatkozó kibocsátási megjegyzéseit tartalmazza.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954395"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Élő videó-elemzések IoT Edge kibocsátási megjegyzésekben

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

Ez a cikk a következő információkat tartalmazza:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

<hr width=100%>

## <a name="january-12-2021"></a>2021. január 12.

Ez a kiadási címke a modul januári 2021 frissítésére szolgál:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek a 2 (Live-Video-Analytics: 2) címkét használják. Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.
### <a name="bug-fixes"></a>Hibajavítások 

* A `ActivationSignalOffset` `MinimumActivationTime` `MaximumActivationTime` Signal Gate-feldolgozók mezői nem voltak megfelelően beállítva a kötelező tulajdonságok. Ezek a tulajdonságok mostantól **opcionálisak** .
* Rögzített egy használati hiba, amely hatására az élő videó Analytics IoT Edge modul összeomlása esetén bizonyos régiókban való üzembe helyezéskor összeomlik.

<hr width=100%>

## <a name="december-14-2020"></a>December 14., 2020
Ez a kiadás a IoT Edge Live Video Analytics nyilvános előzetes verziójának frissítését jeleníti meg. A kiadási címke

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Modul frissítései
* Több mint egy HTTP-bővítmény processzorának és a gRPC-bővítmény processzorának a támogatása gráf-topológián.
* A lemezterület-kezelés támogatása a fogadó [csomópontok számára](upgrading-lva-module.md#disk-space-management-with-sink-nodes).
* `MediaGraphGrpcExtension` a csomópont mostantól támogatja a [extensionConfiguration](grpc-extension-protocol.md) tulajdonságot több AI-modell használatára egyetlen gRPC-kiszolgálón belül.
* Az élő videó Analytics modul metrikáinak begyűjtésének támogatása a [Prometheus formátumban](https://prometheus.io/docs/practices/naming/). További információ a [metrikák gyűjtéséről és a Azure monitor való megtekintéséről.](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* Lehetőség van a kimenet kijelölésének szűrésére. **Csak hang-** vagy **video-** , illetve **hang-és video** -átadható is lehet, ha bármilyen gráf-csomóponthoz segítségére van szüksége `outputSelectors` . 
* A frame rate szűrő processzora **elavult**.  
    * A frame rate Management mostantól elérhető a Graph bővítmény processzor-csomópontjain belül.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-bővítmény
* Megjelent [az élő videók elemzése IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) – egy Visual Studio Code-bővítmény, amely segít a LVA-diagramok kezelésében. Ez a bővítmény a **LVA 2,0 modullal** működik együtt, és a lapos gráfok egyszerű és könnyen használható grafikus felülettel való szerkesztését és kezelését kínálja.
## <a name="september-22-2020"></a>Szeptember 22., 2020

Ez a kiadási címke a modul szeptember 2020 frissítésére szolgál:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="module-updates"></a>Modul frissítései

* Az új Graph-bővítmények csomópontja, a [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) a [térbeli analízis](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(előzetes verzió) modullal integrálható Cognitive Services.
* Linux ARM64-eszközök támogatása – az ilyen eszközökön való üzembe helyezéshez [szükséges manuális lépések](deploy-iot-edge-device.md) használata.

### <a name="documentation-updates"></a>Dokumentációfrissítések

* Az Azure Stack Edge-eszközökön a IoT Edge élő videó-elemzések használatával kapcsolatos [utasítások](deploy-azure-stack-edge-how-to.md) érhetők el.
* Új oktatóanyag a forgatókönyv-specifikus számítógépes szemléletű modellek fejlesztéséhez [Custom Vision szolgáltatás](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) használatával és az [élő videók valós idejű elemzéséhez](custom-vision-tutorial.md) .

<hr width=100%>

## <a name="august-19-2020"></a>Augusztus 19., 2020

Ez a kiadási címke a modul augusztus 2020 frissítésére szolgál:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="module-updates"></a>Modul frissítései

* Mostantól nagy adatátviteli teljesítményt érhet el az élő videók elemzése IoT Edge és egyéni bővítménye között a gRPC Framework használatával. A kezdéshez tekintse meg [a](analyze-live-video-use-your-grpc-model-quickstart.md) rövid útmutatót.
* Az élő videó-elemzések szélesebb regionális telepítése, és csak a Cloud Service frissült.  
* Az élő videó elemzése mostantól 25 további régióban érhető el szerte a világon. Itt látható az összes elérhető régió [listája](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) .  
* A gyors [üzembe helyezés beállítása](https://aka.ms/lva-edge/setup-resources-for-samples) az új régiók támogatásával is frissült.
    * Nincs olyan művelet, aki már beállított erőforrásokat

### <a name="bug-fixes"></a>Hibajavítások 

* Elavult Azure-bővítmény használatának eltávolítása a telepítési parancsfájlban

<hr width=100%>

## <a name="july-13-2020"></a>Július 13., 2020

Ez a kiadási címke a modul július 2020 frissítésére szolgál:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> A rövid útmutatók és oktatóanyagokban az üzembe helyezési jegyzékek 1. címkét használnak (Live-Video-Analytics: 1). Ezért az ilyen jegyzékfájlok újbóli üzembe helyezéséhez frissítenie kell a modult a peremhálózati > eszközökön.

### <a name="module-updates"></a>Modul frissítései

* Most már létrehozhat olyan gráf-topológiákat, amelyekhez tartozik egy eszköz fogadó csomópontja, valamint egy file mosogató csomópont a Signal Gate processzor csomópontján. Tekintse meg [a topológiát](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) példaként.

### <a name="bug-fixes"></a>Hibajavítások

* A kívánt tulajdonságok érvényesítésének fejlesztése

<hr width=100%>

## <a name="june-1-2020"></a>2020. június 1.

Ez a kiadás a IoT Edge Live Video Analytics első nyilvános előzetes kiadása. A kiadási címke

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Támogatott funkciók

* Élő videó streamek elemzése az Ön által választott AI-modulok használatával, és igény szerint rögzíthet videót a peremhálózati eszközön vagy a felhőben
* A IoT Edge által [támogatott](../../iot-edge/support.md) Linux amd64 operációs rendszerek használata
* A modul üzembe helyezése és konfigurálása a IoT Hub Azure Portal vagy Visual Studio Code használatával
* [Graph-topológiák és gráf-példányok](media-graph-concept.md#media-graph-topologies-and-instances) kezelése távolról vagy helyileg a következő közvetlen metódus-hívásokkal

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Következő lépések

[Áttekintés](overview.md)
