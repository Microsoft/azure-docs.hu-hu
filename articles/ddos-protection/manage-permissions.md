---
title: Azure DDoS Protection csomag engedélyei
description: Ismerje meg, hogyan kezelheti az engedélyeket egy védelmi tervben.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806256"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>DDoS Protection csomagok kezelése: engedélyek és korlátozások

A DDoS elleni védelmi terv különböző régiókban és előfizetésekben működik. Ugyanez a csomag más előfizetésekhez tartozó virtuális hálózatokhoz is társítható a különböző régiókban, a bérlők között. Az előfizetés, amelyhez a terv társítva van, a csomag havi ismétlődő számlájának, valamint a túlterhelési díjaknak a része, ha a védett nyilvános IP-címek száma meghaladja a 100-ot. A DDoS díjszabásával kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Előfeltételek

- Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md).

## <a name="permissions"></a>Engedélyek

A DDoS elleni védelmi tervekkel való együttműködéshez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Művelet                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/ddosProtectionPlans/READ        | DDoS Protection-csomag beolvasása              |
| Microsoft. Network/ddosProtectionPlans/Write       | DDoS elleni védelmi terv létrehozása vagy frissítése  |
| Microsoft. Network/ddosProtectionPlans/delete      | DDoS elleni védelmi terv törlése            |
| Microsoft. Network/ddosProtectionPlans/csatlakozás/művelet | A DDoS elleni védelmi terv csatlakoztatása              |

Ha engedélyezni szeretné a DDoS Protectiont egy virtuális hálózat számára, a fióknak hozzá kell rendelnie a [virtuális hálózatok megfelelő műveleteit](../virtual-network/manage-virtual-network.md#permissions)is.

## <a name="azure-policy"></a>Azure Policy

A legtöbb szervezet esetében nem szükséges több csomag létrehozása. A csomag nem helyezhető át az előfizetések között. Ha módosítani szeretné a csomag előfizetését, törölnie kell a meglévő csomagot, és létre kell hoznia egy újat.

Azon ügyfelek esetében, akik különböző előfizetésekkel rendelkeznek, és a bérlőn belül egyetlen csomagot szeretne biztosítani a Cost Control számára, a Azure Policy segítségével [korlátozhatja Azure DDoS Protection standard csomagok létrehozását](https://aka.ms/ddosrestrictplan). Ez a szabályzat letiltja a DDoS-csomagok létrehozását, kivéve, ha az előfizetés korábban kivételként van megjelölve. Ez a szabályzat az összes olyan előfizetés listáját is megjeleníti, amelynek a DDoS-terve telepítve van, de nem.


## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan tekintheti meg és konfigurálhatja a DDoS elleni védelmi terv telemetria, folytassa az oktatóanyagokkal.

> [!div class="nextstepaction"]
> [DDoS Protection-telemetria megtekintése és konfigurálása](telemetry.md)
