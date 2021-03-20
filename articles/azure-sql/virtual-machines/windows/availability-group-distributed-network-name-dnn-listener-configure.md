---
title: DNN-figyelő konfigurálása a rendelkezésre állási csoport számára
description: Megtudhatja, hogyan konfigurálhat egy Distributed Network name (DNN) figyelőt úgy, hogy lecserélje a virtuális hálózat neve (VNN) figyelőt, és irányítsa a forgalmat az Always On rendelkezésre állási csoportba az Azure virtuális gépen SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 07ce01304f27ded4e0a566777fcf7027f7a15e4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359438"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>DNN-figyelő konfigurálása rendelkezésre állási csoporthoz
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure-beli virtuális gépeken SQL Server az elosztott hálózat neve (DNN) átirányítja a forgalmat a megfelelő fürtözött erőforrásra. Így könnyebben csatlakozhat egy always on rendelkezésre állási csoporthoz (AG), mint a virtuális hálózat neve (VNN) figyelő, anélkül, hogy szükség lenne egy Azure Load Balancerra. 

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy DNN-figyelőt, hogy lecserélje a VNN figyelőt, és átirányítsa a forgalmat a rendelkezésre állási SQL Server csoportba az Azure-beli virtuális gépek magas rendelkezésre állású és vész-helyreállítási 

A DNN-figyelő szolgáltatás jelenleg csak a SQL Server 2019 CU8 kezdődően érhető el a Windows Server 2016-es és újabb verzióiban. 

Alternatív kapcsolódási lehetőségként vegye fontolóra egy [VNN-figyelőt és Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) helyette. 

## <a name="overview"></a>Áttekintés

Az elosztott hálózati név (DNN) figyelő helyettesíti a hagyományos virtuális hálózati név (VNN) rendelkezésre állási csoport figyelőjét, ha az [Always On rendelkezésre állási csoportokkal használja SQL Server virtuális gépeken](availability-group-overview.md). Ez megtagadja a forgalom irányítását, az üzembe helyezést, a karbantartást és a feladatátvételt javító Azure Load Balancer szükségességét. 

A DNN figyelő használatával cserélje le a meglévő VNN-figyelőt, vagy használja azt egy meglévő VNN-figyelővel együtt, hogy a rendelkezésre állási csoport két különböző csatlakozási ponttal rendelkezik – az egyik a VNN-figyelő neve (és a port, ha nem alapértelmezett), és egy az DNN-figyelő neve és portja. 

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek elvégzése előtt a következőket kell tennie:

- SQL Server 2019 CU8 vagy újabb verzióban Windows Server 2016 vagy újabb rendszeren
- Úgy döntött, hogy az elosztott hálózat neve a megfelelő [kapcsolódási lehetőség a HADR-megoldáshoz](hadr-cluster-best-practices.md#connectivity).
- Az [Always On rendelkezésre állási csoport](availability-group-overview.md)konfigurálása. 
- Telepítette a [PowerShell](/powershell/azure/install-az-ps)legújabb verzióját. 


## <a name="create-script"></a>Parancsfájl létrehozása

Az elosztott hálózati név (DNN) erőforrás létrehozásához és a rendelkezésre állási csoporthoz való hozzárendeléséhez használja a PowerShellt. 

Ehhez kövesse az alábbi lépéseket: 

1. Nyisson meg egy szövegszerkesztőt, például a Jegyzettömböt. 
1. Másolja és illessze be a következő parancsfájlt: 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Mentse a parancsfájlt `.ps1` fájlként, például: `add_dnn_listener.ps1` . 


## <a name="execute-script"></a>Parancsfájl végrehajtása

A DNN-figyelő létrehozásához hajtsa végre a paraméterben megadott parancsfájlt a rendelkezésre állási csoport, a figyelő neve és a port nevénél. 

Tegyük fel például, hogy a rendelkezésre állási csoport nevét `ag1` , a figyelő nevét `dnnlsnr` és a figyelő portszámát a következő `6789` lépésekkel feltételezheti: 

1. Nyisson meg egy parancssori felületi eszközt, például a parancssort vagy a PowerShellt. 
1. Navigáljon arra a helyre, ahová mentette a `.ps1` parancsfájlt, például c:\Documents. 
1. Futtassa a szkriptet: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Például: 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Figyelő ellenőrzése

SQL Server Management Studio vagy Transact-SQL használatával ellenőrizze, hogy a DNN-figyelő sikeresen létrejött-e. 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

A **rendelkezésre állási csoport figyelők** kibontása [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) a DNN-figyelő megtekintéséhez: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Tekintse meg az DNN figyelőt a rendelkezésre állási csoport figyelők területén SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

A DNN-figyelő állapotának megtekintéséhez használja a Transact-SQL-t: 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

A for érték `1` `is_distributed_network_name` azt jelzi, hogy a figyelő egy elosztott hálózati név (DNN) figyelő: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="A sys.availability_group_listeners segítségével azonosíthatja a DNN-figyelőket, amelyek értéke 1 a is_distributed_network_name":::


## <a name="update-connection-string"></a>A kapcsolatok karakterláncának frissítése

Frissítse a kapcsolati karakterláncokat az alkalmazásokhoz, hogy csatlakozzanak a DNN-figyelőhöz. A feladatátvétel gyors elérésének biztosításához adja hozzá `MultiSubnetFailover=True` a kapcsolati sztringet, ha az SQL-ügyfél támogatja azt. 

## <a name="test-failover"></a>Feladatátvétel tesztelése

Tesztelje a rendelkezésre állási csoport feladatátvételét a működés biztosítása érdekében. 

A feladatátvétel teszteléséhez kövesse az alábbi lépéseket: 

1. Kapcsolódjon a DNN-figyelőhöz vagy az egyik replikához [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)használatával. 
1. Bontsa ki az **Always On rendelkezésre állási csoportot** **Object Explorer**. 
1. Kattintson a jobb gombbal a rendelkezésre állási csoportra, majd válassza a **feladatátvétel** lehetőséget a **feladatátvételi varázsló** megnyitásához. 
1. Kövesse az utasításokat a feladatátvételi cél kiválasztásához, és adja meg a rendelkezésre állási csoportot egy másodlagos replikához. 
1. Ellenőrizze, hogy az adatbázis szinkronizált állapotban van-e az új elsődleges replikán. 
1. Választható Adja vissza az eredeti elsődleges vagy egy másik másodlagos replikát. 

## <a name="test-connectivity"></a>Kapcsolat tesztelése

Tesztelje a kapcsolatot a DNN-figyelővel a következő lépésekkel:

1. Nyissa meg [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Kapcsolódjon a DNN-figyelőhöz. 
1. Nyisson meg egy új lekérdezési ablakot, és győződjön meg arról, hogy melyik replikán fut `SELECT @@SERVERNAME` . 
1. A rendelkezésre állási csoport feladatátvétele egy másik replikára.
1. Ésszerű idő elteltével futtassa a parancsot, `SELECT @@SERVERNAME` hogy ellenőrizze, hogy a rendelkezésre állási csoport már egy másik replikán található-e. 


## <a name="limitations"></a>Korlátozások

- Jelenleg a rendelkezésre állási csoporthoz tartozó DNN-figyelő csak SQL Server 2019 CU8 és újabb verziókban támogatott a Windows Server 2016-es és újabb verzióiban. 
- További szempontokat is figyelembe vehet, ha más SQL Server funkciókat és egy DNN rendelkező rendelkezésre állási csoportot használ. További információ: [AG és DNN együttműködési képesség](availability-group-dnn-interoperability.md). 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure SQL Server HADR szolgáltatásairól, tekintse meg a [rendelkezésre állási csoportok](availability-group-overview.md) és a [feladatátvevő fürt példánya](failover-cluster-instance-overview.md)című témakört. Megtudhatja, [Hogyan](hadr-cluster-best-practices.md) konfigurálhatja a környezetet a magas rendelkezésre álláshoz és a vész-helyreállításhoz. 


