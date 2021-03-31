---
title: 'Gyors útmutató: VMware CloudSimple szolgáltatás létrehozása'
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerje meg, hogyan hozhatja létre a CloudSimple szolgáltatást, a megvásárlási csomópontokat és a tartalék csomópontokat
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86507590"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Rövid útmutató – Azure VMware-megoldás létrehozása a CloudSimple szolgáltatással

Első lépésként hozza létre az Azure VMware-megoldást a Azure Portal CloudSimple.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-megoldás a CloudSimple-szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple az Azure VMware-megoldást használja.  A szolgáltatás létrehozása lehetővé teszi a csomópontok kiépítését, a csomópontok fenntartását és a privát felhők létrehozását.  Adja hozzá a CloudSimple szolgáltatást minden olyan Azure-régióban, ahol a CloudSimple szolgáltatás elérhető.  A szolgáltatás a CloudSimple által definiált Azure VMware-megoldás peremhálózati hálózatát határozza meg.  Ez a peremhálózati hálózat olyan szolgáltatásokhoz használható, amelyek VPN-, ExpressRoute-és internetkapcsolattal rendelkeznek a privát felhőkhöz.

A CloudSimple szolgáltatás hozzáadásához létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. A helyszíni hálózati címtartomány vagy az Azure-beli virtuális hálózati címtartomány egyikével sem lehet átfedésben.

## <a name="before-you-begin"></a>Előkészületek

Egy/28 CIDR-blokk lefoglalása az átjáró-alhálózathoz.  Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózatot az Azure VMware-megoldáshoz CloudSimple Edge hálózati szolgáltatások használják, és egy/28 CIDR blokkra van szükség. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal.  A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.

Tekintse át a [hálózati előfeltételeket](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatást**.

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

## <a name="provision-nodes"></a>Csomópontok kiépítése

Ha a CloudSimple saját felhőalapú környezete számára szeretné beállítani az utólagos elszámolású kapacitást, először a Azure Portal csomópontjait kell kiépíteni.

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

* [Saját felhő létrehozása és környezet konfigurálása](quickstart-create-private-cloud.md)
* További információ a [CloudSimple szolgáltatásról](./cloudsimple-service.md)
