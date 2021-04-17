---
title: 'Oktatóanyag: CI/CD megvalósítása a GitOps használatával Azure Arc engedélyezett Kubernetes-fürtök használatával'
description: Ez az oktatóanyag végigvezeti egy CI-/CD-megoldás GitOps és engedélyezett Kubernetes Azure Arc fürtök használatával történő beállításán. A munkafolyamat fogalmi áttekintését a CI/CD-munkafolyamat GitOps használatával – Azure Arc Kubernetes használatával című cikkben olvashatja el.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589142"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Oktatóanyag: CI/CD megvalósítása a GitOps használatával Azure Arc engedélyezett Kubernetes-fürtök használatával


Ebben az oktatóanyagban egy CI/CD-megoldást fog beállítani a GitOps és Azure Arc Kubernetes-fürtök használatával. Az Azure Vote mintaalkalmazással a következőt fogja használni:

> [!div class="checklist"]
> * Hozzon létre Azure Arc Kubernetes-fürtöt.
> * Az alkalmazás és a GitOps-adattárak csatlakoztatása az Azure-adattárakhoz.
> * CI-/CD-folyamatok importálása.
> * Csatlakoztassa a Azure Container Registry (ACR) az Azure DevOpshoz és a Kuberneteshez.
> * Hozzon létre környezeti változócsoportokat.
> * A és a `dev` környezet `stage` üzembe helyezése.
> * Tesztelje az alkalmazáskörnyezeteket.

Ha nincs Azure-™, kezdés előtt hozzon létre [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) egy ingyenes fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag feltételezi az Azure DevOps, az Azure Repos és a Pipelines, valamint az Azure CLI ismereteit.

* Jelentkezzen be az [Azure DevOps Servicesbe.](https://dev.azure.com/)
* Az előző [oktatóanyagból megtudhatja,](./tutorial-use-gitops-connected-cluster.md) hogyan helyezheti üzembe a GitOpsot a CI/CD-környezetben.
* A funkció [előnyeinek és](./conceptual-configurations.md) architektúrája.
* Ellenőrizze, hogy a következővel van-e:
  * Egy csatlakoztatott Azure Arc engedélyezett, **arc-cicd-cluster** nevű [Kubernetes-fürt.](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster)
  * Egy csatlakoztatott Azure Container Registry (ACR) [AKS-integrációval](../../aks/cluster-container-registry-integration.md) vagy nem [AKS-fürthitelesítéssel.](../../container-registry/container-registry-auth-kubernetes.md)
  * "Rendszergazda létrehozása" és "Projekt-rendszergazda" engedélyek az [Azure Repos](/azure/devops/repos/get-started/what-is-repos) és az [Azure Pipelines számára.](/azure/devops/pipelines/get-started/pipelines-get-started)
* Telepítse a következő Azure Arc kubernetes CLI-bővítményeket a >= 1.0.0 verzióban:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Ezeknek a bővítményeknek a legújabb verzióra való frissítéséhez futtassa a következő parancsokat:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Alkalmazás- és GitOps-adattárak importálása azure-adattárakba

Importálhat [egy alkalmazás-adattárat](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) és egy [GitOps-adattárat](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) az Azure-adattárakba. Ebben az oktatóanyagban használja a következő példatárházat:

* **arc-cicd-demo-src** alkalmazás-repo
   * Url: https://github.com/Azure/arc-cicd-demo-src
   * A GitOps használatával üzembe helyező Azure Vote-példaalkalmazást tartalmazza.
* **arc-cicd-demo-gitops** GitOps-adattár
   * Url: https://github.com/Azure/arc-cicd-demo-gitops
   * Az Azure Vote alkalmazást üzembe indító fürterőforrások alapjaként működik.

További információ a [Git-adattárak importálásról.](/azure/devops/repos/git/import-git-repository)

>[!NOTE]
> Az alkalmazás- és GitOps-adattárak két külön adattárának importálása és használata javíthatja a biztonságot és az egyszerűséget. Az alkalmazás és a GitOps-adattárak engedélyei és láthatósága egyenként hangolható.
> Előfordulhat például, hogy a fürt rendszergazdája nem találja a fürt célállapotához kapcsolódó alkalmazáskód-módosításokat. Ezzel szemben az alkalmazásfejlesztőnek nem kell tudni az egyes környezetek konkrét paramétereit – a paramétereket lefedő tesztértékek készlete elegendő lehet.

## <a name="connect-the-gitops-repo"></a>A GitOps-adattár csatlakoztatása

Az alkalmazás folyamatos üzembe helyezéséhez csatlakoztassa az alkalmazás adattárát a fürthöz a GitOps használatával. Az **arc-cicd-demo-gitops** GitOps-adattár tartalmazza az alkalmazás az **arc-cicd-cluster** fürtön való futtatásához szükséges alapvető erőforrásokat.

A kezdeti GitOps-adattár csak egy  jegyzékfájlt tartalmaz, amely létrehozza [az](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) üzembe helyezési környezetnek megfelelő dev és **stage** névtereket.

A létrehozott GitOps-kapcsolat automatikusan a következő lesz:
* Szinkronizálja a jegyzékfájlokat a jegyzékkönyvtárban.
* Frissítse a fürt állapotát.

A CI/CD-munkafolyamat további jegyzékekkel tölti fel a jegyzékkönyvtárat az alkalmazás üzembe helyezéséhez.


1. [Hozzon létre egy új GitOps-kapcsolatot](./tutorial-use-gitops-connected-cluster.md) az újonnan importált **arc-cicd-demo-gitops** adattárhoz az Azure Reposban.

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

1. Győződjön meg arról, hogy a Flux *csak* `arc-cicd-cluster/manifests` a könyvtárat használja alapútvonalként. Az elérési utat a következő operátorparaméterrel határozhatja meg:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Ha HTTPS-kapcsolati sztringet használ, és csatlakozási problémákat tapasztal, ügyeljen arra, hogy ne adja meg a felhasználónév előtagját az URL-címben. A például `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` el lett `alice@` távolítva. A `--https-user` ehelyett a felhasználót adja meg, `--https-user alice` például: .

1. Ellenőrizze az üzembe helyezés állapotát a Azure Portal.
   * Ha a művelet sikeres, a fürtben létrehozott `dev` és `stage` névtereket is látni fogja.

## <a name="import-the-cicd-pipelines"></a>CI-/CD-folyamatok importálása

Most, hogy szinkronizált egy GitOps-kapcsolatot, importálni kell a jegyzékfájlokat tartalmazó CI/CD-folyamatot.

Az alkalmazás-adattában található egy mappa, amely a lereplikátorokhoz, a CI-hez és a CD-hez `.pipeline` használt folyamatokat tartalmazza. Importálja és nevezze át a mintaadattáraban megadott három folyamatot:

| Folyamatfájl neve | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Az alkalmazás pr-folyamatának neve **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Az alkalmazás CI-folyamatának neve **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Az alkalmazás CD-folyamatának neve **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Az ACR csatlakoztatása
A folyamatok és a fürt is az ACR-t fogja használja a Docker-lemezképek tárolására és lekéréséhez.

### <a name="connect-acr-to-azure-devops"></a>Az ACR csatlakoztatása az Azure DevOpshoz
A CI-folyamat során üzembe fogja helyezni az alkalmazástárolókat egy regisztrációs adatbázisban. Először hozzon létre egy Azure-szolgáltatáskapcsolatot:

1. Az Azure DevOpsban nyissa meg a **Szolgáltatáskapcsolatok** lapot a projektbeállítások oldalán. A TFS-ben nyissa meg  a **Szolgáltatások** lapot a felső menüsáv beállítások ikonján.
2. Válassza **az + Új szolgáltatáskapcsolat lehetőséget,** és válassza ki a kívánt szolgáltatáskapcsolat típusát.
3. Adja meg a szolgáltatáskapcsolat paramétereit. Ebben az oktatóanyagban:
   * A szolgáltatáskapcsolatnak nevezze **el az arc-demo-acr nevet.** 
   * Válassza **ki a myResourceGroup** erőforráscsoportot.
4. Válassza a **Hozzáférési engedély megadása minden folyamathoz lehetőséget.** 
   * Ez a beállítás engedélyezi a YAML-folyamatfájlokat a szolgáltatáskapcsolatok számára. 
5. A **kapcsolat létrehozásához** kattintson az OK gombra.

### <a name="connect-acr-to-kubernetes"></a>Az ACR csatlakoztatása a Kuberneteshez
Engedélyezze a Kubernetes-fürtöt, hogy rendszerképeket lekért az ACR-ről. Ha privát, hitelesítésre lesz szükség.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Az ACR csatlakoztatása meglévő AKS-fürtökhöz

Meglévő ACR integrálása meglévő AKS-fürtökbe a következő paranccsal:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Rendszerkép lekért titkos adatának létrehozása

Ha nem AKS-t és helyi fürtöt szeretne csatlakoztatni az ACR-hez, hozzon létre egy rendszerkép lekért titkos adatát. A Kubernetes rendszerkép-lekért titkos kulcsokat használ a regisztrációs adatbázis hitelesítéséhez szükséges információk tárolására.

Hozzon létre egy rendszerkép lekért titkos adatokat az alábbi `kubectl` paranccsal. Ismételje meg ezt a és `dev` `stage` a névtér esetén is.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Ha nem kell minden podhoz imagePullSecret-t beállítania, vegye fontolóra az imagePullSecret hozzáadását a szolgáltatásfiókhoz a és a `dev` `stage` névtérben. További [információért tekintse meg a Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) oktatóanyagát.

## <a name="create-environment-variable-groups"></a>Környezeti változócsoportok létrehozása

### <a name="app-repo-variable-group"></a>Alkalmazás-repo változócsoport
[Hozzon létre egy](/azure/devops/pipelines/library/variable-groups) **az-vote-app-dev nevű változócsoportot.** Állítsa be a következő értékeket:

| Változó | Érték |
| -------- | ----- |
| AZ_ACR_NAME | (például az Ön ACR-példánya. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (az Azure-szolgáltatáskapcsolat, amelynek az oktatóanyag korábbi, **arc-demo-acr-nek** kell lennie) |
| AZURE_VOTE_IMAGE_REPO | Az Azure Vote Alkalmazás-repo teljes elérési útja, például azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | A GitOps-adattárHoz szükséges Git kapcsolati sztring |
| Pat | Létrehozott [PAT-jogkivonat](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) olvasási/írási forrás engedélyekkel. Mentse a változócsoport későbbi `stage` létrehozásához. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> A PAT-et titkos típusúként jelölje meg. Az alkalmazásokban fontolja meg titkos kulcsok csatolását egy [Azure KeyVaultból.](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)
>
### <a name="stage-environment-variable-group"></a>Környezeti változócsoport szakasza

1. Klónozza **az az-vote-app-dev** változócsoportot.
1. Módosítsa a nevet **az az-vote-app-stage névre.**
1. Ellenőrizze a következő értékeket a megfelelő változókhoz:

| Változó | Érték |
| -------- | ----- |
| ENVIRONMENT_NAME | Fázis |
| TARGET_NAMESPACE | `stage` |

Most már készen áll a és a környezetben való `dev` üzembe `stage` helyezésre.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>A fejlesztői környezet üzembe helyezése első alkalommal
A CI- és CD-folyamatok létrehozása után futtassa a CI-folyamatot az alkalmazás első üzembe helyezéséhez.

### <a name="ci-pipeline"></a>CI-folyamat

A CI-folyamat kezdeti futtatása során erőforrás-engedélyezési hiba jelenhet meg a szolgáltatáskapcsolat nevének beolvasása során.
1. Ellenőrizze, hogy az elért változó AZURE_SUBSCRIPTION.
1. Engedélyezze a használatot.
1. Futtassa újra a folyamatot.

A CI-folyamat:
* Gondoskodik arról, hogy az alkalmazás módosítása minden automatizált minőség-ellenőrzést átessen az üzembe helyezéshez.
* Minden további ellenőrzést, amely nem fejeződött be a le pr-folyamatban.
    * A GitOpsra jellemző folyamat a CD-folyamat által üzembe helyezett véglegesítéshez is közzéteszi az összetevőket.
* Ellenőrzi, hogy a Docker-rendszerkép megváltozott, és lekérte az új rendszerképet.

### <a name="cd-pipeline"></a>CD-folyamat
A ci-folyamat sikeres futtatása elindítja a CD-folyamatot az üzembe helyezési folyamat befejezéséhez. Az üzembe helyezést az egyes környezetekbe fokozatosan fogja.

> [!TIP]
> Ha a CD-folyamat nem aktiválódik automatikusan:
> 1. Ellenőrizze, hogy a név megegyezik-e a ág-eseményindítóval [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * A következőnek kellene itt szerepelnie: `arc-cicd-demo-src CI`.
> 1. Futtassa újra a CI-folyamatot.

Miután a sablon és a jegyzékfájl módosult a GitOps-adattárban, a CD-folyamat létrehoz egy véglegesítést, leküldést és jóváhagyásra vonatkozó leküldéses kérelem létrehozásához.
1. Nyissa meg a feladat kimenetében megadott le `Create PR` pr-hivatkozást.
1. Ellenőrizze a GitOps-adattár módosításait. A következőnek kell megjelennie:
   * A Helm-sablon nagy szintjének módosítása.
   * Alacsony szintű Kubernetes-jegyzékek, amelyek a célállapot mögöttes változásait mutatják. A Flux telepíti ezeket a jegyzékeket.
1. Ha minden rendben van, hagyja jóvá és egészül ki a le pr-t.

1. Néhány perc múlva a Flux felveszi a változást, és elindítja az üzembe helyezést.
1. A port helyi továbbítása a használatával, és `kubectl` az alkalmazás megfelelő működése a következő használatával:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Tekintse meg az Azure Vote alkalmazást a böngészőben a következő: `http://localhost:8080/` .

1. Szavazzon a kedvencekre, és készüljön fel néhány módosításra az alkalmazásban.

## <a name="set-up-environment-approvals"></a>Környezeti jóváhagyások beállítása
Az alkalmazás üzembe helyezésekor nem csupán a kódot vagy a sablonokat módosíthatja, de akaratlan módon rossz állapotba is hagyhatja a fürtöt.

Ha a fejlesztői környezet az üzembe helyezés után felfed egy törést, ne ússa meg a későbbi környezetekbe környezeti jóváhagyásokkal.

1. Az Azure DevOps-projektben menjen a védeni szükséges környezetbe.
1. Lépjen az **Erőforrás Jóváhagyások és** Ellenőrzések lapra.
1. Válassza a **Létrehozás** lehetőséget.
1. Adja meg a jóváhagyókat és egy opcionális üzenetet.
1. Válassza **ismét a Létrehozás** lehetőséget a manuális jóváhagyási ellenőrzés kiegészítésének befejezéséhez.

További részletekért lásd a Jóváhagyás és [ellenőrzések meghatározása oktatóanyagot.](/azure/devops/pipelines/process/approvals)

A CD-folyamat következő futtatásakor a folyamat a GitOps pr létrehozása után szünetel. Ellenőrizze, hogy a módosítás megfelelően lett-e szinkronizálva, és megfelel-e az alapvető funkcióknak. Hagyja jóvá a folyamatból való ellenőrzést, hogy a folyamat a következő környezetbe változott.

## <a name="make-an-application-change"></a>Alkalmazás módosítása

Ezzel az alapkonfigurációval a fürtön az állapotot képviselő sablonok és jegyzékek kismértékben módosítják az alkalmazást.

1. Az **arc-cicd-demo-src adattóban** szerkessze a [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) fájlt.

2. Mivel a "Macskák és kutyák" nem kap elég szavazatot, módosítsa a "Tabs vs Spaces" (Lapok és szóközök) beállításokra a szavazatok számának meghozásaként.

3. Véglegesítés egy új ágban, leküldés, majd lekéréses kérelem létrehozása.
   * Ez a ci-/CD-életciklust el elindítani jellemző fejlesztői folyamat.

## <a name="pr-validation-pipeline"></a>Le pr-érvényesítési folyamat

A le pr-folyamat a hibás változás elleni védelem első vonala. Az alkalmazáskódok általános minőségellenőrzése magában foglalja a lintinget és a statikus elemzést. A GitOps szempontjából ugyanakkora minőséget kell biztosítani az eredményül kapott infrastruktúra üzembe helyezéséhez.

Az alkalmazás Dockerfile- és Helm-diagramjai az alkalmazáshoz hasonlóan használhatnak lintinget.

A linting során előforduló hibák a következő tartományba esik:
* Helytelenül formázott YAML-fájlok:
* Ajánlott javaslatok, például az alkalmazás processzor- és memóriakorlátának beállítása.

> [!NOTE]
> A Helm-linting valós alkalmazásokban való lehető legjobb lefedettségéhez olyan értékeket kell helyettesítenie, amelyek viszonylag hasonlóak a valós környezetben használtakhoz.

A folyamat végrehajtása során talált hibák a futtatás teszteredmények szakaszában jelennek meg. Innen a következő lehetőségeket éri el:
* Nyomon követheti a hibatípusok hasznos statisztikáit.
* Keresse meg az első véglegesítést, amelyen a rendszer észlelte őket.
* A stack nyomkövetési stílusa a hibát okozó kódszakaszra mutató hivatkozásokat tartalmaz.

A folyamat futásának befejezése után ön biztosította az alkalmazáskód és az azt üzembe helyező sablon minőségét. Most már jóváhagyhatja és befejezheti a le pr-t. A CI újra fut, és újra létrehozza a sablonokat és a jegyzékeket a CD-folyamat aktiválása előtt.

> [!TIP]
> Valós környezetben ne felejtsen el ági szabályzatokat beállítani, hogy a lekért lekért szolgáltatás megfelel-e a minőségi ellenőrzéseken. További információt az Ág-szabályzatok [beállítása cikkben](/azure/devops/repos/git/branch-policies) talál.

## <a name="cd-process-approvals"></a>CD-folyamat jóváhagyásai

A ci-folyamat sikeres futtatása elindítja a CD-folyamatot az üzembe helyezési folyamat befejezéséhez. A CD-folyamat első üzembe helyezéséhez hasonlóan az egyes környezetekben is növekményesen fog üzembe helyezni. Ezúttal a folyamat megköveteli az egyes üzembe helyezési környezetek jóváhagyását.

1. Hagyja jóvá a környezetben való `dev` üzembe helyezést.
1. Miután a sablon és a jegyzékfájl módosult a GitOps-adattárban, a CD-folyamat létrehoz egy véglegesítést, leküldéses adatokat, és létrehoz egy leküldéses kérelem jóváhagyásra.
1. Nyissa meg a feladatban megadott le pr-hivatkozást.
1. Ellenőrizze a GitOps-adattár módosításait. A következőnek kell megjelennie:
   * Magas szintű Helm-sablonváltozások.
   * Alacsony szintű Kubernetes-jegyzékek, amelyek a célállapot mögöttes változásait mutatják.
1. Ha minden rendben van, hagyja jóvá és egészül ki a le pr-t.
1. Várjon, amíg az üzembe helyezés befejeződik.
1. Alapszintű füsttesztként lépjen az alkalmazás oldalára, és ellenőrizze, hogy a szavazóalkalmazás most már megjeleníti-e a Tabulátorok és a Szóközök lapokat.
   * A port helyi továbbítása a használatával, és `kubectl` az alkalmazás megfelelő működése a következő használatával: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Tekintse meg az Azure Vote alkalmazást a böngészőben a helyen, és ellenőrizze, hogy a szavazási lehetőségek tabulátorokra vagy http://localhost:8080/ a Spacesre változtak-e. 
1. Ismételje meg az 1–7. lépést a `stage` környezetben.

Az üzembe helyezés befejeződött. Ezzel befejezi a CI/CD-munkafolyamatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforrásokat a következő lépésekkel:

1. Törölje a Azure Arc GitOps konfigurációs kapcsolatát:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Távolítsa el `dev` a névteret:
   * `kubectl delete namespace dev`

3. Távolítsa el `stage` a névteret:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy teljes CI/CD-munkafolyamatot állíthat be, amely a DevOpsot az alkalmazásfejlesztéstől az üzembe helyezésig implementálja. Az alkalmazás módosításai automatikusan aktiválják az ellenőrzést és az üzembe helyezést manuális jóváhagyásokkal.

Ha többet szeretne megtudni a GitOpsról és az engedélyezett Kubernetes-konfigurációkról Azure Arc fogalmi cikkünk.

> [!div class="nextstepaction"]
> [CI/CD-munkafolyamat a GitOps használatával – Azure Arc Kubernetes használata](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
