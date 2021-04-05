---
title: Útmutató a SQL Server Azure-beli virtuális gépen való kiépítéséhez Azure PowerShell használatához
description: Útmutatást és PowerShell-parancsokat tartalmaz az Azure-beli virtuális gépek SQL Server virtuálisgép-katalógus rendszerképeivel való létrehozásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3f51a07b274320d1cd9f12b33703d8ec7f21f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359659"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>A Azure PowerShell használata az Azure-beli SQL Server kiépítéséhez Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez az útmutató a PowerShell használatának azon lehetőségeit ismerteti, amelyekkel az Azure Virtual Machines (VM) SQL Server kiépíthető. Az alapértelmezett értékekre támaszkodó, egyszerűsített Azure PowerShell például az [SQL VM Azure PowerShell](sql-vm-create-powershell-quickstart.md)gyors útmutatójában talál.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

1. A **AzAccount** parancs futtatásával nyissa meg a PowerShellt, és hozzon létre hozzáférést az Azure-fiókjához.

   ```powershell
   Connect-AzAccount
   ```

1. Amikor a rendszer erre kéri, adja meg a hitelesítő adatait. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="define-image-variables"></a>Rendszerkép-változók definiálása

Ha újra szeretné használni az értékeket, és egyszerűbbé teszi a parancsfájlok létrehozását, kezdje a változók számának meghatározásával. Módosítsa a paramétereket úgy, ahogy szeretné, de a megadott értékek módosításakor vegye figyelembe a név hosszával és a speciális karakterekkel kapcsolatos elnevezési korlátozásokat.

### <a name="location-and-resource-group"></a>Hely és erőforráscsoport

Adja meg az adatterületet és az erőforráscsoportot, amelyben létre kívánja hozni a többi virtuálisgép-erőforrást.

Módosítsa a kívánt módosításokat, majd futtassa ezeket a parancsmagokat a változók inicializálásához.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Tárolási tulajdonságok

Adja meg a Storage-fiókot és a virtuális gép által használandó tároló típusát.

Módosítsa a kívánt módosításokat, majd futtassa a következő parancsmagot a változók inicializálásához. Javasoljuk, hogy a [prémium SSD](../../../virtual-machines/disks-types.md#premium-ssd) -ket üzemi számítási feladatokhoz használja.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Hálózati tulajdonságok

Adja meg a virtuális gépen a hálózat által használandó tulajdonságokat. 

- Hálózati adapter
- TCP/IP-kiosztási módszer
- Virtuális hálózat neve
- Virtuális alhálózat neve
- A virtuális hálózat IP-címeinek tartománya
- Az alhálózat IP-címeinek tartománya
- Nyilvános tartománynév címkéje

Módosítsa a kívánt módosításokat, majd futtassa ezt a parancsmagot a változók inicializálásához.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Virtuális gép tulajdonságai

Adja meg a következő tulajdonságokat:

- Virtuális gép neve
- Számítógép neve
- Virtuális gép mérete
- A virtuális gép operációsrendszer-lemezének neve

Módosítsa a kívánt módosításokat, majd futtassa ezt a parancsmagot a változók inicializálásához.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>SQL Server rendszerkép kiválasztása

A következő változók használatával határozhatja meg a virtuális géphez használni kívánt SQL Server-rendszerképet. 

1. Először sorolja fel az összes SQL Server rendszerkép-ajánlatát a `Get-AzVMImageOffer` paranccsal. Ez a parancs felsorolja a Azure Portal elérhető aktuális lemezképeket és a régebbi rendszerképeket, amelyek csak a PowerShell használatával telepíthetők:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Ebben az oktatóanyagban a következő változókat kell megadnia a Windows Server 2016 SQL Server 2017-es értékének megadásához.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ezután sorolja fel az ajánlathoz elérhető kiadásokat.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Ebben az oktatóanyagban használja a SQL Server 2017 fejlesztői kiadását (**SQLDEV**). A fejlesztői kiadás szabadon jogosult tesztelésre és fejlesztésre, és csak a virtuális gép futtatásának költségeit kell fizetnie.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A Resource Manager-alapú üzemi modellben az elsőként létrehozott objektum az erőforráscsoport. A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával hozzon létre egy Azure-erőforráscsoportot és annak erőforrásait. Adja meg az erőforráscsoport nevének és helyének korábban inicializált változóit.

Futtassa ezt a parancsmagot az új erőforráscsoport létrehozásához.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A virtuális gépnek tárolási erőforrásokat kell megadnia az operációsrendszer-lemezhez, valamint a SQL Server-és naplófájlokhoz. Az egyszerűség kedvéért egyetlen lemezt fog létrehozni mindkettőhöz. Az [Add-Azure Disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) parancsmaggal később további lemezeket is csatolhat, hogy a SQL Server adatait és naplófájljait dedikált lemezekre helyezze. A [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával hozzon létre egy szabványos Storage-fiókot az új erőforráscsoporthoz. Adja meg a Storage-fiók neve, a tárolási SKU-név és a hely számára korábban inicializált változókat.

Futtassa ezt a parancsmagot az új Storage-fiók létrehozásához.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A Storage-fiók létrehozása eltarthat néhány percig.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A virtuális gépnek számos hálózati erőforrásra van szüksége a hálózati kapcsolathoz.

* Minden virtuális gépnek szüksége van egy virtuális hálózatra.
* Egy virtuális hálózatnak rendelkeznie kell legalább egy alhálózat definiált alhálózattal.
* Egy hálózati adaptert nyilvános vagy magánhálózati IP-címmel kell definiálni.

### <a name="create-a-virtual-network-subnet-configuration"></a>Virtuális hálózati alhálózat konfigurációjának létrehozása

Először hozzon létre egy alhálózat-konfigurációt a virtuális hálózat számára. Ebben az oktatóanyagban hozzon létre egy alapértelmezett alhálózatot a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmag használatával. Adja meg azokat a változókat, amelyeket korábban inicializált az alhálózat neve és a címe előtaghoz.

> [!NOTE]
> Megadhatja a virtuális hálózati alhálózat konfigurációjának további tulajdonságait ezen parancsmag használatával, de ez az oktatóanyag hatókörén kívül esik.

Futtassa ezt a parancsmagot a virtuális alhálózat konfigurációjának létrehozásához.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ezután hozza létre a virtuális hálózatot az új erőforráscsoporthoz a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) parancsmag használatával. Adja meg azokat a változókat, amelyeket korábban inicializált a név, a hely és a címzés előtagja számára. Használja az előző lépésben megadott alhálózati konfigurációt.

Futtassa ezt a parancsmagot a virtuális hálózat létrehozásához.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása

Most, hogy a virtuális hálózat definiálva van, konfigurálnia kell egy IP-címet a virtuális géphez való csatlakozáshoz. Ebben az oktatóanyagban hozzon létre egy nyilvános IP-címet a dinamikus IP-címzés használatával az internetkapcsolat támogatásához. A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) parancsmag használatával hozza létre a nyilvános IP-címet az új erőforráscsoporthoz. Adja meg azokat a változókat, amelyeket korábban inicializált a név, a hely, a kiosztási módszer és a DNS-tartománynév címkéje számára.

> [!NOTE]
> A nyilvános IP-cím további tulajdonságait is meghatározhatja ezzel a parancsmaggal, de ez a kezdeti oktatóanyag hatókörén kívül esik. Létrehozhat egy magánhálózati címeket vagy egy statikus címekből álló címeket is, de ez az oktatóanyag hatókörén kívül is van.

Futtassa ezt a parancsmagot a nyilvános IP-cím létrehozásához.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása

A virtuális gép biztonságossá tételéhez és SQL Server a forgalomhoz hozzon létre egy hálózati biztonsági csoportot.

1. Először hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a távoli asztal (RDP) számára az RDP-kapcsolatok engedélyezéséhez.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfiguráljon egy olyan hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi a forgalmat a 1433-es TCP-porton. Ez lehetővé teszi, hogy az interneten keresztül SQL Server kapcsolatokat.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Hozza létre a hálózati biztonsági csoportot.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása

Most már készen áll a virtuális gép hálózati adapterének létrehozására. A [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) parancsmag használatával hozza létre a hálózati adaptert az új erőforráscsoporthoz. Adja meg a korábban definiált nevet, helyet, alhálózatot és nyilvános IP-címet.

Futtassa ezt a parancsmagot a hálózati adapter létrehozásához.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Virtuálisgép-objektum konfigurálása

Most, hogy a tárolási és hálózati erőforrások definiálva vannak, készen áll a virtuális gép számítási erőforrásainak definiálására.

- Határozza meg a virtuális gép méretét és az operációs rendszer különböző tulajdonságait.
- Határozza meg a korábban létrehozott hálózati adaptert.
- Adja meg a blob Storage-ot.
- Az operációs rendszer lemezének megadásához.

### <a name="create-the-vm-object"></a>A virtuálisgép-objektum létrehozása

Kezdje a virtuális gép méretének megadásával. Ebben az oktatóanyagban egy DS13 kell megadni. Konfigurálható virtuálisgép-objektum létrehozásához használja a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) parancsmagot. Adja meg a név és a méret korábban inicializált változóit.

Futtassa ezt a parancsmagot a virtuálisgép-objektum létrehozásához.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Hitelesítő objektum létrehozása a helyi rendszergazdai hitelesítő adatok nevének és jelszavának tárolásához

A virtuális gép operációs rendszerének beállítása előtt a helyi rendszergazdai fiók hitelesítő adatait biztonságos karakterláncként kell megadnia. Ennek elvégzéséhez használja a [Get-hitelesítőadat](/powershell/module/microsoft.powershell.security/get-credential) parancsmagot.

Futtassa a következő parancsmagot. A virtuális gép helyi rendszergazdájának nevét és jelszavát be kell írnia a PowerShell hitelesítő adatok kérése ablakba.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>A virtuális gép operációs rendszerének tulajdonságainak beállítása

Most már készen áll a virtuális gép operációs rendszerének tulajdonságainak beállítására a [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) parancsmaggal.

- Az operációs rendszer típusának beállítása Windowsként.
- A [virtuálisgép-ügynök](../../../virtual-machines/extensions/agent-windows.md) telepítésének megkövetelése.
- Azt határozza meg, hogy a parancsmag lehetővé teszi az automatikus frissítést.
- Adja meg a virtuális gép neve, a számítógép neve és a hitelesítő adatok által korábban inicializált változókat.

Futtassa ezt a parancsmagot a virtuális gép operációs rendszerének tulajdonságainak beállításához.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Hálózati adapter hozzáadása a virtuális géphez

Ezután az [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) parancsmag használatával adja hozzá a hálózati adaptert a korábban megadott változóhoz.

Futtassa ezt a parancsmagot a virtuális gép hálózati adapterének beállításához.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Adja meg a virtuális gép által használandó lemez blob Storage-helyét

Ezután állítsa be a virtuális gép lemezének blob Storage-helyét a korábban definiált változókkal.

Futtassa ezt a parancsmagot a blob Storage helyének beállításához.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>A virtuális gép operációsrendszer-lemezének tulajdonságainak beállítása

Ezután állítsa be a virtuális gép operációsrendszer-lemezének tulajdonságait a [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) parancsmag használatával. 

- Azt határozza meg, hogy a virtuális gép operációs rendszere egy rendszerképből fog származni.
- Állítsa be a gyorsítótárazást csak olvasásra (mivel SQL Server ugyanarra a lemezre van telepítve).
- Adja meg a virtuális gép neve és az operációs rendszer lemeze számára korábban inicializált változókat.

Futtassa ezt a parancsmagot a virtuális gép operációsrendszer-lemezének tulajdonságainak beállításához.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>A virtuális gép platformjának rendszerképének megadása

Az utolsó konfigurációs lépés a virtuális géphez tartozó platform rendszerképének megadása. Ebben az oktatóanyagban használja a legújabb SQL Server 2016 CTP-rendszerképet. Használja a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) parancsmagot, hogy ezt a rendszerképet a korábban meghatározott változók használatával használja.

Futtassa ezt a parancsmagot a virtuális géphez tartozó platform rendszerképének megadásához.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása

Most, hogy befejezte a konfigurációs lépéseket, készen áll a virtuális gép létrehozására. A [New-AzVM](/powershell/module/az.compute/new-azvm) parancsmag használatával hozza létre a virtuális gépet a megadott változók használatával.

> [!TIP]
> A virtuális gépek létrehozása néhány percet is igénybe vehet.

Futtassa ezt a parancsmagot a virtuális gép létrehozásához.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Létrejön a virtuális gép.

> [!NOTE]
> Ha hibaüzenet jelenik meg a rendszerindítási diagnosztika használatával kapcsolatban, figyelmen kívül hagyhatja azt. A rendszer létrehoz egy standard szintű Storage-fiókot a rendszerindítási diagnosztika számára, mert a virtuális gép lemezéhez megadott Storage-fiók prémium szintű Storage-fiók.

## <a name="install-the-sql-iaas-agent"></a>Az SQL IaaS-ügynök telepítése

SQL Server virtuális gépek támogatják az automatikus felügyeleti funkciókat a [SQL Server IaaS-ügynök bővítménnyel](sql-server-iaas-agent-extension-automate-management.md). Ha regisztrálni szeretné a SQL Server a bővítménnyel, futtassa a [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) parancsot a virtuális gép létrehozása után. Adja meg a SQL Server VM licencének típusát, válassza az utólagos elszámolású vagy a saját licencet a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával. A licenceléssel kapcsolatos további információkért lásd: [licencelési modell](licensing-model-azure-hybrid-benefit-ahb-change.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```

A bővítmény háromféleképpen regisztrálhat: 
- [Az előfizetésben lévő összes jelenlegi és jövőbeli virtuális gép esetében automatikusan](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuálisan egyetlen virtuális géphez](sql-agent-extension-manually-register-single-vm.md)
- [Több nagy méretű virtuális gép esetén manuálisan](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="stop-or-remove-a-vm"></a>Virtuális gép leállítása vagy eltávolítása

Ha nincs szüksége a virtuális gép folyamatos futtatására, a szükségtelen költségeket elkerülheti, ha nincs használatban. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

A **Remove-AzResourceGroup** paranccsal véglegesen törölheti a virtuális géphez társított összes erőforrást is. Ezzel véglegesen törli a virtuális gépet is, ezért használja ezt a parancsot körültekintően.

## <a name="example-script"></a>Példaszkript

A következő szkript tartalmazza az oktatóanyaghoz tartozó teljes PowerShell-szkriptet. Feltételezi, hogy már beállította az Azure-előfizetést a **AzAccount** és a **Select-AzSubscription** paranccsal való használatra.

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Következő lépések

A virtuális gép létrehozása után a következőket teheti:

- Kapcsolódás a virtuális géphez RDP használatával
- Konfigurálja SQL Server beállításait a virtuális gép portálján, beleértve a következőket:
   - [Tárolási beállítások](storage-configuration.md) 
   - [Automatizált felügyeleti feladatok](sql-server-iaas-agent-extension-automate-management.md)
- [Kapcsolat konfigurálása](ways-to-connect-to-sql.md)
- Ügyfelek és alkalmazások összekötése az új SQL Server példánnyal