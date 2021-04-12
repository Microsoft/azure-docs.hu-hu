---
title: Fogalmak – az Azure Kubernetes Services (ak) Kubernetes alapjai
description: Ismerje meg a Kubernetes alapszintű fürt-és munkaterhelés-összetevőit, valamint azt, hogy azok hogyan kapcsolódnak az Azure Kubernetes szolgáltatás (ak) szolgáltatásaihoz
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105935"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) Kubernetes alapvető fogalmai

Az alkalmazásfejlesztés továbbra is egy tároló alapú megközelítés irányába mozdul el, így egyre nagyobb szükség van az erőforrások összehangolása és kezelése terén. Vezető platformként a Kubernetes megbízható ütemezést biztosít a hibatűrő alkalmazások számítási feladataihoz. Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes-ajánlat, amely tovább egyszerűsíti a tároló-alapú alkalmazások üzembe helyezését és felügyeletét.

Ez a cikk a következőket mutatja be:
* Alapvető Kubernetes infrastruktúra-összetevők:
    * *vezérlő síkja*
    * *csomópontok*
    * *csomópont-készletek*
* Számítási feladatok erőforrásai: 
    * *hüvely*
    * *központi telepítések*
    * *Beállítja* 
* Erőforrások csoportosítása *névterekben*.

## <a name="what-is-kubernetes"></a>Mi az a Kubernetes?

A Kubernetes egy gyorsan változó platform, amely a tároló-alapú alkalmazásokat és a hozzájuk társított hálózatkezelési és tárolási összetevőket kezeli. A Kubernetes az alkalmazás számítási feladataira koncentrál, nem az alapul szolgáló infrastruktúra-összetevőkre. A Kubernetes a központi telepítések deklaratív megközelítését biztosítja, amely az API-k robusztus készletével támogatott a felügyeleti műveletekhez.

A Kubernetes használatával modern, hordozható és szolgáltatásokon alapuló alkalmazásokat hozhat létre és futtathat az alkalmazás-összetevők rendelkezésre állásának előkészítéséhez és kezeléséhez. A Kubernetes olyan állapot nélküli és állapot-nyilvántartó alkalmazásokat is támogat, mint a csapatok előrehaladása a Service-alapú alkalmazások bevezetésével.

Nyílt platformként a Kubernetes lehetővé teszi, hogy alkalmazásai a kívánt programozási nyelv, az operációs rendszer, a könyvtárak vagy az üzenetkezelési busz használatával legyenek felépítve. A meglévő folyamatos integrációs és folyamatos teljesítési (CI/CD) eszközök integrálása a Kubernetes a kiadások ütemezett és üzembe helyezéséhez.

Az AK felügyelt Kubernetes szolgáltatást biztosít, amely csökkenti az üzembe helyezés és az alapvető felügyeleti feladatok összetettségét, például a frissítés koordinálását. Az Azure platform felügyeli az AK vezérlő síkot, és csak az alkalmazásokat futtató AK-csomópontok kell fizetnie. Az AK a nyílt forráskódú Azure Kubernetes Service Engine: [AK-Engine-][aks-engine]re épül.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-fürt architektúrája

A Kubernetes-fürtök két összetevőhöz vannak osztva:

- *Vezérlési sík*: biztosítja a Kubernetes alapvető szolgáltatásait és az alkalmazások számítási feladatainak előkészítését.
- *Csomópontok*: futtassa az alkalmazás munkaterheléseit.

![Kubernetes-vezérlési sík és csomópont-összetevők](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Vezérlősík

Ha AK-fürtöt hoz létre, a rendszer automatikusan létrehozza és konfigurálja a vezérlő síkot. Ez a vezérlési sík a felhasználótól származó felügyelt Azure-erőforrások díjmentesen elérhető. Csak az AK-fürthöz csatolt csomópontok kell fizetnie. A vezérlő síkja és erőforrásai csak abban a régióban találhatók, ahol létrehozta a fürtöt.

A vezérlő síkja a következő alapvető Kubernetes összetevőket tartalmazza:

| Összetevő | Leírás |  
| ----------------- | ------------- |  
| *Kube – apiserver*                                                                                 | Az API-kiszolgáló az alapul szolgáló Kubernetes API-k számára elérhető. Ez az összetevő a felügyeleti eszközök, például `kubectl` a vagy a Kubernetes irányítópultjának interakcióját biztosítja.                                                        |  
| *etcd* | A Kubernetes-fürt és a konfiguráció állapotának fenntartása érdekében a magasan elérhető *etcd* a Kubernetes-en belüli Key Value Store.                                      |  
| *Kube – ütemező*                                                                            | Alkalmazások létrehozásakor vagy méretezése esetén az ütemező határozza meg, hogy mely csomópontok futtathatják a munkaterhelést, és hogyan indíthatók el.                                                                                    |  
| *Kube – vezérlő – kezelő*                                                                            | A vezérlő kezelője több olyan kisebb vezérlőt is lát, amelyek olyan műveleteket hajtanak végre, mint például a hüvelyek replikálása és a csomópont-műveletek kezelése.                                                                  |  

Az AK egy egybérlős vezérlési gépet biztosít dedikált API-kiszolgálóval, ütemező szolgáltatással stb. Megadhatja a csomópontok számát és méretét, és az Azure platform konfigurálja a vezérlési sík és a csomópontok közötti biztonságos kommunikációt. A vezérlési síkon való interakció Kubernetes API-kon keresztül történik, például `kubectl` a Kubernetes-irányítópulton.

Habár a felügyelt vezérlési síkon nem szükséges összetevőket (például egy magasan elérhető *etcd* -tárolót) konfigurálnia, a vezérlő síkja nem érhető el közvetlenül. A Kubernetes-vezérlési sík és a csomópontok frissítése az Azure CLI-n vagy Azure Portalon keresztül történik. A lehetséges problémák elhárításához tekintse át a vezérlési sík naplóit Azure Monitor naplókon keresztül.

A vezérlési sík konfigurálásához vagy közvetlen eléréséhez helyezzen üzembe egy saját Kubernetes-fürtöt az [AK-motor][aks-engine]használatával.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a fürt biztonságához és a frissítésekhez az AK-ban][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Csomópontok és csomópont-készletek

Az alkalmazások és a támogató szolgáltatások futtatásához szükség van egy Kubernetes- *csomópontra*. Az AK-fürtök legalább egy csomóponttal, egy Azure-beli virtuális géppel (VM) rendelkeznek, amely az Kubernetes csomópont-összetevőket és a tároló futtatókörnyezetét futtatja.

| Összetevő | Leírás |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | Az a Kubernetes-ügynök, amely feldolgozza a hangvezérelt kérelmeket a vezérlési síkon, és ütemezi a kért tárolók futtatását.                                                        |  
| *Kube – proxy* | Kezeli a virtuális hálózatkezelést az egyes csomópontokon. A proxy átirányítja a hálózati forgalmat, és kezeli a szolgáltatások és a hüvelyek IP-címzését.                                      |  
| *tároló futtatókörnyezete*                                                                            | Lehetővé teszi, hogy a tároló alkalmazások futtassák és használják a további erőforrásokat, például a virtuális hálózatot és a tárolót. A Kubernetes 1.19 és Node Pools-t használó AK-fürtök `containerd` tároló-futtatókörnyezetként használják. Az Kubernetes-t használó, a Node-készletet megelőzően, a Node-készletek 1,19-es verzióját használó AK-fürtök a [Moby](https://mobyproject.org/) (upstream Docker) tároló-futtatókörnyezet                                                                                    |  


![Azure-beli virtuális gépek és a Kubernetes-csomópontok támogatási erőforrásai](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

A csomópontok Azure-beli virtuális gép mérete határozza meg a tárolási processzorokat, a memóriát, a méretet és a rendelkezésre álló típust (például nagy teljesítményű SSD vagy normál HDD). Tervezze meg a csomópont méretét, hogy az alkalmazásai nagy mennyiségű PROCESSZORt és memóriát vagy nagy teljesítményű tárhelyet igényelhetnek. A kereslet kielégítése érdekében bővítse az AK-fürt csomópontjainak számát.

Az AK-ban a fürt csomópontjaihoz tartozó virtuálisgép-rendszerkép a Ubuntu Linux vagy a Windows Server 2019-alapú. Ha AK-fürtöt hoz létre, vagy bővíti a csomópontok számát, az Azure platform automatikusan létrehozza és konfigurálja a kért számú virtuális gépet. Az ügynökök csomópontjai standard virtuális gépekként vannak kiszámlázva, így a virtuálisgép-méretek (beleértve az [Azure-foglalásokat][reservation-discounts]is) automatikusan érvényesek lesznek.

Helyezzen üzembe saját Kubernetes-fürtöt az [AK-motorral][aks-engine] , ha más gazdagép operációs rendszert, tároló-futtatókörnyezetet vagy különböző egyéni csomagokat használ. A felsőbb rétegbeli `aks-engine` kiadások funkciói és konfigurációs lehetőségeket biztosítanak az AK-fürtök támogatása előtt. Ha azonban a vagy a Moby szolgáltatástól eltérő tároló-futtatókörnyezetet szeretne használni `containerd` , akkor a futtatásával [](https://mobyproject.org/) `aks-engine` konfigurálhatja és telepítheti az aktuális igényeknek megfelelő Kubernetes-fürtöt.

### <a name="resource-reservations"></a>Erőforrás-foglalások

Az AK csomópont-erőforrásokat használ, hogy a csomópont a fürt részeként működjön. Ez a használat a csomópont összes erőforrása és az AK-ban foglalható erőforrások közötti eltérést okozhat. Jegyezze fel ezeket az információkat a felhasználó által telepített hüvelyekre vonatkozó kérelmek és korlátok beállításakor.

Csomópont lefoglalható erőforrásainak kereséséhez futtassa a következő parancsot:
```kubectl
kubectl describe node [NODE_NAME]
```

A csomópontok teljesítményének és funkcióinak fenntartásához az AK az egyes csomópontokon tárolja az erőforrásokat. Mivel a csomópontok nagyobb mértékben növekednek az erőforrásokban, az erőforrás-foglalás a felhasználó által üzembe helyezett hüvelyek kezelésének nagyobb igénye miatt nő.

>[!NOTE]
> Az AK-bővítmények (például a OMS) használatával további csomópont-erőforrásokat fognak használni.

Két típusú erőforrás van fenntartva:

- **CPU**  
    A fenntartott CPU függ a csomópont típusától és a fürt konfigurációjától, ami további funkciók futtatása miatt kevésbé lefoglalható CPU-t eredményezhet.

   | CPU-magok a gazdagépen | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube – fenntartott (millicores)|60|100|140|180|260|420|740|

- **Memória**  
    Az AK által felhasznált memória a két érték összegét tartalmazza.

   1. **`kubelet` démon**   
       A `kubelet` démon minden Kubernetes-ügynök csomópontján telepítve van a tárolók létrehozásának és megszüntetésének kezeléséhez. 
   
        Alapértelmezés szerint az AK-ban a `kubelet` démon rendelkezik a *memóriával. elérhető<750Mi* -kizárási szabály, amely biztosítja, hogy a csomópontnak mindig legalább 750, és minden alkalommal le kell foglalni. Ha egy gazdagép a rendelkezésre álló memória küszöbértéke alatt van, a `kubelet` elindítja a futó hüvelyek egyikét, és felszabadítja a memóriát a gazdagépen.

   2. A kubelet démon által a megfelelő működéshez (*Kube*) tartozó **memória-foglalások degresszív gyakorisága** .
      - az első 4 GB memória 25%-a
      - a következő 4 GB memória 20%-a (legfeljebb 8 GB)
      - a következő 8 GB memória 10%-a (legfeljebb 16 GB)
      - a következő 112 GB memória 6%-a (legfeljebb 128 GB)
      - a 128 GB-nál nagyobb memória 2%-a

Memória-és processzor-kiosztási szabályok:
* A fürt állapotának megfelelően őrizze meg az ügynökök csomópontjait, beleértve néhány üzemeltetési rendszerhüvelyt is. 
* Azt eredményezheti, hogy a csomópont kevésbé lefoglalható memóriát és CPU-t jelent, mint ha nem része a Kubernetes-fürtnek. 

A fenti erőforrás-foglalások nem módosíthatók.

Ha például egy csomópont 7 GB-ot biztosít, akkor a memória 34%-a nem foglalható le, beleértve a 750Mi Hard kilakoltatási küszöbértékét.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

A Kubernetes foglalása mellett az alapul szolgáló Node operációs rendszer a processzor-és memória-erőforrások mennyiségét is lefoglalja az operációs rendszer funkcióinak fenntartásához.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások az alapszintű Scheduler-funkciókhoz az AK-ban][operator-best-practices-scheduler].

### <a name="node-pools"></a>Csomópontkészletek

Az azonos konfiguráció csomópontjai a *csomópont-készletekbe* vannak csoportosítva. A Kubernetes-fürt legalább egy csomópont-készletet tartalmaz. A csomópontok és a méretek kezdeti száma akkor van meghatározva, amikor egy AK-fürtöt hoz létre, amely létrehoz egy *alapértelmezett csomópont-készletet*. Ez az alapértelmezett Node-készlet az AK-ban tartalmazza az ügynök csomópontjait futtató mögöttes virtuális gépeket.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább két (2) csomópontot kell futtatnia az alapértelmezett csomópont-készletben.

Egy AK-fürt méretezése vagy frissítése az alapértelmezett csomópont-készlettel. Dönthet úgy, hogy egy adott csomópont-készletet méretezéssel vagy frissítéssel végez. A frissítési műveletek esetében a futó tárolók a csomópont más csomópontjain vannak ütemezve, amíg az összes csomópont frissítése sikeresen megtörtént.

További információ arról, hogyan használható több Node-készlet az AK-ban: [több csomópontos készlet létrehozása és kezelése a fürthöz az AK-ban][use-multiple-node-pools].

### <a name="node-selectors"></a>Csomópont-választók

Egy több csomópontos készlettel rendelkező AK-fürtben előfordulhat, hogy meg kell adnia a Kubernetes-ütemező számára, hogy melyik csomópont-készletet használja egy adott erőforráshoz. A bejövő vezérlők például nem futnak a Windows Server-csomópontokon. 

A csomópont-választók lehetővé teszik különböző paraméterek (például a Node OS) definiálását a pod ütemezésének vezérléséhez.

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

A Kubernetes *hüvelyek* használatával futtatja az alkalmazás egy példányát. A pod az alkalmazás egy példányát jelöli. 

A hüvelyek általában egy tárolóval rendelkező 1:1-hozzárendeléssel rendelkeznek. A speciális forgatókönyvekben a pod több tárolót is tartalmazhat. A többtárolós hüvelyek egyazon csomóponton vannak ütemezve, és lehetővé teszik a tárolók számára a kapcsolódó erőforrások megosztását.

A pod létrehozásakor megadhatja az *erőforrás-kérelmeket* , hogy bizonyos mennyiségű CPU-vagy memória-erőforrást igényeljen. A Kubernetes ütemező a kérés teljesítéséhez ütemezi a hüvelyek futtatását a rendelkezésre álló erőforrásokkal rendelkező csomóponton. Megadhatja a maximális erőforrás-korlátot is, amely megakadályozza, hogy a pod túl sok számítási erőforrást használ a mögöttes csomópontból. Az ajánlott eljárás az, hogy az összes hüvely erőforrás-korlátait tartalmazza, hogy a Kubernetes ütemező a szükséges, engedélyezett erőforrásokat azonosítsa.

További információ: [Kubernetes hüvelyek][kubernetes-pods] és [Kubernetes Pod életciklusa][kubernetes-pod-lifecycle].

A pod logikai erőforrás, de az alkalmazások munkaterhelései futnak a tárolókban. A hüvelyek általában elmúló, eldobható erőforrások. Az egyénileg ütemezett hüvelyek hiányoznak a magas rendelkezésre állási és redundancia-Kubernetes funkciói. Ehelyett a hüvelyeket Kubernetes- *vezérlők*, például a központi telepítési vezérlő helyezi üzembe és kezeli.

## <a name="deployments-and-yaml-manifests"></a>Központi telepítések és YAML-jegyzékek

Az üzemelő *példányok* a Kubernetes-telepítési vezérlő által kezelt azonos hüvelyeket jelölik. A központi telepítés meghatározza a létrehozandó Pod- *replikák* számát. A Kubernetes Scheduler gondoskodik arról, hogy a további hüvelyek az egészséges csomópontokon legyenek ütemezve, ha a hüvely vagy a csomópont problémákat tapasztal.

A központi telepítések segítségével módosíthatja a hüvelyek, a felhasznált tárolók vagy a csatolt tárolók konfigurációját. A központi telepítési vezérlő:
* Kiüríti és leállítja egy adott számú replikát.
* Replikákat hoz létre az új központi telepítési definícióból.
* Folytatja a folyamatot, amíg a telepítésben szereplő összes replikát frissíti.

Az AK-ban a legtöbb állapot nélküli alkalmazásnak az egyes hüvelyek ütemezése helyett a telepítési modellt kell használnia. A Kubernetes nyomon követheti a telepítési állapotot és az állapotot, így biztosítva, hogy a fürtön belül a szükséges számú replika fusson. Ha külön ütemezik, a hüvelyek nem indulnak újra, ha problémát észlelnek, és nem ütemezik át az aktuális csomópontot, ha a jelenlegi csomópont problémát észlel.

Egy frissítési folyamattal nem szeretné megszakítani a felügyeleti döntéseket, ha az alkalmazáshoz legalább egy rendelkezésre álló példány szükséges. A *Pod-megszakadási költségkeretek* határozzák meg, hogy egy központi telepítés hány replikáját lehet leállítani frissítés vagy csomópont frissítése közben. Ha például *öt (5)* replikája van az üzemelő példányban, megadhat egy *4 (négy)* Pod-megszakadást, hogy csak egy replikát lehessen törölni vagy átütemezni egyszerre. A pod-erőforrások korlátaihoz hasonlóan az ajánlott eljárás az, hogy meghatározza a pod-megszakadási költségvetést olyan alkalmazásokon, amelyekhez legalább egy replika szükséges.

A központi telepítések általában a vagy a alkalmazással hozhatók létre és kezelhetők `kubectl create` `kubectl apply` . Hozzon létre egy központi telepítést egy manifest-fájl YAML formátumban való definiálásával. 

Az alábbi példa az NGINX webkiszolgáló alapszintű telepítését hozza létre. Az üzemelő példány *három (3)* replikát határoz meg, és megköveteli, hogy a *80* -es port nyitva legyen a tárolón. Az erőforrás-kérelmek és-korlátok a PROCESSZORhoz és a memóriához is meg vannak határozva.

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

Összetettebb alkalmazások hozhatók létre, beleértve a YAML-jegyzékben lévő szolgáltatásokat (például a Load balancert).

További információ: Kubernetes- [telepítések][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Csomagkezelő a Helmtal

A [Helm][helm] általában az alkalmazások Kubernetes való kezeléséhez használatos. Az erőforrásokat olyan meglévő nyilvános Helm- *diagramok* létrehozásával és használatával helyezheti üzembe, amelyek az alkalmazás kódjának csomagolt verzióját és a Kubernetes YAML-jegyzékeket tartalmazzák. A Helm-diagramok helyileg vagy távoli adattárban is tárolhatók, például egy [Azure Container Registry Helm chart][acr-helm]-tárházban.

A Helm használatához telepítse a Helm-ügyfelet a számítógépre, vagy használja a [Azure Cloud Shell][azure-cloud-shell]a Helm-ügyfelet. Keressen vagy hozzon létre Helm-diagramokat, majd telepítse őket a Kubernetes-fürtre. További információ: [meglévő alkalmazások telepítése a Helm-ben az AK-ban][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets és DaemonSets

A Kubernetes Scheduler használatával a központi telepítési vezérlő replikákat futtat bármely elérhető erőforrással rendelkező csomóponton. Habár ez a megközelítés elegendő lehet az állapot nélküli alkalmazások számára, a központi telepítési vezérlő nem ideális olyan alkalmazások esetében, amelyeknél a következők szükségesek:
* Állandó elnevezési konvenció vagy tároló. 
* Egy fürtön belüli egyes Select csomópontokon található replika.

A két Kubernetes erőforrás azonban lehetővé teszi az ilyen típusú alkalmazások kezelését:

- A *StatefulSets* az alkalmazások állapotát egy egyedi Pod-életcikluson, például a Storage-on keresztül tartja karban.
- A *DaemonSets* biztosítja a futó példányok minden csomóponton történő futtatását a Kubernetes rendszerindítási folyamatának elején.

### <a name="statefulsets"></a>StatefulSets

A modern alkalmazásfejlesztés gyakran az állapot nélküli alkalmazások kiépítését célozza. Az olyan állapot-nyilvántartó alkalmazások esetében, mint az adatbázis-összetevőket is, használhatja a *StatefulSets*. A központi telepítésekhez hasonlóan a StatefulSet is létrehoz és felügyel legalább egy azonos Pod-t. A StatefulSet lévő replikák az üzembe helyezés, a méretezés, a frissítés és a megszüntetés egy kecses, szekvenciális megközelítését követik. Az elnevezési konvenció, a hálózati nevek és a tárolók megmaradnak, mint a replikák átütemezett StatefulSet.

Adja meg az alkalmazást YAML formátumban a használatával `kind: StatefulSet` . Innen a StatefulSet-vezérlő kezeli a szükséges replikák központi telepítését és felügyeletét. A rendszer az Azure Managed Disks vagy Azure Files által biztosított állandó tárolóba írja az adattárolást. A StatefulSets a mögöttes állandó tárterület marad, még akkor is, ha a StatefulSet törölve lett.

További információ: [Kubernetes StatefulSets][kubernetes-statefulsets].

A StatefulSet lévő replikák ütemezése és futtatása egy AK-fürt bármely elérhető csomópontján történik. Ha gondoskodni szeretne arról, hogy a készletben legalább egy pod egy csomóponton fusson, Daemonset elemet használjon helyette.

### <a name="daemonsets"></a>DaemonSets

Adott naplók vagy figyelés esetén előfordulhat, hogy egy Pod-t kell futtatnia az összes vagy a kijelölt csomóponton. A *daemonset elemet* egy vagy több azonos hüvelyt is használhat, de a daemonset elemet vezérlő biztosítja, hogy a megadott csomópontok a pod egy példányát futtatják.

A Daemonset elemet-vezérlő a fürt rendszerindítási folyamata elején ütemezheti a hüvelyeket a csomópontokon, még az alapértelmezett Kubernetes-ütemező elindítása előtt. Ez a funkció biztosítja, hogy a Daemonset elemet lévő hüvelyek elindulnak, mielőtt a hagyományos hüvelyek egy központi telepítésben vagy StatefulSet vannak ütemezve.

A StatefulSets hasonlóan a Daemonset elemet a YAML definíciójának részeként van definiálva a használatával `kind: DaemonSet` .

További információ: [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Ha a [virtuális csomópontok beépülő modult](virtual-nodes-cli.md#enable-virtual-nodes-addon)használja, a DaemonSets nem hoz létre hüvelyeket a virtuális csomóponton.

## <a name="namespaces"></a>Névterek

A Kubernetes-erőforrások, mint például a hüvelyek és a központi telepítések logikailag csoportosítva vannak egy *névtérbe* egy AK-fürt felosztásához, és az erőforrásokhoz való hozzáférés korlátozása, megtekintése vagy kezelése. Létrehozhat például névtereket az üzleti csoportok elkülönítéséhez. A felhasználók csak a hozzájuk rendelt névterekben lévő erőforrásokkal tudnak kommunikálni.

![Kubernetes névterek az erőforrások és alkalmazások logikai elosztásához](media/concepts-clusters-workloads/namespaces.png)

Ha AK-fürtöt hoz létre, a következő névterek érhetők el:

| Névtér | Description |  
| ----------------- | ------------- |  
| *alapértelmezett*                                                                                 | A hüvelyek és a központi telepítések alapértelmezés szerint jönnek létre, ha nincs megadva. Kisebb környezetekben az alkalmazások közvetlenül az alapértelmezett névtérbe helyezhetők, további logikai elkülönítések létrehozása nélkül. Ha együttműködik a Kubernetes API-val, például a `kubectl get pods` -val, akkor az alapértelmezett névtér lesz használatban, ha nincs megadva.                                                        |  
| *kube-system* | Ahol az alapvető erőforrások léteznek, például a hálózati szolgáltatások, például a DNS vagy a proxy, vagy a Kubernetes irányítópult. Általában nem telepítheti saját alkalmazásait ebbe a névtérbe.                                      |  
| *kube-public*                                                                            | Általában nincs használatban, de használhatók a teljes fürtön látható erőforrások számára, és bármely felhasználó megtekinthető.                                                                                    |  


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
