---
title: Service Mesh megnyitása (előzetes verzió)
description: A Service Mesh (OSM) megnyitása az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 0052c8d2f9b85c34d50a3e9d01253ecaf2d02bab
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106713"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>A Service Mesh (ak) bővítmény megnyitása (előzetes verzió)

## <a name="overview"></a>Áttekintés

A [nyílt Service Mesh (OSM)](https://docs.openservicemesh.io/) egy egyszerű, bővíthető, Felhőbeli natív szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy egységes módon kezeljék, biztonságossá teszik és felhasználják a hatékony, dinamikus üzemi környezetekhez szükséges bevezetési funkciókat.

Az OSM egy megbízottat-alapú Kubernetes-t futtat, és az [SMI](https://smi-spec.io/) API-kkal konfigurálható, és úgy működik, hogy egy megbízottat-tárolót használ az alkalmazás minden példánya mellett. A megbízottat tartalmazó proxy a hozzáférés-vezérlési szabályzatok körébe tartozó szabályokat tartalmaz, és végrehajtja az útválasztási konfiguráció megvalósítását és a metrikák rögzítését. A vezérlő síkja folyamatosan konfigurálja a proxykat, hogy a házirendek és az útválasztási szabályok naprakészek legyenek, és gondoskodjon a proxyk kifogástalan állapotáról.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Képességek és szolgáltatások

Az OSM a következő funkciókat és funkciókat biztosítja, hogy az Azure Kubernetes Service-(ak-) fürtökhöz felhőalapú natív szolgáltatási hálót biztosítson:

- Biztonságos szolgáltatás és szolgáltatás közötti kommunikáció a mTLS engedélyezésével

- Az alkalmazások egyszerűen bevihetők a rácsvonalra azáltal, hogy engedélyezik a megbízottat proxy automatikus befecskendezését

- Egyszerű és transzparens konfigurációk a forgalom átváltásához a központi telepítések során

- A szolgáltatások részletes hozzáférés-vezérlési házirendjeinek meghatározása és végrehajtása

- Az alkalmazás-metrikák megfigyelése és betekintése hibakeresési és figyelési szolgáltatásokhoz

- Integráció külső tanúsítványkezelő szolgáltatásokkal/megoldásokkal egy csatlakoztatható felülettel

## <a name="scenarios"></a>Forgatókönyvek

Az OSM a következő helyzetekben nyújt segítséget az AK-beli üzembe helyezésekhez:

- Titkosított kommunikáció biztosítása a fürtben telepített szolgáltatási végpontok között

- HTTP/HTTPS és TCP forgalom engedélyezése a hálóban

- A súlyozott forgalom szabályozásának konfigurálása két vagy több szolgáltatás között az A/B vagy A Kanári környezetekben

- KPI-k gyűjtése és megtekintése az alkalmazások forgalmában

## <a name="osm-service-quotas-and-limits-preview"></a>Az OSM szolgáltatási kvótái és korlátai (előzetes verzió)

A szolgáltatás kvótái és korlátai esetében az OSM előzetes verziójának korlátai az AK [-kvóták és a regionális korlátok oldalon](https://docs.microsoft.com/azure/aks/quotas-skus-regions)találhatók.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Ne kísérelje meg az OSM telepítését a bináris használatával `osm install` . Ez az OSM olyan telepítését eredményezi, amely nincs integrálva az AK-beli bővítményként.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Az `AKS-OpenServiceMesh` előzetes verzió funkciójának regisztrálása

Ha olyan AK-fürtöt szeretne létrehozni, amely használhatja a Service Mesh-bővítményt, engedélyeznie kell a `AKS-OpenServiceMesh` szolgáltatás jelölőjét az előfizetésében.

Regisztrálja a `AKS-OpenServiceMesh` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Néhány percet vesz igénybe, amíg az állapot _regisztrálva_ jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a _Microsoft. tárolószolgáltatás_ erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Az Open Service Mesh (OSM) Azure Kubernetes Service (ak) bővítmény telepítése új AK-fürthöz

Egy új AK-alapú fürt üzembe helyezési forgatókönyve esetén egy AK-fürt vadonatúj üzembe helyezésével kezdődik, amely lehetővé teszi az OSM-bővítményt a fürt létrehozási műveletében.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal. A következő példában létrehozunk egy _myOsmAksGroup_ nevű erőforráscsoportot a _eastus2_ helyen (régió):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>AK-fürt üzembe helyezése az OSM-bővítmény engedélyezésével

Most üzembe kell helyeznie egy új AK-fürtöt az OSM-bővítmény engedélyezésével.

> [!NOTE]
> Vegye figyelembe, hogy a következő AK üzembe helyezési parancs az operációs rendszer elmúló lemezeit használja. További információt itt talál a [elmúló operációsrendszer-lemezekről az AK](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) -hoz

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AK-fürt hozzáférési hitelesítő adatainak beolvasása

Hozzáférési hitelesítő adatok beszerzése az új felügyelt Kubernetes-fürthöz.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>A Open Service Mesh (OSM) Azure Kubernetes szolgáltatás (ak) bővítményének engedélyezése meglévő AK-fürthöz

Meglévő AK-beli fürt esetén az OSM-bővítményt egy már üzembe helyezett, meglévő AK-fürthöz kell engedélyeznie.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Az OSM-bővítmény engedélyezése meglévő AK-fürthöz

Az AK OSM-bővítmény engedélyezéséhez futtatnia kell a (z) `az aks enable-addons --addons` paramétert áthaladó parancsot. `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Az alábbi kimenethez hasonló kimenetnek kell megjelennie, hogy erősítse meg, hogy az AK-beli OSM-bővítmény telepítve van.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Az AK OSM-bővítmény telepítésének ellenőrzése

Több parancs futtatásával ellenőrizhető, hogy az AK-beli OSM-bővítmény összes összetevője engedélyezve van-e és fut-e:

Először is lekérheti a fürt kiegészítő profiljait a telepített bővítmények engedélyezett állapotának vizsgálatához. A következő parancsnak "true" értéket kell visszaadnia.

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

A következő `kubectl` parancsok jelentést készítenek az OSM-Controller állapotáról.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Az AK OSM-bővítmény elérése

Jelenleg a configmap keresztül érheti el és konfigurálhatja az OSM-vezérlő konfigurációját. Az OSM-vezérlő konfigurációs beállításainak megtekintéséhez kérdezze le az OSM-config configmap, `kubectl` és tekintse meg a konfigurációs beállításait.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Az OSM-configmap kimenetének a következőhöz hasonlóan kell kinéznie:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Figyelje meg, hogy a **permissive_traffic_policy_mode** az **igaz** értékre van konfigurálva. Az OSM-ben elérhető adatforgalmi házirend mód olyan mód, amelyben az [SMI](https://smi-spec.io/) forgalmi szabályzatának kényszerítése megkerül. Ebben a módban az OSM automatikusan felveszi a Service Mesh szolgáltatás részét képező szolgáltatásokat, és a program az egyes megbízotti proxyk által biztosított adatforgalmi házirend-szabályok alapján képes kommunikálni ezekkel a szolgáltatásokkal.

> [!WARNING]
> A továbblépés előtt ellenőrizze, hogy a nem érvényes adatforgalmi házirend mód értéke TRUE (igaz) értékre van-e állítva, ha nem, akkor az alábbi parancs használatával állítsa **igaz** értékre.

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Telepítsen egy új alkalmazást, amelyet az Open Service Mesh (OSM) Azure Kubernetes szolgáltatás (ak) bővítménye kezel.

### <a name="before-you-begin"></a>Előkészületek

Az ebben a bemutatóban ismertetett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.19+` és újabb, KUBERNETES RBAC engedélyezve), létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat (ha segítségre van szüksége ezen elemek bármelyikéhez, majd látnia kell [az AK-](./kubernetes-walkthrough.md)ra vonatkozó útmutatót, és telepítettük az AK-beli OSM-bővítményt.

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.20.0 vagy újabb verzió
- A `aks-preview` bővítmény 0.5.5 vagy újabb verziója
- Az OSM v 0.8.0 vagy újabb verziója
- apt – a jQ telepítésének beolvasása

### <a name="create-namespaces-for-the-application"></a>Névterek létrehozása az alkalmazáshoz

Ebben az útmutatóban a következő Kubernetes-szolgáltatásokkal rendelkező OSM könyvesbolt-alkalmazást fogjuk használni:

- bookbuyer
- bookthief
- könyvesbolt
- bookwarehouse

Hozzon létre névtereket az egyes alkalmazás-összetevőkhöz.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

A következő kimenetnek kell megjelennie:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Az OSM által felügyelt névterek előkészítése

Amikor hozzáadja a névtereket az OSM-hálóhoz, ez lehetővé teszi, hogy az OSM-vezérlő automatikusan beírja a megbízott oldalkocsi proxy tárolóit az alkalmazásba. Futtassa az alábbi parancsot az OSM könyvesbolt alkalmazás névterének előkészítéséhez.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

A következő kimenetnek kell megjelennie:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>A könyvesbolt alkalmazás üzembe helyezése az AK-fürtön

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Az összes üzembe helyezési kimenet összegzése alább látható.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Ellenőrzőpont: mi lett telepítve?

A példában szereplő könyvesbolt-alkalmazás olyan többrétegű alkalmazás, amely négy szolgáltatásból áll: a bookbuyer, a bookthief, a könyvesbolt és a bookwarehouse. Mind a bookbuyer, mind a bookthief szolgáltatás kommunikál a könyvesbolt szolgáltatással, hogy beolvassa a könyveket a Könyvesboltból. A könyvesbolt szolgáltatás lekéri a könyveket a bookwarehouse szolgáltatásból, hogy megadja a bookbuyer és a bookthief. Ez egy egyszerű többrétegű alkalmazás, amely bemutatja, hogyan használható a szolgáltatás hálója az alkalmazások szolgáltatásai közötti kommunikáció biztosítására és engedélyezésére. Ahogy folytatjuk a bemutatót, a Service Mesh Interface (SMI) szabályzatok engedélyezésével és letiltásával engedélyezheti és letilthatja a szolgáltatásoknak az OSM-en keresztüli kommunikációt. Alább látható egy architektúra-diagram, amely a könyvesbolt alkalmazáshoz van telepítve.

![OSM bookbuyer-alkalmazás architektúrája](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Ellenőrizze, hogy fut-e az AK-fürtön belül futó könyvesbolt-alkalmazás

A most már üzembe helyezte a könyvesbolt Mulit-Container alkalmazást, de csak az AK-fürtön belülről érhető el. A későbbi oktatóanyagok segítséget nyújtanak az alkalmazásnak a fürtön kívüli kinyeréséhez egy bejövő vezérlőn keresztül. Most a port továbbítását használjuk a bookbuyer alkalmazás eléréséhez az AK-fürtön belül annak ellenőrzéséhez, hogy a könyvesbolt szolgáltatásban vásárol-e könyveket.

Annak ellenőrzéséhez, hogy az alkalmazás a fürtön belül fut-e, a bookbuyer-és bookthief-összetevők felhasználói felületének megtekintéséhez a portot fogjuk használni.

Első lépésként szerezze be a bookbuyer Pod nevét

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookbuyer Pod egyedi névvel lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

A pod neve után mostantól a Port-Forward paranccsal állíthatja be a helyi rendszerről az alkalmazásba az AK-fürtön belüli alagutat. A következő parancs futtatásával állítsa be a portot a 8080-es helyi porton. Ismét használja a megadott bookbuyer Pod-nevet.

> [!NOTE]
> Minden port továbbítási parancs esetében érdemes egy további terminált használni, hogy továbbra is használhassa ezt a bemutatót, és ne válassza le az alagutat. Azt is javasoljuk, hogy a Azure Cloud Shellon kívül hozza létre a port továbbítására szolgáló alagutat.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Ehhez hasonló kimenetnek kell megjelennie.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

A port továbbítási munkamenetének végrehajtása közben navigáljon a böngészőben a következő URL-címre `http://localhost:8080` . Ekkor az alábbi képhez hasonló módon láthatja a bookbuyer alkalmazás felhasználói felületét.

![Az OSM bookbuyer alkalmazás felhasználói felületének képe](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Azt is megfigyelheti, hogy a vásárolt összes könyv száma továbbra is növekszik a könyvesbolt v1 szolgáltatásával. A könyvesbolt V2 szolgáltatás még nincs telepítve. A könyvesbolt v2 szolgáltatását fogjuk üzembe helyezni, amikor bemutatjuk az SMI-forgalom megosztására vonatkozó szabályzatokat.

Ugyanezt a bookthief szolgáltatás esetében is megtekintheti.

```azurecli-interactive
kubectl get pod -n bookthief
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookthief Pod egyedi névvel lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

A port a bookthief Pod felé.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Navigáljon a böngészőben a következő URL-címre `http://localhost:8080` . Látnia kell, hogy a bookthief jelenleg a könyvesbolt szolgáltatásból ellopja a könyveket. Később bevezetünk egy forgalmi szabályzatot a bookthief leállításához.

![Az OSM bookthief alkalmazás felhasználói felületének képe](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Az OSM által megengedhető forgalmi mód letiltása a hálónál

Az OSM-fürt konfigurációjának megtekintésekor korábban említettek szerint az OSM-konfiguráció alapértelmezett értéke a megengedő forgalmi mód házirend engedélyezése. Ebben a módban a forgalmi szabályzatok betartatása megkerülésre kerül, és az OSM automatikusan felveszi a Service Mesh és a programok adatforgalmi szabályzatára vonatkozó szabályokat az egyes megbízotti proxyn, hogy képes legyen kommunikálni ezekkel a szolgáltatásokkal.

Most letiltjuk a megengedő forgalmi mód szabályzatát, az OSM pedig explicit [SMI](https://smi-spec.io/) -szabályzatokat igényel a fürtön, hogy engedélyezze a kommunikációt a hálóban az egyes szolgáltatásokból. A megengedő forgalmi mód letiltásához futtassa a következő parancsot a configmap tulajdonság frissítéséhez, amely a értékről a verzióra változik `true` `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookthief Pod egyedi névvel lesz hozzáfűzve.

```Output
configmap/osm-config patched
```

A megengedhető forgalmi mód ellenőrzéséhez le lett tiltva a port a bookbuyer vagy a bookthief Pod-ba, hogy megtekintse a felhasználói felületét a böngészőben, és ellenőrizze, hogy a megvásárolt vagy ellopott könyvek már nem növekszik-e. Győződjön meg róla, hogy frissíti a böngészőt. Ha leállt a növekmény, a szabályzat helyesen lett alkalmazva. Sikeresen leállította a bookthief a könyvek lopása alól, de a bookbuyer nem vásárolhatnak a Könyvesboltból, és a könyvesbolt nem tud könyveket beolvasni a bookwarehouse. A következő lépésben az [SMI](https://smi-spec.io/) -szabályzatokat fogjuk megvalósítani, hogy csak a Hálón kommunikálni kívánt szolgáltatások legyenek elérhetők.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>A Service Mesh-interfész (SMI) forgalom-hozzáférési házirendjeinek alkalmazása

Most, hogy letiltottuk az összes kommunikációt a hálóban, engedélyezzük, hogy a bookbuyer szolgáltatásunk kommunikáljon a könyvesbolt szolgáltatással a könyvek vásárlásához, és lehetővé teszi, hogy a könyvesbolt szolgáltatásunk kommunikáljon a bookwarehouse szolgáltatással a könyvek értékesítésének lekérése érdekében.

Telepítse a következő [SMI](https://smi-spec.io/) -házirendeket.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Az alábbihoz hasonló kimenetnek kell megjelennie.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Mostantól beállíthatja a port továbbítási munkamenetét a bookbuyer vagy a könyvesboltba, és láthatja, hogy a vásárolt és a könyvekben eladott könyvek is megnövekszik. Azt is megteheti, hogy a bookthief Pod megegyeznek annak ellenőrzéséhez, hogy továbbra sem tudja ellopni a könyveket.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>A Service Mesh Interface (SMI) forgalom felosztása házirend alkalmazása

Végső bemutatónk esetében egy [SMI](https://smi-spec.io/) -adatforgalom-megosztási szabályzatot hozunk létre, amely az egyik szolgáltatás és a háttérrendszer közötti kommunikáció súlyozását konfigurálja. A forgalom felosztása funkció lehetővé teszi, hogy fokozatosan helyezze át az egyik szolgáltatás kapcsolatait a másikra a forgalom súlyozásával a 0 és 100 közötti skálán.

Az alábbi ábrán az [SMI](https://smi-spec.io/) -forgalom feldarabolt házirendjének ábrája látható. Üzembe helyezünk egy további, 2. verziójú könyvesboltot, majd felosztjuk a bookbuyer érkező bejövő forgalmat, a könyvesbolt v1 szolgáltatás felé irányuló forgalom 25%-át, a 75%-ot pedig a könyvesbolt v2 szolgáltatásához.

![Az OSM bookbuyer Traffic felosztott diagramja](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Telepítse a könyvesbolt v2 szolgáltatást.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

A következő kimenetnek kell megjelennie.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Most telepítse a forgalom felosztása házirendet a bookbuyer-forgalom felosztásához a két könyvesbolt v1 és V2 szolgáltatás között.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

A következő kimenetnek kell megjelennie.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Hozzon létre egy Port Forward tunnelt a bookbuyer Pod-be, és most már látnia kell a könyvesbolt v2 szolgáltatásból vásárolt könyveket. Ha továbbra is megtekinti a vásárlások növekményét, figyelje meg, hogy a könyvesbolt v2 szolgáltatásán keresztül gyorsabban megnövekszik a vásárlások száma.

![Az OSM bookbuyer Books boough felhasználói felülete](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Meglévő telepített alkalmazások kezelése az Open Service Mesh (OSM) Azure Kubernetes szolgáltatás (ak) bővítményének kezeléséhez

### <a name="before-you-begin"></a>Előkészületek

Az ebben a bemutatóban ismertetett lépések feltételezik, hogy korábban engedélyezte a (z) az AK-fürthöz tartozó OSM AK-bővítményt. Ha nem, tekintse át az [Open Service Mesh (OSM) Azure Kubernetes szolgáltatás (ak) bővítményének engedélyezése meglévő AK-fürthöz](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) szakaszt a folytatás előtt. Emellett az AK-fürtnek Kubernetes vagy újabb verziójúnak kell lennie `1.19+` , és a KUBERNETES RBAC engedélyezve van, és létre kell hoznia egy `kubectl` kapcsolódást a fürttel (ha segítségre van szüksége ezen elemek bármelyikéhez, tekintse meg az [AK](./kubernetes-walkthrough.md)-alapú rövid útmutatót, és telepítse az AK OSM-bővítményt.

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.20.0 vagy újabb verzió
- A `aks-preview` bővítmény 0.5.5 vagy újabb verziója
- Az OSM v 0.8.0 vagy újabb verziója
- apt – a jQ telepítésének beolvasása

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Győződjön meg arról, hogy a nyílt szolgáltatási háló (OSM) megengedő forgalmi mód szabályzata

Az OSM megengedő adatforgalmi házirend mód olyan mód, amelyben az [SMI](https://smi-spec.io/) forgalmi szabályzatának kényszerítése megkerül. Ebben a módban az OSM automatikusan felveszi a Service Mesh szolgáltatás részét képező szolgáltatásokat, és a program az egyes megbízotti proxyk által biztosított adatforgalmi házirend-szabályok alapján képes kommunikálni ezekkel a szolgáltatásokkal.

A következő parancs futtatásával ellenőrizheti, hogy van-e az OSM-nek a fürthöz tartozó aktuálisan kikényszerített adatforgalmi módja:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Az OSM-configmap kimenetének a következőhöz hasonlóan kell kinéznie:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Ha a **permissive_traffic_policy_mode** az **igaz** értékre van konfigurálva, a szolgáltatások közötti kommunikáció megszakítása nélkül is biztonságosan bevezetheti a névtereket. Ha a **permissive_traffic_policy_mode** **hamis értékre** van konfigurálva, győződjön meg arról, hogy rendelkezik a megfelelő [SMI](https://smi-spec.io/) forgalom-hozzáférési szabályzattal, és gondoskodik arról, hogy rendelkezzen egy olyan szolgáltatásfiók, amely a névtérben üzembe helyezett összes szolgáltatást jelképezi. Kövesse a meglévő üzembe helyezett alkalmazások bevezetésének útmutatását az [Open Service Mesh (OSM) által hamisként konfigurált adatforgalmi szabályzattal](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Meglévő központilag telepített alkalmazások beléptetése az Open Service Mesh (OSM) szolgáltatással, amely az igaz értékre van konfigurálva

Első lépésként adja hozzá a központilag telepített alkalmazási névteret (ka) t az OSM-hez a kezeléshez.

```azurecli-interactive
osm namespace add bookstore
```

A következő kimenetnek kell megjelennie:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

A következő lépésben megtekintjük az aktuális Pod-telepítést a névtérben. A következő parancs futtatásával tekintheti meg a hüvelyeket a kijelölt névtérben.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

A következő hasonló kimenetnek kell megjelennie:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Figyelje meg az **1/1**-as **Ready (kész** ) oszlopot, ami azt jelenti, hogy az Application Pod csak egy tárolóval rendelkezik. Ezután újra kell indítani az alkalmazás központi telepítését, hogy az OSM beinjektálja a megbízott oldalkocsi proxy tárolót az Application Pod használatával. A névtérben található üzemelő példányok listájának beolvasása.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

A következő kimenetnek kell megjelennie:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Most újraindítjuk a központi telepítést, hogy beinjektáljuk a megbízott oldalkocsis proxy tárolót az Application Pod használatával. Futtassa az alábbi parancsot.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

A következő kimenetnek kell megjelennie:

```Output
deployment.apps/bookbuyer restarted
```

Ha megvizsgáljuk a hüvelyt a névtérben:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Ekkor megfigyelheti, hogy a **Ready (kész** ) oszlop mostantól **2/2** tárolót jelenít meg a pod számára. A második tároló a megbízottat oldalkocsi proxy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

A következő parancs futtatásával tekintheti meg a The Pod-t, hogy megtekintse a megbízott proxyját.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Ellenőrizze, hogy az alkalmazás továbbra is működőképes-e a megbízott oldalkocsi proxy injekciója után.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Meglévő üzembe helyezett alkalmazások előkészítése a nyílt Service Mesh (OSM) szolgáltatással

Ha a hozzáférés-szabályozási szabályzathoz tartozó OSM-konfiguráció értéke a, az OSM-nek `false` explicit [SMI](https://smi-spec.io/) forgalmi hozzáférési szabályzatokra van szüksége, amelyek a szolgáltatás és a szolgáltatás közötti kommunikációhoz lesznek telepítve a fürtön belül. Az OSM jelenleg a szolgáltatások közötti kommunikáció engedélyezésének részeként is használ Kubernetes-szolgáltatásfiókok használatát. Annak biztosítása érdekében, hogy a meglévő telepített alkalmazások kommunikálni tudjanak az OSM Mesh általi felügyelettel, ellenőrizni kell, hogy a szolgáltatás fiókjának létezését kell-e használni, frissítenie kell az alkalmazás központi telepítését a szolgáltatásfiók adataival, alkalmazni kell az [SMI](https://smi-spec.io/) forgalmi hozzáférési házirendeket.

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok ellenőrzése

Ellenőrizze, hogy van-e olyan kubernetes-szolgáltatásfiók a névtérben, amelyben az alkalmazás telepítve van.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

A következőben egy nevű szolgáltatásfiók található `bookbuyer` a bookbuyer névtérben.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Ha nem rendelkezik az alapértelmezett fiókon kívüli szolgáltatási fiókkal, akkor létre kell hoznia egyet az alkalmazásához. Az alábbi parancs példaként használható egy szolgáltatásfiók létrehozására az alkalmazás üzembe helyezett névterében.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Az alkalmazás aktuális üzembe helyezési specifikációjának megtekintése

Ha korábban létrehozott egy szolgáltatásfiókot a korábbi szakaszból, előfordulhat, hogy az alkalmazás üzembe helyezése nincs megadva `serviceAccountName` az üzembe helyezési specifikációban megadott értékkel. Az alkalmazás üzembe helyezési specifikációját az alábbi parancsokkal tekintheti meg:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

A központi telepítések listája a kimenetben jelenik meg.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Most az üzemelő példányt bemutatjuk, hogy ellenőrizze, van-e a pod sablon szakaszban felsorolt szolgáltatásfiók.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

Ebben a központi telepítésben láthatja, hogy a telepítéshez társított szolgáltatásfiók a pod template szakaszban található. Ez a központi telepítés a szolgáltatásfiók bookbuyer használja. Ha nem látja a szolgáltatás teljesítményszámláló- **fiók:** tulajdonságot, a központi telepítés nem a szolgáltatásfiók használatára van konfigurálva.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

A kubernetes-szolgáltatásfiók hozzáadásához számos módszer áll rendelkezésre a központi telepítés frissítéséhez. Tekintse át a Kubernetes dokumentációját a [központi telepítés frissítéséhez](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) , vagy [konfigurálja a hüvelyek szolgáltatási fiókját](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Miután frissítette a telepítési specifikációt a szolgáltatásfiók használatával, telepítse újra az üzembe helyezést (kubectl Apply-f a-Deployment. YAML) a fürtön.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>A szükséges Service Mesh Interface (SMI) szabályzatok üzembe helyezése

Az utolsó lépés, amely lehetővé teszi az engedélyezett forgalom áramlását a hálóban, az alkalmazáshoz szükséges [SMI](https://smi-spec.io/) forgalmi hozzáférési szabályzatok üzembe helyezése. Az [SMI](https://smi-spec.io/) forgalmi hozzáférési szabályzatok által elérhető konfiguráció mennyisége meghaladja a bemutató hatókörét, de részletesen ismertetjük a specifikáció általános összetevőit, és bemutatjuk, hogyan konfigurálható egy egyszerű TrafficTarget és HTTPRouteGroup-szabályzat a szolgáltatások közötti kommunikáció engedélyezéséhez az alkalmazáshoz.

Az [SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) specifikációja lehetővé teszi, hogy a felhasználók definiálják az alkalmazásokhoz való hozzáférés-vezérlési szabályzatot. A **TrafficTarget** és a **HTTPRoutGroup** API-erőforrásokra fogunk összpontosítani.

A TrafficTarget-erőforrás három fő konfigurációs beállítást tartalmaz a cél, a szabályok és a források közül. Alább látható egy példa a TrafficTarget.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

A fenti TrafficTarget-specifikációban a a `destination` cél forrás szolgáltatáshoz konfigurált szolgáltatásfiókot jelöli. Ne feledje, hogy a korábban a központi telepítéshez hozzáadott szolgáltatásfiók a következőhöz csatolva való hozzáférés engedélyezését fogja használni. `rules`Ebben a konkrét példában a szakasz a kapcsolaton keresztül engedélyezett http-forgalom típusát határozza meg. Beállíthatja, hogy a HTTP-fejlécek a HTTP-n keresztül engedélyezettek legyenek. A `sources` szakasz a szolgáltatásból származó kommunikáció. Ez a specifikáció beolvassa a bookbuyer a könyvesbolttal való kommunikációt.

A HTTPRouteGroup-erőforrás a HTTP-fejléc információinak egy vagy több tömbjét tartalmazza, és a TrafficTarget spec követelménye. Az alábbi példában láthatja, hogy a HTTPRouteGroup három HTTP-műveletet, két GET és egy BEJEGYZÉST engedélyez.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Ha nem ismeri az előtér-alkalmazás által az alkalmazás más szintjeire irányuló HTTP-adatforgalom típusát, mivel a TrafficTarget-specifikációhoz szabály szükséges, akkor a HTTPRouteGroup-re vonatkozó alábbi spec használatával hozhatja létre a minden szabálynak megfelelő értéket.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Miután konfigurálta a TrafficTarget és a HTTPRouteGroup SPECT, egyetlen YAML is elhelyezheti őket, és üzembe helyezheti őket. Alább látható a könyvesbolt példájának konfigurációja.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

A specifikációval kapcsolatos részletesebb információkért látogasson el az [SMI](https://smi-spec.io/) webhelyére.

### <a name="manage-the-applications-namespace-with-osm"></a>Az alkalmazás névterének kezelése az OSM-sel

Ezután konfigurálja az OSM-t a névtér kezeléséhez, majd indítsa újra az üzemelő példányokat, hogy az alkalmazással beinjektálja a megbízott oldalkocsi proxyt.

Futtassa a következő parancsot a `azure-vote` névtér felügyelt saját OSM-hez való konfigurálásához.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Ezután indítsa újra a `azure-vote-front` és a `azure-vote-back` központi telepítést a következő parancsokkal.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Ha megtekintjük a névtérhez tartozó hüvelyeket, akkor a `azure-vote` és a 2/2-as **kész** szakasz jelenik meg `azure-vote-front` `azure-vote-back` , ami azt jelenti, hogy a megbízott oldalkocsi proxyja az alkalmazás mellett lett befecskendezve.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Oktatóanyag: nyílt Service Mesh (OSM) által felügyelt alkalmazás üzembe helyezése NGINX bejövő forgalommal

A nyílt Service Mesh (OSM) egy egyszerű, bővíthető, Felhőbeli natív szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy egységes módon kezeljék, biztonságossá teszik és felhasználják a hatékony, dinamikus üzemi környezetekhez szükséges bevezetési funkciókat.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> - Az OSM-fürt aktuális konfigurációjának megtekintése
> - Névtér (ek) létrehozása az OSM számára a telepített alkalmazások kezeléséhez a névtér (ek) ban
> - Az OSM által felügyelt névterek előkészítése
> - A mintaalkalmazás üzembe helyezése
> - Ellenőrizze, hogy fut-e az alkalmazás az AK-fürtön belül
> - A alkalmazások használt NGINX bejövő adatkezelő létrehozása
> - Szolgáltatás közzététele az Azure Application Gateway az interneten keresztül

### <a name="before-you-begin"></a>Előkészületek

A cikkben részletesen ismertetett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.19+` és újabb, KUBERNETES RBAC engedélyezve), létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat (ha segítségre van szüksége ezen elemek bármelyikéhez, tekintse meg az [AK](./kubernetes-walkthrough.md)-gyors útmutatót, és telepítette az AK OSM-bővítményt.

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.20.0 vagy újabb verzió
- A `aks-preview` bővítmény 0.5.5 vagy újabb verziója
- Az OSM v 0.8.0 vagy újabb verziója
- apt – a jQ telepítésének beolvasása

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Az OSM-fürt aktuális konfigurációjának megtekintése és ellenőrzése

Miután engedélyezte az az AK-ra vonatkozó OSM-bővítményt az AK-fürtön, megtekintheti az aktuális konfigurációs paramétereket az OSM-config Kubernetes ConfigMap. Futtassa a következő parancsot a ConfigMap tulajdonságainak megtekintéséhez:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

A kimenet a fürt aktuális OSM-konfigurációját jeleníti meg.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Figyelje meg, hogy a **permissive_traffic_policy_mode** az **igaz** értékre van konfigurálva. Az OSM-ben elérhető adatforgalmi házirend mód olyan mód, amelyben az [SMI](https://smi-spec.io/) forgalmi szabályzatának kényszerítése megkerül. Ebben a módban az OSM automatikusan felveszi a Service Mesh szolgáltatás részét képező szolgáltatásokat, és a program az egyes megbízotti proxyk által biztosított adatforgalmi házirend-szabályok alapján képes kommunikálni ezekkel a szolgáltatásokkal.

### <a name="create-namespaces-for-the-application"></a>Névterek létrehozása az alkalmazáshoz

Ebben az oktatóanyagban az OSM könyvesbolt alkalmazást fogjuk használni, amely a következő alkalmazás-összetevőkkel rendelkezik:

- bookbuyer
- bookthief
- könyvesbolt
- bookwarehouse

Hozzon létre névtereket az egyes alkalmazás-összetevőkhöz.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

A következő kimenetnek kell megjelennie:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Az OSM által felügyelt névterek előkészítése

Ha a névtereket hozzáadja az OSM Mesh hálóhoz, az OSM-vezérlő automatikusan behelyezi a megbízott oldalkocsi proxy tárolóit az alkalmazásba. Futtassa az alábbi parancsot az OSM könyvesbolt alkalmazás névterének előkészítéséhez.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

A következő kimenetnek kell megjelennie:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>A könyvesbolt alkalmazás üzembe helyezése az AK-fürtön

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Az összes üzembe helyezési kimenet összegzése alább látható.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>A Bookbuyer szolgáltatás frissítése

Frissítse a bookbuyer szolgáltatást a megfelelő bejövő port konfigurációra az alábbi szolgáltatási jegyzékfájl használatával.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Ellenőrizze, hogy fut-e az AK-fürtön belül futó könyvesbolt-alkalmazás

A most már üzembe helyezte a könyvesbolt Mulit-Container alkalmazást, de csak az AK-fürtön belülről érhető el. Később hozzáadjuk az Azure Application Gateway beáramló vezérlőt, hogy elérhetővé tegye az alkalmazást az AK-fürtön kívül. Annak ellenőrzéséhez, hogy az alkalmazás a fürtön belül fut-e, a bookbuyer-összetevő felhasználói felületének megtekintéséhez a portot fogjuk használni.

Első lépésként szerezze be a bookbuyer Pod nevét

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookbuyer Pod egyedi névvel lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

A pod neve után mostantól a Port-Forward paranccsal állíthatja be a helyi rendszerről az alkalmazásba az AK-fürtön belüli alagutat. A következő parancs futtatásával állítsa be a portot a 8080-es helyi porton. Ismét használja a megadott bookbuyer Pod-nevet.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Ehhez hasonló kimenetnek kell megjelennie.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

A port továbbítási munkamenetének végrehajtása közben navigáljon a böngészőben a következő URL-címre `http://localhost:8080` . Ekkor az alábbi képhez hasonló módon láthatja a bookbuyer alkalmazás felhasználói felületét.

![OSM bookbuyer alkalmazás az NGINX felhasználói felületének rendszerképéhez](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>NGINX bejövő adatkezelő létrehozása az Azure Kubernetes Service-ben (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Kihasználjuk a bejövő vezérlőt, hogy elérhetővé tegye az OSM által az interneten felügyelt alkalmazást. A bejövő vezérlő létrehozásához használja a Helmt az Nginx-behatolások telepítéséhez. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások _– alapszintű_ forgalomhoz. Szükség szerint adja meg a saját környezetének névterét.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a rendszer egy dinamikus nyilvános IP-címet rendel hozzá, ahogy az a következő példában látható:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Még nem jöttek létre Bejövő szabályok, így a belső IP-cím megkereséséhez az NGINX bejövő vezérlő alapértelmezett 404 lapja jelenik meg. A bejövő szabályok a következő lépésekben vannak konfigurálva.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>A bookbuyer szolgáltatás közzététele az interneten

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

A következő kimenetnek kell megjelennie:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>NGINX-naplók megtekintése

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

A kimenet az NGINX bejövő vezérlő állapotát mutatja, ha a bejövő szabályok alkalmazása sikeresen megtörtént:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Az NGINX-szolgáltatások és a bookbuyer-szolgáltatás külső megtekintése

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Mivel a bejövő jegyzékfájlban található állomásnév a teszteléshez használt psuedo-név, a DNS-név nem lesz elérhető az interneten. Felhasználhatjuk a curl programot is, és a hostname (állomásnév) fejlécet az NGINX nyilvános IP-címére is felhasználhatjuk, és egy 200-es kódot kaptunk a bookbuyer szolgáltatáshoz való csatlakozáshoz.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

A következő kimenetnek kell megjelennie:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Oktatóanyag: nyílt Service Mesh (OSM) által felügyelt alkalmazás üzembe helyezése az Azure Application Gateway beléptetési AK bővítmény használatával

A nyílt Service Mesh (OSM) egy egyszerű, bővíthető, Felhőbeli natív szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy egységes módon kezeljék, biztonságossá teszik és felhasználják a hatékony, dinamikus üzemi környezetekhez szükséges bevezetési funkciókat.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> - Az OSM-fürt aktuális konfigurációjának megtekintése
> - Névtér (ek) létrehozása az OSM számára a telepített alkalmazások kezeléséhez a névtér (ek) ban
> - Az OSM által felügyelt névterek előkészítése
> - A mintaalkalmazás üzembe helyezése
> - Ellenőrizze, hogy fut-e az alkalmazás az AK-fürtön belül
> - Azure-Application Gateway létrehozása a alkalmazások-beli bejövő adatkezelőként való használatra
> - Szolgáltatás közzététele az Azure Application Gateway az interneten keresztül

### <a name="before-you-begin"></a>Előkészületek

A cikkben részletesen ismertetett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.19+` és újabb, KUBERNETES RBAC engedélyezve), létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat (ha segítségre van szüksége ezen elemek bármelyikéhez, majd láthatja Application Gateway, hogy az [AK](./kubernetes-walkthrough.md)

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.20.0 vagy újabb verzió
- A `aks-preview` bővítmény 0.5.5 vagy újabb verziója
- AK-fürt, 1.19 + az Azure CNI hálózatkezelés (Azure vnet csatolva)
- Az OSM v 0.8.0 vagy újabb verziója
- apt – a jQ telepítésének beolvasása

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Az OSM-fürt aktuális konfigurációjának megtekintése és ellenőrzése

Miután engedélyezte az az AK-ra vonatkozó OSM-bővítményt az AK-fürtön, megtekintheti az aktuális konfigurációs paramétereket az OSM-config Kubernetes ConfigMap. Futtassa a következő parancsot a ConfigMap tulajdonságainak megtekintéséhez:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

A kimenet a fürt aktuális OSM-konfigurációját jeleníti meg.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Figyelje meg, hogy a **permissive_traffic_policy_mode** az **igaz** értékre van konfigurálva. Az OSM-ben elérhető adatforgalmi házirend mód olyan mód, amelyben az [SMI](https://smi-spec.io/) forgalmi szabályzatának kényszerítése megkerül. Ebben a módban az OSM automatikusan felveszi a Service Mesh szolgáltatás részét képező szolgáltatásokat, és a program az egyes megbízotti proxyk által biztosított adatforgalmi házirend-szabályok alapján képes kommunikálni ezekkel a szolgáltatásokkal.

### <a name="create-namespaces-for-the-application"></a>Névterek létrehozása az alkalmazáshoz

Ebben az oktatóanyagban az OSM könyvesbolt alkalmazást fogjuk használni, amely a következő alkalmazás-összetevőkkel rendelkezik:

- bookbuyer
- bookthief
- könyvesbolt
- bookwarehouse

Hozzon létre névtereket az egyes alkalmazás-összetevőkhöz.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

A következő kimenetnek kell megjelennie:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Az OSM által felügyelt névterek előkészítése

Amikor hozzáadja a névtereket az OSM-hálóhoz, ez lehetővé teszi, hogy az OSM-vezérlő automatikusan beírja a megbízott oldalkocsi proxy tárolóit az alkalmazásba. Futtassa az alábbi parancsot az OSM könyvesbolt alkalmazás névterének előkészítéséhez.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

A következő kimenetnek kell megjelennie:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>A könyvesbolt alkalmazás üzembe helyezése az AK-fürtön

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Az összes üzembe helyezési kimenet összegzése alább látható.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>A Bookbuyer szolgáltatás frissítése

Frissítse a bookbuyer szolgáltatást a megfelelő bejövő port konfigurációra az alábbi szolgáltatási jegyzékfájl használatával.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Ellenőrizze, hogy fut-e az AK-fürtön belül futó könyvesbolt-alkalmazás

Mostantól már üzembe helyezte a többtárolós könyvesbolt alkalmazást, de csak az AK-fürtön belülről érhető el. Később hozzáadjuk az Azure Application Gateway beáramló vezérlőt, hogy elérhetővé tegye az alkalmazást az AK-fürtön kívül. Annak ellenőrzéséhez, hogy az alkalmazás a fürtön belül fut-e, a bookbuyer-összetevő felhasználói felületének megtekintéséhez a portot fogjuk használni.

Első lépésként szerezze be a bookbuyer Pod nevét

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookbuyer Pod egyedi névvel lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

A pod neve után mostantól a Port-Forward paranccsal állíthatja be a helyi rendszerről az alkalmazásba az AK-fürtön belüli alagutat. A következő parancs futtatásával állítsa be a portot a 8080-es helyi porton. Ismét használja az adott bookbuyer Pod-nevet.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Ehhez hasonló kimenetnek kell megjelennie.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

A port továbbítási munkamenetének végrehajtása közben navigáljon a böngészőben a következő URL-címre `http://localhost:8080` . Ekkor az alábbi képhez hasonló módon láthatja a bookbuyer alkalmazás felhasználói felületét.

![OSM bookbuyer app for app Gateway felhasználói felületének képe](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Azure-Application Gateway létrehozása a bookbuyer-alkalmazás az AK-fürtön kívüli elérhetővé tétele érdekében

> [!NOTE]
> A következő utasítások a bejövő forgalomhoz használandó Azure-Application Gateway új példányát fogják létrehozni. Ha meglévő Azure-Application Gateway szeretne használni, ugorjon arra a szakaszra, amely engedélyezi a Application Gateway beléptetési vezérlő bővítményét.

#### <a name="deploy-a-new-application-gateway"></a>Új Application Gateway üzembe helyezése

> [!NOTE]
> Létező dokumentációra hivatkozunk, amely lehetővé teszi, hogy egy meglévő AK-fürtön a Application Gateway bejövő vezérlő bővítményét. Néhány módosítás történt az OSM-anyagoknak megfelelően. A témával kapcsolatos részletesebb dokumentáció [itt](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)található.

Most egy új Application Gateway üzembe helyezésével szimulálhatja azt a meglévő Application Gateway, amelyet a forgalom terheléselosztásához kíván használni az AK-fürtre ( _myCluster_). A Application Gateway neve _myApplicationGateway_ lesz, de először létre kell hoznia egy _myPublicIp_ nevű nyilvános IP-erőforrást, és egy _myVnet_ nevű új virtuális hálózatot, amelynek a címe 11.0.0.0/8, valamint egy olyan alhálózat, amelynek 11.1.0.0/16 nevű alhálózata _mySubnet, és_ a mySubnet használatával helyezi üzembe _Application Gateway a myPublicIp_ .

Ha AK-alapú fürtöt használ, és Application Gateway külön virtuális hálózatban, akkor a két virtuális hálózat címterület nem fedik át egymást. Az AK-fürtök alapértelmezett 10.0.0.0/8, ezért a Application Gateway virtuális hálózati címek előtagját a 11.0.0.0/8 értékre állítja be.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> A Application Gateway inporting Controller (AGIC) bővítmény **csak** Application Gateway v2 SKU (standard és WAF), és **nem** a Application Gateway v1 SKU-t támogatja.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>AGIC-bővítmény engedélyezése meglévő AK-fürthöz az Azure CLI-n keresztül

Ha továbbra is az Azure CLI-t szeretné használni, továbbra is engedélyezheti a AGIC-bővítményt a létrehozott AK-fürtben, és megadhatja a AGIC-bővítményt a létrehozott meglévő Application Gateway _myCluster_, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

A következő parancs segítségével ellenőrizheti, hogy az Azure Application Gateway AK-bővítmény engedélyezve van-e.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

A parancsnak a kimenetet kell megjelenítenie `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Egyenrangú a két virtuális hálózat együtt

Mivel üzembe helyezte az AK-fürtöt a saját virtuális hálózatában, illetve a Application Gateway egy másik virtuális hálózatban, a két virtuális hálózatot össze kell kapcsolnia ahhoz, hogy a forgalom a Application Gateway a fürtben lévő hüvelybe kerüljön. A két virtuális hálózat társításához az Azure CLI-parancsot két külön alkalommal kell futtatnia, hogy a kapcsolat kétirányú legyen. Az első parancs egy egyenrangú kapcsolatot hoz létre a Application Gateway virtuális hálózatról az AK-beli virtuális hálózatra; a második parancs egy egyenrangú kapcsolatot hoz létre a másik irányban.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>A bookbuyer szolgáltatás közzététele az interneten

Alkalmazza az alábbi beáramlási jegyzéket az AK-fürtre, hogy a bookbuyer szolgáltatás elérhető legyen az interneten az Azure Application Gateway használatával.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

A következő kimenetnek kell megjelennie

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Mivel a bejövő jegyzékfájlban szereplő állomásnév a teszteléshez használt pszeudo-név, a DNS-név nem lesz elérhető az interneten. Felhasználhatjuk a curl programot is, és a hostname (állomásnév) fejlécet az Azure Application Gateway nyilvános IP-címére is használhatja, és egy 200-kód fogadásával sikeresen kapcsolódhat a bookbuyer szolgáltatáshoz.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

A következő kimenetnek kell megjelennie

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Hibaelhárítás

- [AGIC – hibaelhárítási dokumentáció](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [A AGIC GitHub-tárházában további hibaelhárítási eszközök érhetők el](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>A Service Mesh (OSM) figyelése és megfigyelése Azure Monitor és Applications-ismeretek használatával

A Azure Monitor és az Azure Application Insights is lehetővé teszi az alkalmazások és szolgáltatások rendelkezésre állásának és teljesítményének maximalizálását azáltal, hogy átfogó megoldást kínál a Felhőbeli és helyszíni környezetek telemetria gyűjtésére, elemzésére és működésére.

Az OSM AK-bővítménye mélyreható integrációval fog rendelkezni mindkét Azure-szolgáltatásba, és seemless Azure-élményt biztosít az OSM-metrikák által biztosított kritikus KPI-k megtekintéséhez és megválaszolásához. Ha többet szeretne megtudni arról, hogyan engedélyezheti és konfigurálhatja ezeket a szolgáltatásokat az OSM AK-bővítményéhez, további információért látogasson el a [Azure monitor for OSM](https://aka.ms/azmon/osmpreview) oldalára.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Oktatóanyag: a Prometheus, a Grafana és a Jaeger manuális üzembe helyezése a nyílt Service Mesh (OSM) mérőszámok megjelenítéséhez

> [!WARNING]
> A Prometheus, a Grafana és a Jaeger telepítése általános útmutatásként szolgál annak bemutatására, hogy ezek az eszközök hogyan használhatók az OSM-metrikai adatok megtekintésére. A telepítési útmutató nem használható éles környezetben történő telepítéshez. Kérjük, olvassa el az egyes eszközök dokumentációját, hogy a legmegfelelőbb módon megfeleljenek az igényeinek. A legjelentősebb a tartós tárolás hiánya, ami azt jelenti, hogy az összes adatvesztés a Prometheus Grafana és/vagy a Jaeger Pod (ok) leállása után megszűnik.

A nyílt Service Mesh (OSM) a Hálón belüli összes forgalomra vonatkozó részletes mérőszámokat generál. Ezek a mérőszámok betekintést nyújtanak a hálóba tartozó alkalmazások viselkedésére, így a felhasználók az alkalmazásaikat a hibák megoldásában, karbantartásában és elemzésében segítik.

Napjainkban az OSM a metrikákat közvetlenül az oldalkocsis proxyról (megbízottat) gyűjti. Az OSM részletes mérőszámokat biztosít a hálóban lévő összes szolgáltatás bejövő és kimenő forgalmához. Ezekkel a metrikákkal a felhasználó információkat kaphat a forgalom általános forgalmáról, a forgalomban bekövetkező hibákról és a kérések válaszideje.

Az OSM a Prometheus használatával gyűjti össze és tárolja a hálóban futó összes alkalmazásra vonatkozó konzisztens forgalmi metrikákat és statisztikát. A Prometheus egy nyílt forráskódú figyelési és riasztási eszközkészlet, amelyet általában a (de nem kizárólag) Kubernetes és a Service Mesh környezetekben használnak.

A háló részét képező minden alkalmazás egy olyan Pod-ban fut, amely egy olyan megbízottat tartalmaz, amely a mérőszámokat (a proxy metrikáit) a Prometheus formátumában teszi elérhetővé. Továbbá a háló részét képező összes Pod rendelkezik a Prometheus-megjegyzésekkel, ami lehetővé teszi, hogy a Prometheus-kiszolgáló dinamikusan kaparja az alkalmazást. Ez a mechanizmus automatikusan engedélyezi a metrikák leselejtezését, amikor új névtér/Pod/szolgáltatás kerül be a hálóba.

Az OSM-metrikák megtekinthetők a Grafana, amely egy nyílt forráskódú vizualizációs és elemzési szoftver. Lehetővé teszi a metrikák lekérdezését, megjelenítését, riasztását és megismerését.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> - Prometheus-példány létrehozása és üzembe helyezése
> - Az OSM konfigurálása a Prometheus-karcolás engedélyezéséhez
> - A Prometheus-Configmap frissítése
> - Grafana-példány létrehozása és üzembe helyezése
> - Grafana konfigurálása a Prometheus-adatforrással
> - Az OSM-irányítópult importálása a Grafana-hez
> - Jaeger-példány létrehozása és üzembe helyezése
> - Az OSM-beli Jaeger-nyomkövetés konfigurálása

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Prometheus-példány üzembe helyezése és konfigurálása az OSM-hez

Az Prometheus-példány üzembe helyezéséhez a Helmt fogjuk használni. Futtassa a következő parancsokat a Prometheus telepítéséhez a Helm használatával:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Ha a telepítés sikeres volt, az alábbi hasonló kimenetnek kell megjelennie. Jegyezze fel a Prometheus-kiszolgáló portszámát és a fürt DNS-nevét. Ezt az információt később a Grafana-hez adatforrásként a Prometheus konfigurálásához fogjuk használni.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Az OSM konfigurálása a Prometheus-karcolás engedélyezéséhez

Annak biztosítása érdekében, hogy az OSM-összetevők a Prometheus-selejtek számára legyenek konfigurálva, érdemes ellenőrizni az OSM-config konfigurációs fájlban található **prometheus_scraping** konfigurációt. Tekintse meg a konfigurációt a következő paranccsal:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Az előző parancs kimenetének vissza kell térnie, `true` Ha az OSM a Prometheus-selejthez van konfigurálva. Ha a visszaadott értéket adja `false` meg, frissítenie kell a konfigurációt a következőre: `true` . Futtassa az alábbi parancsot **az OSM Prometheus** -karcolás bekapcsolásához:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

A következő kimenetnek kell megjelennie.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>A Prometheus-Configmap frissítése

A Prometheus alapértelmezett telepítése két Kubernetes-configmaps fog tartalmazni. A Prometheus-configmaps listáját a következő paranccsal tekintheti meg.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

A következő OSM-konfigurációval kell helyettesítenie a Prometheus. YML konfigurációját a **STABLE-Prometheus-Server** configmap. A feladat végrehajtásához számos szerkesztési módszer szükséges. Egy egyszerű és biztonságos módszer a configmap exportálása, másolat készítése a biztonsági mentéshez, majd Szerkesztés egy szerkesztővel, például a Visual Studio Code használatával.

> [!NOTE]
> Ha nincs telepítve a Visual Studio Code, akkor letöltheti és telepítheti [itt](https://code.visualstudio.com/Download).

Először exportálja a **stabil-Prometheus-Server** configmap, majd készítsen másolatot a biztonsági mentésről.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Ezután nyissa meg a fájlt a Visual Studio Code használatával a szerkesztéshez.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Miután megnyitotta a configmap a Visual Studio Code Editorban, cserélje le a Prometheus. YML fájlt az alábbi OSM-konfigurációra, és mentse a fájlt.

> [!WARNING]
> Rendkívül fontos, hogy megőrizze a YAML fájl bemélyedési struktúráját. A YAML-fájl struktúrájának módosítása azt eredményezheti, hogy a configmap nem lehet újra alkalmazni.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Alkalmazza a frissített configmap YAML fájlt a következő paranccsal.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> A hiányzó kubernetes-megjegyzéssel kapcsolatos üzenet jelenhet meg. Ezt most figyelmen kívül hagyhatja.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>A Prometheus ellenőrzése úgy van konfigurálva, hogy az OSM Mesh és az API-végpontok kaparása

Annak ellenőrzéséhez, hogy a Prometheus megfelelően van-e beállítva az OSM Mesh és az API-végpontok beselejtezéséhez, a rendszer továbbítja a Prometheus Pod-ra, és megtekinti a célként megadott konfigurációt. Futtassa az alábbi parancsokat.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Böngésző megnyitása `http://localhost:9090/targets`

Ha lefelé görgeti az összes SMI metrikus végpontot **, és** az alább látható ábrán látható egyéb OSM-metrikákat is látnia kell.

![OSM Prometheus – cél mérőszámok felhasználói felületének képe](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Grafana-példány üzembe helyezése és konfigurálása az OSM-hez

A Grafana-példány üzembe helyezéséhez a Helmot fogjuk használni. Futtassa a következő parancsokat a Grafana a Helm használatával történő telepítéséhez:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

A következő lépésben beolvasjuk az alapértelmezett Grafana-jelszót, hogy bejelentkezzen a Grafana webhelyre.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Jegyezze fel a Grafana jelszavát.

A következő lépésben beolvasjuk a Grafana Pod-t a portra a Grafana irányítópultra való bejelentkezéshez.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Böngésző megnyitása `http://localhost:3000`

Az alább látható bejelentkezési képernyőn írja be a **rendszergazda** nevet, és használja a korábban rögzített Grafana-jelszót.

![OSM Grafana bejelentkezési oldal felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>A Grafana Prometheus-adatforrás konfigurálása

Miután sikeresen bejelentkezett a Grafana-be, a következő lépés a Prometheus adatforrásként való hozzáadása a Grafana. Ehhez navigáljon a bal oldali menü konfiguráció ikonjára, és válassza az alább látható adatforrások lehetőséget.

![OSM Grafana adatforrások oldal felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Kattintson az **adatforrás hozzáadása** gombra, és válassza a Prometheus elemet az idősorozat-adatbázisok területen.

![OSM Grafana-adatforrások kiválasztási oldal FELHASZNÁLÓIFELÜLET-képe](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

A **Prometheus-adatforrás konfigurálása az alábbi** lapon adja meg a Kubernetes-fürt teljes tartománynevét a Prometheus szolgáltatáshoz a http URL-cím beállításnál. Az alapértelmezett FQDN értéknek kell lennie `stable-prometheus-server.default.svc.cluster.local` . Miután megadta a Prometheus szolgáltatás végpontját, görgessen a lap aljára, és válassza a **& teszt mentése** elemet. Meg kell kapnia egy zöld jelölőnégyzetet, amely jelzi, hogy az adatforrás működik.

#### <a name="importing-osm-dashboards"></a>Az OSM-irányítópultok importálása

Az OSM-irányítópultok a következőkön keresztül érhetők el:

- A [tárház](/charts/osm/grafana), amely JSON-blobként, a webfelügyeleti portálon keresztül importálható
- vagy [online a Grafana.com](https://grafana.com/grafana/dashboards/14145)

Az irányítópult importálásához keresse meg a `+` bal oldali menüben a bejelentkezést, és válassza a lehetőséget `import` .
Az irányítópultot közvetlenül is importálhatja az azonosítójával `Grafana.com` . Például az irányítópult az `OSM Mesh Details` azonosítót használja `14145` , közvetlenül az űrlapon használhatja az azonosítót, és kiválaszthatja a `import` következőket:

![Az OSM Grafana irányítópultjának importálása oldal felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Amint kiválasztja az importálás lehetőséget, automatikusan bekerül az importált irányítópultra.

![Az OSM Grafana irányítópult rácsvonalának részletei lap felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Jaeger-operátor üzembe helyezése és konfigurálása a Kubernetes for OSM esetében

A [Jaeger](https://www.jaegertracing.io/) egy nyílt forráskódú nyomkövetési rendszer, amely az elosztott rendszerek figyelésére és hibaelhárítására szolgál. Az OSM új példányként is üzembe helyezhető, vagy saját példányát is használhatja. Az alábbi utasítások a Jaeger új példányát telepítik az `jaeger` AK-fürt névterére.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>A Jaeger üzembe helyezése az AK-fürtön

A következő jegyzékfájl alkalmazása a Jaeger telepítéséhez:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Az OSM-bővítmény nyomkövetésének engedélyezése

Ezután engedélyeznie kell az OSM-bővítmény nyomkövetését.

> [!NOTE]
> Mostantól a nyomkövetési tulajdonságok jelenleg nem látható az OSM-config configmap. Ezt a látható az OSM AK-bővítmény új kiadása fogja elvégezni.

Futtassa a következő parancsot az OSM-bővítmény nyomkövetésének engedélyezéséhez:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>A Jaeger felhasználói felületének megtekintése a port továbbításával

A Jaeger felhasználói felülete az 16686-es porton fut. A webes felhasználói felület megtekintéséhez használhatja a kubectl-portot:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

A böngészőben meg kell jelennie a szolgáltatás legördülő listájának, amely lehetővé teszi a könyvesbolt bemutatójában üzembe helyezett különböző alkalmazások kiválasztását. Válasszon ki egy szolgáltatást az összes kinézetének megtekintéséhez. Ha például a bookbuyer-t egy óra Lookback választja ki, akkor az interakciókat a könyvesbolt-v1 és a könyvesbolt-V2 használatával tekintheti meg, az idő szerint rendezve.

![OSM Jaeger nyomkövetési oldal felhasználói felületének képe](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Válassza ki bármelyik elemet a további részletek megtekintéséhez. Jelöljön ki több elemet a Nyomkövetések összehasonlításához. Összehasonlíthatja például a bookbuyer interakcióit a könyvesbolt és a könyvesbolt-V2 használatával egy adott időpontban.

A rendszerarchitektúra lapot is kiválaszthatja, hogy megtekintse a különböző alkalmazások interakcióját vagy kommunikációját ábrázoló grafikont. Ez azt mutatja be, hogy a forgalom hogyan áramlik az alkalmazások között.

![OSM Jaeger rendszerarchitektúra felhasználói felületének képe](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>A Service Mesh (OSM) AK kiegészítő hibaelhárítási útmutatóinak megnyitása

Az OSM AK-bővítmény üzembe helyezésekor időnként probléma merülhet fel. A következő útmutatók segítséget nyújtanak a hibák elhárításához és a gyakori problémák megoldásához.

### <a name="verifying-and-troubleshooting-osm-components"></a>Az OSM-összetevők ellenőrzése és hibaelhárítása

#### <a name="check-osm-controller-deployment"></a>Az OSM-vezérlő üzembe helyezésének keresése

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Egy kifogástalan OSM-vezérlő így néz ki:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Az OSM-vezérlő Pod-fájljának megtekintése

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Egy kifogástalan OSM Pod a következőképpen fog kinézni:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Annak ellenére, hogy egy vezérlő ki lett zárva egy bizonyos ponton, van egy másik, amely KÉSZen áll a 1/1-re, és 0 újraindítással fut. Ha az oszlop KÉSZen áll, a 1/1-nál nem hosszabb a Service Mesh sérült állapotban lenne.
Az 0/1-re kész oszlop jelzi, hogy a vezérlő síkja tárolója összeomlik – a naplókat le kell kérni. Lásd az OSM-vezérlő naplófájljainak beszerzése az Azure támogatási központból című szakaszt alább. Az oszlop az 1 értéknél nagyobb számmal áll KÉSZen, és azt jelzi, hogy az oldalkocsik telepítve vannak. Az OSM-vezérlő valószínűleg nem fog működni a hozzá csatlakoztatott oldalkocsikkal.

> [!NOTE]
> Az 0.8.2-es verziótól kezdve az OSM-vezérlő nem HA-módban van, és az üzembe helyezése 1 – egy Pod replika-számmal történik. A pod rendelkezik állapot-ellenőrzésekkel, és szükség esetén a kubelet újraindul.

#### <a name="check-osm-controller-service"></a>Az OSM-vezérlő szolgáltatásának keresése

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Az egészséges OSM-vezérlő szolgáltatás így néz ki:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> A fürt – az IP-cím eltérő lenne. A szolgáltatás nevének és a PORT (ok) nak meg kell egyeznie a fenti példával.

#### <a name="check-osm-controller-endpoints"></a>Az OSM-vezérlő végpontjának keresése

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Az egészséges OSM-vezérlő végpont (ok) a következőképpen fog kinézni:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Az OSM-injektor üzembe helyezésének keresése

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Az egészséges OSM-befecskendező üzembe helyezése a következőképpen néz ki:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Az OSM-befecskendező Pod-fájljának megtekintése

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Az egészséges OSM-befecskendező Pod a következőképpen fog kinézni:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Az OSM-injektor szolgáltatásának keresése

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Az egészséges OSM-befecskendező szolgáltatás így néz ki:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Az OSM-végpontok keresése

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Egy kifogástalan OSM-végpont így néz ki:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>A webhookok ellenőrzésének és mutációjának ellenőrzése

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Az egészséges OSM-érvényesítő webhook így néz ki:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Az egészséges OSM-változékonyságot a következő módon kell kinéznie:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>A szolgáltatás és az érvényesítési webhook HITELESÍTÉSSZOLGÁLTATÓjának ellenőrzése

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

A jól konfigurált érvényesítési webhook-konfiguráció a következőhöz hasonlóan néz ki:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>A szolgáltatás és a mutációs webhook CA-kötegének keresése

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

A jól konfigurált, mutációt mutató webhook-konfiguráció a következőhöz hasonlóan néz ki:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Annak ellenőrzése, hogy az OSM-vezérlő kapott-e egy HITELESÍTÉSSZOLGÁLTATÓI csomagot az érvényesítési (vagy mutációs) webhookhoz

> [!NOTE]
> Mivel a v 0.8.2 fontos tisztában lennie azzal, hogy az AK RP telepíti az érvényesítési webhookot, az AK-egyeztetés biztosítja, hogy létezik, de az OSM-vezérlő kitölti a HITELESÍTÉSSZOLGÁLTATÓI csomagot.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Ez a szám a bájtok számát vagy a HITELESÍTÉSSZOLGÁLTATÓI köteg méretét jelzi. Ha ez üres, 0 vagy 1000 alatti szám, az azt jelzi, hogy a CA-köteg nincs megfelelően kiépítve. A megfelelő HITELESÍTÉSSZOLGÁLTATÓI csomag nélkül az érvényesítési webhook kikerül, és megtiltja, hogy a felhasználó módosítsa az OSM-config ConfigMap a Kube-System névtérben.

Hiba történt, ha a CA-köteg helytelen:

- Kísérlet az OSM-config ConfigMap módosítására:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Hiba:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Megkerülő megoldás, ha az **érvényesítési** Webhook konfigurációjának hibás tanúsítványa van:

- 1. lehetőség – az OSM-vezérlő újraindítása – ez a művelet újraindítja az OSM-vezérlőt. Indításkor a rendszer felülírja a HITELESÍTÉSSZOLGÁLTATÓI köteget mind a mutációs, mind az érvényesítési webhookok esetében.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- 2. lehetőség – 2. lehetőség. Az érvényesítési webhook törlése – az érvényesítési webhook eltávolítása lehetővé teszi, hogy a `osm-config` ConfigMap mutációi többé ne legyenek érvényesítve. Bármilyen javítás átkerül. Az AK-egyeztető egy bizonyos ponton ellenőrzi, hogy létezik-e az érvényesítési webhook, és hogy újra létrehozza-e. Előfordulhat, hogy az OSM-vezérlőt újra kell indítani a HITELESÍTÉSSZOLGÁLTATÓI csomag gyors újraírásához.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- 3. lehetőség – törlés és javítás: a következő parancs törli az érvényesítési webhookot, amely lehetővé teszi, hogy bármilyen értéket adjon hozzá, és azonnal megpróbálja alkalmazni a javítást. A legvalószínűbb, hogy az AK-egyeztető nem lesz elég idő az érvényesítési webhook egyeztetésére és visszaállítására, amely lehetőséget biztosít a legutóbbi igény szerinti módosításra:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>A `osm-config` **ConfigMap** keresése

> [!NOTE]
> Az OSM-vezérlő nem igényli, `osm-config` hogy a ConfigMap jelen legyen a Kube-System névtérben. A vezérlőnek megfelelő alapértelmezett értékekkel kell rendelkezniük a konfigurációhoz, és anélkül is működhetnek.

A létezés ellenőrzéséhez:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Az OSM-config ConfigMap tartalmának megtekintése

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap értékek:

| Kulcs                              | Típus   | Megengedett értékek                                          | Alapértelmezett érték                          | Függvény                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| kimenő                           | logikai   | igaz, hamis                                             | `"false"`                              | Engedélyezi a kimenő forgalom használatát a hálóban.                                                                                                                                                                                                             |
| enable_debug_server              | logikai   | igaz, hamis                                             | `"true"`                               | Lehetővé teszi az OSM-Controller Pod hibakeresési végpontjának a rácsvonalak, például a proxy-kapcsolatok, a tanúsítványok és az SMI-házirendek adatainak listázását.                                                                                    |
| enable_privileged_init_container | logikai   | igaz, hamis                                             | `"false"`                              | Engedélyezi az emelt szintű inicializálási tárolókat a hálóban lévő hüvelyek számára. Hamis érték esetén az init-tárolók csak NET_ADMIN rendelkeznek.                                                                                                                                   |
| envoy_log_level                  | sztring | nyomkövetés, hibakeresés, információ, figyelmeztetés, figyelmeztetés, hiba, kritikus, kikapcsolva | `"error"`                              | Beállítja az oldalkocsi proxyjának naplózási részletességét, csak az újonnan létrehozott, a hálóhoz csatlakozó hüvelyek esetében. A meglévő hüvelyek naplózási szintjének frissítéséhez indítsa újra a telepítést a következővel: `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | sztring | az a. b. c. d/x formátumú IP-tartományok vesszővel tagolt listája | `-`                                    | Azon IP-címtartományok globális listája, amelyek kizárják az oldalkocsis proxy által elfogást a kimenő forgalomból.                                                                                                                                    |
| permissive_traffic_policy_mode   | logikai   | igaz, hamis                                             | `"false"`                              | A értékre való beállítás `true` lehetővé teszi az összes mód használatát a hálóban, azaz a rácsvonalon nincs a forgalmi szabályzat kényszerítése. Ha a értékre `false` van állítva, engedélyezi a megtagadást – a hálóban lévő összes forgalmi házirend, azaz a `SMI Traffic Target` szolgáltatásoknak a kommunikációhoz szükséges. |
| prometheus_scraping              | logikai   | igaz, hamis                                             | `"true"`                               | Engedélyezi a Prometheus-metrikák leselejtezését az oldalkocsis proxyk esetében.                                                                                                                                                                                 |
| service_cert_validity_duration   | sztring | 24 órás, 1h30m (bármely időtartam)                          | `"24h"`                                | Beállítja a szolgáltatás tanúsítványának érvényességi időtartamát, amely decimális számokból álló sorszámként van megadva, amely az opcionális frakcióval és az egység utótaggal együtt jelenik meg.                                                                                             |
| tracing_enable                   | logikai   | igaz, hamis                                             | `"false"`                              | Engedélyezi a Jaeger-nyomkövetést a hálóhoz.                                                                                                                                                                                                    |
| tracing_address                  | sztring | Jaeger. mesh-Namespace. SVC. cluster. local                 | `jaeger.kube-system.svc.cluster.local` | A Jaeger üzemelő példányának címe, ha a nyomkövetés engedélyezve van.                                                                                                                                                                                |
| tracing_endpoint                 | sztring | /api/v2/spans                                           | /api/v2/spans                          | Az adatnyomkövetési végpont, ha engedélyezve van a nyomkövetés.                                                                                                                                                                                          |
| tracing_port                     | int    | nullától eltérő egész érték                              | `"9411"`                               | A nyomkövetést engedélyező port.                                                                                                                                                                                                       |
| use_https_ingress                | logikai   | igaz, hamis                                             | `"false"`                              | Engedélyezi a HTTPS bejövő forgalmat a Hálón.                                                                                                                                                                                                      |
| config_resync_interval           | sztring | 1 perc alatt letiltja ezt                            | 0 (letiltva)                           | Ha az 1 millió (60-as) értéket adja meg, az OSM-vezérlő az összes elérhető konfigurációt elküldi az egyes csatlakoztatott megbízottak számára az adott intervallumban.                                                                                                    |

#### <a name="check-namespaces"></a>Névterek keresése

> [!NOTE]
> A Kube-rendszernévtér soha nem fog részt venni a szolgáltatási hálóban, és soha nem lesz címkézve és/vagy megjegyzésekkel ellátva az alábbi kulcs/értékek szerint.

A parancs használatával a `osm namespace add` névtereket egy adott Service meshhoz csatlakoztatjuk.
Ha egy k8s-névtér a rácsvonal része (vagy ahhoz, hogy a rácsvonal része legyen), az alábbiaknak igaznak kell lenniük:

Megjegyzések megtekintése a

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

A következő jegyzetnek jelen kell lennie:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Címkék megtekintése a

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

A következő címkének jelen kell lennie:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Ha egy névtér nincs jegyzetve az `"openservicemesh.io/sidecar-injection": "enabled"` OSM-injektorral, vagy nem, `"openservicemesh.io/monitored-by": "osm"` akkor a rendszer nem adja hozzá a megbízott oldalkocsikat.

> Megjegyzés: `osm namespace add` ezt követően a rendszer csak az **új** hüvelyeket fogja befecskendezni egy megbízott oldalkocsival. A meglévő hüvelyeket újra kell indítani `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Az SMI CRDs ellenőrzése:

Győződjön meg arról, hogy a fürt rendelkezik a szükséges CRDs:

```azurecli-interactive
kubectl get crds
```

A fürtön telepítve kell lennie a következőknek:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Szerezze be a paranccsal telepített CRDs verzióját:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Várt kimenet:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

Az OSM Controller v 0.8.2 a következő verziókat igényli:

- traffictargets.access.smi-spec.io – [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io – nem támogatott
- trafficsplits.split.smi-spec.io – [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io – [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Ha a CRDs hiányoznak, a következő parancsok használatával telepítheti ezeket a fürtön:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Az Open Service Mesh (OSM) bővítmény letiltása az AK-fürthöz

Az OSM-bővítmény letiltásához futtassa a következő parancsot:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register