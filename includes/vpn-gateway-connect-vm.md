---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5d631d8492ed1869bdc244e2cc90595183892822
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105103955"
---
Ha létrehoz egy távoli asztali kapcsolatot a virtuális géppel, csatlakozhat egy virtuális hálózaton üzembe helyezett virtuális géphez. A legjobb mód arra, hogy először ellenőrizze, hogy tud-e csatlakozni a virtuális géphez, ha egy magánhálózati IP-címet használ a számítógép neve helyett. Ily módon azt teszteli, hogy tud-e csatlakozni, nem azt, hogy a névfeloldás megfelelően van-e konfigurálva.

1. Keresse meg a magánhálózati IP-címet. Ha egy virtuális gép magánhálózati IP-címét szeretné megkeresni, vagy tekintse meg a virtuális gép tulajdonságait az Azure Portalon, vagy használja a PowerShellt.

   * Azure Portal – Keresse meg a virtuális gépet az Azure Portalon. Tekintse meg a virtuális gép tulajdonságait. A magánhálózati IP-cím a listában szerepel.

   * PowerShell – A példa segítségével tekintse meg a virtuális gépek listáját és magánhálózati IP-címeket az erőforráscsoportokból. Ezt a példát nem kell használat előtt módosítania.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. Győződjön meg arról, hogy pont–hely típusú VPN-kapcsolattal csatlakozik a virtuális hálózathoz.
1. Nyissa meg **Távoli asztali kapcsolat** az "RDP" vagy a "távoli asztali kapcsolat" beírásával a tálcán található keresőmezőbe, majd válassza a távoli asztali kapcsolat lehetőséget. A távoli asztali kapcsolatot megnyithatja a PowerShell „mstsc” parancsával is. 
1. A távoli asztali kapcsolatban írja be a virtuális gép magánhálózati IP-címét. A további beállításokat a „Beállítások megjelenítése” gombra kattintva módosíthatja. Ha végzett, hozza létre a kapcsolatot.

**Kapcsolat hibaelhárítása**

Ha probléma adódik egy virtuális gép VPN-kapcsolaton keresztüli csatlakoztatása során, ellenőrizze az alábbiakat:

* Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.

* Ellenőrizze, hogy a virtuális gép magánhálózati IP-címéhez csatlakozik-e.

* Ha tud csatlakozni a virtuális géphez a magánhálózati IP-címmel, de a számítógép nevével nem, ellenőrizze, hogy a DNS-konfiguráció megfelelő-e. A virtuális gépek névfeloldásának működésével kapcsolatos további információkért lásd [a virtuális gépek névfeloldásával](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) foglakozó cikket.

* Az RDP-kapcsolatokkal kapcsolatos további információkért lásd [a virtuális gép távoli asztali kapcsolatainak hibaelhárításával](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection) foglalkozó cikket.