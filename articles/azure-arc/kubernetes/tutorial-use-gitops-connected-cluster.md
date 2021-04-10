---
title: 'Oktatóanyag: konfigurációk üzembe helyezése a GitOps használatával egy Azure arc-kompatibilis Kubernetes-fürtön'
description: Ez az oktatóanyag bemutatja, hogyan alkalmazhat konfigurációkat egy Azure arc-kompatibilis Kubernetes-fürtön. A folyamat elméleti feltételeit a konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes című cikkben találja.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 987fb5745b6528eb96b4237f698b3ae371d69287
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731819"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Oktatóanyag: konfigurációk üzembe helyezése a GitOps használatával egy Azure arc-kompatibilis Kubernetes-fürtön 

Ebben az oktatóanyagban a konfigurációk a GitOps használatával lesznek alkalmazva egy Azure arc-kompatibilis Kubernetes-fürtön. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Hozzon létre egy konfigurációt egy Azure arc-kompatibilis Kubernetes-fürtön egy példa git-tárház használatával.
> * Ellenőrizze, hogy a konfiguráció létrehozása sikeres volt-e.
> * Konfiguráció alkalmazása privát git-tárházból.
> * Ellenőrizze a Kubernetes konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürt.
    - Ha még nem csatlakoztatta a fürtöt, tekintse meg a [Connect an Azure arc enabled Kubernetes-fürt](quickstart-connect-cluster.md)gyors üzembe helyezését ismertető útmutatót.
- A szolgáltatás előnyeinek és architektúrájának megismerése. További információk: [konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes-cikk](conceptual-configurations.md).

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

A cikkben használt [példa adattár](https://github.com/Azure/arc-k8s-demo) a fürt egy személyének köré épül fel. Az ebben a tárházban található jegyzékfájlok néhány névteret foglalnak magukban, üzembe helyezik a munkaterheléseket, és biztosítanak néhány, a csoportra jellemző konfigurációt. A tárház és a GitOps használatával a következő erőforrásokat hozza létre a fürtön:

* Névterek: `cluster-config` , `team-a` , `team-b`
* Telepítési `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

Az `config-agent` Azure lekérdezése új vagy frissített konfigurációkhoz. Ez a feladat akár 30 másodpercig is eltarthat.

Ha privát tárházat társít a konfigurációhoz, hajtsa végre az alábbi lépéseket a [konfiguráció alkalmazása privát git-tárházból](#apply-configuration-from-a-private-git-repository)című témakörben.

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Az Azure CLI bővítmény használatával `k8s-configuration` csatlakoztathat egy csatlakoztatott fürtöt a [példa git-tárházhoz](https://github.com/Azure/arc-k8s-demo). 
1. Nevezze el ezt a konfigurációt `cluster-config` .
1. Utasítsa az ügynököt az operátor üzembe helyezésére a `cluster-config` névtérben.
1. Adja meg az operátor `cluster-admin` engedélyeit.

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

### <a name="use-a-public-git-repository"></a>Nyilvános git-tárház használata

| Paraméter | Formátum |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] vagy git://Server/repo [. git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Privát git-tárház használata SSH-val és fluxus által létrehozott kulcsokkal

Adja hozzá a Flux által generált nyilvános kulcsot a git-szolgáltató felhasználói fiókjához. Ha a kulcsot hozzáadja a tárházhoz a felhasználói fiók helyett, használja az `git@` URL-cím helyett `user@` . 

További részletekért ugorjon a [konfiguráció alkalmazása egy privát git-tárházból](#apply-configuration-from-a-private-git-repository) című szakaszra.


| Paraméter | Formátum | Jegyzetek
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` lecserélhető `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Privát git-tárház használata SSH-val és felhasználó által biztosított kulcsokkal

Adja meg a saját titkos kulcsát közvetlenül vagy egy fájlban. A kulcsnak [PEM formátumúnak](https://aka.ms/PEMformat) kell lennie, és véget kell adni a sortörés (\n) értéknek. 

Adja hozzá a társított nyilvános kulcsot a git-szolgáltató felhasználói fiókjához. Ha a kulcs a felhasználói fiók helyett a tárházba kerül, használja a `git@` következőt: `user@` . 

További részletekért ugorjon a [konfiguráció alkalmazása egy privát git-tárházból](#apply-configuration-from-a-private-git-repository) című szakaszra.  

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` lecserélhető `user@` |
| `--ssh-private-key` | Base64 kódolású kulcs [PEM formátumban](https://aka.ms/PEMformat) | Kulcs közvetlen megadása |
| `--ssh-private-key-file` | helyi fájl teljes elérési útja | Adja meg a PEM formátumú kulcsot tartalmazó helyi fájl teljes elérési útját

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Privát git-gazdagép használata SSH-val és felhasználó által biztosított ismert gazdagépekkel

A Flux-kezelő a git-tárháznak az SSH-kapcsolat létesítése előtt történő hitelesítéséhez az ismert gazdagépek fájljában tárolja a közös git-gazdagépek listáját. Ha nem *gyakori* git-tárházat vagy saját git-gazdagépet használ, megadhatja a gazdagép kulcsát, hogy a Flux azonosítani tudja a tárházat. 

A titkos kulcsokhoz hasonlóan a known_hosts tartalmat közvetlenül vagy egy fájlban is megadhatja. Saját tartalom megadásakor használja a [known_hosts Content Format specifikációkat](https://aka.ms/KnownHostsFormat), valamint a fenti SSH-kulcsokat.

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` lecserélhető `user@` |
| `--ssh-known-hosts` | Base64 kódolású | Az ismert gazdagépek közvetlen tartalmainak megadása |
| `--ssh-known-hosts-file` | helyi fájl teljes elérési útja | Ismert gazdagépek tartalma helyi fájlban |

### <a name="use-a-private-git-repository-with-https"></a>Privát git-tárház használata HTTPS-sel

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS alapszintű hitelesítéssel |
| `--https-user` | nyers vagy Base64 kódolású | HTTPS-Felhasználónév |
| `--https-key` | nyers vagy Base64 kódolású | HTTPS személyes hozzáférési jogkivonat vagy jelszó

>[!NOTE]
>* A Helm operátor diagramjának 1.2.0 + verziója támogatja a HTTPS Helm kiadás privát hitelesítését.
>* Az AK által felügyelt fürtök esetében a HTTPS Helm kiadás nem támogatott.
>* Ha a Flux segítségével szeretné elérni a git-tárházat a proxyn keresztül, frissítenie kell az Azure arc-ügynököket a proxybeállítások alapján. További információ: [Csatlakozás kimenő proxykiszolgáló használatával](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>További paraméterek

Szabja testre a konfigurációt a következő választható paraméterekkel:

| Paraméter | Leírás |
| ------------- | ------------- |
| `--enable-helm-operator`| Váltson a Helm chart üzemelő példányok támogatásának engedélyezéséhez. |
| `--helm-operator-params` | A Helm operátor diagramjának értékei (ha engedélyezve van). Például: `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | A Helm operátor diagramjának verziója (ha engedélyezve van). Használja a 1.2.0 + verziót. Alapértelmezett: "1.2.0". |
| `--operator-namespace` | Az operátori névtér neve. Alapértelmezett: "default". Max: 23 karakter. |
| `--operator-params` | A kezelőhöz tartozó paraméterek. Egy idézőjelek között kell megadni. Például: ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Támogatott beállítások  `--operator-params` :

| Beállítás | Leírás |
| ------------- | ------------- |
| `--git-branch`  | A git-tárház Kubernetes-jegyzékekhez használt ága. Az alapértelmezett érték a "Master". Az újabb Tárházak neve root ág `main` , ebben az esetben be kell állítania a következőt: `--git-branch=main` . |
| `--git-path`  | Relatív elérési út a git-tárházban a Flux számára a Kubernetes-jegyzékek kereséséhez. |
| `--git-readonly` | A git-tárház csak olvashatóként lesz értelmezve. A Flux nem próbál meg írni. |
| `--manifest-generation`  | Ha engedélyezve van, a Flux a. Flux. YAML és a Kustomize vagy más manifest-generátorok futtatására fog keresni. |
| `--git-poll-interval`  | Az az időszak, amikor a git-tárházat új véglegesíteni kívánja lekérdezni. Az alapértelmezett érték `5m` (5 perc). |
| `--sync-garbage-collection`  | Ha engedélyezve van, a Flux törli a létrehozott erőforrásokat, de már nem jelennek meg a git-ben. |
| `--git-label`  | A szinkronizálási folyamat nyomon követésére szolgáló címke. A git-ág címkézésére szolgál.  Az alapértelmezett szint a `flux-sync`. |
| `--git-user`  | A git-véglegesítő felhasználóneve. |
| `--git-email`  | A git-végrehajtáshoz használandó e-mail-cím. 

Ha nem szeretné, hogy a Flux a tárházba írjon `--git-user` `--git-email` , vagy nincs beállítva, akkor a `--git-readonly` rendszer automatikusan beállítja.

További információt a [Flux dokumentációjában](https://aka.ms/FluxcdReadme)talál.

> [!TIP]
> Az Azure arc-kompatibilis Kubernetes-erőforrás **GitOps** lapján létrehozhat egy konfigurációt az Azure Portal.

## <a name="validate-the-configuration"></a>A konfiguráció ellenőrzése

Az Azure CLI használatával ellenőrizheti, hogy a konfiguráció létrehozása sikeres volt-e.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

A konfigurációs erőforrás frissítése a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal együtt történik.

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

Egy konfiguráció létrehozásakor vagy frissítésekor néhány dolog történik:

1. Az Azure arc `config-agent` figyeli az új vagy frissített konfigurációk () Azure Resource managerét `Microsoft.KubernetesConfiguration/sourceControlConfigurations` , és észreveszi az új `Pending` konfigurációt.
1. A `config-agent` beolvassa a konfigurációs tulajdonságokat, és létrehozza a cél névterét.
1. Az Azure arc `controller-manager` létrehoz egy Kubernetes-szolgáltatásfiókot, és leképezi a megfelelő engedélyek (vagy hatókör) [ClusterRoleBinding vagy RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) `cluster` `namespace` . Ezután üzembe helyezi a példányát `flux` .
1. Ha a Flux által generált kulcsokkal rendelkező SSH lehetőséget használja, `flux` létrehoz egy SSH-kulcsot, és naplózza a nyilvános kulcsot.
1. A `config-agent` jelentések állapota visszakerül az Azure konfigurációs erőforrására.

A kiépítési folyamat során a konfigurációs erőforrás átkerül néhány állapotba. A folyamat figyelése a `az k8s-configuration show ...` fenti paranccsal:

| Szakasz módosítása | Leírás |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | A kezdeti és a folyamatban lévő állapotot jelöli. |
| `complianceStatus` -> `Installed`  | `config-agent` sikeresen konfigurálta a fürtöt, és `flux` hiba nélkül telepítette. |
| `complianceStatus` -> `Failed` | `config-agent` Hiba történt a telepítés során `flux` . A részletek a `complianceStatus.message` Válasz törzsében vannak megadva. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Konfiguráció alkalmazása privát git-tárházból

Ha privát git-tárházat használ, konfigurálnia kell az SSH nyilvános kulcsát a tárházban. Az Ön által megadott vagy a Flux a nyilvános SSH-kulcsot hozza létre. A nyilvános kulcsot az adott git-adattáron vagy a tárházhoz hozzáféréssel rendelkező git-felhasználón is konfigurálhatja. 

### <a name="get-your-own-public-key"></a>Saját nyilvános kulcs beszerzése

Ha létrehozta a saját SSH-kulcsait, akkor már rendelkezik a privát és a nyilvános kulcsokkal.

#### <a name="get-the-public-key-using-azure-cli"></a>Nyilvános kulcs beszerzése az Azure CLI használatával 

Használja az alábbi lépéseket az Azure CLI-ben, ha a Flux generálja a kulcsokat.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>A Azure Portal nyilvános kulcsának beolvasása

Azure Portal, ha a Flux generálja a kulcsokat, az alábbi lépéseket kell eljárnia.

1. A Azure Portal navigáljon a csatlakoztatott fürt erőforrásához.
2. Az erőforrás lapon válassza a "GitOps" lehetőséget, és tekintse meg a fürthöz tartozó konfigurációk listáját.
3. Válassza ki a privát git-tárházat használó konfigurációt.
4. A megnyíló helyi ablakban az ablak alján másolja az **adattár nyilvános kulcsát**.

#### <a name="add-public-key-using-github"></a>Nyilvános kulcs hozzáadása a GitHub használatával

Használja az alábbi lehetőségek egyikét:

* 1. lehetőség: a nyilvános kulcs hozzáadása a felhasználói fiókhoz (a fiókban lévő összes adattárra vonatkozik):  
    1. Nyissa meg a GitHubot, és kattintson a profil ikonjára a lap jobb felső sarkában.
    2. Kattintson a **Settings** (Beállítások) lehetőségre.
    3. Kattintson az **SSH és a GPG kulcsok** elemre.
    4. Kattintson az **új SSH-kulcs** lehetőségre.
    5. Adja meg a címet.
    6. Illessze be a nyilvános kulcsot a környező idézőjelek nélkül.
    7. Kattintson az **SSH-kulcs hozzáadása** lehetőségre.

* 2. lehetőség: a nyilvános kulcs hozzáadása üzembe helyezési kulcsként a git-tárházhoz (csak erre a tárházra vonatkozik):  
    1. Nyissa meg a GitHubot, és navigáljon a tárházhoz.
    1. Kattintson a **Settings** (Beállítások) lehetőségre.
    1. Kattintson a **kulcsok üzembe helyezése** lehetőségre.
    1. Kattintson az **üzembe helyezési kulcs hozzáadása** lehetőségre.
    1. Adja meg a címet.
    1. Győződjön meg az **írási hozzáférés engedélyezése lehetőségről**.
    1. Illessze be a nyilvános kulcsot a környező idézőjelek nélkül.
    1. Kattintson a **Kulcs hozzáadása** lehetőségre.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Nyilvános kulcs hozzáadása egy Azure DevOps-tárház használatával

Az alábbi lépésekkel adhatja hozzá a kulcsot az SSH-kulcsokhoz:

1. A jobb felső sarokban lévő **felhasználói beállítások** alatt (a profil képe mellett) kattintson az **SSH nyilvános kulcsok** elemre.
1. Válassza az  **+ új kulcs** lehetőséget.
1. Adjon meg egy nevet.
1. Illessze be a nyilvános kulcsot a környező idézőjelek nélkül.
1. Kattintson a **Hozzáadás** parancsra.

## <a name="validate-the-kubernetes-configuration"></a>A Kubernetes konfigurációjának ellenőrzése

Miután `config-agent` telepítette a `flux` példányt, a git-tárházban tárolt erőforrásoknak futniuk kell a fürtön. Ellenőrizze, hogy a névterek, a központi telepítések és az erőforrások létre lettek-e hozva a következő paranccsal:

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

Láthatjuk, hogy a, a, `team-a` `team-b` és a `itops` `cluster-config` névterek létre lettek hozva.

Az `flux` operátor a következő `cluster-config` konfigurációs erőforrás utasításai szerint lett telepítve a névtérben:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>További feltárás

A konfigurációs tárház részeként telepített egyéb erőforrásokat a következőkkel derítheti fel:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Egy konfiguráció törlése az Azure CLI vagy a Azure Portal használatával. A DELETE parancs futtatása után a rendszer azonnal törli a konfigurációs erőforrást az Azure-ban. A fürthöz társított objektumok teljes törlése 10 percen belül megtörténik. Ha a konfiguráció eltávolítása sikertelen állapotban van, a társított objektumok teljes törlése akár egy órát is igénybe vehet.

Ha a `namespace` hatókörrel rendelkező konfiguráció törlődik, a névtér nem törlődik az Azure arc használatával a meglévő számítási feladatok megszakításának elkerülése érdekében. Szükség esetén manuálisan is törölheti a névteret a használatával `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> A rendszer a konfiguráció törlésekor nem törli a fürt azon módosításait, amelyek a nyomon követett git-tárházból történő központi telepítések eredményeképpen történtek.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan implementálhatja a CI/CD-t a GitOps.
> [!div class="nextstepaction"]
> [CI/CD implementálása a GitOps](./tutorial-gitops-ci-cd.md)
