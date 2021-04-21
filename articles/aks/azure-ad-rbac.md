---
title: Az Azure AD és a Kubernetes RBAC használata fürtökhöz
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan korlátozhatja Azure Active Directory fürterőforrásokhoz való hozzáférést Azure Active Directory Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával az Azure Kubernetes Service (AKS) szolgáltatásban
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769552"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>A fürterőforrásokhoz való hozzáférés szabályozása Kubernetes szerepköralapú hozzáférés-vezérléssel és Azure Active Directory identitásokkal a Azure Kubernetes Service

Azure Kubernetes Service (AKS) konfigurálható az Azure Active Directory (AD) használatára a felhasználói hitelesítéshez. Ebben a konfigurációban egy Azure AD-hitelesítési jogkivonattal jelentkezik be egy AKS-fürtbe. A Kubernetes szerepköralapú hozzáférés-vezérlését (Kubernetes RBAC) is konfigurálhatja úgy, hogy korlátozza a fürterőforrásokhoz való hozzáférést a felhasználó identitása vagy csoporttagság alapján.

Ez a cikk bemutatja, hogyan szabályozhatja a névterek és fürterőforrások hozzáférését az Azure AD-csoporttagság használatával a Kubernetes RBAC használatával egy AKS-fürtben. A példacsoportok és a felhasználók az Azure AD-ban, majd a szerepkörök és a RoleBindings létrehozása az AKS-fürtben az erőforrások létrehozásához és megtekintéséhez szükséges engedélyek megadásához szükséges.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy egy meglévő AKS-fürtön engedélyezve van az Azure AD-integráció. Ha AKS-fürtre van szüksége, tekintse meg [a Azure Active Directory AKS-sel való integrálását.][azure-ad-aks-cli]

Az Azure CLI 2.0.61-es vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Bemutatócsoportok létrehozása az Azure AD-ban

Ebben a cikkben két felhasználói szerepkört hozunk létre, amelyek megmutatják, hogyan szabályozhatja a Kubernetes RBAC és az Azure AD a fürterőforrásokhoz való hozzáférést. A következő két példaszerepet használjuk:

* **Alkalmazásfejlesztő**
    * Egy *aksdev nevű felhasználó,* amely az *appdev csoport része.*
* **Site Reliability Engineer**
    * Egy *akssre nevű felhasználó,* amely az *opssre csoport tagja.*

Éles környezetben meglévő felhasználókat és csoportokat használhat egy Azure AD-bérlőn belül.

Először szerezze be az AKS-fürt erőforrás-azonosítóját [az az aks show paranccsal.][az-aks-show] Rendelje hozzá az erőforrás-azonosítót egy *AKS_ID* nevű változóhoz, hogy további parancsokban is hivatkozni tudjanak rá.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Hozza létre az első példacsoportot az Azure AD-ban az alkalmazásfejlesztők számára [az az ad group create paranccsal.][az-ad-group-create] Az alábbi példa létrehoz egy *appdev nevű csoportot:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Most hozzon létre egy Azure-beli szerepkör-hozzárendelést az *appdev-csoporthoz* [az az role assignment create paranccsal.][az-role-assignment-create] Ez a hozzárendelés lehetővé teszi, hogy a csoport bármely tagja használja az AKS-fürttel való interakciót azáltal, hogy Azure Kubernetes Service fürt felhasználói `kubectl` *szerepkört.*

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Ha a következő hibaüzenet jelenik meg: , várjon néhány másodpercet, amíg az Azure AD-csoport objektumazonosítója propagál a könyvtáron keresztül, majd próbálkozzon újra a `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` `az role assignment create` paranccsal.

Hozzon létre egy második példacsoportot, amely az *opssre nevű SRE-khez tartozik:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Ismét hozzon létre egy Azure-beli szerepkör-hozzárendelést, amely a csoport tagjainak Azure Kubernetes Service *felhasználói szerepkört:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Bemutatófelhasználók létrehozása az Azure AD-ban

Az Azure AD-ban az alkalmazásfejlesztők és az SRE-k számára létrehozott két példacsoporttal most hozzunk létre két példafelhasználót. A Cikk végén a Kubernetes RBAC-integráció teszteléséhez jelentkezzen be az AKS-fürtbe ezekkel a fiókokkal.

Állítsa be az egyszerű felhasználónevet (UPN) és jelszót az alkalmazásfejlesztők számára. A következő parancs kéri az UPN-t, és AAD_DEV_UPN-t állít be egy későbbi parancsban való használatra (ne feledje, hogy a cikkben megadott parancsok egy BASH-rendszerhéjban vannak megadva).  Az UPN-nek tartalmaznia kell a bérlő ellenőrzött tartománynevét, `aksdev@contoso.com` például: .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

A következő parancs kéri a jelszót, és AAD_DEV_PW *egy* későbbi parancsban való használatra.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Hozza létre az első felhasználói fiókot az Azure AD-ban [az az ad user create paranccsal.][az-ad-user-create]

Az alábbi példa egy *AKS Dev* megjelenített névvel és upn-sel és biztonságos jelszóval létrehozott felhasználót hoz létre a AAD_DEV_UPN *és* a *AAD_DEV_PW:*

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Most adja hozzá a felhasználót az előző szakaszban létrehozott *appdev* csoporthoz az [az ad group member add paranccsal:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Állítsa be az SRE-k upn-ját és jelszavát. A következő parancs kéri az UPN-t, és AAD_SRE_UPN-t ad meg egy későbbi parancsban való használatra (ne feledje, hogy a cikkben megadott parancsok egy BASH-rendszerhéjban vannak megadva).  Az UPN-nek tartalmaznia kell a bérlő ellenőrzött tartománynevét, `akssre@contoso.com` például: .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

A következő parancs bekérdezi a jelszót, és AAD_SRE_PW *egy* későbbi parancsban való használatra.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Hozzon létre egy második felhasználói fiókot. Az alábbi példa egy *AKS SRE* megjelenítendő névvel és upn-sel és biztonságos jelszóval létrehozott felhasználót hoz létre a AAD_SRE_UPN *és* a *AAD_SRE_PW:*

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Az AKS-fürterőforrások létrehozása alkalmazás-fejlesztők számára

Létrejöttek az Azure AD-csoportok és -felhasználók. Azure-beli szerepkör-hozzárendeléseket hoztak létre a csoporttagok számára, hogy normál felhasználóként csatlakozzon egy AKS-fürthöz. Most konfiguráljuk az AKS-fürtöt úgy, hogy engedélyezze ezeknek a különböző csoportoknak az adott erőforrásokhoz való hozzáférést.

Először szerezze be a fürt rendszergazdai hitelesítő adatait [az az aks get-credentials paranccsal.][az-aks-get-credentials] A következő szakaszok egyikében lekért normál felhasználói fürthiteles adatokkal láthatja az Azure AD-hitelesítési folyamatot. 

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Hozzon létre egy névteret az AKS-fürtben a [kubectl create namespace paranccsal.][kubectl-create] Az alábbi példa egy dev névtérnevet *hoz létre:*

```console
kubectl create namespace dev
```

A Kubernetesben a *szerepkörök* határozzák meg az engedélyeiket, és a *RoleBindings* alkalmazza őket a kívánt felhasználókra vagy csoportokra. Ezek a hozzárendelések alkalmazhatók egy adott névtérre vagy a teljes fürtre. További információ: [A Kubernetes RBAC-hitelesítés használata.][rbac-authorization]

Először hozzon létre egy szerepkört a *dev névtérhez.* Ez a szerepkör teljes körű engedélyeket biztosít a névtérhez. Éles környezetben részletesebb engedélyeket is megadhat a különböző felhasználókhoz vagy csoportokhoz.

Hozzon létre egy nevű fájlt, `role-dev-namespace.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Hozza létre a szerepkört a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ezután az [az ad group show][az-ad-group-show] paranccsal szerezze be az *appdev* csoport erőforrás-azonosítóját. Ez a csoport lesz a RoleBinding tárgya a következő lépésben.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Most hozzon létre egy RoleBinding szerepkört az *appdev-csoporthoz,* hogy a korábban létrehozott szerepkört használja a névtér-hozzáféréshez. Hozzon létre egy nevű fájlt, `rolebinding-dev-namespace.yaml` és illessze be a következő YAML-jegyzékfájlt. Az utolsó sorban cserélje le *a groupObjectId*  helyére az előző parancs csoportobjektum-azonosítójának kimenetét:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Hozza létre a RoleBindinget a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Az AKS-fürterőforrások létrehozása SRE-k számára

Most ismételje meg az előző lépéseket egy névtér, egy szerepkör és egy RoleBinding létrehozásához az SRE-k számára.

Először hozzon létre egy névteret az *sre számára* a [kubectl create namespace paranccsal:][kubectl-create]

```console
kubectl create namespace sre
```

Hozzon létre egy nevű fájlt, `role-sre-namespace.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Hozza létre a szerepkört a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f role-sre-namespace.yaml
```

Az [az ad group show][az-ad-group-show] paranccsal szerezze be az *opssre* csoport erőforrás-azonosítóját:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Hozzon létre egy RoleBinding szerepkört az *opssre-csoporthoz* a korábban létrehozott szerepkör névtér-hozzáféréshez való használatára. Hozzon létre egy nevű fájlt, `rolebinding-sre-namespace.yaml` és illessze be a következő YAML-jegyzékfájlt. Az utolsó sorban cserélje le *a groupObjectId*  helyére az előző parancs csoportobjektum-azonosítójának kimenetét:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Hozza létre a RoleBindinget a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Fürterőforrások használata Azure AD-identitások használatával

Most teszteljük a várt engedélyeket, amikor erőforrásokat hoz létre és kezel egy AKS-fürtben. Ezekben a példákban podokat ütemez és megtekint a felhasználó hozzárendelt névterében. Ezután megpróbálja ütemezni és megtekinteni a podokat a hozzárendelt névtéren kívül.

Először állítsa alaphelyzetbe a *kubeconfig* környezetet az [az aks get-credentials paranccsal.][az-aks-get-credentials] Az előző szakaszban a fürt rendszergazdai hitelesítő adataival állíthatja be a környezetet. A rendszergazdai felhasználó megkerüli az Azure AD bejelentkezési kéréseket. A paraméter nélkül a felhasználói környezet lesz alkalmazva, amelyhez az összes kérést hitelesíteni kell `--admin` az Azure AD használatával.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Ütemezz egy alapszintű NGINX-podot a dev névtérben a [kubectl run][kubectl-run] *paranccsal:*

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

A bejelentkezési kérés során adja meg a cikk elején létrehozott saját fiókja `appdev@contoso.com` hitelesítő adatait. Miután sikeresen bejelentkezett, a rendszer gyorsítótárazza a fiók jogkivonatát a későbbi `kubectl` parancsokhoz. Az NGINX sikeresen ütemezve van, ahogy az az alábbi példakimenetben látható:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Most használja a [kubectl get pods parancsot][kubectl-get] a podok megtekintéséhez a *dev névtérben.*

```console
kubectl get pods --namespace dev
```

Ahogy az alábbi kimenetben is látható, az NGINX-pod sikeresen *fut:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Fürterőforrások létrehozása és megtekintése a hozzárendelt névtéren kívül

Most próbálja meg megtekinteni a podokat a *dev névtéren* kívül. Használja ismét a [kubectl get pods][kubectl-get] parancsot, ezúttal a `--all-namespaces` következőképpen:

```console
kubectl get pods --all-namespaces
```

A felhasználó csoporttagságához nem tartozik Kubernetes-szerepkör, amely lehetővé teszi ezt a műveletet, ahogyan az az alábbi kimenetben látható:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Ugyanígy próbáljon meg másik névtérben, például az *sre névtérben ütemezni* egy podot. A felhasználó csoporttagság nem igazodik a Kubernetes-szerepkörhöz és a RoleBindinghez ezen engedélyek megadásához, ahogy az alábbi kimenetben látható:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Az AKS-fürt erőforrásaihoz való SRE-hozzáférés tesztelése

Annak megerősítéséhez, hogy az Azure AD-csoporttagság és a Kubernetes RBAC megfelelően működik a különböző felhasználók és csoportok között, próbálja ki az előző parancsokat, amikor *opssre-felhasználóként van bejelentkezve.*

Állítsa alaphelyzetbe a *kubeconfig* környezetet az [az aks get-credentials][az-aks-get-credentials] paranccsal, amely törli az *aksdev-felhasználó* korábban gyorsítótárazott hitelesítési jogkivonatát:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Próbálja meg ütemezni és megtekinteni a podokat a *hozzárendelt sre névtérben.* Amikor a rendszer kéri, jelentkezzen be a cikk elején létrehozott saját `opssre@contoso.com` hitelesítő adataival:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Az alábbi példakimenetben látható módon sikeresen létrehozhatja és megtekintheti a podokat:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Most próbálja meg megtekinteni vagy ütemezni a podokat a hozzárendelt SRE-névtéren kívül:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Ezek `kubectl` a parancsok sikertelenek, ahogy az alábbi kimenetben látható. A felhasználó csoporttagsági és Kubernetes-szerepköre és RoleBindings szerepköre nem biztosít engedélyeket más névterek erőforrásainak létrehozásához vagy felette való hozzáféréshez:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben erőforrásokat hozott létre az AKS-fürtben, valamint felhasználókat és csoportokat az Azure AD-ban. Az összes erőforrás megtisztítása érdekében futtassa a következő parancsokat:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Következő lépések

További információ a Kubernetes-fürtök biztonságának beállításával kapcsolatban: Hozzáférési és identitási beállítások [az AKS-hez).][rbac-authorization].

Az identitással és az erőforrás-vezérléssel kapcsolatos ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való hitelesítéshez [és engedélyezéshez.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
