---
title: Az Azure arc-adatkezelő üzembe helyezése | Közvetlen csatlakozási mód
description: Az adatvezérlő közvetlen kapcsolódási módban való üzembe helyezését ismerteti.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031487"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Az Azure arc-adatkezelő üzembe helyezése | Közvetlen csatlakozási mód

Ez a cikk bemutatja, hogyan helyezheti üzembe az Azure arc-adatkezelőt közvetlen kapcsolódási módban a funkció aktuális előnézetében. 

Az Azure arc-adatkezelő jelenleg a Azure Portalból hozható létre. Az Azure arc-kompatibilis adatszolgáltatások egyéb eszközei nem támogatják az adatvezérlő közvetlen csatlakozási módban történő létrehozását. Részletekért lásd: [ismert problémák – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Előfeltételek végrehajtása

Mielőtt elkezdené, ellenőrizze, hogy végrehajtotta-e az [adatvezérlő üzembe helyezése – közvetlen csatlakozási mód – előfeltételek](deploy-data-controller-direct-mode-prerequisites.md)című szakaszban ismertetett előfeltételeket.

Ez a cikk magas szinten ismerteti a következő feladatok végrehajtását:

1. Hozzon létre egy Azure arc-kompatibilis adatszolgáltatás-bővítményt. 
1. Hozzon létre egy egyéni helyet.
1. Telepítse az adatkezelőt a portálról.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Azure arc-kompatibilis adatszolgáltatási bővítmény létrehozása

Hozzon létre egy adatszolgáltatási bővítményt a k8s-bővítmény CLI használatával.

### <a name="set-environment-variables"></a>Környezeti változók beállítása

Állítsa be az alábbi környezeti változókat, amelyek ezután a következő lépésben lesznek felhasználva.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Az arc-adatszolgáltatások bővítményének létrehozása

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Az ív-adatszolgáltatások bővítményének telepítése néhány percet igénybe vehet.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Annak ellenőrzése, hogy az ív-adatszolgáltatások bővítmény létrejött-e

Ellenőrizheti, hogy a rendszer létrehozza-e az ív használatára képes adatszolgáltatások bővítményt a portálról, vagy közvetlenül csatlakozik az arc-kompatibilis Kubernetes-fürthöz. 

#### <a name="azure-portal"></a>Azure Portal
1. Jelentkezzen be a Azure Portalba, és keresse meg azt az erőforráscsoportot, ahol a Kubernetes csatlakoztatott fürterőforrás található.
1. Válassza ki az arc-kompatibilis kubernetes-fürtöt (type = "Kubernetes-Azure arc"), ahol a bővítmény üzembe lett helyezve.
1. A bal oldali navigációs sávon a **Beállítások** területen válassza a "bővítmények (előzetes verzió)" lehetőséget.
1. Ekkor meg kell jelennie a korábban a "telepített" állapotban létrehozott bővítménynek.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Bővítmények irányítópultja":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. Kapcsolódjon a Kubernetes-fürthöz egy terminál-ablakon keresztül.
1. Futtassa az alábbi parancsot, és győződjön meg arról, hogy a fent említett (1) névtér létrejött, és (2) a `bootstrapper` Pod "Running" állapotban van, mielőtt továbblép a következő lépésre.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Például az alábbi beolvassa a hüvelyt a `arc` névtérből.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Egyéni hely létrehozása az egyéni hely CLI-bővítménnyel

Az egyéni hely egy olyan Azure-erőforrás, amely egy Kubernetes-fürt névterével egyenértékű.  Az egyéni helyszínek célja, hogy erőforrásokat telepítsenek az Azure-ba vagy az-ból. További információk az [Azure arc-kompatibilis Kubernetes dokumentációjának](../kubernetes/conceptual-custom-locations.md)egyéni helyein található egyéni helyszínekről.

### <a name="set-environment-variables"></a>Környezeti változók beállítása

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Annak ellenőrzése, hogy az egyéni hely létrejött-e

A terminálon futtassa az alábbi parancsot az egyéni hely listázásához, és ellenőrizze, hogy a **kiépítési állapot** sikeres volt-e:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Az Azure arc-adatkezelő létrehozása

A bővítmény és az egyéni hely létrehozása után folytassa a Azure Portalával az Azure arc-adatkezelő üzembe helyezéséhez.

1. Jelentkezzen be az Azure Portalra.
1. Keressen rá az "Azure arc-adatkezelő" kifejezésre az Azure piactéren, és kezdeményezzen folyamat létrehozását.
1. Az **Előfeltételek** szakaszban győződjön meg arról, hogy az Azure arc-kompatibilis Kubernetes-fürt (Direct Mode) be van jelölve, és folytassa a következő lépéssel.
1. Az **adatkezelő adatai** szakaszban válassza ki az előfizetést és az erőforráscsoportot.
1. Adja meg az adatvezérlő nevét.
1. Válasszon egy konfigurációs profilt azon Kubernetes-terjesztési szolgáltató alapján, amelyre telepíteni kívánja.
1. Válassza ki az előző lépésben létrehozott egyéni helyet.
1. Adja meg az adatkezelő rendszergazdai bejelentkezési adatait és jelszavát.
1. Adja meg a ClientId, a TenantId és az ügyfél titkos kulcsát az Azure-objektumok létrehozásához használni kívánt egyszerű szolgáltatáshoz. A szolgáltatás egyszerű fiókjának létrehozásával és a fiókhoz szükséges szerepkörökkel kapcsolatos részletes utasításokért tekintse meg a [metrikák feltöltését](upload-metrics-and-logs-to-azure-monitor.md) ismertető témakört.
1. Kattintson a **tovább** gombra, tekintse át az összefoglalás lapot az összes adathoz, és kattintson a **Létrehozás** gombra.

## <a name="monitor-the-creation"></a>A létrehozás figyelése

Ha a Azure Portal központi telepítésének állapota az üzemelő példány sikerességét mutatja, a következő módon ellenőrizhető az ív-adatkezelő üzembe helyezésének állapota a fürtön:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Következő lépések

[Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása](create-postgresql-hyperscale-server-group.md)

[Azure SQL felügyelt példány létrehozása az Azure arc-ban](create-sql-managed-instance.md)
