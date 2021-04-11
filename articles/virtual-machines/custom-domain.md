---
title: Egyéni tartomány létrehozása és használata
description: Egyéni tartomány összekötése egy Azure-beli virtuális géppel.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250981"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Egyéni tartomány hozzáadása az Azure-beli virtuális géphez vagy erőforráshoz

Az Azure-ban többféle módon csatlakozhat egy egyéni tartományt a virtuális géphez vagy erőforráshoz. Bármely nyilvános IP-címmel rendelkező erőforrás (virtuális gép, Load Balancer, Application Gateway) esetében a legközvetlenebb mód egy rekord létrehozása a megfelelő tartományregisztrálónál. 

## <a name="prerequisites"></a>Előfeltételek 
- Szüksége van egy virtuális gépre, amelyen fut egy webkiszolgáló. [A rövid útmutató segítségével létrehozhat](./linux/quick-create-cli.md) egy virtuális gépet, és hozzáadhatja az NGINX-et.

- A virtuális gépnek elérhetőnek kell lennie a weben (80-as vagy 443-as port). A biztonságosabb üzembe helyezéshez helyezze a virtuális gépet egy terheléselosztó mögé, vagy Application Gateway először. További információ: gyors útmutató [: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Rendelkeznie kell egy meglévő tartománnyal, és hozzá kell férnie a DNS-beállításokhoz. További információ: [egyéni tartomány vásárlása Azure app Service számára](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Egyéni tartomány hozzáadása a virtuális gép nyilvános IP-címéhez

Amikor létrehoz egy virtuális gépet a Azure Portalban, a rendszer automatikusan létrehoz egy nyilvános IP-erőforrást a virtuális géphez. A nyilvános IP-cím megjelenik a virtuális gép áttekintése oldalon. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Megjeleníti a nyilvános IP-címet a virtuális gép áttekintő oldalának Essentials (alapvető erőforrások) szakaszában.":::

Ha az IP-címet választja, további információkat tekinthet meg. Győződjön meg arról, hogy az **IP-hozzárendelés** **statikus** értékre van állítva. A statikus IP-cím nem változik meg, ha a virtuális gép vagy erőforrás újraindul vagy leáll.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Megjeleníti a nyilvános IP-konfigurációt, így láthatja, hogy az IP-cím statikus-e.":::

Ha az IP-címe nem statikus, akkor létre kell hoznia egy teljes tartománynevet. 

1. Válassza ki a virtuális gépet a portálon. 
1. A bal oldali menüben válassza a **Tulajdonságok** lehetőséget.
1. A **nyilvános IP-address\DNS neve felirat** alatt válassza ki az IP-címét.
2. A **DNS-név felirat** alatt adja meg a használni kívánt előtagot.
3. Válassza a **Mentés** lehetőséget az oldal tetején.
4. A bal oldali menüben válassza az **Áttekintés** lehetőséget, hogy visszatérjen a virtuális gép áttekintés paneljére.
5. Ellenőrizze, hogy a *DNS-név* megfelelően jelenik-e meg. 

Nyisson meg egy böngészőt, és adja meg az IP-címét vagy teljes tartománynevét, és győződjön meg arról, hogy a virtuális gépen futó webes tartalom látható.
 
A statikus IP-cím vagy a teljes tartománynév ellenőrzése után nyissa meg a tartományi szolgáltatót, és navigáljon a DNS-beállítások elemre.

Ha hozzáad egy *rekordot* , amely a nyilvános IP-címére vagy a teljes tartománynévre mutat. Példaképp a GoDaddy tartományregisztráló eljárása a következő:
1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.
2. A **tartományok** szakaszban válassza az **összes kezelése**, majd a **DNS lehetőséget | Zónák kezelése**.
3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.
4. A DNS-kezelés lapon válassza a Hozzáadás lehetőséget, majd válassza ki a kívánt elemet a típus listából.
5. Fejezze be az a bejegyzés mezőit:
    - Típus: hagyja meg **A** kiválasztott elemet.
    - Gazdagép: adja meg **@**
    - A következőre mutat: adja meg a virtuális gép nyilvános IP-címét vagy teljes tartománynevét. 
    - TTL: hagyjon egy órát.
6. Kattintson a **Mentés** gombra.

A rekord bejegyzése a DNS-rekordok táblába kerül.
 
A rekord létrehozása után általában körülbelül egy órát vesz igénybe a DNS-propagálás, de esetenként akár 48 órát is igénybe vehet. 


 
## <a name="next-steps"></a>Következő lépések
A [TLS-lezárás és a végpontok közötti TLS áttekintése Application Gateway](../application-gateway/ssl-overview.md)használatával.

 
