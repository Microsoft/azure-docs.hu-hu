---
title: 'Oktatóanyag: Konfigurációk üzembe helyezése a GitOps használatával egy Azure Arc engedélyezett Kubernetes-fürtön'
description: Ez az oktatóanyag bemutatja, hogyan lehet konfigurációkat alkalmazni egy Azure Arc engedélyezett Kubernetes-fürtön. A folyamat fogalmi áttekintését a Configurations and GitOps - Azure Arc Kubernetes enabled Kubernetes (Konfigurációk és GitOps – Azure Arc Kubernetes engedélyezése) című cikkben olvashatja.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 0f1172ffa0d29734e7ec005bf2812eeca215aa0d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484166"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Oktatóanyag: Konfigurációk üzembe helyezése a GitOps használatával egy Azure Arc engedélyezett Kubernetes-fürtön 

Ebben az oktatóanyagban a GitOps használatával fog konfigurációkat alkalmazni egy Azure Arc kubernetes-fürtön. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Hozzon létre egy konfigurációt egy Azure Arc engedélyezett Kubernetes-fürtön egy példa Git-adattár használatával.
> * Ellenőrizze, hogy a konfiguráció sikeresen létrejött-e.
> * Konfiguráció alkalmazása privát Git-adattárból.
> * Ellenőrizze a Kubernetes konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy meglévő Azure Arc kubernetes-hez csatlakoztatott fürt.
    - Ha még nem csatlakoztatott fürtöt, járjon végig a Connect an Azure Arc enabled Kubernetes cluster (Fürt csatlakoztatása Azure Arc [Kubernetes-fürthöz)](quickstart-connect-cluster.md)című rövid útmutatóban.
- A funkció előnyeinek és architektúrája. További információ: [Konfigurációk és GitOps – Azure Arc Kubernetes-kompatibilis cikk.](conceptual-configurations.md)
- Telepítse az `k8s-configuration` Azure CLI-bővítmény >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Ha a bővítmény már telepítve van, a következő paranccsal frissítheti a legújabb `k8s-configuration` verzióra: `az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Az [ebben a cikkben használt](https://github.com/Azure/arc-k8s-demo) példatár egy fürtüzemeltető személyisége köré van strukturálva. Az adattárban a jegyzékek kiépítik a névtereket, üzembe helyeznek számítási feladatokat, és csapatspecifikus konfigurációt biztosítanak. Ha ezt az adattárat a GitOps használatával használja, a következő erőforrásokat hozza létre a fürtön:

* Névterek: `cluster-config` , `team-a` , `team-b`
* Telepítési: `cluster-config/azure-vote`
* ConfigMap :: `team-a/endpoints`

A `config-agent` lekérdezés az Azure-ban keres új vagy frissített konfigurációkat. Ez a feladat akár 30 másodpercet is igénybe fog venni.

Ha privát adattárat társít a konfigurációval, kövesse a Konfiguráció alkalmazása privát [Git-adattárból cikk alábbi lépéseit.](#apply-configuration-from-a-private-git-repository)

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
A azure cli-bővítményének `k8s-configuration` használatával csatolhat egy csatlakoztatott fürtöt a példa [Git-adattárhoz.](https://github.com/Azure/arc-k8s-demo) 
1. A konfigurációnak nevezze el `cluster-config` a nevet.
1. Kérje meg az ügynököt, hogy telepítse az operátort `cluster-config` a névtérben.
1. Adjon engedélyt az `cluster-admin` operátornak.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Nyilvános Git-adattár használata

| Paraméter | Formátum |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] vagy git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Privát Git-adattár használata SSH- és Flux által létrehozott kulcsokkal

Adja hozzá a Flux által létrehozott nyilvános kulcsot a Git-szolgáltatóban található felhasználói fiókhoz. Ha a felhasználói fiók helyett a kulcsot veszi fel az adattárba, az URL-cím helyett használja `git@` `user@` a értéket. 

További részletekért ugorjon a Konfiguráció [alkalmazása privát Git-adattárból](#apply-configuration-from-a-private-git-repository) szakaszra.


| Paraméter | Formátum | Jegyzetek
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] vagy user@server:repo [.git] | `git@` a következőt cseréli le: `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Privát Git-adattár használata SSH-val és felhasználó által megadott kulcsokkal

Adja meg a saját titkos kulcsát közvetlenül vagy egy fájlban. A kulcsnak [PEM](https://aka.ms/PEMformat) formátumban kell lennie, és új vonallal (\n) kell végződnie. 

Adja hozzá a társított nyilvános kulcsot a Git-szolgáltatóban található felhasználói fiókhoz. Ha a felhasználói fiók helyett a kulcsot ad hozzá az adattárhoz, használja a `git@` értéket a `user@` helyett. 

További [részletekért ugorjon az Apply configuration from a private Git repository (Konfiguráció](#apply-configuration-from-a-private-git-repository) alkalmazása privát Git-adattárból) szakaszra.  

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] vagy user@server:repo [.git] | `git@` lecserélheti a következőt: `user@` |
| `--ssh-private-key` | base64 kódolású kulcs [PEM formátumban](https://aka.ms/PEMformat) | Kulcs közvetlen megszava |
| `--ssh-private-key-file` | a helyi fájl teljes elérési útja | Adja meg a PEM-formátumkulcsot tartalmazó helyi fájl teljes elérési útját

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Privát Git-gazdagép használata SSH-val és felhasználó által biztosított ismert gazdagépekkel

A Flux operátor az ismert gazdagépek fájljában tartja fenn a gyakori Git-gazdagépek listáját a Git-adattár hitelesítéséhez az SSH-kapcsolat létrehozása előtt. Ha nem gyakori  Git-adattárat vagy saját Git-gazdagépet használ, meg is használhatja a gazdagépkulcsot, hogy a Flux azonosítani tudja az adattárat. 

A titkos kulcsokhoz hasonlóan közvetlenül known_hosts vagy fájlban is meg lehet adni a saját tartalmai számára. Saját tartalom biztosításakor használja a known_hosts formátum [specifikációit](https://aka.ms/KnownHostsFormat)és a fenti SSH-kulcshasználati forgatókönyvek valamelyikét.

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] vagy user@server:repo [.git] | `git@` lecserélheti a következőt: `user@` |
| `--ssh-known-hosts` | base64 kódolású | Ismert gazdagépek tartalmának közvetlen szolgáltatása |
| `--ssh-known-hosts-file` | a helyi fájl teljes elérési útja | Ismert gazdagépek tartalmának helyi fájlban való megterjesztése |

### <a name="use-a-private-git-repository-with-https"></a>Privát Git-adattár használata HTTPS-sel

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | HTTPS alapszintű hitelesítéssel |
| `--https-user` | nyers vagy base64 kódolású | HTTPS-felhasználónév |
| `--https-key` | nyers vagy base64 kódolású | HTTPS személyes hozzáférési jogkivonat vagy jelszó

>[!NOTE]
>* A Helm-operátordiagram 1.2.0+-s vagy újabb verziója támogatja a HTTPS Helm kiadás privát hitelesítését.
>* A HTTPS Helm-kiadás nem támogatott az AKS által felügyelt fürtökön.
>* Ha flux-re van szüksége a Git-adattár proxyn keresztüli eléréséhez, frissítenie kell a Azure Arc-ügynököket a proxybeállításokkal. További információ: Csatlakozás kimenő [proxykiszolgálóval.](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server)


## <a name="additional-parameters"></a>További paraméterek

Szabja testre a konfigurációt az alábbi választható paraméterekkel:

| Paraméter | Leírás |
| ------------- | ------------- |
| `--enable-helm-operator`| Váltson a Helm-diagramok üzembe helyezésének támogatásához. |
| `--helm-operator-params` | Diagramértékek a Helm-operátorhoz (ha engedélyezve van). Például: `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | A Helm-operátor diagramverziója (ha engedélyezve van). Használja az 1.2.0-s vagy újabb verziót. Alapértelmezett: "1.2.0". |
| `--operator-namespace` | Az operátornévtér neve. Alapértelmezett érték: "default". Maximum: 23 karakter. |
| `--operator-params` | Az operátor paraméterei. A egyszeres idézőjelek között kell lennie. Például: ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>A által támogatott  `--operator-params` lehetőségek:

| Beállítás | Leírás |
| ------------- | ------------- |
| `--git-branch`  | A Git-adattár Kubernetes-jegyzékfájlhoz használható ága. Az alapértelmezett érték a "master". Az újabb adattárak gyökérágának neve , ebben az esetben be kell állítania a `main` következőt: `--git-branch=main` . |
| `--git-path`  | Relatív elérési út a Flux Git-adattárában a Kubernetes-jegyzékek megkeresésében. |
| `--git-readonly` | A Git-adattár csak olvashatónak minősül. A Flux nem kísérel meg írni rá. |
| `--manifest-generation`  | Ha engedélyezve van, a Flux a .flux.yaml fájlra keres rá, és futtatja a Kustomize-t vagy más jegyzékgenerátorokat. |
| `--git-poll-interval`  | Az az időszak, amikor a Git-adattárból lekérdezi az új véglegesítéseket. Az alapértelmezett érték `5m` (5 perc). |
| `--sync-garbage-collection`  | Ha engedélyezve van, a Flux törli a létrehozott erőforrásokat, de már nem érhetők el a Gitben. |
| `--git-label`  | Címke a szinkronizálási folyamat nyomon követéséhez. A Git-ág címkézését használja.  Az alapértelmezett szint a `flux-sync`. |
| `--git-user`  | Felhasználónév a Git-véglegesítéshez. |
| `--git-email`  | Git-véglegesítéshez használható e-mail. 

Ha nem szeretné, hogy a Flux az adattárba írjon, és nincs beállítva, akkor a rendszer automatikusan `--git-user` `--git-email` be lesz `--git-readonly` állítva.

További információt a [Flux dokumentációjában talál.](https://aka.ms/FluxcdReadme)

> [!TIP]
> A konfigurációt az Azure Portal Kubernetes-erőforrás **GitOps** lapján Azure Arc hozhatja létre.

## <a name="validate-the-configuration"></a>A konfiguráció ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a konfiguráció sikeresen létrejött-e.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

A konfigurációs erőforrás megfelelőségi állapottal, üzenetekkel és hibakeresési információkkal fog frissülni.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Konfiguráció létrehozásakor vagy frissítésekor néhány dolog történik:

1. A Azure Arc figyeli Azure Resource Manager új vagy frissített konfigurációkat ( ), és észleli `config-agent` `Microsoft.KubernetesConfiguration/sourceControlConfigurations` az új `Pending` konfigurációt.
1. A `config-agent` beolvassa a konfigurációs tulajdonságokat, és létrehozza a cél névteret.
1. A Azure Arc létrehoz egy Kubernetes-szolgáltatásfiókot, és a megfelelő engedélyek (vagy hatókör) érdekében `controller-manager` [clusterRoleBinding vagy RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) szerepkörhöz `cluster` `namespace` leképezi azt. Ezután üzembe helyez egy `flux` -példányt.
1. Ha az SSH-t Flux által generált kulcsokkal használja, a létrehoz egy SSH-kulcsot, és naplózza `flux` a nyilvános kulcsot.
1. A `config-agent` jelentések az Azure-beli konfigurációs erőforrásnak jelentéseket adnak vissza.

A kiépítési folyamat során a konfigurációs erőforrás néhány állapotváltozáson megy keresztül. A folyamat előrehaladásának figyelése `az k8s-configuration show ...` a fenti paranccsal:

| Szakasz módosítása | Leírás |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | A kezdeti és a folyamatban lévő államokat jelöli. |
| `complianceStatus` -> `Installed`  | `config-agent` sikeresen konfigurálta a fürtöt, és hiba `flux` nélkül üzembe helyezett. |
| `complianceStatus` -> `Failed` | `config-agent` hiba történt az üzembe helyezése `flux` során. A részleteket a válasz `complianceStatus.message` törzse tartalmazza. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Konfiguráció alkalmazása privát Git-adattárból

Ha privát Git-adattárat használ, konfigurálnia kell a nyilvános SSH-kulcsot az adattárban. Vagy ön adja meg, vagy a Flux létrehozza a nyilvános SSH-kulcsot. A nyilvános kulcsot konfigurálhatja az adott Git-adattáron vagy az adattárhoz hozzáféréssel rendelkezik Git-felhasználón. 

### <a name="get-your-own-public-key"></a>Saját nyilvános kulcs lekérte

Ha saját SSH-kulcsokat generált, akkor már rendelkezik a titkos és a nyilvános kulcsokkal.

#### <a name="get-the-public-key-using-azure-cli"></a>A nyilvános kulcs lekért használata az Azure CLI használatával 

Ha a Flux létrehozza a kulcsokat, használja a következőt az Azure CLI-ban.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>A nyilvános kulcs lekért Azure Portal

Ha a Flux létrehozza a kulcsokat Azure Portal a következő lépésekben kell végigjárni.

1. A Azure Portal keresse meg a csatlakoztatott fürterőforrást.
2. Az erőforrás oldalán válassza a "GitOps" lehetőséget, és tekintse meg a fürthöz elérhető konfigurációk listáját.
3. Válassza ki a privát Git-adattárat használó konfigurációt.
4. A megnyíló helyi ablakban másolja a tárház nyilvános kulcsát az ablak **alján.**

#### <a name="add-public-key-using-github"></a>Nyilvános kulcs hozzáadása a GitHub használatával

Használja az alábbi lehetőségek egyikét:

* 1. lehetőség: Adja hozzá a nyilvános kulcsot a felhasználói fiókjához (a fiókban az összes tárházra vonatkozik):  
    1. Nyissa meg a GitHubot, és kattintson a profil ikonra az oldal jobb felső sarkában.
    2. Kattintson a **Settings** (Beállítások) lehetőségre.
    3. Kattintson az **SSH- és GPG-kulcsok elemre.**
    4. Kattintson az **Új SSH-kulcs elemre.**
    5. Cím megszabadása.
    6. Illessze be a nyilvános kulcsot idézőjelek nélkül.
    7. Kattintson az **SSH-kulcs hozzáadása elemre.**

* 2. lehetőség: Adja hozzá a nyilvános kulcsot üzembe helyezési kulcsként a Git-adattárhoz (csak erre az adattárra vonatkozik):  
    1. Nyissa meg a GitHubot, és keresse meg az adattárat.
    1. Kattintson a **Settings** (Beállítások) lehetőségre.
    1. Kattintson a **Kulcsok üzembe helyezése elemre.**
    1. Kattintson az **Üzembe helyezési kulcs hozzáadása elemre.**
    1. Cím megszabadása.
    1. Jelölje be **az Írási hozzáférés engedélyezése jelölőnégyzetet.**
    1. Illessze be a nyilvános kulcsot idézőjelek nélkül.
    1. Kattintson a **Kulcs hozzáadása elemre.**

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Nyilvános kulcs hozzáadása Azure DevOps-adattár használatával

A következő lépésekkel adjuk hozzá a kulcsot az SSH-kulcsokhoz:

1. A **jobb felső sarokban** (a profilkép mellett) a Felhasználói beállítások alatt kattintson az **SSH nyilvános kulcsok elemre.**
1. Válassza **az + Új kulcs lehetőséget.**
1. Nevezze el.
1. Illessze be a nyilvános kulcsot idézőjelek nélkül.
1. Kattintson a **Hozzáadás** parancsra.

## <a name="validate-the-kubernetes-configuration"></a>A Kubernetes-konfiguráció ellenőrzése

A példány telepítése után a Git-adattárban található erőforrásoknak el kell indulni a `config-agent` `flux` fürtbe való folyamba. Ellenőrizze, hogy a névterek, üzemelő példányok és erőforrások létrejöttek-e a következő paranccsal:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Látható, hogy `team-a` , , és névterek `team-b` `itops` `cluster-config` létrejöttek.

Az operátor a konfigurációs erőforrás által irányított névtérben `flux` `cluster-config` lett üzembe helyezni:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>További feltárás

A konfigurációs adattár részeként üzembe helyezett egyéb erőforrásokat a következővel tárhatja fel:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Konfiguráció törlése az Azure CLI vagy a Azure Portal. A delete parancs futtatása után a konfigurációs erőforrás azonnal törlődik az Azure-ban. A fürthöz társított objektumok teljes törlése 10 percen belül megtörténik. Ha a konfiguráció eltávolítása sikertelen állapotban van, a társított objektumok teljes törlése akár egy órát is eltelhet.

Hatókörrel kapcsolatos konfiguráció törlésekor a rendszer nem törli a névteret a Azure Arc a meglévő számítási `namespace` feladatok feltörése érdekében. Szükség esetén manuálisan törölheti ezt a névteret a `kubectl` használatával.

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> A fürtön a nyomonett Git-adattárból való üzembe helyezés eredményeként végrehajtott módosítások nem törlődnek a konfiguráció törlésekor.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyag azt tanulhatja meg, hogyan implementálja a CI/CD-t a GitOps használatával.
> [!div class="nextstepaction"]
> [CI/CD implementálja a GitOpsot](./tutorial-gitops-ci-cd.md)
