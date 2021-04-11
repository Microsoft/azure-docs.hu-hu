---
title: Migrálás az Azure Kubernetes szolgáltatásba (ak)
description: Migrálás az Azure Kubernetes szolgáltatásba (ak).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: d5e3543fd6b7cd1b5534d6e363e51f1778cc7fc9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012127"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrálás az Azure Kubernetes szolgáltatásba (ak)

Az Azure Kubernetes Service (ak) szolgáltatásba való sikeres áttelepítés megtervezéséhez és végrehajtásához ez az útmutató ismerteti az aktuálisan javasolt AK-konfiguráció részleteit. Habár ez a cikk nem fedi le az összes forgatókönyvet, hivatkozásokat tartalmaz a sikeres áttelepítés megtervezéséhez szükséges részletes információkra.

Ez a dokumentum a következő helyzetekben nyújt segítséget:

* Containerizing bizonyos alkalmazásokat, és migrálja őket az AK-ba a [Azure Migrate](../migrate/migrate-services-overview.md)használatával.
* A [rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md) csoportok által támogatott AK-fürt áttelepítése [Virtual Machine Scale Setsra](../virtual-machine-scale-sets/overview.md).
* AK-fürt áttelepítése [szabványos SKU Load Balancer](./load-balancer-standard.md)használatára.
* Migrálás [Azure Container Serviceról (ACS) – 2020 és AK között, január 31-ig](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) .
* Migrálás az [AK-motorról](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) az AK-ra.
* Migrálás nem Azure-alapú Kubernetes-fürtökről AK-ra.
* Meglévő erőforrások áthelyezése egy másik régióba.

Az áttelepítés során győződjön meg arról, hogy a cél Kubernetes verziója az AK-hoz támogatott ablakon belül van. Előfordulhat, hogy a régebbi verziók nem a támogatott tartományon belül vannak, és az AK-ban a verziófrissítést is támogatni kell. További információ: az [AK által támogatott Kubernetes-verziók](./supported-kubernetes-versions.md).

Ha a Kubernetes egy újabb verziójára végez áttelepítést, tekintse át a [Kubernetes verziójának és verziójának döntési támogatási szabályzatát](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Számos nyílt forráskódú eszköz segíthet az áttelepítés során a forgatókönyvtől függően:

* [Velero](https://velero.io/) (Kubernetes 1.7 +) szükséges
* [Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli)
* [Újraeltolás](https://github.com/mhausenblas/reshifter)

Ebben a cikkben a következő áttelepítési adatokat összegzi:

> [!div class="checklist"]
> * Containerizing alkalmazások Azure Migrate 
> * AK standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és Üzletmenet-folytonosság
> * Állapot nélküli alkalmazások szempontjai
> * Az állapot-nyilvántartó alkalmazások szempontjai
> * A fürt konfigurációjának üzembe helyezése

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Alkalmazások migrálása a Azure Migrate használatával az AK-ra

A Azure Migrate egységes platformot biztosít az Azure helyszíni kiszolgálókra, infrastruktúrára, alkalmazásokra és adatszolgáltatásokra való felértékeléséhez és átköltöztetéséhez. Az AK esetében a Azure Migrate a következő feladatokhoz használható:

* [Tárolóba helyezése ASP.NET alkalmazások és Migrálás AK-ra](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Java-webalkalmazások tárolóba helyezése és migrálása AK-ra](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AK standard Load Balancer és Virtual Machine Scale Sets

Az AK egy felügyelt szolgáltatás, amely az alacsonyabb felügyeleti terhelést biztosító egyedi képességeket kínál. Mivel az AK felügyelt szolgáltatás, ki kell választania azokat a [régiókat](./quotas-skus-regions.md) , amelyeket az AK támogat. Előfordulhat, hogy módosítania kell a meglévő alkalmazásait, hogy azok egészségesek maradjanak az AK által felügyelt vezérlési síkon a meglévő fürtről AK-ra való áttérés során.

Javasoljuk, hogy [Virtual Machine Scale sets](../virtual-machine-scale-sets/index.yml) és az [Azure standard Load BALANCER](./load-balancer-standard.md) által támogatott AK-fürtöket használjon, így biztosítva a következő funkciók megszerzését:
* [Több csomópontos készlet](./use-multiple-node-pools.md),
* [Availability Zones](../availability-zones/az-overview.md),
* [Jóváhagyott IP-címtartományok](./api-server-authorized-ip-ranges.md),
* [Fürt autoskálázása](./cluster-autoscaler.md),
* [Azure Policy AK](../governance/policy/concepts/policy-for-kubernetes.md)-hoz, és
* Egyéb új funkciók, mint azok kiadása.

A [virtuális gépek rendelkezésre állási csoportjai](../virtual-machines/availability.md#availability-sets) által támogatott AK-fürtök számos ilyen funkció esetében nem támogatottak.

A következő példa egy AK-fürtöt hoz létre egyetlen, virtuálisgép-méretezési csoporttal támogatott egyetlen csomópontos készlettel. A fürt:
* Standard Load balancert használ. 
* Engedélyezi a fürthöz tartozó csomópontok automéretezőjét a fürt csomópontjain.
* Legalább *1* és legfeljebb *3* csomópontot állít be.

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

Fürtök áttelepítésekor előfordulhat, hogy külső Azure-szolgáltatásokat csatlakoztatott. Noha a következő szolgáltatásokhoz nincs szükség az erőforrás-kikapcsolódásra, a funkciók fenntartása érdekében frissíteniük kell a korábbi és az új fürtök kapcsolatait.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Tárfiók
* Külső adatbázisok

## <a name="ensure-valid-quotas"></a>Érvényes kvóták biztosítása

Mivel az áttelepítés során más virtuális gépek is üzembe helyezhetők az előfizetésben, ellenőriznie kell, hogy a kvóták és a korlátozások elégségesek-e ezekhez az erőforrásokhoz. Ha szükséges, a [vCPU-kvóta](../azure-portal/supportability/per-vm-quota-requests.md)növelését kell kérnie.

Előfordulhat, hogy a [hálózati kvóták](../azure-portal/supportability/networking-quota-requests.md) növelését kell megadnia, hogy ne legyenek kimerítve az IP-címek. További információ: [hálózati és IP-címtartományok az AK-](./configure-kubenet.md)hoz.

További információ: Azure- [előfizetés és-szolgáltatási korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md). Az aktuális kvóták megtekintéséhez a Azure Portal lépjen az [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)panelre, válassza ki az előfizetését, majd válassza a **használat + kvóták** lehetőséget.

## <a name="high-availability-and-business-continuity"></a>Magas rendelkezésre állás és Üzletmenet-folytonosság

Ha az alkalmazás nem tudja kezelni az állásidőt, a magas rendelkezésre állású áttelepítési forgatókönyvekhez ajánlott eljárásokat kell követnie. További információk az [összetett Üzletmenet-folytonosság megtervezésével, a vész-helyreállítással és az Azure Kubernetes szolgáltatásban (ak) való maximális üzemidővel kapcsolatos ajánlott eljárásokkal](./operator-best-practices-multi-region.md)kapcsolatban.

Az összetett alkalmazások esetében általában az idő múlásával, nem pedig egyszerre, hanem a régi és az új környezettel kell kommunikálni a hálózaton keresztül. Előfordulhat, hogy az olyan alkalmazásokat, amelyeket korábban a `ClusterIP` szolgáltatásokkal kommunikálnak, a típusnak meg kell tenni, `LoadBalancer` és megfelelő védelemmel kell rendelkeznie.

Az áttelepítés befejezéséhez az ügyfeleket az AK-on futó új szolgáltatásokra kell irányítani. Javasoljuk, hogy a forgalom átirányításához frissítse a DNS-t úgy, hogy az AK-fürt előtt ülve Load Balancer mutasson.

Az [Azure Traffic Manager](../traffic-manager/index.yml) az ügyfeleket a kívánt Kubernetes-fürtre és alkalmazás-példányra irányíthatja. Traffic Manager egy DNS-alapú forgalom terheléselosztó, amely a régiók közötti hálózati forgalmat terjesztheti. A legjobb teljesítmény és redundancia érdekében a Traffic Manageron keresztül irányítsa az összes alkalmazás forgalmát, mielőtt az AK-fürtre kerül. 

Egy több fürtből álló környezetben az ügyfeleknek csatlakozniuk kell egy Traffic Manager DNS-névhez, amely az egyes AK-fürtök szolgáltatásaira mutat. Adja meg ezeket a szolgáltatásokat Traffic Manager végpontok használatával. Mindegyik végpont a *szolgáltatás terheléselosztó IP-címe*. Ezt a konfigurációt használva irányíthatja a hálózati forgalmat az egyik régióban lévő Traffic Manager végpontról a végpontra egy másik régióban.

![AK Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Az [Azure bejárati ajtó szolgáltatás](../frontdoor/front-door-overview.md) egy másik lehetőség az AK-fürtök forgalmának útválasztására. Az Azure bevezetési szolgáltatásával megadhatja, kezelheti és figyelheti a webes forgalom globális útválasztását, ha optimalizálja a legjobb teljesítményt és azonnali globális feladatátvételt a magas rendelkezésre állás érdekében. 

### <a name="considerations-for-stateless-applications"></a>Állapot nélküli alkalmazások szempontjai

Az állapot nélküli alkalmazások migrálása a legegyszerűbb eset:
1. Alkalmazza az erőforrás-definíciókat (YAML vagy Helm) az új fürtre.
1. Győződjön meg róla, hogy minden a várt módon működik-e.
1. Irányítsa át a forgalmat az új fürt aktiválásához.

### <a name="considerations-for-stateful-applications"></a>Az állapot-nyilvántartó alkalmazások szempontjai

Gondosan tervezze meg az állapot-nyilvántartó alkalmazások áttelepítését, hogy elkerülje az adatvesztést vagy a váratlan állásidőt.

* Ha Azure Files használ, a fájlmegosztást kötetként is csatlakoztathatja az új fürthöz. Lásd: [statikus Azure Files csatlakoztatása kötetként](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* Ha az Azure Managed Disks-t használja, csak akkor csatlakoztathatja a lemezt, ha az nincs csatlakoztatva a virtuális géphez. Lásd: [statikus Azure-lemez csatlakoztatása kötetként](./azure-disk-volume.md#mount-disk-as-volume).
* Ha egyik módszer sem működik, használhatja a biztonsági mentési és visszaállítási lehetőségeket. Lásd: [Velero az Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)-ban.

#### <a name="azure-files"></a>Azure Files

A lemezekkel ellentétben Azure Files egyidejűleg több gazdagéphez is csatlakoztatható. Az AK-fürtben az Azure és a Kubernetes nem akadályozza meg, hogy olyan Pod-t hozzon létre, amelyet az AK-fürt továbbra is használ. Az adatvesztés és a váratlan viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem írnak egyszerre ugyanarra a fájlra.

Ha az alkalmazás több replikát is képes tárolni, amelyek ugyanarra a megosztásra mutatnak, kövesse az állapot nélküli áttelepítési lépéseket, és telepítse a YAML-definíciókat az új fürtre. 

Ha nem, az egyik lehetséges áttelepítési módszer a következő lépéseket foglalja magában:

1. Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
1. Mutasson az élő adatforgalomra az új AK-fürtre.
1. Válassza le a régi fürtöt.

Ha üres megosztással szeretne kezdeni, és másolatot készít a forrásadatokről, a [`az storage file copy`](/cli/azure/storage/file/copy) parancsok segítségével áttelepítheti az adatait.


#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha a meglévő állandó köteteket AK-ra telepíti át, akkor általában az alábbi lépéseket kell követnie:

1. A fokozatos leválasztása az alkalmazásba ír. 
    * Ez a lépés nem kötelező, és leállást igényel.
1. Pillanatképek készítése a lemezekről.
1. Hozzon létre új felügyelt lemezeket a pillanatképek közül.
1. Állandó kötetek létrehozása az AK-ban.
1. A pod-specifikációk frissítése a [meglévő kötetek használatára](./azure-disk-volume.md) a PersistentVolumeClaims helyett (statikus kiépítés).
1. Az alkalmazás üzembe helyezése az AK-ban.
1. Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
1. Mutasson az élő adatforgalomra az új AK-fürtre.

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

Érdemes lehet áthelyezni az AK-fürtöt egy [másik, AK által támogatott régióba][region-availability]. Javasoljuk, hogy hozzon létre egy új fürtöt a másik régióban, majd telepítse az erőforrásokat és az alkalmazásokat az új fürtre. 

Továbbá, ha olyan szolgáltatásokkal rendelkezik, mint például az AK-fürtön futó [Azure dev-tárhelyek][azure-dev-spaces] , telepítenie és konfigurálnia kell ezeket a szolgáltatásokat a fürtön az új régióban.


Ebben a cikkben a következő áttelepítési adatokat összegzi:

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
