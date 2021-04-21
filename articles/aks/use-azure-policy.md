---
title: Fürt Azure Policy a fürt biztonságossá Azure Policy használatával
description: A Azure Policy (AKS Azure Kubernetes Service fürt biztonságossá Azure Policy használhatja.
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777724"
---
# <a name="secure-your-cluster-with-azure-policy"></a>A fürt biztonságossá Azure Policy

A Azure Kubernetes Service (AKS) fürt biztonságának növelése érdekében beépített biztonsági szabályzatokat alkalmazhat és érvényesíthet a fürtön a Azure Policy. [Azure Policy][azure-policy] segít a szervezeti szabványok érvényesítésében és a megfelelőség nagy léptékű értékelésében. Az [AKS Azure Policy][kubernetes-policy-reference]bővítmény telepítése után egyéni szabályzatdefiníciókat vagy kezdeményezésnek nevezett szabályzatdefiníció-csoportokat (más néven szabályzatkészleteket) alkalmazhat a fürtön. Az [AKS Azure Policy][aks-policies] és kezdeményezési definíciók teljes listájáért tekintse meg az AKS beépített definícióit.

Ez a cikk bemutatja, hogyan alkalmazhat szabályzatdefiníciókat a fürtre, és hogyan ellenőrizheti, hogy a hozzárendelések kényszerítve vannak-e.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő AKS-fürt. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]
- Az Azure Policy AKS-fürtre telepített AKS-hez szükséges Azure Policy bővítmény. Kövesse az [alábbi lépéseket a Azure Policy telepítéséhez.][azure-policy-addon]

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Beépített szabályzatdefiníció vagy kezdeményezés hozzárendelése

Szabályzatdefiníció vagy kezdeményezés alkalmazáshoz használja a Azure Portal.

1. Lépjen a Azure Policy a Azure Portal.
1. A bal oldali panelen válassza Azure Policy **Definíciók lehetőséget.**
1. A **Kategóriák alatt válassza** a `Kubernetes` lehetőséget.
1. Válassza ki az alkalmazni kívánt szabályzatdefiníciót vagy kezdeményezést. Ebben a példában válassza ki a `Kubernetes cluster pod security baseline standards for Linux-based workloads` kezdeményezést.
1. Válassza a **Hozzárendelés** elemet.
1. Állítsa **a Hatókört** az AKS-fürt erőforráscsoportra úgy, hogy Azure Policy bővítmény engedélyezve legyen.
1. Válassza ki **a Paraméterek lapot,** és frissítse az **effektust** értékről -re, hogy megakadályozza az alapkonfigurációt megsértő új `audit` `deny` üzemelő példányokat. További névtereket is hozzáadhat, amelyek ki vannak zárva a kiértékelésből. Ebben a példában tartsa meg az alapértelmezett értékeket.
1. Válassza **az Áttekintés + létrehozás,** majd a **Létrehozás** lehetőséget a szabályzat-hozzárendelés elküld létrehozásához.

## <a name="validate-a-azure-policy-is-running"></a>Futó Azure Policy ellenőrzése

A következő futtatásával ellenőrizze, hogy a szabályzat-hozzárendelések alkalmazva vannak-e a fürtre:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> A szabályzat-hozzárendelések szinkronizálása az egyes fürtökbe [akár 20][azure-policy-assign-policy] percet is igénybe vehet.

A kimenetnek a következő módon kell lennie:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Rendszer-jogosultságú pod elutasításának ellenőrzése

Először teszteljük, mi történik, ha ütemez egy podot a biztonsági `privileged: true` környezetében. Ez a biztonsági környezet eszkalálja a pod jogosultságait. A kezdeményezés nem tiltja a kiemelt podokat, így a rendszer megtagadja a kérést, ami az üzembe helyezés elutasítását eredményezi.

Hozzon létre egy nevű fájlt, `nginx-privileged.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Hozza létre a podot [a kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f nginx-privileged.yaml
```

A vártnak megfelelően a pod nem ütemezve van, ahogy az az alábbi példakimenetben látható:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

A pod nem éri el az ütemezési szakaszt, így nem kell törölnie erőforrásokat, mielőtt továbblép.

### <a name="test-creation-of-an-unprivileged-pod"></a>Nem megfelelő jogosultságú pod létrehozásának tesztelése

Az előző példában a tároló rendszerképe automatikusan a gyökér használatával próbálta meg kötni az NGINX-et a 80-as porthoz. Ezt a kérést a szabályzat kezdeményezés elutasította, ezért a pod nem indul el. Próbáljuk meg most már ugyanazt az NGINX-podot rendszer-jogosultsággal rendelkező hozzáférés nélkül is futtatni.

Hozzon létre egy nevű fájlt, `nginx-unprivileged.yaml` és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Hozza létre a podot a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f nginx-unprivileged.yaml
```

A pod sikeresen ütemezve van. Ha a [kubectl get pods][kubectl-get] paranccsal ellenőrzi a pod állapotát, a pod *fut:*

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Ez a példa az alapterv kezdeményezést mutatja be, amely csak a gyűjtemény szabályzatait megsértő üzemelő példányokat érinti. Az engedélyezett üzemelő példányok továbbra is működnek.

Törölje az NGINX nem emelt jogosultságú podot a [kubectl delete][kubectl-delete] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Szabályzat vagy kezdeményezés letiltása

Az alapterv kezdeményezés eltávolítása:

1. Lépjen a Szabályzat panelre a Azure Portal.
1. A **bal oldali panelen** válassza a Hozzárendelések lehetőséget.
1. Kattintson **a kezdeményezés** melletti ... `Kubernetes cluster pod security baseline standards for Linux-based workloads` gombra.
1. Válassza **a Hozzárendelés törlése lehetőséget.**

## <a name="next-steps"></a>Következő lépések

További információ a Azure Policy működése:

- [Azure Policy áttekintése][azure-policy]
- [Azure Policy AKS-hez][aks-policies]
- Távolítsa el [Azure Policy bővítményt.][azure-policy-addon-remove]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
