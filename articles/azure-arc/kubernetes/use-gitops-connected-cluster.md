---
title: Konfigurációk üzembe helyezése a GitOps használatával Arc-kompatibilis Kubernetes-fürtön (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure arc-kompatibilis Kubernetes-fürt (előzetes verzió) konfigurálása a GitOps használatával
keywords: GitOps, Kubernetes, K8s, Azure, arc, Azure Kubernetes szolgáltatás, AK, tárolók
ms.openlocfilehash: 72dc42fffb3653de81477fa504c11b9b0328d2eb
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988705"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Konfigurációk üzembe helyezése a GitOps használatával Arc-kompatibilis Kubernetes-fürtön (előzetes verzió)

A GitOps, ahogy az a Kubernetes vonatkozik, az a gyakorlat, hogy deklarálja a Kubernetes-konfiguráció (központi telepítések, névterek stb.) kívánt állapotát a git-tárházban, majd egy operátor használatával lekérdezi és lekéréses módon telepítette ezeket a konfigurációkat a fürtön. Ez a dokumentum az Azure arc-kompatibilis Kubernetes-fürtökön futó ilyen munkafolyamatok beállítását ismerteti.

A fürt és a git-tárház közötti kapcsolat a Azure Resource Manager `Microsoft.KubernetesConfiguration/sourceControlConfigurations` bővítmény-erőforrásként jön létre. Az `sourceControlConfiguration` Erőforrás-tulajdonságok határozzák meg, hogy a Kubernetes-erőforrások hol és hogyan folynak a git és a fürt között. A `sourceControlConfiguration` rendszer az adatok titkosságának biztosítása érdekében a Azure Cosmos db adatbázisban titkosítva tárolja az adattárolást.

A `config-agent` fürtben való futtatás az `sourceControlConfiguration` Azure arc-kompatibilis Kubernetes-erőforrás új vagy frissített bővítményi erőforrásainak figyelésére szolgál, így a Flux-kezelő üzembe helyezésével megtekintheti a git-tárházat `sourceControlConfiguration` , és alkalmazhatja azokat `sourceControlConfiguration` . Több erőforrás is létrehozható `sourceControlConfiguration` ugyanazon az Azure arc-kompatibilis Kubernetes-fürtön a több-bérlős szolgáltatás eléréséhez. Létrehozhat `sourceControlConfiguration` egy másik `namespace` hatókört is, amellyel a központi telepítéseket a megfelelő névtereken belül korlátozhatja.

A git-tárház tartalmazhat olyan YAML-jegyzékeket, amelyek bármilyen érvényes Kubernetes-erőforrást leírnak, beleértve a névtereket, a ConfigMaps, a központi telepítéseket, a DaemonSets stb.  Az alkalmazások üzembe helyezéséhez is tartozhat Helm-diagramok. A gyakori forgatókönyvek közé tartozik a szervezet alapkonfigurációjának meghatározása, amely közös Azure-szerepköröket és-kötéseket, figyelési vagy naplózási ügynököket, vagy a fürtre kiterjedő szolgáltatásokat is magában foglalhat.

Ugyanez a minta használható a fürtök nagyobb gyűjteményének kezelésére, amely heterogén környezetekben is üzembe helyezhető. Előfordulhat például, hogy rendelkezik egy adattárral, amely meghatározza a szervezet alapkonfigurációját, és egyszerre több tízezer Kubernetes-fürtöt alkalmaz. Az [Azure Policy](use-azure-policy.md) a `sourceControlConfiguration` hatókör (előfizetés vagy erőforráscsoport) alá tartozó összes Azure arc-Kubernetes erőforrás-készlettel automatizálhatja a-t egy adott paraméterekkel.

Ez az első lépéseket ismertető útmutató végigvezeti a konfigurációk fürt-rendszergazdai hatókörrel való alkalmazásának lépésein.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy rendelkezik egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürttel. Ha csatlakoztatott fürtre van szüksége, tekintse meg a [fürt csatlakoztatása](./connect-cluster.md)rövid útmutató című témakört.

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Az ebben a dokumentumban használt [példában szereplő adattár](https://github.com/Azure/arc-k8s-demo) egy olyan fürt munkatársai köré épül fel, akik néhány névteret szeretnének kiépíteni, közös munkaterhelést telepíteni, és a csoportra jellemző konfigurációt biztosítanak. Az adattár használata a következő erőforrásokat hozza létre a fürtön:

**Névterek:** `cluster-config` , `team-a` , `team-b` 
 **üzembe helyezés:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

A `config-agent` lekérdezi az Azure-t az új vagy frissített `sourceControlConfiguration` 30 másodpercenként, ami `config-agent` egy új vagy frissített konfiguráció kiválasztásához szükséges maximális idő.
Ha privát tárházat társít a `sourceControlConfiguration` alkalmazáshoz, győződjön meg arról, hogy elvégezte a [konfiguráció alkalmazása privát git-tárházból](#apply-configuration-from-a-private-git-repository)című témakör lépéseit is.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az Azure CLI bővítmény használatával `k8sconfiguration` csatlakoztathat egy csatlakoztatott fürtöt a [példa git-tárházhoz](https://github.com/Azure/arc-k8s-demo). Ennek a konfigurációnak a nevét kell megadnia `cluster-config` , arra utasítja az ügynököt, hogy telepítse az operátort a `cluster-config` névtérben, és adja meg az operátor `cluster-admin` engedélyeit.

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

#### <a name="use-a-public-git-repo"></a>Nyilvános git-tárház használata

| Paraméter | Formátum |
| ------------- | ------------- |
| – adattár – URL | http [s]://Server/repo [. git] vagy git://Server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Privát git-tárház használata SSH-val és fluxus által létrehozott kulcsokkal

| Paraméter | Formátum | Jegyzetek
| ------------- | ------------- | ------------- |
| – adattár – URL | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` helyettesítheti a következőt: `user@`

> [!NOTE]
> A Flux által generált nyilvános kulcsot hozzá kell adni a git-szolgáltató felhasználói fiókjához. Ha a kulcsot hozzáadja a tárházhoz a felhasználói fiók > helyett, használja az `git@` URL-cím helyett `user@` . [További részletek megtekintése](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Privát git-tárház használata SSH-val és felhasználó által biztosított kulcsokkal

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| – adattár – URL  | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` helyettesítheti a következőt: `user@` |
| --SSH-Private-Key | Base64 kódolású kulcs [PEM formátumban](https://aka.ms/PEMformat) | Kulcs közvetlen megadása |
| --SSH-Private-Key-file | helyi fájl teljes elérési útja | Adja meg a PEM formátumú kulcsot tartalmazó helyi fájl teljes elérési útját

> [!NOTE]
> Adja meg a saját titkos kulcsát közvetlenül vagy egy fájlban. A kulcsnak [PEM formátumúnak](https://aka.ms/PEMformat) kell lennie, és véget kell adni a sortörés (\n) értéknek.  A társított nyilvános kulcsot hozzá kell adni a git-szolgáltató felhasználói fiókjához. Ha a kulcsot a felhasználói fiók helyett a tárházba adja, használja a `git@` következőt: `user@` . [További részletek megtekintése](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Privát git-gazdagép használata SSH-val és felhasználó által biztosított ismert gazdagépekkel

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| – adattár – URL  | ssh://user@server/repo[. git] vagy user@server:repo [. git] | `git@` helyettesítheti a következőt: `user@` |
| --SSH – ismert gazdagépek | Base64 kódolású | az ismert gazdagépek tartalma közvetlenül biztosítva |
| --SSH-ismert-gazdagépek-fájl | helyi fájl teljes elérési útja | az ismert gazdagépek tartalma helyi fájlban van megadva

> [!NOTE]
> A Flux-kezelő a git-adattárat az SSH-kapcsolat létrehozása előtt az ismert gazdagépek fájljában tárolja. Ha nem gyakori git-tárházat vagy saját git-gazdagépet használ, előfordulhat, hogy meg kell adnia a gazdagép kulcsát annak biztosításához, hogy a Flux azonosítani tudja a tárházat. Közvetlenül vagy fájlban is megadhatja az ismert gazdagépek tartalmát. [Tekintse meg az ismert gazdagépek tartalmi formátumának specifikációját](https://aka.ms/KnownHostsFormat).
> Ezt a fentiekben ismertetett SSH-kulcsú forgatókönyvek egyikével együtt is használhatja.

#### <a name="use-a-private-git-repo-with-https"></a>Privát git-tárház használata HTTPS-sel

| Paraméter | Formátum | Jegyzetek |
| ------------- | ------------- | ------------- |
| – adattár – URL | https://server/repo[. git] | HTTPS alapszintű hitelesítéssel |
| --https-User | nyers vagy Base64 kódolású | HTTPS-Felhasználónév |
| --https-Key | nyers vagy Base64 kódolású | HTTPS személyes hozzáférési jogkivonat vagy jelszó

> [!NOTE]
> A HTTPS Helm kiadás privát hitelesítése csak a Helm operátor diagramjának verziójával támogatott: >= 1.2.0.  Alapértelmezés szerint a 1.2.0 verziója van használatban.
> A HTTPS Helm kiadás privát hitelesítése jelenleg nem támogatott az Azure Kubernetes Services által felügyelt fürtök esetében.
> Ha szüksége van a flow-ra a git-tárháznak a proxyn keresztüli eléréséhez, akkor frissítenie kell az Azure arc-ügynököket a proxy beállításaival. [További információ](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>További paraméterek

A konfiguráció testreszabásához több paramétert is használhat:

`--enable-helm-operator` : *Opcionális* kapcsoló a Helm chart központi telepítések támogatásának engedélyezéséhez.

`--helm-operator-params` : Nem *kötelező* diagram-értékek a Helm-kezelőhöz (ha engedélyezve van).  Például: "--set Helm. Versions = v3".

`--helm-operator-version` : Nem *kötelező* diagram-verzió a Helm-kezelőhöz (ha engedélyezve van). A "1.2.0" vagy a nagyobb érték használata. Alapértelmezett: "1.2.0".

`--operator-namespace` : Az operátori névtér neve nem *kötelező* . Alapértelmezett: "default". Max. 23 karakter.

`--operator-params` : Nem *kötelező* paraméterek a kezelőhöz. Egy idézőjelek között kell megadni. Például: ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```

A-operátor-params támogatott beállításai

| Beállítás | Leírás |
| ------------- | ------------- |
| --git-ág  | A git-tárház Kubernetes-jegyzékekhez használt ága. Az alapértelmezett érték a "Master". Az újabb Tárházak "Main" nevű főágra rendelkeznek, ebben az esetben be kell állítania a--git-ág = Main nevet. |
| – git – elérési út  | Relatív elérési út a git-tárházon belül a Flux számára a Kubernetes-jegyzékek megkereséséhez. |
| --git-ReadOnly | A git-adattárat csak olvashatónak tekinti a rendszer. A Flux nem próbál meg írni. |
| --manifest-Generation  | Ha engedélyezve van, a Flux a. Flux. YAML és a Kustomize vagy más manifest-generátorok futtatására fog keresni. |
| --git-Poll-intervallum  | Az az időszak, amikor a git-tárházat új véglegesíteni kívánja lekérdezni. Az alapértelmezett érték az "5m" (5 perc). |
| --Sync-Garbage-Collection  | Ha engedélyezve van, a Flux törli a létrehozott erőforrásokat, de már nem jelennek meg a git-ben. |
| --git-Label  | A szinkronizálási folyamat nyomon követésére szolgáló címke, amely a git-ág címkézésére szolgál.  Az alapértelmezett érték a "Flux-Sync". |
| --git-User  | A git-véglegesítő felhasználóneve. |
| – git-e-mail  | A git-végrehajtáshoz használandó e-mail-cím. |

* Ha a "--git-user" vagy a "--git-email" nincs beállítva (ami azt jelenti, hogy nem szeretné, hogy a Flux írni a tárházba), akkor a--git-ReadOnly automatikusan be lesz állítva (ha még nem állította be).

További információ: Flux- [dokumentáció](https://aka.ms/FluxcdReadme).

> [!TIP]
> SourceControlConfiguration hozhat létre a Azure Portal az Azure arc-kompatibilis Kubernetes erőforrás **GitOps** lapján.

## <a name="validate-the-sourcecontrolconfiguration"></a>A sourceControlConfiguration ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a `sourceControlConfiguration` sikeresen létrejött-e.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Vegye figyelembe, hogy az `sourceControlConfiguration` erőforrás a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal frissül.

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

1. Az Azure arc `config-agent` figyelési Azure Resource Manager új vagy frissített konfigurációkhoz ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ), és az új `Pending` konfigurációt észleli.
1. A `config-agent` beolvassa a konfigurációs tulajdonságokat, és létrehozza a cél névterét.
1. Az Azure arc `controller-manager` előkészít egy Kubernetes szolgáltatásfiókot a megfelelő engedélyekkel ( `cluster` vagy `namespace` hatókörrel), majd üzembe helyezi a példányát `flux` .
1. Ha a Flux által generált kulcsokkal rendelkező SSH lehetőséget használja, `flux` létrehoz egy SSH-kulcsot, és naplózza a nyilvános kulcsot.
1. A `config-agent` jelentések állapota vissza az `sourceControlConfiguration` Azure-beli erőforrásra.

A kiépítési folyamat során a `sourceControlConfiguration` átkerül néhány állapotba. A folyamat figyelése a `az k8sconfiguration show ...` fenti paranccsal:

1. `complianceStatus` -> `Pending`: a kezdeti és a folyamatban lévő állapotot jelöli.
1. `complianceStatus` -> `Installed`: sikerült `config-agent` konfigurálni a fürtöt, és `flux` hiba nélkül üzembe helyezheti a telepítést
1. `complianceStatus` -> `Failed`: `config-agent` hiba történt az üzembe helyezés során `flux` , a részleteknek elérhetőnek kell lenniük a `complianceStatus.message` Válasz törzsében.

## <a name="apply-configuration-from-a-private-git-repository"></a>Konfiguráció alkalmazása privát git-tárházból

Ha privát git-tárházat használ, akkor konfigurálnia kell az SSH nyilvános kulcsát a tárházban. A nyilvános kulcsot a megadott git-tárházon vagy a tárházhoz hozzáféréssel rendelkező git-felhasználón is konfigurálhatja. Az SSH nyilvános kulcs lesz az Ön által megadott vagy a Flux által generált egyik.

**Saját nyilvános kulcs beszerzése**

Ha létrehozta a saját SSH-kulcsait, akkor már rendelkezik a privát és a nyilvános kulcsokkal.

**Nyilvános kulcs beszerzése az Azure CLI használatával (hasznos, ha a Flux létrehozza a kulcsokat)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**A Azure Portal nyilvános kulcsának lekérése (hasznos, ha a Flux hozza létre a kulcsokat)**

1. A Azure Portal navigáljon a csatlakoztatott fürt erőforrásához.
2. Az erőforrás lapon válassza a "GitOps" lehetőséget, és tekintse meg a fürthöz tartozó konfigurációk listáját.
3. Válassza ki a privát git-tárházat használó konfigurációt.
4. A megnyíló helyi ablakban az ablak alján másolja az **adattár nyilvános kulcsát**.

Ha GitHubot használ, használja a következő két lehetőség egyikét:

**1. lehetőség: a nyilvános kulcs hozzáadása a felhasználói fiókhoz (a fiókban lévő összes repóra vonatkozik)**

1. Nyissa meg a GitHubot, kattintson a lap jobb felső sarkában található profil ikonjára.
2. Kattintson a **Beállítások** elemre.
3. Kattintson az **SSH és a GPG kulcsok** lehetőségre
4. Kattintson az **új SSH-kulcs** lehetőségre.
5. Adja meg a címet
6. A nyilvános kulcs beillesztése (a környező idézőjelek mínusz)
7. Kattintson az **SSH-kulcs hozzáadása** lehetőségre.

**2. lehetőség: a nyilvános kulcs hozzáadása üzembe helyezési kulcsként a git-tárházhoz (csak erre a tárházra vonatkozik)**

1. Nyissa meg a GitHubot, navigáljon a tárházhoz, és válassza a beállítások, majd a **kulcsok telepítése** **lehetőséget**.
2. Kattintson az **üzembe helyezési kulcs hozzáadása** lehetőségre.
3. Adja meg a címet
4. **Írási hozzáférés engedélyezése**
5. A nyilvános kulcs beillesztése (a környező idézőjelek mínusz)
6. Kattintson a **Kulcs hozzáadása** lehetőségre.

**Ha Azure DevOps-tárházat használ, adja hozzá a kulcsot az SSH-kulcsokhoz**

1. A jobb felső sarokban lévő **felhasználói beállítások** alatt (a profil képe mellett) kattintson a **nyilvános ssh-kulcsok** elemre.
1. Válassza az **+ új kulcs** lehetőséget
1. Adja meg a nevet
1. A nyilvános kulcs beillesztése a környező idézőjelek nélkül
1. Kattintson a **Hozzáadás** gombra

## <a name="validate-the-kubernetes-configuration"></a>A Kubernetes konfigurációjának ellenőrzése

Miután `config-agent` telepítette a `flux` példányt, a git-tárházban tárolt erőforrásoknak futniuk kell a fürtön. Ellenőrizze, hogy a névterek, a központi telepítések és az erőforrások létrejöttek-e:

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

A konfigurációs adattár részeként telepített egyéb erőforrásokat is megismerheti:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Törölje `sourceControlConfiguration` Az Azure CLI vagy Azure Portal használatával.  Miután elindította a DELETE parancsot, a `sourceControlConfiguration` rendszer azonnal törli az erőforrást az Azure-ban, és a fürthöz kapcsolódó objektumok teljes törlése 10 percen belül megtörténik.  Ha a `sourceControlConfiguration` törlése sikertelen állapotban van, a társított objektumok teljes törlése akár egy órát is igénybe vehet.

> [!NOTE]
> Miután létrehozta a sourceControlConfiguration a névtér hatókörével, lehetséges, hogy a `edit` névterek szerepkör-kötéssel rendelkező felhasználók üzembe helyezik a munkaterheléseket ezen a névtéren. Ha a `sourceControlConfiguration` névtér hatóköre törölve lesz, a névtér érintetlen marad, és a rendszer nem törli a többi számítási feladat megszakítása érdekében.  Ha szükséges, manuálisan törölheti a névteret a kubectl használatával.
> A rendszer nem törli a fürt azon módosításait, amelyek a nyomon követett git-tárházból való üzembe helyezések eredményeként lettek törölve `sourceControlConfiguration` .

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