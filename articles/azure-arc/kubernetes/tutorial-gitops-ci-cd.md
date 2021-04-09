---
title: 'Oktatóanyag: CI/CD implementálása az Azure arc-kompatibilis Kubernetes-fürtöket használó GitOps'
description: Ez az oktatóanyag bemutatja, hogyan állíthat be egy CI/CD-megoldást az Azure arc-kompatibilis Kubernetes-fürtökkel rendelkező GitOps használatával. A munkafolyamat elméleti feladatairól a CI/CD-munkafolyamatot a GitOps-Azure arc-kompatibilis Kubernetes című cikkben találja.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: f720cc196f4034d29ec1d628e28d3534b10f3e41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025815"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Oktatóanyag: CI/CD implementálása az Azure arc-kompatibilis Kubernetes-fürtöket használó GitOps


Ebben az oktatóanyagban egy CI/CD-megoldást állít be az Azure arc-kompatibilis Kubernetes-fürtökkel rendelkező GitOps használatával. A minta Azure vote alkalmazás használatával:

> [!div class="checklist"]
> * Hozzon létre egy Azure arc-kompatibilis Kubernetes-fürtöt.
> * Az alkalmazás és a GitOps-repók összekapcsolhatók az Azure Repos-vel.
> * CI/CD-folyamatok importálása.
> * A Azure Container Registry (ACR) összekötése az Azure DevOps és a Kubernetes.
> * Hozzon létre környezeti változó csoportokat.
> * Telepítse a `dev` és a `stage` környezeteket.
> * Tesztelje az alkalmazás környezeteit.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag feltételezi az Azure DevOps, az Azure Repos és a folyamatok, valamint az Azure CLI megismerését.

* Jelentkezzen be az [Azure DevOps servicesbe](https://dev.azure.com/).
* Fejezze be az [előző oktatóanyagot](./tutorial-use-gitops-connected-cluster.md) , amelyből megtudhatja, hogyan telepítheti a GitOps-t a CI/CD-környezetbe.
* Ismerje meg a szolgáltatás [előnyeit és architektúráját](./conceptual-configurations.md) .
* Ellenőrizze, hogy rendelkezik-e:
  * Egy [csatlakoztatott Azure arc-kompatibilis Kubernetes-fürt](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) **arc-vel-cluster** néven.
  * Csatlakoztatott Azure Container Registry (ACR) [AK-integrációs](../../aks/cluster-container-registry-integration.md) vagy [nem ak-alapú fürt-hitelesítéssel](../../container-registry/container-registry-auth-kubernetes.md).
  * Az [Azure Repos](/azure/devops/repos/get-started/what-is-repos) és az [Azure-folyamatok](/azure/devops/pipelines/get-started/pipelines-get-started)"admin" és "projekt rendszergazdája" engedélyei.
* Telepítse a következő Azure arc-kompatibilis Kubernetes CLI-bővítményeit >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * A bővítmények legújabb verzióra való frissítéséhez futtassa a következő parancsokat:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Alkalmazás-és GitOps-repók importálása az Azure Reposba

Egy [alkalmazás](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#application-repo) -tárház és egy [GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#gitops-repo) -tárház importálása az Azure reposba. Ebben az oktatóanyagban a következő példa-repókat használja:

* **arc-vel-demo-src** Application adattár
   * URL https://github.com/Azure/arc-cicd-demo-src
   * Az Azure vote alkalmazást tartalmazza, amelyet a GitOps használatával fog telepíteni.
* **arc-vel-bemutató-gitops** GitOps-tárház
   * URL https://github.com/Azure/arc-cicd-demo-gitops
   * Az Azure vote alkalmazást kiadó fürterőforrás alapjaként működik.

További információ a [git-repók importálásáról](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Az alkalmazás-és GitOps-repók két különálló tárházának importálása és használata javíthatja a biztonságot és az egyszerűséget. Az alkalmazás-és GitOps adattárházak engedélyei és láthatósága egyenként is beállítható.
> Előfordulhat például, hogy a fürt rendszergazdája nem találja a fürt kívánt állapotára vonatkozó, az alkalmazás kódjában történt módosításokat. Ezzel szemben az alkalmazások fejlesztőinek nem kell ismerniük az egyes környezetekhez tartozó paramétereket – a paraméterek lefedettségét biztosító tesztelési értékek készlete elegendő lehet.

## <a name="connect-the-gitops-repo"></a>A GitOps-tárház összekötése

Az alkalmazás folyamatos üzembe helyezéséhez az GitOps használatával kapcsolja össze az alkalmazások tárházát a fürthöz. Az **arc-vel-demo-gitops** gitops-tárház tartalmazza azokat az alapszintű erőforrásokat, amelyekkel az alkalmazás üzembe helyezése az **arc-vel-fürtön** megkezdhető.

A kezdeti GitOps-tárház csak egy olyan [jegyzékfájlt](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) tartalmaz, amely létrehozza a telepítési környezeteknek megfelelő **fejlesztési** és **fázis** névtereket.

A létrehozott GitOps-kapcsolatok automatikusan elvégzik a következőket:
* Szinkronizálja a jegyzékfájlokat a jegyzékfájl címtárában.
* Frissítse a fürt állapotát.

A CI/CD munkafolyamat további jegyzékfájlokkal tölti fel a jegyzékfájlt az alkalmazás üzembe helyezéséhez.


1. [Hozzon létre egy új GitOps-kapcsolódást](./tutorial-use-gitops-connected-cluster.md) az újonnan importált **arc-vel-demo-GitOps** adattárhoz az Azure reposban.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Győződjön meg arról, hogy a Flux *csak* a `arc-cicd-cluster/manifests` könyvtárat használja alapútvonalként. Adja meg az elérési utat a következő operátori paraméter használatával:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Ha HTTPS-kapcsolati karakterláncot használ, és kapcsolódási problémák léptek fel, győződjön meg róla, hogy kihagyja a Felhasználónév előtagját az URL-címben. El kell távolítani például a következőt: `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` `alice@` . Az a `--https-user` felhasználó helyett például a következőt adja meg: `--https-user alice` .

1. Győződjön meg arról, hogy az üzemelő példány állapota Azure Portalban van.
   * Ha a művelet sikeres, akkor `dev` `stage` a fürtben létrehozott és névtereket is látni fogja.

## <a name="import-the-cicd-pipelines"></a>A CI/CD-folyamatok importálása

Most, hogy szinkronizálta a GitOps-kapcsolatokat, importálnia kell a jegyzékeket létrehozó CI/CD-folyamatokat.

Az alkalmazás-tárház tartalmaz egy `.pipeline` mappát, amely a PRs, CI és CD-hez használni kívánt folyamatokat tartalmazza. A példában szereplő három folyamat importálása és átnevezése:

| Folyamat fájljának neve | Leírás |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Az alkalmazás PR-folyamata, named **arc-vel-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Az Application CI-folyamat, named **arc-vel-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Az Application CD-folyamat, named **arc-vel-demo-src CD** |



## <a name="connect-your-acr"></a>Az ACR összekötése
A folyamatok és a fürt is az ACR-t használja a Docker-rendszerképek tárolásához és lekéréséhez.

### <a name="connect-acr-to-azure-devops"></a>Az ACR összekötése az Azure DevOps
A CI folyamat során üzembe helyezi az alkalmazás tárolóit egy beállításjegyzékben. Első lépésként hozzon létre egy Azure-szolgáltatási kapcsolatokat:

1. Az Azure DevOps nyissa meg a **szolgáltatás kapcsolatai** lapot a projekt beállításai lapon. A TFS-ben nyissa meg a **szolgáltatások** lapot a felső menüsor **Beállítások** ikonján.
2. Válassza az **+ új szolgáltatás-kapcsolatok** lehetőséget, és válassza ki a szükséges szolgáltatási kapcsolatok típusát.
3. Adja meg a szolgáltatási kapcsolatok paramétereit. Ebben az oktatóanyagban:
   * Nevezze el a Service **-összekötő ív-demo-ACR** nevet. 
   * Válassza az **myResourceGroup** elemet az erőforráscsoport elemnél.
4. Válassza a **hozzáférési engedély megadása minden folyamathoz** lehetőséget. 
   * Ez a beállítás engedélyezi a YAML-feldolgozási folyamat fájljait a szolgáltatás kapcsolatai számára. 
5. A kapcsolódás létrehozásához kattintson **az OK gombra** .

### <a name="connect-acr-to-kubernetes"></a>Az ACR összekötése a Kubernetes
Lehetővé teszi, hogy a Kubernetes-fürt képeket lehessen lekérni az ACR-ből. Ha privát, a hitelesítésre lesz szükség.

#### <a name="connect-acr-to-existing-aks-clusters"></a>ACR összekötése meglévő AK-fürtökkel

A következő parancs használatával integrálhatja a meglévő ACR-fürtöket a már létező és a következő paranccsal:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Rendszerkép-lekérési titok létrehozása

Ha a nem AK-t és a helyi fürtöket az ACR-hez szeretné kapcsolni, hozzon létre egy rendszerképet. A Kubernetes a rendszerkép-lekérési titkokat használja a beállításjegyzék hitelesítéséhez szükséges információk tárolására.

Hozzon létre egy képet a következő paranccsal: pull Secret `kubectl` . Ismételje meg a `dev` és a `stage` névteret is.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> Ha nem szeretné, hogy az összes Pod imagePullSecret kelljen beállítania, vegye fel a imagePullSecret a és a névterek szolgáltatásbeli fiókjába `dev` `stage` . További információt a [Kubernetes oktatóanyagban](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) talál.

## <a name="create-environment-variable-groups"></a>Környezeti változó csoportok létrehozása

### <a name="app-repo-variable-group"></a>Alkalmazás-tárház változó csoport
[Hozzon létre egy](/azure/devops/pipelines/library/variable-groups) , **az-vote-app-dev** nevű változó csoportot. Állítsa be a következő értékeket:

| Változó | Érték |
| -------- | ----- |
| AZ_ACR_NAME | (az ACR-példány, például:. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (az Azure-szolgáltatási kapcsolatban, amely az oktatóanyag korábbi részében **arc-demo-ACR** lehet.) |
| AZURE_VOTE_IMAGE_REPO | Az Azure vote app repo teljes elérési útja, például azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | A GitOps-tárház git-kapcsolatok karakterlánca |
| PAT | Egy [létrehozott Pat-jogkivonat](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) olvasási/írási forrás engedéllyel. Mentse el, hogy később használhassa a `stage` változót a csoport létrehozásakor. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Adja meg a PAT titkos típusként való megjelölését. Az alkalmazásaiban érdemes lehet az [Azure](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)kulcstartóból összekapcsolni a titkokat.
>
### <a name="stage-environment-variable-group"></a>Fázis környezeti változójának csoportja

1. Az az **-vote-app-dev** változó csoport klónozása.
1. Módosítsa a nevet az **az-vote-app-Stage** névre.
1. Győződjön meg arról, hogy a következő értékek szerepelnek a megfelelő változóknál:

| Változó | Érték |
| -------- | ----- |
| ENVIRONMENT_NAME | Fázis |
| TARGET_NAMESPACE | `stage` |

Most már készen áll a telepítésre a `dev` és a `stage` környezetekben.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>A fejlesztői környezet első üzembe helyezése
A CI-és CD-folyamatok létrehozása után futtassa a CI-folyamatot az alkalmazás első telepítéséhez.

### <a name="ci-pipeline"></a>CI-folyamat

A CI-folyamat kezdeti futtatása során erőforrás-hitelesítési hiba jelenik meg a szolgáltatás kapcsolati nevének olvasásakor.
1. Ellenőrizze, hogy az elért változó AZURE_SUBSCRIPTION-e.
1. Engedélyezze a használatot.
1. Futtassa újra a folyamatot.

A CI-folyamat:
* Gondoskodik arról, hogy az alkalmazás változása minden automatizált minőségi ellenőrzésen áthaladjon az üzembe helyezéshez.
* Minden olyan további érvényesítést végrehajt, amely nem hajtható végre a PR-folyamatban.
    * A GitOps-re jellemző a folyamat közzéteszi a végrehajtáshoz szükséges összetevőket is, amelyeket a CD-folyamat telepít majd.
* Ellenőrzi, hogy a Docker-rendszerkép módosult-e, és hogy az új rendszerkép leküldése megtörtént-e.

### <a name="cd-pipeline"></a>CD-folyamat
A sikeres CI-folyamat futtatása elindítja a CD-folyamatot a telepítési folyamat befejezéséhez. Az egyes környezetekben fokozatosan fog települni.

> [!TIP]
> Ha a CD-folyamat nem aktiválódik automatikusan:
> 1. Ellenőrizze, hogy a név megegyezik-e az ág triggerével a [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * A következőnek kellene itt szerepelnie: `arc-cicd-demo-src CI`.
> 1. Futtassa újra a CI-folyamatot.

Miután létrejött a sablon és a jegyzékfájl változása a GitOps-tárházban, a CD-folyamat létrehozza a végrehajtást, leküldi, majd létrehoz egy lekéréses kérelmet a jóváhagyáshoz.
1. Nyissa meg a feladat kimenetében megadott PR-hivatkozást `Create PR` .
1. Ellenőrizze a GitOps-adattár módosításait. A következőnek kell megjelennie:
   * A magas szintű Helm-sablon változásai.
   * Alacsony szintű Kubernetes-jegyzékfájlok, amelyek az alapul szolgáló módosításokat mutatják a kívánt állapotban. A Flux üzembe helyezi ezeket a jegyzékeket.
1. Ha minden jól látható, hagyja jóvá és fejezze be a PR-t.

1. Néhány perc elteltével a Flux felveszi a változást, és elindítja az üzemelő példányt.
1. Továbbítsa a portot helyileg a használatával `kubectl` , és győződjön meg arról, hogy az alkalmazás megfelelően működik a használatával:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Tekintse meg az Azure vote alkalmazást a böngészőjében `http://localhost:8080/` .

1. Szavazzon kedvencei számára, és készüljön fel az alkalmazás módosítására.

## <a name="set-up-environment-approvals"></a>Környezeti jóváhagyások beállítása
Az alkalmazások üzembe helyezése után nem csak a kód vagy a sablonok módosítását végezheti el, de a fürtöt véletlenül is rossz állapotba helyezheti.

Ha a fejlesztői környezet az üzembe helyezést követően feltörést mutat, tartsa meg a későbbi környezeteket a környezet-jóváhagyások használatával.

1. Az Azure DevOps-projektben nyissa meg a védeni kívánt környezetet.
1. Navigáljon a **jóváhagyások elemre, és ellenőrizze** az erőforrást.
1. Válassza a **Létrehozás** lehetőséget.
1. Adja meg a jóváhagyókat és egy opcionális üzenetet.
1. Válassza a **Létrehozás** újra lehetőséget a manuális jóváhagyási vizsgálat hozzáadásának befejezéséhez.

További részletekért tekintse [meg a jóváhagyás és ellenőrzés meghatározása](/azure/devops/pipelines/process/approvals) című oktatóanyagot.

A CD-folyamat következő futtatásakor a folyamat a GitOps PR létrehozása után szünetel. Ellenőrizze, hogy a módosítás megfelelően van-e szinkronizálva, és átadja-e az alapszintű funkciókat. Hagyja jóvá a folyamat ellenőrzését, hogy a változás a következő környezetbe lépjen.

## <a name="make-an-application-change"></a>Alkalmazás módosításának elkészítése

A fürt állapotát jelképező sablonok és jegyzékfájlok ezen alapkészlete esetében az alkalmazás egy kis módosítást hajt végre.

1. Az **arc-vel-demo-src** adattárban szerkessze a [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) fájlt.

2. Mivel a "macskák vs Dogs" nem kap elég szavazatot, módosítsa a "tabs vs Spaces" értékre a szavazatok számának elindításához.

3. Véglegesítse egy új ág változását, küldje el, és hozzon létre egy lekéréses kérelmet.
   * Ez a tipikus fejlesztői folyamat, amely elindítja a CI/CD életciklusát.

## <a name="pr-validation-pipeline"></a>PR-ellenőrzési folyamat

A PR-folyamat az első védelmi vonal a hibás változásokkal szemben. Az alkalmazás kódjának szokásos minőségi ellenőrzései közé tartozik a kihelyezés és a statikus elemzés. A GitOps perspektívában ugyanezt a minőséget kell biztosítania az eredményül kapott infrastruktúra üzembe helyezéséhez is.

Az alkalmazás Docker és Helm-diagramjai hasonló módon használhatók az alkalmazáshoz.

Hibák találhatók a feltöltési tartomány során:
* Helytelenül formázott YAML-fájlok
* Ajánlott eljárási javaslatok, például a processzor-és memória-korlátok beállítása az alkalmazáshoz.

> [!NOTE]
> Ahhoz, hogy egy valós alkalmazásban a legjobb lefedettséggel lehessen eljutni, a valós környezetben használt értékeknek megfelelően kell helyettesítenie.

A folyamat végrehajtása során észlelt hibák a Futtatás tesztelési eredmények szakaszában jelennek meg. Innen a következő lehetőségeket éri el:
* A hasznos statisztikák nyomon követése a hibák típusainál.
* Keresse meg az első olyan végrehajtást, amelyet észlelt.
* A verem nyomkövetési stílusa a hibát okozó kódrészletekre hivatkozik.

A folyamat futásának befejeződése után az alkalmazás kódjának és a telepíteni kívánt sablonnak a minősége biztosított. Most már jóváhagyhatja és elvégezheti a lekéréses kérelmet. A CI újra futni fog, majd újragenerálja a sablonokat és a jegyzékeket a CD-folyamat elindítása előtt.

> [!TIP]
> Valós környezetben ne felejtsen el ág-házirendeket beállítani, hogy a PR ellenőrizze a minőségi ellenőrzéseket. További információkért lásd a [fiókirodai szabályzatok beállítása](/azure/devops/repos/git/branch-policies) című cikket.

## <a name="cd-process-approvals"></a>CD-folyamat jóváhagyásai

Sikeres CI-folyamat futtatása elindítja a CD-folyamatot a telepítési folyamat befejezéséhez. A CD-folyamat első indításakor az egyes környezetekben fokozatosan fog települni. Ebben az esetben a folyamat megköveteli az egyes telepítési környezetek jóváhagyását.

1. Hagyja jóvá az üzemelő példányt a `dev` környezetbe.
1. Miután létrejött a sablon és a jegyzékfájl változása a GitOps-tárházban, a CD-folyamat létrehozza a végrehajtást, leküldi, majd létrehoz egy lekéréses kérelmet a jóváhagyáshoz.
1. Nyissa meg a feladatban megadott PR-hivatkozást.
1. Ellenőrizze a GitOps-adattár módosításait. A következőnek kell megjelennie:
   * A magas szintű Helm-sablon változásai.
   * Alacsony szintű Kubernetes-jegyzékfájlok, amelyek az alapul szolgáló módosításokat mutatják a kívánt állapotban.
1. Ha minden jól látható, hagyja jóvá és fejezze be a PR-t.
1. Várjon, amíg az üzembe helyezés befejeződik.
1. Alapszintű füst-tesztként navigáljon az alkalmazás lapra, és ellenőrizze, hogy a szavazó alkalmazás most megjelenik-e a lapok és a szóközök.
   * Továbbítsa a portot helyileg a használatával `kubectl` , és győződjön meg arról, hogy az alkalmazás megfelelően működik a használatával: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Tekintse meg az Azure vote alkalmazást a böngészőjében, http://localhost:8080/ és győződjön meg róla, hogy a szavazatok a lapok és a szóközök között változtak. 
1. Ismételje meg a 1-7 lépést a `stage` környezethez.

A telepítés befejeződött. Ezzel véget ér a CI/CD munkafolyamat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az összes erőforrást a következő lépésekkel:

1. Az Azure arc GitOps konfigurációs kapcsolatainak törlése:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Távolítsa el a `dev` névteret:
   * `kubectl delete namespace dev`

3. Távolítsa el a `stage` névteret:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy teljes körű CI/CD-munkafolyamatot állított be, amely DevOps valósít meg az alkalmazás-fejlesztésből az üzembe helyezésen keresztül. Az alkalmazás módosításai automatikusan elindítják az érvényesítést és az üzembe helyezést a manuális jóváhagyások alapján.

Az Azure arc-kompatibilis Kubernetes GitOps és konfigurációkkal kapcsolatos további információkért tekintse meg a fogalmi cikket.

> [!div class="nextstepaction"]
> [CI/CD-munkafolyamat GitOps használatával – Azure arc-kompatibilis Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd)