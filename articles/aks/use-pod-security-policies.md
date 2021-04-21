---
title: Podbiztonsági szabályzatok használata a Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan szabályozhatja a podok beléptetését a PodSecurityPolicy használatával Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d70b8e8efbf96e50575845ac88993012fed936d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767420"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Előzetes verzió – Fürt biztonságossá biztosítása podbiztonsági szabályzatokkal a Azure Kubernetes Service (AKS) használatával

> [!WARNING]
> **Az ebben a dokumentumban ismertetett podbiztonsági szabályzat [](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) (előzetes verzió) a Kubernetes 1.21-es verziójával elajátsodik, és az 1.25-ös verzióban lesz eltávolítva.** Ahogy a Kubernetes upstream közeledik erre a mérföldkövre, a Kubernetes-közösség azon dolgozik, hogy dokumentálja a működő alternatív megoldásokat. Az elalasztásról korábban már volt bejelentés, mivel az ügyfelek számára nem volt elérhető lehetőség. Most, hogy a Kubernetes-közösség egy alternatív megoldáson dolgozik, már nem kell a Kubernetes előtt elavultnak maradnia. 
>
> Miután a pod biztonsági szabályzata (előzetes verzió) elavult, le kell tiltania a funkciót a meglévő fürtökön az elavult funkcióval a későbbi fürtfrissítések végrehajtásához és a fürtfrissítések Azure-támogatás.

Az AKS-fürt biztonságának növelése érdekében korlátozhatja az ütemezni lehet podokat. A nem megengedett erőforrásokat lekért podok nem futtathatók az AKS-fürtben. Ezt a hozzáférést podbiztonsági szabályzatokkal határozhatja meg. Ez a cikk bemutatja, hogyan használhatja a podbiztonsági szabályzatokat a podok AKS-ben való üzembe helyezésének korlátozására.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.61-es vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

A podbiztonsági szabályzatok alkalmazáshoz az *aks-preview CLI-bővítmény* 0.4.1-es vagy újabb verziójára van szükség. Telepítse *az aks-preview* Azure CLI-bővítményt az [az extension add][az-extension-add] paranccsal, majd az az extension update paranccsal ellenőrizze az elérhető [frissítéseket:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Podbiztonsági szabályzat funkciószolgáltatójának regisztrálása

**Ez a dokumentum és funkció 2020. október 15-én elalasztásként van beállítva.**

Az AKS-fürt podbiztonsági szabályzatok használatára való létrehozásához vagy frissítéséhez először engedélyezzen egy funkciójelölőt az előfizetésen. A *PodSecurityPolicyPreview* funkciójelölő regisztráláshoz használja az [az feature register][az-feature-register] parancsot az alábbi példában látható módon:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* A regisztrációs állapotot az az feature list paranccsal [ellenőrizheti:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Podbiztonsági szabályzatok áttekintése

A Kubernetes-fürtökben a belépésvezérlővel elfoghatóak az API-kiszolgálóra vonatkozó kérések egy erőforrás létrehozásakor. A belépésvezérlő *ezután* ellenőrizheti az erőforrás-kérelmet egy  szabálykészlet alapján, vagy átállíthatja az erőforrást az üzembe helyezési paraméterek módosítása érdekében.

*A PodSecurityPolicy* egy belépésvezérlő, amely ellenőrzi, hogy a pod specifikációja megfelel-e a meghatározott követelményeknek. Ezek a követelmények korlátozhatják az emelt szintű tárolók használatát, bizonyos típusú tárolókhoz való hozzáférést, illetve azt a felhasználót vagy csoportot, amelyként a tároló futtatható. Amikor olyan erőforrást próbál üzembe helyezni, amelyben a podsokajátok nem felelnie a pod biztonsági szabályzatában ismertetett követelményeknek, a rendszer megtagadja a kérést. Az AKS-fürtben ütemezhető podok szabályozásának lehetősége meggátol néhány lehetséges biztonsági rést vagy jogosultságeszkalációs eszkalációt.

Ha engedélyezi a podok biztonsági szabályzatát egy AKS-fürtben, a rendszer néhány alapértelmezett szabályzatot alkalmaz. Ezek az alapértelmezett szabályzatok gyári beállításokat biztosítanak annak meghatározásához, hogy milyen podokat lehet ütemezni. A fürt felhasználói azonban problémákba merülhetnek a podok üzembe helyezése során, amíg meg nem határoz saját szabályzatokat. Az ajánlott módszer a következő:

* AKS-fürt létrehozása
* Saját podbiztonsági szabályzatok meghatározása
* A podbiztonsági szabályzat funkció engedélyezése

Annak megmutatása érdekében, hogy az alapértelmezett szabályzatok hogyan korlátozzák a podok üzembe helyezését, ebben a cikkben először engedélyezünk egy podbiztonsági szabályzat funkciót, majd létrehozunk egy egyéni szabályzatot.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Viselkedésbeli változások a podok biztonsági szabályzata és a Azure Policy

Az alábbiakban összefoglaljuk a podok biztonsági szabályzata és a Azure Policy.

|Eset| Podbiztonsági szabályzat | Azure Policy |
|---|---|---|
|Telepítés|Podbiztonsági szabályzat funkció engedélyezése |Bővítmény Azure Policy engedélyezése
|Szabályzatok telepítése| Podbiztonsági szabályzat erőforrásának üzembe helyezése| Rendeljen Azure-szabályzatokat az előfizetés vagy az erőforráscsoport hatóköréhez. A Azure Policy bővítmény szükséges a Kubernetes-erőforrásalkalmazások számára.
| Alapértelmezett szabályzatok | Ha a podok biztonsági szabályzata engedélyezve van az AKS-ban, a rendszer az alapértelmezett Emelt szintű és Korlátlan szabályzatokat alkalmazza. | A rendszer nem alkalmaz alapértelmezett szabályzatokat a Azure Policy bővítmény engedélyezésével. Explicit módon engedélyeznie kell a szabályzatokat a Azure Policy.
| Kik hozhatnak létre és rendelhetnek hozzá szabályzatokat? | A fürt rendszergazdája létrehoz egy podbiztonsági szabályzat erőforrást | A felhasználóknak legalább "tulajdonosi" vagy "Erőforrás-házirend-közreműködői" engedéllyel kell rendelkezniük az AKS-fürt erőforráscsoportján. – Az API-n keresztül a felhasználók szabályzatokat rendelhetnek hozzá az AKS-fürt erőforrás-hatókörében. A felhasználónak legalább "tulajdonosi" vagy "Erőforrás-házirend-közreműködői" engedéllyel kell rendelkeznie az AKS-fürterőforráson. – A Azure Portal szabályzatok a felügyeleti csoport/előfizetés/erőforráscsoport szintjén rendelhetők hozzá.
| Szabályzatok mérvadódása| A felhasználók és a szolgáltatásfiókok kifejezett engedélyekkel kell rendelkezniük a podbiztonsági szabályzatok használatában. | A szabályzatok engedélyéhez nincs szükség további hozzárendelésre. Miután hozzárendelt szabályzatokat az Azure-ban, a fürt összes felhasználója használhatja ezeket a szabályzatokat.
| Szabályzatok alkalmazhatósága | A rendszergazdai felhasználó megkerüli a podok biztonsági szabályzatának kényszerítését. | Minden felhasználó (& nem rendszergazda) ugyanazt a szabályzatot látja. A felhasználókon alapuló különleges kis- és A szabályzatalkalmazás kizárható a névtér szintjén.
| Szabályzat hatóköre | A podok biztonsági szabályzata nem névtérbe van térben | A Azure Policy használt megkötési sablonok nem névterek.
| Megtagadási/naplózási/mutation művelet | A podbiztonsági szabályzatok csak a megtagadási műveleteket támogatják. A mutatás a kérések létrehozásakor megadott alapértelmezett értékekkel lehetséges. Az ellenőrzés a frissítési kérések során használhatja.| Azure Policy a naplózási és & műveleteket is támogatja. A mutatáció még nem támogatott, de tervben van.
| Podbiztonsági szabályzat megfelelősége | A podok biztonsági szabályzatának engedélyezése előtt meglévő podok megfelelősége nem látható. A podbiztonsági szabályzatok engedélyezése után létrehozott nem megfelelő podok le vannak tiltva. | Az Azure-szabályzatok alkalmazása előtt meglévő nem megfelelő podok a szabályzatsértések között nének. Az Azure-szabályzatok engedélyezése után létrehozott nem megfelelő podok le vannak tiltva, ha a szabályzatok megtagadási hatással vannak beállítva.
| Szabályzatok megtekintése a fürtön | `kubectl get psp` | `kubectl get constrainttemplate` – A visszaadott szabályzatok mindegyikét.
| Podbiztonsági szabályzat szabványa – Emelt szintű | A szolgáltatás engedélyezésekor alapértelmezés szerint létrejön egy emelt szintű podbiztonsági szabályzat erőforrása. | Az emelt szintű mód nem jelent korlátozást, ezért egyenértékű azzal, mintha nem lenne Azure Policy hozzárendelése.
| [Podbiztonsági szabályzat szabványa – Alapkonfiguráció/alapértelmezett](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | A felhasználó telepíti a pod biztonsági szabályzatának alapkonfiguráció-erőforrását. | Azure Policy beépített [alapkonfiguráció-kezdeményezést biztosít,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) amely az alap podbiztonsági szabályzatnak van leképezve.
| [Podbiztonsági szabályzat szabványa – Korlátozott](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | A felhasználó egy korlátozott podbiztonsági szabályzatú erőforrást telepít. | Azure Policy beépített [korlátozott](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) kezdeményezést biztosít, amely a korlátozott podbiztonsági szabályzatra van leképezve.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Podbiztonsági szabályzat engedélyezése AKS-fürtön

A podok biztonsági szabályzatát az [az aks update][az-aks-update] paranccsal engedélyezheti vagy tilthatja le. Az alábbi példa engedélyezi a podbiztonsági szabályzatot a myResourceGroup nevű erőforráscsoportban a *myAKSCluster* *fürtnéven.*

> [!NOTE]
> Valós használatra ne engedélyezze a podbiztonsági szabályzatot, amíg meg nem határozta a saját egyéni szabályzatát. Ebben a cikkben első lépésként engedélyezi a podbiztonsági szabályzatot, hogy lássa, hogyan korlátozzák az alapértelmezett szabályzatok a podok üzembe helyezését.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Alapértelmezett AKS-szabályzatok

Ha engedélyezi a podbiztonsági szabályzatot, az AKS létrehoz egy emelt szintű nevű *alapértelmezett szabályzatot.* Ne szerkessze vagy távolítsa el az alapértelmezett szabályzatot. Ehelyett hozzon létre saját szabályzatokat, amelyek meghatározzák a szabályozni kívánt beállításokat. Először nézzük meg, milyen hatással vannak ezek az alapértelmezett szabályzatok a podok üzemelő példányaira.

Az elérhető szabályzatok megtekintéséhez használja a [kubectl get psp][kubectl-get] parancsot az alábbi példában látható módon

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Az *emelt szintű* podok biztonsági szabályzata az AKS-fürt összes hitelesített felhasználóján érvényes. Ezt a hozzárendelést a ClusterRoles és a ClusterRoleBindings vezérli. Használja a [kubectl get rolebindings][kubectl-get] parancsot, és keresse meg a *default:privileged:* kötést a *kube-system* névtérben:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Ahogy az a következő tömör kimenetben is látható, a *psp:privileged* ClusterRole minden *system:authenticated* felhasználóhoz hozzá van rendelve. Ez a képesség alapszintű jogosultságot biztosít a saját szabályzatok definiálása nélkül.

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

Mielőtt saját podbiztonsági szabályzatokat hoz létre, fontos megérteni, hogy ezek az alapértelmezett szabályzatok hogyan lépnek interakcióba a felhasználói kérésekkel a podok ütemezése érdekében. A következő néhány szakaszban ütemezünk néhány podot, hogy ezek az alapértelmezett szabályzatok is láthatóak legyen.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tesztfelhasználó létrehozása AKS-fürtben

Alapértelmezés szerint az [az aks get-credentials][az-aks-get-credentials] parancs  használata esetén a rendszer hozzáadja az AKS-fürt rendszergazdai hitelesítő adatait a `kubectl` konfigurációhoz. A rendszergazdai felhasználó megkerüli a podok biztonsági szabályzatának kényszerítését. Ha az AKS Azure Active Directory fürtök integrációját használja, akkor egy nem rendszergazdai felhasználó hitelesítő adataival bejelentkezve láthatja a szabályzatok kényszerítését. Ebben a cikkben létrehozunk egy tesztfelhasználói fiókot a használható AKS-fürtben.

Hozzon létre egy *psp-aks* nevű mintanévteret a teszterőforrásokhoz a [kubectl create namespace paranccsal.][kubectl-create] Ezután hozzon létre egy *nonadmin-user* nevű szolgáltatásfiókot a [kubectl create serviceaccount paranccsal:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Ezután hozzon létre egy RoleBinding szerepkört a *nemmin-felhasználó* számára alapszintű műveletek végrehajtásához a névtérben a [kubectl create rolebinding paranccsal:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Aliasparancsok létrehozása rendszergazdai és nem rendszergazdai felhasználók számára

A normál rendszergazda felhasználó és az előző lépésekben létrehozott nem rendszergazdai felhasználó közötti különbség kiemelése érdekében hozzon létre két `kubectl` parancssori aliast:

* A **kubectl-admin** alias a normál rendszergazdai felhasználóhoz tartozó, hatóköre pedig *a psp-aks névtér.*
* A **kubectl-nonadminuser** alias az előző lépésben létrehozott *nemadmin-felhasználóhoz* készült, és a *psp-aks névtér* hatóköre.

Hozza létre ezt a két aliast az alábbi parancsokban látható módon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Rendszer-jogosultságú pod létrehozásának tesztelése

Először teszteljük, mi történik, ha ütemez egy podot a biztonsági `privileged: true` környezetében. Ez a biztonsági környezet eszkalálja a pod jogosultságait. Az AKS-podok alapértelmezett biztonsági szabályzatait  mutatták be az előző szakaszban, a jogosultsági szabályzatnak el kell tagadni ezt a kérést.

Hozzon létre egy nevű fájlt, `nginx-privileged.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Hozza létre a podot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

A pod nem ütemezve van, ahogy az az alábbi példakimenetben látható:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszt, így nem kell törölnie erőforrásokat, mielőtt továbblép.

## <a name="test-creation-of-an-unprivileged-pod"></a>Nem megfelelő jogosultságú pod létrehozásának tesztelése

Az előző példában a pod specifikációja emelt szintű eszkalálást kért. Ezt a kérést az  alapértelmezett jogosultsági podbiztonsági szabályzat megtagadja, ezért a pod nem ütemezhető. Most próbáljuk meg ugyanezt az NGINX-podot a jogosultság eszkalációs kérése nélkül is futtatni.

Hozzon létre egy nevű fájlt, `nginx-unprivileged.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Hozza létre a podot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod nem ütemezve van, ahogy az az alábbi példakimenetben látható:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszt, így a tovább lépés előtt nem kell törölnie erőforrásokat.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Pod létrehozásának tesztelése egy adott felhasználói környezetben

Az előző példában a tároló rendszerképe automatikusan a root használatával próbálta meg kötni az NGINX-et a 80-as porthoz. Ezt a kérést az  alapértelmezett jogosultsági podbiztonsági szabályzat elutasította, ezért a pod nem indul el. Most próbáljuk meg ugyanazt az NGINX-podot egy adott felhasználói környezetben ( például `runAsUser: 2000` ) futtatni.

Hozzon létre egy nevű fájlt, `nginx-unprivileged-nonroot.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Hozza létre a podot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

A pod nem ütemezve van, ahogy az az alábbi példakimenetben látható:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszt, így a tovább lépés előtt nem kell törölnie erőforrásokat.

## <a name="create-a-custom-pod-security-policy"></a>Egyéni podbiztonsági szabályzat létrehozása

Most, hogy már látta az alapértelmezett podbiztonsági szabályzatok viselkedését, biztosítsunk egy lehetőséget a *nemmin-felhasználó* számára a podok sikeres ütemezésére.

Hozzunk létre egy szabályzatot, amely elutasítja az emelt szintű hozzáférést kérelmező podokat. Más beállítások, például *a runAsUser* vagy *az* engedélyezett kötetek nincsenek explicit módon korlátozva. Az ilyen típusú szabályzat megtagadja az emelt szintű hozzáférésre vonatkozó kéréseket, de egyéb esetben lehetővé teszi, hogy a fürt futtassa a kért podokat.

Hozzon létre egy nevű fájlt, `psp-deny-privileged.yaml` és illessze be a következő YAML-jegyzékfájlt:

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

Hozza létre a szabályzatot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Az elérhető szabályzatok megtekintéséhez használja a [kubectl get psp][kubectl-get] parancsot az alábbi példában látható módon. Hasonlítsa össze a *psp-deny-privileged* szabályzatot az előző példákban a podok létrehozásához érvényesített alapértelmezett jogosultsági szabályzatokkal.  A szabályzat csak *a PRIV* eszkalálás használatát tiltja le. A *psp-deny-privileged* szabályzat nem korlátozza a felhasználót vagy csoportot.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Az egyéni podbiztonsági szabályzat használatának engedélyezése felhasználói fiók számára

Az előző lépésben létrehozott egy podbiztonsági szabályzatot az emelt szintű hozzáférést kérelmező podok elutasításához. A szabályzat csak akkor használható, ha létrehoz egy *szerepkört* vagy *egy ClusterRole szerepkört.* Ezután társítsa ezen szerepkörök valamelyikét egy *RoleBinding* vagy *ClusterRoleBinding használatával.*

Ebben a példában hozzon létre egy  ClusterRole-t, amely lehetővé teszi az előző lépésben létrehozott *psp-deny-privileged* szabályzat használatát. Hozzon létre egy nevű fájlt, `psp-deny-privileged-clusterrole.yaml` és illessze be a következő YAML-jegyzékfájlt:

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

Hozza létre a ClusterRole-t a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Most hozzon létre egy ClusterRoleBinding halmazt az előző lépésben létrehozott ClusterRole használatára. Hozzon létre egy nevű fájlt, `psp-deny-privileged-clusterrolebinding.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
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

Hozzon létre egy ClusterRoleBinding parancsot a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> A cikk első lépésében a podbiztonsági szabályzat funkció engedélyezve volt az AKS-fürtön. Az ajánlott eljárás az volt, hogy a podok biztonsági szabályzatának funkcióját csak a saját szabályzatok meghatározása után engedélyezze. Ebben a szakaszban engedélyezheti a podok biztonsági szabályzatának funkcióját. Egy vagy több egyéni szabályzat van meghatározva, és a felhasználói fiókok hozzá vannak társítva ezekhez a szabályzatokhoz. Most már biztonságosan engedélyezheti a podok biztonsági szabályzatának funkcióját, és minimalizálhatja az alapértelmezett szabályzatok által okozott problémákat.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Nem megfelelő jogosultságú pod létrehozásának tesztelése újra

Most, hogy alkalmazva van az egyéni podbiztonsági szabályzat, és a felhasználói fiók kötése a szabályzat használatára, próbáljon meg ismét létrehozni egy nem rendszer-jogosultságú podot. Ugyanezen jegyzékfájl `nginx-privileged.yaml` használatával hozza létre a podot a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod sikeresen ütemezve van. Ha a [kubectl get pods][kubectl-get] paranccsal ellenőrzi a pod állapotát, a pod *fut:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Ez a példa bemutatja, hogyan hozhat létre egyéni podbiztonsági szabályzatokat, amelyek meghatározzák az AKS-fürthöz való hozzáférést a különböző felhasználók vagy csoportok számára. Az alapértelmezett AKS-szabályzatok szigorú ellenőrzéssel határozzák meg, hogy milyen podok futnak, ezért hozzon létre saját egyéni szabályzatokat a szükséges korlátozások helyes meghatározásához.

Törölje az NGINX nem emelt jogosultságú podot a [kubectl delete][kubectl-delete] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A podbiztonsági szabályzat letiltásához használja [újra az az aks update][az-aks-update] parancsot. A következő példa letiltja a podbiztonsági szabályzatot a myResourceGroup nevű erőforráscsoportban a *myAKSCluster* *fürtnéven:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ezután törölje a ClusterRole és a ClusterRoleBinding adatokat:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Törölje a biztonsági szabályzatot a [kubectl delete paranccsal,][kubectl-delete] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Végül törölje a *psp-aks* névteret:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt mutatta be, hogyan hozhat létre olyan podbiztonsági szabályzatot, amely megakadályozza az emelt szintű hozzáférés használatát. A szabályzatok számos olyan funkciót kényszeríthet, mint például a kötet típusa vagy a futtatás-felhasználó. Az elérhető lehetőségekkel kapcsolatos további információkért tekintse meg a [Kubernetes-podok biztonsági][kubernetes-policy-reference]szabályzatának referencia-dokumentumokban található információkat.

További információ a podok hálózati forgalmának korlátozásával kapcsolatban: Secure traffic between pods using network policies in AKS (A podok közötti adatforgalom biztonságossá használata hálózati szabályzatokkal az [AKS-ban).][network-policies]

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
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
