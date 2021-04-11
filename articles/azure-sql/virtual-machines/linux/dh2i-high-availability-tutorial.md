---
title: Always On rendelkezésre állási csoport beállítása Linux-alapú Azure-Virtual Machines futó DH2i-DxEnterprise
description: A DH2i DxEnterprise segítségével magas rendelkezésre állást érhet el a rendelkezésre állási csoporttal SQL Server on Linux Azure Virtual Machines
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 56002aaa977b94b0fabee4f17343f483706eb77d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449426"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Oktatóanyag – három csomópontos always on rendelkezésre állási csoport beállítása Linux-alapú Azure-Virtual Machines futó DH2i-DxEnterprise

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez az oktatóanyag azt ismerteti, hogyan konfigurálható SQL Server always on rendelkezésre állási csoport a Linux-alapú Azure-Virtual Machines (VM-EK) futó DH2i-DxEnterprise. 

További információ a DxEnterprise: [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> A Microsoft támogatja az adatáthelyezést, a rendelkezésre állási csoportot és a SQL Server összetevőket. Forduljon a DH2i az DH2i DxEnterprise-fürt dokumentációjában, a fürt és a kvórum felügyeletéhez kapcsolódó támogatásért.
 

Ez az oktatóanyag a következő lépésekből áll:

> [!div class="checklist"]
> * Telepítse a SQL Servert az összes olyan Azure-beli virtuális gépre (VM), amely a rendelkezésre állási csoport részévé válik.
> * Telepítse a DxEnterprise-t az összes virtuális gépre, és konfigurálja a DxEnterprise-fürtöt.
> * Hozza létre a virtuális gazdagépeket a feladatátvétel támogatásához és a magas rendelkezésre állás biztosításához, adjon hozzá rendelkezésre állási csoportot és adatbázist a rendelkezésre állási csoport számára.
> * Hozza létre a belső Azure Load balancert a rendelkezésre állási csoport figyelője számára (nem kötelező).
> * Manuális vagy automatikus feladatátvételt hajthat végre.

Ebben az oktatóanyagban egy DxEnterprise-fürtöt fogunk beállítani a DxAdmin- [ügyfél felhasználói felületén](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Lehetőség van arra is, hogy a fürtöt a [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) parancssori felületének használatával is beállíthatja. Ebben a példában négy virtuális gépet használunk. Ezek közül három virtuális gépen Ubuntu 18,04 fut, és a három csomópontos fürt részét képezik. A negyedik virtuális gép a Windows 10 rendszert futtatja a DxAdmin eszközzel a fürt felügyeletéhez és konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre négy virtuális gépet az Azure-ban. A Linux-alapú virtuális gépek létrehozásához kövesse a következő rövid útmutatót [: Linux rendszerű virtuális gép létrehozása Azure Portal](../../../virtual-machines/linux/quick-create-portal.md) cikkben. Hasonlóképpen, a Windows-alapú virtuális gép létrehozásához kövesse a rövid útmutató [: Windows rendszerű virtuális gép létrehozása a Azure Portal](../../../virtual-machines/windows/quick-create-portal.md) cikkben.
- Telepítse a .NET 3,1-et minden olyan Linux-alapú virtuális gépre, amely a fürt részét képezi majd. Kövesse az [itt](/dotnet/core/install/linux) megjelenő utasításokat a Linux operációs rendszer kiválasztása alapján.
- A rendelkezésre állási csoport felügyeleti funkcióit engedélyező érvényes DxEnterprise-licencre lesz szükség. További információkért tekintse meg a [DxEnterprise ingyenes](https://dh2i.com/trial/) próbaverzióját, amelyből megtudhatja, hogyan szerezhet be ingyenes próbaverziót.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Telepítse a SQL Servert az összes olyan Azure-beli virtuális gépre, amely a rendelkezésre állási csoport része lesz

Ebben az oktatóanyagban egy három csomópontos Linux-alapú fürtöt állítunk be, amely a rendelkezésre állási csoportot futtatja. A Linux-platformon alapuló [SQL Server Linux-telepítés](/sql/linux/sql-server-linux-overview#install) dokumentációját követheti. Javasoljuk továbbá, hogy telepítse az oktatóanyag [SQL Server eszközeit](/sql/linux/sql-server-linux-setup-tools) .
 
> [!NOTE]
> Győződjön meg arról, hogy a választott Linux operációs rendszer közös disztribúció, amelyet mindkét [DH2i DxEnterprise támogat (lásd a minimális rendszerkövetelmények szakaszt)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) és a [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> Ebben a példában az Ubuntu 18,04-et használjuk, amelyet a DH2i DxEnterprise és a Microsoft SQL Server is támogat.

Ebben az oktatóanyagban nem fogunk SQL Server telepíteni a Windows rendszerű virtuális gépre, mivel ez a csomópont nem lesz a fürt része, és csak a fürt DxAdmin használatával történő kezeléséhez használatos.

Miután elvégezte ezt a lépést, SQL Server és [SQL Server eszközöket](/sql/linux/sql-server-linux-setup-tools) kell telepítenie a rendelkezésre állási csoportban szereplő mindhárom Linux-alapú virtuális gépre.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Telepítse a DxEnterprise-t az összes virtuális gépre, és konfigurálja a fürtöt.

Ebben a lépésben a DH2i DxEnterprise for Linux rendszert fogjuk telepíteni a három Linux rendszerű virtuális gépen. A következő táblázat ismerteti, hogy az egyes kiszolgálók hogyan játszanak a fürtben:

| Virtuális gépek száma | DH2i DxEnterprise-szerepkör | Microsoft SQL Server rendelkezésre állási csoport replikájának szerepköre |
|--|--|--|
| 1 | Fürtcsomópont – Linux-alapú | Elsődleges |
| 1 | Fürtcsomópont – Linux-alapú | Másodlagos – szinkron véglegesítés |
| 1 | Fürtcsomópont – Linux-alapú | Másodlagos – szinkron véglegesítés |
| 1 | DxAdmin-ügyfél | NA |


Ha a DxEnterprise-et a három Linux-alapú csomóponton szeretné telepíteni, kövesse a DH2i DxEnterprise dokumentációját a választott Linux operációs rendszer alapján. Telepítse a DxEnterprise az alább felsorolt módszerek egyikével.

- **Ubuntu**
    - [A tárház telepítése Rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Bővítmény Rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Piactéri rendszerkép Rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [A tárház telepítése Rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Bővítmény Rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Piactéri rendszerkép Rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Ha csak a DxAdmin-ügyfél eszközt szeretné telepíteni a Windows rendszerű virtuális gépen, kövesse az [DxAdmin-ügyfél felhasználói felületének rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Ennek a lépésnek a megkezdése után a DxEnterprise-fürtöt létre kell hozni a linuxos virtuális gépeken, és telepíteni kell a Windows DxAdmin-ügyfelet. 

> [!NOTE]
> Létrehozhat egy három csomópontot tartalmazó fürtöt is, amelyben az egyik csomópontot *csak konfigurációs módban* adja [hozzá a rendszer az automatikus](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) feladatátvétel engedélyezéséhez. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>A virtuális gazdagépek létrehozása a feladatátvételi támogatás és a magas rendelkezésre állás biztosításához

Ebben a lépésben létrehozunk egy virtuális gazdagépet, rendelkezésre állási csoportot, majd hozzáadunk egy adatbázist a DxAdmin felhasználói felületén.   

> [!NOTE]
> Ebben a lépésben a SQL Server példányok újra lesznek indítva, hogy a mindig be legyen kapcsolva. 

Kapcsolódjon a DxAdmin-t futtató Windows-ügyfélszámítógéphez a fenti lépésben létrehozott fürthöz való kapcsolódáshoz. A virtuális gazdagép és a rendelkezésre állási csoport létrehozásához kövesse az [MSSQL rendelkezésre állási csoportok](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) és a DxAdmin című témakörben leírt lépéseket. 

> [!TIP]
> Az adatbázisok hozzáadása előtt győződjön meg arról, hogy az adatbázis létrejön, és biztonsági másolat készül a SQL Server elsődleges példányán.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>A belső Azure Load Balancer létrehozása a figyelőhöz (nem kötelező)

Ebben a választható lépésben létrehozhatja és konfigurálhatja az Azure Load balancert, amely a rendelkezésre állási csoport figyelők IP-címeit tárolja. A Azure Load Balancerről a [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)című témakörben olvashat bővebben. Az Azure Load Balancer és a rendelkezésre állási csoport figyelője DxAdmin használatával történő konfigurálásához kövesse a DxEnterprise [Azure Load Balancer rövid útmutató](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/).

Ennek a lépésnek a megkezdése után létre kell hoznia egy rendelkezésre állási csoport figyelőt, amely a belső Azure Load Balancerhez van rendelve.

## <a name="test-manual-or-automatic-failover"></a>Manuális vagy automatikus feladatátvétel tesztelése

Az automatikus feladatátvételi teszt esetében előre megadhatja az elsődleges replikát (kikapcsolhatja a virtuális gépet a Azure Portal). Ezzel replikálja az elsődleges csomópont hirtelen nem elérhetővé válik. A várt viselkedés:
- A Fürtfelügyelő a rendelkezésre állási csoport egyik másodlagos replikáját az elsődleges értékre mozdítja.
- A sikertelen elsődleges replika automatikusan csatlakozik a fürthöz a biztonsági mentés után. A Fürtfelügyelő támogatja a másodlagos replikát.

 
Az alábbi lépések végrehajtásával manuálisan is elvégezheti a feladatátvételt:

1. Kapcsolódás a fürthöz a DxAdmin használatával   
1. A rendelkezésre állási csoport virtuális gazdagépének kibontása
1. Kattintson a jobb gombbal a cél csomópontra vagy a másodlagos replikára, majd a feladatátvétel kezdeményezéséhez válassza az **Indítás a tag** számára lehetőséget. 

A DxEnterprise-en belüli további műveletekkel kapcsolatos további információkért nyissa meg a [DxEnterprise rendszergazdai útmutatóját](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) és a [DxEnterprise DxCLI útmutatóját](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Következő lépések

- További információ a [Linuxon rendelkezésre állási csoportokról](/sql/linux/sql-server-linux-availability-group-overview)
- [Gyors útmutató: linuxos virtuális gép létrehozása Azure Portalban](../../../virtual-machines/linux/quick-create-portal.md)
- [Rövid útmutató: Windows rendszerű virtuális gép létrehozása az Azure Portalon](../../../virtual-machines/windows/quick-create-portal.md)
- [Támogatott platformok a SQL Server 2019 Linux rendszeren](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)