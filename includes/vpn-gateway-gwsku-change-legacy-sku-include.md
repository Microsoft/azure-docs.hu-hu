---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010782"
---
Ha a Resource Manager-alapú üzemi modellt használ, az új átjárók SKU-ra válthat. Ha örökölt átjáró SKU-ról egy új SKU-ra vált, törölheti a meglévő VPN-átjárót, és létrehozhat egy új VPN-átjárót.

Munkafolyamat

1. Távolítson el minden, a virtuális hálózat átjárójához tartozó kapcsolatot.
2. Törölje a régi VPN-átjárót.
3. Hozza létre az új VPN-átjárót.
4. Frissítse a helyszíni VPN-eszközt az új VPN-átjáró IP-címével (a helyek közötti kapcsolatokhoz).
5. Frissítse minden olyan helyi virtuális hálózatok közötti kapcsolat átjárója IP-címének értékét, amely ehhez az átjáróhoz kapcsolódik.
6. Töltse le az új VPN-konfigurációs csomagokat a virtuális hálózathoz ezen a VPN-átjárón keresztül kapcsolódó P2S-ügyfelekhez.
7. Hozza létre újra a virtuális hálózat átjárójához tartozó kapcsolatokat.

Szempontok:

* Az új SKU-ra való áttéréshez a VPN-átjárónak a Resource Manager-alapú üzemi modellben kell lennie.
* Ha klasszikus VPN-átjáróval rendelkezik, továbbra is az adott átjáró korábbi örökölt SKU-ának használatát kell használnia, azonban átméretezheti az örökölt SKU-ket. Az új SKU-ra nem módosítható.
* Ha örökölt SKU-ról egy új SKU-ra vált, a kapcsolat leállása lesz.
* Új átjáró-SKU-ra való váltáskor a VPN-átjáró nyilvános IP-címe megváltozik. Ez akkor is megtörténik, ha ugyanazt a nyilvános IP-címet adta meg, amelyet korábban használt.