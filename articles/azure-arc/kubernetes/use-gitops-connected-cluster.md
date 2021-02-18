---
title: Konfigurációk üzembe helyezése a GitOps használatával Arc-kompatibilis Kubernetes-fürtön (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure arc-kompatibilis Kubernetes-fürt (előzetes verzió) konfigurálása a GitOps használatával
keywords: GitOps, Kubernetes, K8s, Azure, arc, Azure Kubernetes szolgáltatás, AK, tárolók
ms.openlocfilehash: b30ecde0e128a955967638828dcb7bec008205ea
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652479"
---
# <a name="deploy-configurations-using-gitops-on-an-arc-enabled-kubernetes-cluster-preview"></a>Konfigurációk üzembe helyezése a GitOps használatával egy arc-kompatibilis Kubernetes-fürtön (előzetes verzió)

Ez a cikk bemutatja, hogyan alkalmazhat konfigurációkat egy Azure arc-kompatibilis Kubernetes-fürtön. A folyamat elméleti feltételeit a [konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes című cikkben](./conceptual-configurations.md)találja.

## <a name="before-you-begin"></a>Előkészületek

* Ellenőrizze, hogy rendelkezik-e meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürttel. Ha csatlakoztatott fürtre van szüksége, tekintse meg az [Azure arc-kompatibilis Kubernetes-fürt csatlakoztatása](./connect-cluster.md)című rövid útmutatót.

* Tekintse át a [konfigurációkat és a GitOps az arc for Kubernetes című cikket](./conceptual-configurations.md) a funkció előnyeinek és architektúrájának megismeréséhez.

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

A cikkben használt [példában szereplő adattár](https://github.com/Azure/arc-k8s-demo) egy olyan fürt munkatársai köré épül fel, akik néhány névteret szeretnének kiépíteni, közös munkaterhelést telepíteni, és bizonyos, a csoportra jellemző konfigurációt biztosítanak. Az adattár használata a következő erőforrásokat hozza létre a fürtön:

* **Névterek:** `cluster-config` , `team-a` , `team-b`
* **Üzembe helyezés:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

A `config-agent` lekérdezi az Azure-t új vagy frissített nyelven `sourceControlConfiguration` . Ez a feladat akár 30 másodpercig is eltarthat.

Ha privát tárházat társít a `sourceControlConfiguration` alkalmazáshoz, hajtsa végre a [konfiguráció alkalmazása privát git-tárházból](#apply-configuration-from-a-private-git-repository)című témakör lépéseit.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az Azure CLI bővítmény használatával `k8sconfiguration` csatlakoztathat egy csatlakoztatott fürtöt a [példa git-tárházhoz](https://github.com/Azure/arc-k8s-demo). 
1. Nevezze el ezt a konfigurációt `cluster-config` .
1. Utasítsa az ügynököt az operátor üzembe helyezésére a `cluster-config` névtérben.
1. Adja meg az operátor `cluster-admin` engedélyeit.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

#### <a name="use-a-public-git-repository"></a>Nyilvános git-tárház használata

| Paraméter | Formátum |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] vagy git://Server/repo [. git]

#### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Privát git-tárház használata SSH-val és fluxus által létrehozott kulcsokkal

| Paraméter | Formátum | Jegyzetek
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` lecserélhető `user@`

> [!NOTE]
> A Flux által generált nyilvános kulcsot hozzá kell adni a git-szolgáltató felhasználói fiókjához. Ha a kulcsot hozzáadja a tárházhoz a felhasználói fiók helyett, használja az `git@` URL-cím helyett `user@` . További részletekért ugorjon a [konfiguráció alkalmazása egy privát git-tárházból](#apply-configuration-from-a-private-git-repository) című szakaszra.

#### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Privát git-tárház használata SSH-val és felhasználó által biztosított kulcsokkal

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` lecserélhető `user@` |
| `--ssh-private-key` | Base64 kódolású kulcs [PEM formátumban](https://aka.ms/PEMformat) | Kulcs közvetlen megadása |
| `--ssh-private-key-file` | helyi fájl teljes elérési útja | Adja meg a PEM formátumú kulcsot tartalmazó helyi fájl teljes elérési útját

> [!NOTE]
> Adja meg a saját titkos kulcsát közvetlenül vagy egy fájlban. A kulcsnak [PEM formátumúnak](https://aka.ms/PEMformat) kell lennie, és véget kell adni a sortörés (\n) értéknek.  A társított nyilvános kulcsot hozzá kell adni a git-szolgáltató felhasználói fiókjához. Ha a kulcs a felhasználói fiók helyett a tárházba kerül, használja a `git@` következőt: `user@` . További részletekért ugorjon a [konfiguráció alkalmazása egy privát git-tárházból](#apply-configuration-from-a-private-git-repository) című szakaszra.

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Privát git-gazdagép használata SSH-val és felhasználó által biztosított ismert gazdagépekkel

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` lecserélhető `user@` |
| `--ssh-known-hosts` | Base64 kódolású | Az ismert gazdagépek közvetlen tartalmainak megadása |
| `--ssh-known-hosts-file` | helyi fájl teljes elérési útja | Ismert gazdagépek tartalma helyi fájlban |

> [!NOTE]
> Ahhoz, hogy az SSH-kapcsolat létrehozása előtt hitelesíteni lehessen a git-tárházat, a Flux-kezelő megtartja a közös git-gazdagépek listáját az ismert gazdagépek fájljában. Ha nem gyakori git-tárházat vagy saját git-gazdagépet használ, előfordulhat, hogy meg kell adnia a gazdagép kulcsát annak biztosításához, hogy a Flux azonosítani tudja a tárházat. Known_hosts tartalmat közvetlenül vagy fájlba is megadhatja. A saját tartalom megadásakor a fent ismertetett SSH-fő forgatókönyvek egyikével együtt használja a [known_hosts Content Format specifikációkat](https://aka.ms/KnownHostsFormat) .

#### <a name="use-a-private-git-repository-with-https"></a>Privát git-tárház használata HTTPS-sel

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS alapszintű hitelesítéssel |
| `--https-user` | nyers vagy Base64 kódolású | HTTPS-Felhasználónév |
| `--https-key` | nyers vagy Base64 kódolású | HTTPS személyes hozzáférési jogkivonat vagy jelszó

> [!NOTE]
> A HTTPS Helm kiadás privát hitelesítése csak a Helm Operator diagram 1.2.0 + (alapértelmezett) verziójának használata esetén támogatott.
> A HTTPS Helm kiadás privát hitelesítése jelenleg nem támogatott az Azure Kubernetes Services által felügyelt fürtök esetében.
> Ha a Flux segítségével szeretné elérni a git-tárházat a proxyn keresztül, akkor frissítenie kell az Azure arc-ügynököket a proxybeállítások alapján. További információ: [Csatlakozás kimenő proxykiszolgáló használatával](./connect-cluster.md#connect-using-an-outbound-proxy-server).

#### <a name="additional-parameters"></a>További paraméterek

Szabja testre a konfigurációt a következő választható paraméterekkel:

| Paraméter | Leírás |
| ------------- | ------------- |
| `--enable-helm-operator`| Váltson a Helm chart üzemelő példányok támogatásának engedélyezéséhez. |
| `--helm-operator-params` | A Helm operátor diagramjának értékei (ha engedélyezve van). Például: `--set helm.versions=v3`. |
| `--helm-operator-version` | A Helm operátor diagramjának verziója (ha engedélyezve van). Használja a 1.2.0 + verziót. Alapértelmezett: "1.2.0". |
| `--operator-namespace` | Az operátori névtér neve. Alapértelmezett: "default". Max: 23 karakter. |
| `--operator-params` | A kezelőhöz tartozó paraméterek. Egy idézőjelek között kell megadni. Például: ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>Támogatott beállítások  `--operator-params` :

| Beállítás | Leírás |
| ------------- | ------------- |
| `--git-branch`  | A git-tárház Kubernetes-jegyzékekhez használt ága. Az alapértelmezett érték a "Master". Az újabb Tárházak neve root ág `main` , ebben az esetben be kell állítania a következőt: `--git-branch=main` . |
| `--git-path`  | Relatív elérési út a git-tárházban a Flux számára a Kubernetes-jegyzékek kereséséhez. |
| `--git-readonly` | A git-tárház csak olvasható lesz; A Flux nem próbál meg írni. |
| `--manifest-generation`  | Ha engedélyezve van, a Flux a. Flux. YAML és a Kustomize vagy más manifest-generátorok futtatására fog keresni. |
| `--git-poll-interval`  | Az az időszak, amikor a git-tárházat új véglegesíteni kívánja lekérdezni. Az alapértelmezett érték `5m` (5 perc). |
| `--sync-garbage-collection`  | Ha engedélyezve van, a Flux törli a létrehozott erőforrásokat, de már nem jelennek meg a git-ben. |
| `--git-label`  | A szinkronizálási folyamat nyomon követésére szolgáló címke. A git-ág címkézésére szolgál.  Az alapértelmezett szint a `flux-sync`. |
| `--git-user`  | A git-véglegesítő felhasználóneve. |
| `--git-email`  | A git-végrehajtáshoz használandó e-mail-cím. 

Ha nem szeretné, hogy a Flux a tárházba írjon `--git-user` `--git-email` , vagy nincsenek beállítva, akkor a rendszer `--git-readonly` automatikusan beállítja.

További információt a [Flux dokumentációjában](https://aka.ms/FluxcdReadme)talál.

> [!TIP]
> A `sourceControlConfiguration` Azure Portal az Azure arc-kompatibilis Kubernetes-erőforrás **GitOps** lapján lehet létrehozni.

## <a name="validate-the-sourcecontrolconfiguration"></a>A sourceControlConfiguration ellenőrzése

Az Azure CLI használatával ellenőrizheti, hogy a `sourceControlConfiguration` sikeresen létrejött-e.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

A `sourceControlConfiguration` rendszer frissíti az erőforrást a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal.

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

Ha a létrehozása `sourceControlConfiguration` vagy frissítése megtörtént, néhány dolog a motorháztető alatt történik:

1. Az Azure arc `config-agent` figyeli az új vagy frissített konfigurációk () Azure Resource managerét `Microsoft.KubernetesConfiguration/sourceControlConfigurations` , és észreveszi az új `Pending` konfigurációt.
1. A `config-agent` beolvassa a konfigurációs tulajdonságokat, és létrehozza a cél névterét.
1. Az Azure arc `controller-manager` előkészít egy Kubernetes szolgáltatásfiókot a megfelelő engedélyekkel ( `cluster` vagy `namespace` hatókörrel), majd üzembe helyezi a példányát `flux` .
1. Ha a Flux által generált kulcsokkal rendelkező SSH lehetőséget használja, `flux` létrehoz egy SSH-kulcsot, és naplózza a nyilvános kulcsot.
1. A `config-agent` jelentések állapota vissza az `sourceControlConfiguration` Azure-beli erőforrásra.

A kiépítési folyamat során a `sourceControlConfiguration` átkerül néhány állapotba. A folyamat figyelése a `az k8sconfiguration show ...` fenti paranccsal:

| Szakasz módosítása | Description |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | A kezdeti és a folyamatban lévő állapotot jelöli. |
| `complianceStatus` -> `Installed`  | `config-agent` sikerült konfigurálni a fürtöt, és hiba nélkül is üzembe helyezhető `flux` . |
| `complianceStatus` -> `Failed` | `config-agent` Hiba történt az üzembe helyezés során `flux` , a részleteknek elérhetőnek kell lenniük a `complianceStatus.message` Válasz törzsében. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Konfiguráció alkalmazása privát git-tárházból

Ha privát git-tárházat használ, konfigurálnia kell az SSH nyilvános kulcsát a tárházban. Az SSH nyilvános kulcs lesz a Flux által generált vagy az Ön által megadott egyik. A nyilvános kulcsot az adott git-adattáron vagy a tárházhoz hozzáféréssel rendelkező git-felhasználón is konfigurálhatja. 

### <a name="get-your-own-public-key"></a>Saját nyilvános kulcs beszerzése

Ha létrehozta a saját SSH-kulcsait, akkor már rendelkezik a privát és a nyilvános kulcsokkal.

#### <a name="get-the-public-key-using-azure-cli"></a>Nyilvános kulcs beszerzése az Azure CLI használatával 

A következők akkor hasznosak, ha a Flux létrehozza a kulcsokat.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>A Azure Portal nyilvános kulcsának beolvasása

A következők akkor hasznosak, ha a Flux létrehozza a kulcsokat.

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

**Kimeneti**

```console
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

Az `flux` operátor a következő módon lett telepítve a `cluster-config` névtérre `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Kimeneti**

```console
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

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Törölje `sourceControlConfiguration` Az Azure CLI vagy Azure Portal használatával.  A DELETE parancs elindítása után a `sourceControlConfiguration` rendszer azonnal törli az erőforrást az Azure-ban. A fürthöz társított objektumok teljes törlése 10 percen belül megtörténik. Ha az `sourceControlConfiguration` eltávolítása sikertelen állapotban van, a társított objektumok teljes törlése akár egy órát is igénybe vehet.

> [!NOTE]
> Miután `sourceControlConfiguration` `namespace` létrehozta a hatókört, `edit` a névtér szerepkör-kötéssel rendelkező felhasználók üzembe helyezhetik a munkaterheléseket ezen a névtéren. Ha a `sourceControlConfiguration` névtér hatóköre törölve lesz, a névtér érintetlen marad, és a rendszer nem törli a többi számítási feladat megszakítása érdekében. Ha szükséges, manuálisan törölheti a névteret a használatával `kubectl` .  
> A rendszer nem törli a fürt azon módosításait, amelyek a nyomon követett git-tárházból való üzembe helyezések eredményeképpen történtek, a `sourceControlConfiguration` törlése után.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Következő lépések

- [A Helm használata a verziókövetés konfigurációjával](./use-gitops-with-helm.md)
- [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
