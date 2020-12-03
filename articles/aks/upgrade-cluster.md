---
title: Azure Kubernetes Service- (AKS-) fürt frissítése
description: Ismerje meg, hogyan frissíthet egy Azure Kubernetes-szolgáltatási (ak-) fürtöt a legújabb funkciók és biztonsági frissítések beszerzéséhez.
services: container-service
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 30ad80727c238ae7e415039adf3e4eb75dbbc1b5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531343"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service- (AKS-) fürt frissítése

Egy AK-alapú fürt életciklusának részeként gyakran frissítenie kell a legújabb Kubernetes-verzióra. Fontos, hogy alkalmazza a legújabb Kubernetes biztonsági kiadásokat, vagy frissítsen a legújabb funkciók beszerzéséhez. Ez a cikk bemutatja, hogyan frissítheti a fő összetevőket vagy egyetlen, alapértelmezett Node-készletet egy AK-fürtben.

Több csomópontot vagy Windows Server-csomópontot használó AK-fürtök esetében lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!WARNING]
> Az AK-fürtök frissítése kiváltja a csomópontjait, és kiüríti a csomópontokat. Ha alacsony számítási kvóta áll rendelkezésre, előfordulhat, hogy a frissítés meghiúsul. További információért lásd: [kvóták növelése](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Elérhető AK-fürt frissítésének keresése

A fürthöz elérhető Kubernetes-kiadások vizsgálatához használja az az [AK Get-upgrade][az-aks-get-upgrades] parancsot. Az alábbi példa a *myResourceGroup* nevű erőforráscsoport *myAKSCluster* nevű fürtjében elérhető frissítéseit ellenőrzi:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ha egy támogatott AK-fürtöt frissít, a Kubernetes alverzióit nem lehet kihagyni. Például a *1.12. x*  ->  *1.13. x* vagy *1.13. x*  ->  *1.14. x* közötti frissítések engedélyezettek, a *1.12. x*  ->  *1.14. x* azonban nem.
>
> A frissítéshez *1.12. x*  ->  *1,14. x*-ről először a *1.12. x* 1.13. x verzióról kell frissíteni,  ->  *1.13.x* majd az *1.13. x*  ->  *1.14. x* verziójáról kell frissíteni.
>
> Több verzió kihagyása csak akkor végezhető el, ha nem támogatott verzióról frissít vissza egy támogatott verzióra. Például nem támogatott *1.10. x* – > egy támogatott *1.15. x-et* lehet befejezni.

A következő példa kimenete azt mutatja, hogy a fürt frissíthető a *1.13.9* és a *1.13.10* verzióra:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Ha nincs elérhető frissítés, a következőket kapja:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Csomópont-túlfeszültség-frissítés testreszabása

> [!Important]
> A csomópont-túllépések előfizetési kvótát igényelnek az egyes frissítési műveletekhez szükséges maximális túlfeszültségek számához. Például egy olyan fürt, amely 5 csomópontos készlettel rendelkezik, amelyek mindegyike 4 csomóponttal rendelkezik, összesen 20 csomóponttal rendelkezik. Ha az egyes csomópont-készletek maximális túllépésének értéke 50%, a frissítés befejezéséhez további számítási és IP-kvóta (2 csomópont * 5 készlet) szükséges.
>
> Ha az Azure CNI-t használja, ellenőrizze, hogy vannak-e elérhető IP-címek az alhálózatban, valamint az [Azure CNI IP-követelményeinek kielégítéséhez](configure-azure-cni.md).

Alapértelmezés szerint az AK egy további csomóponttal konfigurálja a verziófrissítést. Az egyik alapértelmezett értéke a maximális túllépési beállítás lehetővé teszi az AK számára a munkaterhelés megszakadásának minimalizálását egy további csomópont létrehozásával, mielőtt a meglévő alkalmazások kihelyezik a régebbi verziójú csomópontokat. A maximális túlfeszültség-érték testreszabható, és a frissítési sebesség és a frissítés megszakadása közötti kompromisszum lehetővé tételéhez. A maximális túlfeszültség-érték növelésével a verziófrissítési folyamat gyorsabban elvégezhető, de a maximális túllépés nagy értékének beállítása a frissítési folyamat során fennakadást okozhat. 

Például a 100%-os maximális túlfeszültség-érték biztosítja a lehető leggyorsabb frissítési folyamatot (a csomópontok számának megkettőzése), de a csomópont-készlet összes csomópontjának egyidejű kiürítését is okozhatja. Érdemes lehet magasabb értéket használni, például tesztelési környezetekhez. Üzemi csomópont-készletek esetén a 33%-os max_surge beállítást javasoljuk.

Az AK egész értékeket és százalékos értéket is elfogad a maximális túlfeszültséghez. Egy egész szám, például az "5", öt további csomópontot jelöl a megugráshoz. A (z) "50%" érték azt jelzi, hogy a készletben lévő csomópontok fele a felére mutat. A maximális túlterhelési százalék értéke lehet legalább 1%, legfeljebb 100%. A rendszer a legközelebbi csomópontok számára kerekíti a százalékos értéket. Ha a maximális túlfeszültség értéke alacsonyabb, mint az aktuális csomópontok száma a frissítéskor, a rendszer az aktuális csomópontok számát használja a maximális túlfeszültség értékhez.

A frissítés során a maximális túlfeszültség értéke lehet legalább 1, a maximális érték pedig a csomópont csomópontjainak számával egyenlő. Megadhat nagyobb értékeket, de a maximális túlfeszültséghez használt csomópontok maximális száma nem haladhatja meg a készletben lévő csomópontok számát a frissítéskor.

Amíg a CLI-verzió 2.16.0 +-ra van szüksége, az *AK-előnézet CLI-* bővítménynek kell megadnia a maximális túlfeszültséget. Használja az az [Extension Add][az-extension-add] parancsot, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Egy csomópont-készleten a maximális túlfeszültség-beállítás állandó.  A későbbi Kubernetes-frissítések vagy a csomópont verziófrissítése ezt a beállítást fogja használni. A csomópont-készletek maximális túlfeszültség-értéke bármikor módosítható. A termelési csomópontok készletei esetében a 33%-os maximális túllépési beállítást javasoljuk.

A következő parancsokkal állíthatja be az új vagy a meglévő csomópont-készletek maximális túlfeszültségi értékeit.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AK-fürt elérhető verzióinak listájával frissítse a frissítést az az [AK upgrade][az-aks-upgrade] paranccsal. A frissítési folyamat során az AK egy új puffer-csomópontot (vagy a [maximálisan](#customize-node-surge-upgrade)megjelenő legtöbb csomópontot) ad hozzá a megadott Kubernetes-verziót futtató fürthöz. Ezután a rendszer a régi csomópontok egyikét [kiüríti, és][kubernetes-drain] leállítja az alkalmazások futtatásának megszakadását (ha a maximális túlfeszültséget használja, és a megadott puffer-csomópontok számával egyszerre több csomópontot is [kiüríti][kubernetes-drain] ). A régi csomópont teljes kiürítése után a rendszer visszaállítja az új verzió megadását, és a következő csomóponthoz tartozó puffer csomópont lesz a frissítésre. Ez a folyamat addig ismétlődik, amíg a fürt összes csomópontja nem frissült. A folyamat végén a rendszer törli az utolsó lecsepegtetett csomópontot, és megtartja a meglévő ügynök-csomópontok számát.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

A fürt frissítése néhány percet vesz igénybe, attól függően, hogy hány csomóponttal rendelkezik.

> [!NOTE]
> A fürt frissítésének befejezéséhez teljes megengedett idő szükséges. Ezt az időt a termékének kiszámításával számítjuk ki `10 minutes * total number of nodes in the cluster` . Egy 20 csomópontos fürtben például a frissítési műveleteknek 200 percen belül sikeresnek kell lenniük, vagy az AK-ban sikertelen lesz a művelet, hogy elkerülje a nem helyreállítható fürt állapotát. A frissítési hiba helyreállításához próbálkozzon újra a frissítési művelettel az időkorlát lejárta után.

A frissítés sikerességének ellenőrzéséhez használja az az [AK show][az-aks-show] parancsot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A következő példa kimenete azt mutatja, hogy a fürt most már a *1.13.10*-t futtatja:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel-preview"></a>Az automatikus frissítési csatorna beállítása (előzetes verzió)

A fürt manuális frissítése mellett beállíthat egy automatikus frissítési csatornát a fürtön. A következő frissítési csatornák érhetők el:

* *nincs*, ami letiltja az automatikus frissítést, és a fürt a Kubernetes jelenlegi verziójában tartja a fürtöt. Ez az alapértelmezett érték, és akkor használható, ha nincs megadva beállítás.
* a *javítást*, amely automatikusan frissíti a fürtöt a legújabb támogatott javítócsomag-verzióra, amikor elérhetővé válik, miközben az alverzió megtartja ugyanezt. Ha például egy fürt *1.17.7* verziót futtat, és a *1.17.9*, a *1.18.4*, a *1.18.6* és a *1.19.1* verzió érhető el, a fürt a *1.17.9*-re frissül.
* *stabil*, amely automatikusan frissíti a fürtöt az *n-1* alverzióban található legújabb támogatott patch kiadásra, ahol *n* a legújabb támogatott alverzió. Ha például egy fürt *1.17.7* verziót futtat, és a *1.17.9*, a *1.18.4*, a *1.18.6* és a *1.19.1* verzió érhető el, a fürt a *1.18.6*-re frissül.
* *gyors*, amely automatikusan frissíti a fürtöt a legújabb támogatott alverzióban támogatott patch kiadásra. Azokban az esetekben, amikor a fürt a Kubernetes olyan verziójával rendelkezik, amely *n-2* alverzión van, ahol *n* a legújabb támogatott alverzió, a fürt először a legújabb támogatott javítási verziót frissíti az *n-1* alverzióban. Ha például egy fürt a *1.17.7* verziót és a *1.17.9*, a *1.18.4*, a *1.18.6* és a *1.19.1* verzióját futtatja, akkor a fürt először a *1.18.6*-re frissül, majd a *1.19.1*-re frissül.

> [!NOTE]
> A fürt automatikusan frissíti a Kubernetes verziójának frissítéseit, és nem frissíti az előzetes verziókat.

A fürt automatikus frissítése ugyanazokat a lépéseket követi, mint a fürt manuális frissítése. További részletek: [AK-fürtök frissítése][upgrade-cluster].

A fürt automatikus frissítése az AK-fürtökhöz előzetes verziójú funkció.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Regisztrálja a `AutoUpgradePreview` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Az az [Extension Add][az-extension-add] paranccsal telepítse az *AK-előnézet* bővítményt, majd keresse meg a rendelkezésre álló frissítéseket az az [Extension Update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

Ha a fürt létrehozásakor az automatikus frissítési csatornát szeretné beállítani, használja az *automatikus frissítési csatorna* paramétert, az alábbi példához hasonlóan.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Az automatikus frissítési csatorna meglévő fürtön való beállításához frissítse az *automatikus frissítési csatorna* paramétert, az alábbi példához hasonlóan.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, hogyan frissíthet egy meglévő AK-fürtöt. Az AK-fürtök üzembe helyezésével és kezelésével kapcsolatos további információkért tekintse meg az oktatóanyagokat.

> [!div class="nextstepaction"]
> [AK-oktatóanyagok][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster