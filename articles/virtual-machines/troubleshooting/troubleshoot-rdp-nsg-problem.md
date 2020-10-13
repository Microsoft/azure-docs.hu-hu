---
title: Nem lehet csatlakozni az Azure-beli virtuális gépekhez, mert az RDP-port nincs engedélyezve a NSG-ben | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a problémát, amikor az RDP nem sikerül, mert a NSG konfigurációja a Azure Portalban | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 878e2c233f2171c3c9a6fbd2a8d629d3f3987c3a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976725"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nem lehet távolról kapcsolódni a virtuális géphez, mert az RDP-port nincs engedélyezve a NSG

Ez a cikk azt ismerteti, hogyan lehet megoldani egy olyan problémát, amely nem tud csatlakozni egy Azure-beli Windows rendszerű virtuális géphez (VM), mert a hálózati biztonsági csoportban (NSG) nincs engedélyezve a RDP protokoll (RDP) port.


## <a name="symptom"></a>Hibajelenség

Nem lehet RDP-kapcsolat az Azure-beli virtuális géppel, mert az RDP-port nincs megnyitva a hálózati biztonsági csoportban.

## <a name="solution"></a>Megoldás 

Új virtuális gép létrehozásakor az internetről érkező összes forgalom alapértelmezés szerint le van tiltva. 

Az RDP-port NSG való engedélyezéséhez kövesse az alábbi lépéseket:
1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com).
2. A **Virtual Machines**területen válassza ki a problémával rendelkező virtuális gépet. 
3. A **Beállítások**területen válassza a **hálózatkezelés**lehetőséget. 
4. A **bejövő portok szabályai**területen győződjön meg arról, hogy az RDP-port helyesen van-e beállítva. Az alábbi példa a konfigurációt szemlélteti: 

    **Prioritás**: 300 </br>
    **Név**: Port_3389 </br>
    **Port (cél)**: 3389 </br>
    **Protokoll**: TCP </br>
    **Forrás**: bármely </br>
    **Célhelyek**: bármely </br>
    **Művelet**: Engedélyezés </br>

Ha megadja a forrás IP-címét, akkor ez a beállítás csak az adott IP-címről vagy IP-címtartományból érkező forgalmat engedélyezi a virtuális géphez való kapcsolódáshoz. Győződjön meg arról, hogy az RDP-munkamenet elindításához használt számítógép a tartományon belül van.

További információ a NSG: [hálózati biztonsági csoport](../../virtual-network/network-security-groups-overview.md).

> [!NOTE]
> Az 3389-es RDP-port elérhető az interneten. Ezért azt javasoljuk, hogy ezt a portot csak teszteléshez ajánlott használni. Éles környezetekben ajánlott VPN-vagy magánhálózati kapcsolatokat használni.

## <a name="next-steps"></a>Következő lépések

Ha az RDP-port már engedélyezve van a NSG-ben, olvassa el a következőt: [RDP általános hiba megoldása az Azure virtuális gépen](./troubleshoot-rdp-general-error.md).