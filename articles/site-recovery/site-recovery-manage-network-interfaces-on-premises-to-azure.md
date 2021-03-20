---
title: Hálózati adapterek kezelése helyszíni vész-helyreállításhoz Azure Site Recovery
description: Ismerteti, hogyan kezelheti a helyszíni vész-helyreállítási hálózati adaptereket az Azure-ba Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: harshacs
ms.openlocfilehash: 4dad7f76edf34782131c7c844978763cda53acc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90068115"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Virtuális gépek hálózati adaptereinek kezelése az Azure-ba történő helyszíni vészhelyreállításhoz

Az Azure-beli virtuális gépnek legalább egy hálózati adapterrel kell rendelkeznie. A virtuális gép mérete által támogatottnál több hálózati csatoló is csatlakoztatható.

Alapértelmezés szerint az Azure-beli virtuális géphez csatolt első hálózati adapter az elsődleges hálózati adapterként van meghatározva. A virtuális gép összes többi hálózati adaptere másodlagos hálózati adapter. Alapértelmezés szerint a virtuális gépről érkező összes kimenő forgalom az elsődleges hálózati adapter elsődleges IP-konfigurációjához hozzárendelt IP-címet is elküldi.

A helyszíni környezetben a virtuális gépek vagy kiszolgálók több hálózati adapterrel rendelkezhetnek a környezetben lévő különböző hálózatokhoz. A különböző hálózatokat jellemzően olyan konkrét műveletek végrehajtásához használják, mint a frissítések, a karbantartás és az Internet-hozzáférés. Ha helyszíni környezetből végez áttelepítést vagy feladatátvételt az Azure-ba, vegye figyelembe, hogy az azonos virtuális gépen lévő hálózati adaptereknek ugyanahhoz a virtuális hálózathoz kell csatlakozniuk.

Alapértelmezés szerint a Azure Site Recovery annyi hálózati adaptert hoz létre egy Azure-beli virtuális gépen, amely a helyszíni kiszolgálóhoz csatlakozik. A replikált virtuális gép beállításai alatt a hálózati adapter beállításainak szerkesztésével elkerülhető, hogy az áttelepítés vagy a feladatátvétel során ne hozzon létre redundáns hálózati adaptereket.

## <a name="select-the-target-network"></a>Válassza ki a célként kijelölt hálózatot

A VMware és a fizikai gépek esetében, valamint a Hyper-V (System Center Virtual Machine Manager nélküli) virtuális gépek esetében megadhatja a célként megadott virtuális hálózatot az egyes virtuális gépekhez. A Virtual Machine Manager által felügyelt Hyper-V virtuális gépek esetében a [hálózati leképezés](./hyper-v-vmm-network-mapping.md) használatával képezhető le a virtuálisgép-hálózatok a forrás Virtual Machine Manager-kiszolgáló és a cél Azure-hálózatok számára.

1. A Recovery Services-tárolóban lévő **replikált elemek** területen válassza ki az egyik replikált elemet az adott replikált elem beállításainak eléréséhez.

2. Válassza a **számítás és hálózat** fület a replikált elem hálózati beállításainak eléréséhez.

3. A **hálózati tulajdonságok** területen válasszon ki egy virtuális hálózatot az elérhető hálózati adapterek listájából.

    ![Hálózati beállítások](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

A célként megadott hálózat módosítása hatással van az adott virtuális gép összes hálózati adapterére.

Virtual Machine Manager felhők esetében a hálózati megfeleltetés módosítása hatással van az összes virtuális gépre és hálózati adapterére.

## <a name="select-the-target-interface-type"></a>Válassza ki a cél illesztőfelület típusát

A **számítás és hálózat** ablaktábla **hálózati adapterek** szakaszában megtekintheti és szerkesztheti a hálózati adapter beállításait. Megadhatja a célként megadott hálózati adapter típusát is.

- A feladatátvételhez **elsődleges** hálózati adapter szükséges.
- Az összes többi kiválasztott hálózati csatoló (ha vannak ilyenek) **másodlagos** hálózati adapterek.
- A **nem használható** lehetőség kiválasztásával zárja ki a hálózati adaptert a létrehozásból a feladatátvétel során.

Alapértelmezés szerint a replikálás engedélyezésekor Site Recovery kiválasztja az összes észlelt hálózati adaptert a helyszíni kiszolgálón. Az egyiket **elsődlegesként** , a másikat pedig **másodlagosként** jelöli meg. A helyszíni kiszolgálón hozzáadott további felületek alapértelmezés szerint **nem használhatók** . Ha további hálózati adaptereket ad hozzá, győződjön meg arról, hogy az Azure-beli virtuális gép megfelelő mérete van kiválasztva az összes szükséges hálózati adapter számára.

## <a name="modify-network-interface-settings"></a>Hálózati adapter beállításainak módosítása

A replikált elemek hálózati adapterei alhálózatának és IP-címének módosítására is lehetőség van. Ha nincs megadva IP-cím, Site Recovery a következő elérhető IP-címet fogja hozzárendelni az alhálózatból a feladatátvételi hálózati adapterhez.

1. Válassza ki az elérhető hálózati adaptereket a hálózati adapter beállításainak megnyitásához.

2. Válassza ki a kívánt alhálózatot az elérhető alhálózatok listájából.

3. Adja meg a kívánt IP-címet (szükség szerint).

    ![Hálózati adapter beállításai](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. A Szerkesztés befejezéséhez kattintson **az OK** gombra, és térjen vissza a **számítás és a hálózat** panelre.

5. Ismételje meg a 1-4. lépést az egyéb hálózati adapterek esetében.

6. Válassza a **Mentés** lehetőséget az összes módosítás mentéséhez.

## <a name="next-steps"></a>Következő lépések
  [További](../virtual-network/virtual-network-network-interface-vm.md) információ az Azure Virtual Machines szolgáltatáshoz használható hálózati adapterekről.
