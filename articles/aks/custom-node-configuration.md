---
title: Csomópont-konfiguráció testreszabása Azure Kubernetes Service (AKS) csomópontkészletek számára (előzetes verzió)
description: Megtudhatja, hogyan szabhatja testre Azure Kubernetes Service fürtcsomópontok és csomópontkészletek konfigurációját.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779974"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Csomópont-konfiguráció testreszabása Azure Kubernetes Service (AKS) csomópontkészletek számára (előzetes verzió)

A csomópont konfigurációjának testreszabásával konfigurálhatja vagy finomhangolhatja az operációs rendszer beállításait vagy a kubelet-paramétereket a számítási feladatok igényeinek megfelelően. Amikor létrehoz egy AKS-fürtöt, vagy csomópontkészletet ad hozzá a fürthöz, testreszabhatja a gyakran használt operációsrendszer- és kubelet-beállítások egy részhalmazát. Az ezen részhalmazon túli beállítások konfigurálásához használjon démonkészletet a szükséges konfigurációk testreszabásához anélkül, hogy támogatnia kell az [AKS-t a csomópontok számára.](support-policies.md#shared-responsibility)

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Az előzetes `CustomNodeConfigPreview` verziójú funkció regisztrálása

A Kubelet-paramétereket vagy operációsrendszer-beállításokat testre szabó AKS-fürt vagy csomópontkészlet létrehozásához engedélyeznie kell a funkciójelölőt `CustomNodeConfigPreview` az előfizetésen.

Regisztrálja `CustomNodeConfigPreview` a funkciójelölőt az [az feature register paranccsal,][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

A kubelet-paramétereket vagy operációsrendszer-beállításokat testre szabó AKS-fürt vagy csomópontkészlet létrehozásához a legújabb *aks-preview Azure CLI-bővítmény* szükséges. Telepítse *az aks-preview* Azure CLI-bővítményt az [az extension add paranccsal.][az-extension-add] Vagy telepítse az elérhető frissítéseket az [az extension update paranccsal.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Egyéni csomópont-konfiguráció használata

### <a name="kubelet-custom-configuration"></a>Kubelet egyéni konfigurációja

A támogatott Kubelet-paramétereket és az elfogadott értékeket az alábbiakban soroljuk fel.

| Paraméter | Megengedett értékek/időköz | Alapértelmezett | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | nincs, statikus | Nincs | A statikus szabályzat lehetővé teszi a garantált [podok](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) tárolói számára, hogy egész számra vonatkozó CPU-kérelmeket használjanak a csomópont kizárólagos processzorai számára. |
| `cpuCfsQuota` | true, false (igaz, hamis) | true |  A CPU CFS-kvóta kényszerítési funkció engedélyezése/letiltása a processzorkorlátokat megszabadó tárolók esetében. | 
| `cpuCfsQuotaPeriod` | Időköz ezredmásodpercben (ms) | `100ms` | A CPU CFS-kvótaidőszak értékét állítja be. | 
| `imageGcHighThreshold` | 0-100 | 85 | A lemezhasználat százalékos aránya, amely után a rendszer mindig futtatja a lemezképek szemétgyűjtését. A **szemétgyűjtést kiváltó minimális** lemezhasználat. A rendszerkép szemétgyűjtésének letiltásához állítsa 100-ra. | 
| `imageGcLowThreshold` | 0–100, nem magasabb, mint `imageGcHighThreshold` | 80 | A lemezhasználat százalékos aránya, amely előtt a rendszerkép szemétgyűjtését soha nem futtatja a rendszer. A szemétgyűjtést **kiváltó minimális** lemezhasználat. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | Nincs | Optimalizálja a NUMA-csomópontok igazítását. További információ [itt található.](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) Csak a kubernetes v1.18+. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Nincsenek | Nem biztonságos sysctl- vagy nem biztonságos sysctl-minták engedélyezett listája. | 

### <a name="linux-os-custom-configuration"></a>Linux operációs rendszer egyéni konfigurációja

A támogatott operációsrendszer-beállításokat és az elfogadott értékeket az alábbiakban soroljuk fel.

#### <a name="file-handle-limits"></a>Fájlkezelők korlátai

Nagy forgalom kiszolgálásakor gyakori, hogy a kiszolgált forgalom nagy számú helyi fájlból érkezik. Az alábbi kernelbeállításokat és a beépített korlátokat úgy finomhangolhatja, hogy néhány rendszermemória árán több kezelhet.

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | A Linux kernel által lefoglalható fájlkezelők maximális száma ennek az értéknek a növelésével megnövelhető a megnyitott fájlok maximális száma. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | A rendszer által engedélyezett fájlóra-figyelők maximális száma. Mindegyik *óra* körülbelül 90 bájt egy 32 bites kernelen, és körülbelül 160 bájt egy 64 bites kernelen. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | Az aio-nr az aszinkron Io-kérések aktuális rendszerre vonatkozó számát jeleníti meg. Az aio-max-nr lehetővé teszi az aio-nr maximális értékének beállítását. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | A folyamat által lefoglalható fájlkezelők maximális száma. |


#### <a name="socket-and-network-tuning"></a>Szoftvercsatorna és hálózat finomhangolása

Az ügynökcsomópontok esetében, amelyek várhatóan nagyon nagy számú egyidejű munkamenetet kezelnek, az alábbi TCP- és hálózati beállítások részkészletét használhatja, amelyet csomópontkészletenként finomhangolhat. 

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | A csatlakozási kérelmek maximális száma, amelyek várólistára tehetőek bármely adott figyelő szoftvercsatornához. A [listen(2)](http://man7.org/linux/man-pages/man2/listen.2.html) függvénynek átadott hátralékparaméter felső korlátja. Ha a hátralék argumentuma nagyobb, mint a , akkor csendesen csonkolva van erre `somaxconn` a korlátra.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Az INPUT oldalon várólistára dobott csomagok maximális száma, amikor az interfész gyorsabban fogadja a csomagokat, mint ahogy a kernel fel tudja azokat feldolgozni. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | A maximális fogadási szoftvercsatorna puffermérete bájtban. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | A küldési szoftvercsatorna maximális puffermérete bájtban. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | A puffer maximális mérete (lehetőségként használható memóriapuffer) foglalatonként. A szoftvercsatorna-beállítás memóriáját néhány esetben a szoftvercsatorna használatával kapcsolatos további struktúrák tárolására használják. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | Azon várólistán lévő csatlakozási kérelmek maximális száma, amelyek még nem kaptak nyugtázást a csatlakozó ügyféltől. Ha ezt a számot túllépi, a kernel elkezdi eldobni a kéréseket. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | A rendszer által egyidejűleg tartott `timewait` szoftvercsatornák maximális száma. Ha túllépi ezt a számot, a rendszer azonnal megsemmisíti a várakozási szoftvercsatornát, és figyelmeztetést ad ki. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Az az időtartam, amíg egy árva kapcsolat (amelyre már nem hivatkozik egy alkalmazás sem) a FIN_WAIT_2 állapotban marad, mielőtt a kapcsolat megszakad a helyi végén. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Milyen gyakran küld a TCP `keepalive` üzeneteket, ha `keepalive` engedélyezve van. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | A TCP által küldött mintavételek száma, amíg el nem `keepalive` dönti, hogy a kapcsolat megszakadt. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | A mintavételek elküldének milyen gyakran. A mintavételek elindulásától a megszorozva azt az időt teszi ki, amikor le kell mondani egy nem válaszoló `tcp_keepalive_probes` kapcsolatot. | 
| `net.ipv4.tcp_tw_reuse` | 0 vagy 1 | 0 | Engedélyezze a `TIME-WAIT` szoftvercsatornák újrafelhasználását az új kapcsolatokhoz, ha a protokoll szempontjából biztonságos. | 
| `net.ipv4.ip_local_port_range` | Első: 1024 - 60999 és utolsó: 32768 - 65000] | Első: 32768 és utolsó: 60999 | A TCP- és UDP-forgalom által a helyi port kiválasztása érdekében használt helyi porttartomány. Két számból áll: Az első szám az ügynökcsomópont TCP- és UDP-forgalmához engedélyezett első helyi port, a második pedig az utolsó helyi portszám. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Az ARP-gyorsítótárban található bejegyzések minimális száma. A szemétgyűjtés nem aktiválódik, ha a bejegyzések száma a beállítás alatt van. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Az ARP-gyorsítótárban található bejegyzések soft maximuma. Ez a beállítás talán a legfontosabb, mivel az ARP szemétgyűjtés körülbelül 5 másodperccel a soft maximum elérése után aktiválódik. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Az ARP-gyorsítótár bejegyzéseinek maximális száma. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` A egy modul, amely nyomon követi a NAT kapcsolati bejegyzését a Linuxon belül. A modul egy kivonattáblát használ a TCP protokoll létrehozott `nf_conntrack` kapcsolati rekordja  rögzítéséhez. `nf_conntrack_max` A a kivonattábla csomópontjainak maximális száma, azaz a modul által támogatott kapcsolatok maximális száma vagy a kapcsolatkövetési `nf_conntrack` tábla mérete. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` A egy modul, amely nyomon követi a NAT kapcsolati bejegyzését a Linuxon belül. A modul egy kivonattáblát használ a TCP protokoll létrehozott `nf_conntrack` kapcsolati rekordja  rögzítéséhez. `nf_conntrack_buckets` A a kivonattábla mérete. | 

#### <a name="worker-limits"></a>Feldolgozói korlátok

A fájlleírók korlátaihoz hasonló, a folyamatok által létrehozható dolgozók vagy szálak számát is korlátozza a kernelbeállítás és a felhasználói korlátok. Az AKS felhasználói korlátja korlátlan. 

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | A folyamatok a munkaszálak felpörgethet. A létrehozható szálak maximális száma a kernelbeállítással van `kernel.threads-max` beállítva. | 

#### <a name="virtual-memory"></a>Virtuális memória

Az alábbi beállításokkal finomhangolhatja a Linux kernel virtuálismemória- (VM-) alrendszerét és a lemezre telepített, nem használt `writeout` adatokat.

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Ez a fájl tartalmazza a folyamat memóriatérkép-területeinek maximális számát. A memóriatérkép területei a, közvetlenül a , a és a hívásának mellékhatásaként, valamint megosztott kódtárak `malloc` `mmap` `mprotect` `madvise` betöltésekor is használhatók. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Ez a százalékos érték szabályozza, hogy a kernel általában visszaigényli a memóriát, amely a könyvtár- és inode-objektumok gyorsítótárazása során használatos. |
| `vm.swappiness` | 0 - 100 | 60 | Ezzel a vezérlővel határozható meg, hogy a kernel milyen agresszív módon cseréli fel a memórialapokat. A magasabb értékek növelik az agresszivitást, az alacsonyabb értékek pedig csökkentik a felcserélés mennyiségét. A 0 érték arra utasítja a kernelt, hogy ne kezdeményezzen felcserélést, amíg a szabad és a fájl háta mögötti lapok mennyisége kisebb, mint a zóna magas vízjele. | 
| `swapFileSizeMB` | 1 MB – Az ideiglenes lemez [mérete](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Nincsenek | A SwapFileSizeMB megadja a felcserélési fájl MÉRETÉT MB-ban, amely a csomópontkészlet ügynökcsomópontjaiban jön létre. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [A transzparens hatalmasok](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) egy Linux-kernel funkció, amely a processzor memórialeképezési hardverének hatékonyabb használatával javítja a teljesítményt. Ha engedélyezve van, a kernel megkísérli a lefoglalást, amikor csak lehetséges, és bármely Linux-folyamat 2 MB méretű oldalakat kap, ha a régió természetesen igazodik a `hugepages` `mmap` 2 MB-hoz. Bizonyos esetekben, amikor engedélyezve van a rendszer egészére, előfordulhat, hogy az alkalmazások több memória-erőforrást `hugepages` foglalnak le. Az alkalmazások nagy méretű régiók lehetnek, de csak 1 bájtot érinthetnek, ebben az esetben egy 2 MB méretű lap lefoglalható egy 4k méretű lap helyett, jó ok `mmap` nélkül. Ez az oka annak, hogy a rendszer egészére kiterjedően letiltható, vagy csak régiókon `hugepages` belül `MADV_HUGEPAGE madvise` vannak. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Ez az érték szabályozza, hogy a kernelnek érdemes-e agresszív memória-tömörítést használnia, hogy az elérhetőbb `hugepages` legyen. | 

> [!IMPORTANT]
> A könnyebb keresés és olvashatóság érdekében az operációs rendszer beállításai név szerint jelennek meg ebben a dokumentumban, de hozzá kell adni a konfigurációs json-fájlhoz vagy az AKS API-hoz a [camelCase capitalization konvenció használatával.](/dotnet/standard/design-guidelines/capitalization-conventions)

Hozzon `kubeletconfig.json` létre egy fájlt a következő tartalommal:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Hozzon `linuxosconfig.json` létre egy fájlt a következő tartalommal:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Hozzon létre egy új fürtöt, amely megadja a kubelet és az operációs rendszer konfigurációját az előző lépésben létrehozott JSON-fájlok használatával. 

> [!NOTE]
> Fürt létrehozásakor megadhatja a kubelet konfigurációját, az operációs rendszer konfigurációját vagy mindkettőt. Ha a fürt létrehozásakor megad egy konfigurációt, csak a kezdeti csomópontkészlet csomópontjaira lesz alkalmazva ez a konfiguráció. A JSON-fájlban nem konfigurált beállítások megőrzik az alapértelmezett értéket.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Adjon hozzá egy új csomópontkészletet, amely megadja a Kubelet-paramétereket a létrehozott JSON-fájl használatával.

> [!NOTE]
> Amikor csomópontkészletet ad hozzá egy meglévő fürthöz, megadhatja a kubelet konfigurációját, az operációs rendszer konfigurációját vagy mindkettőt. Ha csomópontkészlet hozzáadásakor ad meg konfigurációt, csak az új csomópontkészlet csomópontjaira lesz alkalmazva ez a konfiguráció. A JSON-fájlban nem konfigurált beállítások megőrzik az alapértelmezett értéket.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Következő lépések

- Ismerje [meg, hogyan konfigurálhatja az AKS-fürtöt.](cluster-configuration.md)
- Ismerje [meg, hogyan frissítheti a fürtben található](node-image-upgrade.md) csomópont-rendszerképeket.
- A fürt a Kubernetes legújabb verziójára való frissítésének mikéntjéhez tekintse meg az [Azure Kubernetes Service- (AKS-)](upgrade-cluster.md) fürt frissítése című útmutatót.
- Az [AKS-ről gyakran feltett](faq.md) kérdések listájában választ találhat néhány gyakori AKS-kérdésre.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why