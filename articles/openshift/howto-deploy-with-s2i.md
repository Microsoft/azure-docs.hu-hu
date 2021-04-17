---
title: Alkalmazás üzembe helyezése a forrásból a Azure Red Hat OpenShift
description: Megtudhatja, hogyan helyezhet üzembe egy alkalmazást, Azure Red Hat OpenShift a forráskódból a forrás-rendszerkép (S2I) buildstratégia használatával
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, source to image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559123"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Alkalmazás üzembe helyezése a forrásból a Azure Red Hat OpenShift

Ebben a cikkben egy alkalmazást fog üzembe helyezni egy Azure Red Hat OpenShift-fürtön a forráskódból egy forrás-rendszerkép (S2I) build használatával. [A forrás–rendszerkép (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) egy buildfolyamat, amely reprodukálható tároló rendszerképeket hoz létre a forráskódból. Az S2I úgy állítja elő a futtatásra kész rendszerképeket, hogy forráskódot injektál egy tároló rendszerképbe, és hagyja, hogy a tároló előkészítse a forráskódot a végrehajtáshoz. Az OpenShift-alkalmazásokat a forrásból is felépítheti az üzembe helyezéshez, így nem kell minden módosítással kézzel tárolót összeépítenie. Az OpenShift ezután automatikusan képes új verziókat kiépíteni és üzembe helyezni, amikor értesítést kap a forráskód változásairól.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Projekt létrehozása

Egy nevű új projekt létrehozásához `demoproject` futtassa az parancsot:

```azurecli-interactive
oc new-project demoproject
```

A következő hasonló kimenetnek kell lennie:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>A webkonzol elindítása

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

Váltson *a Fejlesztői* perspektívára a bal oldali menü Rendszergazda perspektívája helyett, és válassza a elemet a projektek  `demoproject` listájából. Ekkor a projekt *Topológia lapján* kell lennie.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift projekt topológiája":::

Mivel a projekt üres, nem kell számítási feladatokat találnia, és különböző lehetőségek jelennek meg az alkalmazások üzembe helyezésére.

## <a name="deploying-using-the-web-console"></a>Üzembe helyezés a webkonzollal

Az alkalmazások üzembe helyezéséhez megjelenő lehetőségek közül válassza a *Gitből lehetőséget.* Ezzel meg fog jelenni az *Import from Git (Importálás a Gitből)* oldalra. `https://github.com/sclorg/django-ex.git`Git-adattár **URL-címeként használja a következőt:**. A minta-webalkalmazás a Python programozási nyelvvel van megvalósítva.

:::image type="content" source="media/s2i/from-git.png" alt-text="Azure Red Hat OpenShift a Gitből":::

> [!NOTE]
> Az OpenShift észleli, hogy ez egy Python-projekt, és kiválasztja a megfelelő szerkesztői rendszerképet.

Görgessen le a *Speciális beállításokhoz,* és ellenőrizze, hogy be van-e **jelölve az Útvonal** létrehozása az alkalmazáshoz lehetőség. Ez a művelet létrehoz egy OpenShift-útvonalat, amely egy külsőleg elérhető állomásnévvel teszi elérhetővé a szolgáltatást.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Azure Red Hat OpenShift a Gitből – Útvonal beállítása":::

Ha elkészült, kattintson az oldal alján található Létrehozás **gombra.** Ezzel erőforrásokat hoz létre az alkalmazás felépítésének és üzembe helyezésének kezeléséhez. Ezután a rendszer átirányítja a projekt topológiáját áttekintő oldalra.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Azure Red Hat OpenShift a Gitből – Topológia":::

A topológia áttekintése vizuálisan ábrázolja az üzembe helyezett alkalmazást. Ebben a nézetben a teljes alkalmazásstruktúra látható.

A Git ikonra kattintva arra a Git-adattárra használhatja, amelyből az alkalmazás forráskódja készült. A bal alsó sarokban látható ikon az alkalmazás buildállapotát mutatja. Ha erre az ikonra kattint, megjelenik a Build details (Build részletei) szakasz. Ha az alkalmazás útvonalakat tartalmaz, a jobb felső sarokban található ikonra kattintva megnyithatja a létrehozott alkalmazásútvonal URL-címét.

Az alkalmazás fel- vagy leméretezése, a bevezetések indítása és a podok újralétrehozása során a topológianézetben az alkalmazás reprezentációja animált lesz, így valós idejű képet ad a mi történik.

Az alkalmazás ikonra kattintva további részletek jelennek meg az alább látható módon.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Azure Red Hat OpenShift a Gitből – Részletek":::

## <a name="viewing-the-builder-logs"></a>A szerkesztő naplóinak megtekintése

A build elindulása után kattintson az Erőforrások panelen látható *Naplók* megtekintése *hivatkozásra.*

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Azure Red Hat OpenShift Gitből – Buildnaplók":::

Ez lehetővé teszi, hogy a build futása során figyelje a folyamat előrehaladását. Ebben az esetben a szerkesztő rendszerképe, a Python, be fogja injektálni az alkalmazás forráskódját a végső lemezképbe, mielőtt leküldi azt az OpenShift belső rendszerkép-regisztrációs adatbázisába. A build sikeresen befejeződik, ha megjelenik a "Sikeres leküldés" üzenet.

## <a name="accessing-the-application"></a>Az alkalmazás elérése

Az alkalmazás-rendszerkép buildének befejezése után a rendszerkép üzembe lesz helyezni.

A bal *oldali menüsáv Topológia* gombjára kattintva visszatérhet a projekt topológianézetéhez. Amikor a webkonzollal hozta létre az alkalmazást, *automatikusan* létrejött egy Útvonal az alkalmazáshoz, amely a fürtön kívül is elérhető lesz. Az alkalmazás webböngészőből való eléréséhez használható URL-cím a  korábban megtekintett alkalmazás Erőforrások lapján volt látható.

A topológianézetből az üzembe helyezett alkalmazás URL-címét a kör jobb felső ikonjára kattintva tekintheti meg. Az üzembe helyezés befejezése után kattintson a ikonra, és meg kell jelenik a telepített alkalmazás.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Azure Red Hat OpenShift Gitből – Alkalmazás tallózása":::

## <a name="deploying-using-the-command-line"></a>Üzembe helyezés a parancssor használatával

Megtanulta, hogyan helyezhet üzembe egy alkalmazást a webkonzollal, most pedig üzembe helyezheti ugyanazt a webalkalmazást, ezúttal azonban a `oc` parancssori eszközzel.

Futtassa a következő parancsot a projekt törléséhez és az újrakezdhez:

```azurecli-interactive
oc delete project demoproject
```

Megerősítő üzenetet kell kapnia arról, hogy `demoproject` a törölve lett.

```output
project.project.openshift.io "demoproject" deleted
```

Hozza létre `demoproject` ismét a et a következő futtatásával:

```azurecli-interactive
oc new-project demoproject
```

A projekten belül hozzon létre egy új alkalmazást a forrásból a GitHubon, és adja meg az S2I Buildert a Python legújabb verziójához.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

A következő képhez hasonló kimenetnek kell jelen lennie:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

Az OpenShift a Git-adattár nevét fogja használni az alkalmazás neveként. Tekintse át a build és az üzembe helyezés állapotát a következő futtatásával:

```azurecli-interactive
oc status
```

A build és az üzembe helyezés befejezése után a következő kimenetnek kell lennie: .

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Ahhoz, hogy az alkalmazást az OpenShift-fürtön kívül is elérhetővé tegye, létre kell hoznia egy útvonalat a következő futtatásával:

```azurecli-interactive
oc expose service/django-ex
```

Megerősítést kell kapnia.

```output
route.route.openshift.io/django-ex exposed
```

Az URL-cím lekéréséhez futtasa le a következőt:

```azurecli-interactive
oc get route django-ex
```

Vissza kell kapnia a gazdagépnevet, amely ahhoz az útvonalhoz van hozzárendelve, amely segítségével megkeresheti az üzembe helyezett alkalmazást.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Új bináris build aktiválása

Az alkalmazáson való munka során valószínűleg módosításokat szeretne majd tenni, és látni, ahogy üzembe vannak stb. Könnyedén beállíthat egy webhookot, amely minden kód véglegesítéskor új buildet és üzembe helyezést aktivál. Előfordulhat azonban, hogy ez nem kívánatos, mert néha úgy szeretné látni a módosításokat, hogy nem kell minden kódváltozást leküldetni az adattárba.

Olyan esetekben, amikor gyorsan iteratálja a módosításokat, használhatja az úgynevezett bináris buildet. A forgatókönyv szemléltetéséhez klónozza helyileg az alkalmazás Git-adattárát a következő futtatásával:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Ezzel létrehoz egy `django-ex` alkönyvtárat, amely tartalmazza az alkalmazás forráskódját:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Váltsa át az alkönyvtárat:

```azurecli-interactive
cd django-ex
```

Nyissa meg az integrált Azure Cloud Shell szerkesztőt:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Görgessen lefelé, és módosítsa a következő `Welcome to your Django application on OpenShift` szövegsort: `Welcome to Azure Red Hat OpenShift` . Mentse a fájlt, és zárja be a szerkesztőt a jobb felső `...` menüben.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Azure Red Hat OpenShift a Gitből – Alkalmazás szerkesztése a Azure Cloud Shell szerkesztőben":::

Indítson el egy új buildet az parancs futtatásával:

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

A jelző `--from-dir=.` átadása után az OpenShift parancssora feltölti a forráskódot a megadott könyvtárból, majd elindítja a build és az üzembe helyezés folyamatát. Az alábbihoz hasonló kimenetet kell kapnia, és néhány perc múlva befejeződik a build.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Ha frissíti a böngészőt az alkalmazással, a frissített címnek kell látsza.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Azure Red Hat OpenShift Gitből – Frissített alkalmazás tallózása":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következő parancs futtatásával törölheti a projektet:

```azurecli-interactive
oc delete project demoproject
```

A fürtöt az Oktatóanyag: [4.](./tutorial-delete-cluster.md)fürt törlése Azure Red Hat OpenShift útmutatása alapján is törölheti.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban a következővel tanult:
> [!div class="checklist"]
>
> * Projekt létrehozása
> * Alkalmazás üzembe helyezése forráskódból a webkonzol használatával
> * Alkalmazás üzembe helyezése forráskódból az OpenShift parancssori használatával
> * Bináris build aktiválása az OpenShift parancssori használatával

További információ az alkalmazások forrás-rendszerkép és más buildstratégiák [használatával való felépítéséről és üzembe helyezéséről.](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)
