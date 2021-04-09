---
title: Migrálás az Azure Red Hat OpenShift 3.11-es verziójáról az Azure Red Hat OpenShift 4-es verziójára
description: Migrálás az Azure Red Hat OpenShift 3.11-es verziójáról az Azure Red Hat OpenShift 4-es verziójára
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: Migrálás, ARO, openshift, Red Hat
ms.openlocfilehash: 371672de83a6d745d7b367f8327a64e11059923e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653278"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrálás az Azure Red Hat OpenShift 3,11-ről az Azure Red Hat OpenShift 4-re

Az Azure Red Hat OpenShift a OpenShift 4 rendszeren a Red hat Core operációs rendszerre, a privát fürtökre, a saját virtuális hálózatok támogatására és a teljes fürt rendszergazdai szerepkörére épülő Kubernetes 1,16. Emellett számos új funkció érhető el, például az operátori keretrendszer, az operátori központ és a OpenShift Service Mesh támogatása.

Az Azure Red Hat OpenShift 3,11-ről az Azure Red Hat OpenShift 4-re való áttéréshez mindenképpen tekintse át a [tárolás, a hálózatkezelés, a naplózás, a biztonság és a figyelés különbségeit](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

Ebben a cikkben bemutatjuk, hogyan telepíthet át egy Azure Red Hat OpenShift 3,11-fürtről egy Azure Red Hat 4-fürtre.

> [!NOTE]
> A Red Hat OpenShift áttelepítési eszközök, például a Vezérlőpult-áttelepítési segítségnyújtó eszköz és a fürtözött alkalmazás-áttelepítési eszköz (CAM) nem használhatók az Azure Red Hat OpenShift 3,11-fürtökkel.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő Azure Red Hat OpenShift 3,11-fürttel.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Cél Azure Red Hat OpenShift 4-fürt létrehozása

Először [hozza létre az Azure Red Hat OpenShift 4 fürtöt](tutorial-create-cluster.md) , amelyet célként használni szeretne. Itt az alapszintű konfigurációt fogjuk használni. Ha más beállítások érdeklik, tekintse meg az [Azure Red Hat OpenShift 4-fürt létrehozása című oktatóanyagot](tutorial-create-cluster.md).

Hozzon létre két üres alhálózattal rendelkező virtuális hálózatot a fő-és munkavégző csomópontokhoz.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Ezután használja a következő parancsot a fürt létrehozásához.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>A TARGET OpenShift 4 fürt konfigurálása

### <a name="authentication"></a>Hitelesítés

Ahhoz, hogy a felhasználók kapcsolatba lépjenek az Azure Red Hat OpenShift, először hitelesíteniük kell magukat a fürtön. A hitelesítési réteg azonosítja az Azure Red Hat OpenShift API-hoz érkező kérésekhez társított felhasználót. Az engedélyezési réteg ezután a kérelmező felhasználóval kapcsolatos információkat használ annak megállapítására, hogy a kérelem engedélyezett-e.

Egy Azure Red Hat OpenShift-fürt létrehozásakor létrejön egy ideiglenes rendszergazda felhasználó. [Kapcsolódjon a fürthöz](tutorial-connect-cluster.md), vegyen fel felhasználókat és csoportokat, és [konfigurálja a megfelelő engedélyeket](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) mindkettőhöz.

### <a name="networking"></a>Hálózatkezelés

Az Azure Red Hat OpenShift 4 néhány különböző operátort használ a hálózat beállításához a fürtben: a [fürt hálózati kezelője](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), a [DNS-operátor](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)és a [Bejövő forgalom kezelője](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html). Az Azure Red Hat OpenShift 4 fürt hálózatkezelésének beállításával kapcsolatos további információkért tekintse meg a hálózatkezelés [diagramot](concepts-networking.md) és a [hálózatkezelés megismerését](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)ismertető témakört.

### <a name="storage"></a>Tárolás
Az Azure Red Hat OpenShift 4 a következő PersistentVolume beépülő modulokat támogatja:

- AWS rugalmas blokk-tároló (EBS)
- Azure-lemez
- Azure-fájl
- GCE állandó lemez
- HostPath
- iSCSI
- Helyi kötet
- NFS
- Red Hat OpenShift tároló tároló

A tárolási típusok konfigurálásával kapcsolatos információkért lásd: az [állandó tároló konfigurálása](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Regisztrációs adatbázis

Az Azure Red Hat OpenShift 4 képes képeket készíteni a forráskódból, üzembe helyezheti és felügyelheti az életciklusát. Ennek engedélyezéséhez az Azure Red Hat OpenShift 4 [belső, integrált tároló lemezkép-regisztrációs adatbázist](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) biztosít, amely az Azure Red Hat OpenShift-környezetben üzembe helyezhető a lemezképek helyi kezeléséhez.

Ha külső beállításjegyzékeket használ, például a [Azure Container Registry](../container-registry/index.yml), a [Red Hat Quay-jegyzékeket](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)vagy egy [hitelesítést engedélyező Red Hat-regisztrációt](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options), kövesse a hitelesítő adatok megadásához szükséges lépéseket a fürt számára, hogy a fürt hozzáférjen a tárházhoz.

### <a name="monitoring"></a>Figyelés

Az Azure Red Hat OpenShift tartalmaz egy előre konfigurált, előre telepített és önfrissítő figyelési veremet, amely a Prometheus nyílt forráskódú projekten és a tágabb ökoszisztémán alapul. Lehetővé teszi a fürt összetevőinek figyelését, és riasztásokat tartalmaz, amelyek azonnal értesítik a fürt rendszergazdáit az esetleges problémákról és a Grafana-irányítópultokról. A fürt figyelési vereme csak az Azure Red Hat OpenShift-fürtök figyelésére használható. További információ: [a fürtök figyelése az Azure Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Ha [Azure monitort használ a tárolók számára az Azure Red Hat OpenShift 3,11](../azure-monitor/containers/container-insights-azure-redhat-setup.md)-hez, Azure monitor engedélyezheti a tárolók számára az [Azure Red Hat OpenShift 4 fürtöket](../azure-monitor/containers/container-insights-azure-redhat4-setup.md) , és továbbra is használhatja ugyanazt a log Analytics munkaterületet.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>A DNS-vagy terheléselosztó-konfiguráció áthelyezése az új fürtre

Ha Azure-Traffic Manager használ, a végpontok hozzáadásával tekintse át a célként megadott fürtöt, és rangsorolja ezeket a végpontokat.

## <a name="deploy-application-to-your-target-cluster"></a>Alkalmazás üzembe helyezése a cél fürtön

Ha a célként megadott fürtöt megfelelően konfigurálta a munkaterheléshez, [kapcsolódjon a fürthöz](tutorial-connect-cluster.md) , és hozza létre a szükséges alkalmazásokat, összetevőket vagy szolgáltatásokat a projektekhez. Az Azure Red Hat OpenShift lehetővé teszi, hogy ezeket a git, a Container images, a Red Hat Developer Catalog, a Docker, a YAML/JSON definíciója, vagy egy adatbázis-szolgáltatás kiválasztásával hozza létre a katalógusból.

## <a name="delete-your-source-cluster"></a>A forrásoldali fürt törlése
Miután meggyőződött róla, hogy az Azure Red Hat OpenShift 4 fürt megfelelően van beállítva, törölje az Azure Red Hat OpenShift 3,11-fürtöt.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Következő lépések
Tekintse meg a Red Hat [OpenShift dokumentációját](https://docs.openshift.com/container-platform/4.6/welcome/index.html).
