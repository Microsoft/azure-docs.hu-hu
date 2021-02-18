---
title: A tárolók Azure Monitorának frissítése a metrikák számára | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan frissítheti Azure Monitor a tárolók számára az összesített metrikák vizsgálatát és riasztását támogató egyéni metrikák funkció engedélyezéséhez.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 37c19cd074e9ce1985d5d0e82137d8603913d4bd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615298"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>A tárolókhoz készült Azure Monitor frissítése a metrikák engedélyezéséhez

A tárolók Azure Monitor támogatja az Azure Kubernetes Services (ak) és az Azure arc által engedélyezett Kubernetes-fürtök csomópontjainak és hüvelyének begyűjtését, valamint a Azure Monitor metrikák tárolóba írását. Ennek a változásnak a célja, hogy jobb ütemezést nyújtson az összesített számítások (AVG, Darabszám, max. min.) bemutatása a teljesítménymutatók esetében, támogatja a teljesítmény-diagramok Azure Portal-irányítópultokon való rögzítését, valamint a metrikai riasztások támogatását.

>[!NOTE]
>Ez a funkció jelenleg nem támogatja az Azure Red Hat OpenShift-fürtöket.
>

A szolgáltatás részeként a következő metrikák engedélyezettek:

| Metrikai névtér | Metric | Leírás |
|------------------|--------|-------------|
| Bepillantást nyerhet. tároló/csomópontok | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | A *csomópont* -metrikák közé tartoznak a *gazdagép* dimenzióként. A következőket is tartalmazzák:<br> a csomópont neve a *gazdagép* dimenziójának értékeként. |
| Bepillantást nyerhet. tároló/hüvely | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | A *Pod* -metrikák a következő dimenziókat tartalmazzák: ControllerName, Kubernetes névtér, név, fázis. |
| Bepillantást nyerhet. tároló/tárolók | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Bepillantást nyerhet. tároló/persistentvolumes | pvUsageExceededPercentage | |

Ezen új képességek támogatásához egy új, tárolóban lévő ügynök szerepel a kiadásban, a **Microsoft/OMS: ciprod05262020** for AK és a **Microsoft/OMS: Ciprod09252020** for Azure arc enabled Kubernetes-fürtökben. Az AK új központi telepítései automatikusan tartalmazzák ezt a konfigurációs változást és képességet. A fürt frissítése a szolgáltatás támogatásához a Azure Portal, Azure PowerShell vagy az Azure CLI használatával végezhető el. Azure PowerShell és parancssori felülettel. Ezt a fürtöt vagy az előfizetésben lévő összes fürt számára engedélyezheti.

Bármelyik folyamat hozzárendeli a figyelési **metrika közzétevői** szerepkörét a fürt egyszerű vagy felhasználó által hozzárendelt MSI-fiókjához a figyelési bővítményhez, így az ügynök által gyűjtött adatok közzétehetők a fürtök erőforrásában. A monitorozási metrikák közzétevője csak az erőforráshoz tartozó mérőszámok leküldésére jogosult, nem változtathat meg semmilyen állapotot, nem frissítheti az erőforrást, illetve nem olvashatja el az adatokat. További információ a szerepkörről: a [metrikák közzétevői szerepkörének figyelése](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). A figyelési mérőszámok közzétevői szerepkörének követelménye nem alkalmazható az Azure arc-kompatibilis Kubernetes-fürtökre.

> [!IMPORTANT]
> A frissítés nem szükséges az Azure arc-kompatibilis Kubernetes-fürtökhöz, mivel azok már rendelkeznek a minimálisan szükséges ügynök-verzióval.

## <a name="prerequisites"></a>Előfeltételek

A fürt frissítése előtt erősítse meg a következőket:

* Az egyéni metrikák csak az Azure-régiók egy részhalmazában érhetők el. A támogatott régiók listáját [itt](../essentials/metrics-custom-overview.md#supported-regions)dokumentáljuk.

* Az AK-fürterőforrás **[tulajdonosi](../../role-based-access-control/built-in-roles.md#owner)** szerepkörének tagja, hogy engedélyezze a Node és a pod egyéni teljesítmény-mérőszámok gyűjteményét. Ez a követelmény nem vonatkozik az Azure arc-kompatibilis Kubernetes-fürtökre.

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Fürt frissítése a Azure Portal

A tárolók Azure Monitor által figyelt meglévő AK-fürtök esetében, **Miután kiválasztotta** a fürtöt, hogy az állapotát a többfürtes nézetből Azure monitor vagy közvetlenül a fürtből tekintse meg, a bal oldali ablaktáblán található elemzések megadásával tekintse meg a portál felső részén látható szalagcímet.

![Az AK-fürt szalagcímének frissítése Azure Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Ha az **Engedélyezés** gombra kattint, a rendszer elindítja a fürt frissítésének folyamatát. Ez a folyamat több másodpercig is eltarthat, és a menü értesítések részén nyomon követheti a folyamat állapotát.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Az összes fürt frissítése a bash használatával az Azure Command shellben

A következő lépésekkel frissítheti az előfizetésben lévő összes fürtöt a bash használatával az Azure Command shellben.

1. Futtassa az alábbi parancsot az Azure CLI használatával.  Szerkessze a **subscriptionId** értékét az AK-fürt **AK-áttekintés** oldalának értékével.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    A konfiguráció módosítása néhány másodpercig is eltarthat. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Frissítés egy fürtön az Azure CLI használatával

A következő lépésekkel frissítheti az előfizetéséhez tartozó adott fürtöt az Azure CLI használatával.

1. Futtassa az alábbi parancsot az Azure CLI használatával. Szerkessze a **subscriptionId**, a **ResourceGroupName** és a **clusterName** értékeit az AK-fürt **AK-áttekintés** lapján található értékek használatával.  A **clientIdOfSPN** értékének lekéréséhez a parancs az `az aks show` alábbi példában látható módon fog visszakerülni.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    A **clientIdOfSPNOrMsi** értékének lekéréséhez futtassa a parancsot az `az aks show` alábbi példában látható módon. Ha a **servicePrincipalProfile** objektum érvényes *ClientID* -értékkel rendelkezik, ezt használhatja. Ellenkező esetben, ha az *MSI* értékre van állítva, át kell adnia a ClientID a következőből: `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Az összes fürt frissítése Azure PowerShell használatával

A következő lépések végrehajtásával frissítheti az előfizetésben lévő összes fürtöt a Azure PowerShell használatával.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) a **mdm_onboarding_atscale.ps1** -szkriptet, és mentse egy helyi mappába a GitHub-adattárból.
2. Futtassa a következő parancsot a Azure PowerShell használatával.  Szerkessze a **subscriptionId** értékét az AK-fürt **AK-áttekintés** oldalának értékével.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    A konfiguráció módosítása néhány másodpercig is eltarthat. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Frissítés egy fürtön Azure PowerShell használatával

A következő lépések végrehajtásával frissítheti egy adott fürtöt a Azure PowerShell használatával.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) a **mdm_onboarding.ps1** -szkriptet, és mentse egy helyi mappába a GitHub-adattárból.

2. Futtassa a következő parancsot a Azure PowerShell használatával. Szerkessze a **subscriptionId**, a **ResourceGroupName** és a **clusterName** értékeit az AK-fürt **AK-áttekintés** lapján található értékek használatával.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    A konfiguráció módosítása néhány másodpercig is eltarthat. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Frissítés ellenőrzése

Miután a korábban ismertetett módszerek valamelyikével kezdeményezte a frissítést, használhatja Azure Monitor metrikák Explorert, és ellenőrizheti, hogy a **metrikai névtér** tartalmazza-e az **észlelt** adatokat. Ha igen, megkezdheti a [metrikus riasztások](../alerts/alerts-metric.md) beállítását, vagy rögzítheti a diagramokat az [irányítópultokon](../../azure-portal/azure-portal-dashboards.md).  
