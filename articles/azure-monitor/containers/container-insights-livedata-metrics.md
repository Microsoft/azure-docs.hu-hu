---
title: A metrikák valós idejű megtekintése a Container bepillantást nyerhet | Microsoft Docs
description: Ez a cikk a metrikák valós idejű nézetét mutatja be anélkül, hogy a kubectl-t használja a Container bepillantást.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2e3a8a417a934374c2c0b256bb65a471d98fdebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731850"
---
# <a name="how-to-view-metrics-in-real-time"></a>A metrikák valós idejű megtekintése

A Container bepillantást élő adatok (előzetes verzió) funkciója lehetővé teszi, hogy valós időben jelenítse meg a csomópont-és Pod-állapot mérőszámait a fürtben. Közvetlen hozzáférést emulál a `kubectl top nodes` , a `kubectl get pods –all-namespaces` és `kubectl get nodes` parancsokhoz, hogy meghívja, elemezze és megjelenítse az ebben az elemzésben szereplő teljesítmény-diagramokban lévő információkat.

Ez a cikk részletes áttekintést nyújt, és segít megérteni a funkció használatát.

>[!NOTE]
>Ez a funkció nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AK-fürtöket. A szolgáltatás csak akkor tud működni, ha közvetlenül a böngészőből, egy proxykiszolgálón keresztül éri el a Kubernetes API-t. Ha engedélyezi a hálózati biztonságot, hogy letiltsa a Kubernetes API-t ebből a proxyból, azzal a forgalmat is letiltja.

Az élő adatszolgáltatások (előzetes verzió) beállításával vagy hibaelhárításával kapcsolatos segítségért tekintse át a [telepítési útmutatót](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Működési elv

Az élő adatok (előzetes verzió) funkció közvetlenül a Kubernetes API-hoz érhető el, és a hitelesítési modellel kapcsolatos további információk [itt](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)találhatók.

Ez a szolgáltatás lekérdezési műveletet hajt végre a metrikák végpontokon (beleértve `/api/v1/nodes` a, `/apis/metrics.k8s.io/v1beta1/nodes` és a `/api/v1/pods` ), amely alapértelmezés szerint öt másodpercenként van. Ezeket az adatfájlokat a rendszer a böngészőben gyorsítótárazza, és a **fürt** lapon található, a **(z) Go Live (előzetes verzió)** elemre kattintva a tárolóban bekövetkező négy teljesítményű diagramon ábrázolja őket. Minden további lekérdezés táblázatos, öt perces vizualizációs ablakba kerül.

![Ugrás az élő lehetőségre a fürt nézetben](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

A lekérdezési időközt a **beállított intervallum** legördülő menüben állíthatja be, amely lehetővé teszi az új adatok lekérdezésének megadását az egyes 1, 5, 15 és 30 másodpercekben.

![Ugrás élő legördülő lekérdezési időköz](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Javasoljuk, hogy a lekérdezési időközt egy másodpercre állítsa be, miközben rövid idő alatt hibaelhárítást végez a probléma megoldásában. Ezek a kérések befolyásolhatják a fürtön a Kubernetes API rendelkezésre állását és szabályozását. Ezt követően a rendszer újrakonfigurálja a lekérdezési időközt.

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárolja véglegesen az adattárolást. A munkamenet során rögzített összes információ azonnal törlődik a böngésző bezárásával vagy a szolgáltatásból való kilépéssel. Az adatmegjelenítés csak az öt perces időszakon belül marad elérhető. az öt percnél régebbi mérőszámok is véglegesen törlődnek.

Ezeket a diagramokat nem lehet az élő módban megtekintett utolsó Azure-irányítópultra rögzíteni.

## <a name="metrics-captured"></a>Rögzített metrikák

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Csomópont CPU-kihasználtsága%/Node memória kihasználtsága (%)

Ez a két teljesítmény-diagram a `kubectl top nodes` **CPU%** és a **memória%** Columns eredményének a megfelelő diagramra való meghívásának és rögzítésének megfelelőjét képezi.

![Kubectl – példa a legfontosabb csomópontokra](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Csomópontok CPU-kihasználtságának százalékos diagramja](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Node memória kihasználtságának százalékos diagramja](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

A percentilis-számítások nagyobb fürtökben működnek, hogy könnyebben azonosíthatók legyenek a fürt kiugró csomópontjai. Ha például meg szeretné tudni, hogy a csomópontok alatt kihasználva vannak-e a méretezési célok. A **minimális** összesítést kihasználva láthatja, hogy mely csomópontok rendelkeznek alacsony kihasználtsággal a fürtben. A további vizsgálathoz válassza a **csomópontok** fület, és rendezze a rácsot CPU vagy memória kihasználtsága alapján.

Ez segít megismerni, hogy mely csomópontok vannak leküldve a korlátaik számára, és ha felskálázásra lehet szükség. A **Max** -és a **P95** -összesítések használata segíthet megtekinteni, hogy vannak-e olyan csomópontok a fürtben, ahol magas az Erőforrás kihasználtsága. További vizsgálathoz váltson át a **csomópontok** lapra.

### <a name="node-count"></a>Csomópontok száma

Ez a teljesítménymutató az `kubectl get nodes` **állapot** oszlopnak az állapot típusa szerint csoportosított diagramhoz való meghívásával és leképezésével egyenértékűként van leképezve.

![Kubectl-lekérési csomópontok – példa eredményei](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Csomópontok száma diagram](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

A csomópontok **kész** vagy **nem üzemkész** állapotban vannak jelezve. A rendszer megszámolja őket (és megszámolja a teljes darabszámot), és a két összesítés eredményét ábrázolja.
Például annak megismeréséhez, hogy a csomópontok hibás állapotba esnek-e. A **nem kész** összesítés használatával gyorsan láthatja, hogy a fürtben lévő csomópontok száma jelenleg **nem üzemkész** állapotban van.

### <a name="active-pod-count"></a>Aktív Pod-szám

Ez a teljesítményteszt az `kubectl get pods –all-namespaces` **állapot** típusa szerint csoportosított állapot oszlop meghívásának és leképezésének megfelelő értékkel van leképezve.

![Kubectl beolvasása – példa eredményei](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Csomópontok Pod Count diagram](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Előfordulhat, hogy az állapot neve `kubectl` nem egyezik meg pontosan a diagramban.

## <a name="next-steps"></a>Következő lépések

Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a riasztások, a vizualizációk vagy a fürtök további elemzéséhez.
