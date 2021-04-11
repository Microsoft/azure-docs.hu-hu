---
title: A Speech Service-tárolók használata a Kubernetes és a Helm használatával
titleSuffix: Azure Cognitive Services
description: A Kubernetes és a Helm használatával határozza meg a beszéd-szöveg és a szöveg – beszéd tároló rendszerképeit, és létrehozunk egy Kubernetes-csomagot. Ez a csomag egy helyszíni Kubernetes-fürtön lesz üzembe helyezve.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: aahi
ms.openlocfilehash: 78ac9ae4aa8611f50caa94c84d3e6c95e58fc91c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102200733"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>A Speech Service-tárolók használata a Kubernetes és a Helm használatával

A helyszíni beszédfelismerési tárolók kezelésének egyik lehetősége a Kubernetes és a Helm használata. A Kubernetes és a Helm használatával határozza meg a beszéd-szöveg és a szöveg – beszéd tároló rendszerképeit, és létrehozunk egy Kubernetes-csomagot. Ez a csomag egy helyszíni Kubernetes-fürtön lesz üzembe helyezve. Végezetül megvizsgáljuk, hogyan tesztelheti az üzembe helyezett szolgáltatásokat és a különböző konfigurációs beállításokat. A Docker-tárolók Kubernetes-előkészítés nélküli futtatásával kapcsolatos további információkért lásd: [a Speech Service-tárolók telepítése és futtatása](speech-container-howto.md).

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a helyszíni beszédfelismerési tárolók használata előtt:

| Kötelező | Cél |
|----------|---------|
| Azure-fiók | Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot][free-azure-account]. |
| Container Registry hozzáférés | Ahhoz, hogy a Kubernetes lekérje a Docker-rendszerképeket a fürtre, hozzá kell férnie a tároló-beállításjegyzékhez. |
| Kubernetes CLI | A megosztott hitelesítő adatok a tároló-beállításjegyzékből való kezeléséhez a [KUBERNETES CLI][kubernetes-cli] szükséges. A Kubernetes a Helm előtt is szükséges, amely a Kubernetes csomagkezelő. |
| Helm parancssori felület | Telepítse a [Helm CLI][helm-install]-t, amely a Helm-diagram (Container Package Definition) telepítéséhez használatos. |
|Beszédfelismerési erőforrás |A tárolók használatához a következőket kell tennie:<br><br>Egy _Speech_ Azure-erőforrás a társított számlázási kulcs és a számlázási végpont URI azonosítójának lekéréséhez. Mindkét érték elérhető a Azure Portal **beszédének** áttekintése és a kulcsok oldalain, és a tároló elindításához szükséges.<br><br>**{API_KEY}**: erőforrás-kulcs<br><br>**{ENDPOINT_URI}**: végpont URI-ja például: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Az ajánlott gazdagép-Számítógép konfigurációja

Hivatkozásként tekintse meg a [beszédfelismerési szolgáltatás tárolójának számítógépének][speech-container-host-computer] részleteit. Ez a *Helm-diagram* automatikusan kiszámítja a CPU-és memória-követelményeket azon alapul, hogy hány dekódolást (egyidejű kérést) határoz meg a felhasználó. Emellett azt is meghatározza, hogy a hang/szöveg bemenetének optimalizálása a következőként van-e konfigurálva: `enabled` . A Helm diagram alapértelmezett értéke, két egyidejű kérelem és az optimalizálás letiltása.

| Szolgáltatás | PROCESSZOR/tároló | Memória/tároló |
|--|--|--|
| **Beszéd – szöveg** | egy dekóderhez legalább 1 150 millicores szükséges. Ha az `optimizedForAudioFile` engedélyezve van, akkor a 1 950 millicores megadása kötelező. (alapértelmezett: két dekóder) | Kötelező: 2 GB<br>Korlátozott: 4 GB |
| **Szöveg – beszéd** | egy egyidejű kérelemhez legalább 500 millicores szükséges. Ha az `optimizeForTurboMode` engedélyezve van, akkor a 1 000 millicores megadása kötelező. (alapértelmezés: két egyidejű kérelem) | Kötelező: 1 GB<br> Korlátozott: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Kapcsolódás a Kubernetes-fürthöz

A gazdaszámítógépnek várhatóan rendelkezésre áll egy Kubernetes-fürt. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy [Kubernetes-fürtöt](../../aks/tutorial-kubernetes-deploy-cluster.md) a Kubernetes-fürtök gazdagépre történő központi telepítésének fogalmi megismeréséhez.

## <a name="configure-helm-chart-values-for-deployment"></a>A Helm-diagram értékeinek konfigurálása üzembe helyezéshez

Látogasson el a Microsoft [Helm hubhoz][ms-helm-hub] a Microsoft által kínált nyilvánosan elérhető Helm-diagramokba. A Microsoft Helm hub-ban megtalálja a **Cognitive Services beszéd helyszíni diagramot**. A **helyszíni Cognitive Services-beszéd** a telepítendő diagram, de először létre kell hozni egy `config-values.yaml` explicit konfigurációval rendelkező fájlt. Kezdjük a Microsoft adattár hozzáadásával a Helm-példánnyal.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Ezután a Helm-diagram értékeit fogjuk konfigurálni. Másolja és illessze be a következő YAML egy nevű fájlba `config-values.yaml` . A **Cognitive Services Speech helyszíni Helm diagram** testreszabásával kapcsolatos további információkért lásd: Helm- [diagramok testreszabása](#customize-helm-charts). Cserélje le a `# {ENDPOINT_URI}` és a `# {API_KEY}` megjegyzéseket a saját értékeire.

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Ha a `billing` és az `apikey` értékek nincsenek megadva, akkor a szolgáltatások 15 perc után lejárnak. Hasonlóképpen, az ellenőrzés sikertelen lesz, mert a szolgáltatások nem lesznek elérhetők.

### <a name="the-kubernetes-package-helm-chart"></a>A Kubernetes-csomag (Helm-diagram)

A *Helm diagram* tartalmazza azt a konfigurációt, amelynek a Docker-rendszerképe a `mcr.microsoft.com` tároló beállításjegyzékből való lekéréséhez szükséges.

> A [Helm diagram][helm-charts] a Kubernetes-erőforrások kapcsolódó készletét leíró fájlok gyűjteménye. Egy diagramot felhasználhat egy egyszerű, például egy Memcached vagy egy összetett Pod üzembe helyezésére, például egy teljes webalkalmazás-verem használatára HTTP-kiszolgálók, adatbázisok, gyorsítótárak és így tovább.

A megadott *Helm-diagramok* lekérik a beszédfelismerési szolgáltatás Docker-rendszerképeit, a szöveges és a beszédfelismerési szolgáltatásokat, valamint a tároló-beállításjegyzékből származó beszéd és szöveg szolgáltatást `mcr.microsoft.com` .

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>A Helm diagram telepítése a Kubernetes-fürtön

A *Helm diagram* telepítéséhez végre kell hajtania a parancsot a [`helm install`][helm-install-cmd] `<config-values.yaml>` megfelelő elérési úttal és fájlnév argumentummal. Az `microsoft/cognitive-services-speech-onpremise` alább hivatkozott Helm-diagram itt érhető el a [Microsoft Helm hub][ms-helm-hub-speech-chart]-on.

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Az alábbi példa egy olyan kimenetet mutat be, amely sikeres telepítés után várható:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

A Kubernetes üzembe helyezése több percet is igénybe vehet. A következő parancs végrehajtásával ellenőrizheti, hogy a hüvelyek és a szolgáltatások megfelelően vannak-e telepítve és elérhetők:

```console
kubectl get all
```

A következő kimenethez hasonlónak kell megjelennie:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Helm-telepítés ellenőrzése Helm-tesztekkel

A telepített Helm-diagramok *Helm-teszteket* határoznak meg, amelyek az ellenőrzés kényelmét szolgálják. Ezek a tesztek ellenőrzik a szolgáltatás készültségét. A **beszéd-szöveg** és a **szövegről beszédre** irányuló szolgáltatások ellenőrzéséhez hajtsa végre a [Helm test][helm-test] parancsot.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Ezek a tesztek sikertelenek lesznek, ha a POD állapota nem, `Running` vagy ha a központi telepítés nem szerepel az `AVAILABLE` oszlop alatt. Legyen türelmes, mivel ez akár tíz percet is igénybe vehet.

Ezek a tesztek különböző állapot-eredményeket eredményeznek:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

A *Helm-tesztek* végrehajtásának alternatívájaként a *külső IP-* címeket és a hozzájuk tartozó portokat is összegyűjtheti a `kubectl get all` paranccsal. Az IP-cím és a port használatával nyisson meg egy webböngészőt, és navigáljon a `http://<external-ip>:<port>:/swagger/index.html` (z) elemre az API hencegő oldalának megtekintéséhez.

## <a name="customize-helm-charts"></a>Helm-diagramok testreszabása

A Helm-diagramok hierarchikusak. A hierarchia lehetővé teszi a diagramok öröklését, továbbá a sajátosság fogalmát is kielégíti, ahol pontosabban felülbírált szabályok vonatkoznak.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Következő lépések

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való telepítésével kapcsolatos további információkért [látogasson el ide][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services-tárolók][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md