---
title: Tanúsítványok elforgatása az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan forgathatja el a tanúsítványokat egy Azure Kubernetes-szolgáltatási (ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: fa26762c54ad54835b174b8d814a2e77cb38b885
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619035"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Tanúsítványok elforgatása az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes Service (ak) tanúsítványokat használ a számos összetevőjével való hitelesítéshez. Időnként előfordulhat, hogy ezeket a tanúsítványokat biztonsági vagy házirendi okokból kell elforgatnia. Előfordulhat például, hogy az összes tanúsítvány 90 naponkénti elforgatására vonatkozó szabályzattal rendelkezik.

Ez a cikk bemutatja, hogyan forgathatja el a tanúsítványokat az AK-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.77 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AK-tanúsítványok, hitelesítésszolgáltatók és szolgáltatásfiókok

Az AK a következő tanúsítványokat, hitelesítésszolgáltatókat és szolgáltatásfiókokat hozza létre és használja:

* Az AK API-kiszolgáló létrehoz egy, a fürt HITELESÍTÉSSZOLGÁLTATÓjának nevezett hitelesítésszolgáltatót (CA).
* Az API-kiszolgáló rendelkezik egy fürt HITELESÍTÉSSZOLGÁLTATÓval, amely az API-kiszolgáló és a kubelets közötti egyirányú kommunikációhoz tartozó tanúsítványokat aláírja.
* Minden kubelet létrehoz egy tanúsítvány-aláírási kérelmet (CSR) is, amelyet a fürt HITELESÍTÉSSZOLGÁLTATÓja aláír a kubelet és az API-kiszolgáló közötti kommunikációhoz.
* Az API-gyűjtő a fürt HITELESÍTÉSSZOLGÁLTATÓját használja a más API-kkal való kommunikációhoz szükséges tanúsítványok kiállítására. Az API-gyűjtő rendelkezhet saját HITELESÍTÉSSZOLGÁLTATÓval is a tanúsítványok kiállításához, de jelenleg a fürt HITELESÍTÉSSZOLGÁLTATÓját használja.
* Mindegyik csomópont egy szolgáltatásfiók-(SA-) tokent használ, amelyet a fürt HITELESÍTÉSSZOLGÁLTATÓja aláír.
* Az `kubectl` ügyfél rendelkezik egy tanúsítvánnyal az AK-fürttel való kommunikációhoz.

> [!NOTE]
> A március 2019 előtt létrehozott AK-fürtökhöz két év után járó tanúsítványok tartoznak. A március 2019-ig vagy bármely, a tanúsítvánnyal elforgatott fürttel létrehozott fürtnek 30 év után lejár a fürt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa. Minden más tanúsítvány két év után lejár. A fürt létrejöttének ellenőrzéséhez használja a `kubectl get nodes` következőt: a Node-készletek *kora* .
> 
> Emellett a fürt tanúsítványának lejárati dátumát is megtekintheti. A következő bash-parancs például megjeleníti a *myAKSCluster* -fürthöz tartozó tanúsítvány részleteit.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>A fürt tanúsítványainak elforgatása

> [!WARNING]
> A tanúsítványok használatával történő elforgatása `az aks rotate-certs` akár 30 percet is igénybe vehet az AK-fürthöz.

Az [az AK Get-hitelesítő adatok][az-aks-get-credentials] használatával jelentkezzen be az AK-fürtbe. Ez a parancs az ügyféltanúsítványt is letölti és konfigurálja a `kubectl` helyi gépen.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Ezzel a paranccsal az `az aks rotate-certs` összes tanúsítvány, hitelesítésszolgáltató és SAs elforgatható a fürtön.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> A befejezésig akár 30 percet is igénybe vehet `az aks rotate-certs` . Ha a parancs végrehajtása nem sikerül, a használatával `az aks show` ellenőrizheti, hogy a fürt állapota a *tanúsítvány elforgatása*-e. Ha a fürt hibás állapotban van, futtassa újra a `az aks rotate-certs` tanúsítványokat újra.

A parancs futtatásával ellenőrizze, hogy a régi tanúsítványok már nem érvényesek-e `kubectl` . Mivel nem frissítette a által használt tanúsítványokat `kubectl` , hibaüzenet jelenik meg.  Például:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Frissítse a által használt tanúsítványt a `kubectl` futtatásával `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Ellenőrizze, hogy a tanúsítványok frissítve lettek-e egy parancs futtatásával `kubectl` , amely mostantól sikeres lesz. Például:

```console
kubectl get no
```

> [!NOTE]
> Ha olyan szolgáltatásokkal rendelkezik, amelyek az AK-on felül futnak, például az [Azure dev Spaces][dev-spaces]szolgáltatást, előfordulhat, hogy [frissítenie kell a szolgáltatásokhoz kapcsolódó tanúsítványokat][dev-spaces-rotate] is.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt mutatja be, hogyan lehet automatikusan elforgatni a fürt tanúsítványait, hitelesítésszolgáltatóit és SAs-adatait. A biztonsági [és az Azure Kubernetes szolgáltatásban (ak) elérhető ajánlott eljárások][aks-best-practices-security-upgrades] további információkat találnak az AK biztonsággal kapcsolatos ajánlott eljárásairól.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
