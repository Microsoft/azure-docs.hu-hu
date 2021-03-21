---
title: Az IP flow ellenőrzésének bemutatása az Azure Network Watcherban | Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher IP flow ellenőrzési képességéről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: d6f6fe81c121f547f79fa34be77aab1fb6c0875a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899354"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Az IP flow ellenőrzésének bemutatása az Azure Network Watcher

Az IP-forgalom ellenőrzi, hogy egy csomag engedélyezett vagy megtagadott egy virtuális gépről. Az információ a következőkből áll: irány, protokoll, helyi IP, távoli IP-cím, helyi port és távoli port. Ha a csomagot egy biztonsági csoport megtagadja, a rendszer a csomagot megtagadó szabály nevét adja vissza. A forrás vagy a cél IP-címének kiválasztása esetén az IP flow ellenőrzése lehetővé teszi a rendszergazdák számára, hogy gyorsan diagnosztizálják a kapcsolati problémákat az internetről, illetve a helyszíni környezetből vagy a szolgáltatásból.

Az IP-folyamat ellenőrzése a hálózati adapterre alkalmazott összes hálózati biztonsági csoport (NSG), például egy alhálózat vagy egy virtuális gép hálózati adapterének szabályait vizsgálja. Ezt követően a rendszer ellenőrzi, hogy a forgalmi folyamat a megadott hálózati adapterre vagy onnan érkező beállítások alapján van-e ellenőrizve. Az IP-forgalom ellenőrzése akkor lehet hasznos, ha a hálózati biztonsági csoport egyik szabálya blokkolja a virtuális gép bejövő vagy kimenő forgalmát.

A Network Watcher egy példányát minden olyan régióban létre kell hozni, amelyet az IP-forgalom ellenőrzéséhez tervez futtatni. A Network Watcher regionális szolgáltatás, és csak ugyanabban a régióban lévő erőforrásokon futtatható. A felhasznált példány nem befolyásolja az IP-forgalom ellenőrzésének eredményét, mivel a hálózati adapterhez vagy az alhálózathoz társított bármely útvonal továbbra is visszaadja.

![1][1]

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogy egy csomag engedélyezett vagy megtagadott egy adott virtuális gépen a portálon keresztül. [Ellenőrizze, hogy engedélyezett-e a forgalom egy IP-flow-t használó virtuális gépen a portál használatával](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

