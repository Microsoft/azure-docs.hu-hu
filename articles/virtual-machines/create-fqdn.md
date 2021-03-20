---
title: Teljes tartománynév létrehozása egy virtuális géphez a Azure Portal
description: Megtudhatja, hogyan hozhat létre teljes tartománynevet (FQDN) egy virtuális géphez a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132064"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Teljes tartománynév létrehozása a Azure Portal Linux rendszerű virtuális gép számára

Amikor létrehoz egy virtuális gépet (VM) a [Azure Portal](https://portal.azure.com), a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. Ezt az IP-címet használja a virtuális gép távoli eléréséhez. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után egyet is hozzáadhat. Ez a cikk a DNS-név vagy a teljes tartománynév létrehozásának lépéseit mutatja be. 

## <a name="create-a-fqdn"></a>Teljes tartománynév létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet. Ha szükséges, [Linux](./linux/quick-create-portal.md) vagy [Windows rendszerű](./windows/quick-create-portal.md) virtuális gépet is létrehozhat a portálon. A virtuális gép működése után kövesse az alábbi lépéseket:


1. Válassza ki a virtuális gépet a portálon. 
1. A bal oldali menüben válassza a **Konfigurálás** lehetőséget.
1. A **DNS-név felirat** alatt adja meg a használni kívánt előtagot.
1. Válassza a **Mentés** lehetőséget az oldal tetején.
1. A bal oldali menüben az **Áttekintés** lehetőség kiválasztásával térjen vissza a virtuális gép áttekintés paneljére. 
1. Ellenőrizze, hogy a *DNS-név* megfelelően jelenik-e meg. 

## <a name="next-steps"></a>Következő lépések

A DNS-t [Azure DNS zónák](../dns/dns-getstarted-portal.md)használatával is kezelheti.

