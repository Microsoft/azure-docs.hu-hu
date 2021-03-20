---
title: Csomópontok kiépítése VMware-megoldáshoz az Azure-CloudSimple
description: Ismerje meg, hogyan adhat csomópontokat a VMWare-ben a CloudSimple-telepítéssel a Azure Portal. Az utólagos elszámolású kapacitást a saját felhőalapú környezetéhez is beállíthatja.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140734"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Csomópontok kiépítése az Azure VMware-megoldáshoz CloudSimple szerint

Csomópontok kiépítése a Azure Portalban. Ezt követően a CloudSimple saját felhőalapú környezete számára is beállíthatja a befizetési kirovó kapacitást.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Csomópont hozzáadása a saját CloudSimple-felhőhöz

1. Válassza az **Összes szolgáltatás** elemet.
2. CloudSimple- **csomópontok** keresése.

   ![CloudSimple-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple-csomópontok** lehetőséget.
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, ahol CloudSimple-csomópontokat kíván kiépíteni.
6. Válassza ki a csomópontok erőforrás-csoportját. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása** lehetőségre.
7. Adja meg a csomópontok azonosításához szükséges előtagot.
8. Válassza ki a csomópont-erőforrások helyét.
9. Válassza ki a csomópont-erőforrások tárolására szolgáló dedikált helyet.
10. Válassza ki a [csomópont típusát](cloudsimple-node.md).
11. Válassza ki a kiépíteni kívánt csomópontok számát.
12. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **előző** gombra.
14. Válassza a **Létrehozás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Privát felhő létrehozása](create-private-cloud.md)
