---
title: SQL Server létrehozása Windows rendszerű virtuális gépen Azure PowerShell-vel | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan használható a Azure PowerShell a SQL Server 2017-at futtató Windows rendszerű virtuális gépek létrehozásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356837"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Gyors útmutató: SQL Server létrehozása Windows rendszerű virtuális gépen Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a rövid útmutató végigvezeti a SQL Server virtuális gép (VM) Azure PowerShell használatával történő létrehozásán.

> [!TIP]
> - Ez a rövid útmutató bemutatja az SQL virtuális gépek gyors kiépítését és az azokhoz való csatlakozást. Az SQL virtuális gépek létrehozásának egyéb Azure PowerShell lehetőségeiről további információért lásd: [Kiépítési útmutató az SQL-kiszolgálón futó virtuális gépekhez az Azure PowerShell használatával](create-sql-vm-powershell.md).
> - Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Azure-előfizetés beszerzése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="get-azure-powershell"></a><a id="powershell"></a> Az Azure PowerShell beszerzése

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>A PowerShell konfigurálása

1. A **AzAccount** parancs futtatásával nyissa meg a PowerShellt, és hozzon létre hozzáférést az Azure-fiókjához.

   ```powershell
   Connect-AzAccount
   ```

1. Amikor megjelenik a bejelentkezési ablak, adja meg a hitelesítő adatait. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. Adjon meg egy változót egy egyedi erőforráscsoport-névvel. A rövid útmutató további részének egyszerűsítése érdekében a többi parancs ezt a nevet használja az egyéb erőforrásnevek alapján.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Adjon meg egy Azure-célrégiót a virtuális gép összes erőforrásához.

   ```powershell
   $Location = "East US"
   ```

1. Hozza létre az erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása

1. Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Egy hálózati biztonsági csoport létrehozása; Konfiguráljon szabályokat a távoli asztali (RDP) és SQL Server-kapcsolatok lehetővé tételéhez.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Hozza létre a hálózati adaptert.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása

1. Adja meg a hitelesítő adatait a virtuális gépre való bejelentkezéshez. A Felhasználónév "azureadmin". A parancs futtatása előtt győződjön meg róla, hogy módosul \<password> .

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Hozzon létre egy virtuálisgép-konfigurációs objektumot, majd hozza létre a virtuális gépet. A következő parancs létrehoz egy SQL Server 2017 Developer Edition virtuális gépet a Windows Server 2016 rendszeren.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > A virtuális gép létrehozása több percig tart.

## <a name="register-with-sql-vm-rp"></a>Regisztrálás az SQL VM erőforrás-szolgáltatónál 

A portál-integráció és az SQL-alapú virtuális gépek funkcióinak beszerzéséhez regisztrálnia kell az [SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md).

A teljes funkcionalitás eléréséhez teljes módban regisztrálnia kell a bővítményt. Ezzel azonban újraindítja a SQL Server szolgáltatást, ezért az ajánlott módszer az, hogy egyszerű módban regisztrálja a regisztrációt, majd a karbantartási időszak alatt teljes egészében frissítsen. 

Először regisztrálja a SQL Server VMt könnyű módban: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Ezután a karbantartási időszak alatt frissítsen a teljes módra: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Távoli asztal a virtuális gépen

1. Az új virtuális gép nyilvános IP-címének lekéréséhez használja a következő parancsot.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Adja át a visszaadott IP-címet parancssori paraméterként az **mstsc** számára, hogy egy távoli asztal munkamenetet indítson el az új virtuális gépre.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Amikor a rendszer a hitelesítő adatokat kéri, válassza egy másik fiók hitelesítő adatainak megadását. Adja meg a felhasználónevet az előző fordított perjeltel (például: `\azureadmin` ), valamint az ebben a rövid útmutatóban korábban megadott jelszót.

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

1. A Távoli asztal-munkamenetbe való bejelentkezés után indítsa el **SQL Server Management Studio 2017** alkalmazást a Start menüből.

1. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen tartsa meg az alapértelmezett értékeket. A kiszolgáló neve a virtuális gép neve. A Hitelesítés értéke **Windows Authentication** (Windows-hitelesítés). Válassza a **Kapcsolódás** lehetőséget.

Mostantól a SQL Server helyileg csatlakozik. Ha távolról szeretne csatlakozni, a Azure Portal vagy manuálisan kell [konfigurálnia a kapcsolatot](ways-to-connect-to-sql.md) .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nincs szüksége a virtuális gép folyamatos futtatására, a szükségtelen költségeket elkerülheti, ha nincs használatban. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

A **Remove-AzResourceGroup** paranccsal véglegesen törölheti a virtuális géphez társított összes erőforrást is. Ezzel véglegesen törli a virtuális gépet is, ezért használja ezt a parancsot körültekintően.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy SQL Server 2017-et futtató virtuális gépet hozott létre az Azure PowerShell használatával. Az adatok az új SQL Serverre való migrálásával kapcsolatos további információkért lásd a következő cikket.

> [!div class="nextstepaction"]
> [Adatbázisok migrálása SQL virtuális gépekre](migrate-to-vm-from-sql-server.md)
