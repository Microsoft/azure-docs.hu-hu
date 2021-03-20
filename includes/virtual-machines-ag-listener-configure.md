---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400990"
---
A rendelkezésre állási csoport figyelője az SQL Server rendelkezésre állási csoport által figyelt IP-cím és hálózati név. A rendelkezésre állási csoport figyelő létrehozásához tegye a következőket:

1. <a name="getnet"></a>Szerezze be a fürt hálózati erőforrásának nevét.

    a. Az RDP használatával csatlakozzon az elsődleges replikát futtató Azure-beli virtuális géphez. 

    b. Nyissa meg Feladatátvevőfürt-kezelő.

    c. Válassza a **hálózatok** csomópontot, és jegyezze fel a fürt hálózati nevét. Használja ezt a nevet a `$ClusterNetworkName` PowerShell-parancsfájlban található változóban. A következő ábrán a fürt hálózati neve a **cluster Network 1**:

   ![Fürt hálózatnév](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Adja hozzá az ügyfél-hozzáférési pontot.  
    Az ügyfél-hozzáférési pont annak a hálózatnak a neve, amelyet az alkalmazások a rendelkezésre állási csoportban lévő adatbázisokhoz való kapcsolódáshoz használnak. Hozza létre az ügyfél-hozzáférési pontot Feladatátvevőfürt-kezelőban.

    a. Bontsa ki a fürt nevét, majd kattintson a **szerepkörök** elemre.

    b. A **szerepkörök** ablaktáblán kattintson a jobb gombbal a rendelkezésre állási csoport nevére, majd válassza az **erőforrás**-  >  **ügyfél-hozzáférési pont** hozzáadása lehetőséget.

   ![Az ügyfél-hozzáférési pont menüt megjelenítő képernyőkép.](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. A **név** mezőben hozzon létre egy nevet ehhez az új figyelőhöz. 
   Az új figyelő neve annak a hálózatnak a neve, amelyet az alkalmazások a SQL Server rendelkezésre állási csoportban lévő adatbázisokhoz való kapcsolódáshoz használnak.

    d. A figyelő létrehozásának befejezéséhez kattintson kétszer a **tovább** gombra, majd kattintson a **Befejezés** gombra. Ezen a ponton ne helyezze online állapotba a figyelőt vagy az erőforrást.

1. Állítsa offline állapotba a rendelkezésre állási csoport fürt szerepkörét. **Feladatátvevőfürt-kezelő** a **szerepkörök** területen kattintson a jobb gombbal a szerepkörre, majd válassza a **szerepkör leállítása** lehetőséget.

1. <a name="congroup"></a>Konfigurálja az IP-erőforrást a rendelkezésre állási csoport számára.

    a. Kattintson a **Resources (erőforrások** ) lapra, majd bontsa ki a létrehozott ügyfél-hozzáférési pontot.  
    Az ügyfél-hozzáférési pont offline állapotban van.

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kattintson a jobb gombbal az IP-erőforrásra, majd kattintson a Tulajdonságok elemre. Jegyezze fel az IP-cím nevét, és használja azt a `$IPResourceName` PowerShell-parancsfájlban található változóban.

    c. Az **IP-cím** területen kattintson a **statikus IP-cím** elemre. Állítsa be az IP-címet ugyanazzal a címmel, amelyet a terheléselosztó címének a Azure Portal való beállításakor használt.

   ![Képernyőkép, amely megjeleníti az IP-cím beállításának helyét.](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Az SQL Server rendelkezésre állási csoport erőforrásának kihasználása az ügyfél-hozzáférési ponttól függ.

    a. A Feladatátvevőfürt-kezelő területen kattintson a **szerepkörök** elemre, majd kattintson a rendelkezésre állási csoportra.

    b. Az **erőforrások** lap **egyéb erőforrások** területén kattintson a jobb gombbal a rendelkezésre állási erőforráscsoport elemre, majd kattintson a **Tulajdonságok** elemre. 

    c. A függőségek lapon adja meg az ügyfél-hozzáférési pont (a figyelő) erőforrásának nevét.

   ![Képernyőfelvétel: a függőségek lapon adja meg a nevet.](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kattintson az **OK** gombra.

1. <a name="listname"></a>Az ügyfél-hozzáférési pont erőforrásának kihasználása az IP-címről függ.

    a. A Feladatátvevőfürt-kezelő területen kattintson a **szerepkörök** elemre, majd kattintson a rendelkezésre állási csoportra. 

    b. Az **erőforrások** lapon kattintson a jobb gombbal az ügyfél-hozzáférési pont erőforrásra a **kiszolgáló neve** területen, majd kattintson a **Tulajdonságok** elemre. 

   ![A kiszolgáló nevéhez tartozó Tulajdonságok menüpontot megjelenítő képernyőkép.](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kattintson a **függőségek** fülre. Ellenőrizze, hogy az IP-cím függőség-e. Ha nem, állítsa be az IP-cím függőségét. Ha több erőforrás van felsorolva, ellenőrizze, hogy az IP-címek rendelkeznek-e vagy, nem és, függőségekkel. Kattintson az **OK** gombra. 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Ellenőrizheti, hogy a függőségek megfelelően vannak-e konfigurálva. A Feladatátvevőfürt-kezelő területen lépjen a szerepkörök pontra, kattintson a jobb gombbal a rendelkezésre állási csoportra, kattintson a **További műveletek** parancsra, majd a  **függőségi jelentés megjelenítése** elemre. Ha a függőségek megfelelően vannak konfigurálva, a rendelkezésre állási csoport a hálózat nevétől függ, a hálózat neve pedig az IP-címről függ. 


1. <a name="setparam"></a>A fürt paramétereinek beállítása a PowerShellben.

   a. Másolja az alábbi PowerShell-szkriptet az egyik SQL Server példányára. Frissítse a környezet változóit.

   - `$ListenerILBIP` a rendelkezésre állási csoport figyelője számára az Azure Load balancerben létrehozott IP-cím.
    
   - `$ListenerProbePort` a rendelkezésre állási csoport figyelője számára az Azure Load balancerben konfigurált port.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Állítsa be a fürt paramétereit úgy, hogy futtatja a PowerShell-parancsfájlt az egyik fürtcsomóponton.  

   > [!NOTE]
   > Ha a SQL Server példányai különálló régiókban találhatók, akkor kétszer kell futtatnia a PowerShell-szkriptet. Az első alkalommal használja a `$ListenerILBIP` és az `$ListenerProbePort` első régiót. A második alkalommal használja a `$ListenerILBIP` és a `$ListenerProbePort` elemet a második régióból. A fürt hálózati neve és a fürt IP-erőforrásának neve is eltérő az egyes régiókban.

1. A rendelkezésre állási csoport fürt szerepkörének online állapotba hozása. **Feladatátvevőfürt-kezelő** a **szerepkörök** területen kattintson a jobb gombbal a szerepkörre, majd válassza a **szerepkör indítása** lehetőséget.

Ha szükséges, ismételje meg a fenti lépéseket a WSFC-fürt IP-címéhez tartozó fürt paramétereinek megadásához.

1. Szerezze be a WSFC-fürt IP-címének IP-címét. **Feladatátvevőfürt-kezelő** a **fürt alapvető erőforrásai** területen keresse meg a **kiszolgáló nevét**.

1. Kattintson a jobb gombbal az **IP-cím** elemre, majd válassza a **Tulajdonságok** lehetőséget.

1. Másolja ki az IP-cím **nevét** . Lehetséges, hogy `Cluster IP Address` . 

1. <a name="setwsfcparam"></a>A fürt paramétereinek beállítása a PowerShellben.
  
   a. Másolja az alábbi PowerShell-szkriptet az egyik SQL Server példányára. Frissítse a környezet változóit.

   - `$ClusterCoreIP` az az IP-cím, amelyet az Azure Load balancerben hozott létre az WSFC Core fürterőforrás számára. Eltér a rendelkezésre állási csoport figyelője IP-címétől.

   - `$ClusterProbePort` az a port, amelyet az Azure Load balancerben konfigurált a WSFC Health mintavételhez. Eltér a rendelkezésre állási csoport figyelője mintavételének.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Állítsa be a fürt paramétereit úgy, hogy futtatja a PowerShell-parancsfájlt az egyik fürtcsomóponton.  

>[!WARNING]
>A rendelkezésre állási csoport figyelő állapotának mintavételi portjának eltérőnek kell lennie a fürt alapvető IP-címe állapotának mintavételi portján. Ezekben a példákban a figyelő portja 59999, és a fürt alapszintű IP-címe állapotának mintavételi portja 58888. Mindkét portnak engedélyezve kell lennie a bejövő tűzfalszabály engedélyezése szabálynak.
