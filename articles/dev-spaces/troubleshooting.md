---
title: Hibaelhárítás
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Az Azure dev Spaces engedélyezése és használata során felmerülő gyakori problémák elhárítása és megoldása
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 14ced0c66b42b6f18c946d0c75091be1af5598f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102197792"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Az Azure dev Spaces hibaelhárítása

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Ez az útmutató az Azure dev Spaces használatakor esetlegesen előforduló gyakori problémákról tartalmaz információkat.

Ha problémába ütközik az Azure dev Spaces használata során, hozzon létre egy [problémát az Azure dev Spaces GitHub-tárházban](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Előkészületek

Ha hatékonyabban szeretné elhárítani a problémákat, érdemes lehet részletesebb naplókat létrehozni a felülvizsgálathoz.

A Visual Studióban állítsa a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` környezeti változót 1-re. A környezeti változó érvénybe léptetéséhez indítsa újra a Visual studiót. Ha engedélyezve van, a részletes naplók a `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` címtárba íródnak.

A CLI-ben további információkat adhat meg a parancs végrehajtása során a kapcsoló használatával `--verbose` . További részletes naplókat is megkereshet a alkalmazásban `%TEMP%\Azure Dev Spaces` . Mac gépen a *temp* könyvtár `echo $TMPDIR` egy terminál-ablakból futtatható. Linux rendszerű számítógépeken általában a *temp* könyvtár `/tmp` . Továbbá ellenőrizze, hogy a naplózás engedélyezve van-e az [Azure CLI konfigurációs fájljában](/cli/azure/azure-cli-configuration#cli-configuration-values-and-environment-variables).

Az Azure dev Spaces is működik a legjobban, ha egyetlen példányt vagy Pod-t tesz elérhetővé. A `azds.yaml` fájl tartalmaz egy *replicaCount*-beállítást, amely megadja, hogy a Kubernetes hány hüvelyt futtat a szolgáltatásban. Ha úgy módosítja a *replicaCount* , hogy úgy konfigurálja az alkalmazást, hogy több hüvelyt futtasson egy adott szolgáltatáshoz, akkor a hibakereső az első hüvelyhez csatlakozik, amikor betűrendbe van sorolva. A hibakereső egy másik Pod-hoz csatlakozik, amikor az eredeti Pod újrahasznosítja, ami valószínűleg váratlan viselkedést eredményez.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Az Azure dev Spaces engedélyezésekor felmerülő gyakori problémák

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Hiba: "nem sikerült létrehozni az Azure dev Spaces Controllert"

Ez a hiba akkor fordulhat elő, ha valami nem stimmel a vezérlő létrehozásával. Ha átmeneti hiba történt, törölje, majd hozza létre újra a vezérlőt a javításhoz.

A vezérlőt is törölheti:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Vezérlő törléséhez használja az Azure dev Spaces CLI-t. A vezérlőt nem lehet törölni a Visual studióból. Nem telepítheti az Azure dev Spaces CLI-t a Azure Cloud Shell, így nem törölhet vezérlőt a Azure Cloud Shellból.

Ha nem telepítette az Azure dev Spaces CLI-t, először telepítse azt a következő parancs használatával, majd törölje a vezérlőt:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>A vezérlő létrehozása sikertelen a vezérlő nevének hossza miatt

Az Azure dev Spaces-vezérlő neve nem lehet 31 karakternél hosszabb. Ha a vezérlő neve meghaladja a 31 karaktert, amikor egy AK-fürtön engedélyezi a dev Spaces-t, vagy létrehoz egy vezérlőt, hibaüzenet jelenik meg. Például:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

A probléma megoldásához hozzon létre egy másik nevű vezérlőt. Például:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>A fejlesztői szóközök engedélyezése a Windows-csomópontok AK-fürthöz való hozzáadásakor

Jelenleg az Azure dev Spaces kizárólag Linux-hüvelyeken és-csomópontokon futtatható. Ha egy AK-fürthöz Windows-csomópont tartozik, akkor biztosítania kell, hogy az Azure dev Spaces hüvelyek csak Linux-csomópontokon legyenek ütemezve. Ha egy Azure dev Spaces Pod Windows-csomóponton való futtatásra van ütemezve, a pod nem indul el, és a fejlesztői szóközök engedélyezése sikertelen lesz.

A probléma megoldásához [vegyen fel egy Taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AK-fürtbe, hogy a Linux-hüvelyek ne legyenek ütemezve Windows-csomóponton való futtatásra.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Hiba: nem található a fürtön kész állapotú, nem fertőzött Linux-csomópontok. A hüvelyek "azds" névtérben való üzembe helyezéséhez kész állapotban kell lennie legalább egy nem fertőzött linuxos csomópontnak. "

Az Azure dev Spaces nem tudta létrehozni a vezérlőt az AK-fürtön, mert nem talált egy nem fertőzött csomópontot *készenléti* állapotban a hüvelyek ütemezett beléptetéséhez. Az Azure dev Spaces-ben legalább egy Linux-csomópontra *kész* állapotban kell lennie, amely lehetővé teszi a hüvelyek ütemezését a tolerancia megadása nélkül.

A probléma megoldásához [frissítse a szennyező konfigurációt](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AK-fürtön, hogy legalább egy Linux-csomópont lehetővé tegye a hüvelyek ütemezését a tolerancia megadása nélkül. Győződjön meg arról is, hogy legalább egy Linux-csomópont, amely lehetővé teszi a hüvelyek ütemezését, a tolerálás nélkül *kész* állapotban van. Ha a csomópont hosszú időt vesz elérhetővé a *kész* állapot elérése érdekében, próbálja meg újraindítani a csomópontot.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Hiba: az "Azure dev Spaces CLI nincs megfelelően telepítve" az az AK use-dev-Spaces futtatásakor

Az Azure dev Spaces parancssori felületének frissítése megváltoztatta a telepítési útvonalat. Ha a 2.0.63-nél korábbi Azure CLI-verziót használja, akkor ez a hiba látható. Az Azure CLI verziójának megjelenítéséhez használja a következőt: `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Annak ellenére, hogy a `az aks use-dev-spaces` 2.0.63 előtt az Azure CLI egy verziójával fut, a telepítés sikeres lesz. Továbbra is `azds` problémák nélkül használhatja tovább.

A probléma megoldásához frissítse az [Azure CLI](/cli/azure/install-azure-cli) telepítését 2.0.63 vagy újabb verzióra. Ez a frissítés fogja megoldani a futtatásakor kapott hibaüzenetet `az aks use-dev-spaces` . Másik lehetőségként továbbra is használhatja az Azure CLI aktuális verzióját és az Azure dev Spaces CLI-t.

### <a name="error-unable-to-reach-kube-apiserver"></a>Hiba: "nem sikerült elérni a Kube-apiserver"

Ez a hiba akkor fordulhat elő, ha az Azure dev Spaces nem tud csatlakozni az AK-fürt API-kiszolgálójához.

Ha az AK-fürt API-kiszolgálójához való hozzáférés le van zárva, vagy ha az AK-fürthöz engedélyezve van az [API-kiszolgáló által engedélyezett IP-címtartományok](../aks/api-server-authorized-ip-ranges.md) , akkor a fürtön a [régión alapuló további tartományokat](configure-networking.md#aks-cluster-network-requirements) is [létre kell hoznia](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) vagy [frissítenie](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) kell.

A kubectl parancsok futtatásával győződjön meg arról, hogy az API-kiszolgáló elérhető. Ha az API-kiszolgáló nem érhető el, forduljon az AK-támogatáshoz, és próbálkozzon újra, ha az API-kiszolgáló működik.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Gyakori problémák a projekt előkészítésekor az Azure dev Spaces szolgáltatásban

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Figyelmeztetés: "a Docker nem hozható létre a nem támogatott nyelv miatt"
Az Azure dev Spaces natív támogatást biztosít a C# és a Node.js számára. Ha egy, az `azds prep` ezen nyelvek valamelyikében írt kóddal rendelkező könyvtárban fut, az Azure dev Spaces automatikusan létrehoz egy megfelelő Docker.

Továbbra is használhatja az Azure dev Spaces szolgáltatást más nyelveken írt kóddal, de először manuálisan kell létrehoznia a Docker, mielőtt `azds up` az első alkalommal futtatná.

Ha az alkalmazás olyan nyelven íródott, amelyet az Azure dev Spaces nem támogat, meg kell adnia egy megfelelő Docker a kódot futtató tároló-rendszerkép létrehozásához. A Docker egy [listát biztosít a Dockerfiles írásához ajánlott eljárásokról](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) és egy olyan [Docker-hivatkozásról](https://docs.docker.com/engine/reference/builder/) , amely segíthet az igényeinek megfelelő Docker írására.

Ha megfelelő Docker rendelkezik, futtathatja az `azds up` alkalmazást az Azure dev Spaces szolgáltatásban.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Gyakori problémák az Azure dev Spaces szolgáltatás indításakor vagy leállításakor

### <a name="error-config-file-not-found"></a>Hiba "a konfigurációs fájl nem található:"

Ha fut `azds up` , akkor ez a hiba jelenik meg. Mindkettőt a `azds up` `azds prep` fejlesztői térben futtatni kívánt projekt gyökérkönyvtárában kell futtatni.

A probléma megoldása:
1. Módosítsa az aktuális könyvtárat a szolgáltatási kódot tartalmazó gyökérkönyvtárba. 
1. Ha nem rendelkezik _azds. YAML_ fájllal a Code mappában, futtassa a parancsot a `azds prep` Docker, a Kubernetes és az Azure dev Spaces-eszközök létrehozásához.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Időtúllépés: "Várakozás a tároló rendszerképének összeállítására..." lépés AK virtuális csomópontokkal

Ez az időkorlát akkor fordul elő, ha a fejlesztői szóközök használatával futtat olyan szolgáltatást, amely egy AK-beli [virtuális csomóponton](../aks/virtual-nodes-portal.md)való futtatásra van konfigurálva. A dev Spaces jelenleg nem támogatja a virtuális csomópontokon futó szolgáltatások létrehozását vagy hibakeresését.

Ha `azds up` a `--verbose` kapcsolóval fut, vagy engedélyezi a részletes naplózást a Visual Studióban, további részleteket láthat:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

A fenti parancs azt mutatja, hogy a szolgáltatás Pod hozzá lett rendelve a *virtuális csomópont-ACI-Linux* rendszerhez, amely egy virtuális csomópont.

A probléma megoldásához frissítse a szolgáltatás Helm diagramját, és távolítsa el azokat a *nodeSelector* vagy *toleráló* értékeket, amelyek lehetővé teszik, hogy a szolgáltatás virtuális csomóponton fusson. Ezek az értékek általában a diagram fájljában vannak meghatározva `values.yaml` .

Továbbra is használhat olyan AK-fürtöt, amelyen engedélyezve van a virtuális csomópontok funkciója, ha a fejlesztéshez és a hibakereséshez használni kívánt szolgáltatás egy virtuálisgép-csomóponton fut. A szolgáltatás egy virtuálisgép-csomóponton található fejlesztői szóközökkel való futtatása az alapértelmezett konfiguráció.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Hiba: "nem található a Ready up Pod" a dev Spaces indításakor

Ez a hiba akkor fordul elő, ha a Helm-ügyfél már nem tud kommunikálni a fürtben futó Tiller Pod-val.

A probléma megoldásához indítsa újra a fürtben lévő ügynökök csomópontjait.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Hiba történt a kiadási azds – \<identifier\> - \<spacename\> - \<servicename\> sikertelen: \<servicename\> a (z) "" szolgáltatás már létezik "vagy" lekéréses hozzáférés megtagadva \<servicename\> , a tárház nem létezik, vagy a "Docker bejelentkezhessen" is szükséges.

Ezek a hibák akkor fordulhatnak elő, ha a (például,, vagy) rendszerű Direct Helm-parancsokat (például `helm install` , `helm upgrade` vagy) futtatja a dev Spaces `helm delete` parancsokkal (például `azds up` és `azds down` ). Ezek azért történnek, mert a dev Spaces saját kormányrúd-példánnyal rendelkezik, ami ütközik a saját, azonos fejlesztői térben futó példányával.

A Helm-parancsok és a dev Spaces-parancsok is használhatók ugyanazon az AK-fürtön, de az egyes fejlesztői szóközöket használó névtereknek vagy az egyiket kell használniuk.

Tegyük fel például, hogy egy Helm parancs használatával futtatja a teljes alkalmazást egy szülő fejlesztői térben. A gyermek fejlesztési területeit kikapcsolhatja a szülőtől, a fejlesztői szóközök használatával egyéni szolgáltatásokat futtathat a gyermek-fejlesztési területeken belül, és tesztelheti a szolgáltatásokat. Ha készen áll a módosítások beadására, a Helm parancs használatával telepítse a frissített kódot a szülő fejlesztői területére. A nem használható a `azds up` frissített szolgáltatás futtatására a szülő fejlesztői térben, mert az ütközik a Helm használatával kezdetben futtatott szolgáltatással.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Meglévő Docker nem használatos tároló létrehozásához

Az Azure dev Spaces konfigurálható úgy, hogy a projekt egy adott _Docker_ mutasson. Ha úgy tűnik, hogy az Azure dev Spaces nem a tárolók felépítésére várt _Docker_ használja, akkor előfordulhat, hogy explicit módon meg kell adnia az Azure dev Spaces-Docker, amelyeket használni kíván. 

A probléma megoldásához nyissa meg az _azds. YAML_ fájlt, amelyet az Azure dev Spaces hozott létre a projektben. Frissítési *konfigurációk: fejlesztés: build: Docker* , hogy a használni kívánt Docker mutasson. Például:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>"Jogosulatlan: hitelesítés szükséges" hibaüzenet, ha Docker-rendszerképet szeretne használni egy privát beállításjegyzékből

Olyan Docker-rendszerképet használ, amely hitelesítést igénylő privát beállításjegyzékből áll.

A probléma megoldásához engedélyezheti a fejlesztői terek számára, hogy a [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)használatával hitelesítsék és lehívhatják a képeket ebből a privát beállításjegyzékből. A imagePullSecrets használatához [hozzon létre egy Kubernetes titkot](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) abban a névtérben, amelyben a rendszerképet használja. Ezután adja meg a titkot a imagePullSecret `azds.yaml` .

Alább látható egy példa a imagePullSecrets megadására a alkalmazásban `azds.yaml` .

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> A imagePullSecrets beállítása `azds.yaml` felülbírálja a imagePullSecrets megadott értéket `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Hiba: a szolgáltatás nem indítható el.

Ez a hiba akkor fordulhat elő, ha a szolgáltatási kód nem indul el. Az ok gyakran a felhasználói kódban van. További diagnosztikai adatok megjelenítéséhez engedélyezze a szolgáltatás indításakor részletesebb naplózást.

A parancssorban a használatával engedélyezze a részletesebb `--verbose` naplózást. A használatával kimeneti formátumot is megadhat `--output` . Például:

```cmd
azds up --verbose --output json
```

A Visual Studióban:

1. Nyissa meg az **eszközök > lehetőségeket** , és a **projektek és megoldások** területen válassza a **Létrehozás és Futtatás** lehetőséget.
2. Az MSBuild-projekt beállításainak módosításához adja meg a **kimeneti részletességet** **részletes** vagy **diagnosztikai** értékre.

    ![Képernyőfelvétel az eszközök beállításai párbeszédpanelről](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Szolgáltatás újrafuttatása a vezérlő ismételt létrehozása után

A szolgáltatás *nem indítható el* , ha a szolgáltatás újrafuttatására tett kísérletet követően eltávolította a szolgáltatást, majd újból létrehozta az ehhez a fürthöz társított Azure dev Spaces-vezérlőt. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ez a hiba azért fordul elő, mert a dev Spaces vezérlő eltávolítása nem távolítja el a vezérlő által korábban telepített szolgáltatásokat. A vezérlő újbóli létrehozása, majd a szolgáltatások futtatásának megkísérlése az új vezérlővel meghiúsul, mert a régi szolgáltatások továbbra is érvényben vannak.

A probléma megoldásához a `kubectl delete` paranccsal távolítsa el manuálisan a régi szolgáltatásokat a fürtből, majd futtassa újra a dev Spaces szolgáltatást az új szolgáltatások telepítéséhez.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Hiba: a szolgáltatás nem indítható el. többfázisú Dockerfiles használata esetén

Egy *szolgáltatás nem indítható el* , ha többfázisú Docker használ. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Ez a hiba azért fordul elő, mert az Azure dev Spaces jelenleg nem támogatja a többfázisú buildeket. A többfázisú buildek elkerüléséhez írja át a Docker.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>A hálózati forgalmat nem továbbítja a rendszer az AK-fürthöz a fejlesztői gép csatlakoztatásakor

Ha [Az Azure dev Spaces használatával csatlakozik az AK-fürthöz a fejlesztői géphez](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes), előfordulhat, hogy olyan problémába ütközik, amelyben a hálózati forgalom nem továbbítódik a fejlesztési gép és az AK-fürt között.

Ha a fejlesztési gépet az AK-fürthöz csatlakoztatja, az Azure dev Spaces a fejlesztési gép fájljának módosításával továbbítja a hálózati forgalmat az AK-fürt és a fejlesztői számítógép között `hosts` . Az Azure dev Spaces egy bejegyzést hoz létre a (z) és a (z `hosts` ) Kubernetes-szolgáltatás neveként, amelyet állomásnévként cserél. Ez a bejegyzés a port továbbításával használható a fejlesztői gép és az AK-fürt közötti közvetlen hálózati forgalomhoz. Ha a fejlesztési gépen lévő szolgáltatás ütközik a cserélni kívánt Kubernetes szolgáltatás portjával, az Azure dev Spaces nem tudja továbbítani a Kubernetes szolgáltatás hálózati forgalmát. Például a *Windows BranchCache* szolgáltatás általában *0.0.0.0:80*-ra van kötve, amely ütközést okoz a 80-es port összes helyi IP-címeinél.

A probléma megoldásához le kell állítania minden olyan szolgáltatást vagy folyamatot, amely ütközik a cserélni kívánt Kubernetes szolgáltatás portjával. Az eszközök, például a *netstat* segítségével megvizsgálhatja, hogy a fejlesztői gépen milyen szolgáltatások és folyamatok ütköznek.

Például a *Windows BranchCache* szolgáltatás leállításához és letiltásához:
* Futtassa `services.msc` a parancsot a parancssorból.
* Kattintson a jobb gombbal a *BranchCache* elemre, és válassza a *Tulajdonságok* lehetőséget.
* Kattintson a *Leállítás* gombra.
* Igény szerint letilthatja azt az *indítási típus* *letiltásának beállításával.*
* Kattintson az *OK* gombra.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Hiba: "nem található AzureAssignedIdentity a következőhöz: Pod: azds/azds-webhook-Deployment- \<id\> in Assigned State"

Ha egy, a [felügyelt identitással](../aks/use-managed-identity.md) és a [Pod által felügyelt identitásokkal](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) rendelkező AK-fürtön futó Azure fejlesztői tárhelyekkel rendelkező szolgáltatást futtat, a folyamat nem válaszol a *diagram telepítése* lépés után. Ha megvizsgálja a *azds-injektort – webhookot* a *azds* -névtérben, akkor ezt a hibaüzenetet láthatja.

Az Azure dev Spaces szolgáltatásban futó szolgáltatások a fürt felügyelt identitásával kommunikálnak az Azure dev Spaces háttér-szolgáltatásaival a fürtön kívül. A pod felügyelt identitás telepítésekor a rendszer a fürt csomópontjain konfigurálja a hálózati szabályokat, hogy átirányítsa a felügyelt identitás hitelesítő adatainak összes hívását egy, [a fürtön telepített, csomópont által felügyelt identitás (NMI) daemonset elemet](https://github.com/Azure/aad-pod-identity#node-managed-identity). Ez a NMI Daemonset elemet azonosítja a hívó Pod-t, és gondoskodik arról, hogy a pod megfelelően legyen megjelölve a kért felügyelt identitás eléréséhez. Az Azure dev Spaces nem tudja észlelni, hogy a fürt rendelkezik-e a pod Managed Identity szolgáltatással, és nem tudja végrehajtani a szükséges konfigurációt, hogy az Azure dev Spaces Services hozzáférhessen a fürt felügyelt identitásához. Mivel az Azure dev Spaces szolgáltatás nem lett konfigurálva a fürt felügyelt identitásának elérésére, a NMI Daemonset elemet nem teszi lehetővé, hogy az Azure AD-tokent beszerezzék a felügyelt identitáshoz, és nem tudnak kommunikálni az Azure dev Spaces háttér-szolgáltatásaival.

A probléma megoldásához alkalmazzon egy [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) a *azds-injektor-webhookhoz* , és frissítse a hüvelyeket az Azure dev Spaces által a felügyelt identitás eléréséhez.

Hozzon létre egy *webhookException. YAML* nevű fájlt, és másolja a következő YAML-definíciót:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

A fenti fájl létrehoz egy *AzurePodIdentityException* objektumot a *azds-injektor-webhookhoz*. Az objektum üzembe helyezéséhez használja a következőt `kubectl` :

```cmd
kubectl apply -f webhookException.yaml
```

Az Azure dev Spaces által a felügyelt identitás eléréséhez használt hüvelyek frissítéséhez frissítse a *névteret* az alábbi YAML-definícióban, és `kubectl` alkalmazza azt az egyes fejlesztői területekre.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Azt is megteheti, hogy *AzureIdentity* és *AzureIdentityBinding* objektumokat hoz létre, és frissíti a pod-címkéket az Azure dev Spaces által felügyelt, az AK-fürt által létrehozott felügyelt identitás eléréséhez használt munkaterhelésekhez.

A felügyelt identitás részleteinek listázásához futtassa a következő parancsot az AK-fürthöz:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

A fenti parancs kimenete a felügyelt identitás *clientId* és *resourceId* . Például:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

*AzureIdentity* -objektum létrehozásához hozzon létre egy *clusteridentity. YAML* nevű fájlt, és használja a következő YAML-definíciót az előző parancs által felügyelt identitásának részleteivel:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

*AzureIdentityBinding* -objektum létrehozásához hozzon létre egy *clusteridentitybinding. YAML* nevű fájlt, és használja a következő YAML-definíciót:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

A *AzureIdentity* és a *AzureIdentityBinding* objektumok üzembe helyezéséhez használja a következőt `kubectl` :

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

A *AzureIdentity* és a *AzureIdentityBinding* objektumok üzembe helyezése után a *aadpodidbinding: My-Label-Value* címkével elérheti a fürt felügyelt identitását. Adja hozzá ezt a címkét, és telepítse újra az összes olyan munkaterhelést, amely bármely fejlesztői térben fut. Például:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

### <a name="error-cannot-get-connection-details-for-azure-dev-spaces-controller-abc-because-it-is-in-the-failed-state-something-wrong-might-have-happened-with-your-controller"></a>Hiba: nem lehet lekérni a kapcsolat adatait az Azure dev Spaces Controller "ABC" vezérlőhöz, mert a "sikertelen" állapotban van. Hiba történt a vezérlővel kapcsolatban. "

A probléma megoldásához próbálja meg törölni az Azure dev Spaces-vezérlőt a fürtből, és telepítse újra:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Továbbá, mivel az Azure dev Spaces szolgáltatás kivonás alatt áll, érdemes lehet [áttelepíteni a Bridge-be a Kubernetes](migrate-to-bridge-to-kubernetes.md) , amely jobb élményt nyújt.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>A Visual Studio és a Visual Studio Code együttes használata az Azure dev Spaces használatával – gyakori problémák

### <a name="error-required-tools-and-configurations-are-missing"></a>Hiba: a szükséges eszközök és konfigurációk hiányoznak.

Ez a hiba akkor fordulhat elő, ha a VS Code-ot elindítja: "[Azure dev Spaces] szükséges eszközök és konfigurációk a (z)" [projekt neve] "létrehozásához és hibakereséséhez."
A hiba azt jelenti, hogy a azds.exe nem szerepel a PATH környezeti változóban, ahogy az a VS Code-ban látható.

Próbálkozzon a VS Code indításával egy parancssorban, ahol a PATH környezeti változó helyesen van beállítva.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Hiba: a "projektnév" létrehozásához és hibakereséséhez szükséges eszközök elavultak. "

Ez a hibaüzenet akkor jelenik meg a Visual Studio Code-ban, ha az Azure dev Spaces-hez a VS Code bővítmény újabb verziója van telepítve, de az Azure dev Spaces CLI egy régebbi verziója.

Próbálja meg letölteni és telepíteni az Azure dev Spaces CLI legújabb verzióját:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Hiba: "a hibakereső bővítmény nem található a következő típushoz: coreclr"

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő. Előfordulhat, hogy a fejlesztői gépen nincs telepítve a C# VS Code-bővítmény. A C# bővítmény a .NET Core (CoreCLR) hibakeresési támogatását tartalmazza.

A probléma megoldásához telepítse a [vs Code-bővítményt a C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)-hoz.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Hiba: a "coreclr" konfigurált hibakeresési típus nem támogatott "

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő. Előfordulhat, hogy a fejlesztői gépen nincs telepítve a VS Code bővítmény az Azure dev Spaces szolgáltatáshoz.

A probléma megoldásához telepítse a VS Code-bővítményt az Azure dev Spaces szolgáltatáshoz.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Hiba "Érvénytelen" CWD "/src" érték. A megadott fájl nem található. " vagy a "Launch: program"/src/[a projekt bináris elérési útja] "nem létezik"

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő. Alapértelmezés szerint a VS Code bővítmény `src` a projekt munkakönyvtáraként használja a tárolón. Ha frissítette a `Dockerfile` -t egy másik munkakönyvtár megadásához, akkor ez a hibaüzenet jelenhet meg.

A probléma megoldásához frissítse a `launch.json` fájlt a `.vscode` Project mappa alkönyvtárában. Módosítsa az `configurations->cwd` irányelvet úgy, hogy ugyanarra a címtárra mutasson, mint a `WORKDIR` projektben definiált `Dockerfile` . Előfordulhat, hogy az irányelvet is frissítenie kell `configurations->program` .

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba: a "azds" pipe program váratlanul kilépett a 126 kóddal. "

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő.

A probléma megoldásához zárjuk be és nyissa meg újra a Visual Studio Code-ot. Indítsa újra a hibakeresőt.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Hiba "a belső figyelés sikertelen: ENOSPC megtekintése" a hibakeresés Node.js alkalmazáshoz való csatolásakor

Ez a hiba akkor fordul elő, ha a pod-t futtató csomópont a hibakeresővel csatlakoztatni kívánt Node.js alkalmazással túllépte a *FS.inotify.max_user_watches* értéket. Bizonyos esetekben előfordulhat, [hogy az *FS.inotify.max_user_watches* alapértelmezett értéke túl kicsi ahhoz, hogy a hibakeresőt közvetlenül a pod-hez csatolja](https://github.com/Azure/AKS/issues/772).

A probléma ideiglenes megkerülő megoldásként növelje a *FS.inotify.max_user_watches* értékét a fürt mindegyik csomópontján, majd indítsa újra a csomópontot a módosítások érvénybe léptetéséhez.

## <a name="other-common-issues"></a>Egyéb gyakori problémák

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>A "azds" hiba nem ismerhető fel belső vagy külső parancsként, futtatható programként vagy batch-fájlként.

Ez a hiba akkor fordulhat elő, ha nincs `azds.exe` telepítve vagy megfelelően konfigurálva.

A probléma megoldása:

1. Keresse meg a (z)% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI-t a következő helyen: `azds.exe` . Ha ott van, adja hozzá ezt a helyet a PATH környezeti változóhoz.
2. Ha `azds.exe` nincs telepítve, futtassa a következő parancsot:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>"Microsoft. DevSpaces/regisztráció/művelet" engedélyezési hiba

Az Azure fejlesztői tárhelyek kezeléséhez *tulajdonosi* vagy *közreműködői* hozzáférésre van szüksége az Azure-előfizetésben. Ha a fejlesztői helyeket szeretné kezelni, és nem rendelkezik *tulajdonosi* vagy *közreműködői* hozzáféréssel a társított Azure-előfizetéshez, akkor egy engedélyezési hiba jelenhet meg. Például:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

A probléma megoldásához az Azure-előfizetéshez *tulajdonosi* vagy *közreműködői* hozzáféréssel rendelkező fiók használatával manuálisan regisztrálja a `Microsoft.DevSpaces` névteret:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Az új hüvelyek nem indulnak el

A Kubernetes inicializáló nem tudja alkalmazni a PodSpec az új hüvelyek esetében, mert a fürtben a fürt *-rendszergazdai* SZEREPKÖR Kubernetes RBAC engedélyei változnak. Az új Pod is rendelkezhet érvénytelen PodSpec, például a pod-hoz társított szolgáltatásfiók már nem létezik. Az inicializálási probléma miatt *függőben* lévő állapotban lévő hüvelyek megtekintéséhez használja az `kubectl get pods` parancsot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ez a probléma a fürt *összes névterében* hatással lehet a hüvelyekre, beleértve azokat a névtereket is, amelyekben nincs engedélyezve az Azure dev Spaces.

A probléma megoldásához [frissítse a dev Spaces CLI-t a legújabb verzióra](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , majd törölje a *Azds InitializerConfiguration* az Azure dev Spaces vezérlőből:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Miután eltávolította a azds- *InitializerConfiguration* az Azure dev Spaces-vezérlőből, a használatával `kubectl delete` távolítsa el a *függő* állapotú összes hüvelyt. Miután az összes függőben lévő hüvely el lett távolítva, telepítse újra a hüvelyeket.

Ha az új hüvelyek továbbra is *függőben* vannak egy újratelepítést követően, a használatával `kubectl delete` eltávolíthatja a *függőben* lévő állapotú hüvelyeket. Miután az összes függőben lévő hüvely el lett távolítva, törölje a vezérlőt a fürtből, és telepítse újra:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

A vezérlő újratelepítése után telepítse újra a hüvelyeket.

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nem megfelelő Azure RBAC-engedélyek a dev Spaces-vezérlő és API-k hívásához

Az Azure dev Spaces-vezérlőhöz hozzáférő felhasználónak hozzáféréssel kell rendelkeznie az AK-fürt rendszergazdai *kubeconfig* olvasásához. Ez az engedély például a [beépített Azure Kubernetes szolgáltatás-fürt rendszergazdai szerepkörében](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)érhető el. Az Azure dev Spaces-vezérlőhöz hozzáférő felhasználónak a vezérlő *közreműködői* vagy *tulajdonosi* Azure-szerepkörével is rendelkeznie kell. A felhasználó egy AK-fürtre vonatkozó engedélyeinek frissítéséről [itt talál](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)további információt.

A felhasználó Azure-szerepkörének frissítése a vezérlőhöz:

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Navigáljon a vezérlőt tartalmazó erőforráscsoporthoz, amely általában ugyanaz, mint az AK-fürt.
1. Engedélyezze a *rejtett típusok megjelenítése* jelölőnégyzetet.
1. Kattintson a vezérlőre.
1. Nyissa meg a *Access Control (iam)* ablaktáblát.
1. Kattintson a *szerepkör-hozzárendelések* fülre.
1. Kattintson a *Hozzáadás* , majd a *szerepkör-hozzárendelés hozzáadása* lehetőségre.
    * A *szerepkör* területen válassza a *közreműködő* vagy a *tulajdonos* lehetőséget.
    * A *hozzáférésének hozzárendeléséhez* válassza az *Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév* lehetőséget.
    * A *Select (kiválasztás*) lehetőségnél keresse meg azt a felhasználót, akinek engedélyeket szeretne adni.
1. Kattintson a *Mentés* gombra.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>A DNS-névfeloldás sikertelen a dev Spaces szolgáltatáshoz társított nyilvános URL-cím esetén

A szolgáltatáshoz egy nyilvános URL-végpontot is beállíthat, ha megadja a `--enable-ingress` kapcsolót a `azds prep` parancsra, vagy kiválasztja a `Publicly Accessible` jelölőnégyzetet a Visual Studióban. A nyilvános DNS-nevet a rendszer automatikusan regisztrálja, amikor a szolgáltatást a fejlesztői tárhelyeken futtatja. Ha ez a DNS-név nincs regisztrálva, akkor egy *lap nem jeleníthető meg* , vagy a webböngészőben *nem érhető el* hibaüzenet, ha a nyilvános URL-címhez csatlakozik.

A probléma megoldása:

* Győződjön meg arról, hogy a dev Spaces szolgáltatáshoz társított összes URL-cím állapota:

  ```console
  azds list-uris
  ```

* Ha egy URL-cím *függő* állapotban van, a dev Spaces továbbra is vár a DNS-regisztráció befejeződésére. Időnként előfordulhat, hogy a regisztráció elvégzése néhány percet vesz igénybe. A fejlesztői helyek is megnyitnak egy localhost alagutat az egyes szolgáltatásokhoz, amelyeket a DNS-regisztrációra való várakozás közben használhat.
* Ha egy URL-cím 5 percnél hosszabb ideig marad a *függőben* lévő állapotban, akkor a külső DNS-Pod-mel kapcsolatos problémát jelezhet, amely létrehozza a nyilvános végpontot vagy az Nginx bemenő vezérlő Pod-t, amely a nyilvános végpontot szerzi be. A következő parancsokkal törölheti ezeket a hüvelyeket, és engedélyezheti, hogy az AK automatikusan újra létrehozza őket:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba: "felsőbb rétegbeli kapcsolódási hiba vagy leválasztás/visszaállítás a fejlécek előtt"

Ez a hiba akkor jelenhet meg, amikor megpróbál hozzáférni a szolgáltatáshoz. Ha például egy böngészőben a szolgáltatás URL-címét nyitja meg. Ez a hiba azt jelenti, hogy a tároló portja nem érhető el. Ez a következő okok miatt lehetséges:

* A tároló még mindig a kiépítés és üzembe helyezés folyamata alatt áll. Ez a probléma akkor merülhet fel, ha futtatja `azds up` vagy elindítja a hibakeresőt, majd megpróbál hozzáférni a tárolóhoz a sikeres üzembe helyezése előtt.
* A port konfigurációja nem konzisztens a _Docker_, a Helm diagramon és bármely olyan kiszolgáló kódján, amely egy portot nyit meg.

A probléma megoldása:

1. Ha a tároló a beépített/üzembe helyezett folyamatban van, várjon 2-3 másodpercet, és próbálja meg újra elérni a szolgáltatást. 
1. A port konfigurációjának ellenőrzését a következő eszközökön találja:
    * **[Helm-diagram](https://docs.helm.sh):** A és a `service.port` `deployment.containerPort` értékekben megadva. a YAML a parancs szerint van beállítva `azds prep` .
    * Az alkalmazás kódjában megnyitott portok, például Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Nem található a következő típus vagy névtér neve: "MyLibrary".

Az Ön által használt függvénytár-projekt nem található. A fejlesztői szóközökkel a Build környezet alapértelmezés szerint a projekt/szolgáltatás szintjén van.  

A probléma megoldása:

1. Módosítsa a `azds.yaml` fájlt úgy, hogy a felépítési kontextust állítsa a megoldás szintjére.
2. Módosítsa a `Dockerfile` és a `Dockerfile.develop` fájlokat úgy, hogy azok a projektfájlok, például az `.csproj` új Build-környezethez képest helyesen legyenek hivatkozva.
3. Vegyen fel egy `.dockerignore` fájlt a fájllal megegyező könyvtárba `.sln` .
4. `.dockerignore`Szükség szerint frissítse a további bejegyzéseket.

[Itt](https://github.com/sgreenmsft/buildcontextsample)talál egy példát.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>A horizontális Pod automatikus skálázás nem működik a fejlesztői térben

Ha egy fejlesztési térben futtat egy szolgáltatást, a szolgáltatás Pod-je [további tárolókkal van befecskendezve](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) , és a pod összes tárolójának erőforrás-korláttal és kérelmekkel kell rendelkeznie a vízszintes Pod automatikus skálázáshoz.

A probléma megoldásához alkalmazzon egy erőforrás-kérelmet, és korlátozza a befecskendezett dev Spaces-tárolókat. A befecskendezett tároló (devspaces) esetében az erőforrás-kérelmek és a korlátozások alkalmazhatók, ha hozzáadja a `azds.io/proxy-resources` jegyzetet a pod spechoz. Az értéket egy olyan JSON-objektumra kell beállítani, amely a proxyhoz tartozó tároló specifikációjának erőforrások szakaszát jelképezi.

Alább látható egy példa arra a proxy-erőforrások jegyzetre, amelyet alkalmazni kell a pod spec-ra.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Az Azure dev Spaces használatának engedélyezése meglévő névterek futtatásával

Lehet, hogy van egy meglévő AK-fürt és-névtér, amelyen fut a hüvely, ahol engedélyezni szeretné az Azure dev Spaces szolgáltatást.

Ha engedélyezni szeretné az Azure dev Spaces használatát egy meglévő névtérben egy AK-fürtben, futtassa a parancsot, `use-dev-spaces` és használja `kubectl` az összes hüvely újraindításához a névtérben.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

A hüvely újraindítása után megkezdheti a meglévő névtér használatát az Azure dev Spaces használatával.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Az Azure dev-szóközök engedélyezése az AK-fürtökön a fürt csomópontjainak korlátozott kimenő forgalmával

Ha engedélyezni szeretné az Azure dev Spaces szolgáltatást egy AK-fürtön, amelynél a fürtcsomópontok kimenő forgalmának korlátozása korlátozott, akkor engedélyeznie kell a következő teljes tartományneveket:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | A Linux Alpine és más Azure dev Spaces-lemezképek lekérése |
| gcr.io | HTTP: 443 | A Helm/Tiller-lemezképek lekérése|
| storage.googleapis.com | HTTP: 443 | A Helm/Tiller-lemezképek lekérése|

Frissítse a tűzfalat vagy a biztonsági konfigurációt, hogy engedélyezze a fenti teljes tartománynevek és az [Azure dev Spaces infrastrukturális szolgáltatások](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)hálózati forgalmát.

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Hiba: "a fürt nem található az \<cluster\> előfizetésben \<subscriptionId\> "

Ez a hiba akkor fordulhat elő, ha a kubeconfig-fájl egy másik fürtöt vagy előfizetést céloz meg, mint amennyit az Azure dev Spaces ügyféloldali eszközeivel szeretne használni. Az Azure dev Spaces ügyféloldali eszközkészlete replikálja a *kubectl* viselkedését, amely [egy vagy több kubeconfig-fájlt](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) használ a fürt kiválasztásához és az azokkal való kommunikációhoz.

A probléma megoldása:

* `az aks use-dev-spaces -g <resource group name> -n <cluster name>`Az aktuális környezet frissítésére használható. Ez a parancs azt is lehetővé teszi, hogy az Azure dev-helyek az AK-fürtön legyenek, ha még nincs engedélyezve. Azt is megteheti, `kubectl config use-context <cluster name>` hogy a használatával frissíti az aktuális környezetet.
* `az account show`A használatával megjelenítheti az aktuálisan megcélzott Azure-előfizetést, és ellenőrizheti, hogy helyes-e. Megváltoztathatja a célzáshoz használt előfizetést `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Hiba történt a fejlesztői szóközök használatával az AK-tanúsítványok elforgatása után

Miután [elforgatta a tanúsítványokat az AK-fürtben](../aks/certificate-rotation.md), bizonyos műveletek, például `azds space list` és `azds up` sikertelenek lesznek. Emellett frissítenie kell a tanúsítványokat az Azure dev Spaces-vezérlőn a fürtön lévő tanúsítványok elforgatása után.

A probléma megoldásához ellenőrizze, hogy a *kubeconfig* rendelkezik-e a frissített tanúsítványokkal, `az aks get-credentials` majd futtassa a `azds controller refresh-credentials` parancsot. Például:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
