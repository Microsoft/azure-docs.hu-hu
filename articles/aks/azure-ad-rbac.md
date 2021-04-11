---
title: Az Azure AD és a Kubernetes RBAC használata fürtökhöz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használhatja Azure Active Directory csoporttagság használatát a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 72b2c456d62b899f2b04041929434da668cad82d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583530"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>A fürterőforrások hozzáférésének szabályozása a Kubernetes szerepköralapú hozzáférés-vezérléssel és Azure Active Directory identitásokkal az Azure Kubernetes szolgáltatásban

Az Azure Kubernetes Service (ak) konfigurálható úgy, hogy Azure Active Directory (AD) használatát használja a felhasználói hitelesítéshez. Ebben a konfigurációban egy Azure AD-hitelesítési jogkivonat használatával jelentkezik be egy AK-fürtbe. A Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) is konfigurálhatja a fürt erőforrásaihoz való hozzáférés korlátozásához a felhasználó identitása vagy csoporttagság alapján.

Ez a cikk bemutatja, hogyan kezelheti az Azure AD-csoporttagság használatával a névterek és a fürt erőforrásaihoz való hozzáférést a Kubernetes RBAC egy AK-fürtben. Például a csoportok és a felhasználók az Azure AD-ben jönnek létre, majd a szerepkörök és a RoleBindings az AK-fürtben jönnek létre, így biztosítva a megfelelő engedélyeket az erőforrások létrehozásához és megtekintéséhez.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AK-fürtön engedélyezve van az Azure AD-integráció. Ha AK-fürtre van szüksége, tekintse meg a következőt: [Azure Active Directory integrálása az AK][azure-ad-aks-cli]-nal.

Szüksége lesz az Azure CLI-verzió 2.0.61 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Bemutató csoportok létrehozása az Azure AD-ben

Ebben a cikkben két felhasználói szerepkört hozunk létre, amelyekből megtudhatja, hogyan érheti el a Kubernetes RBAC és az Azure AD-vezérlést a fürt erőforrásaihoz. A következő két példa a szerepköröket használja:

* **Alkalmazás-fejlesztő**
    * A *appdev* csoport részét képező *aksdev* nevű felhasználó.
* **Site megbízhatósági mérnök**
    * A *opssre* csoport részét képező *akssre* nevű felhasználó.

Éles környezetekben meglévő felhasználókat és csoportokat használhat egy Azure AD-bérlőn belül.

Először kérje le az AK-fürt erőforrás-AZONOSÍTÓját az az [AK show][az-aks-show] parancs használatával. Rendelje hozzá az erőforrás-azonosítót egy *AKS_ID* nevű változóhoz, hogy a további parancsokban is hivatkozhat rá.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Hozza létre az első példa csoportot az Azure AD-ben az alkalmazás fejlesztői számára az az [ad Group Create][az-ad-group-create] paranccsal. A következő példában létrehozunk egy *appdev* nevű csoportot:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Most hozzon létre egy Azure-szerepkör-hozzárendelést a *appdev* -csoport számára az az [role hozzárendelés Create][az-role-assignment-create] paranccsal. Ez a hozzárendelés lehetővé teszi, hogy a csoport bármely tagja használja az `kubectl` AK-fürtöket az *Azure Kubernetes Service-fürt felhasználói szerepkörének* megadásával.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Ha hibaüzenetet kap `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , várjon néhány másodpercet, amíg az Azure ad-csoport objektumazonosító át nem terjed a címtárban, majd próbálja megismételni a `az role assignment create` parancsot.

Hozzon létre egy második példa csoportot, amely a *opssre* nevű SREs:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Ismét hozzon létre egy Azure-szerepkör-hozzárendelést a csoport tagjai számára az *Azure Kubernetes Service-fürt felhasználói szerepkörének* megadásához:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Bemutató felhasználók létrehozása az Azure AD-ben

Az Azure AD-ben az alkalmazás-fejlesztőknek és a SREs két példával létrehozott csoportoknak köszönhetően két példás felhasználót hozhat létre. A cikk végén a Kubernetes RBAC-integráció teszteléséhez jelentkezzen be az AK-fürtbe ezekkel a fiókokkal.

Az alkalmazás fejlesztői számára adja meg az egyszerű felhasználónevet (UPN) és a jelszót. A következő parancs bekéri az egyszerű felhasználónevet, és beállítja, hogy *AAD_DEV_UPN* egy későbbi parancsban való használatra (ne feledje, hogy a cikkben szereplő parancsok egy bash-rendszerhéjba kerülnek). Az UPN-nek tartalmaznia kell a bérlő ellenőrzött tartománynevét, például: `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

A következő parancs megkéri a jelszót, és beállítja, hogy *AAD_DEV_PW* egy későbbi parancsban való használatra.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Hozza létre az első felhasználói fiókot az Azure AD-ben az az [ad User Create][az-ad-user-create] paranccsal.

Az alábbi példa egy olyan felhasználót hoz létre, amely az *AK dev* és az UPN és a biztonságos jelszó megjelenítéséhez a *AAD_DEV_UPN* és *AAD_DEV_PW* értékeit használja:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Most adja hozzá a felhasználót az előző szakaszban létrehozott *appdev* csoporthoz az az [ad Group tag Add][az-ad-group-member-add] paranccsal:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Állítsa be az egyszerű felhasználónevet és a jelszót a SREs. A következő parancs bekéri az egyszerű felhasználónevet, és beállítja, hogy *AAD_SRE_UPN* egy későbbi parancsban való használatra (ne feledje, hogy a cikkben szereplő parancsok egy bash-rendszerhéjba kerülnek). Az UPN-nek tartalmaznia kell a bérlő ellenőrzött tartománynevét, például: `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

A következő parancs megkéri a jelszót, és beállítja, hogy *AAD_SRE_PW* egy későbbi parancsban való használatra.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Hozzon létre egy második felhasználói fiókot. Az alábbi példa egy olyan felhasználót hoz létre, amely az *AK sre* és az UPN-t, valamint a biztonságos jelszót használja *AAD_SRE_UPN* és *AAD_SRE_PW* értékeit használva:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Az AK-fürt erőforrásainak létrehozása az alkalmazás-fejlesztőknek

Ekkor létrejön az Azure AD-csoportok és a felhasználók. Az Azure-beli szerepkör-hozzárendelések azért jöttek létre, hogy a csoporttagok egy AK-fürthöz csatlakozzanak normál felhasználóként. Most konfigurálja az AK-fürtöt, hogy a különböző csoportok hozzáférjenek az adott erőforrásokhoz.

Először kérje le a fürt rendszergazdai hitelesítő adatait az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával. A következő szakaszokban az Azure AD-hitelesítési folyamat működés közbeni megtekintéséhez a *felhasználói* fürt szokásos hitelesítő adatai láthatók.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Hozzon létre egy névteret az AK-fürtben a [kubectl Create Namespace][kubectl-create] parancs használatával. A következő példa létrehoz egy névtér- *nevet:*

```console
kubectl create namespace dev
```

A Kubernetes-ben a *szerepkörök* határozzák meg a megadható engedélyeket, és *RoleBindings* azokat a kívánt felhasználókra vagy csoportokra. Ezek a hozzárendelések egy adott névtérre vagy a teljes fürtre is alkalmazhatók. További információ: [a KUBERNETES RBAC-hitelesítés használata][rbac-authorization].

Először hozzon létre egy szerepkört a *fejlesztői* névtérhez. Ez a szerepkör teljes körű engedélyeket biztosít a névtérnek. Éles környezetekben részletesebb engedélyeket adhat meg a különböző felhasználókhoz vagy csoportokhoz.

Hozzon létre egy nevű fájlt `role-dev-namespace.yaml` , és illessze be a következő YAML-jegyzékbe:

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

Hozza létre a szerepkört a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML jegyzékfájljának fájlnevét:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ezután szerezze be a *appdev* -csoport erőforrás-azonosítóját az az [ad Group show][az-ad-group-show] parancs használatával. Ez a csoport a következő lépésben egy RoleBinding tárgyát adja meg.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Most hozzon létre egy RoleBinding a *appdev* csoport számára, hogy a korábban létrehozott szerepkört használja a névtér-hozzáféréshez. Hozzon létre egy nevű fájlt `rolebinding-dev-namespace.yaml` , és illessze be a következő YAML-jegyzékbe. Az utolsó sorban cserélje le az *groupObjectId*  -t az előző parancsban szereplő csoport objektum-azonosító kimenetére:

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

Hozza létre a RoleBinding a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>A SREs-hez készült AK-fürt erőforrásainak létrehozása

Most ismételje meg az előző lépéseket egy névtér, szerepkör és RoleBinding létrehozásához a SREs.

Először hozzon létre egy névteret a *sre* a [kubectl Create Namespace][kubectl-create] paranccsal:

```console
kubectl create namespace sre
```

Hozzon létre egy nevű fájlt `role-sre-namespace.yaml` , és illessze be a következő YAML-jegyzékbe:

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

Hozza létre a szerepkört a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML jegyzékfájljának fájlnevét:

```console
kubectl apply -f role-sre-namespace.yaml
```

Szerezze be a *opssre* -csoport erőforrás-azonosítóját az az [ad Group show][az-ad-group-show] parancs használatával:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Hozzon létre egy RoleBinding ahhoz a *opssre* -csoporthoz, amely a korábban létrehozott szerepkört használja a névtér-hozzáféréshez. Hozzon létre egy nevű fájlt `rolebinding-sre-namespace.yaml` , és illessze be a következő YAML-jegyzékbe. Az utolsó sorban cserélje le az *groupObjectId*  -t az előző parancsban szereplő csoport objektum-azonosító kimenetére:

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

Hozza létre a RoleBinding a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Fürt erőforrásainak használata az Azure AD-identitások használatával

Most tesztelje a várt engedélyeket, amikor egy AK-fürtben hoz létre és felügyel erőforrásokat. Ezekben a példákban a felhasználóhoz rendelt névtérben ütemezhet és megtekintheti a hüvelyeket. Ezután megpróbálja ütemezni és megtekinteni a hüvelyeket a hozzárendelt névtéren kívül.

Először állítsa alaphelyzetbe a *kubeconfig* környezetet az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával. Egy korábbi szakaszban a környezet a fürt rendszergazdai hitelesítő adataival állítható be. A rendszergazda felhasználó megkerüli az Azure AD bejelentkezési utasításait. A `--admin` paraméter nélkül a rendszer alkalmazza a felhasználói környezetet, amely megköveteli az összes kérelem hitelesítését az Azure ad használatával.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Alapszintű NGINX Pod-t ütemezhet a [kubectl Run][kubectl-run] paranccsal a *fejlesztői* névtérben:

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

A bejelentkezéshez írja be a `appdev@contoso.com` cikk elején létrehozott saját fiókhoz tartozó hitelesítő adatokat. Miután sikeresen bejelentkezett, a rendszer a fiók jogkivonatát gyorsítótárazza a jövőbeli `kubectl` parancsokhoz. Az NGINX sikeresen be van jelölve, ahogy az a következő példában látható:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Most a [kubectl Get hüvely][kubectl-get] parancs használatával megtekintheti a *fejlesztői* névtérben található hüvelyeket.

```console
kubectl get pods --namespace dev
```

Ahogy az az alábbi példában is látható, az NGINX Pod sikeresen *fut*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Fürt erőforrásainak létrehozása és megtekintése a hozzárendelt névtéren kívül

Most próbálja meg megtekinteni a hüvelyt a *fejlesztői* névtéren kívül. A [kubectl Get hüvely][kubectl-get] parancs újbóli futtatásához használja a `--all-namespaces` következőt:

```console
kubectl get pods --all-namespaces
```

A felhasználó csoporttagság nem rendelkezik olyan Kubernetes-szerepkörrel, amely engedélyezi ezt a műveletet, ahogy az a következő példában látható:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Ugyanígy próbáljon meg egy Pod-t más névtérben (például a *sre* névtérben) ütemezni. A felhasználó csoporttagság nem igazodik a Kubernetes-szerepkörhöz és a RoleBinding az engedélyek megadásához, ahogy az a következő példában látható:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>A SRE-hozzáférés tesztelése az AK-fürt erőforrásaihoz

Annak ellenőrzéséhez, hogy az Azure AD-csoporttagság és a Kubernetes RBAC megfelelően működik-e a különböző felhasználók és csoportok között, próbálkozzon az előző parancsokkal, amikor bejelentkezett a *opssre* -felhasználóként.

Állítsa alaphelyzetbe a *kubeconfig* környezetét az az az [AK Get-hitelesítőadats][az-aks-get-credentials] paranccsal, amely törli a korábban gyorsítótárazott hitelesítési tokent a *aksdev* -felhasználó számára:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Próbálja meg ütemezni és megtekinteni a hüvelyeket a hozzárendelt *sre* -névtérben. Ha a rendszer kéri, jelentkezzen be a `opssre@contoso.com` cikk elején létrehozott saját hitelesítő adataival:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Ahogy az az alábbi példában is látható, a hüvelyek létrehozása és megtekintése sikeresen megtekinthető:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Most próbáljon meg megtekinteni vagy ütemezni a hüvelyeket a hozzárendelt SRE-névtéren kívül:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Ezek `kubectl` a parancsok sikertelenek, ahogy az az alábbi példában is látható. A felhasználó csoporttagság-és Kubernetes-szerepköre és RoleBindings nem biztosítanak engedélyeket más névterekben lévő erőforrások létrehozásához vagy kezelőjéhez:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben erőforrásokat hozott létre az AK-fürtben, valamint az Azure AD-ben lévő felhasználókat és csoportokat. Az összes erőforrás eltávolításához futtassa a következő parancsokat:

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

További információ a Kubernetes-fürtök védelméről: [a hozzáférési és identitási beállítások az AK-][rbac-authorization]ban.

Az identitás-és erőforrás-vezérléssel kapcsolatos ajánlott eljárásokért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
