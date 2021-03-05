---
title: A fürt biztonságossá tétele a Azure Policy használatával
description: A Azure Policy használatával biztonságossá teheti az Azure Kubernetes-szolgáltatás (ak) fürtjét.
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193440"
---
# <a name="secure-your-cluster-with-azure-policy"></a>A fürt biztonságossá tétele Azure Policy

Az Azure Kubernetes-szolgáltatás (ak) fürtje biztonságának javítása érdekében Azure Policy segítségével alkalmazhatja és kényszerítheti a beépített biztonsági szabályzatokat a fürtön. [Azure Policy][azure-policy] segít a szervezeti szabványok betartatásában és a megfelelőségi követelmények kiértékelésében. Miután telepítette a [Azure Policy-bővítményt az AK][kubernetes-policy-reference]-hoz, alkalmazhat egyes házirend-definíciókat vagy szabályzat-definíciókat (más néven policysets) a fürtre. Az AK-szabályzatok és a kezdeményezési definíciók teljes listájáért lásd: [Azure Policy beépített definíciók az AK-][aks-policies] hoz.

Ez a cikk bemutatja, hogyan alkalmazhatja a szabályzat-definíciókat a fürtre, és ellenőrizheti, hogy a hozzárendelések kényszerítve vannak-e.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő AK-fürt. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].
- Az AK-fürtön telepített Azure Policy-bővítmény. [A Azure Policy bővítmény telepítéséhez][azure-policy-addon]kövesse az alábbi lépéseket.

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Beépített szabályzat-definíció vagy kezdeményezés kiosztása

Házirend-definíció vagy kezdeményezés alkalmazásához használja a Azure Portal.

1. Navigáljon a Azure Policy szolgáltatáshoz Azure Portal.
1. A Azure Policy lap bal oldali ablaktábláján válassza a **definíciók** lehetőséget.
1. A **Kategóriák** területen válassza a lehetőséget `Kubernetes` .
1. Válassza ki az alkalmazni kívánt szabályzat-definíciót vagy kezdeményezést. Ebben a példában válassza ki a `Kubernetes cluster pod security baseline standards for Linux-based workloads` kezdeményezést.
1. Válassza a **Hozzárendelés** elemet.
1. Állítsa a **hatókört** az AK-fürt erőforráscsoporthoz a Azure Policy bővítmény engedélyezésével.
1. Válassza a **Parameters (paraméterek** ) lapot, és frissítse a **effektust** a alkalmazásból az alapkonfigurációt `audit` `deny` sértő új központi telepítések letiltásához. További névtereket is hozzáadhat a kiértékelésből való kizáráshoz. Ebben a példában tartsa meg az alapértelmezett értékeket.
1. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget a szabályzat-hozzárendelés elküldéséhez.

## <a name="validate-a-azure-policy-is-running"></a>Azure Policy futtatásának ellenőrzése

A következő futtatásával erősítse meg, hogy a szabályzat-hozzárendelések a fürtön vannak alkalmazva:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> A szabályzat-hozzárendelések akár 20 percet is igénybe vehetnek az egyes fürtökbe [való szinkronizáláshoz][azure-policy-assign-policy] .

A kimenetnek a következőhöz hasonlónak kell lennie:

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

### <a name="validate-rejection-of-a-privileged-pod"></a>Emelt szintű Pod elutasításának ellenőrzése

Első lépésként tesztelje, mi történik, ha egy Pod-t a biztonsági környezetével ütemezhet `privileged: true` . Ez a biztonsági környezet a pod jogosultságait bővíti. A kezdeményezés nem engedélyezi a Kiemelt hüvelyeket, ezért a rendszer megtagadja a kérést, mert az üzembe helyezés elutasításra kerül.

Hozzon létre egy nevű fájlt `nginx-privileged.yaml` , és illessze be a következő YAML-jegyzékbe:

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

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f nginx-privileged.yaml
```

A várt módon a pod nem ütemezhető, ahogy az a következő példában látható:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

A pod nem éri el az ütemezési szakaszt, ezért nincs szükség a törlendő erőforrásokra, mielőtt továbblép.

### <a name="test-creation-of-an-unprivileged-pod"></a>Nem privilegizált Pod-létrehozási teszt létrehozása

Az előző példában a tároló képe automatikusan megpróbálta a root használatával kötni a NGINX-et a 80-es portra. A házirend-kezdeményezés elutasította ezt a kérést, így a pod nem indul el. Próbálja ki most ugyanezt az NGINX Pod-t, emelt szintű hozzáférés nélkül.

Hozzon létre egy nevű fájlt `nginx-unprivileged.yaml` , és illessze be a következő YAML-jegyzékbe:

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

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f nginx-unprivileged.yaml
```

A pod sikeresen ütemezve. Ha a [kubectl Get hüvely][kubectl-get] parancs használatával tekinti meg a pod állapotát, a pod a következőket *futtatja*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Ez a példa azt az alapkonfigurációt mutatja be, amely csak a gyűjtemény házirendjeit sértő központi telepítéseket érinti. Az engedélyezett központi telepítések továbbra is működőképesek.

A [kubectl delete][kubectl-delete] paranccsal törölje az NGINX unprivilegizált Pod-t, és adja meg a YAML-jegyzék nevét:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Házirend vagy kezdeményezés letiltása

Az alapterv-kezdeményezés eltávolítása:

1. Navigáljon a Azure Portal házirend paneljére.
1. Válassza ki a **hozzárendelések** elemet a bal oldali ablaktáblán.
1. Kattintson a kezdeményezés melletti **...** gombra. `Kubernetes cluster pod security baseline standards for Linux-based workloads`
1. Válassza a **hozzárendelés törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Policy működéséről:

- [Azure Policy áttekintése][azure-policy]
- [Azure Policy kezdeményezések és házirendek az AK-ban][aks-policies]
- Távolítsa el a [Azure Policy bővítményt][azure-policy-addon-remove].

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
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
