---
title: Az Azure Kubernetes Service (ak) Node-készletek csomópont-konfigurációjának testreszabása (előzetes verzió)
description: Megtudhatja, hogyan szabhatja testre a konfigurációt az Azure Kubernetes Service (ak) fürtcsomópontok és a csomópont-készleteken.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 589081149d08983d3cd5a4a8822873f5a6cfca0e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559431"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Az Azure Kubernetes Service (ak) Node-készletek csomópont-konfigurációjának testreszabása (előzetes verzió)

A csomópont-konfiguráció testreszabása lehetővé teszi az operációs rendszer (OS) beállításainak vagy a kubelet paramétereinek konfigurálását vagy finomhangolását a számítási feladatok igényeinek megfelelően. Ha AK-fürtöt hoz létre, vagy egy csomópont-készletet ad hozzá a fürthöz, testreszabhatja a gyakran használt operációs rendszer-és kubelet-beállítások részhalmazát. Ha ezen az alkészleten kívül szeretné konfigurálni a beállításokat, [egy démon-készlettel testreszabhatja a szükséges konfigurációkat anélkül, hogy kiveszítse a csomópontok AK-támogatását](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Az `CustomNodeConfigPreview` előzetes verzió funkciójának regisztrálása

Ha olyan AK-fürtöt vagy-csomópontot szeretne létrehozni, amely testreszabhatja a kubelet-paramétereket vagy az operációsrendszer-beállításokat, engedélyeznie kell a `CustomNodeConfigPreview` szolgáltatás jelölőjét az előfizetésében.

Regisztrálja a `CustomNodeConfigPreview` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Ha egy AK-alapú fürtöt vagy egy olyan csomópont-készletet szeretne létrehozni, amely testreszabhatja a kubelet-paramétereket vagy az operációsrendszer-beállításokat, szüksége lesz a legújabb *AK-előzetes* verzióra, az Azure CLI Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal. Vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Egyéni csomópont-konfiguráció használata

### <a name="kubelet-custom-configuration"></a>Kubelet egyéni konfiguráció

Az alábbi listában a támogatott Kubelet paraméterek és elfogadott értékek szerepelnek.

| Paraméter | Megengedett értékek/időköz | Alapértelmezett | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | nincs, statikus | Nincs | A statikus házirend lehetővé teszi, hogy a tárolók az egész PROCESSZORral rendelkező [garantált hüvelyekben](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) a csomóponton lévő kizárólagos CPU-kérelmekhez hozzáférjenek. |
| `cpuCfsQuota` | igaz, hamis | true |  Engedélyezi/letiltja a CPU CFS-kvóta kikényszerítését a CPU-korlátokat megadó tárolók esetében. | 
| `cpuCfsQuotaPeriod` | Intervallum ezredmásodpercben (MS) | `100ms` | Beállítja a CPU CFS-kvóta időszakának értékét. | 
| `imageGcHighThreshold` | 0-100 | 85 | A lemez használatának százalékos aránya, amely után a rendszer mindig futtatja a képszemetet tartalmazó gyűjteményt. **A szemét** -gyűjtést kiváltó minimális lemezhasználat. A képszemetet-gyűjtemény letiltásához állítsa a 100 értékre. | 
| `imageGcLowThreshold` | 0-100, nem nagyobb, mint `imageGcHighThreshold` | 80 | A lemez használatának százalékos aránya, amely előtt a rendszer soha nem futtatja a Rendszerképbeli adatgyűjtési gyűjteményt. **A minimális** lemezhasználat, amely kiválthatja a Garbage gyűjteményt. |
| `topologyManagerPolicy` | nincs, legjobb erőfeszítés, korlátozott, egyetlen NUMA-csomópont | Nincs | A NUMA-csomópontok igazításának optimalizálása: további [információ.](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) Csak kubernetes v 1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Nincs | Nem biztonságos sysctls vagy nem biztonságos sysctl-minták engedélyezési listája. | 

### <a name="linux-os-custom-configuration"></a>A Linux operációs rendszer egyéni konfigurációja

A támogatott operációsrendszer-beállítások és elfogadott értékek az alábbiakban láthatók.

#### <a name="file-handle-limits"></a>Fájlleíró korlátai

Nagy mennyiségű forgalom kiszolgálásakor gyakori, hogy a kiszolgált forgalom nagyszámú helyi fájlból származik. Az alábbi kernel-beállításokat és a beépített korlátokat úgy is megszabhatja, hogy több, a rendszermemória költségeit is kezelni tudja.

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 – 12000500 | 709620 | A Linux-kernel által lefoglalható fájlok maximális száma – ennek az értéknek a növelésével növelhető az engedélyezett nyitott fájlok maximális száma. |
| `fs.inotify.max_user_watches` | 781250 – 2097152 | 1048576 | A rendszeren engedélyezett fájlok maximális száma. Mindegyik *óra* körülbelül 90 bájt egy 32 bites kernelen, és nagyjából 160 bájt egy 64 bites kernelen. | 
| `fs.aio-max-nr` | 65536 – 6553500 | 65536 | Az AIO-Nr a jelenlegi rendszerszintű aszinkron i/o-kérelmek számát jeleníti meg. az AIO-Max-Nr lehetővé teszi, hogy megváltoztassa a maximális értéket a AIO-Nr értékkel. |
| `fs.nr_open` | 8192 – 20000500 | 1048576 | A fájlok maximális száma – egy folyamat lefoglalását végzi. |


#### <a name="socket-and-network-tuning"></a>Szoftvercsatorna és hálózati hangolás

Az olyan ügynök-csomópontok esetében, amelyek nagy számú egyidejű munkamenet kezelésére várnak, használhatja az alábbi TCP-és hálózati beállítások részhalmazát, hogy a rendszer beállíthatja a csomópontok készletét. 

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 – 3240000 | 16384 | Az adott figyelő szoftvercsatornához várólistára helyezhető kapcsolatkérelmek maximális száma. A [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) függvénynek átadott várakozó paraméter értékének felső korlátja. Ha a várakozó fájlok argumentuma nagyobb, mint a, a rendszer `somaxconn` ezt a korlátot csendesen csonkolja.
| `net.core.netdev_max_backlog` | 1000 – 3240000 | 1000 | A bemeneti oldalon várólistára helyezett csomagok maximális száma, amikor a csatoló gyorsabban fogad csomagokat, mint a kernel képes feldolgozni. |
| `net.core.rmem_max` | 212992 – 134217728 | 212992 | A szoftvercsatorna maximális pufferének mérete bájtban megadva. |
| `net.core.wmem_max` | 212992 – 134217728 | 212992 | A maximális küldési szoftvercsatorna pufferének mérete bájtban megadva. | 
| `net.core.optmem_max` | 20480 – 4194304 | 20480 | A kisegítő puffer maximális mérete (a beállítás memória-puffere) engedélyezett a szoftvercsatornán. A szoftvercsatorna-beállítás memóriája néhány esetben a szoftvercsatorna használatára vonatkozó további struktúrák tárolására szolgál. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 – 3240000 | 16384 | Azon várólistán lévő kapcsolódási kérelmek maximális száma, amelyek még nem kaptak nyugtát a csatlakozó ügyféltől. Ha túllépi ezt a számot, a kernel elkezdi a kérelmek eldobását. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 – 1440000 | 32768 | A `timewait` rendszeren egyszerre tárolt szoftvercsatornák maximális száma. Ha túllépi ezt a számot, a rendszer azonnal megsemmisíti az időkorlátot, és kinyomtatja a figyelmeztetést. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Az árva (az alkalmazás által már nem hivatkozott) időtartam az FIN_WAIT_2 állapotban marad, mielőtt megszakad a helyi végponton. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | `keepalive`A TCP által küldött üzenetek gyakorisága `keepalive` , amikor az engedélyezve van. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | `keepalive`A TCP-mintavételek száma, amíg úgy dönt, hogy a kapcsolatok megszakadnak. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | A mintavételek gyakori elküldése. A megszorozva `tcp_keepalive_probes` a mintavétel elkezdése után időt vesz igénybe a nem válaszoló kapcsolatok leöléséhez. | 
| `net.ipv4.tcp_tw_reuse` | 0 vagy 1 | 0 | Engedélyezi a szoftvercsatornák újrafelhasználását `TIME-WAIT` az új kapcsolatokhoz, ha a protokoll nézőpontja biztonságos. | 
| `net.ipv4.ip_local_port_range` | Első: 1024-60999 és utolsó: 32768-65000] | Első: 32768 és utolsó: 60999 | A TCP-és UDP-forgalom által a helyi port kiválasztásához használt helyi porttartomány. Két számból áll: az első szám az ügynök csomóponton a TCP-és UDP-forgalom számára engedélyezett első helyi port, a második pedig az utolsó helyi portszám. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 – 80000 | 4096 | Az ARP-gyorsítótárban esetlegesen megjelenő bejegyzések minimális száma. A rendszer nem indítja el a szemetet, ha a bejegyzések száma nem éri el ezt a beállítást. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 – 90000 | 8192 | Az ARP-gyorsítótárban esetlegesen megengedett bejegyzések maximális száma. Ez a beállítás vitathatatlanul a legfontosabb, mivel az ARP-adatgyűjtési gyűjtemény körülbelül 5 másodperccel a puha maximális érték elérése után aktiválódik. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 – 100000 | 16384 | Az ARP-gyorsítótár bejegyzéseinek maximális száma. |
| `net.netfilter.nf_conntrack_max` | 131072 – 589824 | 131072 | `nf_conntrack` a egy modul, amely nyomon követi a NAT-kapcsolatok bejegyzéseit a Linuxon belül. A `nf_conntrack` modul egy kivonatoló táblát használ a TCP protokoll *létrejött kapcsolati* rekordjának rögzítéséhez. `nf_conntrack_max` a kivonatoló tábla csomópontjainak maximális száma, azaz a modul által támogatott kapcsolatok maximális száma `nf_conntrack` vagy a kapcsolat nyomon követési táblázata. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 – 147456 | 65536 | `nf_conntrack` a egy modul, amely nyomon követi a NAT-kapcsolatok bejegyzéseit a Linuxon belül. A `nf_conntrack` modul egy kivonatoló táblát használ a TCP protokoll *létrejött kapcsolati* rekordjának rögzítéséhez. `nf_conntrack_buckets` a kivonatoló tábla mérete. | 

#### <a name="worker-limits"></a>Feldolgozói korlátok

A fájlok leíró korlátaihoz hasonlóan a folyamat által létrehozható munkavégzők vagy szálak száma is korlátozott a kernel-beállítás és a felhasználói korlátok esetében. Az AK-ra vonatkozó felhasználói korlát korlátlan. 

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | A folyamatok felhasználhatják a munkavégző szálakat. Az összes létrehozható szál maximális száma a rendszermag beállításával állítható be `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Virtuális memória

Az alábbi beállításokkal beállíthatja a Linux-kernel virtuális memória (VM) alrendszerének működését, valamint az `writeout` inkonzisztens adatlemezeket.

| Beállítás | Megengedett értékek/időköz | Alapértelmezett | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 – 262144 | 65530 | Ez a fájl tartalmazza a folyamat által esetlegesen feldolgozható memória-leképező területek maximális számát. A memória-hozzárendelési területek a hívás, a, a, a, a és a, valamint a `malloc` `mmap` `mprotect` `madvise` megosztott könyvtárak betöltésének mellékhatásai. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Ez a százalékérték azt szabályozza, hogy a rendszermag milyen tendenciát követel meg a memória visszaigényléséhez, amely a címtár-és inode objektumok gyorsítótárazására szolgál. |
| `vm.swappiness` | 0 - 100 | 60 | Ezzel a vezérlővel határozható meg, hogy a kernel milyen agresszíven fogja felcserélni a memória lapjait. A magasabb értékek növelik az agresszivitást, az alacsonyabb értékek csökkentik a swap mennyiségét. A 0 érték arra utasítja a rendszermagot, hogy ne indítson el swap-értéket, amíg a szabad és a fájl nélküli lapok mennyisége kevesebb, mint a zóna magas vízjele. | 
| `swapFileSizeMB` | 1 MB – az [ideiglenes lemez](../virtual-machines/managed-disks-overview.md#temporary-disk) mérete (/dev/sdb) | Nincs | A SwapFileSizeMB megadható, hogy egy lapozófájl mérete MB-ban legyen létrehozva a csomópont ügynök-csomópontjain. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | Az [átlátszó Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) egy Linux kernel-szolgáltatás, amelynek célja a teljesítmény javítása azáltal, hogy hatékonyabban használják fel a processzor memória-leképezési hardverét. Ha engedélyezve van a rendszermag, amikor csak `hugepages` lehetséges, a Linux-folyamat 2 MB-nyi oldalt fog kapni, ha a `mmap` régió 2 MB-ban természetesen össze van igazítva. Bizonyos esetekben, amikor `hugepages` engedélyezve vannak a rendszerszintű alkalmazások, a további memória-erőforrások lefoglalása is előfordulhat. Előfordulhat `mmap` , hogy egy alkalmazás nagyméretű régiót tartalmaz, de csak 1 bájtot érint, ebben az esetben a 2 MB-nyi oldalt a 4k-oldal helyett nem jó ok miatt lehet kiosztani. Ez a forgatókönyv ezért lehetséges a rendszerszintű, `hugepages` vagy csak a régiókban lévők letiltására `MADV_HUGEPAGE madvise` . | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Ez az érték azt határozza meg, hogy a rendszermagnak agresszívan kell-e használnia a memória tömörítését, hogy `hugepages` elérhető legyen. | 

> [!IMPORTANT]
> Az egyszerű keresés és az olvashatóság érdekében az operációs rendszer beállításai a nevük szerint jelennek meg a dokumentumban, de a konfigurációs JSON-fájlhoz vagy az AK API-hoz a [camelCase-tőkésítő konvenció](/dotnet/standard/design-guidelines/capitalization-conventions)használatával kell őket hozzáadni.

Hozzon létre egy `kubeletconfig.json` fájlt a következő tartalommal:

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
Hozzon létre egy `linuxosconfig.json` fájlt a következő tartalommal:

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

Hozzon létre egy új fürtöt, amely a kubelet és az operációs rendszer konfigurációit az előző lépésben létrehozott JSON-fájlokkal határozza meg. 

> [!NOTE]
> Fürt létrehozásakor megadhatja a kubelet konfigurációját, az operációs rendszer konfigurációját vagy mindkettőt. Ha a fürt létrehozásakor megad egy konfigurációt, akkor a rendszer csak a kezdeti csomópont-készlet csomópontjain alkalmazza ezt a konfigurációt. A JSON-fájlban nem konfigurált beállítások megőrzik az alapértelmezett értéket.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Adjon hozzá egy új Node-készletet, amely megadja a Kubelet paramétereket a létrehozott JSON-fájl használatával.

> [!NOTE]
> Ha meglévő fürthöz ad hozzá egy csomópont-készletet, megadhatja a kubelet konfigurációját, az operációs rendszer konfigurációját vagy mindkettőt. Ha egy csomópont-készlet hozzáadásakor megad egy konfigurációt, akkor a rendszer csak az új csomópont-készlet csomópontjain alkalmazza ezt a konfigurációt. A JSON-fájlban nem konfigurált beállítások megőrzik az alapértelmezett értéket.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan konfigurálhatja az AK-fürtöt](cluster-configuration.md).
- Megtudhatja, hogyan frissítheti a fürtben található [csomópont-lemezképeket](node-image-upgrade.md) .
- Lásd: [Azure Kubernetes Service-(ak-) fürt frissítése](upgrade-cluster.md) , amelyből megtudhatja, hogyan frissítheti a fürtöt a Kubernetes legújabb verziójára.
- Tekintse meg az [AK-val kapcsolatos gyakori kérdések](faq.md) listáját, ahol válaszokat talál a gyakori AK-kérdésekre.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
