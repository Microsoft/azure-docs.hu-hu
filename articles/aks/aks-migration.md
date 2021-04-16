---
title: Áttelepítés Azure Kubernetes Service (AKS)
description: Áttelepítés Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7856b2b0668eca7a172b738a7eeea640e466f003
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483112"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Áttelepítés Azure Kubernetes Service (AKS)

Ez az útmutató az AKS-hez (AKS Azure Kubernetes Service való sikeres migrálás tervezésének és végrehajtásának segítése érdekében az aktuális ajánlott AKS-konfiguráció részleteit tartalmazza. Bár ez a cikk nem fed le minden forgatókönyvet, a sikeres migrálás megtervezésével kapcsolatos részletesebb információkra mutató hivatkozásokat tartalmaz.

Ez a dokumentum a következő forgatókönyvek támogatásához nyújt segítséget:

* Tárolóba kell helyezni bizonyos alkalmazásokat, és át kell őket helyezni az [AKS-be a Azure Migrate.](../migrate/migrate-services-overview.md)
* Rendelkezésre állási csoportokkal [Virtual Machine Scale Sets.](../virtual-machine-scale-sets/overview.md) [](../virtual-machines/windows/tutorial-availability-sets.md)
* AKS-fürt áttelepítése standard [termékváltozatú terheléseltöltő használatára.](./load-balancer-standard.md)
* Áttelepítés a [Azure Container Service (ACS) – 2020. január 31-től](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) az AKS-re való kiesés.
* Áttelepítés [AKS-motorról AKS-re.](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
* Áttelepítés nem Azure-alapú Kubernetes-fürtökről AKS-re.
* Meglévő erőforrások áthelyezése egy másik régióba.

Áttelepítéskor győződjön meg arról, hogy a cél Kubernetes-verzió az AKS támogatott ablakában található. Előfordulhat, hogy a régebbi verziók nem a támogatott tartományon belül vannak, és az AKS-nek verziófrissítésre van szüksége. További információ: [Az AKS által támogatott Kubernetes-verziók.](./supported-kubernetes-versions.md)

Ha a Kubernetes újabb verziójára minkál, tekintse át a [Kubernetes](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)verzió- és verzióelágaszolás-támogatási szabályzatát.

A forgatókönyvtől függően számos nyílt forráskódú eszköz segíthet a migrálásban:

* [Velero](https://velero.io/) (a Kubernetes 1.7-es vagy újabb szükséges hozzá)
* [Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

Ebben a cikkben összefoglaljuk a migrálás részleteit a következő adatokat:

> [!div class="checklist"]
> * Alkalmazások tárolóba Azure Migrate 
> * AKS standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és üzletmenet-folytonosság
> * Állapot nélküli alkalmazások megfontolandó szempontjai
> * Állapot- és alkalmazásokkal kapcsolatos szempontok
> * A fürtkonfiguráció üzembe helyezése

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Alkalmazások Azure Migrate AKS-be való áttelepítése a Azure Migrate használatával

Azure Migrate platformot kínál a helyszíni Azure-kiszolgálók, -infrastruktúra, -alkalmazások és -adatok értékeléséhez és az Azure-ba való áttelepítéshez. Az AKS-hez a következő Azure Migrate használhatja:

* [Tárolóba ASP.NET alkalmazásokba és áttelepítés az AKS-be](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Java-webalkalmazások tárolóba való telepítése és áttelepítése az AKS-be](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS standard Load Balancer és Virtual Machine Scale Sets

Az AKS egy olyan felügyelt szolgáltatás, amely egyedi képességeket kínál, alacsonyabb felügyeleti többletterheléssel. Mivel az AKS felügyelt szolgáltatás, az AKS által támogatott régiók közül kell választania. [](./quotas-skus-regions.md) Előfordulhat, hogy módosítania kell a meglévő alkalmazásokat, hogy kifogástalan állapotúak maradjanak az AKS által felügyelt vezérlősíkon a meglévő fürtről az AKS-be való áttérés során.

Javasoljuk, hogy a Virtual Machine Scale Sets és [](../virtual-machine-scale-sets/index.yml) az [Azure standard Load Balancer](./load-balancer-standard.md) által biztosított AKS-fürtök használatával biztosítsa a következő funkciókat:
* [Több csomópontkészlet,](./use-multiple-node-pools.md)
* [Availability Zones:](../availability-zones/az-overview.md),
* [Engedélyezett IP-címtartományok,](./api-server-authorized-ip-ranges.md)
* [Automatikus fürtméretozó,](./cluster-autoscaler.md)
* [Azure Policy AKS-](../governance/policy/concepts/policy-for-kubernetes.md)és
* Egyéb új funkciók a kiadottak szerint.

A virtuálisgép-rendelkezésre állási csoportok által támogatott AKS-fürtök számos ilyen funkciót nem támogatnak. [](../virtual-machines/availability.md#availability-sets)

Az alábbi példa egy AKS-fürtöt hoz létre egy virtuálisgép- (VM-) méretezési csoport által létrehozott egycsomópontos készletben. A fürt:
* Standard terheléselosztást használ. 
* Engedélyezi az automatikus fürtméretozót a fürt csomópontkészletében.
* Legalább *1, de legfeljebb* *3* csomópontot állít be.

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

Fürtök áttelepítésekor előfordulhat, hogy külső Azure-szolgáltatásokat csatolt. Bár a következő szolgáltatások nem igényelnek erőforrás-használatot, a működés fenntartásához frissíteniük kell a korábbi és az új fürtök közötti kapcsolatokat.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Tárfiók
* Külső adatbázisok

## <a name="ensure-valid-quotas"></a>Érvényes kvóták biztosítása

Mivel a migrálás során más virtuális gépeket is üzembe helyez az előfizetésben, ellenőrizze, hogy a kvóták és a korlátok elegendőek-e ezekhez az erőforrásokhoz. Szükség esetén kérje a [vCPU-kvóta emelését.](../azure-portal/supportability/per-vm-quota-requests.md)

Előfordulhat, hogy növelnie [](../azure-portal/supportability/networking-quota-requests.md) kell a hálózati kvótákat, hogy ne kimerítse az IP-eket. További információ: Hálózat és [IP-tartományok az AKS-hez.](./configure-kubenet.md)

További információ: [Azure-előfizetések és -szolgáltatások korlátai.](../azure-resource-manager/management/azure-subscription-service-limits.md) Az aktuális kvóták ellenőrzéséhez a Azure Portal az előfizetések panelen [válassza](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)ki az előfizetését, majd válassza a **Használat + kvóták lehetőséget.**

## <a name="high-availability-and-business-continuity"></a>Magas rendelkezésre állás és üzletmenet-folytonosság

Ha az alkalmazás nem tudja kezelni az állásidőt, kövesse a magas rendelkezésre állású migrálási forgatókönyvek ajánlott eljárásait. További információ: Ajánlott eljárások az összetett üzletmenet-folytonosság tervezésére, a vészhelyreállításra és az üzemidő maximalizálására a Azure Kubernetes Service [(AKS) esetén.](./operator-best-practices-multi-region.md)

Az összetett alkalmazások esetében általában nem egyszerre, hanem idővel kell áttűnni, ami azt jelenti, hogy a régi és az új környezetnek esetleg a hálózaton keresztül kell kommunikálnia. Előfordulhat, hogy a korábban szolgáltatások használatával kommunikáló alkalmazásokat típusként kell elérhetővé tenni, és `ClusterIP` `LoadBalancer` megfelelően kell biztosítani őket.

Az áttelepítés befejezéséhez az ügyfeleket az AKS-on futó új szolgáltatásokra kell mutasson. Javasoljuk, hogy irányítsa át a forgalmat úgy, hogy a DNS úgy Load Balancer az AKS-fürt előtt található tartományra mutasson.

[Azure Traffic Manager](../traffic-manager/index.yml) a kívánt Kubernetes-fürthöz és alkalmazáspéldányhoz irányíthatja az ügyfeleket. Traffic Manager egy DNS-alapú forgalom-terheléselosztási rendszer, amely képes elosztani a hálózati forgalmat a régiók között. A legjobb teljesítmény és redundancia érdekében minden alkalmazásforgalmat a Traffic Manager, mielőtt az AKS-fürthöz kerül. 

Többfürtű üzemelő példány esetén az ügyfeleknek olyan dns-névhez kell Traffic Manager, amely az egyes AKS-fürtök szolgáltatásaira mutat. Definiálja ezeket a szolgáltatásokat a Traffic Manager használatával. Minden végpont a *szolgáltatás terheléselosztási IP-címe.* Ezzel a konfigurációval irányítsa az egyik Traffic Manager végpontról egy másik régióban található végpontra a hálózati forgalmat.

![AKS with Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md) másik lehetőség az AKS-fürtök forgalmának útválasztásához. A Azure Front Door Service a webes forgalom globális útválasztásának meghatározásához, kezeléséhez és monitorozásához optimalizálhatja a legjobb teljesítményt és az azonnali globális feladatátvételt a magas rendelkezésre állás érdekében. 

### <a name="considerations-for-stateless-applications"></a>Állapot nélküli alkalmazások megfontolandó szempontjai

Az állapot nélküli alkalmazásáttelepítés a legegyértelűbb eset:
1. Alkalmazza az erőforrás-definíciókat (YAML vagy Helm) az új fürtre.
1. Győződjön meg arról, hogy minden a várt módon működik.
1. Irányítsa át a forgalmat az új fürt aktiválásához.

### <a name="considerations-for-stateful-applications"></a>Állapot-állapotra vonatkozó alkalmazások megfontolandó szempontjai

Gondosan tervezze meg az állapot-állapotra vonatkozó alkalmazások migrálását, hogy elkerülje az adatvesztést vagy a váratlan leállást.

* Ha kötetet Azure Files, kötetként csatlakoztathatja a fájlmegosztást az új fürthöz. Lásd: [Statikus Azure Files csatlakoztatása kötetként.](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)
* Azure-Managed Disks csak akkor csatlakoztathatja a lemezt, ha nincs csatlakoztatva valamelyik virtuális géphez. Lásd: [Statikus Azure-lemez csatlakoztatása kötetként.](./azure-disk-volume.md#mount-disk-as-volume)
* Ha ezen módszerek közül egyik sem működik, használhat biztonsági mentési és visszaállítási beállításokat. Lásd: [Velero az Azure-ban.](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Azure Files

A lemezektől eltérően a Azure Files több gazdagéphez is egyidejűleg csatlakoztathatóak. Az AKS-fürtben az Azure és a Kubernetes nem akadályozza meg, hogy olyan podot hozzon létre, amit az AKS-fürt még használ. Az adatvesztés és a váratlan viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem egyidejűleg írnak ugyanazokba a fájlokba.

Ha az alkalmazás több replikát is képes ugyanannak a fájlmegosztásnak a kezelésére, kövesse az állapot nélküli migrálás lépéseit, és telepítse a YAML-definíciókat az új fürtön. 

Ha nem, az egyik lehetséges migrálási megközelítés a következő lépésekből áll:

1. Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
1. Az élő forgalom az új AKS-fürtre mutat.
1. Válassza le a régi fürtöt.

Ha egy üres megosztással szeretne kezdeni, és másolatot szeretne másolni a forrásadatokról, a parancsokkal átemelheti [`az storage file copy`](/cli/azure/storage/file/copy) az adatokat.


#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha meglévő állandó köteteket miképpenít át az AKS-be, általában a következő lépéseket fogja követni:

1. Az alkalmazásba ír a rendszer. 
    * Ez a lépés nem kötelező, és állásidőt igényel.
1. Pillanatképek készítése a lemezekről.
1. Hozzon létre új felügyelt lemezeket a pillanatképekből.
1. Állandó kötetek létrehozása az AKS-ban.
1. Frissítse a pod [specifikációit, hogy meglévő](./azure-disk-volume.md) köteteket használjanak a PersistentVolumeClaims (statikus kiépítés) helyett.
1. Az alkalmazás üzembe helyezése az AKS-be.
1. Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
1. Az élő forgalom az új AKS-fürtre mutat.

> [!IMPORTANT]
> Ha úgy dönt, hogy nem válogatja ki az írásokat, replikálni kell az adatokat az új üzemelő példányba. Ellenkező esetben kihagyja a lemez pillanatképének készítése után írt adatokat.

Néhány nyílt forráskódú eszköz segíthet felügyelt lemezek létrehozásában és kötetek Kubernetes-fürtök közötti áttelepítésében:

* [Az Azure CLI Disk Copy bővítménye](https://github.com/noelbundick/azure-cli-disk-copy-extension) átmásolja és átalakítja a lemezeket az erőforráscsoportok és az Azure-régiók között.
* [Az Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli) számba veszi az ACS Kubernetes-köteteket, és migrálja őket egy AKS-fürtbe.


### <a name="deployment-of-your-cluster-configuration"></a>A fürtkonfiguráció üzembe helyezése

Javasoljuk, hogy meglévő folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot használjon egy ismerten jó konfiguráció AKS-hez való üzembe helyezéséhez. Az Azure Pipelines használatával alkalmazásokat építhet ki és helyezhet üzembe [az AKS-ban.](/azure/devops/pipelines/ecosystems/kubernetes/aks-template) Klónozza a meglévő üzembe helyezési feladatokat, és győződjön meg arról, hogy az az `kubeconfig` új AKS-fürtre mutat.

Ha ez nem lehetséges, exportálja az erőforrás-definíciókat a meglévő Kubernetes-fürtből, majd alkalmazza őket az AKS-be. A használatával `kubectl` exportálhatja az objektumokat.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Meglévő erőforrások áthelyezése egy másik régióba

Előfordulhat, hogy az AKS-fürtöt egy másik, az [AKS által támogatott régióba szeretné áthelyezni.][region-availability] Javasoljuk, hogy hozzon létre egy új fürtöt a másik régióban, majd telepítse az erőforrásokat és alkalmazásokat az új fürtön. 

Továbbá, ha rendelkezik olyan szolgáltatásokkal, mint az [Azure Dev Spaces][azure-dev-spaces] az AKS-fürtön, ezeket a szolgáltatásokat telepítenie és konfigurálnia kell a fürtön az új régióban.


Ebben a cikkben összefoglalta a migrálás részleteit a következő adatokat:

> [!div class="checklist"]
> * AKS standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és üzletmenet-folytonosság
> * Állapot nélküli alkalmazások megfontolandó szempontjai
> * Állapot-állapotra vonatkozó alkalmazások megfontolandó szempontjai
> * A fürtkonfiguráció üzembe helyezése


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
