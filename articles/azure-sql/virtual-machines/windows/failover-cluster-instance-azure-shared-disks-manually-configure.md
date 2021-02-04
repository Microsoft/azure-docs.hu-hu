---
title: Hozzon létre egy, az Azure-beli megosztott lemezekkel rendelkező
description: Az Azure Shared Disks használatával hozzon létre egy feladatátvételi fürtszolgáltatást az Azure Virtual Machines SQL Serverával.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 70f4ac69721db57aa06c0d8fda12189f43e79686
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537830"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Az Azure Shared Disks (SQL Server Azure-beli virtuális gépeken) létrehozása
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre egy feladatátvevő fürtszolgáltatást az Azure-beli SQL Server Azure-beli Virtual Machines (VM) használatával az Azure-beli megosztott lemezekkel. 

További információért lásd: az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürttel kapcsolatos ajánlott eljárások](hadr-cluster-best-practices.md)a SQL Server-es és újabb változatának áttekintése. 

## <a name="prerequisites"></a>Előfeltételek 

A cikkben szereplő utasítások elvégzése előtt a következőket kell tennie:

- Azure-előfizetés. Az első lépések [ingyenesek](https://azure.microsoft.com/free/). 
- [Két vagy több Windows Azure-beli virtuális gép](failover-cluster-instance-prepare-vm.md). Az prémium SSD és a [rendelkezésre állási zónák](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) számára támogatott [rendelkezésre állási](../../../virtual-machines/windows/tutorial-availability-sets.md) [csoportok és Proximity elhelyezési csoportok](../../../virtual-machines/co-location.md#proximity-placement-groups) (PPGs) az ultra lemezek esetében támogatottak. Az összes csomópontnak ugyanabban a [közelségi elhelyezési csoportban](../../../virtual-machines/co-location.md#proximity-placement-groups)kell lennie.
- Egy olyan fiók, amely rendelkezik objektumok létrehozásához szükséges engedélyekkel mind az Azure-beli virtuális gépeken, mind pedig a Active Directory.
- A [PowerShell](/powershell/azure/install-az-ps)legújabb verziója. 

## <a name="add-azure-shared-disk"></a>Azure megosztott lemez hozzáadása
Helyezzen üzembe egy felügyelt prémium SSD lemezt, amelyen engedélyezve van a megosztott lemez szolgáltatás. Állítsa be úgy a csomópontok `maxShares` számát, hogy a lemez megosztható legyen az összes%- **os** csomóponton belül. 

Vegyen fel egy Azure-beli megosztott lemezt a következő módon: 

1. Mentse a következő parancsfájlt *SharedDiskConfig.jsként*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```

2. *SharedDiskConfig.jsfuttatása a* PowerShell használatával: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Minden virtuális gép esetében inicializálja a csatlakoztatott megosztott lemezeket GUID partíciós táblaként (GPT), és formázza őket új technológiai fájlrendszerként (NTFS) a következő parancs futtatásával: 

    ```powershell
    $resourceGroup = "<your resource group name>"
    $location = "<region of your shared disk>"
    $ppgName = "<your proximity placement groups name>"
    $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
        -Name "<your VM node name>"
    $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
        -DiskName "<your shared disk name>"
    $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
        -CreateOption Attach -ManagedDiskId $dataDisk.Id `
        -Lun <available LUN - check disk setting of the VM>
    Update-AzVm -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="create-failover-cluster"></a>Feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következők szükségesek:

- A fürtcsomópontok lesznek a virtuális gépek nevei.
- A feladatátvevő fürt neve.
- A feladatátvevő fürt IP-címe. Olyan IP-címet is használhat, amely nem szerepel ugyanazon az Azure-beli virtuális hálózaton és az alhálózaton, mint a fürtcsomópontok.

# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

A következő PowerShell-szkript létrehoz egy feladatátvevő fürtöt. Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

A következő PowerShell-szkript létrehoz egy feladatátvevő fürtöt. Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

További információ: [feladatátvevő fürt: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---

## <a name="configure-quorum"></a>Kvórum konfigurálása

Konfigurálja az üzleti igényeknek leginkább megfelelő kvórum megoldást. Beállíthat egy [tanúsító lemezt](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), egy [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness)vagy egy [tanúsító fájlmegosztást](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). További információ: [kvórum SQL Server virtuális gépekkel](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Fürt ellenőrzése
Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell használatával.

A fürt a felhasználói felületen való ellenőrzéséhez tegye a következőket az egyik virtuális gépen:

1. A **Kiszolgálókezelő** területen válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő** lehetőséget.
1. A **Feladatátvevőfürt-kezelő** alatt válassza a **művelet**, majd a **Konfiguráció ellenőrzése** lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók vagy fürt kijelölése** területen adja meg mindkét virtuális gép nevét.
1. A **tesztelési beállítások** területen válassza a **csak a kiválasztott tesztek futtatása** lehetőséget. 
1. Kattintson a **Tovább** gombra.
1. A **teszt kiválasztása** területen válassza az összes teszt lehetőséget a **tárolás** *kivételével*

## <a name="test-cluster-failover"></a>Fürt feladatátvételének tesztelése

Tesztelje a fürt feladatátvételét. A **Feladatátvevőfürt-kezelőban** kattintson a jobb gombbal a fürtre, válassza a **További műveletek**  >  csomópontot az **alapszintű fürt erőforrásainak**  >  **kiválasztásához**, majd válassza ki a fürt másik csomópontját. Helyezze át az alapszintű fürt erőforrását a fürt minden csomópontjára, majd helyezze vissza az elsődleges csomópontra. Ha sikeresen át tudja helyezni a fürtöt az egyes csomópontokra, készen áll a SQL Server telepítésére.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Fürt feladatátvételének tesztelése az alapvető erőforrás más csomópontokra való áthelyezésével":::

## <a name="create-sql-server-fci"></a>Létrehozás SQL Server

Miután konfigurálta a feladatátvevő fürtöt és a fürt összes összetevőjét, beleértve a Storage-t is, létrehozhatja a SQL Server-t.

1. Kapcsolódjon az első virtuális géphez RDP protokoll (RDP) használatával.

1. **Feladatátvevőfürt-kezelő** ellenőrizze, hogy az összes alapvető fürterőforrás az első virtuális gépen van-e. Ha szükséges, helyezze át az összes erőforrást az adott virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace-lemezképet használja, az adathordozó a következő helyen található: `C:\SQLServer_<version number>_Full` . 

1. Válassza a **beállítás** lehetőséget.

1. **SQL Server telepítési központban** válassza a **telepítés** lehetőséget.

1. Válassza az **új SQL Server feladatátvevő fürt telepítése** lehetőséget. A varázsló utasításait követve telepítse a SQL Server-t.

Az adatkönyvtáraknak az Azure-beli megosztott lemezeken kell lenniük. 

1. Miután elvégezte a varázsló utasításait, a telepítő telepíti az első csomóponton egy SQL Server-t.

1. Miután a telepítő telepíti a (z)-t az első csomóponton, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg a **SQL Server telepítési központot**, majd válassza a **telepítés** lehetőséget.

1. Válassza **a csomópont hozzáadása SQL Server feladatátvevő fürthöz** lehetőséget. A varázsló utasításait követve telepítse a SQL Servert, és adja hozzá a kiszolgálót a modulhoz.

   >[!NOTE]
   >Ha SQL Servert tartalmazó Azure Marketplace Gallery-rendszerképet használt, SQL Server eszközöket tartalmazott a rendszerképben. Ha nem használja ezeket a képeket, telepítse külön a SQL Server-eszközöket. További információ: [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >

## <a name="register-with-the-sql-vm-rp"></a>Regisztrálás az SQL VM RP-vel

A SQL Server VM a portálról való kezeléséhez regisztrálja az SQL IaaS-ügynök kiterjesztésével (RP) az [egyszerűsített felügyeleti módban](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), jelenleg az egyetlen olyan mód, amely az Azure-beli virtuális gépeken a (z) és a SQL Server támogatott. 

SQL Server VM regisztrálása könnyűsúlyú módban a PowerShell használatával:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Kapcsolat konfigurálása 

Ha a forgalmat az aktuális elsődleges csomópontnak megfelelően szeretné irányítani, konfigurálja a környezetének megfelelő kapcsolódási lehetőséget. Létrehozhat egy [Azure Load balancert](failover-cluster-instance-vnn-azure-load-balancer-configure.md) , vagy ha a SQL Server 2019 CU2 (vagy újabb) és a Windows Server 2016 (vagy újabb) rendszert használja, Ehelyett használhatja a [Distributed Network name](failover-cluster-instance-distributed-network-name-dnn-configure.md) szolgáltatást. 

## <a name="limitations"></a>Korlátozások

- Csak az SQL IaaS-ügynök bővítménnyel való regisztráció [egyszerűsített felügyeleti módban](sql-server-iaas-agent-extension-automate-management.md#management-modes) támogatott.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, állítsa be a kapcsolatot a [virtuális hálózat nevével és az Azure Load balancerrel](failover-cluster-instance-vnn-azure-load-balancer-configure.md) vagy az [elosztott hálózat nevével (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Ha az Azure Shared Disks nem az Ön számára megfelelő, és nem az Ön számára megfelelő, akkor érdemes lehet a [prémium fájlmegosztás](failover-cluster-instance-premium-file-share-manually-configure.md) vagy [közvetlen tárolóhelyek](failover-cluster-instance-storage-spaces-direct-manually-configure.md) használatával létrehozni. 

További tudnivalókat az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürt konfigurálásával kapcsolatos ajánlott eljárásokkal](hadr-cluster-best-practices.md)foglalkozó, a SQL Server az Azure-ban című témakörben

További információkért lásd: 
- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
