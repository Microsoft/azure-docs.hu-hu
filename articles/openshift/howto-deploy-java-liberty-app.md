---
title: Java-alkalmazás üzembe helyezése Open Liberty/WebSphere Liberty-vel egy Azure Red Hat OpenShift 4 fürtön
description: Egy Java-alkalmazás üzembe helyezése nyílt Liberty/WebSphere Liberty-vel egy Azure Red Hat OpenShift 4 fürtön.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, JavaEE, profil, Open-Liberty, WebSphere-Liberty, ARO, openshift, Red Hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212994"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Java-alkalmazás üzembe helyezése Open Liberty/WebSphere Liberty-vel egy Azure Red Hat OpenShift 4 fürtön

Ez az útmutató bemutatja, hogyan futtatható a Java, a Java EE, a [Jakarta EE](https://jakarta.ee/)vagy a [profil](https://microprofile.io/) alkalmazása a nyílt Liberty/WebSphere Liberty futtatókörnyezetben, majd a tároló alkalmazás üzembe helyezése egy Azure Red Hat OpenShift (ARO) 4 fürtön a nyílt Liberty operátor használatával. Ebből a cikkből megtudhatja, hogyan készítheti elő a Liberty-alkalmazást, hogyan építheti fel az Application Docker-rendszerképet, és hogyan futtathatja a tároló alkalmazást egy ARO 4 fürtön.  A nyitott szabadságról a [szabadság projekt megnyitása oldalon](https://openliberty.io/)talál további információt. Az IBM WebSphere Liberty szolgáltatással kapcsolatos további információkért tekintse meg [a WebSphere Liberty-termék oldalát](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához hajtsa végre az alábbi előfeltételeket.

> [!NOTE]
> Az Azure Red Hat OpenShift legalább 40 mag szükséges a OpenShift-fürt létrehozásához és futtatásához. Egy új Azure-előfizetéshez tartozó alapértelmezett Azure-erőforrás-kvóta nem felel meg ennek a követelménynek. Az erőforrás-korlát növeléséhez tekintse meg a [standard kvóta: a határértékek](../azure-portal/supportability/per-vm-quota-requests.md)csökkentése virtuálisgép-sorozat szerint című témakört. Vegye figyelembe, hogy az ingyenes próbaverziós előfizetés nem jogosult a kvóta növelésére, az utólagos elszámolású [előfizetésre való áttérésre](../cost-management-billing/manage/upgrade-azure-subscription.md) , a kvóta növelésének kérelmezése előtt.

1. Készítsen elő egy olyan helyi gépet, amely a UNIX-hoz hasonló operációs rendszer van telepítve (például Ubuntu, macOS).
1. Telepítsen Java SE-implementációt (például [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Telepítse a [Maven](https://maven.apache.org/download.cgi) 3.5.0 vagy újabb verzióját.
1. Telepítse a [Docker](https://docs.docker.com/get-docker/) -t az operációs rendszeréhez.
1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -2.0.75 vagy újabb verzióját.
1. Ellenőrizze és telepítse, [`envsubst`](https://command-not-found.com/envsubst) Ha nem az operációs rendszer előre telepítve van.
1. A minta kódjának klónozása a helyi rendszeren. A minta a [githubon](https://github.com/Azure-Samples/open-liberty-on-aro)található.
1. Kövesse az [Azure Red Hat OpenShift 4-fürt létrehozása](./tutorial-create-cluster.md)című témakör utasításait.

   Bár a "Red Hat pull-kulcs beolvasása" lépés opcionálisként van megjelölve, ez a **cikk szükséges**.  A lekéréses titok lehetővé teszi, hogy az Azure Red Hat OpenShift-fürt megtalálja a nyílt Liberty-kezelőt.

   Ha memória-igényű alkalmazásokat szeretne futtatni a fürtön, a paraméter használatával adja meg a munkavégző csomópontok megfelelő virtuálisgép-méretét `--worker-vm-size` . Például `Standard_E4s_v3` a virtuális gép minimális mérete, hogy a Elasticsearch-kezelőt egy fürtön telepítse. További információkért lásd:

   * [Azure CLI fürt létrehozásához](/cli/azure/aro#az-aro-create)
   * [A virtuális gépek támogatott méretei a memória optimalizálása esetén](./support-policies-v4.md#memory-optimized)
   * [A Elasticsearch operátor telepítésének előfeltételei](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Kapcsolódjon a fürthöz a [Kapcsolódás Azure Red Hat OpenShift 4-fürthöz](./tutorial-connect-cluster.md)című témakör lépéseit követve.
   * Ügyeljen arra, hogy kövesse a "OpenShift CLI telepítése" című szakasz lépéseit, mert a `oc` parancsot a cikk későbbi részében fogjuk használni.
   * Jegyezze fel a fürt konzoljának URL-címét, amely a következőképpen néz ki: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Jegyezze fel a `kubeadmin` hitelesítő adatokat.

1. Ellenőrizze, hogy be tud-e jelentkezni a OpenShift CLI-be a felhasználó jogkivonatával `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>A beépített tároló beállításjegyzékének engedélyezése a OpenShift

Az oktatóanyag lépései létrehoznak egy Docker-rendszerképet, amelyet a OpenShift számára elérhető tároló-beállításjegyzékbe kell leküldeni. A legegyszerűbb lehetőség a OpenShift által biztosított beépített beállításjegyzék használata. A beépített tároló beállításjegyzékének engedélyezéséhez kövesse a [beépített tároló beállításjegyzékének konfigurálása az Azure Red Hat OpenShift 4 rendszerhez](built-in-container-registry.md)című témakör lépéseit. Ebben a cikkben három elemet használunk ezekről a lépésekről.

* Az Azure AD-felhasználó felhasználóneve és jelszava a OpenShift webkonzolba való bejelentkezéshez.
* A kimenet a `oc whoami` OPENSHIFT CLI-be való bejelentkezés lépéseinek követése után következik be.  Ennek az értéknek a neve **HRE-User** .
* A tároló beállításjegyzékének URL-címe.

Jegyezze fel ezeket az elemeket a beépített tároló beállításjegyzékének engedélyezéséhez szükséges lépések végrehajtásával.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>OpenShift-névtér létrehozása a Java-alkalmazáshoz

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében a `kubeadmin` hitelesítő adatok használatával.
2. Navigáljon az **adminisztrációs**  >  **névterekhez**  >  **névtér létrehozása** elemre.
3. Adja meg `open-liberty-demo` a **nevet** , és válassza a **Létrehozás** lehetőséget, amint azt a következő mutatja.

   ![névtér létrehozása](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Rendszergazda létrehozása a bemutató projekthez

A képkezelés mellett a **HRE-felhasználó** rendszergazdai jogosultságokat is kap az erőforrások kezeléséhez az ARO 4 fürt bemutató projektben.  Jelentkezzen be a OpenShift CLI-be, és adja meg a **HRE-felhasználó** számára a szükséges jogosultságokat a következő lépésekkel.

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében a `kubeadmin` hitelesítő adatok használatával.
1. A webkonzol jobb felső részén bontsa ki a bejelentkezett felhasználó helyi menüjét, majd válassza a **bejelentkezési parancs másolása** lehetőséget.
1. Ha szükséges, jelentkezzen be egy új Tab-ablakba.
1. Válassza ki a **jogkivonat megjelenítése** elemet.
1. Másolja az alábbi, a **tokent tartalmazó bejelentkezési** azonosítót a vágólapra, és futtassa azt egy rendszerhéjban az itt látható módon.
1. A következő parancsok végrehajtásával adja meg a `admin` szerepkört a **HRE** a névtérben `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Az Open Liberty OpenShift-kezelő telepítése

Miután létrehozta és csatlakoztatta a fürtöt, telepítse az Open Liberty operátort.  A nyílt Liberty operátor fő kezdőlapja a [githubon](https://github.com/OpenLiberty/open-liberty-operator)található.

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében a `kubeadmin` hitelesítő adatok használatával.
2. Navigáljon a **kezelők**  >  **OperatorHub** , és keresse **meg a nyílt Liberty operátort**.
3. Válassza a **szabadság-kezelő megnyitása** lehetőséget a keresési eredmények közül.
4. Válassza a **Telepítés** gombot.
5. Az előugró **ablak létrehozása-kezelő előfizetésben** a **fürt összes névterét (alapértelmezett)** a **telepítési mód**, a **bétaverzió** a **frissítési csatornához** és az **automatikus** **jóváhagyási stratégia** esetében:

   ![kezelői előfizetés létrehozása a nyílt Liberty-kezelőhöz](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Válassza az **előfizetés** lehetőséget, és várjon egy percet, amíg meg nem jelenik a nyílt Liberty operátor.
7. Tekintse meg a "sikeres" állapotú nyitott Liberty-kezelőt.  Ha nem, a folytatás előtt diagnosztizálja és oldja meg a problémát.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="A nyitott Liberty-t bemutató telepített operátorok telepítve vannak.":::

## <a name="prepare-the-liberty-application"></a>A Liberty-alkalmazás előkészítése

A jelen útmutatóban példaként egy Java EE 8 alkalmazást fogunk használni. A Open Liberty egy [Java EE 8 teljes profillal](https://javaee.github.io/javaee-spec/javadocs/) kompatibilis kiszolgáló, így könnyen futtatható az alkalmazás.  A nyílt Liberty is [Jakarta EE 8 teljes profil kompatibilis](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Az alkalmazás futtatása nyitott szabadságon

Ha az alkalmazást nyitott szabadságon szeretné futtatni, létre kell hoznia egy nyitott Liberty-kiszolgáló konfigurációs fájlját, hogy a [Liberty Maven beépülő modul](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) becsomagolja az alkalmazást az üzembe helyezéshez. A Liberty Maven beépülő modul nem szükséges az alkalmazás OpenShift való telepítéséhez.  Ebben a példában azonban a nyílt Liberty fejlesztői (dev) módban fogjuk használni.  A fejlesztői mód lehetővé teszi az alkalmazás helyi futtatását. Hajtsa végre a következő lépéseket a helyi számítógépen.

1. Másolás `2-simple/src/main/liberty/config/server.xml` a `1-start/src/main/liberty/config` meglévő nulla hosszúságú fájl felülírásával. Ezzel `server.xml` konfigurálja a nyílt Liberty-kiszolgálót a Java EE-funkciókkal.
1. Másolás ide: `2-simple/pom.xml` `1-start/pom.xml` .  Ez a lépés hozzáadja a `liberty-maven-plugin` -t a Pom-hez.
1. Módosítsa a könyvtárat a `1-start` helyi klónra.
1. Futtassa a parancsot `mvn clean package` egy konzolon a War-csomag létrehozásához `javaee-cafe.war` a címtárban `./target` .
1. `mvn liberty:dev`Indítsa el a nyílt Liberty fejlesztői módban való megnyitását.
1. Várjon, amíg a kiszolgáló elindul. A konzol kimenetének a következő üzenettel kell végződnie:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. A böngészőben nyissa meg `http://localhost:9080/` az alkalmazás kezdőlapját. Az alkalmazás az alábbi képhez hasonlóan fog kinézni:

   ![JavaEE Cafe webes felhasználói felület](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Nyomja le a **Control-C** billentyűt az alkalmazás leállításához és a Liberty-kiszolgáló megnyitásához.

A `2-simple` helyi klón könyvtára megjeleníti a Maven-projektet a fenti módosításokkal.

## <a name="prepare-the-application-image"></a>Az alkalmazás rendszerképének előkészítése

A Liberty-alkalmazás egy ARO 4 fürtön való üzembe helyezéséhez és futtatásához tárolóba helyezése az alkalmazást Docker-rendszerképként a [nyílt Liberty-tárolók](https://github.com/OpenLiberty/ci.docker) rendszerképein vagy a [WebSphere Liberty Container-lemezképeken](https://github.com/WASdev/ci.docker).

### <a name="build-application-image"></a>Alkalmazás rendszerképének összeállítása

Az alkalmazás rendszerképének létrehozásához hajtsa végre az alábbi lépéseket:

1. Módosítsa a könyvtárat a `2-simple` helyi klónra.
2. Futtassa `mvn clean package` az alkalmazást az alkalmazás előkészítéséhez.
3. Futtassa az alábbi parancsok egyikét az alkalmazás rendszerképének létrehozásához.
   * Build az Open Liberty alapképpel:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Build a WebSphere Liberty Base rendszerképpel:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Az alkalmazás helyi futtatása a Docker-vel

Mielőtt telepítené a tároló alkalmazást egy távoli fürtön, futtassa a helyi Docker-vel, és ellenőrizze, hogy működik-e:

1. Futtassa a parancsot a `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` konzolon.
2. Várjon, amíg a Liberty-kiszolgáló elindul, és az alkalmazás üzembe helyezése sikeresen megtörtént.
3. A böngészőben nyissa meg `http://localhost:9080/` az alkalmazás kezdőlapját.
4. Nyomja le a **Control-C** billentyűt az alkalmazás és a Liberty-kiszolgáló leállításához.

### <a name="push-the-image-to-the-container-image-registry"></a>A rendszerkép leküldése a tároló rendszerképének Hivatalához

Ha elégedett az alkalmazás állapotával, küldje el a beépített tároló rendszerkép-beállításjegyzékbe az alábbi utasításokat követve.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Jelentkezzen be az OpenShift CLI-be az Azure AD-felhasználóként

1. Jelentkezzen be a böngésző OpenShift webkonzolján egy Azure AD-felhasználó hitelesítő adataival.

   1. A konzolba való bejelentkezéshez használjon InPrivate, inkognitóban vagy más, a böngészőablakhoz hasonló szolgáltatást.
   1. **OpenID** kiválasztása

   > [!NOTE]
   > Jegyezze fel a bejelentkezéshez használt felhasználónevet és jelszót. Ez a Felhasználónév és jelszó rendszergazdaként fog működni a többi művelethez és más cikkekhez.
1. Jelentkezzen be az OpenShift CLI-vel a következő lépésekkel.  A vita esetében ez a folyamat ismert `oc login` .
   1. A webkonzol jobb felső részén bontsa ki a bejelentkezett felhasználó helyi menüjét, majd válassza a **bejelentkezési parancs másolása** lehetőséget.
   1. Ha szükséges, jelentkezzen be egy új Tab-ablakba.
   1. Válassza ki a **jogkivonat megjelenítése** elemet.
   1. Másolja az alábbi, a **tokent tartalmazó bejelentkezési** azonosítót a vágólapra, és futtassa azt egy rendszerhéjban az itt látható módon.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>A tároló rendszerképének leküldése a tároló-beállításjegyzékbe a OpenShift

Futtassa ezeket a parancsokat a rendszerkép tároló-beállításjegyzékbe való leküldéséhez a OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

A sikeres kimenet az alábbihoz hasonlóan fog kinézni.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Az alábbi paranccsal leküldheti a képet a beépített tároló rendszerkép-beállításjegyzékbe.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Alkalmazás üzembe helyezése az ARO 4 fürtön

Most már üzembe helyezheti a minta Liberty-alkalmazást az előfeltételeken végzett munka során korábban létrehozott Azure Red Hat OpenShift 4 fürtön.

### <a name="deploy-the-application-from-the-web-console"></a>Az alkalmazás üzembe helyezése a webkonzolról

Mivel a Liberty-alkalmazások kezeléséhez a nyílt Liberty operátort használjuk, létre kell hozni az *egyéni erőforrás-definíció* egy példányát, amelynek típusa "OpenLibertyApplication". A kezelő ezt követően gondoskodik az üzembe helyezéshez szükséges OpenShift-erőforrások kezelésének minden aspektusáról.

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében az Azure AD-felhasználó hitelesítő adataival.
1. Bontsa ki a **Home (Kezdőlap**) lehetőséget, és válassza a   >  **Open-Liberty-bemutató** projektet.
1. Navigáljon a **kezelők** által  >  **telepített operátorokhoz**.
1. A lap közepén válassza a **szabadság-kezelő megnyitása** lehetőséget.
1. A lap közepén válassza a **szabadság alkalmazás megnyitása** lehetőséget.  A felhasználói felületen lévő elemek navigációja a használatban lévő technológiák tényleges betárolási hierarchiáját tükrözi.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java-tároló](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Válassza a **OpenLibertyApplication létrehozása** lehetőséget.
1. Cserélje le a generált YAML a tiéd értékre, amely a következő helyen található: `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Válassza a **Létrehozás** lehetőséget. A rendszer visszaadja a OpenLibertyApplications listáját.
1. Válassza a **JavaEE-Café-Simple** lehetőséget.
1. A lap közepén válassza az **erőforrások** lehetőséget.
1. A táblázatban válassza ki a **JavaEE-Café-Simple** hivatkozást az **útvonal** **típusának** megfelelően.
1. A megnyíló oldalon válassza **az alábbi hivatkozást**.

Ekkor megnyílik az alkalmazás kezdőlapja a böngészőben.

### <a name="delete-the-application-from-the-web-console"></a>Az alkalmazás törlése a webkonzolról

Ha elkészült az alkalmazással, a következő lépésekkel törölheti az alkalmazást a megnyitott Műszakból.

1. A bal oldali navigációs panelen bontsa ki az **operátorok** bejegyzést.
1. Válassza a **telepített operátorok** lehetőséget.
1. Válassza a **szabadság-kezelő megnyitása** lehetőséget.
1. A lap közepén válassza a **nyílt Liberty-alkalmazás** lehetőséget.
1. Válassza ki a függőleges három pontot (három függőleges pont), majd válassza a **OpenLiberty-alkalmazás törlése** lehetőséget.

### <a name="deploy-the-application-from-cli"></a>Az alkalmazás üzembe helyezése a parancssori felületről

A webkonzol grafikus felhasználói felületének használata helyett üzembe helyezheti az alkalmazást a parancssori felületen. Ha még nem tette meg, töltse le és telepítse a `oc` parancssori eszközt az alábbi Red Hat dokumentációs [első lépések a CLI-vel](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében az Azure AD-felhasználó hitelesítő adataival.
2. Jelentkezzen be az OpenShift CLI-be az Azure AD-felhasználó jogkivonatával.
3. Módosítsa a könyvtárat a `2-simple` helyi klónra, és futtassa a következő parancsokat a Liberty-alkalmazás az ARO 4 fürtön való üzembe helyezéséhez.  A parancs kimenete is beágyazottként jelenik meg.

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

4. `1/1`A folytatás előtt ellenőrizze, hogy az oszlop alatt látható-e `READY` .  Ha nem, a folytatás előtt vizsgálja meg és oldja meg a problémát.
5. Fedezze fel az alkalmazáshoz tartozó útvonal gazdagépét az `oc get route` paranccsal, az itt látható módon.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Ha a Liberty-alkalmazás már fut, nyissa meg a **Route Host** kimenetét a böngészőben, és látogasson el az alkalmazás kezdőlapjára.

### <a name="delete-the-application-from-cli"></a>Az alkalmazás törlése a parancssori felületről

Törölje az alkalmazást a CLI-ből a parancs végrehajtásával.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az ARO-fürtöt az [oktatóanyag: Azure Red Hat OpenShift 4-fürt törlése](./tutorial-delete-cluster.md) című témakör lépéseit követve.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan teheti meg a következőket:
> [!div class="checklist"]
>
> * A Liberty-alkalmazás előkészítése
> * Az alkalmazás rendszerképének összeállítása
> * A tároló alkalmazás futtatása ARO 4 fürtön a grafikus felhasználói felület és a CLI használatával

A jelen útmutatóban használt hivatkozásokból többet is megtudhat:

* [Szabadság megnyitása](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Szabadság-kezelő megnyitása](https://github.com/OpenLiberty/open-liberty-operator)
* [A Liberty-kiszolgáló konfigurációjának megnyitása](https://openliberty.io/docs/ref/config/)
* [Liberty Maven beépülő modul](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [A Liberty Container-lemezképek megnyitása](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty-tároló lemezképei](https://github.com/WASdev/ci.docker)