---
title: A Istio használata intelligens útválasztáshoz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használható a Istio az intelligens útválasztás biztosításához és a Kanári-kiadások üzembe helyezéséhez egy Azure Kubernetes Service (ak) fürtben
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 01a7764eb0a353e6842441093f70ad29c9316bbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668271"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Intelligens Útválasztás és Kanári-kiadások használata az Azure Kubernetes Service (Istio) szolgáltatásban (ak)

A [Istio][istio-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtökön futó összes szolgáltatás egyik kulcsfontosságú készletét biztosítja. Ezek a szolgáltatások közé tartoznak a forgalomirányítás, a szolgáltatások identitása és a biztonság, a házirendek betartatása és a Megfigyelhetőség. A Istio kapcsolatos további információkért tekintse meg a hivatalos [Mi az Istio?][istio-docs-concepts] dokumentációt.

Ez a cikk bemutatja, hogyan használhatja a Istio forgalomirányítási funkcióit. A rendszer egy AK-beli szavazási alkalmazást használ az intelligens Útválasztás és a Kanári-kiadások megismerésére.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az alkalmazás központi telepítése
> * Az alkalmazás frissítése
> * Az alkalmazás Kanári-kiadásának kiépítése
> * Bevezetés véglegesítése

## <a name="before-you-begin"></a>Előkészületek

> [!NOTE]
> Ez a forgatókönyv a Istio verzióval `1.3.2`lett tesztelve.

A cikkben részletezett lépések feltételezik, hogy létrehozott egy AK-fürtöt `1.13` (Kubernetes és újabb, RBAC engedélyezve), és létesítettek egy `kubectl` , a fürttel létesített kapcsolatokat. Emellett a fürtben telepített Istio is szüksége lesz.

Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart] -gyors útmutatót, és [telepítse az Istio-t az AK-][istio-install] útmutatóban.

## <a name="about-this-application-scenario"></a>Az alkalmazással kapcsolatos forgatókönyv

A minta**AK-** szavazási alkalmazás két szavazati lehetőséget biztosít **Dogs**a felhasználóknak. Létezik egy tárolási összetevő, amely megőrzi a szavazatok számát az egyes lehetőségeknél. Emellett van egy analitikai összetevő, amely részletesen ismerteti az egyes lehetőségekhez leadott szavazatok körét.

Ebben az alkalmazási helyzetben az analitikai összetevő szavazási alkalmazásának és verziójának `1.0` `1.0` verzióját kell telepítenie. Az analitikai összetevő a szavazatok számának egyszerű számát tartalmazza. A szavazó alkalmazás és az elemzési összetevő a Storage `1.0` összetevő verziójával kommunikál, amelyet a Redis támogat.

Az elemzési összetevőt a verzióra `1.1`frissíti, amely a Counts és a Now összeg és a százalék értéket adja vissza.

A felhasználók egy része, amely `2.0` az alkalmazás egy Kanári-kiadáson keresztüli tesztelési verzióját adja meg. Ez az új verzió egy MySQL-adatbázis által támogatott tárolási összetevőt használ.

Ha biztos abban, hogy a `2.0` verzió a vártnak megfelelően működik a felhasználók részhalmazában, akkor az `2.0` összes felhasználó számára elérhetővé kell tennie a verziót.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Kezdjük azzal, hogy üzembe helyezi az alkalmazást az Azure Kubernetes-szolgáltatás (ak) fürtjében. Az alábbi ábra bemutatja, hogy mit futtat a szakasz vége: az összes `1.0` olyan összetevő verziója, amelyen a bejövő kérelmek a Istio inbounds-átjárón keresztül lettek kiszolgálva:

![Az AK szavazási alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-01.png)

A cikkben ismertetett összetevők az [Azure-Samples/AK-szavazó-alkalmazás][github-azure-sample] GitHub-tárházban érhetők el. A következő módon töltheti le az összetevőket vagy klónozott a tárházat:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Váltson a letöltött/klónozott tárház következő mappájára, és futtassa az összes további lépést ebből a mappából:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Először hozzon létre egy névteret az AK-fürtben a (z) `voting` nevű minta AK-beli szavazási alkalmazáshoz a következő módon:

```console
kubectl create namespace voting
```

Címkézze fel a névteret a-val `istio-injection=enabled`. Ez a címke arra utasítja a Istio-t, hogy a névtérben lévő összes hüvelybe automatikusan adja a Istio-proxykat az oldalkocsiként.

```console
kubectl label namespace voting istio-injection=enabled
```

Most hozzuk létre az AK-szavazási alkalmazás összetevőit. Hozza létre ezeket az összetevőket `voting` az előző lépésben létrehozott névtérben.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A következő példa kimenete a létrehozandó erőforrásokat mutatja:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> A Istio bizonyos követelményeket támaszt a hüvelyek és a szolgáltatások körül. További információ: a [Istio követelményei a hüvelyek és a szolgáltatások dokumentációjában][istio-requirements-pods-and-services].

A létrehozott hüvelyek megjelenítéséhez használja a [kubectl Get hüvely][kubectl-get] parancsot az alábbi módon:

```console
kubectl get pods -n voting --show-labels
```

A következő példa kimenetében látható, hogy a `voting-app` Pod három példánya van, és a és `voting-analytics` `voting-storage` a hüvely egyetlen példánya. A hüvelyek mindegyike két tárolóval rendelkezik. Ezen tárolók egyike az összetevő, a másik a következő `istio-proxy`:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Ha szeretné megtekinteni a pod-ról szóló információkat, a [kubectl-leírásban][kubectl-describe] a pod parancs bejelölésével választhatja ki a `voting-analytics` hüvelyt. A kimenet szűrésével megjelenítheti a pod-ban található két tároló részleteit:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Addig nem tud csatlakozni a szavazati alkalmazáshoz, amíg létre nem hozza a Istio- [átjárót][istio-reference-gateway] és a [virtuális szolgáltatást][istio-reference-virtualservice]. Ezek a Istio-erőforrások átirányítják az adatokat az alapértelmezett Istio bemenő átjáróról az alkalmazásba.

> [!NOTE]
> Az **átjáró** a szolgáltatás rácsvonalának szélén lévő összetevő, amely bejövő vagy kimenő http-és TCP-forgalmat fogad.
> 
> A **virtuális szolgáltatások** egy vagy több cél szolgáltatás útválasztási szabályait határozzák meg.

Használja az `kubectl apply` parancsot az átjáró és a virtuális szolgáltatás YAML üzembe helyezéséhez. Ne felejtse el megadni azt a névteret, amelyre ezek az erőforrások telepítve vannak.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A következő példa kimenete a létrehozandó új átjárót és virtuális szolgáltatást mutatja:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Szerezze be a Istio beáramló átjáró IP-címét a következő parancs használatával:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A következő példa kimenete a bejövő átjáró IP-címét jeleníti meg:

```output
20.188.211.19
```

Nyisson meg egy böngészőt, és illessze be az IP-címet. Megjelenik a minta AK-beli szavazási alkalmazás.

![A Istio-kompatibilis AK-fürtön futó AK-beli szavazási alkalmazás.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

A képernyő alján található információk azt mutatják, hogy `1.0` az alkalmazás a `voting-app` `1.0` `voting-storage` (z) és a (z) (Redis) verzióját használja.

## <a name="update-the-application"></a>Az alkalmazás frissítése

Végezzük el az analitikai összetevő új verziójának üzembe helyezését. Az új verzió `1.1` az egyes kategóriákhoz tartozó darabszámot és százalékokat is megjeleníti.

Az alábbi ábrán látható, hogy mi fog futni a szakasz végén, az `1.1` `voting-analytics` összetevőnek csak az összetevőjét tartalmazó verziója van forgalomban `voting-app` . Annak ellenére, `1.0` hogy az `voting-analytics` összetevő verziója továbbra is fut, és a `voting-analytics` szolgáltatás hivatkozik rá, a Istio-proxyk nem engedélyezik a felé irányuló és onnan érkező forgalmat.

![Az AK szavazási alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-02.png)

Telepítse az `voting-analytics` összetevő verzióját `1.1` . Hozza létre ezt az összetevőt a `voting` névtérben:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A következő példa kimenete a létrehozandó erőforrásokat mutatja:

```output
deployment.apps/voting-analytics-1-1 created
```

Nyissa meg újra a minta AK-beli szavazó alkalmazást egy böngészőben, az előző lépésben beszerzett Istio beléptetési átjáró IP-címének használatával.

A böngésző az alább látható két nézet között váltakozik. Mivel az `voting-analytics` összetevőhöz csak egyetlen [Kubernetes (][kubernetes-service] `app: voting-analytics`) használ, a Kubernetes a ciklikus multiplexelés alapértelmezett viselkedését használja a választónak megfelelő hüvelyek között. Ebben az esetben ez a verzió `1.0` és `1.1` a `voting-analytics` hüvely is.

![Az 1,0-es verzió, amely egy, az AK-beli szavazási alkalmazásban futó analitikai összetevő.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Az 1,1-es verzió, amely egy, az AK-beli szavazási alkalmazásban futó analitikai összetevő.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Az `voting-analytics` összetevő két verziója közötti váltást az alábbi módon jelenítheti meg. Ne felejtse el használni a saját Istio bejövő átjárójának IP-címét.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A következő példa kimenete a visszaadott webhely megfelelő részét mutatja, mivel a hely a verziók között vált:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Az alkalmazás 1,1-es verziójára vonatkozó forgalom zárolása

Most zárja `1.1` be a forgalmat `voting-analytics` az összetevő és az `1.0` `voting-storage` összetevő verziójára. Ezután megadhatja az összes többi összetevő útválasztási szabályait.

> * A **virtuális szolgáltatások** egy vagy több cél szolgáltatás útválasztási szabályait határozzák meg.
> * A **célként megadott szabályok** határozzák meg a forgalmi házirendeket és a verzióra vonatkozó házirendeket.
> * A **szabályzat** meghatározza, hogy milyen hitelesítési módszerek fogadhatók el a munkaterhelés (ok) ban.

A `kubectl apply` parancs használatával cserélje le a virtuális szolgáltatás `voting-app` definícióját, és adja hozzá a többi összetevőhöz a célként megadott [szabályokat][istio-reference-destinationrule] és a [virtuális szolgáltatásokat][istio-reference-virtualservice] . A `voting` névtérhez hozzá kell adnia egy [szabályzatot][istio-reference-policy] , amely biztosítja, hogy a szolgáltatások közötti kommunikáció kölcsönös TLS-és Ügyféltanúsítványok használatával biztosítva legyen.

* A szabályzat úgy `peers.mtls.mode` van beállítva `STRICT` , hogy biztosítsa, hogy a kölcsönös TLS kényszerítve legyen a saját `voting` szolgáltatásai között a névtéren belül.
* A `trafficPolicy.tls.mode` -t az összes `ISTIO_MUTUAL` cél szabályban is be kell állítani. A Istio erős identitásokkal biztosítja a szolgáltatásokat, és biztosítja a kommunikációt a szolgáltatások között a kölcsönös TLS-vel és az Istio transzparens módon felügyelt Ügyféltanúsítványok használatával.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A következő példa kimenete az új szabályzatot, a célhely szabályait és a virtuális szolgáltatások frissítésének/létrehozásának eredményét mutatja be:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Ha újra megnyitja az AK-szavazási alkalmazást egy böngészőben, az `1.1` `voting-analytics` `voting-app` összetevő csak az összetevő új verzióját használja.

![Az 1,1-es verzió, amely egy, az AK-beli szavazási alkalmazásban futó analitikai összetevő.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Az alábbi módon jelenítheti meg, hogy most már csak az `1.1` `voting-analytics` összetevő verziójára irányítja. Ne felejtse el használni a saját Istio bejövő átjárójának IP-címét:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A következő példa kimenete a visszaadott webhely megfelelő részét mutatja:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Most erősítse meg, hogy a Istio kölcsönös TLS-t használ az egyes szolgáltatások közötti kommunikáció biztonságossá tételéhez. Ehhez a [authn TLS-pipa][istioctl-authn-tls-check] parancsot fogjuk használni az `istioctl` ügyfél bináris fájlján, amely a következő űrlapot veszi igénybe.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ezek a parancsok a megadott szolgáltatásokhoz való hozzáférésről, a névtérben lévő összes hüvelyről és a címkék egy csoportjának megfelelő információkat biztosítanak:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

A következő példa kimenete azt mutatja, hogy a fenti lekérdezésekben a kölcsönös TLS kényszerítve van. A kimenet a házirend és a cél szabályait is megjeleníti, amelyek kikényszerítik a kölcsönös TLS-t:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Az alkalmazás Kanári-kiadásának kiépítése

Most hozzuk `2.0` üzembe a, `voting-app` `voting-analytics`és `voting-storage` az összetevők új verzióját. Az új `voting-storage` összetevő a Redis helyett a MySQL-t használja `voting-app` , `voting-analytics` és a és az összetevők frissülnek, hogy lehetővé `voting-storage` tegyék az új összetevő használatát.

Az `voting-app` összetevő mostantól támogatja a funkció-jelölő funkciót. Ez a funkció lehetővé teszi, hogy tesztelje a Istio Kanári kiadási képességeit a felhasználók egy részhalmaza számára.

Az alábbi ábrán látható, hogy mit fog futni a szakasz végén.

* `voting-app` Az `1.0` összetevő verziószáma, `1.1` az `voting-analytics` összetevő verziója és verziója `1.0` `voting-storage` képes kommunikálni egymással.
* `voting-app` Az `2.0` összetevő verziószáma, `2.0` az `voting-analytics` összetevő verziója és verziója `2.0` `voting-storage` képes kommunikálni egymással.
* Az `2.0` `voting-app` összetevő verziója csak olyan felhasználók számára érhető el, akiknek van beállított szolgáltatás-jelölője. Ezt a változást a szolgáltatás jelölője egy cookie-n keresztül kezeli.

![Az AK szavazási alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-03.png)

Először frissítse a Istio-cél szabályait és a virtuális szolgáltatásokat az új összetevők kiszolgálásához. Ezek a frissítések biztosítják, hogy a forgalom ne legyen helytelen az új összetevőkre irányítva, és a felhasználók nem kapnak váratlan hozzáférést:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A következő példa kimenetében a célként megadott szabályok és virtuális szolgáltatások láthatók:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Ezután adja hozzá a Kubernetes objektumokat az új verzió `2.0` összetevőihez. Emellett frissíti a `voting-storage` szolgáltatást, hogy tartalmazza a `3306` MySQL-portot:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A következő példa kimenete a Kubernetes objektumok sikeres frissítését vagy létrehozását mutatja be:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Várjon, amíg a hüvelyek összes verziója `2.0` fut. A [kubectl Get hüvely][kubectl-get] paranccsal a `-w` Watch kapcsolóval tekintheti meg a `voting` névterek összes hüvelyének változásait:

```console
kubectl get pods --namespace voting -w
```

Most már képesnek kell lennie váltani a szavazati `1.0` alkalmazás `2.0` verziójának és verziójának (Kanári) között. A képernyő alján lévő funkció-jelölő váltógomb beállítja a cookie-t. Ezt a cookie-t a `voting-app` virtuális szolgáltatás használja arra, hogy a felhasználókat az `2.0`új verzióra irányítsa.

![NINCS beállítva az 1,0-es verzióra vonatkozó szavazási app-Feature jelző.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![A 2,0-es verzióra van beállítva az AK-beli szavazási alkalmazás-funkció jelzője.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

A szavazatok száma eltér az alkalmazás verziójától. Ez a különbség kiemeli, hogy két különböző tárolási hátteret használ.

## <a name="finalize-the-rollout"></a>Bevezetés véglegesítése

Miután sikeresen tesztelte a Kanári-kiadást, frissítse `voting-app` a virtuális szolgáltatást, hogy átirányítsa `2.0` az összes `voting-app` forgalmat az összetevő összes verziójára. Az összes felhasználó láthatja `2.0` az alkalmazás verzióját, függetlenül attól, hogy a szolgáltatás jelölője be van-e állítva vagy sem:

![Az AK szavazási alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-04.png)

A már nem kívánt összetevők verzióinak eltávolításához frissítse az összes célhely-szabályt. Ezután frissítse az összes virtuális szolgáltatást, hogy ne hivatkozzon a verziókra.

Mivel az összetevők valamelyik régebbi verziójára már nincs forgalom, mostantól biztonságosan törölheti az összetevők összes központi telepítését.

![Az AK szavazási alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-05.png)

Mostantól sikeresen bevezette az AK szavazó alkalmazás új verzióját.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Az ebben a forgatókönyvben használt AK-szavazati alkalmazást eltávolíthatja az AK-fürtből a `voting` névtér törlésével a következőképpen:

```console
kubectl delete namespace voting
```

A következő példa kimenete azt mutatja, hogy az AK szavazó alkalmazás összes összetevője el lett távolítva az AK-fürtből.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>További lépések

A [Istio Bookinfo Application example][istio-bookinfo-example]használatával további forgatókönyveket is megvizsgálhat.

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
