---
title: Oktatóanyag – Azure-beli virtuális hálózatok létrehozása és kezelése Windows rendszerű virtuális gépekhez
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-beli virtuális hálózatokat Windows rendszerű virtuális gépekhez az Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.collection: windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e7f08e3c9869eae76f6c2e0127cfdab8f770d04
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562180"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Oktatóanyag: Azure-alapú virtuális hálózatok létrehozása és felügyelete Windows rendszerű virtuális gépeken az Azure PowerShell-lel

Az Azure-beli virtuális gépek Azure hálózatkezelést használnak a belső és külső hálózati kommunikációhoz. Ez az oktatóanyag végigvezeti két virtuális gép telepítésén és az Azure hálózatkezelés konfigurálásán ezen virtuális gépekhez. Az oktatóanyagban szereplő példák azt feltételezik, hogy a virtuális gépek egy adatbázis-háttérrel rendelkező webalkalmazást üzemeltetnek, azonban az oktatóanyagban nincs telepítve az alkalmazás. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása


## <a name="vm-networking-overview"></a>Virtuális gépek hálózatkezelése – áttekintés

Az Azure Virtual Networks lehetővé teszi a virtuális gépek, az Internet és más Azure-szolgáltatások, például a Azure SQL Database közötti biztonságos hálózati kapcsolatok létrehozását. A virtuális hálózatok alhálózatnak nevezett logikai szegmensekre oszthatók. Az alhálózatok segítségével szabályozható a hálózati forgalom, valamint biztonsági határként is használhatók. Egy virtuális géphez a telepítéskor általában egy virtuális hálózati adapter tartozik, amely egy alhálózathoz csatlakozik.

Az oktatóanyag végrehajtása során a következő erőforrások jönnek létre:

![Virtuális hálózat két alhálózattal](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Az a virtuális hálózat, amely segítségével a virtuális gépek kommunikálnak egymással és az internettel.
- *myFrontendSubnet* – A *myVNet* előtérbeli erőforrásai által használt alhálózat.
- *myPublicIPAddress* – Az internet felől a *myFrontendVM* elérésére használt nyilvános IP-cím.
- *myFrontendNic* – A *myFrontendVM* által a *myBackendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myFrontendVM* – Az internet és a *myBackendVM* közötti kommunikációra használt virtuális gép.
- *myBackendNSG* – A *myFrontendVM* és a *myBackendVM* közti kommunikációt szabályozó hálózati biztonsági csoport.
- *myBackendSubnet* – *myBackendNSG* csoporthoz társított, és a háttérbeli erőforrások által használt alhálózat.
- *myBackendNic* – A *myBackendVM* által *myFrontendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myBackendVM* – A *myFrontendVM* virtuális géppel a 1433-as porton keresztül kommunikáló virtuális gép.


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="create-subnet"></a>Alhálózat létrehozása 

Ebben az oktatóanyagban egy virtuális hálózatot hozunk létre két alhálózattal, egy előtérbeli alhálózatot egy webalkalmazás üzemeltetéséhez, és egy háttérbeli alhálózatot egy adatbázis-kiszolgáló üzemeltetéséhez.

A virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példa létrehoz egy *myRGNetwork* nevű erőforráscsoportot az *EastUS* régióban:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Hozzon létre egy *myFrontendSubnet* nevű alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Továbbá hozzon létre egy alhálózati konfigurációt *myBackendSubnet* néven:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy *myVNet* nevű VNET a *MyFrontendSubnet* és a *MyBackendSubnet* használatával a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)használatával:

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Ekkorra létrehoztunk egy hálózatot, és két alhálózatra osztottuk, amelyek közül az egyik az előtérbeli szolgáltatásokhoz, a másik a háttérbeli szolgáltatásokhoz tartozik. A következő szakaszban a virtuális gépeket hozzuk létre, majd csatlakoztatjuk azokat az alhálózatokhoz.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-cím lehetővé teszi, hogy az Azure-erőforrások elérhetők legyenek az interneten. A nyilvános IP-cím kiosztási módszere konfigurálható dinamikusnak vagy statikusnak. Alapértelmezés szerint a rendszer dinamikusan osztja ki a nyilvános IP-címeket. A dinamikus IP-címek az egyes virtuális gépek felszabadításakor felszabadulnak. Ennek következtében az IP-cím minden, virtuálisgép-felszabadítást is tartalmazó művelet során módosul.

A kiosztási módszer statikus értékre állítható, amely biztosítja, hogy az IP-cím egy virtuális géphez legyen hozzárendelve, még akkor is, ha a felszabadítási állapotban van. Ha statikus IP-címet használ, maga az IP-cím nem adható meg. Ehelyett a rendszer kiosztja az elérhető címek készletét.

Hozzon létre egy *myPublicIPAddress* nevű nyilvános IP [-címet a New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Ha az -AllocationMethod paramétert `Static` értékre állítja, egy statikus nyilvános IP-címet oszt ki.

## <a name="create-a-front-end-vm"></a>Előtérbeli virtuális gép létrehozása

Ahhoz, hogy a virtuális gép kommunikálhasson a virtuális hálózatban, szüksége van egy virtuális hálózati adapterre (NIC). Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)használatával:

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal. Ezekkel a hitelesítő adatokkal csatlakozhat a virtuális géphez a további lépésekben:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozza létre a virtuális gépeket a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Biztonságos hálózati adatforgalom

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Az NSG-k társíthatók alhálózatokhoz vagy egyedi hálózati adapterekhez. Egy hálózati adapterhez tartozó NSG csak a társított virtuális gépre vonatkozik. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra.

### <a name="network-security-group-rules"></a>Hálózat biztonsági csoportok szabályai

Az NSG-szabályok határozzák meg azokat a hálózatkezelési portokat, amelyeken engedélyezett vagy tiltott a forgalom. A szabályok között szerepelhetnek forrás és cél IP-címtartományok, így szabályozható az adatforgalom adott rendszerek vagy alhálózatok között. Az NSG-szabályok között megadható a prioritás is (1–4096). A szabályokat a rendszer prioritás szerinti sorrendben értékeli. A 100-as prioritású szabályt a rendszer a 200-as prioritású szabály előtt ellenőrzi.

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritáshoz vannak rendelve, a létrehozott szabályok felülbírálják őket.

- **Virtuális hálózat** – A virtuális hálózatból kiinduló és oda érkező forgalom a bejövő és kimenő irányban is engedélyezve van.
- **Internet** – A kimenő forgalom engedélyezett, de a bejövő forgalom le van tiltva.
- **Terheléselosztó** – Engedélyezi az Azure terheléselosztója számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem terheléselosztásos készletet használ, ezt a szabályt felül lehet bírálni.

### <a name="create-network-security-groups"></a>Hálózati biztonsági csoportok létrehozása

Hozzon létre egy *myFrontendNSGRule* nevű bejövő szabályt, hogy engedélyezze a bejövő webes forgalmat a *myFrontendVM* a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)használatával:

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Ha létrehoz egy NSG-t a háttéralhálózat számára, azzal a *myBackendVM* belső forgalmát korlátozhatja csak a *myFrontendVM* gépről érkező forgalomra. Az alábbi példa egy *myBackendNSGRule* nevű NSG-szabályt hoz létre:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Vegyen fel egy *myFrontendNSG* nevű hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Most adjon hozzá egy *myBackendNSG* nevű hálózati biztonsági csoportot a New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Adja hozzá a hálózati biztonsági csoportokat az alhálózatokhoz:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Háttérbeli virtuális gép létrehozása

Az oktatóanyagban lévő háttérbeli virtuális gépek legegyszerűbben egy SQL Server-rendszerképpel hozhatók létre. Ez az oktatóanyag csak létrehozza a virtuális gépet az adatbázis-kiszolgálóval, az adatbázis elérésével kapcsolatos információkat nem részletezi.

A *myBackendNic* létrehozása:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a Get-Credential paranccsal:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozza létre a *myBackendNic* hálózati adaptert.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Ebben a példában a rendszerkép SQL Server telepítve van, de ez az oktatóanyag nem használható. Ennek részeként megtudhatja, hogyan konfigurálhat egy virtuális gépet a webes forgalom és az adatbázis-kezelés kezelésére szolgáló virtuális gép használatával.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban virtuális gépekhez csatolva hozta létre és biztosította az Azure-hálózatokat. 

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása

A virtuális gépek lemezeinek védelméről további információért lásd: [biztonsági mentés és vész-helyreállítás lemezek esetén](../backup-and-disaster-recovery-for-azure-iaas-disks.md).