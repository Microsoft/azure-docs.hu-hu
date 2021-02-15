---
title: Fogalmak – az Azure Kubernetes Services (ak) Kubernetes alapjai
description: Ismerje meg a Kubernetes alapszintű fürt-és munkaterhelés-összetevőit, valamint azt, hogy azok hogyan kapcsolódnak az Azure Kubernetes szolgáltatás (ak) szolgáltatásaihoz
services: container-service
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 7485631660395e03c558167c321e6091c6fac755
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373232"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) Kubernetes alapvető fogalmai

Mivel az alkalmazásfejlesztés egy tároló alapú megközelítés irányába mozdul el, fontos a szükséges erőforrások összehangolása és kezelése. A Kubernetes a vezető platform, amely lehetővé teszi a hibatűrő alkalmazások számítási feladatainak megbízható ütemezését. Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes-ajánlat, amely tovább egyszerűsíti a tároló-alapú alkalmazások üzembe helyezését és felügyeletét.

Ez a cikk bemutatja a legfontosabb Kubernetes infrastruktúra-összetevőket, például a *vezérlési síkot*, a *csomópontokat* és a *csomópont-készleteket*. A munkaterhelések erőforrásai, például a *hüvelyek*, a *központi telepítések* és a *készletek* , valamint az erőforrások *névterek* szerinti csoportosítása is bevezethető.

## <a name="what-is-kubernetes"></a>Mi az a Kubernetes?

A Kubernetes egy gyorsan változó platform, amely a tároló-alapú alkalmazásokat és a hozzájuk társított hálózatkezelési és tárolási összetevőket kezeli. A hangsúly az alkalmazás munkaterhelésén, nem pedig az alapul szolgáló infrastruktúra-összetevőkön van. A Kubernetes a központi telepítések deklaratív megközelítését biztosítja, amely az API-k robusztus készletével támogatott a felügyeleti műveletekhez.

Létrehozhat és futtathat modern, hordozható, Kubernetes-alapú alkalmazásokat, amelyek kihasználják az alkalmazás-összetevők rendelkezésre állásának előkészítését és felügyeletét. A Kubernetes olyan állapot nélküli és állapot-nyilvántartó alkalmazásokat is támogat, mint a csapatok előrehaladása a Service-alapú alkalmazások bevezetésével.

Nyílt platformként a Kubernetes lehetővé teszi, hogy alkalmazásai a kívánt programozási nyelv, az operációs rendszer, a könyvtárak vagy az üzenetkezelési busz használatával legyenek felépítve. A meglévő folyamatos integrációs és folyamatos teljesítési (CI/CD) eszközök integrálása a Kubernetes a kiadások ütemezett és üzembe helyezéséhez.

Az Azure Kubernetes Service (ak) olyan felügyelt Kubernetes szolgáltatást biztosít, amely csökkenti az üzembe helyezés és az alapvető felügyeleti feladatok összetettségét, beleértve a frissítések koordinálását is. Az AK-vezérlési gépet az Azure platform felügyeli, és csak az alkalmazásokat futtató AK-csomópontok kell fizetnie. Az AK-t a nyílt forráskódú Azure Kubernetes Service Engine ([AK-motor][aks-engine]) fölé építették.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-fürt architektúrája

A Kubernetes-fürtök két összetevőhöz vannak osztva:

- A *vezérlési sík* biztosítja a Kubernetes alapvető szolgáltatásait és az alkalmazások számítási feladatainak előkészítését.
- Az alkalmazás munkaterheléseit futtató *csomópontok* .

![Kubernetes-vezérlési sík és csomópont-összetevők](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Vezérlősík

Ha AK-fürtöt hoz létre, a rendszer automatikusan létrehozza és konfigurálja a vezérlő síkot. Ez a vezérlési sík a felhasználótól kivont felügyelt Azure-erőforrásként van megadva. A vezérlő síkja nem jár díjmentesen, csak az AK-fürt részét képező csomópontok. A vezérlő síkja és erőforrásai csak abban a régióban találhatók, ahol létrehozta a fürtöt.

A vezérlő síkja a következő alapvető Kubernetes összetevőket tartalmazza:

- *Kube-apiserver* – az API-kiszolgáló az alapul szolgáló Kubernetes API-k által elérhetővé vált. Ez az összetevő a felügyeleti eszközök, például `kubectl` a vagy a Kubernetes irányítópultjának interakcióját biztosítja.
- *etcd* – a Kubernetes-fürt és a konfiguráció állapotának fenntartása érdekében a magasan elérhető *etcd* a Kubernetes belüli Key Value Store.
- *Kube-Scheduler* – alkalmazások létrehozásakor vagy méretezéskor az ütemező meghatározza, hogy mely csomópontok futtathatják a munkaterhelést, és elindítják azokat.
- *Kube-Controller-Manager* – a vezérlő kezelője több kisebb vezérlőt is lát, amelyek olyan műveleteket hajtanak végre, mint például a hüvelyek replikálása és a csomópontok kezelése.

Az AK egy egybérlős vezérlési gépet biztosít dedikált API-kiszolgálóval, ütemező szolgáltatással stb. Megadhatja a csomópontok számát és méretét, és az Azure platform konfigurálja a vezérlési sík és a csomópontok közötti biztonságos kommunikációt. A vezérlési síkon való interakció Kubernetes API-kon keresztül történik, például `kubectl` a Kubernetes-irányítópulton.

Ez a felügyelt vezérlési sík azt jelenti, hogy nem kell olyan összetevőket konfigurálnia, mint például egy magasan elérhető *etcd* -tároló, de ez azt is jelenti, hogy nem fér hozzá közvetlenül a vezérlő síkja. A Kubernetes-re való frissítés az Azure CLI-n vagy Azure Portalon keresztül történik, amely frissíti a vezérlő síkot, majd a csomópontokat. A lehetséges problémák elhárításához tekintse át a vezérlési sík naplóit Azure Monitor naplókon keresztül.

Ha a vezérlési síkot egy bizonyos módon kell konfigurálnia, vagy közvetlen hozzáférésre van szüksége, a saját Kubernetes-fürtöt az [AK-motor][aks-engine]használatával is üzembe helyezheti.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a fürt biztonságához és a frissítésekhez az AK-ban][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Csomópontok és csomópont-készletek

Az alkalmazások és a támogató szolgáltatások futtatásához szükség van egy Kubernetes- *csomópontra*. Egy AK-fürt legalább egy csomóponttal rendelkezik, amely egy Azure-beli virtuális gép (VM), amely a Kubernetes csomópont-összetevőket és a tároló-futtatókörnyezetet futtatja:

- A az a `kubelet` Kubernetes-ügynök, amely feldolgozza a hangvezérelt kérelmeket a vezérlési síkon, és ütemezi a kért tárolók futtatását.
- A virtuális hálózatkezelést a *Kube-proxy* kezeli az egyes csomópontokon. A proxy átirányítja a hálózati forgalmat, és kezeli a szolgáltatások és a hüvelyek IP-címzését.
- A *Container Runtime* az az összetevő, amely lehetővé teszi, hogy a tároló alkalmazások futtassák és használják a további erőforrásokat, például a virtuális hálózatot és a tárolót. Az AK-ban a Moby a tároló futtatókörnyezetként van használatban.

![Azure-beli virtuális gépek és a Kubernetes-csomópontok támogatási erőforrásai](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

A csomópontok Azure-beli virtuálisgép-mérete határozza meg, hogy hány processzor, mennyi memória, valamint a tárterület mérete és típusa érhető el (például nagy teljesítményű SSD vagy normál HDD). Ha várhatóan szükség van olyan alkalmazásokra, amelyek nagy mennyiségű PROCESSZORt és memóriát vagy nagy teljesítményű tárhelyet igényelnek, a csomópont méretét ennek megfelelően tervezze meg. Azt is megteheti, hogy az AK-fürtben lévő csomópontok számát az igények kielégítése érdekében bővíti.

Az AK-ban a fürt csomópontjaihoz tartozó virtuálisgép-rendszerkép jelenleg Ubuntu Linux vagy Windows Server 2019 rendszeren alapul. Ha AK-fürtöt hoz létre vagy bővíti a csomópontok számát, az Azure platform létrehozza a kért számú virtuális gépet, és konfigurálja azokat. Nincs kézi konfiguráció a végrehajtáshoz. Az ügynök csomópontjai standard virtuális gépekként vannak kiszámlázva, ezért a rendszer automatikusan alkalmazza az Ön által használt virtuálisgép-mérethez tartozó kedvezményeket (beleértve az [Azure-foglalásokat][reservation-discounts]is).

Ha egy másik gazdagép operációs rendszert, tároló-futtatókörnyezetet vagy egyéni csomagokat is kell használnia, a saját Kubernetes-fürtöt az [AK-motorral][aks-engine]is üzembe helyezheti. A felsőbb rétegbeli `aks-engine` kiadások funkciói és konfigurációs beállításokat biztosítanak, mielőtt azok hivatalosan támogatottak legyenek az AK-fürtökben. Ha például a Moby-től eltérő tároló-futtatókörnyezetet szeretne használni, a használatával `aks-engine` konfigurálhatja és üzembe helyezheti az aktuális igényeknek megfelelő Kubernetes-fürtöt.

### <a name="resource-reservations"></a>Erőforrás-foglalások

A csomópont-erőforrásokat az AK használja, hogy a Node függvényt a fürt részeként végezze el. Ez a használat ellentmondást okozhat a csomópont összes erőforrása és az AK-beli használat során kiosztható erőforrások között. Fontos megjegyezni, hogy a felhasználók által telepített hüvelyek kéréseinek és korlátainak beállításakor vegye figyelembe ezeket az információkat.

Csomópont lefoglalható erőforrásainak kereséséhez futtassa a következő parancsot:
```kubectl
kubectl describe node [NODE_NAME]
```

A csomópontok teljesítményének és funkcióinak fenntartásához az erőforrásokat az egyes csomópontok az AK szerint vannak lefoglalva. Mivel a csomópontok nagyobb mértékben növekednek az erőforrásokban, az erőforrás-foglalás a felhasználó által üzembe helyezett hüvelyek nagyobb mennyiségű felügyeletének köszönhetően nő.

>[!NOTE]
> Az AK-bővítmények (például a OMS) használatával további csomópont-erőforrásokat fognak használni.

Két típusú erőforrás van fenntartva:

- A **CPU** által lefoglalt CPU függ a csomópont típusától és a fürt konfigurációjától, ami további funkciók futtatása miatt kevésbé lefoglalható processzort eredményezhet

   | CPU-magok a gazdagépen | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube – fenntartott (millicores)|60|100|140|180|260|420|740|

- Az AK által használt **memória** -memória két érték összegét foglalja magában.

   1. A kubelet démon az összes Kubernetes-ügynök csomópontján telepítve van a tárolók létrehozásának és megszüntetésének kezeléséhez. Alapértelmezés szerint az AK-ban ez a démon a következő kiürítési szabállyal rendelkezik: *memória. rendelkezésre álló<750Mi*, ami azt jelenti, hogy egy csomópontnak mindig legalább 750, de mindenkor lefoglalható kell lennie.  Ha egy gazdagép a rendelkezésre álló memória küszöbértéke alá esik, a kubelet leállítja az egyik futó hüvelyt, hogy szabad memóriát szabadítson fel a gazdagépen, és megvédje azt. Ez a művelet akkor aktiválódik, ha a rendelkezésre álló memória a 750Mi küszöbértékén túl csökken.

   2. A második érték a kubelet démon által a megfelelő működéshez (Kube) tartozó lefoglalt memória-foglalások degresszív gyakorisága.
      - az első 4 GB memória 25%-a
      - a következő 4 GB memória 20%-a (legfeljebb 8 GB)
      - a következő 8 GB memória 10%-a (legfeljebb 16 GB)
      - a következő 112 GB memória 6%-a (legfeljebb 128 GB)
      - a 128 GB-nál nagyobb memória 2%-a

A memóriára és a CPU-elosztásra vonatkozó fenti szabályok az ügynök csomópontjainak kifogástalan megőrzésére szolgálnak, beleértve a fürt állapotának kritikus fontosságú részét képező üzemeltetési rendszerhüvelyeket is. Ezek a kiosztási szabályok azt is okozzák, hogy a csomópont kevésbé lefoglalható memóriát és CPU-t jelent, mint általában, ha nem része egy Kubernetes-fürtnek. A fenti erőforrás-foglalások nem módosíthatók.

Ha például egy csomópont 7 GB-ot biztosít, akkor a memória 34%-a nem foglalható le, beleértve a 750Mi Hard kilakoltatási küszöbértékét.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

A Kubernetes foglalása mellett az alapul szolgáló Node operációs rendszer a processzor-és memória-erőforrások mennyiségét is lefoglalja az operációs rendszer funkcióinak fenntartásához.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások az alapszintű Scheduler-funkciókhoz az AK-ban][operator-best-practices-scheduler].

### <a name="node-pools"></a>Csomópontkészletek

Az azonos konfiguráció csomópontjai a *csomópont-készletekbe* vannak csoportosítva. A Kubernetes-fürt egy vagy több csomópontot tartalmaz. A csomópontok és a méretek kezdeti száma akkor van meghatározva, amikor egy AK-fürtöt hoz létre, amely létrehoz egy *alapértelmezett csomópont-készletet*. Ez az alapértelmezett Node-készlet az AK-ban tartalmazza az ügynök csomópontjait futtató mögöttes virtuális gépeket.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben.

AK-fürt méretezése vagy frissítése esetén a művelet az alapértelmezett csomópont-készleten történik. Dönthet úgy is, hogy egy adott csomópont-készletet méretezéssel vagy frissítéssel végez. A frissítési műveletek esetében a futó tárolók a csomópont más csomópontjain vannak ütemezve, amíg az összes csomópont frissítése sikeresen megtörtént.

További információ arról, hogyan használható több Node-készlet az AK-ban: [több csomópontos készlet létrehozása és kezelése a fürthöz az AK-ban][use-multiple-node-pools].

### <a name="node-selectors"></a>Csomópont-választók

A több csomópontot tartalmazó AK-fürtökben előfordulhat, hogy meg kell adnia a Kubernetes-ütemező számára, hogy melyik csomópont-készletet kell használnia egy adott erőforráshoz. A bejövő vezérlők például nem futnak a Windows Server-csomópontokon. A csomópont-választók lehetővé teszik különböző paraméterek (például a csomópont operációs rendszer) definiálását annak vezérléséhez, hogy a pod legyen ütemezve.

A következő alapvető példa egy NGINX-példányt ütemezhet egy Linux-csomóponton a *"Beta.kubernetes.IO/os"* csomópont-választó használatával: Linux:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

További információ a hüvelyek ütemezésének szabályozásáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Hüvely

A Kubernetes *hüvelyek* használatával futtatja az alkalmazás egy példányát. A pod az alkalmazás egy példányát jelöli. A hüvelyek általában egy tárolóval rendelkező 1:1-es hozzárendeléssel rendelkeznek, bár a pod több tárolót is tartalmazhat. Ezek a többtárolós hüvelyek ugyanazon a csomóponton vannak ütemezve, és lehetővé teszik a tárolók számára a kapcsolódó erőforrások megosztását.

A pod létrehozásakor megadhatja az *erőforrás-kérelmeket* , hogy bizonyos mennyiségű CPU-vagy memória-erőforrást igényeljen. A Kubernetes ütemező megkísérli a hüvelyek ütemezését, hogy az elérhető erőforrásokkal rendelkező csomóponton fusson a kérelem teljesítése érdekében. Megadhatja a maximális erőforrás-korlátot is, amely megakadályozza, hogy egy adott Pod túl sok számítási erőforrást fogyasztson a mögöttes csomópontból. Az ajánlott eljárás az, hogy az összes hüvely erőforrás-korlátait tartalmazza, hogy a Kubernetes Scheduler megértse, mely erőforrások szükségesek és engedélyezettek.

További információ: [Kubernetes hüvelyek][kubernetes-pods] és [Kubernetes Pod életciklusa][kubernetes-pod-lifecycle].

A pod logikai erőforrás, de a tároló (k), ahol az alkalmazás munkaterhelései futnak. A hüvelyek általában elmúló, eldobható erőforrások, és egyénileg ütemezett hüvelyek hiányoznak az Kubernetes által biztosított magas rendelkezésre állási és redundancia-funkciók közül. Ehelyett a hüvelyeket Kubernetes- *vezérlők*, például a központi telepítési vezérlő helyezi üzembe és kezeli.

## <a name="deployments-and-yaml-manifests"></a>Központi telepítések és YAML-jegyzékek

A *központi telepítés* egy vagy több azonos hüvelyt jelöl, amelyet a Kubernetes telepítési vezérlő kezel. A központi telepítés meghatározza a létrehozandó *replikák* (hüvelyek) számát, és a Kubernetes ütemező gondoskodik arról, hogy ha a hüvelyek vagy a csomópontok problémákba ütköznek, a további hüvelyek az egészséges csomópontokon vannak ütemezve.

A központi telepítések segítségével módosíthatja a hüvelyek, a felhasznált tárolók vagy a csatolt tárolók konfigurációját. A központi telepítési vezérlő leállítja a megadott számú replikát, létrehozza a replikákat az új központi telepítési definícióból, és addig folytatja a folyamatot, amíg a központi telepítésben szereplő összes replikát nem frissíti.

Az AK-ban a legtöbb állapot nélküli alkalmazásnak az egyes hüvelyek ütemezése helyett a telepítési modellt kell használnia. A Kubernetes nyomon követheti az üzemelő példányok állapotát és állapotát, így biztosítva, hogy a fürtön belül a szükséges számú replika fusson. Ha csak egyes hüvelyeket ütemezhet, a hüvelyek nem indulnak újra, ha probléma lép fel, és nem lettek átütemezett állapotba, ha az aktuális csomópont problémát észlel.

Ha egy alkalmazáshoz a példányok kvóruma szükséges, hogy mindig elérhetők legyenek a felügyeleti döntésekhez, nem szeretné, hogy a frissítési folyamat megszakítsa ezt a képességet. A *Pod-megszakadási költségkeretekkel* határozható meg, hogy egy központi telepítés hány replikáját lehet leállítani a frissítés vagy a csomópont frissítése során. Ha például *öt (5)* replikája van az üzemelő példányban, megadhatja a *4* . Pod-megszakadást, hogy egyszerre csak egy replikát töröljen vagy ütemezzen. A pod-erőforrások korlátaihoz hasonlóan a legjobb megoldás az, ha a pod-megszakadási költségvetést olyan alkalmazásokhoz határozza meg, amelyekhez legalább egy replika szükséges.

A központi telepítések általában a vagy a alkalmazással hozhatók létre és kezelhetők `kubectl create` `kubectl apply` . Központi telepítés létrehozásához meg kell adnia egy jegyzékfájlt a YAML (YAML nem Markup Language) formátumban. Az alábbi példa az NGINX webkiszolgáló alapszintű telepítését hozza létre. Az üzemelő példány *három (3)* replikát határoz meg, és megköveteli, hogy a *80* -es port nyitva legyen a tárolón. Az erőforrás-kérelmek és-korlátok a PROCESSZORhoz és a memóriához is meg vannak határozva.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Összetettebb alkalmazások hozhatók létre, beleértve a YAML-jegyzékben található terheléselosztó szolgáltatásokat is.

További információ: Kubernetes- [telepítések][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Csomagkezelő a Helmtal

A Kubernetes-alkalmazások kezelésének közös megközelítése a [Helm][helm]. Létrehozhat és használhat meglévő nyilvános Helm- *diagramokat* , amelyek az alkalmazás kódjának csomagolt verzióját és Kubernetes YAML-jegyzékeket tartalmaznak az erőforrások üzembe helyezéséhez. Ezek a Helm-diagramok helyileg, vagy gyakran egy távoli adattárban, például egy [Azure Container Registry Helm chart][acr-helm]-tárházban is tárolhatók.

A Helm használatához telepítse a Helm-ügyfelet a számítógépre, vagy használja a [Azure Cloud Shell][azure-cloud-shell]a Helm-ügyfelet. A-ügyféllel kereshet vagy létrehozhat Helm-diagramokat, majd telepítheti őket a Kubernetes-fürtbe. További információ: [meglévő alkalmazások telepítése a Helm-ben az AK-ban][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets és DaemonSets

A központi telepítési vezérlő a Kubernetes ütemező használatával futtat egy adott számú replikát az elérhető erőforrásokkal rendelkező bármely elérhető csomóponton. Az üzemelő példányok használatának ezen megközelítése elegendő lehet az állapot nélküli alkalmazások számára, de nem olyan alkalmazásokhoz, amelyek állandó elnevezési konvenciót vagy tárolót igényelnek. Azon alkalmazások esetében, amelyeknek replikát kell megadniuk az egyes csomópontokon vagy a kijelölt csomópontokon egy fürtön belül, a központi telepítési vezérlő nem vizsgálja meg, hogyan oszlanak meg a replikák a csomópontok között.

Az ilyen típusú alkalmazások kezeléséhez két Kubernetes-erőforrás szükséges:

- *StatefulSets* – az alkalmazások állapotának fenntartása egy adott Pod-életcikluson túl, például a Storage-ban.
- *DaemonSets* – ellenőrizze, hogy fut-e a futó példányok minden csomóponton, a Kubernetes rendszerindítási folyamatának elején.

### <a name="statefulsets"></a>StatefulSets

A modern alkalmazásfejlesztés gyakran az állapot nélküli alkalmazások megvalósítását célozza, de a *StatefulSets* állapot-nyilvántartó alkalmazásokhoz, például adatbázis-összetevőket tartalmazó alkalmazásokhoz is használható. Egy StatefulSet hasonló a központi telepítéshez, amely egy vagy több azonos hüvelyt hoz létre és felügyel. A StatefulSet lévő replikák az üzembe helyezés, a méretezés, a frissítések és a megszakítások kecses, szekvenciális megközelítését követik. A StatefulSet (mint replikák átütemezett) az elnevezési konvenció, a hálózati nevek és a tárterület megmaradnak.

Az alkalmazást YAML formátumban kell megadni `kind: StatefulSet` , és a StatefulSet vezérlő kezeli a szükséges replikák telepítését és felügyeletét. A rendszer az Azure Managed Disks vagy Azure Files által biztosított állandó tárolóba írja az adattárolást. A StatefulSets esetében a mögöttes állandó tárterület még a StatefulSet törlésekor is marad.

További információ: [Kubernetes StatefulSets][kubernetes-statefulsets].

A StatefulSet lévő replikák ütemezése és futtatása egy AK-fürt bármely elérhető csomópontján történik. Ha meg kell győződnie arról, hogy a készletben legalább egy pod egy csomóponton fut, használhat egy Daemonset elemet.

### <a name="daemonsets"></a>DaemonSets

Bizonyos naplók vagy figyelési igények esetében előfordulhat, hogy egy adott Pod-t kell futtatnia az összes vagy a kijelölt csomóponton. Újra *daemonset elemet* egy vagy több azonos hüvely üzembe helyezéséhez, de a daemonset elemet vezérlő biztosítja, hogy a megadott csomópontok a pod egy példányát futtatják.

A Daemonset elemet-vezérlő a fürt rendszerindítási folyamata elején ütemezheti a hüvelyeket a csomópontokon, még az alapértelmezett Kubernetes-ütemező elindítása előtt. Ez a funkció biztosítja, hogy a Daemonset elemet lévő hüvelyek elindulnak, mielőtt a hagyományos hüvelyek egy központi telepítésben vagy StatefulSet vannak ütemezve.

A StatefulSets hasonlóan a Daemonset elemet a YAML definíciójának részeként van definiálva a használatával `kind: DaemonSet` .

További információ: [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Ha a [virtuális csomópontok beépülő modult](virtual-nodes-cli.md#enable-virtual-nodes-addon)használja, a DaemonSets nem hoz létre hüvelyeket a virtuális csomóponton.

## <a name="namespaces"></a>Névterek

A Kubernetes-erőforrások, például a hüvelyek és a központi telepítések logikailag vannak csoportosítva egy *névtérbe*. Ezek a csoportok lehetővé teszik az AK-fürtök logikai elosztását, valamint az erőforrások létrehozásához, megtekintéséhez vagy kezeléséhez való hozzáférés korlátozását. Létrehozhat névtereket is az üzleti csoportok elkülönítéséhez, például:. A felhasználók csak a hozzájuk rendelt névterekben lévő erőforrásokkal tudnak kommunikálni.

![Kubernetes névterek az erőforrások és alkalmazások logikai elosztásához](media/concepts-clusters-workloads/namespaces.png)

Ha AK-fürtöt hoz létre, a következő névterek érhetők el:

- *alapértelmezett* – ez a névtér, ahol a hüvelyek és a központi telepítések alapértelmezés szerint jönnek létre, ha nincs megadva. Kisebb környezetekben az alkalmazások közvetlenül az alapértelmezett névtérbe helyezhetők, további logikai elkülönítések létrehozása nélkül. Ha együttműködik a Kubernetes API-val, például a `kubectl get pods` -val, akkor az alapértelmezett névtér lesz használatban, ha nincs megadva.
- *Kube-System* – ez a névtér az alapvető erőforrások, például a DNS és a proxy, illetve a Kubernetes irányítópultja. Általában nem telepítheti saját alkalmazásait ebbe a névtérbe.
- *Kube – nyilvános* – ez a névtér általában nincs használatban, de a teljes fürtön látható erőforrások számára is használható, és bármely felhasználó megtekinthető.

További információ: Kubernetes- [névterek][kubernetes-namespaces].

## <a name="next-steps"></a>Következő lépések

Ez a cikk a legfontosabb Kubernetes-összetevőket ismerteti, valamint azt, hogy ezek hogyan vonatkoznak az AK-fürtökre. Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-hozzáférés és-identitás][aks-concepts-identity]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-tároló][aks-concepts-storage]
- [Kubernetes/AK-skála][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
