---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple szolgáltatás létrehozása
description: Ismerje meg, hogyan hozhatja létre a CloudSimple szolgáltatást a Azure Portalban. Mielőtt elkezdené, tekintse át a szükséges konfigurációt.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 53f2d0fc9f73985bd70792c8c3b7607eb4c560fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896294"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Az Azure VMware-megoldás létrehozása a CloudSimple szolgáltatással

Az Azure VMware-megoldás CloudSimple való megkezdéséhez hozza létre az Azure VMware-megoldást a CloudSimple szolgáltatással a Azure Portal.

## <a name="before-you-begin"></a>Előkészületek

Foglaljon le egy/28 CIDR-blokkot az átjáró-alhálózathoz. Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózat az Edge hálózati szolgáltatások esetében használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal. A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services** kifejezésre.
    ![CloudSimple szolgáltatás keresése](media/create-cloudsimple-service-search.png)
3. Válassza a **CloudSimple Services** elemet.
4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.
    ![CloudSimple szolgáltatás hozzáadása](media/create-cloudsimple-service-add.png)
5. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a CloudSimple szolgáltatást.
6. Válassza ki a szolgáltatáshoz tartozó erőforráscsoportot. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása** lehetőségre.
7. A szolgáltatás azonosításához adja meg a nevet.
8. Adja meg a CIDR a Service Gateway számára. Olyan/28 alhálózatot ad meg, amely nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy a tervezett CloudSimple alhálózatok egyikét sem. A CIDR nem módosítható a szolgáltatás létrehozása után.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)
9. Kattintson az **OK** gombra.

Létrejön a szolgáltatás, és hozzá lesz adva a szolgáltatások listájához.

## <a name="next-steps"></a>Következő lépések

* További információ a [csomópontok](create-nodes.md) kiépítéséről
* Megtudhatja, hogyan [hozhat létre saját felhőt](create-private-cloud.md)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)
