---
title: Fürtkonfiguráció az Azure Kubernetes Servicesben (ak)
description: Megtudhatja, hogyan konfigurálhat fürtöt az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eaf512915532b482c25e830cd9f2e01d61aa4524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572774"
---
# <a name="configure-an-aks-cluster"></a>AKS-fürt konfigurálása

Az AK-fürtök létrehozásának részeként előfordulhat, hogy a fürt konfigurációját testre kell szabnia az igényeinek megfelelően. Ez a cikk néhány lehetőséget mutat be az AK-fürt testreszabásához.

## <a name="os-configuration"></a>Operációs rendszer konfigurációja

Az AK mostantól támogatja az Ubuntu 18,04-et, mivel a kubernetes-verziókban a 1,18-nál újabb verziójú fürtök esetében az alapértelmezett csomópontos operációs rendszer (GA) a 1,18-as verziónál nagyobb, az AK Ubuntu 16,04 továbbra is az alapértelmezett alaprendszerkép. Az kubernetes v 1.18-as és újabb verziójában az alapértelmezett alapszintű Ubuntu 18,04.

> [!IMPORTANT]
> A Kubernetes v 1.18-es vagy újabb verziójában létrehozott Node-készletek `AKS Ubuntu 18.04` . A 1,18-nál kisebb, támogatott Kubernetes-verzióban található csomópont `AKS Ubuntu 16.04` -készletek csomóponti képként jelennek meg, de `AKS Ubuntu 18.04` a csomópont-készlet Kubernetes verziójának frissítése a v 1.18-ra vagy újabbra történik.
> 
> Erősen ajánlott az AK Ubuntu 18,04 Node-készletekben lévő munkaterhelések tesztelése az 1,18-es vagy újabb fürtök használata előtt.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Az AK Ubuntu 18,04 (GA) használata új fürtökön

A Kubernetes v 1.18-es vagy újabb verziójában létrehozott fürtök `AKS Ubuntu 18.04` . A 1,18-nál kisebb támogatott Kubernetes-verziókban a csomópont-készletek továbbra is `AKS Ubuntu 16.04` a csomóponti képként lesznek megkapva, de a rendszer a `AKS Ubuntu 18.04` fürt vagy a csomópont-készlet Kubernetes verziójának a v 1.18-es vagy újabb verziójára való frissítését követően frissíti.

Erősen ajánlott az AK Ubuntu 18,04 Node-készletekben lévő munkaterhelések tesztelése az 1,18-es vagy újabb fürtök használata előtt.

Ha csomópont-rendszerkép használatával szeretne fürtöt létrehozni `AKS Ubuntu 18.04` , egyszerűen hozzon létre egy kubernetes v 1.18 vagy újabb rendszert futtató fürtöt az alábbi ábrán látható módon.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>AK Ubuntu 18,04 (GA) használata meglévő fürtökön

A Kubernetes v 1.18-es vagy újabb verziójában létrehozott fürtök `AKS Ubuntu 18.04` . A 1,18-nál kisebb támogatott Kubernetes-verziókban a csomópont-készletek továbbra is `AKS Ubuntu 16.04` a csomóponti képként lesznek megkapva, de a rendszer a `AKS Ubuntu 18.04` fürt vagy a csomópont-készlet Kubernetes verziójának a v 1.18-es vagy újabb verziójára való frissítését követően frissíti.

Erősen ajánlott az AK Ubuntu 18,04 Node-készletekben lévő munkaterhelések tesztelése az 1,18-es vagy újabb fürtök használata előtt.

Ha a fürtök vagy a csomópontok készletei készen állnak a `AKS Ubuntu 18.04` Node-rendszerképekre, egyszerűen frissítheti őket egy v 1.18-es vagy újabb verzióra.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Ha csak egy csomópont-készletet szeretne frissíteni:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>AK Ubuntu 18,04 (GA) tesztelése meglévő fürtökön

A Kubernetes v 1.18-es vagy újabb verziójában létrehozott Node-készletek `AKS Ubuntu 18.04` . A 1,18-nál kisebb támogatott Kubernetes-verziók csomópont-készletei továbbra is `AKS Ubuntu 16.04` a csomóponti képként fognak megjelenni, de `AKS Ubuntu 18.04` a csomópont-készlet Kubernetes verziójának frissítését a v 1.18 vagy újabb verzióra frissíti.

A termelési csomópontok készletének frissítése előtt erősen ajánlott az AK Ubuntu 18,04 Node-készletekben lévő számítási feladatok tesztelése.

Ha csomópont-rendszerkép használatával szeretne csomópont-készletet létrehozni `AKS Ubuntu 18.04` , egyszerűen hozzon létre egy kubernetes v 1.18 vagy újabb rendszert futtató csomópont-készletet. A fürt vezérlési síkja legalább a v 1.18-as vagy újabb verzióra van szükség, de a többi csomópont-készlet továbbra is egy régebbi kubernetes-verzióban maradhat.
Az alábbiakban először frissítjük a vezérlési gépet, majd létrehozunk egy új, a v 1.18-vel rendelkező Node-készletet, amely megkapja az új Node-rendszerkép operációsrendszer-verzióját.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Tároló futásidejű konfigurációja

A Container Runtime olyan szoftver, amely tárolókat hajt végre, és a tároló lemezképeit kezeli egy csomóponton. A futtatókörnyezet segíti az absztrakt el-hívásokat vagy az operációs rendszer (OS) specifikus funkcióit a tárolók Linux vagy Windows rendszeren való futtatásához. Az Kubernetes 1,19-es verziójú csomópont-készleteket és a tároló futtatókörnyezetének nagyobb használatát használó AK-fürtök `containerd` . Az Kubernetes-t használó, a csomópontok előtt a v 1.19-t használó-fürtök a [Moby](https://mobyproject.org/) (felsőbb rétegbeli Docker) tároló-futtatókörnyezetet használják.

![Docker ICC 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) a egy [OCI](https://opencontainers.org/) (Open Container Initiative) szabványnak megfelelő alapszintű tároló-futtatókörnyezet, amely a szükséges funkciók minimális készletét biztosítja a tárolók végrehajtásához és a lemezképek egy csomóponton való kezeléséhez. A Felhőbeli natív számítási alaprendszer (CNCF) a 2017 márciusában lett [adományozva](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) . A jelenlegi Moby-verzió, amelyet az AK már használ, és a `containerd` fent látható módon épül fel.

A `containerd` -alapú csomópont-és Node-készletekkel a `dockershim` kubelet közvetlenül az `containerd` ICC (Container Runtime Interface) beépülő modulon keresztül fog beszélgetni, így a Docker ICC megvalósításával összehasonlítva eltávolítja a folyamat további ugrásait. Így jobb lesz a pod indítási késés és kevesebb erőforrás (CPU és memória) használata.

Az `containerd` AK-csomópontok használata esetén a pod indítási késése növeli a tároló futtatókörnyezetét, és csökkenti a csomópontok erőforrás-felhasználását. Ezek a Újdonságok olyan új architektúrán keresztül érhetők el, ahol `containerd` a kubelet közvetlenül az ICC beépülő modulon keresztül beszél a Moby/Docker architektúrán kubelet `dockershim` `containerd` , így a folyamat további ugrásokkal is rendelkezik.

![Docker ICC 2](media/cluster-configuration/containerd-cri.png)

`Containerd` a Kubernetes minden egyes, a v 1,19-es verziójában lévő, a Kubernetes-ben elérhető verziójában működik, és támogatja az összes Kubernetes-és AK-funkciót.

> [!IMPORTANT]
> A Kubernetes v 1,19-es vagy újabb verzióban létrehozott Node-készletekkel rendelkező fürtök a `containerd` tároló futtatókörnyezetéhez. A 1,19-nál kisebb, támogatott Kubernetes-verzióban található `Moby` , a tároló futtatókörnyezetéhez tartozó csomópont-készletekkel rendelkező fürtök, de a rendszer frissíti a `ContainerD` csomópont-készlet Kubernetes verziójának frissítését a v 1.19 vagy újabb verzióra. Továbbra is használhatja `Moby` a csomópont-készleteket és-fürtöket a régebbi támogatott verziókon, amíg azok nem támogatják a támogatást.
> 
> Javasoljuk, hogy a munkaterheléseket az AK-beli csomópont-készleteken tesztelje, a `containerD` 1,19-es vagy újabb fürtök használata előtt.

### <a name="containerd-limitationsdifferences"></a>`Containerd` korlátozások/különbségek

* `containerd`A tároló-futtatókörnyezet használatához a Base os Ubuntu 18,04-et kell használnia az alap operációsrendszer-rendszerképként.
* Amíg a Docker eszközkészlet továbbra is megtalálható a csomópontokon, a Kubernetes `containerd` a tároló futtatókörnyezetét használja. Ezért mivel a Moby/Docker nem kezeli a Kubernetes által létrehozott tárolókat a csomópontokon, nem tudja megtekinteni és használni a tárolókat a Docker-parancsokkal (például `docker ps` ) vagy a Docker API-val.
* A `containerd` (z) esetében javasoljuk, hogy [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) a DOCKer parancssori felület helyett a helyettesítő CLI-t használja a hüvelyek, tárolók és a Kubernetes-csomópontokon lévő tároló-lemezképek (például:) **hibaelhárításához** `crictl ps` . 
   * Nem biztosítja a Docker parancssori felületének teljes funkcionalitását. Csak hibaelhárításra szolgál.
   * `crictl` a a tárolók kubernetes, például a hüvelyek, például a hüvelyek és a hasonló fogalmak megjelenítését kínálja.
* `Containerd` beállítja a naplózást a szabványosított `cri` naplózási formátum használatával (amely eltér a Docker JSON-illesztőprogramtól származó aktuálisan lekérdezett adatoktól). A naplózási megoldásnak támogatnia kell a `cri` naplózási formátumot (például [a tárolók Azure monitor](../azure-monitor/containers/container-insights-enable-new-cluster.md))
* Már nem fér hozzá a Docker-motorhoz, `/var/run/docker.sock` vagy használhatja a Docker-in-Docker-t (DinD).
  * Ha jelenleg az alkalmazás naplófájljait kinyeri vagy a Docker-motorból figyeli az adatait, akkor használjon hasonló [Azure monitor a tárolók](../azure-monitor/containers/container-insights-enable-new-cluster.md) helyett. Emellett az AK nem támogatja a sávon kívüli parancsok futtatását az ügynök csomópontjain, amelyek instabilitást okozhatnak.
  * A fenti módszerekkel még a Moby/Docker használatakor is felhasználhatja a lemezképek kiépítését, és közvetlenül kihasználhatja a Docker-motort a fenti módszerek segítségével. A Kubernetes nem teljesen [tisztában van a](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) felhasznált erőforrásokkal, és ezek a módszerek számos olyan problémát [jelentenek, mint](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)például a.
* Rendszerképek készítése – továbbra is használhatja a jelenlegi Docker-összeállítási munkafolyamatot a szokásos módon, hacsak nem készít lemezképeket az AK-fürtön belül. Ebben az esetben érdemes átváltani a rendszerképek [ACR-feladatokkal](../container-registry/container-registry-quickstart-task-cli.md)történő összeállításának ajánlott megközelítésére, vagy a fürt biztonságosabb, például a [Docker-buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>2. generációs virtuális gépek

Az Azure támogatja a [2. generációs (Gen2) virtuális gépeket (VM)](../virtual-machines/generation-2.md). A 2. generációs virtuális gépek támogatják az 1. generációs virtuális gépeken (Gen1) nem támogatott főbb funkciókat. A szolgáltatások közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és a virtualizált állandó memória (vPMEM).

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. generációs virtuális gépek által használt BIOS-alapú architektúra helyett.
A Gen2 virtuális gépeket csak a megadott SKU-és méretek támogatják. Tekintse át a [támogatott méretek listáját](../virtual-machines/generation-2.md#generation-2-vm-sizes), és ellenőrizze, hogy az SKU támogatja vagy igényli-e a Gen2.

Emellett nem minden virtuálisgép-lemezkép támogatja a Gen2-t, az AK-Gen2 virtuális gépeken az új [AK Ubuntu 18,04-lemezképet](#os-configuration)fogja használni. Ez a rendszerkép az összes Gen2 SKU-t és méretet támogatja.

## <a name="ephemeral-os"></a>Ideiglenes operációs rendszer

Alapértelmezés szerint az Azure automatikusan replikálja egy virtuális gép operációsrendszer-lemezét az Azure Storage-ba, hogy elkerülje az adatvesztést, ha a virtuális gépet át kell helyezni egy másik gazdagépre. Mivel azonban a tárolók nem rendelkeznek helyi állapottal, ez a viselkedés korlátozott értéket kínál, miközben némi hátrányt biztosít, beleértve a csomópontok kiosztását és a magasabb olvasási/írási késést.

Ezzel szemben az elmúló operációsrendszer-lemezeket csak a gazdagép tárolja, ugyanúgy, mint egy ideiglenes lemezzel. Ez alacsonyabb olvasási/írási késést biztosít, valamint a csomópontok gyorsabb skálázását és a fürtök frissítését.

Az ideiglenes lemezhez hasonlóan a virtuális gép ára is tartalmaz egy időszakos operációsrendszer-lemezt, így további tárolási költségek nem merülnek fel.

> [!IMPORTANT]
>Ha a felhasználó nem ad ki explicit módon felügyelt lemezeket az operációs rendszer számára, az alapértelmezett érték az elmúló operációs rendszer, ha lehetséges, az adott nodepool-konfigurációhoz.

Az ideiglenes operációs rendszer használatakor az operációsrendszer-lemeznek a virtuális gép gyorsítótárába kell illeszkednie. A virtuális gépek gyorsítótárának mérete az IO-forgalom ("gyorsítótár mérete a GiB-ban") mellett zárójelben érhető el az [Azure dokumentációjában](../virtual-machines/dv3-dsv3-series.md) .

A VM alapértelmezett virtuálisgép-méretének használata Standard_DS2_v2 az operációsrendszer-lemez alapértelmezett mérete 100 GB példaként, ez a virtuálisgép-méret támogatja az elmúló operációs rendszert, de csak a gyorsítótár 86GB rendelkezik. Ez a konfiguráció alapértelmezés szerint a felügyelt lemezeket fogja megadni, ha a felhasználó nem ad meg explicit módon. Ha egy felhasználó explicit módon kérelmezte az ideiglenes operációs rendszert, akkor a rendszer érvényesítési hibát kapna.

Ha egy felhasználó ugyanazokat a Standard_DS2_v2 60GB operációsrendszer-lemezzel kéri, akkor ez a konfiguráció alapértelmezés szerint elmúló operációs rendszert igényel: a 60GB kért mérete kisebb, mint a 86GB maximális gyorsítótár-mérete.

Ha Standard_D8s_v3t használ a 100 GB operációsrendszer-lemezzel, ez a virtuálisgép-méret támogatja az elmúló operációs rendszert, és 200GB a gyorsítótár területe. Ha a felhasználó nem adja meg az operációsrendszer-lemez típusát, a nodepool alapértelmezés szerint az elmúló operációs rendszert fogja kapni. 

Az elmúló operációs rendszerhez az Azure CLI legalább 2.15.0 kell rendelkeznie.

### <a name="use-ephemeral-os-on-new-clusters"></a>Ideiglenes operációs rendszer használata új fürtökön

Konfigurálja úgy a fürtöt, hogy az elmúló operációsrendszer-lemezeket használja a fürt létrehozásakor. A `--node-osdisk-type` jelzővel állíthatja be az ideiglenes operációs rendszert az új fürt operációsrendszer-lemezének típusaként.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Ha a hálózatra csatlakoztatott operációsrendszer-lemezekkel szeretne normál fürtöt létrehozni, ezt megadhatja `--node-osdisk-type=Managed` . Azt is megteheti, hogy az alábbi módon további elmúló operációsrendszer-csomópont-készleteket ad hozzá.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Ideiglenes operációs rendszer használata meglévő fürtökön
Új csomópont-készlet beállítása az ideiglenes operációsrendszer-lemezek használatára. Használja a `--node-osdisk-type` jelzőt az operációsrendszer-lemez típusaként az adott csomópont operációsrendszer-lemezének típusaként.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Az elmúló operációs rendszerrel a virtuális gépek és a példányok rendszerképeinek üzembe helyezése a virtuális gép gyorsítótárának méretétől függetlenül végezhető el. Az AK esetében az alapértelmezett csomópont operációsrendszer-lemez konfigurációja 128GB használ, ami azt jelenti, hogy olyan virtuálisgép-méretre van szüksége, amely nagyobb, mint a 128GB. Az alapértelmezett Standard_DS2_v2 gyorsítótárának mérete 86GB, ami nem elég nagy. A Standard_DS3_v2 gyorsítótárának mérete 172GB, ami elég nagy. A használatával csökkentheti az operációsrendszer-lemez alapértelmezett méretét is `--node-osdisk-size` . Az AK-képek minimális mérete 30 GB. 

Ha hálózattal csatlakoztatott operációsrendszer-lemezekkel rendelkező csomópont-készleteket szeretne létrehozni, ezt megadhatja a következővel: `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Egyéni erőforráscsoport neve

Ha Azure Kubernetes Service-fürtöt helyez üzembe az Azure-ban, a rendszer létrehoz egy második erőforráscsoportot a munkavégző csomópontokhoz. Alapértelmezés szerint az AK a csomópont erőforráscsoportot nevezi el `MC_resourcegroupname_clustername_location` , de a saját nevét is megadhatja.

A saját erőforráscsoport-név megadásához telepítse az AK – előzetes verziójú Azure CLI-bővítmény 0.3.2 vagy újabb verzióját. Az Azure CLI használatával a `--node-resource-group` parancs paraméterével `az aks create` adhatja meg az erőforráscsoport egyéni nevét. Ha Azure Resource Manager sablont használ egy AK-fürt üzembe helyezéséhez, megadhatja az erőforráscsoport nevét a `nodeResourceGroup` tulajdonság használatával.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Az Azure erőforrás-szolgáltató automatikusan létrehozza a másodlagos erőforráscsoportot a saját előfizetésében. Az egyéni erőforráscsoport neve csak a fürt létrehozásakor adható meg. 

A csomópont-erőforráscsoport használata során ne feledje, hogy a következők nem használhatók:

- Válasszon egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
- Eltérő előfizetést kell megadni a csomópont-erőforráscsoport számára.
- Módosítsa a csomópont-erőforráscsoport nevét a fürt létrehozása után.
- A csomópont erőforráscsoporthoz tartozó felügyelt erőforrások nevének megadása.
- Módosíthatja vagy törölheti az Azure-ban létrehozott, felügyelt erőforrások címkéit a csomópont-erőforráscsoporton belül.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan frissítheti a fürtben található [csomópont-lemezképeket](node-image-upgrade.md) .
- Lásd: [Azure Kubernetes Service-(ak-) fürt frissítése](upgrade-cluster.md) , amelyből megtudhatja, hogyan frissítheti a fürtöt a Kubernetes legújabb verziójára.
- További információ [ `containerd` és Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Tekintse meg az [AK-val kapcsolatos gyakori kérdések](faq.md) listáját, ahol válaszokat talál a gyakori AK-kérdésekre.
- További információ az [elmúló operációsrendszer-lemezekről](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
