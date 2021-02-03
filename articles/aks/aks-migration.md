---
title: Migrálás az Azure Kubernetes szolgáltatásba (ak)
description: Migrálás az Azure Kubernetes szolgáltatásba (ak).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 5881d03603002cc8d5bef1680083f6b4145bc77f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526687"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrálás az Azure Kubernetes szolgáltatásba (ak)

Ez a cikk segítséget nyújt az Azure Kubernetes szolgáltatásba (ak) való sikeres áttelepítés megtervezéséhez és végrehajtásához. A legfontosabb döntések elvégzéséhez ez az útmutató részletesen ismerteti az AK jelenleg javasolt konfigurációját. Ez a cikk nem fedi le az összes forgatókönyvet, és ha szükséges, a cikk a sikeres áttelepítés megtervezésével kapcsolatos részletesebb információkra mutató hivatkozásokat tartalmaz.

Ez a dokumentum a következő forgatókönyvek támogatásához használható:

* [Rendelkezésre állási készletek](../virtual-machines/windows/tutorial-availability-sets.md) által támogatott AK-fürt áttelepítése [Virtual Machine Scale sets](../virtual-machine-scale-sets/overview.md)
* AK-fürt áttelepítése [szabványos SKU Load Balancer](./load-balancer-standard.md) használatára
* Migrálás [Azure Container Serviceról (ACS) – 2020 és AK között, január 31-ig](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)
* Migrálás az [AK-ból motorról](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) AK-ra
* Áttelepítés nem Azure-alapú Kubernetes-fürtökről AK-ra
* Meglévő erőforrások áthelyezése egy másik régióba

Az áttelepítés során győződjön meg arról, hogy a cél Kubernetes verziója az AK-hoz támogatott ablakon belül van. Ha régebbi verziót használ, előfordulhat, hogy nem a támogatott tartományon belül van, és az AK által támogatott verziófrissítési verzióra van szükség. További információért lásd: [AK támogatott Kubernetes-verziók](./supported-kubernetes-versions.md) .

Ha a Kubernetes egy újabb verziójára végez áttelepítést, tekintse át a [Kubernetes verziójának és verziójának döntési támogatási szabályzatát](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Számos nyílt forráskódú eszköz segíthet az áttelepítés során a forgatókönyvtől függően:

* [Velero](https://velero.io/) (Kubernetes 1.7 +) szükséges
* [Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli)
* [Újraeltolás](https://github.com/mhausenblas/reshifter)

Ebben a cikkben a következő áttelepítési adatokat összegzi:

> [!div class="checklist"]
> * AK standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és Üzletmenet-folytonosság
> * Állapot nélküli alkalmazások szempontjai
> * Az állapot-nyilvántartó alkalmazások szempontjai
> * A fürt konfigurációjának üzembe helyezése

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AK standard Load Balancer és Virtual Machine Scale Sets

Az AK egy felügyelt szolgáltatás, amely az alacsonyabb felügyeleti terhelést biztosító egyedi képességeket kínál. A felügyelt szolgáltatásnak köszönhetően olyan [régiókból](./quotas-skus-regions.md) kell kiválasztania, amelyek az AK által támogatottak. A meglévő fürtről az AK-ra való áttérés szükségessé teheti a meglévő alkalmazások módosítását, hogy azok egészségesek maradjanak az AK felügyelt vezérlési síkon.

Javasoljuk, hogy [Virtual Machine Scale sets](../virtual-machine-scale-sets/index.yml) és az [Azure standard Load BALANCER](./load-balancer-standard.md) által támogatott AK-fürtöket használjon, így biztosítva a [több csomópontos készlet](./use-multiple-node-pools.md), a [Availability Zones](../availability-zones/az-overview.md), az [engedélyezett IP-címtartományok](./api-server-authorized-ip-ranges.md), a [fürt autoskálázása](./cluster-autoscaler.md), [a Azure Policy for AK](../governance/policy/concepts/policy-for-kubernetes.md)és más új funkciók használatát.

A [virtuális gépek rendelkezésre állási csoportjai](../virtual-machines/availability.md#availability-sets) által támogatott AK-fürtök számos ilyen funkció esetében nem támogatottak.

A következő példa létrehoz egy AK-fürtöt egyetlen, virtuálisgép-méretezési csoporttal támogatott egycsomópontos készlettel. Standard Load balancert használ. Emellett lehetővé teszi a fürthöz tartozó automéretezőt a fürt csomópont-készletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Meglévő csatolt Azure-szolgáltatások

Előfordulhat, hogy a fürtök áttelepítésekor külső Azure-szolgáltatásokat csatlakoztatott. Ezek nem igénylik az erőforrás-használatot, de az előzőtől az új fürtökig frissíteniük kell a működés fenntartásához szükséges kapcsolatokat.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Tárfiók
* Külső adatbázisok

## <a name="ensure-valid-quotas"></a>Érvényes kvóták biztosítása

Mivel az áttelepítés során további virtuális gépek lesznek telepítve az előfizetésben, ellenőriznie kell, hogy a kvóták és a korlátozások elegendőek-e az adott erőforrásokhoz. Előfordulhat, hogy a [vCPU-kvóta](../azure-portal/supportability/per-vm-quota-requests.md)növelését kell kérnie.

Előfordulhat, hogy a [hálózati kvóták](../azure-portal/supportability/networking-quota-requests.md) növelését kell megadnia, hogy ne legyenek kimerítve az IP-címek. További információért lásd: [hálózati és IP-címtartományok](./configure-kubenet.md) .

További információ: Azure- [előfizetés és-szolgáltatási korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md). Az aktuális kvóták megtekintéséhez a Azure Portal lépjen az [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)panelre, válassza ki az előfizetését, majd válassza a **használat + kvóták** lehetőséget.

## <a name="high-availability-and-business-continuity"></a>Magas rendelkezésre állás és Üzletmenet-folytonosság

Ha az alkalmazás nem tudja kezelni az állásidőt, a magas rendelkezésre állású áttelepítési forgatókönyvekhez ajánlott eljárásokat kell követnie.  Az összetett üzletmenet folytonosságának megtervezése, a vész-helyreállítás és a hasznos üzemidő maximalizálása a jelen dokumentum hatókörén kívül esik.  További információ az [üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos ajánlott eljárásokról az Azure Kubernetes szolgáltatásban (ak)](./operator-best-practices-multi-region.md) .

Összetett alkalmazások esetében általában az idő múlásával, nem pedig egyszerre kell áttérnie. Ez azt jelenti, hogy a régi és az új környezetnek kommunikálnia kell a hálózaton keresztül. Előfordulhat, hogy a korábban a `ClusterIP` kommunikációhoz használt szolgáltatásokat be kell állítani, `LoadBalancer` és megfelelő védelemmel kell elvégezniük.

Az áttelepítés befejezéséhez az ügyfeleket az AK-on futó új szolgáltatásokra kell irányítani. Javasoljuk, hogy a forgalom átirányításához frissítse a DNS-t úgy, hogy az AK-fürt előtt található Load Balancer mutasson.

Az [Azure Traffic Manager](../traffic-manager/index.yml) az ügyfeleket a kívánt Kubernetes-fürtre és alkalmazás-példányra irányíthatja.  Traffic Manager egy DNS-alapú forgalom terheléselosztó, amely a régiók közötti hálózati forgalmat terjesztheti.  A legjobb teljesítmény és redundancia érdekében a Traffic Manageron keresztül irányítsa az összes alkalmazás forgalmát, mielőtt az AK-fürtre kerül.  Többfürtes telepítés esetén az ügyfeleknek csatlakozniuk kell egy Traffic Manager DNS-névhez, amely az egyes AK-fürtök szolgáltatásaira mutat. Adja meg ezeket a szolgáltatásokat Traffic Manager végpontok használatával. Mindegyik végpont a *szolgáltatás terheléselosztó IP-címe*. Ezt a konfigurációt használva irányíthatja a hálózati forgalmat az egyik régióban lévő Traffic Manager végpontról a végpontra egy másik régióban.

![AK Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Az [Azure bejárati ajtó szolgáltatás](../frontdoor/front-door-overview.md) egy másik lehetőség az AK-fürtök forgalmának útválasztására.  Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. 

### <a name="considerations-for-stateless-applications"></a>Állapot nélküli alkalmazások szempontjai

Az állapot nélküli alkalmazások migrálása a legegyszerűbb eset. Alkalmazza az erőforrás-definíciókat (YAML vagy Helm) az új fürtre, győződjön meg róla, hogy minden a várt módon működik-e, és irányítsa át a forgalmat az új fürt aktiválásához.

### <a name="considerations-for-stateful-applications"></a>Az állapot-nyilvántartó alkalmazások szempontjai

Gondosan tervezze meg az állapot-nyilvántartó alkalmazások áttelepítését, hogy elkerülje az adatvesztést vagy a váratlan állásidőt.

Ha Azure Files használ, a fájlmegosztást kötetként csatlakoztathatja az új fürthöz:
* [Statikus Azure Files csatlakoztatása kötetként](./azure-files-volume.md#mount-the-file-share-as-a-volume)

Ha az Azure Managed Disks-t használja, csak akkor csatlakoztathatja a lemezt, ha az nincs csatlakoztatva a virtuális géphez:
* [Statikus Azure-lemez csatlakoztatása kötetként](./azure-disk-volume.md#mount-disk-as-volume)

Ha egyik módszer sem működik, akkor biztonsági mentési és visszaállítási lehetőségeket is használhat:
* [Velero az Azure-ban](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Azure Files

A lemezekkel ellentétben Azure Files egyidejűleg több gazdagéphez is csatlakoztatható. Az AK-fürtben az Azure és a Kubernetes nem akadályozza meg, hogy olyan Pod-t hozzon létre, amelyet az ACS-fürt továbbra is használ. Az adatvesztés és a nem várt viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem írnak egyszerre ugyanarra a fájlra.

Ha az alkalmazás több replikát is képes tárolni, amelyek ugyanarra a megosztásra mutatnak, kövesse az állapot nélküli áttelepítési lépéseket, és telepítse a YAML-definíciókat az új fürtre. Ha nem, az egyik lehetséges áttelepítési módszer a következő lépéseket foglalja magában:

* Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
* Mutasson az élő adatforgalomra az új AK-fürtre.
* Válassza le a régi fürtöt.

Ha üres megosztással szeretne kezdeni, és másolatot készít a forrásadatokről, a [`az storage file copy`](/cli/azure/storage/file/copy) parancsok segítségével áttelepítheti az adatait.


#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha a meglévő állandó köteteket AK-ra telepíti át, akkor általában az alábbi lépéseket kell követnie:

* A fokozatos leválasztása az alkalmazásba ír. (Ez a lépés nem kötelező, és leállást igényel.)
* Pillanatképek készítése a lemezekről.
* Hozzon létre új felügyelt lemezeket a pillanatképek közül.
* Állandó kötetek létrehozása az AK-ban.
* A pod-specifikációk frissítése a [meglévő kötetek használatára](./azure-disk-volume.md) a PersistentVolumeClaims helyett (statikus kiépítés).
* Az alkalmazás üzembe helyezése az AK-ban.
* Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
* Mutasson az élő adatforgalomra az új AK-fürtre.

> [!IMPORTANT]
> Ha úgy dönt, hogy nem fokozatos leválasztása az írásokat, replikálnia kell az új központi telepítésre. Ellenkező esetben kihagyhatja a lemez-Pillanatképek elkészítése után írt adatfájlokat.

Egyes nyílt forráskódú eszközök segítségével felügyelt lemezeket hozhat létre és telepíthet át köteteket a Kubernetes-fürtök között:

* Az [Azure CLI lemez másolási bővítménye](https://github.com/noelbundick/azure-cli-disk-copy-extension) lemásolja és átalakítja a lemezeket az erőforráscsoportok és az Azure-régiók között.
* Az [Azure KUBE CLI-bővítménye](https://github.com/yaron2/azure-kube-cli) az ACS-Kubernetes köteteket sorolja fel, és egy AK-fürtbe telepíti őket.


### <a name="deployment-of-your-cluster-configuration"></a>A fürt konfigurációjának üzembe helyezése

Javasoljuk, hogy a meglévő folyamatos integrációs (CI) és a folyamatos kézbesítés (CD) folyamat használatával helyezzen üzembe egy ismert, jó konfigurációt az AK-ban. Az Azure-folyamatok segítségével alkalmazásokat hozhat [létre és helyezhet üzembe az AK](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)-ban. A meglévő üzembe helyezési feladatok klónozásával ellenőrizze, hogy `kubeconfig` az új AK-fürtre mutat-e.

Ha ez nem lehetséges, exportálja az erőforrás-definíciókat a meglévő Kubernetes-fürtből, majd alkalmazza őket az AK-ra. `kubectl`Az objektumok exportálására használható.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Meglévő erőforrások áthelyezése egy másik régióba

Érdemes lehet áthelyezni az AK-fürtöt egy [másik, AK által támogatott régióba][region-availability]. Azt javasoljuk, hogy hozzon létre egy új fürtöt a másik régióban, majd telepítse az erőforrásokat és az alkalmazásokat az új fürtre. Továbbá, ha olyan szolgáltatásokkal rendelkezik, mint például az AK-fürtön futó [Azure dev-tárhelyek][azure-dev-spaces] , az új régióban is telepítenie és konfigurálnia kell a szolgáltatásokat a fürtön.


Ebben a cikkben a következő áttelepítési adatokat összesítettük:

> [!div class="checklist"]
> * AK standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és Üzletmenet-folytonosság
> * Állapot nélküli alkalmazások szempontjai
> * Az állapot-nyilvántartó alkalmazások szempontjai
> * A fürt konfigurációjának üzembe helyezése


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
