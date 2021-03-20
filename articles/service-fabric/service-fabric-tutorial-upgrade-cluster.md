---
title: A Service Fabric futtatókörnyezet frissítése az Azure-ban
description: Ez az oktatóanyag bemutatja, hogyan frissíthető egy Azure-ban tárolt Service Fabric-fürt futtatókörnyezetének frissítése a PowerShell segítségével.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 23b3aabf8e991e512ef9a5c07d725c3084ea7f83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86244735"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Service Fabric-fürt futtatókörnyezetének frissítése az Azure-ban

Ez az oktatóanyag egy sorozat negyedik része, és bemutatja, hogyan frissítheti az Service Fabric futtatókörnyezetet egy Azure Service Fabric-fürtön. Ez az oktatóanyag-rész az Azure-on futó Service Fabric-fürtökre íródott, és nem vonatkozik az önálló Service Fabric-fürtökre.

> [!WARNING]
> Az oktatóanyag jelen részéhez PowerShell szükséges. Az Azure CLI-eszközök még nem támogatják a fürt-futtatókörnyezet frissítésének támogatását. Másik lehetőségként a fürt a portálon is frissíthető. További információkért lásd az [Azure Service Fabric-fürt frissítését](service-fabric-cluster-upgrade.md).

Ha a fürt már futtatta a legújabb Service Fabric futtatókörnyezetet, ezt a lépést nem kell végrehajtania. Azonban ez a cikk az Azure Service Fabric-fürtön található bármely támogatott futtatókörnyezet telepítéséhez használható.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fürt verziószámának beolvasása
> * A fürt verziójának beállítása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban sablon használatával
> * [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * Fürt futtatókörnyezetének frissítése
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse [Azure PowerShell](/powershell/azure/install-az-ps) vagy az [Azure CLI](/cli/azure/install-azure-cli)-t.
* Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban
* Windows fejlesztési környezet beállítása. Telepítse a [Visual Studio 2019](https://www.visualstudio.com) -es és az **Azure development**, a **ASP.net és a Web Development**, valamint a **.net Core platformfüggetlen fejlesztési** számítási feladatokat.  Ezután hozzon létre egy [.NET fejlesztési környezet](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Azure-parancsok végrehajtása előtt jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Futtatókörnyezet verziójának lekérése

Miután csatlakozott az Azure-hoz, válassza ki a Service Fabric fürtöt tartalmazó előfizetést, lekérheti a fürt futásidejű verzióját.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Vagy tekintse meg az előfizetéshez tartozó összes fürt listáját a következő példával:

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Jegyezze fel a **ClusterCodeVersion** értéket. Ezt az értéket a következő szakaszban fogjuk használni.

## <a name="upgrade-the-runtime"></a>A futtatókörnyezet frissítése

Használja az előző szakaszban látható **ClusterCodeVersion** értéket a `Get-ServiceFabricRuntimeUpgradeVersion` parancsmaggal a frissítéshez elérhető verziók megtekintéséhez. Ez a parancsmag csak az internethez csatlakozó számítógépről futtatható. Például ha látni szeretné, hogy a futtatókörnyezet melyik verzióira frissíthet az `5.7.198.9494`-es verzióról, használja az alábbi parancsot:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

A verziók listája alapján megadhatja az Azure Service Fabric-fürtnek, hogy egy újabb futtatókörnyezetre frissítsen. Például ha a `6.0.219.9494`-es verzióra frissíthet, az alábbi paranccsal frissítse a fürtöt.

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> A fürt futtatókörnyezetének frissítése hosszú időt vehet igénybe. A PowerShell le van tiltva, amíg a frissítés folyamatban van. A frissítés állapotát egy másik PowerShell-munkamenetből ellenőrizheti.

A frissítés állapota a PowerShell használatával vagy az Azure Service Fabric parancssori felületen (sfctl) monitorozható.

Először kapcsolódjon a fürthöz az oktatóanyag első részében létrehozott TLS/SSL-tanúsítvánnyal. Használja a `Connect-ServiceFabricCluster` parancsmagot vagy az `sfctl cluster upgrade-status` parancsot.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Következő lépésként a `Get-ServiceFabricClusterUpgrade` vagy az `sfctl cluster upgrade-status` paranccsal tekintse meg az állapotot. Az alábbihoz hasonló eredmény jelenik meg.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```console
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A fürt-futtatókörnyezet verziójának lekérése
> * A fürt futtatókörnyezetének frissítése
> * A frissítés figyelése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Fürt törlése](service-fabric-tutorial-delete-cluster.md)
