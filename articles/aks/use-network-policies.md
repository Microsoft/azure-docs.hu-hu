---
title: Biztonságos hüvelyek hálózati házirendekkel az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan védheti meg és ki a hüvelyeken kívülre áramló forgalmat az Azure Kubernetes Service (ak) Kubernetes hálózati házirendjeinek használatával
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 92e726529f2c81b169dc5ad485148ad8118bbc81
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592866"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Biztonságos forgalom a hüvelyek között hálózati házirendek használatával az Azure Kubernetes szolgáltatásban (ak)

Ha modern, Kubernetes-alapú alkalmazásokat futtat a-ben, gyakran szeretné szabályozni, hogy mely összetevők kommunikáljanak egymással. A legalacsonyabb jogosultsági szint elvét arra kell alkalmazni, hogy a forgalom hogyan válthat a hüvelyek között egy Azure Kubernetes-szolgáltatásbeli (ak-beli) fürtben. Tegyük fel, hogy valószínűleg le szeretné tiltani a forgalmat közvetlenül a háttérbeli alkalmazásokhoz. A Kubernetes *hálózati házirend* funkciója lehetővé teszi szabályok definiálását a fürtben található hüvelyek közötti bejövő és kimenő forgalomhoz.

Ez a cikk bemutatja, hogyan telepítheti a hálózati házirend-motort, és hogyan hozhat létre Kubernetes hálózati házirendeket a hüvelyek közötti adatforgalom vezérléséhez az AK-ban. A hálózati házirendet csak a Linux-alapú csomópontok és a hüvelyek esetében kell használni az AK-ban.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.61 vagy újabb verziójára, és konfigurálva van. A verzió megkereséséhez futtassa a `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

> [!TIP]
> Ha az előzetes verzióban használta a hálózati házirend szolgáltatást, azt javasoljuk, hogy [hozzon létre egy új fürtöt](#create-an-aks-cluster-and-enable-network-policy).
> 
> Ha továbbra is használni szeretné a hálózati házirendet használó meglévő tesztelési fürtöket az előzetes verzióban, frissítse a fürtöt a legújabb GA-kiadás új Kubernetes-verziójára, majd telepítse a következő YAML-jegyzéket az összeomlási metrikák kiszolgálójának és Kubernetes kijavításához. műszerfal. Ez a javítás csak a tarka hálózati házirend-motort használó fürtök esetében szükséges.
>
> Ajánlott biztonsági eljárásként [tekintse át ennek a YAML-jegyzéknek a tartalmát][calico-aks-cleanup] annak megismeréséhez, hogy mi történik az AK-fürtön való üzembe helyezéssel.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Hálózati házirend áttekintése

Az AK-fürtök összes hüvelye korlátozás nélkül képes továbbítani és fogadni a forgalmat, alapértelmezés szerint. A biztonság növelése érdekében megadhat olyan szabályokat, amelyek vezérlik a forgalom áramlását. A háttérbeli alkalmazások gyakran csak a szükséges előtér-szolgáltatásokra vannak kitéve, például:. Vagy az adatbázis-összetevők csak a hozzájuk kapcsolódó alkalmazási rétegek számára érhetők el.

A hálózati házirend egy Kubernetes-specifikáció, amely a hüvelyek közötti kommunikáció hozzáférési szabályzatait határozza meg. A hálózati házirendek segítségével megadhatja a megrendelt szabályokat a forgalom küldéséhez és fogadásához, és alkalmazhatja azokat olyan hüvelyek gyűjteményére, amelyek egy vagy több címke-választónak felelnek meg.

Ezek a hálózati házirend-szabályok YAML-jegyzékként vannak meghatározva. A hálózati házirendeket egy szélesebb körű jegyzékfájl részeként lehet felvenni, amely egy központi telepítést vagy szolgáltatást is létrehoz.

### <a name="network-policy-options-in-aks"></a>Hálózati házirend-beállítások az AK-ban

Az Azure két módszert biztosít a hálózati házirend megvalósítására. Ha AK-fürtöt hoz létre, válasszon hálózati házirend-beállítást. A fürt létrehozása után a házirend-beállítás nem módosítható:

* Az Azure saját implementációja, az *Azure hálózati szabályzatok*.
* *Tarka hálózati házirendek*, a [tigera][tigera]által alapított nyílt forráskódú hálózati és hálózati biztonsági megoldás.

Mindkét implementáció Linux *iptables* -t használ a megadott házirendek betartatására. A szabályzatok az engedélyezett és a nem engedélyezett IP-párok készletében vannak lefordítva. Ezeket a párokat ezután az iptables-szűrési szabályokként kell programozni.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Az Azure-és a tarka-szabályzatok és azok képességei közötti különbségek

| Képesség                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Támogatott platformok                      | Linux                      | Linux                       |
| Támogatott hálózati beállítások             | Azure CNI                  | Azure CNI és kubenet       |
| Megfelelőség a Kubernetes-specifikációval | Minden támogatott házirend-típus |  Minden támogatott házirend-típus |
| További funkciók                      | Nincs                       | Kiterjesztett házirend-modell, amely a globális hálózati házirendből, a globális hálózati készletből és a gazdagép végpontból áll. A kibővített funkciók kezeléséhez a `calicoctl` CLI használatával kapcsolatos további információkért lásd: [calicoctl felhasználói referenciája][calicoctl]. |
| Támogatás                                  | Az Azure-támogatás és a mérnöki csapat támogatja | A tarka közösségi támogatás. A további fizetős támogatással kapcsolatos további információkért lásd a [Project tarka támogatási lehetőségeit][calico-support]. |
| Naplózás                                  | Az iptables-ben hozzáadott vagy törölt szabályok minden gazdagépen bejelentkezve vannak a */var/log/Azure-NPM.log* alá | További információ: a [tarka összetevő naplói][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AK-fürt létrehozása és hálózati házirend engedélyezése

Ha működés közben szeretné megtekinteni a hálózati házirendeket, hozzon létre, majd terjesszen ki egy olyan szabályzatot, amely meghatározza a forgalmi folyamatot:

* A pod-ra irányuló összes forgalom megtagadása.
* Adatforgalom engedélyezése a pod-címkék alapján.
* Adatforgalom engedélyezése a névtér alapján.

Először hozzon létre egy AK-fürtöt, amely támogatja a hálózati házirendet. 

> [!IMPORTANT]
>
> A hálózati házirend szolgáltatás csak akkor engedélyezhető, ha a fürt létrejött. A hálózati házirend nem engedélyezhető egy meglévő AK-fürtön.

Az Azure hálózati házirendjének használatához az [Azure CNI beépülő modult][azure-cni] kell használnia, és meg kell határoznia a saját virtuális hálózatát és alhálózatait. A szükséges alhálózati tartományok megtervezésével kapcsolatos részletes információkért lásd: [speciális hálózatkezelés konfigurálása][use-advanced-networking]. A tarka hálózati házirend ugyanazzal az Azure CNI beépülő modullal vagy a Kubenet CNI beépülő modullal is használható.

A következő példa szkriptet:

* Egy virtuális hálózatot és alhálózatot hoz létre.
* Létrehoz egy Azure Active Directory (Azure AD) szolgáltatásnevet az AK-fürthöz való használatra.
* *Közreműködői* engedélyeket rendel a virtuális hálózaton található AK-fürtszolgáltatási egyszerű szolgáltatáshoz.
* Létrehoz egy AK-fürtöt a megadott virtuális hálózatban, és engedélyezi a hálózati házirendet.
    * Az *Azure* hálózati házirend-beállítás használatos. Ha ehelyett hálózati házirendként szeretné használni a Tarkat, használja a `--network-policy calico` paramétert. Megjegyzés: a tarka felhasználható `--network-plugin azure` vagy `--network-plugin kubenet`.

Saját biztonságos *SP_PASSWORD*megadása. A *RESOURCE_GROUP_NAME* és az *CLUSTER_NAME* változót is lecserélheti:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

A fürt létrehozása néhány percet vesz igénybe. Ha a fürt elkészült, konfigurálja `kubectl` a Kubernetes-fürthöz való csatlakozáshoz az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Minden bejövő forgalom elutasítása egy Pod-ra

Mielőtt meghatározott hálózati forgalmat engedélyező szabályokat definiál, először hozzon létre egy hálózati házirendet az összes forgalom elutasításához. Ez a házirend egy kiindulási pontot biztosít, amellyel megkezdheti a csak a kívánt forgalom engedélyezési listáját. Azt is láthatja, hogy a hálózati házirend alkalmazása esetén a rendszer eldobta a forgalmat.

A minta alkalmazási környezet és a forgalmi szabályok esetében először hozzon létre egy *fejlesztés* nevű névteret a példa hüvelyek futtatásához:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Hozzon létre egy példát az NGINX-t futtató háttér-Pod-ra. Ez a háttér-pod egy minta háttérbeli webes alkalmazás szimulálására használható. Hozza létre ezt a pod-t a *fejlesztői* névtérben, és nyissa meg a *80* -es portot a webes forgalom kiszolgálásához. Az *app = WebApp, role = backend* címkével megcímkézheti a pod-t, hogy a következő szakaszban egy hálózati házirenddel lehessen megcélozni:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Hozzon létre egy másik hüvelyt, és csatoljon egy terminál-munkamenetet annak teszteléséhez, hogy sikeresen elérheti az alapértelmezett NGINX-weblapot:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorába a `wget` használatával ellenőrizze, hogy elérhető-e az alapértelmezett NGINX-weblap:

```console
wget -qO- http://backend
```

A következő minta kimenet azt mutatja, hogy az alapértelmezett NGINX-weblap visszaadott:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminál-munkamenetből. A teszt Pod automatikusan törlődik.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Hálózati házirend létrehozása és alkalmazása

Most, hogy megerősítettük, használhatja az alapszintű NGINX-weblapot a minta háttér Pod-on, hozzon létre egy hálózati házirendet az összes forgalom elutasításához. Hozzon létre egy `backend-policy.yaml` nevű fájlt, és illessze be a következő YAML-jegyzékbe. Ez a jegyzékfájl egy *podSelector* használatával csatolja a szabályzatot a következő alkalmazással rendelkező hüvelyekhez *: WebApp, szerepkör: háttér* felirat, például a minta NGINX Pod. Nincsenek szabályok meghatározva a *bejövő forgalomban, így*a pod-ra irányuló bejövő forgalom megtagadva:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Alkalmazza a hálózati házirendet az [kubectl Apply][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>A hálózati házirend tesztelése


Nézzük meg, hogy újra használhatja-e az NGINX-weblapot a háttérben futó Pod-on. Hozzon létre egy másik teszt Pod és csatoljon egy terminál-munkamenetet:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorába `wget` használatával ellenőrizze, hogy elérhető-e az alapértelmezett NGINX-weblap. Ezúttal az időtúllépési értéket állítsa *2* másodpercre. A hálózati házirend mostantól blokkolja az összes bejövő forgalmat, így a lap nem tölthető be, ahogy az alábbi példában is látható:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Kilépés a csatlakoztatott terminál-munkamenetből. A teszt Pod automatikusan törlődik.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Bejövő forgalom engedélyezése Pod-címke alapján

Az előző szakaszban egy háttérbeli NGINX Pod volt ütemezve, és egy hálózati házirend lett létrehozva, hogy megtagadja az összes forgalmat. Hozzon létre egy előtér-hüvelyt, és frissítse a hálózati házirendet, hogy engedélyezze az előtér-hüvelyek forgalmát.

Frissítse a hálózati házirendet, hogy engedélyezzék a hüvelyek forgalmát a következő címkékkel *: WebApp, szerepkör: frontend* és bármely névtérben. Szerkessze az előző *háttér-Policy. YAML* fájlt, és adja hozzá a *matchLabels* bejövő szabályokhoz, hogy a jegyzékfájl a következő példához hasonlóan néz ki:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Ez a hálózati házirend egy *namespaceSelector* és egy *podSelector* elemet használ a bejövő szabályokhoz. A YAML szintaxisa fontos, hogy a bejövő szabályok adalékanyagnak legyenek. Ebben a példában mindkét elemnek egyeznie kell az alkalmazni kívánt bejövő szabályokkal. Előfordulhat, hogy a *1,12* előtti Kubernetes-verziók nem értelmezik megfelelően ezeket az elemeket, és a várt módon korlátozzák a hálózati forgalmat. További információ erről a viselkedésről: a [és a választóinak viselkedése][policy-rules].

Alkalmazza a frissített hálózati házirendet az [kubectl Apply][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Egy *app = WebApp, role = frontend* címkével ellátott Pod-t ütemezhet, és csatlakoztathat egy terminál-munkamenetet:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorába `wget` használatával ellenőrizze, hogy elérhető-e az alapértelmezett NGINX-weblap:

```console
wget -qO- http://backend
```

Mivel a beáramlási szabály engedélyezi a forgalmat a címkék alkalmazást használó hüvelyek esetében *: WebApp, szerepkör: frontend*, az előtér-hüvelyből érkező forgalom engedélyezett. A következő példa kimenete a visszaadott alapértelmezett NGINX-weblapot mutatja:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminál-munkamenetből. A pod automatikusan törlődik.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>A pod tesztelése a megfelelő címke nélkül

A hálózati házirend lehetővé teszi, hogy a hüvelyek által címkézett *alkalmazás: WebApp, szerepkör: frontend*, de minden más forgalmat megtagadjon. Vizsgáljuk meg, hogy a címkék nélkül egy másik Pod hozzáfér-e a háttér NGINX Pod-hoz. Hozzon létre egy másik teszt Pod és csatoljon egy terminál-munkamenetet:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorába `wget` használatával ellenőrizze, hogy elérhető-e az alapértelmezett NGINX-weblap. A hálózati házirend blokkolja a bejövő forgalmat, így a lap nem tölthető be, ahogy az alábbi példában is látható:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Kilépés a csatlakoztatott terminál-munkamenetből. A teszt Pod automatikusan törlődik.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Csak meghatározott névtéren belüli forgalom engedélyezése

Az előző példákban létrehozott egy hálózati házirendet, amely megtagadta az összes forgalmat, majd frissítette a szabályzatot, hogy engedélyezzék a hüvelyek forgalmát egy adott címkével. Egy másik gyakori igény, hogy csak egy adott névtéren belül korlátozza a forgalmat. Ha az előző példák egy *fejlesztési* névtér forgalmára irányulnak, hozzon létre egy hálózati házirendet, amely megakadályozza, hogy egy másik névtérből, például a *termelésből*érkező forgalom a hüvelyek eljussanak.

Először hozzon létre egy új névteret egy éles névtér szimulálásához:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Egy teszt Pod-t az *app = WebApp, role = frontend*néven jelölt *üzemi* névtérben ütemezhet. Terminál-munkamenet csatolása:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

A rendszerhéj parancssorába a `wget` használatával ellenőrizze, hogy elérhető-e az alapértelmezett NGINX-weblap:

```console
wget -qO- http://backend.development
```

Mivel a pod címkéi megegyeznek a hálózati házirendben jelenleg engedélyezett értékekkel, a forgalom engedélyezett. A hálózati házirend nem tekinti meg a névtereket, csak a pod-címkéket. A következő példa kimenete a visszaadott alapértelmezett NGINX-weblapot mutatja:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminál-munkamenetből. A teszt Pod automatikusan törlődik.

```console
exit
```

### <a name="update-the-network-policy"></a>A hálózati házirend frissítése

Frissítse a bejövő szabályok *namespaceSelector* szakaszát, hogy csak a *fejlesztési* névtéren belülről engedélyezze a forgalmat. Szerkessze a *háttér-Policy. YAML* jegyzékfájlt az alábbi példában látható módon:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Összetettebb példákban több bejövő szabályt is meghatározhat, például egy *namespaceSelector* , majd egy *podSelector*.

Alkalmazza a frissített hálózati házirendet az [kubectl Apply][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>A frissített hálózati házirend tesztelése

Egy másik Pod-t ütemezhet az *üzemi* névtérben, és csatlakoztathat egy terminál-munkamenetet:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

A rendszerhéj parancssorába `wget` használatával láthatja, hogy a hálózati házirend most megtagadja a forgalmat:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Kilépés a test podből:

```console
exit
```

Az *üzemi* névtértől megtagadott forgalom esetén a rendszer visszairányítja a tesztelési Pod-t a *fejlesztési* névtérbe, és csatolja a terminál-munkamenetet:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorában a `wget` használatával ellenőrizze, hogy a hálózati házirend engedélyezi-e a forgalmat:

```console
wget -qO- http://backend
```

A forgalom engedélyezett, mert a pod olyan névtérben van ütemezve, amely megfelel a hálózati házirendben engedélyezett értéknek. A következő minta kimenet a visszaadott alapértelmezett NGINX-weblapot mutatja:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminál-munkamenetből. A teszt Pod automatikusan törlődik.

```console
exit
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben két névteret hoztunk létre, és alkalmazunk egy hálózati házirendet. Az erőforrások [törléséhez használja a kubectl delete][kubectl-delete] parancsot, és határozza meg az erőforrások nevét:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Következő lépések

További információ a hálózati erőforrásokról: az [Azure Kubernetes Service-ben (ak) futó alkalmazások hálózati fogalmai][concepts-network].

A szabályzatokkal kapcsolatos további információkért lásd: [Kubernetes hálózati házirendek][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
