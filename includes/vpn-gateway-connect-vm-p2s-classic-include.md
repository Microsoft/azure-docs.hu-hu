---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4732abacc80a24d268f0e5cb7d100079e5a8d90b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104017"
---
Hozzon létre egy Távoli asztali kapcsolat a VNet üzembe helyezett virtuális géphez való kapcsolódáshoz. A virtuális géphez való kapcsolódás legjobb módja, ha a számítógép neve helyett a magánhálózati IP-címével csatlakozik. Így teszteli, hogy tud-e csatlakozni, hogy a névfeloldás megfelelően van-e konfigurálva. 

1. Keresse meg a virtuális gép magánhálózati IP-címét. Egy virtuális gép magánhálózati IP-címének megkereséséhez tekintse meg a virtuális gép tulajdonságait a Azure Portalban, vagy használja a PowerShellt.
2. Győződjön meg arról, hogy a pont – hely VPN-kapcsolattal csatlakozik a VNet. 
3. A Távoli asztali kapcsolat megnyitásához írja be az *RDP* vagy a *Távoli asztali kapcsolat* kifejezést a tálcán található keresőmezőbe, majd válassza a **Távoli asztali kapcsolat** lehetőséget. Azt is megnyithatja, ha az **mstsc** parancsot használja a PowerShellben. 
3. A **Távoli asztali kapcsolat** mezőben adja meg a virtuális gép magánhálózati IP-címét. Ha szükséges, válassza a **Beállítások megjelenítése lehetőséget** a további beállítások módosításához, majd a kapcsolódáshoz.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Egy virtuális géppel létrehozni kívánt RDP-kapcsolat hibaelhárítása

Ha nem sikerül csatlakoznia egy virtuális géphez a VPN-kapcsolaton keresztül, néhány dolgot is megadhat. 

- Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.
- Ellenőrizze, hogy csatlakozik-e a virtuális gép magánhálózati IP-címéhez.
- Adja meg az **ipconfig parancsot** annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címnek a megadásához, amelyhez csatlakozik. Az átfedésben lévő címtartomány akkor fordul elő, ha az IP-cím azon VNet tartományán belül van, amelyhez csatlakozik, vagy a Címtérről lévő címtartomány. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.
- Ha a magánhálózati IP-cím használatával tud csatlakozni a virtuális géphez, de a számítógép neve nem, ellenőrizze, hogy megfelelően konfigurálta-e a DNS-t. A virtuális gépek névfeloldásának működésével kapcsolatos további információkért lásd [a virtuális gépek névfeloldásával](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) foglakozó cikket.
- A VNet DNS-kiszolgáló IP-címeinek megadása után ellenőrizze, hogy a VPN-ügyfél konfigurációs csomagja létrejött-e. Ha frissíti a DNS-kiszolgáló IP-címeit, létrehoz és telepít egy új VPN-ügyfél-konfigurációs csomagot.

A hibaelhárítással kapcsolatos további információkért lásd [a virtuális gép távoli asztali kapcsolatainak hibaelhárításával](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection) foglalkozó cikket.