---
title: 'Kubernetes fejlesztői terület létrehozása: Visual Studio Code & Node. js'
services: azure-dev-spaces
ms.date: 09/26/2018
ms.topic: tutorial
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio Code egy Node. js-alkalmazás hibakereséséhez és gyors megismétléséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 916f2b1449e9d1a29fde94be05e03c3e8211af8c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252023"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-nodejs-with-azure-dev-spaces"></a>Kubernetes fejlesztői terület létrehozása: a Visual Studio Code és a Node. js az Azure dev Spaces szolgáltatással

Ebből az útmutatóból a következőket tudhatja meg:

- Fejlesztésre optimalizált Kubernetes-alapú környezet, vagyis egy _Dev Spaces-tér_ létrehozása az Azure-ban.
- Iteratív kódfejlesztés tárolókban a VS Code és a parancssor használatával.
- A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.

> [!Note]
> **Ha bármikor elakad** , tekintse meg a [Hibaelhárítás](troubleshooting.md) című szakaszt.

## <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t
Az Azure Dev Spaces használatához minimális helyi gépbeállítás szükséges. A Dev Spaces-tér konfigurációjának legnagyobb része a felhőben van tárolva, és megosztható más felhasználókkal. Első lépésként töltse le és futtassa az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) parancssori felületet.

### <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be
Jelentkezzen be az Azure-ba. Írja be a következő parancsot egy terminálablakba:

```azurecli
az login
```

> [!Note]
> Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Ha több Azure-előfizetéssel is rendelkezik...
Az előfizetéseit a következő futtatásával tekintheti meg: 

```azurecli
az account list --output table
```

Keresse meg azt az előfizetést, amely a *IsDefault*esetében *igaz* .
Ha nem ezt az előfizetést szeretné használni, módosíthatja az alapértelmezett előfizetést:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Azure Dev Spaceshez engedélyezett Kubernetes-fürt létrehozása

A parancssorban hozza létre az erőforráscsoportot egy olyan régióban, [amely támogatja az Azure dev Spaces platformot][supported-regions].

```azurecli
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A fürt létrehozása néhány percet vesz igénybe.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>AKS-fürt konfigurálása az Azure Dev Spaces használatára

Írja be az alábbi Azure CLI-parancsot, és használja az AKS-fürtöt tartalmazó erőforráscsoportot, valamint az AKS-fürt nevét. A parancs konfigurálni fogja a fürtöt az Azure Dev Spaces támogatására.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Ha létezik, akkor az Azure dev Spaces konfigurációs folyamata eltávolítja a `azds` névteret a fürtben.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Kubernetes-hibakeresés VS Code-hoz
A VS Code-ot használó .NET Core- és Node.js-felhasználók számára számos funkció érhető el, mint például a Kubernetes-hibakeresés.

1. Ha nincs telepítve a [VS Code](https://code.visualstudio.com/Download), telepítse.
1. Töltse le és telepítse a [VS Azure Dev Spaces bővítményt](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Kattintson egyszer a Telepítés lehetőségre a bővítmény Marketplace-oldalán, majd még egyszer a VS Code-ban. 

## <a name="create-a-nodejs-container-in-kubernetes"></a>Node.js-tároló létrehozása a Kubernetesben

Ebben a szakaszban egy Node.js-alapú webalkalmazást hozunk létre, és futtatjuk egy Kubernetes-tárolóban.

### <a name="create-a-nodejs-web-app"></a>Node.js-alapú webes alkalmazás létrehozása
Töltse le a GitHubról a kódot a [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) megnyitásával, és válassza a **klónozás vagy Letöltés** lehetőséget a GitHub-tárház helyi környezetbe való letöltéséhez. A jelen útmutatóban használt kód a következő helyen található: `samples/nodejs/getting-started/webfrontend`.

## <a name="prepare-code-for-docker-and-kubernetes-development"></a>Kód előkészítése a Docker és a Kubernetes-fejlesztéshez
Már rendelkezik egy helyileg futó alapszintű webalkalmazással. Most olyan objektumok létrehozásával egy tárolóba fogja helyezni, amelyek meghatározzák az alkalmazás tárolóját, és a Kubernetesben történő üzembe helyezésének módját. Ez a feladat az Azure Dev Spaces segítségével egyszerűen elvégezhető: 

1. Indítsa el a VS Code-ot, majd nyissa meg a `webfrontend` mappát. (Figyelmen kívül hagyhat minden olyan alapértelmezett kérést, amely az objektumok hibakeresésére vagy a projekt visszaállítására vonatkozik.)
1. Nyissa meg a VS Code Integrált terminálját (a **View (Nézet) > Integrated Terminal (Integrált terminál)** menüvel).
1. Futtassa ezt a parancsot (győződjön meg róla, hogy a **webfrontend** a jelenlegi mappája):

    ```cmd
    azds prep --enable-ingress
    ```

Az Azure CLI `azds prep` parancsa alapértelmezett beállításokkal hoz létre Docker- és Kubernetes-objektumokat:
* A `./Dockerfile` ismerteti az alkalmazás tárolólemezképét, illetve hogy a forráskód hogyan épül fel és hogyan fut a tárolón belül.
* A [ alatt található ](https://docs.helm.sh)Helm-diagram`./charts/webfrontend` ismerteti a konténer Kubernetesben történő üzembe helyezését.

> [!TIP]
> A projekthez tartozó [Docker és Helm diagramot](how-dev-spaces-works.md#prepare-your-code) az Azure dev Spaces használja a kód összeállításához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

Egyelőre még nem fontos a fájlok teljes tartalmát megérteni. Ugyanakkor azt megjegyeznénk, hogy **használhatja ugyanazokat a Docker és a Kubernetes kóddal való konfigurálási objektumokat a fejlesztéstől kezdve egészen az éles környezetig, így jobb konzisztenciát biztosíthat a különböző környezetekben.**
 
A `./azds.yaml` parancs egy `prep` nevű fájlt is létrehoz, amely az Azure Dev Spaces konfigurációs fájlja. Ez olyan további konfigurációval egészíti ki a Docker- és Kubernetes-munkadarabokat, amely engedélyez az Azure-ban egy iteratív fejlesztési környezetet.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben
Most futtassuk a kódunkat! Futtassa ezt a parancsot a terminálablakban a webfrontend nevű kódolási **gyökérmappából**:

```cmd
azds up
```

Figyelje a parancs kimenetét, és számos dolgot megfigyelhet a folyamat közben:
- A program szinkronizálja a forráskódot az Azure-beli Dev Spaces-térrel.
- A kódmappában lévő Docker-adategységek alapján létrejön egy tároló-lemezkép az Azure-ban.
- Kubernetes-objektumok jönnek létre, amelyek a kódmappában található Helm-diagram szerint használják a tároló-lemezképet.
- Megjelennek a tároló végpontjára (vagy végpontjaira) vonatkozó információk. A mi esetünkben egy nyilvános HTTP URL-t várunk.
- Ha a fenti szakaszok sikeresen lezárultak, a tároló indulásakor megjelenik az `stdout` (és `stderr`) kimenet.

> [!Note]
> Ezek a lépések az `up` első futtatásakor hosszabb időt vesznek igénybe, de a további futtatások már gyorsabbak lesznek.

### <a name="test-the-web-app"></a>A webalkalmazás tesztelése
Keresse meg a konzolkimenetben az `up` parancs által létrehozott nyilvános URL-re vonatkozó adatokat. Ez az alábbi formátumban lesz: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Azonosítsa a szolgáltatás nyilvános URL-címét a `up` parancs kimenetében. `.azds.io`végződik. A fenti példában a nyilvános URL-cím `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

A webalkalmazás megjelenítéséhez nyissa meg a nyilvános URL-címet egy böngészőben. Emellett figyelje meg, `stdout` és `stderr` kimenet a *azds nyomkövetési* terminál ablakba kerül, miközben a webalkalmazással kommunikál. A HTTP-kérések nyomkövetési információit is megtekintheti, ahogy azok a rendszeren haladnak át. Így könnyebben nyomon követheti az összetett több szolgáltatásra irányuló hívásokat a fejlesztés során. A dev Spaces által hozzáadott kiépítés biztosítja ezt a kérelmek nyomon követését.

> [!Note]
> A nyilvános URL-címen kívül használhatja a konzol kimenetében megjelenő alternatív `http://localhost:<portnumber>` URL-címet is. Ha a localhost URL-címet használja, úgy tűnhet, mintha a tároló helyileg fut, de valójában az Azure-ban fut. Az Azure dev Spaces Kubernetes *-port továbbítási* funkciója használatával képezi le a localhost PORTOT az AK-ban futó tárolóhoz. Ez megkönnyíti a szolgáltatással való interakciót a helyi gépről.

### <a name="update-a-content-file"></a>Tartalomfájlok frissítése
Az Azure Dev Spaces nem csupán a Kubernetesben futó kódok lekérésére szolgál – a segítségével gyorsan és iteratívan lehet megtekinteni a kódmódosítások életbe lépését a felhőben lévő Kubernetes-környezetben.

1. Keresse meg a `./public/index.html` fájlt, és szerkessze a HTML-kódot. Például módosítsa az oldal háttérszínét kék árnyalatra [a 15. sorban](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/public/index.html#L15):

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

1. Mentse a fájlt. Néhány pillanat múlva egy, a terminálablakban megjelenő üzenet tájékoztatja, hogy a futó tárolóban lévő egyik fájl frissült.
1. Lépjen a böngészőre, és frissítse az oldalt. Látnia kell, ahogy a szín megváltozik.

Mi történt? A tartalomfájlok (például HTML és CSS) módosításai nem igénylik a Node.js folyamat újraindítását, így az aktív `azds up` parancs automatikusan és közvetlenül szinkronizálja a módosított tartalomfájlokat az Azure-ban futó tárolóba, így gyorsan megtekintheti a tartalmak módosításait.

### <a name="test-from-a-mobile-device"></a>Tesztelés mobileszközről
Nyissa meg a webalkalmazást egy mobileszközön a webfrontend nyilvános URL-címével. Érdemes lehet az URL-címet az asztali gépről kimásolni, majd elküldeni az eszközre, hogy ne kelljen a hosszú címet begépelni. Ha a webalkalmazást egy mobileszközön tölti be, megfigyelheti, hogy a felhasználói felület nem megfelelően jelenik meg a kis méretű eszközökön.

A hiba kiküszöbölésére hozzáadunk egy `viewport` metacímkét:
1. Nyissa meg a `./public/index.html` fájlt.
1. Adjon hozzá egy `viewport` META-címkét a [6. sorban](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/public/index.html#L6)megjelenő meglévő `head` elemhez:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Mentse a fájlt.
1. Frissítse a böngészőt az eszközön. Most a webalkalmazásnak megfelelően renderelve kell megjelennie. 

Ez a példa azt mutatja be, hogy bizonyos problémák csak akkor figyelhetők meg, ha olyan eszközökön teszteli az alkalmazást, amelyekre azt szánták. Az Azure Dev Spaces segítségével a kód gyorsan iterálható, és a módosítások is gyorsan ellenőrizhetők a céleszközökön.

### <a name="update-a-code-file"></a>Kódfájlok frissítése
A kiszolgálóoldali kódfájlok frissítése egy kicsit több munkát igényel, mert újra kell indítani egy Node.js-alkalmazást.

1. A terminálablakban nyomja le a `Ctrl+C` billentyűkombinációt (az `azds up` leállításához).
1. Nyissa meg a `server.js` nevű kódfájlt, és szerkessze a szolgáltatás üdvözlőüzenetét: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Mentse a fájlt.
1. Futtassa az `azds up` parancsot a terminálablakban. 

Ez a parancs újraépíti a tárolórendszerképet, és újra üzembe helyezi a Helm-diagramot. Töltse be újra az oldalt a böngészőben a módosított kód életbe léptetéséhez.

Azonban a kódfejlesztésnek van egy még ennél is *gyorsabb módszere*, amelyet a következő szakaszban mutatunk be. 

## <a name="debug-a-container-in-kubernetes"></a>Tároló hibakeresése a Kubernetesben

Ebben a részben közvetlenül a VS Code-dal végezhet hibakeresést az Azure-ban futó tárolón. Azt is megtudhatja, hogyan végezheti el gyorsabban a szerkesztés-futtatás-tesztelés hurkot.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Ha bármikor elakad**, tekintse meg a [Hibaelhárítás](troubleshooting.md) szakaszt, vagy írjon egy hozzászólást erre a lapra.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>A hibakeresési objektum inicializálása a VS Code-bővítménnyel
Először konfigurálja a kódprojektet úgy, hogy a VS Code kommunikálni tudjon az Azure-beli Dev Spaces-terünkkel. A VS Code Azure Dev Spaceshez készült bővítménye biztosít egy segítőparancsot a hibakeresési konfiguráció beállításához. 

Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Ez hozzáadja az Azure Dev Spaceshez készült hibakeresési konfigurációt a `.vscode` mappához. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Az AZDS hibakeresési konfiguráció kiválasztása
1. A hibakeresési nézet megnyitásához kattintson a **tevékenységsáv** Hibakeresés ikonjára a VS Code oldalán.
1. Válassza a **Program indítása (AZDS)** lehetőséget mint aktív hibakeresési konfigurációt.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Ha nem lát Azure Dev Spaces-parancsokat a parancspalettán, győződjön meg róla, hogy [a VS Code Azure Dev Spaceshez készült bővítménye telepítve van](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>A tároló hibakeresése a Kubernetesben
A kód a Kubernetesben való hibakereséséhez nyomja le az **F5** billentyűt.

Az `up` parancshoz hasonlóan a hibakeresés indításakor a kód szinkronizálva lesz a fejlesztési környezetbe, továbbá létrejön és települ egy tároló a Kubernetesben. Ezúttal a hibakereső a távoli tárolóhoz van csatlakoztatva.

> [!Tip]
> A VS Code állapotsora a narancssárga értékre vált, ami azt jelzi, hogy a hibakereső csatolva van. Emellett egy kattintható URL-címet is megjelenít, amely segítségével gyorsan megnyithatja a webhelyet.

![](media/common/vscode-status-bar-url.png)

Állítson be egy töréspontot egy kiszolgálóoldali kódú fájlban, például a `app.get('/api'...`on belül a [`server.js`13. sorában ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13). 

    ```javascript
    app.get('/api', function (req, res) {
        res.send('Hello from webfrontend');
    });
    ```

Frissítse a böngésző oldalát, vagy nyomja meg az *újbóli* elolvasása gombot, és a töréspontot kell megadnia, és képesnek kell lennie a kód beléptetésére.

Ugyanúgy teljes körű hozzáférése van a hibakeresési információkhoz, mint ha helyileg futna a kód (pl. hívási verem, helyi változók, kivételek adatai stb.).

### <a name="edit-code-and-refresh-the-debug-session"></a>A kód szerkesztése és a hibakeresési munkamenet frissítése
Ha a hibakereső aktív, készítse el a kód szerkesztését; Módosítsa például a Hello üzenetet a [`server.js`13. sorába](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) :

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Mentse a fájlt, és a **hibakeresési Műveletek ablaktáblán**kattintson az **Újraindítás** gombra. 

![](media/common/debug-action-refresh.png)

Ahelyett, hogy a kód minden szerkesztése alkalmával újra létrehozna és üzembe helyezne egy új tárolórendszerképet, ami általában sok időt vesz igénybe, az Azure Dev Spaces a hibakeresési munkamenetek között újraindítja a Node.js-folyamatot, hogy gyorsabb szerkesztési/hibakeresési ciklust biztosítson.

Frissítse a webalkalmazást a böngészőben, vagy kattintson a *Kimondás újra* gombra. Az egyedi üzenetnek meg kell jelennie a felhasználói felületen.

### <a name="use-nodemon-to-develop-even-faster"></a>Még gyorsabb fejlesztés a NodeMon használatával
A *Nodemon* egy népszerű eszköz, amelyet a Node.js-fejlesztők használnak a fejlesztés felgyorsítására. Ahelyett, hogy a kiszolgálóoldali kód szerkesztésekor minden alkalommal manuálisan újra kellene indítani a Node-folyamatot, a fejlesztők gyakran úgy konfigurálják a Node-projektet, hogy a *nodemon* monitorozza a fájlok változását, és automatikusan újraindítsa a kiszolgálói folyamatot. Ha ez a módszer működik, a fejlesztő a kódszerkesztés végrehajtása után egyszerűen frissíti a böngészőjét.

Az Azure Dev Spaces használatával számos olyan fejlesztési munkafolyamatot alkalmazhat, amelyeket a helyi fejlesztés során is használ. Ennek bemutatására a `webfrontend` mintaprojektet beállítottuk a *nodemon* használatára (fejlesztői függőségként van konfigurálva a `package.json` fájlban).

Próbálja meg a következőket:
1. Állítsa le a VS Code hibakeresőt.
1. Kattintson a **tevékenységsáv** Hibakeresés ikonjára a VS Code oldalán. 
1. Válassza a **Csatolás (AZDS)** lehetőséget mint aktív hibakeresési konfigurációt.
1. Nyomja le az F5 billentyűt.

Ebben a konfigurációban a tároló úgy van beállítva, hogy elindítsa a *nodemon* eszközt. A kiszolgálói kód módosításakor a *nodemon* automatikusan újraindítja a Node-folyamatot, ahogy azt helyi fejlesztés esetén is tenné. 
1. Módosítsa újra az üdvözlőüzenetet a `server.js` fájlban, majd mentse azt.
1. Frissítse a böngészőt vagy kattintson a *Kimondás újra* gombra, és láthatja, ahogy a módosítások érvénybe lépnek.

**Most tehát rendelkezésére áll egy módszer, amellyel gyorsan iterálhatja a kódot, és közvetlenül a Kubernetesben végezheti a hibakeresést!** A következő részből megtudhatja, hogyan hozhat létre és hívhat meg egy második tárolót.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók a több szolgáltatás fejlesztéséről](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
