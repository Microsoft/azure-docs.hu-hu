---
title: Alkalmazás üzembe helyezése Azure Red Hat OpenShift OpenShift kiszolgáló nélküli használatával
description: Megtudhatja, hogyan helyezhet üzembe egy alkalmazást Azure Red Hat OpenShift OpenShift kiszolgáló nélküli használatával
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, kiszolgáló nélküli
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532831"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Alkalmazás üzembe helyezése Azure Red Hat OpenShift OpenShift kiszolgáló nélküli használatával

Ebben a cikkben egy alkalmazást fog üzembe helyezni egy Azure Red Hat OpenShift [openShift kiszolgáló nélküli használatával.](https://www.openshift.com/learn/topics/serverless) A kiszolgáló nélküli OpenShift segítségével a fejlesztők olyan alkalmazásokat helyezhetnek üzembe és futtatnak, amelyek igény szerint nullára vagy nullára skáláznak fel vagy skáláznak, így kiküszöbölik az erőforrás-használatot, ha nincs használatban.

Az alkalmazáskód csomagolható egy tárolóba a megfelelő futásidejűekkel együtt, és a kiszolgáló nélküli funkció elindítja az alkalmazástárolókat, amikor egy esemény aktiválja őket. Az alkalmazásokat különböző eseményforrások aktiválják, például a saját alkalmazásaiból származó események, több szolgáltató felhőszolgáltatásai, szolgáltatott szoftver (SaaS) rendszerek és más szolgáltatások.

A tárolók kiszolgáló nélküli üzembe helyezésének minden aspektusát közvetlenül az OpenShift-felületbe építi be. A fejlesztők vizuálisan azonosítják, hogy mely események vezetik a tárolóba ezett alkalmazások indítását, és többféleképpen is módosíthatják az eseményparamétereket. A kiszolgáló nélküli OpenShift-alkalmazások integrálhatók más OpenShift-szolgáltatásokkal, például az OpenShift-folyamatokkal, a Service Mesh-sel és a Monitorozással, így teljes körű kiszolgáló nélküli alkalmazásfejlesztést és üzembe helyezést használhatja.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>A Knative parancssori eszköz (kn) telepítése

A CLI legújabb, a gépéhez megfelelő kiadását a következő ről töltheti le: <https://github.com/knative/client/releases/>

Ha a parancsokat a Azure Cloud Shell, töltse le a legújabb, Linuxhoz való Knative parancssori felületét.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>A webkonzol elindítása

A fürt webkonzolja URL-címét a következő futtatásával találhatja meg:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Ehhez hasonló URL-címet kell kapnia.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Indítsa el a konzol URL-címét egy böngészőben, és jelentkezzen be a `kubeadmin` hitelesítő adatokkal.

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift bejelentkezési képernyő":::

## <a name="install-the-openshift-serverless-operator"></a>Az OpenShift kiszolgáló nélküli operátor telepítése

Miután bejelentkezett az OpenShift webkonzolra, győződjön meg arról, hogy rendszergazdai szempontból *van.* Nyissa meg *az Operátorközpontot,* keresse meg és jelölje ki az **OpenShift Kiszolgáló** nélküli operátort.

![Keresse meg a kiszolgáló nélküli OpenShift-operátort](media/serverless/serverless-operatorhub.png)

Ezután nyissa meg az operátor telepítési oldalát a Telepítés **gombra kattintva.**

![Az operátor telepítéséhez kattintson a Telepítés gombra.](media/serverless/serverless-clickinstall.png)

Válassza ki a megfelelő *frissítési csatornát* a Azure Red Hat OpenShift-fürt verziójához, és telepítse az operátort a `openshift-serverless` névtérbe. Görgessen le, és kattintson a **Telepítés gombra.**

![Operátortelepítési oldal](media/serverless/serverless-installpage.png)

Néhány percen belül az állapotlapon látható, hogy az operátor telepítve van, és készen áll a használatra. A folytatáshoz kattintson a **Nézetkezelő** gombra.

![Az operátor telepítve van, és használatra kész](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>A Knative Serving telepítése

Bármely tároló kiszolgáló nélküli futtatásának képessége a kiszolgáló nélküli OpenShiften a upstream Knative használatával lehetséges. A Knative kibővíti a Kubernetes-t, hogy olyan összetevőket biztosítson, amelyek a modern alkalmazások kiszolgáló nélküli módszerrel való üzembe helyezéséhez, futtatásához és kezeléséhez használhatók.

### <a name="create-an-instance-of-the-knative-serving"></a>A Knative Serving egy példányának létrehozása

Váltson a névtérre a bal felső projekt legördülő listára kattintva, majd a Megadott API-k alatt kattintson a Példány létrehozása elemre a `knative-serving`  *Knative Serving kártyán.* 

![Kattintson ide a Knative Service-példány létrehozásához](media/serverless/serverless-createknativeserving.png)

Tartsa meg az alapértelmezett értékeket, és görgessen le a *Knative Serving* létrehozása oldalon a **Létrehozás gombra való kattintáshoz.**

![Tartsa meg az alapértelmezett értékeket, és kattintson a Létrehozás gombra](media/serverless/serverless-createknativeserving2.png)

Várjon, amíg a *Status (Állapot)* **oszlopban a Ready** (Kész) állapot jelenik meg, majd az OpenShift Serverless (Kiszolgáló nélküli) telepítése után készen áll egy Kiszolgáló nélküli OpenShift-projekt létrehozására.

![Knative Serving ready](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Kiszolgáló nélküli projekt létrehozása

Egy nevű új projekt létrehozásához `demoserverless` futtassa az parancsot:

```azurecli-interactive
oc new-project demoserverless
```

A következő hasonló kimenetnek kell lennie:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Váltson *a Bal* oldali  menü Rendszergazda perspektívája helyett a Fejlesztői perspektívára, és válassza ki a elemet a projektek `demoserverless` listájából. Ekkor a projekt *Topológia lapján* kell lennie.

![Azure Red Hat OpenShift topológia létrehozása](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Üzembe helyezés a webkonzollal

Az alkalmazások üzembe helyezéséhez megjelenő lehetőségek közül válassza a *Gitből lehetőséget.* Ezzel meg fog jelenni az *Importálás Gitből oldalra.* `https://github.com/sclorg/django-ex.git`Git-adattár **URL-címeként használja a következőt:**. A minta-webalkalmazás a Python programozási nyelvvel van megvalósítva.

![Azure Red Hat OpenShift Projekt a Gitből](media/serverless/serverless-from-git.png)

> [!NOTE]
> Az OpenShift észleli, hogy ez egy Python-projekt, és kiválasztja a megfelelő szerkesztői rendszerképet.

Görgessen le az *Erőforrások elemhez,* és ellenőrizze, hogy a **Knative Service** van-e kiválasztva a létrehozni szükséges erőforrástípusként. Ez a művelet létrehoz egy Knative Service-t, egy olyan típusú üzembe helyezést, amely lehetővé teszi az OpenShift kiszolgáló nélküli skálázását inaktív állapotban.

![Azure Red Hat OpenShift projekt – Knative](media/serverless/serverless-knative.png)


Ha elkészült, kattintson az oldal alján található Létrehozás **elemre.** Ezzel erőforrásokat hoz létre az alkalmazás felépítésének és üzembe helyezésének kezeléséhez. Ezután a rendszer átirányítja a projekt topológia-áttekintéséhez.

A topológia áttekintése vizuálisan ábrázolja az üzembe helyezett alkalmazást. Ebben a nézetben az alkalmazás teljes struktúráját láthatja.

Várjon, amíg a build befejeződik. Ennek futtatása eltarthat néhány percig. A build befejezése után egy zöld pipa jelenik meg a szolgáltatás bal alsó sarkában.

![Azure Red Hat OpenShift projekt topológiája – Sikeres build](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Az alkalmazás méretének lásd:

A *Topológia nézet* tetején található Megjelenítési beállítások listában kattintson a *Podok száma elemre.* Várja meg, amíg a podok száma nullára csökken. A leméretezés eltarthat néhány percig.

![Azure Red Hat OpenShift projekt topológiája – Nullára skálázva](media/serverless/serverless-scaledtozero.png)

Kattintson *az URL-cím megnyitása* ikonra a Knative Service panel jobb felső sarkában. Az alkalmazás egy új lapon nyílik meg. Zárja be az új böngészőlapot, és térjen vissza a Topológia nézethez. A Topológia nézetben láthatja, hogy az alkalmazás egy podra skálázható fel a kérésnek való igazodás érdekében. Néhány perc múlva az alkalmazás nulla podra skáláz le.

![Azure Red Hat OpenShift projekt topológiája – Skálázható egyetlenre](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Új változat kényszerítés és a forgalomelosztás beállítása

A szolgáltatás konfigurációjának minden egyes frissítését egy új változatban kell létrehozni. A szolgáltatás alapértelmezés szerint a legújabb kész változatra irányít minden forgalmat. Ezt a viselkedést módosíthatja, ha meghatározza, hogy melyik változat kapja meg a forgalom egy részét. A knative szolgáltatások lehetővé teszik a forgalomleképezést, ami azt jelenti, hogy egy szolgáltatás változatai leképezve vannak a forgalom egy lefoglalt részére. A forgalomleképezés lehetőséget biztosít arra is, hogy egyedi URL-címeket hozzon létre az egyes változatok számára.

A *Topológia menüben* kattintson a szolgáltatáson belüli változatra a részleteinek megtekintéséhez. A pod kör alatt és a részletek panel tetején található jelvények a `(REV)` következők: . Az oldalpanel Erőforrások  lapján görgessen le, és kattintson a szolgáltatáshoz társított konfigurációra.

![Azure Red Hat OpenShift projekt topológiája – Változat](media/serverless/serverless-revdetails.png)

Kényszerítsen ki egy konfigurációfrissítést a *YAML* lapra váltva, majd görgessen lefelé a értékének `timeoutSeconds` `301` szerkesztéséhez. A frissített **konfiguráció mentéshez** kattintson a Mentés gombra. Valós helyzetben az ilyen konfigurációfrissítések a tároló rendszerképcímkéje frissítésével is aktiválhatóak.

![Új változat kényszerítés a konfiguráció frissítésével](media/serverless/serverless-confupdate.png)

Vissza a *Topológia* nézetben láthatja, hogy új változat lett telepítve. Kattintson a jelvényt végződő szolgáltatásra, majd kattintson a Forgalomelosztás beállítása gombra. Most már el tudja osztani a forgalmat a szolgáltatás különböző `(KSVC)` változatai között. 

![Forgalomelosztás beállítása](media/serverless/serverless-trafficdist.png)

A *Topológia* nézet most azt mutatja meg, hogyan oszlik meg a forgalom a két változat között.

![Forgalomelosztás áttekintése](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>A Knative parancssori eszköz (kn) használata

Az előző lépésekben az OpenShift webkonzol használatával létrehozott és üzembe helyezett egy alkalmazást a kiszolgáló nélküli OpenShiftben. Mivel a kiszolgáló nélküli OpenShift a Knativet futtatja, a Knative parancssori eszközt (kn) is használhatja a Knative szolgáltatások létrehozásához.

> [!NOTE]
> Ha még nem telepítette a CLI-t, kövesse a cikk `kn` Előfeltételek szakaszában található lépéseket. Győződjön meg arról is, hogy az OpenShift parancssori eszközével jelentkezett `oc` be.

Olyan tároló-rendszerképet fogunk használni, amely már a -ben `quay.io/rhdevelopers/knative-tutorial-greeter` készült.

### <a name="deploy-a-service"></a>Szolgáltatás üzembe helyezése

A szolgáltatás üzembe helyezéséhez futtassa a következő parancsot:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Az alábbihoz hasonló kimenetet fog látni.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

A projektben található útvonalak listáját a következő futtatásával tudja lekérni:

```azurecli-interactive
kn route list
```

A névtérben megjelenik az útvonalak listája. Nyissa meg az URL-címet egy böngészőben az üzembe helyezett szolgáltatás megnyitásához.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>A szolgáltatás új verziójának üzembe helyezése

Telepítse az alkalmazás új verzióját az alábbi parancs futtatásával, a rendszerképcímkével és egy környezeti `:latest` `MESSAGE_PREFIX` változóval:

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

A rendszer visszaigazolót kap egy új változat üzembe `greeter-v2` helyezéséről.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Az összes változat és azok forgalomelosztásának megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
kn revision list
```

Az alábbihoz hasonló listát fog kapni. Vegye figyelembe, hogy az új változat a forgalom 100%-át veszi át.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Kék/zöld és canary üzemelő példányok

Alapértelmezés szerint egy új változat üzembe helyezésekor a forgalom 100%-át kapja meg. Tegyük fel, hogy egy kék/zöld üzembe helyezési stratégiát szeretne megvalósítani, amelyben gyorsan vissza lehet állni az alkalmazás régebbi verziójára. A Knative megkönnyíti ezt.

Frissítse a szolgáltatást úgy, hogy három forgalomcímkét hozzon létre, miközben a forgalom 100%-át a

- **current:** az aktuálisan telepített verziónál lévő pontok
- **prev:** az előző verzió pontjai
- **latest**: mindig a legújabb verzióra mutat

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Az alábbihoz hasonló megerősítést fog kapni.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Listsa ki az útvonalakat az alábbi paranccsal:

```azurecli-interactive
kn route describe greeter
```

Egy kimenetet fog kapni, amely az egyes címkék URL-címeit és a forgalomelosztást mutatja.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Tegyük fel, hogy gyorsan vissza szeretne állni az előző verzióra, frissítheti a forgalomelosztást úgy, hogy a forgalom 100%-át az előző címkére küldje:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Ellenőrizze újra az útvonalak listázását az alábbi paranccsal:

```azurecli-interactive
kn route describe greeter
```

Egy olyan kimenetet fog kapni, amely azt mutatja, hogy a forgalomelosztás 100%-a az előző verzióra mutat.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

A böngészőben a főútvonal (ebben az esetben) frissítése közben használja `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` a forgalomelosztást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következő parancs futtatásával törölheti a projektet:

```azurecli-interactive
oc delete project demoserverless
```

A fürtöt az Oktatóanyag: Fürt törlése [4 Azure Red Hat OpenShift útmutatása alapján is törölheti.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban a következőt tanulta meg:
> [!div class="checklist"]
>
> * Az OpenShift kiszolgáló nélküli operátor és a Knative Serving telepítése
> * Kiszolgáló nélküli projekt üzembe helyezése a webkonzollal
> * Kiszolgáló nélküli projekt üzembe helyezése a Knative CLI (kn) használatával
> * Kék/zöld üzemelő példányok és canary üzemelő példányok konfigurálása a Knative CLI (kn) használatával

További információ a kiszolgáló nélküli, eseményvezérelt alkalmazások Azure Red Hat OpenShift-alapú, kiszolgáló nélküli [OpenShift](https://www.openshift.com/learn/topics/serverless)használatával való létrehozásáról és üzembe helyezéséről: Az [OpenShift](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) kiszolgáló nélküli használatának első lépések dokumentációja és a Kiszolgáló nélküli alkalmazások létrehozása és [kezelése](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html) dokumentáció.
