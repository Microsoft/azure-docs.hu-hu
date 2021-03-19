---
title: Önálló fürt tisztítása
description: Ebből az oktatóanyagból megtudhatja, hogyan törölhet AWS-vagy Azure-erőforrásokat az önálló Service Fabric-fürthöz.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842886"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Oktatóanyag: Az önálló fürt törlése

Service Fabric önálló fürtökben lehetősége van a saját környezetének kiválasztására Service Fabric üzemeltetéséhez. Ebben az oktatóanyag-sorozatban létre fog hozni egy AWS-ben vagy az Azure-ban üzemeltetett önálló fürtöt, és üzembe helyezhet egy alkalmazást.

Ez az oktatóanyag egy sorozat negyedik része. Az oktatóanyag ezen része bemutatja, hogyan törölheti a Service Fabric-fürt üzemeltetéséhez létrehozott AWS-vagy Azure-erőforrásokat.

Ebből a cikkből megtudhatja, hogy:

> [!div class="checklist"]
> * Service Fabric-fürt eltávolítása
> * AWS-vagy Azure-erőforrások törlése

## <a name="remove-a-service-fabric-cluster"></a>Service Fabric-fürt eltávolítása

1. Az RDP-t a virtuális géphez, amelyet a Service Fabric telepítéséhez használt.
2. Nyissa meg a PowerShellt.
3. Lépjen a második oktatóanyagban kibontott mappába.
4. A Service Fabric-fürt eltávolításához futtassa a következő parancsot:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Ha a rendszer kéri, adja meg az értéket `Y` . Ha a művelet sikeres volt, a kimenet a következőhöz hasonlóan fog kinézni (saját IP-címekkel):

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="delete-aws-resources"></a>AWS-erőforrások törlése

1. Jelentkezzen be az AWS-fiókjába.
2. Lépjen az EC2 konzolra.
3. Válassza ki az oktatóanyag első részében létrehozott csomópontokat.
4. Válassza a **műveletek**  >  **példány állapotának**  >  **leállítása** lehetőséget.

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése

1. Jelentkezzen be az Azure Portalra.
2. Lépjen a **Virtual Machines** szakaszra.
3. Jelölje be az oktatóanyag első részében létrehozott három csomópont jelölőnégyzeteit.
4. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan törölheti az előző lépésekben létrehozott erőforrásokat.

> [!div class="checklist"]
> * Az erőforrások törlése

> [!div class="nextstepaction"]
> [Vissza az elejére](service-fabric-tutorial-standalone-create-infrastructure.md)
