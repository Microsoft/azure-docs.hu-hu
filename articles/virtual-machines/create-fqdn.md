---
title: Teljes tartománynév létrehozása egy virtuális géphez a Azure Portal
description: Megtudhatja, hogyan hozhat létre teljes tartománynevet (FQDN) egy virtuális géphez a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42c53e72de7f6b2943086544f607042957f4b849
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220079"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Teljes tartománynév létrehozása a Azure Portal Linux rendszerű virtuális gép számára

Amikor létrehoz egy virtuális gépet (VM) a [Azure Portal](https://portal.azure.com), a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. Ezt az IP-címet használja a virtuális gép távoli eléréséhez. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után egyet is hozzáadhat. Ez a cikk a DNS-név vagy a teljes tartománynév létrehozásának lépéseit mutatja be. 

## <a name="create-a-fqdn"></a>Teljes tartománynév létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet. Ha szükséges, [Linux](./linux/quick-create-portal.md) vagy [Windows rendszerű](./windows/quick-create-portal.md) virtuális gépet is létrehozhat a portálon. A virtuális gép működése után kövesse az alábbi lépéseket:


1. Válassza ki a virtuális gépet a portálon. 
1. A bal oldali menüben válassza a **Tulajdonságok** lehetőséget.
1. A **nyilvános IP-address\DNS neve felirat** alatt válassza ki az IP-címét.
2. A **DNS-név felirat** alatt adja meg a használni kívánt előtagot.
3. Válassza a **Mentés** lehetőséget az oldal tetején.
4. A bal oldali menüben válassza az **Áttekintés** lehetőséget, hogy visszatérjen a virtuális gép áttekintés paneljére.
5. Ellenőrizze, hogy a **DNS-név** megfelelően jelenik-e meg. 

## <a name="next-steps"></a>Következő lépések

A DNS-t [Azure DNS zónák](../dns/dns-getstarted-portal.md)használatával is kezelheti.

