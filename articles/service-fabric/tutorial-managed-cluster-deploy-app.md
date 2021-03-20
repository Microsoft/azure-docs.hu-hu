---
title: Alkalmazás üzembe helyezése Service Fabric felügyelt fürtön a PowerShell használatával (előzetes verzió)
description: Ebben az oktatóanyagban csatlakozni fog egy Service Fabric felügyelt fürthöz, és üzembe helyez egy alkalmazást a PowerShell használatával.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91410445"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Oktatóanyag: alkalmazás üzembe helyezése Service Fabric felügyelt fürtön (előzetes verzió)

Ebben az oktatóanyag-sorozatban a következőket fogjuk megvitatni:

> [!div class="checklist"]
> * [Service Fabric felügyelt fürt üzembe helyezése](tutorial-managed-cluster-deploy.md)
> * [Service Fabric felügyelt fürt felskálázása](tutorial-managed-cluster-scale.md)
> * [Csomópontok hozzáadása és eltávolítása Service Fabric felügyelt fürtben](tutorial-managed-cluster-add-remove-node-type.md)
> * Alkalmazás központi telepítése Service Fabric felügyelt fürtön

A sorozat ezen része a következőket ismerteti:

> [!div class="checklist"]
> * Kapcsolódás a Service Fabric felügyelt fürthöz
> * Alkalmazás feltöltése egy fürtre
> * Alkalmazás példányának létrehozása fürtben
> * Alkalmazás eltávolítása egy fürtről

## <a name="prerequisites"></a>Előfeltételek

* Egy Service Fabric felügyelt fürt (lásd: [*felügyelt fürt üzembe helyezése*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Kapcsolódás a fürthöz

A fürthöz való kapcsolódáshoz szüksége lesz a fürt tanúsítványának ujjlenyomatára. Ezt az értéket megkeresheti az erőforrás-telepítés fürt tulajdonságainak kimenetében, vagy egy meglévő erőforrás fürt tulajdonságainak lekérdezésével.

A következő parancs használatával kérdezheti le a fürt erőforrását a fürt tanúsítványának ujjlenyomatára.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Ha a fürt tanúsítványának ujjlenyomata van, készen áll a fürthöz való kapcsolódásra.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Alkalmazáscsomag feltöltése

Ebben az oktatóanyagban a [Service Fabric szavazási alkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) mintát fogjuk használni. Service Fabric alkalmazások PowerShell használatával történő telepítésével kapcsolatos további részletekért lásd: [alkalmazások telepítése és eltávolítása Service Fabric](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> A Service Fabric felügyelt fürt előzetes verziójában nem fog tudni alkalmazásokat közzétenni közvetlenül a Visual studióból.

Először telepítenie kell [az alkalmazást az üzembe helyezéshez](service-fabric-package-apps.md). Ebben az oktatóanyagban kövesse az alkalmazások Visual studióból történő csomagolásának lépéseit. Fontos megjegyezni azt az elérési utat, ahol az alkalmazás be van csomagolva, mivel az alábbi elérési útra lesz használva.

Az alkalmazáscsomag létrehozása után feltöltheti az alkalmazáscsomag a fürtbe. Frissítse az `$path` értéket úgy, hogy az azt az elérési utat adja meg, ahol az alkalmazáscsomag létezik, és futtassa a következőt:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Alkalmazás létrehozása

A New-ServiceFabricApplication parancsmag használatával bármely olyan alkalmazás-típusból létrehozhat egy alkalmazást, amely sikeresen regisztrálva van. Az egyes alkalmazások nevének a "Fabric:" sémával kell kezdődnie, és minden egyes alkalmazás-példány esetében egyedinek kell lennie. A rendszer az alkalmazás jegyzékfájljában definiált alapértelmezett szolgáltatásokat is létrehozza.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

A művelet befejezése után meg kell jelennie a Service Fabric Explorerban futó alkalmazás példányainak.

### <a name="remove-an-application"></a>Alkalmazás eltávolítása

Ha egy alkalmazás-példányra már nincs szükség, véglegesen eltávolíthatja azt név alapján a `Remove-ServiceFabricApplication` parancsmag használatával, amely automatikusan eltávolítja az alkalmazáshoz tartozó összes szolgáltatást, véglegesen eltávolítja az összes szolgáltatás állapotát.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Következő lépések

Ebben a lépésben egy Service Fabric felügyelt fürtre telepítettünk egy alkalmazást. A Service Fabric felügyelt fürtökkel kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Service Fabric felügyelt fürtök gyakran ismételt kérdései](faq-managed-cluster.md)
