---
title: Java-alkalmazás üzembe helyezése az Open Azure Red Hat OpenShift/WebSphereVel egy 4 Azure Red Hat OpenShift fürtön
description: Java-alkalmazás üzembe helyezése az Open Azure Red Hat OpenShift/WebSphereVel egy 4 Azure Red Hat OpenShift fürtön.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-microprofile, websphere-mert, aro, openshift, red hat
ms.openlocfilehash: 2a308c7de754f395a3ef8a1bd97ed2441d27d21d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783574"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Java-alkalmazás üzembe helyezése az Open Azure Red Hat OpenShift/WebSphereVel egy 4 Azure Red Hat OpenShift fürtön

Ez az útmutató bemutatja, hogyan futtathatja Java-, Java EE-, [Jakarta EE-](https://jakarta.ee/)vagy [MicroProfile-alkalmazását](https://microprofile.io/) az Open WebSphereÁsára futtatott futási környezetben, majd hogyan helyezheti üzembe a tárolóba helyezett alkalmazást egy Azure Red Hat OpenShift- (ARO-) 4-fürtön az Open Container Operator használatával. Ez a cikk végigkényéri egy Application Application előkészítésén, az alkalmazás Docker-rendszerképének elkészítésén és a tárolóba ezett alkalmazás egy ARO 4-fürtön való futtatásán.  További részletekért lásd az Open [Merton projektoldalt.](https://openliberty.io/) Az IBM WebSphere Fogról további részleteket a [WebSphere Fog termékoldalán talál.](https://www.ibm.com/cloud/websphere-liberty)

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Előfeltételek

Az útmutató sikeres végigvezeti az alábbi előfeltételeken.

> [!NOTE]
> Azure Red Hat OpenShift OpenShift-fürt létrehozásához és futtatásához legalább 40 mag szükséges. Az új Azure-előfizetések alapértelmezett Azure-erőforráskvótája nem felel meg ennek a követelménynek. Az erőforráskorlát növelésének kérését lásd: [Standard kvóta: Korlátok növelése virtuálisgép-sorozatok szerint.](../azure-portal/supportability/per-vm-quota-requests.md) Vegye figyelembe, hogy az ingyenes próba-előfizetés [](../cost-management-billing/manage/upgrade-azure-subscription.md) nem jogosult a kvóta növelésére, ezért a kvóta növelésének kérelmezését megelőzően frissítsen használat szerint fizetett előfizetésre.

1. Helyi gép előkészítése telepített Unix-szerű operációs rendszerrel (például Ubuntu vagy macOS).
1. Telepítsen egy Java SE-implementációt (például [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9).](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)
1. Telepítse [a Maven](https://maven.apache.org/download.cgi) 3.5.0-s vagy újabb verzióját.
1. Telepítse [a Docker-t](https://docs.docker.com/get-docker/) az operációs rendszer számára.
1. Telepítse [az Azure CLI](/cli/azure/install-azure-cli) 2.0.75-ös vagy újabb frissítését.
1. Ellenőrizze és telepítse, hogy nincs-e előre telepítve [`envsubst`](https://command-not-found.com/envsubst) az operációs rendszerben.
1. Klónozza a minta kódját a helyi rendszeren. A minta a [GitHubon található.](https://github.com/Azure-Samples/open-liberty-on-aro)
1. Kövesse a [Create an Azure Red Hat OpenShift 4 cluster (4 fürt létrehozása) cikk utasításait.](./tutorial-create-cluster.md)

   Bár a "Get a Red Hat pull secret" lépés opcionálisként van megcímkézve, ez szükséges ehhez a **cikkhez:**.  A lekért titkos gombra való Azure Red Hat OpenShift, hogy a fürt megtalálja az Open Operator operátort.

   Ha memóriaigényes alkalmazásokat tervez futtatni a fürtön, a paraméterrel adja meg a munkavégző csomópontok számára megfelelő `--worker-vm-size` virtuálisgép-méretet. A például a virtuális gép minimális mérete az `Standard_E4s_v3` Elasticsearch-operátor fürtön való telepítéséhez. További információkért lásd:

   * [Azure CLI fürt létrehozásához](/cli/azure/aro#az_aro_create)
   * [A memóriaoptimalizált virtuálisgép-méretek támogatott méretei](./support-policies-v4.md#memory-optimized)
   * [Az Elasticsearch-operátor telepítésének előfeltételei](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Csatlakozzon a fürthöz a Csatlakozás 4 Azure Red Hat OpenShift [lépéseit követve.](./tutorial-connect-cluster.md)
   * Mindenképpen kövesse az "OpenShift parancssori felület telepítése" lépéseit, mert a cikk későbbi, `oc` parancsát fogjuk használni.
   * Írja fel a fürtkonzol URL-címét, amely így néz `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` ki: .
   * Jegyezze fel a `kubeadmin` hitelesítő adatokat.

1. Ellenőrizze, hogy be tud-e jelentkezni az OpenShift cli-be a felhasználó `kubeadmin` jogkivonatával.

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Az OpenShift beépített tárolójegyzékének engedélyezése

Az oktatóanyag lépései egy Docker-rendszerképet hoznak létre, amelyet az OpenShift számára elérhető tároló-beállításjegyzékbe kell leküldeni. A legegyszerűbb megoldás az OpenShift által biztosított beépített beállításjegyzék használata. A beépített tároló-beállításjegyzék engedélyezéséhez kövesse A beépített tároló-beállításjegyzék konfigurálása a [4 Azure Red Hat OpenShift lépéseit.](built-in-container-registry.md) Ebben a cikkben három elemet használunk a lépésekből.

* Az OpenShift webkonzolra való bejelentkezéshez használt Azure AD-felhasználó felhasználóneve és jelszava.
* A kimenete az OpenShift CLI-be való bejelentkezés `oc whoami` lépéseit követve.  Ezt az értéket **aad-usernek nevezzük** a vitafórumhoz.
* A tárolójegyzék URL-címe.

Figyelje meg ezeket az elemeket a beépített tároló-beállításjegyzék engedélyezéséhez szükséges lépések befejezése után.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>OpenShift-névtér létrehozása a Java-alkalmazáshoz

1. Jelentkezzen be az OpenShift webkonzolra a böngészőből a `kubeadmin` hitelesítő adatokkal.
2. Lépjen az **Adminisztrációs**  >  **névterek**  >  **Névtér létrehozása lapra.**
3. A Név `open-liberty-demo` mezőben adja meg **a nevet,** és **válassza a Létrehozás** lehetőséget, ahogy az alább látható.

   ![névtér létrehozása](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Rendszergazda létrehozása a bemutató projekthez

A rendszerképkezelés mellett **az aad-felhasználó** rendszergazdai engedélyeket is kap az erőforrások kezeléséhez az ARO 4-fürt bemutatóprojektében.  Jelentkezzen be az OpenShift cli-be, és adja meg **az aad-felhasználónak a** szükséges jogosultságokat az alábbi lépésekben.

1. Jelentkezzen be az OpenShift webkonzolra a böngészőből a `kubeadmin` hitelesítő adatokkal.
1. A webkonzol jobb felső menüjében bontsa ki a bejelentkezett felhasználó helyi menüjét, majd válassza a Bejelentkezési parancs **másolása lehetőséget.**
1. Ha szükséges, jelentkezzen be ugyanazokkal a felhasználóval egy új lapablakba.
1. Válassza **a Jogkivonat megjelenítése lehetőséget.**
1. Másolja a Login (Bejelentkezés) alatt látható **értéket** a vágólapra, és futtassa egy rendszerhéjban az itt látható módon.
1. A következő parancsok végrehajtásával adjon szerepkört `admin` **az aad-felhasználónak a** névtérben. `open-liberty-demo`

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Az Open OpenShift-operátor telepítése

Miután létrehozta a fürtöt, és csatlakozott a fürthöz, telepítse az Open Azok operátorát.  Az Open Mostantól operátor fő kezdőlapja a [GitHubon található.](https://github.com/OpenLiberty/open-liberty-operator)

1. Jelentkezzen be az OpenShift webkonzolra a böngészőből a hitelesítő `kubeadmin` adatokkal.
2. Lépjen a Operators OperatorHub **(Operátorok**  >  **operátora) lapra,** és keressen rá az **Open Mert operátor kifejezésre.**
3. A keresési eredmények közül válassza ki az **Open Merte Operator** gombra.
4. Válassza a **Telepítés** gombot.
5. Az előugró **Operátor-előfizetés** létrehozása ablakban jelölje be a Fürt összes névterét **(alapértelmezett)** a Telepítési **mód,** a **bétaverzió** frissítési **csatorna** és **automatikus** jóváhagyási **stratégia beállításnál:**

   ![operátori előfizetés létrehozása az Open Fog Operátorhoz](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Válassza **a Feliratkozás** lehetőséget, és várjon egy-két percet, amíg megjelenik az Open Open The Operator operátor.
7. A "Sikeres" állapottal figyelje meg az OpenRelked operátort.  Ha nem, a folytatás előtt diagnosztizálja és oldja meg a problémát.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Telepített operátorok, amelyeken látható, hogy az Open Fog telepítve van.":::

## <a name="prepare-the-liberty-application"></a>A Rendszeralkalmazás előkészítése

Ebben az útmutatóban egy Java EE 8-alkalmazást fogunk használni példaként. Az Open Fog egy [Java EE 8](https://javaee.github.io/javaee-spec/javadocs/) teljes profillal kompatibilis kiszolgáló, amely könnyedén futtathatja az alkalmazást.  Az Open Pedig [Jakarta EE 8 teljes profillal kompatibilis.](https://jakarta.ee/specifications/platform/8/apidocs/)

### <a name="run-the-application-on-open-liberty"></a>Az alkalmazás futtatása az Open Fogon

Ahhoz, hogy az alkalmazást az Open Képesben futtassa, létre kell hoznia egy Open Ér-kiszolgáló konfigurációs fájlját, hogy a [Rendszer Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) beépülő modul becsomagolja az alkalmazást az üzembe helyezéshez. Az alkalmazás OpenShiften való üzembe helyezéséhez nincs szükség a Arram Maven beépülő modulra.  Ebben a példában azonban ezt fogjuk használni az Open Dev (dev) módban.  A fejlesztői móddal egyszerűen helyileg futtathatja az alkalmazást. A helyi számítógépen kövesse az alábbi lépéseket.

1. Másolja `2-simple/src/main/liberty/config/server.xml` a `1-start/src/main/liberty/config` következőre: , felülírva a meglévő, nulla hosszúságú fájlt. Ez java EE-funkciókkal konfigurálja `server.xml` az OpenScript-kiszolgálót.
1. Másolja a `2-simple/pom.xml` `1-start/pom.xml` gombra.  Ez a lépés hozzáadja a `liberty-maven-plugin` et a POM-hoz.
1. Módosítsa a könyvtárat `1-start` a helyi klón könyvtárába.
1. Futtassa `mvn clean package` a következőt egy konzolon egy War-csomag létrehozásához a `javaee-cafe.war` `./target` könyvtárban: .
1. Futtassa `mvn liberty:dev` a következőt: Az Open Dev Mode megnyitásához.
1. Várjon, amíg a kiszolgáló elindul. A konzol kimenetének a következő üzenettel kell végződni:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Nyissa `http://localhost:9080/` meg a böngészőben az alkalmazás kezdőlapját. Az alkalmazás az alábbi képen láthatóhoz hasonlóan fog kinézni:

   ![JavaEE Fog Webes felhasználói felület](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Nyomja **le a Control-C billentyűvel** állítsa le az alkalmazást, és nyissa meg a Kiszolgálót.

A helyi `2-simple` klón könyvtárában megjelenik a Maven-projekt, és a fenti módosítások már alkalmazva vannak.

## <a name="prepare-the-application-image"></a>Az alkalmazás rendszerképének előkészítése

Ha egy ARO 4-fürtön kell üzembe helyeznie és futtatnia a Rendszeralkalmazást, tárolóba kell helyeznie az alkalmazást Docker-rendszerképként [Open Container Images](https://github.com/OpenLiberty/ci.docker) vagy [WebSphere Fog tárolólemezképek használatával.](https://github.com/WASdev/ci.docker)

### <a name="build-application-image"></a>Alkalmazás rendszerképének összeállítása

Az alkalmazás rendszerképének felépítéséhez kövesse az alábbi lépéseket:

1. Módosítsa a könyvtárat `2-simple` a helyi klón könyvtárába.
2. Az `mvn clean package` alkalmazás csomagolásához futtassa a következőt: .
3. Futtassa az alábbi parancsok egyikét az alkalmazás rendszerképének felépítéséhez.
   * Build az Open Build alapként való rendszerképe:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Build a WebSphere Arra alapként való rendszerképe:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Az alkalmazás helyi futtatása a Docker segítségével

Mielőtt üzembe helyez egy tárolóba helyezett alkalmazást egy távoli fürtön, futtassa a következőt a helyi Docker segítségével annak ellenőrzéséhez, hogy működik-e:

1. Futtassa `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` a következőt a konzolon: .
2. Várjon, amíg a Rendszer-kiszolgáló elindul, és az alkalmazás üzembe helyezése sikeres lesz.
3. Nyissa `http://localhost:9080/` meg a böngészőben az alkalmazás kezdőlapját.
4. Az alkalmazás és a Kiszolgáló leállításához nyomja le a **Control-C** billentyűt.

### <a name="push-the-image-to-the-container-image-registry"></a>A rendszerkép leküldése a tároló rendszerkép-beállításjegyzékbe

Ha elégedett az alkalmazás állapotával, az alábbi utasításokat követve leküldi azt a beépített tároló rendszerkép-beállításjegyzékbe.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Jelentkezzen be az OpenShift CLI-be Azure AD-felhasználóként

1. Jelentkezzen be az OpenShift webkonzolra a böngészőből egy Azure AD-felhasználó hitelesítő adataival.

   1. Az InPrivate, Inkognitó vagy más ezzel egyenértékű böngészőablak funkcióval jelentkezzen be a konzolra.
   1. Openid **kiválasztása**

   > [!NOTE]
   > Jegyezze fel a bejelentkezéshez használt felhasználónevet és jelszót. Ez a felhasználónév és jelszó rendszergazdaként fog működni a jelen és más cikkek más műveletei során.
1. Jelentkezzen be az OpenShift CLI-hez a következő lépésekkel.  Ennek a folyamatnak a neve `oc login` .
   1. A webkonzol jobb felső menüjében bontsa ki a bejelentkezett felhasználó helyi menüjét, majd válassza a Bejelentkezési parancs **másolása lehetőséget.**
   1. Ha szükséges, jelentkezzen be ugyanaz a felhasználó egy új lapablakába.
   1. Válassza **a Jogkivonat megjelenítése lehetőséget.**
   1. Másolja a Login (Bejelentkezés) alatt látható **értéket** a vágólapra, és futtassa egy rendszerhéjban az itt látható módon.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>A tároló rendszerképének leküldése az OpenShift tároló-beállításjegyzékbe

A következő parancsok végrehajtásával leküldi a rendszerképet az OpenShift tároló-beállításjegyzékének.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

A sikeres kimenet az alábbihoz hasonló lesz.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

A következő paranccsal lekért rendszerképet a beépített tároló rendszerkép-beállításjegyzékbe.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Alkalmazás üzembe helyezése az ARO 4-fürtön

Most már üzembe helyezheti a MintAten-alkalmazást a Azure Red Hat OpenShift 4 fürtben, amit az előfeltételek alkalmazásakor hozott létre.

### <a name="deploy-the-application-from-the-web-console"></a>Az alkalmazás üzembe helyezése a webkonzolról

Mivel az Open Fog operátort használjuk a Tol-alkalmazások kezeléséhez, létre kell hoznunk az egyéni erőforrás-definíciójának "OpenLibertyApplication" típusú példányát. A kezelő ezután gondoskodik az üzembe helyezéshez szükséges OpenShift-erőforrások kezelésével kapcsolatos összes szempontról.

1. Jelentkezzen be az OpenShift webkonzolra a böngészőből az Azure AD-felhasználó hitelesítő adataival.
1. **Bontsa ki a Home (Kezdőlap)** gombra, majd válassza a **Projects**  >  **open-open-open-demo (Projektek nyílt-mező-bemutató) lehetőséget.**
1. Lépjen az **Operátorok**  >  **telepített operátorok lapra.**
1. Az oldal közepén válassza az **OpenBro Operator** lehetőséget.
1. Az oldal közepén válassza az **Open Application (Open Application) gombra.**  A felhasználói felület elemeinek navigációja tükrözi a ténylegesen használt technológiák el tartalmazott hierarchiáját.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java-eltár](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Válassza **az OpenLibertyApplication létrehozása lehetőséget**
1. Cserélje le a létrehozott yaml-t a következő helyen található saját yaml-fájlra: `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Válassza a **Létrehozás** lehetőséget. A listában az OpenLibertyApplications megjelenik.
1. Válassza **a javaee-bil-simple lehetőséget.**
1. Az oldal közepén válassza az Erőforrások **lehetőséget.**
1. A táblázatban válassza a **javaee-bil-simple** hivatkozást a Kind of Route **(Útvonal** **fajtája) beállítással.**
1. A megnyíló oldalon kattintson a Hely alatti **hivatkozásra.**

Az alkalmazás kezdőlapja megnyílik a böngészőben.

### <a name="delete-the-application-from-the-web-console"></a>Az alkalmazás törlése a webkonzolról

Ha végzett az alkalmazással, az alábbi lépésekkel törölheti az alkalmazást az Open Shiftből.

1. A bal oldali navigációs panelen bontsa ki az **Operátorok bejegyzést.**
1. Válassza **a Telepített operátorok lehetőséget.**
1. Válassza **a Megnyitás a Mező operátort** lehetőséget.
1. Az oldal közepén válassza az **Open Application (Open Application) lehetőséget.**
1. Válassza a függőleges három pont (három pont), majd a **Delete OpenLiberty Application (OpenLiberty-alkalmazás törlése) lehetőséget.**

### <a name="deploy-the-application-from-cli"></a>Az alkalmazás üzembe helyezése a cli-ről

A webkonzol grafikus felhasználói felületének használata helyett üzembe helyezheti az alkalmazást a CLI-ről. Ha még nem tette meg, töltse le és telepítse a parancssori eszközt a `oc` Következő Red Hat-dokumentáció Getting Started with the CLI (Ismerkedés a [parancssori felület használatával) dokumentumban.](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)

1. Jelentkezzen be az OpenShift webkonzolra a böngészőből az Azure AD-felhasználó hitelesítő adataival.
2. Jelentkezzen be az OpenShift CLI-be az Azure AD-felhasználó jogkivonatával.
3. Módosítsa a könyvtárat a helyi klón könyvtárára, és futtassa a következő parancsokat a Saját alkalmazás `2-simple` ARO 4-fürtön való üzembe helyezéséhez.  A parancs kimenete beágyazottan is megjelenik.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. A folytatás előtt `1/1` ellenőrizze, hogy látható-e `READY` az oszlop alatt.  Ha nem, a folytatás előtt vizsgálja meg és oldja meg a problémát.
5. Az paranccsal derítheti fel az alkalmazás útvonalának `oc get route` gazdagépét az itt látható módon.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Ha a Rendszeralkalmazás már működik, nyissa meg a **Route Host** kimenetét a böngészőben, és nyissa meg az alkalmazás kezdőlapját.

### <a name="delete-the-application-from-cli"></a>Az alkalmazás törlése a CLI-ről

Törölje az alkalmazást a parancssori felületről a következő paranccsal.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az ARO-fürtöt az [Oktatóanyag: 4-es](./tutorial-delete-cluster.md) fürt Azure Red Hat OpenShift lépéseit követve

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban a következőt tanulta meg:
> [!div class="checklist"]
>
> * A Rendszeralkalmazás előkészítése
> * Az alkalmazás rendszerképének összeállítása
> * A tárolóba ezett alkalmazás futtatása egy ARO 4-fürtön a grafikus felhasználói felület és a cli használatával

Az útmutatóban használt hivatkozásokból többet is megtudhat:

* [Open Fog](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Fog Operátor](https://github.com/OpenLiberty/open-liberty-operator)
* [OpenBro Server Configuration](https://openliberty.io/docs/ref/config/)
* [Érdi Maven beépülő modul](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [A Container Images megnyitása](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty Container Images](https://github.com/WASdev/ci.docker)