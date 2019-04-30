---
title: Zónaredundáns előtér - Azure portal a Load Balancer létrehozása
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozhat létre egy nyilvános Standard Load Balancer zónaredundáns nyilvános előtérbeli IP-Címmel cím az Azure portal használatával
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 448ae5f8a615a526460ac92eaaf6c7d16761aec2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684917"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Standard Load Balancer létrehozása az Azure portal használatával, a zónaredundáns frontend

Ez a cikk végigvezeti egy nyilvános létrehozása [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) és a egy zónaredundáns frontend-IP szabványos nyilvános cím segítségével. A Standard Load Balancer egy egyetlen előtérbeli IP-cím zónaredundáns alapértelmezés szerint.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
>  A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-zone-redundant-load-balancer"></a>Zóna zónaredundáns load balancer létrehozása

1. Egy böngészőből keresse fel az Azure portal: [ https://portal.azure.com ](https://portal.azure.com) és az Azure-fiókjával jelentkezzen be.
2. A képernyő bal felső sarkában válassza **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancert.**
3. Az a **terheléselosztó létrehozása** lap **neve** típus **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. Válassza a Termékváltozat, **Standard**.
6. Kattintson a **nyilvános IP-cím**, kattintson a **új létrehozása**, majd a **nyilvános IP-cím létrehozása** oldalon, a név, típus **myPublicIPStandard**.
    >[!NOTE] 
    > A nyilvános IP-ebben a lépésben létrehozott Standard termékváltozat és zónaredundáns alapértelmezés szerint. 
8. Alatt **hely**válassza **USA 2. keleti régiója**, és kattintson a **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



