---
title: Computer Vision tároló használata a Kubernetes és a Helm használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezheti üzembe a Computer Vision tárolót a Kubernetes és a Helm használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9a8e0dde8b24c39180a584c26af725ab82ea0176
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907108"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Computer Vision tároló használata a Kubernetes és a Helm használatával

Az Computer Vision tárolók helyszíni kezelésének egyik lehetősége a Kubernetes és a Helm használata. Egy Computer Vision-tároló rendszerképének definiálásához a Kubernetes és a Helm használatával hozzunk létre egy Kubernetes-csomagot. Ez a csomag egy helyszíni Kubernetes-fürtön lesz üzembe helyezve. Végezetül megismerheti az üzembe helyezett szolgáltatások tesztelésének módját. További információ a Docker-tárolók Kubernetes-előkészítés nélküli futtatásáról: [Computer Vision tárolók telepítése és futtatása](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Előfeltételek

Computer Vision tárolók helyszíni használata előtt a következő előfeltételek szükségesek:

| Kötelező | Cél |
|----------|---------|
| Azure-fiók | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot][free-azure-account], mielőtt hozzákezd. |
| Kubernetes CLI | A megosztott hitelesítő adatok a tároló-beállításjegyzékből való kezeléséhez a [KUBERNETES CLI][kubernetes-cli] szükséges. A Kubernetes a Helm előtt is szükséges, amely a Kubernetes csomagkezelő. |
| Helm parancssori felület | Telepítse a [Helm CLI][helm-install]-t, amely a Helm-diagram (Container Package Definition) telepítéséhez használatos. |
| Erőforrás Computer Vision |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Computer Vision** erőforrás és a hozzá tartozó API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}**: a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}**: az **Áttekintés** lapon megadott végpont|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kapcsolódás a Kubernetes-fürthöz

A gazdaszámítógépnek várhatóan rendelkezésre áll egy Kubernetes-fürt. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy [Kubernetes-fürtöt](../../aks/tutorial-kubernetes-deploy-cluster.md) a Kubernetes-fürtök gazdagépre történő központi telepítésének fogalmi megismeréséhez. A központi telepítésekről a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)talál további információt.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-hitelesítő adatok megosztása a Kubernetes-fürttel

Ha engedélyezni szeretné a Kubernetes-fürt számára a `docker pull` konfigurált rendszerkép (eke) t a `containerpreview.azurecr.io` tároló-beállításjegyzékből, át kell adnia a Docker hitelesítő adatait a fürtbe. Az [`kubectl create`][kubectl-create] alábbi parancs végrehajtásával hozzon létre egy *Docker-beállításjegyzékbeli titkot* a tároló beállításjegyzék-hozzáférési előfeltétele alapján megadott hitelesítő adatok alapján.

A választható parancssori felületen futtassa a következő parancsot. Ne felejtse el lecserélni a `<username>` , a `<password>` és a `<email-address>` értékét a tároló beállításjegyzékbeli hitelesítő adataival.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Ha már rendelkezik hozzáféréssel a `containerpreview.azurecr.io` tároló-beállításjegyzékhez, az általános jelző használatával létrehozhat egy titkos Kubernetes. Vegye figyelembe a következő parancsot, amely a Docker-konfiguráció JSON-fájlján fut.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A rendszer a következő kimenetet nyomtatja ki a konzolra, amikor a titkos kulcs sikeresen létrejött.

```console
secret "containerpreview" created
```

Annak ellenőrzéséhez, hogy létrejött-e a titkos kulcs, hajtsa végre a jelölőt a következővel: [`kubectl get`][kubectl-get] `secrets` .

```console
kubectl get secrets
```

A `kubectl get secrets` kinyomtatja az összes beállított titkot.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>A Helm-diagram értékeinek konfigurálása üzembe helyezéshez

Először hozzon létre egy *READ*nevű mappát. Ezután illessze be a következő YAML-tartalmat egy nevű új fájlba `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

A Helm diagram alapértelmezett értékeinek konfigurálásához másolja és illessze be a következő YAML egy nevű fájlba `values.yaml` . Cserélje le a `# {ENDPOINT_URI}` és a `# {API_KEY}` megjegyzéseket a saját értékeire. Szükség esetén konfigurálja a resultExpirationPeriod, a Redis és a RabbitMQ.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Ha a `billing` és az `apikey` értékek nincsenek megadva, a szolgáltatások 15 perc elteltével lejárnak. Hasonlóképpen, az ellenőrzés sikertelen, mert a szolgáltatások nem érhetők el.
> 
> - Ha több olvasási tárolót helyez üzembe egy terheléselosztó mögött, például a Docker-összeállítás vagy a Kubernetes alatt, külső gyorsítótárral kell rendelkeznie. Mivel előfordulhat, hogy a feldolgozó tároló és a GET kérelem tárolója nem azonos, a külső gyorsítótár tárolja az eredményeket, és megosztja őket a tárolók között. A gyorsítótár-beállításokkal kapcsolatos további információkért lásd: [Computer Vision Docker-tárolók konfigurálása](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).
>

Hozzon létre egy *templates* mappát az *olvasási* könyvtár alatt. Másolja és illessze be a következő YAML egy nevű fájlba `deployment.yaml` . A `deployment.yaml` fájl Helm-sablonként fog szolgálni.

> A sablonok jegyzékfájlokat hoznak, amelyek a Kubernetes által értelmezhető YAML formázottak. [– Helm diagram sablonjának útmutatója][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Ugyanebben a *sablonok* mappában másolja és illessze be a következő segítő függvényeket a alkalmazásba `helpers.tpl` . `helpers.tpl` a Helm-sablon létrehozásához használható hasznos függvények meghatározása.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
A sablon megadja a terheléselosztó szolgáltatást és a tároló/rendszerkép üzembe helyezését olvasásra.

### <a name="the-kubernetes-package-helm-chart"></a>A Kubernetes-csomag (Helm-diagram)

A *Helm diagram* tartalmazza azt a konfigurációt, amelynek a Docker-rendszerképe a `containerpreview.azurecr.io` tároló beállításjegyzékből való lekéréséhez szükséges.

> A [Helm diagram][helm-charts] a Kubernetes-erőforrások kapcsolódó készletét leíró fájlok gyűjteménye. Egy diagramot felhasználhat egy egyszerű, például egy Memcached vagy egy összetett Pod üzembe helyezésére, például egy teljes webalkalmazás-verem használatára HTTP-kiszolgálók, adatbázisok, gyorsítótárak és így tovább.

A megadott *Helm-diagramok* lekérik a Computer Vision szolgáltatás Docker-rendszerképeit és a megfelelő szolgáltatást a `containerpreview.azurecr.io` tároló-beállításjegyzékből.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>A Helm diagram telepítése a Kubernetes-fürtön

A *Helm diagram*telepítéséhez végre kell hajtania a [`helm install`][helm-install-cmd] parancsot. Győződjön meg arról, hogy a telepítési parancsot a mappa felett lévő könyvtárból hajtja végre `read` .

```console
helm install read ./read
```

Az alábbi példa egy olyan kimenetet mutat be, amely sikeres telepítés után várható:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

A Kubernetes üzembe helyezése több percet is igénybe vehet. A következő parancs végrehajtásával ellenőrizheti, hogy a hüvelyek és a szolgáltatások megfelelően vannak-e telepítve és elérhetők:

```console
kubectl get all
```

A következő kimenethez hasonlónak kell megjelennie:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Következő lépések

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való telepítésével kapcsolatos további információkért [látogasson el ide][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services-tárolók][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
