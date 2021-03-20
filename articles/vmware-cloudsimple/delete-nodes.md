---
title: A VMware-megoldás csomópontjainak törlése a CloudSimple által – Azure
description: Ismerje meg, hogyan törölhet csomópontokat VMWare-ről a CloudSimple-telepítéssel. A CloudSimple-csomópontok mérése megtörténik. Törölje a Azure Portal nem használt csomópontokat.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88142264"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Csomópontok törlése az Azure VMware-megoldásból a CloudSimple használatával

A CloudSimple-csomópontok a létrehozásuk után vannak mérten.  A csomópontok mérésének leállításához törölni kell a csomópontokat.  Törli a Azure Portal nem használt csomópontokat.

## <a name="before-you-begin"></a>Előkészületek

Csomópontot csak a következő feltételekkel lehet törölni:

* A csomópontokkal létrehozott privát felhő törlődik.  Privát felhő törléséhez tekintse meg a [CloudSimple privát felhőből származó Azure VMware-megoldás törlése](delete-private-cloud.md)című témakört.
* A csomópont el lett távolítva a privát felhőből a privát felhő zsugorodása mellett.  A privát felhő csökkentése érdekében lásd: [Az Azure VMware-megoldás zsugorítása a CloudSimple privát felhővel](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="delete-cloudsimple-node"></a>CloudSimple csomópont törlése

1. Válassza az **Összes szolgáltatás** elemet.

2. CloudSimple- **csomópontok** keresése.

   ![CloudSimple-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple-csomópontok** lehetőséget.

4. Válassza ki azokat a csomópontokat, amelyek nem tartoznak a törölni kívánt privát felhőhöz.  A **saját felhő neve** oszlop azt a saját Felhőbeli nevet jeleníti meg, amelyhez a csomópont tartozik.  Ha egy csomópontot nem használ egy privát felhő, az érték üres lesz. 

    ![CloudSimple-csomópontok kiválasztása](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Csak azokat a csomópontokat lehet törölni, amelyek nem részei a privát felhőnek.

## <a name="next-steps"></a>Következő lépések

* A [Private Cloud](cloudsimple-private-cloud.md) megismerése
