---
title: Zónában lévő virtuális gép létrehozása a Azure Portal
description: Windows rendszerű virtuális gép létrehozása rendelkezésre állási zónában a Azure Portal
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 7c7f135d4033a31f855342c172d73f51478931ab
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501685"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Virtuális gép létrehozása rendelkezésre állási zónában a Azure Portal használatával

Ez a cikk végigvezeti a virtuális gép Azure-beli rendelkezésre állási zónában való létrehozásához szükséges Azure Portal használatával. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zóna használatához egy [támogatott Azure-régióban](../../availability-zones/az-region.md) hozza létre a virtuális gépet.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 

3. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](faq.md#what-are-the-password-requirements-when-creating-a-vm). Válasszon olyan helyet, mint az USA 2. keleti régiója, amely támogatja a rendelkezésre állási zónákat. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Válasszon méretet a virtuális gép számára. Válasszon egy ajánlott méretet, vagy a funkciók alapján szűrje a szűrőt. Ellenőrizze, hogy a méret elérhető-e a használni kívánt zónában.

    ![Virtuális gép méretének kiválasztása](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. A **Beállítások**  >  **magas rendelkezésre állása** területen válassza ki az egyik számú zónát a **rendelkezésre állási zóna** legördülő listából, tartsa meg a fennmaradó alapértékeket, majd kattintson **az OK** gombra.

    ![Rendelkezésre állási zóna kiválasztása](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Az összefoglalás lapon kattintson a **Létrehozás** elemre a virtuális gép telepítésének elindításához.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>A felügyelt lemez és az IP-cím zónájának megerősítése

Ha a virtuális gép rendelkezésre állási zónában van telepítve, a virtuális gép felügyelt lemeze ugyanabban a rendelkezésre állási zónában jön létre. Alapértelmezés szerint a rendszer a zónában is létrehoz egy nyilvános IP-címet.

Ezen erőforrások zónájának beállításait a portálon ellenőrizheti.  

1. Kattintson az **erőforráscsoportok** elemre, majd a virtuális géphez tartozó erőforráscsoport nevére, például *myResourceGroup*.

2. Kattintson a lemez erőforrásának nevére. Az **Áttekintés** oldalon az erőforrás hely-és rendelkezésre állási zónájának adatai szerepelnek.

    ![A felügyelt lemez rendelkezésre állási zónája](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kattintson a nyilvános IP-cím erőforrás nevére. Az **Áttekintés** oldalon az erőforrás hely-és rendelkezésre állási zónájának adatai szerepelnek.

    ![IP-cím rendelkezésre állási zónája](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. További információ az Azure-beli virtuális gépek [rendelkezésre állásáról](../availability.md) .