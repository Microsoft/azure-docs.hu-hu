---
title: Több IP-cím Azure-beli virtuális gépekhez – Portál | Microsoft Docs
description: Megtudhatja, hogyan rendelhet hozzá több IP-címet egy virtuális géphez a Azure Portal | Resource Manager.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 050d3ac23562e7822d186a16675d03c1b9dc670b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739205"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Több IP-cím hozzárendelése virtuális gépekhez a Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Ez a cikk bemutatja, hogyan hozhat létre virtuális gépet (VM) az Azure Resource Manager-telepítési modellel a Azure Portal. A klasszikus üzembe helyezési modellel létrehozott erőforrásokhoz nem rendelhető hozzá több IP-cím. Az Azure-beli üzembe helyezési modellekkel kapcsolatos további információkért olvassa el [az üzembe helyezési modelleket megismerő](../azure-resource-manager/management/deployment-models.md) cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Ha több IP-címmel vagy statikus magánhálózati IP-címmel rendelkező virtuális gépet szeretne létrehozni, azt a PowerShell vagy az Azure CLI használatával kell létrehoznia. Ha meg szeretne ismerkedni a cikk tetején található PowerShell- vagy parancssori felületi beállításokkal, Létrehozhat egy virtuális gépet egyetlen dinamikus magánhálózati IP-címmel és (opcionálisan) egyetlen nyilvános IP-címmel. A portálon kövesse a [Windows](../virtual-machines/windows/quick-create-portal.md) rendszerű virtuális gép létrehozása vagy a Linux rendszerű virtuális gép létrehozása [cikkben található lépéseket.](../virtual-machines/linux/quick-create-portal.md) A virtuális gép létrehozása után módosíthatja az IP-cím típusát dinamikusról statikusra, és további IP-címeket adhat hozzá a portálon a cikk [IP-címek hozzáadása](#add) virtuális géphez című szakaszában található lépések alapján.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-címek hozzáadása virtuális géphez

A következő lépések elvégzésével hozzáadhat privát és nyilvános IP-címeket egy Azure hálózati adapterhez. A következő szakaszok példái azt feltételezik, hogy már rendelkezik egy [](#scenario)virtuális gép a forgatókönyvben leírt három IP-konfigurációval, de ez nem kötelező.

### <a name="core-steps"></a><a name="coreadd"></a>Alapvető lépések

1. Nyissa meg a Azure Portal a https://portal.azure.com webhelyet, és szükség esetén jelentkezzen be.
2. A portálon kattintson a **További** szolgáltatások elemre, > *gépelje* be a virtuális gépeket a szűrőmezőbe, majd kattintson a Virtuális gépek **elemre.**
3. A Virtuális **gépek panelen** kattintson arra a virtuális gépre, amelybe IP-címeket szeretne hozzáadni. Lépjen **a Hálózat lapra.** Kattintson **a Hálózati adapter** elemre az oldalon. Ahogy az alábbi képen is látható: 


    ![Nyilvános IP-cím hozzáadása virtuális géphez](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. A Hálózati **adapter panelen** kattintson a **IP-konfigurációk elemre.**

5. A kiválasztott hálózati adapterhez megjelenő panelen kattintson az **IP-konfigurációk elemre.** Kattintson **a Hozzáadás** gombra, hajtsa végre a következő szakaszok egyikének lépéseit a hozzáadni kívánt IP-cím típusa alapján, majd kattintson az OK **gombra.** 

### <a name="add-a-private-ip-address"></a>Magánhálózati IP-cím hozzáadása

Új magánhálózati IP-cím hozzáadásához kövesse az alábbi lépéseket:

1. Kövesse a cikk [Alapvető](#coreadd) lépések szakaszában található lépéseket, és győződjön meg arról, hogy a virtuális gép hálózati **adapterének** IP-konfigurációk szakaszában van.  Tekintse át az alapértelmezettként megjelenő alhálózatot (például 10.0.0.0/24).
2. Kattintson a **Hozzáadás** parancsra. A megjelenő **IP-konfiguráció hozzáadása** panelen hozzon létre egy *IPConfig-4* nevű IP-konfigurációt egy új statikus magánhálózati IP-címmel úgy, hogy új számot választ az utolsó oktetthez, majd kattintson az **OK** gombra.   (A 10.0.0.0/24 alhálózat például a *10.0.0.7.)*

    > [!NOTE]
    > Statikus IP-cím hozzáadásakor meg kell adnia egy nem használt, érvényes címet azon az alhálózaton, amelyhez a hálózati adapter csatlakozik. Ha a kiválasztott cím nem érhető el, a portál egy X-et jelenít meg az IP-címhez, és ki kell választania egy másikat.

3. Miután rákattintott az OK gombra, a panel bezárul, és megjelenik az új IP-konfiguráció. Kattintson **az OK** gombra az **IP-konfiguráció hozzáadása panel bezárul.**
4. A Hozzáadás **gombra** kattintva további IP-konfigurációkat adhat hozzá, vagy bezárhatja az összes megnyitott panelt az IP-címek hozzáadásának befejezéséhez.
5. Adja hozzá a magánhálózati IP-címeket a virtuális gép operációs rendszeréhez a cikk [IP-címek](#os-config) hozzáadása virtuális gép operációs rendszerhez című szakaszában található lépések elvégzésével.

### <a name="add-a-public-ip-address"></a>Nyilvános IP-cím hozzáadása

A nyilvános IP-címeket úgy lehet hozzáadni, hogy egy nyilvános IP-cím erőforrást társítunk egy új IP-konfigurációhoz vagy egy meglévő IP-konfigurációhoz.

> [!NOTE]
> A nyilvános IP-címek névleges díjat számítnak fel. Az IP-címek díjszabására vonatkozó további információkért olvassa el az [IP-címek díjszabását.](https://azure.microsoft.com/pricing/details/ip-addresses) Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Nyilvános IP-cím erőforrás létrehozása

A nyilvános IP-cím a nyilvános IP-cím erőforrás egyik beállítása. Ha olyan nyilvános IP-cím-erőforrással dolgozik, amely jelenleg nincs IP-konfigurációhoz társítva, akkor hagyja ki a következő lépéseket, és hajtsa végre a következő szakaszok valamelyikének lépéseit, ha szükséges. Ha nem áll rendelkezésre nyilvános IP-cím erőforrás, a következő lépésekkel hozhat létre egyet:

1. Nyissa meg a Azure Portal a https://portal.azure.com webhelyet, és szükség esetén jelentkezzen be.
3. A portálon kattintson **az Erőforrás létrehozása** Hálózati nyilvános  >  **IP-cím**  >  **elemre.**
4. A megjelenő Nyilvános **IP-cím** létrehozása panelen adjon meg egy Nevet, válasszon ki egy **IP-cím-hozzárendelési** típust, egy Előfizetést, egy Erőforráscsoportot és egy **Helyet,** majd kattintson a Létrehozás gombra az alábbi ábrán látható módon:   

    ![Nyilvános IP-cím erőforrás létrehozása](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Az alábbi szakaszok egyikének lépéseit követve társítsa a nyilvános IP-cím erőforrást egy IP-konfigurációhoz.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>A nyilvános IP-cím erőforrás társítása új IP-konfigurációhoz

1. Kövesse a cikk [Alapvető](#coreadd) lépések szakaszában található lépéseket.
2. Kattintson a **Hozzáadás** parancsra. A megjelenő **IP-konfiguráció hozzáadása** panelen hozzon létre egy *IPConfig-4 nevű IP-konfigurációt.* Engedélyezze a **Nyilvános IP-címet,** és válasszon ki egy meglévő, elérhető nyilvános IP-cím erőforrást a megjelenő **Nyilvános IP-cím** kiválasztása panelen.

    Miután kiválasztotta a nyilvános IP-cím erőforrást, kattintson az **OK gombra,** és a panel bezárul. Ha még nincs nyilvános IP-címe, létrehozhat egyet a cikk Nyilvános [IP-cím](#create-public-ip) erőforrás létrehozása című szakaszában található lépések elvégzésével. 

3. Tekintse át az új IP-konfigurációt. Annak ellenére, hogy a magánhálózati IP-cím nem lett explicit módon hozzárendelve, a rendszer automatikusan hozzárendelt egyet az IP-konfigurációhoz, mert minden IP-konfigurációnak magánhálózati IP-címmel kell lennie.
4. A Hozzáadás **gombra** kattintva további IP-konfigurációkat adhat hozzá, vagy bezárhatja az összes megnyitott panelt az IP-címek hozzáadásának befejezéséhez.
5. Adja hozzá a magánhálózati IP-címet a virtuális gép operációs rendszeréhez a cikk [IP-címek](#os-config) hozzáadása virtuális gép operációs rendszerhez című szakaszában található, az operációs rendszerre vonatkozó lépések elvégzésével. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>A nyilvános IP-cím erőforrás társítása meglévő IP-konfigurációhoz

1. Kövesse a cikk [Alapvető lépések](#coreadd) szakaszában található lépéseket.
2. Kattintson arra az IP-konfigurációra, amelybe a nyilvános IP-cím erőforrást hozzá szeretné adni.
3. A megjelenő IPConfig panelen kattintson a **IP-cím elemre.**
4. A megjelenő **Nyilvános IP-cím kiválasztása** panelen válasszon ki egy nyilvános IP-címet.
5. Kattintson **a Mentés gombra,** és zárja be a paneleket. Ha még nincs nyilvános IP-címe, létrehozhat egyet a cikk Nyilvános [IP-cím](#create-public-ip) erőforrás létrehozása című szakaszában található lépések elvégzésével.
3. Tekintse át az új IP-konfigurációt.
4. A Hozzáadás **gombra** kattintva további IP-konfigurációkat adhat hozzá, vagy bezárhatja az összes megnyitott panelt az IP-címek hozzáadásának befejezéséhez. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.

> [!NOTE]
> Az IP-cím konfigurációjának módosítása után újra kell indítania a virtuális gépet, hogy a módosítások életbe lépnek a virtuális gépen.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
