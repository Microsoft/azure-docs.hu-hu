---
title: Oktatóanyag – Azure Service Fabric Mesh-erőforrások karbantartása
description: Ez a dokumentum az Azure Service Fabric Mesh-erőforrások eltávolítását ismerteti, hogy a már nem használt erőforrásokért ne kelljen fizetnie.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d78e1e27d70b147bb52dff13675e63b79335d62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625465"
---
# <a name="tutorial-remove-azure-resources"></a>Oktatóanyag: Azure-erőforrások eltávolítása

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez az oktatóanyag egy sorozat ötödik része, amely azt mutatja be, hogyan törölheti az alkalmazásokat és azok erőforrásait, hogy ne kelljen értük fizetnie.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Az alkalmazások által használt erőforrások törlése a további költségek felmerülésének megelőzése érdekében.

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Egy, a helyi fejlesztési fürtön futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * Service Fabric Mesh-erőforrások törlése

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem helyezte üzembe a teendőkezelő alkalmazást, kövesse a [Service Fabric Mesh-webalkalmazás közzétételét](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) ismertető cikk utasításait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag vége. Amikor befejezte a létrehozott erőforrások használatát, törölje azokat, hogy a már nem használt erőforrásokért ne kelljen fizetnie. Ez amiatt is különösen fontos, mivel a háló egy kiszolgáló nélküli szolgáltatás, amelynek a számlázása másodpercalapon történik. A Mesh díjszabásával kapcsolatos további részletekért lásd: https://aka.ms/sfmeshpricing.

Az Azure által biztosított kényelmi szolgáltatások egyike, hogy az egy adott erőforráscsoportban létrehozott összes erőforrás törléséhez elegendő magát az erőforráscsoportot törölni, mivel ez az összes társított erőforrást is törli. Így azokat nem kell manuálisan egyenként törölnie.

Mivel a teendőkezelő alkalmazás létrehozásához új erőforráscsoportot hozott létre, nyugodtan törölheti azt, ami az összes társított erőforrást is törli.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Az **sfmeshTutorial1RG** erőforráscsoportot [a portálról is törölheti](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Következő lépések

Most, hogy közzétette az Azure-ban a Service Fabric Mesh-alkalmazást, próbálkozzon a következőkkel:

* A szolgáltatások közötti kommunikáció további példájaként tekintse meg a [minta szavazóalkalmazást](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
* A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
* Tájékozódjon a [Cloud Shellről](../cloud-shell/overview.md).
