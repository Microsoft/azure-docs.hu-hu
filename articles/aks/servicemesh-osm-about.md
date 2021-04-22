---
title: A Service Mesh megnyitása (előzetes verzió)
description: A Service Mesh (OSM) megnyitása a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: b0a37400d02ff324e7191bb0b04243d7f16090bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877155"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>A Service Mesh AKS bővítményének megnyitása (előzetes verzió)

## <a name="overview"></a>Áttekintés

Az [Open Service Mesh (OSM)](https://docs.openservicemesh.io/) egy egyszerűsített, méretezhető, natív felhőszolgáltatás-háló, amely lehetővé teszi a felhasználók számára a rendkívül dinamikus mikroszolgáltatás-környezetek egységesen kezelhető, biztonságos és használatra alkalmas megfigyelhetőségi funkcióinak lekértségét.

Az OSM egy Envoy-alapú vezérlősíkot futtat a Kubernetesben, [konfigurálható SMI](https://smi-spec.io/) API-okkal, és úgy működik, hogy az alkalmazás minden példánya mellé be kell injektálni egy Envoy proxyt oldalkocsi tárolóként. Az Envoy proxy a hozzáférés-vezérlési házirendekkel kapcsolatos szabályokat tartalmaz és hajt végre, útválasztási konfigurációt implementál, és metrikákat rögzít. A vezérlősík folyamatosan konfigurálja a proxykat, hogy a házirendek és az útválasztási szabályok naprakészek maradjanak, és hogy a proxyk kifogástalan állapotúak maradjanak.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Képességek és funkciók

Az OSM a következő képességeket és funkciókat biztosítja, hogy natív felhőszolgáltatás-hálót biztosítson a Azure Kubernetes Service (AKS) fürtök számára:

- A szolgáltatások közötti kommunikáció biztonságossá tételének biztosítása az mTLS engedélyezésével

- Az Envoy proxy automatikus oldalkocsi-injektálásának engedélyezésével könnyedén beveheti az alkalmazásokat a hálóba

- Könnyen és átlátható konfigurációk az üzemelő példányok forgalmának áttolása érdekében

- Lehetőség a szolgáltatásokra vonatkozó, finomhangolt hozzáférés-vezérlési szabályzatok meghatározására és végrehajtására

- Megfigyelhetőség és betekintés az alkalmazásmetrikákba hibakeresési és monitorozási szolgáltatásokhoz

- Integráció külső tanúsítványkezelési szolgáltatásokkal/megoldásokkal moduláris illesztővel

## <a name="scenarios"></a>Forgatókönyvek

Az OSM a következő helyzetekben segíti az AKS-környezetek üzembe helyezését:

- Titkosított kommunikáció létrehozása a fürtben üzembe helyezett szolgáltatásvégpontokkal

- Http/HTTPS- és TCP-forgalom engedélyezése a hálóban

- Súlyozott forgalomvezérlők konfigurálása két vagy több szolgáltatás között A/B vagy canary üzemelő példányok esetén

- KPI-k gyűjtése és megtekintése az alkalmazás forgalmából

## <a name="osm-service-quotas-and-limits-preview"></a>OSM-szolgáltatáskvóták és -korlátok (előzetes verzió)

Az OSM előzetes verzió szolgáltatási kvótáira és korlátaira vonatkozó korlátozásokat az AKS-kvóták és a regionális korlátok oldalán [talál.](https://docs.microsoft.com/azure/aks/quotas-skus-regions)

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
> Ne kísérelje meg telepíteni az OSM-et a bináris fájlból a `osm install` használatával. Ez az OSM olyan telepítését eredményezi, amely nincs az AKS bővítményeként integrálva.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Az előzetes `AKS-OpenServiceMesh` verziójú funkció regisztrálása

Az Open Service Mesh bővítményt használni képes AKS-fürt létrehozásához engedélyeznie kell a funkciójelölőt `AKS-OpenServiceMesh` az előfizetésen.

Regisztrálja `AKS-OpenServiceMesh` a funkciójelölőt az [az feature register paranccsal,][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Eltarthat néhány percig, hogy az állapot Regisztrált _állapotúra mutasson._ Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a _Microsoft.ContainerService_ erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Az Open Service Mesh (OSM) Azure Kubernetes Service (AKS) bővítmény telepítése új AKS-fürthöz

Egy új AKS-fürttelepítési forgatókönyvhöz egy AKS-fürt teljesen új üzembe helyezési folyamatával fog kezdeni, amely lehetővé teszi az OSM-bővítményt a fürt létrehozási műveletében.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli hozzá. Hozzon létre egy erőforráscsoportot [az az group create használatával.](/cli/azure/group#az_group_create) Az alábbi példa létrehoz egy _myOsmAksGroup_ nevű erőforráscsoportot az _eastus2_ helyen (régióban):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>AKS-fürt üzembe helyezése engedélyezett OSM-bővítménysel

Most üzembe fog helyezni egy új AKS-fürtöt, engedélyezett OSM-bővítménysel.

> [!NOTE]
> Vegye figyelembe, hogy a következő AKS üzembe helyezési parancs operációsrendszer-gyorslemezeket használ. Az [AKS-hez](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) szükséges rendszerlemezekkel kapcsolatban itt talál további információt.

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS-fürt hozzáférési hitelesítő adatainak le szolgáltatása

Szerezze be az új felügyelt Kubernetes-fürt hozzáférési hitelesítő adatait.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Open Service Mesh- (OSM-) Azure Kubernetes Service (AKS) bővítmény engedélyezése meglévő AKS-fürthöz

Meglévő AKS-fürt esetén engedélyeznie kell az OSM-bővítményt egy már üzembe helyezett meglévő AKS-fürthöz.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>OSM-bővítmény engedélyezése meglévő AKS-fürthöz

Az AKS OSM bővítmény engedélyezéséhez futtatnia kell a `az aks enable-addons --addons` parancsot, amely a paramétert adja át `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Az alábbi kimenethez hasonló kimenetnek kell lennie annak megerősítéséhez, hogy az AKS OSM bővítmény telepítve van.

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

## <a name="validate-the-aks-osm-add-on-installation"></a>Az AKS OSM bővítmény telepítésének ellenőrzése

Több parancs futtatásával ellenőrizheti, hogy az AKS OSM bővítmény összes összetevője engedélyezve van-e és fut-e:

Először lekérdezheti a fürt bővítményprofiljait a telepített bővítmények engedélyezett állapotának ellenőrzéséhez. A következő parancsnak "true" (igaz) értékeket kell visszaadni.

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Az alábbi `kubectl` parancsok az osm-controller állapotát jelzik.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Az AKS OSM bővítmény elérése

Az OSM-vezérlő konfigurációját jelenleg a konfigurációs térkép segítségével lehet elérni és konfigurálni. Az OSM-vezérlő konfigurációs beállításainak megtekintéséhez a használatával lekérdezheti az osm-config konfigurációs leképezőt a `kubectl` konfigurációs beállításainak megtekintéséhez.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Az OSM-konfigurációtérkép kimenetének az alábbihoz hasonlónak kell lennie:

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

Figyelje **meg, hogy permissive_traffic_policy_mode** beállítása igaz.  Az OSM megengedő forgalmi szabályzat üzemmódja olyan mód, amelyben a rendszer megkerüli az [SMI](https://smi-spec.io/) forgalmi szabályzat kényszerítését. Ebben a módban az OSM automatikusan felderíti a szolgáltatási háló részét képezi szolgáltatásokat, és programja az egyes envoy proxy-oldalkocsikra vonatkozó adatforgalmi házirendszabályokat, hogy kommunikálni tudjanak ezekkel a szolgáltatásokkal.

> [!WARNING]
> Mielőtt továbblépne, ellenőrizze, hogy a megengedő forgalmi szabályzat mód igazra van-e állítva, ha nem, módosítsa **true** (igaz) értékre az alábbi paranccsal

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Az Open Service Mesh (OSM) Azure Kubernetes Service (AKS) bővítmény által felügyelt új alkalmazás üzembe helyezése

### <a name="before-you-begin"></a>Előkészületek

Az útmutató lépései azt feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes és újabb verziók, engedélyezett Kubernetes RBAC-val), kapcsolatot létesített a fürttel (ha segítségre van szüksége ezen elemek bármelyikéhez, tekintse meg az AKS rövid útmutatóját, és telepítette az `1.19+` `kubectl` [AKS](./kubernetes-walkthrough.md)OSM bővítményt.

A következő erőforrásoknak kell telepítve lennie:

- Az Azure CLI 2.20.0-s vagy újabb verziója
- A `aks-preview` bővítmény 0.5.5-ös vagy újabb verziója
- OSM 0.8.0-s vagy újabb verzió
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>Névterek létrehozása az alkalmazáshoz

Ebben az útmutatóban az OSM-könyváruház alkalmazást fogjuk használni, amely a következő Kubernetes-szolgáltatásokkal rendelkezik:

- bookbuyer
- bookthief
- Könyvesbolt
- bookwarehouse

Hozzon létre névtereket mindegyik alkalmazás-összetevőhöz.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Az OSM által felügyelt névterek

Amikor hozzáadja a névtereket az OSM-hálóhoz, az lehetővé teszi, hogy az OSM-vezérlő automatikusan bejektálja az Envoy sidecar proxytárolókat az alkalmazásba. Futtassa a következő parancsot az OSM-könyváruház alkalmazásnévterének a táblába való be- és feliratkához.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>A bookstore alkalmazás üzembe helyezése az AKS-fürtön

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

Az üzembe helyezés összes kimenetét az alábbiakban foglaljuk össze.

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

### <a name="checkpoint-what-got-installed"></a>Ellenőrzőpont: Mi lett telepítve?

A Bookstore mintaalkalmazás egy többrétegű alkalmazás, amely négy szolgáltatásból áll: a bookbuyer, a bookthief, a bookstore és a bookwarehouse. A bookbuyer és a bookthief szolgáltatás is kommunikál a könyváruház szolgáltatással, hogy könyveket olvas be a könyváruház szolgáltatásból. A könyváruház szolgáltatás könyveket olvas be a könyváruház szolgáltatásból, hogy a könyvbuyerét és a könyvthiefet is meg tudja kínálni. Ez egy egyszerű, többrétegű alkalmazás, amely jól mutatja be, hogyan használható a szolgáltatási háló az alkalmazásszolgáltatások közötti kommunikáció védelmére és az közöttük való kommunikációra. Az útmutató folytatásában engedélyezni és letiltani fogjuk a Service Mesh Interface- (SMI-) szabályzatokat, hogy a szolgáltatások az OSM-en keresztül is kommunikáljanak. Az alábbiakban egy architektúradiagram látható, amely azt mutatja be, hogy mi lett telepítve a könyváruház-alkalmazáshoz.

![AZ OSM bookbuyer alkalmazás architektúrája](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Az AKS-fürtön belül futó bookstore alkalmazás ellenőrzése

A jelenleg üzembe helyezett bookstore mulit-container alkalmazás csak az AKS-fürtön belülről érhető el. A későbbi oktatóanyagok segítséget nyújtanak az alkalmazásnak a fürtön kívüli, bejövő vezérlőn keresztüli felfedésében. Jelenleg portirányítással férünk hozzá az AKS-fürtön belüli bookbuyer alkalmazáshoz, és ellenőrizzük, hogy könyveket vásárol-e a könyváruház szolgáltatástól.

Annak ellenőrzéséhez, hogy az alkalmazás fut-e a fürtön belül, egy port továbbító használatával fogjuk megtekinteni a bookbuyer és a bookthief összetevők felhasználói felületét.

Először is szerezze be a bookbuyer pod nevét

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookbuyer podhoz egyedi név lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Ha már megvan a pod neve, a port forward paranccsal beállíthatjuk az alagutat a helyi rendszer és az AKS-fürtön belüli alkalmazás között. Futtassa a következő parancsot a helyi rendszer 8080-as portjának beállítására. Használja ismét a megadott bookbuyer podnevet.

> [!NOTE]
> Minden port-továbbítási parancshoz ajánlott egy további terminált használni, hogy tovább haladjon ezen az útmutatón, és ne válassza le az alagutat. Emellett az is a legjobb, ha a port továbbító alagutat a Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Ehhez hasonló kimenetnek kell lennie.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Amíg a port-továbbítási munkamenet a helyén van, nyissa meg a következő URL-címet egy `http://localhost:8080` böngészőből. Most már az alábbi képen láthatóhoz hasonló módon meg kell látnia a bookbuyer alkalmazás felhasználói felületét a böngészőben.

![Az OSM Bookbuyer alkalmazás felhasználói felületének képe](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Azt is figyelje meg, hogy a megvásárolt könyvek teljes száma folyamatosan növekszik a könyváruház v1 szolgáltatásában. A könyváruház v2 szolgáltatás még nem lett üzembe helyezni. A könyváruház v2 szolgáltatását az SMI forgalomfedő szabályzatok szemléltetését fogjuk üzembe helyezni.

Ugyanezt ellenőrizheti a bookthief szolgáltatáshoz is.

```azurecli-interactive
kubectl get pod -n bookthief
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookthief podhoz egyedi név lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Port továbbítva a bookthief podba.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Nyissa meg a következő URL-címet egy `http://localhost:8080` böngészőben. Láthatja, hogy a könyvthief éppen könyveket ellop a könyváruház szolgáltatástól. Később egy forgalmi szabályzatot implementálunk, amely leállítja a foglalást.

![Az OSM bookthief alkalmazás felhasználói felületének képe](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Az OSM megengedő forgalmi módjának letiltása a hálóhoz

Ahogy korábban említettük, az OSM-fürt konfigurációjának megtekintésekor az OSM-konfiguráció alapértelmezés szerint engedélyezi a megengedő forgalom módú szabályzatot. Ebben a módban a rendszer megkerüli a forgalmi szabályzat kényszerítését, és az OSM automatikusan felderíti a szolgáltatási háló részét képezi szolgáltatásokat, és az egyes envoy proxyoldali oldalkocsikra vonatkozó forgalmi házirendszabályokat programja, hogy kommunikálni tudjanak ezekkel a szolgáltatásokkal.

Most letiltjuk a megengedő forgalom módú szabályzatot, és az OSM-nek explicit [SMI-szabályzatokat](https://smi-spec.io/) kell üzembe helyeznie a fürtön, hogy lehetővé tegye a hálóban az egyes szolgáltatások kommunikációját. A megengedő forgalmi mód letiltásához futtassa a következő parancsot a configmap tulajdonság frissítéséhez, és az értéket értékről értékre `true` `false` módosítja.

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookthief podhoz egyedi név lesz hozzáfűzve.

```Output
configmap/osm-config patched
```

Annak ellenőrzéséhez, hogy a megengedő forgalmi mód le van-e tiltva, továbbítsa a portot a könyvbuyerbe vagy a bookthief podba, hogy megtekintse a felhasználói felületet a böngészőben, és ellenőrizze, hogy a megvásárolt vagy ellopott könyvek száma már nem növekszik-e. Frissítse a böngészőt. Ha a növekmény leállt, a szabályzat megfelelően lett alkalmazva. Sikeresen leállította a könyváruházat a könyvek ellopásából, de sem a könyvbuyer nem vásárolhat a könyváruházból, sem a könyváruház nem tud könyveket lekérni a könyváruházból. A következő lépésként [SMI-szabályzatokat](https://smi-spec.io/) alkalmazunk, amelyek csak a hálóban olyan szolgáltatásokat engedélyeznek, amelyekről kommunikálni szeretne.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Service Mesh-felület (SMI) forgalom-hozzáférési szabályzatok alkalmazása

Most, hogy letiltottunk minden kommunikációt a hálóban, lehetővé tegyük, hogy a bookbuyer szolgáltatás kommunikáljon a könyváruház-szolgáltatással könyvek vásárlása érdekében, és hogy a könyváruház-szolgáltatás kommunikáljon a könyváruház szolgáltatással az értékesíthető könyvek leolvasása érdekében.

Telepítse az [alábbi SMI-szabályzatokat.](https://smi-spec.io/)

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

Most már portirányítási munkamenetet állíthat be a bookbuyer vagy a bookstore podon, és láthatja, hogy a megvásárolt könyvek és az eladott könyvek metrika is növekszik. Ugyanezt a könyvthief podon is meg lehet tenni annak ellenőrzéséhez, hogy a továbbiakban nem tud-e könyveket ellopni.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Service Mesh Interface- (SMI-) forgalomfosztó szabályzatok alkalmazása

Az utolsó bemutatóhoz létrehozunk egy [SMI-forgalomfosztó](https://smi-spec.io/) szabályzatot, amely háttérként konfigurálja az egyik szolgáltatás és több szolgáltatás közötti kommunikáció súlyozását. A forgalom felosztása funkció lehetővé teszi a kapcsolatok fokozatos áthelyezését az egyik szolgáltatásból a másikba a forgalom 0 és 100 közötti súlyozása révén.

Az alábbi ábra az üzembe helyezendő [SMI](https://smi-spec.io/) Traffic Split szabályzatot ábrázolja. Üzembe helyezünk egy további, 2. verziójú könyváruházat, majd elosztjuk a bookbuyertől érkező forgalmat, a forgalom 25%-át a könyvestár v1 szolgáltatásra, 75%-át pedig a könyváruház v2 szolgáltatásra.

![Az OSM bookbuyer forgalmi felosztási diagramja](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

A bookstore v2 szolgáltatás üzembe helyezése.

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

Most telepítse a forgalomfelosztó szabályzatot a bookbuyer forgalom felosztására a két könyváruház v1 és v2 szolgáltatás között.

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

Állítson be egy port forward alagutat a bookbuyer podhoz, és most már látnia kell a könyváruház v2 szolgáltatástól vásárolt könyveket. Ha továbbra is a vásárlások növekményét nézi, a vásárlások gyorsabb növekményét kell látnia a könyvestár v2 szolgáltatáson keresztül.

![OSM bookbuyer-könyvek a felhasználói felületen](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Az Open Service Mesh (OSM) Azure Kubernetes Service (AKS) bővítmény által felügyelni Azure Kubernetes Service telepített alkalmazások kezelése

### <a name="before-you-begin"></a>Előkészületek

Az útmutató lépései azt feltételezik, hogy korábban engedélyezte az OSM AKS bővítményt az AKS-fürthöz. Ha nem, a folytatás előtt tekintse át az [Open Service Mesh (OSM) Azure Kubernetes Service (AKS)](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) bővítmény engedélyezése meglévő AKS-fürthöz című szakaszt. Emellett az AKS-fürtnek Kubernetes vagy újabb verziójúnak kell lennie, engedélyeznie kell a Kubernetes RBAC-t, és kapcsolatot kell létesítenie a fürttel (ha segítségre van szüksége ezen elemek bármelyikéhez, tekintse meg az AKS rövid útmutatóját, és telepítette az `1.19+` `kubectl` AKS [](./kubernetes-walkthrough.md)OSM bővítményt.

A következő erőforrásoknak kell telepítve lennie:

- Az Azure CLI 2.20.0-s vagy újabb verziója
- A `aks-preview` bővítmény 0.5.5-ös vagy újabb verziója
- OSM 0.8.0-s vagy újabb verzió
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Ellenőrizze az Open Service Mesh (OSM) megengedő forgalommód szabályzatát

Az OSM megengedő forgalom-szabályzat mód olyan mód, amelyben a rendszer megkerüli az [SMI](https://smi-spec.io/) forgalmi szabályzat kényszerítését. Ebben a módban az OSM automatikusan felderíti a szolgáltatási háló részét képezi szolgáltatásokat, és az egyes envoy proxy-oldalkocsikra vonatkozó adatforgalmi házirendszabályokat programja, hogy kommunikálni tudjanak ezekkel a szolgáltatásokkal.

A fürthöz jelenleg megengedő OSM-forgalom módjának ellenőrzéséhez futtassa a következő parancsot:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Az OSM-konfigurációtérkép kimenetének az alábbihoz hasonlónak kell lennie:

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

Ha **a permissive_traffic_policy_mode** igazra van konfigurálva, biztonságosan, a szolgáltatások közötti kommunikáció megszakítása nélkül is felveheti a névtereket. Ha a **permissive_traffic_policy_mode** hamisra van konfigurálva, győződjön meg arról, hogy a megfelelő [SMI-forgalom-hozzáférési](https://smi-spec.io/) házirendjegyzékek vannak telepítve, valamint gondoskodnia kell arról, hogy legyen egy szolgáltatásfiókja, amely a névtérben üzembe helyezett összes szolgáltatást képviseli. Kövesse a meglévő üzembe helyezett alkalmazások az [Open Service Mesh (OSM)](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false) megengedő forgalmi szabályzattal való, hamisként konfigurált beállításával kapcsolatos útmutatót

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Meglévő üzembe helyezett alkalmazások beállítása igazként konfigurált Open Service Mesh (OSM) megengedő forgalom-szabályzattal

Először hozzáadjuk az üzembe helyezett alkalmazásnévtereket az OSM-hez a kezeléshez.

```azurecli-interactive
osm namespace add bookstore
```

A következő kimenetnek kell megjelennie:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

A következő lépés a pod aktuális üzemelő példányának a névtérben való megvetése. Futtassa a következő parancsot a kijelölt névtérben a podok megtekintéséhez.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

A következő hasonló kimenetnek kell lennie:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Figyelje meg, hogy a **READY** **oszlopban az 1/1** érték látható, ami azt jelenti, hogy az alkalmazáspod csak egy tárolóval rendelkezik. A következő lépés az alkalmazás üzembe helyezésének újraindítása, hogy az OSM be tudja injektálni az Envoy sidecar proxytárolót az alkalmazáspodba. Nézzük meg a névtérben található üzemelő példányok listáját.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

A következő kimenetnek kell megjelennie:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Most újraindítjuk az üzembe helyezést, hogy bevezényljük az envoy sidecar proxytárolót az alkalmazáspodba. Futtassa az alábbi parancsot.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

A következő kimenetnek kell megjelennie:

```Output
deployment.apps/bookbuyer restarted
```

Ha ismét megnézzük a podokat a névtérben:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Figyelje meg, hogy a **READY** (KÉSZ) oszlopban **most már 2/2** tároló látható, amelyek készen állnak a podra. A második tároló az Envoy sidecar proxy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

A konfiguráció megtekintéséhez tovább vizsgálhatja a podot a envoy proxy megtekintéséhez a describe parancs futtatásával.

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

Ellenőrizze, hogy az alkalmazás továbbra is működőképes-e az Envoy sidecar proxy injektálás után.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Meglévő üzembe helyezett alkalmazások beállítása hamisként konfigurált Open Service Mesh (OSM) megengedő forgalom-szabályzattal

Ha a megengedő adatforgalmi szabályzat OSM-konfigurációja a következőre van beállítva: , az OSM-nek explicit SMI-forgalom-hozzáférési szabályzatokat kell telepítenie ahhoz, hogy a szolgáltatások közötti kommunikáció a fürtön belül `false` történjen. [](https://smi-spec.io/) Az OSM jelenleg Kubernetes-szolgáltatásfiókokat is használ a szolgáltatások közötti kommunikációhoz. Annak érdekében, hogy a meglévő telepített alkalmazások kommunikáljanak az OSM-háló kezelésekor, ellenőrizni kell, hogy létezik-e a használni szükséges szolgáltatásfiók, frissíteni kell az alkalmazás üzembe helyezését a szolgáltatásfiók adataival, alkalmazni kell az [SMI](https://smi-spec.io/) forgalom-hozzáférési szabályzatait.

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes Service-fiókok ellenőrzése

Ellenőrizze, hogy van-e Kubernetes Service-fiók abban a névtérben, amelybe az alkalmazást üzembe helyeznie kell.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

A következőben a bookbuyer névtérben található egy nevű `bookbuyer` szolgáltatásfiók.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Ha az alapértelmezett fiókon kívül nincs más szolgáltatásfiókja, létre kell hoznia egyet az alkalmazáshoz. Használja példaként a következő parancsot egy szolgáltatásfiók létrehozásához az alkalmazás üzembe helyezett névterében.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Az alkalmazás aktuális üzembe helyezési specifikációinak megtekintése

Ha a korábbi szakaszban létrehozott egy szolgáltatásfiókot, akkor valószínű, hogy az alkalmazás üzembe helyezése nincs konfigurálva az üzembe `serviceAccountName` helyezési specifikációban megadottak szerint. Az alkalmazás üzembe helyezési specifikációit a következő parancsokkal lehet megtekinteni:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

A kimenetben megjelenik az üzemelő példányok listája.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Most ellenőrzésként ismertetjük az üzembe helyezést annak ellenőrzéséhez, hogy van-e szolgáltatásfiók a podsablon szakaszban.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

Ebben az üzemelő példányban láthatja, hogy van egy szolgáltatásfiók, amely a podsablon szakaszban felsorolt üzemelő példányhoz van társítva. Ez az üzembe helyezés a bookbuyer szolgáltatásfiókot használja. Ha nem látja a **Szolgáltatásfiók:** tulajdonságot, az üzemelő példány nincs konfigurálva szolgáltatásfiók használatára.

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

Az üzemelő példány többféle módszerrel frissítve kubernetes-szolgáltatásfiókot adhat hozzá. Tekintse át a Kubernetes dokumentációját [az üzemelő](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) példány beágyazott frissítéséről vagy a [podok szolgáltatásfiókjának konfigurálásáról.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) Miután frissítette az üzembe helyezési specifikációt a szolgáltatásfiókkal, ismét üzembe kell azt (kubectl apply -f your-deployment.yaml) a fürtön.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>A szükséges Service Mesh Interface- (SMI-) szabályzatok üzembe helyezése

A hitelesített forgalom hálóban való áramlásának utolsó lépése [](https://smi-spec.io/) a szükséges SMI-forgalom-hozzáférési szabályzatok üzembe helyezése az alkalmazás számára. Az [SMI](https://smi-spec.io/) forgalom-hozzáférési szabályzatokkal elérhető konfiguráció mennyisége túlmutat ennek az útmutatónak a hatókörében, de részletesen is kiadunk néhány gyakori összetevőt a specifikációból, és megmutatjuk, hogyan konfigurálhat egy egyszerű TrafficTarget- és HTTPRouteGroup-szabályzatot, hogy lehetővé tegye a szolgáltatások közötti kommunikációt az alkalmazás számára.

Az [SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) megadhatja a felhasználók számára a hozzáférés-vezérlési szabályzatot az alkalmazásaikhoz. A **TrafficTarget** és a **HTTPRoutGroup** API-erőforrásokra koncentrálunk.

A TrafficTarget erőforrás három fő konfigurációs beállítási célból, szabályokból és forrásokból áll. Alább látható egy példa a TrafficTargetre.

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

A fenti TrafficTarget-specifikációban a a cél forrásszolgáltatáshoz konfigurált `destination` szolgáltatásfiókot jelöli. Ne feledje, hogy a korábban az üzemelő példányhoz hozzáadott szolgáltatásfiókkal engedélyezi a hozzáférést ahhoz a központi telepítéshez, amelyhez csatolva van. A szakasz ebben a konkrét példában a kapcsolaton engedélyezett `rules` HTTP-forgalom típusát határozza meg. A HTTP-fejlécek részletes regex-mintáit úgy konfigurálhatja, hogy pontosan megszabadják a HTTP-n keresztül engedélyezett forgalmat. A `sources` szakasz a kommunikációt kezdeményező szolgáltatás. Ez a specifikáció azt olvassa, hogy a bookbuyernek kommunikálnia kell a könyváruházval.

A HTTPRouteGroup erőforrás a HTTP-fejlécinformációk egyezéseit tartalmazó tömbből vagy egy tömbből áll, és a TrafficTarget specifikáció követelménye. Az alábbi példában láthatja, hogy a HTTPRouteGroup három HTTP-műveletet ad vissza: két GET és egy POST műveletet.

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

Ha nem ismeri az előtere alkalmazás által az alkalmazás más rétegei felé generált HTTP-forgalom típusát, mivel a TrafficTarget specifikációhoz szabályra van szükség, létrehozhatja az összes szabály engedélyezése megfelelőt a HTTPRouteGroup alábbi specifikációjával.

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

Miután konfigurálta a TrafficTarget és a HTTPRouteGroup specifikációt, egyetlen YAML-fájlként helyezheti őket üzembe. Az alábbiakban a könyvestár példakonfigurációja látható.

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

A [specifikációval kapcsolatos](https://smi-spec.io/) további információkért látogasson el az SMI webhelyére.

### <a name="manage-the-applications-namespace-with-osm"></a>Az alkalmazás névterének kezelése az OSM-ben

Ezután konfiguráljuk az OSM-et a névtér kezelésére, és újraindítjuk az üzemelő példányokat az alkalmazással injektált Envoy Sidecar proxy leindításakor.

A következő parancs futtatásával konfigurálja a `azure-vote` névteret az OSM-ként való felügyeltként.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Ezután indítsa újra a és `azure-vote-front` `azure-vote-back` az üzemelő példányokat az alábbi parancsokkal.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Ha megtekintjük a podokat a névtérhez, a és a READY szakasza `azure-vote`  `azure-vote-front` is 2/2-ként látható, ami azt jelenti, hogy az Envoy sidecar proxy az alkalmazással együtt lett `azure-vote-back` injektálva.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Oktatóanyag: Az Open Service Mesh (OSM) által felügyelt alkalmazás üzembe helyezése NGINX bejövő forgalommal

Az Open Service Mesh (OSM) egy egyszerűsített, méretezhető, natív felhőszolgáltatás-háló, amely lehetővé teszi a felhasználók számára a rendkívül dinamikus mikroszolgáltatás-környezetek egységesen kezelhető, biztonságos és használatra alkalmas megfigyelhetőségi funkcióinak lekértségét.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> - Az OSM-fürt aktuális konfigurációjának megtekintése
> - Az OSM névtere(i) létrehozása az üzembe helyezett alkalmazások kezeléséhez a névtérben(nak)
> - Az OSM által felügyelt névterek
> - A mintaalkalmazás üzembe helyezése
> - Az AKS-fürtön belül futó alkalmazás ellenőrzése
> - Az alkalmazáshoz használt NGINX bejövő vezérlő létrehozása
> - Szolgáltatás elérhetővé Azure Application Gateway bejövő forgalomból az internetre

### <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes és újabb, engedélyezett Kubernetes RBAC-val), kapcsolatot létesített a fürttel (ha segítségre van szüksége ezen elemek bármelyikéhez, tekintse meg az AKS rövid útmutatóját, és telepítette az `1.19+` `kubectl` [AKS](./kubernetes-walkthrough.md)OSM bővítményt.

A következő erőforrásoknak kell telepítve lennie:

- Az Azure CLI 2.20.0-s vagy újabb verziója
- A `aks-preview` bővítmény 0.5.5-ös vagy újabb verziója
- OSM 0.8.0-s vagy újabb verzió
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Az OSM-fürt aktuális konfigurációjának megtekintése és ellenőrzése

Ha az AKS-hez használható OSM-bővítmény engedélyezve van az AKS-fürtön, az aktuális konfigurációs paramétereket az osm-config Kubernetes ConfigMap könyvtárban tudja megtekinteni. Futtassa a következő parancsot a ConfigMap tulajdonságainak megtekintéséhez:

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

Figyelje **meg, hogy permissive_traffic_policy_mode** beállítása igaz.  Az OSM megengedő forgalmi szabályzat üzemmódja olyan mód, amelyben a rendszer megkerüli az [SMI](https://smi-spec.io/) forgalmi szabályzat kényszerítését. Ebben a módban az OSM automatikusan felderíti a szolgáltatási háló részét képezi szolgáltatásokat, és programja az egyes envoy proxy-oldalkocsikra vonatkozó adatforgalmi házirendszabályokat, hogy kommunikálni tudjanak ezekkel a szolgáltatásokkal.

### <a name="create-namespaces-for-the-application"></a>Névterek létrehozása az alkalmazáshoz

Ebben az oktatóanyagban az OSM bookstore alkalmazást fogjuk használni, amely a következő alkalmazás-összetevőkkel rendelkezik:

- bookbuyer (könyvbuyer)
- bookthief (könyvthief)
- Könyvesbolt
- bookwarehouse (könyvházak)

Hozzon létre névtereket mindegyik alkalmazás-összetevőhöz.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Az OSM által felügyelt névterek

Ha hozzáadja a névtereket az OSM-hálóhoz, az OSM-vezérlő automatikusan beveszi az Envoy sidecar proxytárolókat az alkalmazásba. Futtassa a következő parancsot az OSM-könyváruház alkalmazásnévterének a táblába való be- és feliratkához.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>A bookstore alkalmazás üzembe helyezése az AKS-fürtön

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

Az üzembe helyezés összes kimenetét az alábbiakban foglaljuk össze.

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

Frissítse a bookbuyer szolgáltatást a megfelelő bejövő portkonfigurációra az alábbi szolgáltatásjegyzékben.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Az AKS-fürtön belül futó könyváruház-alkalmazás ellenőrzése

A jelenleg a bookstore mulit-container alkalmazást üzembe helyeztünk, de az csak az AKS-fürtön belülről érhető el. Később hozzáadjuk a Azure Application Gateway vezérlőt, hogy elérhetővé tegye az alkalmazást az AKS-fürtön kívül. Annak ellenőrzéséhez, hogy az alkalmazás fut-e a fürtön belül, egy port továbbító használatával fogjuk megtekinteni a bookbuyer összetevő felhasználói felületét.

Először szerezze be a bookbuyer pod nevét

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookbuyer podhoz egy egyedi név lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Ha már megvan a pod neve, a port forward paranccsal állíthatunk be egy alagutat a helyi rendszer és az AKS-fürtön belüli alkalmazás között. Futtassa a következő parancsot a helyi rendszer 8080-as portjának beállítására. Használja ismét a megadott bookbuyer podnevet.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

A kimenetnek ehhez hasonlónak kell lennie.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Amíg a port-továbbítási munkamenet a helyén van, nyissa meg a következő URL-címet egy `http://localhost:8080` böngészőből. Most már az alábbi képen láthatóhoz hasonló módon meg kell látnia a bookbuyer alkalmazás felhasználói felületét a böngészőben.

![AZ NGINX felhasználói felülethez való OSM Bookbuyer alkalmazás képe](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>NGINX bejövő forgalomvezérlő létrehozása Azure Kubernetes Service (AKS)

A bejövőforgalom-vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-leválasztást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályának és útvonalának konfigurálására használhatók. A bejövőforgalom-vezérlő és a bejövő szabályok használatával egyetlen IP-cím használatával irányítható a forgalom egy Kubernetes-fürt több szolgáltatására.

A bejövő forgalom vezérlője segítségével elérhetővé teszi az OSM által kezelt alkalmazást az interneten. A bejövő vezérlő létrehozásához használja a Helmet az nginx-ingress telepítéséhez. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő vezérlő replikái futtatásának teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> Az alábbi példa létrehoz egy Kubernetes-névteret a _ingress-basic nevű bejövő forgalom erőforrásaihoz._ Szükség szerint adjon meg egy névteret a saját környezetéhez.

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

Amikor létrejön a Kubernetes terheléselosztási szolgáltatása az NGINX bejövő forgalomvezérlőhöz, a rendszer dinamikus nyilvános IP-címet rendel hozzá, ahogyan az az alábbi kimenetben látható:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Még nem hoztak létre bejövő szabályokat, így az NGINX bejövő forgalomvezérlő alapértelmezett 404-es lapja jelenik meg, ha a belső IP-címre keres. A bejövő forgalomra vonatkozó szabályokat a következő lépésekben konfiguráljuk.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>A bookbuyer szolgáltatás elérhetővéte az interneten

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

### <a name="view-the-nginx-logs"></a>Az NGINX-naplók megtekintése

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

A kimenet az NGINX bejövő vezérlő állapotát jeleníti meg, ha a bejövő szabály sikeresen alkalmazva lett:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Az NGINX-szolgáltatások és a bookbuyer szolgáltatás külső megtekintése

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Mivel a bejövő forgalom jegyzékfájlban a gazdagépnév a teszteléshez használt psuedo név, a DNS-név nem lesz elérhető az interneten. Alternatív megoldásként használhatjuk a curl programot, és az állomásnév fejlécét az NGINX nyilvános IP-címére használhatja, és fogadhatunk egy 200-as kódot, amely sikeresen csatlakozik a bookbuyer szolgáltatáshoz.

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Oktatóanyag: Az Open Service Mesh (OSM) által felügyelt alkalmazás üzembe helyezése Azure Application Gateway AKS-bővítmény használatával

Az Open Service Mesh (OSM) egy egyszerűsített, méretezhető, natív felhőszolgáltatás-háló, amely lehetővé teszi a felhasználók számára a rendkívül dinamikus mikroszolgáltatás-környezetek egységesen kezelhető, biztonságos és használatra alkalmas megfigyelhetőségi funkcióinak lekértségét.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> - Az OSM-fürt aktuális konfigurációjának megtekintése
> - Az OSM névtere(i) létrehozása az üzembe helyezett alkalmazások kezeléséhez a névtérben(nak)
> - Az OSM által felügyelt névterek
> - A mintaalkalmazás üzembe helyezése
> - Az AKS-fürtön belül futó alkalmazás ellenőrzése
> - Hozzon Azure Application Gateway, amely az alkalmazás bejövő vezérlőjeként lesz használva
> - Szolgáltatás elérhetővé Azure Application Gateway bejövő forgalomból az internetre

### <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes és újabb verziók, engedélyezett Kubernetes RBAC-val), kapcsolatot létesített a fürttel (ha segítségre van szüksége ezen elemek bármelyikéhez, tekintse meg az AKS rövid útmutatóját, amely telepíti az `1.19+` `kubectl` AKS [](./kubernetes-walkthrough.md)OSM bővítményt, és új Azure Application Gateway-t hoz létre a bejövő forgalomhoz.

A következő erőforrásoknak kell telepítve lennie:

- Az Azure CLI 2.20.0-s vagy újabb verziója
- A `aks-preview` bővítmény 0.5.5-ös vagy újabb verziója
- Az AKS-fürt 1.19-es vagy újabb verziója Azure CNI használatával (Azure-beli virtuális hálózathoz csatlakoztatva)
- OSM 0.8.0-s vagy újabb verzió
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Az OSM-fürt aktuális konfigurációjának megtekintése és ellenőrzése

Ha az AKS-hez használható OSM-bővítmény engedélyezve van az AKS-fürtön, az aktuális konfigurációs paramétereket az osm-config Kubernetes ConfigMap segítségével tudja megtekinteni. Futtassa a következő parancsot a ConfigMap tulajdonságainak megtekintéséhez:

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

Figyelje **meg permissive_traffic_policy_mode** hogy a beállítás true (igaz) értékre **van konfigurálva.** Az OSM megengedő forgalmi szabályzat üzemmódja olyan mód, amelyben a rendszer megkerüli az [SMI](https://smi-spec.io/) forgalmi szabályzat kényszerítését. Ebben a módban az OSM automatikusan felderíti a szolgáltatási háló részét képezi szolgáltatásokat, és programja az egyes envoy proxy-oldalkocsikra vonatkozó adatforgalmi házirendszabályokat, hogy kommunikálni tudjanak ezekkel a szolgáltatásokkal.

### <a name="create-namespaces-for-the-application"></a>Névterek létrehozása az alkalmazáshoz

Ebben az oktatóanyagban az OSM bookstore alkalmazást fogjuk használni, amely a következő alkalmazás-összetevőkkel rendelkezik:

- bookbuyer (könyvbuyer)
- bookthief (könyvthief)
- Könyvesbolt
- bookwarehouse (könyvházak)

Hozzon létre névtereket mindegyik alkalmazás-összetevőhöz.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Az OSM által felügyelt névterekboard

Amikor hozzáadja a névtereket az OSM-hálóhoz, az lehetővé teszi, hogy az OSM-vezérlő automatikusan beveszi az Envoy sidecar proxytárolókat az alkalmazásba. Futtassa a következő parancsot az OSM-könyváruház alkalmazásnévterének a bevetéshez.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>A bookstore alkalmazás üzembe helyezése az AKS-fürtön

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

Az üzembe helyezés összes kimenetét az alábbiakban foglaljuk össze.

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

Frissítse a bookbuyer szolgáltatást a megfelelő bejövőport-konfigurációra az alábbi szolgáltatásjegyzékben.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Az AKS-fürtön belül futó bookstore alkalmazás ellenőrzése

A jelenleg üzembe helyezett többtárolós könyváruház-alkalmazás csak az AKS-fürtön belülről érhető el. Később hozzáadjuk a Azure Application Gateway vezérlőt, hogy elérhetővé tegye az alkalmazást az AKS-fürtön kívül. Annak ellenőrzéséhez, hogy az alkalmazás fut-e a fürtön belül, egy port továbbító használatával fogjuk megtekinteni a bookbuyer összetevő felhasználói felületét.

Először is szerezze be a bookbuyer pod nevét

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Az alábbihoz hasonló kimenetnek kell megjelennie. A bookbuyer podhoz egy egyedi név lesz hozzáfűzve.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Ha már megvan a pod neve, a port forward paranccsal állíthatunk be egy alagutat a helyi rendszer és az AKS-fürtön belüli alkalmazás között. Futtassa a következő parancsot a helyi rendszer 8080-as portjának beállítására. Használja ismét az adott bookbuyer podnevet.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

A kimenetnek ehhez hasonlónak kell lennie.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Amíg a port-továbbítási munkamenet a helyén van, nyissa meg a következő URL-címet egy `http://localhost:8080` böngészőből. Most már az alábbi képen láthatóhoz hasonló módon meg kell látnia a bookbuyer alkalmazás felhasználói felületét a böngészőben.

![Az OSM Bookbuyer alkalmazás App Gateway felhasználói felületének képe](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Hozzon létre Azure Application Gateway, hogy elérhetővé tegye a bookbuyer alkalmazást az AKS-fürtön kívül

> [!NOTE]
> Az alábbi utasítások a bejövő forgalomhoz Azure Application Gateway új példányt hoznak létre. Ha meglévő Azure Application Gateway szeretne használni, ugorjon a bejövő Application Gateway vezérlő bővítmény engedélyezésére vonatkozó szakaszra.

#### <a name="deploy-a-new-application-gateway"></a>Új alkalmazás Application Gateway

> [!NOTE]
> A meglévő dokumentációra hivatkozunk, amely lehetővé teszi a Application Gateway bejövő forgalomvezérlő bővítményének engedélyezését egy meglévő AKS-fürthöz. Az OSM-anyagoknak megfelelően módosítottunk néhányat. A témával kapcsolatos részletes dokumentáció itt [található.](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)

Most üzembe fog helyezni egy új Application Gateway, amely szimulálja, hogy van egy meglévő Application Gateway, amely a _myCluster_ AKS-fürtre vonatkozó forgalom terheléselosztását szeretné használni. A név Application Gateway _myApplicationGateway_ lesz, de először létre kell hoznia egy nyilvános IP-erőforrást. A neve _myPublicIp_, és egy _myVnet_ nevű új virtuális hálózat, amely a 11.0.0.0/8 címtérből és egy mySubnet nevű alhálózatból és egy _mySubnet_ nevű alhálózatból a myPublicIp használatával helyez üzembe egy Application Gateway-t a _mySubnetben._ 

Ha AKS-fürtöt használ, Application Gateway külön virtuális hálózatokban található tárolóhálózatokat használ, a két virtuális hálózat címterei nem lehetnek átfedésben. Az AKS-fürt által üzembe helyezett alapértelmezett címtér a 10.0.0.0/8, ezért a Application Gateway virtuális hálózat címelőtagját a 11.0.0.0/8 címre állítva állítva.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway (AGIC) bővítmény csak a Application Gateway  v2 SKUs (Standard és WAF)  és a Application Gateway v1 SKUs-t támogatja.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Az AGIC-bővítmény engedélyezése meglévő AKS-fürthöz az Azure CLI-ben

Ha továbbra is használni szeretné az Azure CLI-t, továbbra is engedélyezheti az AGIC bővítményt a létrehozott AKS-fürtben _(myCluster)_ és megadhatja az AGIC bővítményt a létrehozott Application Gateway _myApplicationGateway használatára._

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

A következő paranccsal Azure Application Gateway, hogy az AKS-bővítmény engedélyezve van-e.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Ennek a parancsnak a következőként kell mutatnia a `true` kimenetet: .

#### <a name="peer-the-two-virtual-networks-together"></a>Társviszony létesítása a két virtuális hálózat között

Mivel az AKS-fürtöt a saját virtuális hálózatában, az Application Gateway-t pedig egy másik virtuális hálózatban telepítettük, társviszonyt kell létesítenünk a két virtuális hálózat között ahhoz, hogy a Application Gateway felől a fürt podjaiba áramló forgalom el tudja végezni a forgalmat. A két virtuális hálózat társviszony-létesítéséhez az Azure CLI-parancs két külön futtatására van szükség, hogy a kapcsolat kétirányú legyen. Az első parancs társviszony-kapcsolatot hoz létre a Application Gateway és az AKS virtuális hálózat között; A második parancs létrehozza a társviszony-kapcsolatot a másik irányban.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>A bookbuyer szolgáltatás elérhetővéte az interneten

Alkalmazza a következő bejövő forgalom jegyzékfájlját az AKS-fürtön a bookbuyer szolgáltatás interneten keresztüli elérhetővé Azure Application Gateway.

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

Mivel a bejövő forgalom jegyzékfájlban a gazdagépnév a teszteléshez használt pszeudonév, a DNS-név nem lesz elérhető az interneten. Alternatív megoldásként használhatjuk a curl programot, és az állomásnév fejlécét a Azure Application Gateway nyilvános IP-címére használhatja, és egy 200-as kódot kaphatunk, amely sikeresen csatlakozik a bookbuyer szolgáltatáshoz.

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

- [Az AGIC hibaelhárítási dokumentációja](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [További hibaelhárítási eszközök érhetők el az AGIC GitHub-adattárában](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Az Open Service Mesh (OSM) monitorozása és megfigyelhetősége a Azure Monitor és az Applications Insights használatával

Az Azure Monitor és az Azure Application Insights is segít maximalizálni az alkalmazások és szolgáltatások rendelkezésre állását és teljesítményét azáltal, hogy egy átfogó megoldást kínál a felhőből és a helyszíni környezetből származó telemetriai adatok gyűjtésére, elemzésére és kezelésére.

Az OSM AKS bővítmény mély integrációt biztosít mindkét Azure-szolgáltatásba, és az OSM-metrikák által biztosított kritikus KPI-k megtekintéséhez és a kritikus kpI-kre való válaszadáshoz az Azure-beli élmény nyújtotta. A szolgáltatások OSM AKS-bővítményhez való engedélyezéséről és konfigurálásról további információt a Azure Monitor [for OSM](https://aka.ms/azmon/osmpreview) oldalán található.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Oktatóanyag: A Prometheus, a Grafana és a Jaeger manuális üzembe helyezése az Open Service Mesh- (OSM-) metrikák megtekintéséhez a megfigyelhetőség érdekében

> [!WARNING]
> A Prometheus, a Grafana és a Jaeger telepítése általános útmutatásként szolgál, amely bemutatja, hogyan használhatók ezek az eszközök az OSM-metrikaadatok megtekintéséhez. A telepítési útmutató nem használható éles környezetben való telepítéshez. Tekintse meg az egyes eszközök dokumentációját arról, hogy mi a legmegfelelőbb a telepítésükhöz. A legfontosabb az állandó tároló hiánya, ami azt jelenti, hogy a Prometheus Grafana és/vagy Jaeger-podok leállása után minden adat elveszik.

Az Open Service Mesh (OSM) részletes metrikákat hoz létre a hálón belüli összes forgalomhoz. Ezek a metrikák betekintést nyújtanak az alkalmazások viselkedésbe a hálóban, és segítik a felhasználókat az alkalmazások hibaelhárításában, karbantartásában és elemzésében.

A mai naptól az OSM közvetlenül az oldalkocsi proxykról (Envoy) gyűjt metrikákat. Az OSM részletes metrikákat biztosít a hálóban lévő összes szolgáltatás bejövő és kimenő forgalmához. Ezekkel a metrikákkal a felhasználó információt kaphat a teljes forgalom mennyiségről, a forgalomon belüli hibákról és a kérések válaszidejről.

Az OSM a Prometheus segítségével konzisztens forgalmi metrikákat és statisztikákat gyűjt és tárol a hálóban futó összes alkalmazáshoz. A Prometheus egy nyílt forráskódú monitorozási és riasztási eszközkészlet, amelyet gyakran használnak (de nem kizárólagosan) Kubernetes- és Service Mesh-környezetekben.

A háló részét képezi minden alkalmazás egy podban fut, amely egy olyan envoy sidecart tartalmaz, amely Metrikákat (proxymetrikákat) fed fel Prometheus formátumban. Emellett minden pod, amely a háló része, Prometheus-jegyzetekkel rendelkezik, ami lehetővé teszi, hogy a Prometheus-kiszolgáló dinamikusan leselkedje az alkalmazást. Ez a mechanizmus automatikusan lehetővé teszi a metrikák lekért mintákat, amikor új névteret/podot/szolgáltatást adnak a hálóhoz.

Az OSM-metrikák a Grafanával megtekinthetők, amely egy nyílt forráskódú vizualizációs és elemzési szoftver. Lehetővé teszi a metrikák lekérdezését, vizualizálását, riasztását és felfedezését.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> - Prometheus-példány létrehozása és üzembe helyezése
> - Az OSM konfigurálása a Prometheus-leselkedés engedélyezése érdekében
> - A Prometheus-konfigurációtérkép frissítése
> - Grafana-példány létrehozása és üzembe helyezése
> - A Grafana konfigurálása a Prometheus-adatforrással
> - OSM-irányítópult importálása a Grafanához
> - Jaeger-példány létrehozása és üzembe helyezése
> - Jaeger-nyomkövetés konfigurálása OSM-hez

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Prometheus-példány üzembe helyezése és konfigurálása OSM-hez

A Prometheus-példányt a Helm használatával fogjuk üzembe helyezni. Futtassa a következő parancsokat a Prometheus Helmen keresztüli telepítéséhez:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Ha a telepítés sikeres volt, az alábbihoz hasonló kimenetet kell látnia. Jegyezze fel a Prometheus-kiszolgáló portját és a fürt DNS-nevét. Ezt az információt később a Grafana adatforrásaként fogja használni a Prometheus konfigurálhoz.

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

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Az OSM konfigurálása a Prometheus-leselkedés engedélyezése érdekében

Annak érdekében, hogy az OSM-összetevők konfigurálva vannak a Prometheus-adatgyűjtemények számára, ellenőrizni kell az osm-config konfigurációs fájlban található prometheus_scraping-konfigurációt.  Tekintse meg a konfigurációt a következő paranccsal:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Az előző parancs kimenetének akkor kell visszaadnia, ha az `true` OSM prometheus-leselejtezésre van konfigurálva. Ha a visszaadott érték , akkor a konfigurációt a következőre kell `false` frissítenünk: `true` . Futtassa a következő parancsot **az** OSM Prometheus-lekésés bekapcsoláshoz:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

A következő kimenetnek kell megjelennie.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>A Prometheus-konfigurációtérkép frissítése

A Prometheus alapértelmezett telepítése két Kubernetes-konfigurációtérképet tartalmaz. A Prometheus-konfigurációtérképek listáját az alábbi paranccsal tudja megtekinteni.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

A **stable-prometheus-server** konfigurációban található prometheus.yml konfigurációt az alábbi OSM-konfigurációra kell cserélnünk. Ezt a feladatot többféle fájlszerkesztési módszerrel is el lehet érni. Egy egyszerű és biztonságos módszer a konfigurációs térkép exportálása, másolat készítése a biztonsági mentéshez, majd szerkesztés egy szerkesztővel, például egy Visual Studio szerkesztéssel.

> [!NOTE]
> Ha nincs telepítve Visual Studio Code, töltse le és telepítse [itt.](https://code.visualstudio.com/Download)

Először exportáljuk a **stable-prometheus-server** konfigurációtérképet, majd készítsünk egy másolatot a biztonsági mentéshez.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Most nyissa meg a fájlt az Visual Studio kód használatával a szerkesztéshez.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Miután megnyitotta a konfigurációtérképet a Visual Studio Code szerkesztőben, cserélje le a prometheus.yml fájlt az alábbi OSM-konfigurációra, és mentse a fájlt.

> [!WARNING]
> Rendkívül fontos, hogy megtartsa a yaml-fájl behúzási szerkezetét. A yaml-fájlstruktúra bármilyen változtatása azt eredményezheti, hogy a configmap nem alkalmazható újra.

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

Alkalmazza a frissített configmap yaml-fájlt a következő paranccsal.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Előfordulhat, hogy üzenetet kap egy hiányzó kubernetes-jegyzetről. Ez jelenleg figyelmen kívül hagyható.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Ellenőrizze, hogy a Prometheus konfigurálva van-e az OSM-háló és az API-végpontok összecsomópontozásához

Annak ellenőrzéséhez, hogy a Prometheus megfelelően van-e konfigurálva az OSM-háló és az API-végpontok összecsukása érdekében, portot továbbítunk a Prometheus podra, és megtekintjük a célkonfigurációt. Futtassa az alábbi parancsokat.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Nyisson meg egy böngészőt a `http://localhost:9090/targets`

Ha lefelé görget, láthatja, hogy az összes SMI-metrikavégpont **állapota UP,** valamint az alább látható egyéb OSM-metrikáknak.

![Az OSM Prometheus célmetrikák felhasználói felületének képe](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Grafana-példány üzembe helyezése és konfigurálása OSM-hez

A Grafana-példányt a Helm használatával fogjuk üzembe helyezni. Futtassa az alábbi parancsokat a Grafana Helmen keresztüli telepítéséhez:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

A következő lépés az alapértelmezett Grafana-jelszó lekérése a Grafana-webhelyre való bejelentkezéshez.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Jegyezze fel a Grafana-jelszót.

A következő lépés a Grafana-pod lekérése a Grafana-irányítópultra való port továbbítása a bejelentkezéshez.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Nyisson meg egy böngészőt a `http://localhost:3000`

Az alábbi bejelentkezési képernyőn adja meg az **admin** felhasználónevet, és használja a korábban rögzített Grafana-jelszót.

![Az OSM Grafana bejelentkezési oldal felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>A Grafana Prometheus adatforrás konfigurálása

Miután sikeresen bejelentkezett a Grafanába, a következő lépés a Prometheus adatforrásként való hozzáadása a Grafana számára. Lépjen a bal oldali menü konfigurációs ikonjára, és válassza az Adatforrások lehetőséget az alább látható módon.

![Az OSM Grafana-adatforrások lap felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Kattintson az **Adatforrás hozzáadása gombra,** és válassza a Prometheus lehetőséget az idősorozat-adatbázisok alatt.

![Kép: OSM Grafana Datasources Selection Page UI (OSM Grafana-adatforrások kijelölése lap felhasználói felületének képe)](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Az alábbi **A Prometheus-adatforrás konfigurálása** lapon adja meg a Kubernetes-fürt FQDN-jét a Prometheus szolgáltatáshoz a HTTP URL-cím beállításhoz. Az alapértelmezett FQDN-nek a következőnek kell lennie: `stable-prometheus-server.default.svc.cluster.local` . Miután megadta a Prometheus szolgáltatásvégpontot, görgessen az oldal aljára, és válassza a Save & Test (Teszt **mentése) lehetőséget.** Egy zöld jelölőnégyzetet kell kapnia, amely jelzi, hogy az adatforrás működik.

#### <a name="importing-osm-dashboards"></a>OSM-irányítópultok importálása

Az OSM-irányítópultok a következőn keresztül érhetők el:

- [A és a adattár](https://github.com/grafana/grafana)json-blobként importálható a webes felügyeleti portálon keresztül
- vagy [online a Grafana.com](https://grafana.com/grafana/dashboards/14145)

Irányítópult importálásához keresse meg a jelet a bal oldali `+` menüben, és válassza a `import` lehetőséget.
Az irányítópultot közvetlenül importálhatja az azonosítójuk alapján a `Grafana.com` következőn: . Az irányítópultunk például a `OSM Mesh Details` azonosítót használja, használhatja közvetlenül az űrlapon az azonosítót, és kiválaszthatja a `14145` `import` lehetőséget:

![Az OSM Grafana irányítópultjának importálási oldal felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Az importálás kiválasztása után a rendszer automatikusan az importált irányítópultra lépteti.

![Az OSM Grafana-irányítópult háló részletei oldal felhasználói felületének képe](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Jaeger-operátor üzembe helyezése és konfigurálása a Kubernetesben OSM-hez

[A Jaeger](https://www.jaegertracing.io/) egy nyílt forráskódú nyomkövetési rendszer, amely elosztott rendszerek monitorozását és hibaelhárítását használja. Az OSM-ben új példányként is üzembe helyezhető, de saját példány is lehet. Az alábbi utasítások a Jaeger új példányát telepítik az `jaeger` AKS-fürt névterébe.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>A Jaeger üzembe helyezése az AKS-fürtön

Alkalmazza a következő jegyzékfájlt a Jaeger telepítéséhez:

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

#### <a name="enable-tracing-for-the-osm-add-on"></a>Nyomkövetés engedélyezése az OSM bővítményhez

A következő lépés az OSM-bővítmény nyomkövetésének engedélyezése.

> [!NOTE]
> A nyomkövetési tulajdonságok jelenleg nem láthatóak az osm-config konfigurációs térképen. Ez látható lesz az OSM AKS-bővítmény új kiadásában.

Futtassa a következő parancsot az OSM-bővítmény nyomkövetésének engedélyezéséhez:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>A Jaeger felhasználói felületének megtekintése portirányítással

A Jaeger felhasználói felülete az 16686-os porton fut. A webes felhasználói felület megtekintéséhez használhatja a kubectl port-forward használhatja:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

A böngészőben látnia kell egy Szolgáltatás legördülő menüt, amely lehetővé teszi, hogy a könyváruház bemutatója által üzembe helyezett különböző alkalmazások közül válasszon. Válasszon ki egy szolgáltatást az összes rátét megtekintéséhez. Ha például a bookbuyer egyórás Lookbacket választja, a bookstore-v1 és a bookstore-v2 interakcióit idő szerint rendezve láthatja.

![Az OSM Jaeger nyomkövetési oldal felhasználói felületének képe](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Válasszon ki egy elemet a további részletek megtekintéséhez. Jelöljön ki több elemet a nyomkövetések összehasonlításhoz. Összehasonlíthatja például a bookbuyer és a bookstore-v2 interakcióit egy adott időpontban.

A Rendszerarchitektúra lapon megtekintheti a különböző alkalmazások közötti interakciók/kommunikáció grafikonját. Ez áttekintést nyújt arról, hogyan áramlik a forgalom az alkalmazások között.

![Az OSM Jaeger rendszerarchitektúra felhasználói felületének képe](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Az Open Service Mesh (OSM) AKS bővítmény hibaelhárítási útmutatói

Az OSM AKS-bővítmény üzembe helyezésekor időnként problémákat tapasztalhat. Az alábbi útmutatók segítséget nyújtanak a hibák elhárításához és a gyakori problémák megoldásához.

### <a name="verifying-and-troubleshooting-osm-components"></a>OSM-összetevők ellenőrzése és hibaelhárítása

#### <a name="check-osm-controller-deployment"></a>Az OSM-vezérlő üzembe helyezésének ellenőrzése

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Egy kifogástalan állapotú OSM-vezérlő így nézne ki:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Ellenőrizze az OSM-vezérlő podot

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Egy kifogástalan állapotú OSM-pod így néz ki:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Annak ellenére, hogy egy bizonyos ponton egy vezérlőt ki is érvénybe léptünk, van egy másik, READY 1/1 és Running 0 újraindítással. Ha a READY oszlop nem 1/1, akkor a szolgáltatási háló hibás állapotban lenne.
A READY oszlop 0/1 értékekkel azt jelzi, hogy a vezérlősík tárolója összeomlik – le kell szereznünk a naplókat. Lásd az OSM Controller Logs from Azure ügyfélszolgálata Center (OSM-vezérlő naplóinak le Azure ügyfélszolgálata Centerből) című szakaszt. A READY oszlop 1-esnél nagyobb számmal a / után azt jelezné, hogy oldalkocsik vannak telepítve. Az OSM-vezérlő valószínűleg nem működik a csatlakoztatott oldalkocsival.

> [!NOTE]
> A 0.8.2-es verziótól az OSM-vezérlő nem HA módban van, és egy üzembe helyezett, 1 podos replikaszámmal fut. A pod állapot-mintavételekkel rendelkezik, és szükség esetén a kubelet újraindítja.

#### <a name="check-osm-controller-service"></a>Az OSM-vezérlőszolgáltatás ellenőrzése

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Egy kifogástalan állapotú OSM Controller szolgáltatás a következőhöz hasonló:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> A CLUSTER-IP eltérő lenne. A szolgáltatás NEVÉnek és PORT(ának) meg kell egynie a fenti példával.

#### <a name="check-osm-controller-endpoints"></a>Az OSM-vezérlő végpontjainak ellenőrzése

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Egy megfelelő állapotú OSM-vezérlő végpontja(i) a következőhöz hasonlók:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Az OSM-injektor üzembe helyezésének ellenőrzése

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Egy megfelelő állapotú OSM-injektor üzembe helyezése a következő lenne:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Ellenőrizze az OSM injektorpodot

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Egy megfelelő állapotú OSM Injektor-pod így néz ki:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Az OSM Injektor szolgáltatás ellenőrzése

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Egy megfelelő állapotú OSM Injector-szolgáltatás a következő lenne:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>OSM-végpontok ellenőrzése

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Egy kifogástalan állapotú OSM-végpont így néz ki:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Webhookok ellenőrzése és mutatása

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Egy megfelelő állapotú OSM-érvényesítő webhook a következő lenne:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Egy megfelelő állapotú OSM-alapú muting webhook a következő lenne:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Ellenőrizze a szolgáltatást és az érvényesítő webhook hitelesítésszolgáltatói csomagját

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Egy jól konfigurált Webhook-konfiguráció érvényesség-érvényesítő konfigurációja pontosan így nézne ki:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Ellenőrizze a szolgáltatás és a mutating webhook hitelesítésszolgáltatói csomagját

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Egy jól konfigurált Mutating Webhook-konfiguráció pontosan így nézne ki:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Ellenőrizze, hogy az OSM-vezérlő hitelesítésszolgáltatói csomaggal adta-e meg az érvényesítő (vagy a mutating) webhookot

> [!NOTE]
> A 0.8.2-es verziótól fontos tudni, hogy az AKS RP telepíti az érvényesítő webhookot, az AKS Reconciler ellenőrzi, hogy létezik-e, de a CA-csomagot az OSM Controller tölti ki.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Ez a szám a bájtok számát vagy a CA csomag méretét jelzi. Ha ez üres, 0 vagy egy 1000 alatti szám, az azt jelezné, hogy a CA-csomag nincs megfelelően kiépítve. Megfelelő hitelesítésszolgáltatói csomag nélkül az érvényesítő webhook hibát jelezne, és megtiltja, hogy a felhasználó módosítsa a kube-system névtérben található osm-config ConfigMap kódot.

Példahiba, ha a HITELESÍTÉSszolgáltatói csomag helytelen:

- Kísérlet az osm-config ConfigMap módosítása:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Hiba:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

A következőt  kell megtenné, ha a webhook-konfiguráció érvényesítő tanúsítványa hibás:

- 1. lehetőség – Az OSM-vezérlő újraindítása – ez újraindítja az OSM-vezérlőt. A kezdéshez felülírja a ca csomagot a webhookok mutációja és érvényesség-ének stb.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- 2. lehetőség – 2. lehetőség. Törölje az érvényesítő webhookot – az érvényesítő webhook eltávolítása miatt a ConfigMap-leképezés mutatációi `osm-config` már nem érvényesítve vannak. Minden javítás végig fog menni. Az AKS Reconciler egy ponton gondoskodik arról, hogy létezik az érvényesítő webhook, és újra létrehozza. Előfordulhat, hogy az OSM-vezérlőt újra kell indítani a CA-csomag gyors újraírásához.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- 3. lehetőség – Törlés és javítás: A következő parancs törli az érvényesítő webhookot, így bármilyen értéket hozzáadhat, és azonnal megpróbál javítást alkalmazni. Valószínű, hogy az AKS Reconcilernek nincs elég ideje az érvényesítő webhook egyeztetésre és visszaállításra, így lehetőség nyílik arra, hogy végső megoldásként alkalmazza a változást:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>A `osm-config` **ConfigMap ellenőrzése**

> [!NOTE]
> Az OSM-vezérlő nem igényli, hogy a ConfigMap jelen legyen `osm-config` a kube-system névtérben. A vezérlő ésszerű alapértelmezett értékekkel rendelkezik a konfigurációhoz, és anélkül is működhet.

A meglétének ellenőrzése:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Ellenőrizze az osm-config ConfigMap tartalmát

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
| bejövő forgalom                           | logikai   | true, false                                             | `"false"`                              | Engedélyezi a bejövő forgalom a hálóban.                                                                                                                                                                                                             |
| enable_debug_server              | logikai   | true, false                                             | `"true"`                               | Engedélyezi a hibakeresési végpontot az osm-controller podon a hálóval kapcsolatos információk, például a proxykapcsolatok, a tanúsítványok és az SMI-házirendek felsorolásához.                                                                                    |
| enable_privileged_init_container | logikai   | true, false                                             | `"false"`                              | Engedélyezi az emelt szintű init tárolókat a hálóban található podok számára. Ha hamis, az init tárolók csak a NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | sztring | trace, debug, info, warning, warn, error, critical, off | `"error"`                              | Beállítja az Envoy proxy sidecar naplózási részletességét, amely csak a hálót összekötő újonnan létrehozott podok esetén alkalmazható. A meglévő podok naplózási szintjének frissítéséhez indítsa újra az üzembe helyezést a `kubectl rollout restart` következővel: .                            |
| outbound_ip_range_exclusion_list | sztring | Az a.b.c.d/x típusú IP-tartományok vesszővel elválasztott listája | `-`                                    | Azon IP-címtartományok globális listája, amelyek kizárják az oldalkocsiproxy által elfogt kimenő forgalomból.                                                                                                                                    |
| permissive_traffic_policy_mode   | logikai   | true, false                                             | `"false"`                              | A beállítása lehetővé teszi az összes engedélyezése módot a hálóban, azaz nem kényszeríti ki a hálóban a forgalom `true` szabályzatát. Ha a beállítása , akkor engedélyezi az összes forgalom megtagadási szabályzatát a hálóban, vagyis a a szolgáltatások kommunikációja érdekében `false` `SMI Traffic Target` szükséges. |
| prometheus_scraping              | logikai   | true, false (igaz, hamis)                                             | `"true"`                               | Lehetővé teszi a Prometheus-metrikák oldalkocsi proxykon való leselkedést.                                                                                                                                                                                 |
| service_cert_validity_duration   | sztring | 24 óra, 1h30m (bármely időtartam)                          | `"24h"`                                | Beállítja a szolgáltatás tanúsítványának érvényességi időtartamát, amelyet tizedes törtszámok sorozatával és opcionális törtszámmal és egy egység utótaggal ábrázol.                                                                                             |
| tracing_enable                   | logikai   | true, false (igaz, hamis)                                             | `"false"`                              | Engedélyezi a Jaeger-nyomkövetést a hálóhoz.                                                                                                                                                                                                    |
| tracing_address                  | sztring | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | A Jaeger üzemelő példányának címe, ha a nyomkövetés engedélyezve van.                                                                                                                                                                                |
| tracing_endpoint                 | sztring | /api/v2/spans                                           | /api/v2/spans                          | Az adatok nyomkövetésének végpontja, ha engedélyezve van a nyomkövetés.                                                                                                                                                                                          |
| tracing_port                     | int    | bármely nem nulla egész szám                              | `"9411"`                               | Az a port, amelyen a nyomkövetés engedélyezve van.                                                                                                                                                                                                       |
| use_https_ingress                | logikai   | true, false                                             | `"false"`                              | Engedélyezi a HTTPS-bejövő forgalom használatát a hálón.                                                                                                                                                                                                      |
| config_resync_interval           | sztring | 1 perc alatt letiltja ezt                            | 0 (letiltva)                           | Ha egy 1m (60-as) feletti érték van megadva, az OSM-vezérlő az összes rendelkezésre álló konfigurációt elküldi az egyes csatlakoztatott követnek a megadott időközönként                                                                                                    |

#### <a name="check-namespaces"></a>Névterek ellenőrzése

> [!NOTE]
> A kube-system névtér soha nem vesz részt a szolgáltatási hálóban, és soha nem lesz címkézve és/vagy jegyzetekkel megcímkézve az alábbi kulccsal/értékekkel.

A parancs `osm namespace add` használatával a névtereket egy adott szolgáltatási hálóhoz illesztjük.
Ha egy k8s-névtér a háló része (vagy ahhoz, hogy a háló része legyen), az alábbiaknak igaznak kell lennie:

A jegyzetek megtekintése a következővel:

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

A következő jegyzetnek kell jelen lennie:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Címkék megtekintése a következővel:

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

A következő címkének kell jelen lennie:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Ha egy névtér nincs jegyzetekkel címkézve az OSM Injektorral, vagy nincs megcímkézve, akkor a `"openservicemesh.io/sidecar-injection": "enabled"` `"openservicemesh.io/monitored-by": "osm"` Envoy oldalkocsik nem lesznek hozzáadva.

> Megjegyzés: Az után csak az új podok lesznek injektálva egy `osm namespace add` envoy  sidecarba. A meglévő podokat újra kell indítani a `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Ellenőrizze az SMI CRD-ket:

Ellenőrizze, hogy a fürt rendelkezik-e a szükséges CRD-ekkel:

```azurecli-interactive
kubectl get crds
```

A fürtön a következőknek kell telepítve lennie:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

A következő paranccsal szerezze be a telepített CRD-verziókat:

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

Az OSM-vezérlő 0.8.2-es verziójához a következő verziókra van szükség:

- traffictargets.access.smi-spec.io – [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io – Nem támogatott
- trafficsplits.split.smi-spec.io – [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io – [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Ha hiányoznak CRD-k, a következő parancsokkal telepítheti őket a fürtön:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Az Open Service Mesh- (OSM-) bővítmény letiltása az AKS-fürthöz

Az OSM-bővítmény letiltásához futtassa a következő parancsot:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
