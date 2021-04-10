---
title: CoreDNS testreszabása az Azure Kubernetes szolgáltatáshoz (ak)
description: Megtudhatja, hogyan szabhatja testre a CoreDNS az altartományok hozzáadásához vagy az egyéni DNS-végpontok kibővítéséhez az Azure Kubernetes Service (ak) használatával
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368681"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>A CoreDNS testreszabása Azure Kubernetes Service-szel

Az Azure Kubernetes Service (ak) a [CoreDNS][coredns] -projektet használja a fürt DNS-kezeléséhez és feloldásához az összes *1,12. x* és újabb fürtök esetében. Korábban a rendszer a Kube-DNS-projektet használta. Ez a Kube-DNS-projekt már elavult. A CoreDNS testreszabásával és Kubernetes kapcsolatos további információkért tekintse meg a [hivatalos upstream dokumentációját][corednsk8s].

Mivel az AK felügyelt szolgáltatás, nem módosíthatja a CoreDNS (a *alapfájl*) fő konfigurációját. Ehelyett Kubernetes- *ConfigMap* használ az alapértelmezett beállítások felülbírálásához. Az alapértelmezett AK-CoreDNS ConfigMaps megtekintéséhez használja az `kubectl get configmaps --namespace=kube-system coredns -o yaml` parancsot.

Ebből a cikkből megtudhatja, hogyan használhatja a ConfigMaps-t az CoreDNS alapszintű, az AK-ban található testreszabási lehetőségei Ez a megközelítés különbözik a CoreDNS más kontextusokban való konfigurálásának, például a alapfájl használatával. Ellenőrizze, hogy a futtatott CoreDNS verziója módosul-e a verziók között.

> [!NOTE]
> `kube-dns` a Kubernetes konfigurációs térképén keresztül különböző [testreszabási lehetőségek][kubednsblog] is elérhetők. A CoreDNS visszafelé **nem** kompatibilis a Kube-DNS szolgáltatással. A korábban használt testreszabásokat frissíteni kell a CoreDNS-mel való használatra.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Az alábbi példákhoz hasonló konfiguráció létrehozásakor az adatszakaszban található neveknek a *. Server* vagy a *. felülbírálás* *értékkel* kell végződnie. Ez az elnevezési konvenció az alapértelmezett AK-CoreDNS Configmap van definiálva, amelyet a parancs használatával tekinthet meg `kubectl get configmaps --namespace=kube-system coredns -o yaml` .

## <a name="what-is-supportedunsupported"></a>Támogatás/nem támogatott

A beépített CoreDNS beépülő modulok támogatottak. A bővítmények és a harmadik féltől származó beépülő modulok nem támogatottak.

## <a name="rewrite-dns"></a>DNS újraírása

Az egyik forgatókönyv az, ha az on-the-fly DNS-név újraírását végzi. A következő példában cserélje le a `<domain to be written>` nevet a saját teljes tartománynevére. Hozzon létre egy nevű fájlt `corednsms.yaml` , és illessze be a következő példát:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Ha egy DNS-kiszolgálóra irányít át, például a CoreDNS szolgáltatás IP-címére, a DNS-kiszolgálónak képesnek kell lennie az újraírható tartománynév feloldására.

Hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f corednsms.yaml
```

A testreszabások alkalmazásának ellenőrzéséhez használja a [kubectl Get configmaps][kubectl-get] , és adja meg a *coredns-Custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Most kényszerítse a CoreDNS a ConfigMap újratöltését. A [kubectl delete Pod][kubectl delete] parancs nem ártalmas, és nem okoz leállási időt. A `kube-dns` hüvelyek törlődnek, a Kubernetes ütemező pedig újra létrehozza őket. Ezek az új hüvelyek a TTL-érték változását tartalmazzák.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> A fenti parancs helyes. A váltás közben `coredns` a telepítés a **Kube-DNS-** név alatt található.

## <a name="custom-forward-server"></a>Egyéni továbbító kiszolgáló

Ha meg kell adnia egy továbbító kiszolgálót a hálózati forgalomhoz, létrehozhat egy ConfigMap a DNS testreszabásához. A következő példában frissítse a `forward` nevet és a internetcímet a saját környezete értékeivel. Hozzon létre egy nevű fájlt `corednsms.yaml` , és illessze be a következő példát:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Ahogy az előző példákban is, hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS a ConfigMap újratöltésére a [kubectl delete Pod][kubectl delete] használatával a Kubernetes Scheduler létrehozásához:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Egyéni tartományok használata

Előfordulhat, hogy olyan egyéni tartományokat szeretne konfigurálni, amelyek csak belsőleg oldhatók fel. Előfordulhat például, hogy fel szeretné oldani a *puglife. local* egyéni tartományt, amely nem érvényes legfelső szintű tartomány. Egyéni tartományi ConfigMap nélkül az AK-fürt nem tudja feloldani a címeket.

Az alábbi példában frissítse az egyéni tartományt és az IP-címet, hogy a forgalmat a saját környezetéhez tartozó értékekre irányítsa. Hozzon létre egy nevű fájlt `corednsms.yaml` , és illessze be a következő példát:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Ahogy az előző példákban is, hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS a ConfigMap újratöltésére a [kubectl delete Pod][kubectl delete] használatával a Kubernetes Scheduler létrehozásához:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Helyettes tartományok

A CoreDNS a helyettes tartományok konfigurálására is használható. Az alábbi példában frissítse az egyéni tartományokat és IP-címeket a saját környezete értékeivel. Hozzon létre egy nevű fájlt `corednsms.yaml` , és illessze be a következő példát:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Ahogy az előző példákban is, hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS a ConfigMap újratöltésére a [kubectl delete Pod][kubectl delete] használatával a Kubernetes Scheduler létrehozásához:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Gazdagépek beépülő modul

Mivel az összes beépített beépülő modul támogatott, az azt jelenti, hogy a CoreDNS- [gazdagépek][coredns hosts] beépülő modul a testreszabáshoz is elérhető:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>DNS-lekérdezési hibakeresés naplózásának engedélyezése 

A DNS-lekérdezések naplózásának engedélyezéséhez alkalmazza a következő konfigurációt a coredns-Custom ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk néhány példát mutat be a CoreDNS testreszabásához. A CoreDNS projekttel kapcsolatos további információkért tekintse meg [a CoreDNS felső projektje lapot][coredns].

Az alapvető hálózati fogalmakkal kapcsolatos további tudnivalókért tekintse meg a [hálózati fogalmak az AK-beli alkalmazásokhoz][concepts-network]című témakört.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
