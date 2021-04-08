---
title: Gyorsított hálózatkezelés engedélyezése az Azure-beli virtuális gépek vész-helyreállításához Azure Site Recovery
description: Útmutató a gyorsított hálózatkezelés engedélyezéséhez Azure Site Recovery Azure-beli virtuális gépek vész-helyreállításához
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000249"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Gyorsított hálózatkezelés az Azure-beli virtuális gépek vész-helyreállításával

A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, nagy mértékben javítja hálózati teljesítményét. Ez a nagy teljesítményű elérési út megkerüli a gazdagépet a DataPath, csökkenti a késést, a vibrálás és a CPU-kihasználtságot, és a legszigorúbb hálózati számítási feladatokhoz használja a támogatott virtuálisgép-típusoknál. Az alábbi képen a két virtuális gép közötti kommunikáció gyorsított hálózatkezeléssel és anélkül látható:

![Összehasonlítás](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

A Azure Site Recovery lehetővé teszi a gyorsított hálózatkezelés előnyeinak kihasználását olyan Azure-beli virtuális gépek esetén, amelyek feladatátvétele egy másik Azure-régióba történik. Ez a cikk azt ismerteti, hogyan engedélyezheti az Azure-beli virtuális gépek gyorsított hálózatkezelését Azure Site Recovery használatával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy érti:
-   Azure-beli virtuális gépek [replikációs architektúrája](azure-to-azure-architecture.md)
-   Az Azure-beli virtuális gépek [replikálásának beállítása](azure-to-azure-tutorial-enable-replication.md)
-   [Feladatátvétel](azure-to-azure-tutorial-failover-failback.md) Azure-beli virtuális gépek

## <a name="accelerated-networking-with-windows-vms"></a>Gyorsított hálózatkezelés Windows rendszerű virtuális gépekkel

Azure Site Recovery támogatja a gyorsabb hálózatkezelést a replikált virtuális gépek számára, csak akkor, ha a forrás virtuális gép gyorsított hálózatkezelést engedélyez. Ha a forrás virtuális gépen nincs engedélyezve a gyorsított hálózat, megtudhatja, hogyan engedélyezheti a gyorsított hálózatkezelést a Windows [](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)rendszerű virtuális gépekhez.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Az Azure-katalógusból az alábbi disztribúciók támogatottak:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Támogatott VM-példányok
A gyorsított hálózatkezelést a legtöbb általános célú és a számítási optimalizált példány mérete támogatja 2 vagy több vCPU.  A támogatott adatsorozatok a következők: D/DSv2 és F/FS

A feleznie támogató példányokon a gyorsított hálózatkezelést a 4 vagy több vCPU rendelkező virtuálisgép-példányok támogatják. Támogatott adatsorozatok: D/DSv3, E/ESv3, Fsv2 és MS/MMS

A virtuálisgép-példányokkal kapcsolatos további információkért lásd: [Windows rendszerű virtuális gépek méretei](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Gyorsított hálózatkezelés Linux rendszerű virtuális gépekkel

Azure Site Recovery támogatja a gyorsabb hálózatkezelést a replikált virtuális gépek számára, csak akkor, ha a forrás virtuális gép gyorsított hálózatkezelést engedélyez. Ha a forrás virtuális gépnek nincs engedélyezve a gyorsított hálózatkezelés, megtudhatja, hogyan engedélyezheti a gyorsított hálózatkezelést Linux rendszerű [virtuális gépekhez](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Az Azure-katalógusból az alábbi disztribúciók támogatottak:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "stretch" a backports kernelrel**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Támogatott VM-példányok
A gyorsított hálózatkezelést a legtöbb általános célú és a számítási optimalizált példány mérete támogatja 2 vagy több vCPU.  A támogatott adatsorozatok a következők: D/DSv2 és F/FS

A feleznie támogató példányokon a gyorsított hálózatkezelést a 4 vagy több vCPU rendelkező virtuálisgép-példányok támogatják. A támogatott adatsorozatok: D/DSv3, E/ESv3, Fsv2 és MS/MMS.

A virtuálisgép-példányokkal kapcsolatos további információkért lásd: Linux rendszerű [virtuális gépek mérete](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>A gyorsított hálózatkezelés engedélyezése a replikált virtuális gépekhez

Ha engedélyezi az Azure-beli virtuális gépek [replikálását](azure-to-azure-tutorial-enable-replication.md) , a site Recovery automatikusan felismeri, hogy a virtuálisgép-hálózati adapterek gyorsított hálózatkezelést engedélyeztek-e. Ha a gyorsított hálózatkezelés már engedélyezve van, Site Recovery automatikusan beállítja a gyorsított hálózatkezelést a replikált virtuális gép hálózati adapterei között.

A gyorsított hálózatkezelés állapota ellenőrizhető a replikált virtuális gép **számítási és hálózati** beállításainak **hálózati adapterek** szakasza alatt.

![Gyorsított hálózatkezelési beállítás](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Ha a replikáció engedélyezése után engedélyezte a gyorsított hálózatkezelést a forrás virtuális gépen, a következő eljárással engedélyezheti a gyorsított hálózatkezelést a replikált virtuális gép hálózati adapterei számára:
1. A replikált virtuális gép **számítási és hálózati** beállításainak megnyitása
2. A **hálózati adapterek szakaszban kattintson** a hálózati adapter nevére.
3. Jelölje be az **engedélyezve** lehetőséget a legördülő menüből a gyorsabb hálózatkezeléshez a **cél** oszlopban

![Gyorsított hálózatkezelés engedélyezése](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

A fenti folyamatot a meglévő replikált virtuális gépek esetében is követni kell, amelyek korábban nem lettek automatikusan engedélyezve a gyorsított hálózatkezelés Site Recovery.

## <a name="next-steps"></a>Következő lépések
- További információ [a gyorsított hálózatkezelés előnyeiről](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- További információ a [Windows rendszerű virtuális gépek](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) és a [linuxos virtuális gépek](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)gyorsított hálózatkezelésével kapcsolatos korlátozásokról és korlátozásokról.
- További információ az alkalmazások feladatátvételének automatizálására szolgáló [helyreállítási tervekről](site-recovery-create-recovery-plans.md) .