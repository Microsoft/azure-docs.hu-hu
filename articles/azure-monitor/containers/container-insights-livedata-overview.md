---
title: Élő adatértékek megtekintése a Container-elemzésekkel | Microsoft Docs
description: Ez a cikk a Kubernetes-naplók,-események és-Pod-metrikák valós idejű nézetét írja le anélkül, hogy kubectl kellene használni a Container bepillantást.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: references_regions
ms.openlocfilehash: 5277f5051e291e9058255d8920ac0be950389704
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203198"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>A Kubernetes-naplók,-események és a pod-metrikák valós idejű megtekintése

A Container-elemzések tartalmazzák az élő adatok funkciót, amely egy fejlett diagnosztikai funkció, amely lehetővé teszi az Azure Kubernetes szolgáltatás (ak) tároló-naplófájljainak (StdOut/stderror), az események és a pod metrikák közvetlen elérését. Közvetlen hozzáférést tesz elérhetővé a `kubectl logs -c` , az `kubectl get` eseményekhez és a szolgáltatásokhoz `kubectl top pods` . A konzol ablaktáblán láthatók a tároló-motor által generált naplók, események és mérőszámok, amelyek a hibák valós idejű hibaelhárítását segítik elő.

Ez a cikk részletes áttekintést nyújt, és segít megérteni a funkció használatát.

Az élő adatszolgáltatás beállításához vagy hibaelhárításához tekintse át a [telepítési útmutatót](container-insights-livedata-setup.md). Ez a funkció közvetlenül hozzáfér a Kubernetes API-hoz, és a hitelesítési modellel kapcsolatos további információ [itt](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)található.

## <a name="view-aks-resource-live-logs"></a>AK-erőforrások élő naplófájljainak megtekintése
Az alábbi eljárással megtekintheti a hüvelyek, a központi telepítések és a replikák élő naplóit az AK-erőforrás nézetből származó vagy anélkül.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Válassza a számítási **feladatok** elemet a menü **Kubernetes-erőforrások** szakaszában.

3. Válassza ki a megfelelő lapon a pod, a Deployment és a replika beállítást.

4. Válassza az **élő naplók** lehetőséget az erőforrás menüjében.

5. Válassza ki az élő adatgyűjtést elindító Pod-t.

    [![Üzembe helyezési élő naplók](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Naplók megtekintése

A valós idejű naplózási adatok megtekinthetők úgy, ahogy a tároló motorja létrehozta a **csomópontok**, a **vezérlők** és a **tárolók** nézetből. A naplózási adatok megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.**

3. Válassza ki a **csomópontok**, **vezérlők** vagy **tárolók** fület.

4. Válasszon ki egy objektumot a teljesítmény rácsból, és a jobb oldalon található Tulajdonságok ablaktáblán válassza az **élő adatok megtekintése** lehetőséget. Ha az AK-fürt egyszeri bejelentkezéssel van konfigurálva az Azure AD-vel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.

    >[!NOTE]
    >Amikor az Log Analytics munkaterületről tekinti meg az adatok megtekintését a Tulajdonságok ablaktáblán látható **Megtekintés az elemzésben** lehetőség kiválasztásával, a naplók keresési eredményei potenciálisan megjelennek a **csomópontok**, a **démon-készletek**, a **replikakészlet**, a **feladatok**, a **cron-feladatok**, a **hüvelyek** és a **tárolók** , amelyek már nem léteznek. A-ben nem elérhető tárolók naplóiban való keresésére tett kísérlet `kubectl` itt is sikertelen lesz. A korábbi naplók, események és mérőszámok megtekintésével kapcsolatos további információkért tekintse át az [elemzési funkció nézetét](container-insights-log-search.md#search-logs-to-analyze-data) .

A sikeres hitelesítés után az élő adatkonzol ablaktábla a teljesítményadatok rács alatt fog megjelenni, ahol megtekintheti a naplózási adataikat egy folyamatos adatfolyamban. Ha a beolvasás állapotjelzője egy zöld pipa jelenik meg, amely a panel jobb szélén található, az azt jelenti, hogy a rendszer lekéri az adatait, és megkezdi a folyamatos átvitelt a konzolra.

![Csomópont-tulajdonságok ablaktáblájának adatnézete beállítás](./media/container-insights-livedata-overview/node-properties-pane.png)

A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva.

## <a name="view-events"></a>Események megtekintése

Megtekintheti a tároló motor által a **csomópontok**, **vezérlők**, **tárolók** és **központi telepítések** által generált valós idejű események adatait, ha a tároló, a hüvely, a csomópont, a ReplicaSet, a daemonset elemet, a Job, a CronJob vagy a telepítés lehetőség van kiválasztva. Az események megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.**

3. Válassza ki a **csomópontok**, **vezérlők**, **tárolók** vagy **központi telepítések** fület.

4. Válasszon ki egy objektumot a teljesítmény rácsból, és a jobb oldalon található Tulajdonságok ablaktáblán válassza az **élő adatok megtekintése** lehetőséget. Ha az AK-fürt egyszeri bejelentkezéssel van konfigurálva az Azure AD-vel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.

    >[!NOTE]
    >Amikor az Log Analytics munkaterületről tekinti meg az adatok megtekintését a Tulajdonságok ablaktáblán látható **Megtekintés az elemzésben** lehetőség kiválasztásával, a naplók keresési eredményei potenciálisan megjelennek a **csomópontok**, a **démon-készletek**, a **replikakészlet**, a **feladatok**, a **cron-feladatok**, a **hüvelyek** és a **tárolók** , amelyek már nem léteznek. A-ben nem elérhető tárolók naplóiban való keresésére tett kísérlet `kubectl` itt is sikertelen lesz. A korábbi naplók, események és mérőszámok megtekintésével kapcsolatos további információkért tekintse át az [elemzési funkció nézetét](container-insights-log-search.md#search-logs-to-analyze-data) .

A sikeres hitelesítés után az élő adatkonzol ablaktábla a teljesítményadatok rács alatt fog megjelenni. Ha a beolvasás állapotjelzője egy zöld pipa jelenik meg, amely a panel jobb szélén található, az azt jelenti, hogy a rendszer lekéri az adatait, és megkezdi a folyamatos átvitelt a konzolra.

Ha a kiválasztott objektum egy tároló, válassza az **események** lehetőséget a panelen. Ha kiválasztotta a csomópontot, a hüvelyt vagy a vezérlőt, a rendszer automatikusan kijelöli az események megtekintését.

![Vezérlő tulajdonságai ablaktábla – események megtekintése](./media/container-insights-livedata-overview/controller-properties-live-event.png)

A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva.

### <a name="filter-events"></a>Események szűrése

Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **szűrő** pirulával is korlátozhatja. Attól függően, hogy melyik erőforrást választotta ki, a pirula egy Pod, Namespace vagy fürtöt listáz ki, amelyről a választott forrás van kiválasztva.

## <a name="view-metrics"></a>Metrikák megtekintése

A valós idejű metrikai adatokat úgy tekintheti meg, ahogy a tároló motorja a **csomópontok** vagy **vezérlők** nézetből csak akkor jelenik meg, ha egy **Pod** van kiválasztva. A metrikák megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.**

3. Válassza ki a **csomópontok** vagy a **vezérlők** lapot.

4. Válasszon ki egy **Pod** objektumot a teljesítmény rácsból, és a jobb oldalon található Tulajdonságok ablaktáblán válassza az **élő adatok megtekintése** lehetőséget. Ha az AK-fürt egyszeri bejelentkezéssel van konfigurálva az Azure AD-vel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.

    >[!NOTE]
    >Amikor az Log Analytics munkaterületről tekinti meg az adatok megtekintését a Tulajdonságok ablaktáblán látható **Megtekintés az elemzésben** lehetőség kiválasztásával, a naplók keresési eredményei potenciálisan megjelennek a **csomópontok**, a **démon-készletek**, a **replikakészlet**, a **feladatok**, a **cron-feladatok**, a **hüvelyek** és a **tárolók** , amelyek már nem léteznek. A-ben nem elérhető tárolók naplóiban való keresésére tett kísérlet `kubectl` itt is sikertelen lesz. A korábbi naplók, események és mérőszámok megtekintésével kapcsolatos további információkért tekintse át az [elemzési funkció nézetét](container-insights-log-search.md#search-logs-to-analyze-data) .

A sikeres hitelesítés után az élő adatkonzol ablaktábla a teljesítményadatok rács alatt fog megjelenni. A rendszer beolvassa a metrikai adatokat, és megkezdi a folyamatos átvitelt a konzolon a két diagramon való megjelenítéshez. A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva.

![Példa a pod mérőszámok megtekintésére](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Élő adatnézetek használata
A következő szakaszok a különböző élő adatnézetekben használható funkciókat ismertetik.

### <a name="search"></a>Keresés
Az élő adat funkció keresési funkciókat is tartalmaz. A **keresőmezőbe** az eredmények szűréséhez írja be a kulcs szót vagy kifejezést, és a rendszer kiemeli a megfelelő eredményeket a gyors áttekintés érdekében. Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **szűrő** pirulával is korlátozhatja. Attól függően, hogy melyik erőforrást választotta ki, a pirula egy Pod, Namespace vagy fürtöt listáz ki, amelyről a választott forrás van kiválasztva.

![Példa az élő adatkonzol ablaktáblájának szűrésére](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Az élő adatkonzol ablaktáblájának szűrése példa az üzembe helyezéshez](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>Scroll lock és pause

Az autogörgetés felfüggesztéséhez és a panel működésének szabályozásához, amely lehetővé teszi az új adatolvasás manuális görgetését, használhatja a **görgetőgomb** lehetőséget. Az autoscroll újbóli engedélyezéséhez egyszerűen válassza a **Scroll (görgetés** ) lehetőséget. A naplózási vagy az esemény adatainak lekérését is szüneteltetheti a **szüneteltetés** lehetőség kiválasztásával, és ha készen áll a folytatásra, egyszerűen válassza a **Lejátszás** lehetőséget.

![Élő adatkonzol ablaktábla az élő nézet szüneteltetése](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Élő adatkonzol ablaktábla az élő nézet felfüggesztése az üzembe helyezéshez](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>A probléma elhárítása során javasoljuk, hogy csak rövid idő alatt felfüggessze vagy szüneteltesse az autoscroll-et. Ezek a kérések befolyásolhatják a fürtön a Kubernetes API rendelkezésre állását és szabályozását.

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárolja véglegesen az adattárolást. A rendszer a munkamenet során rögzített összes információt törli a böngésző bezárásakor, vagy innen navigál. Az adatok csak a metrika funkció öt perces időszakában jelennek meg a vizualizáción belül. az öt percnél régebbi metrikákat is törli a rendszer. Az élő adatpuffer lekérdezések az ésszerű memóriahasználat korlátain belül.

## <a name="next-steps"></a>Következő lépések

- A Azure Monitor használatának megismeréséhez és az AK-fürt egyéb szempontjainak figyeléséhez lásd: az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md).

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a riasztások, a vizualizációk vagy a fürtök további elemzéséhez.
