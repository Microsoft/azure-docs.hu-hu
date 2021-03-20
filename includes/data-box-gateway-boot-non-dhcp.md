---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582164"
---
Ha nem DHCP-környezetben hajtja végre a rendszerindítást, az alábbi lépésekkel telepítheti a virtuális gépet a Data Box Gateway számára.

1. [Kapcsolódjon az eszköz Windows PowerShell-felületéhez](#connect-to-the-powershell-interface).
2. A `Get-HcsIpAddress` parancsmag használatával listázhatja a virtuális eszközön engedélyezett hálózati adaptereket. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

    A következő példa a parancsmag használatát mutatja be:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Lásd a következő példát:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

