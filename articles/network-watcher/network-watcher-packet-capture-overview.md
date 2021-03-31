---
title: A csomagok rögzítésének bemutatása az Azure Network Watcherban | Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher Packet Capture funkcióról
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1c458508dbf8d98349ec8549af32e3dd48bbd09b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966428"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Az Azure Network Watcher változó csomagjainak rögzítésének bemutatása

Network Watcher változó csomagok rögzítése lehetővé teszi a csomagok rögzítési munkameneteinek létrehozását a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A csomagok rögzítése segít a hálózati rendellenességek reaktív és proaktív módon történő diagnosztizálásában. A többi felhasználás magában foglalja a hálózati statisztikák összegyűjtését, a hálózati behatolásokkal kapcsolatos információk megszerzését, az ügyfél és a kiszolgáló közötti kommunikáció hibakeresését.

A csomagok rögzítése olyan virtuálisgép-bővítmény, amely Network Watcheron keresztül távolról elindult. Ez a funkció megkönnyíti a csomagok manuális futtatásának terhét a kívánt virtuális gépen, amely értékes időt takarít meg. A csomagok rögzítése a portál, a PowerShell, a CLI vagy a REST API segítségével indítható el. A csomagok rögzítésének elindításának egyik példája a virtuális gépekkel kapcsolatos riasztások. A rögzítési munkamenethez szűrőket biztosít a figyelni kívánt forgalom rögzítésének biztosításához. A szűrők 5 rekordos (protokoll, helyi IP-cím, távoli IP-cím, helyi port és távoli port) adatain alapulnak. A rögzített adattároló a helyi lemezen vagy egy tárolási blobban tárolódik. Régiónként legfeljebb 10 csomag-rögzítési munkamenet érhető el. Ez a korlát csak a munkamenetekre vonatkozik, és nem vonatkozik a mentett csomag-rögzítési fájlokra helyileg a virtuális gépen vagy egy Storage-fiókban.

> [!IMPORTANT]
> A csomagok rögzítése a virtuálisgép-bővítményt igényli `AzureNetworkWatcherExtension` . A bővítmény Windows rendszerű virtuális gépen való telepítéséhez látogasson el az [azure Network Watcher Agent virtuálisgép-bővítmény a Windows](../virtual-machines/extensions/network-watcher-windows.md) rendszerhez és a Linux rendszerű virtuális gépekhez látogasson el az [Azure Network Watcher Agent virtuálisgép-bővítménye Linuxra](../virtual-machines/extensions/network-watcher-linux.md).

Ha csökkenteni szeretné a rögzített adatokat, csak a kívánt információkra, a következő lehetőségek állnak rendelkezésre a csomagok rögzítési munkamenetéhez:

**Rögzítési konfiguráció**

|Tulajdonság|Leírás|
|---|---|
|**Maximális bájt/csomag (bájt)** | A rögzített csomagok bájtjainak száma, az összes bájt rögzítése, ha üresen marad. A rögzített csomagok bájtjainak száma, az összes bájt rögzítése, ha üresen marad. Ha csak az IPv4-fejlécre van szüksége – itt jelezze a 34 |
|**Maximális bájt/munkamenet (bájt)** | A rögzített bájtok teljes száma, ha az érték elérte a munkamenet végét.|
|**Időkorlát (másodperc)** | Beállítja a csomag rögzítési munkamenetének időbeli korlátozását. Az alapértelmezett érték 18000 másodperc vagy 5 óra.|

**Szűrés (nem kötelező)**

|Tulajdonság|Leírás|
|---|---|
|**Protokoll** | A csomagok rögzítésének szűrésére szolgáló protokoll. Az elérhető értékek a TCP, az UDP és az összes.|
|**Helyi IP-cím** | Ez az érték a csomagok rögzítését olyan csomagokra szűri, amelyekben a helyi IP-cím megfelel ennek a szűrő értéknek.|
|**Helyi port** | Ez az érték szűri a csomagok rögzítését azokra a csomagokat, amelyeken a helyi port megfelel ennek a szűrő értéknek.|
|**Távoli IP-cím** | Ez az érték szűri a csomagok rögzítését olyan csomagokat, amelyeken a távoli IP-cím megfelel ennek a szűrő értéknek.|
|**Távoli port** | Ez az érték szűri a csomagok rögzítését azokra a csomagokat, amelyeken a távoli port megfelel ennek a szűrő értéknek.|

### <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan kezelheti a csomagokat a portálon keresztül a [Azure Portal](network-watcher-packet-capture-manage-portal.md) vagy a PowerShell segítségével a csomagok rögzítésének kezelése a [PowerShell](network-watcher-packet-capture-manage-powershell.md)használatával című témakörben.

Megtudhatja, hogyan hozhat létre proaktív csomagokat a virtuális gépek riasztásai alapján, ha meglátogatja a [riasztás által aktivált csomagok rögzítése lehetőséget](network-watcher-alert-triggered-packet-capture.md) .

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png