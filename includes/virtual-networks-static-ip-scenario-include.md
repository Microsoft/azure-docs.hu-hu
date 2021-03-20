---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015924"
---
## <a name="scenario"></a>Eset

A virtuális gép statikus IP-címének konfigurálásához a dokumentum a következő forgatókönyvet használja:

![Virtuális hálózati forgatókönyv: előtér-és háttérbeli alhálózatok statikus IP-címmel az előtér-alhálózathoz](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

Ebben az esetben létre kell hoznia egy *DNS01* nevű virtuális gépet a *FrontEnd* alhálózatban, majd be kell állítania, hogy a *192.168.1.101* statikus IP-címét használja.
