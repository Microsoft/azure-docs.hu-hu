---
title: A pod biztonsági szabályzatok használata az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan vezérelheti a pod-felvételeket az Azure Kubernetes Service (ak) PodSecurityPolicy használatával
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 5bd4e1b85513ed5473b4136b458d20fef4faa79c
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374491"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Előzetes verzió – a fürt biztonságossá tétele a pod biztonsági szabályzatok használatával az Azure Kubernetes szolgáltatásban (ak)

Az AK-fürt biztonságának növelése érdekében korlátozhatja, hogy a hüvelyek hogyan ütemezhetők. A nem engedélyezett erőforrásokat kérő hüvelyek nem futhatnak az AK-fürtben. Ezt a hozzáférést a pod biztonsági szabályzatok használatával határozhatja meg. Ez a cikk bemutatja, hogyan használhatja a pod biztonsági házirendeket a hüvelyek AK-ban való üzembe helyezésének korlátozására.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI-verzió 2.0.61 vagy újabb verziójára, és konfigurálva van.  `az --version`A verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

A pod biztonsági szabályzatok használatához a CLI *-előnézet CLI-* bővítményének 0.4.1 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>A pod biztonsági házirend szolgáltatás-szolgáltató regisztrálása

Ha AK-t tartalmazó fürtöt szeretne létrehozni vagy frissíteni a pod biztonsági házirendek használatára, először engedélyezzen egy funkció-jelölőt az előfizetésén. A *PodSecurityPolicyPreview* szolgáltatás jelzőjét a következő példában látható módon regisztrálja az az [Feature Register][az-feature-register] parancs használatával:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>A pod biztonsági szabályzatok áttekintése

Egy Kubernetes-fürtben a belépésvezérlés az API-kiszolgálónak küldött kérések elfogására szolgálnak egy erőforrás létrehozásakor. A beléptetési vezérlő Ezután *érvényesítheti* az erőforrás-kérést egy adott szabálykészlet alapján, vagy átválthatja az erőforrást *az üzembe* helyezési paraméterek módosításához.

A *PodSecurityPolicy* egy olyan belépésvezérlés, amely ellenőrzi, hogy a pod-specifikáció megfelel-e a megadott követelményeknek. Ezek a követelmények korlátozhatják a privilegizált tárolók használatát, bizonyos típusú tárolók elérését, illetve a tároló által futtatható felhasználót vagy csoportot. Ha olyan erőforrást próbál telepíteni, amelyben a pod-specifikációk nem felelnek meg a pod biztonsági szabályzatban leírt követelményeknek, a rendszer megtagadja a kérelmet. Ez a lehetőség azt szabályozhatja, hogy az AK-fürtben milyen hüvelyek ütemezhetők, így meggátolhatja a biztonsági rések vagy a jogosultságok eszkalációját.

Ha egy AK-fürtben engedélyezi a pod biztonsági szabályzatot, a rendszer néhány alapértelmezett házirendet alkalmaz. Ezek az alapértelmezett házirendek beépített felhasználói élményt nyújtanak a hüvelyek ütemezésének meghatározásához. A fürt felhasználói azonban a hüvelyek üzembe helyezése során problémákba kerülhetnek, amíg meg nem határozta a saját szabályzatait. Az ajánlott módszer a következő:

* AKS-fürt létrehozása
* Saját Pod biztonsági szabályzatok definiálása
* A pod biztonsági házirend funkció engedélyezése

Ha szeretné megmutatni, hogy az alapértelmezett szabályzatok hogyan korlátozzák a pod üzemelő példányokat, ebben a cikkben először engedélyezzük a pod biztonsági házirendek szolgáltatást, majd létrehozunk egy egyéni házirendet.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Pod biztonsági szabályzat engedélyezése AK-fürtön

Az az [AK Update][az-aks-update] paranccsal engedélyezheti vagy letilthatja a pod biztonsági házirendet. A következő példa engedélyezi a pod biztonsági szabályzatot a *myResourceGroup*nevű *myAKSCluster* található fürt nevére.

> [!NOTE]
> A valós használat érdekében ne engedélyezze a pod biztonsági házirendet, amíg meg nem határozta a saját egyéni szabályzatait. Ebből a cikkből megtudhatja, hogy az alapértelmezett szabályzatok hogyan korlátozzák a pod üzemelő példányokat az első lépésként.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Alapértelmezett AK-szabályzatok

Ha engedélyezi a pod biztonsági házirendet, az AK létrehoz egy *privilegizált*nevű alapértelmezett szabályzatot. Ne szerkessze vagy távolítsa el az alapértelmezett házirendet. Ehelyett hozzon létre saját szabályzatokat, amelyek meghatározzák a vezérelni kívánt beállításokat. Először nézzük meg, hogy ezek az alapértelmezett szabályzatok hogyan befolyásolják a pod üzemelő példányait.

Az elérhető szabályzatok megtekintéséhez használja a [kubectl Get PSP][kubectl-get] parancsot az alábbi példában látható módon.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A *Privileged* Pod biztonsági szabályzatot az AK-fürt bármely hitelesített felhasználója alkalmazza. Ezt a hozzárendelést a ClusterRoles és a ClusterRoleBindings vezérli. Használja a [kubectl Get rolebindings][kubectl-get] parancsot, és keresse meg az *alapértelmezett: privilegizált* : kötést a *Kube-System* névtérben:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Ahogy az a következő tömörített kimenetben látható, a *PSP: korlátozott* ClusterRole bármely rendszerhez van rendelve *: hitelesített* felhasználók. Ez a képesség alapvető szintű korlátozásokat biztosít a saját szabályzatok meghatározása nélkül.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Fontos megérteni, hogy ezek az alapértelmezett házirendek hogyan használják a felhasználói kéréseket a hüvelyek időzítésére, mielőtt elkezdi létrehozni saját Pod biztonsági házirendjeit. A következő néhány szakaszban néhány hüvelyt ütemezhet, hogy megtekintse ezeket az alapértelmezett szabályzatokat működés közben.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tesztelési felhasználó létrehozása AK-fürtben

Alapértelmezés szerint az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatakor a rendszer HOZZÁADJA az AK-fürthöz tartozó *rendszergazdai* hitelesítő adatokat a `kubectl` konfigurációhoz. A rendszergazda felhasználó megkerüli a pod biztonsági szabályzatok kényszerítését. Ha Azure Active Directory-integrációt használ az AK-fürtökhöz, bejelentkezhet a nem rendszergazda felhasználó hitelesítő adataival, hogy megtekintse a házirendek kényszerítését működés közben. Ebben a cikkben hozzunk létre egy tesztelési felhasználói fiókot az AK-fürtben, amelyet használhat.

Hozzon létre egy *PSP-AK* nevű minta névteret az erőforrások teszteléséhez a [kubectl Create Namespace][kubectl-create] parancs használatával. Ezután hozzon létre egy nem *rendszergazda* nevű szolgáltatásfiókot a [kubectl Create ServiceAccount][kubectl-create] parancs használatával:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Következő lépésként hozzon létre egy RoleBinding a nem *rendszergazdai jogú felhasználó* számára a névtér alapszintű műveleteinek a [kubectl Create RoleBinding][kubectl-create] paranccsal történő elvégzéséhez:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Alias-parancsok létrehozása a rendszergazda és a nem rendszergazda felhasználók számára

`kubectl`Hozzon létre két parancssori aliast a normál rendszergazda felhasználó és az előző lépésben létrehozott nem rendszergazda felhasználó közötti különbség kiemeléséhez:

* A **kubectl-rendszergazdai** alias a normál rendszergazda felhasználóhoz tartozik, és a *PSP-Kabai* névtérre van kiterjedően.
* Az **kubectl-nonadminuser** alias az előző lépésben létrehozott nem *rendszergazda felhasználóhoz* tartozik, és a *PSP-Kabai* névtérre van korlátozva.

Hozza létre ezt a két aliast az alábbi parancsokban látható módon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Emelt szintű Pod létrehozásának tesztelése

Első lépésként tesztelje, mi történik, ha egy Pod-t a biztonsági környezetével ütemezhet `privileged: true` . Ez a biztonsági környezet a pod jogosultságait bővíti. Az előző szakaszban, amely az alapértelmezett AK Pod biztonsági házirendeket mutatta, a *korlátozott* szabályzatnak meg kell tagadnia ezt a kérést.

Hozzon létre egy nevű fájlt `nginx-privileged.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

A pod nem ütemezhető, ahogy az a következő példában látható:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszt, ezért nincs szükség a törlendő erőforrásokra, mielőtt továbblép.

## <a name="test-creation-of-an-unprivileged-pod"></a>Nem privilegizált Pod-létrehozási teszt létrehozása

Az előző példában a pod specifikáció a Kiemelt jogosultságok kiterjesztését kérte. Ezt a kérést az alapértelmezett *korlátozott* Pod biztonsági házirend elutasította, így a pod nem ütemezhető. Most próbáljon meg ugyanezt a NGINX Pod-t futtatni a jogosultság-eszkalációs kérelem nélkül.

Hozzon létre egy nevű fájlt `nginx-unprivileged.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod nem ütemezhető, ahogy az a következő példában látható:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszt, ezért nincs szükség a törlendő erőforrásokra, mielőtt továbblép.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>A pod egy adott felhasználói környezettel való létrehozásának tesztelése

Az előző példában a tároló képe automatikusan megpróbálta a root használatával kötni a NGINX-et a 80-es portra. Ezt a kérést az alapértelmezett *korlátozott* Pod biztonsági házirend megtagadta, így a pod nem indul el. Most próbálja meg ugyanezt az NGINX Pod-t egy adott felhasználói környezettel, például: `runAsUser: 2000` .

Hozzon létre egy nevű fájlt `nginx-unprivileged-nonroot.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

A pod nem ütemezhető, ahogy az a következő példában látható:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszt, ezért nincs szükség a törlendő erőforrásokra, mielőtt továbblép.

## <a name="create-a-custom-pod-security-policy"></a>Egyéni Pod biztonsági szabályzat létrehozása

Most, hogy megismerte az alapértelmezett Pod biztonsági szabályzatok viselkedését, lehetővé teszi, hogy a nem *rendszergazda felhasználó* sikeresen ütemezze a hüvelyeket.

Hozzunk létre egy szabályzatot, amely elutasítja az emelt szintű hozzáférést kérő hüvelyeket. Az egyéb beállítások, például a *runAsUser* vagy az engedélyezett *kötetek*nincsenek explicit módon korlátozva. Ez a típusú szabályzat megtagadja a jogosultsági szintű hozzáférés kérését, de más módon lehetővé teszi, hogy a fürt futtassa a kért hüvelyt.

Hozzon létre egy nevű fájlt `psp-deny-privileged.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Hozza létre a szabályzatot a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Az elérhető szabályzatok megtekintéséhez használja a [kubectl Get PSP][kubectl-get] parancsot az alábbi példában látható módon. Hasonlítsa össze a *PSP-megtagadás jogosultságokkal* rendelkező szabályzatot az előző példákban kikényszerített alapértelmezett *korlátozott* házirenddel a pod létrehozásához. A szabályzat csak a *priv* -eszkaláció használatát utasítja el. A *PSP-megtagadási jogosultságú* szabályzathoz nem tartoznak korlátozások a felhasználóra vagy a csoportra vonatkozóan.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Egyéni Pod biztonsági házirend használatának engedélyezése a felhasználói fiók számára

Az előző lépésben létrehozott egy Pod biztonsági szabályzatot, amely elutasítja a privilegizált hozzáférést kérő hüvelyeket. A szabályzat használatának engedélyezéséhez hozzon létre egy *szerepkört* vagy egy *ClusterRole*. Ezt követően rendeljen hozzá egyet ezekhez a szerepkörökhöz egy *RoleBinding* vagy *ClusterRoleBinding*használatával.

Ehhez a példához hozzon létre egy ClusterRole, amely lehetővé teszi az előző lépésben létrehozott *PSP-megtagadás-privilegizált* szabályzat *használatát* . Hozzon létre egy nevű fájlt `psp-deny-privileged-clusterrole.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Hozza létre a ClusterRole a [kubectl Apply][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Most hozzon létre egy ClusterRoleBinding az előző lépésben létrehozott ClusterRole használatára. Hozzon létre egy nevű fájlt `psp-deny-privileged-clusterrolebinding.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Hozzon létre egy ClusterRoleBinding a [kubectl Apply][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> A cikk első lépéseként a pod biztonsági házirend funkció engedélyezve lett az AK-fürtön. Az ajánlott eljárás az volt, hogy csak a saját szabályzatok meghatározása után engedélyezze a pod biztonsági házirend funkciót. Ebben a szakaszban engedélyezheti a pod biztonsági házirend funkcióját. Egy vagy több egyéni házirend lett definiálva, és a felhasználói fiókok társítva vannak ezekhez a szabályzatokhoz. Mostantól biztonságosan engedélyezheti a pod biztonsági házirend funkcióját, és csökkentheti az alapértelmezett házirendek által okozott problémákat.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>A nem privilegizált Pod létrehozásának tesztelése

Ha az egyéni Pod biztonsági szabályzatot alkalmazta, és a felhasználói fiókra vonatkozó kötést használ a szabályzat használatára, próbálkozzon újra egy nem védett Pod létrehozásával. Használja ugyanazt a `nginx-privileged.yaml` jegyzékfájlt a pod létrehozásához a [kubectl Apply][kubectl-apply] parancs használatával:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod sikeresen ütemezve. Ha a [kubectl Get hüvely][kubectl-get] parancs használatával tekinti meg a pod állapotát, a pod a következőket *futtatja*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Ebből a példából megtudhatja, hogyan hozhat létre egyéni Pod biztonsági házirendeket a különböző felhasználókhoz vagy csoportokhoz való hozzáférés definiálásához. Az alapértelmezett AK-szabályzatok szigorú szabályozást biztosítanak a hüvelyek futtatásához, ezért hozza létre saját egyéni szabályzatait, majd megfelelően határozza meg a szükséges korlátozásokat.

A [kubectl delete][kubectl-delete] paranccsal törölje az NGINX unprivilegizált Pod-t, és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A pod biztonsági szabályzat letiltásához használja újra az az [AK Update][az-aks-update] parancsot. A következő példa letiltja a pod biztonsági házirendet a *myResourceGroup*nevű erőforráscsoport *myAKSCluster* található fürt neve elemnél:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ezután törölje a ClusterRole és a ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Törölje a biztonsági házirendet a [kubectl delete][kubectl-delete] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Végezetül törölje a *PSP-Kabai* névteret:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan hozhat létre egy Pod biztonsági házirendet, hogy megakadályozza a privilegizált hozzáférés használatát. A szabályzatok számos funkciót kikényszerítenek, például a kötet típusát vagy a futtató felhasználót. Az elérhető beállításokkal kapcsolatos további információkért tekintse meg az [Kubernetes Pod biztonsági szabályzatának dokumentációját][kubernetes-policy-reference].

A pod hálózati forgalom korlátozásával kapcsolatos további információkért lásd: [biztonságos forgalom a hüvelyek között a hálózati házirendek használatával az AK-ban][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
