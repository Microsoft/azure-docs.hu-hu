---
title: Tanúsítványok elforgatása Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan forgathatja a tanúsítványokat egy Azure Kubernetes Service -fürtben.
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 6baad681a9d629c397c53ab90057cc5746fc3b85
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776014"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Tanúsítványok elforgatása Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) tanúsítványokat használ a számos összetevővel való hitelesítéshez. Biztonsági vagy szabályzati okokból időnként szükség lehet a tanúsítványok váltogatára. Előfordulhat például, hogy van egy szabályzata, amely 90 naponta váltja fel az összes tanúsítványt.

Ez a cikk bemutatja, hogyan forgathatja le az AKS-fürtben található tanúsítványokat.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.77-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-tanúsítványok, hitelesítésszolgáltató és szolgáltatásfiókok

Az AKS a következő tanúsítványokat, hitelesítésszolgáltatókat és szolgáltatásfiókokat hozza létre és használja:

* Az AKS API-kiszolgáló egy fürt ca nevű hitelesítésszolgáltatót (CA) hoz létre.
* Az API-kiszolgáló fürt-hitelesítésszolgáltatóval rendelkezik, amely tanúsítványokat aláír az API-kiszolgáló és a kubelets közötti egyútos kommunikációhoz.
* Minden kubelet létrehoz egy tanúsítvány-aláírási kérelmet (CSR), amelyet a fürt hitelesítésszolgáltatója írt alá a kubelet és az API-kiszolgáló közötti kommunikációhoz.
* Az API-gyűjtő a fürt hitelesítésszolgáltatóját használja tanúsítványok kiállításához más API-okkal való kommunikációhoz. Az API-gyűjtő saját hitelesítésszolgáltatóval is lehet a tanúsítványok kiállításához, de jelenleg a fürt hitelesítésszolgáltatóját használja.
* Minden csomópont egy Szolgáltatásfiók (SA) jogkivonatot használ, amelyet a fürt hitelesítésszolgáltatója írt alá.
* Az `kubectl` ügyfél rendelkezik az AKS-fürtkel való kommunikációhoz szükséges tanúsítvánnyal.

> [!NOTE]
> A 2019 márciusa előtt létrehozott AKS-fürtök két év után lejáró tanúsítványokkal is járnak. A 2019 márciusa után létrehozott fürtök vagy a tanúsítványokat felforgató fürtök fürt ca tanúsítványai 30 év után lejárnak. Minden más tanúsítvány két év után lejár. A fürt létrejöttének ellenőrzéséhez a használatával tekintse meg a `kubectl get nodes` csomópontkészletek  korát.
> 
> Emellett ellenőrizheti a fürt tanúsítványának lejárati dátumát is. A következő Bash-parancs például megjeleníti a *myAKSCluster* fürt tanúsítványának részleteit.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Fürttanúsítványok forgása

> [!WARNING]
> A tanúsítványok a használatával való rotása akár 30 perc leállást is okozhat az `az aks rotate-certs` AKS-fürtön.

Az [az aks get-credentials használatával][az-aks-get-credentials] jelentkezzen be az AKS-fürtbe. Ez a parancs az ügyfél tanúsítványát is letölti és `kubectl` konfigurálja a helyi gépen.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

A használatával a fürt összes tanúsítványát, hitelesítésszolgáltatását és `az aks rotate-certs` hitelesítésszolgáltatás-engedélyét elforgathatja.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> A folyamat akár 30 percet `az aks rotate-certs` is igénybe vehet. Ha a parancs a művelet befejezése előtt meghiúsul, a paranccsal ellenőrizze, hogy a fürt állapota `az aks show` *Tanúsítványforgatva.* Ha a fürt hibás állapotban van, futtassa újra a `az aks rotate-certs` tanúsítványokat a tanúsítványok újraforgatásaként.

Egy parancs futtatásával ellenőrizze, hogy a régi tanúsítványok már nem `kubectl` érvényesek.) Mivel nem frissítette a által használt `kubectl` tanúsítványokat, hibaüzenet jelenik meg.  Például:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Frissítse a által használt `kubectl` tanúsítványt a `az aks get-credentials` futtatásával.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Egy parancs futtatásával ellenőrizze, hogy a tanúsítványok frissítve `kubectl` vannak-e, ami most már sikeres lesz. Például:

```console
kubectl get no
```

> [!NOTE]
> Ha az AKS-t futtatja, például az [Azure Dev Spacest,][dev-spaces]akkor előfordulhat, hogy az ezekhez a szolgáltatásokhoz kapcsolódó tanúsítványokat [is][dev-spaces-rotate] frissítenie kell.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt mutatta be, hogyan lehet automatikusan elforgatni a fürt tanúsítványait, hitelesítésszolgáltatásait és hitelesítésszolgáltatás-szerződéseit. Az ajánlott AKS-biztonsági eljárásokkal kapcsolatos további információkért tekintse meg a fürtbiztonsággal és -frissítéssel kapcsolatos ajánlott eljárásokat az Azure Kubernetes Service [(AKS)][aks-best-practices-security-upgrades] oldalon.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
