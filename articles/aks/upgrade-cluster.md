---
title: Azure Kubernetes Service- (AKS-) fürt frissítése
description: Megtudhatja, hogyan frissítheti a Azure Kubernetes Service -fürtöt a legújabb funkciók és biztonsági frissítések lekért érdekében.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779614"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service- (AKS-) fürt frissítése

Az AKS-fürt életciklusának egy része magában foglalja a legújabb Kubernetes-verzióra való rendszeres frissítéseket. Fontos, hogy a legújabb biztonsági kiadásokat alkalmazza, vagy frissítsen a legújabb funkciókhoz. Ez a cikk bemutatja, hogyan frissítheti egy AKS-fürt fő összetevőit vagy egyetlen alapértelmezett csomópontkészletét.

A több csomópontkészletet vagy Windows Server-csomópontot felhasználó AKS-fürtök esetén [lásd: Csomópontkészlet frissítése az AKS-ban.][nodepool-upgrade]

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.65-ös vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!WARNING]
> Az AKS-fürt frissítése elindítja a cordont, és kiüríti a csomópontokat. Ha alacsony számítási kvótája van, a frissítés meghiúsulhat. További információ: kvóták [növelése](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Az elérhető AKS-fürtfrissítések ellenőrzése

A fürthöz elérhető Kubernetes-kiadások ellenőrzéséhez használja az [az aks get-upgrades][az-aks-get-upgrades] parancsot. Az alábbi példa a *myResourceGroup* *myAKSCluster* szolgáltatásának elérhető frissítését ellenőrzi:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Támogatott AKS-fürt frissítésekkor a Kubernetes alverziói nem hagyhatóak ki. Az összes frissítést sorrendben kell végrehajtani főverziószám szerint. Például az *1.14.x*  ->  *1.15.x* vagy *az 1.15.x*  ->  *1.16.x* közötti frissítés engedélyezett, az *1.14.x*  ->  *1.16.x* verzió azonban nem engedélyezett. 
> > Több verzió kihagyása csak akkor használhatja, ha nem támogatott verzióról támogatott verzióra _frissít._  Például frissíthet egy nem támogatott *1.10.x* --> támogatott *1.15.x* verzióról.

Az alábbi példakimenet azt mutatja, hogy a fürt frissíthető az *1.19.1-es* és *1.19.3-as verzióra:*

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Ha nem érhető el frissítés, a következő üzenet jelenik meg:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Csomópontok túlcsomópont-frissítésének testreszabása

> [!Important]
> A csomópont-túlcsomópont-túlcsomóponthoz előfizetési kvótára van szükség a kért maximális túlcsomópontszámhoz az egyes frissítési műveletekhez. Egy 5 csomópontkészletet számláló, 4 csomópontot számláló fürt például összesen 20 csomóponttal rendelkezik. Ha az egyes csomópontkészletek maximális hirtelen 50%-os hirtelen ingadozási értékkel bírnak, a frissítés befejezéséhez további 10 csomópontra (2 csomópont * 5 készletre) vonatkozó számítási és IP-kvóta szükséges.
>
> Ha a Azure CNI használ, ellenőrizze, hogy vannak-e elérhető IP-címek az [alhálózaton,](configure-azure-cni.md)valamint az IP-címekre vonatkozó Azure CNI.

Alapértelmezés szerint az AKS úgy konfigurálja a frissítéseket, hogy egy további csomóponttal túlcsukják a frissítéseket. A maximális túlterhelési beállítások alapértelmezett értéke lehetővé teszi, hogy az AKS minimalizálja a munkaterhelés megszakadásait azáltal, hogy létrehoz egy további csomópontot a meglévő alkalmazások elkábele/ürítse ki egy régebbi verziójú csomópont cseréje érdekében. A maximális túlcsomópont-érték csomópontkészletenként testre szabható, hogy lehetővé tegye a frissítési sebesség és a frissítés megszakadása közötti különbségeket. A maximális hirtelen ingadozás értékének növelésével a frissítési folyamat gyorsabban befejeződik, de ha nagy értéket ad meg a maximális túl sok értékhez, az a frissítési folyamat során fennakadásokat okozhat. 

A 100%-os maximális hirtelen ingadozás például a lehető leggyorsabb frissítési folyamatot biztosítja (megkéteredi a csomópontok számát), ugyanakkor a csomópontkészlet összes csomópontja egyszerre lesz kiürítve. Előfordulhat, hogy ennél magasabb értéket szeretne használni a tesztelési környezetekhez. Éles csomópontkészletek esetén 33%-os max_surge ajánlott.

Az AKS egész számértékeket és százalékos értéket is elfogad a maximális túl sok értékhez. Az olyan egész szám, mint az "5" öt további csomópont túlcsomópontot jelez. Az "50%" érték azt jelzi, hogy a készletben lévő aktuális csomópontszám felének hirtelen megnöklére van szó. A maximális hirtelen emelkedés százalékos értéke legalább 1% és legfeljebb 100% lehet. A százalékértéket a legközelebbi csomópontszámra kerekítettük fel. Ha a maximális hirtelen számlálási érték alacsonyabb, mint a frissítéskor aktuális csomópontszám, a rendszer az aktuális csomópontok számát használja a maximális túlcsomó értékhez.

A frissítés során a maximális túlcsomó érték lehet legalább 1, a maximális érték pedig egyenlő a csomópontkészletben lévő csomópontok számának értékével. Nagyobb értékeket is be lehet állítani, de a maximális ingadozáshoz használt csomópontok maximális száma nem lesz nagyobb, mint a frissítéskor a készletben lévő csomópontok száma.

> [!Important]
> A csomópontkészletek maximális hirtelen ingadozási beállítása végleges.  Az ezt követő Kubernetes-frissítések vagy csomópontverzió-frissítések ezt a beállítást használják. A csomópontkészletek maximális hirtelen ingadozási értékét bármikor módosíthatja. Éles csomópontkészletek esetén a 33%-os maximális ingadozási beállítást javasoljuk.

Az alábbi parancsokkal beállíthatja az új vagy meglévő csomópontkészletek maximális túlhasználati értékét.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AKS-fürthöz elérhető verziók listáját az [az aks upgrade][az-aks-upgrade] paranccsal frissítheti. A frissítési folyamat során az AKS a következőt fogja: 
- adjon hozzá egy új puffercsomópontot [](#customize-node-surge-upgrade)(vagy a maximális ingadozásban konfigurált számú csomópontot) ahhoz a fürthöz, amely a megadott Kubernetes-verziót futtatja. 
- [cordon és ürítse][kubernetes-drain] ki az egyik régi csomópontot a futó alkalmazások [][kubernetes-drain] megszakításának minimalizálása érdekében (ha maximális túlméretet használ, az el lesz különedve és annyi csomópontot ürít ki, mint a puffercsomópontok megadott száma). 
- Amikor a régi csomópont teljesen ki van ürítve, a rendszer újra rendszerképet hoz létre az új verzió fogadására, és ez lesz a következő csomópont frissítésének puffercsomópontja. 
- Ez a folyamat addig ismétlődik, amíg a fürt összes csomópontja nem lett frissítve. 
- A folyamat végén a rendszer törli az utolsó puffercsomópontot, fenntartva a meglévő ügynökcsomópontszámot és a zónaegyenleget.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

A fürt frissítése a csomópontok száma alapján néhány percet vesz igénybe.

> [!IMPORTANT]
> Győződjön meg arról, hogy bármely (PDB) lehetővé teszi legalább 1 podreplika egyszerre való mozgatését, ellenkező esetben a `PodDisruptionBudgets` kiürítési/kiürítési művelet sikertelen lesz.
> Ha a kiürítési művelet sikertelen, a frissítési művelet a terv szerint sikertelen lesz, hogy az alkalmazások ne szakadnak meg. Javítsa ki, mi okozta a művelet leállítását (helytelen PDB-k, kvóta hiánya stb.), majd próbálkozzon újra a művelettel.

A frissítés sikeres voltának megerősítéséhez használja az [az aks show][az-aks-show] parancsot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Az alábbi példakimenet azt mutatja, hogy a fürt most *már az 1.18.10-et futtatja:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Automatikus frissítési csatorna beállítása

A fürtök manuális frissítése mellett automatikus frissítési csatornát is be lehet állítani a fürtön. A következő frissítési csatornák érhetők el:

|Csatorna| Művelet | Példa
|---|---|---|
| `none`| letiltja az automatikus frissítéseket, és megőrzi a fürtöt a Kubernetes aktuális verziójában| Alapértelmezett beállítás, ha nem változott|
| `patch`| automatikusan frissítse a fürtöt a legújabb támogatott javításverzióra, amikor az elérhetővé válik, miközben az alverzió is ugyanaz marad.| Ha például egy fürt *az 1.17.7-es* és *az 1.17.9-es,* *1.18.4-es,* *1.18.6-os* és *1.19.1-es* verziókat futtatja, a fürt az *1.17.9-es* verzióra lesz frissítve|
| `stable`| automatikusan frissítse a fürtöt az *N-1* alverzió legújabb támogatott javításverzióra, *ahol az N* a legújabb támogatott alverzió.| Ha például egy fürt *az 1.17.7-es* és *az 1.17.9-es,* *1.18.4-es,* *1.18.6-os* és *1.19.1-es* verziókat futtatja, a fürt az *1.18.6-os* verzióra lesz frissítve.
| `rapid`| automatikusan frissítse a fürtöt a legújabb támogatott javításverzióra.| Abban az esetben, ha a fürt a Kubernetes egy *N-2-es* alverziójú verziója, ahol az *N* a legújabb támogatott alverzió, a fürt először az *N-1 alverzió* legújabb támogatott javítási verziójára frissít. Ha például egy fürt *az 1.17.7-es,* az *1.17.9-es,* az *1.18.4-es,* az *1.18.6-os* és az *1.19.1-es* verzióját futtatja, a fürt először az *1.18.6-os* verzióra, majd az *1.19.1* verzióra lesz frissítve.

> [!NOTE]
> A fürt automatikus frissítése csak a Kubernetes GA-verzióira frissül, és nem frissül az előzetes verziókra.

A fürtök automatikus frissítése ugyanazt a folyamatot követi, mint a fürtök manuális frissítése. További részletekért lásd: [AKS-fürt frissítése.][upgrade-cluster]

Az AKS-fürtök automatikus fürtfrissítése előzetes verziójú funkció.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Regisztrálja `AutoUpgradePreview` a funkciójelölőt az [az feature register paranccsal,][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Az automatikus frissítési csatorna fürt létrehozásakor való  beállítását az automatikus frissítési csatorna paraméterrel állíthatja be, az alábbi példához hasonlóan.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Az automatikus frissítési csatorna meglévő fürtön való beállításhoz frissítse az *automatikus* frissítési csatorna paramétert az alábbi példához hasonlóan.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy meglévő AKS-fürt frissítését mutatta be. Az AKS-fürtök üzembe helyezésével és kezelésével kapcsolatos további információkért tekintse meg az oktatóanyagokat.

> [!div class="nextstepaction"]
> [AKS-oktatóanyagok][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
