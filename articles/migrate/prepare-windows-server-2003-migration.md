---
title: A Windows Server 2003-kiszolgálók előkészítése a Azure Migrate való áttelepítéshez
description: Megtudhatja, hogyan készítheti elő a Windows Server 2003-kiszolgálókat Azure Migrate használatával történő áttelepítésre.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 33519764b138c7711e6c03a85aa33ec6f936a748
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84172283"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Windows Server 2003 rendszerű gépek előkészítése áttelepítésre

Ez a cikk a Windows Server 2003 rendszert futtató gépek előkészítését ismerteti az Azure-ba való áttelepítéshez. 

- A [Hyper-V virtuális gépek](tutorial-migrate-hyper-v.md) és a [VMWare virtuális gépek](tutorial-migrate-vmware.md) az Azure-ba való áttelepítéséhez használhatja az ügynök nélküli áttelepítést.
- Az áttelepítést követően az Azure-beli virtuális gépekhez való csatlakozáshoz telepíteni kell a Hyper-V integrációs szolgáltatásokat az Azure-beli virtuális gépen. A Windows Server 2003 rendszerű gépek alapértelmezés szerint nem települnek.
- Nincs közvetlen letöltési hivatkozás a Hyper-V integrációs szolgáltatások telepítéséhez, ezért a következőket kell tennie:
    - Azokon a Hyper-V virtuális gépeken, amelyeken nincs telepítve, a Windows Server 2012 R2/Windows Server 2012 rendszerű gépen a Hyper-V szerepkörrel kibonthatja az integrációs szolgáltatások telepítési fájljait, majd a telepítőt a Windows Server 2003 gépre másolhatja. A telepítési fájlok nem érhetők el a Windows Server 2016 operációs rendszert futtató gépeken.
    - A VMware virtuális gépek esetében az integrációs szolgáltatásokat telepítő indítási feladatot hoz létre, amikor az Azure-beli virtuális gép elindul az áttelepítés után.


## <a name="install-on-hyper-v-vms"></a>Telepítés Hyper-V virtuális gépeken

Az áttelepítés előtt ellenőrizze, hogy telepítve van-e a Hyper-V integrációs szolgáltatások, majd szükség esetén telepítse azt.

1. Az [alábbi útmutatást](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) követve ellenőrizhető, hogy telepítve van-e.
2. Ha nincs telepítve, jelentkezzen be egy Windows Server 2012 R2/Windows Server 2012 rendszerű gépre a Hyper-V szerepkörrel.
3. Navigáljon a telepítési fájlhoz a **C:\Windows\System32\vmguest.ISO**címen, és csatlakoztassa a fájlt.
2. Másolja a telepítési mappát a Windows Server 2003 rendszerű gépre, és telepítse az integrációs szolgáltatásokat.
4. A telepítést követően meghagyhatja az alapértelmezett beállításokat az integrációs szolgáltatásokban. 

## <a name="install-on-vmware-vms"></a>Telepítés VMware virtuális gépeken

1. Jelentkezzen be egy Windows Server 2012 R2/Windows Server 2012 operációs rendszert futtató gépre a Hyper-V szerepkörrel.
2. Navigáljon a telepítési fájlhoz a **C:\Windows\System32\vmguest.ISO**címen, és csatlakoztassa a fájlt.
3. Másolja a telepítési mappát a VMware virtuális gépre.
4. Futtassa a parancsot a virtuális gép parancssorából ```gpedit.msc``` .
5. Nyissa meg a **Számítógép konfigurációja**  >  **Windows**  >  **-Beállítások parancsfájlokat (indítás/leállítás)**.
6. Az **indításkor**  >  **adja**meg  >  a**parancsfájl nevét**mezőbe írja be a setup.exe-címeket.
7. Az Azure-ba való Migrálás után a szkript az Azure-beli virtuális gép első indításakor fut le.
8. Manuálisan indítsa újra az Azure-beli virtuális gépet. A rendszerindítási diagnosztika előugró ablaka jelzi, hogy újraindítás szükséges.
9. A szkript futtatása és a Hyper-V integrációs szolgáltatások az Azure-beli virtuális gépen való telepítése után eltávolíthatja a parancsfájlt az indításból.
10. A telepítést követően meghagyhatja az alapértelmezett beállításokat az integrációs szolgáltatásokban. 

## <a name="next-steps"></a>További lépések

- Tekintse át a [VMware](migrate-support-matrix-vmware-migration.md) és a [Hyper-V](migrate-support-matrix-hyper-v-migration.md) virtuális gépek áttelepítési követelményeit.
- Telepítse át a [VMware](server-migrate-overview.md) és a [Hyper-V](tutorial-migrate-hyper-v.md) virtuális gépeket.
