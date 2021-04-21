---
title: Podforgalom biztonságossá biztosítása hálózati szabályzattal
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan biztosíthatja a podok be- és kifelé áramlik forgalmát Kubernetes hálózati szabályzatok használatával az Azure Kubernetes Service (AKS) szolgáltatásban
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: b05c4add0a62f07b187376d670f23179ba97f3a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767438"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>A podok közötti adatforgalom biztonságossá tere hálózati szabályzatok használatával Azure Kubernetes Service (AKS)

Ha modern, mikroszolgáltatás-alapú alkalmazásokat futtat a Kubernetesben, gyakran szeretné szabályozni, hogy mely összetevők kommunikálhatnak egymással. A legkisebb jogosultság elvének alkalmazása arra vonatkozik, hogy a forgalom hogyan áramlik egy Azure Kubernetes Service fürt podja között. Tegyük fel, hogy valószínűleg közvetlenül a háttéralkalmazások felé szeretné blokkolni a forgalmat. A  Kubernetes Hálózati házirend szolgáltatása lehetővé teszi a fürt podok közötti bejövő és bejövő forgalmára vonatkozó szabályok definiálást.

Ez a cikk bemutatja, hogyan telepítheti a hálózati házirend motorját, és hogyan hozhat létre Kubernetes-hálózati szabályzatokat az AKS-podok közötti forgalom szabályozásához. A hálózati szabályzatot csak Linux-alapú csomópontokhoz és podokhoz szabad használni az AKS-ban.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61-es vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

> [!TIP]
> Ha az előzetes verzióban a hálózati szabályzat funkciót használta, javasoljuk, hogy hozzon létre egy [új fürtöt.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Ha továbbra is használni szeretné az előzetes verzióban hálózati szabályzatot használó meglévő tesztfürtöt, frissítse a fürtöt a legújabb ga ga verziójú Kubernetes-verzióra, majd telepítse a következő YAML-jegyzékfájlt az összeomló metrikakiszolgáló és a Kubernetes irányítópultjának kijavítása érdekében. Ez a javítás csak a Calico hálózati házirendmotort használt fürtök esetén szükséges.
>
> Ajánlott biztonsági eljárásként [][calico-aks-cleanup] tekintse át ennek a YAML-jegyzékfájlnak a tartalmát, hogy megértse, mi van üzembe helyezni az AKS-fürtön.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>A hálózati szabályzat áttekintése

Alapértelmezés szerint az AKS-fürt összes podja korlátozás nélkül küldhet és fogadhat forgalmat. A biztonság növelése érdekében szabályokat határozhat meg a forgalom szabályozására. A háttéralkalmazások gyakran csak a szükséges előtere-szolgáltatások számára vannak elérhetővé téve, például. Vagy az adatbázis-összetevők csak a hozzájuk kapcsoló alkalmazásrétegek számára érhetők el.

A Hálózati szabályzat egy Kubernetes-specifikáció, amely hozzáférési szabályzatokat határoz meg a podok közötti kommunikációhoz. A Hálózati szabályzatok használatával egy rendezett szabálykészletet határozhat meg a forgalom küldését és fogadását, és alkalmazhatja azokat egy vagy több címkeválasztóval egyező podgyűjteményre.

Ezek a hálózatiszabály-szabályok YAML-jegyzékként vannak definiálva. A hálózati házirendek egy szélesebb jegyzékfájl részeként is szerepeltetve, amely egyben üzembe helyezést vagy szolgáltatást is létrehoz.

### <a name="network-policy-options-in-aks"></a>Hálózati szabályzat beállításai az AKS-ban

Az Azure két módszert kínál a hálózati szabályzat megvalósításához. A hálózati szabályzatot az AKS-fürt létrehozásakor választhatja ki. A szabályzat beállítását a fürt létrehozása után nem lehet módosítani:

* Az Azure saját implementációja, az *Azure Hálózati szabályzatok.*
* *Calico Network Policies*, egy nyílt forráskódú hálózati és hálózati biztonsági megoldás, amelyet [a Microsoft ad meg.][tigera]

Mindkét implementáció Linux *IPTable-ket használ* a megadott szabályzatok kényszerítésében. A házirendek engedélyezett és nem engedélyezett IP-párok készleteibe vannak lefordítva. Ezek a párok ezután IPTable szűrőszabályokként programozhatóak.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Az Azure- és Calico-szabályzatok és képességeik közötti különbségek

| Képesség                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Támogatott platformok                      | Linux                      | Linux, Windows Server 2019 (előzetes verzió)  |
| Támogatott hálózati lehetőségek             | Azure CNI                  | Azure CNI (Windows Server 2019 és Linux) és a kubenet (Linux)  |
| Megfelelőség a Kubernetes-specifikációnak | Az összes támogatott szabályzattípus |  Az összes támogatott szabályzattípus |
| További funkciók                      | Nincsenek                       | Bővített házirendmodell, amely a globális hálózati házirendet, a globális hálózati készletet és a gazdagép végpontját tartalmazza. További információ a kiterjesztett funkciók cli használatával való `calicoctl` kezeléséhez: [calicoctl user reference (calicoctl felhasználói referencia).][calicoctl] |
| Támogatás                                  | A Azure-támogatás és mérnöki csapat által támogatott | Calico közösségi támogatás. A további fizetős támogatással kapcsolatos további információkért lásd [a Project Calico támogatási lehetőségeit.][calico-support] |
| Naplózás                                  | Az IPTable-ben hozzáadott/törölt szabályok minden gazdagépen a */var/log/azure-npm.log* fájlban vannak naplózva | További információ: [Calico component logs (A Calico összetevő naplói)][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS-fürt létrehozása és hálózati szabályzat engedélyezése

A hálózati szabályzatok megfelelő alkalmazásához hozzunk létre, majd bontsunk ki egy olyan szabályzatot, amely meghatározza a forgalom áramlását:

* Tiltsa le a podra való összes forgalmat.
* Forgalom engedélyezése podcímkék alapján.
* Forgalom engedélyezése névtér alapján.

Először hozzunk létre egy hálózati szabályzatot támogató AKS-fürtöt.

> [!IMPORTANT]
>
> A hálózati szabályzat funkció csak a fürt létrehozásakor engedélyezhető. Meglévő AKS-fürtön nem engedélyezhető hálózati szabályzat.

Az Azure Network Policy használata érdekében az Azure CNI [beépülő][azure-cni] modult kell használnia, és meg kell határoznia a saját virtuális hálózatát és alhálózatát. A szükséges alhálózati tartományok tervezésével kapcsolatos további információkért lásd: [Speciális hálózatépítés konfigurálása.][use-advanced-networking] A Calico Hálózati szabályzat ugyanezekkel a beépülő modulokkal Azure CNI a Kubenet CNI beépülő modulhoz is használható.

Az alábbi példaszkprogram:

* Egy virtuális hálózatot és alhálózatot hoz létre.
* Létrehoz egy Azure Active Directory (Azure AD) szolgáltatásnév az AKS-fürthöz való használatra.
* Közreműködői *engedélyeket* rendel az AKS-fürt szolgáltatásnévhez a virtuális hálózaton.
* Létrehoz egy AKS-fürtöt a meghatározott virtuális hálózaton, és engedélyezi a hálózati szabályzatot.
    * A _rendszer az Azure Hálózati_ szabályzat lehetőséget használja. Ha ehelyett Calico-t használna hálózati szabályzatként, használja a `--network-policy calico` paramétert. Megjegyzés: A Calico a vagy a segítségével `--network-plugin azure` is `--network-plugin kubenet` használható.

Vegye figyelembe, hogy szolgáltatásnév használata helyett felügyelt identitást is használhat az engedélyekhez. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

Adja meg saját biztonságos *SP_PASSWORD.* A változókat lecserélheti *RESOURCE_GROUP_NAME* és *CLUSTER_NAME* változókra:

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
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>AKS-fürt létrehozása Azure hálózati szabályzatok számára

Hozza létre az AKS-fürtöt, és adja meg a virtuális hálózatot, a szolgáltatásnév adatait és az *Azure-t* a hálózati beépülő modulhoz és a hálózati szabályzathoz.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

A fürt létrehozása néhány percet vesz igénybe. Amikor a fürt elkészült, konfigurálja a parancsot a Kubernetes-fürthöz való csatlakozásra `kubectl` [az az aks get-credentials paranccsal.][az-aks-get-credentials] Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét a használatukhoz:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>AKS-fürt létrehozása Calico hálózati szabályzatok számára

Hozza létre az AKS-fürtöt, és adja meg a virtuális hálózatot, a szolgáltatásnév adatait, az *Azure-t* a hálózati beépülő modulhoz és a *calico* adatokat a hálózati házirendhez. A *calico hálózati* szabályzatként való használata lehetővé teszi a Calico-hálózatépítést Linux- és Windows-csomópontkészletek esetén is.

Ha Windows-csomópontkészleteket tervez hozzáadni a fürthöz, adja hozzá a és paramétereket, amelyek megfelelnek a `windows-admin-username` `windows-admin-password` Windows Server [jelszókövetelményeinek.][windows-server-password] Ha a Calico-t Windows-csomópontkészletekkel is használnia kell, regisztrálnia kell a következőt `Microsoft.ContainerService/EnableAKSWindowsCalico` is: .

Regisztrálja `EnableAKSWindowsCalico` a funkciójelölőt [az az feature register paranccsal][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 A regisztrációs állapotot az az feature list paranccsal [ellenőrizheti:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> A Calico hálózati szabályzatok Windows-csomópontokkal való használata jelenleg a Kubernetes 1.20-as vagy újabb verzióját és a Calico 3.17.2-es verzióját használó új fürtökön érhető el, és Azure CNI igényel. A Calico-t engedélyező AKS-fürtök Windows-csomópontjaiban alapértelmezés szerint a Közvetlen kiszolgáló-visszatérés [(DSR)][dsr] is engedélyezve van.
>
> A Kizárólag a Kubernetes 1.20-at futtató Linux-csomópontkészleteket futtató fürtök esetén a Calico-verzió automatikusan frissül a 3.17.2-es verzióra.

A Windows-csomópontokkal való calico hálózati szabályzatok jelenleg előzetes verzióban állnak rendelkezésre.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Hozzon létre egy felhasználónevet, amely rendszergazdai hitelesítő adatokként szolgál a fürtön található Windows Server-tárolókhoz. Az alábbi parancsok egy felhasználónevet kérnek, és WINDOWS_USERNAME egy későbbi parancsban való használatra beállítják (ne feledje, hogy a cikkben megadott parancsok egy BASH-rendszerhéjban vannak megadva).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

A fürt létrehozása néhány percet vesz igénybe. Alapértelmezés szerint a fürt csak Linux-csomópontkészletekkel jön létre. Ha Windows-csomópontkészleteket szeretne használni, hozzáadhat egyet. Például:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Ha a fürt készen áll, konfigurálja a-t úgy, hogy az `kubectl` [az aks get-credentials][az-aks-get-credentials] paranccsal csatlakozzon a Kubernetes-fürthöz. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét a használatukhoz:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Pod összes bejövő forgalmának megtagadása

Mielőtt szabályokat határozna meg adott hálózati forgalom engedélyezése érdekében, először hozzon létre egy hálózati szabályzatot, amely megtagadja az összes forgalmat. Ez a szabályzat kiindulási pontot biztosít ahhoz, hogy csak a kívánt forgalomhoz hozzon létre engedélyezési listát. Azt is világosan láthatja, hogy a hálózati szabályzat alkalmazásakor a forgalom el lesz dobott.

A mintaalkalmazás-környezet és a forgalmi szabályok esetében először hozzunk létre egy *development* (fejlesztés) nevű névteret a példa podok futtatásához:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Hozzon létre egy NGINX-et futtató háttérpodot. Ez a háttérpod egy mintás háttér-webalkalmazás szimulálására használható. Hozza létre ezt a podot a *fejlesztési* névtérben, és nyissa meg a *80-as portot* a webes forgalom kiszolgálására. Címkézze fel a podot *az app=webapp,role=backend* címkével, hogy a következő szakaszban hálózati szabályzattal célozható meg:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Hozzon létre egy másik podot, és csatoljon egy terminál-munkamenetet annak tesztelése érdekében, hogy sikeresen eléri-e az alapértelmezett NGINX-weblapot:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

A rendszerhéj parancssorában a használatával győződjön meg arról, hogy `wget` hozzáfér az alapértelmezett NGINX-weblaphoz:

```console
wget -qO- http://backend
```

Az alábbi példakimeneten az látható, hogy az alapértelmezett NGINX-weblapot ad vissza:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetből. A tesztpod automatikusan törlődik.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Hálózati szabályzat létrehozása és alkalmazása

Most, hogy megerősítette, hogy használhatja az alapszintű NGINX weblapot a minta háttérpodon, hozzon létre egy hálózati szabályzatot, amely megtagadja az összes forgalmat. Hozzon létre egy nevű fájlt, `backend-policy.yaml` és illessze be a következő YAML-jegyzékfájlt. Ez a jegyzékfájl *egy podSelector* használatával csatolja a szabályzatot olyan podokhoz, amelyek az *app:webapp,role:backend* címkével vannak felcímkézve, a mintában használt NGINX-podhoz hasonló módon. Nincsenek szabályok definiálva a bejövő forgalom *alatt,* így a podra irányuló összes bejövő forgalom le van tiltva:

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

Nyissa meg [https://shell.azure.com](https://shell.azure.com) a Azure Cloud Shell a böngészőben.

Alkalmazza a hálózati szabályzatot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>A hálózati szabályzat tesztelése

Lássuk, hogy használhatja-e újra az NGINX weblapot a háttérpodon. Hozzon létre egy másik tesztpodot, és csatoljon egy terminál-munkamenetet:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

A rendszerhéj parancssorában a használatával nézze meg, hogy `wget` hozzáfér-e az alapértelmezett NGINX-weblaphoz. Ezúttal állítson be 2 másodperces *időtúllépési* értéket. A hálózati szabályzat most már minden bejövő forgalmat letilt, így az oldal nem tölthető be, ahogy az alábbi példában látható:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Lépjen ki a csatolt terminál-munkamenetből. A tesztpod automatikusan törlődik.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Bejövő forgalom engedélyezése podcímke alapján

Az előző szakaszban egy háttér-NGINX-pod volt ütemezve, és létrejött egy hálózati szabályzat, amely megtagadja az összes forgalmat. Hozzunk létre egy előoldali podot, és frissítjük a hálózati szabályzatot úgy, hogy engedélyezze az előoldali podok forgalmát.

Frissítse a hálózati szabályzatot úgy, hogy engedélyezze a podok adatforgalmát az *app:webapp, role:frontend* címkével és bármely névtérben. Szerkessze az előző *backend-policy.yaml* fájlt, és adjon hozzá *matchLabels* bejövő szabályokat, hogy a jegyzékfájl az alábbi példához hasonlítsa:

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
> Ez a hálózati szabályzat egy *namespaceSelector* és egy *podSelector* elemet használ a bejövő szabályhoz. A YAML-szintaxis fontos ahhoz, hogy a bejövő szabályok additívak lesznek. Ebben a példában mindkét elemnek egyeznie kell ahhoz, hogy a bejövő szabály alkalmazva legyen. Előfordulhat, hogy az *1.12-es* előtti Kubernetes-verziók nem megfelelően értelmezik ezeket az elemeket, és a várt módon korlátozzák a hálózati forgalmat. További információ erről a viselkedésről: A és a [beválasztók viselkedése.][policy-rules]

Alkalmazza a frissített hálózati szabályzatot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f backend-policy.yaml
```

Ütemezz egy *app=webapp,role=frontend* címkével jelölt podot, és csatoljon egy terminál-munkamenetet:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

A rendszerhéj parancssorában a használatával nézze meg, hogy `wget` hozzáfér-e az alapértelmezett NGINX-weblaphoz:

```console
wget -qO- http://backend
```

Mivel a bejövő forgalmi szabály olyan podokkal engedélyezi a forgalmat, amelyeken a *címkealkalmazás található: webapp,role: frontend*, az előtere podról származó forgalom engedélyezett. Az alábbi példakimeneten az alapértelmezett NGINX-weblap van visszaadva:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetből. A pod automatikusan törlődik.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Pod tesztelése egyező címke nélkül

A hálózati szabályzat engedélyezi az alkalmazás címkével jelölt podok *forgalmát: webalkalmazás,szerepkör:* előtere , de minden más forgalmat le kell tiltani. Teszteljük, hogy a címkék nélküli másik pod hozzáfér-e a háttér NGINX-podhoz. Hozzon létre egy másik tesztpodot, és csatoljon egy terminál-munkamenetet:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

A rendszerhéj parancssorában a használatával nézze meg, hogy `wget` hozzáfér-e az alapértelmezett NGINX-weblaphoz. A hálózati szabályzat blokkolja a bejövő forgalmat, így az oldal nem tölthető be, ahogy az alábbi példában látható:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Lépjen ki a csatolt terminál-munkamenetből. A tesztpod automatikusan törlődik.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Csak meghatározott névtéren belülről származó forgalom engedélyezése

Az előző példákban létrehozott egy hálózati szabályzatot, amely megtagadta az összes forgalmat, majd frissítette a szabályzatot, hogy engedélyezze az adott címkével bíró podok forgalmát. Egy másik gyakori cél a forgalom korlátozása egy adott névtéren belülre. Ha az előző példákban fejlesztési névtérben volt forgalom, hozzon létre egy hálózati szabályzatot, amely megakadályozza, hogy egy másik névtérből, például az éles környezetből származó forgalom elérje *a* podokat.

Először hozzon létre egy új névteret az éles névtér szimulálására:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Ütemezz az éles  névtérben az *app=webapp,role=frontend* címkével jelölt tesztpodot. Terminál-munkamenet csatolása:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

A rendszerhéj parancssorában a használatával győződjön meg arról, hogy `wget` hozzáfér az alapértelmezett NGINX-weblaphoz:

```console
wget -qO- http://backend.development
```

Mivel a pod címkéi megegyeznek a hálózati szabályzatban jelenleg engedélyezett címkékkel, a forgalom engedélyezett. A hálózati szabályzat nem a névtereket, csak a podcímkéket nézi meg. Az alábbi példakimeneten az alapértelmezett NGINX-weblap van visszaadva:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetből. A tesztpod automatikusan törlődik.

```console
exit
```

### <a name="update-the-network-policy"></a>A hálózati házirend frissítése

Frissítjük a bejövő szabály *namespaceSelector* szakaszát úgy, hogy csak a fejlesztési névtéren belül engedélyezze a *forgalmat.* *Szerkessze a backend-policy.yaml* jegyzékfájlt az alábbi példában látható módon:

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

Összetettebb példákban több bejövő szabályt is definiálhat, például egy *namespaceSelector,* majd egy *podSelector szabályt.*

Alkalmazza a frissített hálózati szabályzatot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>A frissített hálózati szabályzat tesztelése

Ütemezz egy másik podot az *éles névtérben,* és csatoljon egy terminál-munkamenetet:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

A rendszerhéj parancssorában a használatával láthatja, hogy a `wget` hálózati szabályzat most megtagadja a forgalmat:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Lépjen ki a tesztpodból:

```console
exit
```

Ha az éles  névtérről megtagadta a forgalmat, ütemezz egy tesztpodot a fejlesztési névtérben, és csatoljon egy terminál-munkamenetet: 

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

A rendszerhéj parancssorában a használatával láthatja, hogy a `wget` hálózati szabályzat engedélyezi-e a forgalmat:

```console
wget -qO- http://backend
```

A forgalom azért engedélyezett, mert a pod a hálózati szabályzatban engedélyezettekkel egyezésű névtérben van ütemezve. Az alábbi példakimeneten az alapértelmezett NGINX-weblap van visszaadva:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetből. A tesztpod automatikusan törlődik.

```console
exit
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben két névteret hoztunk létre, és alkalmaztunk egy hálózati szabályzatot. Az erőforrások törléséhez használja a [kubectl delete][kubectl-delete] parancsot, és adja meg az erőforrásneveket:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Következő lépések

További információ a hálózati erőforrásokról: Hálózati fogalmak alkalmazásokhoz a Azure Kubernetes Service [(AKS) szolgáltatásban.][concepts-network]

A szabályzatokkal kapcsolatos további információkért lásd: [Kubernetes hálózati szabályzatok.][kubernetes-network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip