---
title: Fürtkonfiguráció az Azure Kubernetes Servicesben (AKS)
description: Megtudhatja, hogyan konfigurálhatja a fürtöt a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5740c1c299e8a6a2e8874bd13aae76b0353cc6a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775869"
---
# <a name="configure-an-aks-cluster"></a>AKS-fürt konfigurálása

Az AKS-fürt létrehozásának részeként előfordulhat, hogy az igényeinek megfelelően testre kell szabni a fürtkonfigurációt. Ez a cikk bemutat néhány lehetőséget az AKS-fürt testreszabására.

## <a name="os-configuration"></a>Operációs rendszer konfigurációja

Az AKS mostantól az Ubuntu 18.04-et támogatja alapértelmezett csomóponti operációs rendszerként (OS) általánosan elérhetőként (GA) az 1.18-asnál újabb Kubernetes-verziójú fürtök esetén. Az 1.18-asnál régebbi verziók esetén az AKS Ubuntu 16.04 továbbra is az alapértelmezett alapként használt rendszerkép. A kubernetes 1.18-as és újabb verziókban az alapértelmezett alap az AKS Ubuntu 18.04.

> [!IMPORTANT]
> A Kubernetes 1.18-as vagy újabb, alapértelmezés szerint a csomópont-rendszerképhez létrehozott `AKS Ubuntu 18.04` csomópontkészletek. Az 1.18-asnál régebbi támogatott Kubernetes-verzión található csomópontkészletek csomópont-rendszerképként lesznek fogadva, de a rendszer a csomópontkészlet `AKS Ubuntu 16.04` Kubernetes-verziójának 1.18-as vagy újabb verzióra való frissítése után `AKS Ubuntu 18.04` frissül.
> 
> Erősen ajánlott a számítási feladatok AKS Ubuntu 18.04 csomópontkészleten való tesztelése az 1.18-as vagy annál nagyobb fürtök használata előtt.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Az AKS Ubuntu 18.04 (GA) használata új fürtökön

A Kubernetes v1.18-as vagy újabb rendszerképen létrehozott fürtök alapértelmezés szerint `AKS Ubuntu 18.04` csomópont-rendszerképet hoznak létre. Az 1.18-asnál régebbi támogatott Kubernetes-verzión található csomópontkészletek továbbra is csomópont-rendszerképként lesznek megkapva, de a rendszer a fürt vagy a csomópontkészlet `AKS Ubuntu 16.04` Kubernetes-verziójának 1.18-as vagy újabb verzióra való frissítése után `AKS Ubuntu 18.04` frissül.

Erősen ajánlott a számítási feladatok AKS Ubuntu 18.04 csomópontkészleten való tesztelése az 1.18-as vagy annál nagyobb fürtök használata előtt.

Fürt csomópont-rendszerkép használatával való létrehozásához egyszerűen hozzon létre egy `AKS Ubuntu 18.04` kubernetes v1.18 vagy újabb rendszerképet futtató fürtöt az alább látható módon

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Az AKS Ubuntu 18.04 (GA) használata meglévő fürtökön

A Kubernetes v1.18-as vagy újabb rendszerképen létrehozott fürtök alapértelmezés szerint `AKS Ubuntu 18.04` csomópont-rendszerképet hoznak létre. Az 1.18-asnál régebbi támogatott Kubernetes-verzión található csomópontkészletek továbbra is csomópont-rendszerképként lesznek megkapva, de a rendszer a fürt vagy a csomópontkészlet `AKS Ubuntu 16.04` Kubernetes-verziójának 1.18-as vagy újabb verzióra való frissítése után `AKS Ubuntu 18.04` frissül.

Erősen ajánlott a számítási feladatok AKS Ubuntu 18.04 csomópontkészleten való tesztelése, mielőtt 1.18-as vagy annál nagyobb fürtöt használ.

Ha a fürtök vagy csomópontkészletek készen állnak a csomópont-rendszerképre, egyszerűen frissítheti őket egy 1.18-as vagy újabb verzióra `AKS Ubuntu 18.04` az alábbiak szerint.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Ha csak egy csomópontkészletet szeretne frissíteni:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Az AKS Ubuntu 18.04 (GA) tesztelése meglévő fürtökön

Alapértelmezés szerint a Kubernetes 1.18-as vagy újabb rendszerképe által létrehozott `AKS Ubuntu 18.04` csomópontkészletek. Az 1.18-asnál régebbi támogatott Kubernetes-verzión található csomópontkészletek továbbra is csomópont-rendszerképként lesznek fogadva, de a rendszer a csomópontkészlet `AKS Ubuntu 16.04` Kubernetes-verziójának 1.18-as vagy újabb verzióra való frissítése után frissül a `AKS Ubuntu 18.04` verzióra.

Az éles csomópontkészletek frissítése előtt erősen ajánlott AKS Ubuntu 18.04 csomópontkészleten tesztelni a számítási feladatokat.

Ha csomópont-rendszerkép használatával hoz létre csomópontkészletet, egyszerűen hozzon létre egy `AKS Ubuntu 18.04` kubernetes 1.18-as vagy újabbat futtató csomópontkészletet. A fürt vezérlősíkjának legalább az 1.18-as vagy újabb verzión kell lennie, de a többi csomópontkészlet továbbra is egy régebbi Kubernetes-verzióban maradhat.
Az alábbiakban először frissítjük a vezérlősíkot, majd létrehozunk egy új, 1.18-as verziójú csomópontkészletet, amely megkapja a csomópont új rendszerképének operációsrendszer-verzióját.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Tároló-futásidejű konfiguráció

A tároló-futtatás olyan szoftver, amely tárolókat hajt végre, és tároló-rendszerképeket kezel egy csomóponton. A futásidejű környezet segít absztrakciót kiveszni a sys-hívásokból vagy az operációs rendszerre (OS) vonatkozó funkciókból a tárolók Linux vagy Windows rendszeren való futtatásához. Kubernetes 1.19-es verziójú csomópontkészleteket használó AKS-fürtök, amelyek tároló-futásideje nagyobb `containerd` mértékben használható. A Node-készletekhez az 1.19-esnél korábbi, Kubernetes-t használó AKS-fürtök a [Mobyt](https://mobyproject.org/) (upstream docker) használják tároló-futtatásként.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) A egy [OCI](https://opencontainers.org/) (Open Container Initiative) szabványnak megfelelő alaptároló-futtatás, amely biztosítja a tárolók végrehajtásához és a lemezképek csomóponton való kezeléséhez minimálisan szükséges funkciókat. A [Cloud](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) Native Compute Foundation (CNCF) 2017 márciusában lett áttűnve. Az AKS által használt aktuális Moby-verzió már használja a-t, és a (fent látható) `containerd` verzióra épül.

A -alapú csomópont- és csomópontkészletek esetén a kubelet közvetlenül a CRI (tároló-futásidejű felület) beépülő modullal fog beszélni, így a Docker CRI-implementációhoz képest a folyamaton felesleges ugrások `containerd` `dockershim` lesznek `containerd` eltávolítva. Így jobb indítási késést és kisebb erőforrás- (CPU- és memóriahasználatot) fog látni a podok indításakor.

Az AKS-csomópontokhoz való használatával a podok indítási késése javul, a tároló-futtatás pedig csökkenti a csomópontok `containerd` erőforrás-fogyasztását. Ezeket a fejlesztéseket ez az új architektúra engedélyezi, ahol a kubelet közvetlenül a CRI beépülő modulon keresztül folytatja a kapcsolatot, míg a Moby/Docker architektúrában a kubelet a és a Docker-motorral beszélget, mielőtt elérné a -et, így extra ugrásokat tartalmaz `containerd` `dockershim` a `containerd` folyamaton.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` az AKS-ben a Kubernetes minden GA-verzióján, valamint az 1.19-es verzió feletti összes fölé irányuló Kubernetes-verzión működik, és támogatja az összes Kubernetes- és AKS-funkciót.

> [!IMPORTANT]
> A Kubernetes 1.19-es vagy újabb rendszerében létrehozott csomópontkészleteket tartalmazó fürtök alapértelmezés szerint a `containerd` tároló-futtatáshoz vannak létrehozva. A támogatott Kubernetes-verzión található, 1.19-esnél régebbi verziójú csomópontkészlettel futó fürtök megkapják a tároló-futásidejüket, de a rendszer a csomópontkészlet `Moby` Kubernetes-verziójának 1.19-es vagy újabb verziójára való frissítése után `ContainerD` frissül. A régebbi támogatott verziókban továbbra is használhat csomópontkészleteket és fürtöt, `Moby` amíg azok nem támogatottak.
> 
> Erősen ajánlott a számítási feladatok AKS-csomópontkészleten való tesztelése az 1.19-es vagy annál nagyobb fürtök `containerD` használata előtt.

### <a name="containerd-limitationsdifferences"></a>`Containerd` korlátozások/különbségek

* A `containerd` tároló-futtatásként való használathoz az AKS Ubuntu 18.04-et kell használnia alap operációsrendszer-rendszerképként.
* Bár a Docker-eszközkészlet továbbra is jelen van a csomópontokon, a Kubernetes a `containerd` tároló-futásidőt használja. Ezért, mivel a Moby/Docker nem kezeli a Csomópontokon a Kubernetes által létrehozott tárolókat, nem tudja megtekinteni és kezelni a tárolókat Docker-parancsokkal (például ) vagy a `docker ps` Docker API-val.
* A (rendszer) esetén a Kubernetes-csomópontokon (például) lévő podok, tárolók és tároló-rendszerképek hibaelhárítására a Docker CLI helyett a használatát javasoljuk helyettesítő `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl)  CLI-ként. `crictl ps` 
   * Nem biztosítja a Docker CLI teljes funkcionalitását. Csak hibaelhárításra szolgál.
   * `crictl` A kubernetes-hez felhasználóbarátabb nézetet kínál a tárolókról, és olyan fogalmakat tartalmaz, mint a podok stb.
* `Containerd` A szabványosított naplózási formátummal állítja be a naplózást (ami eltér a `cri` Docker JSON-illesztőprogramja által jelenleg elérhetőtől). A naplózási megoldásnak támogatnia kell a naplózási `cri` formátumot (például Azure Monitor [tárolókhoz)](../azure-monitor/containers/container-insights-enable-new-cluster.md)
* Már nem férhet hozzá a Docker-motorhoz( `/var/run/docker.sock` ), vagy használhatja a Docker-in-Docker (DinD) et.
  * Ha jelenleg alkalmazásnaplókat vagy monitorozási adatokat szeretne kinyerni a Docker Engine-ból, használjon inkább Azure Monitor [tárolókhoz hasonlót.](../azure-monitor/containers/container-insights-enable-new-cluster.md) Emellett az AKS nem támogatja sávon túli parancsok futtatását az ügynökcsomópontokon, amelyek instabillá válhatnak.
  * Még a Moby/Docker használata esetén sem ajánlott a rendszerképek és a Docker-motornak a fenti módszerekkel történő közvetlen használata. A Kubernetes nem ismeri teljesen a felhasznált erőforrásokat, és [](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) ezek [](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)a megközelítések számos itt és itt részletezett problémát mutatnak be, például.
* Rendszerképek létrehozása – Továbbra is a megszokott módon használhatja az aktuális Docker-build-munkafolyamatot, kivéve, ha az AKS-fürtön belül hoz létre rendszerképeket. Ebben az esetben fontolja meg, hogy átvált az ajánlott módszerre a [rendszerképek ACR-feladatok](../container-registry/container-registry-quickstart-task-cli.md)vagy egy biztonságosabb fürtön belül, például a [docker buildx használatával való buildelése esetén.](https://github.com/docker/buildx)

## <a name="generation-2-virtual-machines"></a>2. generációs virtuális gépek

Az Azure a [2. generációs (Gen2) virtuális](../virtual-machines/generation-2.md)gépeket támogatja. A 2. generációs virtuális gépek támogatják az 1. generációs virtuális gépek (Gen1) által nem támogatott fő funkciókat. Ezek közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX) és a virtualizált állandó memória (vPMEM).

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. generációs virtuális gépek által használt BIOS-alapú architektúra helyett.
Csak bizonyos termékkódok és méretek támogatják a Gen2 virtuális gépeket. Tekintse meg [a támogatott méretek listáját,](../virtual-machines/generation-2.md#generation-2-vm-sizes)és ellenőrizze, hogy a termékváltozat támogatja-e a Gen2-t, vagy szükség van-e rá.

Emellett nem minden virtuálisgép-rendszerkép támogatja a Gen2-t, az AKS Gen2 virtuális gépek az új [AKS Ubuntu 18.04 rendszerképet fogják használni.](#os-configuration) Ez a rendszerkép az összes Gen2-s SKUs és méret esetén támogatott.

## <a name="ephemeral-os"></a>Aphemerális operációs rendszer

Alapértelmezés szerint az Azure automatikusan replikálja egy virtuális gép operációsrendszer-lemezét az Azure Storage-ba, hogy elkerülje az adatvesztést, ha a virtuális gépet másik gazdagépre kell áthelyezni. Mivel azonban a tárolók nem úgy vannak kialakítva, hogy a helyi állapot megmarad, ez a viselkedés korlátozott értéket biztosít, ugyanakkor hátrányokat is kínál, például lassabb csomópont-kiépítést és nagyobb olvasási/írási késést.

Ezzel szemben a rendszer csak a gazdagépen tárolja a ideiglenes operációsrendszer-lemezeket, csakúgy, mint az ideiglenes lemezeket. Ez alacsonyabb olvasási/írási késést, valamint gyorsabb csomópontméretezést és fürtfrissítéseket biztosít.

Az ideiglenes lemezhez hasonló, a virtuális gép ára egy ideiglenes operációsrendszer-lemezt is tartalmaz, így nem jár további tárolási költség.

> [!IMPORTANT]
>Ha a felhasználó nem kér kifejezetten felügyelt lemezeket az operációs rendszerhez, az AKS alapértelmezés szerint a rendszer teljes operációs rendszert kér, ha lehetséges egy adott nodepool-konfiguráció esetén.

A gyors operációs rendszer használata esetén az operációsrendszer-lemeznek el kell férnie a virtuális gép gyorsítótárában. A virtuálisgép-gyorsítótár méretei [](../virtual-machines/dv3-dsv3-series.md) az Azure-dokumentációban az IO-átviteli sebesség (GiB-ban lévő gyorsítótár mérete) mellett zárójelben érhetők el.

Az AKS alapértelmezett virtuálisgép-Standard_DS2_v2 alapértelmezett, 100 GB-os lemezmérettel használva ez a virtuálisgép-méret támogatja a rendszer nélküli operációs rendszert, de csak 86 GB gyorsítótármérettel rendelkezik. Ez a konfiguráció alapértelmezés szerint felügyelt lemezeket adna meg, ha a felhasználó nem ad meg explicit módon. Ha egy felhasználó kifejezetten a rendszerkézbesítési operációs rendszert kéri, érvényesítési hibát kap.

Ha egy felhasználó ugyanezt a Standard_DS2_v2 60 GB-os operációsrendszer-lemezzel kéri, ez a konfiguráció alapértelmezés szerint egy operációs rendszeré: a kért 60 GB-os méret kisebb, mint a maximális 86 GB-os gyorsítótár mérete.

A Standard_D8s_v3 100 GB-os operációsrendszer-lemezzel való használata esetén ez a virtuálisgép-méret támogatja a rendszer nélküli operációs rendszert, és 200 GB gyorsítótár-területtel rendelkezik. Ha a felhasználó nem adja meg az operációsrendszer-lemez típusát, a csomópontkészlet alapértelmezés szerint a rendszer a hosszútűnés operációs rendszert kapja meg. 

Aphemeral operációs rendszer használatához az Azure CLI legalább 2.15.0-s verziója szükséges.

### <a name="use-ephemeral-os-on-new-clusters"></a>Aphemeral operációs rendszer használata új fürtökön

Konfigurálja a fürtöt úgy, hogy a fürt létrehozásakor használjon hosszútű operációsrendszer-lemezeket. A `--node-osdisk-type` jelölővel állítsa be aphemerális operációs rendszert az új fürt operációsrendszer-lemezének típusaként.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Ha normál fürtöt szeretne létrehozni hálózatra csatlakoztatott operációsrendszer-lemezekkel, ezt a megadásával adhatja `--node-osdisk-type=Managed` meg. Dönthet úgy is, hogy az alább látható módon további operációsrendszer-csomópontkészleteket ad hozzá.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Aphemeral operációs rendszer használata meglévő fürtökön
Konfigurálja az új csomópontkészletet a hosszútévő operációsrendszer-lemezek használatára. A `--node-osdisk-type` jelölővel állítsa be az operációsrendszer-lemez típusát az adott csomópontkészlet operációsrendszer-lemezének típusaként.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> A rendszer nélküli operációs rendszerekkel virtuálisgép- és példányrendszerképeket helyezhet üzembe a virtuálisgép-gyorsítótár méretének megfelelően. Az AKS esetében az alapértelmezett csomópont operációsrendszer-lemezkonfigurációja 128 GB-ot használ, ami azt jelenti, hogy olyan virtuálisgép-méretre van szükség, amelynek gyorsítótára nagyobb, mint 128 GB. Az alapértelmezett Standard_DS2_v2 a gyorsítótár mérete 86 GB, ami nem elég nagy. A Standard_DS3_v2 172 GB gyorsítótármérettel rendelkezik, ami elég nagy. Az operációsrendszer-lemez alapértelmezett méretét a használatával is `--node-osdisk-size` csökkentheti. Az AKS-lemezképek minimális mérete 30 GB. 

Ha hálózatra csatlakoztatott operációsrendszer-lemezekkel szeretne csomópontkészleteket létrehozni, ezt a megadásával adhatja `--node-osdisk-type Managed` meg.

## <a name="custom-resource-group-name"></a>Egyéni erőforráscsoport neve

Amikor üzembe helyez egy Azure Kubernetes Service fürtöt az Azure-ban, egy második erőforráscsoport jön létre a munkavégző csomópontokhoz. Alapértelmezés szerint az AKS a csomópont erőforráscsoportjának adja a nevet, de a saját nevét `MC_resourcegroupname_clustername_location` is meg lehet adni.

Saját erőforráscsoport-név megadásához telepítse az aks-preview Azure CLI-bővítmény 0.3.2-es vagy újabb verzióját. Az Azure CLI használatával a parancs paraméterének használatával adjon meg egy egyéni nevet `--node-resource-group` `az aks create` az erőforráscsoport számára. Ha egy AKS Azure Resource Manager fürt üzembe helyezéséhez sablont használ, az erőforráscsoport nevét a tulajdonság használatával `nodeResourceGroup` határozhatja meg.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

A másodlagos erőforráscsoportot automatikusan az Azure erőforrás-szolgáltatója hozta létre a saját előfizetésében. Az egyéni erőforráscsoport nevét csak a fürt létrehozásakor adhatja meg. 

A csomópont-erőforráscsoporttal való munka során ne feledje, hogy nem tudja:

- Adjon meg egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
- Adjon meg egy másik előfizetést a csomópont-erőforráscsoporthoz.
- A fürt létrehozása után módosítsa a csomópont erőforráscsoportjának nevét.
- Adja meg a csomópont-erőforráscsoportban található felügyelt erőforrások nevét.
- A csomópont-erőforráscsoportban található felügyelt erőforrások Azure által létrehozott címkéinek módosítása vagy törlése.

## <a name="next-steps"></a>Következő lépések

- Ismerje [meg, hogyan frissítheti a fürtben található](node-image-upgrade.md) csomópont-rendszerképeket.
- A fürt a Kubernetes legújabb verziójára való frissítésének mikéntjéhez tekintse meg az [Azure Kubernetes Service- (AKS-)](upgrade-cluster.md) fürt frissítése című útmutatót.
- További információ a és a [ `containerd` Kubernetesről](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Az [AKS-ről gyakran feltett](faq.md) kérdések listájában választ találhat néhány gyakori AKS-kérdésre.
- További információ [aphemerális operációsrendszer-lemezekről.](../virtual-machines/ephemeral-os-disks.md)


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
