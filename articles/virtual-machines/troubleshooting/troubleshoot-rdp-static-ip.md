---
title: Statikus IP-cím miatt nem lehet távoli asztali Azure-Virtual Machinesni | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a statikus IP-Microsoft Azure által okozott RDP-problémát. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: bf19a6f77a87f2424f9e7b889e48119d57d1e2e5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820982"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Statikus IP-cím miatt nem lehet távoli asztali Azure-Virtual Machines

Ez a cikk egy olyan problémát ismertet, amelyben a virtuális gép statikus IP-címének konfigurálása után nem lehet távoli asztali Azure-Windows Virtual Machines (VM).


## <a name="symptoms"></a>Hibajelenségek

Amikor RDP-kapcsolattal csatlakozik egy Azure-beli virtuális géphez, a következő hibaüzenet jelenik meg:

**Távoli asztal nem tud csatlakozni a távoli számítógéphez a következő okok valamelyike miatt:**

1. **A kiszolgálóhoz való távoli hozzáférés nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógép be van-e kapcsolva és csatlakoztatva van-e a hálózathoz, és hogy engedélyezve van-e a távoli hozzáférés.**

Amikor bejelöli a képernyőképet a Azure Portal [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) eszközében, a virtuális gép rendesen elindul, és megvárja a hitelesítő adatokat a bejelentkezési képernyőn.

## <a name="cause"></a>Ok

A virtuális gép statikus IP-címmel rendelkezik, amely a Windowson belüli hálózati adapteren van meghatározva. Ez az IP-cím eltér a Azure Portalban definiált címtől.

## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához a soros vezérlőelem használatával engedélyezze a DHCP-t, vagy állítsa vissza a virtuális gép [hálózati adapterét](reset-network-interface.md) .

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg a [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md)című témakört.
2. Ellenőrizze, hogy a DHCP le van-e tiltva a hálózati adapteren:

    ```console
    netsh interface ip show config
    ```

3. Ha a DHCP le van tiltva, állítsa be a hálózati adapter konfigurációját a DHCP használatára:

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhcp
    ```

    Ha például az "Ethernet 2" nevű kapcsolati felület neve, futtassa a következő parancsot:

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhcp
    ```

4. Az IP-konfiguráció ismételt lekérdezésével győződjön meg arról, hogy a hálózati adapter helyesen van beállítva. Az új IP-címnek egyeznie kell az Azure által biztosítotttal.

    ```console
    netsh interface ip show config
    ```

    Ezen a ponton nem kell újraindítani a virtuális gépet. A virtuális gép elérhető lesz.

Ezt követően, ha a statikus IP-címet szeretné konfigurálni a virtuális géphez, tekintse meg a [statikus IP-címek konfigurálása virtuális géphez](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)című témakört.
